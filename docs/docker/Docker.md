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
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin


# 如果是8
sudo sed -i 's|https://download.docker.com|https://mirrors.aliyun.com/docker-ce|' /etc/yum.repos.d/docker-ce.repo
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

# 镜像加速
## 阿里云加速地址获取
访问这个链接进行配置：
`
https://cr.console.aliyun.com/cn-chengdu/instances/mirrors
`
## 更换镜像源
```shell
# 编辑Docker配置文件: 打开或创建 
sudo vi /etc/docker/daemon.json

# 重启Docker服务: 为使配置生效，请执行以下命令：
sudo systemctl daemon-reload
sudo systemctl restart docker
```
配置如下：
```shell
{
    "registry-mirrors": [
            "https://docker.211678.top",
            "https://docker.1panel.live",
            "https://hub.rat.dev",
            "https://docker.m.daocloud.io",
            "https://do.nark.eu.org",
            "https://dockerpull.com",
            "https://dockerproxy.cn",
            "https://docker.awsl9527.cn"
      ]
}
```