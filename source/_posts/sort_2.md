---
layout: post
title: "排序 (二)：基于选择的排序算法"
subtitle: "简单选择排序 & 堆排序"
date: 2018-02-07 07:00
author: "Hayden Wang"
header-img: "/images/algorithms.jpg"
cdn: header-off
tags:
	- Algorithm
	- Sort
---


## 1. 简单选择排序

```python
def selection_sort(a, n):
    for i in range(0, n):
        min = i
        for j in range(i, n):
            if a[j] < a[min]:
                min = j
        swap(a[min], a[i])
```

## 2. 堆排序

```python
def heap_sort(a, n):
    for i in range(2, n+1):
        shift_up(a, i)
    for i in range(n, 1, -1):
        swap(a[1], a[i])
        shift_down(a, i-1)

def shift_up(a, n):
    i = n
    while i > 1:
        p = i / 2
        if a[p] >= a[i]:
            break
        swap(a[p], a[i])
        i = p
        
def shift_down(a, n):
    i = 1
    while i*2 <= n:
        c = i*2
        if c+1 <= n and a[c+1] > a[c]:
            c += 1
        if a[c] <= a[i]:
            break
        swap(a[c], a[i])
        i = c
```
