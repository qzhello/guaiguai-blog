---
title: Redis
weight: 3
---

# 部署
```shell
docker run --name redis -p 6379:6379 -d redis --requirepass guaiguai 
```

# 如何访问redis
```shell
docker exec -it redis redis-cli
```
