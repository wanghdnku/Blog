---
layout: post
title: 最小生成树算法
subtitle: Prim's & Kruskal's Algorithm
author: Hayden Wang
header-img: graph.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-28 20:29:05
---

这里讨论的两种最小生成树算法 Prim 算法和 Kruskal 算法都是贪心算法。

贪心算法的每一步必须在多个可能的选择中选择一种，贪心算法推荐选择在当前看来最好的选择。这种策略一般并不能保证找到一个全局最优的解决方案。但是对于最小生成树问题来说，可以证明确实能找到权重最小的生成树。

## 贪心算法的思路



```shell
GENERIC-MST(G, w)
    A = nil
    while A does not form a spanning tree
        find an edge (u,v) that is safe for A
        add (u,v) to A
    return A
```

## Kruskal's Algorithm

```shell
MST-KRUSKAL(G, w)
    A = []
    for each vertex v from G.V
        MAKE-SET(v)
    sort the edges of G.E into nondecreasing order by weight w
    for each edge (u,v) from G.E
        if FIND-SET(u) != FIND-SET(v)
            add (u,v) to A
            UNION(u,v)
    return A
```

## Prim's Algorithm

```shell
Input: G-Graph, w-weight, r-root
```