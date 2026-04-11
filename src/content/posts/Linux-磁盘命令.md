---
title: Linux-磁盘命令
published: 2026-04-11          
description:  磁盘命令       
image: "api"   
tags: [linux]      
category: linux      
draft: false       
---

## 添加磁盘，分区并挂载

### lsblk 查看磁盘和分区情况

```shell
[talen@localhost ~]$ lsblk // list block
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part 
  ├─centos-root 253:0    0   17G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sr0              11:0    1  4.3G  0 rom  /run/media/talen/CentOS 7 x86_64
```

### fdisk 分区命令

![image-20260411151735529](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260411151735627.png)

### mkfs格式化磁盘准备使用

```shell
mkfs -t ext4 /dev/sdb1 
```

### mount挂载磁盘开始使用

> 注意命令挂载是临时的，重启失效

![image-20260411152042432](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260411152042571.png)

> 写入 /etc/fatab 永久生效

![image-20260411152252769](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260411152252855.png)

## 查看磁盘使用情况

### 查询系统整体磁盘使用情况 df -h 

```shell
[talen@localhost ~]$ df -h
文件系统                 容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root   17G  4.0G   12G   26% /
devtmpfs                 894M     0  894M    0% /dev
tmpfs                    910M     0  910M    0% /dev/shm
tmpfs                    910M   10M  900M    2% /run
tmpfs                    910M     0  910M    0% /sys/fs/cgroup
/dev/sda1                976M  149M  761M   17% /boot
/dev/sr0                 4.3G  4.3G     0  100% /run/media/talen/CentOS 7 x86_64
tmpfs                    182M     0  182M    0% /run/user/1000
```

### 查询指定目录的磁盘占用情况 du -h

`du -h /opt`

- -s 指定目录占用大小总汇
- -h 带计量单位
- -a 含文件
- --max-depth = 1 子目录深度
- -c 列出明细的同时，增加总汇值

```shell
[talen@localhost ~]$ sudo du -sh /opt
4.7M	/opt
[talen@localhost ~]$ sudo du -sha /opt //sha 傻
du: 不能既显示总用量，同时又显示每个项目
```

