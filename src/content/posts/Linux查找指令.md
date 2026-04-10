---
title: Linux查找指令
published: 2026-04-10           # 是  发布日期
description:  Linux查找指令,find,locate       # 否  文章简短描述，显示在首页文章卡片上
image: "api"   # 否  封面图片路径 可以写 "api"
tags: [linux, centos]       # 否  文章标签
category: linux       # 否  文章分类
draft: false       # 否  是否为草稿，草稿不会对读者可见
---

## find 指令

### find  开始路径 -name 文件名

安照文件名进行查找文件

```shell
find /home -name hello.txt
```

### find 开始路径 -user 用户名

按照用户来查找文件

```shell
find /opt -user tom
```

### find -size

按照文件大小来查找文件

- +n:  大于 n的文件
- n： 等于n的文件
- -n：小于n的文件

```shell
find / -size +200M // 单位有K，M，G 
```



## locate

locate指令可以快速定位文件路径

:::note 

由于locate指令基于数据库进行查询，所以第一次运行前必须使用**updatedb**指令创建locate数据库

:::

## grep  过滤查找

- -n ：显示匹配行号
- -i ：忽略大小写

```shell
cat hello.txt | grep -n "yes" // 查找hello.txt里面包含yes的行
```



