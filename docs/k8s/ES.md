---
title: ES
# 禁用目录
toc: true
comments: true
weight: 2
---

# 创建namespace
```shell
apiVersion: v1
kind: Namespace
metadata:
  name: elasticsearch-local
```


```shell
# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: elasticsearch-config
  namespace: elasticsearch-local
data:
  elasticsearch.yml: |
    cluster.name: "es-cluster"
    network.host: 0.0.0.0
    discovery.seed_hosts: "elasticsearch-0.elasticsearch.elasticsearch-local.svc.cluster.local,elasticsearch-1.elasticsearch.elasticsearch-local.svc.cluster.local,elasticsearch-2.elasticsearch.elasticsearch-local.svc.cluster.local"
    cluster.initial_master_nodes: "elasticsearch-0,elasticsearch-1,elasticsearch-2"
    xpack.security.enabled: false
    xpack.security.http.ssl.enabled: false
    xpack.security.transport.ssl.enabled: false
```

```shell
# headless-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: elasticsearch
  namespace: elasticsearch-local
  labels:
    app: elasticsearch
spec:
  ports:
  - port: 9200
    name: http
  - port: 9300
    name: transport
  clusterIP: None
  selector:
    app: elasticsearch
```

```shell
# service.yaml
# elasticsearch-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: elasticsearch-client
  namespace: elasticsearch-local
  labels:
    app: elasticsearch
spec:
  ports:
  - port: 9200
    name: http
    targetPort: 9200
    nodePort: 30920  # 添加这行，指定 NodePort
  selector:
    app: elasticsearch
  type: NodePort
```

```shell
# pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: elasticsearch-pv-0
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: elasticsearch-local
  claimRef:
    namespace: elasticsearch-local
    name: elasticsearch-data-elasticsearch-0
  hostPath:
    path: /mnt/data/elasticsearch-0
    type: DirectoryOrCreate
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: elasticsearch-pv-1
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: elasticsearch-local
  claimRef:
    namespace: elasticsearch-local
    name: elasticsearch-data-elasticsearch-1
  hostPath:
    path: /mnt/data/elasticsearch-1
    type: DirectoryOrCreate
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: elasticsearch-pv-2
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: elasticsearch-local
  claimRef:
    namespace: elasticsearch-local
    name: elasticsearch-data-elasticsearch-2
  hostPath:
    path: /mnt/data/elasticsearch-2
    type: DirectoryOrCreate
```

```shell
# statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: elasticsearch
  namespace: elasticsearch-local
spec:
  serviceName: elasticsearch
  replicas: 3
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      securityContext:
        fsGroup: 1000
      containers:
      - name: elasticsearch
        image: docker.elastic.co/elasticsearch/elasticsearch:8.13.2
        ports:
        - containerPort: 9200
          name: http
        - containerPort: 9300
          name: transport
        env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: node.name
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: cluster.name
          value: "es-cluster"
        - name: discovery.seed_hosts
          value: "elasticsearch-0.elasticsearch.elasticsearch-local.svc.cluster.local,elasticsearch-1.elasticsearch.elasticsearch-local.svc.cluster.local,elasticsearch-2.elasticsearch-local.elasticsearch.svc.cluster.local"
        - name: cluster.initial_master_nodes
          value: "elasticsearch-0,elasticsearch-1,elasticsearch-2"
        - name: ES_JAVA_OPTS
          value: "-Xms1g -Xmx1g"
        - name: bootstrap.memory_lock
          value: "true"
        - name: path.data
          value: "/usr/share/elasticsearch/data/$(POD_NAME)"
        resources:
          limits:
            memory: 2Gi
            cpu: "1"
          requests:
            memory: 1Gi
            cpu: "0.5"
        volumeMounts:
        - name: elasticsearch-data
          mountPath: /usr/share/elasticsearch/data
        - name: config
          mountPath: /usr/share/elasticsearch/config/elasticsearch.yml
          subPath: elasticsearch.yml
        securityContext:
          runAsUser: 1000
          runAsGroup: 1000
          privileged: true
          capabilities:
            add:
              - IPC_LOCK
      volumes:
      - name: config
        configMap:
          name: elasticsearch-config
  volumeClaimTemplates:
  - metadata:
      name: elasticsearch-data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: elasticsearch-local
      resources:
        requests:
          storage: 10Gi
```

```shell
# 查看 Pod 的完整 DNS 名称
kubectl get pods -n elasticsearch-local -o wide

# 测试 DNS 解析
kubectl exec -it elasticsearch-0 -n elasticsearch-local -- curl -X GET "http://elasticsearch-0.elasticsearch.elasticsearch-local.svc.cluster.local:9200/_cat/health"
kubectl exec -it elasticsearch-1 -n elasticsearch-local -- curl -X GET "http://elasticsearch-1.elasticsearch.elasticsearch-local.svc.cluster.local:9200/_cat/health"
kubectl exec -it elasticsearch-2 -n elasticsearch-local -- curl -X GET "http://elasticsearch-2.elasticsearch.elasticsearch-local.svc.cluster.local:9200/_cat/health"
```


