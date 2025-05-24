---
title: "删除Linux内核"
linkTitle: "删除内核"
weight: 20
date: 2022-01-20
description: >
  删除不用的Linux内核
---

多次升级之后，系统内就会累计有多个内核版本，可以考虑删除旧的不用的内核。

参考：

https://askubuntu.com/questions/1253347/how-to-easily-remove-old-kernels-in-ubuntu-20-04-lts

```bash
mkdir ~/work/soft/ubuntu
cd ~/work/soft/ubuntu
vi remove_old_kernels.sh
```

新建一个文件内容如下：

```bash
#!/bin/bash
# Run this script without any param for a dry run
# Run the script with root and with exec param for removing old kernels after checking
# the list printed in the dry run

uname -a
IN_USE=$(uname -a | awk '{ print $3 }')
if [[ $IN_USE == *-generic ]]
then
  IN_USE=${IN_USE::-8}
fi
echo "Your in use kernel is $IN_USE"

OLD_KERNELS=$(
    dpkg --list |
        grep -v "$IN_USE" |
        grep -v "linux-headers-generic" |
        grep -v "linux-image-generic"  |
        grep -Ei 'linux-image|linux-headers|linux-modules' |
        awk '{ print $2 }'
)
echo "Old Kernels to be removed:"
echo "$OLD_KERNELS"

if [ "$1" == "exec" ]; then
    for PACKAGE in $OLD_KERNELS; do
        yes | apt purge "$PACKAGE"
    done
fi
```

执行 

```bash
bash ./remove_old_kernels.sh
``` 

看查看到要删除的内核版本和相关的包，确认没有问题之后再通过 

```bash
sudo bash ./remove_old_kernels.sh exec
``` 

进行实际删除。

之后重启，执行： 

```bash
dpkg --list | grep -Ei 'linux-image|linux-headers|linux-modules' 
```

检查现有的内核： 

```bash
ii  linux-headers-5.15.0-140               5.15.0-140.150                          all          Header files related to Linux kernel version 5.15.0
ii  linux-headers-5.15.0-140-generic       5.15.0-140.150                          amd64        Linux kernel headers for version 5.15.0 on 64 bit x86 SMP
ii  linux-headers-generic                  5.15.0.140.135                          amd64        Generic Linux kernel headers
ii  linux-image-5.15.0-140-generic         5.15.0-140.150                          amd64        Signed kernel image generic
ii  linux-image-generic                    5.15.0.140.135                          amd64        Generic Linux kernel image
ii  linux-modules-5.15.0-140-generic       5.15.0-140.150                          amd64        Linux kernel extra modules for version 5.15.0 on 64 bit x86 SMP
ii  linux-modules-extra-5.15.0-140-generic 5.15.0-140.150                          amd64        Linux kernel extra modules for version 5.15.0 on 64 bit x86 SMP
```
