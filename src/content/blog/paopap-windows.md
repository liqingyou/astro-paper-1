---
author: liqingyou
pubDatetime: 2023-10-03T14:55:54.547Z
title: PaoPao-ce 配置本地环境
postSlug: ""
featured: false
tags:
  - paopao
  - mysql
  - linux
  - redis
description: "PaoPao-ce 配置本地环境"
---

# PaoPao-ce配置本地环境

1、Go语言安装包下载，请下载对应电脑系统的安装：[https://studygolang.com/dl](https://studygolang.com/dl)

2、Go语言运行环境配置

```
Go版本 1.13 及以上（推荐）

对于macOS 或 Linux系统

打开你的终端并执行

$ export GO111MODULE=on
$ export GOPROXY=https://goproxy.cn

或者下面这种也可以
$ echo "export GO111MODULE=on" >> ~/.profile
$ echo "export GOPROXY=https://goproxy.cn" >> ~/.profile
$ source ~/.profile
完成。

对于Windows系统

打开你的 PowerShell 并执行

C:\> $env:GO111MODULE = "on"
C:\> $env:GOPROXY = "https://goproxy.cn"

执行完输入命令
$ go version
go version go1.19.8 darwin/amd64 //会输出类似安装版本信息

```

3，下载项目代码

项目地址：[https://github.com/rocboss/paopao-ce](https://github.com/rocboss/paopao-ce)

![](https://p.ipic.vip/iw5dtg.png?ynotemdtimestamp=1696309457049)

4，安装Zinc （项目依赖服务）

下载地址：[https://github.com/zincsearch/zincsearch/tags](https://github.com/zincsearch/zincsearch/tags)

![](https://p.ipic.vip/gc1sfz.png?ynotemdtimestamp=1696309457049)

```
下载解压后会得到一个zincsearch文件

对于macOS 或 Linux系统

打开你的终端并执行
$ mkdir data
$ ZINC_FIRST_ADMIN_USER=admin ZINC_FIRST_ADMIN_PASSWORD=Complexpass#123 ./zincsearch

对于Windows系统

打开你的 PowerShell 进入文件对应目录并执行
C:\> set ZINC_FIRST_ADMIN_USER=admin
C:\> set ZINC_FIRST_ADMIN_PASSWORD=Complexpass#123
C:\> mkdir data
C:\> zincsearch.exe

执行完成后可以在浏览器打开即是Zinc的Web控制台 
http://localhost:4080

```

5，安装Redis（项目依赖服务）

安装地址：[https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/](https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/)

```
便捷安装方式

对于Mac系统，控制台执行命令
$ brew install redis

对于Windows系统, 请按如下教程安装
https://blog.csdn.net/qq_40220309/article/details/125185615


```

6，安装Mysql （项目依赖服务）

```
便捷安装方式

对于Mac系统，控制台执行命令
$ brew install mysql

对于Windows系统, 请按如下教程安装
https://blog.csdn.net/lixuanhong/article/details/127393043
Mysql一般默认安装账号名 root 密码为空

导入项目数据库：
1. 在数据库中创建数据库paopao
2. 把项目scripts/paopao-mysql.sql导入数据库paopao

```

7，运行后端项目

```
控制台执行命令
$ cp config.yaml.sample config.yaml

# 生成本地配置文件后，需要修改config.yaml里的对应配置，请找到并修改
	LoggerZinc: # 使用Zinc写日志
  Host: localhost:5080
  Index: paopao-log
  User: root@example.com
  Password: Complexpass#123
  Secure: False
  
  Zinc: # Zinc搜索配置
  Host: localhost:5080
  Index: paopao-data
  User: root@example.com
  Password: Complexpass#123
  Secure: False
  
  MySQL: # MySQL数据库
  Username: root
  Password: 
  Host: localhost:3306
  DBName: paopao
  Charset: utf8mb4
  ParseTime: True
  MaxIdleConns: 10
  MaxOpenConns: 30
  
  Redis:
  InitAddress:
  - localhost:6379

# 修改完配置后，在项目根目录下执行命令
$ mkdir -p custom/data/paopao-ce/oss
$ chmod -R 777 custom/data/paopao-ce/oss
$ go run .

等待几分钟出现...
  starting run service...

  WebService@0.1.0 [start] - listen on http://0.0.0.0:8008


```

8，运行前端项目

```
本机首先要安装node环境，建议版本16+

控制台执行命令
$ cd web
$ cp .env .env.local

# 生成本地配置文件后，需要修改.env.local里的请求地址为
	VITE_HOST="http://localhost:8008"

# 修改完配置后，在项目根目录下执行命令
$ yarn dev

等待几分钟出现...
  VITE v4.3.8  ready in 800 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: http://192.168.101.3:5173/
  
  

```

9，浏览器访问

本地地址：[http://localhost:5173/](http://localhost:5173/)

![](https://p.ipic.vip/0wesoz.png?ynotemdtimestamp=1696309457049)