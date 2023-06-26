---
title: "修改hostname"
linkTitle: "修改hostname"
weight: 15
date: 2021-01-24
description: >
  修改Ubuntu Server的hostname
---



## 背景

在 esxi 等虚拟平台上复制虚拟机时，就会遇到 hostname 重复的问题，这时最好是能在虚拟机复制之后永久性的修改 hostname 。

查看当前 hostname ：

```bash
hostname
```

## ubuntu 20.04

可以通过 hostnamectl 来修改：

```bash
sudo hostnamectl set-hostname newNameHere
```

完成后再额外修改一下 hosts 文件中的 hostname：

```bash
sudo nano /etc/hosts
```

完成后重启即可：

```bash
sudo reboot
```





## 参考资料

- [Ubuntu 20.04 LTS change hostname permanently](https://www.cyberciti.biz/faq/ubuntu-20-04-lts-change-hostname-permanently/)
- [How to Change Hostname on Ubuntu 20.04](https://phoenixnap.com/kb/ubuntu-20-04-change-hostname)
