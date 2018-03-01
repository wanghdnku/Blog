---
layout: post
title: 图的遍历
subtitle: 深度优先 & 广度优先
author: Hayden Wang
header-img: graph.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-03-01 02:51:35
---

图的遍历（Graph Transversal）类似于树的遍历（事实上，树可以看成是图的一个特例），也分为深度优先和广度优先。

## 深度优先遍历

深度优先算法尽可能“深”地搜索一个图。对于某个节点 v，如果它有未搜索的边，则沿着这条边继续搜索下去，直到该路径无法发现新的节点，回溯回节点 v，继续搜索它的下一条边。深度优先算法也通过着色标记节点，白色表示未被发现，灰色表示被发现，黑色表示已访问。算法通过递归实现先进后出。一句话总结，深度优先算法一旦发现没被访问过的邻近节点，则立刻递归访问它，直到所有邻近节点都被访问过了，最后访问自己。

《算法导论》第 22 章关于图的基本算法部分给出了深度优先的伪代码实现， 引用如下：

```shell
DFS(G)
for each vertex v in G
    do Color[v] = WHITE
    Parent[v] = NIL
for each vertex v in G
    DFS_Visit(v)

DFS_Visit(u)
Color[u] = GRAY
for each neighbor v of u
    if Color[v] == WHITE
        Parent[v] = u
        DFS_Visit(v)
Color[u] = BLACK
```


### 连通分量

深度优先遍历的一个用途就是计算连通分量。

## 广度优先遍历

对于某个节点，广度优先会先访问其所有邻近节点，再访问其他节点。即，对于任意节点，算法首先发现距离为 d 的节点，当所有距离为 d 的节点都被访问后，算法才会访问距离为 d+1 的节点。广度优先算法将每个节点着色为白、灰或黑，白色表示未被发现，灰色表示被发现，黑色表示已访问。算法利用先进先出队列来管理所有灰色节点。一句话总结，广度优先算法先访问当前节点，一旦发现未被访问的邻近节点，推入队列，以待访问。

《算法导论》第 22 章关于图的基本算法部分给出了广度优先的伪代码实现， 引用如下：

```shell
BFS(G, s)
for each vertex u exept s
    do Color[u] = WHITE
        Distance[u] = MAX
        Parent[u] = NIL
Color[s] = GRAY
Distance[s] = 0
Parent[s] = NIL
Enqueue(Q, s)

While Q not empty
    Do u = Dequeue(Q)
        For each v is the neighbor of u
            do if Color[v] == WHITE
                Color[v] = GRAY
                Distance[v] = Distance[u] + 1
                Parent[v] = u
                Enqueue(Q, v)
            Color[u] = BLACK
```
