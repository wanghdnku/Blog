---
layout: post
title: 浅谈二分法
subtitle: 二分查找法与二分搜索树
author: Hayden Wang
header-img: binary_tree.jpg
cdn: header-on
tags: [Algorithm]
date: 2018-02-21 11:35:37
---



## 1. 二分查找法
### 非递归形式：
```python
def binary_search(arr, target):
    # 在[l, r]之间寻找target
    l, r = 0, len(arr)-1
    while l <= r:
        mid = l + (r - l) / 2
        if arr[mid] == target:
            return mid
        if target < arr[mid]:
            r = mid - 1
        else:
            l = mid + 1
    return -1
```
### 递归形式：
```python
def BS_recursive(arr, target, left, right):
    if left > right:
        return -1
    mid = left + (right - left) / 2
    if target == arr[mid]:
        print 'find: ', mid
        return mid
    elif target < arr[mid]:
        return BS_recursive(arr, target, left, mid - 1)
    else:
        return BS_recursive(arr, target, mid + 1, right)
```

---
<br>


## 2. 二分搜索树的数据结构

```python
# 二分搜索树的节点
class TreeNode:
    def __init__(self, k, v):
        self.key = k
        self.value = v
        self.left = None
        self.right = None
```

### 2.1 插入节点
```python
# 向二分搜索树中插入节点
def insert(node, key, value):
    if node is None:
        return TreeNode(key, value)
    if key == node.key:
        node.value = value
    elif key < node.key:
        node.left = insert(node.left, key, value)
    else:
        node.right = insert(node.right, key, value)
    return node
```

### 2.2 查找 key 对应的 value
```python
# 在二分搜索树中查找key对应的value
def search(node, key):
    if node is None:
        return None
    if key == node.key:
        return node.value
    elif key < node.key:
        return search(node.left, key)
    else:
        return search(node.right, key)
```

### 2.3 二叉树的深度优先遍历

#### 递归写法

```python
# 前序优先遍历
def pre_order(node, result):
    if node:
        result.append(node.key)
        pre_order(node.left, result)
        pre_order(node.right, result)
    return result

# 中序优先遍历
def in_order(node, result):
    if node:
        in_order(node.left, result)
        result.append(node.key)
        in_order(node.right, result)
    return result

# 后序优先遍历
def post_order(node, result):
    if node:
        post_order(node.left, result)
        post_order(node.right, result)
        result.append(node.key)
    return result
```

#### 非递归写法

```python

```

### 2.4 二叉树的广度优先遍历

#### 层序遍历连续打印
```python
# 层序遍历
def level_order(root):
    if not root:
        return []
    result, queue = [], []
    queue.append(root)
    while queue:
        node = queue.pop(0)
        result.append(node.key)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    return result
```

#### 层序遍历分层打印
```python
def level_order_hierarchical(root):
    if not root:
        return []
    result, queue = [], []
    queue.append((root, 0))
    while queue:
        node, level = queue.pop(0)
        if level == len(result):
            result.append([])
        result[level].append(node.val)
        if node.left:
            queue.append((node.left, level+1))
        if node.right:
            queue.append((node.right, level+1))
    return result
```


### 2.5 查找最大、最小节点
```python
def find_minimum(node):
    if node.left is None:
        return node
    return find_minimum(node.left)

def find_maximum(node):
    if node.right is None:
        return node
    return find_maximum(node.right)
```

### 2.6 删除最大、最小节点
最大的节点总在最右侧，最小的节点总在最左侧
```python
def remove_minimum(node):
    if node is None:
        raise Exception('This is an empty tree')
    if node.left is None:
        right_node = node.right
        del node
        return right_node
    node.left = remove_minimum(node.left)
    return node

def remove_maximum(node):
    if node is None:
        raise Exception('This is an empty tree')
    if node.right is None:
        left_node = node.left
        del node
        return left_node
    node.right = remove_maximum(node.right)
    return node
```

### 2.7 删除任意节点
Hubbard Deletion

- 用右子树中最小节点替代删除节点，或
- 用左子树中最大节点替代删除节点。

```python
# Hubbard Deletion, 返回最新的node节点
def remove(node, key):
    if node is None:
        return None
    # 递归在左子树删除
    if key < node.key:
        node.left = remove(node.left, key)
        return node
    # 递归在右子树删除
    elif key > node.key:
        node.right = remove(node.right, key)
        return node
    # 删除当前结点
    else:  # key == node.key
        # 情况1: 左侧是空，直接用右子树取代
        if node.left is None:
            right_tree = node.right
            del node
            return right_tree
        # 情况2: 右侧是空，直接用左子树取代
        if node.right is None:
            left_tree = node.left
            del node
            return left_tree
        # 情况3: 左右都不为空，寻找右侧最小的作为继任
        successor = find_minimum(node.right).copy()
        successor.right = remove_minimum(node.right)
        successor.left = node.left
        del node
        return successor
```
