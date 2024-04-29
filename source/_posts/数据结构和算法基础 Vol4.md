---
title: 数据结构和算法基础 Vol4
tags:
  - 数据结构
  - 算法基础
categories:
  - 程序设计
abbrlink: b8a375fb
date: 2024-04-29 16:38:45
---


适合初始算法与数据结构的新手和想要在短时间内高效提升的人，熟练掌握这100道题，可以具备在代码世界通行的基本能力。想借这一百道题来了解一下算法的一些基本思想。本次主要为题号1～20共11道题。第四题是重点。

### 0x00 自我总结

数据结构和算法是看待一道题目的不同解读。数据结构更多的是如何去表述一件事情，不同的数据结构具有不同的时间空间复杂度的性能和不同的接口，这也是不同数据结构的特点所在，算法更多的是解决问题的流程，如何把手里面已经有的数据结构通过三种基本的逻辑运算结合形成高效的操作流程。

在刷题之前掌握具有哪些常见的数据结构和算法是有必要的，数据结构可以分为线性数据结构：栈和队列、数据与矩阵；半线性结构：树，非线性结构：链表、数组、图、位运算等；从算法的角度可以分为基本：分治、贪心、动态规划、回溯、分枝定界；和一些特殊的技巧双指针、二分查找、搜索法等，以及特定的场景排序、树的搜索等

### 0x01 T1 两数之和

标签：数组、哈希表

难度：简单

>  给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。你可以按任意顺序返回答案。 

```
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        hashtable=dict()
        # 新建一个哈希表，来记录每个数字它对应的补数
        for i in range(len(nums)):
            if target-nums[i] in hashtable:
            # 在这个哈希表中就OK了，而且这个时间复杂度并不高
                return [hashtable[target-nums[i]],i]
            hashtable[nums[i]]=i
        return []
```

### 0x02 T2 两数相加

标签：递归、链表、标签

难度：中等

>  给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。 请你将两个数相加，并以相同形式返回一个表示和的链表。 你可以假设除了数字 0 之外，这两个数都不会以 0 开头。  

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1,l2):
        def addTwoNumbers2(l1, l2, flag):
            '''
            三种情况
            情况1：链表都存在，l1!=None
            情况2：链表不存在，赋值为0
            计算两数之和
            如果大于10，则val为余数
            flag值为1
            链表不存在并且flag==0，则循环终止，输出最终的链表
            链表存在，则继续下一个循环
            '''
            if l1!=None:
                num1=l1.val
                l1=l1.next
            else:
                num1 = 0
            if l2!=None:
                num2=l2.val
                l2=l2.next
            else:
                num2 = 0
            # 更新求和
            sum_result = num1 + num2 + flag
            # 结果求余
            val = sum_result % 10
            # 更新flag，大于9设置为1，其他的则为0
            if sum_result > 9:
                flag = 1
            else:
                flag = 0
            # 终止条件，l1为空，并且flag为0
            if l1 == None and l2==None and  flag == 0:
                return ListNode(val)
            return ListNode(val, addTwoNumbers2(l1, l2, flag))
        return addTwoNumbers2(l1,l2,0)
```

### 0x03 T3 无重复字符的最长子串

标签：字符串，滑动窗口哈希表

难度：中等

>  给定一个字符串  s ，请你找出其中不含有重复字符的 **最长子串** 的长度 

```
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
            # 感觉比较简单
        if not s:return 0
        left = 0
        lookup = set()
        n = len(s)
        max_len = 0
        cur_len = 0
        for i in range(n):
            cur_len += 1
            while s[i] in lookup:
                lookup.remove(s[left])
                left += 1
                cur_len -= 1
            if cur_len > max_len:max_len = cur_len
            lookup.add(s[i])
        return max_len
