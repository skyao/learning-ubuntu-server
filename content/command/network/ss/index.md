---
title: "ss 命令"
linkTitle: "ss"
weight: 20
date: 2022-03-16
description: >
  ss 命令
---



## 介绍

ss 是 Socket Statistics 的缩写。

ss 命令可以用来获取 socket 统计信息，它显示的内容和 netstat 类似。但 ss 的优势在于它能够显示更多更详细的有关 TCP 和连接状态的信息，而且比 netstat 更快。当服务器的 socket 连接数量变得非常大时，无论是使用 netstat 命令还是直接 cat /proc/net/tcp，执行速度都会很慢。

ss 命令利用到了 TCP 协议栈中 tcp_diag。tcp_diag 是一个用于分析统计的模块，可以获得 Linux 内核中第一手的信息，因此 ss 命令的性能会好很多。



## 用法

### 常用选项

-h, –help 帮助

 -V, –version 显示版本号 

-t, –tcp 显示 TCP 协议的 sockets 

-u, –udp 显示 UDP 协议的 sockets 

-x, –unix 显示 unix domain sockets，与 -f 选项相同 

-n, –numeric 不解析服务的名称，如 “22” 端口不会显示成 “ssh” 

-l, –listening 只显示处于监听状态的端口 

-p, –processes 显示监听端口的进程(Ubuntu 上需要 sudo) 

-a, –all 对 TCP 协议来说，既包含监听的端口，也包含建立的连接 

-r, –resolve 把 IP 解释为域名，把端口号解释为协议名称

具体可见：

```bash
s -h
Usage: ss [ OPTIONS ]
       ss [ OPTIONS ] [ FILTER ]
   -h, --help          this message
   -V, --version       output version information
   -n, --numeric       don't resolve service names
   -r, --resolve       resolve host names
   -a, --all           display all sockets
   -l, --listening     display listening sockets
   -o, --options       show timer information
   -e, --extended      show detailed socket information
   -m, --memory        show socket memory usage
   -p, --processes     show process using socket
   -i, --info          show internal TCP information
       --tipcinfo      show internal tipc socket information
   -s, --summary       show socket usage summary
       --tos           show tos and priority information
   -b, --bpf           show bpf filter socket information
   -E, --events        continually display sockets as they are destroyed
   -Z, --context       display process SELinux security contexts
   -z, --contexts      display process and socket SELinux security contexts
   -N, --net           switch to the specified network namespace name

   -4, --ipv4          display only IP version 4 sockets
   -6, --ipv6          display only IP version 6 sockets
   -0, --packet        display PACKET sockets
   -t, --tcp           display only TCP sockets
   -S, --sctp          display only SCTP sockets
   -u, --udp           display only UDP sockets
   -d, --dccp          display only DCCP sockets
   -w, --raw           display only RAW sockets
   -x, --unix          display only Unix domain sockets
       --tipc          display only TIPC sockets
       --vsock         display only vsock sockets
   -f, --family=FAMILY display sockets of type FAMILY
       FAMILY := {inet|inet6|link|unix|netlink|vsock|tipc|xdp|help}

   -K, --kill          forcibly close sockets, display what was closed
   -H, --no-header     Suppress header line
   -O, --oneline       socket's data printed on a single line

   -A, --query=QUERY, --socket=QUERY
       QUERY := {all|inet|tcp|udp|raw|unix|unix_dgram|unix_stream|unix_seqpacket|packet|netlink|vsock_stream|vsock_dgram|tipc}[,QUERY]

   -D, --diag=FILE     Dump raw information about TCP sockets to FILE
   -F, --filter=FILE   read filter information from FILE
       FILTER := [ state STATE-FILTER ] [ EXPRESSION ]
       STATE-FILTER := {all|connected|synchronized|bucket|big|TCP-STATES}
         TCP-STATES := {established|syn-sent|syn-recv|fin-wait-{1,2}|time-wait|closed|close-wait|last-ack|listening|closing}
          connected := {established|syn-sent|syn-recv|fin-wait-{1,2}|time-wait|close-wait|last-ack|closing}
       synchronized := {established|syn-recv|fin-wait-{1,2}|time-wait|close-wait|last-ack|closing}
             bucket := {syn-recv|time-wait}
                big := {established|syn-sent|fin-wait-{1,2}|closed|close-wait|last-ack|listening|closing}

```

