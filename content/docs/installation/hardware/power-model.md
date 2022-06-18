---
title: "电源模式"
linkTitle: "电源模式"
weight: 30
date: 2022-01-26
description: >
  设置CPU电源模式
---



### 查看电源模式

```bash
$ cpufreq-info
                  
cpufrequtils 008: cpufreq-info (C) Dominik Brodowski 2004-2009
Report errors and bugs to cpufreq@vger.kernel.org, please.
analyzing CPU 0:
  driver: intel_pstate
  CPUs which run at the same hardware frequency: 0
  CPUs which need to have their frequency coordinated by software: 0
  maximum transition latency: 4294.55 ms.
  hardware limits: 1.20 GHz - 3.50 GHz
  available cpufreq governors: performance, powersave
  current policy: frequency should be within 1.20 GHz and 3.50 GHz.
                  The governor "powersave" may decide which speed to use
                  within this range.
  current CPU frequency is 1.20 GHz.
```



### 设置电源模式

设置电源模式为 "performance": 

```bash
sudo bash -c 'for i in {0..31}; do cpufreq-set -c $i -g performance; done'
```

设置电源模式为 "powersave": 

```bash
sudo bash -c 'for i in {0..31}; do cpufreq-set -c $i -g powersave; done'
```

设置电源模式为 "ondemand": 

```bash
sudo bash -c 'for i in {0..31}; do cpufreq-set -c $i -g ondemand; done'
```





## 参考资料

- [CPU Performance Mode On Ubuntu](https://www.robertroos.net/blog/cpu-performance-ubuntu-elementaryos/)
