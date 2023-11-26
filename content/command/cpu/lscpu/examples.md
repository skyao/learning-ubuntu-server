---
title: "lscpu命令的案例"
linkTitle: "案例"
weight: 10
date: 2021-03-03
description: >
  收集一些lscpu命令的案例
---

## Intel Core

### Intel Core i7-13700K

```bash
lscpu   
Architecture:            x86_64
  CPU op-mode(s):        32-bit, 64-bit
  Address sizes:         46 bits physical, 48 bits virtual
  Byte Order:            Little Endian
CPU(s):                  24
  On-line CPU(s) list:   0-23
Vendor ID:               GenuineIntel
  Model name:            13th Gen Intel(R) Core(TM) i7-13700K
    CPU family:          6
    Model:               183
    Thread(s) per core:  2
    Core(s) per socket:  16
    Socket(s):           1
    Stepping:            1
    CPU max MHz:         5700.0000
    CPU min MHz:         800.0000
    BogoMIPS:            6835.20
    Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fx
                         sr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs bts re
                         p_good nopl xtopology nonstop_tsc cpuid aperfmperf tsc_known_freq pni pclmulqdq dtes64 monitor 
                         ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm sse4_1 sse4_2 x2apic movbe popcnt tsc_dead
                         line_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb ssbd ibrs ibpb s
                         tibp ibrs_enhanced tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 s
                         mep bmi2 erms invpcid rdseed adx smap clflushopt clwb intel_pt sha_ni xsaveopt xsavec xgetbv1 x
                         saves split_lock_detect avx_vnni dtherm ida arat pln pts hwp hwp_notify hwp_act_window hwp_epp 
                         hwp_pkg_req umip pku ospke waitpkg gfni vaes vpclmulqdq tme rdpid movdiri movdir64b fsrm md_cle
                         ar serialize pconfig arch_lbr flush_l1d arch_capabilities
Virtualization features: 
  Virtualization:        VT-x
Caches (sum of all):     
  L1d:                   640 KiB (16 instances)
  L1i:                   768 KiB (16 instances)
  L2:                    24 MiB (10 instances)
  L3:                    30 MiB (1 instance)
NUMA:                    
  NUMA node(s):          1
  NUMA node0 CPU(s):     0-23
Vulnerabilities:         
  Itlb multihit:         Not affected
  L1tf:                  Not affected
  Mds:                   Not affected
  Meltdown:              Not affected
  Mmio stale data:       Not affected
  Retbleed:              Not affected
  Spec store bypass:     Mitigation; Speculative Store Bypass disabled via prctl and seccomp
  Spectre v1:            Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:            Mitigation; Enhanced IBRS, IBPB conditional, RSB filling, PBRSB-eIBRS SW sequence
  Srbds:                 Not affected
  Tsx async abort:       Not affected
```

频率情况：

```bash
lscpu -e
CPU NODE SOCKET CORE L1d:L1i:L2:L3 ONLINE    MAXMHZ   MINMHZ      MHZ
  0    0      0    0 0:0:0:0          yes 5300.0000 800.0000 1598.945
  1    0      0    0 0:0:0:0          yes 5300.0000 800.0000 3400.000
  2    0      0    1 4:4:1:0          yes 5300.0000 800.0000 3400.000
  3    0      0    1 4:4:1:0          yes 5300.0000 800.0000 3400.000
  4    0      0    2 8:8:2:0          yes 5300.0000 800.0000 1056.193
  5    0      0    2 8:8:2:0          yes 5300.0000 800.0000 3400.000
  6    0      0    3 12:12:3:0        yes 5300.0000 800.0000 3400.000
  7    0      0    3 12:12:3:0        yes 5300.0000 800.0000 3400.000
  8    0      0    4 16:16:4:0        yes 5400.0000 800.0000 3400.000
  9    0      0    4 16:16:4:0        yes 5700.0000 800.0000 3400.000
 10    0      0    5 20:20:5:0        yes 5700.0000 800.0000 3400.000
 11    0      0    5 20:20:5:0        yes 5700.0000 800.0000 3400.000
 12    0      0    6 24:24:6:0        yes 5700.0000 800.0000 3400.000
 13    0      0    6 24:24:6:0        yes 5700.0000 800.0000 3400.000
 14    0      0    7 28:28:7:0        yes 5700.0000 800.0000 3400.000
 15    0      0    7 28:28:7:0        yes 5700.0000 800.0000 3400.000
 16    0      0    8 32:32:8:0        yes 4200.0000 800.0000 3400.000
 17    0      0    9 33:33:8:0        yes 4200.0000 800.0000 3400.000
 18    0      0   10 34:34:8:0        yes 4200.0000 800.0000 3400.000
 19    0      0   11 35:35:8:0        yes 4200.0000 800.0000 3400.000
 20    0      0   12 36:36:9:0        yes 4200.0000 800.0000 3400.000
 21    0      0   13 37:37:9:0        yes 4200.0000 800.0000 3400.000
 22    0      0   14 38:38:9:0        yes 4200.0000 800.0000 3400.000
 23    0      0   15 39:39:9:0        yes 4200.0000 800.0000 3400.000
```



