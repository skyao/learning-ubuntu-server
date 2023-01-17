---
title: "配置网络"
linkTitle: "配置网络"
weight: 215
date: 2021-08-06
description: >
  配置网络
---

### 问题：卡住两分钟

开机启动时出现两分钟的停顿，显示提醒如下：

```
A start job is running for wait for Network to be configured
```

然后2分钟超时才能继续启动。

登录之后检查：

```bash
$ sudo systemctl status systemd-networkd-wait-online.service

systemd-networkd-wait-online.service - Wait for Network to be Configured
     Loaded: loaded (/lib/systemd/system/systemd-networkd-wait-online.service; enabled; vendor preset: enabled)
     Active: failed (Result: exit-code) since Sat 2022-01-15 08:12:57 UTC; 7min ago
       Docs: man:systemd-networkd-wait-online.service(8)
    Process: 1272 ExecStart=/lib/systemd/systemd-networkd-wait-online (code=exited, status=1/FAILURE)
   Main PID: 1272 (code=exited, status=1/FAILURE)

Jan 15 08:10:57 skywork2 systemd[1]: Starting Wait for Network to be Configured...
Jan 15 08:11:28 skywork2 systemd-networkd-wait-online[1272]: managing: ens1
Jan 15 08:11:28 skywork2 systemd-networkd-wait-online[1272]: managing: enp6s0
Jan 15 08:11:28 skywork2 systemd-networkd-wait-online[1272]: managing: ens1
Jan 15 08:11:28 skywork2 systemd-networkd-wait-online[1272]: managing: enp6s0
Jan 15 08:12:57 skywork2 systemd-networkd-wait-online[1272]: Event loop failed: Connection timed out
Jan 15 08:12:57 skywork2 systemd[1]: systemd-networkd-wait-online.service: Main process exited, code=exited, status=1/FAILURE
Jan 15 08:12:57 skywork2 systemd[1]: systemd-networkd-wait-online.service: Failed with result 'exit-code'.
Jan 15 08:12:57 skywork2 systemd[1]: Failed to start Wait for Network to be Configured.
```

问题所在在于网卡的配置：

```bash
$ networkctl 
IDX LINK   TYPE       OPERATIONAL SETUP      
  1 lo     loopback   carrier     unmanaged  
  2 enp5s0 ether      no-carrier  configuring
  3 enp6s0 ether      routable    configured 
  4 ens1   ether      routable    configured 
  5 ibs1d1 infiniband off         unmanaged  

5 links listed.
```

### 解决方案1-减少超时时间

```bash
cd /etc/systemd/system/network-online.target.wants/
$ ls -l
lrwxrwxrwx 1 root root 56 Aug 16 11:02 systemd-networkd-wait-online.service -> /lib/systemd/system/systemd-networkd-wait-online.service
$ sudo vi systemd-networkd-wait-online.service
```

在`[Service]`下添加一行 `TimeoutStartSec=2sec`：

```bash
[Service]
Type=oneshot
ExecStart=/lib/systemd/systemd-networkd-wait-online
RemainAfterExit=yes
TimeoutStartSec=2sec			# 增加这一行
```

这样2秒钟之后就会继续启动，而不是卡住两分钟，虽然治标不治本。

TBD： 发现我的40G网络会有dhcp获取IP地址很慢的问题，基本要30秒左右才能拿到IP地址，导致启动时很慢。即使这里设置timeout可以继续启动操作系统， 但是进入桌面之后由于40G网络的IP尚未能获取，用40G网络的IP地址会无法访问。千兆网络dhcp的速度就非常快。

### 解决方案2-配置网络

终极解决方案还是要配置好网络。用 ip 命令查看当前网卡情况：

```bash
$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp4s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:e0:4c:68:f7:da brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.50/24 brd 192.168.0.255 scope global dynamic enp4s0
       valid_lft 81706sec preferred_lft 81706sec
    inet6 fe80::2e0:4cff:fe68:f7da/64 scope link 
       valid_lft forever preferred_lft forever
3: enp5s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether 00:e0:4c:54:17:3a brd ff:ff:ff:ff:ff:ff
4: enp6s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether 00:e0:4c:54:17:3b brd ff:ff:ff:ff:ff:ff
5: ens4: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 48:0f:cf:ef:08:11 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.50/24 brd 10.0.0.255 scope global dynamic ens4
       valid_lft 38533sec preferred_lft 38533sec
    inet6 fe80::4a0f:cfff:feef:811/64 scope link 
       valid_lft forever preferred_lft forever
6: ibs4d1: <BROADCAST,MULTICAST> mtu 4092 qdisc noop state DOWN group default qlen 256
    link/infiniband a0:00:03:00:fe:80:00:00:00:00:00:00:48:0f:cf:ff:ff:ef:08:12 brd 00:ff:ff:ff:ff:12:40:1b:ff:ff:00:00:00:00:00:00:ff:ff:ff:ff
```

