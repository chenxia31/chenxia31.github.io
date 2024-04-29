---
title: Rainbow_Combining_Improvements_in_Deep_Reinforcement_Learning categories
tags:
  - Paper 阅读
  - 强化学习
categories:
  - 算法基础
date: 2024-04-29 17:14:13
---

作者：Hessel, M., Modayil, J., Van Hasselt, H., Schaul, T., Ostrovski, G., Dabney, W., ... & Silver, D. 

实验室：Google DeepMind

论文地址：https://arxiv.org/pdf/1710.02298

发表： In *Thirty-second AAAI conference on artificial intelligence*.

### 0x01 摘要

### 1.1 摘要--背景及问题

从DQN[1](https://zhuanlan.zhihu.com/write#ref1)推导过程中发现依旧存在很多问题，常见的改进措施Double DQN[2](https://zhuanlan.zhihu.com/write#ref1)、Dueling DQN[3](https://zhuanlan.zhihu.com/write#ref1)、Prioritized replay[4](https://zhuanlan.zhihu.com/write#ref1)、Multi-step[5](https://zhuanlan.zhihu.com/write#ref1)、Distributional RL[6](https://zhuanlan.zhihu.com/write#ref1)、Noisy Net[7](https://zhuanlan.zhihu.com/write#ref1)等方法，这些方法并不是完全独立，比如在Dueling中其实已经将Double DQN和Prioritized replay结合起来。

### 1.2 摘要--方法

本文希望将上述六种DQN方法结合经验融合在一起，来得到一个更好的网络。

### 1.3 摘要--贡献

1. 成为Atari 2600中SOTA
2. 我们还提供详细的消融研究的结果，该研究结果显示了每个组件对整体性能的贡献。

### 0x02 问题背景

### 2.1 RL problem & 记号

强化学习希望一个具有行为（action)的智能体(agent) 在与环境(environment)交互的过程可以最大化奖励(reward),在这个过程中并不会直接监督式的学习。这里分享另外一种定义:

>  一、Mathematical formalism for learning- based decision making 二、Approach for learning decision making and control form experience

**MDP (Markov Decision Process)** 𝑆,𝐴,𝑇,𝑟,𝛾 

在不同的时间步下$t=0,1,2,..$，环境状态$S_t$提供给智能体一个观测信息 $𝑂𝑡$ ,通常我们会认为是完全观测(即 $𝑆𝑡=𝑂𝑡$ )，同时智能体根据观测信息做出动作$A_t$, 之后环境给出下一个奖励  ,奖励的折扣  以及更新状态为  

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-cc2471c3fd3224c57fcb4309ebde94b9_1440w.jpg)





MDP

在这个过程通常$S,A$是有限的情况,对于环境来说状态转移（stochastic transition function)；奖励方程包括

 

 

对于智能体来说，根据状态$S_t$（或者完全观测下的观测  )得到得到动作  来自于策略  （policy),在序列决策中我们的目标是最大化某个状态采取某个动作的折扣奖励之和

 

 

我们在利用算法进行梯度提升通常会经过三个步骤

1. 生成样本
2. 评估模型或者是计算return
3. 提升策略

### 2.2 Policy Gradient：直接提升policy

为了最大化策略的回报，我们可以直接对  最大化（REINFRORCEMENT），推导过程略

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-a78b13a4cdc16a979f34e2684e017049_1440w.png)





REINFORCE algorithm 

我们可以利用baseline、n-steps、discount、import sampling的方法对他进行改进。

### 2.3 **Actor-Crtic方法**：Return与Policy分开

也可以引入新的状态价值函数  来结合拟合的方式计算$G_t$之后最大化(A3C),也可以直接利用  和动作状态价值函数  来进行基于价值函数的学习方法。

 

 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-02f72a087a1cf9714bbbf71f18e17aca_1440w.png)





batch actor-crtic algorithm

我们可以利用replay buffer、神经网络来学习降低policy gradient中的方差。

### 2.4 Value- based method  抛弃policy

Policy iteration->Value iteration->Q learning

首先从policy iteration与value iteration说起，[参考链接](https://hrl.boyuai.com/chapter/1/动态规划算法)可以看作是利用动态规划的方式反应强化学习的过程，两者的区别在于反应的是贝尔曼期望还是贝尔曼最优。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-3ac2312ee68584e699cf5ddb4029dd76_1440w.png)





贝尔曼期望方程

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-958d0797498237377ada28c5f040fe89_1440w.png)





贝尔曼最优方程

