---
title: CS229机器学习 集成学习 ｜ Vol8
tags:
  - CS229
  - 机器学习
categories:
  - 算法基础
abbrlink: 8c525481
date: 2024-04-29 16:38:37
---

集成学习是通过构建并结合多个学习器来完成学习任务，有时候也被称为多分类系统（multi- classifier system）、基于委员会的学习（committee based learning）等等之类的，在其中主要面临基本学习器的选择和训练过程，我们希望学习器可以在相同的数据集上训练，但是得到的最终的弱学习器最好的是相互独立的，这样才能提高我们最高算法的准确程度。

集成学习成立的基础是：基学习器的误差相互独立，因此这也是我们在集成方法中需要尽可能的去实现的，因此本文从基本的方法和以决策树作为一系列基学习器开始

## 0x01 引言

在任何应用中，我们都可以使用多个学习算法汇总的一个，使用确定的算法都在一组**基本假设**，并训练得到影响最终学习器的超参数，因此可以尝试使用构建多个彼此不同的学习器，尽可能的减少它们之间误差的相关性来通过某种策略的组合来提升模型的效果，如何构建不同的学习器？

1. 使用不同的算法和数据集，比如使用参数化的逻辑回归和非参数化的决策树模型，这样多算法的学习器之间的误差是可能独立的；对于相同的参数化的模型也可以采用不同的超参数，比如对于K-means算法可以在不同的算法选择不同的k值；同样对于不同的算法模型中选择不同的数据集是也可以产生不同的学习器，因此可以使用自助法等构建彼此相互独立数据集的基础上来训练得到不同的算法模型
2. 使用一些特殊的策略，包括但不限于：平均法（averaging）、投票法（voting）、学习法（learning）
3. 使用一些特殊的策略，包括但不限于：bagging（典型模型：随机森林random forest）和boosting（典型模型：Adaboost、GB）

## 0x02  集成学习 -- 不同的模型

比较简单，看0x01引言--第一点

## 0x03 集成学习-- 不同的策略

3.1 平均法averaging（回归器）

对于数值型常用的结合策略是平均法，又可以分为简单平均法（simple average）和加权平均法（weighted average）

虽然看名字可能就知道这个代表是什么，但是对于加权平均法其中最重要的是权重的确定，或许可以根据多元高斯分布来确定由近及远的权重、可以根据专家打分的德尔菲法、可以有迷糊的一致性矩阵计算得到的AHP、Kalman filter中检测和状态转移的估计等得到的权重，无论是何种方法最重要的是可以得到一个令自己满意、顶得住别人argue的处理方法

加权平均方法被用于集成学习可以被看作是集成学习研究的基本出发点，对给定的基学习器，不同的学习方法可以视作通过不同的方式来确定加权平均法中的基学习器的权重。

3.2 投票法voting（分类器）

投票的方式本质上和回归的平均法是类似的，但是因为class是可以数清楚的，因此可以分为绝对多数投票法（majority voting）、相对多数投票法（plurality voting）、加权投票法（weighed voting）、

3.3 学习法

当训练数据很多的时候，一种更为强大的结合策略是使用学习法，通过另外一种学习器来进行结合

stacking是学习法的典型代表，在这里将个体学习称为初级学习器，用于结合的学习器称为次级学习器或者元学习器（meta- learner），stacking先从初始数据集和中训练得到初级学习器，然后“生成“一个新的数据集来训练次级学习器，在这个新的数据集中，初级学习器的输出被当作样例来输入特征，而初始样本的标记被当作样例标记，这里的假设是初级学习器是使用不同学习算法得到的不同的模型，参考周志华-《机器学习》代码

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-52e362c38d83416ff5093fc9b7787116_1440w.jpg)





添加图片注释，不超过 140 字（可选）

可以看出，其中的次级学习算法可以看出是一种自适应adaptive的权重调整方法，同时这种权重是通过一个学习器来训练得到的，抛弃其中的数学原理不谈，这里常用的包括多响应线性回归Multi- response Linear regression和贝叶斯模型平均Bayes Model AVeraging BMA等效果较好

## 0x04 集成学习-- 特殊的概念

在集成学习中最晦涩的就是boosting和bagging（bootstrap aggregating）两种方法

### 4.1 bagging（有放回称为bagging、无放回的称为pasting）

