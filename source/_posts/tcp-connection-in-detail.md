---
layout: post
title: TCP 的连接与断开
subtitle: 三次握手与四次挥手
author: Hayden Wang
header-img: handshake.jpg
cdn: header-on
tags: [Networks]
date: 2018-02-28 00:33:46
---

TCP 建立连接要三次握手，断开连接要四次挥手

网络协议的设计思想：
- 网络时不可靠的，任何包都可能丢
- 遇见问题，解决问题，不断迭代
- 能用就好

任何包都要ACK，除了最后一个ACK

![TCP Connection Overview](/images/tcp_connection/tcp_connection.jpg)

## 建立连接 - 三次握手

![TCP Connection Establish](/images/tcp_connection/tcp_establish.png)

1. 【第一次】客户端向服务端发送 SYN(Synchronize) 标记设为 1 的TCP包。Seq(Sequence Number)为由操作系统生成的随机数。然后，客户端进入 SYN_SEND 状态，等待服务器的确认。
2. 【第二次】服务器收到客户端的 SYN 包，会发一个对 SYN 包的确认包(SYN/ACK)回去。ACK(Acknowledgment Number) 为 x+1(Sequence Number+1)，服务器端把 SYN+ACK 报文段发送给客户端，此时服务器进入 SYN_RECV 状态。
3. 【第三次】客户端收到 SYN/ACK 包后，发一个确认包(ACK)，通知服务器端连接已建立。

## 断开连接 - 四次挥手

![TCP Connection Finish](/images/tcp_connection/tcp_finish.png)

1. 【第一次】客户端：『没有数据给你了』；
2. 【第二次】服务端：『收到，我也没有数据给你了』；
3. 【第三次】服务端：『请求关闭连接』；
4. 【第四次】客户端：『确认』；

服务端收到后关闭连接，客户端收不到回复了也关闭了连接。

> 状态说明：
- `CLOSE_WAIT`: 接收方，等待断开连接，等待准备完毕
- `TIME_WAIT`: 等待最后一个ACK，超时之后默认认为ACK已收到

