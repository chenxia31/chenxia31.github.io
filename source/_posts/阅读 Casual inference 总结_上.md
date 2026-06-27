---
title: 阅读 Casual inference 总结上
tags:
  - 因果推断
categories:
  - 算法基础
abbrlink: 29337
date: 2024-09-13 10:56:52
---

这篇文章主要针对 casual inference 的前置概念进行介绍，核心希望回答的问题是为什么需要因果性，如何得到因果估计。介绍到常见的因果发现的方法。之后会在因果路径的基础上介绍更多的因果方法。

## 一、为什么需要因果性而不是相关性

### 1.1 从辛普森悖论说起

- 辛普森悖论的现象
  - 目的希望查看药物T（A，B） 在患者 X（轻、重、总体）下的效果
  - 悖论：
    - 从分层人群来看，药物 A 均优于药物 B
    - 从总体人群来看，药物 A 均差于药物 B
    - 问题？应当对于一个新的人给于什么样的治疗
    - <img src="https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913110909447.png" alt="image-20240913110909447" style="zoom:50%;" />
  - 解释：
    - 可能的解释 1：某种因素影响 T 的分配，比如更严重的人才会用 B，这个时候 B 更好
    - 可能的解释 2：如果存在中介变量，例如药物 B 需要人停留很长的时间而不被允许，这个时候 A 更好
  - 结论：
    - 需要因果才能解释数据中出现违反直觉的结论

### 1.2 因果推断的应用

- 希望找到因果的描述，而不是相关性的描述（Associational），这样才能做出有效的策略
  - 药物治疗
  - 强化学习
  - 社会实验
- 因果推断对关键决策至关重要
  - 温室气体控制

### 1.3 相关性不代表因果性 

> Correlation does not imply causation

关联关系（Association 或者 Correlation in linear statistical dependence）与因果关系

- 并不是所有的 association 都是 casual
- 也不是所有的 association 都不是 casual
- 这句话的目的在于说明 association 和 causation 之间是有区别的
- 例子：不脱鞋和头疼，但是潜在的混杂在于喝酒 -> 不脱鞋，喝酒 -> 头疼。 这种为 casual association，但是不脱鞋 -> 头疼，为confounding association

**核心问题：希望找到因果关系来产出有效的策略方案**

### 1.4 核心主题

- 统计和因果

  - 统计（Statistical）：在有限的样本中估计随机性

  - 因果性（casual）：但是 statistical 中的correlation 不代表 casual

- 识别和估计

  - identification：识别因果效应（casual effect）是因果推断的关键
  - estimation：常用到 ML 中的估计方法来实现效应的估计

- 干预和实验

  - interventional 干预实验中可以很容易得到因果效应估计
  - observation 数据中容易出现很多的观察因素

- 假设

  - 基于一些假设我们才能正确的计算 potential outcome



## 二、基本假设：如何得到 Potential outcome

T 表示策略、Y 表示输出值、X 表示可行的协变量，利用大写变量表示随机性、利用小写变量表示具体值。

### 2.1 潜在输出到ITE

> 例子 1：假如你现在很开心（Y=1），现在给了你一个玩具狗 （T=1），这个时候你仍然很开心（Y=1），但是玩具狗对你开心的效应是很弱的
>
> 例子 2：假设你现在不开心（Y=0），现在给了你一个玩具狗（T=1），这个时候你变开心了（Y=1），说明玩具狗具有很强的开心效应

Y（t）表示实施某种策略下潜在的输出，因此对于单个个体的策略包括

$$ t_i = Y_i(1)-Y_i(0) $$

### 2.2  基本问题

但是对于同一个个体无法在同一个时间下同时观察到两种策略下的输出，也就是反事实counterfactuals

### 2.3 如何解决这些问题

如何无法计算 ITE，是否可以有某种方式来计算 ATE

$$t = E[t_i] = E[Y_i(1)-E_i(0)] = E[Y(1)-Y(0)]$$ 

但是在通常的条件下，这个并不是等于

$$E[Y|T=1] - E[Y|T=0]$$

由此来计算对应的 ATE 输出

#### 2.3.1 假设1：ignorability and exchange

需要保证施加策略的用户是可以实现互换的

- 可忽略性假设是人们如何选择策略以及被策略选择
- 互换性假设希望两者之间可以互换的

![条件独立性](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913134745864.png)

如何使用 a2.1 来实现因果关系的识别，也就是可以从相关关系的识别中来观察到因果关系效应，这样可以将 causal expression 转换成为一个 statistical expression。

![可识别性定义](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913142533915.png)

#### 2.3.2 假设2：unconfoundeness （CIA）或者 conditional exchange ability

