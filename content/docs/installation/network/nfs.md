---
title: "安装配置nfs文件共享"
linkTitle: "nfs文件共享（单硬盘版）"
weight: 30
date: 2021-08-26
description: >
  在ubuntu server上安装nfs，进行文件共享
---



## 配置nfs服务器端

### 安装nfs-server

```bash
sudo apt update
sudo apt install nfs-kernel-server -y
```

查看 nfs-server 的状态：

```bash
$ sudo systemctl status nfs-server

● nfs-server.service - NFS server and services
     Loaded: loaded (/lib/systemd/system/nfs-server.service; enabled; vendor pr>
     Active: active (exited) since Wed 2021-12-29 00:45:44 CST; 5min ago
   Main PID: 758742 (code=exited, status=0/SUCCESS)
      Tasks: 0 (limit: 154080)
     Memory: 0B
     CGroup: /system.slice/nfs-server.service

Dec 29 00:45:43 skyserver systemd[1]: Starting NFS server and services...
Dec 29 00:45:44 skyserver systemd[1]: Finished NFS server and services.
```

### 创建nfs共享目录

```bash
sudo mkdir /mnt/nfs-share
```

让所有的客户端都可以访问所有的文件，修改文件的所有者和许可：

```bash
sudo chown nobody:nogroup /mnt/nfs-share
sudo chmod -R 777 /mnt/nfs-share
```

### 授权客户端访问nfs server

`sudo vi /etc/exports` 打开文件，为每个客户端授权访问:

```bash
/mnt/nfs-share client-IP(rw,sync,no_subtree_check)
```

如果有多个客户端则需要重复多次授权，也可以通过子网掩码一次性授权：

```bash
/mnt/nfs-share 192.168.0.0/24(rw,sync,no_subtree_check)
/mnt/nfs-share 10.0.0.0/24(rw,sync,no_subtree_check)
```

参数解释：

- rw (Read and Write)
- sync (Write changes to disk before applying them)
- no_subtree_check (Avoid subtree checking )

执行下面命令进行export：

```bash
sudo exportfs -a
```

### 配置防火墙

关闭防火墙，或者设置防火墙规则:

```bash
sudo ufw allow from 192.168.0.0/24 to any port nfs
sudo ufw allow from 10.0.0.0/24 to any port nfs
```

### 增加nfs共享的硬盘

服务器机器上有一块4t的ssd和两块3t的旧硬盘，准备通过nfs共享出来，方便其他机器访问。

可以通过fdisk命令获取相关的硬盘和分区信息：

```bash
$ fdisk -l
......
Disk /dev/sda: 2.75 TiB, 3000878383104 bytes, 5861090592 sectors
Device     Start        End    Sectors  Size Type
/dev/sda1   2048 5861089279 5861087232  2.7T Linux filesystem

Disk /dev/sdb: 2.75 TiB, 3000592982016 bytes, 5860533168 sectors
Device     Start        End    Sectors  Size Type
/dev/sdb1   2048 5860532223 5860530176  2.7T Linux filesystem

Disk /dev/nvme1n1: 3.5 TiB, 3840755982336 bytes, 7501476528 sectors
Device     Start        End    Sectors  Size Type
/dev/nvme1n1p1  2048 7501475839 7501473792  3.5T Linux filesystem
```

然后查分区对应的uuid备用:

```bash
$ ls -l /dev/disk/by-uuid/
......
lrwxrwxrwx 1 root root 10 Jan 16 12:34 7c3a3aca-9cde-48a0-957b-eead5b2ab7dc -> ../../sda1
lrwxrwxrwx 1 root root 10 Jan 16 12:34 fcae6bde-4789-4afe-b164-c7189a0bdf5f -> ../../sdb1
lrwxrwxrwx 1 root root 15 Jan 17 01:35 561fe530-4888-4759-97db-f36f607ca18e -> ../../nvme1n1p1

$ sudo mkdir /mnt/e
$ sudo mkdir /mnt/f
```

