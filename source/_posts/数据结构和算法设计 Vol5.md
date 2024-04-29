---
title: 数据结构和算法设计 Vol5
tags:
  - 数据结构
  - 算法设计
  - 数组
categories:
  - 程序设计
abbrlink: 3d7504b6
date: 2024-04-29 16:56:01
---

## 0x01   数组排序

### 1.1 冒泡排序

```
def bubbleSort(arr):
    # 冒泡排序的思想
    # 相邻元素之间的比较和变换，将值较小的元素逐步从后面移到前面，值较大的元素从前面移到后面
    # 
    # 冒泡排序的步骤
    # 逐步将i和i+1元素相比较，如果大小不合适则交换，这样重复一次可以保证下标为n的值最大
    # 之后对n-2元素重复操作，一直到排序结束
    # 
    for i in range(len(arr)):
        for j in range(len(arr)-i-1):
            if arr[j]>arr[j+1]:
                arr[j],arr[j+1]=arr[j+1],arr[j]

    return arr
```

\## 1.2 选择排序

```
def selectSort(arr):
    # 选择排序的思想
    # 每一次排序中，从剩余未排序元素中选择一个最小的元素，未排好序的元素最前面的那个元素交换位置
    # 
    # 选择排序算法步骤
    # 在算法中设置整型变量i，既可以作为排序树木的计算、同时i也作为执行第i次排序的时候，参加排序的后n-i+1元素的位置
    # 整型变量 min_i记录最小元素的下标
    # 结束之中交换两者之间的顺序
    for i in range(len(arr)-1):
        min_i=i
        for j in range(i+1,len(arr)):
            if arr[j]<arr[min_i]:
                min_i=j

        if i!=min_i:
            arr[i],arr[min_i]=arr[min_i],arr[i]

    return arr
```

### 1.3 插入排序

```
def insertSort(arr):
    # 插入排序的基本思想
    # 每一次排序中，将剩余无序列序列的第一个元素，插入到有序序列的适当位置上
    # 
    # 插入排序的基本步骤
    # 将第一个元素看作一个有序序列
    # 从头到尾扫描无序序列，将扫描到的每个元素插入到有序序列的适当位置上
    for i in range(1,len(arr)):
        temp=arr[i]
        j=i
        # 0-（i-1）都是有序数组
        while j>0 and arr[j-1]>temp:
            arr[j]=arr[j-1]
            # 因为肯定需要移动一个位置
            j-=1
        arr[j]=temp

    return arr
```

### 1.4 希尔排序（没搞懂干什么）

```
def shellSort(arr):
    # 希尔排序的基本思想
    # 按照一定的间隔取值划分为若干个子序列，每个子序列按照插入排序，然后逐渐缩小间隔进行
    # 下一轮划分子序列和插入排序，一直到最后一轮排序间隔为1
    # 
    # 希尔排序是在插入排序的基础上进行改进的，因为我们可以看出插入排序在已经排好序的效率非常高
    # 但是插入排序效率比较低的原因是每次只能将数据移动以为
    # 
    # 希尔排序的算法步骤
    # 1. 确定元素间隔Gap，将序列按照1开始划分为若干个子序列，之间元素的间隔为一个gap
    # 2. 减少间隔数，并重新将整个序列按照新的间隔数分成若干个子序列，并对每个子序列进行排序
    # 
    # 
    size=len(arr)
    gap=size//2

    while gap>0:
        for i in range(gap,size):
            temp=arr[i]
            j=i
            while j>=gap and arr[j-gap]>temp:
                arr[j]=arr[j-gap]
                j-=gap
            arr[j]=temp
        gap=gap//2
    return arr
```

### 1.5 归并排序（很喜欢的排序）

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

### 1.6 快速排序（第二喜欢）

```
def quickSort(arr,low,high):
    # 快速排序，排序的边界条件 low比high小

    def resort(arr,low,high):
        # 这里的数组中，目标数已经放到的最右边
        # 现在需要把大于目标值的数据放到右边

        flag=low-1

        for j in range(low,high+1):
            # 双指针
            if arr[j]<=arr[high]:
                flag+=1
                arr[flag],arr[j]=arr[j],arr[flag]

        return flag


    def random_index(arr,low,high):
        # 当然还需要进行一些小的操作
        index=(low+high)//2

        # 默认左边都是最小的
        arr[index],arr[high]=arr[high],arr[index]
        return resort(arr,low,high)

    if low<high:
        # 每次按照队列排序
        # 需要返回标杆的下标
        index =random_index(arr,low,high)

        # 之后还需要继续分解
        quickSort(arr,low,index-1)
        quickSort(arr,index+1,high)
    return arr
```

### 1.7 堆排序（没看懂，感觉太难了）

