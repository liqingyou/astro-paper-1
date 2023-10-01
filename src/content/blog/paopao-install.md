---
author: liqingyou
pubDatetime: 2023-09-28T14:55:54.547Z
title: linux泡泡安装
postSlug: ""
featured: false
tags:
  - paopao
  - mysql
  - linux
description: "泡泡安装命令"
---

#### 安装redis
```
apt install redis-server
sudo systemctl status redis-server
```

#### 安装zinc

#### 拷贝项目根目录下 config.yaml.sample 文件至 config.yaml
```
cp config.yaml.sample config.yaml
```

#### 编辑config.yaml
```
vim config.yaml
```
在 Vim 编辑器中，你可以使用键盘进行编辑。要插入文本，按下 i 键，然后开始输入。要保存文件，首先按下 Esc 键，然后输入 :w 并按下 Enter 键。要退出 Vim 编辑器，再次按下 Esc 键，然后输入 :q 并按下 Enter 键。如果你在编辑过程中已经保存了更改，可以使用 :wq 命令保存并退出。

#### 下载golang
```
wget https://golang.org/dl/go1.17.2.linux-amd64.tar.gz
```

#### 解压
```
tar -C /usr/local -xzf go1.17.2.linux-amd64.tar.gz
```

### Zinc 搜索引擎:
####
```
# 创建用于存放zinc数据的目录
mkdir -p data/zinc/data

# 使用Docker运行zinc
docker run -d --name zinc --user root -v ${PWD}/data/zinc/data:/data -p 4080:4080 -e ZINC_FIRST_ADMIN_USER=admin -e ZINC_FIRST_ADMIN_PASSWORD=admin -e DATA_PATH=/data public.ecr.aws/zinclabs/zinc:latest

# 查看zinc运行状态
docker ps
CONTAINER ID   IMAGE                                COMMAND                  CREATED        STATUS        PORTS                    NAMES
41465feea2ff   getmeili/meilisearch:v0.27.0         "tini -- /bin/sh -c …"   20 hours ago   Up 20 hours   0.0.0.0:7700->7700/tcp   paopao-ce-meili-1
7daf982ca062   public.ecr.aws/prabhat/zinc:latest   "/go/bin/zinc"           3 weeks ago    Up 6 days     0.0.0.0:4080->4080/tcp   zinc

# 使用docker compose运行
docker compose up -d zinc
# visit http://localhost:4080 打开自带的ui管理界面
```