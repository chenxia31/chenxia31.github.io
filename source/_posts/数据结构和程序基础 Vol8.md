---
title: 数据结构和程序基础 Vol8
tags:
  - 数据结构
  - 程序基础
  - 算法思想
categories:
  - 程序设计
date: 2024-04-29 16:56:07
---

在前面稍微的完整了解的基础的线性表结构，知道数组、链表、队、栈、哈希表、堆之后；本来按照数据结构要学习高级数据结构字符串和二叉树，但是在学习KMP的时候对一些算法中的基本概念不清晰，同时需要对前一阶段中的排序、二分、双指针、优先队列、单调栈等进行总结，所以借此机会了解一下算法。算法中最基础的就是枚举或者说迭代，之后便是递归为基础的分治和回溯算法。

## 0x01 枚举算法（Enumeration Algorithm）

### 1.1  枚举算法简介

穷举算法，指的是按照问题的本身的性质，来列举出所有该问题可能存在的解，并在逐一枚举的过程中讲它们逐一和目标状态进行比较来得到满足问题要求的解

枚举算法的核心是需要列举问题的所有状态，并与目标状态进行比较。它的优点是，容易编程调试、算法的正确性容易证明；它的缺点是效率比较低，不适合求解规模较大的问题

### 1.2 枚举算法的思路

1. 确定枚举对象、 枚举范围和判断条件，并判断条件设立的正确性
2. 一一枚举可能的情况并验证是否是问题的解
3. 考虑提高枚举算法的效率

### 1.3  枚举算法的应用