```
def heapSort(arr):
    # 借用堆结构所设计的排序算法，将数组转换为大顶堆，重复从大顶堆中取出数值最大的节点，并让剩余的堆维持大顶堆的性质
    # 
    # 【堆的定义】
    # 大顶堆，根节点值大于子节点值
    # 小顶堆，根节点值小于等于子节点值
    # 
    # 【算法步骤】
    # 1. 首先将无序序列构造成第1个大顶堆，使得m个元素的最大值在序列的第一个值
    # 2. 交换序列的最大值元素与最后一个元素的位置
    # 3. 将前面n-1元素组成的序列调整称为一个新的大顶堆，这样得到第2个最大值元素
    # 4. 如此循环下去，知道称为一个有序序列
    # 
    arrLen =len(arr)

    def heapify(arr,i):
        left=2*i+1
        right=2*i+2
        largest=i
        if left<arrLen and arr[left]>arr[largest]:
            largest=left
        if right<arrLen and arr[right]>arr[largest]:
            largest=right

        if largest!=i:
            arr[i],arr[largest]=arr[largest],arr[i]
            heapify(arr,largest)
        print(arr)

    def buileMaxHeap(arr):
        for i in range(len(arr)//2,-1,-1):
            heapify(arr,i)

    buileMaxHeap(arr)
    print('buil')
    for i in range(arrLen-1,0,-1):
        arr[0],arr[i]=arr[i],arr[0]
        arrLen-=1
        heapify(arr,0)

    return arr
```

### 1.8 计数排序(没意思)

```
def countingSort(arr):
    # 【基本思想】
    # 使用一个额外的数组counts，其中counts元素是排序数组中arr等于i的个数
    # 根据数组counts来将arr的元素排列到正确位置
    min_arr,max_arr=min(arr),max(arr)

    counts =[0 for _ in range(max_arr-min_arr+1)]

    for num in arr:
        counts[num-min_arr]+=1

    for j in range(1,max_arr-min_arr+1):
        counts[j]+=counts[j-1]

    res=[0 for _ in range(len(arr))]
    for i in range(len(arr)-1,-1,-1):
        res[counts[arr[i]-min_arr]-1]=arr[i]
        counts[arr[i]-min_arr]-=1
    return res
```

### 1.9 基数排序（很有意思，注意有负数的情况）

```
def raidxSort(arr):
    # 基数排序radix sort【基本思想】
    # 将整数按照位切割称为不同的数字，然后按照个位数来从小到达进行排列
    # 注意这个排序方式是先比较个位数，然后逐渐向高位数

    # 首先需要了解到基数排序中最大位数
    max_radix=0
    for num in arr:
        if num>0:
            temp=len(str(num))
            if max_radix<temp:
                max_radix=temp
        else:
            temp=len(str(num))-1
            if max_radix<temp:
                max_radix=temp

    for radix in range(max_radix):
        # 从最低位到最高位开始计算
        # 按照位数来生成
        buckets=[[] for _ in range(10)]
        # 按照个位数，放到每个篮子，再按照十位数放到篮子
        for num in arr:
            # 提取radix对应的位数，0代表个位
            # 这里使用转换为字符串的方式来方便理解

            if len(str(abs(num)))<(radix+1):
                # 位数不够的时候，说明这个位置没有0，就需要放到第一格
                buckets[0].append(num)
            else:
                index=str(abs(num))[len(str(abs(num)))-1-radix]
                buckets[int(index)].append(num)
        # 提取到buckets之后需要重新解析arr中
        arr.clear()
        for bukcet in buckets:
            for num in bukcet:
                arr.append(num)
    neg_arr=[]
    pos_arr=[]  

    for num in arr:
        if num>0:
            pos_arr.append(num)
        else:
            neg_arr.append(num)
    return neg_arr[::-1]+pos_arr
```

### 1.10 桶排序（听着就没意思）

跳过

排序相关的题目：

