---
title: 官方文档 pandas 的阅读和感悟
tags:
  - Python
  - 文档阅读
categories:
  - 程序设计
abbrlink: fdd91ae2
date: 2024-04-29 17:14:17
---

官方文档是开发者对自己代码的解释。对于成熟的框架，官方文档可以最准确、权威的资料。如何阅读英文的官方文档一直是作为Coder weaker和English weaker的心魔，这里以Pandas文档为例子，尝试给出自己对于阅读官方文档、官方文档的查询工具、代码注释和Pandas文档的总结。

### 0x01 Why 阅读官方文档

对于质量比较高的项目，其官方文档往往能反映开发者的最直接的思想，而互联网上经过许多人的编码和解码，导致最后的意思可能和本意相差巨大。虽然可能存在其他更好的教程，但是官方文档给出的思想一定是最准确、权威的。

>  之前一直不知道GitHub在国内为什么访问这么慢。 在网上找到很多答案，比如修改DNS的、修改Hosts的、修改镜像源或者增加代理的，但其实本质的问题是GitHub.com的域名解析在国内往往需要多层中转进而因为污染等原因造成速度较慢，因此上述方法对应的是修改DNS、本机解析、设置镜像、走代理等多种方法，这些方法都没有错。但是只是给出这些方法会让人不能一览背后的思想而困惑。

### 0x02 What 官方文档

以Pandas为例子，文档通常可以分为

- Getting start：新手教程，一般会教你如何搭建一个简单的应用示例。
- User guide：使用教程，介绍技术的关键知识、思想和概念。
- API reference：API文档，包括具体的API使用细节以及机制
- Developer guide：开发文档

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-88d95021f0405610776fcf6d42d6afc4_1440w.png)





image-20230507140000120

