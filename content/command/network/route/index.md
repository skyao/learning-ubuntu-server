---
title: "route 命令"
linkTitle: "route"
weight: 40
date: 2022-04-07
description: >
  route 命令
---



```ba
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