###  Intel Core i5-12400

```bash
$ lscpu

Architecture:            x86_64
  CPU op-mode(s):        32-bit, 64-bit
  Address sizes:         39 bits physical, 48 bits virtual
  Byte Order:            Little Endian
CPU(s):                  12
  On-line CPU(s) list:   0-11
Vendor ID:               GenuineIntel
  BIOS Vendor ID:        Intel(R) Corporation
  Model name:            12th Gen Intel(R) Core(TM) i5-12400
    BIOS Model name:     12th Gen Intel(R) Core(TM) i5-12400 To Be Filled By O.E.M. CPU @ 2.4GHz
    BIOS CPU family:     205
    CPU family:          6
    Model:               151
    Thread(s) per core:  2
    Core(s) per socket:  6
    Socket(s):           1
    Stepping:            5
    Frequency boost:     enabled
    CPU(s) scaling MHz:  32%
    CPU max MHz:         2501.0000
    CPU min MHz:         800.0000
    BogoMIPS:            4992.00
    Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm
                          constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf tsc_known_freq pni pclmulqdq dtes64 monitor ds_cpl vmx 
                         est tm2 ssse3 sdbg fma cx16 xtpr pdcm sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid
                         _fault epb cat_l2 cdp_l2 ssbd ibrs ibpb stibp ibrs_enhanced tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erm
                         s invpcid rdt_a rdseed adx smap clflushopt clwb intel_pt sha_ni xsaveopt xsavec xgetbv1 xsaves split_lock_detect avx_vnni dtherm ida arat pln pts hw
                         p hwp_notify hwp_act_window hwp_epp hwp_pkg_req hfi umip pku ospke waitpkg gfni vaes vpclmulqdq rdpid movdiri movdir64b fsrm md_clear serialize arch
                         _lbr ibt flush_l1d arch_capabilities
Virtualization features: 
  Virtualization:        VT-x
Caches (sum of all):     
  L1d:                   288 KiB (6 instances)
  L1i:                   192 KiB (6 instances)
  L2:                    7.5 MiB (6 instances)
  L3:                    18 MiB (1 instance)
NUMA:                    
  NUMA node(s):          1
  NUMA node0 CPU(s):     0-11
Vulnerabilities:         
  Itlb multihit:         Not affected
  L1tf:                  Not affected
  Mds:                   Not affected
  Meltdown:              Not affected
  Mmio stale data:       Not affected
  Retbleed:              Not affected
  Spec store bypass:     Mitigation; Speculative Store Bypass disabled via prctl
  Spectre v1:            Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:            Mitigation; Enhanced IBRS, IBPB conditional, RSB filling, PBRSB-eIBRS SW sequence
  Srbds:                 Not affected
  Tsx async abort:       Not affected
```

频率情况：

```bash
lscpu -e
CPU NODE SOCKET CORE L1d:L1i:L2:L3 ONLINE    MAXMHZ   MINMHZ      MHZ
  0    0      0    0 0:0:0:0          yes 2501.0000 800.0000 800.0070
  1    0      0    0 0:0:0:0          yes 2501.0000 800.0000 800.0000
  2    0      0    1 1:1:1:0          yes 2501.0000 800.0000 800.0000
  3    0      0    1 1:1:1:0          yes 2501.0000 800.0000 800.0550
  4    0      0    2 2:2:2:0          yes 2501.0000 800.0000 800.0000
  5    0      0    2 2:2:2:0          yes 2501.0000 800.0000 800.0000
  6    0      0    3 3:3:3:0          yes 2501.0000 800.0000 800.0000
  7    0      0    3 3:3:3:0          yes 2501.0000 800.0000 800.0000
  8    0      0    4 4:4:4:0          yes 2501.0000 800.0000 800.0000
  9    0      0    4 4:4:4:0          yes 2501.0000 800.0000 800.0000
 10    0      0    5 5:5:5:0          yes 2501.0000 800.0000 800.0000
 11    0      0    5 5:5:5:0          yes 2501.0000 800.0000 800.1110
```



