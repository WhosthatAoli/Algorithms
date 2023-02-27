## 前缀和数组 Prefix Sums

```python
class PrefixSum:

    def __init__(self, nums):
        self.prefix = []
        total = 0
        for n in nums:
            total += n
            self.prefix.append(total)
        
    def rangeSum(self, left, right):
        preRight = self.prefix[right]
        preLeft = self.prefix[left - 1] if left > 0 else 0
        return (preRight - preLeft)

```

### 视频讲解发在了小红书

### 时间复杂度分析

建立前缀和prefixSum数组的时间是O(n)。之后计算连续的子数组的和都是O(1)的时间复杂度。

### 总结

前缀和方法-Prefix Sum适用于你需要持续地计算一个数组中子数组的和。除此之外不仅可以建立前缀和的数组，也可以建立前缀乘积来使用。

leetcode：303；304；724；238；560