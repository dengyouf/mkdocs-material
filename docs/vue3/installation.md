# Installation

## 安装nvm

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash
```
## 安装node

```
nvm install 22.13.0
nvm use 22.13.0
```
## 安装nrm

```
npm install -g nrm --registry=https://registry.npmmirror.com
nrm ls
npm ---------- https://registry.npmjs.org/
yarn --------- https://registry.yarnpkg.com/
tencent ------ https://mirrors.tencent.com/npm/
cnpm --------- https://r.cnpmjs.org/
taobao ------- https://registry.npmmirror.com/
npmMirror ---- https://skimdb.npmjs.com/registry/
huawei ------- https://repo.huaweicloud.com/repository/npm/
nrm use taobao
```

## 安装yarn

```shell
npm i -g yarn
npm i -g yrm
yrm use taobao
```


## Vscode插件

- `Vue (Official)`： 官方插件，用于识别vue语法
- `Vue VSCode Snippets`: 快速生成完整的 Vue 代码结构
- `JavaScript (ES6) code snippets`: ES6语法提示
- `Auto Rename Tag`： 自动重命名标签
- `Auto Close Tag`: 自动闭合标签
- `Vue Peek`: Vue 模块组件中快速跳转
- `Prettier ESLint`：格式化代码插件
- `vscode-icons`: 文件图标