在基于价值学习算法的过程中，优点是我们只需要一个经验回放池，只需要$(s,a,s',r)$而不是需要完整的决策序列。我们通常会引入随机探索（exploration）的概念，常见的包括$\epsilon-Greedy$的方法，在一定概率下选择非策略产生的动作。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-4e0ed0915b45a8e2808e03a92259174a_1440w.png)





epsilon-greedy

在 value iteration的基础上，我们可以抛弃对$V(s)$的学习，而只是记录$Q(s,a)$;Q- iteration algorithm(或者$Q- learning$）看过程如下图所示

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-33626839aa82073a3251a3742bbb2bed_1440w.png)





image-20221106114721046

### 2.2 DQN推导

在上述我们认识对于MDP目标，从显式表达Policy，到结合Value function再到之后的完全使用Value function来使用Q- learning的方法，我们没有解决的问题包括

1. 状态-动作空间的连续性
2. 在状态空间和动作空间纬度大的时候无法准确刻画 状态-动作价值

随着神经网络的发展，我们希望利用网络拟合的方式来解决上述问题，同时每一步利用  的方式来探索回放池中的经验，并利用梯度下降等方法最小化价值函数表达的回报

 

- 1 初始化大小为$N$的经验回放池(PS：注意有大小限制)
- 2 用相同随机的网络参数初始化  与目标网络  
- 3 for 回合episode=1，N do：
- 4 获取环境初始状态  
- 5 for 时间步numstep=1，T do：
- 6 根据当前网络  结合  方法来得到  （PS：注意这一步动作的确定隐含之后DQN回报偏大的特点）
- 7 执行动作  ,获取  ,环境状态变为  
- 8 存储上述采样信息到经验回放池
- 9 if 经验回放池数目足够：
- 10 采样batchsize样本  
- 11 计算目标值  
- 12 最小化损失函数  
- 13 更新网络参数
- 14 END FOR
- 15 更新目标网络参数
- 16 END FOR

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-faee16d293d12c702263d6ecae03b3f1_1440w.png)





Deep Q- learning with Experience Replay

其中非常有趣的技巧包括：

1. 经验回放(Experience Replay )与随机探索;这一部分主要是为了提高样本采样效率，同时降低后续梯度下降中样本的相关性。
2. 目标网络(Target Network)，由于TD误差在策略改变过程中也会改变，因此造成神经网络拟合过程的不稳定性，因此构建新的目标网络，在每次迭代过程中暂时固定，在回合结合后更新参数，这样需要两层Q网络

[相关代码实现](https://paperswithcode.com/paper/playing-atari-with-deep-reinforcement)

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-24e9e9b1c1c452283697b2853225d8b4_1440w.png)





A more general view of DQN

### 0x03 DQN改进

虽然DQN成功让强化学习在某些方面超过人类，但是依旧有这许多限制。

### 3.1 改进1--Double Q- Learning

在DQN中会有估计值过高的情况，证明如下：

 

 

根据期望公式

 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-babe227bf657508c9892776ae274f451_1440w.png)





image-20221106161746893

我们通过证明发现估计值较大的原因是因为我在模型选择行为和计算Q值使用同一个网络，如果降低行为选择和Q值计算的相关性就可以降低高估，因此直觉的我们可以设计两个网络

 

 

我们的确可以新加一个网络，但是会增加学习难度，需要重新设计架构。所以为什么不直接使用$Q_{\theta}(s,a)$作为行为的估计？

 

### 3.2 改进2：Prioritized replay

在DQN学习中为高效利用（s，a，r，s）样本，我们会使用经验回放的方式来存储一定规模的样本，在梯度下降的时候通常是从经验回放中均匀采样（uniformly sampling）来进行学习，但是我们依旧会存在两个问题：

1. 依旧没有完全解决数据之间独立同分布的假设
2. 容易忘记一些罕见的、重要的经验数据

在该论文中作者首先制定指标“TD-error”作为衡量$(s_t^i,a_t^i,r_t^i,s^i_{t+1})$的信息量大小，作为采样的优先级，同时利用随机优先级采样、偏置和重要性采样等方式来避免贪心的问题。优先级的获取有3.2.1和3.2.2两种方式

 

### 3.2.1 比例优先级（Proportional prioritization）

 

### 3.2.2 基于排名的优先级(Rank-based prioritization)

；优点可以保证线性性质，对异常值不敏感。

上述两种是不同得到重要性的方式；在实现时候采用sum-tree的数据结构降低算法复杂程度。在采样中考虑重要性采样（importance sampling），并由此来进行热偏置（Annealing the bias）来修正误差

 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-db7adb0c329e9262af6a85f74bcc1ecf_1440w.png)





Double DQN with proportional prioritization 

### 3.3 改进3: Dueling networks

Dueling DQN是一种针对基于价值函数的强化学习的网络结构设计，其并不直接输出$Q(s,a)$，而是输出$V(s)$与$A(s,a)$,通常会共用前几层的卷积参数，在后面则是状态价值函数与优势函数各自的参数。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-b3f3418fb6be4620a05c0cd6b3a4711a_1440w.png)





image-20221106165135693

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-cab3f6edb16e72bafac1d719d04c58cd_1440w.png)





Dueling DQN

### 3.4 改进4:Multi-step learning

在对状态动作函数的优势估计时候，通常我们会分为蒙特卡洛方法与Bootstrap(或者是Actor- critic内的C)的方法

 

 

前者方法偏差低但是方差较大；后者方差低但是有偏。因此结合两者我们通常会有Multi-step target的方法。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-384e86c45e4e05170247ed8de5768876_1440w.png)





N-step更进一步，广义优势估计

