---
title: "基于raid的nfs文件共享"
linkTitle: "nfs文件共享(raid版)"
weight: 31
date: 2021-08-26
description: >
  在 ubuntu server上安装基于raid的nfs，进行文件共享
---


## 背景

安装的 ubuntu 20.04 版本，内核为 5.4：

```bash
$ uname -a
Linux switch99 5.4.0-192-generic #212-Ubuntu SMP Fri Jul 5 09:47:39 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux
```

硬盘为两块三星 PM983a 900G 22110 ssd 企业版硬盘：

```bash
lspci | grep Non-Volatile
03:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller SM981/PM981/PM983
04:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller SM981/PM981/PM983
```

两块硬盘组成 raid0，有两块分区，md0p1安装有 ubuntu 操作系统，md0p2 是用于 timeshift 的备份分区： 

```bash
Disk /dev/md0: 1.65 TiB, 1799020871680 bytes, 3513712640 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 524288 bytes / 1048576 bytes
Disklabel type: gpt
Disk identifier: 9B47B927-9C18-4026-AD6D-E68F9F3F8751

Device          Start        End    Sectors  Size Type
/dev/md0p1       2048 3405912063 3405910016  1.6T Linux filesystem
/dev/md0p2 3405912064 3513706495  107794432 51.4G Linux filesystem
```

### 硬盘速度测试

分别写入 1g / 10g / 100g 三个文件，速度如下：


```bash
$ dd if=/dev/zero of=/home/sky/temp/1g.img bs=1G count=1 oflag=dsync 
1+0 records in
1+0 records out
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 1.39956 s, 767 MB/s

$ dd if=/dev/zero of=/home/sky/temp/10g.img bs=1G count=10 oflag=dsync
10+0 records in
10+0 records out
10737418240 bytes (11 GB, 10 GiB) copied, 12.4784 s, 860 MB/s

$ dd if=/dev/zero of=/home/sky/temp/100g.img bs=1G count=100 oflag=dsync
100+0 records in
100+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 127.14 s, 845 MB/s

$ ls -lh
total 112G
-rw-rw-r-- 1 sky sky 100G Aug  2 09:43 100g.img
-rw-rw-r-- 1 sky sky  10G Aug  2 09:41 10g.img
-rw-rw-r-- 1 sky sky 1.0G Aug  2 09:40 1g.img
```

分别读取三个文件，速度都在3g附近：

```bash
$ dd if=/home/sky/temp/1g.img of=/dev/null bs=8M
128+0 records in
128+0 records out
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 0.366136 s, 2.9 GB/s

$ dd if=/home/sky/temp/10g.img of=/dev/null bs=8M
1280+0 records in
1280+0 records out
10737418240 bytes (11 GB, 10 GiB) copied, 3.29498 s, 3.3 GB/s

$ dd if=/home/sky/temp/100g.img of=/dev/null bs=8M
12800+0 records in
12800+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 37.4048 s, 2.9 GB/s

```

比较遗憾的是，raid0 并没有代理读写速度上的提升，基本维持在单块硬盘的水准。原因暂时不明。

后续考虑换一个大容量的硬盘（2t或者4t）取代这两块小容量（1t）。

## 搭建 nas 服务器端

### 安装 nfs server

```bash
# 安装
sudo apt install nfs-kernel-server -y

# 开机自启
sudo systemctl start nfs-kernel-server
sudo systemctl enable nfs-kernel-server

# 验证
sudo systemctl status nfs-kernel-server
Jan 29 20:40:15 skynas3 systemd[1]: Starting nfs-server.service - NFS server and services...
Jan 29 20:40:15 skynas3 exportfs[1422]: exportfs: can't open /etc/exports for reading
Jan 29 20:40:16 skynas3 systemd[1]: Finished nfs-server.service - NFS server and services.
```

### 配置 UFW 防火墙

安装 nfs 之后必须配置防火墙。先安装 ufw:

