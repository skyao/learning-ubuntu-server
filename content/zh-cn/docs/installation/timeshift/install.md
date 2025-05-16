---
title: "timeshift的安装配置"
linkTitle: "安装"
weight: 100
date: 2022-01-06
description: >
  安装并配置timeshift
---

## 安装

```bash
sudo apt install timeshift
```

完成之后看一下：

```bash
$ sudo timeshift

Timeshift v20.03 by Tony George (teejeetech@gmail.com)

Syntax:

  timeshift --check
  timeshift --create [OPTIONS]
  timeshift --restore [OPTIONS]
  timeshift --delete-[all] [OPTIONS]
  timeshift --list-{snapshots|devices} [OPTIONS]
```

## 配置

默认安装后，在第一次运行前，我们需要修改 timeshift 的配置文件，否则 timeshift 会默认找到一个 ext4 分区作为备份区。

看一下目前的硬盘情况：

```bash
$ sudo fdisk -l
Disk /dev/loop0: 55.45 MiB, 58130432 bytes, 113536 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/loop1: 70.32 MiB, 73728000 bytes, 144000 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/loop2: 32.3 MiB, 33865728 bytes, 66144 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/nvme0n1: 838.37 GiB, 900185481216 bytes, 219771846 sectors
Disk model: MZ1LB960HBJR-000FB                      
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 131072 bytes / 131072 bytes
Disklabel type: gpt
Disk identifier: 7C431E31-78CA-4600-9C2F-C68D10E793CC

Device             Start       End   Sectors  Size Type
/dev/nvme0n1p1       256    131327    131072  512M EFI System
/dev/nvme0n1p2    131328 196739327 196608000  750G Linux filesystem
/dev/nvme0n1p3 196739328 219771391  23032064 87.9G Linux filesystem
```

这里的 `/dev/nvme0n1p3` 是我为 timeshift 预留的分区，存放在 nvme 磁盘上，以保证备份和恢复的速度。

```bash
$ sudo lsblk -f
NAME        FSTYPE   LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINT
loop0       squashfs                                                  0   100% /snap/core18/2128
loop1       squashfs                                                  0   100% /snap/lxd/21029
loop2       squashfs                                                  0   100% /snap/snapd/12704
nvme0n1                                                                        
├─nvme0n1p1 vfat           72C9-B4E4                             504.9M     1% /boot/efi
├─nvme0n1p2 ext4           a83415e6-ed69-4932-9d08-1e87d7510dc1  689.1G     1% /
└─nvme0n1p3 ext4           9b22569d-9410-48cc-b994-10257b2d0498   81.5G     0% /run/timeshift/backup
```

记录 nvme0n1p3 的 uuid ，然后修改配置, `sudo vi /etc/timeshift/timeshift.json`  打开后设置 backup_device_uuid 为 nvme0n1p3 的 uuid :

```json
{
  "backup_device_uuid" : "9b22569d-9410-48cc-b994-10257b2d0498",
  "parent_device_uuid" : "",
  "do_first_run" : "true",
  "btrfs_mode" : "false",
  "include_btrfs_home" : "false",
  "stop_cron_emails" : "true",
  "schedule_monthly" : "false",
  "schedule_weekly" : "false",
  "schedule_daily" : "false",
  "schedule_hourly" : "false",
  "schedule_boot" : "false",
  "count_monthly" : "2",
  "count_weekly" : "3",
  "count_daily" : "5",
  "count_hourly" : "6",
  "count_boot" : "5",
  "snapshot_size" : "0",
  "snapshot_count" : "0",
  "exclude" : [
  ],
  "exclude-apps" : [
  ]
}
```

执行timeshift命令，就能看到配置生效了：

```bash
sudo timeshift --list
First run mode (config file not found)
Selected default snapshot type: RSYNC

/dev/nvme0n1p3 is mounted at: /run/timeshift/backup, options: rw,relatime,stripe=32

Device : /dev/nvme0n1p3
UUID   : 9b22569d-9410-48cc-b994-10257b2d0498
Path   : /run/timeshift/backup
Mode   : RSYNC
Status : No snapshots on this device
First snapshot requires: 0 B

No snapshots found
```

