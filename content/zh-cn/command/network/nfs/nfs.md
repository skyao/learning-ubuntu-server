---
title: "nfs命令概述"
weight: 1
date: 2022-03-20
description: >
  常用nfs命令
---



## 安装

nfs 服务器和客户端的安装见 [安装配置nfs文件共享](../../../../docs/installation/network/nfs/)

## 使用

列出远程nfs服务器上的挂载信息，然后在本地建立目录并挂载：

```bash
# 查看远程服务器的挂载情况
$ showmount --exports 192.168.0.40 
Export list for 192.168.0.40:
/mnt/d         192.168.0.0/16
/mnt/nfs-share 192.168.0.0/16

# 挂载到本地
$ mkdir -p /mnt/d
$ sudo mount -t nfs 192.168.0.40:/mnt/d /mnt/d

# 不使用时卸载
$ sudo umount /mnt/d
```





## 资料

- [Show NFS shares | List NFS mount points | List NFS clients Linux](https://www.golinuxcloud.com/show-nfs-shares-list-nfs-client-mount-points/#Use_showmount_to_show_NFS_shares)
