---
title: Everything is Connected Graph Neural Networks
tags:
  - paper 阅读
  - gnn
  - 图神经网络
categories:
  - 算法基础
abbrlink: 67694a92
date: 2024-04-29 17:14:16
---

在许多地方，Graph是获取信息的主要形式。因为无论在自然界或者是人造系统中通常需要用图结构表示。常见的重要例子包括分子结构、社交网络、交通路网。这被一些关键的科研和公司所了解，并在交通流预测、药物发芽、社交媒体分析和推荐系统中影响。更进一步，之前机器学习在：图片、文本、语音分析都可以看作是一种特殊的Graph  representation learning。因此这些领域之间会有可以相互借鉴的地方。 本片总结的目的是为了让读者可以了解这些领域中共同的概念，并认清楚Graph representation learning与其他领域的关系

<!--more-->

\## 引言：为什么需要Graphs的数据结构

本次报告回给出一个充满生机（variant）和令人激动的深度学习研究：Graph representation learning；或者说通过Graph（这个包含nodes和edges）来构建机器学习模型，这些模型被普遍认识为GNN

这里有一个非常好的学习GNN的原因。从分子到大脑，图结构是描述结构的一个普遍的数据结构，相似的，从交通网络到社交网络也是相似的图的结构

这些潜在已经被学术界和工业界所熟知，并具有一下作用：发现抗生素、评估出行时间、内容推荐、商品推荐、设计新型机器学习硬件。更进一步，基于GNN的系统包括潜在的数学形式的发现、表示理论领域新的顶级猜想。说数十亿人每天都在接触GNN的预测，这并不轻描淡写。这并非轻描淡写。 因此，研究GNN可能是一个有价值的追求，即使不旨在直接促进其发展。

除此之外。这也是一种驱动我们提问和做决策的认知过程可能也是基于图结构的。当我们阅读一段对话，我们可能在脑海中得到各种概念，并将他们串联起来，并表示到实际的系统当中。如果实际是这样的，说明我们也可以依靠Graph representation learning来构建一个类似的智能系统。注意这样的GNN研究并不和现有的研究相冲突，我们后续会发现Transformers时一类特殊的GNN

\## 基础：排列等变性与不变性

