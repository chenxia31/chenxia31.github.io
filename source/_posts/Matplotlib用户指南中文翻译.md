---
title: Matplotlib用户指南中文翻译
tags:
  - Python
  - matplotlib
categories:
  - 程序设计
abbrlink: b8147cf
date: 2024-04-29 16:09:05
---

用户指南  

这份辅导指南包括最基本的使用方法和最好的训练帮助你开始使用matplotlib

```
import matplotlib.pyplot as plt
import numpy as np
```

## **入门例子**

Matplotlib将你的图片在**FIgure**中绘制：包括一个或多个Axes（坐标轴，包括x-y坐标、极坐标、3D坐标） 最简单的是利用[pyplot.subplots](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.subplots.html#matplotlib.pyplot.subplots)来创建一个包含坐标轴Axes的Figure，然后可以用[Axes.plot](https://matplotlib.org/stable/api/_as_gen/matplotlib.axes.Axes.plot.html#matplotlib.axes.Axes.plot)的方式绘制数据

```
fig,ax=plt.subplots() #创建一个figure，包含一个坐标轴
ax.plot([1,2,3,4],[1,4,3,2])
[<matplotlib.lines.Line2D at 0x1270552e0>]
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-3f7a90d412c61140e284529bd9edc0e2_1440w.png)





添加图片注释，不超过 140 字（可选）

在一些其他的绘图语言或者工具中并不要求你创建一个坐标轴，比如Matlab中，直接使用plot。

事实上，你可以同样实现在Matplotlib：对于任意一个Axes的绘图方法，都会有对应的方式在matplotlib.pyplot模块中让你可以在现有的坐标中中绘制。比如上面可以换写成：

```
plt.plot([1,2,3,4],[1,4,3,2])
[<matplotlib.lines.Line2D at 0x12727e400>]
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-3f7a90d412c61140e284529bd9edc0e2_1440w.png)





添加图片注释，不超过 140 字（可选）

## **Figure的组成**

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-6cc5f2a20abd152c33aa45c6e1ab6cf6_1440w.png)





添加图片注释，不超过 140 字（可选）

### **Figure**

> 完整的Figure包括坐标轴Axes的所有字类，少量的特殊特质（title、figure legend等），以及绘图各种图形元素，其中创建Figure中至少要包含一个坐标轴

可以用以下的方法创建Figure和Axes同时，也可以之后添加坐标轴来实现更加复杂的布局

```
fig=plt.figure() #没有坐标轴的Figure
fig,ax=plt.subplots() # 一个坐标轴的Figure
fig,axs=plt.subplots(2,2) #四个坐标轴的Figure
<Figure size 432x288 with 0 Axes>
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-0039e23b44d9d1f634e25d034ad84168_1440w.png)





添加图片注释，不超过 140 字（可选）

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-59d0aace4e3f02c44744e6e5d96041c0_1440w.png)





添加图片注释，不超过 140 字（可选）

### **坐标轴Axes**

> 这就是认识到关于绘图plot的地方，这是数据空间的绘制区域。一个给定的Figure可能包含很多Axes，但是一个Axes必须包含在一个Figure中 每个二维坐标轴具有两个Axis对象，具有以下的功能：

- axes.Axes.set_xlim(   )
- axes.Axes.set_ylim(   )
- axes.Axes.set_title(   )
- axes.Axes.set_xlabel(   )
- axes.Axes.set_ylabel(   )

### **单根轴axis**

> 用于控制数据的上下限，数据的刻度ticks，以及对应刻度的名称ticklabels

## **object- oriented interface 和 pyplot interface**

在matplotlib中主要存在两种方式

- 一种是制定特定的Figures和axes，之后在其中添加各种方法method（object- oriented）
- 一种是依赖于pyplot，来管理figure和axes，利用pyplot- function来绘制（pyplot ）

```
x=np.linspace(0,2,100)
fig,ax=plt.subplots()
ax.plot(x,x,label='linear')
ax.plot(x,x**2,label='quad')
ax.plot(x,x**3,label='cubic')
ax.set_xlabel('x label')
ax.set_ylabel('y label')
ax.set_title('Simple')
ax.legend()
<matplotlib.legend.Legend at 0x1273fe850>
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-09e5141f6ff37b540dc7b48b000b98e3_1440w.png)





添加图片注释，不超过 140 字（可选）

```
x=np.linspace(0,2,100)
plt.plot(x,x,label='linear')
plt.plot(x,x**2,label='quad')
plt.plot(x,x**3,label='cubic')
plt.xlabel('x label')
plt.ylabel('y label')
plt.title('simple')
plt.legend()
<matplotlib.legend.Legend at 0x127487760>
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-ed86111ea70044065f6746f7f4185670_1440w.png)





添加图片注释，不超过 140 字（可选）

在matplotlib的官方教程中实例的实现方法中均包含其中两个方法，但是希望使用者可以选择其中的一个然后坚持使用它，这样才能熟练掌握，而不是将两者混淆。

通常我们建议在jupyter notebook等即时可见中使用pyplot，而在脚本环境中使用oo方式

> Note from pylab import *这个方式已经被弃用了，这种方式现在并不鼓励使用，所以请删除

## **Backends 后段**

什么是backends？

现在许多网站上大量的文档都存在“backend”这个词，许多新学者可能会被专用名词弄混淆。matplotlib旨在利用不同的backend得到不同的输出，不同的人使用不同的手段使用matplotlib，这种通过后端来交互、嵌入到其他用户图形接口中、web应用场景、批处理脚本中应用场景

为了支持这些特殊场景，matplotlib可以设置不同的输出，这些设置变成为backedns

- 第一种是user interface backends（PyQt/PySide, PyGObject, Tkinter, wxPython, or macOS/Cocoa）
- 一种是输出格式 hardcopy backends（PNG, SVG, PDF, PS; also referred to as "non-interactive backends"）

## 第一种作为和其他系统的接口一般使用频率不大

制定图片格式并保存

- matplotlib.pyplot.savefig('filename')

```
import numpy as np
import matplotlib.pyplot as plt

plt.ion()
for i in range(3):
    plt.plot(np.random.rand(10))
    plt.show()
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-e474c7e51beb325e6da552f1841138a5_1440w.png)





添加图片注释，不超过 140 字（可选）

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-743bc3c98ed3dc19c21230ac9301b26c_1440w.png)





添加图片注释，不超过 140 字（可选）

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-4fc83de3e86baa034ec57623ee0f488c_1440w.png)





添加图片注释，不超过 140 字（可选）
