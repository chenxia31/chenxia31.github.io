---
title: CS229 机器学习 常用模型总结 ｜ Vol6
tags:
  - CS229
  - 机器学习
categories:
  - 算法基础
  - 数据科学
abbrlink: b796995
date: 2024-04-29 16:09:23
---

在机器学习看到一半的过程中，对于之前学习的广义线性模型、高斯判别分析、支持向量机、决策树、多层感知器进行阶段性的总结，并尝试使用过去课程中可能存在例子来进行展示，最后利用Kaggle的实验来进行展示

如有侵权，可以删除；如有错误，欢迎提出

## 0x01基本模型

### 1.1 GLM（logistic regression、linear regression、softmax regression）

Andrew Wu 第一部分--线性模型blog.tjdata.site/2022/01/29/Andrew-Wu-%E7%AC%AC%E4%B8%80%E9%83%A8%E5%88%86-%E7%BA%BF%E6%80%A7%E6%A8%A1%E5%9E%8B/

>  吐槽： 明明是discriminate的模型，却叫广义线性模型（generative linear model）；明明是generative的模型，却叫高斯判别分析（gaussian discriminat analysis） 

广义线性模型中的步骤是，对于一群i.i.d（独立同分布）的数据集X，通过学习算法得到一个关于输出预测y的$P(\hat y|X)$,再每次输出中将期望值作为最终的输出，使得其的概率可以最大。其中做出三个基本假设

1. ～$𝑋～𝐸𝑥𝑝𝑜𝑛𝑒𝑛𝑡𝑖𝑎𝑙 𝐹𝑎𝑚𝑖𝑙𝑦(𝜂)=𝑏(𝑦)𝑒𝑥𝑝(𝜂𝑇𝑇(𝑦)−𝑎(𝜂))$ 
2. 输出的预测值 $𝑦^=ℎ𝜃(𝑥)=𝐸(𝑦|𝑥)$ 
3. 由于是线性模型，最强的假设是 $𝜂=𝜃𝑇𝑥(𝑖)$ 

可以从三个例子来看出推导过程：

### example01:逻辑回归（y=「0，1」,ps：y=[-1,1]不一样）

>  GLM分析： $ℎ𝜃(𝑥)=𝐸(𝑦|𝑥,𝜃)$ ,这里的分布假设是Bernoulli（伯努利分布），因此 $ℎ𝜃(𝑥)=𝜙=1/(1+𝑒−𝜂)=1/(1+𝑒−𝜃𝑇𝑥)$  

数据集和假设 （）$（𝑥(𝑖),𝑦(𝑖)）,𝑦∈0,1,ℎ𝜃(𝑥)=𝑔(𝑧),𝑔(𝑧)=11+𝑒−𝑧$ 

由此推导得到预测值的输出的损失函数: $𝑃(𝑦|𝑥)=∏ℎ𝜃(𝑥)𝑦∗(1−ℎ𝜃(𝑥)1−𝑦$ 

之后为了利用梯度下降Gradient Descent下降的方式需要计算  ,同时为了简便计算

 

### example02: 线性回归

>  GLM分析：  ,这里的分布假设是高斯分布   

数据集和假设  

由此推导得到预测值的输出的损失函数:  

之后为了利用梯度下降Gradient Descent下降的方式需要计算  ,同时为了简便计算

 

等价于  

 

### example03: softmax 回归

GLM分析：

 ,这里我们不知道具体的概率分布是什么，只能假设每一类的输出是$\phi_k$,得到概率分布函数

 

经过化简可以得到

 

同时

 

可以得到

 

因此对于

 

在得到分布函数的基础上，我们就可以计算期望值

 

损失函数就是我们最初定义的：

  

### 1.2 GDA（Gaussian Discriminat  Analysis）

与判别式模型（GLM）想比较$p(y|x)$，生成式模型generative用于生成$p(x|y)p(y)$来对每一种类别建模，寻找每个类别的特征，也就是比如输出多种类别，同时假设每个类别都是高斯分布，则可以计算两个类别的高斯分布参数，这样在得到输入可以直接使用分布函数来得到输出  计算就好了蛮

### 1.3 SVM(Support Vector Machine)

支持向量机是最大边缘方法，允许把模型表示为训练实例的一个子集的影响之和，这些影响用面向应用的相似性核给出，采用：不要在解决实际问题之前把解决一个更复杂的问题作为第一步；在训练过程中希望找到的是那个最佳分离平面hyperplane：$w^Tx+b$中的两个参数

模型的输入是$(x^{(i)},y^{(i)}),y\in{-1,+1}$,希望找到的是可以讲数据集可以正确区分开的参数$w\ b$

Ps:为了简单起见，可以从线性可分的情况来谈论，再拓展到非线性可分的情况

通过定义functional margin和geometry margin，我们可以建立最符合直觉的模型为：  经过凸优化中的相关变化，使其变成可以求解的形式  对于这种类型的凸优化问题，可以采用拉格朗日方法或者其他方法直接进行求解；也可以将其转换为对偶问题dual problem，再利用拉格朗日算法或者其他方法来求解，⚠️我们希望求解的是w和b！！

 

对于这个函数求极值，就是希望

 等于0

 

 

将这个带入到L中，化简得到

 

构建对偶问题，等价于

 

 

其中需要满足KKT条件：

1.  

通过某种算法SMO可以很轻松的求解上述对偶问题，得到$\alpha_i$并根据KKT条件可以确定支持向量（support vector）,由此计算得到w和b，这样就可以判断正例还是反例

- SMO算法？以后才知道吧

在线性不可分的情况，可以使用核机器（kernel function）来进行重新度量向量之间的非相似性

在间隔划分中，可以通过设置软间隔的方式来允许有部分噪声点发生

同时为了过拟合的风险，依旧可以增加模型复杂程度的项（term）

### 1.4 CART（Classfication and regression tree）

### 1.5 ANN（Artificial Neural Network）[决策树CART](https://blog.tjdata.site/2022/02/20/决策树CART/)1.5 ANN（Artificial Neural Network）

对于每个神经元包括两个部分，一个是接受前端的激活权重，另外一方面是自身的激活函数的选取

$线性连接：z^i=w^ia^{i-1}+b^i$

$激活输出：a^i=g(z^i)$

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-4ce75c932592dde7ff5bffc6dc133f2f_1440w.jpg)





