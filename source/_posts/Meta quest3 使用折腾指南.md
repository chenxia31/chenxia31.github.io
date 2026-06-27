---
title: Meta quest3 使用折腾指南
tags:
  - quest3
  - AR 眼镜
  - 科学上网
  - 新奇硬件
categories:
  - 生活杂谈
abbrlink: 2d990b59
date: 2024-04-29 19:34:48
---

Quest3作为一台头戴眼镜内设备，针对现在依赖手机、电脑的工作方式，天然具有“反链接“的特点，同时在国内难以魔法上网和校园网奇葩的路由器的情况，如何顺利的享受虚拟世界的乐趣是一件非常困难的工作。因此在本文总结自己踩过的坑作为记录。

## 引言

对于一些教程用一些未见过的语言来描述 Quest3 的使用过程，实际上是对小白自信心的一次打击，此时针对拿到手，需要了解的事情做一个总结，也就是“拿到 Quest3 我可能需要做哪些工作”：

1. 机内游戏下载
   1. 目的：下载一体机内部使用的app、游戏等
   2. 方法：1️⃣ 注册官方账号，在官方商店中充值并购买 2️⃣ 破解软件下载安装
   3. 问题：1️⃣ 此时面临魔法、账号、购买问题 2️⃣ 仅支持PC、文件传输、安装复杂等问题
2. 串流
   1. 目的：通过串流来查看电脑屏幕，实现办公、多媒体观看、游戏等目的
   2. 方法：通信app上可以分为官方自带的quest link， steam VR 或者virtual desktop。通信方式分为有线串流和无线串流
   3. 问题：有线对于线材要求较高，无线对于路由器要求较高
3. 视频观看
   1. 目的：在尽可能不折腾的情况下观看视频
   2. 方法：1️⃣ 传输到机器内部本地观看 2️⃣使用局域网共享的方式来查看高码率视频
   3. 问题：无线传输过程对于路由器要求较高
4. 头戴配重
   1. 目的：由于quest3本体仅作为一个简陋的设备，无法满足使用者对于舒适度的要求
   2. 方法：可以通过头戴、配重、面罩等方面来考虑
   3. 问题：市场上东西比较多

在过程平台硬件注释：

1. window主机
2. 一根type-typec的线
3. 校园网（局域网共享困难）

购买的服务包括：

| Virtual desktop | 画质好的串流软件 | $16.99 |
| --------------- | ---------------- | ------ |
| Steam VR        | 免费串流软件     | 0      |
| Quest link      | 免费串流软件     | 0      |
| 半衰期  Alyx    | PC端的VR游戏     | ￥79   |
| 破解网站        | 一体机游戏       | 0      |
| Clash           | 不解释           | 不解释 |
| Skybox          | 一体机内部播放器 | $ 7.99 |
| Air screen      | 电脑端播放服务器 | 0      |

## 第一部分：魔法、账号、充值

《如何在window下使用clash》略过

如果使用quest link进行链接，首先需要在官方上下载对应的PC软件（免费）

[為 Meta Quest Link 安裝 Oculus 電腦版應用程式](https://www.meta.com/zh-hk/help/quest/articles/headsets-and-accessories/oculus-rift-s/install-app-for-link/)

但是在这个过程中面临 Facebook 账号浏览器认证失败的问题，这里需要修改host加速：

```jsx
20.205.243.166 github.com
157.240.11.49 graph.oculus.com
157.240.11.49 www2.oculus.com
157.240.8.49 scontent.oculuscdn.com
157.240.8.49 securecdn.oculus.com
```

同时注意，Meta的账号是分为 Facebook+Instagram+Meta；而 meta quest 对应的是 meta 账号。

之后的问题是如何讲window的代理分享给quest3，这里由于在校园网环境，未直接使用 clash 的allow for lan 来进行，而是使用 “window代理热点 + clash tunnel共享“的方式来分享代理：

1. 打开clash的tunnel功能
2. 代开window的代理热点功能，此时 WLAN为共享、WLAN2为链接校园网
3. 在 控制面板-网络与连接-适配器管理 中，点击clash tunnel共享，选择WLAN来共享代理
4. 在 quest3中链接WINDOW热点，成功完成魔法。

之后的账号和充值可以在PC端、手机端、Quest3端中完成

对于破解软件下载的通常方式，在破解助手中下载软件安装包，之后利用文件传输工具传送到quest3本地，完成安装。相关免费教程可以查看这类游戏网站

[Oculus Quest教程 – VR魔趣网](https://www.vrmoo.net/tools)

## 第二部分：串流

第一部分代理热点安装好之后，串流不成问题

## 第三部分：视频观看

1. 使用SMB协议共享window文件夹
2. 推荐购买 skybox，配合window中免费的air screen中完成文件夹共享

## 第四部分：配件购买

1. quest3 头戴
2. quest3 开放式面罩
3. 头戴充电宝
4. 冰丝海绵替换装
5. 串流线

![image-20240429193602735](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429193602735.png)
