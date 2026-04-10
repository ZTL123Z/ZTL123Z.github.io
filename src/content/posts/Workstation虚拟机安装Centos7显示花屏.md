---
title: Workstation虚拟机安装Centos7显示花屏
published: 2026-04-09 
category: vmware
tags: 
  - vmware
  - centos7
  - linux
---

## 问题

<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409121110668.png" alt="116cff1f-b2fd-4fd5-bf21-f9d26b4846cb" style="zoom:50%;" />





<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409121301628.png" alt="d57d5112-ada3-4bda-961a-f95ed200c3f6" style="zoom:50%;" />

## 解决方法

### 取消加速3D图形化





<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409121310634.png" alt="8ba02939-63fd-4845-9ccc-2889df654f4d" style="zoom:50%;" />

### 更新vm_tools

https://packages-prod.broadcom.com/tools/frozen/linux/linux.iso 

<img src="C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20260409124107084.png" alt="image-20260409124107084" style="zoom:50%;" />

这个报错是因为 **高版本 VMware Workstation 已停止为 CentOS 7 这类旧版系统内置传统 VMware Tools 安装包**，官方推荐使用开源的 `open-vm-tools` 替代，下面给你分「**推荐方案（一键安装）**」和「**传统方案（手动安装）**」两种方法，可直接复制执行。

```shell
yum install -y open-vm-tools open-vm-tools-desktop
```

