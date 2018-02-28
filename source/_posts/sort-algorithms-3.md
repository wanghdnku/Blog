---
layout: post
title: 排序 (三)：基于交换的排序算法
subtitle: 冒泡排序 & 快速排序
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
<br>

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


### 2.5 快速排序及其优化

首先是简单的快速排序算法，与上面的实现不一样，为了简单易读，这里采用类似于 Haskell 的写法：

```python
def partition(arr):
    pi, arr = arr[0], arr[1:]
    lo = [x for x in arr if x <= pi]
    hi = [x for x in arr if x > pi]
    return lo, pi, hi

def quick_sort(arr):
    if len(arr) <= 1: 
        return arr
    lo, pi, hi = partition(arr)
    return quick_sort(lo) + [pi] + quick_sort(hi)

# 此方法当数组过大，尤其是近乎有序时会发生 "maximum recursion depth exceeded"
```

下面我们来优化快速排序：
- 优化思路1: 当元素少的时候，使用插入排序优化
- 优化思路2: 对于近乎有序的数组，分出来的子数组太偏，退化成O(n^2)，随机选择数作为pivot，杜绝有序数组的影响
- 优化思路3-1: 对于有很多重复元素的数组，再次造成极度不平衡的状态，把等于pivot的元素平均分散到左右两侧
- 优化思路3-2: 三路快速排序，等于pivot的元素单独拿出来，不参加递归

```python
def partition_opt(arr):
    # 1. 选取随机的数值作为pivot
    pi = arr[randint(0, len(arr))-1]
    # 2. 利用三路快排序
    left = [x for x in arr if x < pi]
    pivot = [x for x in arr if x == pi]
    right = [x for x in arr if x > pi]
    return left, pivot, right

def quick_sort_opt(arr):
    # 3. 规模较小时，使用插入排序
    if len(arr) <= 15:
        insertion_sort_opt(arr, len(arr))
        return arr
    left, pivot, right = partition_opt(arr)
    return quick_sort_opt(left) + pivot + quick_sort_opt(right)
```