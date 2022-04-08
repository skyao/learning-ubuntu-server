---
title: "Linux find 命令的 -exec 选项"
linkTitle: "exec 选项"
weight: 10
date: 2021-03-03
description: >
  Linux find 命令的 -exec 选项
---



```bash
find . -type f -exec grep "192.168.0.30:10808" {} \;
```

