---
title: Nginx Elasticsearch
toc: true
comments: true
weight: 3
---

# 部署
## 6.8.0
一般分两步，先把配置文件copy出来，然后删掉重新启动带映射的。
```shell
docker run -d --name elasticsearch680 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
 docker.elastic.co/elasticsearch/elasticsearch:6.8.0
```
```shell
docker cp elasticsearch680:/usr/share/elasticsearch/config /Users/quzhihao/es/config680
```
```shell
docker run -d --name elasticsearch680 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  -v /Users/quzhihao/es/config680:/usr/share/elasticsearch/config \
-v /Users/quzhihao/localtime:/etc/localtime:ro \
 docker.elastic.co/elasticsearch/elasticsearch:6.8.0
```

## 8.11.3
```shell
docker run -d --name elasticsearch811 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  elasticsearch:8.11.3
```

```shell
docker cp elasticsearch811:/usr/share/elasticsearch/config /Users/quzhihao/es/config811
```

```shell
docker run -d --name elasticsearch811 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  -v /Users/quzhihao/es/config811:/usr/share/elasticsearch/config \
  elasticsearch:8.11.3
```

## 7.7.0
```shell
docker run -d --name elasticsearch770 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  -v /Users/quzhihao/es/config770:/usr/share/elasticsearch/config \
docker.elastic.co/elasticsearch/elasticsearch:7.7.0
```
```
docker cp elasticsearch770:/usr/share/elasticsearch/config /Users/quzhihao/es/config770
```
```
docker run -d --name elasticsearch770 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
docker.elastic.co/elasticsearch/elasticsearch:7.7.0
```


## 5.6.16-arm64
```shell
docker run -d --name elasticsearch522 --privileged=true\
  -p 9200:9200 -p 9300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
 webhippie/elasticsearch:5.6-arm64
```

```shell
docker cp elasticsearch522:/etc/elasticsearch/ /Users/quzhihao/es/config522
```

```shell
docker run -d --name elasticsearch522 --privileged=true\
  -p 9200:9200 -p 9300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  -v /Users/quzhihao/es/config522/elasticsearch:/etc/elasticsearch \
 webhippie/elasticsearch:5.6-arm64
```


# 增加用户与角色
```shell
./elasticsearch-users useradd quzhihao -p quzhihao -r kibana_admin,logstash_system,beats_system,apm_system,kibana_system,beats_admin,ingest_admin,logstash_admin,rollup_admin,superuser,machine_learning_admin,watcher_admin
```