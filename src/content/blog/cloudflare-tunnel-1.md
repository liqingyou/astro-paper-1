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
7. 转换为vbs启动
```shell
set shell=wscript.createObject("wscript.shell")  run=shell.Run("D:\test.bat", 0)
```
<p>
而且vbs不区分大小写，跟js一样是弱语言 要注意！如果你路径是有中文的，必须要使用ANSI编码，不这么做会执行时乱码！会一直找不到文件
</p>

8. bat转exe
<p>
Bat to Exe Converter这是bat转exe工具
连接：https://download.csdn.net/download/qq_28362747/13217664
操作很简单，选择隐形就是不显示控制台,
而且vbs也可以转为exe的
</p>

9. 参考地址：
1.https://blog.zapic.moe/archives/tutorial-176.html
2.https://blog.csdn.net/qq_28362747/article/details/110472877 [全网最详细，bat不弹黑框/不显示控制台方法]
