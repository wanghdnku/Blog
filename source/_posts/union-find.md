---
layout: post
title: 并查集（Union Find）
subtitle: 检查连通性的高效数据结构
author: Hayden Wang
header-img: code.png
cdn: header-on
tags: [Algorithm]
date: 2018-02-27 22:34:23
---

并查集可以高效地回答连接问题(Connectivity Problem)。

**并查集的两个操作：**
1. `union(p, q)`：将p和q连接起来
2. `find(p)`：查找p所在组

**并查集可以回答的问题：**
- `is_connected(p, q)`：p和q是否相连

## 1. 并查集的一种实现 (Quick Find)

### 基本结构
1. 每一个节点对应一个编号，初始编号位自己
2. 两节点相连，只需将并查集中所有与其中一个节点相同的id都改成另一个id

![Quick Find](/images/union_find/quick_find.png)

### 代码实现
```python
class UnionFind1:
    def __init__(self, count):
        self.count = count
        self.id = range(count)

    def find(self, p):
        assert 0 <= p < self.count
        return self.id[p]

    def is_connected(self, p, q):
        return self.find(p) = self.find(q)

    def union(self, p, q):
        p_id = self.find(p)
        q_id = self.find(q)
        if p_id == q_id:
            return
        for i in range(count):
            if self.id[i] == p_id:
                self.id[i] = q_id
```

Quick Find 结构的 `find` 操作非常高效，只需一次查询；但进行 `union` 操作的时候，每次要将所有的节点 id 都遍历一次。

`find` 复杂度 O(1)，`union` 复杂度 O(n)

## 2. 并查集的另一种实现（Quick Union）

### 基本结构
1. 根指向自己
2. 两节点相连，只需将两节点最终指向的根相连

![Quick Union](/images/union_find/quick_union.png)

### 代码实现
```python
class UnionFind2:
    def __init__(self, count):
        self.count = count
        self.parents = range(count)

    def find(self, p):
        assert 0 <= p < self.count
        while p != self.parents[p]:
            p = self.parents[p]
        return p

    def is_connected(self, p, q):
        return self.find(p) == self.find(q)

    def union(self, p, q):
        p_root = self.find(p)
        q_root = self.find(q)
        if p_root == q_root:
            return
        self.parents[q_root] = p_root
```

与上面的结构相反，Quick Union 结构的 `union` 操作非常高效，只需将两节点的根相连，其他的节点并无影响；但进行 `find` 操作时，要追溯到根为止。

> 由于第二种实现使用了类似于树的结构，所以在追溯跟的过程中，复杂度相对于第一种实现的遍历所有节点要低。后面将讨论的并查集实现的优化方法都将基于 Quick Union 结构。

## 3. 使用 Size 优化并查集

之前的 Quick Union 实现中，`union` 操作只是简单地将 q 的根节点指向 p 的根节点。这样可能会出现 p 根节点相连的节点较少而 q 根节点相连节点较多，使得 union 之后形成的树高度增大。为了解决这个问题，可以简单地存储每个根节点所连接的节点数量，再执行 `union` 操作时，总是将节点较少的根指向节点较多的根，从而降低树的高度。

![Union Find with Size](/images/union_find/union_find_size.png)

### 优化思路
- 当并查集的树不平衡的时候，效率很低。故合并的时候将节点少的树加到节点多的树上。
- size[i]表示以i为根的集合中节点的个数

### 代码实现
```python
class UnionFindWithSize:
    def __init__(self, count):
        self.count = count
        self.parents = range(count)
        self.size = [1] * count

    def find(self, p):
        assert 0 <= p < self.count
        while p != self.parents[p]:
            p = self.parents[p]
        return p

    def is_connected(self, p, q):
        return self.find(p) == self.find(q)

    def union(self, p, q):
        p_root = self.find(p)
        q_root = self.find(q)
        if p_root == q_root:
            return
        if self.size[p_root] < self.size[q_root]:
            self.parents[p_root] = q_root
            self.size[q_root] += self.size[p_root]
        else:
            self.parents[q_root] = p_root
            self.size[p_root] += self.size[q_root]
```

