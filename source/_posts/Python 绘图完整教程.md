---
title: Python 绘图完整教程
tags:
  - 文档阅读
  - Python 可视化
  - 绘图教程
categories:
  - 程序设计
abbrlink: 7f325577
date: 2024-04-29 19:17:26
---

## 0x00 Basic setting

> What is matplotlib? Matplotlib is a comprehensive library for creating static, animated, interactive visualization in Python. We can as follows:

1. 创建高质量的图片
2. 创建可以交互的、放大、拖动、更新的图片
3. 定制化风格和尺寸
4. 导出为不同的格式
5. 在Jupyter notebook或其他应用中使用
6. 丰富的第三方库

> 

在使用中图片可以按照A4比例来进行设置，同时设置对应的字体和颜色

```bash
mm = 1/25.4
# plt.figure(figsize=(10*mm,20*mm)
plt.rcParams['font.family']= "Times New Roman"
mpl.rcParams['xtick.labelsize'] = 10
mpl.rcParams['ytick.labelsize'] = 10
plt.rcParams['mathtext.fontset']='cm'
plt.style.use('tableau-colorblind10')
```

## 0x01 Intro to Matplotlib pyplot

### 1.1 两种绘图方式

1. 面向对象（Objetc- oriented style）的绘图方式将图片的fig和axis区分开来，使用plt.subplots( )来创建figure对象
2. 函数编程，直接使用封装好的plt.figure() plt.plot()来创建figure和绘图对象

### 1.2 图片对象包括哪些属性

![image-20240429192904205](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429192904205.png)

```bash
fig,ax = plt.subplots(figsize=(150*mm,100*mm))

ax.set_title('The title stays',fontsize=10)
ax.set_xlabel('xlabel stays',fontsize=10)
ax.set_ylabel('ylabel stays',fontsize=10)
ax.xaxis.set_major_formatter(mpl.ticker.FormatStrFormatter('%.1f'))
ax.yaxis.set_major_formatter(mpl.ticker.FormatStrFormatter('%.1f'))
ax.xaxis.set_major_locator(mpl.ticker.MultipleLocator(1))
ax.yaxis.set_major_locator(mpl.ticker.MultipleLocator(1))
ax.xaxis.set_minor_locator(mpl.ticker.MultipleLocator(0.5))
ax.yaxis.set_minor_locator(mpl.ticker.MultipleLocator(0.5))
ax.set_xscale('linear')
ax.set_yscale('linear')
ax.set_xticks(np.linspace(0,10,11))
ax.set_yticks(np.linspace(0,10,11))

ax.plot(np.linspace(0,10,100),np.linspace(0,10,100),label='1:1',color='k',linestyle='--')
ax.scatter(np.linspace(0,10,20),np.sin(np.linspace(0,10,20)+1),label='1:2',color='g',marker='o')
ax.legend(loc='upper left',fontsize=10,frameon=False)
ax.grid('major',color='k',linestyle='--',linewidth=0.1)
ax.spines['right'].set_color('None')
ax.spines['top'].set_color('None')
ax.spines['bottom'].set_position(('data',1)) #将x轴移到y=0处
ax.spines['left'].set_position(('data',1)) #将x轴移到x=0处
ax.text(6,9,"function:y=x",size=15,color='r',style='italic',weight='light',bbox=dict(facecolor='w',alpha=0.5))
for coord in np.linspace(3,7,10):
    ax.text(coord,coord+1,'%.0f'%coord,ha='center',va='bottom')

ax.annotate('annotate',xy=(9,9),xytext=(7,6),arrowprops=dict(arrowstyle='->',connectionstyle='arc3,rad=0.2'))

ax2 = ax.twinx()
ax2.set_ylabel('ylabel stays',fontsize=10)
ax2.set_yscale('linear')
ax2.set_yticks(np.linspace(0,10,11))
ax2.set_ylim(0,10)
X,Y = np.meshgrid(np.linspace(0,10,11),np.linspace(0,10,11))
Z = np.sin(X)+np.cos(Y)
pc = ax2.pcolormesh(X,Y,Z,cmap='RdBu_r',vmin=-1, vmax=1,alpha=0.3)
fig.colorbar(pc,ax=ax2,shrink=0.8,pad=0.1)
ax2.set_ylabel('colorbar',fontsize=10)
```

### 1.3 Pyplot 设置

上面的图形选项很多，从学习的角度可以全而广的了解，从实际使用的角度可能只会用到其中的几个。

color：pink。lightblue；

```
ax.set_prop_cycle(color=['#1f77b4', '#aec7e8', '#ff7f0e', '#ffbb78', '#2ca02c', '#98df8a','#d62728', '#ff9896', '#9467bd', '#c5b0d5', '#8c564b', '#c49c94','#e377c2', '#f7b6d2', '#7f7f7f', '#c7c7c7', '#bcbd22', '#dbdb8d','#17becf', '#9edae5'])
```

