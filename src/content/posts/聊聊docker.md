---
title: 聊聊docker
published: 2026-04-13          
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

### 查看资源占用命令

```bash
# 命令1：top
docker top <容器ID或容器名> [ps参数]

# 命令2：stats
docker stats
CONTAINER ID   NAME       CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O     PIDS
bd6dcfd44056   tomcat01   0.20%     130.4MiB / 1.777GiB   7.17%     9.73kB / 106kB    0B / 0B       38
486e75ce0501   nginx      0.00%     3.82MiB / 1.777GiB    0.21%     2.38kB / 2.57kB   0B / 8.19kB   5
87de88f4c364   centos     0.00%     800KiB / 1.777GiB     0.04%     740B / 0B         0B / 0B       2
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

### 进入当前正在运行的容器

```bash
# 方法一 进入容器后开启一个新的终端，可以在里面操作（推荐，常用）
docker exec -it 容器id /bin/[sh|bash|...]

# 方法二 进入容器正在执行的终端，不会启动新的进程！（不推荐）
docker attach  容器id 
```

### 把文件从主机拷贝到容器

```bash
# 1. 主机 → 容器（把本地文件拷进容器）
docker cp <主机文件/目录路径> <容器ID或容器名>:<容器内目标路径>

# 2. 容器 → 主机（把容器里的文件拷到本地）
docker cp <容器ID或容器名>:<容器内文件/目录路径> <主机目标路径>
```

## 小结

![image-20260413084202063](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413084202297.png)

## 练习

### 部署nginx

```bash
[root@localhost talen]# docker run -d --name nginx -p 8085:80 nginx
486e75ce0501e121842fff76353032e255ac8b4eace516b537c4927c574c952f
```

![image-20260413090007529](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413090007651.png)

### 部署tomcat

```bash
# 官方运行命令 这个命令用完关掉就会自己删除，一般用于测试
$ docker run -it --rm tomcat:9.0
# 正常部署
[root@localhost talen]# docker run -d -p 8086:8080 --name tomcat01 tomcat
```

![image-20260413091621403](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413091621593.png)

## 可视化

### protainer

```bahs
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

| 参数                                           | 作用                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| `docker run`                                   | 创建并启动容器                                               |
| `-d`                                           | 后台守护进程运行                                             |
| `-p 8088:9000`                                 | 端口映射：宿主机`8088`端口 ↔ 容器`9000`端口（Portainer 默认管理端口） |
| `\`                                            | 命令换行符，用于拆分长命令                                   |
| `--restart=always`                             | 容器自动重启策略：Docker 重启 / 容器崩溃时，自动拉起容器     |
| `-v /var/run/docker.sock:/var/run/docker.sock` | 挂载宿主机 Docker 套接字，让容器能直接管理宿主机 Docker      |
| `--privileged=true`                            | 授予容器特权权限，确保套接字挂载和系统操作正常               |
| `portainer/portainer`                          | 镜像名：Portainer（Docker 可视化管理工具）                   |

![image-20260413094635193](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413094635290.png)

# 容器数据卷

## 什么是容器数据卷

**docker的理念**

将应用和环境打包为一个镜像！

如果数据都在容器中，我们删除容器，数据就会丢失！==需求：数据可以持久化==

Mysql，容器删除，就删库跑路了！需求：==Mysql数据可以存储在本地==

容器之间可以有一个数据共享！docker容器中产生的数据，同步到本地！

这就是卷技术！目录的挂载，将我们容器内的目录，挂载Linux上面！

![image-20260413103700666](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413103700761.png)

**总结：容器的持久化和同步操作！容器间也是可以数据共享的！**

## 使用数据卷

> 方式一：直接使用命令来挂载 -v

```bash
[root@localhost ~]# docker run -d -v /home/test:/home centos
44933a9c5f60dc1227d796a92659e54b584f938caec100661bf7d2d6fb3c0700

