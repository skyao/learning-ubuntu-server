---
title: "配置dns server"
linkTitle: "dns"
weight: 25
date: 2021-08-30
description: >
  配置 dns server
---


不能直接修改 `/etc/resolv.conf` ，重启之后会被 systemd-resolved 还原。


```bash
sudo vi /etc/systemd/resolved.conf
```

在 [Resolve] 一节中加入 dsn server ，如：

```bash
[Resolve]
DNS=8.8.8.8
```

之后重启 systemd-resolved 服务：

```bash
systemctl restart systemd-resolved
```

可以重启机器后试试 dns 设置是否还有效。

