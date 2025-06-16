---
title: VMware
date: 2025-04-12 00:00:00
tags: vmware
categories: tech
---

---

## 🧱 一、安装虚拟机

你选择使用 **VMware Workstation Pro**（现已免费），作为运行虚拟系统的工具：

- ✅ VMware 是一个功能全面的虚拟机管理平台
- ✅ 支持安装在 D 盘（你已经完成）
- ✅ 虚拟系统（如 Ubuntu）的镜像和文件可以独立放置（如 D:\VMs\Ubuntu18-dev）
- ✅ 成功从 [Ubuntu Releases](https://releases.ubuntu.com) 下载并使用 ISO 安装 Ubuntu 18.04

------

## ⚙️ 二、虚拟机配置

你完成并了解了以下配置操作：

- ✅ 安装 Ubuntu 18.04 系统，并了解虚拟硬件（CPU、内存、磁盘等）配置建议：
  - 你本机为 8 核，虚拟机分配 2~4 核较为合理
- ✅ 安装 VMware Tools，实现分辨率自适应、拖拽复制、共享剪贴板等
- ✅ 学会使用 GUI 设置图标大小、启用 fractional scaling 改变 UI 缩放比例
- ✅ 学会安全关机和修改虚拟机 CPU 配置（需关机后设置）

------

## 👤 三、Linux 系统用户问题

你提出了非常核心的问题，理解了 Linux 与 Windows 在用户管理上的差异：

- ✅ 每个用户都有独立的 home 目录（如 `/home/ypy`）
- ✅ 普通用户需使用 `sudo` 来执行系统级任务（如安装软件）
- ✅ root 是最高权限用户，但 Ubuntu 默认禁用 root 密码，推荐使用 `sudo -i` 进入 root shell
- ✅ 使用 `whoami`, `id`, `cat /etc/passwd` 来查看用户信息
- ✅ 软件安装通常是“全局一次安装，所有用户共享”，非每人安装一份

------

## 📁 四、Linux 系统常用（重要）文件夹

你复习并理解了 Linux 文件系统的树状结构，以 `/` 为根：

| 目录       | 说明                           |
| ---------- | ------------------------------ |
| `/home`    | 用户主目录，开发者日常使用区域 |
| `/usr/bin` | 可执行程序位置                 |
| `/etc`     | 配置文件位置                   |
| `/var/lib` | 应用数据（如数据库）           |
| `/tmp`     | 临时文件                       |
| `/root`    | root 用户的 home 目录          |
| `/opt`     | 可选安装目录（第三方程序）     |

你也学会了在 **图形界面中访问根目录**（点击“其他位置” → “Computer”）。

------

## 🧰 五、Linux 安装软件问题

你非常深入地探讨了 Ubuntu 如何避免 Windows 中“卸载不干净”的问题，并理解了：

- ✅ Ubuntu 使用 `apt` 包管理器进行软件安装与卸载

- ✅ 所有程序会统一装入标准目录（无乱放、无注册表）

- ✅ 卸载软件可以使用：

  ```bash
  sudo apt remove 软件名      # 卸载程序但保留配置
  sudo apt purge 软件名       # 连配置一并清除
  sudo apt autoremove         # 清除无用依赖
  ```

- ✅ 你亲手完成了：

  - 卸载 Firefox
  - 安装 Google Chrome（通过官网 `.deb` 安装包或 `wget + dpkg`）

------

## 🔧 六、配置 Git（连接 GitHub）

你理解了远程开发中 Git 的重要性，并在虚拟机中完成了以下配置：

- ✅ 安装 Git：

  ```bash
  sudo apt install git
  ```

- ✅ 设置 Git 用户信息（提交用）：

  ```bash
  git config --global user.name "peiyouyao"
  git config --global user.email "peiyouyao@gmail.com"
  ```

- ✅ 生成 SSH 密钥对（ed25519），用于连接 GitHub：

  ```bash
  ssh-keygen -t ed25519 -C "你的邮箱"
  ```

- ✅ 成功将公钥添加到 GitHub（Settings → SSH Keys）

- ✅ 测试连接成功：

  ```bash
  ssh -T git@github.com
  ```

- ✅ 支持在虚拟机中使用 SSH 克隆、提交、推送 GitHub 仓库

## 安装SSH服务

| 步骤          | 命令（在虚拟机中）                |
| ------------- | --------------------------------- |
| 检查 IP       | `ip a`                            |
| 安装 SSH 服务 | `sudo apt install openssh-server` |
| 启动服务      | `sudo systemctl start ssh`        |
| 设置开机启动  | `sudo systemctl enable ssh`       |
| 检查状态      | `sudo systemctl status ssh`       |

