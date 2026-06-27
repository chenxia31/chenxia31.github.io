---
title: 计算机网络 Vol3 ｜ 运输层协议
tags:
  - 计算机网络
  - 运输层
categories:
  - 计算机科学
abbrlink: f3860fe5
date: 2024-04-29 16:55:59
---

尝试将课本中的知识重新整理成为自己的post，虽然用自己的理解去描述可能会带来错误，但是“太对的话并没有新的价值”，本次主要描述位于计算就网络应用层和网络层之间的运输层的通信协议，以及其在传递过程中的：可靠性和拥塞控制两个基本问题。

### 0x01 背景

### 1.1 运输层概述

运输层为运行在不同主机上的应用进程之间提供逻辑通信（logic communication），从应用程序的角度来说，运行不同进程的主机之间好像直接相连一样，而不用考虑承载这些物理基础设施的细节。

运输层协议是在端系统中而不是路由器中实现的：

**在发送端，运输层将从发送应用程序进程接收到报文转换成运输层分组成为运输层报文段（segment）**，（后续网络层可能会封装成网络层分组（数据报）向目的地发送，在这个过程中网络路由器仅作用于该数据报的网络层字段），**在接收端网络层从数据报中提取出运输层字段，并将报文上交给运输层**

网络应用程序中可以有多种运输层协议，在因特网中包括UDP和TCP协议。

？运输层和网络层区别？

运输层主要负责两个host之间不同进程之间的通信；而网络层提供host之间的逻辑通信；以寄快递的方式来描述：

>  应用层报文是快递里面的东西，运输层主要负责收件人的具体信息，运输层协议主要负责各地区收集和分发快递人员；网络层主要负责收货地址，网络层协议主要负责运输车辆、方式的分配

这种解耦的过程可以为不同进程通信之间规定不同的协议，而不是每个人都安排一辆车或者一种地址编码方式来造成杂乱的情况；同时不同的运输层协议也可以搭配不同的网络层协议。

### 1.2 因特网运输层概述

UDP：用户数据报协议，这个进程提供一种不可靠、无连接的服务

TCP：传输控制协议，提供可靠的、面向连接的服务

结合IP层理解可能会更好，这里需要了解的是：因特网网络协议的为IP协议（网际协议），提供主机与主机之间的逻辑通信，服务模型是尽力而为角度，并不作任何确保，也就是称为不可靠服务，在这一章我们需要知道的是每一台主机都有一个IP地址

UDP和TCP的基本责任是将两个端系统之间的IP交付服务拓展到端系统上两个进程之间的交付服务，这个称为多路复用（transport- layer multiplexing）和多路分解（demultiplexing）。除此之外TCP还会提供可靠数据传输（reliable data transfer）和拥塞控制（congestion control）

>  举个例子：当你坐在计算机下载web页面，这个时候需要运行一个FTP会话和两个Telnet会话，这样4个网络应用进程在运行，在这个时候我们需要将主机接受到的信息分配给这4个进程中的一个，这就是运输层负责事情。应用层里面是通过套接字（socket）将数据从应用层传向运输层和向运输层进程传递数据的门户，因此在任何一个时刻主机上存在多个套接字，套接字具有唯一的标识符。

？那么主机如何将一个到达的运输层报文定向到适当的套接字？

这个就需要我们在运输层报文段中定义一些字段，在接收端运输层来解析这些字段来将报文定向到套接字，这个就是多路分解（demultiplexing）的过程；在源主机上将报文段传递到运输层这个工作称为多路复用（multiplexing）；但是当然TCP相比较UDP的解析过程会更加复杂

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-6c8c385610b9eb78a9fd7a6a732587e4_1440w.jpg)





image-20220624104913355

### 1.3 规范语言

报文段（segment）：经过网络层到达运输层后生成的分组报文

数据报（datagram）：运输层的报文到达网络层生成的分组报文

socket：应用层进程和运输层之间数据通信的方式，同一个host存在多个套接字，每个套接字具有唯一标识符

多路复用：多个数据传输到运输层

多路分解：收到下层数据转移到上层多个进程

### 0x02 基本原理

