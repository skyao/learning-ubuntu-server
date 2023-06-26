---
title: "创建timeshift快照"
linkTitle: "创建快照"
weight: 2110
date: 2022-01-06
description: >
  通过create命令创建timeshift快照进行备份
---

## 创建快照

### 命令介绍

常见快照的命令为：

```bash
sudo timeshift --create --comments "first backup after install" --tags O
```

tags的类型:

- O: Ondemand，默认值，一般用于手工创建快照
- B: Boot
- H: Hourly
- D: Daily
- W: Weekly
- M: Monthly

### 示例

这是创建的第一个快照，操作系统和 timeshift 安装完成之后的第一个快照：

```bash
$ sudo timeshift --create --comments "first backup after install" 

/dev/nvme0n1p6 is mounted at: /run/timeshift/backup, options: rw,relatime

------------------------------------------------------------------------------
Estimating system size...
Creating new snapshot...(RSYNC)
Saving to device: /dev/nvme0n1p6, mounted at path: /run/timeshift/backup
Synching files with rsync...
Created control file: /run/timeshift/backup/timeshift/snapshots/2022-01-06_08-19-32/info.json
RSYNC Snapshot saved successfully (28s)
Tagged snapshot '2022-01-06_08-19-32': ondemand
```

完成后查看：

```bash
$ sudo timeshift --list

/dev/nvme0n1p6 is mounted at: /run/timeshift/backup, options: rw,relatime

Device : /dev/nvme0n1p6
UUID   : 208eb500-fd49-4580-b4ea-3b126d5b0fe4
Path   : /run/timeshift/backup
Mode   : RSYNC
Status : OK
1 snapshots, 96.1 GB free

Num     Name                 Tags  Description                 
------------------------------------------------------------------------------
0    >  2022-01-06_08-19-32  O     first backup after install  
```

