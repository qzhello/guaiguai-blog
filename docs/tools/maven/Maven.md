---
title: Maven常用命令
toc: true
weight: 1
---

> https://blog.csdn.net/u012263509/article/details/142478370


# Maven 压缩包下载与解压
华为云下载源，自行选择版本

下面的示例使用的是 3.8.1 版本
```shell
wget https://repo.huaweicloud.com/apache/maven/maven-3/3.8.1/binaries/apache-maven-3.8.1-bin.tar.gz
```

## 1. 解压
```shell
tar -zxvf apache-maven-3.8.1-bin.tar.gz
```
## 2. 移动目录
```shell
mv apache-maven-3.8.1 /usr/local/
```
## 3. 配置环境变量
```shell
vi /etc/profile
```
## 4. 在最后面追加
```shell
export MAVEN_HOME=/usr/local/apache-maven-3.8.1/
export PATH=${PATH}:${MAVEN_HOME}/bin
```
## 5. 使环境变量生效
```
source /etc/profile
```

## 6. 安装完成
使用 mvn 检查是否安装成功

```shell
mvn -verison
```
