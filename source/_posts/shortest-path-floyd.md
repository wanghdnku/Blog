---
layout: post
title: 多源最短路径算法
subtitle: Floyd's Algorithm
author: Hayden Wang
header-img: path.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-28 20:28:25
---

## 多源最短路径问题

> 另一个关于图常见的算法是，如何获得任意两点之间的最短距离(All-pairs shortest paths)。直观的想法是，可以对于每个节点运行 Dijkstra 算法，该方法可行，但更适合的算法是 Floyd-Warshall 算法。

Floyd 算法的核心是动态规划，利用二维矩阵存储 i，j 之间的最短距离，矩阵的初始值为 i，j 之间的权值，如果 i，j 不直接相连，则值为正无穷。动态规划的递归式为:
$$ d_{ij}^{(k)}=min(d_{ij}^{(k-1)},d_{ik}^{(k-1)}+d_{kj}^{(k-1)}) $$

直观上理解，对于第 k 次更新，我们比较从 i 到 j 只经过节点编号小于 k 的中间节点 $d_{ij}^{(k-1)}$，和从 i 到 k，从 k 到 j 的距离之和 $d_{ik}^{(k-1)}+d_{kj}^{(k-1)}$。

Floyd算法的复杂度是 $O(n^3)$。关于Floyd 算法的理论分析，请见《算法导论》第 25 章所有结点对的最短路径问题。这里 给出伪代码如下:

```
FLOYD(G)
Distance(0) = Weight(G) 
For k = 1 to n
    For i = 1 to n 
        For j = 1 to n
            Distance(k)_ij = min(Distance(k-1)_ij, Distance(k-1)_ik+ Distance(k-1)_kj) 
Return Distance(n)
```