`sudo vi /etc/fstab` 增加挂载信息:

```bash
# two old disks
/dev/disk/by-uuid/7c3a3aca-9cde-48a0-957b-eead5b2ab7dc /mnt/e ext4 defaults 0 1
/dev/disk/by-uuid/fcae6bde-4789-4afe-b164-c7189a0bdf5f /mnt/f ext4 defaults 0 1
# one ssd disk
/dev/disk/by-uuid/561fe530-4888-4759-97db-f36f607ca18e /mnt/d ext4 defaults 0 1
```

执行 `sudo mount -av` 立即生效。

加到nfs共享中：

```bash
sudo chown nobody:nogroup /mnt/d
sudo chmod -R 777 /mnt/d
sudo chown nobody:nogroup /mnt/e
sudo chmod -R 777 /mnt/e
sudo chown nobody:nogroup /mnt/f
sudo chmod -R 777 /mnt/f
```

`sudo vi /etc/exports` 增加授权访问:

```bash
/mnt/d 192.168.0.0/24(rw,sync,no_subtree_check)
/mnt/d 10.0.0.0/24(rw,sync,no_subtree_check)
/mnt/e 192.168.0.0/24(rw,sync,no_subtree_check)
/mnt/e 10.0.0.0/24(rw,sync,no_subtree_check)
/mnt/f 192.168.0.0/24(rw,sync,no_subtree_check)
/mnt/f 10.0.0.0/24(rw,sync,no_subtree_check)
```

执行 `sudo exportfs -a` 立即生效。

## 配置nfs客户端

### 安装nfs软件

```bash
sudo apt update
sudo apt install nfs-common
```

### 挂载nfs server到本地

创建用来挂载 nfs server的本地目录：

```bash
sudo mkdir -p /mnt/nfs-skyserver
sudo mkdir -p /mnt/d
sudo mkdir -p /mnt/e
sudo mkdir -p /mnt/f
```

挂载 nfs server 共享目录到这个客户端本地目录:

```bash
sudo mount 10.0.0.40:/mnt/nfs-share /mnt/nfs-skyserver
sudo mount 10.0.0.40:/mnt/d /mnt/d
sudo mount 10.0.0.40:/mnt/e /mnt/e
sudo mount 10.0.0.40:/mnt/f /mnt/f
```

验证一下：

```bash
cd /mnt/nfs-skyserver 
touch a.txt
```

回到服务器端那边检查一下看文件是否创建。

为了方便使用，创建一些软链接：

```bash
mkdir -p ~/data
cd ~/data
ln -s /mnt/nfs-skyserver skyserver
ln -s /mnt/d d
ln -s /mnt/e e
ln -s /mnt/f f
```

### 设置永久挂载

上面的挂载在重启之后就会消失，`/mnt/nfs-skyserver` 会变成一个普通的目录。

为了在机器重启之后继续自动挂载, `sudo vi /etc/fstab` 打开文件增加以下内容:

```properties
# nfs from skyserver
10.0.0.40:/mnt/nfs-share /mnt/nfs-skyserver   nfs   defaults,timeo=15,retrans=5,_netdev	0 0
10.0.0.40:/mnt/d /mnt/d   nfs   defaults,timeo=15,retrans=5,_netdev	0 0
10.0.0.40:/mnt/e /mnt/e   nfs   defaults,timeo=15,retrans=5,_netdev	0 0
10.0.0.40:/mnt/f /mnt/f   nfs   defaults,timeo=15,retrans=5,_netdev	0 0
```

timeout 时间不要放太长，以备skyserver服务器没有开机时其他机器不至于在启动时阻塞太长时间。

## 参考资料

- [How to Install NFS Server on Ubuntu 20.04 (Focal Fossa)](https://www.linuxtechi.com/install-nfs-server-on-ubuntu/)
- [How to Install and Configure an NFS Server on Ubuntu 20.04](https://linuxize.com/post/how-to-install-and-configure-an-nfs-server-on-ubuntu-20-04/)
