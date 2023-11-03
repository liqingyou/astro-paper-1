---
author: liqingyou
pubDatetime: 2023-11-03T11:55:54.547Z
title: golang gin windows 打包
postSlug: ""
featured: true
tags:
  - golang
  - gin
  - linux
description: "PowerShell 不同环境下打包其他平台设置"
---

### 不同环境下命令

#### For Command Prompt, the commands to set these environment variables would be:
#### 对于命令提示符，用于设置这些环境变量的命令为：
```
set GOOS=linux
set GOARCH=amd64
```
#### For PowerShell, the commands would be:
#### 对于 PowerShell，命令为：
```
$env:GOOS = "linux"
$env:GOARCH = "amd64"
```
#### For a Unix-based terminal:
#### 对于基于 Unix 的终端：
```
export GOOS=linux
export GOARCH=amd64
```

### 打包
```
go build example.go 
或者
go build -o myapp

```

### 增加权限
```
chmod +x myapp
```