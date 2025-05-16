---
title: "使用 ip 命令操作路由"
linkTitle: "操作路由"
weight: 70
date: 2021-03-03
description: >
  使用 ip 命令操作路由
---



## 添加路由



```bash
$ route -n                                                
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.100.40  0.0.0.0         UG    100    0        0 ens1
0.0.0.0         192.168.0.1     0.0.0.0         UG    101    0        0 eno1
169.254.0.0     0.0.0.0         255.255.0.0     U     1000   0        0 ens1
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
192.168.0.0     0.0.0.0         255.255.255.0   U     101    0        0 eno1
192.168.100.0   0.0.0.0         255.255.255.0   U     100    0        0 ens1

```

为了访问 10.0.0.0/8 号段，增加一条路由规则：

```bash
sudo ip route add 10.0.0.0/8 via 192.168.100.40 dev ens1
```

完成后的路由情况：

```bash
route -n                                                
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.100.40  0.0.0.0         UG    100    0        0 ens1
0.0.0.0         192.168.0.1     0.0.0.0         UG    101    0        0 eno1
10.0.0.0        192.168.100.40  255.0.0.0       UG    0      0        0 ens1
169.254.0.0     0.0.0.0         255.255.0.0     U     1000   0        0 ens1
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
192.168.0.0     0.0.0.0         255.255.255.0   U     101    0        0 eno1
192.168.100.0   0.0.0.0         255.255.255.0   U     100    0        0 ens1
```





## 参考资料

- [How To Add Route on Linux](https://devconnected.com/how-to-add-route-on-linux/)
- [How To Add New Route In Ubuntu, Linux?](https://www.poftut.com/add-new-route-ubuntu-linux/)
- [How to Add and Delete Static Route in Linux using IP Command](https://www.linuxtechi.com/add-delete-static-route-linux-ip-command/)