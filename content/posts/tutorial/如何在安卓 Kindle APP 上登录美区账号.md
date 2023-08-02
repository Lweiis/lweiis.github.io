---
title: "如何在安卓 Kindle APP 上登录美区账号"
date: 2023-08-02
categories:
  - tutorial
tags:
  - 教程
  - tutorial
  - Kindle
  - 电子书
summary: 为了解决 PC、Android、Kindle 三端阅读同步的问题
---
亚马逊取消中国区 Kindle 服务，为解决多端阅读进度同步，从中国区账号切换为美区账号，安卓端 APP 会出现无法登录美区账号的情况 

最终通过搜索：“安卓 kindle 无法登录美区账户”，得以解决，方案来源：知乎问题 “[安卓版kindle国内如何登录国外亚马逊账号？](https://www.zhihu.com/question/267303564)” 下 xq260 用户的回答：
>我有kindle国内和国外两个账号，因为[亚马逊中国](https://www.zhihu.com/search?q=%E4%BA%9A%E9%A9%AC%E9%80%8A%E4%B8%AD%E5%9B%BD&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A387535533%7D)和墙外的是没有关联的，一直无法使用国外账号登录。后来我自己解决的，虽然有些曲折。方法如下，首先你要有把梯子，下载Amazon shopping和kindle这两个APP。然后运行shopping，选择非中文语言，就可以用国外账号登录Amazon shopping，登录成功后，运行kindle APP，这时就可以直接用国外账号登录了。我测试过，卸载Amazon shopping后再尝试用国外账号登录kindle又不行了。

经测试：Amazon shopping 也无法登录美区账号，后通过 Prime Video 实现。可能：亚马逊商城在中国可用，Amazon shopping 默认判断使用中国区账号，Prime Video 仅海外可用，故 Prime Video 默认使用海外账号登录。  
### 操作原理：
**通过另一个亚马逊应用代替 Kindle 应用进行美区账号登录。**
使用某个 Amazon 旗下的应用登录美区 Amazon 账号，此时账户登录信息会被手机缓存，Kindle APP 会默认使用缓存的 Amazon 账号。
### 操作前提：
有梯子
### 操作步骤：
1. 在 Google Play 或者通过安装包安装 Prime Video 
2. 在代理环境下，于  Prime Video 中登陆美区 Amazon 账号
3. 此时开启 Kindle APP 会默认关联美区 Amazon 账号
