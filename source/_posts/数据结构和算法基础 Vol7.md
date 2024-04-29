---
title: 数据结构和算法基础 Vol7
tags:
  - 数据结构
  - 算法基础
  - 哈希表
categories:
  - 程序设计
abbrlink: 21aa2441
date: 2024-04-29 16:56:04
---

新的数据结构，查找速度贼快的哈希表！！

### s0x01 为什么我们需要哈希表？

首先我们细数之前学过的线性数据结构，其中最基本的分类为数组（Array）和链表（Linked list），按照我个人的看法，最大的区别在于数组按址取值的方式让其在索引速度很快、链表由于数据之间的隔离让其在插入修改等速度很快。

然后在这个基础上，我们可以人为的设计规则（FIFO或者LIFO）来更好的描述这个物理世界，也就是在数组和链表基础上所加的一层抽象（就像程序语言在寄存器基础上抽象一样），但是在学习优先队列的时候，发现堆（Heap）的出现是在数组和链表之间的Trade-off，它实现了查找的logN、修改删除的logN时间，发现非常的Amazing

所以为什么我们需要哈希表呢？因为我们需要实现值的查找

(Sedgewick and Wayne, 2021)在<算法，第四版>中的第三章（查找）中所描述的：

>  现代计算机和网络使我们能够访问海量的信息。高效检索这些信息的能力是处理它们的重要前 提。本章描述的都是数十年来在广泛应用中经过实践检验的经典查找算法。没有这些算法，现代信 息世界的基础计算设施都无从谈起。 我们会使用符号表这个词来描述一张抽象的表格，我们会将信息（值）存储在其中，然后按照 指定的键来搜索并获取这些信息。键和值的具体意义取决于不同的应用。符号表中可能会保存很多 键和很多信息，因此实现一张高效的符号表也是一项很有挑战性的任务。 符号表有时被称为字典，类似于那本将单词的释义按照字母顺序排列起来的历史悠久的参考书。 在英语字典里，键就是单词，值就是单词对应的定义、发音和词源。符号表有时又叫做索引，即书 本最后将术语按照字母顺序列出以方便查找的那部分。在一本书的索引中，键就是术语，而值就是 书中该术语出现的所有页码。 在说明了基本的 API 和两种重要的实现之后，我们会学习用三种经典的数据类型来实现高效的 符号表：二叉查找树、红黑树和散列表。在总结中我们会看到它们的若干扩展和应用，它们的实现 都有赖于我们在本章中将会学到的高效算法。

简单来说，就比如说我们需要删除[1,2,3,4]中3这个值，如果从数组的角度需要知道3对应的下标，就会比较麻烦，所以我们希望借助哈希表的方式来快速找到这个值

### 0x02 哈希表和哈希映射

**哈希表（Hash Table），**通过键 key和一个映射函数 Hash(key)计算出对应的值 value ，把关键码值映射到表中一个位置来访问记录，以加快查找的速度。

举个例子，我们通过value=Hash(key)=key//1000作为哈希函数，由此可以实现插入和查找：

1. 比如插入0138，我们可以通过哈希函数计算出value=0，然后分配到0对应的区块中
2. 查找2321，通过哈希函数可以得到2，在2对应的区块中寻找就可以了

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-6930ab429cd2525cb7425f6113a5d6dd_1440w.jpg)





image-20220720234945173

在这个过程中最终的点在**哈希函数(Hash Function)\*****和\*****哈希冲突（Hash Collision）**,首先看哈希函数，本质上是一个**多对一的映射，要求：**

1. 容易计算的，并且计算的索引值可以均匀分布
2. 所得到的哈希值是一个固定长度的输出
3. 多对一，如果Hash（key1）！=Hash（key2），则key1和key2一定不想等
4. 如果Hash（key1）==Hash（key2—），则两者可能相同，也可能不同（冲突）

上述第4点中的不同key得到相同的hash（key）就是哈希冲突（Hash Collision），这也是好理解的有得必有失，会有针对哈希冲突的优化。这就需要精妙的设计能力了。

### 2.1 哈希函数的方法

- 直接定址法
- 除留余数法
- 平方折中法
- 基数转换法
- 等等

