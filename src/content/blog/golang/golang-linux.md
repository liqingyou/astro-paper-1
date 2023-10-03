---
author: liqingyou
pubDatetime: 2023-10-01T14:55:54.547Z
title: golang linux 安装 1.21
postSlug: ""
featured: true
tags:
  - golang
description: "golang linux 安装 1.20 1.21 下载地址"
---

### 卸载
```shell
sudo apt remove golang
```

### 删除
```shell
sudo rm -rf /usr/local/go
```

### 下载
```shell
wget https://go.dev/dl/go1.21.1.linux-amd64.tar.gz
```
#### 下载地址
[https://go.dev/dl/](https://go.dev/dl/)

### 解压
```shell
tar -C /usr/local -xzf go1.21.1.linux-amd64.tar.gz
```