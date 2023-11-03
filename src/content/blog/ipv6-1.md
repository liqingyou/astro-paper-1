---
author: liqingyou
pubDatetime: 2023-10-18T09:12:54.547Z
title: 基于ipv6实现几乎零成本的内网穿透方案，小白的踩坑历程与经验分享
postSlug: ""
featured: true
tags:
  - ipv6
description: 基于ipv6实现几乎零成本的内网穿透方案，小白的踩坑历程与经验分享. 最近想远程访问家里nas的想法老在脑海中浮现，原因大概是本人二开了一个管理系统，并在上面跑了些定时任务做自动化，就有了远程访问系统的需求。同时又想到，如果真能实现内网穿透，那么家里的nas真实的成为一个服务器，且配置不低，带宽也凑活，最重要的是与云服务器相比，可以省下一大笔钱。
---

### ipv6测试地址
[https://test-ipv6.com/](https://test-ipv6.com/)

## 前言

最近想远程访问家里nas的想法老在脑海中浮现，原因大概是本人二开了一个管理系统，并在上面跑了些定时任务做自动化，就有了远程访问系统的需求。同时又想到，如果真能实现内网穿透，那么家里的nas真实的成为一个服务器，且配置不低，带宽也凑活，最重要的是与云服务器相比，可以省下一大笔钱。

那说干就干，接着就分析下内网穿透的几个思路：

- `frp`形式的，首先需要一台具有公网ip的服务器，并在服务器上跑内网穿透的服务端，自家nas上跑客户端；或者可以购买内网穿透的服务，但可能有安全隐患。我寻思这种方式都需要有公网服务器了，总之就是**费钱**，那本人省钱的想法就泡汤了，故只能pass。
- 宽带的`公网ip加上ddns`，`ipv4`地址现在是不指望了，就算是电信宽带也很难申请到，而且今天我登录光猫上看了`wan`口的ip地址，是100打头的，好家伙果然是内网地址。但俺发现`ipv6`地址目前已基本普及，所以打算试试`ipv6与ddns`结合来进行内网穿透

## [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#%E5%8F%AF%E8%A1%8C%E6%80%A7%E5%88%86%E6%9E%90 "可行性分析")可行性分析

如果我们要访问的是`ipv6`的地址，前提是目前的网络必须是支持`ipv6`的，所以这个可行性俺一定要先测清楚，要保证手机和家里的宽带都得支持。

我简单测试了下，无论是联通还是电信的手机都已经默认支持`ipv6`了，然后家里的路由器在开启`ipv6`后也支持了。

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618193210.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618193210.png)

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618193334.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618193334.png)

可以通过以下两个地址测试：

- [https://test-ipv6.com/](https://test-ipv6.com/)
- [https://ipv6.ddnspod.com/](https://ipv6.ddnspod.com/)

其中第二个如果是`ipv6`地址访问会返回你的`ipv6`地址

## [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#%E5%85%89%E7%8C%AB%E6%94%B9%E6%A1%A5%E6%8E%A5%EF%BC%8C%E9%80%9A%E8%BF%87%E8%B7%AF%E7%94%B1%E6%8B%A8%E5%8F%B7 "光猫改桥接，通过路由拨号")光猫改桥接，通过路由拨号

现在的宽带默认是通过光猫进行拨号的，这就有几个问题，首先是光猫的性能较差，通过光猫拨号可能会影响速度，但最重要的还是光猫会设置防火墙，可能莫名奇妙的拦截了`ipv6`的进站请求，奇奇怪怪的，所以首先一定要把光猫改桥接，拨号交给路由器。

### [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#1%E4%BF%AE%E6%94%B9%E5%85%89%E7%8C%AB%EF%BC%9A%E5%BC%80%E5%90%AF%E6%A1%A5%E6%8E%A5%E5%92%8C-IPv6-%E8%AE%BE%E7%BD%AE "1修改光猫：开启桥接和 IPv6 设置")1修改光猫：开启桥接和 IPv6 设置

不同运营商的配置方式不同，以下是俺的光猫

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/C74860CA098DB9747A5D4A06B6A17333.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/C74860CA098DB9747A5D4A06B6A17333.png)

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/E325A73F5F59C92B7C89269129AC4A3C.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/E325A73F5F59C92B7C89269129AC4A3C.png)

进入光猫的后台管理系统，使用超级管理员用户登录：

