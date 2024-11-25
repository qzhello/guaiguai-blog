---
title: Elasticsearch入门指南
# 禁用目录
toc: true
comments: true
weight: 1
---
# Elasticsearch 入门指南
Elasticsearch 是一个分布式搜索和分析引擎，常用于处理海量数据的实时搜索和分析。它建立在 Apache Lucene 基础之上，并提供了一个简单的 RESTful API 进行交互。
https://www.elastic.co/guide/en/elasticsearch/reference/8.13/mapping.html

## 1. 基本概念

- **文档（Document）**: Elasticsearch 中的基本信息单元，存储为 JSON 格式。
- **索引（Index）**: 一组具有相似特性的文档的集合。
- **类型（Type）**: Elasticsearch 6.x 及更早版本中用于定义文档的逻辑分组。7.x 之后，类型被逐渐废弃。
- **节点（Node）**: 集群中的一个单实例 Elasticsearch 服务器。
- **集群（Cluster）**: 一个或多个节点的集合，具有相同的集群名称。
- **分片（Shard）**: 一个索引可以拆分成多个分片，每个分片是一个独立的 Lucene 实例。
- **副本（Replica）**: 分片的副本，用于提高容错和性能。

## 2. 安装 Elasticsearch

### 2.1 使用 Docker 安装

```bash
docker pull elasticsearch:latest
docker run -d -p 9200:9200 -p 9300:9300 --name elasticsearch elasticsearch:latest
```