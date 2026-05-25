---
title: ansible-docker-deploy
published: 2026-05-25
updated: 2026-05-25
description: ansible-docker-deploy学习
image: "api"   
tags: [RockyLinux]
category: linux
draft: false
---

### **项目目标**

使用 **https://github.com/fccn/ansible-docker-deploy** 这个 Ansible Role，在 **Rocky Linux 9** 服务器上实现 Docker 应用的自动化部署。

---

### **完整流程总结**

### **阶段 1：安装 Docker（Rocky 9）**

**遇到的问题**：

- 官方仓库添加失败（SSL connect error）
- `docker run hello-world` 失败（connection refused / timeout）

**解决方案命令**：

```bash
# 1. 更新系统
sudo dnf update -y
sudo dnf install -y yum-utils ca-certificates

# 2. 使用阿里云 Docker 源
sudo dnf config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sudo sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo

# 3. 安装 Docker
sudo dnf makecache
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 4. 启动 Docker
sudo systemctl enable --now docker

# 5. 加入 docker 组
sudo usermod -aG docker $USER
newgrp docker
```

---

### **阶段 2：配置 Docker 国内镜像加速**

**遇到的问题**：
- 多次 `docker run hello-world` 失败（timeout / connection refused）

**最终成功命令**：

```bash
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
    "https://docker.xuanyuan.me",
    "https://docker.1ms.run",
    "https://docker.m.daocloud.io",
    "https://dockerproxy.com",
    "https://hub-mirror.c.163.com"
  ]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker

# 测试
docker run hello-world
```

**结果**：`hello-world` 成功运行。

---

### **阶段 3：控制机（WSL Ubuntu）准备 Ansible**

```bash
sudo apt update && sudo apt install ansible git sshpass -y
```

---

### **阶段 4：下载 Role**

```bash
mkdir -p ~/ansible-projects && cd ~/ansible-projects
git clone https://github.com/fccn/ansible-docker-deploy.git
```

---

### **阶段 5：SSH 连接问题解决**

**遇到的问题**：
1. `Permission denied (publickey,gssapi-keyex...)`
2. `sshpass program` 未安装

**解决方案**：

```bash
# 安装 sshpass
sudo apt install sshpass -y
```

**inventory.ini**（最终版本）：
```ini
[web_servers]
rocky9 ansible_host=192.168.66.11 ansible_user=root ansible_ssh_pass=你的root密码
```

**测试连接**：
```bash
ansible web_servers -i inventory.ini -m ping
```

**结果**：`SUCCESS` + `ping: "pong"`

---

### **阶段 6：最终成功的 deploy.yml 配置**

**docker-compose.yml.j2**：
```yaml
version: '3.8'
services:
  web:
    image: nginx:alpine
    container_name: nginx-test
    ports:
      - "8080:80"
    restart: always
```

**deploy.yml**（最终稳定版本）：
```yaml
---
- name: Deploy Nginx with Ansible Docker Role
  hosts: rocky9
  become: yes
  roles:
    - ansible-docker-deploy

  vars:
    docker_deploy_base_folder: /opt/myapp
    docker_deploy_compose_template: docker-compose.yml.j2

    docker_deploy_templates:
      - src: docker-compose.yml.j2
        dest: "{{ docker_deploy_base_folder }}/docker-compose.yml"
        mode: '0644'
```

---

### **阶段 7：最终部署命令**

```bash
# 清理旧目录（可选）
ansible web_servers -i inventory.ini -m shell -a "rm -rf /opt/myapp"

# 执行部署
ansible-playbook -i inventory.ini deploy.yml -vvv
```

---

### **最终验证命令**

```bash
docker ps
ls -l /opt/myapp
curl -I http://localhost:8080
```

**验证结果**：Nginx 容器正常运行，8080 端口可访问。

---

### **完整项目文件结构（控制机）**

```
~/ansible-projects/
├── ansible-docker-deploy/          # git clone 的 Role
├── inventory.ini
├── deploy.yml
└── docker-compose.yml.j2
```



