---
title: Elasticsearch命令
# 禁用目录
toc: true
comments: true
weight: 1
---
# Elasticsearch 常用命令汇总

本文档汇总了 Elasticsearch 的常用命令，按索引、索引映射、索引设置和集群管理进行分类。

## 索引操作

### 创建索引

```http
PUT /<index_name>
```

删除索引
```
DELETE /<index_name>
```

查看所有索引
```
GET /_cat/indices?v
```

查看索引文档计数
```
GET /<index_name>/_count
```

索引映射（Mapping）
查看索引的映射
GET /<index_name>/_mapping

更新索引的映射
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

获取特定字段的映射
```shell
GET /<index_name>/_mapping/field/<field_name>
```


索引设置（Settings）
查看索引的设置
```shell
GET /<index_name>/_settings
```

更新索引的设置
```shell
PUT /<index_name>/_settings
{
  "index": {
    "number_of_replicas": 2
  }
}
```

# 集群管理
查看集群健康状态
```shell
GET /_cluster/health
```
查看集群状态
```
GET /_cluster/stats
```
查看分片信息
```
GET /_cat/shards?v
```
查看分配信息
```
GET /_cat/allocation?v
```