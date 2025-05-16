---
title: "安装后常出现的问题"
linkTitle: "常见问题"
weight: 20
date: 2022-01-06
description: >
  Ubuntu Server 安装后常出现的问题
---



### 登录后出现无法连接到 ubuntu.com 的错误提示

ssh 登录后，有时（主要是 ubuntu 22.04 下）会遇到这样的提示：

```bash
ssh sky@192.168.0.152
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-77-generic x86_64)

......

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Mon Jun 26 08:10:26 2023 from 192.168.0.90
```

解决的方式如下：

```bash
sudo rm /var/lib/ubuntu-release-upgrader/release-upgrade-available
/usr/lib/ubuntu-release-upgrader/release-upgrade-motd
```

重新登录就不会再看到错误提示了:

```bash
ssh sky@192.168.0.152
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Jun 26 03:15:45 PM UTC 2023

  System load: 0.00537109375      Memory usage: 2%   Processes:       258
  Usage of /:  1.5% of 441.87GB   Swap usage:   0%   Users logged in: 0


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


Last login: Mon Jun 26 08:31:13 2023 from 192.168.0.90
```

参考：

- [[已解决]Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings](https://www.cnblogs.com/xsjzhao/p/11001541.html)
