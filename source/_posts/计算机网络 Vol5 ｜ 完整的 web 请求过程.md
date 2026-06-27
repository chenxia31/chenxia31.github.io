---
title: 计算机网络 Vol5 ｜ 完整的 web 请求过程
tags:
  - 计算机网络
  - web 请求
categories:
  - 计算机科学
abbrlink: 22420aa8
date: 2024-04-29 16:56:10
---

# 摘要

应用层协议 [计算机网络学习笔记 -- 应用层协议](https://blog.tjdata.site/2022/06/18/计算机网络-02-Chapter02 应用层协议/) 万维网 HTTP Proxy 电子游戏 SMTP POP3IMAP 局域网IP地址分配 DHCP 域名解析 DNS 网络下载 P2P 流媒体 CDN 运输层协议 [计算机网络学习笔记 -- 运输层协议](https://blog.tjdata.site/2022/06/24/计算机网络-03-Chapter03 运输层协议/) UDP TCP 网络层协议 [计算机网络学习笔记 -- 网络层协议](https://blog.tjdata.site/2022/06/28/计算机网络-04-Chapter04 网络层协议/) IP协议 - 数据层面 IP协议 - 控制层面 链路层协议 比较偏低层，不重视 以太网 物理层协议 完全底层，不重视 IEEE802.11

### 0x02 完整的使用过程

问题描述：在学校，学生是如何通过校园网链接到网络，然后下载www.baidu.com的主页面的 涉及到的协议：应用层的DHCP、DNS、HTTP；运输层的UDP；网络层的IP；链路层的以太网

### 2.1

- User启动它的PC，可以使用一根以太网电缆或者WI-FI与学校的以太网交换机连接，学校的交换机也和学校的Router连接。   学校的Router与本地的ISP（可能是中国移动也有可能是其他的什么东西，原文中的历史comcast.net)连接，因此提供DNS服务，也就是说DNS服务器是在ISP的网络中
- User为了连接网络，首先需要通过DHCP来获得自己的IP地址，因此它通过DHCP请求报文来获得自己的IP地址
- 请求报文放到目的端口67和请求端口68的UDP报文段，放到广播IP（255.255.255.255）和源IP地址（0.0.0.0）的IP数据报中     包含IP数据报防止道以太网帧中，目的MAC地址（FF.FF.FF.FF.FF.FF），源Mac地址就是User的PC的Mac地址，比如说是3c:a6:f6:05:15:2c
- Router接受到对应的Mac地址后，从以太网Frame中抽取出IP数据报，然后获得UDP报文段，由此得到了DHCP的请求报文     假设Router中处理 DHCP请求报文的方式是使用CIDR，68.85.2.0/24分配IP地址，因此分配68.85.2.101给PC、网关地址68.85.2.1和子网掩码68.85.2.0/24的DHCPACK报文和DNS的地址，放到UDP报文段、IP数据报、以太网数据帧，发送回发出请求的PC的mac地址
- ⚠️返回的时候mac地址并不是广播的，在DHCP的ACK到达PC的时候，开始得到自己的IP地址和DNS的IP地址，可以开始上网了！
- 当User键入www.google.com之后，web浏览器生成一个TCP socket开始发送HTTP request，这里需要得到www.google.com的IP地址
- 为了得到www.google.com的IP地址，需要经历DNS查询报文，这个报文需要有DNS服务器的地址（比如68.87.71.226）以及源IP地址（68.85.2.101），以及字符串（‘www.google.com')
- 为了得到学校网络的网关路由器，需要通过ARP得到网关路由器的Mac地址
- 得到网关路由器的地址之后，PC发出的Frame中的IP数据报的目的IP地址为DNS、源IP地址为PC地址、帧的目的地址是网关路由器的地址
- 网关路由器接受到Frame病抽取包含DNS查询的IP数据报，解析在重新原则路由器，再将IP数据报放置到链路层Frame中国发送     到达之后，重新返回给PC
- 得到www.google.com的IP地址之后，就可以进行交互了，需要TCP连接和HTTP协议
