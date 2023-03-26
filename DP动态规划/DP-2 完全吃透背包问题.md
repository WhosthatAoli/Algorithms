## DP-2 完全吃透背包问题

### 01背包

首先我们从0-1背包问题开始

0-1背包问题是给出N个物品，每个物品有自己的重量和价值，每个物品只能用一次。背包有总容量限制。求怎么选物品可以获得最大的价值。

那么先从最直观的理解开始：要获得最大的价值，那么使用暴力解法，求出来所有的能放下的组合，选其中价值最大的。

```python
# Brute force Solution
# Time: O(2^n), Space: O(n)
# Where n is the number of items.

#这里profit，weight是给出的物品的价值，重量的数组，capacity是背包总容量
def dfs(profit, weight, capacity):
    return dfsHelper(0, profit, weight, capacity)

#i是物品的编号
def dfsHelper(i, profit, weight, capacity):
    if i == len(profit):
        return 0

    # Skip item i 第一种情况不把此物品放入背包
    maxProfit = dfsHelper(i + 1, profit, weight, capacity)

    # Include item i 第二种情况将此物品放入背包
    newCap = capacity - weight[i]  #剩余容量减少
    if newCap >= 0:
        p = profit[i] + dfsHelper(i + 1, profit, weight, newCap)  #新的价值
        # Compute the max
        maxProfit = max(maxProfit, p)  #取新价值和旧价值更大的

    return maxProfit
```

这里相当于遍历出了所有可能性，很容易发现，调用了多次重复的`dfsHelper(i + 1, profit, weight, newCap)`或是`dfsHelper(i + 1, profit, weight, capacity)`，它们表示的是在放某个物品时，在特定的背包剩余容量下，最大价值是多少。

这里面改变的只有物品序号i和背包的剩余容量，且同一个i和背包剩余容量的组合被使用多次，那么就可以使用cache来进行缓存。

```python
# Memoization Solution
# Time: O(n * m), Space: O(n * m)
# Where n is the number of items & m is the capacity.
def memoization(profit, weight, capacity):
    # A 2d array, with N rows and M + 1 columns, init with -1's
    N, M = len(profit), capacity
    cache = [[-1] * (M + 1) for _ in range(N)]
    return memoHelper(0, profit, weight, capacity, cache)

def memoHelper(i, profit, weight, capacity, cache):
    if i == len(profit):
        return 0
    if cache[i][capacity] != -1:
        return cache[i][capacity]

    # Skip item i
    cache[i][capacity] = memoHelper(i + 1, profit, weight, capacity, cache)
    
    # Include item i
    newCap = capacity - weight[i]
    if newCap >= 0:
        p = profit[i] + memoHelper(i + 1, profit, weight, newCap, cache)
        # Compute the max
        cache[i][capacity] = max(cache[i][capacity], p)

    return cache[i][capacity]

```

这样就将时间复杂度降到了O（n * m）



那么上面两种办法都是从上至下，下面我们将其转化为自下而上搭积木，使用DP数组作为我们的积木，一个维度是放置顺序到了物品i，一个维度是剩余的背包容量，从0到全部。剩余的背包容量，又可以理解为独立的小背包的容量。

同时我们也遵循dp问题的常规推导方法一步步来：

1.确定dp数组以及下标的含义，确定结果是返回什么值

2.确定递推公式

3.dp数组如何初始化

4.确定遍历顺序

5.举例推导dp数组

#### 01背包： 每件物品只能用一次

1.`dp[i][j]` 表示从0-i的物品里任意取，放进容量为j的背包，价值总和最大是多少，最终返回`dp[i][j]`

2. + 不放此物品：`dp[i-1][j]`

   + 放此物品：`dp[i-1][j-weight[i]]+value[i]`

   + 递推公式：`dp[i][j] = max(dp[i-1][j],dp[i-1][j-weight[i]]+value[i])`

3. 初始化

   ```
   j < weight[0]: dp[0][j] = 0  #背包容量小于第一个物品的重量（物品重量已经从小到大排序）
   j >= weight[0]: dp[0][j] = value[0]
   dp[n][0] = 0
   ```

