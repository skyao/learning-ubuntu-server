---
title: "网络代理快捷命令"
linkTitle: "网络代理"
weight: 10
date: 2021-08-30
description: >
  设置启用网络代理的快捷命令，方便随时开启和关闭网络代理
---

### 手动启用代理

将以下内容添加到 .zshrc :


```properties
#proxy enable
alias proxyon='export all_proxy=socks5://192.168.0.1:23456;export http_proxy=http://192.168.0.1:3333;export https_proxy=http://192.168.0.1:3333'
alias proxyoff='unset all_proxy http_proxy https_proxy'
```

> 背景：我的代理安装在路由器上，http端口为 3333， socks5 端口为 23456

### 给git配置代理

`vi ~/.ssh/config` ，内容如下：

```bash
# 这里必须是 github.com，因为这个跟我们 clone 代码时的链接有关
Host github.com
# 如果用默认端口，这里是 github.com，如果想用443端口，这里就是 ssh.github.com 详
# 见 https://help.github.com/articles/using-ssh-over-the-https-port/
HostName github.com
User git
# 如果是 HTTP 代理，把下面这行取消注释，并把 proxyport 改成自己的 http 代理的端>口
#ProxyCommand socat - PROXY:127.0.0.1:%h:%p,proxyport=3333
# 如果是 socks5 代理，则把下面这行取消注释，并把 6666 改成自己 socks5 代理的端口
ProxyCommand nc -v -x 192.168.0.1:23456 %h %p
```

