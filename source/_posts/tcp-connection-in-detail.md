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

## 建立连接 - 三次握手

![TCP Connection Establish](/images/tcp_connection/tcp_establish.png)


## 断开连接 - 四次挥手

![TCP Connection Finish](/images/tcp_connection/tcp_finish.png)

CLOSE_WAIT: 接收方，等待断开连接，等待准备完毕
TIME_WAIT: 等待最后一个ACK，超时之后默认认为ACK已收到
