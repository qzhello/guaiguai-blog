---
title: Jenkins使用
toc: true
weight: 3
---


# 部署
```shell
# 拉取
docker pull jenkins/jenkins:2.485

# 启动
docker run -d -uroot -p 9095:8080 -p 50000:50000 \
--privileged=true \
-e JAVA_OPTS=-Dhudson.model.DownloadService.noSignatureCheck=true \
--name jenkins -v /usr/local/jenkins:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /usr/lib/jvm/java-1.8.0-openjdk:/usr/local/java \
-v /usr/share/maven:/usr/local/maven \
-v /etc/localtime:/etc/localtime 5162af98ab37

# 修改文件夹权限
/usr/local/jenkins
chmod 775 /usr/local/jenkins

cat /usr/local/jenkins/secrets/initialAdminPassword
获取密码：admin adminPasswrod
```

# 接入java服务
> https://blog.csdn.net/zhenlanxiao20/article/details/142210263

1. 安装三个插件：Maven Integration、Publish Over SSH、Gitee，安装后重启
2. 系统管理-系统配置：JDK配置路径。
3. 系统管理-系统配置：Maven配置路径。
4. 系统管理-系统配置：SSH Servers配置。
5. Gitee配置：凭证配置。
6. 新建任务。
7. 配置Git的gitee地址、用户名密码等。
8. Build：配置pom.xml和clean package
9. Post Steps: Run only if build success.
10. 然后在Add post build step添加ssh。
11. SSH Publishers: 
    - Verbose output in console，开启操作日志。
    - Source files（如果本机copy可不填。如果要填），例如：target/test-1.0.SNAPSHOT.jar
    - Remove prefix: target/
    - Remote directory（如果本机copy可不填。如果要填），例如：/user/local/dockerfile/filesystem。
    Exec command： 例如：
    ```shell
    docker cp jenkins:/var/jenkins_home/workspace/guaiguai/guaiguai-shorturl/target/guaiguai-shorturl.jar /usr/local/dockerfile/filesystem/
    sh /root/exec-java.sh
    ```
13. 构建。
14. 触发规则修改：构建触发器，可以配置web hock。

# 问题
## 网络
导致拉取插件一直超时。
```shell
将default文件中的updates.jenkins.io/download全部替换为mirrors.tuna.tsinghua.edu.cn/jenkins
文件夹：/usr/local/jenkins/updates
sed -i 's|updates.jenkins-ci.org/download|mirrors.tuna.tsinghua.edu.cn/jenkins|g' default.json
sed -i 's|http://www.google.com|http://www.baidu.com|g' default.json

```

## 没有插件
```shell
需要在该目标下安装：
/usr/local/jenkins/war/WEB-INF/detached-plugins
wget https://updates.jenkins-ci.org/download/plugins/cloudbees-folder/6.5.1/cloudbees-folder.hpi
```

## Jenkins修改镜像源
```shell
修改镜像源
编辑：/usr/local/jenkins/hudson.model.UpdateCenter.xml
https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
```