### 2.1 可靠数据传输原理（Reliable Data Transfer Protocol）

在数据传输的过程中，我们会遇到差错、损坏和丢失的现象，想要按照原来数据复原就是可靠数据传输协议需要规范的事情，这种协议可以看作是策略方面的method，因为它不仅可以适用于运输层、也可以网络层或者应用层，更重要的是内在思想，而TCP相比较UDP的一方面就是使用了可靠数据传输的东西

可以参考https://blog.csdn.net/qq_40177015/article/details/112836182

最基本的模型如下图所示，后面我们可以层层递进来看这个是如何实现的：

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-805d5b08c3b34503233bc09e571d4086_1440w.jpg)





image-20220624105512085

### 2.1.1 RDT1.0 -- 底层信道可靠的情况

那就只需要

发送端：接受上层的data、转换（make-pkt）成数据段packet、然后发送（send）

接收端：接受下层的packet、提取extract出数据data、然后移交deliver给上层

### 2.1.2 RDT2.0 -- 底层差错的情况（但不会丢失）

出现差错我们的第一反应是需要：如何检查出差错、以及如果告诉发送方错了、以及重新发送，这种称为基于自动重传请求（ARQ、automatic repeat request）

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-ac84b7325043d7f123734cd4bae10ad3_1440w.jpg)





image-20220624110157727

### 2.1.3 RDT2.1&2.2

但是RDT2.0并没有考虑ACK和NAK错误的情况，这个时候发生发送端忘记重传或者过度重传，这个时候我们需要对报文进行标记，也就是需要增加数据分组编号（sequence number）来帮助区分，这也就是RDT2.1的工作

由于同时检验ACK和NAK很麻烦，因此可以设计单纯只用ACK的确认和否定就可。

### 2.1.4 RDT3.0 -- 经过比特差错和丢包信道的可靠信息传输

针对丢包，我们需要设置定时器（？如何针对每个报文设置），在一定的时间间隔（？后面介绍这么确定）下，没有收到ACK就重新发送

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-c7499fadd40ad08d71b76e9a26b1ba7c_1440w.jpg)





image-20220624110956831

### 2.1.4 暂停思考RDT

RDT是一个功能完全正确的协议，但是它是一个停等协议，在于我每个分组报文需要等到上一个ACK到来的时候再重新发送，信道中只有一个报文必然是浪费的

因此我们可以采用流水线的方式，不停的发送报文，但是这个时候又会面临新的问题

### 2.1.5 流水线-退回N步（GBN）

由于流水线允许发送发连续发送多个分组而不需要等待确认，在这个过程中出现某个数据分组差错或者丢失的情况就非常麻烦，GBN设置是错误就退回N步重新发送，这样的缺点是通信系统可能会被许许多多的无用报文充斥

### 2.1.6 流水线-选择重传（SR）

为了解决GBN的问题，可以使用选择重传的协议，对于错误之后的序列号接收端保存

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-e6e7aae007da732b88dfac9db88b5962_1440w.jpg)





image-20220624111436365

### 2.1.7 总结

在经过上述可靠数据传输机制的描述之后，存在一些基本概念：

- 差错检验（checksum）
- 定时器（timer）
- 序号（sequence number）
- 确认（acknowledgement）
- 否定确认（NAK）
- 窗口、流水线（windows、pipeline）

### 2.2 拥塞控制原理

https://zhuanlan.zhihu.com/p/37379780

### 2.2.1 什么是拥塞

作用于网络的，避免过多的无效重传数据充斥在传输网络中

第一种情况：两台机器之间+无限缓存的路由器

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-3d1d3b4c48a25e1134c54f4222b5cb22_1440w.jpg)





image-20220624112144178

第二种情况：两台机器之间+有限缓存的路由器

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-c5543caad0790ac9a9982e115cd9b0c9_1440w.jpg)





image-20220624112159474

第三种情况：多台机器之间+有限缓存的路由器

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-aff3974a2d4d84d9970e4e5ad4cfd275_1440w.jpg)





image-20220624112217633

### 2.2.2 拥塞控制方法

