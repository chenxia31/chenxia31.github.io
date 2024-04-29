---
title: CS285 深度强化学习 Vol5 ｜ 智能体与搜索
tags:
  - 搜索问题
  - 人工智能
  - search problem
categories:
  - 算法基础
abbrlink: 9af6a934
date: 2024-04-29 19:08:58
---

行为流派01_智能体与搜索blog.tjdata.site/posts/9c6b1465.html

根据CS188《Intro to AI》来对搜索策略进行一定的总结

### 0x01 智能体

在人工智能中，核心问题是创建一个理性的**智能体(Rational agent)**，是一个实体通过一些系列的**动作(Actions)**来实现目标或者是**喜好(Goal or perferences),**它存在于**环境中（Environment）**，智能体通过**传感器（sensors）**获取信息并驱动自己的**动作（Actuators）**。

Reflex agent：仅仅根据现有来选择动作

Planning agent：根据一系列的动作来确定现有状态下的动作

定义一个任务通常会用到PEAS（评价指标，环境，动作，传感器）

Performance Measure：指的是智能体需要提高的衡量指标

Environment：总结影响智能体的因素

Actions and Sensors：说明智能体如何对环境造成改变，同时如何获取信息

特定的我们确定几类不同特征的任务：

1. 部分观察的任务(Partially observable environments) 或者 完全观察(Fully observation)
2. 随机过程(Stochastic) 或者 确定过程（Deterministic）
3. 多智能体环境（multi-agent）
4. 静态环境（static）或者动态环境（Dynamic）

### 0x02 搜索问题定义

搜索问题包括以下几个部分：

1. State 在所给案例中的所有状态
2. Action 在每个状态可取动作的集合
3. Transition 在某一状态下采用动作得到的下一个状态
4. Cost 从一个状态到另外一个状态的成本
5. Start state 初始状态
6. Goal test 判断是否到达最终的状态

例如吃豆人Pathing和吃掉所有豆子Eat-all-dots问题之间的区别：

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-66e0dba2c604a7b4d0aba42f7b2fe3e7_1440w.png)





搜索问题举例

常见的表现形式为状态空间图和搜索树

state space graphs and search trees

状态空间图：用不同状态之间的转移关系来表示

搜索树：以状态为节点，动作作为边，但是每个状态和节点不仅编码状态本身，还编码路径

- getNextState
- get Action
- get Action Cost

我们只存储立即处理的state，并计算最新的状态

### 0x03 基本搜索（normal search）

一个标准的方式来寻找到轨迹（Plan），是从初始状态开始(getStartState)来到达最终状态（Goal state），同时维护可能的过程（Frontier），同时拓展我们的当前过程通过删除节点state为state- action- state。（称作strategy），当我们删除缩手的frontiers，我们得到最终的路径（path）

这些搜索中具有一些基本属性：

1. 搜索的完整性 completeness
2. 搜索的最优性 optimality
3. 分枝因素
4. 最大深度
5. 最浅解的深度

常见的无信息搜索包括：DFS、BFS、UCS

常见的信息搜索包括；Greedy search and A star search

后者相比较前者主要增加代价，代价的含义包括两个方面

1. Estimation of distance to goal state，as the cost function may be the lower bound for the problem, the heuristics are typically solutions to relaxed problem(where some of the constraint  of the original problem have been removed)

比如利用启发式的方法可以让PACMAN更加倾向于到达最终的目的地

1. Estimation of the empirical cost function

### 0x04 约束问题（Constraint satisfied problem）和回溯（Backtracking search）和local search

之前的文章中介绍什么是规划问题（Planing problem），可以使用搜索策略（Search strategy）进行求解。对于另外一种问题可以描述为约束满足问题（Constraint satisfaction problems，CSP）是一类识别（Identification problem）

> **识别问题（Identification problem）：我们必须简单的识别最终的结果是否为目标状态** 这个术语通常指在机器学习和人工智能中,系统需要从一组潜在的候选项中识别出正确的项目的任务。这类问题通常涉及模式识别,即从输入的数据中提取有意义的信息模式。 一些常见的识别问题示例包括: 

- 图像识别:从图片中识别出特定对象或场景
- 语音识别:从语音输入中识别出说话内容
- 面部识别:从图片或视频中识别出特定人脸
- 手写识别:识别手写输入的文字内容
- 生物识别:通过指纹、声纹等生物特征识别个体身份
- 文档分类:将文本文档分类到预定义的类别中

 识别问题通常需要构建合适的机器学习模型,然后使用大量带标签的训练数据对模型进行训练,使其能够对新输入进行可靠的识别与分类。同时还需要处理各种困难情况,如光线、角度、遮挡等对图像识别的影响,以提高模型的鲁棒性。

三元组：

1. variables，一系列的变量集合
2. domain，不同变量可能存在的取值范围
3. constraints，不同变量之间的约束关系

例如：八皇后问题

约束编程问题的求解方式是NP-hard问题，随着问题规模的扩大时间呈现指数型增加。我们可以将**约束满足问题转换成为搜索问题：**

