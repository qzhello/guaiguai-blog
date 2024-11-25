---
title: Nginx入门指南
# 禁用目录
toc: true
weight: 1
---

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