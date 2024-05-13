---
title: "简单软路由"
linkTitle: "简单软路由"
weight: 10
description: >
  使用 linux bridge 简单实现软路由
---

## 准备工作

### 网卡信息

主板上插了四块 hp544+ 双头网卡，芯片是 Mellanox ConnectX-3 Pro：

```bash
lspci | grep Eth
01:00.0 Ethernet controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]
02:00.0 Ethernet controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]
05:00.0 Ethernet controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]
06:00.0 Ethernet controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]
```

安装 ubuntu server 之后，只使用了其他一个网口作为 wan：

```bash
2: ens2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether e0:07:1b:77:41:31 brd ff:ff:ff:ff:ff:ff
3: ens2d1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether e0:07:1b:77:41:32 brd ff:ff:ff:ff:ff:ff
4: ens6: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether e0:07:1b:78:3e:a1 brd ff:ff:ff:ff:ff:ff
5: ens6d1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether e0:07:1b:78:3e:a2 brd ff:ff:ff:ff:ff:ff
6: ens4: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether 50:65:f3:89:24:21 brd ff:ff:ff:ff:ff:ff
7: ens4d1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether 50:65:f3:89:24:22 brd ff:ff:ff:ff:ff:ff
8: enp6s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether c4:34:6b:df:e1:81 brd ff:ff:ff:ff:ff:ff
9: enp6s0d1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether c4:34:6b:df:e1:82 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.99/24 brd 192.168.0.255 scope global enp6s0d1
       valid_lft forever preferred_lft forever
    inet6 fe80::c634:6bff:fedf:e182/64 scope link 
       valid_lft forever preferred_lft forever
```

## 配置软交换

### 安装 network-manager

首先需要安装 network-manager：

```bash
sudo apt install network-manager
```

### 配置 linux bridge

备份原有的网络配置文件：

```bash
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.original
```

然后修改文件内容：

```bash
sudo vi /etc/netplan/00-installer-config.yaml
```

内容如下：

```json
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    wan1:
      match:
        macaddress: c4:34:6b:df:e1:82
      set-name: wan1
      dhcp4: no
    lan1:
      match:
        macaddress: c4:34:6b:df:e1:81
      set-name: lan1
      dhcp4: no
    lan2:
      match:
        macaddress: 50:65:f3:89:24:21
      set-name: lan2
      dhcp4: no
    lan3:
      match:
        macaddress: 50:65:f3:89:24:22
      set-name: lan3
      dhcp4: no
    lan4:
      match:
        macaddress: e0:07:1b:78:3e:a1
      set-name: lan4
      dhcp4: no
    lan5:
      match:
        macaddress: e0:07:1b:78:3e:a2
      set-name: lan5
      dhcp4: no
    lan6:
      match:
        macaddress: e0:07:1b:77:41:31
      set-name: lan6
      dhcp4: no
    lan7:
      match:
        macaddress: e0:07:1b:77:41:32
      set-name: lan7
      dhcp4: no
  bridges:
    br0:
      interfaces:
        - wan1
        - lan1
        - lan2
        - lan3
        - lan4
        - lan5
        - lan6
        - lan7
      addresses:
        - 192.168.0.99/24
      dhcp4: no

```

四块网卡8个网口，一个作为wan，7个作为lan，配置好之后都作为 br0 的 interfaces 。



## 总结

这是配置软交换最简单的方法，不需要建立子网，也不需要配置 DHCP server。



