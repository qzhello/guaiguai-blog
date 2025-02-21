---
title: 内存分析
# 禁用目录
toc: true
comments: true
weight: 1
---


# 下载并安装
| 命令                                                                                                                    | 描述             |
|-----------------------------------------------------------------------------------------------------------------------|----------------|
| curl -o arthas.zip xxxx/arthas-bin.zip && unzip arthas.zip && java -jar arthas-boot.jar                               | 下载并且安装         |
| jmap -histo:live <PID> \| head -n 20   <br/>jcmd 82 GC.class_histogram \| grep -v "Total" \| sort -nrk3 \| head -n 10 | 查看堆内存占用最高的几个对象 |
| jmap -dump:live,format=b,file=heapdump.hprof <PID>                                                                    | 生成堆快照          |
 ｜ thread -n 3                                                                                                         | 前三占用cpu最多的线程 |

