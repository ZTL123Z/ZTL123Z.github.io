---
title: K8S从入门到实战
published: 2026-05-11
updated: 2026-05-12
description: Kubernetes（k8s）入门到实战教程丨全新升级完整版
image: "api"   
tags: [k8s]
category: 运维
draft: false
---

- 第一部分 K8S基本概念

- 第二部分 搭建K8S集群
  - 基于集群部署工具kubeadm
  - 基于二进制包方式
- 第三部分 K8S核心技术   Pod，控制器，存储，Service，调度器，安全机制，RBAC，包管理器 Helm等等
- 第四部分 部署统一日志管理
- 第五部分 部署性能监控平台
- 第六部分 搭建高可用的K8S集群
- 第七部分 K8S集群部署项目

# 第一部分 K8S基本概念

## k8s是google发布的一个==容器化集群管理系统==

1. 自动装箱：自动配置容器
2. 自动修复：自动修复报错的容器，容器失败时，会对容器进行重启
3. 水平拓展：对容器进行大规模的扩大或者裁剪
4. 对外提供统一的入口
5. 滚动更新，回退版本
6. 可以进行热部署

## k8s集群组件框架

![image-20260511153124933](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512143822465.png)

- Master（主控节点）和node（工作节点）

1. master组件

   - apiserver

     集群统一入口，一restful方式，交给etcd存储

   - scheduler

     节点调度，选择node节点应用部署

   - controller-manager

     处理集群中常规后台任务，一个资源对应一个控制器

   - etcd

     存储系统，用于保存集群相关的数据

2. node组件
   - kubeelet
     master派到node节点代表，管理本机容器
   - kube-proxy
     提供网络代理，负载均衡等操作

## k8s核心概念

1. Pod
   - 最小部署单元
   - 一组容器的集合
   - 它们是共享网络的
   - 生命周期是短暂的

2. controller
   - 确保预期的pod副本数量，一般是自动的
   - 无状态应用部署：随时可用
   - 有状态用于部署：需要特定条件使用 
   - 确保所以得node运行同一个pod
   - 一次性任务和定时任务

3. Service
   - 定义一组pod的访问规则：就是接口

## Kubernetes集群命令行工具 kubectl 

### kubectl 概述

**kubectl是kubernetes集群的命令行工具**，通过kubectl能够对集群本身进行管理，并能够在集群上进行容器化应用的安装部署

### kubectl命令的语法

- kubectl [command] [TYPE] [NAME] [flags]

(1) **command**：指定要对资源执行的操作，列如 create、get、describe 和 delete

(2)**TYPE**：指定资源类型，资源类型是大小写敏感的，开发者能够以单数、复数和缩略的形式。列如：

```sh
kubectl get pod pod1
kubectl get pods pod1
kubectl get po pod1
```

(3)**NAME**: 指定资源的名称，名称也是大小写敏感的。如果省略、则会显示所以得资源，列如：

```sh
kubectl get pods
```

(4) **flags**： 指定可选的参数。列如，可用-s 或者 -server 参数指定Kubernetes API server 的地址和端口

### kubectl help 获得更多信息

```sh
获取kubectl帮助方法
kubectl --help
```

![image-20260512200754911](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512200755039.png)

![image-20260512200802665](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512200802707.png)

![image-20260512200907478](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512200907545.png)

### **kubectl子命令使用分类**

#### 基础命令

![image-20260512200955471](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512200955522.png)

#### 部署和集群管理命令

![image-20260512201049069](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512201049131.png)

#### 故障和调试命令

![image-20260512201115539](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512201115591.png)

#### 其他命令

![image-20260512201143622](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512201143674.png)

## kubenetes集群YMAL文件详解

### YAML文件概述

k8s集群中对资源管理和资源对象编排不俗都可以通过声明样式（YAML）文件来解决，也就是可以把需要对资源对象操作的操作编辑到YMAL格式文件中，我们把这种文件脚注资源清单文件，通过kubectl命令直接使用资源清单文件就可以实现对大量资源对象进行编排部署了。

### YAML文件书写格式

1. YMAL介绍

YAML：是一种标记语言。为了强调这种语言以数据作为中心，而不是标记语言为中心

