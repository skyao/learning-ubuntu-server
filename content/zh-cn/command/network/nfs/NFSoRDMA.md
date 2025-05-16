---
title: "NFSoRDMA"
weight: 60
date: 2022-03-20
description: >
  
---



## 准备工作

### 正确安装网卡的 MLNX_OFED 驱动

注意在安装时，一定要加 `--add-kernel-support` 参数，否则只能重新安装。我之前安装 MLNX_OFED 驱动时没有加这个参数，因此不得不重新安装：

```bash
 sudo ./mlnxofedinstall --without-fw-update --force --add-kernel-support
```

这个命令会删除现有已经安装的驱动，然后重新安装

### 安装 rdma 相关的包

参考 redhat 的文档 [通过 RDMA(NFSoRDMA)启用 NFS](https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/8/html/deploying_different_types_of_servers/enabling-nfs-over-rdma-nfsordma_exporting-nfs-shares) 

在 nfs 服务器端和客户端都安装 rdma-core ：

```bash
sudo apt install rdma-core
```

并确保 `/etc/rdma/modules/rdma.conf` 文件中没有注释掉 `xprtrdma` 和 `svcrdma` 这两行：

```properties
# NFS over RDMA client support
xprtrdma

# NFS over RDMA server support
svcrdma
```

### 确保 rdma 相关的 mod 正确装载

