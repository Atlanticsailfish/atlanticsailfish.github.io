---
title: Ubuntu命令合集
published: 2025-08-14
description: '一小撮Ubuntu命令合集，含系统管理、网络配置和故障排查。'
image: './posts-images/124836141_p0_master1200.jpg'
tags: [System,Linux,Ubuntu,Network]
category: 'Work'
draft: false 
lang: ''
---

> Cover image source: [Source](https://www.pixiv.net/artworks/124836141)

## 一小撮Ubuntu命令合集，含系统管理、网络配置和故障排查。



-----

### 系统信息查看

这些命令用于查看系统的基本信息，包括操作系统版本、CPU和内存使用情况。

```bash
# 查看操作系统版本
cat /etc/os-release

# 查看 CPU 信息
lscpu

# 查看内存使用情况
free -h

# 查看磁盘空间使用情况
df -h
```

-----

### 网络配置与防火墙设置

以下命令用于查看网络接口信息和配置 **ufw（Uncomplicated Firewall）** 防火墙规则。

```bash
# 查看详细网络/网卡信息
ip addr
# 也可以使用简写
# ip a 

# 查看 ufw 防火墙状态
sudo ufw status

# 允许 SSH 端口（22）
sudo ufw allow ssh

# 允许 HTTP 端口（80）
sudo ufw allow http

# 允许指定端口（5433）的 TCP 和 UDP 流量
sudo ufw allow 5433/tcp
sudo ufw allow 5433/udp

# 允许 HTTP（80）和 HTTPS（443）的 TCP 流量
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# 启用 ufw 防火墙
sudo ufw enable

# 再次查看 ufw 状态，确认规则已生效
sudo ufw status
```

-----

### 时间同步设置

这个命令用于将系统时区设置为上海。

```bash
# 将系统时区设置为亚洲/上海
sudo timedatectl set-timezone Asia/Shanghai
```

-----

### 高级网络和防火墙操作 (iptables) & Docker防火墙

这些命令展示了如何使用 **iptables** 进行更精细的网络规则管理。**请谨慎使用，错误的规则可能导致网络连接问题。**

```bash
# 查看 iptables OUTPUT 链规则及其行号
sudo iptables -L OUTPUT -n --line-numbers

# 尝试连接到指定 IP 和端口（用于测试）
# curl -v http://192.168.xx.xx:68xx/

# 在 OUTPUT 链的第一行插入一条新规则，拒绝发往 192.168.xx.xx:68xx 的 TCP 流量，并发送 TCP RESET 包
sudo iptables -I OUTPUT 1 -d 192.168.xx.xx -p tcp --dport 68xx -j REJECT --reject-with tcp-reset

# 查看 DOCKER-USER 链的规则，这个链通常用于 Docker 网络相关的规则
sudo iptables -L DOCKER-USER -n --line-numbers

# 删除 OUTPUT 链的第一条规则（请根据实际情况替换行号）
sudo iptables -D OUTPUT 1
```

-----

### 系统性能监控

Glances 是一个功能强大的命令行系统监控工具，它以用户友好的界面实时显示系统资源使用情况。

```bash
# 在 Ubuntu/Debian 系统上安装 Glances
sudo apt install glances

# 运行 glances
glances
```