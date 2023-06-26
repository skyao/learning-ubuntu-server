---
title: "安装Ubuntu Server"
linkTitle: "安装过程"
weight: 1
date: 2022-01-06
description: >
  Ubuntu Server的安装
---



## 注意事项

### 安装时关闭网络

ubuntu server 在安装完成之后，会自动进入更新状态，然后由于没有设置国内的源，会导致速度很慢，时间会长达30分钟，而且无法中断，完全浪费时间。

因此，安装时的最佳实践是不带网络安装，这样安装过程非常快（SSD硬盘的话大概3分钟）。安装完成之后，设置好源，再进行 apt 

## 物理机安装

常规安装，没啥特殊。

我习惯的磁盘分区：

1. EFI 分区： 200或者300M
2. 根目录： `/`  除了 timeshift 分区之外的空间都给这里
3. timeshift 备份分区： 一般留 50-100g 作为 timeshift 的备份分区，非常实用。

一般建议用分区软件先行分好区再进行安装，因为 ubuntu 安装器的设置比较简陋。但如果是ubuntu单独占用一整块硬盘，设置简单也可以用 ubuntu 安全器。

选择自定义方方式：

1. "reformat"： 先清理整块硬盘，去除所有现有分区
2. "use as boot device"：设置硬盘为启动盘，这样安装器会默认创建 efi 分区，大小为 512m
3. "add gpt partition": 在硬盘的空余空间中创建一个分区，占用除 timeshift 分区之外的所有空间，格式化为 ext4，挂载为 `/`
4. "add gpt partition": 在剩余空间中创建分区，格式化为 ext4, 挂载为 `/timeshift`

>  备注：和windows一起安装时，不需要划分额外的 ESP 分区，ubuntu server在安装时会自动选择 windows 所在的 ESP 分区，而且无法改动。（新版本没有确认过）

安装过程中必选安装 openssh 服务。

## 虚拟机安装

ubuntu server 的安装非常简单，如果是用 vmware 安装，则更加的简单，vmware 会自动完成安装过程中的设置，直接自动安装完成。

## ESXi安装

在 ESXi 中新建虚拟机，选择 ubuntu server 的 iso 启动，一路安装。

bios 启动选择 efi，开启安全启动。

