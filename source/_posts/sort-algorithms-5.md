---
layout: post
title: 排序 (五)：基于分类的排序算法
subtitle: 桶排序 & 基数排序
author: Hayden Wang
header-img: algorithms.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-07 08:45:10
---

## 1. 桶排序

桶排序（Bucket Sort）和基数排序（Radix Sort）不需要进行数据之间的两两比较，但是需要事先知道数组的一些具体情况。特别地，桶排序适用于知道待排序数组大小范围的情况。其特性在于将数据根据其大小，放入合适的“桶（容器）”中，再依次从桶中取出，形成有序序列。

具体实现如下：
```python
# 桶排序: array为待排序的数组，max为元素集大小
def bucketSort(array, max):
    n = len(array)
    sortedArray = [0] * n
    buckets = [0] * max
    # 将放入数组的元素依次放入桶中
    for index in xrange(n):
        # 若果越过元素集上界，则排序失败
        if array[index] >= 10:
            return None
        buckets[array[index]] += 1
    # 计算 index+1 对应的下标
    for index in xrange(1, max):
        buckets[index] += buckets[index - 1]
    # 排序
    for index in xrange(0, n):
        sortedArray[buckets[array[index]] - 1] = array[index]
        buckets[array[index]] -= 1
    return sortedArray
```

该实现总的时间代价为 $O(max+n)$，适用于 max 相对 n 较小的情况。空间复杂度也为 $O(max+n)$，用以记录原始数组和桶计数。


---
<br>


## 2. 基数排序

桶排序只适合 max 很小的情况，如果数据范围很大，可以将一个记录的值即排序码拆分为多个部分来进行比较，即使用基数排序。基数排序相当于将数据看作一个个有限进制数，按照由高位到低位（适用于字典序），或者由低位到高位（适用于数值序）进行排序。排序具体过程如下：对于每一位，利用桶排序进行分类，在维持相对顺序的前提下进行下一步排序，直到遍历所有位。该算法复杂度为 $O(k*n)$，k 为位数（或者字符串长度）。直观上，基数排序进行了 k 次桶排序。具体实现如下：

```python
# Radix sort for numbers in base 10
def radixSortNumbers(array):
    maxLen = -1
    for number in array: # Find longest number, in digits
        numLen = int(math.log10(number)) + 1
        if numLen > maxLen:
            maxLen = numLen
    buckets = [[] for i in range(0, 10)] # Buckets for each digit
    for digit in range(0, maxLen):
        for number in array:
            buckets[number / 10**digit % 10].append(number)
        del array[:]
        for bucket in buckets:
            array.extend(bucket)
            del bucket[:]
    return array
```

与其他排序方式相比，桶排序和基数排序不需要交换或比较，它更像是通过逐级的分类来把元素排序好。