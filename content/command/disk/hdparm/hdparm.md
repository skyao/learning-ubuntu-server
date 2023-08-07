---
title: "hdparm命令"
linkTitle: "hdparm"
weight: 25
date: 2021-03-03
description: >
  hdparm 命令用于显示与设定硬盘的参数，还可以测试硬盘的读取速度
---



## 介绍

在Linux下可以使用hdparm工具查看硬盘的相关信息或对硬盘进行测速、优化、修改硬盘相关参数设定。

## 安装

一般 ubutu server 都自带 hdparm 命令，如果没有则通过下面的命令安装：

```bash
sudo apt install hdparm
```

## 实践案例

### 查看硬盘的相关设置

```bash
sudo hdparm /dev/sdb1
```

输出为：

```bash
/dev/sdb1:
SG_IO: bad/missing sense data, sb[]:  70 00 05 00 00 00 00 0a 00 00 00 00 20 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
 multcount     =  0 (off)
 readonly      =  0 (off)
 readahead     = 256 (on)
 geometry      = 401023/255/63, sectors = 6442448863, start = 2048
```

其中 geometry 的参数解释为：

> geometry = 401023［柱面数］/ 255［磁头数］/ 63［扇区数］, sectors = 6442448863［总扇区数］, start = 2048［起始扇区数］

### 测试硬盘的读取速度

```bash
sudo hdparm -t /dev/sdb1
```

输出为：

```bash
/dev/sdb1:
SG_IO: bad/missing sense data, sb[]:  70 00 05 00 00 00 00 0a 00 00 00 00 20 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
 Timing buffered disk reads: 9652 MB in  3.00 seconds = 3217.29 MB/sec
```

### 测试硬盘缓存的读取速度

```bash
sudo hdparm -T /dev/sdb1
```

输出为：

```bash
/dev/sdb1:
 Timing cached reads:   25838 MB in  1.99 seconds = 12959.95 MB/sec
```

### 检测硬盘的电源管理模式

```bash
sudo hdparm -C /dev/sdb1
```

输出为：

```bash
/dev/sdb1:
SG_IO: bad/missing sense data, sb[]:  70 00 05 00 00 00 00 0a 00 00 00 00 20 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
 drive state is:  standby
```



## 参考资料

- [Linux hdparm命令](https://www.runoob.com/linux/linux-comm-hdparm.html)