这里大部分不会用的，需要了解的可以配合搜索引擎使用

### 2.2 哈希冲突的解决

- 开放地址法（Open Addressing）

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-783dbc8eb070f698dda037290cdce696_1440w.jpg)





image-20220720234955234

- 链地址法（Chaining）-- 用python会很简单

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-7f38deb879d012ebac05ef20d9cd511f_1440w.jpg)





image-20220720235004945

### 0x03 哈希表和哈希映射的设计

[T705 设计哈希集合](https://leetcode.cn/problems/design-hashset/)

```
class MyHashSet:

    def __init__(self):
        self.set=[[] for _ in range(1000)]

    def add(self, key: int) -> None:
        index=key%1000
        for i in range(len(self.set[index])):
            if self.set[index][i]==key:
                break
        self.set[index].append(key)
        return self.set

    def remove(self, key: int) -> None:
        index = key%1000
        for i in range(len(self.set[index])):
            if self.set[index][i]==key:
                self.set[index][i]=-1
        return self.set

    def contains(self, key: int) -> bool:
        index=key%1000
        for i in range(len(self.set[index])):
            if self.set[index][i]==key:
                return True
        return False
```

[T706 设计哈希映射](https://leetcode.cn/problems/design-hashmap/)

```
class MyHashMap:

    def __init__(self):
        self.N=1000
        self.hashMap=[[] for _ in range(self.N)]

    def put(self, key: int, value: int) -> None:
        index=key%self.N
        for values in self.hashMap[index]:
            if values[0]==key:
                # 更新值
                values[1]=value
                return None
        self.hashMap[index].append([key,value])

    def get(self, key: int) -> int:
        index = key % self.N
        for values in self.hashMap[index]:
            if values[0]==key:
                return values[1]
        return -1

    def remove(self, key: int) -> None:
        index = key% self.N
        for i in range(len(self.hashMap[index])):
            if self.hashMap[index][i][0]==key:
                self.hashMap[index].pop(i)
                return None
        return -1
```

### 0x04 哈希表的应用

比较有意思的是原地哈希

[T217 存在重复元素](https://leetcode.cn/problems/contains-duplicate/)

[T219 存在重复元素II](https://leetcode.cn/problems/contains-duplicate-ii/)

[T220 存在重复元素III](https://leetcode.cn/problems/contains-duplicate-iii/)

这里不能简单的使用之前的哈希函数，因为我不能在一个桶里面比较，我还需要和其他桶对比

```
class Solution:
    def containsNearbyAlmostDuplicate(self, nums: List[int], k: int, t: int) -> bool:
        bucket_dict = dict()
        for i in range(len(nums)):
            # 将 nums[i] 划分到大小为 t + 1 的不同桶中
            num = nums[i] // (t + 1)

            # 桶中已经有元素了
            if num in bucket_dict:
                return True

            # 把 nums[i] 放入桶中
            bucket_dict[num] = nums[i]

            # 判断左侧桶是否满足条件
            if (num - 1) in bucket_dict and abs(bucket_dict[num - 1] - nums[i]) <= t:
                return True
            # 判断右侧桶是否满足条件
            if (num + 1) in bucket_dict and abs(bucket_dict[num + 1] - nums[i]) <= t:
                return True
            # 将 i-k 之前的旧桶清除，因为之前的桶已经不满足条件了
            if i >= k:
                bucket_dict.pop(nums[i-k] // (t + 1))

        return False
```

[T136 只出现一次的数字](https://leetcode.cn/problems/single-number/)

[T001 两数之和](https://leetcode.cn/problems/two-sum/)

第一道题，很经典

```
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        headMap={}
        for i in range(len(nums)):
            if nums[i] in headMap:
                return [i,headMap[nums[i]]]
            headMap[target-nums[i]]=i
```

[T015 三数之和](https://leetcode.cn/problems/3sum/)

重大打击的一题，也很经典

```
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        res=[]
        def twoSum(arr,target):
            left=0
            right=len(arr)-1

            while(left<right):
                temp_sum=arr[left]+arr[right]
                if temp_sum==target:
                    if left>0 and arr[left]==arr[left-1]:
                        left+=1
                    elif right<len(arr)-1 and arr[right]==arr[right+1]:
                        right-=1
                    else:
                        res.append([target*(-1),arr[left],arr[right]])
                        left+=1
                        right-=1
                elif temp_sum<target:
                    left+=1
                elif temp_sum>target:
                    right-=1
        nums.sort()

        for i in range(len(nums)):
            if nums[i]>0:
                break
            if i>0 and nums[i]==nums[i-1]:
                continue
            twoSum(nums[i+1:],(-1)*nums[i])        
        return res
```

[T454 四数相加II](https://leetcode.cn/problems/4sum-ii/)

大事化小

```
class Solution:
    def fourSumCount(self, nums1: List[int], nums2: List[int], nums3: List[int], nums4: List[int]) -> int:
        hashMap1={}
        for num1 in nums1:
            for num2 in nums2:
                if num1+num2 in hashMap1:
                    hashMap1[num1+num2]+=1
                else:
                    hashMap1[num1+num2]=1

        count=0

        for num3 in nums3:
            for num4 in nums4:
                if -(num3+num4) in hashMap1:
                    count+=hashMap1[-(num3+num4)]

        return count
```

[T442 数组中重复的数据](https://leetcode.cn/problems/find-all-duplicates-in-an-array/)

很有意思

```
class Solution:
    def findDuplicates(self, nums: List[int]) -> List[int]:
        # 依旧使用原地哈希
        # hashtable(key)=key-1
        # 数组中只会存在两种情况，一种是正常映射、另外一种是重复的，每次交换的终止条件是
        # 1. i处的正常映射
        # 2. i处与nums[i]出现重复
        for i in range(len(nums)):
            while nums[i]!=i+1 and nums[i]!=nums[nums[i]-1]:
                nums[nums[i]-1],nums[i]=nums[i],nums[nums[i]-1]

        return [num for i, num in enumerate(nums) if num - 1 != i]
```

[T149 直线上最多的点数](https://leetcode.cn/problems/max-points-on-a-line/)

需要注意python运算的时候，小数的问题，比如0.1+0.2=0.333334；结合贪心的思想使用更佳

```
class Solution:
    def maxPoints(self, points: List[List[int]]) -> int:
        n = len(points)
        if n < 3:
            return n
        ans = 0
        for i in range(n):
            line_dict = dict()
            line_dict[0] = 0
            same = 1
            for j in range(i+1, n):
                dx = points[j][0] - points[i][0]
                dy = points[j][1] - points[i][1]
                if dx == 0 and dy == 0:
                    same += 1
                    continue
                gcd_dx_dy = math.gcd(abs(dx), abs(dy))
                if (dx > 0 and dy > 0) or (dx < 0 and dy < 0):
                    dx = abs(dx) // gcd_dx_dy
                    dy = abs(dy) // gcd_dx_dy
                elif dx < 0 and dy > 0:
                    dx = -dx // gcd_dx_dy
                    dy = -dy // gcd_dx_dy
                elif dx > 0 and dy < 0:
                    dx = dx // gcd_dx_dy
                    dy = dy // gcd_dx_dy
                elif dx == 0 and dy != 0:
                    dy = 1
                elif dx != 0 and dy == 0:
                    dx = 1
                key = (dx, dy)
                if key in line_dict:
                    line_dict[key] += 1
                else:
                    line_dict[key] = 1
            ans = max(ans, same + max(line_dict.values()))
        return ans
```

[T811 子域名的访问计数](https://leetcode.cn/problems/subdomain-visit-count/)

学会python的字符串操作还是很有用的

```
class Solution:
    def subdomainVisits(self, cpdomains: List[str]) -> List[str]:
        ans={}
        for domain in cpdomains:
            count,domains=domain.split()
            count=int(count)

            subdomains=domains.split('.')
            temp=''
            for subdomain in subdomains[::-1]:
                if temp:
                    temp=subdomain+'.'+temp
                else:
                    temp=subdomain
                if temp in ans:
                    ans[temp]+=count
                else:
                    ans[temp]=count
        res=[]
        for key,value in ans.items():
            res.append(str(value)+' '+key)
        return res
```
