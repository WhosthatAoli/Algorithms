# 图论intro+MatrixDFS&BFS+Adjancy List

图论可以说是初学者很头疼的一大类问题，因为图论的问题样式太多了，解决图论问题时也往往包含着recursion，backtrack，还有可能用到一些data structure，这些都让解题变得复杂。

后面的图论内容会一点点拆解图论可能遇到的情况。先从图的基本属性开始吧。

## 图的属性

图包含边（edges）和节点（vertices），我们用E来代表边的数量，用V来代表节点的数量。V是永远小于等于V**2。

如果边是带有方向的，通常以箭头来表示，那么我们称为有向图（directed graph），如果没有方向，为无向图（undirected graph）。

## 图的表示形式

我们用两种形式来表示一个图：邻接矩阵和邻接表

### 邻接矩阵（Adjacency Matrix）

```python
adjMatrix = [[0, 0, 0, 0],
             [1, 1, 0, 0],
             [0, 0, 0, 1],
             [0, 1, 0, 0]]

#含义如下
# an edge exists from v1 to v2
adjMatrix[v1][v2] = 1  #权重可能根据题目改变

# an edge exists from v2 to v1
adjMatrix[v2][v1] = 1

# No edge exists from v1 to v2
adjMatrix[v1][v2] = 0

# No edge exists from v2 to v1
adjMatrix[v2][v1] = 0

```

### 邻接表

一个节点拥有一个列表来记录和自己相连的邻居。后面会涉及特别多使用邻接表的问题。

```python
# GraphNode used for adjacency list
class GraphNode:
    def __init__(self, val):
        self.val = val
        self.neighbors = []

```

邻接表的创建可以用类，可以用字典，比如：

```python
#edges = [["A", "B"], ["B", "C"], ["B", "E"], ["C", "E"], ["E", "D"]]
for src, dst in edges:
    if src not in adjList:
        adjList[src] = []
    if dst not in adjList:
        adjList[dst] = []
    adjList[src].append(dst)
```

也可以进行加权等等，要根据具体题意来进行



## DFS深搜

深度搜索往往用来判断能否成功，有多少条成功路径此类问题。

深搜三部曲：

1.确认递归函数，需要的参数

2.确认终止条件，确定处理什么节点，有三种选择方式

​	1.处理当前节点，判断下一节点，是否去下一节

​	2.判断当前节点（visited过return），处理当前节点，去下一节点

​	3.判断下一节点，处理下一节点，去下一节点

3.处理出发路径，是否需要回溯

### Matrix DFS

下面是计算有多少条路可以从左上角走到右下角的Matrix DFS代码：

```python
# Matrix (2D Grid)
grid = [[0, 0, 0, 0],
        [1, 1, 0, 0],
        [0, 0, 0, 1],
        [0, 1, 0, 0]]

# Count paths (backtracking)
def dfs(grid, r, c, visit):
    ROWS, COLS = len(grid), len(grid[0])
    #处理当前节点
    if (min(r, c) < 0 or
        r == ROWS or c == COLS or
        (r, c) in visit or grid[r][c] == 1):
        return 0
    if r == ROWS - 1 and c == COLS - 1:
        return 1

    visit.add((r, c))

    count = 0
    count += dfs(grid, r + 1, c, visit)
    count += dfs(grid, r - 1, c, visit)
    count += dfs(grid, r, c + 1, visit)
    count += dfs(grid, r, c - 1, visit)

    visit.remove((r, c))
    return count

  #Time Complexity 4**(M*N) 有四种移动方向
```

leetcode练习：200，695



## BFS广搜

### Matrix BFS

BFS通常用来寻找最短的路径，因为是广搜，第一次搜到目标点时，用的路径是最短的。

```python
from collections import deque

# Matrix (2D Grid)
grid = [[0, 0, 0, 0],
        [1, 1, 0, 0],
        [0, 0, 0, 1],
        [0, 1, 0, 0]]

# Shortest path from top left to bottom right
def bfs(grid):
    ROWS, COLS = len(grid), len(grid[0])
    visit = set()
    queue = deque()  #使用一个queue
    queue.append((0, 0))
    visit.add((0, 0))

    length = 0
    while queue:
        for i in range(len(queue)):
            r, c = queue.popleft()
            if r == ROWS - 1 and c == COLS - 1:
                return length

            neighbors = [[0, 1], [0, -1], [1, 0], [-1, 0]]
            for dr, dc in neighbors:
                if (min(r + dr, c + dc) < 0 or
                    r + dr == ROWS or c + dc == COLS or
                    (r + dr, c + dc) in visit or grid[r + dr][c + dc] == 1):
                    continue
                queue.append((r + dr, c + dc))
                visit.add((r + dr, c + dc))
        length += 1

        #Time Complexity O(n*m) 每个区域搜索一次
```

leetcode练习：1091，994

### 邻接表Adjancy List

这应该是最常见的形式，题目也是最多的，之后出现的二分图，最小生成树，最短路径问题，Topological Sort等问题都会用到

```python
from collections import deque

# GraphNode used for adjacency list
class GraphNode:
    def __init__(self, val):
        self.val = val
        self.neighbors = []

# Or use a HashMap
adjList = { "A": [], "B": [] }

# Given directed edges, build an adjacency list
edges = [["A", "B"], ["B", "C"], ["B", "E"], ["C", "E"], ["E", "D"]]

adjList = {}

for src, dst in edges:
    if src not in adjList:
        adjList[src] = []
    if dst not in adjList:
        adjList[dst] = []
    adjList[src].append(dst)


# Count paths (backtracking) 满足要求路径数
def dfs(node, target, adjList, visit):
    if node in visit:
        return 0
    if node == target:
        return 1
    
    count = 0
    visit.add(node)
    for neighbor in adjList[node]:
        count += dfs(neighbor, target, adjList, visit)
    visit.remove(node)

    return count
#Time Complexity O(N**v)
  
  
# Shortest path from node to target  #最短路长
def bfs(node, target, adjList):
    length = 0
    visit = set()
    visit.add(node)
    queue = deque()
    queue.append(node)

    while queue:
        for i in range(len(queue)):
            curr = queue.popleft()
            if curr == target:
                return length

            for neighbor in adjList[curr]:
                if neighbor not in visit:
                    visit.add(neighbor)
                    queue.append(neighbor)
        length += 1
    return length
#Time Complexity O(V+E)
```

leetcode练习：133，207