---
title: Nginx入门指南
# 禁用目录
toc: true
weight: 1
---
# 部署
## 本机部署
```shell
yum -y install gcc gcc-c++ autoconf automake make
yum install zlib zlib-devel openssl openssl-devel pcre pcre-devel wget httpd-tools vim -y
curl -O https://nginx.org/download/nginx-1.28.0.tar.gz

# wget http://nginx.org/download/nginx-1.25.2.tar.gz
#
#tar -zxvf nginx-1.25.2.tar.gz
#mv nginx-1.25.2/ nginx-build
#
#cd /usr/local/nginx-build
# ./configure --prefix=/usr/local/nginx

#make  
#sudo make install
#
#或者

yum install nginx
# /usr/local/nginx/sbin/nginx


#sudo vi /etc/systemd/system/nginx.service
#sudo systemctl daemon-reload
#sudo systemctl start nginx
#sudo systemctl stop nginx
#sudo systemctl enable nginx
#sudo systemctl status nginx


```

nginx.service
```
[Unit]
Description=Nginx web server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s stop
PIDFile=/usr/local/nginx/logs/nginx.pid
Restart=always

[Install]
WantedBy=multi-user.target           
```

```shell
获取日志路径
nginx -V
```

# 常用配置
### SSL配置
```shell
server {
    listen 443 ssl;
    server_name nginx-ui.kizuki.cn;
    ssl_certificate /usr/local/nginx/ssl/nginx-ui.crt;
    ssl_certificate_key /usr/local/nginx/ssl/nginx-ui.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
        # auth_basic "secret";
        # auth_basic_user_file /usr/local/openresty/nginx/db/pw.db;
        client_max_body_size 2048M;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://xxxx:9000;
        proxy_connect_timeout 3;
        proxy_read_timeout 3;
        proxy_send_timeout 3;
        access_log off;
        break;
    }
}
```


```shell

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    client_max_body_size 1048M;
    proxy_buffering on;
    proxy_buffer_size 128k;
    proxy_buffers  256 1m;
    proxy_busy_buffers_size 128m;
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;
    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```