---
title: 数据结构和算法基础 Vol6
tags:
  - 数据结构
  - 算法基础
  - 堆栈
categories:
  - 程序设计
abbrlink: 56ad14d7
date: 2024-04-29 16:56:02
---

数据结构、算法思想，具体问题的设计能力，以及一些黑话也是有必要的，比如双指针、滑动窗口、单调栈、优先队列等

### 0x01 堆栈的基本知识

**堆栈（Stack）**简称为栈。一种线性表数据结构，是一种只允许在表的一端进行插入和删除操作的线性表。

我们把栈中允许插入和删除的一端称为 **「栈顶（top）」；**另一端则称为 **「栈底（bottom）」** 。当表中没有任何数据元素时，称之为 **「空栈」。**

主要操作分为插入和删除操作，分别是入栈操作和出栈操作。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-15a438bf4f94a4ccb7586d82ce694b13_1440w.jpg)





image-20220708221924230

让我们来设计一个堆栈，对一个非直观的数据结构掌握的最好方法是用基本的数据结构来实现它，参照：[**T155 最小栈设计**](https://leetcode.cn/problems/min-stack/)

```
class MinStack:

    def __init__(self):
        self.stack=[]
        self.topPointer=-1
        self.min=[]

    def push(self, val: int) -> None:
        self.stack.append(val)
        self.topPointer+=1
        if self.topPointer==0:
            self.min.append(val)
        else:
            if val<self.min[-1]:
                self.min.append(val)
            else:
                self.min.append(self.min[-1])

    def pop(self) -> None:
        self.min.pop()
        self.stack.pop()
        self.topPointer-=1

    def top(self) -> int:

        return self.stack[-1]
```

### 0x02 堆栈的基本作用

与其说堆栈是一种数据结构，它更多的是一种设计算法的技巧（如果单纯从算法的角度来说），当然在实际硬件中也存在由于硬件的限制（比如磁带）导致顺序的问题。

从算法的角度来说，堆栈具有的作用

- 使用堆栈可以很方便的保存和取用信息，因此长被用作算法和程序中的辅助存储结构，临时保存信息，供后面操作中使用。
- 例如：操作系统中的函数调用栈，浏览器中的前进、后退功能。
- 堆栈的后进先出规则，可以保证特定的存取顺序。
- 例如：翻转一组元素的顺序、铁路列车车辆调度。

### 0x03 堆栈的题目

### 3.1 有效的括号

[T20 有效的括号](https://leetcode.cn/problems/valid-parentheses/)

一个非常经典的题目，算是堆栈的先入后出的典型的应用

```
class Solution:
    def isValid(self, s: str) -> bool:
        if (len(s)%2==1):
            return False

        flagStack=[]
        pair={')':'(','}':'{',']':'['}
        for char in s:
            if len(flagStack)!=0:
                if char in pair and flagStack[-1]==pair[char]:
                    flagStack.pop()
                else: 
                    flagStack.append(char)
            else:
                flagStack.append(char)

        if flagStack:
            return False
        else:
            return True
```

### 3.2 基本计算器II

[T227 基本计算器II](https://leetcode.cn/problems/basic-calculator-ii/)

重点思想是将每个元素，都看成num在option的后面，但是逐个取出num和option；z这里没有考虑 到python的字符串操作功能

```
s Solution:
    def calculate(self, s: str) -> int:
        size=len(s)
        stack=[]
        op='+'
        index=0
        num=''
        while(index<size):

            if s[index] in '+-*/':
                # 如果是运算符
                num=''
                op=s[index]
                index+=1
            else:
                # 需要将num取出来
                while(index<size and s[index] not in '+-*/'):
                    num+=s[index]
                    index+=1
                num=int(num)
                if op=='+':
                    stack.append(num)
                elif op=='-':
                    stack.append(-num)
                elif op=='*':
                    top=stack.pop()
                    stack.append(top*num)
                elif op=='/':
                    top=stack.pop()
                    stack.append(int(top/num))
```

### 3.3 逆波兰表达式求值

[T150 逆波兰表达式求值](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)

思路和T227差不多

```
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        numStack=[]
        for char in tokens:
            if char in '+-*/':
                # 取出来前两个数字
                num1=numStack.pop()
                num2=numStack.pop()
                if char=='+':
                    numStack.append(num1+num2)
                elif char=='-':
                    numStack.append(num2-num1)
                elif char=='*':
                    numStack.append(num1*num2)
                else:
                    numStack.append(int(num2/num1))
            else:
                numStack.append(int(char))
        return numStack.pop()
```

### 3.4 字符串解码

用这个栈来记录当前的元素，如果为】就向后一直搜寻到【，得到num和str，来反复

```
class Solution:
    def decodeString(self, s: str) -> str:
        elementStack=[]
        temp=''
        num=''
        for char in s:
            elementStack.append(char)
            if char==']':
                # 提取字符串
                elementStack.pop()
                while(elementStack[-1]!='['):
                    top=elementStack.pop()
                    temp+=top[::-1]
                elementStack.pop()
                while(len(elementStack)>0 and elementStack[-1].isdigit()):
                    num+=elementStack.pop()
                # 新的字符
                for i in range(int(num[::-1])):
                    elementStack.append(temp[::-1])
                num=''
                temp=''
        res=''
        for e in elementStack:
            res+=e
        return res
```

### 3.5 验证栈序列

[T946 验证栈序列](https://leetcode.cn/problems/validate-stack-sequences/)

感觉这个就非常考验思维能力，因为pushed和popped相当于为逆反操作，因此可以翻归来按照堆栈的方式继续

```
class Solution:
    def validateStackSequences(self, pushed: List[int], popped: List[int]) -> bool:
        # 和括号一样的
        # 既然pushed和popped都是按照stack的规则
        # 反过来也可以用stack的规则来进行排序
        j=0
        stack=[]
        j=0
        for p in pushed:
            stack.append(p)
            while(stack and stack[-1]==popped[j]):
                stack.pop()
                j+=1
        return j==len(pushed)
```

### 0x04 单调栈（Monotone stack）基础知识

单调栈只是具有更强限制的栈，它需要人为设置规则（设置递增或者递减）的单调栈，由此可以实现的目的为：【可以在常数时间内找到一定数组内的最大值或者最小值；同时精简删除一些无用的信息】，所以在算法设计上需要更加巧妙。

通常可以分为单调递增栈和单调递减栈，这个顺序和什么是栈顶、什么是栈底一样很难让人理清楚顺序，因此只需要考虑在单调栈中栈的大小顺序是单调的，由此可以设计进出栈的顺序，以单调递增栈为例：

- 假设当前进栈元素为 x，如果 x 比栈顶元素小，则直接入栈。
- 否则从栈顶开始遍历栈中元素，把小于 x 或者等于 x 的元素弹出栈，直到遇到一个大于 x 的元素为止，然后再把 x 压入栈中。

### 0x05 单调栈的使用场景

网上有很多博客，也有很多的教学视频，这里摘选一些总结的，单调栈主要解决的问题为：

1. 左侧第一个比当前元素大的元素
2. 左侧第一个比当前元素小的元素
3. 右侧第一个比当前元素大的元素
4. 右侧第一个比当前元素小的元素

在实际的应用中，需要先将实际问题抽象称为上述的四种问题，再利用单调栈求解，有点过于抽象了，结合问题来看会好一点

### 5.1 寻找左侧第一个比当前元素大的元素

从左到右遍历元素，构造单调递增栈（从栈顶到栈底递增）：一个元素左侧第一个比它大的元素就是将其「插入单调递增栈」时的栈顶元素。如果插入时的栈为空，则说明左侧不存在比当前元素大的元素。

### 5.2 寻找左侧第一个比当前元素小的元素 [#](https://algo.itcharge.cn/03.Stack/02.Monotone-Stack/01.Monotone-Stack/#22-寻找左侧第一个比当前元素小的元素)

从左到右遍历元素，构造单调递减栈（从栈顶到栈底递减）：一个元素左侧第一个比它小的元素就是将其「插入单调递减栈」时的栈顶元素。如果插入时的栈为空，则说明左侧不存在比当前元素小的元素。

### 5.3 寻找右侧第一个比当前元素大的元素 [#](https://algo.itcharge.cn/03.Stack/02.Monotone-Stack/01.Monotone-Stack/#23-寻找右侧第一个比当前元素大的元素)

从左到右遍历元素，构造单调递增栈（从栈顶到栈底递增）：一个元素右侧第一个比它大的元素就是将其「弹出单调递增栈」时即将插入的元素。如果该元素没有被弹出栈，则说明右侧不存在比当前元素大的元素。

从右到左遍历元素，构造单调递增栈（从栈顶到栈底递增）：一个元素右侧第一个比它大的元素就是将其「插入单调递增栈」时的栈顶元素。如果插入时的栈为空，则说明右侧不存在比当前元素大的元素。

### 5.4 寻找右侧第一个比当前元素小的元素 [#](https://algo.itcharge.cn/03.Stack/02.Monotone-Stack/01.Monotone-Stack/#24-寻找右侧第一个比当前元素小的元素)

- 从左到右遍历元素，构造单调递减栈（从栈顶到栈底递减）：一个元素右侧第一个比它小的元素就是将其「弹出单调递减栈」时即将插入的元素。如果该元素没有被弹出栈，则说明右侧不存在比当前元素小的元素。
- 从右到左遍历元素，构造单调递减栈（从栈顶到栈底递减）：一个元素右侧第一个比它小的元素就是将其「插入单调递减栈」时的栈顶元素。如果插入时的栈为空，则说明右侧不存在比当前元素小的元素。

```
def 递增栈(nums):
    stack=[]
    for num in nums:
        while stack and num>=stack[-1]:
            stack.pop()
        stack.append(num)
```

### 0x06 单调栈的实际应用

### 6.1 下一个更大元素I

[T496 下一个更大元素 I](https://leetcode.cn/problems/next-greater-element-i/)

这个很符合单调栈使用的场景的，所以主要是需要熟悉单调栈求解问题的方法

```
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # 第二种使用单调递增栈，因为nums1是num2的子集，所以可以遍历nums2
        # 构造单调递增栈，求出nums2每个元素右侧下一个最大的元素，然后存储在哈希表中
        # 
        # 【具体做法】
        # res存储答案，使用stack表示单调递增栈，使用哈希表num-map存储nums2中比下一个当前
        # 元素大的数值，当前数值：下一个比当前元素大的数值
        # 
        # 遍历nums2，对于当前元素，如果小则入栈，如果元素大则一直出栈，出栈元素是第一个大
        # 于当前元素值的元素
        # 
        # 遍历玩数组nums2周，建立好哈希表之后，遍历数组1
        # 
        # 从num-map中取出对应的值
        res=[]
        stack=[]
        num_map=dict()

        for num in nums2:
            while(stack) and num>stack[-1]:
                num_map[stack[-1]]=num
                stack.pop()
            stack.append(num)
            print(stack)

        for num in nums1:
            res.append(num_map.get(num,-1))
        return res
```

### 6.2 下一个更大元素II

[T503 下一个更大元素II](https://leetcode.cn/problems/next-greater-element-ii/)

和上面一样，遍历两次就好了

```
class Solution:
    def nextGreaterElements(self, nums: List[int]) -> List[int]:
        res=[]
        stack=[]
        temp=[-1 for _ in range(len(nums))]
        stack_index=[]
        for i in range(len(nums)*2):
            while stack and nums[i%len(nums)]>stack[-1]:
                index=stack_index.pop()
                stack.pop()
                temp[index]=nums[i%len(nums)]
            stack_index.append(i%len(nums))
            stack.append(nums[i%len(nums)])
        return temp
```

### 6.3 每日温度

[T739 每日温度](https://leetcode.cn/problems/daily-temperatures/)

也是单调栈解题的常规思路

```
class Solution:
    def dailyTemperatures(temperatures):
        # 因为等价于找第i元素后，高于这个元素的index之差
        # 可以考虑使用一个stack来记录递减元素的下标
        size=len(temperatures)
        ans=[0 for _ in range(size)]
        stack=[]
        for i in range(size):
            while( stack and temperatures[i]>temperatures[stack[-1]]):
                index=stack.pop()
                ans[index]=i-index
            stack.append(i)
        return ans
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-20252b09ed3a25c8f958da2fa8ef0321_1440w.jpg)





image-20220708222328976

### 6.4 股票时间跨度

[T901 股票价格跨度](https://leetcode.cn/problems/online-stock-span/)

单调栈可以存储值，也可以存储对应的下标

```
class StockSpanner:
    # 小于或等于今天价格的最大连续日数
    # 等价于求解左侧最近的一次大于价格的日数

    def __init__(self):

        self.stack=[]
        self.day=[]

    def next(self, price: int) -> int:
        dayT=1
        while(self.stack and price>=self.stack[-1]):
            dayT+=self.day.pop()
            self.stack.pop()
        self.day.append(dayT)
        self.stack.append(price)
        return dayT
```

### 6.5 去除重复字母

[T316 去除重复字母](https://leetcode.cn/problems/remove-duplicate-letters/)

这一题看上去是和单调栈没有关系的，这里就需要将现实问题转换称为单调栈的问题；首先结果的字典序最小，就是尽可能的将a放到b前面、c放到d前面，因此我们每次遍历的时候需要对应字符放入结果并排序。但是我们要警惕后面是否还剩字符，因此就需要先遍历字符串中字符对应的数量，然后在遍历删除的时候根据字符串是否有剩余来决定删不删字符。amazing！

```
class Solution:
    def removeDuplicateLetters(self, s: str) -> str:
        # 首先需要统计各种字符串出现的次数
        # 对s进行遍历
        # 要求字典序最小，也就是a要尽可能在b前面，b要尽可能在c前面
        # 这就需要用单调栈来约束，但是需要注意单调栈中元素是否存在
        charMap=dict()
        resStack=[]

        for char in s:
            if char in charMap:
                charMap[char]+=1
            else:
                charMap[char]=1

        for char in s:
            if char not in resStack:
                # 如果不在resStack中,我需要设置一个尽可能小的序列
                # 同时需要保证还有数
                while resStack and char<resStack[-1] and charMap[resStack[-1]]>0:
                    resStack.pop()
                resStack.append(char)
                charMap[char]-=1
            else:
                charMap[char]-=1
        return ''.join(resStack)
```

### 6.6 最短无序连续子数组

[T581 最短无序子数组](https://leetcode.cn/problems/shortest-unsorted-continuous-subarray/)

同样的这个需要先分析问题，我们需要找到什么，隐含的就是序列一定是先递增、然后乱序、然后递增，这个第一个递增序列的最大值是后面的最小值、第二个递增序列的最小值是后面的最大值；由此就可以用单调栈的方式来求解问题

```
class Solution:
    def findUnsortedSubarray(self, nums: List[int]) -> int:
        # 如果必然存在这样的一个数组，那么肯定是单调递增，然后***、然后单调递增
        # 从小到大，找到左边界
        if len(nums)<2:
            return 0
        stack=[]
        size=len(nums)
        left=size-1
        right=0
        for i in range(size):
            while(stack and nums[i]<nums[stack[-1]]):
                left=min(left,stack.pop())
            stack.append(i)
        stack[:]=[]
        for i in range(size-1,-1,-1):
            while(stack and nums[i]>nums[stack[-1]]):
                right=max(right,stack.pop())
            stack.append(i)

        if right-left+1>0:
            return right-left+1
        else:
            return 0
```
