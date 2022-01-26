---
title: "cpu压力测试"
linkTitle: "cpu压力测试"
weight: 20
date: 2022-01-26
description: >
  对cpu进行压力测试
---

主要是想看一下压力测试时cpu的频率，看是否工作在性能模式。

> 备注： 对于x99主板，则同时可以检验一下鸡血bios是否生效

## sysbench

安装sysbench：

```bash
sudo apt install sysbench
```

执行cpu压力测试：

```bash
sysbench cpu --threads=40 run
```

很欣喜的看到x99双路主板上两个e5 2666 v3 cpu在鸡血bios之后都可以跑在全核3.5G的频率：

```bash
cpufreq-info | grep "current CPU"
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
  current CPU frequency is 3.49 GHz.
```

## stress

## 参考资料

- [6 Useful Linux Apps to Stress Test and Benchmark CPU Performance](https://linuxhint.com/useful_linux_stress_test_benchmark_cpu_perf/)
- [How to Benchmark Your Linux System](https://linuxconfig.org/how-to-benchmark-your-linux-system)