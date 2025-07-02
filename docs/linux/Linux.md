---
title: Linux入门指南
# 禁用目录
toc: true
weight: 1
---

# 常用命令
| 命令                          | 描述           |
|-----------------------------|--------------|
| ls -hl                      | 查看文件大小       |
| lsof -i:8888                | 查看端口号占用情况    |
| nslookup nginx-ui.kizuki.cn | 查看域名连通情况     |
| less a.txt                  | 查看文件内容       |
| find / -type f -size +200M  | 查看文件大于200MB的 |
| tcpdump -i eth0 -A -s 0 'tcp port 80' > http_requests.txt｜ 抓包|


# 查看日志
| 命令    | 描述       |
|-------|----------|
| dmesg | 查看操作系统日志 |