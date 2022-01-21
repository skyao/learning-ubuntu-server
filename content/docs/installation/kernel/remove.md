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

`vi remove_old_kernels.sh` 新建一个文件内容如下：

```bash
#!/bin/bash
# Run this script without any param for a dry run
# Run the script with root and with exec param for removing old kernels after checking
# the list printed in the dry run

uname -a
# IN_USE=$(uname -a | awk '{ print $3 }')
IN_USE="5.4.0.97"
echo "Your in use kernel is $IN_USE"

OLD_KERNELS=$(
    dpkg --list |
        grep -v "$IN_USE" |
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

这个脚本有个小问题，就是有时 `uname -a` 拿到的内核版本号，有时会带有 generic 后缀， 比如：

```bash
$ uname -a
Linux skyserver 5.4.0-97-generic #110-Ubuntu SMP Thu Jan 13 18:22:13 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
```

手工hardcode一个版本号就好了，反正平时删除内核的机会也不多。

执行 `./remove_old_kernels.sh` 看查看到要删除的内核版本和相关的包，确认没有问题之后再通过 `sudo ./remove_old_kernels.sh exec` 进行实际删除。之后重启，检查现有的内核： 

```bash
dpkg --list | grep -Ei 'linux-image|linux-headers|linux-modules' 
ii  linux-headers-5.4.0-97               5.4.0-97.110                            all          Header files related to Linux kernel version 5.4.0
ii  linux-headers-5.4.0-97-generic       5.4.0-97.110                            amd64        Linux kernel headers for version 5.4.0 on 64 bit x86 SMP
ii  linux-headers-generic                5.4.0.97.101                            amd64        Generic Linux kernel headers
ii  linux-image-5.4.0-97-generic         5.4.0-97.110                            amd64        Signed kernel image generic
ii  linux-image-generic                  5.4.0.97.101                            amd64        Generic Linux kernel image
ii  linux-modules-5.4.0-97-generic       5.4.0-97.110                            amd64        Linux kernel extra modules for version 5.4.0 on 64 bit x86 SMP
ii  linux-modules-extra-5.4.0-97-generic 5.4.0-97.110                            amd64        Linux kernel extra modules for version 5.4.0 on 64 bit x86 SMP
```
