---
title: "查看cpu频率"
linkTitle: "cpu频率"
weight: 10
date: 2022-01-26
description: >
  查看当前cpu各个核心的实时频率
---

## cpufreq-info

需要安装 cpufrequtils : 

```bash
sudo apt-get install cpufrequtils
```

然后执行:

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
analyzing CPU 1:
  driver: intel_pstate
  CPUs which run at the same hardware frequency: 1
  CPUs which need to have their frequency coordinated by software: 1
  maximum transition latency: 4294.55 ms.
  hardware limits: 1.20 GHz - 3.50 GHz
  available cpufreq governors: performance, powersave
  current policy: frequency should be within 1.20 GHz and 3.50 GHz.
                  The governor "powersave" may decide which speed to use
                  within this range.
  current CPU frequency is 1.20 GHz.
......
```

如果想快速概括的查看所有内核的实时频率，可以这样：

```bash
$ cpufreq-info | grep "current CPU frequency"
  current CPU frequency is 1.38 GHz.
  current CPU frequency is 1.23 GHz.
  current CPU frequency is 1.20 GHz.
  current CPU frequency is 1.20 GHz.
  current CPU frequency is 1.20 GHz.
  current CPU frequency is 1.20 GHz.
  current CPU frequency is 1.20 GHz.
......
```

也可以这样每秒钟刷新一下：

```bash
watch -n1 "lscpu | grep MHz | awk '{print $1}'";
```





## auto-cpufreq

https://snapcraft.io/auto-cpufreq

## 参考资料

- [Ubuntu – How to set “Performance” instead of “Powersave” as default](https://itectec.com/ubuntu/ubuntu-how-to-set-performance-instead-of-powersave-as-default/)

- [How to obtain CPU information on Linux](https://linuxconfig.org/how-to-obtain-cpu-information-on-linux)