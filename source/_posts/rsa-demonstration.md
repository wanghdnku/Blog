---
layout: post
title: 非对称加密算法 RSA（二）
subtitle: RSA 算法原理
author: Hayden Wang
header-img: lock.jpg
cdn: header-on
tags: [Cryptography]
date: 2018-02-28 02:18:15
---


## 生成密钥对

1. 选择不同的质数 $p$、$q$
2. 计算 $n = p \times q$ ($n$ 长度为密钥长度)
3. 计算 $\varphi(n) = (p-1)(q-1)$，欧拉函数
4. 取随机整数 $e$，$1 < e < \varphi(n)$，$e$ 和 $\varphi(n)$ 互质
5. 求 $e$ 对于 $\varphi(n)$ 的模反元素 $d$，$e \cdot d \equiv 1 \pmod {\varphi(n)}$
6. $(n, e)$ 为公钥，$(n, d)$ 为私钥

> $e \cdot d \equiv 1 \pmod {\varphi(n)}$，有 $e \cdot d -1 = k \cdot \varphi(n)$，即 $e \cdot x + \varphi(n) \cdot y = 1$，利用扩展欧几里得定理求出模反元素。

## 算法可靠性

已知 $n$、$e$，求 $d$
即 $e \cdot d \equiv 1 \pmod {\varphi(n)}$，求 $\varphi(n)$
即 $\varphi(n) = (p-1) \cdot (q-1)$，求 $p$、$q$
即 $n = p \cdot q$，分解 $n$

RSA 的可靠性建立在大整数很难分解的基础上。


## 加密和解密

### 加密与解密（$m$ 必须小于 $n$）
1. 公钥加密：$m^e \equiv c \pmod n$
2. 私钥解密：$c^d \equiv m \pmod n$

### 证明（私钥解密的过程）
待续……

## RSA 签名


## 盲签名攻击