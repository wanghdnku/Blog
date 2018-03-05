---
layout: post
title: 图论 (三)：最小生成树算法
subtitle: Prim's & Kruskal's Algorithm
author: Hayden Wang
header-img: graph.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-03-01 20:29:05
---

前提：带权无向图

这里讨论的两种最小生成树算法 Prim 算法和 Kruskal 算法都是贪心算法。

贪心算法的每一步必须在多个可能的选择中选择一种，贪心算法推荐选择在当前看来最好的选择。这种策略一般并不能保证找到一个全局最优的解决方案。但是对于最小生成树问题来说，可以证明确实能找到权重最小的生成树。

## 贪心算法的思路

**切分**：把图中的结点分为两部分，成为一个切分(Cut)。
**横切边**：如果一个边的两个端点，属于切分不同的两边，这个边称为横切边(Corssing Edge)。

**切分定理**：给定__任意__切分，横切边中最小的边必然属于最小生成树。


```shell
GENERIC-MST(G, w)
    A = nil
    while A does not form a spanning tree
        find an edge (u,v) that is safe for A
        add (u,v) to A
    return A
```

## Kruskal's Algorithm

使用数据结构：并查集

```shell
MST-KRUSKAL(G, w)
    A = []
    UNION-FIND(G.V)
    sort the edges of G.E into nondecreasing order by weight w
    for each edge (u,v) from G.E
        if UNION-FIND.find(u) != UNIONFIND.find(v)
            add (u,v) to A
            UNION-FIND.union(u,v)
    return A
```

## Prim's Algorithm

使用数据结构：最小优先队列

```shell
Input: G-Graph, w-weight, r-root
```

