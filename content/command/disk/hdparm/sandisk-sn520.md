---
title: "sandisk sn520的测试结果"
linkTitle: "sandisk sn520"
weight: 110
date: 2021-03-03
description: >
  sandisk sn520的测试结果
---

硬盘型号 sandisk sn520，容量 512G, m2 接口 nvme 协议， pcie 3.0 x4。

## 技嘉x99 + 10G M2

在技嘉 x99 ud4 主板上，m2 接口为 10G 带宽。

物理机安装 ubuntu 20.04 系统， 5.4 内核。

### 基本信息

```bash
$ lspci | grep disk
05:00.0 Non-Volatile memory controller: Sandisk Corp WD Black 2018/PC SN520 NVMe SSD (rev 01)
```

磁盘数据：

```bash
Disk /dev/nvme0n1: 465.78 GiB, 500107862016 bytes, 976773168 sectors
Disk model: WDC WDS500G1B0C-00S6U0                  
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: AB8D3D88-0124-404E-B1BC-658E567C569D

Device             Start       End   Sectors  Size Type
/dev/nvme0n1p1      2048   1050623   1048576  512M EFI System
/dev/nvme0n1p2   1050624 839911423 838860800  400G Linux filesystem
/dev/nvme0n1p3 839911424 976771071 136859648 65.3G Linux filesystem
```

分区信息：

```bash
sudo hdparm /dev/nvme0n1p3


/dev/nvme0n1p3:
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 readonly      =  0 (off)
 readahead     = 256 (on)
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 geometry      = 66826/64/32, sectors = 136859648, start = 839911424
```

### 速度测试

读取速度测试：

```bash
sudo hdparm -t /dev/nvme0n1p3


/dev/nvme0n1p3:
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 Timing buffered disk reads: 2294 MB in  3.00 seconds = 764.43 MB/sec
```

缓存读取速度测试：

```bash
sudo hdparm -T /dev/nvme0n1p3


/dev/nvme0n1p3:
 Timing cached reads:   20412 MB in  1.99 seconds = 10261.23 MB/sec
```

