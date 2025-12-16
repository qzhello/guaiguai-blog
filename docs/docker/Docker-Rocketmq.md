---
title: Rocketmq5
weight: 3
---

# 部署
启动namesrv
```shell
docker run -d \
  --name namesrv \
  -p 9876:9876 \
  apache/rocketmq:5.0.0 \
  sh mqnamesrv
```

启动Broker
```shell
docker run -d \
  --name broker \
  -p 10911:10911 \
  -p 10909:10909 \
  -e "NAMESRV_ADDR=host.docker.internal:9876" \
  -e "BROKER_MEM=4096m" \
  apache/rocketmq:5.0.0 \
  sh mqbroker
```

启动console
```shell
docker run -d \
  --name rocketmq-console \
  -p 8080:8080 \
  -e "NAMESRV_ADDR=host.docker.internal:9876" \
  styletang/rocketmq-console-ng
```

访问控制台
```shell
http://localhost:8080
```