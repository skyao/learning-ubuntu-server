---
title: "fdisk命令"
linkTitle: "fdisk"
weight: 10
date: 2021-03-03
description: >
  Linux fdisk 命令
---



## 查看分区情况

```bash
sudo fdisk -l
```

输出类似，这里有两块sdd硬盘：

```bash
Disk /dev/nvme1n1: 465.78 GiB, 500107862016 bytes, 976773168 sectors
Disk model: WDC WDS500G1B0C-00S6U0                  
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: AB8D3D88-0124-404E-B1BC-658E567C569D

Device             Start       End   Sectors  Size Type
/dev/nvme1n1p1      2048   1050623   1048576  512M EFI System
/dev/nvme1n1p2   1050624 839911423 838860800  400G Linux filesystem
/dev/nvme1n1p3 839911424 976771071 136859648 65.3G Linux filesystem


Disk /dev/nvme0n1: 3.74 TiB, 4096805658624 bytes, 8001573552 sectors
Disk model: YSSDHB-4TN7000                          
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

```



## 对硬盘进行分区

通过 `fdisk -l` 命令找到要进行分区的硬盘，比如这里是 `/dev/nvme0n1`：

```bash
sudo fdisk /dev/nvme0n1
```

如果硬盘没有分区表，则会提示：

```bash
Device does not contain a recognized partition table.
The size of this disk is 3.7 TiB (4096805658624 bytes). DOS partition table format cannot be used on drives for volumes larger than 2199023255040 bytes for 512-byte sectors. Use GUID partition table format (GPT).

Created a new DOS disklabel with disk identifier 0x55c9e168.

Command (m for help):
```

推荐使用 GPT 分区。

之后输入 m 可以得到帮助菜单：

```bash
Command (m for help): m

Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table
```

这里首先输入 `g` 来创建 GPT 分区表：

```bash
Command (m for help): g
Created a new GPT disklabel (GUID: DA503D82-D785-4A47-A701-504DB1ED256C).
```

然后输入 n 来创建新的分区，这里简单起见，将整个硬盘作为一个单个分区 3.7T：

```bash
Command (m for help): n
Partition number (1-128, default 1): 1
First sector (2048-8001573518, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-8001573518, default 8001573518): 

Created a new partition 1 of type 'Linux filesystem' and of size 3.7 TiB.
```

输入 w 写入硬盘并退出：

```bash
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

分区完成之后检查硬盘：

```bash
sudo fdisk -l 

Disk /dev/nvme1n1: 465.78 GiB, 500107862016 bytes, 976773168 sectors
Disk model: WDC WDS500G1B0C-00S6U0                  
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: AB8D3D88-0124-404E-B1BC-658E567C569D

Device             Start       End   Sectors  Size Type
/dev/nvme1n1p1      2048   1050623   1048576  512M EFI System
/dev/nvme1n1p2   1050624 839911423 838860800  400G Linux filesystem
/dev/nvme1n1p3 839911424 976771071 136859648 65.3G Linux filesystem


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

## 后续操作

### 格式化

再对分区进行格式化，这里采用 ext4 格式：

```bash
sudo mkfs.ext4 /dev/nvme0n1p1

➜  ~ sudo mkfs.ext4 /dev/nvme0n1p1
mke2fs 1.45.5 (07-Jan-2020)
Discarding device blocks: done                            
Creating filesystem with 1000196433 4k blocks and 250052608 inodes
Filesystem UUID: 0478bcae-bab0-4762-a48a-6bdab9d99d5b
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
	102400000, 214990848, 512000000, 550731776, 644972544

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done 
```

### 挂载

通过

```bash
lsblk --fs
```

命令查看分区的 UUID 备用：

```bash

NAME        FSTYPE   LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINT
nvme1n1                                                                        
|-nvme1n1p1 vfat           4FD8-D561                             504.9M     1% /boot/efi
|-nvme1n1p2 ext4           7a60bb3b-ea82-47c0-86d0-f92edba2a466  356.4G     4% /
`-nvme1n1p3 ext4           68100d98-ccea-4f5e-8663-16ff1b194ac1   52.7G    12% /timeshift
nvme0n1                                                                        
`-nvme0n1p1 ext4           0478bcae-bab0-4762-a48a-6bdab9d99d5b  
```

执行

```bash
sudo vi /etc/fstab
```

可以看到：

```bash
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/nvme0n1p2 during curtin installation
/dev/disk/by-uuid/7a60bb3b-ea82-47c0-86d0-f92edba2a466 / ext4 defaults 0 1
# /timeshift was on /dev/nvme0n1p3 during curtin installation
/dev/disk/by-uuid/68100d98-ccea-4f5e-8663-16ff1b194ac1 /timeshift ext4 defaults 0 1
# /boot/efi was on /dev/nvme0n1p1 during curtin installation
/dev/disk/by-uuid/4FD8-D561 /boot/efi vfat defaults 0 1
```

类似的增加一行记录

```bash
/dev/disk/by-uuid/0478bcae-bab0-4762-a48a-6bdab9d99d5b /data ext4 defaults 0 1
```

注意需要提前创建挂载的目录

```bash
sudo mkdir /data
```

保存后，重启或者执行 `sudo mount -a` 生效。