同样的也可以用在DQN中对于状态动作价值函数的估计：

 

更新之后的损失函数为

 

### 3.5 改进5:Distributional RL

在基于价值函数的学习中我们通常是返回一个期望或者最大值而丢失很多其他信息，因此Distributional RL尝试利用其分布而不是单个值来进行强化学习。首先本文尝试将价值函数范围$[V_{min},V_{max}]$划分为N个各自来估计价值函数，利用Boltzmann分布表示价值函数的分布，同时利用投影的操作

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-b31a71e49725736c62b5ccc61443ae65_1440w.png)





估计Z(s)

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-86cf9dbbfbdd340d28d05c8fcd11d722_1440w.png)





不同option

由此对于分布拟合可以划分为交叉熵的形式，算法流程

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-5eb13a004a7b0c1e8be3c99dc490c454_1440w.png)





Distribution RL

### 3.6 改进6：Noisy Nets

在Q- learning或者是DQN中，我们的轨迹并不是完全采样的，而是与我们的探索策略相关，最原本的是$\epsilon-Greedy$策略，这里提出一种NoisyNet来对参数增加噪声来增加模型的探索能力

 

噪声的生成可以分为Independent Gaussian noise；Factorised Gaussian noise两种方式。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-21600e262570a348eeeb34b769eae3c3_1440w.png)





Nosy net的效果

### 3.7 融合上述策略

首先将（改进5:Distributional RL）中的损失函数更换称为（改进4:Multi-step learning），并利用（改进1--Double Q- Learning）计算新的目标值

 

损失函数为

 

同时在采样过程中我们通常会减少TD-error，而在本文中我们的损失函数为KL损失，因此我们的（改进2：Prioritized replay）中的优先级定义为

 

同时改变（改进3: Dueling networks）由接受期望转向接受价值函数分布，最后更改所有的线性层更换为（改进6：Noisy Nets）

 

### 0x04 实验

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-7f47620ec1f75b3c027213f2466e4c0c_1440w.png)





Median human- normalized performance

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-0051ac2a60cefae48683c4cccff9c982_1440w.png)





结果

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-52e28513cf5535cd82701933f348e200_1440w.png)





image-20221106175357633

### 0x05 总结

### 5.1 结论

1. rainbow想比较其他现有的算法要更好，速度也会更快
2. 在消融实现中；我们会发现（改进2：Prioritized replay）与（改进4:Multi-step learning）会造成结果中位数大幅度下降;(改进5:Distributional RL)在最开始表现良好，但是最终结果表现较差；同时（改进6：Noisy Nets）通常会有更好的中位数表现，同时由于本次状态中通常是underestimate的，所以（改进1--Double Q- Learning）效果并不显著，（改进3: Dueling networks）提升幅度不大。

### 5.2 讨论

作者在最后总结他们的工作，主要是从value- based的Q-learning方法集合中寻找，而没有考虑purely policy- based的算法（比如TRPO),本文从网络探索、网络初始化、数据使用、损失或函数等方面进行集合，与之相对应的同样有很多工作，未来还可以用很多其他的方法。但是我们相信

>  In general, we believe that exposing the real game to the agent is a promising direction for future research.

### 5.3 个人感悟

这篇论文看上去很水，但其实作者做了很多dirty work并最终有效，工作量非常大！

【1】[Mnih, V., Kavukcuoglu, K., Silver, D., Graves, A., Antonoglou, I., Wierstr*a, D., & Riedmiller, M. (2*013). Playing atari with deep reinforcement learning. arXiv preprint arXiv:1312.5602.](https://arxiv.org/abs/1312.5602)

【2】[Van Hasselt, H., Guez, A., &am*p; Silver, D. (2016, March). Deep reinforcement learning with* double q-learning. In Proceedings of the AAAI conference on artificial intelligence (Vol. 30, No. 1).](http://arxiv.org/abs/1509.06461)

【3】  [Schaul, *T., Quan, J., Antonoglou, I., &*amp; Silver, D. (2015). Prioritized experience replay. arXiv preprint arXiv:1511.05952.](https://arxiv.org/abs/1511.05952)

【4】[Multi Step Learning](https://www.cs.mcgill.ca/~dprecup/courses/RL/Lectures/8-multistep-2019.pdf)

【5】[Wang, Z., Schaul, T., Hessel, M., Hasselt, H., Lanctot, M., & Freita*s, N. (2016, June). Dueling network architec*tures for deep reinforcement learning. In International conference on machine learning (pp. 1995-2003). PMLR.](https://arxiv.org/abs/1511.06581)

【6】[Bellemare, M. G., Dabney, *W., & Munos, R. (2017, July). A distrib*utional perspective on reinforcement learning. In International Conference on Machine Learning (pp. 449-458). PMLR.](https://arxiv.org/pdf/1707.06887.pdf)

【7】[Fortunato, M., Azar, M. G., Piot, B., Menick, J*., Osband, I., Graves, A., ...* & Legg, S. (2017). Noisy networks for exploration. arXiv preprint arXiv:1706.10295.](https://arxiv.org/abs/1706.10295)
