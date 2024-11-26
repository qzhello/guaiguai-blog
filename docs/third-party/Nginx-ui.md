---
title: nginx-ui使用
toc: true
weight: 1
---

/usr/local/etc/nginx-ui

配置[app.ini]
https://github.com/0xJacky/nginx-ui/blob/dev/README-zh_CN.md

nginx文件的http下需要加：
```shell
http {
  include /etc/nginx/conf.d/*.conf;
  include /etc/nginx/sites-enabled/*;
}
```


```shell
[app]
PageSize  = 20
JwtSecret = 23c45a4a-602c-420e-9d4a-13bd2ba274a0

[server]
Host    = 
Port    = 9000
RunMode = debug

[database]
Name = database

[auth]
IPWhiteList         = 
BanThresholdMinutes = 10
MaxAttempts         = 10

[casdoor]
Endpoint        = 
ClientId        = 
ClientSecret    = 
CertificatePath = 
Organization    = 
Application     = 
RedirectUri     = 

[cert]
RecursiveNameservers = 
Email                = 951012707@qq.com
CADir                = 
RenewalInterval      = 7
HTTPChallengePort    = 9180

[cluster]
Node = 

[crypto]
Secret = e577d66a9ac101c0fbc9f3f70468ff841d2d9c3fd43b16d30f4f77703b22daac

[http]
GithubProxy        = 
InsecureSkipVerify = false

[logrotate]
Enabled  = false
CMD      = logrotate /etc/logrotate.d/nginx
Interval = 1440

[nginx]
AccessLogPath   = /usr/local/nginx/logs/access.log
ErrorLogPath    = /usr/local/nginx/logs/error.log
LogDirWhiteList = /usr/local/nginx/logs
ConfigDir       = /usr/local/nginx/
PIDPath         = /usr/local/nginx/logs/nginx.pid
TestConfigCmd   = 
ReloadCmd       = /usr/local/nginx/sbin/nginx -s reload
RestartCmd      = 

[node]
Name             = 
Secret           = fbc16ba6-a3f8-4f39-baca-679c07535ce2
SkipInstallation = false
Demo             = false

[openai]
BaseUrl = 
Token   = 
Proxy   = 
Model   = 

[terminal]
StartCmd = login

[webauthn]
RPDisplayName = 
RPID          = 
RPOrigins     = 
```

# 命令
```shell
#启动
/usr/local/bin/nginx-ui -config /usr/local/etc/nginx-ui/app.ini

systemctl start nginx-ui
systemctl stop nginx-ui
systemctl restart nginx-ui
```