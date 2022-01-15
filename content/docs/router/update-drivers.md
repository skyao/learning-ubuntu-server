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

- mlnx-en-4.9-4.1.7.0-ubuntu20.04-x86_64.tgz 文件
- MLNX_OFED_LINUX-4.9-4.1.7.0-ubuntu20.04-x86_64.tgz 文件

### 准备工作

参考：

- https://ixnfo.com/en/how-to-update-the-mellanox-connectx-driver.html

检查当前默认驱动：

```bash
$ sudo su
$ modinfo mlx4_core | grep version
version:        4.0-0
srcversion:     CD88194143D98D15E719CD7
vermagic:       5.4.0-94-generic SMP mod_unload modversions

$ modinfo mlx4_core | grep ^version:|sed 's/version: * //g'
4.0-0
```

在操作前，网卡最好连接好网线，否则ifconfig会看不到网卡信息，或者需要加 -a 参数：

```bash
$ ifconfig -a

ens1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.0.20  netmask 255.255.255.0  broadcast 10.0.0.255
        inet6 fe80::4a0f:cfff:fef7:89c1  prefixlen 64  scopeid 0x20<link>
        ether 48:0f:cf:f7:89:c1  txqueuelen 1000  (Ethernet)
        RX packets 2196  bytes 166931 (166.9 KB)
        RX errors 0  dropped 1577  overruns 0  frame 0
        TX packets 418  bytes 48817 (48.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

$ ethtool -i ens1
driver: mlx4_en
version: 4.0-0
firmware-version: 2.42.5700
expansion-rom-version: 
bus-info: 0000:b3:00.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: yes
```

查看HP544+网卡的设备信息：

```bash
$ lspci -vvv
$ lspci | grep Mellanox
b3:00.0 Network controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]

$ lspci -vv -s b3:00.0 | grep "Part number" -A 3
			[PN] Part number: 764285-B21
			[EC] Engineering changes: A3
			[SN] Serial number: IL254902M0
			[V0] Vendor specific: Alom FDR x8 13W
```

### 更新驱动

从其他机器上复制文件:

```bash
scp sky@10.0.0.10:/home/sky/data/samba/MLNX_OFED_LINUX-4.9-4.1.7.0-ubuntu20.04-x86_64.tgz .
scp sky@10.0.0.10:/home/sky/data/samba/mlnx-en-4.9-4.1.7.0-ubuntu20.04-x86_64.tgz .
```

解压缩：

```bash
tar -xvf MLNX_OFED_LINUX-4.9-4.1.7.0-ubuntu20.04-x86_64.tgz
cd MLNX_OFED_LINUX-4.9-4.1.7.0-ubuntu20.04-x86_64
```

执行安装命令：

