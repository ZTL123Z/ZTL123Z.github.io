---
title: 虚拟机启动Centos没有ipv4地址
published: 2026-04-09 
category: Centos
tags: 
  - vmware
  - Centos	 
  - ipv4
---

## 问题

```shell
[talen@localhost ~]$ ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        ether 00:0c:29:88:2f:cd  txqueuelen 1000  (Ethernet)
        RX packets 16  bytes 960 (960.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

## 解决

1. 先编辑网卡配置文件

```shell
sudo vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

```shell
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=e5368d5d-5d61-4178-9753-5696932a62dc
DEVICE=ens33
ONBOOT=no
```

你这里 **ONBOOT=no**，意思是：开机**不自动启动网卡**，所以一直拿不到 IP。

解决

![image-20260409134543845](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409134544070.png)