```bash
sudo apt install ufw -y
```

安装完成之后第一个必须执行的步骤就是开放 ssh 登录：

```bash
sudo ufw allow ssh
sudo ufw enable
```

然后是容许访问 nfs

```bash
sudo ufw allow from 192.168.0.0/16 to any port nfs
```

重启 ufw 并查看 ufw 状态:

```bash
sudo ufw reload
sudo ufw status
```

可以看到 2049 端口开放给 nfs 了。

```bash
$ sudo ufw status
[sudo] password for sky: 
Firewall reloaded
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere                  
2049                       ALLOW       192.168.0.0/16            
22/tcp (v6)                ALLOW       Anywhere (v6) 
```

备注：暂时不知道为什么 ufw 开启之后就无法访问 nfs 了，上面的 allow rule 没能生效。只好暂时先 `sudo ufw disable` 关闭防火墙先。

### 准备共享目录

为了方便后续的管理，采用伪文件系统

```bash
sudo mkdir -p /data/{share,pve-share}

sudo chown -R nobody:nogroup /data/share
sudo chown -R nobody:nogroup /data/pve-share
```

创建 export 目录：

```bash
sudo mkdir -p /exports/{share,pve-share}

sudo chown -R nobody:nogroup /exports
```

修改 `/etc/fstab` 文件来 mount 伪文件系统和 exports

```bash
 sudo vi /etc/fstab
```

增加如下内容:

```bash
# nfs exports
/data/share /exports/share     none bind
/data/pve-share /exports/pve-share    none bind
```

### 配置 nfs export

```bash
sudo vi /etc/exports
```

修改 nfs exports 的内容:

```bash
/exports/share   192.168.0.0/16(rw,no_root_squash,no_subtree_check,crossmnt,fsid=0)
/exports/pve-share   192.168.0.0/16(rw,no_root_squash,no_subtree_check,crossmnt,fsid=0)
```

重启 nfs-kernel-server，查看 nfs-kernel-server 的状态：

```bash
sudo systemctl restart nfs-kernel-server
sudo systemctl status nfs-kernel-server
```

输出为：

```bash
● nfs-server.service - NFS server and services
     Loaded: loaded (/lib/systemd/system/nfs-server.service; enabled; vendor preset: enabled)
     Active: active (exited) since Sun 2024-08-04 01:31:09 UTC; 3h 35min ago
    Process: 10626 ExecStartPre=/usr/sbin/exportfs -r (code=exited, status=0/SUCCESS)
    Process: 10627 ExecStart=/usr/sbin/rpc.nfsd $RPCNFSDARGS (code=exited, status=0/SUCCESS)
   Main PID: 10627 (code=exited, status=0/SUCCESS)

Aug 04 01:31:08 switch99 systemd[1]: Starting NFS server and services...
Aug 04 01:31:09 switch99 systemd[1]: Finished NFS server and services.
```

验证：

```bash
ps -ef | grep nfs
```

输出为：

```
root       10636       2  0 01:31 ?        00:00:00 [nfsd]
root       10637       2  0 01:31 ?        00:00:00 [nfsd]
root       10638       2  0 01:31 ?        00:00:00 [nfsd]
root       10639       2  0 01:31 ?        00:00:00 [nfsd]
root       10640       2  0 01:31 ?        00:00:00 [nfsd]
root       10641       2  0 01:31 ?        00:00:00 [nfsd]
root       10642       2  0 01:31 ?        00:00:00 [nfsd]
root       10643       2  0 01:31 ?        00:00:01 [nfsd]
```

查看当前挂载情况：

```bash
sudo showmount -e
```

输出为:

```bash
Export list for switch99:
/exports/pve-share 192.168.0.0/16
/exports/share     192.168.0.0/16
```

## 总结

1. 其实和是不是 raid 没啥关系
2. ubuntu 的软raid的性能加成为0是万万没有想到的，准备放弃
