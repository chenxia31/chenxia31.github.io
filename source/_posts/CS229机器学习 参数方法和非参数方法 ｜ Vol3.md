---
title: CS229机器学习 参数方法和非参数方法 ｜ Vol3
tags:
  - CS229
  - 机器学习
categories:
  - 算法基础
  - 数据科学
abbrlink: 5568281b
date: 2024-04-29 16:09:19
---

Andrew Wu--番外参数方法和非参数方法blog.tjdata.site/2022/02/13/Andrew%20Wu--%E7%95%AA%E5%A4%96%E5%8F%82%E6%95%B0%E6%96%B9%E6%B3%95%E5%92%8C%E9%9D%9E%E5%8F%82%E6%95%B0%E6%96%B9%E6%B3%95/

[Andrew Wu--番外参数方法和非参数方法](https://blog.tjdata.site/2022/02/13/Andrew Wu--番外参数方法和非参数方法/)

在初步了解了线性模型之后，总结一下算法的共性

（材料来自网络，可能会侵权）

## 0X00 引言

（实验讨论如何在使用概率对不确定性建模，留个坑之后再说，如何作出最优决策）现在考虑如何从给定的训练集中估计这些概率，从分类和回归的参数方法开始

参数方法，这里假设样本取自服从已知模型的某个分布（eg.高斯分布），模型定义在有效统计量（eg. 方差和均值），一旦从样本中估计参数就知道了整个分布，方法主要分为最大似然估计（maximum likelihood estimation）、贝叶斯估计（Bayes‘ estimator）等

参数方法的优点：将估计概率密度、判别式或者回归函数问题归结成为少量参数值

参数方法的缺点：假设并不是总是整理的，并且不成立会导致较大的误差

非参数方法（nonparametric estimation）：算法使用合适的距离度量，从训练集中找出相似的实例，并且由他们插值得到正确的输出（assumption：世界是平稳的，并且无论是密度、判别式还是回归函数都能缓慢变化，相似的例子意味这相似的事物）

基本概念

密度估计（density estimation）

密度估计是估计$p(x)$的一般情况，在分类中，其中估计密度是能够计算后验概率$P(C_i/x)$并做决策的类密度$P(x/C_i) and P(C_i)$；在回归中，估计的密度是 :$P(y|x)$

## 0X01 参数方法

## 01-1 method—最大似然估计（MLE，maximum likelihood estimation）

### 已知

样本参数，多个独立同分布（i.i.d）的样本集：${x^t}_{t=1}^m$，同时假设样本都是**某个定义在参数$\theta$的已知概率密度族中抽取的实例：**

$$ x^i\in p(x|\theta) $$

### 求

最感兴趣是找到参数$\theta$

### 解答：

因为每个样本是独立分布的，所以给定参数$\theta$,样本的似然likelihood是单点似然的乘积：

 

为了简化计算，将乘积转换为求和，并且假设某种密度族（eg. 之前的exp family）进一步简化计算量

 

常见的似然函数是convex的，所以直接求导就行了

 

### eg：

关于指数族的推导

## 01-2 method—最大后验估计（MAP，maximum A posterior）

### 已知

在MLE的likelihood基础上，我们又知道了参数$\theta$的先验prior分布

$$ p(\theta) $$

### 求

在此基础上我们依旧希望得到最佳的参数$\theta$

### 解

根据贝叶斯规则，后验密度posterior density,告诉我们在看到样本之后$\theta$的可能取值：

 

在正常情况下，知道了$p(\theta|X)$，之后想要估计x则：

$$ p(x|X)=\int p(x,\theta|X)d \theta\=\int p(x|\theta,X)p(\theta|X)d\theta\=\int p(x|\theta)p(\theta|X)d \theta $$

所以知道了$p(\theta|X)$就知道了关于分布的一切，但是这个积分比较困难，因此为了用简便，我们希望将$p(\theta|X)$看成一个常数：

$$ \theta_{MAP}=argmax_{\theta}p(\theta|X) $$

## 01-3 method—贝叶斯估计（Bayesian estimation）

和MAP相似，但是对于$\theta$ 处理并不是最大值，而是均值

$$ \theta_{bayes}=E(\theta|X) $$

## 01-4 example—参数估计

我们假设

 

由此知道了

似然函数  

先验参数  

由此得到  

### 01-5 discussion —讨论

- MAP和贝叶斯估计都将整个后验密度归约到单个点且损失信息，除非后验单峰的且这些点周围有一个窄峰，但是随着计算能力的提高蛮可以使用蒙特卡洛或者近似方法来计算整个积分
- （bias｜variance dilemma），常见的解决方法
- 交叉验证 cross-validation训练看损失函数的“拐点”
- 正则化regularization：E=数据上误差+a*模型复杂度
- 正则化另外一个角度：E=训练误差+a*偏差的乐观项，利用AIC（Akaike‘s information criterion）和BIC（Bayesian information criterion）
- 结构风险最小化（SRM，structural risk minimization）将模型集合按照复杂程度（随机变量的个数，或者VC维）排序一次训练
- 最小描述长度（minimum description length，MDL），利用信息论中的kolmogorov复杂度度量

## 0X02 半参数方法

待定

## 0X03 非参数方法

不同的非参数方法会定义不同相似性 or 或者不同的定义插值的方法，在非参数模型中不存在单个全局模型，需要时需要有局部模型只收到邻近训练实例的影响

常见的机器学习中非参数方法又被称为instance-based或者memory-based方法

### 03-1 密度估计

- 直方图估计 histogram
- 质朴估计 naive estimator
- 核估计 kernel estimator
- k最近邻估计 k-nearest neighbor，k-nn

K-NN算法

>  1 计算测试对象到训练集中每个对象的距离 2 按照距离的远近排序  3 选取与当前测试对象最近的k的训练对象，作为该测试对象的邻居  4 统计这k邻居的类别频率  5 选择k个邻居中频率最高的类别，作为测试对象的类别 

```
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np

class knn:
    def loadDataset(self,filename):
        # 借用SVM的数据集，但是这里使用numpy返回np.array数据
        dataMat = [];
        labelMat = []
        fr = open(filename)
        for line in fr.readlines():  # 逐行读取，滤除空格等
            lineArr = line.strip().split('\t')
            dataMat.append([float(lineArr[0]), float(lineArr[1])])  # 添加数据
            labelMat.append(float(lineArr[2]))  # 添加标签
        dataMat=np.array(dataMat)
        self.dataMat=dataMat
        self.labelMat=labelMat

    def  visualDataset(self):
        sns.scatterplot(self.dataMat[:, 0],self.dataMat[:, 1],hue=self.labelMat)
        plt.show()

    def predict(self,input,k):
        def distance(point):
            points=np.tile(point,(self.dataMat.shape[0],1))
            dis=np.linalg.norm(points - self.dataMat,axis=1)
            return dis
        dis=distance(input)
        target=np.array(self.labelMat)[np.argsort(dis)<k]
        if sum(target)>0:
            return 1
        else:
            return -1

if __name__ == '__main__':
    model=knn()
    model.loadDataset('CS229_2018autumn/ProblemSet(课后作业）/codeByhand/datasets/testSet.txt')
    model.visualDataset()
    example=[0,0]
    print(model.predict(example,6))
```

### 03-2 分类

上次的SVM算法

优点：

- 解决高维特征的分类问题和回归问题很有效,在特征维度大于样本数时依然有很好的效果。
- 仅仅使用一部分支持向量来做超平面的决策，无需依赖全部数据。
- 有大量的核函数可以使用，从而可以很灵活的来解决各种非线性的分类回归问题。
- 样本量不是海量数据的时候，分类准确率高，泛化能力强。

缺点：

- 如果特征维度远远大于样本数，则SVM表现一般。
- SVM在样本量非常大，核函数映射维度非常高时，计算量过大，不太适合使用。
- 非线性问题的核函数的选择没有通用标准，难以选择一个合适的核函数。
- SVM对缺失数据敏感。
