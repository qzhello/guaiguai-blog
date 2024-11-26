---
title: Nginx Mysql
toc: true
weight: 3
---

# 部署
```shell
docker pull jcloudhub.jcloudcs.com/jdcloudhub/redis

docker run --name redis -p 6379:6379 -d jcloudhub.jcloudcs.com/jdcloudhub/redis 
```


# 如何访问redis
```shell
docker exec -it redis redis-cli
```