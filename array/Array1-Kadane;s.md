#### Kadane algorithm

```python
    def maxSubArray(self, nums: List[int]) -> int:
        maxSum = nums[0]
        curSum = 0
        for R in nums:
            curSum = max(0,curSum)  #当curSum没有贡献的时候，置0
            curSum += R
            maxSum = max(maxSum,curSum)

        return maxSum
```

Kadane's Algorithm
Leetcode 53
#### 问题：从一个数组中找到一个拥有最大和的连续子数组
解决方法很简单，我们只需要使用两个指针，就可以遍历所有的子数组。但是这种暴力方法的时间复杂度是O(n**2)。

```
fn bruteForce(nums):
    maxSum = nums[0]

    for i = 0 until nums.length:
        curSum = 0
        for j = i until nums.length:
            curSum += nums[j]
            maxSum = max(maxSum, curSum)
    return maxSum

```



Kadanes‘s Algorithm的解决方式是其实是一种贪心法，本质是当前面的某一部分，对后面的部分起不到贡献（或者说负贡献）的时候，就将其弃掉。

![array1-1](https://github.com/WhosthatAoli/Algorithms/blob/main/images/array1/array1-1.png)当currSum的数字小于0，说明对后面不会再有贡献，此时就将currSum置0。算法的时间复杂度是O(n)。

```
fn kadanes(nums):
    maxSum = nums[0]
    curSum = 0

    for n in nums:
        curSum = max(curSum, 0)
        curSum += n
        maxSum = max(maxSum, curSum)
    return maxSum

```



若题目要求返回实际的子数组，我们只需要使用一个左指针L=0，和右指针R，从右侧加入元素。当L与R表示的数组和小于0时，将L指针右移到R指针即可。

![array1-2](https://github.com/WhosthatAoli/Algorithms/blob/main/images/array1/array1-1.png)对于图四滑动窗口方法的伪代码如下：

```
fn slidingWindow(nums):
    maxSum = nums[0]
    curSum = 0
    maxL, maxR = 0, 0
    L = 0

    for R = 0 until nums.length:
        // if constraint is broken, remove elements from the left
        if curSum < 0:
            curSum = 0
            L = R

        // add elements from the right
        curSum += nums[R]
        // update maxSum if curSum is greater
        if curSum > maxSum:
            maxSum = curSum
            maxL, maxR = L, R

    return [maxL, maxR]

```



**Leetcode 918，978**
算法的细节不一样，但是这种贪心的思想是一致的。即当前部分达到某些条件（如和小于0，对之后的和起负作用了）即刷新（舍弃）。可以用在很多不同题目上。
