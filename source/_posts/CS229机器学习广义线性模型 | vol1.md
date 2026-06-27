---
title: CS229机器学习广义线性模型 | vol1
tags:
  - CS229
  - 机器学习
categories:
  - 算法基础
  - 数据科学
abbrlink: ceb408a2
date: 2024-04-29 16:09:16
---

根据Andrew wu [机器学习](https://www.bilibili.com/video/BV1JE411w7Ub?from=search&seid=12699853882563574773&spm_id_from=333.337.0.0)的监督学习的第一部分总结

## **0x01 引言**

机器学习的定义：

> 对于某类任务task和性能度量performance，如果一个计算机程序在task上以performance衡量的性能随着经验experience而自我完善，那么我们称这个计算机程序在从experience中学习

机器学习的方法在不同领域中以不同的方式逐渐发展到共通的状态，从统计学习中从特殊到一般的inference、从工程学中的模式识别，以及结合神经网络、信号处理、自动控制、人工智能以及数据挖掘等不同领域来综合方法，从数据中induction，从训练的模型deduction

在区分中机器学习常见分为监督学习supervised learning、非监督学习unsupervised learning和强化学习reinforcement learning

- 监督学习，给定dataset，包括输入x和输出y，由此来构建一个算法得到x和y的映射关系
- 非监督学习：给定dataset，包括输入x，由此来构建一些算法

机器学习算法的过程包括：

1. 首先构建一个假设函数（hypothesis），其中包含一个模型中需要的参数（parameter）
2. 之后构建目标函数（target），来让包含参数的模型更加贴合实际，这里需要一定的目标函数来刻画
3. 转换成为一个优化问题（optimization），可以利用一些算法来进去求解
4. 对模型进行指标的评价

## **0x02 线性回归 linear regression**

### **数据集**

$$ 输入变量：输出变量：单个实例：完整数据集：输入变量：𝑋𝑖=[𝑥1𝑖,𝑥2𝑖,...,𝑥𝑛𝑖]输出变量：𝑦𝑖单个实例：{𝑋𝑖,𝑦𝑖}完整数据集：{(𝑋𝑖,𝑦𝑖),𝑖=1,...,𝑚}  $$

### **假设**

线性回归中比较强的假设为

$$ 𝑦^=ℎ𝜃(𝑋)=𝜃𝑇∗𝑋𝜃:[1,(𝑛+1)]𝑋:=[𝑥0,𝑥1,...,𝑥𝑛],𝑥0=1  $$

### **目标函数**

- 直观的方法，目标函数是预测值predict和真实值ground- truth之间的某种不相似的关系，我们希望的是两者尽可能的相似，所以这里可以使用欧氏距离来刻画： $$  
- $$ （这里的1/2只是为了方便后续求导的方便） 我们希望这个越小越好
- 概率解释的方法，在预测中总会有着各种各样的误差，所以 $$  
- $$ 这样解释**其实是将预测值 y 看成是一个分布，这个分布首先具有一个概率分布函数，之后我们在预测中希望的是所有预测值的分布在取真实值的时候概率最大，这时候求解出来的参数就是模型想要的参数** 根据中心极限定理这个推导得出误差项分布是一种高斯分布，则得到预测值的分布 $$   $$ 得到最大似然函数（likelihood function） $$   $$ 然后对其取对数后求导 $$   $$ 如果希望上式最小，**等价** $$   $$ 或者**等价**于 $$   $$

非常amazing的看出两个方法居然得到相同的结论！但其实深思我们会发现第一种方法我们是根据经验来得到，我们主观的认识用在线性回归中利用欧式距离会好一点，第二种方法我们带着一个假设（误差满足高斯分布）出发，来得到概率分布，通过maximum likelihood estimation（MLE）得到损失函数,两者只是解释的方法不同

### **优化算法**

这种优化算法的常见是使用梯度下降的方式进行计算：

$$   $$

由上述推导看出我们的目标函数的梯度

$$   $$

这样就可以每次运行下去了，但是我们会发现在优化的过程每次梯度下降的过程都需要对所有数据进行计算，所以通常被称为**batch gradient descent（BGD）**，但是这样速度通常会变的很慢，因此需要尝试**stochastic gradient descent（SGD）**下降的梯度只使用一个点而不是所有点的梯度相加，由此得到最终解

## **0x03 逻辑回归 logistics regression**

### **数据集**

$$   $$

与之前不同的是，这里的输出变量或者说是label，仅为0或者1

### **假设**

我们仍然假设为线性模型：

$$   $$

### **损失函数**

这里我们从概率分布的角度出发，利用MLE的方式来寻找最佳的损失函数

首先确定预测值 y 的分布

$$   $$

由此我们可以出预测值正确的概率分布函数为：

$$   $$

我们希望针对所有的分布函数可以最大化

$$   $$

### **优化算法**

- GD 梯度下降的方法 首先计算梯度、然后更新 $$   $$ 更新梯度 $$   $$
- Newton法

推导过程没有看，但是更新参数的过程为

$$   $$

### **番外-- logistic code**

根据机器学习课后习题来的：

1. 建立一个线性模型的class：

```
class LinearModel(object):
   """Base class for linear models."""

   def __init__(self, step_size=0.2, max_iter=100, eps=1e-2,
                theta_0=None, learning_rate=0.01,verbose=True):
       """
      Args:
          step_size: Step size for iterative solvers only.
          max_iter: Maximum number of iterations for the solver.
          eps: Threshold for determining convergence.
          theta_0: Initial guess for theta. If None, use the zero vector.
          verbose: Print loss values during training.
      """
       self.theta = theta_0
       self.step_size = step_size
       self.max_iter = max_iter
       self.eps = eps
       self.verbose = verbose
       self.learning_rate=learning_rate

   def fit(self, x, y):
       """Run solver to fit linear model.

      Args:
          x: Training example inputs. Shape (m, n).
          y: Training example labels. Shape (m,).
      """
       raise NotImplementedError('Subclass of LinearModel must implement fit method.')

   def predict(self, x):
       """Make a prediction given new inputs x.

      Args:
          x: Inputs of shape (m, n).

      Returns:
          Outputs of shape (m,).
      """
       raise NotImplementedError('Subclass of LinearModel must implement predict method.')

```

之后对其进行更改

```
import numpy as np
import util
from linear_model import LinearModel
import numpy as np


def main(train_path, eval_path, pred_path):
   """Problem 1(b): Logistic regression with Newton's Method.

  Args:
      train_path: Path to CSV file containing dataset for training.
      eval_path: Path to CSV file containing dataset for evaluation.
      pred_path: Path to save predictions.
  """
   x_train, y_train = util.load_dataset(train_path, add_intercept=True)
   x_eval, y_eval = util.load_dataset(eval_path, add_intercept=True)

   # *** START CODE HERE ***
   model = LogisticRegression()
   model.fit(x_train, y_train)
   # *** END CODE HERE ***


class LogisticRegression(LinearModel):
   """Logistic regression with Newton's Method as the solver.

  Example usage:
      > clf = LogisticRegression()
      > clf.fit(x_train, y_train)
      > clf.predict(x_eval)
  """

   def fit(self, x, y):
       """Run Newton's Method to minimize J(theta) for logistic regression.

      Args:
          x: Training example inputs. Shape (m, n).
          y: Training example labels. Shape (m,).
      """

       # *** START CODE HERE ***
       def h(theta, x):
           ''' logistic的假设

          参数：
              theta：超参数
              x：输入变量，或者是特征值
          '''
           return 1 / (1 + np.exp(-np.dot(x, theta)))

       # *** END CODE HERE ***

       def gradient(theta, x, y):
           ''' 计算梯度

          参数：
              theta：超参数
              x：输入变量
              y：验证变量

          '''
           m, _ = x.shape
           return -1 / m * np.dot(x.T, (y - h(theta, x)))

       def hessian(theta, x):
           """ 计算Hessian公式
          """
           m, _ = x.shape
           h_theta_x = np.reshape(h(theta, x), (-1, 1))
           return 1 / m * np.dot(x.T, h_theta_x * (1 - h_theta_x) * x)

       def next_theta(theta, x, y):
           """通过Newton法来更新theta

          :param theta: Shape (n,).
          :return:     The updated theta of shape (n,).
          """
           return theta - np.dot(np.linalg.inv(hessian(theta, x)), gradient(theta, x, y))

       m, n = x.shape  # m是训练集的大小，n是特征的多少

       # 初始化theta
       if self.theta is None:
           self.theta = np.zeros(n)

       # 更新theta
       old_theta = self.theta
       new_theta = next_theta(self.theta, x, y)

       # 合适的时候停止
       while np.linalg.norm(new_theta - old_theta, 1) >= self.eps:
           old_theta = new_theta
           new_theta = next_theta(old_theta, x, y)

       self.theta = new_theta

   def predict(self, x):
       """Make a prediction given new inputs x.

      Args:
          x: Inputs of shape (m, n).

      Returns:
          Outputs of shape (m,).
      """
       # *** START CODE HERE ***
       return x @ self.theta >= 0
       # *** END CODE HERE ***


train_path = '../data/ds1_train.csv'
valid_path = '../data/ds1_valid.csv'
prad_path = '../data/ds1_valid.csv'
main(train_path, valid_path, prad_path)

```

## **0x04  广义线性模型 Generative linear model**

三个最基本的假设

1. 预测值y的分布是指数族，～Exponential Family（n）其中n为超参数
2. 对于给定的x，我们得到一个关于预测值y的概率分布函数，这个分布函数的期望我们将其作为我们最终的预测值，则满足 $$   $$
3. （限制性最大的假设）假设自然参数n和输入变量x之前的关系是线性的 $$   $$

> [关于什么是指数族](https://zhangzhenhu.github.io/blog/glm/source/指数族/content.html) 指数族分布并不是一个具体的概率分布，指的是一类分布，这类分布具有一些共同的特性，所以形成一个概率分布族family，常见的指数族分布包括高斯分布、二项分布、多项式分布、泊松分布、gamma分布、beta分布等 $$ P(y,\eta)=b(y)*exp(\eta^TT(y)-a(\eta))\\ 这里的\eta 为自然参数\\ T(y)是充分统计量\\ a(\eta)是对数分割函数，来帮助\int p(y,\eta)dy=1 $$ 常见的指数族和对应分类算法的： 实数～高斯分布 0-1分布～伯努利分布 计数分布～泊松分布 非零实数～Gamma正参数分布

### **由GLM构建线性模型的过程**

1. linear regression $$   $$
2. logistic regression $$   $$

## **0x05 高斯判别式算法 Gauss discriminant analysis**

判别式算法：希望找到一个分别的算法

生成式算法：希望针对数据本身建模

（判别式算法意味着一个找到一个线来区分两者，生成算法意味着找到数据本身的性质）

或许一个是频率学派、一个是贝叶斯学派...
