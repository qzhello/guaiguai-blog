---
title: ES
# 禁用目录
toc: true
comments: true
weight: 2
---

# 创建namespace
```shell
# kibana-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kibana-config
  namespace: elasticsearch-local
data:
  kibana.yml: |
    server.host: "0.0.0.0"
    server.shutdownTimeout: "5s"
    elasticsearch.hosts: ["http://elasticsearch-client.elasticsearch-local:9200"]
    monitoring.ui.container.elasticsearch.enabled: true
    xpack.security.enabled: false
```


```shell
# kibana-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kibana
  namespace: elasticsearch-local
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
      - name: kibana
        image: bitnami/kibana:8.13.2
        ports:
        - containerPort: 5601
          name: http
        env:
        - name: ELASTICSEARCH_HOSTS
          value: "http://elasticsearch-client.elasticsearch-local:9200"
        resources:
          limits:
            memory: 1Gi
            cpu: "0.5"
          requests:
            memory: 512Mi
            cpu: "0.2"
        volumeMounts:
        - name: config
          mountPath: /usr/share/kibana/config/kibana.yml
          subPath: kibana.yml
      volumes:
      - name: config
        configMap:
          name: kibana-config
```

```shell
# kibana-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: kibana
  namespace: elasticsearch-local
  labels:
    app: kibana
spec:
  ports:
  - port: 5601
    name: http
    targetPort: 5601
    nodePort: 30601  # 添加这行，指定 NodePort
  selector:
    app: kibana
  type: NodePort
```

```shell
# 查看 Kibana Pod 状态
kubectl get pods -n elasticsearch-local -l app=kibana

# 查看 Kibana 日志
kubectl logs -f deployment/kibana -n elasticsearch-local

# 获取 Kibana 访问地址
kubectl get svc kibana -n elasticsearch-local
```

```shell
# 使用端口转发访问 Kibana
kubectl port-forward svc/kibana -n elasticsearch-local 5601:5601
```
