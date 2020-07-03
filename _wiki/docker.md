---
layout: wiki
title: Docker-未完成
categories: [docker]
description: 学习Docker,及时记录下来
keywords: Docker
---

### 安装

环境： centos 7

```bash

# 安装yum-utils并设置稳定的存储库。
sudo yum install -y yum-utils

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

# 安装DOCKER引擎
sudo yum install docker-ce docker-ce-cli containerd.io

# 启动docker
sudo systemctl start docker

# 通过运行hello-world 映像来验证是否正确安装了Docker Engine 。
sudo docker run hello-world

```

### 启动

```bash

# 启动docker
sudo systemctl start docker

```

### 命令

```bash

# 列出所有镜像
docker image ls

# 列出正在运行的容器
docker ps

# 列出运行过的容器
docker ps -all

# 在包含有Dockerfile的文件夹中，build并测试镜像
docker build --tag bulletinboard:1.0 .

# 启动容器
# --publish要求Docker将主机端口8000上传入的流量转发到容器的端口8080。容器具有自己的专用端口集，因此，如果要从网络访问某个端口，则必须以这种方式将流量转发到该端口。否则，作为默认的安全状态，防火墙规则将阻止所有网络流量到达您的容器。
# --detach 要求Docker在后台运行此容器。
# --name指定一个名称，在后续命令中，您可以使用该名称来引用您的容器bb。
docker run --publish 8000:8080 --detach --name bb bulletinboard:1.0

# 停止并删除容器
docker rm --force bb

# 停止容器
docker stop bb

# 删除容器
docker rm bb

```

### Dockerfile样本

```bash

# 使用官方镜像最为父镜像
FROM node:current-slim

# 设置镜像系统的工作目录，非主机目录
WORKDIR /usr/src/app

# 复制当前目录的主机文件到镜像目录/usr/src/app/package.json
COPY package.json .

# 镜像中运行命令
RUN npm install

# 容器运行时暴露8080端口
EXPOSE 8080

# 容器运行命令
CMD [ "npm", "start" ]

# 将剩余主机源码复制到容器中
COPY . .

```

### Docker Hub
制作好的镜像可以在Docker Hub中共享，以便轻松下载并运行。

```bash

# 登录docker
docker login

# Docker Hub页面创建仓库Create Repository，并命名为bulletinboard

# 位于Dockerfile目录下，运行
docker tag bulletinboard:1.0 <Your Docker ID>/bulletinboard:1.0

# push到Docker Hub中
docker push <Your Docker ID>/bulletinboard:1.0




```
