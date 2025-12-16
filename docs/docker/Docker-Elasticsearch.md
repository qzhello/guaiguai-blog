---
title: Elasticsearch
toc: true
comments: true
weight: 3
---

# 部署ES
## 6.8.0
一般分两步，先把配置文件copy出来，然后删掉重新启动带映射的。
```shell
docker run -d --name elasticsearch680 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
 docker.elastic.co/elasticsearch/elasticsearch:6.8.0
```
```shell
docker cp elasticsearch680:/usr/share/elasticsearch/config /Users/kizuhiko/es/config680
```
```shell
docker run -d --name elasticsearch680 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  -v /Users/kizuhiko/es/config680:/usr/share/elasticsearch/config \
 docker.elastic.co/elasticsearch/elasticsearch:6.8.0
```

## 8.11.3
```shell
docker run -d --name elasticsearch811 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  elasticsearch:8.11.3
  
  docker run -d --name elasticsearch813 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  elasticsearch:8.13.0
```

```shell
docker cp elasticsearch811:/usr/share/elasticsearch/config /Users/kizuhiko/es/config811
docker cp elasticsearch813:/usr/share/elasticsearch/config /Users/kizuhiko/es/config813
```

```shell
docker run -d --name elasticsearch811 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  -v /Users/kizuhiko/es/config811:/usr/share/elasticsearch/config \
  elasticsearch:8.11.3
  
  docker run -d --name elasticsearch813 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  -v /Users/kizuhiko/es/config813:/usr/share/elasticsearch/config \
  elasticsearch:8.13.0
```

## 7.7.0
```shell
docker run -d --name elasticsearch770 --privileged=true\
  -p 19200:9200 -p 19300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  -v /Users/kizuhiko/es/config770:/usr/share/elasticsearch/config \
docker.elastic.co/elasticsearch/elasticsearch:7.7.0
```
```
docker cp elasticsearch770:/usr/share/elasticsearch/config /Users/kizuhiko/es/config770
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
docker cp elasticsearch522:/etc/elasticsearch/ /Users/kizuhiko/es/config522
```

```shellf
docker run -d --name elasticsearch522 --privileged=true\
  -p 9200:9200 -p 9300:9300 \
  -e "ES_JAVA_OPTS=-Xms2g -Xmx2g" \
  -v /Users/kizuhiko/es/config522/elasticsearch:/etc/elasticsearch \
 webhippie/elasticsearch:5.6-arm64
```


# 增加用户与角色
```shell
./elasticsearch-users useradd kizuhiko -p kizuhiko -r kibana_admin,logstash_system,beats_system,apm_system,kibana_system,beats_admin,ingest_admin,logstash_admin,rollup_admin,superuser,machine_learning_admin,watcher_admin
```

# 部署Kibana
```shell
# v8
docker run -d -p 15601:5601  \
  --name kibana862  -e TZ='CST-8' \
  --volume "/Users/kizuhiko/docker/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml" \ 
  docker.elastic.co/kibana/kibana:8.6.2
  
  
docker run -d -p 15601:5601  --name kibana813  -e TZ='CST-8' --volume "/Users/kizuhiko/docker/kibana/kibana813.yml:/usr/share/kibana/config/kibana.yml" docker.elastic.co/kibana/kibana:8.13.0
 
# v7
docker run -d -p 15601:5601  \
  --name kibana770  \ 
  --volume "/Users/kizuhiko/docker/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml" \
  docker.elastic.co/kibana/kibana:7.7.0
 
# v6
docker run -d -p 15601:5601  \
  --name kibana680  \
  --volume "/Users/kizuhiko/docker/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml" \
  docker.elastic.co/kibana/kibana:6.8.0
```

```shell
# 源码启动
yarn kbn bootstrap
npm run start


node scripts/generate_plugin

yarn plugin-helpers build

```