在观察数据中，满足假设 1 是完全不可能的，因为不可能找到两者完全相同的组，但是如果我们可以控制住相关的变量，那么在相同条件下可以计算对应的因果效应。

- 如何定义“相关变量”在因果图中可以表示

![条件独立性](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913142834998.png)

这里的核心思想在于 T 和 Y 虽然可能是相关联的 association，但是在控制住相同的协变量之后，不同 T 组别之间是可比的

![控制住 confounders 来满足计算](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913143054989.png)

通过控制混淆变量的方式可以让我们在无法观察到反事实的情况下，计算出因果效应，这里的公式被定义为 adjustment formula，进而可以计算除因果效应。通过将假设 2.1 转换为假设 2.2 ，让这个额条件更加实际，但但是我们仍然不知道控制的变量是什么形式，但是由于协变量中不可能保证完整的混淆变量 confounders 被观察到，因此仍然不满足条件独立性假设

- 在自然对照实验中这并不是问题
  - （个人想法，但是理论上完全的自然对照实验是不存在的，因此通过一些纠偏的方法来AB 实验的数据中进行清洗可能会得到一个更好的结果）
- 在观察数据中很难解决的事情
  - 唯一可行的是观察和拟合尽可能多的协变量 X，来尝试保证（无混淆假设 unconfoundedness）

![调整公式](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913143236910.png)

#### 2.3.3 假设3：positivity

虽然尽可能的保证协变量 X 中包含尽可能多的混淆变量 confounders 可以让 CIA 假设得到的满足，但是为了计算因果效应，我们需要保证正值假设，而随着变量的增多可能会导致某些个体无法观察到对应的数据进而不能计算因果效应。

![Assumption23](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913143823575.png)

证明如下：计算 ATE 提升期望的过程中，使用贝叶斯法则：

![正值假设](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913143902540.png)

> 直觉的解释，为什么需要正值假设？
>
> 因为如果不满足正值假设，说明其中某些子群体 subgroup 常常被分配到固定的策略方式，这种完全观察不到另一种方式会导致无法计算合理的casual effect



因此我们往往面临 假设 2.2-假设 2.3 之间的 tradeoff。协变量 X 中表示更多的混杂变量可以更容易满足假设 2.2，但是随着变量的增多，我们更容易陷入不满足假设 2.3 中，因为一部分群体只会接受到固定的策略。此时在外推情况下往往会导致输出为 0

#### 2.3.4 假设 4：No interference + consistency = SUTVA

没有干扰性的假设表示每个人的输出不会受到其他人的影响

![image-20240913144519294](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913144519294.png)

一致性假设表示在给定一个策略下，必然得到固定的产出

![image-20240913144556971](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913144556971.png)

#### 2.3.5 总结

在满足上述假设的过程中，我们可以通过期望公式将条件因果效应得到对应的输出

![满足假设条件下的因果效应估计方法](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913144707018.png)

- Casual estimand 因果效应真实值（通过 identification）
- Statistical estimand 统计效应真实值 （通过 estimation）
- Estimate

## 三、违反基本假设的困难：从Casual Graph 解释

#### 3.1 基本图的术语

- graph 
  - nodes
  - edges
  - Connection 
  - path 

#### 3.2 贝叶斯网络 

The chain rule of probability 

![概率计算链条](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913150150844.png)

结合贝叶斯网络和马尔科夫性质

![如何计算 casual estimand](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913150342251.png)

![image-20240913150604517](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913150604517.png)

PS： 定义 3.2 没太看懂

#### 3.3 因果图 Casual Graph

> 什么是因果性？
>
> 如果 X 变化，Y 一定会随着变化的。在因果图中每一个父节点都是子节点的直接原因

#### 3.4 两个节点

对于两个没有关联的节点之间是存在独立性的 $P(x_1,x_2) = P(x_1)P(x_2)$

对于两个存在关联的节点之间是存在 association 的 

#### 3.5 因果链 chain 和 因果叉 fork

![因果图中常见的关系包括](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913151026087.png)

在 Chain 下如何计算 X1 对于 X3 的因果效应？

在 Fork 下如何计算 X1 对于 X3 的因果效应？

如果在保证变量 X2 的条件下，可以证明 X1 和 X3 之间存在独立性，因此可以计算因果性。由此可以满足条件独立性假设。

#### 3.6 因果碰撞 Collider 与其后代

看上去 Immorality 中 X1 和 X3 之间没有association，但是其都是指向了相同的变量 X2，因此在采用 X2 作为变量控制，可能会让两个不相关的变量存在 association

![Collider 因果发现](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913151519188.png)

