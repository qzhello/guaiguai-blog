---
title: Git常用命令
# 禁用目录
toc: true
comments: true
weight: 1
---

sudo yum install git

git config --global user.name "没有小名的曲"
git config --global user.email "951012707@qq.com"
// 照提示操作，生成的密钥对将存储在~/.ssh/id_rsa和~/.ssh/id_rsa.pub。
ssh-keygen -t rsa -b 4096 -C "951012707@qq.com"
cat ~/.ssh/id_rsa.pub 登录github，进入设置 -> SSH公钥 -> 添加公钥。将刚刚复制的公钥粘贴到“公钥”字段中，并保存即可！
sudo cat ~/.ssh/id_rsa 设置到gitee的Credentials里


# 重置代理
科学上网比如有时候指定了，可以取消
```text
git config --global --unset http.proxy
git config --global --unset https.proxy
```

# 提交总是失败
可能是提交了图片，容易中断传输
`git config --global http.postBuffer 524288000`

