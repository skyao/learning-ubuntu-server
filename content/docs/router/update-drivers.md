---
title: "更新HP 544网卡驱动"
linkTitle: "更新HP 544网卡驱动"
weight: 10010
date: 2022-01-05
description: >
  更新HP 544网卡驱动
---



### 驱动下载

HP 544网卡linux驱动下载地址：

https://www.mellanox.com/products/ethernet-drivers/linux/mlnx_en

https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed

选择 "LTS Download", 目前只有 4.9-4.1.70 版本可以下载，选择 ubuntu 20.04 x86_64,下载:

- [mlnx-en-4.9-4.1.7.0-ubuntu20.04-x86_64.tgz](http://www.mellanox.com/downloads/ofed/MLNX_EN-4.9-4.1.7.0/mlnx-en-4.9-4.1.7.0-ubuntu20.04-x86_64.tgz) 文件
- [MLNX_OFED_LINUX-4.9-4.1.7.0-ubuntu20.04-x86_64.tgz](http://www.mellanox.com/page/mlnx_ofed_eula?mtag=linux_sw_drivers&mrequest=downloads&mtype=ofed&mver=MLNX_OFED-4.9-4.1.7.0&mname=MLNX_OFED_LINUX-4.9-4.1.7.0-ubuntu20.04-x86_64.tgz) 文件

### 更新en驱动

参考：

- https://ixnfo.com/en/how-to-update-the-mellanox-connectx-driver.html

检查当前默认驱动：

```bash
$ sudo su
$ modinfo mlx4_core | grep version
version:        4.0-0
srcversion:     B73874CD84AFA9FA6FC4D0F
vermagic:       5.13.0-23-generic SMP mod_unload modversions 

$ modinfo mlx4_core | grep ^version:|sed 's/version: * //g'
4.0-0
```

切记在操作前，网卡要连接好网线，否则操作系统中会看不到网卡信息。

```bash
$ ifconfig

ens4: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::e6b7:e646:836b:ffe6  prefixlen 64  scopeid 0x20<link>
        ether 24:be:05:bd:08:01  txqueuelen 1000  (Ethernet)
        RX packets 43  bytes 8722 (8.7 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 207  bytes 37215 (37.2 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

$ ethtool -i ens4
driver: mlx4_en
version: 4.0-0
firmware-version: 2.42.5700
expansion-rom-version: 
bus-info: 0000:81:00.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: yes
```



```bash
$ lspci -vvv
$ lspci | grep Mellanox
81:00.0 Network controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]

$ lspci -vv -s 81:00.0 | grep "Part number" -A 3
			[PN] Part number: 764285-B21
			[EC] Engineering changes: A3
			[SN] Serial number: IL24440355
			[V0] Vendor specific: Alom FDR x8 13W
```



### 更新ofed驱动



```bash
Checking SW Requirements...
One or more required packages for installing MLNX_OFED_LINUX are missing.
Attempting to install the following missing packages:
tk tcl libgfortran4 gfortran
Removing old packages...
Installing new packages
Installing ofed-scripts-4.9...
Installing mlnx-ofed-kernel-utils-4.9...
Installing mlnx-ofed-kernel-dkms-4.9...
Failed to install mlnx-ofed-kernel-dkms DEB
Collecting debug info...
See /tmp/MLNX_OFED_LINUX.28066.logs/mlnx-ofed-kernel-dkms.debinstall.log
```



```bash
$ apt install tk bison debhelper flex autoconf automake graphviz dpatch gfortran libgfortran4 quilt dkms chrpath tcl m4 swig libnl-route-3-dev
```



```bash
$ apt install ofed-scripts rshim-dkms iser-dkms isert-dkms srp-dkms libibverbs1


mlnx-ofed-kernel-utils
mlnx-ofed-kernel-dkms
rshim-dkms
iser-dkms
isert-dkms
srp-dkms
libibverbs1
ibverbs-utils
libibverbs-dev
libibverbs1-dbg
libmlx4-1
libmlx4-dev
libmlx4-1-dbg
libmlx5-1
libmlx5-dev
libmlx5-1-dbg
libibumad
libibumad-static
libibumad-devel
ibacm
ibacm-dev
librdmacm1
librdmacm-utils
librdmacm-dev
mstflint
ibdump
libibmad
libibmad-static
libibmad-devel
libopensm
opensm
opensm-doc
libopensm-devel
infiniband-diags
infiniband-diags-compat
mft
kernel-mft-dkms
libibcm1
libibcm-dev
perftest
ibutils2
libibdm1
ibutils
ar-mgr
dump-pr
ibsim
ibsim-doc
ucx
sharp
hcoll
knem-dkms
knem
openmpi
mpitests
libdapl2
dapl2-utils
libdapl-dev
srptools
mlnx-ethtool
mlnx-iproute2

```



https://nofu.jp/wiki/virtualization/proxmox_ve_6_3_how_to_build_latest_mlnx_ofed

