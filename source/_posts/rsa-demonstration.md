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
要证 $ c^d \equiv m \pmod d $
已知 $ m^e \equiv c \pmod n \rightarrow c = m^e - kn$ 
即证 $ (m^e - kn)^d \equiv m \pmod n $
即证 $ m^{ed} \equiv m \pmod n $
又 $ ed \equiv 1 \pmod {\varphi (n)} $，即 $ ed = h \varphi(n) + 1 $
即证 $m^{h\varphi(n)+1} \equiv m \pmod n$

(1) 若 $m$、$n$ 互质，$m^{\varphi(n)} \equiv 1 \pmod n$（欧拉定理）
故 $m^{h \varphi(n)+1}= (m^{\varphi(n)})^h \cdot m$，得证。

(2) 若 $m$、$n$ 不互质，必有 $m = kp$（$k$、$p$ 必互质）或 $n = kq$
有 $(kp)^{q-1} \equiv 1 \pmod q$（欧拉定理）
$\rightarrow [(kp)^{q-1}]^{(p-1)h} \cdot kp \equiv kp \pmod q$
$\rightarrow (kp)^{ed} \equiv kp \pmod q$
$\rightarrow (kp)^{ed} = tq + kp$，$t$ 能被 $p$ 整除，$t = t'p$
$\rightarrow (kp)^{ed} = t'pq + kp$，$\because m = kp, n = pq$
$\rightarrow m^{ed} \equiv m \pmod n$，得证。
 
## RSA 签名

公钥 $(n, e)$，私钥$(n, d)$
生成签名：$s = m^d \bmod n$
验证签名：$m' = s^e \bmod n, m' = m$

## 盲签名攻击

1. 生成随机数 $r$ 使和 $n$ 互素
2. 生成盲信息：$m_b = m \cdot r^e \bmod n$
3. 请求签名：$s_b = (m_b)^d \bmod n$
4. 移除 $r$：$s \equiv s_b \cdot r^{-1} \bmod n$

证明：
$s \equiv s_b \cdot r^{-1} \equiv (m_b)^d \cdot r^{-1} \equiv m^d \cdot r^{ed} \cdot r^{-1}$
$\equiv m^d \cdot r \cdot r^{-1} \equiv m^d \pmod m$