同样是根据希望的得到不同基训练模型，因此我们可以对相同的学习器来赋予不同的训练集来得到不同的模型，但是由于数据集是有限的，因此如果数据集完全隔离开，这样训练得到的学习器必然是不好的（或者说是不够好的），如果我们想尽可能的好的得到独立同时效果也好的学习模型，[可以参考之前对模型评估](https://blog.tjdata.site/2022/02/25/Andrew Wu-番外02-机器学习的实验设计/)中的样本的自助法选择（bootstrap sampling）来得到相互独立数据集得到的不同学习模型；在此基础上得到的结果可以使用**简单平均策略**或者**简单投票**来得到最终的结果

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-16fc7815c5999f8082ba609838ca8c8a_1440w.jpg)





添加图片注释，不超过 140 字（可选）

从bias=variance的角度来看，bagging主要集中在降低方差 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-25e39256fe8cd4502ade8a5a9c79d2f1_1440w.jpg)





添加图片注释，不超过 140 字（可选）

### 4.2 boosting

是一类可以将弱学习器提升为强学习器的算法，这种算法的工作机制类似：先从初始训练集训练得到一个基学习器，再根据基学习器的表现对训练样本分布进行调整，使得先前基学习器做错的训练样本再后续受到跟多关注，然后基于调整后的样本分布是的先前基学习器做错的训练样本再后续收到更多的关注，然后使用基于调整后的样本分布来训练下一个基学习器，如此重复进行，直到基学习器数目达到来事先制定的值，最终将这些基学习器进行加权结合

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-fed68e0739c70d75816dc16bb49a5d81_1440w.jpg)





添加图片注释，不超过 140 字（可选）

从bias- variance的角度来看，boosting主要集中在降低偏差

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-7f7de7b989d0324742edb836942ed50d_1440w.jpg)





添加图片注释，不超过 140 字（可选）

（这里感觉书上讲的还是比较模糊的，主要在后面的实际例子中看）

### 4.3 bagging和boosting的区别

1. 从样本选择角度
2. 从样本的权重
3. 从预测函数的权重
4. 从计算的角度

## 0x05 实际例子

### 5.1 投票法

一个比较简单的例子

```
## 集成学习需要得到的是不相关的预测器
# 可以使用不同的学习器
# 可以使用不同的数据来训练得到不同的学习器
# 一些特殊的方法bagging和boosting
#%%
from sklearn.datasets import make_moons
from sklearn.model_selection import train_test_split
x,y=make_moons(n_samples=100,noise=0.25)
x_train,x_val,y_train,y_val=train_test_split(x,y,test_size=0.2)
#%%
## vote
from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import VotingClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC

#%%
lr_clf=LogisticRegression()
rnd_clf=RandomForestClassifier()
svm_clf=SVC()
#%%
voting_clf=VotingClassifier(estimators=[('lr',lr_clf),('rf',rnd_clf),('svc',svm_clf),],voting='hard')
#%%
voting_clf.fit(x_train,y_train)
from sklearn.metrics import accuracy_score
for clf in (lr_clf,rnd_clf,svm_clf,voting_clf):
    clf.fit(x_train,y_train)
    y_pred=clf.predict(x_val)
    print(accuracy_score(y_val,y_pred))
```

### 5.2 随机森林 random forest（based on bagging）

随机森林就是使用许多个决策树来集成得到最终的结果，论文最早发布与1997年的如下，他是如何将有放回的抽样应用到决策树CART中的呢？（这或许是随机森林学习的重点），以及具体的实践方法，其中的数学原理可能比较困难

