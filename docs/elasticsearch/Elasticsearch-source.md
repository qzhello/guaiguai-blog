---
title: Elasticsearch源码编译
# 禁用目录
toc: true
comments: true
weight: 1
---
Elasticsearch 8.13.4

# 源码运行
```shell
git clone git@github.com:elastic/elasticsearch.git

分支：da95df1

reset一下到这个分支就是8.13.4
```

# 修改路径
`gradle/wrapper/gradle-wrapper.properties`
 
https://services.gradle.org/distributions/gradle-8.7-all.zip 

   distributionUrl：改为本地路径，记得把distributionSha256Sum也改掉。最好是所有的gradle都改。
```shell
distributionUrl=file:///Users/quzhihao/Downloads/gradle-8.7-all.zip
```

# 修改build.gradle
```shell
// 修改该文件,将其中的
//  nativeBundle("org.elasticsearch.ml:ml-cpp:${project.version}:deps@zip") {
//    changing = true
//  }
//  nativeBundle("org.elasticsearch.ml:ml-cpp:${project.version}:nodeps@zip") {
//    changing = true
//  }
进行注释， 否则无法构建，原因是对应版本的依赖不存在;
```

## 下载license.key（如果发布正式的话）
```shell
curl -O https://artifacts.elastic.co/GPG-KEY-elasticsearch
```

## 修改ml下载源
https://github.com/elastic/elasticsearch/issues/48350

位置 `x-pack/plugin/ml/build.gradle`
```shell
  注释：     url "https://artifacts-snapshot.elastic.co/"
  改为：     url "https://prelert-artifacts.s3.amazonaws.com/"
  
  artifact改为：
  artifact '/maven/org/elasticsearch/ml/ml-cpp/8.13.4-SNAPSHOT/ml-cpp-8.13.4-SNAPSHOT.zip'
```
如果不改的话，
只能用该命令跳过
```shell
./gradlew :distribution:archives:darwin-aarch64-tar:assemble -Dlicense.key=licenses/GPG-KEY-elasticsearch -Dbuild.docker=false -Dbuild.snapshot=true -x :x-pack:plugin:ml:explodedBundlePlugin -x :x-pack:plugin:ml:generateNotice
```
# 构建
```shell
./gradlew :distribution:archives:darwin-aarch64-tar:assemble -Dlicense.key=licenses/GPG-KEY-elasticsearch -Dbuild.docker=false -Dbuild.snapshot=true 
```

解压后，可以关闭
```shell
// 修改elasticsearch.yml 配置文件关闭以下几个配置(根据自身需求)
xpack.security.enabled: false
xpack.security.enrollment.enabled: false
xpack.ml.enabled: false
```

## Idea启动
启动`Debug Elasticsearch`

开启idea debug
```shell
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005
```

# 配置
elasticsearch.yml

自己弄3个比如：es1.yml、es2.yml、es3.yml
```shell
cluster.name: test
node.name: node-3
path.logs: /Users/quzhihao/logs
http.port: 9203
transport.port: 9303
discovery.seed_hosts: ["127.0.0.1:9301", "127.0.0.1:9302", "127.0.0.1:9303"]

node.roles: ["master", "data", "ingest"]
http.cors.enabled: true
http.cors.allow-origin: "*"

cluster.initial_master_nodes: ["127.0.0.1:9301", "127.0.0.1:9302", "127.0.0.1:9303"]
cluster.fault_detection.leader_check.retry_count: 2
cluster.fault_detection.leader_check.timeout: 30s

xpack.monitoring.collection.enabled: true
xpack.security.enabled: false
xpack.security.enrollment.enabled: false
xpack.ml.enabled: false
xpack.security.http.ssl:
  enabled: false
xpack.security.transport.ssl:
  enabled: false
http.host: 0.0.0.0

ingest.geoip.downloader.enabled: false
```


# 权限问题
如果出现权限问题。 
修改server/src/main/resources/org/elasticsearch/bootstrap/security.policy 中的配置
- （1）注释掉所有baseCode
- （2）文件最末尾加入
```shell
permission java.lang.RuntimePermission "createClassLoader";
permission java.lang.RuntimePermission "setContextClassLoader";
permission java.lang.RuntimePermission "accessDeclaredMembers";
permission java.lang.RuntimePermission "loadLibrary.*";
```

# 部署kibana
## 增加角色

关闭xpack的话就可以不加。
`xpack.security.enabled: false`
```shell
./elasticsearch-users useradd quzhihao -p quzhihao -r kibana_admin,logstash_system,beats_system,apm_system,kibana_system,beats_admin,ingest_admin,logstash_admin,rollup_admin,superuser,machine_learning_admin,watcher_admin
```

