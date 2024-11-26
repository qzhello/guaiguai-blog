---
title: Shell常见脚本
toc: true
comments: true
weight: 4
---

# 启动一个java项目
```shell
#!/bin/bash
# 定义变量
JAR_NAME="guaiguai-shorturl.jar"
JAR_PATH="/usr/local/dockerfile/filesystem/$JAR_NAME"
LOG_FILE="/usr/local/dockerfile/filesystem/logs/myapp.log"

# 找到并杀掉之前的 Java 应用程序进程
echo "Looking for existing Java process..."
PID=$(ps aux | grep $JAR_NAME | grep -v grep | awk '{print $2}')

if [ -n "$PID" ]; then
  echo "Found existing Java process with PID: $PID. Killing it..."
  kill -9 $PID
else
  echo "No existing Java process found."
fi

# 确保日志文件存在并且可写
mkdir -p $(dirname $LOG_FILE)
touch $LOG_FILE

# 启动新的 Java 应用程序进程
echo "Starting new Java process..."
nohup java -Dspring.profiles.active=product \
        -Xmx2g \
        -XX:CompressedClassSpaceSize=128m \
        -XX:+UseG1GC \
        -XX:G1HeapRegionSize=16m \
        -Dlog.path=/usr/local/dockerfile/filesystem/logs \
        -XX:+UseCompressedClassPointers \
        -XX:+HeapDumpOnOutOfMemoryError \
        -XX:HeapDumpPath=/data/logs/ \
        -Dserver.port=9999 \
        -jar $JAR_PATH > $LOG_FILE 2>&1 &

# 输出新的进程 ID
NEW_PID=$!
echo "New Java process started with PID: $NEW_PID"

echo "Done."
```

# 一个循环的html请求
```shell
#!/bin/bash

# 设置接口 URL 和请求头信息
URL='cn-east.test-qzh.com/fuse'
TOKEN='123456'
CONTENT_TYPE_HEADER='Content-Type: application/json'
LOG_FILE="change_master_log.txt"
DATA='{
    "az": "hd2",
    "status": 0,
    "test": true,
    "fixedClusters": ["test"]
}'

# 循环每秒请求一次接口
while true; do
    response=$(curl -s -w "\n%{http_code}\n%{time_total}" \
        -H "Content-Type: application/json" \
        -H "token: $TOKEN" \
        -d "$DATA" \
        "$URL" \
        )
    timestamp=$(date +"%Y-%m-%d %H:%M:%S")
    # 解析响应
    body=$(echo "$response" | awk 'NR==1 {print}' | tr -d '\n')  # 获取响应体并移除换行符
    status_code=$(echo "$response" | sed -n '2p')
    time_total=$(echo "$response" | sed -n '3p')

    # 打印结果
    echo "[$timestamp][$status_code]$body" | tee -a "$LOG_FILE"
    sleep 5
done


# nohup ./changeMasterTest.sh > /dev/null 2>&1 &
```