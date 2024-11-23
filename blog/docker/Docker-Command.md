---
title: Docker命令
# 禁用目录
toc: true
comments: true
weight: 1
---
# 1. 容器
## 列出容器
```docker ps [-a]```

## 日志
```docker logs -f <container_id> --tail=100```

## 进入
```docker exec -it <container_id> bash```

## 运行
```docker run -d -p <host_port>:<container_port> --name <container_name> <image_name>```

## 删除
```docker rm <container_id>```

# 2. 镜像
## 列出镜像
```docker images```

## 拉取镜像
```docker pull <image_name>```

## 删除镜像
```docker rmi <image_name>```

## 构建镜像
```docker build -t <image_name>```

## 查看镜像历史
```docker history <image_name>```

## 导入/导出镜像
```docker save -o <output_file.tar> <image_name>```

```docker load -i <input_file.tar>```

# 3. 网络
## 列出网络
```docker network ls```

## 删除网络
```docker network rm <network_name>```

## 创建网络
```docker network create <network_name>```