## kibana.yml
```shell
server.host: "0.0.0.0"
elasticsearch.hosts: [ "http://host.docker.internal:9201" ]
monitoring.ui.ccs.enabled: false
csp.strict: false
xpack.observabilityAIAssistant.enabled: false
monitoring.ui.enabled: true
xpack.monitoring.collection.enabled: true
```

如果缺少许可证，可以用免费的（毕竟我们不用机器学习）
```shell
curl -X POST "http://localhost:9201/_license/start_basic"
```

参考：https://www.docker.elastic.co/r/kibana/kibana:8.13.4-SNAPSHOT-arm64
```shell
docker run -d -p 15601:5601  \
  --name kibana813  -e TZ='CST-8' \
  --volume "/Users/quzhihao/docker/kibana/kibana813.yml:/usr/share/kibana/config/kibana.yml" \
  kibana:8.13.4
  
```

# 调试命令
jvm.options

增加这句，3个节点就分隔开，比如5007、5008、5009
```shell
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5007
```

# 源码启动
细节自行修改
```shell#!/bin/bash

# 定义基础路径
BASE_PATH="/Users/quzhihao/IdeaProjects/elasticsearch/distribution/archives/darwin-aarch64-tar/build/install"
ES_VERSION="elasticsearch-8.13.4-SNAPSHOT"
ES_INSTANCES=("$BASE_PATH/${ES_VERSION}-1" "$BASE_PATH/${ES_VERSION}-2" "$BASE_PATH/${ES_VERSION}-3")
ES_INSTANCES_DEBUG_PORT=("5007" "5008" "5009") # 定义调试端口

# 定义配置文件路径
CONFIG_FILES=("/Users/quzhihao/Downloads/es1.yml" "/Users/quzhihao/Downloads/es2.yml" "/Users/quzhihao/Downloads/es3.yml")

# JVM 堆内存设置 (4GB)
HEAP_SIZE="4g"

# 清理 `elasticsearch.keystore.tmp` 文件
echo "清理 elasticsearch.keystore.tmp 文件..."
for ES_INSTANCE in "${ES_INSTANCES[@]}"; do
  KEYSTORE_TMP_FILE="${ES_INSTANCE}/config/elasticsearch.keystore.tmp"
  if [ -f "$KEYSTORE_TMP_FILE" ]; then
    rm -f "$KEYSTORE_TMP_FILE"
    echo "已删除文件: $KEYSTORE_TMP_FILE"
  else
    echo "未找到文件: $KEYSTORE_TMP_FILE，跳过清理。"
  fi
done

# 杀掉所有运行中的 Elasticsearch 进程
echo "清理所有运行中的 Elasticsearch 进程..."
ES_PROCESSES=$(pgrep -f "elasticsearch")
if [ -n "$ES_PROCESSES" ]; then
  echo "发现以下 Elasticsearch 进程，准备杀掉："
  echo "$ES_PROCESSES"
  echo "$ES_PROCESSES" | xargs kill -9
  echo "所有 Elasticsearch 进程已被杀掉。"
else
  echo "没有发现运行中的 Elasticsearch 进程。"
fi

# 清理运行中的 Elasticsearch 实例
echo "清理运行中的 Elasticsearch 实例..."
for ES_INSTANCE in "${ES_INSTANCES[@]}"; do
  PID_FILE="${ES_INSTANCE}/elasticsearch.pid"
  if [ -f "$PID_FILE" ]; then
    PID=$(cat "$PID_FILE")
    if ps -p "$PID" > /dev/null 2>&1; then
      echo "停止 Elasticsearch 实例: $ES_INSTANCE (PID: $PID)"
      kill -9 "$PID"
    fi
    rm -f "$PID_FILE"
  fi
done

# 删除旧的 Elasticsearch 目录
echo "删除旧的 Elasticsearch 实例目录..."
for ES_INSTANCE in "${ES_INSTANCES[@]}"; do
  if [ -d "$ES_INSTANCE" ]; then
    rm -rf "$ES_INSTANCE"
    echo "删除目录: $ES_INSTANCE"
  fi
done

# 复制新的 Elasticsearch 实例
echo "复制新的 Elasticsearch 实例..."
for i in "${!ES_INSTANCES[@]}"; do
  cp -R "$BASE_PATH/$ES_VERSION" "${ES_INSTANCES[$i]}"
  echo "复制完成: ${ES_INSTANCES[$i]}"
done

# 应用配置文件
echo "应用配置文件..."
for i in "${!ES_INSTANCES[@]}"; do
  cp "${CONFIG_FILES[$i]}" "${ES_INSTANCES[$i]}/config/elasticsearch.yml"
  echo "配置文件已复制到: ${ES_INSTANCES[$i]}/config/elasticsearch.yml"
done

# 设置堆内存大小和调试选项
echo "设置堆内存大小和调试选项..."
for i in "${!ES_INSTANCES[@]}"; do
  JVM_OPTIONS_FILE="${ES_INSTANCES[$i]}/config/jvm.options"
  DEBUG_PORT="${ES_INSTANCES_DEBUG_PORT[$i]}" # 获取对应的调试端口

  if [ -f "$JVM_OPTIONS_FILE" ]; then
    # 在文件末尾追加堆内存设置
    echo "" >> "$JVM_OPTIONS_FILE"  # 添加一个空行，确保格式清晰
    echo "-Xms${HEAP_SIZE}" >> "$JVM_OPTIONS_FILE"
    echo "-Xmx${HEAP_SIZE}" >> "$JVM_OPTIONS_FILE"
    echo "-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:${DEBUG_PORT}" >> "$JVM_OPTIONS_FILE"
    echo "堆内存设置和调试选项已追加到文件末尾: $JVM_OPTIONS_FILE"
  else
    echo "未找到 jvm.options 文件: $JVM_OPTIONS_FILE"
  fi
done

# 启动 Elasticsearch 实例
echo "启动 Elasticsearch 实例..."
for i in "${!ES_INSTANCES[@]}"; do
  DEBUG_PORT="${ES_INSTANCES_DEBUG_PORT[$i]}"
  "${ES_INSTANCES[$i]}/bin/elasticsearch" &
  echo "启动 Elasticsearch: ${ES_INSTANCES[$i]} (调试端口: $DEBUG_PORT)"
done

echo "所有 Elasticsearch 实例已启动！"

```

