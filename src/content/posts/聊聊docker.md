---

title: 聊聊docker
published: 2026-04-11          
description: 系统进程       
image: "api"   
tags: [docker,网站]      
category: docker
draft: false       
---

# docker概述

## 聊聊docker

Docker 是基于Go语言开发的！开源项目！

官网：https://www.docker.com/

文档地址：https://docs.docker.com/

docker仓库：https://www.docker.com/products/docker-hub/

![image-20260412161343758](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412161343973.png)

## 虚拟机&docker

> 虚拟机技术缺点

1. 资源占用十分多，有完整的虚拟硬件
2. 冗余步骤多
3. 启动很慢！

> 比较Docker和虚拟机技术的不同

1. 传统虚拟机，虚拟出一个硬件，运行一个完整的操作系统，然后在这个系统上面安装和运行软件
2. 容器类的应用直接运行在宿主机的内核，容器是没有自己的内核的，也没有虚拟我们的硬件，所以就轻便了
3. 每个容器间是相互隔离的，每个容器内都有属于自己的文件系统，互不影响

## Docker组成

![image-20260412164742395](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412164742589.png)

- **镜像（image）**：docker 镜像就好比是一个模板，可以通过这个模板来创建容器服务，`tomcat镜像 ===> run ===> tomcat01 容器（提供服务器）`，通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）。

- **容器（container）**：Docker 利用容器技术，独立运行一个或者一个组应用，通过镜像来创建的。
  - 支持启动、停止、删除等基本命令
  - 目前可以把这个容器理解为一个简易的 linux 系统

- **仓库（repository）**：仓库就是存放镜像的地方！

  - 仓库分为公有仓库和私有仓库

  - Docker Hub（默认是国外的）

  - 阿里云等都有容器服务器（可配置镜像加速）

# 安装docker

## 环境准备

1. 一点Linux基础
2. Centos7
3. 远程软件，xshell，finalshell等

## 环境查看

```sh
# 系统内核3.10以上
[root@localhost ~]# uname -r
3.10.0-957.el7.x86_64
```

```sh
# 系统版本
[root@localhost ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

> centos7已经不维护了，建议学习使用

![image-20260412170528533](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412170528591.png)

## 开始安装

```shell
# 1、卸载旧的版本
yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine
# 配置可用 DNS（解决无法解析域名）
echo "nameserver 8.8.8.8" > /etc/resolv.conf
echo "nameserver 114.114.114.114" >> /etc/resolv.conf
# 2. 替换为阿里云 CentOS 7 源（官方源已停止服务，必须换）
cd /etc/yum.repos.d/
mkdir backup
mv *.repo backup/
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
# 清空并重建 yum 缓存
yum clean all
yum makecache
# 2、需要的安装包
yum install -y yum-utils
# 3、设置镜像的仓库
# 国外官方源（不推荐国内使用）
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo  # 默认是从国外的！

# 国内阿里云源（推荐，速度快）
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo  # 推荐使用阿里云的，十分的快
# 4、安装docker  docker-ce 社区版   ee 企业版
yum install -y docker-ce docker-ce-cli containerd.io

# 5、启动docker
systemctl start docker
# 设置 Docker 开机自启（强烈建议执行）
systemctl enable docker
# 查看 Docker 运行状态
systemctl status docker

6.# 验证 Docker 安装成功
docker run hello-world
```

> 一键修复 Docker 镜像加速

```sh
# 创建 docker 配置文件夹
mkdir -p /etc/docker

# 写入阿里云镜像加速地址
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://dockerpull.com",
    "https://docker.nju.edu.cn",
    "https://dockerproxy.com",
    "https://mps5o7ra.mirror.aliyuncs.com"
  ]
}
EOF

# 重新加载配置并重启 docker
systemctl daemon-reload # 让系统重新扫描服务配置文件，感知最新的变化。
systemctl restart docker
```

> tee 命令 读取标准输入（stdin），然后将其内容同时写入标准输出（stdout）和文件。

| **命令方式**              | **屏幕输出** | **文件写入** | **说明**                 |
| ------------------------- | ------------ | ------------ | ------------------------ |
| `ls`                      | ✅ 有         | ❌ 无         | 普通查看                 |
| `ls > out.txt`            | ❌ 无         | ✅ 覆盖写入   | 结果“静默”存入文件       |
| `ls >> out.txt`           | ❌ 无         | ✅ 追加写入   | 结果“静默”存到末尾       |
| **`ls | tee out.txt`**    | ✅ 有         | ✅ 覆盖写入   | **边看边存**             |
| **`ls | tee -a out.txt`** | ✅ 有         | ✅ 追加写入   | **边看边存，不删旧内容** |

> 卸载docker

```shell
# 停止服务
sudo systemctl stop docker docker.socket