> 4大运营商[光猫](https://so.csdn.net/so/search?q=%E5%85%89%E7%8C%AB&spm=1001.2101.3001.7020)默认初始超级密码：
> 
> 1、电信超级密码 telecomadmin nE7jA%5m
> 
> 2、移动超级密码 CMCCAdmin aDm8H%MdA
> 
> 3、联通超级密码 CUAdmin CUAdmin
> 
> 4、广电超级密码 admin aDm8H%MdA

**连接名称** 选择 `3_INTERNET_R_VID_41`，**连接模式** 选择 `桥接`，**IP模式**为 `IPv4&IPv6`。点击 `保存/应用`，大约等待 10s 会生效。

[![3_INTERNET_R_VID_41](https://static.ipw.cn/images/doc/ipv6/user/media/enable_ipv6/16355621400735.jpg)](https://static.ipw.cn/images/doc/ipv6/user/media/enable_ipv6/16355621400735.jpg)

在状态菜单中，可以看到 `IPv4` 和 `IPv6` 已经开启成功。

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618194441.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618194441.png)

### [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#2%E8%AE%BE%E7%BD%AE%E8%B7%AF%E7%94%B1%E5%99%A8PPPoE%E6%8B%A8%E5%8F%B7%E5%92%8C%E5%BC%80%E5%90%AF-IPv6%E8%AE%BE%E7%BD%AE "2设置路由器PPPoE拨号和开启 IPv6设置")2设置路由器PPPoE拨号和开启 IPv6设置

这是本人的路由器，办理电信宽带的时候送的，相当丑..

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/BD264BBBB2A5ED6CBF2625E3B0E9599A.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/BD264BBBB2A5ED6CBF2625E3B0E9599A.png)

查看路由器背后的管理地址，登录路由器的后台

默认用户名和密码都是`admin`

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618194703.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618194703.png)

进入网络配置菜单，上网方式选择拨号上网（PPPoE），输入宽带的账号和密码，一定要勾选`IPV6`，获取方式选择自动就行了，拨号生效需要几分钟。

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618194754.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618194754.png)

拨号成功后查看状态信息，可以看到`ipv4和ipv6`地址都有了，其中`GUA`指的其实就是`ipv6`地址

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618195029.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618195029.png)

至此，光猫改桥接，路由拨号完成。

## [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#ipv6%E5%9C%B0%E5%9D%80%E6%B5%8B%E8%AF%95 "ipv6地址测试")ipv6地址测试

首先我们拿着路由器上的`ipv6`地址先试试到底是不是公网的，毕竟电信太坑了保不齐又是内网…

嗯，没问题！！

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618195217.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618195217.png)

我们再通过命令行ping下看通不通

可以看到也是没有问题的

**有问题的小伙伴请记得将路由器的防火墙关闭！！！**

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618195450.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618195450.png)

那么我们再试试通过`ipv6`地址直接访问路由器的管理台是否可行呢？果然，成功了！！！这也太惊喜了！！

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618195727.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618195727.png)

路由器没问题了就试试我的nas吧！！嘿嘿！

本人使用的是`unas`，，这个特别坑的nas系统..

可以看到也正确获取了`ipv6`的地址

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618200141.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618200141.png)

因为`ipv6`的地址是公网的，所以不能写成静态ip，推荐使用自动的。

那么这个地址的公网的吗，答案是的！

那么通过这个`ipv6`地址进行访问可行么？**可行！太惊喜了！**

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/image-20230618200423773.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/image-20230618200423773.png)

接下来就只剩下一个`ipv6`地址是动态的这个问题了，但是有解决方案，通过`ddns`的方式动态解析域名地址就行了。

## [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#ddns%E6%96%B9%E6%A1%88 "ddns方案")ddns方案

提供`ddns`方案的厂家有很多，比如阿里云，腾讯云，dnsPod等，但俺认为最靠谱的还是`cloudflare`这个老牌的厂商。

`ddns`的原理简单理解就是，通过给一个域名添加`AAAA`记录解析到一个`ipv6`地址。同时在自家的`nas`上运行定时检测`ip`变化的任务，如果域名发生变化就发送`api`请求，将域名解析到最新的`ipv6`地址。

### [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#%E8%8E%B7%E5%8F%96%E4%B8%80%E4%B8%AA%E5%9F%9F%E5%90%8D "获取一个域名")获取一个域名

购买域名的方式就不赘述了，网上太多文章有介绍，俺在很早前买了一个10年期限的数字域名，不到100，至今还没过期，嘿嘿，相当于免费了，以前不知道有啥用，没想到现在排上用场了

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618201218.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618201218.png)

### [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#cloudflare%E6%B7%BB%E5%8A%A0dns%E8%A7%A3%E6%9E%90%E8%AE%B0%E5%BD%95 "cloudflare添加dns解析记录")cloudflare添加dns解析记录

在`cloudflare`添加`dns`解析，记住不能选择`代理`

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618201434.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618201434.png)

