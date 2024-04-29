---
title: Mitchell 机器学习读后感
tags:
  - Mitchell
  - 机器学习
categories:
  - 算法基础
abbrlink: d3686930
date: 2024-04-29 16:09:14
---

<aside> 

✈️ 讨论机器学习在统计学、模式识别、神经网络、人工智能、信号处理等不同领域的应用，主要内容包括监督学习、贝叶斯决策理论、参数方法、多元方法、维度规约、非参数方法、决策树、线性判别式、多层感知器、隐马尔可夫、组合多学习期以及增强学习等

https://book.douban.com/subject/25881740/book.douban.com/subject/25881740/

</aside>[机器学习导论（原书第2版）](https://book.douban.com/subject/25881740/)</aside>

## chapter01 绪论

>  我们知道桌子不是木材和其他材料的随机堆砌，手写数字不是像素的随机分布，熟人的声音也不是各种声波的随机组合，现实世界总是有规律的，机器学习正式从已有的实例中发现这个规律，建立起对未知实例的预测模型，根据经验不断提高来改进越策性能 

good and useful approximation: 希望我们可以构建一个好的并且有用的近似，虽然不足以将数据中的规律全部解释，但是希望可以用来解释数据的一部分或者机理

### 机器学习的应用实例：

1.  association rule 关联规则 $P(Y|X)$的条件概率的相关分析 
2.  classification 分类，一旦拥有来拟合以往数据的规则就能对新的实例进行分析，包括credit scoring、pattern recognition、face recognition、medical diagnosis、speech recognition、knowledge extraction、compression、outlier detection 
3.  regression，包括robot navigation、连续值预测等 

-  response surface design 我们试图优化一个函数，假设我们想要造一个咖啡的机器，该机器包含多个影响咖啡品质的输入，各种温度、时间、咖啡豆种类等配置，我们针对不同的输入配置进行大量实验并测量咖啡的品质，例如根据消费者的满意度测量咖啡的品质，为来需求最优配置我们需要拟合这些输入和咖啡品质的回归模型，并在当前模型的最优样本附近选择一些新的点来方便寻找更好的配置。 

1.  unsupervised  learning 在监督学习汇总，我们的目标是学习从输入到输出的映射关系，其中输出的正确指已经由指导者提供，但是非监督学习中却没有这样的指导者，因此目标是发现输入数据中的规律，输入空间中内部存在的结构使得特定的模式比其他模式更常出现，因此这个在统计学中成为density estimation（密度估计），其中一种方法是利用clustering聚类的方式来发现输入数据的簇、image compression\bioinformatics 
2.  reinforcement learning 增强学习 系统的输出是一个动作a你的sequence，在这种情况下，单个动作并不重要，重要的是policy策略，常用于game playing、robot navigatio、multiple agents协同操作 

### 杂谈

```
之后讨论的方法来源于不同的学科领域，有时候相同的算法会在多个领域中沿着各自不同的历史轨迹被独立发现，在统计学中，从特殊的
观测到一般的描述成为inference、 学习被成为estimation；分类在统计学中成为discriminant analysis；在工程学中，分类
被称为pattern recognition，方法是非参数的，机器学习与人工智能有关是因为智能系统能够适应其环境的变化，在电子工程领
域signal processing使得自适应计算机视觉和语音程序出现，以后artificial neural networks 和knowledge discovery 
in databased发展
```

在统计学、模式识别、神经网络、信号处理、人工智能以及数据挖掘等不同领域中，研究工作遵循这各自的途径并有各自的侧重点，本书的目的是结合所有这些研究给出重点并给出统一的处理问题的方法后给出求解方案

## chapter02 监督学习

<aside> ✈️ 从最简单的情况讨论监督学习，首先从positive和negative中学习类别，之后讨论到多类的情况，最后讨论输出为连续值的regression

</aside>

### **2.1 二元分类**

example：只考了价格和发送机功率作为input representation，来对class=家用汽车进行分类，

我们假设价格为input 1，发动机功率作为input 2

$$X=[x_1,x_2]^T$$

同时利用标号来表示汽车的类型

$$r=\begin{cases} 1\ r\  is \  positive \\ 0\ r\ is\ nagitive \end{cases}$$

由此每辆汽车都以用一个有序对来表示

$$X=\{x^t,r^t\}^N_{t=1}$$

我们构造一个hypothesis class，认为对于家用汽车，价格和发动机功率都在某一个确定的范围内：

$$（p_1<x_1<p_2）and (e_1 <x_2<e_2)$$

尽管专家定义来这个假设类，我们需要找到一个hypothesis来最接近 与，也就是需要确定上述假设类中的参数$[p_1,p_2,e_1,e_2]$

我们寻找希望实现的最终效果是让h和真实的C尽可能的蕾丝，但是实际上我们并不知道$C（X）$，因此也无法评估$h(x)\ and\ c(x)$之间的误差，我们所拥有的是训练集x，它知识x的一个小的子集，针对预测值和预期值不同的训练实例所占的比例，误差可以定义为：

$$E(h|x)=\Sigma^N_{t=1}1\ (h(x^t)!=r^t)$$

我们希望训练的结果是涵盖所有的正例而不包括任何负例，虽然可能会存在无穷多个预测问题，但是需要给定一个接近正例和负例边界的某个未来实例，不同的候选假设可能作出不同的越策，因此需要考虑其generalization的问题

策略1⃣️：找出most special hypothesis，涵盖所有正例而不包括任何负例的最紧凑的矩形，这样得出的一个假设h=S作为我们的induced class，同时找到most general hypothesis，涵盖所有正例而不包括负例的最大矩形，作为S和G之内的所有h都是无误差的有效假设，称为和训练集相融consistent，构成解空间version space

策略2⃣️：  处于之间都是doubt例子，由于缺乏数据支持，因此在这种情况下系统将去除reject这些实际例子

**def（VC维 vapnik-chervonenkis dimension）**：假设我们有一个数据集，包含N个点，这点总共可以用$2^N$ 中方法来标记positive or negative，因此可以定位为多种不同的学习问题，如果对于这些问题中任何一个都可以找到一个假设h将positive和negative分开，那么我们成为h shatter N各店，也就是说N个点定义的任何的学习都能够用一个从H中抽取的假设无误差学习，可以被H shatter的点的最大数量成为 VC维,用来度量假设类H的学习能力capacity

[统计学习理论之VC维究竟是什么](https://tangshusen.me/2018/12/09/vc-dimension/)

def（probably approximately correct，PAC）：误差概率不高某个值，还要对我们的假设有信息吗，因为我们想知道我们的假设在大多数时间里都是正确的，因此我们希望我们的假设是正确的

给定类和从未知但是具有确定概率分布中抽取样本，我们希望找出样本书N，是的对于任意的$\sigma<=1/2$和 $\phi>0$,假设h的无法之多$\phi$的概率至多为$1-\sigma$

$$P\{C\Delta h<=\sigma \}>=1-\phi$$

**def(noise 噪声)：是数据中有害的异常，类的学习可能更加困难，并且是使用简单的假设困难做不到零误差，对于噪声具有一下的分类：**

- 记录输入属性困难不准确，这困难导致数据点在输入空间驿动
- 标记数据点困难有错误 teacher noise指导噪声
- 困难存在我们没有考虑到的附加属性，包括hidden and latent的

<aside> 

✈️ occam’s razor：较简单的解释看上去更可信，并且任何不必要的复杂性都应该被抛弃

</aside>

### 2.2 多分类

我们能够通过两个假设或者更多的假设来抛弃这种输入

### 2.3 回归问题

type1:如果回归问题的数据中不存在noise，则该任务是interpolation插值

### 2.5 model selection and generalization

由于学习是一个ill-posed problem，单纯依靠数据本身不足够找到解，因此我们应该做一些特别的假设，以便得到已有数据的唯一解，我们为使学习成为可能所做的假设集称为学习算法的inductive bias

但是我们知道每个假设类都有一定的能力，并且只能学习确定的函数underfitting以及overfitting的问题

寻找三类之间的平衡

triple trade-off：

1. 拟合数据的假设的复杂性
2. 训练数据的总量
3. 在新例子上的泛化误差

### chapter summary

1.  model $g(x|\theta)$,g()表示假设类，而$\theta$是假设类汇总的一个假设，模型由机器学习胸的设计者根据其应用知识背景决定，参数由学习算法，利用取样于实际应用的训练集来进行调整 
2.  loss function$L(`)$用于计算预期输出和给定参数值对于近似之间的误差，逼近误差approximation error或者loss是每个单例的损失和 $$E(\theta|x)=\Sigma L(r^t,g(x^t|\theta))$$ 
3.  optimization precedure 最优化问题是用来求解最小化近似误差的$\theta^*$,常见的优化算法可能有基于梯度的方法、模拟退火的方法、遗传方法等 

想要最好上面的工作需要满足条件：

1. 假设类g需要足够大，需要有足够大的容量，以便在包含噪声的情况下产生r表示的数据的未知数
2. 必须有足够的训练数据，使得我们从假设类中识别正确的假设
3. 给定训练数据应该有好的优化方法来找出正确的假设

不同的机器学习方法之间的区别是可能是假设的模型不同、他们的损失度量不同、或者是所采用的最优化过程不同

### chapter problem

1. 设想你一次只能得到一个训练实例，而不是一次得到所有的训练实力，如何增量调整？
2. 我们对于实际值和估计值之差的平方求和该误差函数是使用最频繁的误差函数，但是他只是可行的误差函数之一，他对于离群点并不是robust的，为实现robust regression，更好的误差函数是什么吗

## chapter03 贝叶斯决策定理

<aside>

 ✈️ 在不确定情况下决策的概率理论框架；在classification中，贝叶斯规则用来计算class的概率，我们将讨论推广到怎样作出合理的decision来min exception of risk，同时还介绍贝叶斯网络来有效表示随机变量之间的依赖关系

</aside>

### 3.1 introduce

温习基本的概率知识

在experiment中我们会unobservable variable以及observable variable，因此从样本中来推断出整体的概率分布，需要用到一定的估计

$$\bar p_0={postive}/{p+n}$$

### 3.2 bayes in classification

example : 在银行的信用评分中，根据过去的交易，客户是分为高风险和低风险的，为来判断客户具体的风险水平，我们希望从数据中进行分类，如果我们知道所有的信息，我们可以准确的判断，但是有一些是unobservable，因此我们需要从能够观测的信息observable variable中进行推断

描述就成为：客户的信用的观测条件为$X=[x_1,x_2]^T$的输入变量，输出为$C$,为1的时候高风险，为0的时候低风险，如果$P(C|X_1,X_2)$知道，当$X_1=x_1,X_2=x_2$时，我们可以判断是否为高风险：

$$choose=\begin{cases}C=1\ P(C=1|x_1,x_2)>P(C=0|x_1,x_2)\\ C=0 \end{cases}$$

由贝叶斯规则可以表示成为：

$$p(C|x)=p(C)p(x|C)/p(x)$$

$p(C=1)$成为C取1的prior probability，这是我们在看到观测量x之前就获得关于C值的知识，这里满足：

$$\Sigma P(C=i)=1$$

$p(x|C)$成为class likelihood，是属于C的时间具有相关联的观测值的x的条件概率，在我们的例子中

$p(x1,x2|C=1)$就是高风险具有$X_1=x_1,X_2=x_2$的概率，这就是我们通过数据得到的关于类的信息

$p(x)$是evidence，是看到观测x的边缘概率，此时无论他是正实例还是负实例

最终为了将误差最小化，贝叶斯分类器$Bayes's\ classifier$选择具有最高后验概率的类

$$choose\ C=C_i \ P(C_i|x)=maxP(C_k|x)$$

### 3.3 bayes in risk 损失和风险

决策的好坏程度或者代价可能不同

- 金融机构错误接受一个高风险的申请人带来的损失和错误拒绝一个低风险的申请人带来的潜在收益是不同的
- 在医疗诊断、地震预测等显示更加重要

后面推导类一下情况：

1. zero-one loss ，所有错误具有相同的代价，因此在3.2中识别出最大的概率就是最优解
2. 错误的决策具有很高的代价，通过推导可以发现需要max的概率大于1-lameda

度量中的倾向是：

1. loss或者expected risk尽可能的小
2. utility function尽可能大
3. 新特征的value of information

### 3.4 bayes network

Bayesian Network，or belief network，or probabilistic network，represent relationship between variables as a graphical model，一个贝叶斯网路由节点和节点之间的弧构成，每个阶段对应一个随机变量，该影响的由条件概率$P(y|X)$指定

eg1: 下雨R引起草地W变湿，天下雨的可能是0.4，下雨导致草地0.9，不下雨草地湿0.2

question：草地湿W的时候下雨的R的概率

eg2:新增一个喷岁器S作为W的另一个原因，各种概率分布

计算出：S开的时候草地湿的概率、草地W时S的概率、假设下过雨R后草地W时S的概率

推断出explaining away：如果给定我们下过雨，则喷水器导致湿草地的可能性降低了

贝叶斯网络的总结：

1. 这个网络描述了条件独立性，并且允许我们多个变量的联合分布问题分解成为局部local的问题，这样简化的分析和计算，如果每个节点只有少量的父节点，这个复杂度由指数降低到线性
2. 可以利用belif propagation的方法来游戏哦啊的计算概率分布，如果网络是树状的时候可以行程junction tree 结树
3. **主要优点**：不必要明确指定变量作为输入，某些其他变量作为输出，任何变量集的值都能通过争取建立，而任何其他变量集的概率都可以推断
4. 如果输入之间的是相互独立的，这种网络被成为naive bayes’ calssfier，朴素贝叶斯网络，忽略输入之间可能的依赖性，讲一个多变量问题规约成为一组单变量问题

## chapter04 参数方法

### 4.1 引言

example参数方法：假设样本服从已知模型的某个分布，比如高斯分布，由此来从样本估计模型所需要的参数来得到完整的分布，将这些估计放到假设的模型来得到估计的分布，之后进行决策。

用来估计的方法常见的最大似然估计，之后还会讨论贝叶斯估计的方法

example：正态分布的均值和方差的最大似然估计

example：伯努利分布的p的最大似然估计

### 4.3 极大似然评价估计：bias和variance（偏倚和方差）

这里涉及的就是无偏估计或者渐进无偏估计等等之类的

$$令X是取自参数\theta 指定的总体上的样本，令d=d(\theta)是\theta的一个估计，为评估一个估计的质量，我们可以估计其与\theta 有多大的不同\\r(d,\theta)=E[(d(X)-\theta)^2]\\估计的bias是\\b_\theta(d)=E[d(x)]-\theta$$

variance:度量在平均情况下，估计值在期望值附近的变化程度（在样本发生改变）

bias：度量期望值和正确指不同的程度

## 4.4 贝叶斯估计

在得到样本之前，可能会有一些关于参数$\theta$ 可能取值范围的prior信息

**prior density** 告诉我们在看到样本之前$\theta$ 的可能取值

**likelihood density** 数据告诉我们的

**posterior density** 后验密度告诉我们看到样本之后$\theta$ 的可能取之

如果我们假定似然密度在众数周围有一个窄的峰值，则使用maximum a posteriorMAP估计将使得计算比较容易，

另一个方法是利用Bayes‘s estimator来定义后验密度的期望值

另一求积分的方式是利用蒙特卡洛方法或者从后验密度中产生样本，利用一些近似方法来计算整个积分

## 4.5 参数方法来分类

example：假设一个汽车公司销售K种不同的汽车，为来简单期间，我们假设唯一影响购买的因素是他们的年收入用x表示

$$P(C_i)是购买类型i汽车顾客所占的比例\\购买i汽车的顾客的概率P(x|C_i)服从分布N(\mu_i,\sigma_i^2)$$

这里俄队参数使用最大似然估计，如果密度函数不是高斯的分类算法可能会出现错误，因此存在正态性的检验

## 4.6 参数方法来回归

可能算是屁话的话

## 4.7 调整模型的复杂度，bias/variance dilemma

任何机器学习都存在bias和variance的冒险，模型应当是柔性的

如果方差保持的比较低，则可能不能很好的拟合数据，并且具有较高的偏倚

最佳模型是最好的权衡偏倚和方差的模型

## 4.8 模型选择过程

cross-validation（属于：策略经验风险最小化 策略）

我们不能计算一个模型的偏倚和方差但是我们能够计算总误差

regularization 正则化（属于：SRM结构风险最小化策略）

这里使用一个增广误差函数$E=数据上的误差+\lambda*模型复杂度$ 其中$\lambda$给出来罚函数的权重，当我们最小化增广误差函数而不是数据的误差 是，我们惩罚来复杂模型，因此降低来方差，但是如果取得太多，就会被引进偏倚的风险

structural risk minimization SRM 结构风险最小化

使用一个模型集合，比如多项式的参数数量，或者是VC维度也是模型复杂度

minimum description length MDL 最小描述长度

如果数据简单就有最短的复杂度

贝叶斯方法

定义为模型的先验分布p模型，给定数据并假设模型，之后根据贝叶斯规则计算

交叉确认与其他模型选择的方法不同，因为它不对模型做任何先验假设，如果有足够大的确认数据集就是最好的方法，在数据样本很小是，其他模型变的有用

## chapter05 多元方法

<aside> ✈️ 在chapter04中讨论的分类和回归的参数方法，现在可以推广到多元情况

</aside>

### 5.1 多元-

在chapter04讨论了利用参数估计的方法来对单个变量的情况进行classification和regression，现在可以将其推广到多元情况，其这些输入和输出都是离散的，我们将讨论如何从标记的多元样本学习这样的函数

在许多应用中，许多测量都在每个个体或者事件上产生一个观测响亮，这样的情况下包括data matrix，也可以被成为input、feature、attribute；每一行可以成为对于事件的iid的observation、example、instance

这些# 数据 都具有其基本的特点，比如离散数据、或者连续数据；离散数据可以包括序数数据、数值数据等等

但是通常这些变量是相关的，但是我们的目标通常会simplification，就是利用相对较少的参数汇总大量的数据，目标可能是exploratory，由此引申multi-classification、multi-regression

### 5.2 参数估计（和之前的相同）

常用的估计量包括：

- mean vector
- covariance matrix
- correlation
- sample correlation

### 5.3 缺失值估计

>  如果观测中的某些变量的值缺失，最好的策略是将这些观测一同丢弃，但是一般没有足够大的样本让我们这样做，因此需要对其进行填补，成为imputation 

imputation by mean：平均或者众数代替

imputation by regression：尝试从其他已知变量来预测缺失，有定义为回归或者分类问题

### 5.4 多元正态分布

### 5.5 多元分类

### 5.6 调整复杂度

bias/variance 选择减少协方差矩阵的参数树木，在简单模型的适用性和通用型之间折中：

一方面，简化协方差矩阵的假设并降低被估计的参数数目，可能增大bias

另一方面，不做折中假设，由于矩阵好似任意的，在二次判别式函数在小数据集众会有很大的方差

### 5.7 离散特征

### 5.8 多元回归 multivariate linear regression

- 线性模型可以观察w的值来提取知识
- 观察w的富豪可以知道x对于输出结果的影响是正的还是负的
- 如果所有的x具有相同的值域

### summary

如果数据是多元正态，任意两个变量的图应该大致上是线性的，模式识别的大部分工作都是假设多元正态密度上进行，但这通常是错误的

只有当密度确实是多元正态时，并且我们有足够的数据来计算正确的参数是，这才是最优的，可以利用评估多元正态性的检验以及检查相关协方差举证的检验；在利用多元正态分布分类中比较线性和二次判别式

## chapter06 维度归约

### 6.1 引言

在我们确信含有信息的观测数据都被用作input来输入到系统中作为决策，理想情况下我们不应该将feature choose或者feature extraction作为一个单独的进程，分类方法或者回归方法应该能够利用任何必要的特征，但是有需要原让我们对降维作为一个单独的预处理步骤：

- learning method汇总复杂程度依赖于model-d和databased-N
- 当一个输入认定不必要的时候节省提取开销
- 较简单的模型在小数据集上更加鲁棒
- 数据能用较少的特征解释，我们可以更好的理解数据的过程
- 数据可以用少数维度表示而不丢失信息是可以对数据绘图

降低维度的方法：特征选择feature selection、特征提取extraction

feature selection：从d维中找出提供最多信息的k维度，讨论subset selection

feature extraction：感兴趣是找到k维度的组合，这些事原来d的组合，常用的方法有PCA、LDA，以及FA和MDS

## chapter07～13:

有点过时，具体policy到model的具体实现要根据实际来