第一种方法：端到端的拥塞控制，比如根据报文丢包情况来观察网络拥塞状态，进而缩小自己的窗口（TCP采用的）

第二种方法：网络辅助拥塞控制，网络中路由器等传输给端设备，高速拥堵情况

### 0x03 UDP/TCP

### 3.1 UDP传输协议

### 3.1.1 UDP报文

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-7f81073f885e5550c0eef862c70e4d86_1440w.png)





img

源端口：发送方的进程对应的socket的端口

目的端口：接收方的进程对应的socket端口

报文长度：用来检查报文是否会出错

检验和：最基本的差错的checksum检验

### 3.1.2 UDP套接字编程

```
UDPClient.py

from socket import *
serverName='hostname;
serverPort=12000

// 常见客户端的套接字
clientSocket=socket(AF_INET，SOCK_DGRAM)
// 第一个参数代表使用了ipv4；第二个参数代表该套接字是SOCK——DGRAM类型的
message=raw_input('Input your message:')

//有了信息和发送端socket
clientSocket.sendto(message.encode(),(serverName,serverPort))

//之后等待服务器的数据
modifiedMessage,serverAddreess =clientSocket.recvfrom(2048)
// 2048代表分组信息到达该客户端套接字，该分组的数据的放到变量modified message中
// 源地址放置到变量server Address，取缓存长度2048作为输入
print(modifiedMessage.decode())
clientSocket.close()
```

### 3.2 TCP传输协议

### 3.2.1 TCP报文

- 首部包括16比特源端口号和16比特目的端口号，被用于多路复用/分解来自或者送到上层应用的数据
- 32比特的序号字段（sequence number field）和32比特的确认号字段（acknowledgment number field）用于实现可靠数据传输服务，随机的初始序列号还是很有意思的；讨论见后
- 4比特的首部字段长度（header length field），指示以32比特的字为单位的TCP首部长度，由于TCP选项字段的原因，TCP首部的长度是可变的，一般为空则说明典型长度为20字节
- 8比特的标志字段（flag field）
- ACK，指示确认字段的值是有效的
- RST、SYN、FIN，用于连接和拆除
- CWR、ECE，拥塞通知里面使用
- PSH，有值时，应当指示接收方立即将数据交给上层
- URG，指示报文段中存在的被发送端的上层设置为紧急的数据，最后一个字节有紧急数据指针字段给出
- 16比特的接受窗口（receive windows field），用于流量控制，用于指示接收方愿意接受的字节数量
- 校验和字段（checksum field）
- 16比特的紧急数据指针（urgent data pointer field）

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-ddad00336a9331c8a1ca2ae5d5b8f1f8_1440w.jpg)





image-20220624134417983

### 3.2.2 TCP整个过程

TCP连接是在复杂的物理网络中建立两台主机（host）之间的点对点（point to point）之间的连接；两台主机之间连接的简约过程是通过三次握手（three-way handshake）进行的：

客户端发送一个特殊的TCP报文段，服务器使用另一个特殊的TCP报文段来响应，之后客户端利用第三个特殊报文段作为响应，第三个可以包含有效负荷（也就是应用层数据），整个过程称为三次握手；之后包括四次挥手的过程；详细如下：

- 第一步，client首先向服务器TCP发送一个特殊的TCP报文段，SYN报文段设计为1，同时client随机选择一个初始序号，包装在一个IP数据报发送给server
- 第二步，这个特殊的TCP报文段到达server后，该TCP连接分配缓存和变量，并向client发送允许连接的报文段，这个允许的报文段包括三个重要的信息：SYN比特为1、确认号设置client-isn+1、设置服务器的server-isn字段，这个被称为SYNACK segment
- client在受到回复之后，为该连接分配缓存和变量，client向server发送另外一个报文段，对于服务器的允许连接的报文段进行确认，server-isn+1设置到TCP报文段首部确认字节，这里可以报文段中携带数据

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-aa03cc5dc97404641d622ad761fa23f1_1440w.jpg)





image-20220624134710294

拆除：

通过FIN字段来设置关闭报文段，client发送包含FIN报文，server发送ACK；之后server发送FIN，client回复ACK，如果没有关闭设置超时重传机制

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-4109489368e428396027ae7f506af6f2_1440w.jpg)





