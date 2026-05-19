---
title: Jenkins持续集成从入门到精通
published: 2026-05-17
updated: 2026-05-17
description: RockyLinux初始化
image: "api"   
tags: [RockyLinux]
category: linux
draft: false
---



# Jenkins持续集成从入门到精通

## 持续集成及Jenkins介绍

### 什么是持续集成

持续集成（Continuous integration, 简称CI） 指的是，频繁的（一天多次的）将代码集成到主分支

**持续集成的目的，就是让产品可以快速迭代，同时还能保持高质量。** 

步骤就是：代码进主分支前，必须通过自动化测试。

### 步骤

* 提交：提交代码
* 测试（第一轮）：代码仓库对commit操作配置了钩子（hook），只要提交代码或者合拼进主干，就会跑自动化测试
* 构建：就是将源代码转换为可以运行的实际代码
* 部署：将这个版本的所以文件打包存档，发到生成服务器
* 回滚：发现版本有问题，就要回滚到稳定版本

![image-20260514204156036](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/imgs/20260514204158991.png)