[189 轮转数组](https://leetcode.cn/problems/rotate-array/)

[66 加一](https://leetcode.cn/problems/plus-one/)

[724 寻找数组中心下标](https://leetcode.cn/problems/find-pivot-index/)

[485 最大连续1的个数](https://leetcode.cn/problems/max-consecutive-ones/)

[238除自身以外乘积](https://leetcode.cn/problems/product-of-array-except-self/)

[498对角线遍历](https://leetcode.cn/problems/diagonal-traverse/)

[48旋转图像](https://leetcode.cn/problems/rotate-image/)

[118 杨辉三角](https://leetcode.cn/problems/pascals-triangle/)

[119 杨辉三角2](https://leetcode.cn/problems/pascals-triangle-ii/)

[73矩阵置零](https://leetcode.cn/problems/set-matrix-zeroes/)

[54螺旋数组](https://leetcode.cn/problems/spiral-matrix/)

[59 螺旋数组2](https://leetcode.cn/problems/spiral-matrix-ii/)

[移动零](https://leetcode.cn/problems/move-zeroes/)

[215数组第K大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

[75颜色分类](https://leetcode.cn/problems/sort-colors/)

[506 相对名次](https://leetcode.cn/problems/relative-ranks/)

[912排序数组](https://leetcode.cn/problems/sort-an-array/)

[88合并两个有序数组](https://leetcode.cn/problems/merge-sorted-array/)

[169 多数元素](https://leetcode.cn/problems/majority-element/)

[最大间距](https://leetcode.cn/problems/maximum-gap/)

## 0x02 二分查找

注意二分查找不同边界条件可以求解不同的问题，可以简单的查找数组中存在元素的位置，也可以查找第一个大于等于数组的下标，也可以查找最后一个小于等于该数据的下标，也可以查询相关的元素的值

```
# ====================================================================#
# ==========================二分查找====================================#
# 
# 【算法概述】
# 确定待查找元素所在的区间范围，再逐步缩小范围，直到找到元素或者找不到该元素为止
# 
# 【算法思想】
# 经典的减而治之的思想，减小问题规模来解决问题，每一次查找排除掉一定不存在目标元素的
# 区间，在剩下可能存在的目标元素的区间中继续查找，每一次通过一些条件判断，将待搜索的
# 区间逐渐缩小，来达到减少问题规模的目的
# 
# 【算法过程】
# 1. 每次查找从数组的中间元素开始，如果中间元素正好是查找的元素，则搜索过程结束
# 2. 如果特定元素大于或者小于中间元素，则在大于或者小于的元素中查找
# 3. 如果在某一步骤数组为空则代表找不到
# 
# 【算法重点】
# 1. 区间的开闭问题？
# 2. mid的取值问题
# 3. 出界条件的判断？
# 4. 搜索区间的范围选择问题？
# =====================================================================#
# 

def baseBinarySearch(nums,target):
    # nums是一个升序
    # 存在下标就返回
    # 不存在就返回-1
    # 因为要返回下标，所以采用index采用
    left=0
    right=len(nums)-1
    #=========================================#
    # 【二分查找的开闭问题】
    # 第一种：左闭右闭，区间中所有的点都可以得到
    # 第二种：左闭右开，右边界的点不能被取到
    #=========================================#

    while(left<=right):
    #=========================================#
    # 【出界条件的判断】
    # left<=right
    # 说明查找的元素不存在
    # left<right
    # 此时查找的区间不
    # 
    #=========================================#
        mid=(left+right)//2
    #=========================================#
    # 【MID的取值问题】
    # 第一种：(left+right)//2
    # 第二种：left+（right-left）//2
    # 前者是常见写法，后者是为了防止整型溢出。//2的代表的中间数是向下取整
    # 同时这种倾向于寻找左边的数组，这里可以选择
    # mid=(left+right+1)//2或者mid=left（right-left+1）//2
    # 同时这里的查找过成功1/2，也可以选择靠左一点、或者靠右一点
    # 从一般的意义上来说，趣中间位置元素在平均意义中达到的效果最好
    #=========================================#
        if nums[mid]<target:
    #=========================================#
    # 【搜索区间的选择】
    # 直接法，在循环体中元素之间返回
    # 排除法，在循环体中排出目标元素一定不存在区间
    #=========================================#

            # 说明target在右边
            left=mid+1
        elif nums[mid]>target:
            # 说明target在左边
            right=mid-1
        else:
            return mid

    return -1

nums=[1,1,2,3,4,44]
target=4
# print(baseBinarySearch(nums,target))
```

[704 二分查找](https://leetcode.cn/problems/binary-search/)

[374 猜数字大小](https://leetcode.cn/problems/guess-number-higher-or-lower/)

[35 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

[34 排序数组中查找元素的第一个和最后一个元素](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

[167两数之和](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)

[153 寻找旋转数组最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

[74 搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)

[x的平方根](https://leetcode.cn/problems/sqrtx/)

## 0x3 双指针

```
# ====================================================================#
# ==========================数组双指针====================================#
# 
# 【算法概述】
# 在遍历元素的过程中，不是使用单个指针进行访问，而是使用双指针来访问达到目的
# 
# 【算法思想】
# 根据指针的方向，可以分为
# 对撞指针：两个指针的方向相反
# 快慢指针：指针方向相同
# 分离双指针：如果两个指针分别属于不同的数组或者链表
# 
# =====================================================================#
# 
def threeSum(nums):
    res=[]
    def twoSum(arr,target):
        left=0
        print(arr)
        print(target)
        right=len(arr)-1

        while(left<right):
            temp_sum=arr[left]+arr[right]
            if temp_sum==target:
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
nums = [-1,0,1,2,-1,-4]
# print(threeSum(nums))
# 
#
```

[167 两数之和](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted)

[344 反转字符串](https://leetcode.cn/problems/reverse-string)

[345反转字符串中的元音字母](https://leetcode.cn/problems/reverse-vowels-of-a-string)

[11 最多的容器](https://leetcode.cn/problems/container-with-most-water)

[15三数之和](https://leetcode.cn/problems/3sum)

[16最接近的三数之和](https://leetcode.cn/problems/3sum-closest)

## 0x04 滑动窗口

下周任务

## 0x05

简单题中也会蕴含着一些基本的操作

比如设置一些标志位、边界条件的设计、辅助元素的设置，这些需要潜移默化的刷题的过程中才能看到