[talen@localhost home]$ ls
home  talen  test  tom
```

![image-20260413105643145](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413105643187.png)

## mysql实战

> 解决数据持久化问题

```bash
# 拉取镜像
[root@localhost ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
20e4dcae4c69: Pull complete 
1c56c3d4ce74: Pull complete 
e9f03a1c24ce: Pull complete 
68c3898c2015: Pull complete 
6b95a940e7b6: Pull complete 
90986bb8de6e: Pull complete 
ae71319cb779: Pull complete 
ffc89e9dfd88: Pull complete 
43d05e938198: Pull complete 
064b2d298fba: Pull complete 
df9a4d85569b: Pull complete 
Digest: sha256:4bc6bc963e6d8443453676cae56536f4b8156d78bae03c0145cbe47c2aad73bb
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
# 运行命令
docker run --name mysql01 \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -p 3306:3306 \
  -v /home/mysql/data:/var/lib/mysql \
  -v /home/mysql/conf:/etc/mysql/conf.d \
  -d \
  mysql:5.7
```

![image-20260413112708383](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413112708509.png)

删除容器后，数据依然存在在本机上面

## 具名挂载和匿名挂载

匿名挂载

```bash
# -P 是随机端口 -v 容器内路径   匿名挂载
[root@localhost talen]# docker run -d -P --name nginx01 -v /etc/nginx nginx
34a199624f2dc9eceedbde4bf683183425a29120866a44670cd1fe8f2029ce61
# volume 查看所有挂载卷
[root@localhost talen]# docker volume ls
DRIVER    VOLUME NAME
local     729dbe986ce423517945d5a9414e223869dda8281e02b96cba18a667c92d34a5
local     b409be3a1b1ee2908f8baef123fa24834bcabafe14d30ae4862d543f08f30ea7
```

具名挂载

```bash
# 具名挂载 只写名字 可不写路径
[root@localhost talen]# docker run -d -P --name nginx02 -v mynginx:/etc/nginx nginx
8639a93886f1a62eacfc3dcf21ec922b73f7bd5384381b3494a3579036822b35
# 查看卷
[root@localhost talen]# docker volume ls
DRIVER    VOLUME NAME
local     729dbe986ce423517945d5a9414e223869dda8281e02b96cba18a667c92d34a5
local     b409be3a1b1ee2908f8baef123fa24834bcabafe14d30ae4862d543f08f30ea7
local     mynginx
# inspect查看路径
[root@localhost talen]# docker volume inspect mynginx
[
    {
        "CreatedAt": "2026-04-13T12:44:24+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mynginx/_data",
        "Name": "mynginx",
        "Options": null,
        "Scope": "local"
    }
]
```

所有的docker容器内的卷，没有指定目录的情况下都是在==/var/lib/docker/volumes/xxxxxx/_data==

通过具名挂载可以很方便找到卷，大多数情况使用==具名挂载==

```bash
# 具名挂载，匿名挂载和指定路径挂载
-v 容器内路径            # 匿名挂载
-v 卷名：容器内路径       # 具名挂载
-v /宿主机路径：容器内路径 # 指定路径挂载
```

拓展

```bash
# 通过 -v 容器内路径：ro rw 改变读写权限
ro	readonly # 只读
rw  readwrite #可读可写

# 设置容器权限
docker run -d -P --name nginx02 -v mynginx:/etc/nginx:ro nginx
# ro说明这个路径只能通过宿主机来操作，容器内部是无法操作的!
```

# Dockerfile

Dockerfiel 是用来构建docker镜像的文件！命令参数脚本！

构建步骤：

1. 编写一个dockerfile文件
2. docker build构建成为一个镜像
3. docker run 运行镜像
4. docker push 发布镜像（dockerhub、阿里云镜像仓库）



## Dockerfile的构建过程

1. 每个关键字都是必须是大写字母
2. 执行从上到下顺序执行
3.  `# ` 表示注释
4. 每一个指令都会创建提交一个新的镜像层，并提交！

![image-20260413211759787](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413211759998.png)

## dockerfile的指令

```bash
FROM             	# 基础镜像
MAINTAINER			# 作者信息 姓名+邮箱
RUN					# 镜像构建的时候需要运行的命令
ADD					# 添加其他镜像，不如tomcat压缩包，会自动解压
WORKDIR				# 镜像的工作目录
VOLUME				# 挂载的目录
EXPOSE  			# 保留端口配置
CMD 				# 容器启动时候运行的命令，只有最后一个会生效
ENTRYPOINT 			# 容器启动时候运行的命令，可以追加命令
ONBUILD				# 构建一个被继承的Dockerfile就会运行这个命令
COPY				# 把文件复制到镜像中
ENV					# 构建的时候设置环境变量
```



![k8s记录-Dockerfile详解- 信方- 博客园](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413213015418.png)

## 实战 centos

```bash
FROM centos:7   # 基础镜像
MAINTAINER talen<tianlunzhu87@gmail.com> # 作者

ENV MYPATH /usr/local  # 环境变量
WORKDIR $MYPATH # 工作目录

# CentOS 7 的官方镜像源已于 2024 年 6 月 30 日彻底停止维护（EOL）。
# --- 关键修复：更换为国内阿里云的 Vault 存档源 ---
RUN sed -i s/mirror.centos.org/mirrors.aliyun.com/g /etc/yum.repos.d/CentOS-Base.repo && \
    sed -i s/^#.*baseurl=http/baseurl=http/g /etc/yum.repos.d/CentOS-Base.repo && \
    sed -i s/mirrorlist=http/#mirrorlist=http/g /etc/yum.repos.d/CentOS-Base.repo && \
    yum clean all && \
    yum makecache

RUN yum -y install vim  # 启动前命令
RUN yum -y install net-tools

EXPOSE 80 # 暴露端口

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash

# ==============
# -f 指定文件名 -t 指定镜像名和版本
[root@localhost code]# docker build -f Dockerfile -t mycentos:1.0 .
```

![image-20260413215913246](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413215913345.png)

## CMD与ENTRYPOINT的区别

> cmd

==CMD的命令下 -l 替换了CMD ["ls","-a"] 命令，-l 不是命令所以报错!==

```bash
# 测试cmd
# 编写 dockerfile 文件
[root@kuangshen dockerfile]# vim dockerfile-cmd-test
FROM centos
CMD ["ls","-a"]

# 构建镜像
[root@kuangshen dockerfile]# docker build -f dockerfile-cmd-test -t cmdtest .

# run运行，发现我们的ls -a 命令生效
[root@kuangshen dockerfile]# docker run dd8e4401d72f
.
..
.dockerenv
bin
dev
etc
home
lib
lib64

# 想追加一个命令 -l  ls -al
[root@kuangshen dockerfile]# docker run dd8e4401d72f -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"-l\": executable file not found in $PATH": unknown.

# cmd的清理下 -l 替换了CMD ["ls","-a"] 命令，-l 不是命令所以报错！
```

==ENTAYPOINT的命令下 -l 追加了CMD ["ls","-a"] 命令！==

![image-20260413221126470](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260413221126716.png)

## 实战：tomcat

### 编写Dockerfile文件

```bash
# 基础镜像
FROM centos:7
MAINTAINER talen<2746395413@qq.com> #作者

# 1. 修复 CentOS 7 停止维护导致的 yum 源失效问题
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* && \
    sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*

# 2. 安装常用工具并清理缓存以减小镜像体积
RUN yum -y install vim && yum clean all

# 3. 添加环境压缩包（ADD 会自动解压）
ADD jdk-8u11-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.22.tar.gz /usr/local/

# 4. 设置环境变量
ENV JAVA_HOME /usr/local/jdk1.8.0_11
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.22
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin

# 5. 设置工作目录
WORKDIR $CATALINA_HOME

# 6. 开放端口
EXPOSE 8080

# 7. 启动 Tomcat（使用 run 模式可以直接把日志输出到控制台，无需 tail -F）
CMD ["catalina.sh", "run"]
```

### 构建镜像

```bash
[root@localhost code]# docker build -f Dockerfile -t mytomcat:1.0 .
```

![image-20260414092104358](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414092104585.png)

## 发布镜像到阿里云

https://cr.console.aliyun.com/cn-shanghai/instances

![image-20260414094011794](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414094011952.png)

### 创建镜像仓库

创建仓库

![image-20260414094745892](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414094746018.png)

选择本地

![image-20260414094844986](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414094845028.png)



### 设置登入凭证

![image-20260414094412826](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414094412885.png)

### 上传

```bash
# 登入
[root@localhost code]# docker login --username=aliyun9669157139 crpi-ovjnr512v641s4ad.cn-hangzhou.personal.cr.aliyuncs.com
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
Login Succeeded
# 打标签 写上作者信息
[root@localhost code]# docker tag 00293d9866e6  crpi-ovjnr512v641s4ad.cn-hangzhou.personal.cr.aliyuncs.com/talen
zhu/mytomcat:1.0
# 上传
[root@localhost code]# docker push crpi-ovjnr512v641s4ad.cn-hangzhou.personal.cr.aliyuncs.com/talenzhu/mytomcat:1.0
The push refers to repository [crpi-ovjnr512v641s4ad.cn-hangzhou.personal.cr.aliyuncs.com/talenzhu/mytomcat]
395f1d987790: Pushed 
52768266f2d3: Pushed 
385be9a3d6bd: Pushed 
db3873a5df05: Pushed 
030f1c702978: Pushed 
174f56854903: Pushed 
1.0: digest: sha256:b7870c12b17f2d4ecd9f81505bdae15571ccc8a6d758ddc411e87f51e6c41ae8 size: 1580
```

### 总结

![image-20260414100149020](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414100149137.png)

# Docker网络

## 理解Docker0

```bash
[root@localhost code]# ip addr
```

![image-20260414103202913](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414103203058.png)

**docker0 (172.17.0.1)**: 这是 Docker 默认生成的**虚拟网桥**。你可以把它想象成一个虚拟的交换机。所有运行在默认网络模式下的容器，其网关都是这个 `172.17.0.1`。

**vetha0b93d7 / veth8475f4e**: 这些是 **Virtual Ethernet (veth) 对**。每启动一个容器，Docker 就会成对创建这种网卡：

- 一端留在**宿主机**上（就是你看到的这些）。
- 另一端放入**容器内部**（通常被重命名为 `eth0`）。
- 数据通过这对“网线”在容器和宿主机之间传输。

![image-20260414104933840](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414104933975.png)

```bash
# 我们发现这个容器带来网卡，都是一对对的
# veth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连
# 正因为有这个特性，veth-pair 充当一个桥梁，连接各种虚拟网络设备的
# OpenStack，Docker容器之间的连接，OVS的连接，都是使用 veth-pair 技术
```

![image-20260414110328594](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414110328797.png)

![image-20260414110437273](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414110437405.png)

## 容器之间可以相互ping通

```bash
[root@localhost code]# docker exec -it 6168c26d4dcd ping  172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.173 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.060 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.059 ms
```

![image-20260414111527677](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414111527815.png)

## 自定义网络

容器互联

> 查看所有的网络

```bash
[root@localhost ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
3fcf7a247611   bridge    bridge    local
8698dd6b8563   host      host      local
97bb605a2ab6   none      null      local
```

**网络模式**

bridge : 桥接 docker（默认）

none  : 不配置网络

host : 和宿主机共享网络

container : 容器网络连通！（用的少！）



**测试**

```bash
# 我们直接启动的命令 --net bridge。 这个就是我们的docker0
docker run -d -P --name tomcat01 tomcat
docker run -d -P --name tomcat01 --network bridge tomcat

# docker0 特点：默认。域名不能访问， --link可以打通链接！


# --driver bridge
# --subnet 192.168.0.0/16
# --gateway 192.168.0.1
# 我们可以自定义一个网络！
[root@localhost ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
ead8e1ca85a7f5c7a6165b2d5894d25541a4f36cc942a7805ad3affa8d7db6bf
[root@localhost ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
3fcf7a247611   bridge    bridge    local
8698dd6b8563   host      host      local
ead8e1ca85a7   mynet     bridge    local
97bb605a2ab6   none      null      local
[root@localhost ~]# 
# 查看网络
[root@localhost ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "ead8e1ca85a7f5c7a6165b2d5894d25541a4f36cc942a7805ad3affa8d7db6bf",
        "Created": "2026-04-14T12:08:55.535994559+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

## 使用自定义网络

```bash
# 创建两个容器
[root@localhost ~]# docker run -d -P --name tomcat-net-01 --network mynet tomcat
ee00eb115239ad1e1c179c4e78a8c0b9b7665593ae68a5ee901188bf3c7f81a0
[root@localhost ~]# docker run -d -P --name tomcat-net-02 --network mynet tomcat
db32a3d82d7f5fadbfbf3abdd789da4fde8ec85f36efabc488e04fbb6fcec8d5
# 查看
"Containers": {
            "db32a3d82d7f5fadbfbf3abdd789da4fde8ec85f36efabc488e04fbb6fcec8d5": {
                "Name": "tomcat-net-02",
                "EndpointID": "c7d3a9077138d021444121d66bed2bd0391b35c0a60b9d30ac9ba0749988686c",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "ee00eb115239ad1e1c179c4e78a8c0b9b7665593ae68a5ee901188bf3c7f81a0": {
                "Name": "tomcat-net-01",
                "EndpointID": "ac9207ae0b013180f6ef5cece2a7934442fa63579f2e6968daeac0674f230bae",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
```

测试

![image-20260414121525474](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414121525718.png)

我们自定义的网络docker都已经帮我们维护好了对应的关系，推荐我们平时这样使用网络！



好处：

- redis-不同的集群使用不用的网络，保证集群是安全和健康的！
- mysql-不同的集群使用不用的网络，保证集群是安全和健康的！

## 网络连通

网卡和容器连通

![image-20260414122728000](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414122728170.png)

![image-20260414122747853](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414122747936.png)

```bash
# 测试连通 tomcat01 - mynet

# 连通之后就是将 tomcat01 放到了 mynet 网络下！
# 一个容器两个ip
```

![image-20260414123131276](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414123131420.png)



# Docker Compose

## 简介

Dockerfile build run 手动操作，单个容器！

微服务，100个微服务，依赖关系。

Docker Compose 来轻松高效的管理容器，定义运行多个容器。

作用：批量容器编排

狂神的理解

Compose是Docker官方的开源项目，需要安装！

Dockerfile让程序在任何地方运行。web服务。redis、mysql、nginx… 多个容器。 run

Compose

```
version: '2.0'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

docker-compose up 100个服务

Compose：重要概念

- 服务services， 容器、应用（web、redis、mysql…）
- 项目project。 一组关联的容器.博客。web、mysql

## 安装

```bash
sudo yum -y install docker-compose-plugin
# 验证
[root@localhost ~]# docker compose version
Docker Compose version v2.27.1
```

##快速开始

python应用。 计数器。redis！

### 搭建项目

1. 为项目创建一个目录：

```bash
 mkdir compose-demo
 cd compose-demo
```

2. 在你的项目目录中创建 `app.py` 并添加以下内容：

```python
import os
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(
    host=os.getenv("REDIS_HOST", "redis"),
    port=int(os.getenv("REDIS_PORT", "6379")),
)

@app.route("/")
def hello():
    count = cache.incr("hits")
    return f"Hello from Docker! I have been seen {count} time(s).\n"
```

3. 在你的项目目录中创建 `requirements.txt` 并添加以下内容：

```markdown
flask
redis
```

4. 创建一个  `Dockerfile`:

```bash
# syntax=docker/dockerfile:1
FROM python:3.12-alpine  # Builds an image with the Python 3.12 image
WORKDIR /code  # Sets the working directory to `/code`

# 重点：在安装包之前，先切换为阿里云镜像源（加速核心）
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

ENV FLASK_APP=app.py  # Sets environment variables used by the `flask` command
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers  # Installs `gcc` and other dependencies
COPY requirements.txt .  # Copies `requirements.txt`
RUN pip install -r requirements.txt  # Installs the Python dependencies
COPY . .  # Copies the current directory `.` in the project to the workdir `.` in the image
EXPOSE 5000
CMD ["flask", "run", "--debug"]  # Sets the default command for the container to `flask run --debug`
```

5. 创建一个 `.env` 文件来存储配置值：

```bash
APP_PORT=8000
REDIS_HOST=redis
REDIS_PORT=6379
```

6. 创建一个 `.dockerignore` 文件，避免不必要的文件出现在你的构建上下文中：

```bash
.env
*.pyc
__pycache__
redis-data
```

### 定义并启动您的服务

1. 在你的项目目录中创建 `compose.yaml`，并粘贴以下内容：

```bash
services:
  web:
    build: . # 这个build就会找本文件夹内的Dockerfile
    ports:
      - "${APP_PORT}:5000"
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}

  redis:
    image: redis:alpine # 这个是web需要用的
```

2. 启动你的应用：

```bash
 docker compose up
```

只需一个命令，你就能从配置文件创建并启动所有服务。Compose 构建你的网页镜像，拉取 Redis 镜像，启动两个容器。

3. Open `http://localhost:8000`. You should see:

### 为了解决启动竞争，Compose 需要等到 `redis` 确认正常后再开始`网页 `。

1. 更新 `compose.yaml`：

```bash
services:
  web:
    build: .
    ports:
      - "${APP_PORT}:5000"
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
    depends_on:
      redis:
        condition: service_healthy # 检查条件就是服务的健康检测
 
  redis:
    image: redis:alpine
    healthcheck: # 监控检测
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5
      start_period: 10s
```

`start_period` 给 Redis10 秒初始化时间，然后开始健康检查。在这个窗口内的任何失败都不计入重试次数。

`Interval` 在开始时间结束后每 5 秒进行一次检定。

`timeout`会给每个检查 3 秒的时间来响应，否则视为失败。

`重试`设置连续失败次数，之后 Compose 会将容器标记为不健康。在间`隔：5 秒 `、` 重试次数为 5` 秒时，Compose 会等待最多 25 秒才放弃。

### 启用 Compose Watch 以获取实时更新

1. 更新 `compose.yaml`，将 `develop.watch` 块添加到 `Web` 服务中：

```bahs
services:
  web:
    build: .
    ports:
      - "${APP_PORT}:5000"
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
    depends_on:
      redis:
        condition: service_healthy
    develop:
      watch:
        - action: sync+restart
          path: .
          target: /code
        - action: rebuild
          path: requirements.txt

  redis:
    image: redis:alpine
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5
      start_period: 10s
```

` sync+restart`操作会监控你主机上的项目目录（`.`）。当文件发生变化时，Compose 会将任何更改的文件复制到 `/code` 中运行中的容器内，然后重启容器。由于容器重启时已包含更新文件，Flask 直接读取新代码——无需手动重建或重启。

`requirements.txt` 的`rebuild`操作会在添加新依赖时触发完整的映像重建，因为安装包需要重建映像，而不仅仅是同步文件。

2. 启用 Watch 开始

```bash
docker compose up --watch
```

### 数据卷的持久化

1. 更新 compose.yaml：

```bash
services:
  web:
    build: .
    ports:
      - "0.0.0.0:${APP_PORT}:5000"
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
    depends_on:
      redis:
        condition: service_healthy
    develop:
      watch:
        - action: sync+restart
          path: .
          target: /code
        - action: rebuild
          path: requirements.txt

  redis:
    image: redis:alpine
    volumes:
      - redis-data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5
      start_period: 10s

volumes:
  redis-data:
```

现在用 `docker compose down -v` 重置计数器。
`-v` 标志会移除命名卷和容器。有意识地使用它——它会永久删除存储的数据。

### 用多个 Compose 文件构建你的项目结构

1. 在你的项目目录里创建一个名为 `infra.yaml` 的新文件，并将 Redis 服务和卷迁移到其中：

```bash
 services:
  redis:
    image: redis:alpine
    volumes:
      - redis-data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5
      start_period: 10s

volumes:
  redis-data:
```

2. 更新 `compose.yaml` 以包含 `infra.yaml`：

```bash
include:
  - path: ./infra.yaml

services:
  web:
    build: .
    ports:
      - "${APP_PORT}:5000"
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
    depends_on:
      redis:
        condition: service_healthy
    develop:
      watch:
        - action: sync+restart
          path: .
          target: /code
        - action: rebuild
          path: requirements.txt
```

### 检查并调试你的运行栈

1. 在开始堆栈之前，请确认 Compose 已经解析了你的 .env 变量并正确合并了所有文件：

```bash
docker compose config
```

![image-20260414191306595](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260414191306924.png)

###从所有服务流式日志

```bash
 # 查看全部
 docker compose logs -f
 # 只查看web
 docker compose logs -f web
```

### 在运行中的容器中运行命令

```bash
# 运行命令查看环境变量
docker compose exec web env | grep REDIS
REDIS_HOST=redis
REDIS_PORT=6379


# 运行命令查看redis
docker compose exec web python -c "import redis; r = redis.Redis(host='redis'); print(r.ping())"
```





# CI/CD之Jenkins



