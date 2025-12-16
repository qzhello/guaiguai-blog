---
title: nginx-ui使用
toc: true
weight: 1
---
https://nginxui.com/zh_CN/guide/getting-started.html

# 创建目录
/usr/local/etc/nginx-ui

# 安装nodejs

配置[app.ini]

nginx文件的http下需要加：
```shell
http {
  include /etc/nginx/conf.d/*.conf;
  include /etc/nginx/sites-enabled/*;
}
```

注意log和nginx处。
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
AccessLogPath   = /var/log/nginx/access.log
ErrorLogPath    = /var/log/nginx/error.log
LogDirWhiteList = /var/log/nginx
ConfigDir       = /etc/nginx/
PIDPath         = /run/nginx.pid
TestConfigCmd   = 
ReloadCmd       = nginx -s reload
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
# 安装
curl -O https://github.com/0xJacky/nginx-ui/releases/download/v2.3.2/nginx-ui-linux-64.tar.gz
#启动
nohup /usr/local/bin/nginx-ui -config /usr/local/etc/nginx-ui/app.ini &

```

重置密码：
/usr/local/bin/nginx-ui  reset-password --config=/usr/local/etc/nginx-ui/app.ini


默认端口9000