---
title: Linux入门指南
# 禁用目录
toc: true
weight: 1
---

# 常用命令
| 命令                                                                                                                                                                                                                                                          | 描述               |
|---------------------------------------------------------------------------------------------------------------------[search_es_serving_tencent_cloud-5.0.1-SNAPSHOT](..%2F..%2F..%2F..%2FDownloads%2Fsearch_es_serving_tencent_cloud-5.0.1-SNAPSHOT)----------------------------------------------------------------------------------------------------------------------------------------|------------------|
| ls -hl                                                                                                                                                                                                                                                      | 查看文件大小           |
| lsof -i:8888                                                                                                                                                                                                                                                | 查看端口号占用情况        |
| nslookup nginx-ui.kizuki.cn                                                                                                                                                                                                                                 | 查看域名连通情况         |
| less a.txt                                                                                                                                                                                                                                                  | 查看文件内容           |
| find / -type f -size +200M                                                                                                                                                                                                                                  | 查看文件大于200MB的     |
| tcpdump -i eth0 -A -s 0 'tcp port 80' > http_requests.txt ｜ 抓包                                                                                                                                                                                              |
| tcpdump -i bond0 -nn -vv -A -c 100 port 36029                  \| grep -E 'stoken'                                                                                                                                                                          | 抓包对应port         |
| tcpdump -i any -A -s 0 'host 172.1.1.1 and port 31556 and (tcp[((tcp[12:1] & 0xf0) >> 2)] = 0x47 and tcp[((tcp[12:1] & 0xf0) >> 2)+1] = 0x45 and tcp[((tcp[12:1] & 0xf0) >> 2)+2] = 0x54)'                                                                | 完整抓取             |
| lsof -i :30749 \| awk '{print $9}'                                                                                                                                                           \| cut -d'>' -f2 \| cut -d: -f1 \| sort \| uniq -c \| sort -nr | 查看当前机器被调用的端口连接ip |
| lsof -i @172.1.1.1 \| awk '{print $1}' \| sort \| uniq -c \| sort -nr                                                                                                                                                                                    | 查看目标机器调用该ip最多的进程 |


# 查看日志
| 命令    | 描述       |
|-------|----------|
| dmesg | 查看操作系统日志 |