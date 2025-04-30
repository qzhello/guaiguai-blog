---
title: k8s
weight: 1
---

# 切换版本号
```shell
brew install kubectl@1.22
# 安装 asdf
brew install asdf
# 安装 kubectl 插件
asdf plugin add kubectl
# 安装特定版本
asdf install kubectl 1.22.3
# 设置全局版本
asdf current kubectl 1.22.3

brew install kubectx

brew list --versions kubectl

# 切换到特定版本
brew switch kubectl 1.22.3
```


```shell

```