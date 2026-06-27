---
title: CPLEX 安装指南
tags:
  - 教程
categories:
  - 计算机科学
abbrlink: 37cef8cc
date: 2024-04-29 16:09:24
---

本文主要介绍在Centos7上的cplex安装，以及在python3.7和python3.8中的pythonAPI的调用，⚠️Cplex是一个单独的软件cplex studio、在python中的库为cplex，同时在此基础上封装的更面向对象的有docplex

注意 不同版本对cplex的要求，12.10官方支持python3.6和3.7，也可以通过修改版本验证文件来在python3.8使用！

更新链接：https://drive.google.com/file/d/14nnCQiNPJ4tEejqj5uJ2seiPDyN4N3xJ/view?pli=1

## 0x01简介

Cplex是IBM公司开发的一款商业版的优化引擎，主要分为：

1. 免费版（在python3.6～3.7可以直接pip安装）
2. 付费版，价格比较贵
3. 教育版，需要申请，但由于IBM并不重视网速较慢，因此可以用别人的安装包

Cplex专门用于求解大规模的线性规划（LP）、二次规划（QP）、带约束的二次规划（QCQP）、二阶锥规划（SOCP）等四类基本问题，以及相应的混合整数规划（MIP）问题。

这里是官网的一些简介

IBM Docswww.ibm.com/docs/en/icos/12.8.0.0?topic=cplex-setting-up-python-api

### 0x02 安装Cplex studio （centos7）

安装包：

https://drive.google.com/file/d/14nnCQiNPJ4tEejqj5uJ2seiPDyN4N3xJ/view?usp=sharingdrive.google.com/file/d/14nnCQiNPJ4tEejqj5uJ2seiPDyN4N3xJ/view?usp=sharing

安装JDK（属于基础环节配置，网上有很多教程）

参考这份博客

[CentOS 7 安装JDK 1.8 环境教程](https://www.timberkito.com/?p=12)

```
#查看云端yum库中支持安装的JDK库
yum search java|grep jdk
#选择版本安装JDK
yum install -y java-1.8.0-openjdk*
#安装完成后是否成功
java -version
```

创建安装包的目录（推荐在/tmp在存储，作为暂存文件）

```
# 创建目录
mkdir cplex
# 先上传文件到这个目录中
# 设置权限
chmod u=rwx,g=rwx,o=rx cplex_studio1210.linux-x86-64.bin 
#开始安装
 ./cplex_studio1210.linux-x86-64.bin 
# 1. 语言选择English
# 2. 软件介绍
# 3. 协议介绍
# 4. 安装路径，推荐，也可以自己设置
/toot/cplex/cplex
# 5. 安装python引擎？确认就完事了
# 6. 是否收集用户数据
# 7. 安装成功
```

配置环境变量

```
vi  ~/.bashrc 
# vim编辑器基本操作：insert插入、：wq保存退出
# 在最后一行加入下列
export PATH=
$PATH:/root/cplex/cplex/cplex/bin/x86-64_linux/
:/root/cplex/cplex/cpoptimizer/bin/x86-64_linux/
# 保存退出后，保存文件
source ~/.bashrc
# 测试是否安装成功
cplex -c read /root/cplex/cplex/cplex/examples/data/afiro.mps
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-0ff286604f1bd4116bed2eade167df4f_1440w.png)





成功✅

## 0x03 python中的使用

按照官网的说法是在cplex的目录下面找到set up.pyl来安装

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-24a65f36c12eaed0cf5e988e3483c107_1440w.png)





添加图片注释，不超过 140 字（可选）

```
python setup.py install
python setup.py install --home yourPythonPackageshome/cplex
```

出现一大堆，也没有报错，但是就是import不了，所以我们可以自己将cplex中已经生成的python的cplex包复制粘贴到我们需要对应的python的site-package下，conda下的

在Anaconda-envs-【conda name】-lib-【python-vesion】

可以将cplex中的cplex/cplex/cplex/python/python/3.7/[系统]/cplex目录；复制到conda的python的site- package下。这次测试的是root/anaconda3/envs/[conda name]/lib/python3.8/site-package/里就行了

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-966cc875ea7331ca473619556bff9619_1440w.png)





官方支持python3.6、python3.7

但是并不支持python3.8，可以在cplex包里面找到内置函数/_internal中找到/_p ycplex_platform.py来修改，可能会有风险，更改需要谨慎

```
# ------------------------------------------------------------------------------
# Licensed Materials - Property of IBM
# 5725-A06 5725-A29 5724-Y48 5724-Y49 5724-Y54 5724-Y55 5655-Y21
# Copyright IBM Corporation 2008, 2019. All Rights Reserved.
#
# US Government Users Restricted Rights - Use, duplication or
# disclosure restricted by GSA ADP Schedule Contract with
# IBM Corp.
# ------------------------------------------------------------------------------
"""Imports the shared library on supported platforms."""

import platform

from sys import version_info

ERROR_STRING = "CPLEX 12.10.0.0 is not compatible with this version of Python."

if platform.system() in ('Darwin', 'Linux', 'AIX', 'Windows', 'Microsoft'):
    if version_info < (3, 6, 0):
        raise Exception(ERROR_STRING)
    elif version_info < (3, 7, 0):
        from cplex._internal.py36_cplex12100 import *
    elif version_info < (3, 9, 0):
        from cplex._internal.py37_cplex12100 import *
    else:
        raise Exception(ERROR_STRING)
else:
    raise Exception("The CPLEX Python API is not supported on this platform.")
```

后续安装docplex

```
pip install DOcplex
```

### 0x04 测试

来简单的测试一下，求解这种问题

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-b0cf308e581e156bb48c875a8548de14_1440w.png)





求解模型

```
from docplex.mp.model import Model  #导出库，只用这一个就够了
model = Model() #创建模型
var_list = [i for i in range(0, 7)] #创建列表
X = model.binary_var_list(var_list, lb=0, name='X') #创建变量列表
#设定目标函数
model.maximize(11* X[0] + 9 * X[1] + 29 * X[2]+9* X[3]+21*X[4]+31*X[5]+22*X[6])  
#添加约束条件
model.add_constraint(X[0]+X[1]+X[2] <= 2)
model.add_constraint(X[3] + X[4] >=1)
model.add_constraint(X[5] + X[6] >=1)
model.add_constraint(10* X[0] + 8* X[1] + 20 * X[2]+5* X[3]+13*X[4]+22*X[5]+10*X[6] <=60)
sol = model.solve() #求解模型
print(sol)  #打印结果
```

结果

```
solution for: docplex_model1
objective: 94
X_0=1
X_3=1
X_4=1
X_5=1
X_6=1
```

over！！