推荐使用  [Dash gives your Mac instant offline access to 200+ API documentation sets.](https://kapeli.com/dash)  来进行官方API文档的管理。作为一个API文档浏览器(API documentation brower)和代码片段管理（Code snippet manager），可以提供的帮助有

1. 迅速
2. 直接
3. 本地
4. Alfred协作

当然常见的函数用法可以直接通过命令来获取

```
import module_name #such as import math
help(module_name) #模块帮助查询
dir(module_name) # 查询模块下所有的函数
help(module_name.func_name) #查询具体函数的用法
print(func_name.__doc__) # 打印函数的用法
```

### 0x03 Pandas 新手教程

首先介绍pandas是什么：

>  Pandas is a [Python](https://www.python.org/) package providing fast, flexible, and expressive data structures designed to make working with “relational” or “labeled” data both easy and intuitive. It aims to be the fundamental high-level building block for doing practical, **real-world** data analysis in Python.

其中基本的数据类型可以分为Series和DataFrame两种，基于NumPy构建。新手教程给出常见的Pandas使用的Topic。包括

### 3.1 创建数据、查看数据、索引数据

```
# Series 一维标记数组，可以容纳各种数据类型(int、float、string、chat、python object)
# Pandas 二维标记数据框架，可以看作是一张数据表格，或者是SQL表格
# Insight01：Pandas中的数据结构可以看作是ndarray方式的标量（通过index索引），也可以看作是dict方式的字典（可以通过key索引）
# Attention01:Pandas数据之间的运算通常是按照元素的，并且是broadcasting的。对于缺失值通常使用numpy.nan填补

# 创建数据，可以来自于 1. ndarray、2. dict、3. scalar、4. tuple of dict
d = {
    "one": pd.Series([1.0, 2.0, 3.0], index=["a", "b", "c"]),
    "two": pd.Series([1.0, 2.0, 3.0, 4.0], index=["a", "b", "c", "d"]),
}

d = {"one": [1.0, 2.0, 3.0, 4.0], "two": [4.0, 3.0, 2.0, 1.0]}
pd.DataFrame(d, index=["a", "b", "c", "d"])

data2 = [{"a": 1, "b": 2}, {"a": 5, "b": 10, "c": 20}]
pd.DataFrame(data2, index=["first", "second"])

data3= {
        ("a", "b"): {("A", "B"): 1, ("A", "C"): 2},
        ("a", "a"): {("A", "C"): 3, ("A", "B"): 4},
        ("a", "c"): {("A", "B"): 5, ("A", "C"): 6},
        ("b", "a"): {("A", "C"): 7, ("A", "B"): 8},
        ("b", "b"): {("A", "D"): 9, ("A", "B"): 10},
    }
pd.DataFrame(data3)

# 查看数据
.head() #查看dataframe的顶部
.tail() #查看dataframe的底部
.columns()
.index()
.to_numpy() #注意这个可能是一个昂贵的操作
.describe() # 显示数据的快速摘要
.T # 转置

df.sort_index(axis=1, ascending=False) #按照轴进行排序
df.sort_values(by="B")

# 更改控制台显示
info() # 用于快速显示dataframe信息
baseball.info()

to_string() # 以表格的形式返回dataframe的字符串表示
print(baseball.iloc[-20:, :12].to_string())
display.width # 更改单行的打印量
pd.set_option("display.width", 40)  # default is 80
pd.set_option("display.max_colwidth", 30)
pd.DataFrame(np.random.randn(3, 12))

# 索引数据
# 继承series的索引
df["one"]
df["three"] = df["one"] * df["two"]
df["flag"] = df["one"] > 2
del df["two"]
three = df.pop("three")
df["foo"] = "bar" #自动broadcasting
df["one_trunc"] = df["one"][:2] # 自动符合index
df.insert(1, "bar", df["one"])

# assign() 始终返回数据的副本，但是原始的dataframe并不会修改
iris.assign(sepal_ratio=lambda x: (x["SepalWidth"] / x["SepalLength"])).head()
df.loc #根据标签选择
df.iloc #根据下标选择
df[5:10] # 切片行
df[bool] # 切片行
```

### 3.2 数据缺失处理、运算函数

```
np.nan #表示丢失的数据类型

df.reindex() # 允许更改、添加和删除指定轴上的索引，同时返回数据副本
df1 = df.reindex(index=dates[0:4], columns=list(df.columns) + ["E"])
df1.loc[dates[0] : dates[1], "E"] = 1

df.dropna() # 删除任何缺少数据的行
df.fillna() # 填充确实的数据
isna() #获取值是否为nan

默认按照broadcasting进行计算
df.mean() # 计算平均值
df.mean(1) # 另外一个方向
df.apply() # 将用户自定义的函数应用于数据
df.apply(np.cumsum)
df.apply(lambda x: x.max() - x.min())

values_count()
df.str.ufunc() # 在str属性下，可以调用需要其他的字符串
```

### 3.3 集合之间的处理：合并、分组、重塑和多索引

```
pd.concat([df1,df2,df3]) # 按照axis进行合并，join参数为outer或者inner，ignore_inde重建索引
pd.merge(df1,df2,key) # 类似SQL中的连接，可以根据一个或者多个键将不同的dataframe连接起来
pd.join() # 用于key的合并

df.groupby("A")[["C", "D"]].sum() # 类似SQL中的group by

# 重塑和多索引看不懂～
```

### 3.4时间序列处理函数专题

```
resample() #将第二列数据转换成5min，有点意思
series.tz_localize() # 将时间序列转换成为本地时区
series.tz_convert() # 将时区感知时间序列转换成为另一个时区
.to_period() # 转换成为时间跨度
.to_timestamp() # 转换成为时间戳
```

### 0x04 Pandas 用户教程

在用户教程中，Pandas提供了一些更加细致的专题。这里只给出有趣的话题

### 4.1 数据结构简介 intro to data structure

和第三章的类似

### 4.2 关键基础函数 essential basic functionality

Attention02:对于dataframe的操作可以三个维度：元素的维度、行或者列的维度、整个DataFrame的维度

```
# 查看
.head()
.tail()

.shape
.index
.columns

.to_numpy()
.array()
# Attention03:更加推荐使用这两个，而不是使用values来索引。因为dataframe中常见的操作包括扩展类型，或者不同类型，使用values可能会出现性能上的问题

# 运算（elementLevel）
df1 > df2
df1*df2
df1+df2

# 二维运算，这里需要重视 1.高维和低维之间存在broadcasting 2.计算中数据丢失的情况
.add() # 相加，可以设置axis函数
.sub() # 相减，可以设置axis
.mul() # 相乘，可以设置axis
.div() # 相除，可以设置axis

.radd()
.rsub()
.divmod() # 商，和余

# 缺失值操作
fill_value=0 #可选参数
.fill_na() #利用函数填补

# 二进制比较方法 eq ne lt qt le ge
# 布尔运算符号
.empty() # 测试对象是否为空
.all() # 测试并
.any() # 测试或
.bool() # 测试单个元素

#Attention 04：不可以使用 if df:...，这样不符合布尔逻辑

# 描述性分析
sum()
mean()
quantile()
cumsum()
cumprod()

describe()
idxmin() # 寻找最小值下标 argmin
idxmax() # 寻找最大值下标 argmax
res=df.value_counts()
mode()

# discretization and quantiling
cut() #基于值的bin
qcut() #基于分位数的bin

# 函数应用：pipeline(),apply(),applymap(); 聚合函数agg() and transform()

# 尝试想象一下如何新增两个列
def extractCityName(df):
    df['cityName']=df['cityAndCode'].str.aplit(",").str.get(0)
    return df

def addCountryName(df,countryName=None):
    col='cityName'
    df['cityAndCounty']=df[col]+countryName
df_p=pd.Dataframe({'cityAndCode':['chicago','IL']})
# 现在可以有两种方式来新增心得countryName
addCounryName(extractCityName(df_p),country_name='US')#法1
df_p.pipeline(extractCityName).pipe(addCountryName,countryName='US')#法2:或者使用pipeline

# 重索引 reindex

# 迭代 iteration
for label,ser in df.items():
  print(label)
  print(ser)
for row_index,row in df.iterrows():
    print(row)

for row in df.itertuples():
    print(row)

# 排序访问
.sort_index()
.sort_values()
```

### 4.5 索引和选择数据 Indexing and selecting data

和新手教程差不多

### 4.6 多重下标和高级索引 MultiIndex and advanced indexing

和新手教程差不多

### 4.6.5 Merge、join、concatenate、compare

和SQL教程差不多

### 4.7 重塑和数据透视表

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-b4979b776f518f16933c32856474853c_1440w.png)





../_images/reshaping_pivot.png

```
# pivot() 来按照元素进行堆叠,可以选择stack或者unstack
         date variable     value
0  2000-01-03        A  0.469112
1  2000-01-04        A -0.282863
2  2000-01-05        A -1.509059
3  2000-01-03        B -1.135632
4  2000-01-04        B  1.212112
5  2000-01-05        B -0.173215
6  2000-01-03        C  0.119209
7  2000-01-04        C -1.044236
8  2000-01-05        C -0.861849
9  2000-01-03        D -2.104569
10 2000-01-04        D -0.494929
11 2000-01-05        D  1.071804

pivoted = df.pivot(index="date", columns="variable", values="value")
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-6bd477e1e00ff31ad153591bbe7b46b4_1440w.png)





../_images/reshaping_stack.png

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-1329d236b79b3157190ef93dcde0881d_1440w.png)





../_images/reshaping_unstack.png

剩余的太理论了～

### 0x05 心得

文档很多，但是其中给出开发者的default的选项，可以提高对其的理解。