添加图片注释，不超过 140 字（可选）

假设对于这样的一个函数：

前向传递：

 

 

 

 

 

 

梯度下降来更新参数：

ps：解决问题要从源头一步一步解决

可以选取交叉熵作为误差函数

 

 

很amazing，在于输出的梯度下降可以和前一个节点的激活有关，如果使用内存把这些记录下来，可以大幅度的降低训练的时间，同时可以并行的计算一些参数

在定义好一个网络结果只是一个基础，还需要确定网络中的激活函数，定义好网络中训练的基本参数初始化、参数的随机选择、以及采用比较好的优化求解算法

**激活函数**

softmax、tanh、logistic、ReLu等等

**参数归一化**

min-max归一化、z-score、均值方差归一化

**参数初始化**

在网络层数变多的情况下，梯度会出现爆炸或者变小的情况，因此可以采用的随机化包括：均值、xvaier initiation、he initialization、np.random .randn(shaope)*np.sgrt(1/n[z-1]) simoid 用1比较好、relu用2比较好等等不知名的参数初始化

**优化方法**

GD、SGD、BGD、动量算法、Adam、启发式算法（模拟退火、遗传算法）等等

**常见的网络**

1. RBF（radial basis function，径向基网络）
2. ART（adaptive resonance theory，自适应谐振理论）
3. SOM（self- organizing map ，自组织映射）
4. CC（cascade- correlation，级联相关网络）
5. Elamn网络
6. Boltzmann机

## 0x02实验

一些比较简单的codebase

[GLM](https://github.com/chenxia31/Mercury/blob/master/summary_AndrewWu/GLM.ipynb)

[决策树CART在IRIS](https://github.com/chenxia31/Mercury/blob/master/summary_AndrewWu/iris_CART.ipynb)

[tensorflow实现的人工神经网络](https://github.com/chenxia31/Mercury/blob/master/summary_AndrewWu/mnist_NN.ipynb)

[支持向量机](https://github.com/chenxia31/Mercury/blob/master/summary_AndrewWu/supportVM.ipynb)

## 0x03Kaggle- Titanic

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-de261fa1071e89f75768152454f4ff43_1440w.jpg)





添加图片注释，不超过 140 字（可选）

草率的花了一晚上了解了一下kaggle的流程，有点拉胯，一万多.容易让人想起来痛苦的交通信息检测处理的大作业

```
01 数据集的导入和基本信息
train数据集中包括的特征有： PassengerId：乘客的编号 Survived：是否存活 Pclass：票的类型，1是最好的、3是最差的 Name:性命 Sex:年龄 Age:年龄 SibSp:相伴的兄弟姐妹以及配偶 Parch：相伴的父母或者孩子 Ticker：乘客的票号 Fare:乘客的票价 Cabin：机舱号码 Embarked：下车地点
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
train_data=pd.read_csv('../titanic/train.csv')
test_data=pd.read_csv('../titanic/test.csv')
train_data.head()
# 和交通数据中一样拿到数据需要考虑数据是否存在缺失、异常、或者是重复等等
train_data.info()
# 可以看出数据中存在缺失的特征包括：Age、Cabin、Embarked
## 缺失值的修补
#Embarked 上船地点中缺少两个数，对于之后并没有太大的影响，可以使用众数进行修补
train_data.Embarked.fillna(value=train_data.Embarked.dropna().mode().values[0])
data_test=test_data.Fare.fillna(value=test_data.Fare.dropna().mean())
test_data.loc[:,'Fare']=data_test
# 从直觉上来看，年龄对于之后是否坠船有相关的影响，因此在这里选择对应的非空特征进行绚练
feature_name=['Pclass','Sex', 'SibSp', 'Parch','Fare','Embarked','Survived']
# cabin 的机场缺失太多，这里就不作为训练来
# 对性别进行编码
train_data_copy=train_data[feature_name]
from sklearn.preprocessing import LabelEncoder
label=LabelEncoder()

train_data_copy.loc[:,'Sex']=label.fit_transform(train_data_copy.Sex)
train_data_copy.loc[:,'Embarked']=label.fit_transform(train_data_copy.Embarked)

test_data.loc[:,'Sex']=label.fit_transform(test_data.Sex)
test_data.loc[:,'Embarked']=label.fit_transform(test_data.Embarked)
## 尝试使用SVM
import numpy as np
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.preprocessing import PolynomialFeatures
from sklearn.svm import LinearSVC

p_s_c=Pipeline([
    ('poly_features',PolynomialFeatures(degree=3)),
    ('scaler',StandardScaler()),
    ('svm_clf',LinearSVC(C=10,loss='hinge'))
])
p_s_c.fit(train_data_copy.loc[:,('Pclass','Sex', 'SibSp', 'Parch','Fare','Embarked')],train_data_copy.iloc[:,-1])
from sklearn.metrics import accuracy_score
result=p_s_c.predict(test_data.loc[:,('Pclass','Sex', 'SibSp', 'Parch','Fare','Embarked')])
submisstion=pd.DataFrame({
    'PassengerId':test_data['PassengerId'],
    'Survived':result
})
submisstion.to_csv('submission.csv',index=False)
```
