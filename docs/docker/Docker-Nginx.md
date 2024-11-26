---
title: Nginx Docker
toc: true
comments: true
weight: 3
---

# 部署
```shell
docker pull jcloudhub.jcloudcs.com/jdcloudhub/nginx
docker run -it --name nginx -p 80:80  -d jcloudhub.jcloudcs.com/jdcloudhub/nginx 
docker cp nginx:/etc/nginx /root/
docker stop nginx
docker rm nginx
docker run -it --name nginx -p 80:80 \
-v /root/nginx:/etc/nginx \
-v /root/nginx/logs:/var/log/nginx  \
-v /root/nginx/data:/usr/share/nginx/data \
-d jcloudhub.jcloudcs.com/jdcloudhub/nginx 
```

# 问题
## SSL配置升级
缺少http_ssl_module模块，编译安装的时候带上--with-http_ssl_module配置就行了，但是现在的情况是我的nginx已经安装过了，怎么添加模块，其实也很简单，往下看：

1. 如在/usr/local/nginx下添加安装包 nginx-1.17.9.tar.gz，并解压：tar zxvf nginx-1.17.9.tar.gz

2. 切换到源码包：cd /usr/local/nginx/nginx-1.17.9

3. 执行：./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module

4. 编译完成后 : make (这里不要进行make install，否则就是覆盖安装)

5. 备份原来nginx配置文件 cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak

6. 保证nginx是关闭状态 nginx -s stop

7. 复制刚编译生成的配置覆盖原有的配置：cp ./objs/nginx /usr/local/nginx/sbin/

8. 进入sbin文件夹，启动nginx就可以了（./nginx）

```shell
./nginx(启动)

./nginx -s reload（重启）

./nginx -s stop（停止）
```