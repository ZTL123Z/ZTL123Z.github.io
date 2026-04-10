---
title: Linux压缩和解压命令
published: 2026-04-10           # 是  发布日期
description:  Linux查找指令,find,locate       # 否  文章简短描述，显示在首页文章卡片上
image: "api"   # 否  封面图片路径 可以写 "api"
tags: [linux, centos]       # 否  文章标签
category: linux       # 否  文章分类
draft: false       # 否  是否为草稿，草稿不会对读者可见
---

## gzip/gunzip

```shell
gzip // 用于压缩文件
gunzip // 用于解压的
```

```shell
[root@localhost code]# ls
hello.txt
[root@localhost code]# gzip hello.txt 
[root@localhost code]# ls
hello.txt.gz
[root@localhost code]# gunzip hello.txt.gz 
[root@localhost code]# ls
hello.txt
```

## zip/unzip

- zip用于压缩文件
- unzip用于解压

```shell
zip [选项] xxx.zip //将要压缩的文件和目录命令
unzip [选项] xxx.zip //解压文件
zip常用选项 -r : 递归压缩，用于压缩目录
unzip常用选项 -d : 用于指定解压后文件的存放目录
```

```shell
[root@localhost /]# ls
bin   dev  home  lib64       media  myhome.zip  proc  run   srv  tmp  var
boot  etc  lib   lost+found  mnt    opt         root  sbin  sys  usr
[root@localhost /]# zip myhome.zip -r /home/
```

```shell
[root@localhost /]# unzip myhome.zip -d /opt/tmp/
Archive:  myhome.zip
```

## tar 指令

tar指令是打包指令，最后打包的文件是.tar.gz的文件

> tar [选项] xxx.tar.gz  打包的内容

| 选项 |        功能        |
| :--: | :----------------: |
|  -c  |  产生.tar打包文件  |
|  -v  |    显示详细信息    |
|  -f  | 指定压缩后的文件名 |
|  -z  |    打包同时压缩    |
|  -x  |    解包.tar文件    |
|  -C  |  指定解压后的目录  |

压缩

```shell
sudo tar -zcvf pc.tar.gz /home/pig.txt /home/cat.txt  // f必须放到最后，不然会报错
```

解压

```shell
tar -zxvf pc.tar.gz -C /home // f必须放到最后，不然会报错
tar -zxvf  压缩包名  -C   要解压到的目录
```



