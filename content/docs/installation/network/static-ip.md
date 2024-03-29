---
title: "配置静态IP"
linkTitle: "静态IP"
weight: 9
date: 2021-08-30
description: >
  配置静态IP地址
---

通常ip地址采用 dhcp，然后在路由器上绑定 IP 地址和 mac 地址即可。但在使用大量虚拟机时，每次都去修改路由器增加静态地址绑定比较麻烦，因此还是有必要在需要时设置静态IP地址。

## 使用 NetworkManager 

> 适用于 ubuntu 20.04 版本

设置前先安装 network-manager：

```bash
sudo apt install network-manager
```

安全起见，备份原有的配置文件：

```bash
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.original
```

修改 `/etc/netplan` 的配置文件，如 `00-installer-config.yaml`，

```bash
sudo vi /etc/netplan/00-installer-config.yaml
```

将需要配置为静态IP地址的网卡设置 `dhcp4: false` ，然后手工配置：

```yaml
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    wan1:
      match:
        macaddress: 00:0c:29:23:d3:de
      set-name: wan1
      dhcp4: false
      addresses: [192.168.0.21/24]
      gateway4: 192.168.0.1
      nameservers:
        addresses: [192.168.0.1]
    wan2:
      match:
        macaddress: 48:0f:cf:ef:08:11
      set-name: wan2
      dhcp4: true
```

## 使用 Networkd

> 适用于 ubuntu 22.04 / 22.10 / 23.04 版本

安全起见，备份原有的配置文件：

```bash
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.original
```

修改 `/etc/netplan` 的配置文件，如 `00-installer-config.yaml`，

```bash
sudo vi /etc/netplan/00-installer-config.yaml
```

手工配置：

```yaml
network:
  renderer: networkd
  ethernets:
    ens160:
      addresses:
        - 192.168.0.56/24
      nameservers:
        addresses: [192.168.0.1]
      routes:
        - to: default
          via: 192.168.0.1
  version: 2
```





