---
author: liqingyou
pubDatetime: 2023-09-27T14:25:54.547Z
title: cloudflare tunnel 内网穿透
postSlug: ""
featured: false
tags:
  - cloudflare
description: "cloudflare tunnel 内网穿透"
---

1. 下载程序<br>
https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/downloads/

2. 登录账号<br>
```shell
cloudflared tunnel login
```
3. 创建隧道<br>
```shell
cloudflared tunnel create Website
```
4. 启动隧道
```shell
cloudflared tunnel run --url <Source> <NAME or UUID>
cloudflared tunnel run --url http://localhost:2283 photo_home
```
5. 绑定域名
```shell
cloudflared tunnel route dns <UUID or NAME> <DOMAIN>
cloudflared tunnel route dns photo_home ddoo.com
```
6. 注册服务
```shell
sc create StartFlareTunnel binPath= "D:\service\Docker\projects\immich-app\start_tunnel.bat" DisplayName= "Start Flare Tunnel"
```