YAML是一个高可读性语言，用来表达数据序列的格式

2. YMAL基本语法

* 使用空格作为缩进
* 低版本不能使用tab键
* 使用#表示注释，从这个字符一直到行尾，都会被解释器忽略

3. YAML支持的数据结构

- 对象
  键值对的集合，又叫做映射（mapping）/ 哈希（hashes） / 字典（dictionary）

  ```yaml
  # 对象类型
  name: tom
  age: 18
  # 另一种写法,将所以键值对写成一个行内对象
  hash: { name: tom, age: 18 }
  ```

- 数组
  ```yaml
  # 数组类型：一组连词线开头的行，构成一个数组
  people
  - tom
  - jack
  # 行类写法: 我喜欢
  people: [tom, jack]
  ```

- 纯量（scalars）
  当个的、不可在分的值

  ```yaml
  # 数值
  number: 12.30
  # 布尔值
  isSet: true
  # null 空值
  parent: ~
  # 时间采用 ISO8601 格式
  iso8601: 2001-12-14t21:59:43.10-05:00
  # 日期采用复合iso8601格式的年、月、日
  date: 1976-07-31
  # YAML 允许使用两个感叹号，强制转换数据类型
  e: !!str 123
  f: !!str true
  # 字符串默认不使用引号表示
  str: 这就是一个字符串
  # 如果字符串之中包含空格或者特殊字符，需要放在引号中
  stt: '内容： 字符串'
  # 单引号和双引号都可以使用，双引号不会对特使字符转义
  s1: '内容\n字符串'
  s1: "内容\n字符串"
  # 单引号之中如果还有单引号，必须连续使用两个转义
  str: 'labor''s day '
  # 字符串可以写成多行，从第二行开始，必须有一个但空格缩进。换行符会被转为空格
  str: 这是一段
   多行
   字符串
  # 多行字符串可以使用|保留换行符，也可以使用>折叠换行
  this: |
  ```

###资源清单描述方法

(1) 在k8s中，一一般使用YAML格式的文件来创建pod，这样的ymal被称为资源清单

(2) 常用字段

- 必须存在的属性

![image-20260512204930523](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512204930593.png)

- 主要对象

![image-20260512205022209](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512205022265.png)

![image-20260512205037771](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512205037815.png)

![image-20260512205043991](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512205044032.png)

![image-20260512205053500](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512205053549.png)

* 额外的参数
  ![image-20260512205129646](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512205129700.png)



















# 第二部分 搭建K8S集群

## 基于集群部署工具kubeadm

### 下载镜像

https://mirrors.huaweicloud.com/centos/7/isos/x86_64/

### 创建三个虚拟机

![image-20260511172330497](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512143829401.png)

### 开始安装

#### 编辑网卡配置文件

```sh
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

####  修改/添加以下内容

```sh
TYPE=Ethernet
BOOTPROTO=static              # 改为static
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
NAME=ens33
DEVICE=ens33
ONBOOT=yes  # 修改为yes

# 静态IP配置 master
IPADDR=192.168.44.146         # 静态IP地址
NETMASK=255.255.255.0         # 子网掩码
GATEWAY=192.168.44.2          # 网关（VMware NAT默认）
DNS1=8.8.8.8                  # 首选DNS
DNS2=114.114.114.114          # 备用DNS
# 静态IP配置 node1
IPADDR=192.168.44.145         # 静态IP地址
NETMASK=255.255.255.0         # 子网掩码
GATEWAY=192.168.44.2          # 网关（VMware NAT默认）
DNS1=8.8.8.8                  # 首选DNS
DNS2=114.114.114.114          # 备用DNS
# 静态IP配置 node2
IPADDR=192.168.44.144         # 静态IP地址
NETMASK=255.255.255.0         # 子网掩码
GATEWAY=192.168.44.2          # 网关（VMware NAT默认）
DNS1=8.8.8.8                  # 首选DNS
DNS2=114.114.114.114          # 备用DNS
```

####  重启网络服务 测试网络

```sh
systemctl restart network

# 查看IP地址
ip addr show ens33

# 测试网络连通性
ping 192.168.44.2
ping www.baidu.com
```

####  更新yum源

```sh
# 1. 备份原有源配置
sudo mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak

