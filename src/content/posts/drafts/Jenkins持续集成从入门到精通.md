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

## 1.持续集成及Jenkins介绍

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

## 2.Jenkins安装和持续集成环境配置

### 持续集成流程说明

![image-20260526145344998](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/imgs/20260526145347502.png)

1）首先，开发人员每天进行代码提交，提交到Git仓库
2）然后，Jenkins作为持续集成工具，使用Git工具到Git仓库拉取代码到集成服务器，再配合JDK，
Maven等软件完成代码编译，代码测试与审查，测试，打包等工作，在这个过程中每一步出错，都重新再执行一次整个流程。
3）最后，Jenkins把生成的jar或war包分发到测试服务器或者生产服务器，测试人员或用户就可以访问应用。

### Gitlab代码托管服务器安装

官网： https://about.gitlab.com/
GitLab 是一个用于仓库管理系统的开源项目，使用Git作为代码管理工具，并在此基础上搭建起来的web服务。
GitLab和GitHub一样属于第三方基于Git开发的作品，免费且开源（基于MIT协议），与Github类似，可以注册用户，任意提交你的代码，添加SSHKey等等。不同的是，GitLab是可以部署到自己的服务器上，数据库等一切信息都掌握在自己手上，适合团队内部协作开发，你总不可能把团队内部的智慧总放
在别人的服务器上吧？简单来说可把GitLab看作个人版的GitHub。

**Gitlab安装**

1. 安装相关依赖

  > yum -y install policycoreutils openssh-server openssh-clients postfix

2. 启动ssh服务&设置为开机启动

  > systemctl enable sshd && sudo systemctl start sshd

3. 设置postfix开机自启，并启动，postfix支持gitlab发信功能

  > systemctl enable postfix && systemctl start postfix

4. 开放ssh以及http服务，然后重新加载防火墙列表

  > firewall-cmd --add-service=ssh --permanent
  > firewall-cmd --add-service=http --permanent
  > firewall-cmd --reload
  >
  > 如果关闭防火墙就不需要做以上配置

5. 下载gitlab包，并且安装
  在线下载安装包：

  > curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash yum install gitlab-ce -y

  ![image-20260526153548620](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/imgs/20260526153550714.png)

6. 修改gitlab配置

  > vim /etc/gitlab/gitlab.rb
  > 修改gitlab访问地址和端口，默认为80，我们改为82external_url 'http://192.168.66.100:82'
  > nginx['listen_port'] = 82

7. 重载配置及启动gitlab

  > gitlab-ctl reconfigure
  > gitlab-ctl restart

8. 把端口添加到防火墙

  > firewall-cmd --zone=public --add-port=82/tcp --permanent
  > firewall-cmd --reload

​		启动成功后，看到以下修改管理员root密码的页面，修改密码后，然后登录即可