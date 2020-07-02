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

```
