---
title: Nginx的安装
published: 2026-05-07
updated: 2026-05-07
description: Nginx的安装
image: "api"   
tags: [Nginx]
category: Linux
draft: false
---
#Nginx的安装

## 版本区别

版本区别

常用版本分为四大阵营

Nginx开源版
http://nginx.org/

Nginx plus 商业版
https://www.nginx.com

openresty
http://openresty.org/cn/

Tengine
http://tengine.taobao.org

Nginx的安装可以选择源码编译的方式也可以使用宝塔面板安装，本文采用的是源码编译安装。

## 使用源码编译安装

```sh
[root@localhost opt]# tar -zxvf nginx-1.21.6.tar.gz
[root@localhost nginx-1.21.6]# ls
auto  CHANGES  CHANGES.ru  conf  configure  contrib  html  LICENSE  man  README  src
```

提前安装需要的库

```sh
yum install -y gcc
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
```

安装

```sh
./configure --prefix=/usr/local/nginx  # --prefix=/usr/local/nginx 指安装路径是/usr/local/nginx
make
make install
```

启动nginx

进入安装好的目录 /usr/local /nginx/sbin

```sh
./nginx					    # 启动
./nginx -s stop			 	#快速停止
./nginx -s quit 			#优雅关闭，在退出前完成已经接受的连接请求
./nginx -s reload 			#重新加载配置
```

访问成功

![image-20260507100647704](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260507100705770.png)

## 安装成系统服务

在如下位置创建服务脚本nginx.service

```sh
vi /usr/lib/systemd/system/nginx.service
```

```sh
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

重新加载系统服务

```sh
systemctl daemon-reload
```

启动服务

```sh
systemctl start nginx.service
```

开机启动

```sh
systemctl enable nginx.service
```

