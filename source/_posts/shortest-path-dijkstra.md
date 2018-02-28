---
layout: post
title: 单源最短路径算法（一）
subtitle: Dijkstra's Algorithm
author: Hayden Wang
header-img: path.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-28 20:27:23
---

## 单源最短路径问题

> 对于每条边都有一个权值的图来说，单源最短路径问题是指从某个节点出 发，到其他节点的最短距离。该问题的常见算法 Bellman-Ford 和 Dijkstra 算法。前者适用于一般情况(包括存在负权值的情况，但不存在从源点可达的负权值回路)，后者仅适用于均为非负权值边的情况。Dijkstra 的运行时间可以小于 Bellman-Ford。

特别地，如果每条边权值相同(无权图)，由于从源开始访问图遇到节点的 最小深度就等于到该节点的最短路径，因此 Priority Queu 就退化成 Queue，Dijkstra 算法就退化成 BFS。

## Dijkstra's Algorithm

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