## 基于 kubeadm 部署 Kubernetes v1.34 集群

使用Kubeadm快速部署Kubernetes集群，操作系统为Ubuntu 24.04.3 LTS(noble)，用到的各相关程序版本如下:

- kubernetes: 1.34.5
- containerd: 1.7.28
- calico: v3.28.4

### 基础环境配置

```bash
# 主机名解析
cat >> /etc/hosts << EOF
172.16.143.111 u24-k8s-master01
172.16.143.121 u24-k8s-worker01
172.16.143.123 u24-k8s-worker02
172.16.143.123 u24-k8s-worker03
172.16.143.111 u24-k8s-vip
EOF

# 关闭防火墙和selinux
 systemctl  disable ufw --now

# 关闭swap分区
sed -i 's/.*swap.*/#&/' /etc/fstab
swapoff -a && sysctl -w vm.swappiness=0
systemctl  mask swap.target

# 时间同步
systemctl stop systemd-timesyncd
systemctl disable systemd-timesyncd

timedatectl  set-timezone Asia/Shanghai
apt install chrony -y

cat  > /etc/chrony/chrony.conf << 'EOF'
pool ntp.aliyun.com       iburst maxsources 4
keyfile /etc/chrony/chrony.keys
driftfile /var/lib/chrony/chrony.drift
logdir /var/log/chrony
maxupdateskew 100.0
rtcsync
makestep 1 3
EOF
systemctl restart chrony.service && systemctl  enable chrony.service
chronyc sources

# 加载IPVS模版
apt install ipset ipvsadm -y
cat >> /etc/modules-load.d/k8s.conf << 'EOF'
br_netfilter
ip_vs
ip_vs_rr
ip_vs_wrr
ip_vs_sh
nf_conntrack
nf_nat
overlay
vxlan
iptable_nat
xt_MASQUERADE
EOF
systemctl restart systemd-modules-load
lsmod | grep -E 'br_netfilter|ip_vs|nf_conntrack|overlay|vxlan'

# 内核参数优化
cat > /etc/sysctl.d/k8s.conf << 'EOF'
# 开启IPv4转发
net.ipv4.ip_forward = 1
# 允许桥接的流量进入iptables/netfilter
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
# 优化连接跟踪表大小，防止大规模连接爆掉
net.netfilter.nf_conntrack_max = 2310720
# TCP优化（缩短 TIME_WAIT，快速回收连接）
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 15
net.ipv4.tcp_keepalive_time = 600
net.ipv4.tcp_keepalive_intvl = 30
net.ipv4.tcp_keepalive_probes = 5
# 内存相关优化（防止OOM）
vm.swappiness = 0
vm.overcommit_memory = 1
vm.panic_on_oom = 0
# 文件句柄限制
fs.file-max = 52706963
# 网络层面优化
net.core.somaxconn = 32768
net.core.netdev_max_backlog = 16384
net.ipv4.tcp_max_syn_backlog = 16384
EOF
sysctl --system
cat >> /etc/security/limits.conf << 'EOF'
* soft nofile 1048576
* hard nofile 1048576
* soft nproc 1048576
* hard nproc 1048576
* soft memlock unlimited
* hard memlock unlimited
EOF
ulimit  -n 64435
ulimit  -s 10240
```

### 安装容器运行时


