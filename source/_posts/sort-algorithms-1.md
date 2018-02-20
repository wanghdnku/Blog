---
layout: post
title: "排序 (一)：基于插入的排序算法"
subtitle: "插入排序 & 希尔排序"
author: Hayden Wang
header-img: algorithms.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-07 06:00:00
---

## 1. 插入排序

### 1.1 插入排序的基本实现

思路：第一个元素默认有序，把后面的每一个元素插入到已排序数列的合适位置。

```python
def insertion_sort(a, n):
    for i in range(1, n):
        # 寻找第i的元素合适的插入位置，从i向前依次查看
        j = i
        while j-1 >= 0 and a[j] < a[j-1]:
            swap(a[j], a[j-1])
            j -= 1
    return a
```

### 1.2 插入排序的特点
- 第二层循环可以提前结束，所以效率应该略高于选择排序，但是实际却慢于选择排序（应为交换比比较浪费时间）
- 插入排序对于有序的数组，复杂度降为O(n)

### 1.3 插入排序的优化

插入排序交换次数过多: 优化方法是不贸然交换，将比较的元素复制出来，用赋值代替交换
- 一个非常重要的性质: 可以提前终止循环，如果数组基本有序的话，效率很高
- 甚至在数组近乎有序的时候，速度快于O(nlogn)的算法

```python
# 插入排序的优化
def insertion_sort_opt(a, n):
    for i in range(1, n):
        num_i = a[i]
        j = i
        while j-1 >= 0 and num_i < a[j-1]:
            a[j] = a[j-1]
            j -= 1
        a[j] = num_i
    return a
```

---


## 2. 希尔排序

思路：希尔排序(Shell Sort)是插入排序的一种。也称缩小增量排序，是直接插入排序算法的一种更高效的改进版本。希尔排序是把记录按下标的一定增量分组，对每组使用直接插入排序算法排序；随着增量逐渐减少，每组包含的关键词越来越多，当增量减至1时，整个文件恰被分成一组，算法便终止。步长的选择是希尔排序效率的关键，如果步长为1，即插入排序，时间复杂度退化到 O(n^2)

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