```bash
$ ./mlnxofedinstall

Logs dir: /tmp/MLNX_OFED_LINUX.18504.logs
General log file: /tmp/MLNX_OFED_LINUX.18504.logs/general.log

Below is the list of MLNX_OFED_LINUX packages that you have chosen
(some may have been added by the installer due to package dependencies):

ofed-scripts
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

This program will install the MLNX_OFED_LINUX package on your machine.
Note that all other Mellanox, OEM, OFED, RDMA or Distribution IB packages will be removed.
Those packages are removed due to conflicts with MLNX_OFED_LINUX, do not reinstall them.

Do you want to continue?[y/N]:y

Checking SW Requirements...
One or more required packages for installing MLNX_OFED_LINUX are missing.
Attempting to install the following missing packages:
quilt automake dpatch libgfortran4 flex dkms gcc make autoconf chrpath tcl gfortran swig autotools-dev graphviz tk m4 libnl-route-3-200 libltdl-dev debhelper bison pkg-config
Removing old packages...
Installing new packages
Installing ofed-scripts-4.9...
Installing mlnx-ofed-kernel-utils-4.9...
Installing mlnx-ofed-kernel-dkms-4.9...

Installing rshim-dkms-1.18...
Installing iser-dkms-4.9...
Installing isert-dkms-4.9...
Installing srp-dkms-4.9...
Installing libibverbs1-41mlnx1...
Installing ibverbs-utils-41mlnx1...
Installing libibverbs-dev-41mlnx1...
Installing libibverbs1-dbg-41mlnx1...
Installing libmlx4-1-41mlnx1...
Installing libmlx4-dev-41mlnx1...
Installing libmlx4-1-dbg-41mlnx1...
Installing libmlx5-1-41mlnx1...
Installing libmlx5-dev-41mlnx1...
Installing libmlx5-1-dbg-41mlnx1...
Installing libibumad-43.1.1.MLNX20200211.078947f...
Installing libibumad-static-43.1.1.MLNX20200211.078947f...
Installing libibumad-devel-43.1.1.MLNX20200211.078947f...
Installing ibacm-41mlnx1...
Installing ibacm-dev-41mlnx1...
Installing librdmacm1-41mlnx1...
Installing librdmacm-utils-41mlnx1...
Installing librdmacm-dev-41mlnx1...
Installing mstflint-4.14.0...
Installing ibdump-6.0.0...
Installing libibmad-5.4.0.MLNX20190423.1d917ae...
Installing libibmad-static-5.4.0.MLNX20190423.1d917ae...
Installing libibmad-devel-5.4.0.MLNX20190423.1d917ae...
Installing libopensm-5.7.2.MLNX20201014.9378048...
Installing opensm-5.7.2.MLNX20201014.9378048...
Installing opensm-doc-5.7.2.MLNX20201014.9378048...
Installing libopensm-devel-5.7.2.MLNX20201014.9378048...
Installing infiniband-diags-5.6.0.MLNX20200211.354e4b7...
Installing infiniband-diags-compat-5.6.0.MLNX20200211.354e4b7...
Installing mft-4.15.1...
Installing kernel-mft-dkms-4.15.1...
Installing libibcm1-41mlnx1...
Installing libibcm-dev-41mlnx1...
Installing perftest-4.5.0.mlnxlibs...
Installing ibutils2-2.1.1...
Installing libibdm1-1.5.7.1...
Installing ibutils-1.5.7.1...
Installing ar-mgr-1.0...
Installing dump-pr-1.0...
Installing ibsim-0.10...
Installing ibsim-doc-0.10...
Installing ucx-1.8.0...
Installing sharp-2.1.2.MLNX20200428.ddda184...
Installing hcoll-4.4.2968...
Installing knem-dkms-1.1.4.90mlnx1...
Installing knem-1.1.4.90mlnx1...
Installing openmpi-4.0.3rc4...
Installing mpitests-3.2.20...
Installing libdapl2-2.1.10.1.mlnx...
Installing dapl2-utils-2.1.10.1.mlnx...
Installing libdapl-dev-2.1.10.1.mlnx...
Installing srptools-41mlnx1...
Installing mlnx-ethtool-5.4...
Installing mlnx-iproute2-5.4.0...
Selecting previously unselected package mlnx-fw-updater.
(Reading database ... 87526 files and directories currently installed.)
Preparing to unpack .../mlnx-fw-updater_4.9-4.1.7.0_amd64.deb ...
Unpacking mlnx-fw-updater (4.9-4.1.7.0) ...
Setting up mlnx-fw-updater (4.9-4.1.7.0) ...

Added 'RUN_FW_UPDATER_ONBOOT=no to /etc/infiniband/openib.conf

Initializing...
Attempting to perform Firmware update...

The firmware for this device is not distributed inside Mellanox driver: b3:00.0 (PSID: HP_1380110017)
To obtain firmware for this device, please contact your HW vendor.

Failed to update Firmware.
See /tmp/MLNX_OFED_LINUX.18504.logs/fw_update.log
Device (b3:00.0):
	b3:00.0 Network controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]
	Link Width: x8
	PCI Link Speed: 8GT/s

Installation passed successfully
To load the new driver, run:
/etc/init.d/openibd restart
```