# 移除软件包
sudo yum remove -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 清理所有镜像、容器、卷和配置 (执行前请确认数据已备份)
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
sudo rm -rf /etc/docker
```

阿里云镜像加速

![image-20260412183035128](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260412183035181.png)

# docker常用命令

## 帮助命令

```shell
docker --version # 查看docker版本
docker info # 查看docker更多信息，比如容器，镜像
docker 命令 --help # 帮助命令
```

## 镜像命令

docker images

```shell
[root@localhost talen]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED       SIZE
hello-world   latest    e2ac70e7319a   2 weeks ago   10.1kB
# 可选项
-a --all   # 列出所以镜像
-q --quite # 只显示镜像id
```

docker search 镜像名称

```shell
# 基于所提供条件的滤波器输出
-f, --filter  -f， --滤波器		Filter output based on conditions provided
# 最大搜索结果数
--limit  ——极限		Max number of search results
```

docker pull 镜像名称:tag

```shell
# 没有加版本号，就默认使用最新的
[root@localhost talen]# docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
```

docke rmi 删除镜像

```shell
[root@localhost talen]# docker rmi -f 容器id # 删除指定容器
[root@localhost talen]# docker rmi -f 容器id 容器id  容器id # 删除多个容器
[root@localhost talen]# docker rmi -f $(docker images -aq) # 强制删除所有的镜像
Untagged: hello-world:latest
Untagged: hello-world@sha256:452a468a4bf985040037cb6d5392410206e47db9bf5b7278d281f94d1c2d0931
Untagged: mysql:latest
Untagged: mysql@sha256:24e450bbd24f621c71b10404c946cc9ea1cbb0e6e7464b2be2de5193dcf1d05b
Untagged: mysql:5.7
Untagged: mysql@sha256:4bc6bc963e6d8443453676cae56536f4b8156d78bae03c0145cbe47c2aad73bb
```

## 容器命令

### **docker run [选项]** image 

```shell
docker run [可选参数] image
# 参数解析
# 参数说明
--name="Name"    容器名字  tomcat01  tomcat02，用来区分容器
-d               后台方式运行
-it              使用交互方式运行，进入容器查看内容
-p               指定容器的端口  -p  8080:8080
    -p ip:主机端口:容器端口
    -p 主机端口:容器端口 （常用）
-P               随机指定端口
-c	告诉 Shell：不要从文件 / 终端读命令，而是直接执行后面引号里的字符串
```

### 列出所以运行的容器

```bash
docker ps      # 列出正在运行的容器
docker ps -a   # 列出所有容器
docker ps -n=? # 列出最近创建的n个容器
docker ps -aq  # 列出所以容器的id

[root@localhost talen]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@localhost talen]# docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
ee4f8d2f78fb   hello-world   "/hello"   55 minutes ago   Exited (0) 55 minutes ago             amazing_moore
[root@localhost talen]# docker ps -n=2
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
ee4f8d2f78fb   hello-world   "/hello"   55 minutes ago   Exited (0) 55 minutes ago             amazing_moore
[root@localhost talen]# docker ps -aq
ee4f8d2f78fb
```

### 退出容器

```bash
exit # 直接退出并关闭容器
ctrl + q + p # 不关闭并退出容器
```

### 删除容器

```bash
docker rm 容器id # 删除指定容器
docker rm -f $(docker ps -aq)  # 强制删除所以容器
docker ps -a -q | xargs docker rm # 删除所有的容器
# xargs：将管道传来的多行 / 多列容器 ID，转换为docker rm命令的命令行参数
```

### 启动和停止容器的操作

```bash
docker start 容器id          # 启动容器 
docker stop 容器id		   # 停止容器
docker restart 容器id 	   # 重启容器
docker kill 容器id		   # 强行停止容器
docker attch 容器id          # 进入正在运行的容器内部，直接连接到容器的标准输入 / 输出终端
```

## 其他常用命令

### 后台启动命令

```bash
# 命令 docker run -d 镜像名！
[root@kuangshen /]# docker run -d centos

