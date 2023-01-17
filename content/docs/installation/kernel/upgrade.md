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

对于大版本更新，需要手工。

考虑到 22.04 版本不稳定，试用之下发现有一些莫名其妙的问题，不想折腾，继续试用 20.04版本，但是希望可以把内核从 5.4 升级到更新的版本，比如 5.15.

先看一下有哪些版本可以选择：

```bash
apt update
apt list linux-headers-5.15.*-*-generic linux-image-5.15.*-*-generic

linux-headers-5.15.0-33-generic/focal-updates,focal-security 5.15.0-33.34~20.04.1 amd64
linux-headers-5.15.0-41-generic/focal-updates,focal-security 5.15.0-41.44~20.04.1 amd64
linux-headers-5.15.0-43-generic/focal-updates,focal-security 5.15.0-43.46~20.04.1 amd64
linux-headers-5.15.0-46-generic/focal-updates,focal-security 5.15.0-46.49~20.04.1 amd64
linux-headers-5.15.0-48-generic/focal-updates,focal-security 5.15.0-48.54~20.04.1 amd64
linux-headers-5.15.0-50-generic/focal-updates,focal-security 5.15.0-50.56~20.04.1 amd64
linux-headers-5.15.0-52-generic/focal-updates,focal-security 5.15.0-52.58~20.04.1 amd64
linux-headers-5.15.0-53-generic/focal-updates,focal-security 5.15.0-53.59~20.04.1 amd64
linux-headers-5.15.0-56-generic/focal-updates,focal-security 5.15.0-56.62~20.04.1 amd64
linux-headers-5.15.0-57-generic/focal-updates,focal-security 5.15.0-57.63~20.04.1 amd64
linux-headers-5.15.0-58-generic/focal-updates,focal-security 5.15.0-58.64~20.04.1 amd64
linux-image-5.15.0-33-generic/focal-updates,focal-security 5.15.0-33.34~20.04.1 amd64
linux-image-5.15.0-41-generic/focal-updates,focal-security 5.15.0-41.44~20.04.1 amd64
linux-image-5.15.0-43-generic/focal-updates,focal-security 5.15.0-43.46~20.04.1 amd64
linux-image-5.15.0-46-generic/focal-updates,focal-security 5.15.0-46.49~20.04.1 amd64
linux-image-5.15.0-48-generic/focal-updates,focal-security 5.15.0-48.54~20.04.1 amd64
linux-image-5.15.0-50-generic/focal-updates,focal-security 5.15.0-50.56~20.04.1 amd64
linux-image-5.15.0-52-generic/focal-updates,focal-security 5.15.0-52.58~20.04.1 amd64
linux-image-5.15.0-53-generic/focal-updates,focal-security 5.15.0-53.59~20.04.1 amd64
linux-image-5.15.0-56-generic/focal-updates,focal-security 5.15.0-56.62~20.04.1 amd64
linux-image-5.15.0-57-generic/focal-updates,focal-security 5.15.0-57.63~20.04.1 amd64
linux-image-5.15.0-58-generic/focal-updates,focal-security 5.15.0-58.64~20.04.1 amd64
```

试试最新的 5.15.0-58

```bash
sudo apt install linux-headers-5.15.0-58-generic linux-image-5.15.0-58-generic
```

安装完成后重启，检查：

```bash
$ uname -a
Linux skyserver 5.15.0-58-generic #64~20.04.1-Ubuntu SMP Fri Jan 6 16:42:31 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux

$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.5 LTS
Release:	20.04
Codename:	focal
```

参考资料：

- https://sysin.org/blog/ubuntu-2004-upgrade-kernel/
