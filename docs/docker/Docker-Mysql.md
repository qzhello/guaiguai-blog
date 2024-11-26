---
title: Mysql
weight: 3
---

# 部署
```shell
docker pull jcloudhub.jcloudcs.com/jdcloudhub/mysql

docker run --name mysql -e MYSQL_ROOT_PASSWORD=1234 \
       -p 3306:3306 \ 
       -d jcloudhub.jcloudcs.com/jdcloudhub/mysql
```
