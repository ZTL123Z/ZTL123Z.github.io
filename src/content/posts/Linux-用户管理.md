---
title: Linux_用户管理
category: linux
tags:
  - linux
  - 权限管理
published: 2026-04-09 15:15:26
---

## 添加用户&修改密码

### 添加用户

```shell
useradd 用户名
useradd 用户名 -d 路径 // 指定用户home文件夹
```

### 修改用户密码

```shell
passwd // 默认修改当前用户密码
passwd 用户名 // 修改指定用户密码
```

### 删除用户

```shell
userdel 用户名 // 删除用户
userdel -r 用户名 // 删除用户并删除用户目录
```

### 查询用户

```shell
id 用户名
```

### 切换用户

```shell
su // 切换为root用户
su 用户 // 切换指定用户，权限高到低不用密码，反之需要密码
exit/logout // 退出当前用户
```

### 查看当前用户

```shell
[tom@localhost home]$ whoami // 查看当前用户
tom
[tom@localhost home]$ who am i // 查看用什么用户进行连接，最原始的用户
talen    pts/0        2026-04-09 15:21 (192.168.44.1)

```

### 用户组

```shell
groupadd 组名 // 添加用户组
groupdel 组名 // 删除用户组
useradd -g 用户组 用户名 // 创建用户的时候选择用户组
usermod -g 用户组 用户名 // 修改用户组
```

![image-20260409160138985](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409160139972.png)
