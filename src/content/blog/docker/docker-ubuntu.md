---
author: liqingyou
pubDatetime: 2023-10-01T14:55:54.547Z
title: ubuntu 安装 docker 和 docker-compose
postSlug: ""
featured: true
tags:
  - mysql
description: "ubuntu 安装 docker 和 docker-compose"
---

更新系统
```
sudo apt-get update
sudo apt-get upgrade
```

安装
```
sudo apt-get install docker.io
```

启动
```
sudo service docker start
```

docker compose
```
sudo curl -SL https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```

赋予权限
```
sudo chmod +x /usr/local/bin/docker-compose
```

查看是否安装成功
```
docker-compose -v
```