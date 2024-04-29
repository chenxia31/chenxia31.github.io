---
title: 数据分析和算法基础 Vol9
tags:
  - 单调栈
  - 复习
  - 力扣刷题
categories:
  - 程序设计
abbrlink: c0d90fda
date: 2024-04-29 19:17:23
---

堆栈 Stack是一种线性表数据，只允许在表的一端进行插入（push）和删除（pop）操作。堆栈的好处在于操作时间为线性的。因此可以极大的提供效率。最基本的题目是《T20 有效的大括号》，而在其中更加复杂的是单调栈：

>  单调栈（Monotone stack）：一种特殊的栈，在先进先出的基础上，要求从top到bottom的元素是单调的（单调递增 or 单调递减）

### 0x01 理解单调栈

维护一个单调栈的过程如图：

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-f3ae981069363b72a821a5e84f810160_1440w.png)





单调栈过程

单调栈可以解决的问题包括：

1. 寻找左侧第一个比当前元素大的元素
2. 寻找左侧第一个比当前元素小的元素
3. 寻找右侧第一个比当前元素大的元素
4. 寻找右侧第一个从当前元素小的元素

单调栈思路模版

```
stack = []
for num in nums:
    while stack and rule(stack[-1],num):
        # rule should be defined
        stack.pop()
    stack.push(gen(num))
    # gen should be related to the num
```

### 0x02 单调解决问题

### 2.1 [T901 股票价格跨度](https://leetcode.cn/problems/online-stock-span/)

典型的单调栈记录中值下标

```
class StockSpanner:

    def __init__(self):
        self.stack = []
        self.index = [0]
        self.record = 0

    def next(self, price: int) -> int:
        self.record += 1
        while self.stack and self.stack[-1]<=price:
            self.stack.pop()
            self.index.pop()
        self.stack.append(price)
        self.index.append(self.record)
        return self.index[-1]-self.index[-2]
```

### 2.2 [T853 车队](https://leetcode.cn/problems/car-fleet/)

严格来说并不是完全的单调栈，遍历即可

```
class Solution(object):
    def carFleet(self, target, position, speed):
        cars = sorted(zip(position, speed))
        times = [float(target - p) / s for p, s in cars]
        ans = 0
        while len(times) > 1:
            lead = times.pop()
            if lead < times[-1]: ans += 1  # if lead arrives sooner, it can't be caught
            else: times[-1] = lead # else, fleet arrives at later time 'lead'

        return ans + bool(times) # remaining car is fleet (if it exists)
```

### 2.3  [T907 子数组的最小值之和](https://leetcode.cn/problems/sum-of-subarray-minimums/)

典型的利用单调栈作为一种工具，来记录自己左边和右边的第一个发现的元素。这题更重要的是要明白重复是如何计算的

```
MOD = 10 ** 9 + 7
class Solution:
    def sumSubarrayMins(self, arr: List[int]) -> int:
        # # stage 为连续子数组长度为k的长度，max k 为 len(arr)
        # res = []
        # for i in range(len(arr)-1):
        #     res.append(min(arr[i],arr[i+1]))
        # if res:
        #     return (sum(arr)+self.sumSubarrayMins(res)) % MOD
        # else:
        #     return arr[0]

        # 根据元素的最小值程度来计算
        if len(arr) == 0:
            return 0
        if len(arr) == 1:
            return arr[0]

        ans = 0
        left_1 = [1]*len(arr) # 每个元素左边小于自己的值
        right_1 = [1]*len(arr) # 每个元素右边小于自己的值

        def singleStack(arr,left):
            stack = []
            for i in range(len(arr)):
                while stack and arr[stack[-1]]>=arr[i]:
                    # 当存在的时候
                    stack.pop()
                stack.append(i)
                if len(stack)==1:
                    left[i] = stack[0]+1
                else:
                    left[i] = stack[-1]-stack[-2]
            return left

        def singleStack2(arr,left):
            stack = []
            for i in range(len(arr)):
                while stack and arr[stack[-1]]>arr[i]:
                    # 当存在的时候
                    stack.pop()
                stack.append(i)
                if len(stack)==1:
                    left[i] = stack[0]+1
                else:
                    left[i] = stack[-1]-stack[-2]
            return left

        # how ever, this will make the duplicated 
        # only one way stack is accpted
        left = singleStack(arr,left_1)
        right = singleStack2(arr[::-1],right_1)[::-1]
        res = 0
        for i in range(len(arr)):
            res+=arr[i]*left[i]*right[i]
        return res%MOD
```

### 2.4 T456 132模式

首先根据模式我们可以很简单的判断可以设计单调递升的栈作为方法，但是如何判断2是否存在？也就是只要存在一个在13之间的数字即可，而这个正好是每次单调栈所不需要的

```
class Solution:
    def find132pattern(self, nums: List[int]) -> bool:
        ak = float("-inf")
        stack = []
        for num in reversed(nums):
            if ak > num: 
                return True
            while stack and stack[-1] < num:
                ak = stack.pop()
            stack.append(num)
        return False
```

### 2.5 T2865 美丽塔

引入presum的气息，在计算每个元素左边和右边相关元素之前，先计算从左向右的所有数组，再计算自右向左的所有数组，最后遍历得到自己想要的值

```
class Solution:
    def maximumSumOfHeights(self, maxHeights: List[int]) -> int:
        # 自左向右建立单调栈
        n = len(maxHeights)
        suffix = [0]*(n+1)
        presum = [0]*(n+1)
        stack = []
        # 根据单调栈求前缀和
        for i in range(n):
            while stack and maxHeights[stack[-1]] > maxHeights[i]:
                stack.pop()
            j = stack[-1] if stack else -1
            # i 目标前缀和的下标
            # j 是左侧第一个比目标值小的元素，也就是下山的元素
            presum[i+1] = presum[j+1]+(i-j)*maxHeights[i]
            stack.append(i)

        stack = []
        # 根据单调栈求后缀和
        for i in range(n-1,-1,-1):
            while stack and maxHeights[stack[-1]] > maxHeights[i]:
                stack.pop()
            j = stack[-1] if stack else n
            suffix[i] = suffix[j]+(j-i)*maxHeights[i]
            stack.append(i)

        # 合并
        res = 0
        for i in range(n):
            res = max(res,presum[i+1]+suffix[i]-maxHeights[i])
        return res
```
