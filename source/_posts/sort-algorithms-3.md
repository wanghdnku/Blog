---
layout: post
title: "排序 (三)：基于交换的排序算法"
subtitle: "冒泡排序 & 快速排序"
author: Hayden Wang
header-img: algorithms.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-07 08:00:00
---

## 1. 冒泡排序

思路：重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。

```python
def bubble_sort(a, n):
    for i in range(0, n):
        for j in range(i+1, n):
            if a[j] < a[i]:
                swap(a[j], a[i])
```

---

## 2. 快速排序

### 2.1 基础快速排序
```python
def quick_sort(a, l, r):
    if l >= r:
        return
    pivot = partition(a, l, r)
    quick_sort(a, 0, pivot-1)
    quick_sort(a, pivot+1, 0)
    
def partition(a, l, r):
    p = a[l]
    j = l
    for i in range(l+1, r):
        if a[i] < p:
            swap(a[i], a[j+1])
            j += 1
    swap(a[l], a[j])
    return j
```

### 2.2 双路快速排序
把等于pivot的元素分布于两侧，解决重复元素过多时算法退化的问题
```python
def partition(a, l, r):
    p = a[l]
    i, j = l+1, r
    while True:
        while a[i] < p:
            i += 1
        while a[j] > p:
            j -= 1
        if i > j:
            break
        swap(a[i], a[j])
        i += 1
        j -= 1
    swap(a[l], a[j])
    return j
```

### 2.3 三路快速排序
```python
def quick_sort(arr, l, r):
    if l >= r:
        return
    lt, rt = partition(a, l, r)
    quick_sort(l, lt)
    quick_sort(gt, r)
    
def partition(a, l, r):
    p = a[l]
    lt, i, gt = l+1, l, r+1
    while i < gt:
        if arr[i] < v:
            swap(a[i], a[lt+1])
            i += 1
            lt += 1
        elif arr[i] > v:
            swap(a[i], a[gt-1])
            gt -= 1
        else:
            i += 1
    swap(a[l], a[lt])
    lt -= 1
    return (lt, gt)
```

### 2.4 函数式写法（Haskell）
```haskell
qsort :: (Ord a) => [a] -> [a]
qsort [] = []
qsort (x:xs) = qsort left + [x] + qsort right
               where left = [a | a <- xs, a <= x]
                     right = [a | a <- xs, a > x]
```
