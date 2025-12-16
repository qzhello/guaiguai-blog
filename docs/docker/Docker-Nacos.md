---
title: Nacos
weight: 3
---
https://nacos.io/docs/v3.0/quickstart/quick-start-docker/

# 部署
```shell
docker run -d --name nacos \
  -e MODE=standalone \
  -e NACOS_AUTH_ENABLE=true \
  -e NACOS_AUTH_TOKEN=Z3VhaWd1YWlndWFpZ3VhaWd1YWlndWFpZ3VhaWd1YWk= \
  -e NACOS_AUTH_IDENTITY_KEY=guaiguai \
  -e NACOS_AUTH_IDENTITY_VALUE=guaiguai \
  -p 8888:8080 \
  -p 8848:8848 \
  -p 9848:9848 \
  nacos/nacos-server

```

8080: 容器端口，console

http://localhost:8888/index.html

```shell

NACOS_AUTH_TOKEN: Nacos 用于生成JWT Token的密钥，使用长度大于32字符的字符串，再经过Base64编码。
guaiguaiguaiguaiguaiguaiguaiguai

NACOS_AUTH_IDENTITY_KEY: Nacos Server端之间 Inner API的身份标识的Key，必填。

NACOS_AUTH_IDENTITY_VALUE: Nacos Server端之间 Inner API的身份标识的Value，必填。

用户名：nacos
密码：初始化时设置
```


开源地址：https://github.com/alibaba/nacos
官方网站：https://nacos.io/zh-cn/index.html