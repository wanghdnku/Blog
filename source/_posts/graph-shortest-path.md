---
layout: post
title: 图论 (四)：最短路径算法
subtitle: 单源最短路径 & 多源最短路径
author: Hayden Wang
header-img: path.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-03-01 20:57:04
---

## 单源最短路径问题

> 对于每条边都有一个权值的图来说，单源最短路径问题是指从某个节点出 发，到其他节点的最短距离。该问题的常见算法 Bellman-Ford 和 Dijkstra 算法。前者适用于一般情况(包括存在负权值的情况，但不存在从源点可达的负权值回路)，后者仅适用于均为非负权值边的情况。Dijkstra 的运行时间可以小于 Bellman-Ford。

特别地，如果每条边权值相同(无权图)，由于从源开始访问图遇到节点的 最小深度就等于到该节点的最短路径，因此 Priority Queu 就退化成 Queue，Dijkstra 算法就退化成 BFS。

### Dijkstra's Algorithm

Dijkstra 的核心在于，构造一个节点集合 S，对于 S 中的每一个节点，源点到该节点的最短距离已经确定。进一步地，对于不在 S 中的节点，我们总是选择其中到源点最近的节点，将它加入 S，并且更新其邻近节点到源点的距离。算法实现时需要依赖优先队列。

一句话总结，Dijkstra 算法利用贪心的思想，在剩下的节点中选取离源点最近的那个加入集合，并且更新其邻近节点到源点的距离，直至所有节点都被加入集合。关于 Dijkstra 算法的正确性分析，可以使用数学归纳法证明，详见《算法导论》第 24 章单源最短路径。这里给出伪代码如下:

```
DIJKSTRA(G, s)
S = EMPTY
Insert all vertexes into Q 
While Q is not empty
    u = Q.top
    S.insert(u)
    For each v is the neighbor of u
        If d[v] > d[u] + weight(u, v) 
            d[v] = d[u] + weight(u, v) 
            parent[v] = u
```

### Bellman-Ford Algorighm


## 多源最短路径问题

> 另一个关于图常见的算法是，如何获得任意两点之间的最短距离(All-pairs shortest paths)。直观的想法是，可以对于每个节点运行 Dijkstra 算法，该方法可行，但更适合的算法是 Floyd-Warshall 算法。

### Floyd's Algorithm

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