上面的 `Failed to update Firmware.` 错误可以忽略。重启设备驱动：


```bash
/etc/init.d/openibd restart

Unloading HCA driver:                                      [  OK  ]
Loading HCA driver and Access Layer:                       [  OK  ]
```

重启机器，然后检验：

```bash
$ modinfo mlx4_core | grep version

version:        4.9-4.1.7
srcversion:     B7B1BFEEF8DC7BE5A999C14
vermagic:       5.4.0-94-generic SMP mod_unload modversions 
```

可以看到现在的驱动版本已经从默认安装的 4.0-0 变成了 4.9-4.1.7 。

### ~~linux mint~~

在基于 ubuntu 20.04 内核的 linux mint 20.02 版本上更新驱动。但奈何没能搞定，各种错误，最后放弃，重新安装回原版的ubuntu 20.04.

以下记录仅作为归档。

直接执行命令会报错，因为默认有linux发行版本的检查：

```bash
./mlnxofedinstall
Current operation system is not supported (linuxmint20.2)!
```

解决的方式之一是通过命令行参数 `--distro` 传递发行版本信息进去：

```bash
./mlnxofedinstall --distro ubuntu20.04

Removing old packages...
Installing new packages
Installing ofed-scripts-4.9...
Installing mlnx-ofed-kernel-utils-4.9...
Installing mlnx-ofed-kernel-dkms-4.9...
Failed to install mlnx-ofed-kernel-dkms DEB
Collecting debug info...
See /tmp/MLNX_OFED_LINUX.11004.logs/mlnx-ofed-kernel-dkms.debinstall.log
```

遇到报错，`/tmp/MLNX_OFED_LINUX.11004.logs/mlnx-ofed-kernel-dkms.debinstall.log` 中的信息为:

```bash
/usr/bin/dpkg -i --force-confnew --force-confmiss /home/sky/hp544/MLNX_OFED_LINUX-4.9-4.1.7.0-ubuntu20.04-x86_64/DEBS/MLNX_LIBS/mlnx-ofed-kernel-dkms_4.9-OFED.4.9.4.1.7.1_all.deb
Selecting previously unselected package mlnx-ofed-kernel-dkms.
(Reading database ... 322968 files and directories currently installed.)
Preparing to unpack .../mlnx-ofed-kernel-dkms_4.9-OFED.4.9.4.1.7.1_all.deb ...
Unpacking mlnx-ofed-kernel-dkms (4.9-OFED.4.9.4.1.7.1) ...
Setting up mlnx-ofed-kernel-dkms (4.9-OFED.4.9.4.1.7.1) ...
Loading new mlnx-ofed-kernel-4.9 DKMS files...
First Installation: checking all kernels...
Building only for 5.4.0-92-generic
Building for architecture x86_64
Building initial module for 5.4.0-92-generic
Error! Bad return status for module build on kernel: 5.4.0-92-generic (x86_64)
Consult /var/lib/dkms/mlnx-ofed-kernel/4.9/build/make.log for more information.
dpkg: error processing package mlnx-ofed-kernel-dkms (--install):
 installed mlnx-ofed-kernel-dkms package post-installation script subprocess returned error exit status 10
Errors were encountered while processing:
 mlnx-ofed-kernel-dkms
```

`/var/lib/dkms/mlnx-ofed-kernel/4.9/build/make.log` 文件中的信息：

```bash
Copying build sources from '/var/lib/dkms/mlnx-ofed-kernel/4.9/build/../build' to '/usr/src/ofa_kernel/5.4.0-92-generic' ...
/bin/cp: cannot stat 'Module*.symvers': No such file or directory
```

检查这台机器上的相关信息：