参考资料：

- [解决Ubuntu开机过程中，配置网络的步骤需要等待两分钟的问题](https://magiclen.org/ubuntu-start-job-wait-network)

但上面的方案对于不准备使用的网口（比如连网线都没插）来说是无效的。对于这样的网口，我们需要彻底的禁用他们。

```bash
systemctl | grep net-devices        
  sys-subsystem-net-devices-enp4s0.device                                                   loaded active plugged   RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller                                      
  sys-subsystem-net-devices-enp5s0.device                                                   loaded active plugged   RTL810xE PCI Express Fast Ethernet controller                                                  
  sys-subsystem-net-devices-enp6s0.device                                                   loaded active plugged   RTL810xE PCI Express Fast Ethernet controller                                                  
  sys-subsystem-net-devices-ens4.device                                                     loaded active plugged   MT27520 Family [ConnectX-3 Pro] (InfiniBand FDR/Ethernet 10Gb/40Gb 2-port 544+FLR-QSFP Adapter)
  sys-subsystem-net-devices-ibs4d1.device                                                   loaded active plugged   MT27520 Family [ConnectX-3 Pro] (InfiniBand FDR/Ethernet 10Gb/40Gb 2-port 544+FLR-QSFP Adapter)
```

实践中发现网上的很多方法都无效，比如 ifconfig / ip 中的 down/up 命令，重启之后就无效了。

目前 systemd 接管的网卡情况如下：

```
$ networkctl 
IDX LINK   TYPE       OPERATIONAL SETUP      
  1 lo     loopback   carrier     unmanaged  
  2 enp4s0 ether      routable    configured 
  3 enp5s0 ether      no-carrier  configuring
  4 enp6s0 ether      no-carrier  configuring
  5 ens4   ether      routable    configured 
  6 ibs4d1 infiniband off         unmanaged  
```

在这里，enp5s0 和 enp6s0 这两张网卡是我们希望禁用的。

参考以下资料的说明：

- [Disable network interface in systemd-networkd](https://www.linuxquestions.org/questions/linux-networking-3/disable-network-interface-in-systemd-networkd-4175676868/)
- [systemd.network — Network configuration](https://www.freedesktop.org/software/systemd/man/systemd.network.html)

我们在 `/usr/lib/systemd/network` 目录下放置两个文件来申明我们要禁用两块网卡，操作如下：

```bash
cd /usr/lib/systemd/network
```

`sudo vi 01-disable-enp5s0.network` 创建文件，内容如下：

```
[Match]
MACAddress=00:e0:4c:54:17:3a

[Link]
Unmanaged=yes
```

`sudo vi 02-disable-enp6s0.network` 创建文件，内容如下：

```
[Match]
MACAddress=00:e0:4c:54:17:3b

[Link]
Unmanaged=yes
```

注意：`[Match]` 这里用 mac 地址来匹配，不要用 name，实际测试中我发现用 name 做匹配和设置 Unmanaged 后，系统中的网卡会发生名字变化的情况，导致匹配出现问题。切记用 mac 地址进行匹配。

重启机器之后，看效果：

```bash
$ networkctl 
IDX LINK   TYPE       OPERATIONAL SETUP     
  1 lo     loopback   carrier     unmanaged 
  2 enp4s0 ether      routable    configured
  3 enp5s0 ether      off         unmanaged 			# 被禁用了
  4 enp6s0 ether      off         unmanaged  			# 被禁用了
  5 ens4   ether      routable    configured
  6 ibs4d1 infiniband off         unmanaged 			# 这个口本来被禁用了
```

### 增加要管理的网卡

有某台机器，出现了网卡状态为 down 的情况，造成无法使用，原因不明。

```bash
$ ip addr                                     
......
5: ens4: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 70:10:6f:aa:2a:81 brd ff:ff:ff:ff:ff:ff
    
$ networkctl
IDX LINK   TYPE       OPERATIONAL SETUP     
  1 lo     loopback   carrier     unmanaged 
  2 enp4s0 ether      routable    configured
  3 enp5s0 ether      off         unmanaged 
  4 enp6s0 ether      off         unmanaged 
  5 ens4   ether      off         unmanaged 
  6 ibs4d1 infiniband off         unmanaged 
```

解决方法：

```bash
cd /usr/lib/systemd/network
vi 03-ens4-dhcp.network
```

输入以下内容：

```properties
[Match]
MACAddress=70:10:6f:aa:2a:81

[Link]
Unmanaged=no

[Network]
DHCP=yes
```

重启即可。