# 问题docker ps，发现 centos 停止了
# 常见的坑：docker 容器使用后台运行，就必须要有要一个前台进程，docker发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

### 查看日志

```bash
[root@localhost talen]# docker logs --help
Usage:  docker logs [OPTIONS] CONTAINER
Options:
      --details        显示日志中提供的额外详细信息
  -f, --follow         实时跟踪日志输出（持续刷新，像 tail -f）
      --since string   显示从某个时间点之后的日志
                       支持时间戳（如 "2013-01-02T13:23:37Z"）
                       或相对时间（如 "42m" 表示 42 分钟）
  -n, --tail string    从日志末尾显示指定行数（默认显示全部）
  -t, --timestamps     显示日志时间戳
      --until string   显示到某个时间点之前的日志
                       支持时间戳或相对时间
# 常用
docker logs -tf  # 全部显示
docker logs -tf --tail [n] 容器id   # n是显示日志条数                    
```

### top命令

```bash
docker top <容器ID或容器名> [ps参数]
```

### 查看镜像的云数据

```bash
[root@localhost talen]# docker inspect 87de88f4c364
[
    {
        "Id": "87de88f4c364730f786e25c1aea30c557c28212884b1d5c91181a9c7114bfad6",
        "Created": "2026-04-12T12:22:29.345068528Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 21140,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2026-04-12T12:22:29.714975509Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:eeb6ee3f44bd0b5103bb561b4c16bcb82328cfe5809ab675bb17ab3a16c517c9",
        "ResolvConfPath": "/var/lib/docker/containers/87de88f4c364730f786e25c1aea30c557c28212884b1d5c91181a9c7114bfad6/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/87de88f4c364730f786e25c1aea30c557c28212884b1d5c91181a9c7114bfad6/hostname",
        "HostsPath": "/var/lib/docker/containers/87de88f4c364730f786e25c1aea30c557c28212884b1d5c91181a9c7114bfad6/hosts",
        "LogPath": "/var/lib/docker/containers/87de88f4c364730f786e25c1aea30c557c28212884b1d5c91181a9c7114bfad6/87de88f4c364730f786e25c1aea30c557c28212884b1d5c91181a9c7114bfad6-json.log",
        "Name": "/centos",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "bridge",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                32,
                114
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": [],
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware",
                "/sys/devices/virtual/powercap"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/8630bb48bc947c02a430b63a12108500642233c6cd8ba6e151bd6e12684b6fe6-init/diff:/var/lib/docker/overlay2/b2153c292fec38eaa2da742fcfbc3c3fab2afabc4c92e7718c2167cfdf036ab5/diff",
                "MergedDir": "/var/lib/docker/overlay2/8630bb48bc947c02a430b63a12108500642233c6cd8ba6e151bd6e12684b6fe6/merged",
                "UpperDir": "/var/lib/docker/overlay2/8630bb48bc947c02a430b63a12108500642233c6cd8ba6e151bd6e12684b6fe6/diff",
                "WorkDir": "/var/lib/docker/overlay2/8630bb48bc947c02a430b63a12108500642233c6cd8ba6e151bd6e12684b6fe6/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "87de88f4c364",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "eeb6ee3f44bd",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201113",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS",
                "org.opencontainers.image.created": "2020-11-13 00:00:00+00:00",
                "org.opencontainers.image.licenses": "GPL-2.0-only",
                "org.opencontainers.image.title": "CentOS Base Image",
                "org.opencontainers.image.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "76cee3fbfad68e4705bb5a30db867bd4170bf45fc0e2561e87654f2924300c64",
            "SandboxKey": "/var/run/docker/netns/76cee3fbfad6",
            "Ports": {},
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "a6777f36901587642047f9d233d2082f449897e9b335b3f4070db96c5f74945f",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "MacAddress": "02:42:ac:11:00:02",
                    "NetworkID": "3fcf7a24761165d3d1176480a602c7549372b42e2bfe60252071b78d7e3bda5d",
                    "EndpointID": "a6777f36901587642047f9d233d2082f449897e9b335b3f4070db96c5f74945f",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DriverOpts": null,
                    "DNSNames": null
                }
            }
        }
    }
]

```

