---
title: "隐速4TN7000测试数据"
linkTitle: "隐速4TN7000"
weight: 140
date: 2021-03-03
description: >
  隐速4TN7000测试数据
---

硬盘型号 隐速4TN7000，容量 4T , m2 接口 nvme 协议， pcie 4.0 x4， 规格为 2280。

## 技嘉x99 ud4 + ubuntu server

主板为技嘉x99 ud4, 通过pcie拆分卡得到了一个 m2 接口 pcie 4.0 x4。

安装 ubuntu 20.04 系统，5.4 内核, ext4 格式。

### 基本信息

```bash
$ lspci | grep Volatile

03:00.0 Non-Volatile memory controller: Device 1e4b:1602 (rev 01)
```

磁盘数据：

```bash
$ sudo fdisk -l

Disk /dev/nvme0n1: 3.74 TiB, 4096805658624 bytes, 8001573552 sectors
Disk model: YSSDHB-4TN7000                          
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: DA503D82-D785-4A47-A701-504DB1ED256C

Device         Start        End    Sectors  Size Type
/dev/nvme0n1p1  2048 8001573518 8001571471  3.7T Linux filesystem
```

测试分区为 nvme0n1p1，分区信息：

```bash
$ sudo hdparm /dev/nvme0n1p1

/dev/nvme0n1p1:
 readonly      =  0 (off)
 readahead     = 256 (on)
 geometry      = 3907017/64/32, sectors = 8001571471, start = 2048
```

### 速度测试

硬盘写入速度：

```bash
$ sudo dd if=/dev/zero of=/data/temp/test1.img bs=1G count=10 oflag=dsync
10+0 records in
10+0 records out
10737418240 bytes (11 GB, 10 GiB) copied, 11.6533 s, 921 MB/s
```

只有 0.9 GB，低的离谱，虽然主板限制只能跑pcie 3.0 x4 32G的带宽, 但按说写入速度应该是3-4GB级别，就算打折扣也不应该这么低。参考爱国者p7000z的速度是 1.8GB/秒。

读取速度为 6.6GB/S，这个还行，应该是缓存：

```bash
$ dd if=/data/temp/test1.img of=/dev/null bs=8M
1280+0 records in
1280+0 records out
10737418240 bytes (11 GB, 10 GiB) copied, 1.62146 s, 6.6 GB/s
```

持续写入 100G的文件，还是 0.9 GB/秒：

```bash
$ sudo dd if=/dev/zero of=/data/temp/test1.img bs=1G count=100 oflag=dsync

100+0 records in
100+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 118.151 s, 909 MB/s
```

硬盘读取速度（直接以 8M 的 block size 读取 100GB 大文件）：

```bash
$ dd if=/data/temp/test1.img of=/dev/null bs=8M                           
12800+0 records in
12800+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 37.7508 s, 2.8 GB/s
```

2.8 GB的速度有点低，对比爱国者p7000z 是 4.0GB/s 。

持续写入 1T的文件，还是 0.9 GB/秒，看来缓外速度就真的只有900M了：

```bash
$ sudo dd if=/dev/zero of=/data/temp/test-1t.img bs=1G count=1000 oflag=dsync
1000+0 records in
1000+0 records out
1073741824000 bytes (1.1 TB, 1000 GiB) copied, 1221.81 s, 879 MB/s
```

硬盘读取速度（以 8M 的 block size 读取 1 TB 大文件）：

```bash
dd if=/data/temp/test-1t.img of=/dev/null bs=8M     
128000+0 records in
128000+0 records out
1073741824000 bytes (1.1 TB, 1000 GiB) copied, 368.641 s, 2.9 GB/s
```

看来 2.8/2.9G 就是 pcie3 下的无缓存极限速度了。

## 华硕z690 + windows10

TBD

## 华硕z690 + ubuntu

TBD

## 速度参考资料

- [4T只需999，移速 黑豹 PCIe4.0固态硬盘 实测分享](https://post.smzdm.com/p/agq7kov3/)
