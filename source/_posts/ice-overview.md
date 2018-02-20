---
layout: post
title: 交互式连接建立（ICE）
subtitle: 一种穿透内网的方法
author: Hayden Wang
header-img: ice.jpg
cdn: header-on
tags: [Networks]
date: 2018-02-11 02:22:20
---

From [知乎专栏](https://zhuanlan.zhihu.com/p/25087606)

交互式连接建立是一种标准穿透协议，利用Stun和Turn服务器来帮助端点建立连接。市面上已有不少介绍ICE的资料，像《WebRTC权威指南(第三版)》中的“9.2 交互式连接建立”。但看了那些后，有人还是不能理解，这里试着用一个实例来描述整个过程。ICE协议只是制定规范，没规定怎么实现细节，在细节实现上这里参考Google的WebRTC。

