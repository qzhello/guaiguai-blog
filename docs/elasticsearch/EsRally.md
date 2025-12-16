# 压测
使用ES Rally

https://esrally.readthedocs.io/en/latest/developing.html#installation-instructions-for-development


# 安装
```shell
#!/bin/bash

# 更新系统软件包
sudo yum update -y

# 安装 Python3 和 pip
sudo yum install -y python3 python3-pip python3-devel gcc make

# 验证 Python 和 pip 版本
python3 --version
pip3 --version

# 安装 ESRally
pip3 install esrally

# 验证安装
esrally --version

# 配置 ESRally
echo "ESRally 安装完成。您可以使用 'esrally' 命令运行基准测试。"

```