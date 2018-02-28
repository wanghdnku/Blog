---
layout: post
title: 排序 (四)：归并排序算法
subtitle: 归并排序算法及其优化
author: Hayden Wang
header-img: algorithms.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-07 08:25:10
---

## 1. 归并排序的基本实现

需要O(n)的额外空间来进行归并

```python
# 归并排序
def merge(left, right):
    i, j = 0, 0
    result = []
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result += left[i:]
    result += right[j:]
    return result

def merge_sort(lists):
    if len(lists) <= 1:
        return lists
    num = len(lists) / 2
    left = merge_sort(lists[:num])
    right = merge_sort(lists[num:])
    return merge(left, right)
```

## 2. 归并排序的优化

- 优化思路1: 在merge的时候，如果left的最后一个元素，小于right的第一个元素，那么直接归并即可
- 优化思路2: 杜绝递归到底。当元素少的时候，使用插入排序。元素少的时候，近乎有序的可能性大

```python
def merge_opt(left, right):
    if left[-1] <= right[0]:
        return left + right
    i, j = 0, 0
    result = []
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result += left[i:]
    result += right[j:]
    return result

def merge_sort_opt(lists):
    if len(lists) <= 15:
        insertion_sort_opt(lists, len(lists))
        return lists
    num = len(lists) / 2
    left = merge_sort_opt(lists[:num])
    right = merge_sort_opt(lists[num:])
    return merge_opt(left, right)
```


### 3. 自底向上的归并排序

用迭代实现自底向上的归并排序: 没有利用数组的无序访问特性，所以可以对链表进行排序
未完成，__merge函数的实现和递归方法中相同

```python
def merge_sort_bottom_up(arr, n):
    size = 1
    while size < n:
        i = 0
        while i + size < n:
            __merge(arr, i, i+size-1, min(i+size+size-1, n-1))
            i += size * 2
        size *= 2
```
