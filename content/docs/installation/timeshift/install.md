---
title: "timeshift的安装配置"
linkTitle: "安装"
weight: 2110
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

默认安装后第一次运行时，由于没有配置文件，timeshift会默认找到一个 ext4 分区作为备份区：

```bash
$ sudo timeshift --list
First run mode (config file not found)
Selected default snapshot type: RSYNC
Mounted '/dev/sda1' at '/run/timeshift/backup'
Selected default snapshot device: /dev/sda1
Device : /dev/sda1
UUID   : aa0d664b-bb69-564f-a5a7-d32d645b68b3
Path   : /run/timeshift/backup
Mode   : RSYNC
Status : No snapshots on this device
First snapshot requires: 0 B

No snapshots found
```

看一下目前的硬盘情况：

```bash
$ sudo fdisk -l
Disk /dev/loop0: 55.45 MiB, 58130432 bytes, 113536 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/loop1: 32.3 MiB, 33865728 bytes, 66144 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/loop2: 70.32 MiB, 73728000 bytes, 144000 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/nvme0n1: 894.26 GiB, 960197124096 bytes, 1875385008 sectors
Disk model: SAMSUNG MZ1LW960HMJP-000MV              
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: FECA5117-B485-4E26-AB5A-BFCEB95B946F

Device              Start        End    Sectors  Size Type
/dev/nvme0n1p1       2048     206847     204800  100M EFI System
/dev/nvme0n1p2     206848     239615      32768   16M Microsoft reserved
/dev/nvme0n1p3     239616  209952767  209713152  100G Microsoft basic data
/dev/nvme0n1p4  209952768  210362367     409600  200M EFI System
/dev/nvme0n1p5  210362368 1678370815 1468008448  700G Linux filesystem
/dev/nvme0n1p6 1678370816 1875384319  197013504   94G Linux filesystem


Disk /dev/sda: 2.75 TiB, 3000878383104 bytes, 5861090592 sectors
Disk model: WDC WD3000FYYZ-0
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 0E6923C2-540C-44CB-8CFA-4DF69549741A

Device          Start        End    Sectors   Size Type
/dev/sda1        2048 4294971391 4294969344     2T Linux filesystem
/dev/sda2  4294971392 5861089279 1566117888 746.8G Microsoft basic data
```

这里的 `/dev/nvme0n1p6` 是我为 timeshift 预留的分区，存放在 nvme 磁盘上，以保证备份和恢复的速度。

`/dev/sda1` 分区是普通机械硬盘，用来存放对速度不敏感的数据，如日志等。

由于 timeshift 默认找到了 `/dev/sda1` 分区并设置为备份地，因此第一件时间就是要修改配置, `sudo vi /etc/timeshift.json` 打开:

```json
{
  "backup_device_uuid" : "",
  "parent_device_uuid" : "",
  "do_first_run" : "false",
  "btrfs_mode" : "false",
  "include_btrfs_home_for_backup" : "false",
  "include_btrfs_home_for_restore" : "false",
  "stop_cron_emails" : "true",
  "btrfs_use_qgroup" : "true",
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
  "date_format" : "%Y-%m-%d %H:%M:%S",
  "exclude" : [
  ],
  "exclude-apps" : [
  ]
}
```

其中 backup_device_uuid 需要设置为对应分区的UUID，可以通过下面的命令查找：

```bash
$ sudo blkid | grep nvme0n1p6
/dev/nvme0n1p6: UUID="208eb500-fd49-4580-b4ea-3b126d5b0fe4" TYPE="ext4" PARTLABEL="Linux data partition" PARTUUID="0f43b2c9-95ce-468d-b0cc-3d95976e17bd"
```

修改 backup_device_uuid 为上面的 UUID 即可：

```json
{
  "backup_device_uuid" : "208eb500-fd49-4580-b4ea-3b126d5b0fe4",
}
```

重新执行timeshift命令，就能看到配置生效了：

```bash
sudo timeshift --list
Mounted '/dev/nvme0n1p6' at '/run/timeshift/backup'
Device : /dev/nvme0n1p6
UUID   : 208eb500-fd49-4580-b4ea-3b126d5b0fe4
Path   : /run/timeshift/backup
Mode   : RSYNC
Status : No snapshots on this device
First snapshot requires: 0 B

No snapshots found
```