---
title: "设置更新源"
linkTitle: "设置更新源"
weight: 10
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
sudo vi /etc/apt/sources.list
```

然后修改 `/etc/apt/sources.list` 文件. 



### ubuntu 23.04

阿里云源 :

```bash
deb http://mirrors.aliyun.com/ubuntu/ lunar main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ lunar main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ lunar-security main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ lunar-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ lunar-updates main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ lunar-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ lunar-proposed main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ lunar-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ lunar-backports main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ lunar-backports main restricted universe multiverse
```

中科大源：

```bash
deb https://mirrors.ustc.edu.cn/ubuntu/ lunar main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ lunar main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ lunar-updates main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ lunar-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ lunar-backports main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ lunar-backports main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ lunar-security main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ lunar-security main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ lunar-proposed main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ lunar-proposed main restricted universe multiverse
```

### ubuntu 22.04

阿里云源 (不知道为什么特别慢）:

```bash
deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
```

中科大源（非常快）：

```bash
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
```



### ubuntu 20.04

阿里云源:

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

中科大镜像源:

```bash
deb https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```

对于腾讯云/阿里云上的服务器，默认都配置好了腾讯云和阿里云的源，直接用即可，速度超好。

偶尔会遇到阿里云或者中科大源不可用的情况（以前没遇到过，最近很频繁），可以切换其他源试试。

网易163源：

```bash
deb http://mirrors.163.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ focal-backports main restricted universe multiverse
# deb-src http://mirrors.163.com/ubuntu/ focal main restricted universe multiverse
# deb-src http://mirrors.163.com/ubuntu/ focal-security main restricted universe multiverse
# deb-src http://mirrors.163.com/ubuntu/ focal-updates main restricted universe multiverse
# deb-src http://mirrors.163.com/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src http://mirrors.163.com/ubuntu/ focal-backports main restricted universe multiverse
```

清华源：

```bash
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan-security main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ eoan-proposed main restricted universe multiverse
```

### 更新系统

先更新各种软件到最新，简单的 apt upgrade 搞定：

```bash
sudo apt update
sudo apt upgrade
```

## 取消PPA仓库

当添加太多的 PPA 仓库之后，apt update 的速度就会慢很多。

考虑到大多数软件不会经常更新，而且我们也没有立即更新的迫切需求，因此建议取消这些 PPA 仓库。

### 终端操作

PPA仓库存储位置：

```bash
$ cd /etc/apt/sources.list.d
$ ls -l
git-core-ubuntu-ppa-focal.list
```

打开具体要取消的PPA仓库，如上面的git的ppa仓库文件，注释掉相关的内容。

不要直接删，以后如果需要更新，可以手工取消注释而不用重新找ppa仓库地址再添加。

### UI操作

"开始菜单" -> "系统管理" -> "软件源" -> "PPA", 将不需要及时更新的软件的 PPA 取消。
