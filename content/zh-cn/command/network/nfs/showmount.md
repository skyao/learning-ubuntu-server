---
title: "showmount命令"
weight: 40
date: 2022-03-20
description: >
  showmount用来显示nfs服务器的挂载信息
---

## 使用

```bash
$ showmount --exports 192.168.0.40 
Export list for 192.168.0.40:
/mnt/d         192.168.0.0/16
/mnt/nfs-share 192.168.0.0/16
```



## 资料

- [ubuntu手册中的showmount命令详细说明](http://manpages.ubuntu.com/manpages/focal/man8/showmount.8.html)
