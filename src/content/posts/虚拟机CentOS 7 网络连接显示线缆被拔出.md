---
title: 虚拟机CentOS 7 网络连接显示"线缆被拔出"       
published: 2026-04-10         
description: 虚拟机CentOS 7 网络连接显示"线缆被拔出" 
image: ./“api”   
tags: [linux,centos]       
category: linux      
draft: false       
---

## 虚拟机centOS 7显示“线缆被拔出”的原因是两个服务被禁用了。

![img](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260410152756662.png)

## 首先查看网络连接方式

![img](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260410152826769.png)



<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260410152834134.png" alt="img" style="zoom:150%;" />

## 开启相关服务

- 在桌面选中“计算机”，左键“管理”，打开计算机管理窗口。
- 点击“服务和应用”，选择“服务”，找到VMware DHCP Service”和”VMware NAT Service“服务。
- 鼠标右击，选择“启动”。

![img](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260410152909706.png)

