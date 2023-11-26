---
title: "西数sn550测试数据"
linkTitle: "西数sn520"
weight: 140
date: 2021-03-03
description: >
  西数 sn550 测试数据
---

硬盘型号 西数 sn520，容量 500 GB , m2 接口 nvme 协议， pcie 3.0 x4。

## 技嘉 x99 + ubuntu 20.04

主板为技嘉 x99 ud4 主板, m2 接口 pcie 3.0 x4。但这个主板自带的 m2 接口速度只有 10G，不是正常满血 pcie 3.0 x4 的 32G。

安装 ubuntu 20.04 操作系统，5.4 内核。

### 基本信息

```bash
$ lspci | grep Non-Volatile 

06:00.0 Non-Volatile memory controller: Sandisk Corp WD Black 2018/PC SN520 NVMe SSD (rev 01)
```

磁盘数据：

```bash
$ sudo fdisk -l

Disk /dev/nvme1n1: 465.78 GiB, 500107862016 bytes, 976773168 sectors
Disk model: WDC WDS500G1B0C-00S6U0                  
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: AB8D3D88-0124-404E-B1BC-658E567C569D

Device             Start       End   Sectors  Size Type
/dev/nvme1n1p1      2048   1050623   1048576  512M EFI System
/dev/nvme1n1p2   1050624 839911423 838860800  400G Linux filesystem
/dev/nvme1n1p3 839911424 976771071 136859648 65.3G Linux filesystem
```

测试分区为 nvme1n1p2，分区信息：

```bash
$ sudo hdparm /dev/nvme1n1p2

/dev/nvme1n1p2:
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 readonly      =  0 (off)
 readahead     = 256 (on)
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 geometry      = 409600/64/32, sectors = 838860800, start = 1050624
```

### 速度测试

硬盘写入速度：

```bash
$ dd if=/dev/zero of=/home/sky/temp/test1.img bs=1G count=10 oflag=dsync

10+0 records in
10+0 records out
10737418240 bytes (11 GB, 10 GiB) copied, 23.3803 s, 459 MB/s
```

只有 459 MB/s，低的有点出乎意外，虽然是块笔记本用的 ssd 硬盘，但这个速度也没比机械硬盘快多少。

持续写入 100G的性能，还是 454 MB/s：

```bash
$ dd if=/dev/zero of=/home/sky/temp/test1.img bs=1G count=100 oflag=dsync

100+0 records in
100+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 236.347 s, 454 MB/s
```

硬盘读取速度（直接以 8M 的 block size 读取 100GB 大文件）：

```bash
$ dd if=/home/sky/temp/test1.img of=/dev/null bs=8M

dd if=/home/sky/temp/test1.img of=/dev/null bs=8M
12800+0 records in
12800+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 131.35 s, 817 MB/s
```

817 MB/s ，看来这就是这块硬盘的最大速度了，10G 接口对它而言反而不是瓶颈。

对比

```bash
$ sudo hdparm -tT /dev/nvme1n1p2

/dev/nvme1n1p2:
 Timing cached reads:   19496 MB in  1.99 seconds = 9792.62 MB/sec
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 Timing buffered disk reads: 2298 MB in  3.00 seconds = 765.56 MB/sec
```

765.56 MB/sec 这个数据还比较符合实际测试性能。

## 总结

这个 sn520 硬盘的性能实在不堪，二手出掉吧。
