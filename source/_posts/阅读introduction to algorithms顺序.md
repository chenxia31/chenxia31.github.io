---
title: 阅读introduction to algorithms顺序
tags:
  - 算法导论
categories:
  - 程序设计
abbrlink: a3ed2269
date: 2024-04-29 16:09:10
---

一下是原文地址 ，仅翻译其中的英文部分，供参考，这是目录 

如何正确地撸《算法导论》？1562 赞同 · 43 评论回答

第一章

作为兴趣阅读，可以跳过

第二章

2.1 insertion sort ，这是最基础的内容，实际上最好可以了解所有主要的排序算法

2.2 Analysis of Algorithms 除了导言部分都需要知道

2.3 Designing Algorithms 包含merge sort方法和分析，以及divide-and- conquer（分而治之）的概述，非常重要，值得阅读

第三章

所有的部分都需要知道，这是关于时间复杂度分析部分

第四章

4.1 maximum subarray problem ，值得阅读，书中使用divide-and- conquer方法虽然不是最高效的，但是作为练习和理解divide-and- conquer的思想很有用

4.2 Strassen‘s algorithm，很美妙的算法，令人震惊

4.3 substitution method，可能面试的时候不会用到，但是这是最基础的工具去寻找递归算法的时间复杂度

4.4 递归树分析，和4.3一样重要

4.5 master method，重要到最好可以深刻到潜意识里，面试中需要即时使用在算法的时间度分析中

4.6 proof of the master theorem，可以跳过，但是阅读可以帮助理解之前学习的master method

第五章

并未阅读这一章节，因为过于偏向数学而不是算法

第六章

6.1～6.5 heaps and heapsort

第七章

7.1、7.2、7.3 quick sort and its recommend version，需要了解概念

7.4 值得了解

第八章

8.1 lower bounds on sorting，这是非常基础的内容，需要掌握

8.2 counting sort 需要详细了解细节，会有很多变种的问题

8.3 radix sort 非常简单的算法

8.4 bucket sort 可以跳过

第九章

9.1 small section 值得阅读

9.2 selection in expected linear time 非常重要！！

9.3 selection in worst-case linear time 可以跳过，只需要明白worst-case  linear time在某些情况下需要

第十章

10.1 stacks and queues，基本的知识，非常重要

10.1 linked list 非常重要

10.3 implementing pointers and objects 看你编程能力

10.4 representing rooted trees 小章节，可以略读

第十一章

对于哈希表，了解其基本概念比实现它更重要。

11.1 direct addressing 仅需要明白概念

11.2 hash tables 非常重要

11.3 hash function 了解基本概念很重要，但是不值得深入挖掘，了解常见的函数例子

11.4 open addressing 具有相关的概念很重要

11.5 perfect hashing 可以跳过

第十二章

12.1 what is a binary search tree？需要

12.2 querying a BST 需要全部阅读

12.3 insertion and deletion 需要阅读

12.4 randomly built BSTs 只需要明白定理12.4

第十三章

仅需要了解red-black tree和最坏情况下height/insert/delete/find是什么，其他可以跳过

第十四章

值得浏览14.2去了解不同数据结构为什么会有用，或者可以做一两个例子，可以跳过14.1 and 14.3

第十五章

DP （dynamic programming），必须知道

15.1 rod- cutting DP编程的开始，必须知道

15.2 matrix- chain- multiplication，和15.1一样

15.3 elements of DP，值得阅读去清晰理解DP的思想，而不是仅仅知道什么是DP或者去实现它

15.4 LCS 必须知道

15.5 optimal binary search tree 并没阅读，不好评价

第十六章

你需要清晰明白greedy algorithm（贪心算法）是什么，所以需要阅读引言

16.1 an activity selection problem 并没有详细阅读，略读

16.2 elements of the greedy strategy 同16.1

16.3 Huffman code，很有名的编码方式

16.4 task- scheduling problem as a matroid

第十七章

了解amortized analysis方法很重要，可以通过Google了解基本的概念，或者阅读17.1

17.2～17.4可以跳过

第十八章

明白B- tree是在解决更高难度的问题很有用，但是你可以仅仅知道相关的基本概念，本章可以跳过

第十九章

Fibonacci heaps 不需要阅读

第二十章

不需要

第二十一章

最初认为不重要，但是深思熟虑之后，推荐阅读21.1和21.2，其余的可以跳过

第二十二章

22.1 representation of graphs 需要

22.2 BFS需要

22.3 DFS需要

22.4 Topological sort需要

22.5 strongly connected components 虽然没有以上四种运用的频繁，但还是值得阅读

第二十三章

minimum spanning trees 可能是最不重要的图算法，处理max flow（仅对于面试来说）

但是这依旧值得阅读因为这是一个广为人知的问题

23.1 growing a mst

23.2 prim and Kruskal

第二十四章

shortest path非常重要，需要阅读导言

24.1 bellman-ford 了解算法和正确性的证明

24.2 shortest path in DAGs 值得阅读

24.3 Dijkstra ，非常重要

24.2 可以跳过

第二十五章

阅读引言非常重要

25.1 matrix multiplication 有空闲时间值得阅读

25.2 Floyd- warsshall 明白算法和分析

25.3 可以跳过

第二十六章

可以跳过