[LC78 子集](https://leetcode.cn/problems/subsets/)

如果集合A的任意一个元素都是集合S的元素，则集合A事集合S的子集，枚举子集的方法很多，一种简单有效的枚举方法是二进制枚举子集算法

```
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        res=[]
        path=[]
        def backtracking(nums,index):
            res.append(path[:])
            if index>=len(nums):
                return 
            for i in range(index,len(nums)):
                path.append(nums[i])
                backtracking(nums,i+1)
                path.pop()
        backtracking(nums,0)
        return res
```

python 的位运算符；

& ：and运算，参与运算的两个值相应位为1，则结果为1

｜：or 运算，只要对应的两个二位有一个为1，结果就为1

^:  XOR运算，两个对应的相异的时候，结果为1

～：not运算，

<< 左移运算符，

```
class Solution:
    def subsets(self, S: List[int]) -> List[List[int]]:
        sub_sets=[]
        n=len(S)
        # 1<<n 相当于2^n次方，range(1<<n)相当于0～2^n-1
        for i in range(1<<n):
            sub_set=[]
            for j in range(n):
                # &1 相当于取最后一位
                if i>>j&1:
                    sub_set.append(S[j])
            sub_sets.append(sub_set)
        return sub_sets
```

[LC221 最大正方形](https://leetcode.cn/problems/maximal-square/)

需要用到一些简单的DP

```
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        # 最大面积的正方形
        # 积分图的方式
        max_len=0          
        m,n=len(matrix),len(matrix[0])
        dp=[]
        for i in range(m):
            sub_dp=[]
            for j in range(n):
                matrix[i][j]=int(matrix[i][j])
                if matrix[i][j]==1:
                    max_len=1
                sub_dp.append(matrix[i][j])
            dp.append(sub_dp)


        for i in range(1,m):
            for j in range(1,n):
                if matrix[i][j]==1 and dp[i-1][j]>=1 and dp[i][j-1]>=1 and dp[i-1][j-1]>=1:
                    dp[i][j]=min(dp[i-1][j-1],dp[i-1][j],dp[i][j-1])+1
                if dp[i][j]>max_len:
                    max_len=dp[i][j]
        return max_len*max_len
```

[LC204 计算质数](https://leetcode.cn/problems/count-primes/)

和以前在学校学习的就完全不一样，枚举最重要的不是要确定列举的对象，还需要确定剪枝的条件。所以本题可以有筛子的概念

```
class Solution:
    def countPrimes(self, n: int) -> int:
        is_prime = [True]*(n)
        ans = 0
        for num in range(2,n): 
            if is_prime[num]:
                ans+=1
                # 右边界:因为数字最大是n-1 所以只需要到(n-1)//num 右边是开区间 所以+1
                for k in range(1,(n-1)//num+1):
                    is_prime[num*k]=False
        return ans
```

## 0x02 递归算法

### 2.1 递归简介

是一种通过重复讲原问题分解为同类的子问题而解决的方法，在绝大数编程语言中，可以通过在函数中再次调用函数自身的方式来实现递归

简单的例子就是阶乘的计算

```
def fact(n):
    if n==0:
        return 1
    return n*fact(n-1)
```

递归可以分为两个部分：

1. （递推过程）先逐层向下调用自身，直到达到结束条件；指的是**将原问题一层一层分解为与原问题形式相同、规模更小的字问题，直到达到结束条件时停止，此时返回最底层问题的解**
2. 然后想上逐层返回结果，直到返回原问题的解；指的是**从最底层字问题的解开始，逆向逐一回归，最终达到递推开始的原问题，直到返回原问题的解**

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-be1d25f9c4aa77104cfb51ae7a4caa55_1440w.jpg)





image-20220731230242978

### 2.2  递推与数据归纳法

多米罗骨牌类似的数学归纳法的步骤是：

1. 证明当n==b的时候，基本情况下成立
2. 证明当n>b的时候，n=k成立的情况下，可以推导得出n=k+1成立

这个时候需要解决的递归就需要

1. 递归的终止条件
2. 递归的过程
3. 回归过程

### 2.3 递归的模版

### 2.3.1 递归公式

需要找到原问题分解称为子问题的规律，并且根据规律写出递推公式。这里的关键是需要找到原问题分解称为子问题的规律，并抽象称为递推公式。

注意，在思考递归公式的时候没有必要将整个递推过程和回归过程一层层的想清楚，这样可能还没有递推到栈底就已经绕晕了。重点在于想清楚n=k到n=k+1这个步骤，而不是n=i到n=i+1（i=1:m）；也就是找到递归过程中的循环不变量（loop variant），先假设子问题解决了，再看如何将原问题分解

### 2.3.2 终止条件

递归的终止条件也叫做递归出口，在写出递推公式之后，就需要考虑递归的终止条件是什么。通常条件下，递归的终止条件是问题的边界值

### 2.3.3 翻译成代码

第一步，定义递归函数，明确函数意义、input和output

第二步，根据循环不变量来推论得到递归公式

第三步，明确递归的终止条件

第四步，伪代码

```
def recursion(大规模）：
    if 终止条件：
        终止条件处理
    return recursion（小规模）
```

### 2.3.4 递归的注意点

避免栈溢出

避免重复运算

### 2.4 递归的应用

递归在使用的过程需要明确你的loop variant是什么，在每次运行过程中循环不变量是什么

[LC509 斐波那契数列](https://leetcode.cn/problems/fibonacci-number/)

[LC70爬楼梯](https://leetcode.cn/problems/climbing-stairs)

[LC344 反转字符串](https://leetcode.cn/problems/reverse-string/)

[LC24 交换节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        if head==None or head.next==None:
            return head
        dummy=ListNode(0,head)
        node1=head
        res=ListNode(0,head.next)
        node2=head.next
        while(node1!=None and node2!=None):
            # 交换两者
            node1.next=node2.next
            node2.next=node1
            dummy.next=node2

            # 更新node
            dummy=node1

            if node1.next==None:
                break
            node1=node1.next
            node2=node1.next
        return res.next
```

[LC119 杨辉三角](https://leetcode.cn/problems/pascals-triangle-ii/)

[LC104 二叉树最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        return max(self.maxDepth(root.left),self.maxDepth(root.right))+1
```

[LC226 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: TreeNode) -> TreeNode:
        if root == None:
            return root
        # 等于左边的反转 加上 右边的反转
        left=self.invertTree(root.left)
        right=self.invertTree(root.right)
        root.right=left
        root.left=right
        return root
```

[LC779 第K个语法符号](https://leetcode.cn/problems/k-th-symbol-in-grammar/)

```
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        if n == 0:
            return 0
        if k % 2 == 1:
            return self.kthGrammar(n-1, (k+1) // 2)
        else:
            return abs(self.kthGrammar(n-1, k // 2) - 1)
```

[LC95 不同的二叉搜索树II](https://leetcode.cn/problems/unique-binary-search-trees-ii/)

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def generateTrees(self, n: int) -> List[TreeNode]:
        if n==0:
            return []


        def recursionTree(left,right):
            # 生成left-right之间的树
            if left>right:
                return [None]
            trees=[]
            for i in range(left,right+1):
                left_trees=recursionTree(left,i-1)
                right_trees=recursionTree(i+1,right)
                for left_tree in left_trees:
                    for right_tree in right_trees:
                        curr_tree=TreeNode(i)
                        curr_tree.left=left_tree
                        curr_tree.right=right_tree
                        trees.append(curr_tree)
            return trees
        return recursionTree(1,n)
```

[offer62 圆圈中最后剩下的数字](https://leetcode.cn/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)

```
class Solution:
    def lastRemaining(self, n: int, m: int) -> int:
        ans = 0
        for i in range(2, n + 1):
            ans = (m + ans) % i
        return ans
```

## 0x03 分治法（Divide and Conquer)

这里只是简单的了解，以归并排序作为基础。理解一个算法的好处就是去模拟它

```
def mergeSort(arr):
    # 归并排序的基本思想：
    # 采用经典的分治策略，先递归将当前序列平均分成两半，然后将有序序列合并，最终合并成一个有序序列
    # 
    # 【算法步骤】
    # 1. 将数组中的所有数据堪称n有序的子序列
    # 2. 将当前序列组中的有序序列两两归并，完成一遍之后序列组里的排序序列的个数减版，每个子序列的长度加倍
    # 3. 重复上述操作得到一个长度为n的有序序列
    # 
    def merge(left_arr,right_arr):
        arr=[]
        while left_arr and right_arr:
            if left_arr[0]<=right_arr[0]:
                arr.append(left_arr.pop(0))
            else:
                arr.append(right_arr.pop[0])

        while left_arr:
            arr.append(left_arr.pop(0))

        while right_arr:
            arr.append(right_arr.pop(0))

        return arr

    size =len(arr)

    # 边界情况
    if size<2:
        return arr

    mid =size//2

    left_arr,right_arr=arr[0:mid],arr[mid:]
    return merge(mergeSort(left_arr),mergeSort(right_arr))
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-ec610d0d0eb9cdeb7f4333ae1dd33148_1440w.jpg)





IMG_C3B04D575C51-1

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-007394dc841d4af3d00b6956e35aa6f6_1440w.jpg)





img

## 0x04 回溯算法(Backtracking algorithm)

### 4.1  回溯算法简介

一种能够避免不必要搜索的穷举式的搜索算法，采用尝试错误的思想，在搜索尝试过程中寻找问题的解，当探索到某一步的时候，发现原先的选择并不满足求解条件、或者还需要满足更多的求解条件的的时候，就退回一步重新选择。这个过程中走不通就退回的技术称为回溯法、满足回溯条件的某个状态的点称为回溯点

回溯算法通常用简单的递归的方法来实现，在进行回溯的过程中可能会出现两种情况：

1. 找到一个可能存在的正确答案
2. 在尝试所有可能的分布方法之后宣布该问题没有答案

### 4.2  回溯算法的例子

比如求解1，2，3的全排列的问题：

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-0ceb278bc145c639c5efc3cfe0f54f32_1440w.jpg)





image-20220731231528233

```
def premute(nums):
    res=[] # 存放所有符合条件结果的集合
    path=[] # 存放当前符合条件的结果
    def backtracking(nums):
        if len(path)==len(nums):
            res.append(path[:])
            return

        for i in range(len(nums)):
            if nums[i] not in path:
                # 从当前路径中没有出现的数字中选择
                path.append(nums[i])
                # 递归搜索
                backtracking(nums)
                # 撤销选择
                path.pop()

    backtracking(nums)

    return res
print(premute([1,2,3,4]))
```

### 4.3 回溯算法的过程

回溯算法的解题步骤比较抽象，这里只是做一个简单的介绍

第一步：结合所给的问题，定义问题的求解空间，包括求解的组织形式和显性约束

1. 解的组织形式，将解的组织形式都规范称为一个n元祖
2. 显约束，对解分量的取值范围限定，用来控制解空间的大小

第二步：确定解空间的组织结构，解空间的组织借口通常用解空间树的方式形象表达，根据解空间树的不同，解空间分为子集树、排列树和m叉树

第三步：按照深度优先搜索策略，根据隐约束，在解空间中搜索问题的可行解或者最优解，当发现当前节点不满足求解条件时候回溯尝试其他的路径

上面的是解题的抽象逻辑，后面根据code书写回溯算法的步骤可以分为：

1. 明确所有的选择，画出搜索过程的决策树，根据决策树来确定搜索路径
2. 明确终止条件，推敲出递归的终止条件，以及递归终止时要执行的处理方法
3. 将决策树和终止条件翻译成代码

### 4.4 回溯算法的应用

初步的回溯算法是将现有的可能性分类，然后选择其中的一个递归到下一层，并允许返回后执行下一个可能性。重点在于每一层递归都会有N个可能性，所以更近一步需要考虑剪枝。当然需要先把回溯学会，才能学会剪纸

[LC46 全排列](https://leetcode.cn/problems/permutations/)

```
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        res=[]
        path=[]
        def bt(nums):
            if len(path)==len(nums):
                res.append(path[:])
                return 

            for i in range(len(nums)):
                if nums[i] not in path:
                    path.append(nums[i])
                    bt(nums)
                    path.pop()
        bt(nums)
        return res
```

[LC 47全排列II](https://leetcode.cn/problems/permutations-ii/)

[LC560 和为k的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

```
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        pre_dic = {0: 1}
        pre_sum = 0
        count = 0
        for num in nums:
            pre_sum += num
            if pre_sum - k in pre_dic:
                count += pre_dic[pre_sum - k]
            if pre_sum in pre_dic:
                pre_dic[pre_sum] += 1
            else:
                pre_dic[pre_sum] = 1
        return count
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-9a38d478e58a1fe5568a9c1d5c161cb1_1440w.jpg)





IMG_CF3CEA8976F6-1

[LC22 括号生成](https://leetcode.cn/problems/generate-parentheses/)

```
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        ans = []
        def backtrack(S, left, right):
            if len(S) == 2 * n:
                ans.append(''.join(S))
                return
            if left < n:
                S.append('(')
                backtrack(S, left+1, right)
                S.pop()
            if right < left:
                S.append(')')
                backtrack(S, left, right+1)
                S.pop()

        backtrack([], 0, 0)
        return ans
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-28c62ba51b3ecacb061da0f15cb859a0_1440w.jpg)





IMG_D7DDED1F4ECE-1

[LC 17 电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

```
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        num2letter=['abc','def','ghi','jkl','mno','pqrs','tuv','wxyz']
        n=len(digits)
        res=[]
        if not digits:
            return []
        def backtracking(S):
            if len(S)==n:
                res.append(''.join(S))
                return

            index=len(S)
            num=int(digits[index])
            print(num)
            for i in num2letter[num-2]:
                S.append(i)
                backtracking(S)
                S.pop()
        backtracking([])
        return res
```

[LC784 字幕大小写全排列](https://leetcode.cn/problems/letter-case-permutation/)

```
class Solution:
    def letterCasePermutation(self, s: str) -> List[str]:
        res=[]
        path=[]


        def backtracking(path,index):
            if index==len(s):
                # 所有的字母都遍历完毕
                res.append(''.join(path[:]))
                return

            char=s[index]
            if char.isdigit():
                # 如果是数字，直接加上去就行
                path.append(char)
                backtracking(path,index+1)
                path.pop()
            else:
                path.append(char.lower())
                backtracking(path,index+1)
                path.pop()
                path.append(char.upper())
                backtracking(path,index+1)
                path.pop()
        backtracking([],0)
        return res
```

[LC79 单词搜索](https://leetcode.cn/problems/word-search/)

暂时还不会hhh

```
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]

        def check(i: int, j: int, k: int) -> bool:
            if board[i][j] != word[k]:
                return False
            if k == len(word) - 1:
                return True

            visited.add((i, j))
            result = False
            for di, dj in directions:
                newi, newj = i + di, j + dj
                if 0 <= newi < len(board) and 0 <= newj < len(board[0]):
                    if (newi, newj) not in visited:
                        if check(newi, newj, k + 1):
                            result = True
                            break

            visited.remove((i, j))
            return result

        h, w = len(board), len(board[0])
        visited = set()
        for i in range(h):
            for j in range(w):
                if check(i, j, 0):
                    return True

        return False
```

[LC1079 活字印刷](https://leetcode.cn/problems/letter-tile-possibilities/)

```
class Solution:
    def numTilePossibilities(self, tiles: str) -> int:
        res=set()
        path=[]
        hashmap={}
        for i in range(len(tiles)):
            if tiles[i] in hashmap:
                hashmap[tiles[i]]+=1
            else:
                hashmap[tiles[i]]=1

        def backtracking(tiles):

            temp=''.join(path[:])
            if temp!='' and temp not in res:
                res.add(temp)

            for i in range(len(tiles)):
                while(hashmap[tiles[i]]==0):
                    i=i+1
                    if i ==len(tiles):
                        return 
                hashmap[tiles[i]]-=1
                path.append(tiles[i])
                backtracking(tiles)
                va=path.pop()
                hashmap[va]+=1
        backtracking(tiles)
        return len(res)
```

[LC93 复原IP地址](https://leetcode.cn/problems/restore-ip-addresses/)

```
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        res=[]
        path=[]
        flag=4
        def backtracking(s,index):
            temp=''.join(path[:])
            if len(temp)==len(s)+4:
                res.append(temp[:-1])
                return

            nonlocal flag
            for i in range(index+1,len(s)+1):
                if flag>0 and int(s[index:i])<=255:
                    if i!=index+1 and int(s[index])==0:
                        return 
                    path.append(s[index:i])
                    path.append('.')
                    flag-=1
                else:
                    return
                backtracking(s,i)
                path.pop()
                path.pop()
                flag+=1
        backtracking(s,0)
        return res
```

[LC51-N皇后](https://leetcode.cn/problems/n-queens/)

感觉N皇后是回溯里面最简单的一种了；确实比较经典

```
class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        res=[]
        path=[]
        def backtracking(n):
            if len(path)==n:
                res.append(path[:])
                return 

            for i in range(n):
                # 根据前面的path可以得出目前行能走的路线
                forbid=[]
                for j in range(len(path)):
                    forbid.append(path[j])
                    forbid.append(path[j]+len(path)-j)
                    forbid.append(path[j]+j-len(path))
                if i not in forbid:
                    path.append(i)
                    backtracking(n)
                    path.pop()
        backtracking(n)

        rest=[]
        # 翻译
        for i in range(len(res)):
            rest.append([])
            for j in range(len(res[0])):
                temp=['.' for _ in range(n)]
                temp[res[i][j]]='Q'
                #合成
                ts=''
                for k in temp:
                    ts+=k
                rest[-1].append(ts)
        return rest
```

## 0x05 总结（Conclusion）

1. 理解一个算法，并不像一个数据结构一样直观。所以模拟运行，一步一步的执行才能理解其中的一些关键点。只是单纯的看概念是不ok的
2. 无论是算法还是数据结构，学习的过程并不是一个创造性的过程，只是将计算机的单条指令执行的思维强加在人身上，再写出利用合适的数据结构和算法能得到一个解决方案
3. 分治和回溯的根基还是递归，理解递归需要理解循环不变量
4. 剪枝后面再看趴
