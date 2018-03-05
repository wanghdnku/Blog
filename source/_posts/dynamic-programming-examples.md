---
layout: post
title: 从几个常见问题看动态规划
subtitle: Dynamic Programming Examples
author: Hayden Wang
header-img: function.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-03-05 10:06:15
---

## 背包问题


## 最长公共子串

状态转移方程

```python
if word_a[i] == word_b[j]:
    cell[i][j] = cell[i][j] + 1
else:
    cell[i][j] = 0
```


## 最长公共子序列

状态转移方程

```python
if word_a[i] == word_b[j]:
    cell[i][j] = cell[i-1][j-1] + 1
else:
    cell[i][j] = max(cell[i-1][j], cell[i][j-1])
```

## 最短编辑距离

