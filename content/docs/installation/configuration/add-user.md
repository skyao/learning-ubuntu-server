---
title: "添加新用户"
linkTitle: "添加新用户"
weight: 212
date: 2021-01-24
description: >
  添加新用户以便日常使用
---

### 添加新用户

默认安装后只有root账户，肯定不能直接用root。

> 注： 如果是用vmware自动安装，则会提前录入一个用户名，安装完成之后就可以直接用这个用户名。这样就可以跳过这个步骤。

因此增加一个日常使用的用户，这个用户需要拥有 sudo 的权限，以便在必要时可以得到 root 权限：

```bash
sudo adduser sky
sudo adduser sky sudo
```

后续可以通过 passwd 命令修改密码：

```bash
sudo passwd sky
```
