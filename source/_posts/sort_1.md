---
layout: post
title: "排序 (一)：基于插入的排序算法"
subtitle: "直接插入排序 & 希尔排序"
date: 2018-02-07 06:00
author: "Hayden Wang"
header-img: "/images/algorithms.jpg"
cdn: header-off
tags:
	- Algorithm
	- Sort
---

## 1. 直接插入排序

```python
def insertion_sort(a, n):
    for i in range(1, n):
        temp = a[i]
        j = i
        while j-1 >= 0 and temp < a[j-1]:
            a[j] = a[j-1]
            j -= 1
        a[j] = temp
```

## 2. 希尔排序

```python
def insertion_sort(a, n, step):
    for i in range(1, n):
        temp = a[i]
        j = i
        while j-step >= 0 and temp < a[j-step]:
            a[j] = a[j-step]
            j -= step
        a[j] = temp

def shell_sort(a, n):
    step = n / 2
    while step >= 1:
        insertion_sort(a, n, step)
        step /= 2
```
