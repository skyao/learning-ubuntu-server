---
title: "azure-cli"
linkTitle: "azure-cli"
weight: 50
description: >
  azure cli 安装配置
---

参考官方文档：

[Install the Azure CLI on Linux](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt)

## LTS 版本

对于 18.04 LTS (Bionic Beaver), 20.04 LTS (Focal Fossa), 22.04 (Jammy Jellyfish) 这些 LST 版本，可以通过下面的命令一次性安装：

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

也可以一步一步安装，详情见官方文档。

```bash
az --version    
```

检查一下：

```bash
                                      
azure-cli                         2.49.0

core                              2.49.0
telemetry                          1.0.8

Dependencies:
msal                              1.20.0
azure-mgmt-resource               22.0.0

Python location '/opt/az/bin/python3'
Extensions directory '/home/sky/.azure/cliextensions'

Python (Linux) 3.10.10 (main, May 19 2023, 08:20:32) [GCC 9.4.0]

Legal docs and information: aka.ms/AzureCliLegal


Your CLI is up-to-date.
```



## 非 LTS 版本

理论上非 LTS 版本是不支持的。

### 22.10

