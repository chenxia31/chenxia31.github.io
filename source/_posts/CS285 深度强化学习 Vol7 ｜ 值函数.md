---
title: CS285 深度强化学习 Vol7 ｜ 值函数
tags:
  - Q函数
  - 价值学习
  - 深度学习
  - 强化学习
  - CS285
categories:
  - 算法基础
date: 2024-04-29 17:14:11
---

在经过Lecture04介绍RL的基本概念之后，Lecture05介绍基于policy的方法，我们直接利用return的梯度进行策略学习，之后我们尝试利用Q或者V来改进policy evaluation步骤，那么我们是否可以直接抛弃参数化的梯度（parameterized policies），转向仅仅利用Q或者V进行RL

### 0x01 抛弃Actor- critic中的Actor

### 1.1 回顾Actor- critic

第一步：我们利用现有的policy得到多个采样轨迹$\tau_i$  第二步：利用数据拟合$\hat V_\phi^\pi(s)$（常见用蒙特卡洛方法、bootstrapped、n-step等方法） 第三步：评估优势策略A 第四步：根据优势策略得到Actor的梯度 第五步：更新Actor的梯度

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-3033b37cb9c3a8a28f6091af830e22e9_1440w.png)





image-20221102214616955

但是这样存在的潜在问题是方差太大，因为我们拟合出来的state- value- function方差太大，通常我们会做经验重放等方法，但是并不能完全解决这个问题。

所以为什么我们不直接丢到有参的policy，转向生成一个$\pi*$ 𝜋∗=𝑎𝑟𝑔𝑚𝑎𝑥𝑎𝑡𝐴𝜋(𝑠𝑡,𝑎𝑡) 

### 1.2 Policy Evaluation

在探讨上述可能性我们需要先了解一下policy evaluation的概念；也就是从Dynamic programming的来看我们会如何求解问题；同时从policy evaluation我们会逐渐走向value evaluation。

第一步：首先状态转移概率T是均匀的，那么我们可以在Policy evaluation()中得到每个状态下的Q-state-action-list；求平均来得到V-state-list。当然一次计算不够，我们会设置一个epsilon，当V- state变化的最大值都小于这个epsilon的时候停止更新。说明我们得到所有状态下的V- state 第二步：在得到V- state下我们便能判断什么action是好的，那么我们就利用Q- state- action与V- state之间的关系来选择不同state下的最优action，重新分配概率，也就是policy evaluation 第三步：由于policy evaluation会重新改变空间。V- state也会改变，所以需要重新计算Policy evaluation，直到收敛。

Policy evaluation与Value evaluation之间的区别在于Policy evaluation中根据Q- state- value-list得到V- state-list是求期望，还是求最大值的方式。

 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-63aeb1bf1dce8628c0391bdaa8659fab_1440w.png)





image-20221102221107185

 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-78b90f5955c86ad937e3d2caa2647ccc_1440w.png)





image-20221102221124987

这里参考，可以很清晰的看出policy evaluation，以及value evaluation对其做了什么简化

[动手学强化学习- Dynamic Programming](https://hrl.boyuai.com/chapter/1/动态规划算法)

### 0x02 Fitted value  iteration and Q- iteration

### 2.1 我们如何表示state- value- function V(s)

在数量较少的离散中我们可以使用表格或者什么来记录，但是对于连续空间我们可能无能为力。所以我们可以使用**神经网络来实现两者的近似**，也就是转换为

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-264ab90af8cbfaa5152a1cb8372aa29a_1440w.png)





image-20221102221230276

### 2.2 仍然有问题

因为在离散状态下，我们需要知道状态概率转移方式。我们会假设一个平均概率再重新分配的方式来更新转移概率。但是在连续情况下虽然我们知道拟合拟合出state- value- function，但是我们还不知道状态转移概率，所以我们求解不出state- value- function

但是为什么我们需要state- function？直接利用state- action- function来取最大值就行

 

但是这样我们并不能保证收敛性

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-0cf7ec47a81ba7ba8287e03a1735a530_1440w.png)





image-20221102221836862

### 0x03 Q- learning method

### 3.1 Q-iteration 的一些性质

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-78738f638c702cb23ee96b6efd10b0ab_1440w.png)





image-20221102222232800

1. 它是off- policy的
2. 等价优化bellman error

### 3.2 Online Q- learning

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-5c9870b1f223e0735e7df3adb2314ecc_1440w.png)





image-20221102222533482

通常会需要一些exploration

1. $\epsilon-greedy$
2. Boltzman exploration

### 0x04 Value Functions in Theory

我们需要尝试解释之前取最大值的方式是否可以收敛（converge）

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-6270cc134f6c0bac989188dcf7e86787_1440w.png)





image-20221102222722182

1. MDP中的bellman一定是收敛的
2. 在利用近似网络（function approximation）的方式下并不收敛

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-322370ad4b907e3ec3dbb7163ab1b4f8_1440w.png)





image-20221102223053608

我们得到一个非常sad corollary，但是在下一张的DQN中可以让它变的更好
