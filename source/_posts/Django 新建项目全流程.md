---
title: Django 新建项目全流程
tags:
  - Django
  - 网站开发
categories:
  - 计算机科学
abbrlink: c5f0b1c2
date: 2024-04-29 16:09:22
---

文本希望根据Django作为框架，前端利用Vue来实现个人博客网站的开发

作为一个外行人看专门的Django的书本会被一些基础知识所拖累，比如HTML中的一些基本概念，然而抛去细节，用自己的方式来完成一个比较难的（难度不会特别高，但是又会比官方教程学习）的多一些，这可能是本教程的意义在此

## 0x00 基础知识

网站上是后端服务器生成视频、文字、图片、数字等等数据，通过HTTPS或者各种网络通信协议来传递到前端浏览器中进行显示（B-S框架），这涉及三个方面：

1. 服务端（server）需要什么逻辑处理程序语言？需要什么数据存储语言？
2. 之间通信如何安排？
3. 前端显示的格式是什么？

幸运的是，这里我们有一套标准，前端显示是HTML（html+css+js），在此基础上有也有一些框架如Vue、React来讲一些漂亮的格式固定化方便调用，通信中Django作为框架已经处理好了，所以我们用它就ok了！，服务端逻辑处理程序语言中python、C、Java等等都可以，重要的是要处理逻辑，这里使用python就好，数据管理可以使用MySQL

其中再拿出Django单独说，它不仅仅服务于网络通信，也可以帮助后台网站模版的组织、帮助利用python语言来控制服务库，同时将前后端开发中复杂的文件关系使用MVC模式

1. model：处理与数据相关的业务（人话：可以用python语言来增删改查mysql，所以不用学mysql具体语句）
2. template：处理与表现相关的决定（也就是前端的网页是如何显示的）
3. view：存取模型及调用恰当模版的相关逻辑（也就是程序的本质，比如前端点击一个按钮，后端需要运算一段程序，来返回结果，这就是需要的工作）

