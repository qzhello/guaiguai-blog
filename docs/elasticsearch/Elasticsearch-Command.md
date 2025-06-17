---
title: Elasticsearch命令
# 禁用目录
toc: true
comments: true
weight: 1
---
本文档汇总了 Elasticsearch 的常用命令，按索引、索引映射、索引设置和集群管理进行分类。

## 索引（Index）

### 创建索引
```shell
PUT /<index_name>
```

### 删除索引
```shell
DELETE /<index_name>
```

### 查看所有索引
```shell
GET /_cat/indices?v
```

### 查看索引文档计数
```shell
GET /<index_name>/_count
```

## 索引映射（Mapping）
https://www.elastic.co/guide/en/elasticsearch/reference/8.13/mapping.html
### 查看索引的映射
```shell
GET /<index_name>/_mapping
```

### 更新索引的映射
```shell
PUT /<index_name>/_mapping
{
  "properties": {
    "<field_name>": {
      "type": "<type>"
    }
  }
}
```

### 获取特定字段的映射
```shell
GET /<index_name>/_mapping/field/<field_name>
```


## 索引设置（Settings）
### 查看索引的设置
```shell
GET /<index_name>/_settings
```

### 更新索引的设置
```shell
PUT /<index_name>/_settings
{
  "index": {
    "number_of_replicas": 2
  }
}
```

# 集群管理
### 查看集群健康状态
```shell
GET /_cluster/health
```
### 查看集群状态
```shell
GET /_cluster/stats
```
### 查看分片信息
```shell
GET /_cat/shards?v
```
### 查看分配信息
```shell
GET /_cat/allocation?v
```

### 排序最大占用
```shell
_cat/indices/*?v&s=pri.store.size:desc
```


# 运维
## reroute
### reroute 重新分配
```shell
POST _cluster/reroute?retry_failed=true
```

```shell
POST /_cluster/reroute
{
  "commands": [
    {
      "cancel": {
        "index": "index-alldata7-2025-05-03",
        "shard": 2,
        "node": "10.177.40.225:30623",
        "allow_primary": true
      }
    }
  ]
}
```

## 索引
### 优先级
```shell
PUT index-alldata7-25-03-26/_settings
{
  "index.priority": 1
}
```

### 获取索引
```shell
GET _cat/indices?health=yellow
```

### 分配
```shell
GET _cat/allocation?v
```


## 集群
### 分析分配
```shell
GET _cluster/allocation/explain
```

### 分配限速
```shell
PUT _cluster/settings
{
  "transient": {
    "cluster.routing.allocation.node_initial_primaries_recoveries": 6,
    "cluster.routing.allocation.node_concurrent_recoveries": 2,
    "cluster.routing.allocation.node_concurrent_incoming_recoveries": 2,
    "cluster.routing.allocation.node_concurrent_outgoing_recoveries": 2,
    "indices.recovery.max_bytes_per_sec": "10m"
  }
}

### 排除节点
PUT _cluster/settings
{
  "transient": {
     "cluster.routing.allocation.exclude._ip": "10.177.52.46"
  }
}

PUT _cluster/settings
{
  "transient": {
     "cluster.routing.allocation.exclude._ip": null
  }
}
```

### 查看恢复任务
```shell
GET _recovery?active_only=true
```

# 单独配置
```shell
PUT _cluster/settings
{
  "transient": {
    "indices.recovery.max_bytes_per_sec": "1b"
  }
}
```

## 分片
### 开启关闭分配

```shell
# 关闭
PUT /_cluster/settings
{
  "transient": {
    "cluster.routing.allocation.enable": null
  }
}

# 打开
PUT /_cluster/settings
{
  "transient": {
    "cluster.routing.allocation.enable": "all"
  }
}

```