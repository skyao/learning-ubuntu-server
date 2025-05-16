---
title: "爱国者p7000z测试数据"
linkTitle: "爱国者p7000z"
weight: 120
date: 2021-03-03
description: >
  爱国者p7000z 测试数据
---

硬盘型号 爱国者p7000z，容量 4T , m2 接口 nvme 协议， pcie 4.0 x4， 规格为 2280。

## 华硕z690 + pve 8

主板为华硕 z690-p d4, m2 接口 pcie 4.0 x4。

安装 ubuntu 20.04 系统，5.4 内核, ext4 格式。

### 基本信息

```bash
$ lspci | grep Volatile

02:00.0 Non-Volatile memory controller: Device 1e4b:1602 (rev 01)

```

磁盘数据：

```bash
$ sudo fdisk -l

Disk /dev/nvme0n1: 3.74 TiB, 4096805658624 bytes, 8001573552 sectors
Disk model: aigo NVMe SSD P7000Z 4TB
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: EE02EE97-D530-4107-9D83-6A15532005BA

Device              Start        End    Sectors  Size Type
/dev/nvme0n1p1       2048    1050623    1048576  512M EFI System
/dev/nvme0n1p2    1050624 1074792447 1073741824  512G Linux filesystem
/dev/nvme0n1p3 1074792448 1179650047  104857600   50G Linux filesystem
/dev/nvme0n1p4 1179650048 8001570815 6821920768  3.2T Linux filesystem
```

测试分区为 nvme0n1p4，分区信息：

```bash
$ sudo hdparm /dev/nvme0n1p4

/dev/nvme0n1p4:
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 readonly      =  0 (off)
 readahead     = 256 (on)
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 geometry      = 3331016/64/32, sectors = 6821920768, start = 1179650048
```

### 速度测试

硬盘写入速度：

```bash
$ dd if=/dev/zero of=/mnt/data/share/temp/test1.img bs=1G count=10 oflag=dsync

10+0 records in
10+0 records out
10737418240 bytes (11 GB, 10 GiB) copied, 5.96539 s, 1.8 GB/s
```

只有 1.4 GB，有点低的，p7000z 是 pcie 4.0,按说写入速度应该是5-6GB级别。

持续写入 100G的文件，还是 1.8 GB/秒：

```bash
$ dd if=/dev/zero of=/mnt/data/share/temp/test1.img bs=1G count=100 oflag=dsync

100+0 records in
100+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 60.8423 s, 1.8 GB/s
```

硬盘读取速度（直接以 8M 的 block size 读取 100GB 大文件）：

```bash
$ dd if=/mnt/data/share/temp/test1.img of=/dev/null bs=8M
                                             
12800+0 records in
12800+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 26.7172 s, 4.0 GB/s
```

4.0GB/s 的速度只能说差强人意，凑合吧。

