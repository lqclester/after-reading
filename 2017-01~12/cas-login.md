---
title: cas-login
date: 2017-09-21 17:58:24
tags:
- cas
categories:
- coding
comments: false
---

> cas登录学习分享,cas在企业里面会经常用到，因为只要登录一次珍在所有其他系统都不需要再登录。

<!-- more -->

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->
- [CAS协议的规范](#cas协议的规范)
- [运行机制说明](#运行机制说明)
- [流程图](#流程图)
- [参考资料](#参考资料)

<!-- /TOC -->

# CAS协议的规范
> 注：CAS协议大体包含两个方面的内容：各种票根（ Ticket ）和暴露给 CAS 客户的 HTTP（ S ） URL 。这些UPL （ /login 、 /logout、 /validate 、 /serviceValidate 、 /proxy 、/proxyValidate 等）围绕着这些票根（ ST、 TGC 、 PGT 、PT 等）进行活动。在此期间服务和终端服务之间会进行多次HTTPS 交互。

# 运行机制说明
![cas-login](https://raw.githubusercontent.com/lqclester/lqclester.github.io/master/images/1.gif)

- step1: 无session的情况,CAS Client 首先分析 HTTP 请求中是否包含Service Ticket(上图中的 Ticket) ,如果没有,则说明该用户是没有经过认证的,于是,CAS Client 会重定向用户请求到 CAS Server
> Service Ticket
•ST 只能被尝试验证一次。无论验证是否成功,CAS 必须使该ST 无效,并且要使得今后拒绝再验证同一个ST 。
•CAS 应该能在一段合理的时间内终止一个没有验证通过的ST 。如果一个服务使用了过期的ST ,CAS 必须作出反应,返回必须是验证失败。

- step2: 如果没有,则说明该用户是没有经过认证的。于是,CAS Client会在headers location:cas-server,重定向用户请求到 CAS Server（第2步）。
- step3: 第3步是用户认证过程,当用户提供的账号及密码正确,CAS Server会产生一个随机的Service Ticket,并且重定向用户到 CAS Client。
类似
```
GET /app/?ticket=ST-xxx-cas.server.com HTTP/1.1
```
- step4: CAS Client获取Service Ticket,请求CAS Server认证Service Ticket。
- Step5：CAS Server验证正确,返回用户信息给CAS Client,还会保存TGT在cookies,set cookies,CASTGC=TGT-xxxxx-cas.server.com

> ticket-granting cookie
TGC 是由CAS 建立在SSO 会话上的一个HTTP 的cookie 。此Cookie 保持客户端的登录状态,当它是有效的时候,客户端可以把它提交给CAS 以代替主认证证书。

- step6: Cas Client登录完成,存储session,进入index

# 流程图
![cas-login](https://raw.githubusercontent.com/lqclester/lqclester.github.io/master/images/2.png)

# 参考资料
[使用 CAS 在 Tomcat 中实现单点登录](https://www.ibm.com/developerworks/cn/opensource/os-cn-cas/)
[SSO单点登录原理和流程分析](http://www.jianshu.com/p/c35344d15278)