```bash
# 配置docker源
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg
tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Signed-By: /etc/apt/keyrings/docker.gpg
EOF
# 安装指定版本的容器运行时
apt update
apt-cache madison containerd
apt install -y containerd=1.7.28-0ubuntu1~24.04.2

# 配置 containerd
mkdir -pv /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
# 修改cgroup Driver为systemd
sed -ri 's@SystemdCgroup = false@SystemdCgroup = true@' /etc/containerd/config.toml
# 更改sandbox_image
sed -ri 's@registry.k8s.io\/pause:3.8@registry.cn-hangzhou.aliyuncs.com\/google_containers\/pause:3.9@' /etc/containerd/config.toml
# 配置containerd 的镜像存储目录
sed -i 's@root = "/var/lib/containerd"@root = "/data/containerd"@g' /etc/containerd/config.toml
# 配置加速器
sed -i 's@config_path = ""@config_path = "\/etc\/containerd\/certs.d\/"@g' /etc/containerd/config.toml
# docker.io 镜像加速
mkdir -p /etc/containerd/certs.d/docker.io
cat > /etc/containerd/certs.d/docker.io/hosts.toml << 'EOF'
server = "https://docker.io" # 源镜像地址

[host."https://docker.1ms.run"]
  capabilities = ["pull","resolve"]

[host."https://docker.m.daocloud.io"] # 道客-镜像加速地址
  capabilities = ["pull","resolve"]

[host."https://dockerproxy.com"] # 镜像加速地址
  capabilities = ["pull", "resolve"]

[host."https://docker.mirrors.sjtug.sjtu.edu.cn"] # 上海交大-镜像加速地址
  capabilities = ["pull","resolve"]

[host."https://docker.mirrors.ustc.edu.cn"] # 中科大-镜像加速地址
  capabilities = ["pull","resolve"]

[host."https://docker.nju.edu.cn"] # 南京大学-镜像加速地址
  capabilities = ["pull","resolve"]

[host."https://registry-1.docker.io"]
  capabilities = ["pull","resolve","push"]
EOF

# registry.k8s.io 镜像加速
mkdir -p /etc/containerd/certs.d/registry.k8s.io
cat > /etc/containerd/certs.d/registry.k8s.io/hosts.toml << 'EOF'
server = "https://registry.k8s.io"

[host."https://k8s.m.daocloud.io"]
  capabilities = ["pull", "resolve", "push"]
EOF

# quay.io 镜像加速
mkdir -p /etc/containerd/certs.d/quay.io
cat > /etc/containerd/certs.d/quay.io/hosts.toml << 'EOF'
server = "https://quay.io"

[host."https://quay.m.daocloud.io"]
  capabilities = ["pull", "resolve", "push"]
EOF

# docker.elastic.co镜像加速
mkdir -p /etc/containerd/certs.d/docker.elastic.co
tee /etc/containerd/certs.d/docker.elastic.co/hosts.toml << 'EOF'
server = "https://docker.elastic.co"

[host."https://elastic.m.daocloud.io"]
  capabilities = ["pull", "resolve", "push"]
EOF
systemctl daemon-reload &&  systemctl restart containerd && systemctl enable containerd
```

### 安装Kubernetes软件包

```bash
version=v1.34
apt-get update && apt-get install -y apt-transport-https
curl -fsSL https://mirrors.aliyun.com/kubernetes-new/core/stable/${version}/deb/Release.key |
    gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://mirrors.aliyun.com/kubernetes-new/core/stable/${version}/deb/ /" |
    tee /etc/apt/sources.list.d/kubernetes.list

apt-get update
apt-cache madison kubeadm
apt install kubeadm=1.34.4-1.1  kubectl=1.34.4-1.1 kubelet=1.34.4-1.1 -y
# 配置kubelet开机自启
systemctl enable --now kubelet
```

### 集群初始化

```bash
# 更换sandbox_image
kubeadm config images list --kubernetes-version=1.34.4
kubeadm config images pull --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers --kubernetes-version=1.34.4
grep sandbox_image /etc/containerd/config.toml
    pause_threshold = 0.02
    sandbox_image = "registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.9"
sed -i 's@registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.9@registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.10.1@g' /etc/containerd/config.toml
systemctl  restart containerd
#
kubeadm init \
  --kubernetes-version=1.34.4 \
  --control-plane-endpoint="u24-k8s-vip" \
  --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers \
  --pod-network-cidr=10.244.0.0/16 \
  --service-cidr=10.96.0.0/12 \
  --token-ttl=0 \
  --upload-certs|tee kubeadm.log
```

### 添加工作节点

```bash
kubeadm join u24-k8s-vip:6443 --token epk5ac.zissz8hfez3q54wh \
	--discovery-token-ca-cert-hash sha256:072740e9c647340697525dccd1c6b9991af3268e416d8e71fa35f3920a5fa56e
kubectl  get nodes -o wide
NAME                  STATUS     ROLES           AGE     VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
u24-k8s-master01      NotReady   control-plane   2m53s   v1.34.4   172.16.143.111   <none>        Ubuntu 24.04.3 LTS   6.8.0-124-generic   containerd://1.7.28
u24-k8s-worker01      NotReady   <none>          46s     v1.34.4   172.16.143.121   <none>        Ubuntu 24.04.3 LTS   6.8.0-124-generic   containerd://1.7.28
u24-k8s-worker03      NotReady   <none>          36s     v1.34.4   172.16.143.123   <none>        Ubuntu 24.04.3 LTS   6.8.0-124-generic   containerd://1.7.28
u24-master-worker02   NotReady   <none>          40s     v1.34.4   172.16.143.122   <none>        Ubuntu 24.04.3 LTS   6.8.0-124-generic   containerd://1.7.28
```


### 修改集群网络为IPVS

