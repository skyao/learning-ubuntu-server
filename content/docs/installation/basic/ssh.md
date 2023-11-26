---
title: "SSH登录"
linkTitle: "SSH登录"
weight: 20
date: 2021-01-24
description: >
  使用新用户SSH登录服务器
---

### 安装openssh(可选)

安装ssh软件，以便从外部直接ssh进来，而不用在服务器上操作。通常选择安装openssh，有些服务器默认有安装openssh，如果服务器默认没有安装ssh软件，则：

```bash
sudo apt-get install openssh-server
```

### 使用密码远程登录

执行：

```bash
ssh sky@ubuntu.server.ip
```

输入密码即可。

### 使用密钥远程登录

为了进一步方便使用，不必每次输入密码, 还可以通过authorized_keys 的方式来自动登录。

上传本机的 `.ssh/id_isa.pub` 文件到ubuntu server服务器端：

```bash
scp ~/.ssh/id_rsa.pub sky@192.168.0.10:/home/sky 
```

在ubuntu server服务器上运行：

```bash
mkdir -p .ssh
touch ~/.ssh/authorized_keys
cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
```

以后简单输入 "ssh ubuntu.server.ip" 即可自动登录。

### 快捷登录

如果服务器ip地址固定，则可以进一步简化，修改本机的`/etc/hosts`文件，加入下面内容：

```bash
# local machine list
192.168.100.10            skywork
192.168.100.20            skywork2
192.168.100.30            skydownload
192.168.100.40            skyserver
192.168.100.50            skyserver2
192.168.100.60            skyserver3
```

以后简单输入 "ssh skyserver" 即可。

### 指定特定端口登录

ssh默认采用22端口，对于需要进行端口映射导致不能访问22端口的情况，需要在ssh时通过 `-p` 参数指定具体的端口。

如下面的命令，有一台服务器的22端口是通过路由器的2122端口进行端口映射，则远程ssh连接的命令为：

```bash
ssh -p 2122 sky@dev.sky.io
```

修改本机的 `~/.bash_profile` 或者 `~/.zshrc` 文件，加入以下内容，以后就可以一个简单命令直接ssh到远程服务器了：

```bash
# ssh to home
alias sshwork="ssh sky@skywork"
alias sshwork2="ssh sky@skywor2"
alias sshserver="ssh sky@skyserver"
alias sshserver2="ssh sky@skyserver2"
alias sshserver3="ssh sky@skyserver3"
```

### 特别补充

#### 腾讯云

在腾讯云上购买的linux主机，使用 SSH 登录：

https://cloud.tencent.com/document/product/1207/44643

>  特别提醒：首次通过本地 SSH 客户端登录 Linux 实例之前，您需要重置默认用户名（root）的密码，或者绑定密钥。否则会直接报错。

