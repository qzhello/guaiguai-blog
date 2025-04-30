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

docker run --name mysql -e MYSQL_ROOT_PASSWORD=1234 \
       -p 3306:3306 mysql
       
```


# mac安装
```shell
brew services start mysql@8.0
```

```shell
vi ~/.zshrc
最后增加
export PATH="/opt/homebrew/Cellar/mysql@8.0/8.0.42/bin:$PATH"
```
```shell
source ~/.zshrc
mysql -uroot
# 修改密码
ALTER USER 'root'@'localhost' IDENTIFIED BY '1234';
```
