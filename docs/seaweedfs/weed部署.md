# Admin
```shell
weed admin
-dataDir=/Users/kizuhiko/docker/seaweed/admin
-master="localhost:9333,localhost:9334,localhost:9335"
-port=23646
```


# master
```shell
weed master
-port=9333
-mdir=/Users/kizuhiko/docker/seaweed/mdata
-volumeSizeLimitMB=2048
-peers="localhost:9334,localhost:9335"
-maxParallelVacuumPerServer=2
```

# volume
```shell
weed volume
-dir=/Users/kizuhiko/docker/seaweed/data1
-max=100
-mserver="localhost:9333,localhost:9334,localhost:9335"
-port=8081
-fileSizeLimitMB=4096
```

# filer
```shell
weed filer
-master="localhost:9333"
-port=8888
-metricsPort=9327
-s3
-s3.port=8444


#-juicefs.fidPool.size=100
#-juicefs.fidPool.refillPercent=30
#-optimizationMode=juicefs
```


# GoTools
-gcflags "all=-N -l"]()