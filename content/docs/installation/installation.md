---
title: "安装Ubuntu Server"
linkTitle: "安装过程"
weight: 1
date: 2022-01-06
description: >
  Ubuntu Server的安装
---

### 物理机安装

常规安装，没啥特殊。

就是和windows一起安装时，不需要划分额外的 ESP 分区，ubuntu server在安装时会自动选择 windows 所在的 ESP 分区，而且无法改动。

### 虚拟机安装

ubuntu server的安装非常简单，如果是用vmware安装，则更加的简单，vmware会自动完成安装过程中的设置，直接自动安装完成。

### ESXi安装

在 ESXi 中新建虚拟机，选择 ubuntu server 的 iso 启动，一路安装。

bios启动选择 efi。



