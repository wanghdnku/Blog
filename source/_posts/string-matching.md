---
layout: post
title: 字符串匹配算法总结
subtitle: String Matching Algorithms
author: Hayden Wang
header-img: information.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-03-05 15:23:08
---

下述算法假设在长度为 n 的母串中匹配长度为 m 的子串。

## Brute-Force 算法

顺序遍历母串，将每个字符作为匹配的起始字符，判断是否匹配子串。时间复杂度为 $O(mn)$。



## Rabin-Karp 算法

将每一个匹配子串映射为一个哈希值。例如，将子串看做一个多进制数，比较它的值与母串中相同长度子串的哈希值，如果相同，再细致地按字符确认字符串是否确实相同。顺序计算母串哈希值的过程中，使用增量计算的方法：扣除最高位的哈希值，增加最低位的哈希值。因此能在平均情况下做到 $O(m+n)$。

示例：为描述简单，假设字符串只含有十进制数字，母串为"1234"，待匹配子串 为"23"，定义 hash 函数把字符串转成整数值。
- 首先计算子串 hash：值为 23。
- 然后计算母串前两个字符的 hash：值为 12，与子串不符合，需要后移。此时我们扣除最高位"1"的 hash，增加新的最低位"3"的 hash，得到新的 hash 值 23，与子串相符。
- 通过按字符比较发现的确匹配成功，可以返回母串匹配上的下标。

```cpp
int RabinKarp(string s[1..n], string pattern[1..m])
    hpattern = hash(pattern[1..m]);
    hs = hash(s[1..m]);
    for i from 1 to n-m+1
        if hs = hpattern
            if s[i..i+m-1] = pattern[1..m]
                return i
        hs = hash(s[i+1..i+m])
    return not found
```


## Knuth-Morris-Pratt 算法


## Boyer-Moore 算法


## References
- Wikipedia: https://en.wikipedia.org/wiki/String_searching_algorithm