---
title: 镜像仓库harbor
published: 2026-05-14
updated: 2026-05-14
description: 镜像仓库harbor
image: "api"   
tags: [harbor]
category: docker
draft: false
---

# 保姆级搭建企业级镜像仓库harbor，jenkins+k8s部署cicd必备组件

## **前言**

> 官网：[https://goharbor.io/](https://link.zhihu.com/?target=https%3A//goharbor.io/)
> [Harbor](https://zhida.zhihu.com/search?content_id=239967407&content_type=Article&match_order=1&q=Harbor&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3Nzg4NTE1NTYsInEiOiJIYXJib3IiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyMzk5Njc0MDcsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.XsY9MdZOj7mWG1G_MEMzGDtaEkrER8pERgKqg1x-3jw&zhida_source=entity)是由VMware公司开源的企业级的[Docker Registry](https://zhida.zhihu.com/search?content_id=239967407&content_type=Article&match_order=1&q=Docker+Registry&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3Nzg4NTE1NTYsInEiOiJEb2NrZXIgUmVnaXN0cnkiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyMzk5Njc0MDcsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.fHTsz4Spf_DkBE7IKwjzbAP4ot9zZjDOEY30n8uPvig&zhida_source=entity)管理项目，它包括权限管理(RBAC)、[LDAP](https://zhida.zhihu.com/search?content_id=239967407&content_type=Article&match_order=1&q=LDAP&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3Nzg4NTE1NTYsInEiOiJMREFQIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjM5OTY3NDA3LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.n1FjjsJ06TdGMCDS52h0y7r-C8ksrgNuT08BLN14WM0&zhida_source=entity)、日志审核、管理界面、自我注册、镜像复制和中文支持等功能。

- 为什么需要harbor？
  Docker容器应用的开发和运行离不开可靠的镜像管理，虽然Docker官方也提供了公共的镜像仓库，但是从安全和效率等方面考虑，部署我们私有环境内的Registry也是非常必要的。
- 要求
  - 磁盘空间要有3G以上的空余
  - 内存要有4G及以上的空余
  - 不然就会一直转圈，核心服务不断重启
  - ![image-20260514092442511](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/imgs/20260514092446852.png)


## **安装**

### **安装方式**

安装方式主要有三张：

- [helm](https://zhida.zhihu.com/search?content_id=239967407&content_type=Article&match_order=1&q=helm&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3Nzg4NTE1NTYsInEiOiJoZWxtIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjM5OTY3NDA3LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.n6wMaAHJzDtX7nYjvdRqZLvbz5iZu6vR6H2HDPbZy_k&zhida_source=entity)安装

- - 可以参考kebesphere官网使用helm安装： [https://kubesphere.io/zh/docs/v3.3/devops-user-guide/how-to-integrate/harbor/](https://link.zhihu.com/?target=https%3A//kubesphere.io/zh/docs/v3.3/devops-user-guide/how-to-integrate/harbor/)

- 下载离线安装包手动安装

- - 离线安装包地址：[https://github.com/goharbor/harbor](https://link.zhihu.com/?target=https%3A//github.com/goharbor/harbor)

- 使用kubesphere开启OpenPitrix后，在应用商店内安装

- - 参考网址：[https://kubesphere.io/zh/docs/v3.3/application-store/built-in-apps/harbor-app/](https://link.zhihu.com/?target=https%3A//kubesphere.io/zh/docs/v3.3/application-store/built-in-apps/harbor-app/)

### **离线安装包手动安装**

**1、下载安装包**

> 若命令下载太慢，可以先去github网站把包下载下来，再手动上传~
>
> 或者使用https://doget.nocsdn.com/ 获得高速下载链接 （https://doget.nocsdn.com/api/download?token=eyJhbGciOiJIUzI1NiJ9.eyJ1cmwiOiJodHRwczovL2dpdGh1Yi5jb20vZ29oYXJib3IvaGFyYm9yL3JlbGVhc2VzL2Rvd25sb2FkL3YyLjE0LjMvaGFyYm9yLW9mZmxpbmUtaW5zdGFsbGVyLXYyLjE0LjMudGd6In0.e-kiYVPmJL2SCfAoFIOLoVyWB8ejlkESPsOLhNujAiI）自己去下，这是我的

```text
# wget命令下载
wget https://github.com/goharbor/harbor/releases/download/v2.10.3/harbor-offline-installer-v2.10.3.tgz
# 命令下载太慢，可以试着使用 加速器，个别系统可以使用
wget https://gh-proxy.com/https://github.com/goharbor/harbor/releases/download/v2.10.3/harbor-offline-installer-v2.10.3.tgz
wget https://kkgithub.com/goharbor/harbor/releases/download/v2.10.3/harbor-offline-installer-v2.10.3.tgz
wget https://gh.llkk.cc/https://github.com/goharbor/harbor/releases/download/v2.10.3/harbor-offline-installer-v2.10.3.tgz
```

**2、解压**

> 解压命令：`tar -xzvf harbor-offline-installer-v2.14.3.tgz`
> 这个包解压内部包含如下图：

```sh
[root@localhost code]# ls
harbor-offline-installer-v2.14.3.tgz
[root@localhost code]# tar -xzvf harbor-offline-installer-v2.14.3.tgz
harbor/harbor.v2.14.3.tar.gz
harbor/prepare
harbor/LICENSE
harbor/install.sh
harbor/common.sh
harbor/harbor.yml.tmpl
```

3、配置harbor.yml

```text
# 使用cp命令复制一个harbor.yml
cp harbor.yml.tmpl harbor.yml
```

> 修改配置信息 `vim harbor.yml`
> 注意：若不配置https，则可以将https的配置注释掉，若使用https，则需要配置https证书，然后将证书路径如实填写（证书全路径）~

![img](https://pic1.zhimg.com/v2-0070bcb18badee9f29e1a6cad58c1de2_1440w.jpg)

配置https证书

> 官网文档：[https://goharbor.io/docs/2.5.0/install-config/configure-https/](https://link.zhihu.com/?target=https%3A//goharbor.io/docs/2.5.0/install-config/configure-https/)
> 如果代理商已有域名证书，则可以直接使用，跳过如下步骤~

- 生成CA证书私钥

> 会生成ca.crt和ca.key俩个秘钥文件~

```text
openssl genrsa -out ca.key 4096

# 【yourdomain.com】  改成自己的  域名
openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=121.199.49.72" \
 -key ca.key \
 -out ca.crt
```

![img](https://pic1.zhimg.com/v2-a975820ceff188267e749058c792d6cc_1440w.jpg)

4、安装检查

> 可以使用 ./prepare 检查是否可以正常安装

![img](https://pic3.zhimg.com/v2-0a58c746ec20209f3a5b28c2fed591c8_1440w.jpg)

5、安装 docker-compose

> 需要提前安装docker-compose，因为它是通过docker-compose来管理的
> yum -y install docker-compose
> 或者
> curl -L "[https://github.com/docker/compose/releases/download/1.26.2/docker-compose-](https://link.zhihu.com/?target=https%3A//github.com/docker/compose/releases/download/1.26.2/docker-compose-)$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
> chmod +x /usr/local/bin/docker-compose
> 检查是否安装成功：`docker-compose -v`
> 若失败，可以尝试`cat docker-compose`

```text
# 查看 docker-compose 应用
docker-compose ps
# 启动命令
docker-compose up -d
# 停止命令
docker-compose down
```

6、启动

> `sh install.sh` 就行（如果docker版本过低，会导致这个命令报错~）
> 启动会生成如下俩个文件 docker-compose.yml 和 common

![img](https://pic2.zhimg.com/v2-49567560412d96202beb2ade4cc2629f_1440w.jpg)

![image-20260514093202268](https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/imgs/20260514093205801.png)

## 安装之后

### 生成的容器

> 启动后，会有如下容器运行~

![img](https://pic1.zhimg.com/v2-4dc6ab471f07443738f53dad29360e7c_1440w.jpg)

## 常见问题

### 问题一：

![img](https://pic3.zhimg.com/v2-41d19187014074fc95676a3979786b40_1440w.jpg)

解决方法：添加docker仓库信任，并重启docker

```text
tee /etc/docker/daemon.json <<-'EOF'
{
  "insecure-registries": ["172.27.169.233:8831"]
}
EOF
systemctl daemon-reload
systemctl restart docker
```

### 问题二：

![img](https://pic1.zhimg.com/v2-0fd75dbfa1f9969e2f1c2c4da72ca620_1440w.jpg)

解决方法：

```text
# 进入docker 目录 
cd /etc/docker

mkdir cert.d

cd cert.d

mkdir 域名或者ip:端口

cd 域名或者ip:端口

cp /data/cert/ca.crt ./

systemctl restart docker
```