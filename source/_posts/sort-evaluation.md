---
layout: post
title: 排序 (七)：排序算法的比较
subtitle: 几种常见排序算法的性能测试
author: Hayden Wang
header-img: algorithms.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-07 10:00:00
---

之前的几篇文章对三种不同思想的排序算法及其优化做了总结，本篇文章将会对几种排序算法作出评价。

## 1. 编写辅助函数

### 1.1 随机生成测试用例

**随机生成数组**
```python
def generate_unordered_array(n, start=0, end-100):
    return [randint(start, end) for _ in range(n)]
```

**生成近乎有序的数组**
```python
def generate_nearly_rodered_array(n, swap_times):
    arr = range(n)
    for i in range(swap_times):
        pos_x = randint(0, n-1)
        pos_y = randint(0, n-1)
        arr[pos_y], arr[pos_x] = arr[pos_x], arr[pos_y]
    return arr
```

### 1.2 评价算法性能

```python
def measure_sort(sort_name, sort, arr, n):
    time_start = time()
    sort(arr, n)
    time_end = time()
    print sort_name, ': time =', str(time_end - time_start)
```

## 2. 正式开始

