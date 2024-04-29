---
title: CS229机器学习 SVM | vol2
tags:
  - CS229
  - 机器学习
categories:
  - 算法基础
  - 数据科学
abbrlink: e7902d53
date: 2024-04-29 16:09:17
---

讨论一种不同的线性分类和回归方法，每种学习算法都具有不同的归纳偏倚，做不同的假设，定义不同的目标函数，因此可能找到不同的模型 Support Vector Machine

## 0x01 引言

支持向量机使用Vapnik原则

>  不要在解决实际问题之前把解决一个更复杂的问题作为第一步（vapnik，1995） 

训练后，线性模型的参数（权重向量）可以用训练集的一个子集表示，这个子集称为支持向量support vector。对于分类这些是靠近边界的实例，因此知道它们可以提取知识：这些事两个类之间的边界附近、不确定或有错误的实例，它们的个数给我们提供了范化误差的一个估计，并且正如我们将在下面看到的，能够用实例集表示模型参数进行核化

其总体框架：是一种分类算法，主要应用于二分类算法，通过定位两类数据在集合空间中的margin，来确定分类起，主要步骤分为：

1. （建立模型）将二分类问题转换成为一个优化问题
2. （求解模型）利用SMO算法、拉格朗日对偶、KKT条件来求解简化优化问题
3. （分类讨论）之后利用松弛变量、核函数、惩罚因子等对不同情况下的优化问题进行求解

它的优点和缺点是什么？

- 优点：参数非常少，不像logistic regression还具有超参数，虽然比神经网络效果差，但是很简单
- 缺点：

## 0x02 建立模型

### 符号说明（notation）

$$ ：等价于之前的𝑖𝑛𝑝𝑢𝑡:𝑋=(𝑥1,...,𝑥𝑛) 𝑙𝑎𝑏𝑒𝑙𝑠：𝑦∈−1,1 ℎ𝑦𝑝𝑒𝑟𝑝𝑙𝑎𝑛𝑒:𝜔𝑇𝑋+𝑏(等价于之前的𝜃𝑇𝑋) 𝑔(𝑍)={1 𝑖𝑓 𝑍>=0 −1 𝑖𝑓 𝑜𝑡ℎ𝑒𝑟𝑤𝑖𝑠𝑒 $$

数据集和超平面的距离

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-c3b7e25a83a399c4e7f2cae9aeee13f8_1440w.jpg)





添加图片注释，不超过 140 字（可选）

### 定义边缘（margin）

### geo- margin

从单个实例来看，就是向量到一个平面的距离，根据向量的推导知识可以看出： $$ geo_{margin}^{i}=y^{(i)}*(\omega^TX^i+b) $$ 从整个数据集来看，就是一群点到一个平面中距离的最小值，定义为 $$ geo_{margin}=min_{i=1}^m geo_{margin}^i $$

### fun- margin

上述的几何边缘的定义中，最终出现的结果可能会出现结果会随着不同的w的取值而出现不同的情况，因此需要从归一化的角度来更新边缘 $$ fun_{margin}^{(i)}=\frac{y^{(i)}*(\omega^TX^{(i)}+b)}{||w||} $$ 同样定义数据集的fun margin $$ fun_{margin}=min_{i=1}^m\ fun_{margin}^{i} $$

### 得到模型（model）

最直观的，我们希望所有点的margin可以都以大于零，在这个约束的基础上，数据集的margin可以越大越好 $$ target:max[min_{i=1}^m y^{(i)}*(\omega^TX^i+b)]\ s.t.:y^{(i)}*(\omega^TX^i+b)>=0\ ||w||=1 $$ 在上述直觉的基础上，需要对模型进行进一步的调整

1.  我们希望所有的margin都可以大于零，也可以说是希望所有的margin都大于最小margin（目标函数）的值    $$    max\ \hat\gamma\    s.t.:y^{(i)}(w^Tx^{(i)}+b)>=\hat\gamma\    ||w||=1    $$ 
2.  可以将||w||=1的假设调整到目标函数上    $$    max\ \hat\gamma/||w||\    s.t.:y^{(i)}(w^Tx^{(i)}+b)>=\hat\gamma    $$ 
3.  同样的跟进一步，我们假设之间的距离为1    $$    \hat \gamma=1    $$    那么模型等价于    $$    max\ \frac{1}{||w||}\    s.t.:y^{(i)}(w^Tx^{(i)}+b)>=\hat\gamma    $$    为了方便后续计算    $$    min \frac{1}{2}||w||^2    $$ 
4.  由此转换为一个凸优化问题    $$    min \frac{1}{2}||w||^2\    s.t.:1-y^{(i)}(w^Tx^{(i)}+b)<=0    $$ 