```bash
$ uname --all
Linux skyserver3 5.4.0-94-generic #106-Ubuntu SMP Thu Jan 6 23:58:14 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
$ ls /usr/src/
linux-headers-5.4.0-94  linux-headers-5.4.0-94-generic  mlnx-ofed-kernel-4.9  ofa_kernel  ofa_kernel-4.9
$ ls /lib/modules/
5.4.0-94-generic
$ locate Module.symvers
# 注意这里没有信息
```

对照成功安装驱动的ubuntu server 20.04 机器上的信息：

```bash
$ uname --all
Linux skywork2 5.4.0-94-generic #106-Ubuntu SMP Thu Jan 6 23:58:14 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
$ ls /usr/src/
iser-4.9                linux-headers-5.4.0-94          ofa_kernel-4.9
isert-4.9               linux-headers-5.4.0-94-generic  rshim-1.18
kernel-mft-dkms-4.15.1  mlnx-ofed-kernel-4.9            srp-4.9
knem-1.1.4.90mlnx1      ofa_kernel
$ ls /lib/modules/
5.4.0-94-generic
$ locate Module.symvers
/usr/src/linux-headers-5.4.0-94-generic/Module.symvers
/usr/src/ofa_kernel/5.4.0-94-generic/Module.symvers
/usr/src/ofa_kernel/5.4.0-94-generic/compat/build/Module.symvers
```

执行 `locate Module.symvers` 时发现没有列出信息：

```bash
$ locate Module.symvers
```



```bash
# 事实上python3已经安装好了
sudo apt-get install python3
# 但是python命令不存在，需要通过python-is-python3来把python命令按照python3来执行
sudo apt-get install python-is-python3
# 安装过程中需要用到 distutils
sudo apt-get install python3-distutils

$ 开始安装
$./mlnxofedinstall  --distro ubuntu20.04 --without-fw-update

Logs dir: /tmp/MLNX_OFED_LINUX.1976.logs
General log file: /tmp/MLNX_OFED_LINUX.1976.logs/general.log

Below is the list of MLNX_OFED_LINUX packages that you have chosen
(some may have been added by the installer due to package dependencies):

ofed-scripts
mlnx-ofed-kernel-utils
mlnx-ofed-kernel-dkms
......
This program will install the MLNX_OFED_LINUX package on your machine.
Note that all other Mellanox, OEM, OFED, RDMA or Distribution IB packages will be removed.
Those packages are removed due to conflicts with MLNX_OFED_LINUX, do not reinstall them.

Do you want to continue?[y/N]:y

Checking SW Requirements...


One or more required packages for installing MLNX_OFED_LINUX are missing.
Attempting to install the following missing packages:
gfortran graphviz tcl swig chrpath dpatch debhelper libltdl-dev libgfortran4 tk automake quilt autotools-dev autoconf


Removing old packages...
Installing new packages
Installing ofed-scripts-4.9...
Installing mlnx-ofed-kernel-utils-4.9...
Installing mlnx-ofed-kernel-dkms-4.9...


Error: mlnx-ofed-kernel-dkms installation failed!
Collecting debug info...

See:
	/tmp/MLNX_OFED_LINUX.1976.logs/mlnx-ofed-kernel-dkms.debinstall.log
Removing newly installed packages...
```

但奇怪的是 `/tmp/MLNX_OFED_LINUX.1976.logs/mlnx-ofed-kernel-dkms.debinstall.log` 日志文件中并没有报错，反而是显示 `DKMS: install completed.` 

```py
./mlnxofedinstall  --distro ubuntu20.04 
./mlnxofedinstall  --distro ubuntu20.04 --without-fw-update
./mlnxofedinstall --add-kernel-support --distro ubuntu20.04 --skip-repo
```

参考 https://docs.nvidia.com/networking/display/MLNXOFEDv494080/Installing+Mellanox+OFED

```bash
./mlnxofedinstall --without-dkms --add-kernel-support --kernel 3.13.0-85-generic --without-fw-update --force
```

失败但是日子没有错误信息，实在无力再拍错了。

退回去用 ubuntu 原版了。