除了上述需要Graph的原因，后面推导主要来自于[Geometric Deep Learning: Grids, Groups, Graphs, Geodesics, and Gauges]([Geometric Deep Learning: Grids, Groups, Graphs, Geodesics, and Gauges](https://arxiv.org/abs/2104.13478))；对于图主要来自于$G=(V,E)$,也就是通过一系列边和节点组成。

因为我们关心基于点的特征，因此对于每个点$u\in V$ 作为一个特征向量，$x_u \in R^k$,因此在常见的机器学习数据集通常可以表示为点特征矩阵（node feature matrix），也就是$X \in R^{|v|*k}$,可以表示为

$$X=[x_1,x_2,...,x_{|v|}]^T$$

也有很多方式表达边，1⃣️基于线性代数的邻接矩阵（adjacency matrix），可以表示为

$$ f(x)=\left\{

\begin{aligned}

 1&&(u,v)\in E\\

 0&&(u,v) \notin E

\end{aligned}

\right.

$$

注意初次之外，我们通常希望一些额外的信息可以添加到边上（比如距离标量、甚至是完整的特征向量），本文中不考虑这样的事情来保证文章的内容纯洁性。

然而，使用上述表示的行为本身强加了节点排序，因此是一种任意选择，与无序的节点和边不一致！，因此我们需要确保点和边之间是无序并不会导致输出的变化$PAP^T$，我们发现使用下述方式表示一个GNN可能是有用的

$$f(PX,PAP^T)=f(X,A) \ \ (无序性）$$

$$F(PX,PAP^T)=PF(X,A) \ \ (相等性)$$

这里我们假设函数并不会对Graph的链接性造成影响，我们只是假设根据图返回性质。

此外，Graph的边天然的导致在一些函数中会有局部性限制（locality constraint），比如CNN在图像中的局部进行卷机运算，GCN也只能在一些节点的邻居节点（neighbourhood of a node），邻居节点的定义为：

$$N_u={v|(u,v)\in E}$$

![16771285716973](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/stylename.jpeg)

在对应节点和节点对应的邻居节点中可以定义相关的函数，用于考虑所有的相近的关系，通过简单的线性组合我们会发现

$$h_u=\phi(X_u,X_{N_u)}$$

$$F(X)=[h_1,h_2,...,h_{|v|}]^T$$

如果函数$\phi$具有排列不变（permutation invariant），那么函数F具有排列等变性permutation equivariant

所以接下来的重点是如何定义函数$\phi$

\## GNN：Graph neural network

不必说，定义函数$\phi$是机器学习领域最活跃的研究之一，按照之前的文献综述，这里可能会有diffusion、propagation、message passing等方法，在2021年的这篇文章中，从空域的角度可以划分为下面三种方式

\- convolutional

\- attentional

\- message- passing

这篇文章的综述也不仅仅包含在特定的GNN层的理解，也就是说

\- 表示卷积网络 representative convolutional ChebyNet

\- 图卷积网路 GNN

\- 图卷积表示注意力网络混合模型 MoNet

\- 图注意力网络 GAT和GATv2

\- 图网络 GN

对于上述给定的GNN曾中，我们可以学习一些有趣的任务，表示不同近似联合 approximate combining的变量，这里通常会有三种任务

\- 节点分类 Node classification

\- 图分类 Graph classification

\- 连接预测 Link prediction 

在此基础上，一些更加自然的问题被提出，通过message- passing给出的图神经网络是否可以表示所有有效的permutation

-equivariant function ,这里具有不同的观点。it is the author’s opinion that the formalism in this section is likely all we need to build powerful GNNs—although, of course, different perspec- tives may benefit different problems, and existence of a powerful GNN does not mean it is easy to find using stochastic gradient descent.

虽然GNN可能会强大，但是也许找不到一个合适的SDG来寻找到收敛的情况

\## 没有图的GNN：Deep sets and Transformers

经过之前的章节的讨论，我们的假设都是我们可以得到一个图的形式

但是往往在实际问题中，我们并不能得到一个明显的A，甚至不清楚图存在的地方，甚至一个如果一个没有噪音的图A，也通常不会是最优的计算图，因为它导致的message passing可能是有问题的，因此需要考虑实际问题中输入图方式的建模

\> As such, it is generally a useful pursuit to study GNNs that are capable of modulating the input graph structure.

我们可以先假设我们只有一对特征节点X，没有邻接矩阵。最开始的想法是“悲观的”：**假设我们根本没有邻接矩阵，A+I**，在这个假设下，根据三种任务的分类我们可以得到Deep sets models，但是没有体现价值

另外一种完全相反的想法是，**假设我们具有全链接的图，fully- connected graph**，这种对于数量较少的节点可能是一种比较适合的方式，也常常被捡到卷积图神经网络。但是也有可能会退化成为一种Deepsets

下面一种是基于注意力模块的GNN，推导如下所示。是Transformer的一种关键应用，对于为什么需要Transformer，这里可以逆向的考虑一下NLP中的东西

\- 原因之一 类似句子中的词元之间关系的远近，在图中

\- 原因之二，attention可以做并行处理。由于不同任务中联系可能是不想死的，我们可以通过attention来学习不同节点的连接关系，这样可以保证对于不同的任务中都可以有相同的最优解的形式。

除此之外，还有第三种的没有输入图的GNN方式，用来GNN的连接推断，这是一种隐图推断 latent graph inference

\## 排列等变性的GNN：几何图

为了总结，我们需要回顾另外一个假设：**我们假设图通常是离散的、无序的、具有点和边、同时易受排列对称性的影响。但是这并不能代表完全的图，事实上，通常具有空间几何特征

更普遍的，我们假设输入时geometric graphs：**节点的属性来自于自身特征和坐标点**，我们希望不仅建立一个基于排列不变性的点，还不包括3D旋转不变性的点。