```bash
kubectl  edit cm/kube-proxy -n kube-system -o yaml
  ipvs:
    ...
    strictARP: true
  mode: "ipvs"
kubectl -n kube-system rollout restart daemonset kube-proxy
ipvsadm -Ln
 ipvsadm -Ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  10.96.0.1:443 rr
  -> 172.16.143.111:6443          Masq    1      1          0
TCP  10.96.0.10:53 rr
  -> 10.244.157.1:53              Masq    1      0          0
  -> 10.244.157.3:53              Masq    1      0          0
TCP  10.96.0.10:9153 rr
  -> 10.244.157.1:9153            Masq    1      0          0
  -> 10.244.157.3:9153            Masq    1      0          0
UDP  10.96.0.10:53 rr
  -> 10.244.157.1:53              Masq    1      0          0
  -> 10.244.157.3:53              Masq    1      0          0
```

### 网络插件安装

!!! note "[版本对应关系](https://docs.tigera.io/calico/3.30/getting-started/kubernetes/requirements):`https://docs.tigera.io/calico/3.30/getting-started/kubernetes/requirements`"

```bash
curl https://raw.githubusercontent.com/projectcalico/calico/v3.30.7/manifests/calico.yaml -O
# 配置Pod网络
sed -i 's@# - name: CALICO_IPV4POOL_CIDR@- name: CALICO_IPV4POOL_CIDR@g' calico.yaml
sed -i 's@#   value: "192.168.0.0/16"@  value: "10.244.0.0/16"@g' calico.yaml
# 默认为26位掩码，改成24，可用地址有多少个2^8 -2  = 254 个
sed -i '/value: "10\.244\.0\.0\/16"/a\            - name: CALICO_IPV4POOL_BLOK_SIZE\n              value: "24"'  calico.yaml

kubectl apply -f calico.yaml
```

### 集群可用性验证

```bash
# 创建deploy和svc
kubectl  create deployment myapp --image=ikubernetes/myapp:v1 --replicas=5
kubectl expose deployment myapp --port=80 --target-port=80 --type="NodePort"

kubectl  get pod -A -o wide -l app=myapp
NAMESPACE   NAME                     READY   STATUS    RESTARTS   AGE     IP              NODE                  NOMINATED NODE   READINESS GATES
default     myapp-65f9b8dd5b-dqgtg   1/1     Running   0          4m23s   10.244.81.194   u24-master-worker02   <none>           <none>
default     myapp-65f9b8dd5b-nfzbq   1/1     Running   0          4m23s   10.244.0.65     u24-k8s-worker01      <none>           <none>
default     myapp-65f9b8dd5b-rm5k5   1/1     Running   0          4m23s   10.244.0.66     u24-k8s-worker01      <none>           <none>
default     myapp-65f9b8dd5b-vchpv   1/1     Running   0          4m23s   10.244.157.4    u24-k8s-worker03      <none>           <none>
default     myapp-65f9b8dd5b-xb5jz   1/1     Running   0          4m23s   10.244.81.193   u24-master-worker02   <none>           <none>
root@u24-k8s-master01:~# kubectl  get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        31m
myapp        NodePort    10.101.23.183   <none>        80:32395/TCP   4m29s
root@u24-k8s-master01:~# for i in `seq 5`;do curl 10.101.23.183/hostname.html;done
myapp-65f9b8dd5b-nfzbq
myapp-65f9b8dd5b-xb5jz
myapp-65f9b8dd5b-vchpv
myapp-65f9b8dd5b-rm5k5
myapp-65f9b8dd5b-dqgtg

apt install dnsutils
kubectl get svc -n kube-system
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   3h7m
dig -t A myapp.default.svc.cluster.local @10.96.0.10

kubectl  exec -it myapp-65f9b8dd5b-dqgtg -- sh
/ # wget -O - -q myapp/hostname.html
myapp-65f9b8dd5b-vchpv
/ # wget -O - -q myapp/hostname.html
myapp-65f9b8dd5b-rm5k5
/ # wget -O - -q myapp/hostname.html
myapp-65f9b8dd5b-dqgtg
```

## 卸载集群

### 1.卸载整个集群

!!! note "先拆除各个工作节点，在拆除控制平面"

```shell
kubeadm reset
rm -rf /etc/kubernetes /var/lib/kubelet/ /var/lib/cni/ /etc/cni/net.d/ /var/lib/etcd/
```

### 2.拆除单个工作节点

```shell
# 1. 禁止调度
kubectl cordon k8s-worker03
# 2. 排空节点
kubectl drain k8s-worker03
# 3. 删除节点
kubectl delete node  k8s-worker03
# 4. 执行reset跟后续的清理工作
kubeadm reset.
rm -rf /etc/kubernetes /var/lib/kubelet/ /var/lib/cni/ /etc/cni/net.d/ /var/lib/etcd/
```