## 0x03 求解模型

### 数学基础1（凸优化）

优化问题可以分为三类：

同样所有的优化问题，都可以转换成为对偶问题，来简化求解过程

-  无约束条件的优化问题   $$   min\ f(x)\   ---\   eg:min\ f(x)=x_1^2+x_1-4   $$   求解思路：求导完事了 
-  仅含等式约束的优化问题   $$   min\ f(x)\   s.t.\ h_j(x)=0,j\in {1,2,...,n}\   ---\   eg: min\ f(x)=2x_1^2+3x_2^2+7x_3^2\   s.t.\ 2x_1+x_2=1\   2x_2+3x_3=2   $$   求解思路：利用拉格朗日乘子法，见下一节 
-  含不等式和等式的优化问题   $$   min\ f(x)\   s.t. h_j(x)=0,j\in {1,2,...,n}\   g_j(x)<=0,j\in{1,2,...,m}\ 

\   eg:min\ f(x)=x_1^2-2x_1+1+x_2^2+4x_2+4\   s.t.:x_1+10x_2>10\   10x_1-x_2<10   $$   求解思路：利用拉格朗日乘子法，但是需要满足KKT条件

### 数学基础2（拉格朗日乘子法）

### 求解上述：仅含等式约束的优化问题

首先构建拉格朗日函数 $$ L（x，\alpha）=2x_1^2+3x_2^2+7x_3^2+\alpha_1*(2x_1+x_2-1)+\alpha_2*(2x_2+3x_3-2) $$ 此时针对拉格朗日函数对变量X求导，希望等于0 $$ \frac{\partial L}{\partial x_1}=0\ \frac{\partial L}{\partial x_2}=0\ \frac{\partial L}{\partial x_3}=0 $$ 得到 $$ x_1=-\frac {1}{2}\alpha_1\ x_2=-\frac {1}{6}\alpha_1-\frac {1}{3}\alpha_2\ x_3=-\frac {3}{14}\alpha_2\ $$ 带入约束条件中求解等式，得到最优解

### 同样的求解上述：含有等式和不等式的优化问题

$$ L(x,\alpha)=x_1^2-2x_1+1+x_2^2+4x_2+4+\alpha_1(-x_1-10x_2+10)+\alpha_2(10x_1-x_2-10)\ \alpha_i>=0 $$

和上述相似对拉格朗日求导，希望等于0 $$ \frac{\partial L}{\partial x_1}=0\ \frac{\partial L}{\partial x_2}=0\ $$ 这里需要满足KKT条件 $$  *导数等于0\*  h(X)=0\ *\Sigma_{j=1}^m\alpha_ig_i(X)=0 $$

### 数学基础3（对偶求解）

>  Slater条件：一个凸优化问题如果存在一个候选x是的所有不等式约束都严格满足，则存在是原问题的对偶问题的最优解 

这样有时候可以简化操作计算,

### 求解SVM的凸优化问题

1. 首先将优化问题转换成为一个 convex optimization（P）
2. 因为满足Slater条件，P为对偶问题D是同解的

拉格朗日乘子法-P问题的函数： $$ L(x,w,a)=\frac{1}{2}||w||^2+\Sigma_{i=1}^na_i(1-y^{(i)}(wx^{(i)}+b)) $$ 原问题： $$ p=min_{w,b}max_a L $$ 转换为对偶问题 $$ d=max_amin_{w,b}L $$ 这里是将第三种凸优化问题转换成为一个无约束的第一种凸优化问题 $$ \frac{\partial L}{\partial w}=w-\Sigma_{i=1}^na_iy^{(i)}x_i^T=0\ \frac{\partial L}{\partial b}=\Sigma_{i=1}^na_iy^{(i)}=0 $$ 带入到拉格朗日函数中 $$ L=\frac{1}{2}\Sigma_{i,j=1}^na_ia_jy_iy_jx_i^Tx_j-\Sigma_{i,j=1}^na_ia_jy_iy_jx_i^Tx_j-b\Sigma_{i=1}^na_iy^{(i)}+\Sigma_{i=1}^na_i\ =\Sigma_{i=1}^na_i-\frac{1}{2}\Sigma_{i,j=1}^na_ia_jy_iy_jx_i^Tx_j $$ 约束条件： $$ s.t.:\Sigma_{i=1}^ma_iy_i=0\ a_i>=0 $$ 之后利用SMO算法来求解得到 $$ a_i $$ 之后得到hyperplane为： $$ \Sigma (a_iy^{(i)}x_i^Tx_j+b) $$

