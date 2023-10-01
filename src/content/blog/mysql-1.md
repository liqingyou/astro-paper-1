---
author: liqingyou
pubDatetime: 2023-10-01T14:55:54.547Z
title: mysql 常用命令
postSlug: ""
featured: true
tags:
  - mysql
description: "mysql 登录 导入sql文件 创建/进入数据库"
---

#### 登录命令
```shell
mysql -u root -p
```

#### 导入sql文件
```shell
source /路径/到/你的文件.sql;
source /root/paopao/paopao-ce/scripts/paopao-mysql.sql
```

#### 创建数据库
```
CREATE DATABASE paopao;
```

#### 进入数据库
```
use paopao
```