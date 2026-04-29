
# 构建文档

## 克隆仓库

```shell
git clone git@github.com:dengyouf/mkdocs-material.git
git checkout source
```

## 安装依赖

```shell
conda create -n py311 python=3.11
pip install -e . -i https://mirrors.tencent.com/pypi/simple  && pip install mkdocs-minify-plugin mkdocs-redirects -i https://mirrors.tencent.com/pypi/simple
```

> python 版本为 3.11

## 启动服务

```shell
mkdocs serve --dev-addr 127.0.0.1:8000 --watch docs --watch mkdocs.yml  --livereload
```
