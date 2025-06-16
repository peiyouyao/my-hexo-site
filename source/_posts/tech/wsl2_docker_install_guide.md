---
title: Install Docker in WSL2
date: 2025-04-23 00:00:00
tags: wsl
categories: tech
---

---

# 🐳 WSL2 中安装 Docker 全流程笔记（纯二进制安装方式）

> 本文记录了在 WSL2（Ubuntu 20.04）环境下，通过**二进制方式**安装 Docker 的完整步骤及相关概念解析。适用于无需依赖 Docker Desktop，打造轻量、高效的开发环境。

---

## 📦 一、安装流程概述

### 1️⃣ 下载安装包
- 访问阿里云镜像站下载 Docker 二进制包：
- 地址：https://mirrors.aliyun.com/docker-ce/linux/static/stable/x86_64/

```bash
wget https://mirrors.aliyun.com/docker-ce/linux/static/stable/x86_64/docker-24.0.7.tgz
```

### 2️⃣ 解压并安装

```bash
tar -xvzf docker-24.0.7.tgz
sudo cp docker/* /usr/local/bin/
```

此步骤将 `docker` 和 `dockerd` 等核心可执行文件复制到系统路径下。

---

## ⚙️ 二、配置镜像加速器

创建配置文件 `/etc/docker/daemon.json`：

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json > /dev/null <<EOF
{
  "registry-mirrors": ["https://<你的阿里云ID>.mirror.aliyuncs.com"]
}
EOF
```

> 作用：加速拉取 Docker 镜像，解决国内网络问题。

ACR会为每一个账号（阿里云账号或RAM用户）生成一个镜像加速器地址，配置镜像加速器前，您需要获取镜像加速器地址。

1. 登录[容器镜像服务控制台](https://cr.console.aliyun.com/?spm=a2c4g.11186623.0.0.75121d82E0FDn8)。
2. 在左侧导航栏选择***\*镜像工具\** > \**镜像加速器\****
3. 在**镜像加速器**页面获取**加速器地址**。

---

## 🚀 三、启动 Docker 守护进程

由于 WSL2 默认无 systemd，需手动启动 `dockerd`：

```bash
sudo dockerd > /tmp/dockerd.log 2>&1 &
```

或者编写启动脚本 `start-docker.sh` 方便使用。

---

## 👤 四、配置用户权限

避免每次运行 Docker 命令都需要 sudo：

```bash
sudo usermod -aG docker $USER
exec su -l $USER
```

---

## ✅ 五、验证 Docker 是否安装成功

```bash
docker version
docker run hello-world
```

---

## 🧠 六、关键概念解析

### 1️⃣ `dockerd` —— Docker Daemon（守护进程）
- 负责管理容器、镜像、网络等所有后台服务
- 必须运行 `dockerd`，客户端 `docker` 命令才能工作

### 2️⃣ `/var/run/docker.sock`
- Docker 客户端与守护进程通信的 Unix Socket 文件
- 权限不足会导致 `permission denied` 错误

### 3️⃣ `daemon.json`
- Docker 的主配置文件，通常用于设置镜像加速器、日志驱动等
- 位置：`/etc/docker/daemon.json`

### 4️⃣ 镜像加速器
- 国内访问 Docker Hub 缓慢，通过阿里云、网易等提供的加速服务，大幅提升镜像拉取速度

---

## 📌 七、常用命令总结

| 命令 | 说明 |
|------|------|
| `sudo dockerd &` | 启动 Docker 后台服务 |
| `docker ps` | 查看运行中的容器 |
| `docker images` | 查看本地镜像 |
| `docker run hello-world` | 测试 Docker 是否正常运行 |
| `docker stop <容器ID>` | 停止容器 |
| `docker rm <容器ID>` | 删除容器 |

---

> 本文档适用于个人开发环境，生产环境建议结合 systemd 或使用 Docker Desktop。

## 脚本

`docker_start.sh`

```bash
sudo dockerd > /tmp/dockerd.log 2>&1 &
```



注: 一下脚本不合适国内网络环境, APT get不到, 其中安装的多个应用:

`docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`

均涵盖在阿里云镜像的二进制程序压缩包中

`wsl2_docker_install.sh`

```bash
#!/bin/bash
# WSL2 安装 Docker Engine 脚本 (适用于 Ubuntu)

echo "🚨 清理旧版本 Docker..."
sudo apt purge -y docker.io docker-doc docker-compose docker-compose-v2 docker-engine docker containerd runc
sudo apt autoremove -y

echo "✅ 安装依赖..."
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

echo "🔑 添加 Docker 官方 GPG 密钥..."
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "📦 添加 Docker APT 源..."
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

echo "🚀 开始安装 Docker Engine..."
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

echo "✅ Docker 安装完成！"

echo "⚡ 配置阿里云镜像加速器..."
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json > /dev/null <<EOF
{
  "registry-mirrors": ["https://<你的阿里云ID>.mirror.aliyuncs.com"]
}
EOF

echo "🚨 注意：WSL2 无法使用 systemd 启动 Docker 服务"
echo "👉 请使用以下命令手动启动 Docker 守护进程："
echo "sudo dockerd > /tmp/dockerd.log 2>&1 &"
echo ""
echo "💡 启动后，使用 'docker ps' 进行验证"

```

