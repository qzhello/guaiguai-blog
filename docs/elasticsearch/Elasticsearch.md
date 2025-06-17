---
title: Elasticsearch入门指南
# 禁用目录
toc: true
comments: true
weight: 1
---
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

# 新特性
1. 7.7 后，
    限制每个节点最大分片数。cluster.max_shards_per_node 
    限制上下文滚动500个。search.max_open_scroll_context 
    父熔断器，indices.breaker.total.use_real_memory，默认jvm堆内存95%。
    废除_all字段支持。
   https://www.elastic.co/guide/en/elasticsearch/reference/7.4/breaking-changes-7.0.html#breaking_70_indices_changes
2. 7.10
   提高存储字段压缩能力，降低成本。
   https://www.elastic.co/guide/en/elasticsearch/reference/7.10/migrating-7.10.html#breaking-changes-7.10
3. 7.16
   跨集群搜索支持SQL查询。
   优化缓存，提高查询性能。
4. 8.5
   HNSW算法实现向量相似度搜索
   新增时序功能（TSDS）
   改进Lucene压缩算法降低索引大小
   Range query性能增强。
   random sampler聚合查询。
   降低Master和Data节点的堆内存消耗。
   删除_type，但是8.x会兼容7.x的请求。
   https://www.elastic.co/guide/en/elasticsearch/reference/8.5/breaking-changes.html
5.  8.13
    向量优化。维度提高到4096，向量索引支持Scalar Quantization特性，可降低接近75%的向量索引的内存。支持稀疏向量，增加sparse_vector类型。
    支持单个shard的查询并发。
    
6.  8.17
    向量优化。dense_vector（稠密向量）引入BBQ （Better Binary Quantization）的量化类型，向量索引可以实现32倍的内存压缩，极大降低内存容量。
    best_compression的codec压缩使用zstd，大概降低12%的存储，提升14%的写入吞吐量。
    ES|QL若干特性优化，支持全文检索等，详细信息
7. 