# 2. 下载阿里云 CentOS 7 归档源配置
sudo curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

# 3. 清理旧缓存并生成新缓存
sudo yum clean all
sudo yum makecache

# 4. 验证源是否生效
sudo yum repolist enabled | grep -E "base|updates|extras"

# 5.更新
sudo yum update -y
```

####  写成脚本环境命令

```sh
#!/bin/bash

# 检查是否为root用户
if [ "$EUID" -ne 0 ]; then
    echo "请用root用户运行"
    exit 1
fi

# 检查师范提供了主机名参数
if [ -z "$1" ]; then
    echo "请提供主机名作为参数"
    echo "用法 $0 <主机名>"
fi

HOSTNAME="$1"

echo "开始配置k8s节点环境"

# 1.关闭防火墙
systemctl stop firewalld
systemctl disable firewalld

# 2.关闭SELinux
sed -i 's/enforcing/disabled/' /etc/selinux/config
setenforce 0
echo "SELinux 已经永久关闭"

# 3.关闭swap
swapoff -a
sed -ri 's/.*swap.*/#&/' /etc/fstab
echo "swap 已永久禁用，当前立即关闭"

# 4.set HOSTNAME
hostnamectl set-hostname "$HOSTNAME"

# 5. 添加 hosts 记录（请根据实际情况修改 IP 和域名）
cat >> /etc/hosts << EOF
192.168.44.141 master
192.168.44.142 node1
192.168.44.143 node2
EOF
echo "已添加静态 hosts 解析"

# 6. 配置 sysctl，允许桥接流量经过 iptables
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

# 8. 重新加载所有 sysctl 配置
sysctl --system > /dev/null 2>&1
echo "sysctl 配置已生效"

# 9. 安装 ntpdate 并同步时间
yum install ntpdate -y -q > /dev/null 2>&1
echo "ntpdate 安装完成"

ntpdate time.windows.com
echo "系统时间已同步"

# 10. 将系统时间写入硬件时钟
hwclock --systohc
echo "硬件时钟已更新"

echo "所有配置完成！建议重启系统以确保所有更改完全生效。"
```

```sh
以 root 身份运行，并传入主机名（例如 k8smaster、k8snode1、k8snode2）：
sudo ./init_k8s.sh k8smaster
```

#### 安装 kubeadm，kubelet 和 kubectl

```sh
yum install -y kubelet kubeadm kubectl
systemctl enable kubelet
```

#### **部署**Kubernetes Master

```sh
kubeadm init \
--apiserver-advertise-address=192.168.44.146 \
--image-repository registry.aliyuncs.com/google_containers \--kubernetes-version v1.17.0 \
--service-cidr=10.96.0.0/12 \
--pod-network-cidr=10.244.0.0/16
```

#### 使用 kubectl工具

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config sudo chown $(id -u):$(id -g) $HOME/.kube/config
$ kubectl get nodes
```

### 测试安装是否成功

先给docker换源

```sh
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://mirror.baidubce.com",
    "https://dockerproxy.com"
  ],
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}

# 重启生效
systemctl daemon-reload
systemctl restart docker
```

装nginx测试

```sh
kubectl create deployment nginx --image=nginx 
kubectl expose deployment nginx --port=80 --type=NodePort
kubectl get pod,svc
```

成功结果

```sh
[root@k8smaster talen]# kubectl get pod 
NAME                    READY   STATUS    RESTARTS   AGE
nginx-f89759699-vvrsd   1/1     Running   0          7m46s
[root@k8smaster talen]# kubectl expose deployment nginx --port=80 --type=NodePort
service/nginx exposed
[root@k8smaster talen]# kubectl get svc nginx
NAME    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx   NodePort   10.108.43.251   <none>        80:30492/TCP   11s
```

![image-20260512152848119](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260512152848247.png)

#第三部分 K8S核心技术  

Pod，控制器，存储，Service，调度器，安全机制，RBAC，包管理器 Helm等等













#第四部分 部署统一日志管理

# 第五部分 部署性能监控平台

# 第六部分 搭建高可用的K8S集群

# 第七部分 K8S集群部署项目