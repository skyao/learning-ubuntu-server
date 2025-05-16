---
title: "安装Maven"
linkTitle: "Maven"
weight: 20
description: >
  安装并配置Maven
---



## 安装maven

### 用sdkman安装

```bash
$ sdk install maven

Downloading: maven 3.8.5

In progress...

Setting maven 3.8.5 as default.
```





## 配置代理服务器

export MAVEN_OPTS="-DsocksProxyHost=192.168.0.1 -DsocksProxyPort=7891"

- https://stackoverflow.com/questions/34267443/maven-to-use-socks-proxy-for-specific-repo

