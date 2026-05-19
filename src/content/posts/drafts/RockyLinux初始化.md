---
title: RockyLinux初始化
published: 2026-05-16
updated: 2026-05-16
description: RockyLinux初始化
image: "api"   
tags: [RockyLinux]
category: linux
draft: false
---



## 初始化环境

第一个创建的网卡的数是最小的

```sh
[root@localhost ~]# ls /etc/NetworkManager/system-connections/
ens160.nmconnection  ens192.nmconnection
[root@localhost ~]# vi /etc/NetworkManager/system-connections/ens160.nmconnection 
[root@localhost ~]# systemctl restart NetworkManager # 重启生效
```

改为手动manual，添加ip地址

![image-20260514191154392](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/imgs/20260514191359290.png)

结果

![image-20260514191345364](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/imgs/20260514191401495.png)

 Rocky 系统软件源更换

```sh
# Rocky 系统软件源更换
sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.aliyun.com/rockylinux|g' \
    -i.bak \
    /etc/yum.repos.d/[Rr]ocky*.repo
# 更新软件缓存，让刚才的国内源立刻生效。
dnf makecache
```

关闭防火墙

```sh
# 防火墙修改 firewalld 为 iptables
systemctl stop firewalld
systemctl disable firewalld

yum -y install iptables-services
systemctl start iptables
iptables -F
systemctl enable iptables
service iptables save
```

禁用selinux

```sh
# 禁用 Selinux
setenforce 0
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
grubby --update-kernel ALL --args selinux=0

# 查看是否禁用，grubby --info DEFAULT
# 回滚内核层禁用操作，grubby --update-kernel ALL --remove-args selinux
```

设置时区

```sh
# 设置时区
timedatectl set-timezone Asia/Shanghai
```

## 安装docker环境

```sh
# 加载 bridge
yum install -y epel-release
yum install -y bridge-utils

modprobe br_netfilter
echo 'br_netfilter' >> /etc/modules-load.d/bridge.conf
echo 'net.bridge.bridge-nf-call-iptables=1' >> /etc/sysctl.conf
echo 'net.bridge.bridge-nf-call-ip6tables=1' >> /etc/sysctl.conf
echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
sysctl -p
```

```sh
# 添加 docker-ce yum 源
# 中科大(ustc)
sudo dnf config-manager --add-repo https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
cd /etc/yum.repos.d
# 切换中科大源
sed -e 's|download.docker.com|mirrors.ustc.edu.cn/docker-ce|g' docker-ce.repo

# 安装 docker-ce
yum -y install docker-ce
# 配置 daemon.
cat > /etc/docker/daemon.json <<EOF
{
  "default-ipc-mode": "shareable",
  "data-root": "/data/docker",
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m",
    "max-file": "100"
  },
  "insecure-registries": ["harbor.xinxainghf.com"],
  "registry-mirrors": ["https://kfp63jaj.mirror.aliyuncs.com"]
}
EOF

mkdir -p /etc/systemd/system/docker.service.d

# 重启docker服务
systemctl daemon-reload && systemctl restart docker && systemctl enable docker
```

关闭swap分区

```sh
# 使用 sed 命令自动注释掉 swap 行
swapoff -a
sed -i '/swap/s/^\(.*\)$/#\1/g' /etc/fstab
```

