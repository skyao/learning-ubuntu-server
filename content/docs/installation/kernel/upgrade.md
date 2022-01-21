---
title: "更新Linux内核"
linkTitle: "更新内核"
weight: 10
date: 2022-01-20
description: >
  更新Linux内核
---



### 简单更新小版本

在ssh登录到ubuntu server时，有时会看到类似的提示：

```bash
10 updates can be applied immediately.
To see these additional updates run: apt list --upgradable
```

查看具体内容:

```bash
$ sudo apt list --upgradable
[sudo] password for sky: 
Listing... Done
linux-generic/focal-proposed 5.4.0.97.101 amd64 [upgradable from: 5.4.0.96.100]
linux-headers-generic/focal-proposed 5.4.0.97.101 amd64 [upgradable from: 5.4.0.96.100]
linux-image-generic/focal-proposed 5.4.0.97.101 amd64 [upgradable from: 5.4.0.96.100]
linux-libc-dev/focal-proposed 5.4.0-97.110 amd64 [upgradable from: 5.4.0-96.109]
```

 通常这种都是小版本的更新提示，比如我这里就是安装了 5.4.0-96 然后提示有 5.4.0-97 版本的更新。

升级也非常简单：

```
sudo apt upgrade
```

过程中相对复杂的已有的 dkms 模块要在新内核上重新编译，一般时间会比较长。

升级晚之后重启，然后检查一下 dkms：

```bash
dkms status
iser, 4.9, 5.4.0-94-generic, x86_64: installed
iser, 4.9, 5.4.0-96-generic, x86_64: installed
iser, 4.9, 5.4.0-97-generic, x86_64: installed
isert, 4.9, 5.4.0-94-generic, x86_64: installed
isert, 4.9, 5.4.0-96-generic, x86_64: installed
isert, 4.9, 5.4.0-97-generic, x86_64: installed
kernel-mft-dkms, 4.15.1, 5.4.0-94-generic, x86_64: installed
kernel-mft-dkms, 4.15.1, 5.4.0-96-generic, x86_64: installed
kernel-mft-dkms, 4.15.1, 5.4.0-97-generic, x86_64: installed
knem, 1.1.4.90mlnx1, 5.4.0-94-generic, x86_64: installed
knem, 1.1.4.90mlnx1, 5.4.0-96-generic, x86_64: installed
knem, 1.1.4.90mlnx1, 5.4.0-97-generic, x86_64: installed
mlnx-ofed-kernel, 4.9, 5.4.0-94-generic, x86_64: installed
mlnx-ofed-kernel, 4.9, 5.4.0-96-generic, x86_64: installed
mlnx-ofed-kernel, 4.9, 5.4.0-97-generic, x86_64: installed
rshim, 1.18, 5.4.0-94-generic, x86_64: installed
rshim, 1.18, 5.4.0-96-generic, x86_64: installed
rshim, 1.18, 5.4.0-97-generic, x86_64: installed
srp, 4.9, 5.4.0-94-generic, x86_64: installed
srp, 4.9, 5.4.0-96-generic, x86_64: installed
srp, 4.9, 5.4.0-97-generic, x86_64: installed
```

我这里因为有多个内核版本，所以模块比较多，后面会删除不用的旧版本内核。

### 手动更新大版本

对于大版本更新，需要手工

TBD: 暂时只跑 5.4 LTS 版本，先不升级大版本。

