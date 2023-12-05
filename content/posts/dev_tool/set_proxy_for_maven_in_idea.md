---
title: 为 Intellij IDEA 中的 Maven 设置网络代理
summary: 为 Intellij IDEA 中的 Maven 设置网络代理
date: 2023-12-01
tags:
  - 开发工具
  - Proxy
  - IDEA
  - Maven
categories:
  - DevTool
series: 
  - TheGreatWall
---
>引用：stack overflow
>[Intellij Community can't use http proxy for Maven](https://stackoverflow.com/questions/1784132/intellij-community-cant-use-http-proxy-for-maven)

1. 于 IDEA 中双击 Shift 键打开快捷搜索窗口，输入：`Vm options for importer`，然后 Enter 进入设置项[![pirX2lQ.png](https://z1.ax1x.com/2023/12/01/pirX2lQ.png)](https://imgse.com/i/pirX2lQ)
2. 填入代理服务器地址和 IP (HTTP 协议)：`-DproxySet=true -DproxyHost=127.0.0.1(示例值) -DproxyPort=10889(示例值)`[![pirXbpF.png](https://z1.ax1x.com/2023/12/01/pirXbpF.png)](https://imgse.com/i/pirXbpF)