## 4. 使用 Rank 优化并查集

经过 Size 的优化，并查集的查询效率可以得到很大的提高，但是仍然面临一些极端的情况：如果一个根节点层数很低，但是拥有的叶子节点很多，这样使用 Size 将不能准确判断树的高度，从而使 Size 优化失效。

![Union Find with Rank](/images/union_find/union_find_rank.png)

### 优化思路
- 根据size来判断并不完全准确，可能会有树只有一层，但是有很多节点，这样size很大，层数却很少。
- rank[i]表示跟节点为i的树的高度

### 代码实现
```python
class UnionFindWithRank:
    def __init__(self, count):
        self.count = count
        self.parents = range(count)
        self.rank = [1] * count

    def find(self, p):
        assert 0 <= p < self.count
        while p != self.parents[p]:
            p = self.parents[p]
        return p

    def is_connected(self, p, q):
        return self.find(p) == self.find(q)

    def union(self, p, q):
        p_root = self.find(p)
        q_root = self.find(q)
        if p_root == q_root:
            return
        if self.rank[p_root] < self.rank[q_root]:
            self.parents[p_root] = q_root
        elif self.rank[q_root] < self.rank[p_root]:
            self.parents[q_root] = p_root
        else:
            self.parents[p_root] = q_root
            self.rank[q_root] += 1
```

## 5. 使用路经压缩优化并查集

前面的优化，都将优化重点放在了 `union` 的操作上：在执行 `union` 时，尽可能减小连接后的树的高度。事实上，在 `find` 操作的过程中，也可以实现优化：

![Path Compression](/images/union_find/union_find_path_compression.png)

### 优化思路
- 路经压缩 (Path Compression)：在find的过程中，不断将层数变低

### 代码实现
```python
class UnionFindPathCompression:
    def __init__(self, count):
        self.count = count
        self.parents = range(count)
        self.rank = [1] * count

    def find(self, p):
        assert 0 <= p < self.count
        while p != self.parents[p]:
            self.parents[p] = self.parents[self.parents[p]]
            p = self.parents[p]
        return p

    def is_connected(self, p, q):
        return self.find(p) == self.find(q)

    def union(self, p, q):
        p_root = self.find(p)
        q_root = self.find(q)
        if p_root == q_root:
            return
        if self.rank[p_root] < self.rank[q_root]:
            self.parents[p_root] = q_root
        elif self.rank[q_root] < self.rank[p_root]:
            self.parents[q_root] = p_root
        else:
            self.parents[p_root] = q_root
            self.rank[q_root] += 1
```

## 6. 使用路经压缩优化并查集（终极版）

之前的路径压缩只是将树的高度缩减，但并没有达到最终效果：所有的叶子都直接指向根，即树的高度为 2。

![Path Compression 2](/images/union_find/union_find_path_compression_2.png)

### 优化思路
- 使用递归方法，将所有的叶子结点都最终指向根

### 代码实现
```python
class UnionFindPathCompression2:
    def __init__(self, count):
        self.count = count
        self.parents = range(count)
        self.rank = [1] * count

    def find(self, p):
        assert 0 <= p < self.count
        if p != parent[p]:
            parent[p] = self.find(parent[p])
        return parent[p]

    def is_connected(self, p, q):
        return self.find(p) == self.find(q)

    def union(self, p, q):
        p_root = self.find(p)
        q_root = self.find(q)
        if p_root == q_root:
            return
        if self.rank[p_root] < self.rank[q_root]:
            self.parents[p_root] = q_root
        elif self.rank[q_root] < self.rank[p_root]:
            self.parents[q_root] = p_root
        else:
            self.parents[p_root] = q_root
            self.rank[q_root] += 1
```

> 从逻辑上，这种方法比第一种路径压缩更优。但是由于使用了递归的方法，实际使用中，会比第一种方法稍慢一些。