---
title: "安装配置sftp"
linkTitle: "sftp"
weight: 40
date: 2021-08-26
description: >
  在ubuntu server上安装sftp
---



### 准备group和user

```bash
sudo addgroup sftpgroup
sudo useradd -m sftpuser -g sftpgroup
sudo passwd sftpuser

sudo chmod 700 /home/sftpuser/
```



### 配置 ssh service

修改 `/etc/ssh/sshd_config`

```properties
Match group sftpgroup
ChrootDirectory /home
X11Forwarding no
AllowTcpForwarding no
ForceCommand internal-sftp
```