## 0x04 分类讨论

1. 在线性可分的情况下可以顺利的求解出 a_i
2. 加入松弛变量和惩罚因子来找到相对更好的hyperplane
3. 使用kernel trick来将低维映射到高纬的空间，使得在高纬空间中数据是线性可分的

详细介绍kernel trick：

本来是在低维中直接内积 $$ x_i^Tx_j $$ 之后希望都从地维转换到高纬，在计算内积 $$ \phi(x_i)^T\phi(x_j) $$ 跟进一步，希望直接在低维空间计算高维内积 $$ 线性核：x_i^Tx_j\ 多项式核：（x_i^Tx_j+1）^q\ 高斯核：exp(-\frac{(||x_i-x_j||^2))}{2}\ sigmoid:tanh(x_i^Tx_j+1) $$

## 0x05 SVM code

[github](https://github.com/chenxia31/code_Study_Practice/tree/main/CS229_2018autumn/ProblemSet(课后作业）/课后习题02) 

借鉴别人的，留一个坑自己写

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-956ab1b0ca37f05247403887591cc5d8_1440w.jpg)





添加图片注释，不超过 140 字（可选）

```
import matplotlib.pyplot as plt
import numpy as np
import random

"""
函数说明:读取数据
Parameters:
    fileName - 文件名
Returns:
    dataMat - 数据矩阵
    labelMat - 数据标签
"""
def loadDataSet(fileName):
    dataMat = []; labelMat = []
    fr = open(fileName)
    for line in fr.readlines():                                     #逐行读取，滤除空格等
        lineArr = line.strip().split('\t')
        dataMat.append([float(lineArr[0]), float(lineArr[1])])      #添加数据
        labelMat.append(float(lineArr[2]))                          #添加标签
    return dataMat,labelMat


"""
函数说明:随机选择alpha
Parameters:
    i - alpha
    m - alpha参数个数
Returns:
    j -
"""
def selectJrand(i, m):
    j = i                                 #选择一个不等于i的j
    while (j == i):
        j = int(random.uniform(0, m))
    return j

"""
函数说明:修剪alpha
Parameters:
    aj - alpha值
    H - alpha上限
    L - alpha下限
Returns:
    aj - alpah值
"""
def clipAlpha(aj,H,L):
    if aj > H:
        aj = H
    if L > aj:
        aj = L
    return aj

"""
函数说明:简化版SMO算法
Parameters:
    dataMatIn - 数据矩阵
    classLabels - 数据标签
    C - 松弛变量
    toler - 容错率
    maxIter - 最大迭代次数
Returns:
    无
"""
# 参数分别为 数据集、类别标签、常数C、容错率、最大循环次数
def smoSimple(dataMatIn, classLabels, C, toler, maxIter):#
    #转换为numpy的mat存储
    dataMatrix = np.mat(dataMatIn); labelMat = np.mat(classLabels).transpose()#转置成列向量
    #初始化b参数，统计dataMatrix的维度
    b = 0; m,n = np.shape(dataMatrix)# m=100,n=2
    #初始化alpha参数，设为0
    alphas = np.mat(np.zeros((m,1)))# 100个alpha
    #初始化迭代次数
    iter_num = 0
    #最多迭代matIter次
    while (iter_num < maxIter):
        alphaPairsChanged = 0 #用于记录alpha是否已经进行优化
        for i in range(m):
            #步骤1：计算误差Ei
            #此公式为分类决策公式
            fXi = float(np.multiply(alphas,labelMat).T*(dataMatrix*dataMatrix[i,:].T)) + b
            #print("fXi:",fXi)
            Ei = fXi - float(labelMat[i])#误差
            #优化alpha，更设定一定的容错率。
            if ((labelMat[i]*Ei < -toler) and (alphas[i] < C)) or ((labelMat[i]*Ei > toler) and (alphas[i] > 0)):
                #随机选择另一个与alpha_i成对优化的alpha_j
                j = selectJrand(i,m)
                #步骤1：计算误差Ej
                fXj = float(np.multiply(alphas,labelMat).T*(dataMatrix*dataMatrix[j,:].T)) + b
                Ej = fXj - float(labelMat[j])
                #保存更新前的aplpha值，使用深拷贝
                alphaIold = alphas[i].copy(); alphaJold = alphas[j].copy();
                #步骤2：计算上下界L和H
                if (labelMat[i] != labelMat[j]):
                    L = max(0, alphas[j] - alphas[i])
                    H = min(C, C + alphas[j] - alphas[i])
                else:
                    L = max(0, alphas[j] + alphas[i] - C)
                    H = min(C, alphas[j] + alphas[i])
                if L==H: print("L==H"); continue

                #步骤3：计算eta，即η。
                eta = dataMatrix[i,:]*dataMatrix[i,:].T +dataMatrix[j,:]*dataMatrix[j,:].T-2.0 * dataMatrix[i,:]*dataMatrix[j,:].T
                if eta == 0: print("eta=0"); continue
                #步骤4：更新alpha_j
                alphas[j] += labelMat[j]*(Ei - Ej)/eta
                #步骤5：修剪alpha_j
                alphas[j] = clipAlpha(alphas[j],H,L)
                #abs 函数  返回数字的绝对值
                if (abs(alphas[j] - alphaJold) < 0.00001): print("alpha_j变化太小"); continue
                #步骤6：更新alpha_i
                alphas[i] += labelMat[j]*labelMat[i]*(alphaJold - alphas[j])
                #步骤7：更新b_1和b_2
                b1 = b - Ei- labelMat[i]*(alphas[i]-alphaIold)*dataMatrix[i,:]*dataMatrix[i,:].T - labelMat[j]*(alphas[j]-alphaJold)*dataMatrix[i,:]*dataMatrix[j,:].T
                b2 = b - Ej- labelMat[i]*(alphas[i]-alphaIold)*dataMatrix[i,:]*dataMatrix[j,:].T - labelMat[j]*(alphas[j]-alphaJold)*dataMatrix[j,:]*dataMatrix[j,:].T
                #步骤8：根据b_1和b_2更新b
                if (0 < alphas[i]) and (C > alphas[i]): b = b1
                elif (0 < alphas[j]) and (C > alphas[j]): b = b2
                else: b = (b1 + b2)/2.0
                #统计优化次数
                alphaPairsChanged += 1
                #打印统计信息
                print("第%d次迭代 样本:%d, alpha优化次数:%d" % (iter_num,i,alphaPairsChanged))
        #更新迭代次数
        if (alphaPairsChanged == 0): iter_num += 1
        else: iter_num = 0
        print("迭代次数: %d" % iter_num)
    return b,alphas

"""
函数说明:分类结果可视化
Parameters:
    dataMat - 数据矩阵
    w - 直线法向量
    b - 直线解决
Returns:
    无
"""
def showClassifer(dataMat, w, b):
    #绘制样本点
    data_plus = []                                  #正样本
    data_minus = []                                 #负样本
    for i in range(len(dataMat)):
        if labelMat[i] > 0:
            data_plus.append(dataMat[i])
        else:
            data_minus.append(dataMat[i])
    data_plus_np = np.array(data_plus)              #转换为numpy矩阵
    data_minus_np = np.array(data_minus)            #转换为numpy矩阵
    plt.scatter(np.transpose(data_plus_np)[0], np.transpose(data_plus_np)[1], s=30, alpha=0.7)   #正样本散点图
    plt.scatter(np.transpose(data_minus_np)[0], np.transpose(data_minus_np)[1], s=30, alpha=0.7) #负样本散点图
    #绘制直线
    x1 = max(dataMat)[0]
    x2 = min(dataMat)[0]
    a1, a2 = w
    b = float(b)
    a1 = float(a1[0])
    a2 = float(a2[0])
    y1, y2 = (-b- a1*x1)/a2, (-b - a1*x2)/a2
    plt.plot([x1, x2], [y1, y2])
    #找出支持向量点
    for i, alpha in enumerate(alphas):
        if abs(alpha) > 0:#如果alpha>0,表示 alpha所在的不等式条件起作用了
            x, y = dataMat[i]
            plt.scatter([x], [y], s=150, c='none', alpha=0.7, linewidth=1.5, edgecolor='red')
    plt.show()


"""
函数说明:计算w
Parameters:
    dataMat - 数据矩阵
    labelMat - 数据标签
    alphas - alphas值
Returns:
    w
"""
def get_w(dataMat, labelMat, alphas):
    alphas, dataMat, labelMat = np.array(alphas), np.array(dataMat), np.array(labelMat)
    w = np.dot((np.tile(labelMat.reshape(1, -1).T, (1, 2)) * dataMat).T, alphas)
    return w.tolist()


if __name__ == '__main__':
    dataMat, labelMat = loadDataSet('datasets/testSet.txt')
    b,alphas = smoSimple(dataMat, labelMat, 0.6, 0.001, 40)
    # print("b,alphas",b,alphas)
    w = get_w(dataMat, labelMat, alphas)
    print("w:",w)
    showClassifer(dataMat, w, b)
```

