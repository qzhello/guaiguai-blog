---
title: Docker命令
toc: true
comments: true
weight: 2
---
# 1. 容器
## 列出容器
```shell
docker ps [-a]
```

## 日志
```shell
docker logs -f <container_id> --tail=100
```

## 进入
```shell
docker exec -it <container_id> bash
```

## 运行
```shell
docker run -d -p <host_port>:<container_port> --name <container_name> <image_name>
```

## 删除
```shell
docker rm <container_id>
```

# 2. 镜像
## 列出镜像
```shell
docker images
```

## 拉取镜像
```shell
docker pull <image_name>
```

## 删除镜像
```shell
docker rmi <image_name>
```

## 构建镜像
```shell
docker build -t <image_name>
```

## 查看镜像历史
```shell
docker history <image_name>
```

## 导入/导出镜像
```shell
docker save -o <output_file.tar> <image_name>
```

```shell
docker load -i <input_file.tar>
```

# 3. 网络
## 列出网络
```shell
docker network ls
```

## 删除网络
```shell
docker network rm <network_name>
```

## 创建网络
```shell
docker network create <network_name>
```