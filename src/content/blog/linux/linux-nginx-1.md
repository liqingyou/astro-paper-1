---
author: liqingyou
pubDatetime: 2023-10-26T10:15:54.547Z
title: linux Ubuntu 安装 nginx
postSlug: ""
featured: true
tags:
  - linux
  - Ubuntu
description: linux Ubuntu 安装 nginx
---

在安装 Nginx 之前，请确保您的系统上已经安装了 `apt` 或 `apt-get` 包管理工具。以下是在 Ubuntu 上安装 Nginx 的步骤：

### 步骤 1: 更新包列表

```bash
sudo apt update
```

### 步骤 2: 安装 Nginx

```bash
sudo apt install nginx
```

### 步骤 3: 启动 Nginx 服务

安装完成后，Nginx 通常会自动启动。如果没有自动启动，您可以手动启动它：

```bash
sudo systemctl start nginx
```

### 步骤 4: 验证安装

通过访问服务器的公共 IP 地址或域名来验证 Nginx 是否已成功安装。在浏览器中输入 `http://服务器的 IP 地址` 或 `http://您的域名`，如果安装成功，您应该能够看到 Nginx 的欢迎页面。

### 其他有用的命令：

- **检查 Nginx 服务状态**：

```bash
sudo systemctl status nginx
```

- **重新启动 Nginx 服务**：

```bash
sudo systemctl restart nginx
```

- **停止 Nginx 服务**：

```bash
sudo systemctl stop nginx
```

这些命令将帮助您控制 Nginx 服务的运行。希望这些步骤能够帮助您在 Ubuntu 上成功安装 Nginx。