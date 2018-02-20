---
layout: post
title: 关于位运算
subtitle: 如何高效地解决常见问题
author: Hayden Wang
header-img: logic_gate.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-20 16:01:18
---

位运算，即按二进制进行的运算。

## 1. 位运算符

C/C++中，基本的位运算操作符（优先级从高到低）：

操作符 | 功能 | 用法
---|---|---
`~` | 取反 | `~a`
`<<` | 左移右侧补 0，相当于乘法 | `a<<1`
`>>` | 右移左侧补符号位，相当于除法 | `a>>1`
`>>>` | 右移左侧补 0 | `a>>>1`
`&` | 按位与 | `a&b`
`^` | 按位异或 | `a^b`
`⎪` | 按位或 | `a⎪b`

> 注意：不同的语言或者机器，对有符号数的移位操作表现可能不一致。

## 2. 基本的位操作

### 获取位
`&(0x1 << pos)`

### 设置位
`|(0x1 << pos)`

### 清除位
`&(~(0x1 << pos))`

### 判断位相同
`a&(0x1<<pos) ^ b&(0x1<<pos)`

### 获取最低的一位1
`n&(-n)`

### 清除最低的一位1
`n = n&(n-1)`

### 取负
`-n = ~(n-1) = ~n+1`

## 3. 位运算的应用

### 不用额外变量，交换两个整数的值
常规方法：
```python
def seap(a, b):
    a = a + b
    b = a - b
    a = a - b
```

使用位运算：
```python
def swap(a, b):
    a = a^b
    b = a^b
    a = a^b
    return a, b
```

### 取两数平均值
```python
def get_avg(a, b):
    return (a&b) + ((a^b)>>1)
```

说明：a&b 为两数相同部分，a^b 为两数不同部分。

### 不用`+-`号，计算两个整数之和
```python
def get_sum(a, b):
    while b:
        a, b = a^b + (a&b)<<1
    return a
```

### 求整型数的二进制表示中1的个数

方法1: 依次将最低位1抹去
```python
def number_of_one(n):
    count = 0
    while(n):
        count += 1
        n = n&(n-1)
```

方法2: 左移并逐次检查最低位
```python
def number_of_one(n):
    count = 0
    while(n):
        count += n & 0x1
        n >> 1
```

### 不用比较运算符，返回两个32位数中较大那个
可根据 a-b 的符号决定。

```python
def get_max(self, a, b):
    return b if 1&((a-b)>>31) else a
```
问题：当 a-b 溢出时，可能产生问题

### 找出数组中唯一出现奇数次的数

思路：
1. n 与 0 异或为 n
2. n 与 n 异或为 0

```python
def find_odd(arr):
    return reduce(lambda x, y: x^y, arr)
```

### 找出数组中唯二出现奇数次的数

经过上题的方法，将得出 a^b，由此可知如何区分a和b

```python
def findOdds(self, arr, n):
    result = reduce(lambda x, y: x ^ y, arr, 0)
    k = 1
    while k & result == 0:
        k <<= 1
    newArr = filter(lambda x: x & k != 0, arr)
    a = reduce(lambda x, y: x ^ y, newArr, 0)
    b = a ^ result
    return [min(a, b), max(a, b)]
```

### 只用逻辑运算符实现加法运算

### 只用逻辑运算符实现乘法运算

### 只用逻辑运算符实现正整数除法运算

### 布隆过滤器

[布隆过滤器](https://www.nowcoder.com/study/vod/1/8/1)

应用场景：容忍一定程度的失误率，但对空间的要求比较严格
- 网页黑名单过滤系统
- 垃圾邮件过滤系统
- 爬虫的网址判断重复系统