state 为部分赋值（对CSP的可变赋值，其中一些变量被分配了值，而另一些变量没有）。

successor function 输出所有状态，并分配一个新变量，目标测试验证所有变量都被分配，并在其测试的状态中满足所有约束。

约束问题比之前的搜索问题具有更复杂的框架，因此可以尝试将上述公示和合适的启发式方法结合起来

之前的文章中介绍什么是规划问题（Planing problem），可以使用搜索策略（Search strategy）进行求解。对于另外一种问题可以描述为约束满足问题（Constraint satisfaction problems，CSP）是一类识别（Identification problem）

> **识别问题（Identification problem）：我们必须简单的识别最终的结果是否为目标状态** 这个术语通常指在机器学习和人工智能中,系统需要从一组潜在的候选项中识别出正确的项目的任务。这类问题通常涉及模式识别,即从输入的数据中提取有意义的信息模式。 一些常见的识别问题示例包括: 

- 图像识别:从图片中识别出特定对象或场景
- 语音识别:从语音输入中识别出说话内容
- 面部识别:从图片或视频中识别出特定人脸
- 手写识别:识别手写输入的文字内容
- 生物识别:通过指纹、声纹等生物特征识别个体身份
- 文档分类:将文本文档分类到预定义的类别中

 识别问题通常需要构建合适的机器学习模型,然后使用大量带标签的训练数据对模型进行训练,使其能够对新输入进行可靠的识别与分类。同时还需要处理各种困难情况,如光线、角度、遮挡等对图像识别的影响,以提高模型的鲁棒性。

三元组：

1. variables，一系列的变量集合
2. domain，不同变量可能存在的取值范围
3. constraints，不同变量之间的约束关系

例如：八皇后问题

约束编程问题的求解方式是NP-hard问题，随着问题规模的扩大时间呈现指数型增加。我们可以将**约束满足问题转换成为搜索问题：**

state 为部分赋值（对CSP的可变赋值，其中一些变量被分配了值，而另一些变量没有）。

successor function 输出所有状态，并分配一个新变量，目标测试验证所有变量都被分配，并在其测试的状态中满足所有约束。

约束问题比之前的搜索问题具有更复杂的框架，因此可以尝试将上述公示和合适的启发式方法结合起来

map coloring problem : 给定一堆颜色，要求相邻的地区之间的颜色不能相等

通常我们可以对约束进行分类：

1. unary constraints 专有约束，在约束图中表示 点
2. binary constraint：两个变量，在约束图中表示 边
3. higher-order constraint：非常规

另一方面，回溯搜索仅在变量值不违反任何约束的情况下为变量分配值，从而显著减少回溯。虽然回溯搜索是比深度优先搜索的野蛮强化的巨大改进，但通过过滤、变量/值排序和结构拓宽的进一步改进，我们仍然可以获得更多的速度收益。

- 变量的取值范围已经是一个较大的进步
- Tricks1: 过滤（Filter）：
- Tricks2:变量排序（Variable or value ordering）
- minimum remaining values，选择分配有效剩余值最少的变量
- least constraining value，从剩余未份配值的domain中选择prunes最少的值
- Tricks3: 结构拓宽（Structural exploration）

另外一种方法：本地搜索通过迭代的改进对一些值的随机分配开始，然后迭代的选择一个随机冲突变量，并对变量重新分配给违反约束最小的变量，直到不再存在约束违规，称为一种具有最小冲突启发式的策略

1. 爬山搜索 Hill-Climbing Search
2. 模拟退火搜索 Simulate annealing search
3. 遗传搜索 generic algorithm

### 0x05 游戏（Game） 和对抗搜索（Adversarial search problem）

在之前的搜索问题中，我们尝试解决他们高效并且最优的，利用不同的搜索方式。以及更加具有启发式的强大的搜索算法。但是在面临一些问题中搜索算法中会遇到对方的阻碍，这种情况下单纯的利用搜索策略。这一类新的问题通常可以被称为adversarial search problem，或者叫做游戏games。

不同于传统搜索问题返回直接的答案，对抗搜索由于problem一直在发生变化，这样复杂的场景中往往要求我们返回一个策略（Strategy or policy），一个简单的对抗搜索问题包括一下策略：

1. Initial state
2. players ：每一轮属于谁的player
3. actions，player可以实现的动作
4. transition model 状态转移的结果
5. terminal test
6. terminal values

可以使用

1. Min-max搜索
2. Alpha-beta搜索
3. evaluation function

### 0x06 动态决策过程（MDP）和强化学习（Reinforcement learning）

～ to be continued

参考链接：

[OI wiki：搜索部分简介](https://oi-wiki.org/search/)

[《CS188: Intro to atrificial intelligent》](https://inst.eecs.berkeley.edu/~cs188/fa22/)

伯克利人工智能入门课按经典教材章节顺序授课,内容涵盖各主要领域;课程笔记通俗易懂,作业可在Gradescope平台完成实时测评,项目复现经典游戏,运用所学知识实现算法,使吃豆人在迷宫自由行动。
