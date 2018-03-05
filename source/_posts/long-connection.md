---
layout: post
title: 谈谈长连接
subtitle: TCP 与 HTTP 的 Keep-Alive
author: Hayden Wang
header-img: global_network.png
cdn: header-on
tags: [Networks]
date: 2018-03-03 01:14:31
---

## 写在前面

### HTTP 和 TCP/IP 的关系

**HTTP的长连接和短连接本质上是TCP长连接和短连接**。HTTP属于应用层协议，在传输层使用TCP协议，在网络层使用IP协议。IP协议主要解决网络路由和寻址问题，TCP协议主要解决如何在IP层之上可靠的传递数据包，使在网络上的另一端收到发端发出的所有包，并且顺序与发出顺序一致。TCP有可靠，面向连接的特点。

### HTTP协议是无状态的

HTTP协议是无状态的，指的是协议对于事务处理没有记忆能力，服务器不知道客户端是什么状态。也就是说，打开一个服务器上的网页和你之前打开这个服务器上的网页之间没有任何联系。HTTP是一个无状态的面向连接的协议，**无状态不代表HTTP不能保持TCP连接，更不能代表HTTP使用的是UDP协议（无连接）**。


> 下文中的 Keep Alive 与长连接指同一概念，Keep Alive 是通用的称谓，但为了方便交流，有时会使用长连接

---
<br>


## TCP 长连接 (Keep Alive)

TCP 长连接是一种保持 TCP 连接的机制。当一个 TCP 连接建立之后，启用 TCP Keep Alive 的一端便会启动一个计时器，当这个计时器到达 0 之后，一个 TCP 探测包便会被发出。这个 TCP 探测包是一个纯 ACK 包，但是其 Seq 与上一个包是重复的。
打个比喻，TCP Keep Alive 是这样的：

> TCP 连接两端好比两个人，这两个人之间保持通信往来（建立 TCP 连接）。如果他俩经常通信（经常发送 TCP 数据），那这个 TCP 连接自然是建立着的。但如果两人只是偶尔通信。那么，其中一个人（或两人同时）想知道对方是否还在，就会定期发送一份邮件（Keep Alive 探测包），这个邮件没有实质内容，只是问对方是否还在，如果对方收到，就会回复说还在（对这个探测包的 ACK 回应）。

需要注意的是，keep alive 技术只是 TCP 技术中的一个可选项。因为不当的配置可能会引起诸如一个正在被使用的 TCP 连接被提前关闭这样的问题，所以默认是关闭的


---
<br>


## HTTP 长连接 (Keep Alive)

在 HTTP 1.0 时期，每个 TCP 连接只会被一个 HTTP Transaction（请求加响应）使用。之后，这个 TCP 连接便会被关闭。当网页内容越来越复杂，包含大量图片、CSS 等资源之后，这种模式效率就显得太低了。所以，在 HTTP 1.1 中，引入了 HTTP persistent connection 的概念，也称为 HTTP keep-alive（后面统一称呼为 HTTP 长连接）。

HTTP 1.0 和 1.1 在 TCP 连接使用方面的差异可见下图：
![HTTP 1.0 和 1.1](/images/tcp_connection/http_version.png)

当需要建立 HTTP 长连接时，HTTP 请求头将包含：`Connection: Keep-Alive`
如果服务端同意建立长连接，HTTP 响应头也将包含：`Connection: Keep-Alive`
当需要关闭连接时，HTTP 头中会包含：`Connection: Close`

也打个比方：

> 两个人互相通信，一个人发信，并附上一句，我们保持联系好吗。另一个人在回信中写到，好的，我们继续保持联系。此后，他们每封信里都写上相同的内容。直到有一天，友谊的小船翻了，一个人在回信里写到，我们不要再联系了。另一个人信守承诺，回道：好的，我们不联系了。从此两人天各一方。。。


---
<br>


## TCP 长连接与 HTTP 长连接的关系

如上文的解释，TCP Keep Alive 和 HTTP Keep Alive 是两个目的不同的技术，不存在谁依赖于谁的关系。TCP Keep Alive 用于探测对端是否存在，而 HTTP Keep Alive 用于协商以复用 TCP 连接。即便一个 TCP 连接未启用 Keep Alive 功能，也不妨碍 HTTP 层面开启长连接。

但如果 TCP Keep Alive 的 interval 数值设置过短，就可能导致 HTTP 无法重复利用已建立的 TCP 连接。


---
<br>

## References

- TCP长连接和短连接：https://www.jianshu.com/p/313798ae863d
- HTTP长连接那些事儿：https://www.jianshu.com/p/56881801d02c
- HTTP长连接和短连接：http://www.cnblogs.com/0201zcr/p/4694945.html

