---
title: "mkisofs命令"
linkTitle: "mkisofs"
weight: 100
date: 2023-07-30
description: >
  mkisofs 命令用于制作 ISO 文件
---



## 制作 ISO 文件

### 使用某个目录的文件

使用指定文件夹中的所有文件制作 iso 文件：

```bash
mkisofs -r -o file.iso your_folder_name/
```

可以选择生成对应 MD5 文件，只要执行：

```bash
md5sum file.iso > file.iso.md5
```





## 参考资料

- https://blog.51cto.com/u_15127517/4572026
