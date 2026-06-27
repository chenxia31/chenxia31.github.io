---
title: Pyomo 简单示例学习
tags:
  - pyomo
  - 运筹优化
categories:
  - 交通工程
abbrlink: 450e6b54
date: 2024-04-29 16:38:42
---

pyomo是Python中的一个建模求解语言 Pyomo(Python Optimization Modeling Objects):优化建模对象，支持复杂优化应用的数学模型的建立和分析,是一种功能齐全的高级编程语言，包含一组丰富的支持库；建模的过程是科学研究、工程和商业许多方面的基本过程，建模涉及系统或现实世界对象的简化表示的制定。可以调用cplex这样的求解器来进行计算。

[Cplex12.10--Linux--(Py3~Py38安装过程（附安装包）)](https://zhuanlan.zhihu.com/p/479835520)

因此像Pyomo的建模工具可以用于：

1. 解释系统中出现的现象
2. 预测系统的未来状态
3. 识别系统中可能的最坏情况或者最低成本的极值点
4. 分析权衡来给予决策者支持

常见的求解器：

- CBC，开源求解器（COIN-OR）开发的线性规划求解器，性能不足
- GLPK（GNU Linear Programmed Kit）是GNU维护一个线形规划工具包，对于求解大规模的额线性规划问题速度缓慢
- CPLEX，是IBM开发的商业线性规划求解器，可以求解LP,QP,QCQP,SOCP等四类基本问题和对应的MIP，社区版仅支持低于1000变量的使用，教育版无限制，建模时间长，但是求解速度非常快
- Gurobi，是CPLEX团队创始人重新创建的商业求解器，和cplex速度差别不大，相对来说比Cplex好一点

Pyomo的学习网站

[Installation - Pyomo 6.4.0 documentation](https://pyomo.readthedocs.io/en/stable/installation.html)

### 0x01 Pyomo overview

### 1.1 Mathematical modeling 数学建模

数学模型是用形式化的语言表示系统只是，以下数学概念是现代建模活动的核心

1. 变量variable，变量代表模型的未知或变化部分，（例如是否做出决定，或系统结果的特征），变量所取的值通常称为解，通常是优化过程的输出
2. 参数parameters，参数表示为执行优化必须提供的数据，实际上在某些设置中data用来代替parameters
3. 关系relations，这里是在定义模型的不同部分如何相互链接的方程、不等式或者其他的数学关系
4. 目标goal，反应被建模系统的目标和功能

在计算机求解的过程中可以分为建模和求解两个方面，计算性能的替僧是的数学模型的数值分析称为一项司空见惯的活动，但是如果没有建模语言，设置输入文件、执行求解器以及从求解器中输出中提取结果的过程即繁琐又容易出错，在发生错误时难以调试，在大规模的实际应用程序中这种困难更加复杂，此外优化软件包使用的格式很多，而许多优化器实际识别的格式很少，因此应用多个优化器求解来分析模型会带来额外的复杂性

在这个过程中pyomo希望拓展用于python的数学建模，并调用对应的优化求解器来辅助求解，在这个过程中其具有的特征有

- 开源
- 可定制
- 求解器集成
- 具有非常robust的语言、广泛的文档、丰富的标准库集，对现代编程中类和函数的支持

### 1.2 Overview of modeling components and processes建模组建和过程描述

简单建模过程的基本步骤

1. 创建模型并声明组建
2. 实例化模型
3. 应用求解器
4. 询问求解器结果

在整个过程中pyomo的建模组件定义来模型的不同方面，包括现代AML通常支持的建模组件，

sets, symbolic parameters, decision variables, objectives，constraints

在pyomo中定义相关的python类

- Set，用于定义模型实例的数据
- Param，用于定义模型实例的参数数据
- Var。模型中的决策变量
- Objective，模型中最小化或最大化的目标函数
- Constraint，对模型中的Var施加约束限制的表达式

### 0x02 Abstract versus concrete models抽象模型和具体模型

使用类AbstractModel（）可以使用表示数据值的符号来定义数学模型，，例如下列表示一个线性程序，用于找到向量的最优值x，包括参数n和b，和参数向量a和c

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-386b10c9d37e5fdac8c32aae59460624_1440w.jpg)





添加图片注释，不超过 140 字（可选）

使用类ConcreteModel（）来建立具体模型

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-4e46c5aada9960304314c7536a55b5d7_1440w.jpg)





添加图片注释，不超过 140 字（可选）

python程序可能更喜欢 编写具体模型，而其他的一些代数建模语言的用户可能更喜欢编写抽象模型

### 2.1 Simple models简单模型

```
import pyomo.environ as pyo
model=pyo.ConcreteModel()
model.x=pyo.Var([1,2],domain=pyo.NonNegativeReals)
model.Obj=pyo.Objective(expr=2*model.x[1]+3*model.x[2])
model.Constraint1=pyo.Constraint(expr=3*model.x[1]+4*model.x[2]>=1)
```

虽然规则函数也可以用于指定Constraint和Objective，但是在此示例中使用expr来仅在具体模型中可用的选项，来直接指定表达式

### 2.2 Abstract Models抽象模型

