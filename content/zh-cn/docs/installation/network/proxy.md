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
# proxy
alias proxyon='export all_proxy=socks5://192.168.0.1:7891;export http_proxy=http://192.168.0.1:7890;export https_proxy=http://192.168.0.1:7890;export no_proxy=127.0.0.1,localhost,local,.local,.lan,192.168.0.0/16,10.0.0.0/16'
alias proxyoff='unset all_proxy http_proxy https_proxy no_proxy'
```

> 背景：我的代理安装在路由器上，http端口为 7890， socks5 端口为 7891

### 给git配置代理

```bash
vi ~/.ssh/config
```

内容如下：

```bash
Host github.com
HostName github.com
User git
# http proxy
#ProxyCommand socat - PROXY:192.168.0.1:%h:%p,proxyport=7890
# socks5 proxy
ProxyCommand nc -v -x 192.168.0.1:7891 %h %p
```

