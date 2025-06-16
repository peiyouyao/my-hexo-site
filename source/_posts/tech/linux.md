---
title: Linux Common Operations
date: 2025-04-12 00:00:00
tags: linux
categories: tech
---

---

# 🐧 Linux 常见操作对照手册（类比 Windows 系统）

> 本文旨在帮助熟悉 Windows 系统的用户快速掌握 Linux 中的常用系统操作，同时补充常用命令的含义便于记忆。

---

## 🧠 基础信息查看

### 查看当前用户名（whoami → who am i）
```bash
whoami  # 显示当前登录用户（who am I）
```

### 查看当前所在路径（pwd → print working directory）
```bash
pwd  # 显示当前所在的绝对路径
```

### 查看系统信息（uname → unix name）
```bash
uname -a         # 查看内核版本、架构
lsb_release -a   # 查看发行版信息
hostnamectl      # 显示主机名和系统版本等信息
```

---

## 📁 文件与目录操作

### 查看目录内容（ls → list segments）
```bash
ls -lh      # 列出当前目录下的文件，带单位和时间
ls -a       # 显示所有文件，包括隐藏文件（以.开头）
ls -R       # 递归列出所有子目录内容
```

### 复制 / 移动 / 删除 文件
```bash
cp file1 file2       # cp → copy，复制文件
cp -r dir1 dir2      # 递归复制整个目录
mv file newname      # mv → move，移动或重命名文件
rm file.txt          # rm → remove，删除文件
rm -r folder/        # 递归删除文件夹
```

### 修改文件权限（chmod → change mode）
```bash
chmod +x run.sh      # 增加执行权限
chmod 644 file.txt   # 设定具体权限（rw-r--r--）
```

### 7. 修改归属用户和组（chown → change owner）
```bash
chown yifan file.txt  # 把文件归属更改为 yifan
```

---

## 📋 文件查看与编辑

### 快速查看文件内容
```bash
cat file.txt         # cat → concatenate，一次性查看全部内容
less file.txt        # 分页查看，可上下翻页
head -n 10 file.txt  # 显示前 10 行
tail -n 10 file.txt  # 显示后 10 行

cat $(find dir1 dir2 -type f) # 打印出 dir1 dir2 目录下所有文件的内容 (递归进行)

find dir1 dir2 -type f | while read file; do
  echo "===== $file ====="
  cat "$file"
  echo
done # 在文件与文件之间加上 ===== 文件名 =====

find dir1 dir2 -type f -name "*.java" | while read file; do # 限制文件类型
  echo "===== $file ====="
  cat "$file"
  echo
done
```

### 编辑文件

```bash
nano file.txt        # 终端内简单编辑器
vim file.txt         # 高级编辑器（vi improved）
```

---

## 💻 系统资源管理

### 查看进程（top → table of processes）
```bash
top        # 实时显示系统进程和资源占用
htop       # 更友好、图形化的 top
ps aux     # 显示所有进程详细信息（ps → process status）
```

### 查看端口和连接（ss → socket statistics）

```bash
sudo ss -tulnp
# -p: 表示显示 监听该端口的进程（PID/名称）
# -n：不解析服务名称（例如显示 3306 而不是 mysql）
# -l：只显示监听状态（Listen）
```

```bash
ss -tuln           # 显示监听的 TCP/UDP 端口
lsof -i:8080       # 查看哪个进程占用端口（lsof → list open files）
```

---

## 💿 磁盘与空间

### 查看磁盘使用情况（df → disk free）
```bash
df -h  # 查看每个挂载点的磁盘使用情况
```

### 查看目录大小（du → disk usage）
```bash
du -sh .    # 当前目录总大小
du -sh *    # 当前目录下各文件/目录大小
```

### 查看内存使用情况
```bash
free -h  # 显示内存和 swap 使用情况
```

---

## 🧩 软件包管理

### 安装程序（apt → advanced package tool）
```bash
sudo apt install <软件名>
```

### 查看已安装的软件
```bash
dpkg -l  # 列出所有安装的软件包
```

### 卸载程序
```bash
sudo apt remove <软件名>
```

---

## ⚙️ 服务与启动项管理

### 查看服务状态（systemctl → system control）
```bash
sudo systemctl status mysql
```

### 启动 / 停止 / 重启服务
```bash
sudo systemctl start redis
sudo systemctl stop redis
sudo systemctl restart redis
```

---

## 🌐 网络相关

### 查看 IP 地址
```bash
ip a           # 查看详细 IP 信息
hostname -I    # 快速查看主机 IP
```

### 测试网络连接
```bash
ping www.baidu.com
curl -I https://google.com
```

---

## ✅ 推荐工具汇总

| 工具 | 命令 | 作用 |
|------|------|------|
| htop | `sudo apt install htop` | 进程查看器，替代 top |
| ncdu | `sudo apt install ncdu` | 图形化查看目录大小 |
| tree | `sudo apt install tree` | 以树形结构显示目录 |

---

> 本文由 ChatGPT 整理生成，适合初中高级 Linux 使用者类比 Windows 熟练掌握系统常见操作。
