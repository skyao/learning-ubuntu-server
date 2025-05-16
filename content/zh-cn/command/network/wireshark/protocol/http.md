---
title: "抓取 http 包"
linkTitle: "http"
weight: 100
date: 2021-04-08
description: >
  在 ubuntu 上安装 wireshark 抓取 http 包
---



### 抓取本地http包

目标是抓取访问 http://localhost:1313/ 地址的HTTP包，设置中选择 "Loopback: lo", filter 设置为 `http and tcp.port=1313`:

![http-local](images/http-local.png)

以下是抓取多次 http 交互的一个例子，这是 dapr java-sdk 中的一个bug，当时排查时发现 https://github.com/dapr/java-sdk/issues/709： 

![http-example](images/http-example.png)