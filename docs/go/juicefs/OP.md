
# Macbook本地启动


/Users/kizuhiko/GolandProjects/juicefs/

## 元数据
```
juicefs format "mysql://root:1234@(localhost:3306)/juicefs" myjfs 
```


## 挂载文件系统
```
# 安装fuse
https://macfuse.github.io/

# 挂载目录
juicefs mount -d "mysql://root:1234@(localhost:3306)/juicefs" /Users/kizuhiko/Downloads/mnt


这种挂载不能打开文件等，我们还是用s3的方式
juicefs umount /Users/kizuhiko/Downloads/mnt/jfs
```
参考：

https://blog.51cto.com/u_15294985/5933963



## 挂载s3

```
创建桶
curl --location --request PUT 'http://localhost:8444/myjfs' \
--header 'X-Amz-Content-Sha256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855' \
--header 'X-Amz-Date: 20251001T023947Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=1234/20251001/cn-east/s3/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=ea23087350f47f4c436d4c4442ae98589b3dcf8721f6d7b934b71b3cb69d4375'
```

```
# 格式化s3
juicefs format --storage s3 \
    --bucket http://10.22.32.190:8444/myjfs \
    --access-key 1234 \
    --secret-key 1234 \
    "mysql://root:1234@(10.22.32.190:3306)/juicefs" myjfs

# 格式化元数据
juicefs format "mysql://root:1234@(10.22.32.190:3306)/juicefs" myjfs 
    
   
# 挂载到 /Users/kizuhiko/Downloads/mnt/jfs
100MB缓存
juicefs mount --background "mysql://root:1234@(10.22.32.190:3306)/juicefs" \
    --cache-dir /Users/kizuhiko/Downloads/cache \
    --cache-size 100 \
    --debug \
    /Users/kizuhiko/Downloads/mnt/jfs
```


## 其他机器挂载
```
# 挂载
juicefs mount --background "mysql://root:1234@(10.22.32.190:3306)/juicefs" \
--cache-dir /media/psf/Home/Desktop/cache \
--cache-size 100 \
--debug \
/media/psf/Home/Desktop/mnt

# 卸载
juicefs umount /media/psf/Home/Desktop/mnt
```

## 查看状态
```
juicefs status "mysql://root:1234@(localhost:3306)/juicefs"
```


# 其他
```
# 挂载查看
df -h /Users/kizuhiko/Downloads/mnt
```