```

### 0x04 T4 寻找两个正序数组的中位数

标签：数组、二分查找、分治

难度：困难

>  给定两个大小分别为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的 中位数 。 算法的时间复杂度应该为 O(log (m+n)) 。  

```
class Solution(object):
    def findMedianSortedArrays(self, nums1, nums2):
        # even 需要找（m+n+1）/2
        # odd 需要找（m+n）/2 （m+n）/2+1
        m=len(nums1)
        n=len(nums2)
        if (m+n)%2 ==1:
            # odd
            return self.getK(nums1,nums2,(m+n+1)/2)
        else:
                # even
            a=self.getK(nums1,nums2,(m+n)/2)
            b=self.getK(nums1,nums2,(m+n)/2+1)
            return (a+b)/2


    def getK(self,nums1,nums2,k):
        # nums1：第一个正序的数组
        # nums2：第二正序的数组
        # k：两个并列数组中第k大的数组
        # 终止条件为，k为1
        k=int(k)
        m=len(nums1)
        n=len(nums2)
        index1,index2=int(min(k//2,m))-1,int(min(k//2,n))-1
        # 两个正序数组中的值
        if m==0:
            # 第一个数组到头
            return nums2[k-1]
        if n==0:
            # 说明第二个数组到头
            return nums1[k-1]
        if k==1:
            return min(nums1[0],nums2[0])
        # 下面来看如何更新迭代
        value1=nums1[index1]
        value2=nums2[index2]
        if value1 <= value2:
            k=k-index1-1
            nums1=nums1[index1+1:]
        else:
            k=k-index2-1
            nums2=nums2[index2+1:]
        return self.getK(nums1,nums2,k)
```

### 0x05 T5 最长回文子串

标签：字符串，动态规划

难度：中等

>  给你一个字符串  s ，找到 s 中最长的回文子串。 

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        ## dynamic programing ,基本步骤是确定下标的含义和递推公式的含义
        # 第一步，确定dp数据，用dp【i,j】来表示区间范围内的淄川是否为回文子串dp[i][j]是否为true
        # 第二步，确定递推的三种情况，回文数取真的情况分为
        # 1. i=j相等，true
        # 2. i和j相差等于1，true
        # 3. i和j相差大于1的情况，如果i和j相等，需要查看i和j的区间内部是不是相等，也就是dp[i+1][j-1]是不是true
        # 第三步，确定dp数组如何初始化，当然都是false
        # 第四步，为了保证dp[i+1][j-1]要最新开始计算,所以可以先将第一种情况和第二种情况先得到

        dp=[[0 for i in range(len(s))] for j in range(len(s))]
        left = 0
        right = 0
        maxlength=0
        for i in range(len(s)-1,-1,-1):
            for j in range(i, len(s)):
                if s[i]==s[j]:
                    if (j - i <= 1):
                        dp[i][j] = 1
                    elif dp[i + 1][j - 1] == 1:
                        dp[i][j] = 1
                if dp[i][j] == 1 and j - i + 1 > maxlength:
                    maxlength = j - i + 1
                    left = i
                    right = j
        return s[left:right + 1]
```

### 0x06 T10 正则表达式匹配

标签：字符串、递归、动态规划

难度：困难

>  给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。 '.' 匹配任意单个字符 '*' 匹配零个或多个前面的那一个元素 所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。 

```
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m, n = len(s), len(p)

        def matches(i: int, j: int) -> bool:
            if i == 0:
                return False
            if p[j - 1] == '.':
                return True
            return s[i - 1] == p[j - 1]

        f = [[False] * (n + 1) for _ in range(m + 1)]
        f[0][0] = True
        for i in range(m + 1):
            for j in range(1, n + 1):
                if p[j - 1] == '*':
                    f[i][j] |= f[i][j - 2]
                    if matches(i, j - 1):
                        f[i][j] |= f[i - 1][j]
                else:
                    if matches(i, j):
                        f[i][j] |= f[i - 1][j - 1]
        return f[m][n]
```

### 0x07 T11 盛最多水的容器

标签：贪心、数组、双指针

难度：中等

>  给定一个长度为 n 的整数数组 height 。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i]) 。 找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。 返回容器可以储存的最大水量。 说明：你不能倾斜容器。  

```
class Solution:
    def maxArea(self, height):
        # 找出其中的两条线，找出其中与x轴构成可以容纳最多的水
        left=0
        right=len(height)-1
        result=0
        while left < right:
            result=max(result,min(height[right],height[left])*(right-left))
            # 更新right和left
            # 提升的意义，在于删除小于当前值
            if height[right]>height[left]:
                left=left+1   
            else:
                right=right-1
        return result
```

### 0x08 T15 三数之和

标签：数组、双指针、排序

难度：中等

>  给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。  

```
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        hashtable=dict()
        # 新建一个哈希表，来记录每个数字它对应的补数
        for i in range(len(nums)):
            if target-nums[i] in hashtable:
            # 在这个哈希表中就OK了，而且这个时间复杂度并不高
                return [hashtable[target-nums[i]],i]
            hashtable[nums[i]]=i
        return []
```

### 0x09 T17电话号码的字符组合

标签：字符串、哈希表、回溯

难度：中等

>  给定一个仅包含数字  2-9 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。 

```
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        if not digits: return []

        phone = {'2':['a','b','c'],
                 '3':['d','e','f'],
                 '4':['g','h','i'],
                 '5':['j','k','l'],
                 '6':['m','n','o'],
                 '7':['p','q','r','s'],
                 '8':['t','u','v'],
                 '9':['w','x','y','z']}

        def backtrack(conbination,nextdigit):
            if len(nextdigit) == 0:
                res.append(conbination)
            else:
                for letter in phone[nextdigit[0]]:
                    backtrack(conbination + letter,nextdigit[1:])

        res = []
        backtrack('',digits)
        return res
```

### 0x10 T19 删除链表的倒数第N个节点

标签：链表，双指针

难度：中等

>  给你一个链表，删除链表的倒数第  n 个结点，并且返回链表的头结点。 

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy = ListNode(0, head)
        first = head
        second = dummy
        for i in range(n):
            first = first.next

        while first:
            first = first.next
            second = second.next

        second.next = second.next.next
        return dummy.next
```

### 0x11 T20 有效的括号

标签：栈、字符串

难度：简单

>  给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。 有效字符串需满足： 左括号必须用相同类型的右括号闭合。 左括号必须以正确的顺序闭合。 

```
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []

        for item in s:
            if item == '(':
                stack.append(')')
            elif item == '[':
                stack.append(']')
            elif item == '{':
                stack.append('}')
            elif not stack or stack[-1] != item:
                return False
            else:
                stack.pop()

        return True if not stack else False
```