## Intel Xeon

### Intel Xeon E5-2630L v3

```bash
$ lscpu

Architecture:                    x86_64
CPU op-mode(s):                  32-bit, 64-bit
Byte Order:                      Little Endian
Address sizes:                   46 bits physical, 48 bits virtual
CPU(s):                          16
On-line CPU(s) list:             0-15
Thread(s) per core:              2
Core(s) per socket:              8
Socket(s):                       1
NUMA node(s):                    1
Vendor ID:                       GenuineIntel
CPU family:                      6
Model:                           63
Model name:                      Intel(R) Xeon(R) CPU E5-2630L v3 @ 1.80GHz
Stepping:                        2
CPU MHz:                         1199.975
CPU max MHz:                     2900.0000
CPU min MHz:                     1200.0000
BogoMIPS:                        3599.72
Virtualization:                  VT-x
L1d cache:                       256 KiB
L1i cache:                       256 KiB
L2 cache:                        2 MiB
L3 cache:                        20 MiB
NUMA node0 CPU(s):               0-15
Vulnerability Itlb multihit:     KVM: Mitigation: Split huge pages
Vulnerability L1tf:              Mitigation; PTE Inversion; VMX conditional cache flushes, SMT vulnerable
Vulnerability Mds:               Mitigation; Clear CPU buffers; SMT vulnerable
Vulnerability Meltdown:          Mitigation; PTI
Vulnerability Mmio stale data:   Mitigation; Clear CPU buffers; SMT vulnerable
Vulnerability Retbleed:          Not affected
Vulnerability Spec store bypass: Mitigation; Speculative Store Bypass disabled via prctl and seccomp
Vulnerability Spectre v1:        Mitigation; usercopy/swapgs barriers and __user pointer sanitization
Vulnerability Spectre v2:        Mitigation; Retpolines, IBPB conditional, IBRS_FW, STIBP conditional, RSB filling, PBRSB-eIBRS Not affected
Vulnerability Srbds:             Not affected
Vulnerability Tsx async abort:   Not affected
Flags:                           fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb r
                                 dtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx
                                  est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm c
                                 puid_fault epb invpcid_single pti intel_ppin ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2
                                  smep bmi2 erms invpcid cqm xsaveopt cqm_llc cqm_occup_llc dtherm ida arat pln pts md_clear flush_l1d
```

频率情况：

```bash
 lscpu -e    
CPU NODE SOCKET CORE L1d:L1i:L2:L3 ONLINE    MAXMHZ    MINMHZ
  0    0      0    0 0:0:0:0          yes 2900.0000 1200.0000
  1    0      0    1 1:1:1:0          yes 2900.0000 1200.0000
  2    0      0    2 2:2:2:0          yes 2900.0000 1200.0000
  3    0      0    3 3:3:3:0          yes 2900.0000 1200.0000
  4    0      0    4 4:4:4:0          yes 2900.0000 1200.0000
  5    0      0    5 5:5:5:0          yes 2900.0000 1200.0000
  6    0      0    6 6:6:6:0          yes 2900.0000 1200.0000
  7    0      0    7 7:7:7:0          yes 2900.0000 1200.0000
  8    0      0    0 0:0:0:0          yes 2900.0000 1200.0000
  9    0      0    1 1:1:1:0          yes 2900.0000 1200.0000
 10    0      0    2 2:2:2:0          yes 2900.0000 1200.0000
 11    0      0    3 3:3:3:0          yes 2900.0000 1200.0000
 12    0      0    4 4:4:4:0          yes 2900.0000 1200.0000
 13    0      0    5 5:5:5:0          yes 2900.0000 1200.0000
 14    0      0    6 6:6:6:0          yes 2900.0000 1200.0000
 15    0      0    7 7:7:7:0          yes 2900.0000 1200.0000
```

