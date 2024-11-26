---
title: JDK
# 禁用目录
toc: true
weight: 1
---

# 安装
## 方式1:
```shell
yum install java-1.8.0-openjdk.x86_64

ls /usr/lib/jvm/
```

## 方式2:
下载：
```shell
https://repo.huaweicloud.com:8443/artifactory/java-local/jdk/8u151-b12/jdk-8u151-linux-x64.tar.gz
```
解压：
```shell
tar -zxvf jdk-8u151-linux-x64.tar.gz -C /usr/local/
# 删除压缩包
rm -rf jdk-8u151-linux-x64.tar.gz
```


# 配置环境
```shell
JAVA_HOME=/usr/local/jdk1.8.0_151
JRE_HOME=/usr/local/jdk1.8.0_151/jre
export MAVEN_HOME=/usr/local/apache-maven-3.8.1/
export PATH=${PATH}:${MAVEN_HOME}/bin:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```