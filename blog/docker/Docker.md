---
title: Docker入门指南
# 禁用目录
toc: true
weight: 1
---

# 搭建Docker
## 1. 设置Docker安装仓库
```shell
sudo yum install -y yum-utils

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```
## 2. 安装并运行Docker
```shell
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
## 3. 启动
```sudo systemctl start docker```
## 4. 第一个启动项目：Hello world
```sudo docker run hello-world```
## 5. 安装运行nginx
```
sudo docker pull nginx
sudo docker run --name docker-nginx-demo -d -p 8080:80 nginx
```