/datasets/testSet.txt

```
3.542485    1.977398    -1
3.018896    2.556416    -1
7.551510    -1.580030   1
2.114999    -0.004466   -1
8.127113    1.274372    1
7.108772    -0.986906   1
8.610639    2.046708    1
2.326297    0.265213    -1
3.634009    1.730537    -1
0.341367    -0.894998   -1
3.125951    0.293251    -1
2.123252    -0.783563   -1
0.887835    -2.797792   -1
7.139979    -2.329896   1
1.696414    -1.212496   -1
8.117032    0.623493    1
8.497162    -0.266649   1
4.658191    3.507396    -1
8.197181    1.545132    1
1.208047    0.213100    -1
1.928486    -0.321870   -1
2.175808    -0.014527   -1
7.886608    0.461755    1
3.223038    -0.552392   -1
3.628502    2.190585    -1
7.407860    -0.121961   1
7.286357    0.251077    1
2.301095    -0.533988   -1
-0.232542   -0.547690   -1
3.457096    -0.082216   -1
3.023938    -0.057392   -1
8.015003    0.885325    1
8.991748    0.923154    1
7.916831    -1.781735   1
7.616862    -0.217958   1
2.450939    0.744967    -1
7.270337    -2.507834   1
1.749721    -0.961902   -1
1.803111    -0.176349   -1
8.804461    3.044301    1
1.231257    -0.568573   -1
2.074915    1.410550    -1
-0.743036   -1.736103   -1
3.536555    3.964960    -1
8.410143    0.025606    1
7.382988    -0.478764   1
6.960661    -0.245353   1
8.234460    0.701868    1
8.168618    -0.903835   1
1.534187    -0.622492   -1
9.229518    2.066088    1
7.886242    0.191813    1
2.893743    -1.643468   -1
1.870457    -1.040420   -1
5.286862    -2.358286   1
6.080573    0.418886    1
2.544314    1.714165    -1
6.016004    -3.753712   1
0.926310    -0.564359   -1
0.870296    -0.109952   -1
2.369345    1.375695    -1
1.363782    -0.254082   -1
7.279460    -0.189572   1
1.896005    0.515080    -1
8.102154    -0.603875   1
2.529893    0.662657    -1
1.963874    -0.365233   -1
8.132048    0.785914    1
8.245938    0.372366    1
6.543888    0.433164    1
-0.236713   -5.766721   -1
8.112593    0.295839    1
9.803425    1.495167    1
1.497407    -0.552916   -1
1.336267    -1.632889   -1
9.205805    -0.586480   1
1.966279    -1.840439   -1
8.398012    1.584918    1
7.239953    -1.764292   1
7.556201    0.241185    1
9.015509    0.345019    1
8.266085    -0.230977   1
8.545620    2.788799    1
9.295969    1.346332    1
2.404234    0.570278    -1
2.037772    0.021919    -1
1.727631    -0.453143   -1
1.979395    -0.050773   -1
8.092288    -1.372433   1
1.667645    0.239204    -1
9.854303    1.365116    1
7.921057    -1.327587   1
8.500757    1.492372    1
1.339746    -0.291183   -1
3.107511    0.758367    -1
2.609525    0.902979    -1
3.263585    1.367898    -1
2.912122    -0.202359   -1
1.731786    0.589096    -1
2.387003    1.573131    -1
```
