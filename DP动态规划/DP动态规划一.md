# 动态规划 Dynamic Programming（一）

动态规划是让无数朋友头疼的问题，我曾经也被动态规划所困扰。有很多人也总结过动态规划的做题方法，包括我自己，如：

解题顺序：

1.确定dp数组以及下标的含义，确定结果是返回什么值

2.确定递推公式

3.dp数组如何初始化

4.确定遍历顺序

5.举例推导dp数组

尽管如此，还是会有想不出来的时候。比如复杂的字符串，背包问题，看了答案知道为什么这么做，但是很像是通过背答案才知道为什么这么做。

后来开始尝试从本质上去理解动态规划问题，而不是一上来就使用dp数组，慢慢有了一些自己的理解。

这里我会从一个特殊的角度进入动态规划。

### 首先，动态规划问题，（多数）本质上是一个BruteForce的问题

BruteForce包括recursion，backtracking等

以Fibonacci来举例, F(*n*) = *F*(*n* − 1) + *F*(*n* − 2) ，大家都用recursion来做，代码是

```python
def bruteForce(n):
    if n <= 1:
        return n
    return bruteForce(n - 1) + bruteForce(n - 2)

```

Recursion的时间复杂度是O(2**n)，这就是一个暴力方法

我们发现指数型的时间复杂度，n的数值大了之后性能太差，需要改进。那么改进方法是什么呢？

可以来分析一下暴力方法计算时用到的tree

![Screen Shot 2023-03-23 at 4.55.24 PM](/Users/aoli/Desktop/自媒体/md/DP1/Screen Shot 2023-03-23 at 4.55.24 PM.png)

可以见得像F(3), F(2)等我们计算了多次，都是重复工作，浪费了性能。所以我们开始想要将一些计算过的数值，以后还能用到的，进行重复使用。

建立一个缓存cache，使用cache的方法复用数据，减少时间复杂度

```python
def memoization(n, cache):
    if n <= 1:
        return n
    if n in cache:
        return cache[n]

    cache[n] = memoization(n - 1) + memoization(n - 2)
    return cache[n]

```

通过cache的方法，所以要用的数据只会被计算一次，时间复杂度减少到O(n)



上面两种方法，都是自上而下的方法，将一个想要的结果拆分成部分，再去分别计算部分。（再拆分，计算，直到最底层。）

这是recursion的思想，有时候还觉得不够直观，那怎么办呢，最后就演化出了最普通的dp方法，是一种自下而上的方法。使用dp数组，来代替cache。从最底层的数据开始计算，一步一步往上算出上层数据，同时也会保存下来计算过的数据（相当于缓存），直到计算出我们想要的数据。

所以dp数组本质上，就是缓存而已

```python
def dp(n):
    if n < 2:
        return n

    dp = [0, 1]
    i = 2
    while i <= n:
        tmp = dp[1]
        dp[1] = dp[0] + dp[1]
        dp[0] = tmp
        i += 1
    return dp[1]

```

![Screen Shot 2023-03-23 at 5.09.11 PM](/Users/aoli/Desktop/自媒体/md/DP1/Screen Shot 2023-03-23 at 5.09.11 PM.png)

注意这里fibonacci只需要长度为2的数组就够了。

第一篇DP用的fibonacci的例子很简单，后面开始背包问题，dp会变得没那么好理解，尝试从bruteforce出发，去理解最终形态的dp。
