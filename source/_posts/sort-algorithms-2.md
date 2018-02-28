---
layout: post
title: 排序 (二)：基于选择的排序算法
subtitle: 选择排序 & 堆排序
author: Hayden Wang
header-img: algorithms.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-07 07:00:00
---

## 1. 选择排序

思路：每次在剩下的元素中找到最小的，放在最前面。

```python
def selection_sort(a, n):
    for i in range(0, n):
        min_index = i
        for j in range(i, n):
            if a[j] < a[min_index]:
                min_index = j
        swap(a[min_index], a[i])
```

---
<br>

## 2. 堆排序

### 2.1 堆的性质

- 堆中某个结点的值总是不大于其父亲节点的值（最大堆）
- 堆总是一棵完全二叉树：除了最后一层，其他层节点书必须是最大值，且集中在最左侧
- 并不意味着层数越高数值越大

堆的存储：使用数组存储二叉堆
1. 如果下标从 0 开始：
	- i的左孩子是 2i+1，右孩子是 2i+2.
	- i的父亲节点是 (i-1)/2.
2. 如果下标从 1 开始：
	- i的左孩子是 2i，右孩子是 2i+1.
	- i的父亲节点是 i/2.

### 2.2 使用 ShiftUp / ShiftDown 进行堆排序

向堆中添加元素：Shift Up
1. 添加到堆的末尾
2. 和父节点比较，如果比父节点大，则交换位置
3. 重复第2步直到比父节点小，满足最大堆定义

```python
def shift_up(a, n):
    i = n
    while i > 1:
        p = i / 2
        if a[p] >= a[i]:
            break
        swap(a[p], a[i])
        i = p
```

从堆中取出元素：Shift Down
1. 取出堆顶元素，并把最后一个元素挪到第一个
2. 与左右孩子中最大的节点换位置
3. 重复第2步直到比左右孩子都大或者没有左右孩子

```python
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

进行堆排序

```python
def heap_sort(a, n):
    for i in range(2, n+1):
        shift_up(a, i)
    for i in range(n, 1, -1):
        swap(a[1], a[i])
        shift_down(a, i-1)
```

### 2.3 使用 Heapify 进行堆排序

将普通列表转换成堆：Heapify
1. 从第一个非叶子节点(count/2)开始，与左右孩子进行比较
2. 对每个非叶子节点，进行Shift Down操作，一定要Shift Down到底
3. 直到执行到根节点，Heapify结束

```python
# 用arr中[1,n]的元素构建一个堆
def heap_bottom_up(arr, n):
    # 遍历所有的非叶子节点
    for i in range(n / 2, 0, -1):
        parent = arr[i]
        heap = False
        while not heap and 2 * i <= n:
            j = 2 * i
            if j < n and arr[j] < arr[j + 1]:
                j += 1
            if parent >= arr[j]:
                heap = True
            else:
                arr[i] = arr[j]
                i = j
        arr[i] = parent
    return arr
```

进行堆排序

```python
def heap_sort(arr, n):
    n = len(arr)
    arr = [-1] + arr
    while n >= 1:
        heap_bottom_up(arr, n)
        arr[1], arr[n] = arr[n], arr[1]
        n -= 1
    return arr[1:]
```


