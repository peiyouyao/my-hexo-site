---
title: WSL
date: 2025-04-12 00:00:00
tags: wsl
categories: tech
---

---

# 🐧 WSL 安装与配置笔记

------

## ✅ 一、WSL 的安装与说明

### 📌 为什么使用 WSL：

- 官方集成，资源轻，占用少，启动快
- 兼容大部分 Linux 命令、开发工具
- 支持 GUI（WSLg），可运行 Linux 图形程序
- 可直接访问 Windows 文件系统（`/mnt/c/...`）

### 📦 安装命令（管理员 PowerShell）：

```powershell
wsl --install
```

默认安装 Ubuntu（最新版）+ WSL2 平台。

------

## ✅ 二、安装 Ubuntu 虚拟机至 D 盘

> 为节省空间，避免默认安装在 C:\Users\AppData

### 🧩 步骤：

1. 下载 Ubuntu Appx 安装包：
   - https://aka.ms/wslubuntu2004
2. 解压并手动注册：

```bash
# 将 .appx 改名为 .zip 并解压
Rename-Item .\Ubuntu_*.appx ubuntu.zip
Expand-Archive .\ubuntu.zip .\
.\ubuntu.exe
```

设置用户名密码，即初始化成功。

------

## ✅ 三、WSL 虚拟机初始化配置（推荐安装脚本）

### ⚙️ 更新 & 基础工具安装：

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl wget unzip vim net-tools htop
```

------

## ✅ 四、Git + SSH 配置

### 1️⃣ Git 安装与全局配置：

```bash
sudo apt install git -y

git config --global user.name "xxx"
git config --global user.email "your_email@example.com"
git config --global core.editor "vim"
```

------

### 2️⃣ SSH 密钥生成与配置：

#### 生成 SSH 密钥（本地和 WSL 各自生成）：

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

按提示一路回车，默认保存在：

```bash
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

#### 将公钥添加到 GitHub：

```bash
cat ~/.ssh/id_ed25519.pub
```

复制粘贴至 GitHub → `Settings > SSH and GPG Keys > New Key`

------

### 3️⃣ 在 Windows 主机配置 SSH 连接 Ubuntu 虚拟机

```bash
# 编辑 C:\Users\<你>\.ssh\config
Host ubuntu-wsl
    HostName 172.27.xxx.xxx         # 用 `ip addr show eth0` 获取
    User ypy
    IdentityFile C:/Users/<你>/.ssh/id_ed25519
```

------

## ✅ 五、远程 VSCode 开发推荐做法

- 安装 VSCode
- 安装扩展：`Remote - SSH` + `WSL`
- 用 `ssh ubuntu-wsl` 测试连接
- 成功连接后，使用 VSCode 的 Remote SSH 自动同步开发环境

------

## ✅ 六、SSH 服务启动命令（不使用 systemctl）

```bash
sudo apt install openssh-server -y
sudo service ssh start    # 启动 sshd
ps -ef | grep sshd        # 查看服务状态
```

------

## 📁 附：常用路径小抄

| 路径                     | 用途                        |
| ------------------------ | --------------------------- |
| `~/.ssh/authorized_keys` | 存储允许登录的公钥          |
| `/mnt/c/...`             | 访问 Windows C 盘           |
| `\\wsl$\Ubuntu`          | Windows 访问 Linux 文件系统 |
| `~/WorkStation/...`      | 你的开发工作区目录          |