参考 oracle 的这个文档， [Using the NFS over RDMA Client](https://docs.oracle.com/en/operating-systems/uek/3/relnotes3.4/uek3_techpreview-NFSoRDMA.html) 

在 nfs 服务器端，检查：

```bash
sudo modprobe svcrdma
```

在 nfs 客户端，检查：

```bash
sudo modprobe xprtrdma
```

备注： svcrdma 和 xprtrdma 似乎都只是 rpcrdma mod 的别名？？

```bash
sudo modprobe -r xprtrdma
modprobe: FATAL: Module rpcrdma is in use.
```



## 安装

### 安装 VAST-NFS Client Driver

https://vastnfs.vastdata.com/docs/4.0/Intro.html

下载最新版本:

```bash
curl -sSf https://vast-nfs.s3.amazonaws.com/download.sh | bash

Checking latest version...
Downloaded: vastnfs-4.0.18.tar.xz
```

解压缩：

```bash
tar xvf vastnfs-4.0.18.tar.xz
cd vastnfs-4.0.18
```

编译：

```bash
./build.sh bin

......
dpkg-buildpackage: info: binary-only upload (no source included)
total 912
-rw-r--r-- 1 sky sky 933788 Aug 11 02:17 vastnfs-dkms_4.0.18-vastdata-MLNX.OFED.LINUX-4.9-6.0.6.0_all.deb
------------------------------------------------------------------

Output in dist/

total 912
-rw-r--r-- 1 sky sky 933788 Aug 11 02:17 vastnfs-dkms_4.0.18-vastdata-MLNX.OFED.LINUX-4.9-6.0.6.0_all.deb
```

注意这里，如果编译出来的文件名是类似上面的 “vastnfs-dkms_4.0.18-vastdata-MLNX.OFED.LINUX-4.9-6.0.6.0_all.deb”，是有问题的，需要重新安装。

这是因为 MLNX_OFED 驱动在安装是没有加 `--add-kernel-support` 参数。如果 MLNX_OFED 驱动在安装时有正确的加 `--add-kernel-support` 参数，则编译出来的文件名会类似 `vastnfs-modules_4.0.18-vastdata-MLNX.OFED.LINUX-4.9-6.0.6.0.kver.5.4.0-156-generic_amd64.deb` 这样带有 linux 内核的版本。

安装：

```bash
cd dist
# 注意这个文件是有问题的
# sudo apt install ./vastnfs-dkms_4.0.18-vastdata-MLNX.OFED.LINUX-4.9-6.0.6.0_all.deb
# 这个文件才是对的
sudo apt install ./vastnfs-modules_4.0.18-vastdata-MLNX.OFED.LINUX-4.9-6.0.6.0.kver.5.4.0-156-generic_amd64.deb

......
compat_nfs_ssc.ko:
Running module version sanity check.
 - Original module
   - No original module exists within this kernel
 - Installation
   - Installing to /lib/modules/5.4.0-156-generic/updates/dkms/

depmod...

DKMS: install completed.
```

更新 initramfs：

```bash
sudo update-initramfs -u -k `uname -r`

update-initramfs: Generating /boot/initrd.img-5.4.0-156-generic
```

重启机器。然后检验：

```bash
$ cat /sys/module/sunrpc/srcversion
FAA3D4CDBA86B8279289A02

$ modinfo sunrpc | grep srcversion
srcversion:     FAA3D4CDBA86B8279289A02
```

两个 srcversion 要一致。

```bash
$ vastnfs-ctl status          

version: 4.0.18-vastdata-MLNX.OFED.LINUX-4.9-6.0.6.0
kernel modules: sunrpc rpcrdma compat_nfs_ssc lockd nfs_acl auth_rpcgss nfsd nfs nfsv3
services: rpcbind.socket rpcbind
rpc_pipefs: /run/rpc_pipefs
```



### 启动 nfs 服务器端

```bash
sudo vi /etc/exports
```

设置 nfs export 的内容，比如我这里要导出两个地址：

```bash
/mnt/data/share 192.168.0.0/16(fsid=0,rw,async,insecure,no_root_squash,no_subtree_check)
/mnt/data/pve-share 192.168.0.0/16(fsid=0,rw,async,insecure,no_root_squash,no_subtree_check)
```

重启 nfs-server ：

```bash
sudo systemctl restart nfs-server
```

关键设置，在启动 nfs-server 之后再设置 rdma 的端口 20049：

```bash
# 还不能用 sudo,会宝座
sudo echo rdma 20049 > /proc/fs/nfsd/portlist
zsh: permission denied: /proc/fs/nfsd/portlist

# 要先 su 到 root
sudo su
# 看一眼现在的设置， rdma 20049 端口的设置在 restart nfs-server 之后就消失了
vi /proc/fs/nfsd/portlist
# 也不能通过 vi 命令来修改，会无法保存文件

# 可以这样修改
echo rdma 20049 > /proc/fs/nfsd/portlist
```

这个时候 `/proc/fs/nfsd/portlist` 文件的内容是这样的：

```properties
rdma 20049
rdma 20049
udp 2049
tcp 2049
udp 2049
tcp 2049
```



### nfs 客户端mount

在 nfs 客户端执行命令，尝试 mount ：

```bash
sudo mount -t nfs -o vers=3,proto=rdma,port=20049 192.168.99.100:/mnt/data/share /mnt/nfs-fast
```

如果 mount 命令一直卡住不返回，一般是 nfs 服务器端无法连接，在 ip 地址和路径没问题的情况下，最大可能性就是 20049 端口没有开启或者无法连接。

如果成功，此时就可以看到服务器端的文件被 mount 过来了。

## 验证

执行 dd 命令读取一个 100G 的大文件验证一下：

```bash
dd if=/mnt/nfs-fast/temp/test1.img of=/dev/null bs=8M
12800+0 records in
12800+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 72.2484 s, 1.5 GB/s
```

很遗憾，1.5GB/s 的速度明显不是 rdma 开启后应有的速度。

再次重复上面的测试，期间通过 top 命令查看 nfs 服务器端的 cpu 使用情况：

```bash
top - 06:56:12 up  3:15,  2 users,  load average: 0.90, 0.36, 0.14
Tasks: 259 total,   1 running, 258 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us, 10.2 sy,  0.0 ni, 88.5 id,  1.0 wa,  0.0 hi,  0.3 si,  0.0 st
MiB Mem :  31841.3 total,    261.1 free,   1129.2 used,  30451.0 buff/cache
MiB Swap:   8192.0 total,   8180.2 free,     11.8 used.  30244.3 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                
   5950 root      20   0       0      0      0 D  45.5   0.0   0:45.37 nfsd  
   5951 root      20   0       0      0      0 S  45.2   0.0   0:46.05 nfsd  
    153 root      20   0       0      0      0 S  32.2   0.0   1:10.76 kswapd0 
    744 root       0 -20       0      0      0 I   2.3   0.0   0:02.72 kworker/5:1H-ib-comp-wq 
   5949 root      20   0       0      0      0 S   1.0   0.0   0:00.91 nfsd  
     10 root      20   0       0      0      0 I   0.3   0.0   0:03.39 rcu_sched  
```

直接在 nfs 服务器端执行 dd 命令读取这个 100G 的大文件试试：

```bash
dd if=/mnt/data/share/temp/test1.img of=/dev/null bs=8M

12800+0 records in
12800+0 records out
107374182400 bytes (107 GB, 100 GiB) copied, 25.8021 s, 4.2 GB/s
```

可以看到读取的速度是 4.2 GB/s，可见ssd硬盘的读取速度（4.2 GB/s）远远大于 nfs 传输的速度（1.5 GB/s）。

推断：从传输速度上看，rdma 应该是没有开启。

期间通过 top 命令查看 nfs 服务器端的 cpu 使用情况：

```bash
top - 06:59:16 up  3:18,  2 users,  load average: 0.41, 0.40, 0.20
Tasks: 261 total,   2 running, 259 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  9.2 sy,  0.0 ni, 89.7 id,  0.9 wa,  0.0 hi,  0.3 si,  0.0 st
MiB Mem :  31841.3 total,    238.3 free,   1131.8 used,  30471.2 buff/cache
MiB Swap:   8192.0 total,   8169.7 free,     22.2 used.  30242.1 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND  
   6046 root      20   0   13724   9896   1728 R  89.0   0.0   0:17.25 dd 
    153 root      20   0       0      0      0 S  23.0   0.0   1:29.40 kswapd0 
     10 root      20   0       0      0      0 I   0.3   0.0   0:03.57 rcu_sched    
   5866 root      20   0       0      0      0 I   0.3   0.0   0:01.62 kworker/u24:2-mlx4_en  
   5955 root      20   0       0      0      0 I   0.3   0.0   0:00.04 kworker/0:2-events  
```

发现和走 nfs 传输相比，服务器端的 cpu 消耗基本相当： `10.2 sy,  0.0 ni, 88.5 id` 对 `9.2 sy,  0.0 ni, 89.7 id`。

推断：从nfs服务器端 cpu 占用上看，rdma 应该是开启了。

现在就有点矛盾了！

## 参考资料

- [NFS Features for Enhanced Performance](https://support.vastdata.com/hc/en-us/articles/5140626736028-NFS-Features-for-Enhanced-Performance)： 主要参考这篇文章，但这边文章中漏了一些内容，比如 nfs 服务器端是怎么设置 20049 端口的
- https://vastnfs.vastdata.com/docs/4.0/Intro.html： 这是 vastnfs 的官方文档，vastnfs 的安装主要参考这个文档，但这个文档同样没有解释 20049 端口的由来
- [通过 RDMA(NFSoRDMA)启用 NFS](https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/8/html/deploying_different_types_of_servers/enabling-nfs-over-rdma-nfsordma_exporting-nfs-shares): redhat 的这个文档提到了要安装 rdma-core 并确保 rdma.conf 文件有正确设置 xprtrdma 和 svcrdma。nfs export 的参数也是参考这个帖子的案例
- [Using the NFS over RDMA Client](https://docs.oracle.com/en/operating-systems/uek/3/relnotes3.4/uek3_techpreview-NFSoRDMA.html): oracle 的这个文档终于有提到要修改 nfsd 的配置文件，增加 rdma 的 20049 端口。
