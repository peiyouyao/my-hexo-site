---
title: Clash in Win, proxy in wsl
date: 2025-04-23 00:00:00
tags: wsl
categories: tech
---



# 🐧 使用 Clash + WSL 安装并配置 Docker 代理访问外网的完整指南

> 本笔记记录了在 Windows 主机上使用 Clash 实现代理，并在 WSL 中配置代理环境与 Docker 访问 Docker Hub 的全过程。

------

## 🧱 系统环境

- Windows 主机已安装 **Clash for Windows**（英文版）
- 使用 **WSL2**（Ubuntu 发行版）
- 在 **WSL 中独立安装了 Docker Engine**（无 Docker Desktop）

------

## 📦 步骤一：Windows 上配置 Clash for Windows

### 1. 下载并安装 Clash for Windows

从 GitHub 下载最新版：https://github.com/Fndroid/clash_for_windows_pkg

### 2. 导入订阅配置

- 打开 Clash
- 菜单栏点击 **Profiles**
- 点击 `Import` → `Subscription`
- 粘贴你的订阅链接 → 下载并激活

### 3. 设置代理模式与端口

- 点击 **General** 标签页
  - `Allow LAN`: ✅ 开启
  - `Mixed Port`: 默认 `7890`，保留
  - `Mode`: 推荐选 **Global**（全局代理）
  - `System Proxy`: 可选开关（影响 Windows 浏览器）

------

## 🌐 步骤二：在 WSL 中设置 Clash 代理

### 1. 添加以下函数到 `~/.bashrc`

```bash
# 获取代理地址
get_proxy_host() {
  ip route | grep default | awk '{print $3}'
}

# 获取代理端口（默认 7890，可通过 PROXY_PORT 环境变量覆盖）
get_proxy_port() {
  echo "${PROXY_PORT:-7890}"
}

# 启用代理
proxy_on() {
  WIN_PROXY_HOST=$(get_proxy_host)
  WIN_PROXY_PORT=$(get_proxy_port)
  export http_proxy="http://${WIN_PROXY_HOST}:${WIN_PROXY_PORT}"
  export https_proxy="http://${WIN_PROXY_HOST}:${WIN_PROXY_PORT}"
  export all_proxy="socks5://${WIN_PROXY_HOST}:${WIN_PROXY_PORT}"
  echo "⚠️ Make sure Clash is running in Windows first."
  echo "✅ Proxy set to $WIN_PROXY_HOST:$WIN_PROXY_PORT"
  echo "----------------------------------------"
  curl -s cip.cc || echo "❌ curl failed. Clash may not be running or port is blocked."
  echo "----------------------------------------"
}

# 关闭代理
proxy_off() {
  unset http_proxy
  unset https_proxy
  unset all_proxy
  echo "Proxy disabled"
}
```

### 2. 启用代理

```bash
source ~/.bashrc
proxy_on
```

确认返回 IP 地址为代理出口地址。

------

## 🐳 步骤三：在 WSL 中运行 Docker 并使用代理拉镜像

### 情况：你未启用 `systemd`，需手动启动 Docker Daemon

#### 1. 使用代理启动 Docker Daemon：

```bash
start_docker_with_proxy() {
  echo "🚀 Starting Docker daemon with proxy..."
  WIN_PROXY_HOST=$(get_proxy_host)
  WIN_PROXY_PORT=7890
  sudo HTTP_PROXY=http://${WIN_PROXY_HOST}:${WIN_PROXY_PORT} \
       HTTPS_PROXY=http://${WIN_PROXY_HOST}:${WIN_PROXY_PORT} \
       dockerd > /tmp/dockerd.log 2>&1 &
  sleep 2
  docker info > /dev/null 2>&1 && echo "✅ Docker started" || echo "❌ Failed to start Docker"
}
```

#### 2. 或使用非代理方式启动（适用于无需翻墙时）

```bash
start_docker() {
  echo "🚀 Starting Docker daemon without proxy..."
  sudo dockerd > /tmp/dockerd.log 2>&1 &
  sleep 2
  docker info > /dev/null 2>&1 && echo "✅ Docker started" || echo "❌ Failed to start Docker"
}
```

#### 3. 停止 Docker

```bash
stop_docker() {
  echo "🛑 Stopping Docker daemon..."
  sudo pkill dockerd && echo "✅ Docker daemon stopped"
}
```

------

## 📁 步骤四：使用 `docker compose` 启动 Consul 服务

### 1. 项目结构

```bash
~/code/projects/gorder/
└── docker-compose.yml
```

### 2. 示例 `docker-compose.yml`

```bash
version: "3.9"
services:
  consul:
    image: hashicorp/consul
    container_name: consul-server
    command: agent -server -ui -node=server-1 -bootstrap-expect=1 -client=0.0.0.0
    ports:
      - "8500:8500"
      - "8600:8600/udp"
```

### 3. 快捷函数或 alias 启动 Consul

```bash
start_consul() {
  cd ~/code/projects/gorder
  docker compose up -d
}

alias consulup='cd ~/code/projects/gorder && docker compose up -d'
alias consuldn='cd ~/code/projects/gorder && docker compose down'
```

------

## 🔁 一键启动全流程（推荐顺序）

```bash
proxy_on                     # 开启 Clash 网络代理
start_docker_with_proxy      # 启动 Docker Daemon 使用代理
start_consul                 # 启动 Consul 服务
```

关闭时：

```bash
consuldn                     # 停止容器
stop_docker                  # 停止 Docker Daemon
proxy_off                    # 关闭代理
```

------

## ✅ 常见问题

| 问题                             | 原因                                 | 解决方法                         |
| -------------------------------- | ------------------------------------ | -------------------------------- |
| `curl` 能用但 `docker pull` 失败 | Docker daemon 没有使用代理           | 用 `HTTP_PROXY=... dockerd` 启动 |
| `127.0.0.1:7890` 无法连接        | WSL 的 localhost ≠ Windows localhost | 使用 `ip route` 获取宿主机地址   |
| `docker compose` 找不到文件      | 文件名应为 `docker-compose.yml`      | 使用标准命名或 `-f` 指定文件     |
| `systemctl` 无法使用             | WSL 未启用 systemd                   | 添加 `/etc/wsl.conf` 并重启 WSL  |