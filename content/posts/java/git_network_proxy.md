---
title: Windows 下设置 Git 网络代理
date: 2023-11-30
categories:
  - Git
tags:
  - Git
  - Proxy
summary: Windows 下设置 Git 网络代理
---
由于神秘力量影响，想要从 GitHub Pull 或 Push 代码经常失败、缓慢，可以通过对 Git 设置网络代理来解决。

1. **通过命令行设置：** 在命令行窗口（CMD）或者 Git Bash 中，运行以下命令，将代理设置为代理地址和端口，替换 `proxy_address` 和 `proxy_port` 为代理服务器的实际地址和端口：
```Shell
git config --global http.proxy http://proxy_address:proxy_port
git config --global https.proxy https://proxy_address:proxy_port
```

2. **通过环境变量设置：**

于 CMD 中执行：
```Shell
set http_proxy=http://proxy_address:proxy_port
set https_proxy=https://proxy_address:proxy_port
```
或者在PowerShell中执行：
```Shell
$env:http_proxy = "http://proxy_address:proxy_port"
$env:https_proxy = "https://proxy_address:proxy_port"
```

3. **检查代理设置：**

运行以下命令来查看当前Git的全局配置，确保代理设置已经生效：
```Shell
git config --global --get-regexp http.proxy
git config --global --get-regexp https.proxy
```