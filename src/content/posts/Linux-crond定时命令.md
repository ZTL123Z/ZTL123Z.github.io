---
title: Linux-crond定时命令&at一次性定时
published: 2026-04-10           # 是  发布日期
description:  Linux-crond定时命令       # 否  文章简短描述，显示在首页文章卡片上
image: "api"   # 否  封面图片路径 可以写 "api"
tags: [linux, crond]       # 否  文章标签
category: linux       # 否  文章分类
draft: false       # 否  是否为草稿，草稿不会对读者可见
---

# crond定时任务

## 快速入门

- 设置任务调度文件：/etc/crontab
- 设置个人任务调度。执行corntab -e命令

```shell
*/1 * * * * ls -l /etc/ > /tmp/to.txt 
// 意思是每小时的每分钟执行ls -l /etc > /tmp/to.txt 命令
```

## 参数细节说明

![image-20260410211849874](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260410211850122.png)

## 特殊符号说明

![image-20260410212005730](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260410212005817.png)

> 任务案例

![image-20260410212108576](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260410212108705.png)

## 相关命令

- `crontab -r` : 终止任务调度
- `crontab -l` ：列出当前有哪些任务调度
- `service crond restart` ： 重启任务调度

# at 一次性定时任务

## 基本介绍

1. at命令是一次性定时任务，at的守护进程atd会以后台模式运行，检查左右队列来运行
2. 默认情况下，atd守护进程每60秒检查左右队列
3. at命令是一次性定时任务，执行完一个任务后不在执行此任务
4. 使用at命令的时候，一定要保证atd进程的启动，用 ps -ef | grep atd

## at命令选项

![image-20260410214925376](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260410214925434.png)

## at时间定义的6种写法

![image-20260410215057333](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260410215057586.png)