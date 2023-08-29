---
title: "三星980测试数据"
linkTitle: "三星980"
weight: 150
date: 2021-03-03
description: >
  三星 980 测试数据
---

## 硬盘信息

硬盘型号 三星980，容量 1 T, m2 接口 nvme 协议， pcie 3.0 x4。

三星官方给到980 1TB的参数为：

- pcie3.0×4，nvme1.4，HMB无缓设计
- 最大读：3500MB/s
- 最大写：3000MB/s
- 最大随读：50W IOPS
- 最大随写：48W IOPS

相关资料参考：

- [细狗真不行丨三星980固态硬盘测评报告，凭啥你卖这么多？](https://www.zhihu.com/tardis/zm/art/571432182?source_id=1003)
- [标杆家的无缓——Samsung 980 1TB评测](https://post.smzdm.com/p/a5dq242x/)
- [SAMSUNG 980 1TB實測開箱，NVMe M.2高效能固態硬碟！](http://www.pcdiy.com.tw/detail/21558)
- [三星NVMe SSD 980深度评测：极致性价比，开启无DRAM SSD时代 ](https://www.sohu.com/a/498956720_120489718)

## 技嘉 x99 + ubuntu 20.04

主板为技嘉 x99 ud4 主板, m2 接口 pcie 3.0 x4。

安装 ubuntu 20.04 操作系统，5.4 内核。

### 基本信息

```bash
$ lspci | grep Non-Volatile 

03:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd Device a809
```

磁盘数据：

```bash
$ sudo fdisk -l

Disk /dev/nvme0n1: 931.53 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: Samsung SSD 980 1TB                     
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 16384 bytes / 131072 bytes
Disklabel type: gpt
Disk identifier: DB6DCF2B-7BCD-4C10-8B72-B95EF2DD634A

Device         Start        End    Sectors   Size Type
/dev/nvme0n1p1  2048 1953525134 1953523087 931.5G Linux filesystem
```

测试分区为 nvme0n1p1，分区信息：

```bash
$ sudo hdparm /dev/nvme0n1p1

/dev/nvme0n1p1:
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 readonly      =  0 (off)
 readahead     = 256 (on)
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 geometry      = 953868/64/32, sectors = 1953523087, start = 2048
```

### 速度测试

硬盘写入速度：

```bash
$ dd if=/dev/zero of=/mnt/data/share/temp/test1.img bs=1G count=10 oflag=dsync

10+0 records in
10+0 records out
10737418240 bytes (11 GB, 10 GiB) copied, 11.9802 s, 896 MB/s
```

只有 896 MB/s，低的有点出乎意外，这个标称写入速度是3G啊。

持续写入 100G的性能，这次只有 794 MB/s：

```bash
$ dd if=/dev/zero of=/mnt/data/share/temp/test1.img bs=1G count=100 oflag=dsync

100+0 records in
100+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 135.252 s, 794 MB/s
```

硬盘读取速度（直接以 8M 的 block size 读取 100GB 大文件）：

```bash
$ dd if=/mnt/data/share/temp/test1.img of=/dev/null bs=8M

12800+0 records in
12800+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 44.1279 s, 2.4 GB/s
```

2.4 GB/s ，和宣称的 3G 速度有一点出入，但好在没有离谱。

对比

```bash
$ sudo hdparm -tT /dev/nvme0n1p1

/dev/nvme0n1p1:
 Timing cached reads:   20218 MB in  1.99 seconds = 10157.69 MB/sec
 HDIO_DRIVE_CMD(identify) failed: Inappropriate ioctl for device
 Timing buffered disk reads: 5302 MB in  3.00 seconds = 1767.28 MB/sec
```

1767.28 MB/sec 这个数据比实际测试性能（2.4 GB/s）要小一点。

## 总结

这个 三星 980 硬盘按说是 pcie3.0 中高端硬盘，宣称的读写性能应该在 3 GB这个级别，接近 pcie3.0 x4 32G 的理论带宽。但实际测试下来，读 2.4 GB/s 勉强还能凑合，写  800M 就差的很远。
