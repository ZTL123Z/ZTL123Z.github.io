---
title: Linux-进程
published: 2026-04-11          
description: 系统进程       
image: "api"   
tags: [linux, 进程]      
category: linux      
draft: false       
---

## ps 命令

ps命令是用来查看目前系统中，有哪些正在执行，以及它们的执行的状况。可以不加任何参数。

ps -af ：带 **%CPU、%MEM**，能直接看 CPU、内存占用；有进程状态 STAT（S 睡眠、R 运行、Z 僵尸等）

ps -ef ：显示**父进程 ID（PPID）**，方便看进程树关系；没有 CPU、内存占比，只看进程结构

| 选项 | 作用                                                 |
| :--: | :--------------------------------------------------- |
|  -a  | 显示当前终端的所以进程信息,比e多显示了其他用户的进程 |
|  -u  | 以用户的格式显示进程信息                             |
|  -x  | 显示后台进程运行的参数                               |
|  -e  | every process 所有进程（和 a 效果一样）              |
|  -f  | 完整格式                                             |

![image-20260411171301617](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260411171301928.png)

|  参数   | 信息                 |
| :-----: | -------------------- |
|  USER   | 进程执行的用户       |
|   PID   | 进程号               |
|   CPU   | 占用CPU的百分比      |
|   MEM   | 占用物理内存的百分比 |
|   VSZ   | 占用虚拟内存大小     |
|   RSS   | 占用实际内存大小     |
|   TTY   | 终端信息             |
|  SATA   | 运行状态             |
|  START  | 运行开始时间         |
|  TIME   | 占用CPU运行时间      |
| COMMAND | 执行改进程的指令     |

## kill 终止命令

### 基本语法

`kill [选项] 进程号` （通过进程号杀死进程）

`killall 进程名称`  （通过进程名称杀死进程，也支持通配符，下面所有子进程也会被杀死）

- 常用选项 -9 ：表示强迫进程立即终止

### 查看进程树

`pstree [选项]`，可以更加直观的来看进程信息

- -p: 显示进程PID
- -u: 显示进程的所属用户

> pstree -pu

![image-20260411193112054](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260411193112113.png)

## 服务(service) 管理，已淘汰，只看概念

### 服务service

服务（service）本质就是进程，但是是运行在后台的，通常都会监听某个端口，等待其它程序的请求，比如（mysqld，sshd，防火墙等），因此又被称为守护进程。

- service管理指令

1. `service 服务名[start| stop| restart | reload | status]`
2. centos7后面大多服务使用`systemctl`
3. 查看service指令，/etc/init.d      //绿色是service可用

<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260411194639436.png" alt="image-20260411194639405" style="zoom:150%;" />

### systemctl 管理指令

1. `systemctl [start | stop | restart | status] 服务名`
2. 在/usr/lib/systemd/system 查看

systemctl设置服务的自启动状态

1. systemctl list-unit-files [| grep 服务名] （查看服务开机启动状态）
2. systemctl enable 服务名 （设置服务开机自启）
3. systemctl disable 服务名（关闭服务开机自启）
4. systemctl is-enable 服务名 （查询某个服务是否自启动）

## firewall指令

- 打开端口：firewall-cmd --permanent --add-port=端口号/协议
- 关闭端口：firewall-cmd --permanent --remove-port=端口号/协议
- 重新载入，才能生效：firewall-cmd --reload
- 查看端口是否开放：firewall-cmd --query-port=端口号/协议

## 动态监控进程

top与ps命令很像。最大的不同是top在执行一段时间可以更新正在运行的进程。

- top [选项]

| 选项 | 功能                                   |
| ---- | -------------------------------------- |
| -d   | 几秒更新一次，默认3秒                  |
| -i   | 让top不显示任何闲置或者僵死的进程      |
| -p   | 通过指定监控进程ID来监控某个进程的状态 |

![image-20260411205157317](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260411205157523.png)

- 交互操作说明

| 操作 | 说明                       |
| :--: | -------------------------- |
|  P   | 以CPU使用率排序            |
|  M   | 以内存的使用率排序         |
|  N   | 以PID排序                  |
|  q   | 退出                       |
|  u   | 再输入用户民，监控指定用户 |
|  k   | 再输入要结束进程的PID      |

## 监控网络状态

### 查看系统网络情况netstat

- `netstat [选项]`
- -an ：按一定顺序排序输出
- -p：显示哪个进程在调用

> netstat -an

![image-20260411212440468](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260411212440638.png)

> netstat -anp  //添加显示什么进程在调用

![image-20260411212548547](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260411212548709.png)