### Intel Xeon E5-2698B v3 (双路)

```bash
$ lscpu

Architecture:            x86_64
  CPU op-mode(s):        32-bit, 64-bit
  Address sizes:         46 bits physical, 48 bits virtual
  Byte Order:            Little Endian
CPU(s):                  64
  On-line CPU(s) list:   0-63
Vendor ID:               GenuineIntel
  BIOS Vendor ID:        Intel
  Model name:            Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz
    BIOS Model name:     Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz  CPU @ 2.0GHz
    BIOS CPU family:     179
    CPU family:          6
    Model:               63
    Thread(s) per core:  2
    Core(s) per socket:  16
    Socket(s):           2
    Stepping:            2
    Frequency boost:     enabled
    CPU(s) scaling MHz:  60%
    CPU max MHz:         2001.0000
    CPU min MHz:         1200.0000
    BogoMIPS:            3990.62
    Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm
                          constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 s
                         dbg fma cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm cpuid_fault epb invpcid_s
                         ingle pti intel_ppin ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid cqm xsav
                         eopt cqm_llc cqm_occup_llc dtherm ida arat pln pts md_clear flush_l1d
Virtualization features: 
  Virtualization:        VT-x
Caches (sum of all):     
  L1d:                   1 MiB (32 instances)
  L1i:                   1 MiB (32 instances)
  L2:                    8 MiB (32 instances)
  L3:                    80 MiB (2 instances)
NUMA:                    
  NUMA node(s):          2
  NUMA node0 CPU(s):     0-15,32-47
  NUMA node1 CPU(s):     16-31,48-63
Vulnerabilities:         
  Itlb multihit:         KVM: Mitigation: VMX disabled
  L1tf:                  Mitigation; PTE Inversion; VMX conditional cache flushes, SMT vulnerable
  Mds:                   Mitigation; Clear CPU buffers; SMT vulnerable
  Meltdown:              Mitigation; PTI
  Mmio stale data:       Mitigation; Clear CPU buffers; SMT vulnerable
  Retbleed:              Not affected
  Spec store bypass:     Mitigation; Speculative Store Bypass disabled via prctl
  Spectre v1:            Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:            Mitigation; Retpolines, IBPB conditional, IBRS_FW, STIBP conditional, RSB filling, PBRSB-eIBRS Not affected
  Srbds:                 Not affected
  Tsx async abort:       Not affected
```

频率情况：