Paper：[Amit, Y., & Geman, D. (1997). Shape quantization and recognition with randomized trees. Neural computation, 9(7), 1545-1588.](https://link.springer.com/article/10.1023/A:1010933404324)

API:[sklearn.ensemble.RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html)

回顾之前关于实现[决策树模型的过程](https://blog.tjdata.site/2022/02/20/Andrew-Wu基本模型03-决策树CART/)，它是一个很简单的过程，一种采用递归思想的算法，在不考虑最终停止的条件，它总是从现有的特征集中选择最好的特征来进行模型的分割，然后删除这个特征进行下一次递归，那么随机森林在这个过程中做了什么优化呢？

1. 样本的角度，在训练决策树中并不选择所有的样本训练，利用bootstrap的方法来有放回的抽取数据集来训练不同的决策树（decision tree）
2. 特征的角度，传统的decision tree由上述的描述可以看出它实际上是一种没有放回抽样（why？），那么随机森林的改进就是每次不删除特征集，而是随机选择一定属性的特征（比如原始特征集的10%）来选择其中最佳的特征来进行模型的分割一直到不能再分裂为止

模型的优点

1. 有可以做出高纬度的数据、并且不用降纬，不需要做特征选择
2. 可以判断特征的重要程度、特征之间的相互影响
3. 训练速度可以很快，做成一种并行处理的方法
4. 对于不均衡的数据可以平衡误差

模型的缺点

1. 被证明在某些noise比较大的分类或回归问题中会产生过拟合
2. 对于不同取之的属性的数据，取值划分较多的属性会对随机森林产生更大的影响，所以随机森林在这种数据上产生的属性权重是不可信的

### 5.3 Adaboost（based on boosting，short for adaptive boosting）

自适应增强学习Adaboost是如何将boosting的方法使用在训练的过程中呢？总的来看它考虑如何通过更加规则（而不是随机）的方式来得到更加有效的基学习器的训练，并采用一定的策略来加权所有的预测

Paper：[Freund, Y., & Schapire, R. E. (1997). A decision-theoretic generalization of on-line learning and an application to boosting. Journal of computer and system sciences, 55(1), 119-139.](https://www.face-rec.org/algorithms/Boosting-Ensemble/decision-theoretic_generalization.pdf)

API：[sklearn.ensemble.AdaBoostClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.AdaBoostClassifier.html)

Adaboost的算法可以简述为三个步骤：

1. 初始化数据集，这里保证数据集中的每个样本的数据的权重是相同的
2. 在第N次的训练中，得到原始标签和模型的输出，如果预测是正确的那么在下一次训练中需要降低它的权重、如果预测是错误的则需要提升它的权重，在进行N+1学习器的训练
3. 在综合最终的结果就是对每个阶段的学习器进行加权平均，如何加权呢？根据正确率来判断，正确率高的权重大

模型的优点：这里参考https://zhuanlan.zhihu.com/p/41536315

1. 很好的利用弱分类器进行级联cascade
2. 可以将不同的分类算法作为弱分类器
3. AdaBoost具有很高的精度
4. 可以充分考虑每个分类器的权重（也就是加权平均中权的选择合理）

模型的缺点：

1. 模型的迭代次数不好设计，可以使用交叉验证来进行确定
2. 数据不平衡导致分类精度下降
3. 由于不能并行训练，因此计算时间比较长

### 5.4 GB（gradient boosting，based on boosting and slightly bagging）

有一点复杂2022-03-28还没有完全搞清楚梯度提升（gradient boosting），其算法实现主要分为XGBoost和LightGBM，从boosting算法的角度是必须串行计算的，但是在工程的实际应用中可以使用一些**稀奇古怪** 的方法来反复使用其中的一些东西来并行计算提高计算效率

Paper：[Friedman, J. H. (2001). Greedy function approximation: a gradient boosting machine. Annals of statistics, 1189-1232.](http://luthuli.cs.uiuc.edu/~daf/courses/Opt-2017/Papers/2699986.pdf)

一些工业化的实现：

lightGBM：https://proceedings.neurips.cc/paper/2017/file/6449f44a102fde848669bdd9eb6b76fa-Paper.pdf

XGBoost：[Chen, T., & Guestrin, C. (2016, August). Xgboost: A scalable tree boosting system. In Proceedings of the 22nd acm sigkdd international conference on knowledge discovery and data mining (pp. 785-794).](https://www.kdd.org/kdd2016/papers/files/rfp0697-chenAemb.pdf)

**存疑**：GBDT（采用decision tree作为基学习器的GB算法）的核心是学习之前每棵树结论和的残差，这个残差就是一个加预测值后能得到的真实值的累加量，这里相比Adaboost的优点

1. 与Adaboost算法不同的是，梯度提升在迭代每一步构建一个能够沿着梯度最陡的方向降低损失来弥补现有模型的不足
2. 经典的Adaboost的算法只能采用指数损失函数的二分类学习任务，而梯度提升可以通过不同的可微损失函数来处理各类学习任务（multi-class、regression、rank）
3. Adaboost中对异常点比较敏感，而梯度提升算法通过引入bagging的思想、正则化可以提升robust
