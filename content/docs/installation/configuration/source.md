---
title: "设置更新源"
linkTitle: "设置更新源"
weight: 211
date: 2021-01-24
description: >
  设置Ubuntu Server的更新源
---

## 设置更新源

在进行任何更新和软件安装前，建议先设置更新源，以保证速度。

如果服务器在国内，则可以考虑设置apt源为国内代理，这样速度要好很多。

首先备份源列表:

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list_original
```

然后修改 `/etc/apt/sources.list` 文件，以下以增加阿里云源为例:

```bash
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```

对于腾讯云/阿里云上的服务器，默认都配置好了腾讯云和阿里云的源，直接用即可，速度超好。

### 更新系统

先更新各种软件到最新，简单的apt-get搞定：

```bash
apt-get update
apt-get upgrade
```

