# 集群
## 状态
```shell
cluster.ps

# 连通性检查
cluster.check

```



## volume
### 修复
```shell
# 修复 command_volume_fix_replication.go
volume.fix.replication -volumesPerStep 10 -retry 3 -collectionPattern *
## 修复指定collections
volume.fix.replication -collectionPattern=important*

# 其他参数：
# -doDelete	true	除了修复复制不足外，还可以删除过度复制的卷
# -maxParallelization 最大并行任务数
# -retry 5 重试次数
# -volumesPerStep 0 每轮修复的volume数量
# 如果要真实执行，加上 -apply或者-force
```

### 平衡与清理
```shell
# 平衡volume分布  
volume.balance -force  
  
# Volume压缩（vacuum）  
volume.vacuum -garbageThreshold=0.3 -collection=data

# 移动volume到其他服务器  104857600=100MB
volume.move -volumeId=34 -ioBytePerSecond=104857600 -target=10.22.34.246:8082 -source=10.22.34.246:8081
 
  
# 复制volume  
volume.copy -source=host1:8080 -target=host2:8080 -volumeId=1  
  
# 热迁移volume server  
volume.server.evacuate -server=host:8080 -replication=010
```

### check
```shell
# 检查复制一致性（检查所有复制volume的不一致性，修复副本间差异）
volume.check.disk -v 
# 如果要真实执行，加上 -apply或者-force


# 文件系统一致性
# 检查孤立chunk（只检查）  
volume.fsck -v  
  
# 删除孤立chunk  
volume.fsck -reallyDeleteFromVolume -findMissingChunksInFiler  
  
# 检查特定collection  
volume.fsck -collection=data -volumeId=1,2,3
```

### 删除与搬运volume
```shell
# 删除特定volume
volume.delete -node=localhost:8080 -volumeId=123
# 删除空volume
volume.deleteEmpty -quietFor=24h -force


# 高级（服务器撤离）
volumeServer.evacuate -node=localhost:8081 
# 跳过无法移动的volume
volumeServer.evacuate -node=localhost:8081 -skipNonMoveable -apply 


# 将volume移动到不同磁盘类型  
volume.tier.move -fromDiskType=hdd -toDiskType=ssd -collection=bucket-name -apply  
  
# 上传到远程存储  
volume.tier.upload -volumeId=123 -dest=s3
```

### 离线
```shell
volumeServer.leave -node=localhost:8081 -apply


# 推荐
1. 搬迁：volumeServer.evacuate -node=localhost:8081 -apply
2. 等待
3. 离线：volumeServer.leave -node=localhost:8081 -apply
```

### 启动

```shell
volume
-dir=/Users/kizuhiko/docker/seaweed/data1
-max=100
-mserver="localhost:9333,localhost:9334,localhost:9335"
-port=8081
-fileSizeLimitMB=4096
```


# EC
```shell
# 编码EC volumes  
ec.encode -fullPercent=95 -quietFor=1h  
  
# 重建丢失的EC shards  
ec.rebuild -apply  
  
# 平衡EC shards分布  
ec.balance -apply
```


# S3
## bucket
```shell
# 查看
s3.bucket.list

# 创建bucket
s3.bucket.create -name myjfs

# 创建用户，赋予权限
s3.configure -user qzh -access_key=1234 -secret_key=1234 -actions Read,Write,List -buckets myjfs -apply
# 删除用户权限
s3.configure -user=qzh -delete -actions=Read:qzh -apply
# 删除用户
s3.configure -user=qzh -delete -apply


# 删除
s3.bucket.delete -name=bucket-name
```
### 配置
```shell
# 更新配置
fs.configure -locationPrefix /buckets/my-bucket -volumeGrowthCount=12 -apply 
 # 当没有可写volume时，自动创建12个物理volume
 # -collection 指定collection
 # -replication 复制策略
 # -volumeGrowthCount 无可写volume时自动创建的物理volume数量
 # -disk 磁盘类型标签
 # -ttl 数据生存时间 
 # -worm false 启用写一次读多次模式
 # -wormGracePeriod 0 WORM宽限期（秒）
 # -wormRetentionTime 0 WORM保留时间（秒）
 # -fsync false 写入时是否执行fsync
 # -readOnly false 禁用写入，设为只读
 # -maxFileNameLength 0 文件名长度限制（字节）
 # -dataCenter 指定数据中心
 # -rack 指定机架
 # -dataNode 指定数据节点

# 删除指定路径配置
fs.configure -locationPrefix=/buckets/my-bucket -delete -apply


# 创建
curl "http://localhost:9333/vol/grow?replication=010&collection=automq-data&disk=nvme&count=200"

#参数	值	含义
#replication
# 000: no replication                                            没有复制
# 001: replicate once on the same rack                          在同一机架上复制一次
# 010: replicate once on a different rack, but same data center 在同一数据中心的不同机架上复制一次
# 100: replicate once on a different data center                在不同的数据中心复制一次
# 200: replicate twice on two different data center             在两个不同的数据中心上复制两次
# 110: replicate once on a different rack, and once on a different data center 在不同的机架上复制一次，在不同的数据中心复制一次

#collection	my-bucket 指定collection名称
#disk	nvme	只在nvme类型的磁盘上创建volume master_server_handlers_admin.go
# count	200	创建200个可写volume

```


### 清理
```shell

# 清理过期数据  
s3.clean.uploads -timeAgo=24h  
```

### 移动与复制
```shell
# 移动整个bucket到新位置  
fs.mv /buckets/source-bucket /buckets/target-bucket 
  
# 移动bucket到另一个目录  
fs.mv /buckets/source-bucket /backup/buckets/

fs.cp -r /buckets/source-bucket /buckets/target-bucket

fs.cp -r /buckets/source-bucket http://:8888/buckets/target-bucket/



# 合并
fs.mergeVolumes -collection=myjfs -apply

```

## collection
### 查看
```shell
collection.list 
```



### 删除
```shell
collection.delete -collection=myjfs
collection.delete -collection=myjfs -force

# 慎用：会删除所有无collection的!!!!!!!!!!!
collection.delete -collection=_default_ -force

```