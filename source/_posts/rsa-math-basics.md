---
layout: post
title: 非对称加密算法 RSA（一）
subtitle: RSA 的数学基础
author: Hayden Wang
header-img: lock.jpg
cdn: header-on
tags: [Cryptography]
date: 2018-02-28 01:19:04
---

## 1. 互质关系

两正整数除了 1 之外没有公因子。

1. 两任意质数互质
2. 指数与非其倍数的数互质
3. 较大数为质数，两数互质
4. 1 和任意自然数互质
5. p 是大于 1 的整数，p 和 p-1 互质
6. p 时大于 1 的奇数，p 和 p-2 互质

## 2. 欧拉函数

$\varphi(n)$：小于等于 $n$ 的正整数中，与 $n$ 互质的数的个数。

1. $n$ 为 $1$ 时，$\varphi(1) = 1$
2. $n$ 为质数，$\varphi(p) = p-1$
3. $n=p^k$ ($p$ 为质数，$k$ 为大于等于 $1$ 的整数)，$\varphi(p^k) = p^k-p^{k-1} = p^k(1- {\frac 1 p})$
4. $n=p_1 \cdot p_2$ ($p_1$，$p_2$ 互质)，$\varphi(p_1 \cdot p_2) = \varphi(p_1) \cdot \varphi(p_2)$
5. 任意大于 $1$ 的整数，$n=p_1^{k_1} \cdot p_2^{k_2} \ldots p_r^{k_r}$ (一系列质数的积)，

{% math %}
\begin{aligned}
\varphi(n) & = \varphi(p_1^{k_1} \cdot p_2^{k_2} \ldots p_r^{k_r}) \\
           & = \varphi(p_1^{k_1}) \cdot \varphi(p_2^{k_2}) \ldots \varphi(p_r^{k_r}) \\
           & = p_1^{k_1}(1- {\frac 1 p_1}) \cdot p_2^{k_2}(1- {\frac 1 p_2}) \ldots p_r^{k_r}(1- {\frac 1 p_r}) \\
           & = n \cdot (1- {\frac 1 p_1}) \cdot (1- {\frac 1 p_2}) \ldots (1- {\frac 1 p_r})
\end{aligned}
{% endmath %}

## 3. 欧拉定理

正整数 $a$, $n$ 互质，则 $a^{\varphi(n)} \equiv 1 \pmod n$
例如：$7$ 和 $10$ 互质，则 $7^{\varphi(10)} \equiv 1 \pmod {10}$，$7^{4} \equiv 1 \pmod {10}$，$7^{4k} \equiv 1 \pmod {10}$

## 4. 费马小定理

费马小定理是欧拉定理的特殊情况：
$p$ 为质数，则 $a^{p-1} \equiv 1 \pmod n$

## 5. 模反元素（乘法逆元）

正整数 $a$, $n$ 互质，$a$ 一定有关于 $n$ 的模反元素。
- $a \cdot b \equiv 1 \pmod n $，且 
- $a^{\varphi(n)} = a \cdot a^{\varphi(n)-1} \equiv 1 \pmod n$

$a$ 的模反元素为 $a^{\varphi(n)-1}$


## 附：Python 实现的简单 RSA
```python
# Euclidean algorithm.
def gcd(a, b):
    while a != 0:
        a, b = b % a, a
    return b


# Calculate the module inverse using extended Euclidean algorithm.
def mod_inverse(a, m):
    if gcd(a, m) != 1:
        print("a and m are not co-prime!")
        quit()
    u1, u2, u3 = 1, 0, a
    v1, v2, v3 = 0, 1, m
    while v3 != 0:
        q = u3 // v3
        v1, v2, v3, u1, u2, u3 = \
            (u1 - q * v1), (u2 - q * v2), (u3 - q * v3), v1, v2, v3
    return u1 % m

# Receive two primes and generate RSA key pair.
def rsa(p, q):
    n = p * q
    phi_n = (p - 1) * (q - 1)
    e = 2
    while e < phi_n:
        if gcd(e, phi_n) == 1:
            break
        e += 1
    d = mod_inverse(e, phi_n)
    return n, phi_n, e, d

# The entrance of program.
if __name__ == "__main__":
    p = 196065871
    q = 102305491
    n, phi_n, e, d = rsa(p, q)

    print("n: ", n)
    print("phi_n: ", phi_n)
    print("e: ", e)
    print("d: ", d)
```

## 附：扩展欧几里得算法

[扩展欧几里得算法](https://zh.wikipedia.org/wiki/%E6%89%A9%E5%B1%95%E6%AC%A7%E5%87%A0%E9%87%8C%E5%BE%97%E7%AE%97%E6%B3%95)