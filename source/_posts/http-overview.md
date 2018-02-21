---
layout: post
title: HTTP 协议概述
subtitle: 超文本传输协议
author: Hayden Wang
header-img: http.jpg
cdn: header-on
tags: [Networks]
date: 2018-02-21 10:39:04
---

HTTP协议是用于客户端与服务器之间的通信，当使用HTTP协议时，一端担任客户端的角色，另一端担任服务器的角色。

## 1. Client-Server 模式

客户端与服务器之间通过请求与响应的交换来达成通信：

请求报文由：**请求方法、请求URI、协议版本、（可选）请求首部字段、内容实体** 构成。
![HTTP请求报文结构](/images/http/http_request.png)

响应报文由：**协议版本、状态码、状态码原因短语、（可选）相应首部字段、实体主体** 构成。
![HTTP响应报文结构](/images/http/http_reply.png)


---
<br>

## 2. 持久连接和状态管理

**HTTP是一种无状态协议**，协议自身不对请求和相应之间的通信状态进行保存。协议对发送过的请求或者相应内容不做持久化处理。
原因：为了更快地处理大量事物，确保协议的可伸缩性。
HTTP/1.1虽然是无状态的协议，但是为了实现期望的保持状态的功能，引入了Cookie技术。

### 2.1 持久链接

HTTP协议的初始版本，每次进行一次HTTP通信就要断开一次TCP连接

**持久连接**：
持久连接特点是，只要任意一端没有明确提出断开连接，则保持TCP连接状态。
持久连接的好处在与减少了TCP重复建立和断开造成的额外开销，减轻了服务器的负载。

**管线化**：
持久连接使得多数请求以管线化(pipelining)方式发送成为可能。这样就能同时并行发送多个请求，而不需要一个一个地等待响应了。

![HTTP持久连接](/images/http/http_conn.png)

### 2.2 使用Cookie的状态管理
Cookie 会根据从服务器端发送的响应报文内的一个叫做Set-Cookie的首部子短信息，通知客户端保存Cookie。当下次客户端再往服务器发送请求时，客户端会自动在请求报文中加入Cookie值后发送出去。
服务器端发现客户端发送过来的Cookie后，会去检查究竟是哪一个客户端发来的连接请求，然后对比服务器上的记录，最后得到之前的状态信息。

![HTTP Cookie](/images/http/http_cookie.png)


---
<br>


## 3. HTTP 报文结构

HTTP报文本身是由多行数据（用CR+LF做换行符）构成的字符串文本。

![HTTP报文结构](/images/http/http_message.png)

首部内容为客户端和服务器分别处理请求和响应提供所需要的信息。

报文首部的构成：
  - 请求行：包含用于请求的方法，请求URI和HTTP版本。
  - 状态行：包含表明响应结果的状态码，原因短语和HTTP版本。
  - 首部字段：包含表示请求和响应的各种条件和属性的各类首部。分为：请求首部、响应首部、通用首部、实体首部。
  - 其他：包含RFC未定义的首部，如Cookie。

![HTTP报文实例](/images/http/http_message_example.png)

---
<br>


## 4. HTTP 首部字段

使用首部字段是为了给浏览器和服务器提供报文主体大小、所使用的语言、认证信息等内容。
HTTP首部字段由首部字段名和字段值构成，中间用冒号分隔。

### 通用首部字段 General Header Fields
请求报文和响应报文都会使用的首部。
  - Cache-Control：控制缓存行为
  - Connection：逐跳首部、连接的管理
  - Date：创建报文的日期时间

### 请求首部字段 Request Header Fields
从客户端向服务器发送请求报文时使用的首部。补充了请求的附加内容、客户端信息、响应内容相关优先级等信息。
  - Accept：用户代理可处理的媒体类型
  - Authorization：Web认证信息
  - Max-Forwards：最大传输逐跳数
  - User-Agent：HTTP客户端程序的信息

### 响应首部字段 Response Header Fields
从服务器像客户端返回响应报文时使用的首部。补充了响应的附加内容、也会要求客户端附加额外的内容信息。
  - Retry-After：对再次发起请求的时机要求
  - WWW-Authenticate：服务器对客户端的认证信息

### 实体首部字段 Entity Header Fields
针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等与实体相关的信息。
  - Allow：资源可支持的HTTP方法
  - Content-Encoding：实体主体适用的编码方式
  - Content-Language：实体主体的自然语言
  - Expires：实体主体过期的日期时间
  - Last-Modified：资源最后修改的日期时间


---
<br>


## 5. HTTP 请求方法

**GET：获取资源**
GET方法用来请求访问已被URI识别的资源，指定的资源经服务器端解析后返回相应内容。

**POST：传输实体主体**
POST方法用来传输实体的主体，虽说功能与GET相似，但是POST的主要目标并不是获取相应的实体内容。

**PUT：传输文件**
PUT方法用来传输文件，就像FTP协议的文件上传一样，要求在请求报文的主体中包含文件内容，然后保存到请求URI制定的位置。

**HEAD：获取资源头部**
HEAD方法和GET方法一样，只是不返回报文主体部分。用于确认URI的有效性以及资源更新的日期时间等。

**DELETE：删除文件**
DELETE方法用来删除文件，是与PUT方法相反的方法。DELETE方法按请求URI删除指定的资源。

**OPTIONS：询问支持的方法**
查询针对请求URI指定的资源支持的方法。

**TRACE：追踪路径**
让Web服务器将之前的请求通信环回给客户端。

**CONNECT：要求用隧道协议连接代理**
CONNECT方法要求在与代理服务器通信时建立隧道，实现用隧道协议进行的TCP通信，主要使用SSL和TLS协议把通信内容加密后经网络隧道传输。

---
<br>


## 6. HTTP 响应状态码

![HTTP状态码](/images/http/http_code.png)

  - 200 OK：表示客户端发来的请求在服务器被正常处理了。
  - 204 No Content：请求成功受理，但是返回的响应报文不包含实体部分。
  - 206 Partial Content：客户端进行了范围请求，而服务器成功执行。
  - 301 Moved Permanently：请求的资源已被分配新的URI，以后使用新URI
  - 302 Found：临时重定向。本次使用新的URI。
  - 303 See Other：所请求的资源对应着另外一个URI。
  - 304 Not Modified：服务器资源未改变，客户端可以使用未过期缓存。不包含主体部分。
  - 400 Bad Request：请求报文中存在语法错误。
  - 403 Forbidden：对请求资源的访问被服务器拒绝了。
  - 404 Not Found：未找到请求资源，或者服务器拒接原因不可描述。
  - 500 Internal Server Error：服务器执行请求时发生错误。
  - 503 Service Unavailable：服务器超负荷或者停机维护，无法处理请求。


---
<br>


## 7. HTTPS

HTTP的缺点：
  1. 使用明文通信，内容可能被**窃听**
  2. 不验证通信方的身份，可能遭遇**伪装**
  3. 无法证明报文的完整性，信息可能遭**篡改**

**HTTP + 加密 + 认证 + 完整性保护 = HTTPS**

![密码学工具箱](/images/http/http_crypt.jpg)

HTTPS并非是应用层的一种新协议，只是HTTP通信接口部分用SSL(Secure Socket Layer)和TLS(Transport Layer Security)协议代替而已。
通常HTTP直接和TCP通信，当使用SSL时，泽阳编程先和SSL通信，再由SSL和TCP通信。

![HTTPS流程](/images/http/https.png)

HTTPS的缺点：
  1. 通信慢
  2. 消耗CPU和内存
  3. 购买证书费用