在此基础上，我们希望搭建自己的个人博客系统，[主要参考别人的文章](https://www.dusaiphoto.com/article/4/)，分析个人博客的逻辑其实比较简单：

服务端存储博客的原始数据、前端中显示博客页面，使用view来实现一些功能，使用template来实现原始博客的美观并在前端生成，利用model来保存博客数据

在一个博客应用中，需要创建的逻辑包括

- 博客首页、用来展示最近的几项内容
- 内容详情页，用来详细展示某项内容
- 评论处理器用来响应为一项内容添加评论的操作

**无论做什么，都需要在coding之前认清楚自己想做什么，当然跟着别人的教程做不用考虑这些**

开发环境：python3.7+Django 2.2（centOS），利用conda安装环节

>  conda create -n djangoblog python=3.7 // 用来生成python的环节，-n后面代表是djangoblog名称，后续从大 activate djangoblog就行了 conda acticate djangoblog Pip install django==2.2 

本文借鉴了很多网上的教程，若侵删

[Django搭建个人博客-杜塞](https://www.dusaiphoto.com/article/2/)

[廖雪峰个人网站](https://www.liaoxuefeng.com/)

[菜鸟教程](https://www.runoob.com/)

[W3School](https://www.w3school.com.cn/)

[Django文档](https://docs.djangoproject.com/zh-hans/4.0/)

## 0x01 使用Django

### 1.1 创建APP

>  // cd到对应的目录，并激活对应的conda django-admin startproject my_blog 

由此创建的网站文件夹(由于是Linux系统没有db.sqlite3,奇怪)

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-2e194338e53234947f6167f9feaea430_1440w.jpg)





添加图片注释，不超过 140 字（可选）

之后

>  cd my_blog python manage.py runserver 0.0.0.0:80 // 后面是设置ip地址和域名，当然可以不设置 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-ead7b595b7d8f101ff1a282e35205cf7_1440w.jpg)





添加图片注释，不超过 140 字（可选）

然后创建APP，在Django中每个app代表一个功能模块，开发者可以将不同功能的模块放到不同的app中，方便代码的复用，app就是项目的基石，因此开发博客的第一步就是创建新的app

>  python manage.py startapp article 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-3fcc544366ed58a481d28722ad30372e_1440w.jpg)





添加图片注释，不超过 140 字（可选）

每个文件夹的简单功能；

- /my_blog/db.sqllite3 是轻量级的数据库文件；/my_blog/manage.py 是项目执行文件的入口
- /my_blog/article 是 刚创建的app，用来防治博客文章相关的代码，后台管理文件admin、数据模型文件models、视图文件view、存放数据迁移文件migrations
- /my_blog/my_blog 其中setting.py包含项目的配置参数，urls.py 包含项目的根路由文件

新建app需要做的简单的事情

1. 注册app

在setting里面添加就行

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-37f94e4088422bf2b6753ae320976502_1440w.jpg)





添加图片注释，不超过 140 字（可选）

1. 修改项目的中的urls.py，这里将根路由转发到app的路由（这里转发到article的urls）这里的路由配置比较复杂，后续需要深入了解

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-85c2d74429a077d836d5d1440b7dfd3a_1440w.jpg)





添加图片注释，不超过 140 字（可选）

>  来看path中相关的参数 article/ 表示app的访问路径 include 将路径分发到下一步处理 namespace 保证反查到唯一的url 

1. 修改app中的urls

```
from django.urls import path

# 正在部署的应用的名称
app_name = 'article'

urlpatterns = [
    # 目前还没有urls
]
```

这里暂时还是空的

### 1.2 创建model

数据库的基本概念，数据库是存储电子文件的场所，存储单独的数据集合，一个数据库由多个数据表构成，在数据库中通常使用标准的SQL语句来进行增删改查，这里是一个新的内容，在面对新的内容总会出现一定的困难，因此：

Django使用对象关系映射（object relational mapping，ORM）来实现面向对象编程语言中不同类型系统的数据之间的转换

**意味着不要学习如何使用SQL，学会如何配置models.py就行**

对于博客后台的数据库中，我们设想如何描述文章这个对象“

>  需要有文章作者author、需要有文章标题title、需要有文章正文body、需要有文章的创建时间created、需要有文章更新时间update，这里的数据类型由Django自己定义，这里每个模型都被表示为Django.db.models.Model的类，需要用法可以直接去查 

Django本身具有一个简单的user账号系统，因此这里定义foreignkey可以包含与外部的环境

```
# Create your models here.
from django.db import models
# 导入内建的User模型。
from django.contrib.auth.models import User
# timezone 用于处理时间相关事务。
from django.utils import timezone

# 博客文章数据模型
class ArticlePost(models.Model):
    # 文章作者。参数 on_delete 用于指定数据删除的方式
    author = models.ForeignKey(User, on_delete=models.CASCADE)

    # 文章标题。models.CharField 为字符串字段，用于保存较短的字符串，比如标题
    title = models.CharField(max_length=100)

    # 文章正文。保存大量文本使用 TextField
    body = models.TextField()

    # 文章创建时间。参数 default=timezone.now 指定其在创建数据时将默认写入当前的时间
    created = models.DateTimeField(default=timezone.now)

    # 文章更新时间。参数 auto_now=True 指定每次数据更新时自动写入当前时间
    updated = models.DateTimeField(auto_now=True)

    # 内部类 class Meta 用于给 model 定义元数据
    class Meta:
        # ordering 指定模型返回的数据的排列顺序
        # '-created' 表明数据应该以倒序排列
        ordering = ('-created',)

    # 函数 __str__ 定义当调用对象的 str() 方法时的返回值内容
    def __str__(self):
        # return self.title 将文章标题返回
        return self.title
```

ForeignKey：

>  数据库中多种多样的数据表，有时候数据表的数据是相互关联的，因此这两张表之间就产生了关系，外健就是用来表示这种关系的，（本文中user由Django自己生成），当然也有一对多onetoonefield、多对多manytomanyfield 

内部类:

>  内部类class-meta提供模型的元数据，任何不是字段的东西，比如ordering、数据库名称、单数和复数名称 

创建数据库之后需要数据迁移，迁移时Django模型所做的更改传递到数据库中的方式，Django的前一代码时由模型文件自动生成，本质上是一个历史记录，可以勇气可嘉进行数据库的滚动更新，通过这种方式使其能够和当前的模型匹配

```
python manage.py makemigrations
```

### 1.3 编写view

Django中试图的概念是一类具有相同功能和模版的网页的集合，通常需要使用通过view来处理前端的请求，在数据库中增删改查来返回给前端显示，写好一个view函数后，还需要配置路由来讲用户请求和视图链接起来

### demo

在article/view.py

```
# 导入 HttpResponse 模块
from django.http import HttpResponse

# 视图函数
def article_list(request):
    return HttpResponse("Hello World!")
```

之后配置路由URL

```
from django.urls import path
from . import views

# 正在部署的应用的名称
app_name = 'article'

urlpatterns = [
    # path函数将url映射到视图
    path('article-list/', views.article_list, name='article_list'),
]
```

在浏览器输入

```
http://101.43.56.17/article/article-list/
```

### 1.4 view和model之间的连接

Model中虽然定义了数据表，但是这个表是空的，不方便展示view调取数据的效果，因此需要向数据表中记录一些数据

1. 创建管理员账号superuser

```
python manage.py createsuperuser
```

1. 将articlepost（之前数据表）注册到后台

在admin.py中

```
from django.contrib import admin

# Register your models here.
# 别忘了导入ArticlerPost
from .models import ArticlePost

# 注册ArticlePost到admin中
admin.site.register(ArticlePost)
```

这样就可以在后台自己上传文章啦（如何支持markdown语法？？）

### 1. 5 利用view

上述我们可以增加model中的数据，后续需要增加将数据库的数据显示在前端是view的工作，如何美观的现实是template的事情

在article/views.py内

```
from django.shortcuts import render

# Create your views here.
from django.http import HttpResponse

# 导入数据模型ArticlePost
from .models import ArticlePost

# 视图函数
def article_list(request):
    # 取出所有博客文章
    articles = ArticlePost.objects.all()
    # 需要传递给模板（templates）的对象
    context = { 'articles': articles }
    # render函数：载入模板，并返回context对象
    return render(request, 'article/list.html', context)
```

之后配置模版存放的路径，在setting文件夹内

\```python  // 55行 TEMPLATES = [     {         'BACKEND': 'django.template.backends.django.DjangoTemplates',         'DIRS': [os.path.join(BASE_DIR, 'templates')],         'APP_DIRS': True,         'OPTIONS': {             'context_processors': [                 'django.template.context_processors.debug',                 'django.template.context_processors.request',                 'django.contrib.auth.context_processors.auth',                 'django.contrib.messages.context_processors.messages',             ],         },     }, ]

```
之后在模版中list.html中加入

```html
{% for article in articles %}
    <p>{{ article.title }}</p>
{% endfor %}
```

这样就可以查看到生成了最新的模版

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-8d6fb67de4b0b5b13be636f0795ca5f5_1440w.jpg)





添加图片注释，不超过 140 字（可选）

## 阶段总结

在上述走过了Django的model数据库的建立和admin管理、view函数对前端的请求响应、templates中关于模版的使用，完整的学习了Django的MVT模式，在之后还需要改进的地方有：

1. 数据层面，数据库的建立。整个数据库系统中多个表格的使用，model建立过程中关键字的定义，变量类型的定义
2. view中数据逻辑，如果是个人博客系统需要考虑到网站整体的需求，然后在view函数中进行解析，需要设计具体的功能和具体功能的流程，最后反映到编程之中，比如页面的排名、页面的详细信息、页面的浏览等等
3. 模版和网页前端的交互，模版中规定了基础的Django数据的传输方式，还有网页中通常使用的Ajax通信来传递相关信息，以及如何使用脚手架来使得前端页面更加好看，利用HTML中特殊属性来添加元素、利用CSS来规定元素的格式、利用JS来设置页面的动态效果
4. 整体管理，包括路由配置、包括实际部署、包括变量的管理等等