```bash
lscpu -e         
CPU NODE SOCKET CORE L1d:L1i:L2:L3 ONLINE    MAXMHZ    MINMHZ       MHZ
  0    0      0    0 0:0:0:0          yes 2001.0000 1200.0000 1200.0000
  1    0      0    1 1:1:1:0          yes 2001.0000 1200.0000 1197.1820
  2    0      0    2 2:2:2:0          yes 2001.0000 1200.0000 1200.0000
  3    0      0    3 3:3:3:0          yes 2001.0000 1200.0000 1200.0000
  4    0      0    4 4:4:4:0          yes 2001.0000 1200.0000 1200.0000
  5    0      0    5 5:5:5:0          yes 2001.0000 1200.0000 1197.3110
  6    0      0    6 6:6:6:0          yes 2001.0000 1200.0000 1200.0000
  7    0      0    7 7:7:7:0          yes 2001.0000 1200.0000 1200.0000
  8    0      0    8 8:8:8:0          yes 2001.0000 1200.0000 1200.0000
  9    0      0    9 9:9:9:0          yes 2001.0000 1200.0000 1200.0000
 10    0      0   10 10:10:10:0       yes 2001.0000 1200.0000 1200.0000
 11    0      0   11 11:11:11:0       yes 2001.0000 1200.0000 1200.0000
 12    0      0   12 12:12:12:0       yes 2001.0000 1200.0000 1200.0000
 13    0      0   13 13:13:13:0       yes 2001.0000 1200.0000 1200.0000
 14    0      0   14 14:14:14:0       yes 2001.0000 1200.0000 1200.0000
 15    0      0   15 15:15:15:0       yes 2001.0000 1200.0000 1200.0000
 16    1      1   16 16:16:16:1       yes 2001.0000 1200.0000 1200.0000
 17    1      1   17 17:17:17:1       yes 2001.0000 1200.0000 1200.0000
 18    1      1   18 18:18:18:1       yes 2001.0000 1200.0000 1200.0000
 19    1      1   19 19:19:19:1       yes 2001.0000 1200.0000 1200.0000
 20    1      1   20 20:20:20:1       yes 2001.0000 1200.0000 1200.0000
 21    1      1   21 21:21:21:1       yes 2001.0000 1200.0000 1200.0000
 22    1      1   22 22:22:22:1       yes 2001.0000 1200.0000 1200.0000
 23    1      1   23 23:23:23:1       yes 2001.0000 1200.0000 1200.0000
 24    1      1   24 24:24:24:1       yes 2001.0000 1200.0000 1200.0000
 25    1      1   25 25:25:25:1       yes 2001.0000 1200.0000 1200.0000
 26    1      1   26 26:26:26:1       yes 2001.0000 1200.0000 1200.0000
 27    1      1   27 27:27:27:1       yes 2001.0000 1200.0000 1200.0000
 28    1      1   28 28:28:28:1       yes 2001.0000 1200.0000 1202.1851
 29    1      1   29 29:29:29:1       yes 2001.0000 1200.0000 1197.1851
 30    1      1   30 30:30:30:1       yes 2001.0000 1200.0000 1201.3190
 31    1      1   31 31:31:31:1       yes 2001.0000 1200.0000 1195.7460
 32    0      0    0 0:0:0:0          yes 2001.0000 1200.0000 1200.0000
 33    0      0    1 1:1:1:0          yes 2001.0000 1200.0000 1200.0000
 34    0      0    2 2:2:2:0          yes 2001.0000 1200.0000 1200.0000
 35    0      0    3 3:3:3:0          yes 2001.0000 1200.0000 1200.0000
 36    0      0    4 4:4:4:0          yes 2001.0000 1200.0000 1200.0000
 37    0      0    5 5:5:5:0          yes 2001.0000 1200.0000 1200.0000
 38    0      0    6 6:6:6:0          yes 2001.0000 1200.0000 1200.0000
 39    0      0    7 7:7:7:0          yes 2001.0000 1200.0000 1200.0000
 40    0      0    8 8:8:8:0          yes 2001.0000 1200.0000 1200.0000
 41    0      0    9 9:9:9:0          yes 2001.0000 1200.0000 1200.0000
 42    0      0   10 10:10:10:0       yes 2001.0000 1200.0000 1200.0000
 43    0      0   11 11:11:11:0       yes 2001.0000 1200.0000 1200.0000
 44    0      0   12 12:12:12:0       yes 2001.0000 1200.0000 1200.0000
 45    0      0   13 13:13:13:0       yes 2001.0000 1200.0000 1200.0000
 46    0      0   14 14:14:14:0       yes 2001.0000 1200.0000 1200.0000
 47    0      0   15 15:15:15:0       yes 2001.0000 1200.0000 1200.0000
 48    1      1   16 16:16:16:1       yes 2001.0000 1200.0000 1387.6670
 49    1      1   17 17:17:17:1       yes 2001.0000 1200.0000 1200.0000
 50    1      1   18 18:18:18:1       yes 2001.0000 1200.0000 1200.0000
 51    1      1   19 19:19:19:1       yes 2001.0000 1200.0000 1200.0000
 52    1      1   20 20:20:20:1       yes 2001.0000 1200.0000 1200.0000
 53    1      1   21 21:21:21:1       yes 2001.0000 1200.0000 1200.0000
 54    1      1   22 22:22:22:1       yes 2001.0000 1200.0000 1200.0000
 55    1      1   23 23:23:23:1       yes 2001.0000 1200.0000 1200.0000
 56    1      1   24 24:24:24:1       yes 2001.0000 1200.0000 1200.0000
 57    1      1   25 25:25:25:1       yes 2001.0000 1200.0000 1200.0000
 58    1      1   26 26:26:26:1       yes 2001.0000 1200.0000 1200.0000
 59    1      1   27 27:27:27:1       yes 2001.0000 1200.0000 1200.0000
 60    1      1   28 28:28:28:1       yes 2001.0000 1200.0000 1200.0000
 61    1      1   29 29:29:29:1       yes 2001.0000 1200.0000 1200.0000
 62    1      1   30 30:30:30:1       yes 2001.0000 1200.0000 1200.0000
 63    1      1   31 31:31:31:1       yes 2001.0000 1200.0000 1200.0000
```