> 为什么通过控制碰撞变量可以得到因果效应的估计呢？
>
> 比如在相亲的过程中， X1 善良，X3 外表；你会发现大多数好看的男生都是混蛋，而大多数善良的男生都不好看，因此必需要在外表和善良之间做出选择，也就是两者存在负相关。
> 但是变量 X2 表示可获得性，也就是没有对象的人才会相亲。由于 X2 导致两者存在 association，在条件于 X2 的情况下，会导致两者存在 association

因此通过控制 Collider 变量，会导致一个阻断链路成为一个非阻断链路。

- X1 和 X3 在整个人群中没有关系
- 但是条件与 X2下，会导致两者出现association
- berkson‘s paradox，这也是导致 selection bias 的存在

#### 3.7 Do 算子 ｜ d-separation

那么上述的阻断路径指的是什么？

- 对于 chain 和 fork 变量，需要控制住
- 对于 Collider及其子变量，都不需要控制

未被阻断的路径就是路径中存在未被阻断的节点，其中可能会存在 association，但是在 blocked path 中不存在 association 而可以识别出casual，有次定义的 D -分离的定义<img src="https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913153535071.png" alt="符号定义" style="zoom:25%;" />

![d 分离定义](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913153502329.png)

#### 3.8 因果图和相关图

在介绍完因果图中常见的概念之外，最终的目的是为了能够介绍因果图中的 association 和 causation

- association
  - casual association 按照 direct path 运行
  - confounding association 混淆关联



在上述的基础后提出的“后门准则”和“前门准则”中的意义在于

- 在某些研究中，虽然 DAG 的某些变量不可观测，我们仍然可以从有限的观测数据中估计因果效应
- 有助于鉴别 混杂变量和设计观察性研究



- 后门准则(Fork)：定义变量之间除了直连路径之外的其他 unblocked path 均为后门路径
  - Z 中节点不是 X 的后代
  - Z 阻断所有指向 X 的路径
- 前门准则(Chain)
  - Z  阻断了所有 X 到 Y 的直接路径
  - X 到 Z 没有后门路径
  - 所有 Z 到 Y 的后门路径都被 X 阻断

> **例子：**我们关心吸烟和肺癌之间的因果关系。由于一个潜在的不可观测的基因 的存在，吸烟和肺癌之间有一条“活”的后门路径，因此不借助其他的条件，我们无法识别吸烟与肺癌的因果关系。如果我们有这样的知识“吸烟 仅仅通过肺部烟焦油的含量 来影响肺癌 ”，那么吸烟对肺癌的因果作用就可以估计出来了。不过，这里需要两个条件，也就是在证明中使用的两个条件独立性，他们表明：（1）吸烟  和肺部烟焦油的含量  之间没有“活”的后门路径（或者没有混杂因素）；（2）吸烟 对肺癌 的作用仅仅来源于吸烟 对肺部烟焦油 的作用，或者说，吸烟 对肺癌 没有“直接作用“”



再回到原始的辛普森悖论的 问题，可以发现在背后可以有很多种解释的方法，因此只有基于因果的方式才能更好的描述这个状况

![辛普森悖论背后的解释方法](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913155020606.png)

## 四、如何得到准确的答案：Casual Models

#### 4.1 do算子和干预输出分布

因果推断中 do 算子的一些描述

![可视化描述](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913160033455.png)

#### 4.2 核心的假设：模块化 Modularity

为确保因果图中对于变量 X 的干预只会改变 X 本身的概率，等价于移除所有指向 X 的因果边

![模块化的定义](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913161235590.png)

例如从观察数据中，和对其施加干预之后的干预分布定义

![分布示意图](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913161333239.png)

被定义为反事实规则或者是干预规则

#### 4.3 因果图分解 truncated factorization

在因果图的局部假设的基础上，可以提出因果图的分解方法

- 贝叶斯网络分解公式

  - 对其进行分解

- 马尔可夫假设 或者 模块化假设

  - 对其进行分解

  通过三个步骤计算出因果统计量

![因果图分解](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913161443725.png)

#### 4.4 后门调整 backdoor adjustment

如果在上述的到完整的后门准则调整的集合之后，在给定的数据集后则可以识别出对应的因果效应

![后门调整](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20240913161836120.png)

前提是需要满足 正值假设、后门准则（充分集合）来求解出对应的因果效应

#### 4.5 结构因果模型 SCM

#### 4.6 后门调整的应用

#### 4.7 回顾假设

## 五、自然对照实验和非参数识别

## 六、如何估计因果效应 Casual effect

## 七、 未观察到的混杂因子

## 八、工具变量 IV

## 九、时间差异 DID

## 十、观察数据中的因果发现



## Reference 

《Introduction to casual inference from a machine learning perspective》 by Brady Neal