关闭脚本
```shell
#!/bin/bash

# 使用 ps -ef 找到所有运行中的 Elasticsearch 进程
echo "查找所有运行中的 Elasticsearch 进程..."
ES_PROCESSES=$(ps -ef | grep elasticsearch | grep -v grep | awk '{print $2}')

if [ -n "$ES_PROCESSES" ]; then
  echo "发现以下 Elasticsearch 进程，准备杀掉："
  echo "$ES_PROCESSES"
  
  # 杀掉所有找到的进程
  echo "$ES_PROCESSES" | xargs kill -9
  echo "所有 Elasticsearch 进程已被杀掉。"
else
  echo "没有发现运行中的 Elasticsearch 进程。"
fi

echo "Elasticsearch 已关闭完成！"

```


重启/停止

chmod +x manage_node.sh

例如：
./manage_node.sh 1 stop
./manage_node.sh 2 restart
./manage_node.sh 3 stop

```shell
#!/bin/bash

# 定义基础路径
BASE_PATH="/Users/quzhihao/IdeaProjects/elasticsearch/distribution/archives/darwin-aarch64-tar/build/install"
ES_VERSION="elasticsearch-8.13.4-SNAPSHOT"
ES_INSTANCES=("$BASE_PATH/${ES_VERSION}-1" "$BASE_PATH/${ES_VERSION}-2" "$BASE_PATH/${ES_VERSION}-3")

# 检查输入参数
if [ $# -lt 1 ] || [ $# -gt 2 ]; then
  echo "使用方法: $0 <node_number> [stop|restart]"
  echo "例如: $0 1 stop    (仅停止第一个节点)"
  echo "例如: $0 2 restart (重启第二个节点)"
  exit 1
fi

NODE_NUMBER=$1
ACTION=${2:-restart} # 默认行为为重启

# 验证输入参数
if ! [[ "$NODE_NUMBER" =~ ^[1-3]$ ]]; then
  echo "错误: 请输入有效的节点编号 (1, 2 或 3)"
  exit 1
fi

if [[ "$ACTION" != "stop" && "$ACTION" != "restart" ]]; then
  echo "错误: 第二个参数必须是 stop 或 restart"
  exit 1
fi

# 获取要操作的节点路径
NODE_INDEX=$((NODE_NUMBER - 1))
ES_INSTANCE=${ES_INSTANCES[$NODE_INDEX]}

# 停止节点
echo "停止节点: $ES_INSTANCE"
PROCESS_INFO=$(ps -ef | grep "${ES_VERSION}-${NODE_NUMBER}" | grep -v grep) # 获取进程信息
if [ -n "$PROCESS_INFO" ]; then
  echo "找到以下进程:"
  echo "$PROCESS_INFO"
  PID=$(echo "$PROCESS_INFO" | awk '{print $2}') # 提取 PID
  echo "停止进程 (PID: $PID)"
  kill -9 "$PID"
  echo "节点已停止: $ES_INSTANCE"
else
  echo "未找到正在运行的进程，节点可能未启动。"
fi

# 如果操作为 "stop"，直接退出
if [ "$ACTION" == "stop" ]; then
  echo "操作完成：节点 $NODE_NUMBER 已停止。"
  exit 0
fi

# 等待 5 秒
echo "等待 5 秒后重启节点..."
sleep 5

# 启动节点
echo "启动节点: $ES_INSTANCE"
"${ES_INSTANCE}/bin/elasticsearch" &
echo "节点已启动: $ES_INSTANCE"

exit 0


```