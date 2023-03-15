## Union-Find(Disjoint Sets)

Union-Find is a useful tool for keeping track of nodes connected in a graph and detecting cycles in a graph. Of course, we can achieve this with DFS as well by using a hashset, call it, `seen`. However, DFS is a good choice when there is a static graph, i.e. no edges are added overtime. If we are adding edges overtime, that makes the graph dynamic, and Union-Find is a better choice. Although it is rarer than DFS.

## Disjoint sets

Union-Find operates on disjoint sets. Let's briefly go over them.

Disjoint sets are sets that don't have any element(s) in common. Formally, two disjoint sets are sets whose intersection is the empty set. Suppose we have two sets, `S1 = {1,2,3}` and `S2 = {4,5,6}`. `S1` and `S2` are referred to as disjoint sets, while two sets, `S3 = {1,2,5}`, and `S4 = {5,6,7}` are not disjoint.

Union-Find operates on disjoint sets. If we want to perform a union of two vertices, we need to ensure that those vertices belong to disjoint sets.

## Concept

Suppose that we are given an array of edges, `edges: [1,2], [4,1], [2,4]`, and we are asked to build a graph and detect a cycle. Here, each array in `edges` is an undirected, connected pair of vertices, i.e. `1` is connected to `2`, so on and so forth.

Union-Find is referred to as a "forest of trees". Initially, each vertex stands by itself, and for each vertex, we want to store the pointer to its parent. Since we have not connected them yet, each node is a parent to itself, i.e. points to itself.

![Union-Find-1](https://github.com/WhosthatAoli/Algorithms/blob/main/images/Trees/Union-Find-1.png)

Then, we want to connect the components together. Since `2` is connected to `1`, we can select it to be the child of `1`. Here, it does not matter which vertex is the parent and which vertex is the child. However, this order starts to matter when the two components we are trying to union have different heights, also referred to as the rank. If you are confused about this part, don't worry, we will expand on this soon.

The Union-Find data structure, has two operations. The Find operation and the Union operation. We want to ensure that we are not connecting vertices that are part of the same component. So, given a vertex, `n`, the find operation finds the parent of `n`. We can then use this in the union operation to see to join vertices together.



## Implementation

To implement Union-Find, we can have a `UnionFind` class, using which we can instantiate our `parent` hashmap and our `rank` hashmap. Some people like to use arrays instead of hashmaps, and that is also a valid option.

### The initial setup

```python
class UnionFind:
    def __init__(self, n):
        self.par = {}
        self.rank = {}

        for i in range(1, n + 1):
            self.par[i] = i
            self.rank[i] = 0
```

### Find

Our find function will take a vertex, `n`, as an argument and return its parent. We can do this by using our `parent` hashmap where the key is the vertex and the value is the parent. If a vertex is a parent to itself, we can just return the vertex itself.

##### Path Compression

As we are performing union on a large number of vertices, it can end up creating a pretty long chain, similar to a long linked list. Then, to determine if two nodes are part of the same component, it will take us a lot of steps. However, we can reduce the amount of these steps by traversing up two vertices at a time instead of one. This would mean that when we are going up the tree, the parent is actually the grandparent. This won't have any improvements in performance the first time but if we ever encounter the same vertex again, we can retrieve the parent immediately. This is referred to as path compression.

```python
def find(self, n):
    p = self.par[n]
    while p != self.par[p]:
        self.par[p] = self.par[self.par[p]]
        p = self.par[p]
    return p
```

The line `self.par[p] = self.par[self.par[p]]` is performing the path compression. It is updating the parent of the given vertex to point to the grandparent.

### Union

The union function takes two vertices and determines if a union can be performed. If the two vertices share the same parent, a union cannot be formed and we can return false. If the two vertices, call them `n1` and `n2`, have parents `p1` and `p2` respectively, and `p1`'s rank is higher than `p2`, `p2` is the child to `p1`. Conversely, `p1` is the child to `p2` if `p2`'s rank is higher than `p1`. If the rank/height of `p1` and `p2` are equal, we can set `p2` to be the parent of `p1` and increment the rank by 1.

```python
def union(self, n1, n2):
    p1, p2 = self.find(n1), self.find(n2)
    if p1 == p2:
        return False

    if self.rank[p1] > self.rank[p2]:
        self.par[p2] = p1
    elif self.rank[p1] < self.rank[p2]:
        self.par[p1] = p2
    else:
        self.par[p1] = p2
        self.rank[p2] += 1
    return True
```

The below visual demonstrates the find and the union function given `edges = [1,2], [4,1], [2,4]`. Notice that we connect 2 to 1, then we connect 4 to 1 because 1 has a higher rank. But, when we reach `[2,4]`, `2`'s parent is `1` and `4`'s parent is also `1`, meaning they belong to the same connected component, i.e. there is a cycle.

![Union-Find-2](https://github.com/WhosthatAoli/Algorithms/blob/main/images/Trees/Union-Find-2.png)

## Time Space Complexity

In the naive case, the find function will result in *O*(*n*) because it is possible that the tree is just a chain like a linked list and we have to traverse every single node. With path compression, since we are updating the parent to be the grandparent each time, we can reduce the complexity down to O(logn).

If we combine it with the union function, we get a time complexity called Inverse Ackermann,*α*(*n*), which can be simplified to constant time, *O*(1). So, if *m* is the number of edges we have, then the time complexity of Union-Find is *O*(*m* ∗ *l*o*g*n).

```class UnionFind:
    def __init__(self, n):
        self.par = {}
        self.rank = {}

        for i in range(1, n + 1):
            self.par[i] = i
            self.rank[i] = 0
    
    # Find parent of n, with path compression.
    def find(self, n):
        p = self.par[n]
        while p != self.par[p]:
            self.par[p] = self.par[self.par[p]]
            p = self.par[p]
        return p

    # Union by height / rank.
    # Return false if already connected, true otherwise.
    def union(self, n1, n2):
        p1, p2 = self.find(n1), self.find(n2)
        if p1 == p2:
            return False
        
        if self.rank[p1] > self.rank[p2]:
            self.par[p2] = p1
        elif self.rank[p1] < self.rank[p2]:
            self.par[p1] = p2
        else:
            self.par[p1] = p2
            self.rank[p2] += 1
        return True```
