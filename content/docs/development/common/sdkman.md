---
title: "sdkman"
linkTitle: "sdkman"
weight: 10
description: >
  管理 SDK 的多个版本
---



SDKMAN 是一款管理多版本 SDK 的工具，可以实现在多个版本间的快速切换。

> sdkman 支持 macos 和 linux，可以参考：
> 
> https://skyao.io/learning-macos/docs/programing/common/sdkman.html

### 安装sdkman

### macOS

```bash
$ curl -s "https://get.sdkman.io" | bash

$ source "/home/sky/.sdkman/bin/sdkman-init.sh"
$ sdk version
SDKMAN 5.15.0
```



### ubuntu

```bash
$ sudo apt install unzip zip
$ curl -s "https://get.sdkman.io" | bash

$ source "/home/sky/.sdkman/bin/sdkman-init.sh"
$ sdk version

SDKMAN 5.15.0
```