image-20220624134748694

之后应用层通过套接字（socket）来传递数据流，这些数据被引导到连接到发送缓存（send buffer）中，之后TCP会时不时地从发送缓存中取出来一块数据，并将数据传递到网络层，这里取出多少数据受限于最大报文段长度（maximum segment size，MSS），这个通过由最初确定的最大链路层帧长度（MTU）确定

### 3.2.3 TCP的可靠数据传输

定时器的重要设计是需要了解多长时间才是超时？因此需要对两个host之间的传送时间进行确认，这个需要考虑的这个超时时间间隔重传的设置，它必须要大于该链接的往返时间（RTT），以及是否需要为所有未确认的报文段各设置一个定时器

TCP连接为每一个时间段做出一次SampleRTT，结合历史估计的Estimate RTT来做移动平均（EWMA，exponential weighed moving average）；同时定义偏差DevRTT

EstimateRTT=0.875EstimateRTT+0.125SampleRTT

DevRTT=0.75DevRTT+0.25（SampleRTT- EstimateRTT）

设置时间间隔为

Timeout Interval=EstimateRTT+4DevRTT

在可靠性传输章节我们可以看到对于丢包最重要的是定时器的设置、对于发送方最重要的是定义如何重传数据，因此在TCP中采用的方法如图：也就是采用一个定时器的超时重传机制。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-aa33dee36152450e71aa1f5e551692b5_1440w.jpg)





image-20220624135014852

1. 超时间隔加倍
2. 定时器时限过期之后超时间间隔的长度，在这种修改中，没当超时事件发生后，TCP重传具有最小序号的还未被确认的报文段，只是每次TCP重传都会将以下一次超时间隔设置为前面的2倍；这种修改提供了一种形式受限的拥塞控制，因为时延增加并且重传会导致拥塞更严重
3. 快速重传
4. 上述的超时重传的问题在于超时周期可能相对较长，当一个报文段丢失时，这种长超时周期使得发送方延迟重传丢失的分组，从而增加了端到端的时延，这里可以采用冗余ACK（duplicate ACK）来帮助检测丢包
5. GBN or SR
6. TCP更多的事选择确认（selective acknowledgement），属于两者之间balance

### 3.2.4 TCP的拥塞控制

还没看懂～～

https://zhuanlan.zhihu.com/p/37379780

这位博主总结为

1. 慢开始、拥塞避免
2. 快重传、快恢复

https://zhangbinalan.gitbooks.io/protocol/content/tcpde_yong_sai_kong_zhi.html

慢开始( slow-start )、拥塞避免( congestion avoidance )、快重传( fast retransmit )和快恢复( fast recovery )。

### 3.3 两者对比

既然TCP比UDP多出来这么多功能？两者之间的优劣在哪里呢，UDP提供了速度快、报文传递多的特点，而TCP虽然点对点安全传递但是计算开销大，适合用于安全性较强的业务。

- 关于发送什么数据以及何时发送的应用层控制更为精细；采用UDP主需要进程将数据传送给UDP即可，但是TCP具有拥塞控制机制；在一些应用中更加强调实时性，而不必要数据的完整性
- 无须连接建立，对于网页HTTP连接中可靠性是重要的，同时实时性也是重要的，所以谷歌的QUIC协议是在UDP协议的基础上在应用层协议中实现了可靠性
- 无连接状态，TCP需要保持连接状态，这个包括接受和发送缓存、拥塞控制i参数以及序号与确认好的参数，而UDP不需要追踪这些参数，因此在某些特定应用使用UDP可以支持更多的并发用户
- 分组首部开销小，TCP报文段20字节的首部开销、UDP仅有8字节

同时在UDP不可靠的基础上，可以在应用层上增加可靠传输协议来实现可靠UDP（比如Google的QUIC协议）

### 0x04 回顾、收获和展望

将本科信息传输原理、通信网络基础没有听懂的数据报整明白了出处；并且在描述一件事情的过程最好先简化一个基本模型、然后逐步添加。同时理清楚其中的原理、策略和具体操作之间的区别和联系