解析需要一定时间生效，等几分钟就可以试试使用域名访问了

### [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#ddns-go "ddns-go")ddns-go

定时更新最新ip这种程序必然是有大佬造过轮子了，目前最火的应该是`ddns-go`,我们对大佬致以最真诚的敬意！！并随了个赞！！

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618201808.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618201808.png)

因为`nas`基本都支持docker了，所有俺通过docker方式安装，安装过程是傻瓜式的，ddns-go的说明也很清楚了。

安装好后就是这个界面，我们选择`cloudflare`

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618201903.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618201903.png)

token通过在`cloudflare`中创建令牌获取

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618202032.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618202032.png)

### [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#%E6%9F%A5%E7%9C%8B%E6%95%88%E6%9E%9C "查看效果")查看效果

好了所有工作都完毕了，我们可以愉快的在外网通过域名访问我们在内网中的服务了

[![](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618202207.png)](https://huanghedidi-img-repository.oss-cn-shenzhen.aliyuncs.com/20230618202207.png)

## [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#ipv6%E7%90%86%E8%AE%BA "ipv6理论")ipv6理论

ipv6比ipv4复杂很多，我觉得知其然也得知其所以然，所以一些理论的东西也得了解，但在本文就不介绍了，贴点大佬的分享吧

> 链接：红茶三杯[http://www.ccietea.com/大佬的视频](http://www.ccietea.com/%E5%A4%A7%E4%BD%AC%E7%9A%84%E8%A7%86%E9%A2%91)
> 
> 【IPv6 01】IPv6概述 [https://www.youtube.com/watch?v=scyzX3Zd5JU](https://www.youtube.com/watch?v=scyzX3Zd5JU)  
> 【IPv6 02】IPv6基础配置 [https://www.youtube.com/watch?v=Vr-UvtwA3kA](https://www.youtube.com/watch?v=Vr-UvtwA3kA)  
> 【IPv6 03】ICMPv6详解（上） [https://www.youtube.com/watch?v=MYBcNPUx9YU](https://www.youtube.com/watch?v=MYBcNPUx9YU)  
> 【IPv6 04】ICMPv6详解（中）无状态自动配置 [https://www.youtube.com/watch?v=s_yASxcjMl0](https://www.youtube.com/watch?v=s_yASxcjMl0)  
> 【IPv6 05】ICMPv6详解（下）以及DHCPv6 [https://www.youtube.com/watch?v=pw46Qu6Shsc](https://www.youtube.com/watch?v=pw46Qu6Shsc)  
> 链接：openwrt官方文档
> 
> Network Interfaces [https://oldwiki.archive.openwrt.org/zh-cn/doc/networking/network.interfaces](https://oldwiki.archive.openwrt.org/zh-cn/doc/networking/network.interfaces)  
> Switch Documentation [https://oldwiki.archive.openwrt.org/zh-cn/doc/uci/network/switch](https://oldwiki.archive.openwrt.org/zh-cn/doc/uci/network/switch)  
> 链接：他人整理文档
> 
> 闲谈IPv6-典型特征的一些技术细节  
> [https://blog.csdn.net/dog250/article/details/8169984](https://blog.csdn.net/dog250/article/details/8169984)  
> 闲谈IPv6-一起玩转IPv6地址自动配置  
> [https://blog.csdn.net/dog250/article/details/88176596](https://blog.csdn.net/dog250/article/details/88176596)

## [](https://huanglusong.github.io/2023/06/18/%E5%9F%BA%E4%BA%8Eipv6%E5%AE%9E%E7%8E%B0%E5%87%A0%E4%B9%8E%E9%9B%B6%E6%88%90%E6%9C%AC%E7%9A%84%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E6%96%B9%E6%A1%88%EF%BC%8C%E5%B0%8F%E7%99%BD%E7%9A%84%E8%B8%A9%E5%9D%91%E5%8E%86%E7%A8%8B%E4%B8%8E%E7%BB%8F%E9%AA%8C%E5%88%86%E4%BA%AB/#%E5%8F%82%E8%80%83%E6%96%87%E7%AB%A0 "参考文章")参考文章

- [4大运营商光猫默认初始超级密码](https://blog.csdn.net/dengkangming/article/details/129909376)
- [IPv6 工具箱使用文档](https://ipw.cn/doc/)
- [个人宽带如何开启IPv6网络访问](https://ipw.cn/doc/ipv6/user/enable_ipv6.html)
- [如何利用CloudFlare设置Dynamic DNS(DDNS)获取动态IP教程](https://www.veidc.com/37858.html)]([https://www.veidc.com/37858.html](https://www.veidc.com/37858.html))