```
from _future_ import division
import pyomo.environ as pyo

model=pyo.AbstractModel()

//  定义一些参数的集合
// within 用于验证分配给参数数据值的选项的使用
model.m=pyo.Param(within=pyo.NonNegativeReals)
model.n=pyo.Param(within=pyo.NonNegativeReals)

// 使用rangeset来声明集合是一个整数序列
Model.I=Pyo.RangeSet(1,model.m)
Model.J=Pyo.RangeSet(1,model.n)

// 当集合Set作为Param的组件参数给出之后，表明该集合将索引参数
model.a=pyo.Param(model.I,model.J)
model.b=pro.Param(model.I)
model.c=pyo.Param(model.J)

//根据参数j定义变量，第二个参数表示Variable指定的参数域
model.x=pyo.Var(model.J,domain=pyo.NonNegativeReals)

/*
在抽象模型中，promo表达式通常通过def来定义函数给定目标和约束声明，该def为函数及其参数建立一个名称，当pyomo使用函数来获取objective或者constraint时候，总是将模型作为第一个参数输入
因此在pyomo中声明此类函数时，模型始终是第一个形式参数，如果需要请遵循其他参数
定义的过程中有，常见的
sum函数，来表示求和
summation（）表示两个参数在他们的索引的乘积之和，默认最小化，如果希望最大化需要加入senese=pyo.maximize
*/

//定义目标函数
def obj_experssion(m):
    return pyo.summation(m.c,m.x)//??这里应该是矩阵相乘
model.OBJ=pyo.Objective(rule=obj_experssion)//？？这里的参数输入为什么是rule

def ax_constraint_rule(m, i):
    # return the expression for the constraint for i
    return sum(m.a[i,j] * m.x[j] for j in m.J) >= m.b[i]

/*
从抽象模型中可以看出，我们需要为每个值设置一个约束i从1到m
为参数化表达式i，我们将它作为形式参数包含在声明约束表达式的函数中，使用pyomo.constraint参数
因为我们模型具有许多相同形式的约束，并且我们创建来一个集合，model.I这些约束可以在其上被索引，因此这是约束声明的第一个参数，
下个参数给出来将用于生成约束表达式的规则，

这个约束声明model.I将创建一个由集合索引的约束列表，并且对于每个成员model.I都会调用函数ax-constraint-rule并传递给模型对象以及成员model.I
*/
# the next line creates one constraint for each member of the set model.I
model.AxbConstraint = pyo.Constraint(model.I, rule=ax_constraint_rule)
```

为了使用这个模型，必须给出参数值的数据，可以提供数据的文件，格式为dat

### 0x03 Pyomo model setting

- Any = 所有可能的值
- Reals = 浮点值
- PositiveReals = 严格的正浮点值
- NonPositiveReals = 非正浮点值
- NegativeReals = 严格的负浮点值
- NonNegativeReals = 非负浮点值
- PercentFraction = 区间 [0,1] 中的浮点值
- UnitInterval = PercentFraction 的别名
- Integers = 整数值
- PositiveIntegers = 正整数值
- NonPositiveIntegers = 非正整数值
- NegativeIntegers = 负整数值
- NonNegativeIntegers = 非负整数值
- Boolean 布尔值，可以表示为 False/True、0/1、'False'/'True' 和 'F'/'T'
- Binary = 整数 {0, 1}

### 3.1 Sets

```
pyo.Set()
* dimen,集合参数的维度
* doc，描述集合的字符串
* filter，在构造过程中使用布尔函数
* initialize，包含set的初始成员的可迭代对象
* ordered，一个bool来表示集合是有序的
* validate，验证新成员数据的bool
* within，用于验证的集合

pyo.rangeSet()
生成一个【min,max,step】
```

### 3.2 Parameters

使用该词表示必须提供的数据，以便为决策变量找到最佳的分配

```
pyo.Param()
* default
* doc
* initialize 返回用于初始化参数值的数据函数
* mutable，bool来设置是否允许在param初始化后更改值
* validate，一个回调函数，接受模型、建议值和建议值的索引
* within，用于验证的集合，指定有效参数值的域
```

### 3.3 Varibles

```
pyo.Var()
* bounds
* domain
* initialize,为变量提供初始值，或者提供初始索引值
```

### 3.4 Objectives

返回试图最大化或最小化值的变量函数，可以构建一个默认参数位model的函数来作为输入

```
def ObjRule(model):
    return 2*model.x[1] + 3*model.x[2]
model.obj1 = pyo.Objective(rule=ObjRule)
```

可以使用sense参数来最大化

### 3.5 Constraints

大多数约束是使用规则创建的等式或不等式指定表达的

约束可以通过列表或集合来索引，当声明中包含列表或集合作为参数，元素会迭代传递给规则函数

```
model.A = RangeSet(1,10)
model.a = Param(model.A, within=PositiveReals)
model.ToBuy = Var(model.A)
def bud_rule(model, i):
    return model.a[i]*model.ToBuy[i] <= i
aBudget = Constraint(model.A, rule=bud_rule)
```

### 求解实例

```
# 学习promo中建模的基本过程
# min 2*x+3*y
# s.t. 3x+4y>=1
# x,y>=0
# 尝试构建模型，求解并得到最终的结果
from pyomo.environ import *
import pyomo.environ as pyo

# 建立实际模型
model=ConcreteModel()
model.x=Var([1,2],domain=pyo.NonNegativeReals)
model.Obj=Objective(expr=2*model.x[1]+3*model.x[2])
model.Constraint1=Constraint(expr=3*model.x[1]+4*model.x[2]>=1)

opt=pyo.SolverFactory('cplex')
opt.solve(model)
print(pyo.value(model.x[1]))
print(pyo.value(model.x[2]))
```