4. 遍历顺序：

   先遍历物品和先遍历背包重量都可以，但是先遍历物品更好理解。本质在于dp的更新方向

5. 推导dp数组

```python
def bag_problem01(bag_size,weight,value):
    weight = sorted(weight)
    rows, cols = len(weight),bag_size +1
    dp = [[0 for _ in range(cols)] for _ in range(rows)]
    
    #初始化dp数组
    for i in range(rows):
      	dp[i][0] = 0
    first_item_weight, first_item_value = weight[0],value[0]
    for j in range(1,cols):
      	if first_item_weight <= j:
          	dp[0][j] = first_item_value
    
    #更新dp数组，外层物品，内层背包重量
 		for i in range(1,len(weight)):
      	cur_weight, cur_val = weight[i], value[i]
        for j in range(1,cols):
          	if cur_weight > j:
              	dp[i][j] = dp[i-1][j]
            else:
              	dp[i][j] = max(dp[i-1][j],dp[i-1][j-cur_weight]+cur_val)  #递推公式
                
    return dp
```

对于背包问题，我们可以使用一个小技巧，来进一步节省空间的使用，将二维的dp数组减少到一维。

逻辑是，当我们开始选择放一个物品，需要的是放过上一个物品的一行数据，不再需要放上上个物品时的数据。那我们就可以只使用一行数据，新数据直接在本行覆盖旧数据。

但是背包容量的遍历顺序需要改为从后向前，因为当我们使用一行数据时，取旧数据都是从更小的背包容量取。从后向前遍历背包容量，不会影响到我们在更新本行时取旧数据的操作，因为更小的背包容量的数据就是旧数据。（0-1背包保证物品只能取一次）如果使用从前向后，那么取更小背包容量的数据，就有可能取到本行操作更新的新数据。（但是完全背包就要从前向后，因为物品可以取多次）

```python
#01 背包 一维数组
for i in range(len(weight)):
  #注意要从后向前，保证每个物品只放一次
  	for j in range(bag_weight, weight[i] - 1, -1): #注意是到weight[i]-1,左闭右开，j到weight
      	dp[j] = max(dp[j],dp[j - weight[i]] + value[i])
        
```

二维和一维一定要注意遍历顺序的区别，一维倒序遍历相当于保证物品只放入一次，二维不需要担心，因为是从上一层推导，不需要担心本层更新后数据覆盖。

背包倒叙实际上的意思是只看上一层放物品的结果。正序相当于看了本层放物品的结果。

如果不想使用背包容量倒叙也是可以的，但是要每层新建一个临时的curRow来计算，最后让curRow去更新原本的dp

```python
for i in range(1, N):
        curRow = [0] * (M + 1)
        for c in range(1, M + 1):
            skip = dp[c]
            include = 0
            if c - weight[i] >= 0:
                include = profit[i] + dp[c - weight[i]]
            curRow[c] = max(include, skip)
        dp = curRow
```



### 完全背包

如果以上01背包的内容已经完全理解，那么做完全背包可以说非常简单。

完全背包的区别就在于同一物品可以放多次，只要还有容量就行。这样就直接拿本层更新过的数据来进行推导

二维dp的区别就是如果不取第i个物品，拿上一层数据；如果取第i个物品，拿本层数据进行推导。大家可以尝试修改检验一下是否理解。

下面直接贴一维dp的代码：

```python
def complete_pack1(weight,value,bag_weight):
  	dp = [0 for _ in range(len(bag_weight)+1)]
    
    for i in range(len(weight)):
      	for j in range(weight[i],bag_weight +1):
          	dp[j] = max(dp[j],dp[j-weight[i]] + value[i])
   	return dp
```

### 多重背包

多重背包是每种物品的数量是固定的，将m件物品直接摊开，转化为0-1背包去做。

```python
for i in range(len(nums)):
  	while nums[i]>1:
      	weight.append(weight[i])
        value.append(value[i])
        nums[i] -= 1
```

