---
title: "dd命令介绍"
linkTitle: "dd介绍"
weight: 20
date: 2021-03-03
description: >
  Linux dd 命令介绍
---



## 介绍



## 使用

### 测试硬盘读取速度

```bash
dd if=/dev/zero of=/tmp/test1.img bs=1G count=1 oflag=dsync

dd if=/dev/zero of=/root/temp/test1.img bs=10G count=1 oflag=dsync
```





## 参考资料

- [Linux and Unix Test Disk I/O Performance With dd Command](https://www.cyberciti.biz/faq/howto-linux-unix-test-disk-performance-with-dd-command/)