---
title: JVM的运行原理
# 禁用目录
toc: true
comments: true
weight: 2
---

# 命令

## jstack
### 查询线程状态
```shell
jstack <pid> | grep "java.lang.Thread.State" | sort | uniq -c
```