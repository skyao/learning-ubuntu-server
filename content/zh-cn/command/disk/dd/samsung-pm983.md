---
title: "三星pm983测试数据"
linkTitle: "三星pm983"
weight: 110
date: 2021-03-03
description: >
  三星pm983测试数据
---

硬盘型号 三星 pm983，容量 900G, m2 接口 nvme 协议， pcie 3.0 x4， 规格为 22110。

## 华硕z690 

主板为华硕 z690-p d4, m2 接口 pcie 4.0 x4，受硬盘限制实际为 pcie 3.0 x4。

安装 pve 8.0 系统，6.2 内核。

### 基本信息

```bash
$ lspci | grep SSD 

08:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller SM981/PM981/PM983
```

磁盘数据：

```bash
$ fdisk -l

Disk /dev/nvme1n1: 838.36 GiB, 900185481216 bytes, 219771846 sectors
Disk model: MZ1LB960HBJR-000FB                      
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 131072 bytes / 131072 bytes
Disklabel type: gpt
Disk identifier: 87408245-67E3-47E0-AF90-85B8DDDAD75D

Device             Start       End   Sectors   Size Type
/dev/nvme1n1p1 193462528 219771647  26309120 100.4G Linux filesystem
/dev/nvme1n1p2       256    262399    262144     1G EFI System
/dev/nvme1n1p3    262400 193462272 193199873   737G Linux LVM
```

分区信息：

```bash
$ hdparm /dev/nvme1n1p3

/dev/nvme1n1p3:
 readonly      =  0 (off)
 readahead     = 512 (on)
 geometry      = 754687/64/32, sectors = 1545598984, start = 2099200
```

### 速度测试

 硬盘写入速度：

```bash
$ dd if=/dev/zero of=/root/temp/test1.img bs=1G count=10 oflag=dsync

10+0 records in
10+0 records out
10737418240 bytes (11 GB, 10 GiB) copied, 14.4349 s, 744 MB/s
```

> 只有 744 兆，低的有点出乎意外，应该是缓存耗尽。

 硬盘读取速度：

```bash
dd if=/root/temp/test1.img of=/dev/null bs=8k
```



对比

```bash
hdparm -tT /dev/nvme1n1p3

/dev/nvme1n1p3:
 Timing cached reads:   32260 MB in  2.00 seconds = 16152.14 MB/sec
 Timing buffered disk reads: 5074 MB in  3.00 seconds = 1691.16 MB/sec
```





## 参考资料

- [Linux and Unix Test Disk I/O Performance With dd Command](https://www.cyberciti.biz/faq/howto-linux-unix-test-disk-performance-with-dd-command/)