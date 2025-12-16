
# docker-compose
```shell
version: '3.9'
 
services:
  master:
    network_mode: "host"
    image: chrislusf/seaweedfs # use a remote image
    command: "master -ip=172.0.16.143 -ip.bind=172.0.16.143 -metricsPort=9324"
    volumes:
      - ./data/master:/data
  volume:
    network_mode: "host"
    image: chrislusf/seaweedfs # use a remote image
    command: 'volume -mserver="172.0.16.143:9333" -ip.bind=172.0.16.143 -port=8080  -metricsPort=9325'
    depends_on:
      - master
    volumes:
      - ./data/volume:/data
  filer:
    network_mode: "host"
    image: chrislusf/seaweedfs # use a remote image
    command: 'filer -master="172.0.16.143:9333" -ip.bind=172.0.16.143 -metricsPort=9326'
    tty: true
    stdin_open: true
    depends_on:
      - master
      - volume
    volumes:
      - ./data/filer:/data
  s3:
    network_mode: "host"
    image: chrislusf/seaweedfs # use a remote image
    command: 's3 -filer="172.0.16.143:8888" -ip.bind=172.0.16.143 -metricsPort=9327'
    depends_on:
      - master
      - volume
      - filer
  webdav:
    network_mode: "host"
    image: chrislusf/seaweedfs # use a remote image
    command: 'webdav -filer="172.0.16.143:8888"'
    depends_on:
      - master
      - volume
      - filer
#  prometheus:
#    image: prom/prometheus:v2.21.0
#    ports:
#      - 9000:9090
#    volumes:
#      - ./prometheus:/etc/prometheus
#    command: --web.enable-lifecycle  --config.file=/etc/prometheus/prometheus.yml
#    depends_on:
#      - s3
```


# install.sh
```shell
docker-compose -f seaweedfs-compose.yml -p seaweedfs up
```