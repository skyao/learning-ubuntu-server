---
title: "nc命令"
weight: 90
date: 2022-03-20
description: >
  netcat命令
---



## 使用

检查端口：

```bash
# 查看单个端口
$ nc  -zv  192.168.0.40 2049
Connection to 192.168.0.40 2049 port [tcp/nfs] succeeded!

# 查看多个端口
$ nc -zv 192.168.56.10 80 22 21

# 查看指定端口范围 
$ nc -zv 192.168.56.10 20-80
```

`-w` 可用于设置超时：

```bash
nc -w 5 -zv  192.168.0.40 2049
```



## 参考资料

- [使用 nc 命令检查远程端口是否打开](https://linux.cn/article-8186-1.html)