colormap：RdBu

linestyle：https://matplotlib.org/stable/gallery/lines_bars_and_markers/linestyles.html#

marker：https://matplotlib.org/stable/gallery/lines_bars_and_markers/marker_reference.html

等等，除此之外还需要设置针对不同的图增加不同的表达方式。这个就需要从图的种类开始学习。

### 1.4 Image展示设置

可以通过PIL的Image.open实现图像转换为numpy.array，之后使用imshow方式来可视化图片。同时可以图像进行通道遮盖、色彩映射、添加colorbar等展示，并计算色彩范围，重塑图片大小、插值等操作

## 0x02 Figure from the Matplotlib

选择官方https://matplotlib.org/stable/gallery/index.html 中一些比较有意思的作为参考

### 2.1 直线（Lines）、条形图（Bar）、标记（Markers）

- ax.bar_label 给bar添加标签
- ax.bar(…,xerr=error,…) 添加误差线
- 通过添加upperlimits来设置误差线的箭头
- fill_between 填充，可以设置where参数
- dash style的设置。set_dash间隔
- steps 和 stair设计

### ![image-20240429192918364](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429192918364.png)

### ![image-20240429192938968](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429192938968.png)2.2 图片（Images），轮廓（Contours）和平面（Filed)

- 添加水印 fig.figimage
- 绘制轮廓
- matshow显示二维数组

![image-20240429192950571](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429192950571.png)

### 2.3 子图（Subplot），坐标轴（Axes）和图（Figures）

- 在axes中增加add_axes()
- 创建多张axes之间的链接
- 放大效果，axins插入一个框，同时设置set_xlim和set_ylim

![image-20240429193000016](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429193000016.png)

### 2.4 统计（Statistics）

- 箱图boxplot，可以选择vert垂直标记，path_artist是否填充颜色，labels
- 箱型图hist，可以返回cumulative的数据

### ![image-20240429193009837](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429193009837.png)2.5 饼图（Pie）和极坐标图（Polar charts)

- 有pie绘制图，用bar绘制堆叠图，之后使用connectionPatch来进行两者的链接

![image-20240429193021170](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429193021170.png)

### 2.6 文本（Text）、标签（Label）和标记（Annotation）

- 不玩花的

### 2.7 pyplot module

- 辅助线 axhlines，axvhline

### 2.8 颜色（Color）

- colormap，https://matplotlib.org/stable/gallery/color/colormap_reference.html#sphx-glr-gallery-color-colormap-reference-py
- colors，https://matplotlib.org/stable/gallery/color/named_colors.html#sphx-glr-gallery-color-named-colors-py

### 2.9 形状和（Shapes）和集合（Collections）

- 不玩花的
- 通常在matplotlib patch中的形状或者路径https://blog.csdn.net/qq_27825451/article/details/82967904

### 2.10 风格指南（Style sheet）

- 集合 https://matplotlib.org/stable/gallery/style_sheets/style_sheets_reference.html#sphx-glr-gallery-style-sheets-style-sheets-reference-py
- 选择多了很难呀

### 2.11 坐标轴网格

- 略

### 2.12 坐标轴设计

- 略

### 2.13 特殊例子 Show case

- xkcd非常好玩
- 好看的颜色

![image-20240429193032886](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429193032886.png)

### 2.14 动画 Animation

- 调用animation.FuncAnimation就可以实现动图
- 不玩花的

### 2.15 事件句柄 （Event handling）

- 动态交互，不玩花的

### 2.16 混杂的（Miscellaneous）

- 给图添加table，可以使用plt.table对象

![image-20240429193045568](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429193045568.png)

### 2.17 3D绘图（3D plotting）

### 2.18 缩放（Scales）

### 2.19 特殊绘图（Specialty plots）

### 2.20 坐标框（Splines）

### 2.21 标签（Ticks）

- 坐标

https://matplotlib.org/stable/gallery/ticks/tick-formatters.html#sphx-glr-gallery-ticks-tick-formatters-py

![image-20240429193055908](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429193055908.png)

### 2.22 单位（Units）

- 我超！可以从basic_unit中导入cm

![image-20240429193104816](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429193104816.png)

### 2.23 嵌入（Embedding into GUI）

- 略

### 2.24 组件（Widgets）

- 指针 cursor添加一些操作，tracking position
- 按钮 button
- 确认 check
- 选择 selector
- 选择器 menu
- 鼠标 mouse
- 区间选择 rangeslider
- 

似乎没这个必要哈哈哈

![image-20240429193114203](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/image-20240429193114203.png)

### 2.25 用户例子（Userdemo）

- 
