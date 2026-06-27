---
title: 工科生研0的Macbook Air M1 13inch（16+256）深度使用感受
tags:
  - Apple
  - MacBook
categories:
  - 生活杂谈
abbrlink: 9148fb62
date: 2024-04-29 16:55:56
---

在深度使用M1一年之后，在即将比如研究生生活之前，介绍一次Mac。同时也会结合自己的华为Mate40pro介绍macOS+ipadOS+harmonyOS在日常使用的习惯，说明常用的软件、硬件的配件、文件管理和同步以及password以及GTD管理、环境管理中Linux和python环境以及Git使用等，作为一次Summary

本次介绍的主体是2020年发布的M1芯片的Mac，也经历了结构更迭之苦，当然一年之后能看到ARM架构的优越性。具体规格配置如图

[MacBook Air (M1 芯片机型) - 技术规格](https://www.apple.com.cn/macbook-air-m1/specs/)

【写作时间】2022年6月12日

## 01 Software management

在使用的过程中，很多软件都没有兼容M1，兼容M1的破解版也少的可怜

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-617065001909f0346e299d2839b64853_1440w.jpg)





软件思维导图

常用的Mac软件下载网站：

[MacWk - 精品mac软件下载](https://www.macwk.com/)

### 1.1 处理不同格式文件软件

Office 三件套：docx、ppt、xlsx WPS ：同office，虽然功能会少一点，但是带来的是性能的提升 Typora：Markdown Sublime Text：打开、轻编辑 【code】、markdown、latex（快） Skim：打开PDF文件，搭配Sublime Text使用（快） PDF Expert：编辑PDF文件 iMovie：编辑视频文件 IINA：视频播放软件（快） Capture One：修图软件

### 1.2 牛刀类工具

Notion：非常好用的笔记软件 Zotero：文献管理软件，可以搭配notion使用 Marginnote3 : 阅读PDF和知识管理软件，不太好用 OmniGraffle ： 流程图绘制软件（平替 draw.io网站） Xmind：思维导图软件，正在用的 Edge：很不错的浏览器 Axure RP：原型设计软件 Parallels Desktop：虚拟机管理 Terminus：终端增强

### 1.3 润物细无声的小工具

ClashX：代理软件简介 Rectangle：窗口管理 欧路词典：查单词 iStat：硬件状态 iPic Free：markdown的图片上传 Aldente：充电管理 GifSki：压缩生成GIF文件 SVGview：查看SVG Hidden bar：状态栏设置隐藏 iShot：截图软件，可以长截图和标记（command shift 4/5可以截图和录屏） Better Zip：解压缩文件 ClipborderManagement：剪贴板管理 坚果云+Nextcloud：Webdav文件管理 cheetsheet：快捷键提醒 Monitor Control：管理显示器的亮度 Mail+Calendar+Reminder+Notes+Music：自带的软件都非常高

### 1.4 娱乐类工具

B站客户端 腾讯视频 虚拟机内的植物大战僵尸 微信-QQ 百度网盘+阿里云盘

## 02 Hardware management

Anker Nano II 65W充电头

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-835bdbe7ebd0732a725b5258d74dfeff.jpg)

广告

Anker 安克  USB-C PIQ3.0 PD20W快充头

知乎

¥55.00

去购买

小米100w快充线（2m）

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-5d7500ce9dd672ca6731d147feea5a07.jpg)

广告

ROCK 三合一数据线    一拖三

知乎

¥39.00

去购买

Belkin七合一拓展坞

罗技Master 2s

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-cb6b35636e7282f932af01caeb5a399e.jpg)

广告

Razer 雷蛇 炼狱蝰蛇 V2 游戏鼠标

知乎

¥249.00

去购买

DP1.4绿联转接线

西数移动硬盘*2T

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-7989088113b7c2078c1689abe1904e38.jpg)

广告

ROCK typec 多功能转换器 十合一

知乎

¥259.00

去购买

华为原装TypeC耳机

## 03 File management and workflow

### 3.1 Mac本地文件管理和备份

256G的固态硬盘用起来比较着急，好在速度很快，全新 M1 MacBook Air 的固态硬盘写入速度为 2190 MB/s，读取速度为 2675 MB/s；相比较手机的sfs的速度可发现该机连续读取约为 1966M/s，持续写入 1280M/s；相比较UFS3.1通常为1200M/s和800M/s，速度感觉非常不错。

在日常使用的文件中主要分为：

1. 大文件，比如下载的4K电影、爱死机系列片等，这一类文件在使用之后可以放到移动硬盘作为存储，或者上传到百度网盘
2. 不常用的文件，比如下载的文件PDF、下载的PPT、不用的课程PPT，这一类文件通常在一个阶段结束之后可以备份，比如放在移动硬盘或者放到百度网盘中
3. 小而杂的文件，临时做的ppt、下载的通知，这是管理的重点

管理方法Tag+Folder：

作为一个对macOS的的Finder热爱的人来说，非常喜欢提供tag的选项，但是常常利用Tag也会让我感到苦恼，因为会因为一个文件满足多个tag而不知道如何区分，然后新建一个Temp文件夹，久而久之所有的文件都会被放到这个临时文件夹中。就在苦苦思考的时候，我们会发现天然的无论是windows还是macOS都给我提供好了应该有的方法Folder，按照Document、Picture、Video、Download、Desktop的文件夹方式来进行分类，这里的管理原则是：

>  首先按照文件的时效性区分开： 

1. 时效较短的 download desktop
2. 时效较长的 document、picture、video

 再根据自己的Project来对不同文件类型的文件进行标记Tag，方便自己查找 Tag存在的意义是让自己容易找到他，而不是分类，因为Folder天然具有分类的作用 

### 3.2 基于Webdav的多端同步Sync

！！macOS12还没有适配坚果云的云桥模式！！

网盘通常可以分为两种

1. 大容量，作为资源库仅限App操作的，比如百度网盘、阿里云盘、夸克云盘等
2. 同步网盘，比如iCloud、坚果云、DropBox、nextcloud、one drive等

针对第一种网盘纯粹可以当作备份盘处理，虽然百度网盘推出同步空间，体验依旧非常不好。第二种网盘主要针对写作办公和同步，如果你是iPhone+iPad+Mac当然iCloud是最佳的选择，没有之一，在国内的环境使用非常的舒服，但是如果没有则需要挑选，主要的选择有：

1. 时不时抽风的one drive
2. 会员当道的坚果云
3. 自建webdav协议的nextcloud

### 3.2.1 第一种one drive

速度有时候莫名的快，有时候莫名的慢，使用很难受。同步必须在自己的电脑中新建一个文件夹作为同步，不能实现同步其他任意文件夹的功能，比较鸡肋。但是可以释放本地空间，以快捷方式保存在本地，同时作为学生可以享受超大容量。

### 3.2.2 第二种 坚果云

目前开了一年的会员，在尝试其中的使用技巧，利用坚果云来构建Mac+iPad+Mate40pro之间的文件共享体系（重点！华为居然可以将文件夹放在桌面，太好用），在国内的同步速度非常快，快到看不见。但是目前macOS12应该是不支持云桥模式的api，也就是我文件都是同步的，而不能释放本地空间

### 3.2.3 第三种 webdav协议自建nextcloud

因为本人自己有一台腾讯云服务器，所以尝试使用nextcloud构建自己的云盘，但是因为功能比较初级，想要在Mac系统中集成需要走HTTPS协议，暂时还没有搞好，但是webdav协议除了可以下载文件之外，还可以同步日历等，构建macOS+iPados+HarmonyOS的同步，但是目前https协议还没有搞好，期待后面的使用

### 3.3 Password管理

密码管理的重要性，无论是个人信息中需要的密码管理，还是掌握服务器中的使用，都需要掌握良好的米阿们

- 拖库:拖库就是指黑客通过各种社工手段、技术手段将数据库中敏感信息非法获取，一般这些敏感信息包括用户的账号信 息如用户名、密码;身份信息如真实姓名、证件号码;通讯信息如电子邮箱、电话、住址等。
- 洗库:在拖库后，取得大量的用户数据之后，黑客会通过一系列的技术手段和黑色产业链将有价值的用户数据变现。
- 撞库:撞库是黑客通过收集互联网已泄露的用户和密码信息，生成对应的字典表，尝试批量登陆其他网站 后，得到一系列可以登录的用户。很多用户在不同网站使用的是相同的帐号密码，因此黑客可以通过获取 用户在A网站的账户从而尝试登录B网址，这就可以理解为撞库攻击。

手机内部华为自带的密码管理器真的是太差劲了，为了满足多端同步的功能，既然用不了Apple的钥匙串服务，Google密码在华为中用不了，只能使用微软的EDGE，电脑和平板端使用浏览器，手机端开启浏览器外部填充功能。

当时实际的密码最好还是用备忘录或者笔记本记下来！！

### 3.4 GTD管理

它的所有方法都围绕着把所有事情都从大脑中弄出来并体系化地管理，让人专注于眼前的工作，完全信任外部体系的事务管理。

因此，GTD的方法最适用以下情况：

- 事务繁杂，容易忘记和混淆
- 害怕忘记任务而反复回想，浪费时间造成压力
- 杂务纷扰，重要的事常来不及做

通常使用reminder来将需要做的事情记录下，收集所有的事情，然后划分到长期和短期应用，之后在日历calendar中记录准确的事情，在同步过程中，依旧采用微软的outlook邮箱来实现多端同步，手机上需要下载一个Microsoft ToDo，很简洁！

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-b79640844d6dfb329cdf3aca60765b0d_1440w.jpg)





添加图片注释，不超过 140 字（可选）

### 3.5 Notes管理

笔记整理中，通常的来源包括

1. 讲座、会议等的会议记录和总结
2. 对于视频、文档、课本的阅读和总结
3. 个人博客
4. 自己的代码仓库

由于使用不同文件就让自己的笔记整理困难，因此个人选择的ALl in one的Notion来管理自己的笔记，作为一个基于Web的笔记管理，很好用，个人笔记的特征管理选择

-  Area-user ：笔记中主要分为person（自己的思考、自己的整理、自己的读书记录）；或者是school（课题组的项目、讲座）；以及一些第三方的东西，我是去study，或者做一些record 
-  Group-Tag：用于分类的依据，将自己的笔记分为四种：Library（个人的汇总表）、Post（一些单独的文章）、Wiki（完整知识体系，帮助学习）、Log（项目的日志） 
-  Name：该页面的主要名称，汇总表建议带上汇总两字、series的课程或者希望可以带上<名称>作为标识 
-  Create Date：创建日期 
-  Resource： 这个页面的根源是什么，包括个人的管理类或者思考类、school的项目日志或一些杂事、study的课程或文档 
-  Area：随便的一些tag 

## 04 Environment management

### 4.1 Homebrew —软件包管理工具

[Homebrew](https://brew.sh/index_zh-cn.html)

Homebrew是一款macOS的软件包管理工具，通过其可以很方便的install or uninstall软件工具，类似与Linux下的apt-get、node的npm等包管理工具、centos下的yum，使用homebrew可以安装系统没有预装的东西，，同时会将软件包安装到独立目录，也就是local文件夹下面的homebrew自己安装的文件，然后再通过软链接到usr/local

```
/bin/bash -c "$(curl -fsSL <https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh>)"
```

（可选的，如果希望将homebrew添加到环境变量）

```
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

常用的一些命令

```
brew install 包名
brew uninstall 包名
brew upgrade 包名

brew update
brew upgrade
brew outdated

// 查看Homebrew版本
brew --version
brew list
brew home
brew info 包名
brew doctor
```

### 4.2 Mini forge3 — macOS的conda

1. 一种是下载sh脚本，然后自己安装
2. 使用homebrew安装mini forge3

Anaconda和Miniconda到现在都没有提供M1处理器的conda环境，可以使用conda-forge提供miniforge，用来支持Apple Silicon的版本软件

下载软件的地址

[GitHub - conda-forge/miniforge: A conda-forge distribution.](https://github.com/conda-forge/miniforge/#download)

下载好mini forge3中对应的macOS+apple silicon的脚本，假设下载咋download文件夹

```
cd download

// 之后安装脚本
// 假如之前安装过，可以重新卸载再安装
// 卸载的脚本 
// rm -rf ~/miniforge3 
// rm -rf .conda 
// rm -rf .condarc
// 没有以上可以略过
bash Miniforge3-MacOSX-arm64.sh
```

完成安装之后，需要重新激活配置文件

```
source .zshrc
```

完成之后可以测试conda是否安装完成

```
conda --version
```

第二种方法是使用homebrew安装

```
brew install miniforge
```

conda常见命令的使用

```
// conda环境的创建、激活
# 查看所有conda环境
conda info -e
# 创建虚拟环境
conda create -n env_name python=python_version
Eg:conda create -n py39 python=3.9
# 激活虚拟环境
conda activate env_name
Eg: conda activate py39
# 退出虚拟环境
conda deactivate

# 删除虚拟环境
conda remove -n env_name --all

// 包的查看、安装、删除和更新
# 查看环境下所有安装的包
conda activate env_name
conda list
# 查看是否安装特定的包
conda activate env_name
conda list package_name
# 安装包
conda antivate env_name
conda insatall package_name
# 删除包
conda antivate env_name
conda remove package_name 
# 更新包
conda antivate env_name
conda update package_name

// conda 安装本地包
# 进入包所在路径 cd /d路径
cd /dE:\\Download
# 安装包 conda install --use-local 包名
conda install --use-local pytorch-1.8.0-py3.7_cuda11.1_cudnn8_0.tar.bz2

// conda清理环境
conda clean -p
conda clean 
```

### 4.3 Git — 版本控制

### 4.3.1 常见指令

[Book](https://git-scm.com/book/zh/v2)

[常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)

```
// 新建代码库
# 在当前目录新建一个 Git 代码库
$ git init
# 新建一个目录，将其初始化为 Git 代码库
$ git init [project-name]
# 下载一个项目和它的整个代码历史
$ git clone [url]

// 配置git，配置文件为gitconfig，可以在用户主目录，也可以在项目目录
# 显示当前的 Git 配置
$ git config --list
# 编辑 Git 配置文件
$ git config -e [--global]
# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"

等等

# 添加所有文件

git commit -m '[your message]'
# 上传，应该是上一次add的文件夹的信息


# GitHub的本地ssh的设置
# GitHub的本地

git config --global  --list
git config --global  user.name "这里换上你的用户名"
git config --global user.email "这里换上你的邮箱"
ssh-keygen -t rsa -C "这里换上你的邮箱"
# 后续确认就行，在GitHub上进行GUI的设置就OK
Settings -- SSH and GPG keys

# 为dnf添加
sudo dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo
# 安装就好
dnf install gh
# 之后认证
gh auto login
# 按照规定输入选择输入token就行了
```

### 4.3.2 Git - PyCharm - GitHub操作

首先安装好自己的git，然后在pycharm中的version control system中为当前的项目构建自己的git同步目录，其中的 git每次的文件会存储在相对应的.git文件夹的目录下，所以也会导致在每次提取的过程会逐渐变的越来越大，同时为自己的GitHub账户中的一些操作

1. [chenxia31.github.io](http://chenxia31.github.io/) ,主要是个人blog的使用，在其中使用markdown来撰写相关的推文，使用hexo g、hexo d，以及GitHub page来管理域名来完成自己的公开
2. code_study_pratice 这个是过去的一些操作，这里就删除，算是最开始的algorithm的东西，因为名字不好听以后就删除来，为啥不换个名字，算了换一个名字重新开始
3. Mercury这里以后就是代码学习的地方，主要分为BaseSkill（比如pandas的操作）、Course（比如CS229、CS61A、李宏毅之类的）、Kaggle（其中比赛的名称）、Leetcde（包括力扣官网的，还有一些关于算法导论中习题的刷）、latex中的一些模版、如果想要更近一步还需要有其他代码语言比如web开发、C++语言等属于Special Area

### 4.3.3 Git - VsCode - GitHub操作

暂时不需要

### 4.4 （Python）Tensorflow + Pytorch

2022-04-09 pytorch暂时没有支持GPU加速，仅仅是讨论阶段，tensorflow已经支持GPU加速，使用的是apple提供的metal的toolkit

```
conda install -c pytorch pytorch torchvision
```

[Tensorflow Plugin - Metal - Apple Developer](https://developer.apple.com/metal/tensorflow-plugin/)

在conda管理中，直接使用homebrew安装的miniforge可能会有问题，可以选择第一种下载之后手动安装的方式来管理

之后是新建一个conda环境，然后activate进去

利用conda安装相关依赖

```
// 可以管理安装的版本
conda install -c apple tensorflow-deps==2.6.0
```

然后在环境内使用pip安装相对应的tensorflow和插件

```
// 安装
python -m pip install tensorflow-macos
python -m pip install tensorflow-metal

// 如果需要卸载之后重新安装
python -m pip uninstall tensorflow-macos
python -m pip uninstall tensorflow-metal
```

测试代码

```
from tensorflow.keras import layers
from tensorflow.keras import models
model = models.Sequential()
model.add(layers.Conv2D(32, (3, 3), activation='relu', input_shape=(28, 28, 1)))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
model.add(layers.Flatten())
model.add(layers.Dense(64, activation='relu'))
model.add(layers.Dense(10, activation='softmax'))
model.summary()

from tensorflow.keras.datasets import mnist
from tensorflow.keras.utils import to_categorical
(train_images, train_labels), (test_images, test_labels) = mnist.load_data()
train_images = train_images.reshape((60000, 28, 28, 1))
train_images = train_images.astype('float32') / 255
test_images = test_images.reshape((10000, 28, 28, 1))
test_images = test_images.astype('float32') / 255
train_labels = to_categorical(train_labels)
test_labels = to_categorical(test_labels)
model.compile(optimizer='rmsprop',
              loss='categorical_crossentropy',
              metrics=['accuracy'])
model.fit(train_images, train_labels, epochs=5, batch_size=64)
test_loss, test_acc = model.evaluate(test_images, test_labels)
test_acc
```

https://zhuanlan.zhihu.com/p/408877901)

### 4.5 Linux使用

Linux 系统启动过程

- 内核的引导
- BIOS开机自检，按照BIOS中设置的启动设备来启动，操作系统接管硬件之后，首先读入/boot目录下的内核文件
- 运行init
- init是系统所有进程的起点，首先读取配置文件 /etc/inittab
- 系统初始化
- 建立终端
- 用户登陆系统
- 命令行
- ssh
- 图形界面

Linux 系统目录结构

```
ls：查看文件目录
常见目录的解释：
/bin =binaries 二进制文件的缩写，这个目录存放最常用使用的明林
/boot 存放启动Linux的核心文件，包括一些连接文件以及镜像文件
/dev 是device的缩写，存放的是Linux的外部设备，Linux中饭昂问设备和访问文件的方式是相同的
/etc 是etcetera的缩写，用来存放所有的系统管理所需要的配置文件和子目录
/home 用户的主目录，一般该目录是用用户的账号名称
/lib 是library，存放在和系统最基本的动态链接库
/media 系统会将识别的设备挂载到这个目录下
/mnt 让用户临时挂载到别的文件系统
/opt optional，是主机额外安装软件所摆放的目录，弄人是空的
/proc 是processes的缩写，是虚拟文件下i痛，这个目录的netting不再硬盘上而是在内存里面，可以直接修改里面的文件来屏蔽命令
/root 系统管理员的用户主目录
/sbin superuser binaries的缩写，存放的额是系统管理员使用的系统管理程序
/selinux 是centos特有的目录，类似于Windows的额防火墙
/usr unix shared resources的缩写，用户的许多应用程序和文件
/usr/bin 系统用户使用的应用程序
/usr/sbin 
/usr/src 内核源代码默认的存放目录
```

Linux系统是一种典型的多用户系统，不同的用户处于不同的地位，拥有不同的权限，为来保护系统的安全性，Linux系统对不同的用户访问相同的文件的权限做出了不同的规定，在Linux中常用一下两个命令来修改文件或者目录所述的用户与权限

- chown（change owner）修改所属的user和group
- chmod（change mode）修改用户的权限

```
ll 或者ls-l
来显示一个文件的属性以及文件所属于的user和group
```

Linux 文件和目录管理

Linux的目录结构为tree结构，最顶级的目录为根目录，其他目录通过挂载可以添加到tree中，通过解除挂载可以移除他们，首先区分绝对路径和相对路径

- 绝对路径 /usr/share/doc
- 相对路径 ../man

处理目录的常用命令

```
ls：list file 列出目录及文件名
cd：change directory 切换目录
pwd：佩内塔 work directory 显示目前的目录
mkdir：make directory 创建一个新的目录
rmdir：remove directory 删除一个空的目录
cp：copy file复制文件
rm：remove删除文件或者目录
mv：move file 移动文件与目录
man 【command】来查看使用文档
```

Linux的文件内容查看

```
cat 由第一行开始显示文本内容
tac 从最后一行开始显示
nl 显示的时候输出行号
more 一页一页的显示内容
less 可以
head 前几行
tail 只看后几行
```

Linux的连接link的概念，一种是硬连接，另外一种是符号连接

- hard link 通过索引界面的来连接，保存在磁盘分区汇总的文件不管是什么类型都给它分配一个编号成为inode index，在Linux汇总多个文件名指向同一个索引届电视存在的，硬连接允许一个文件拥有多个有效的文件名
- symbolic link：类似于快捷方式

Linux 用户和用户组管理

Linux系统是一个多用户多任务的分时操作系统，任何一个要使用系统资源的用户都必须首先向系统管理员申请一个账号，然后用账号的身份进入系统

用户的账号一方面可以帮助系统管理员对使用系统得到用户进行跟踪并控制他们对系统资源的访问，另一方面也可以帮助用户组织文件，并为用户提供安全性防护

实现用户账号的管理，要完成的工作主要有几个方面

- 用户账号的添加、删除和修改
- 用户口令的管理
- 用户组的管理

Linux磁盘管理好坏直接关系到整个系统的性能问题

Linux磁盘管理常用的命令为df、du、fidsk

- disk full（df）
- disk used（du）
- fdisk 用于磁盘分区

我的base环境是我之前用的

首先安装anaconda：

wget -P /tmp https://repo.anaconda.com/archive/Anaconda3-2021.11-Linux-x86_64.sh

wget是工具的名称，后面的 是对应的下载地轴

后面的/tmp是让下载文件存储在这个文件夹下面

常见的command

yum yellow dog update modified 是一个在fedora和redhat以及suse的shell前端软看包管理器，基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次性安装所有依赖软件包，无须繁琐对的一次次下载

```
grammar of yum：
yum 【option】 【command】
* option 可选，包括-h（help） -y（yes） -q（不显示安装过程）
* command 要进行的操作
* package 安装的包名

常见的命令：
check-update
update
install <package-name>
update <package -name>
yum list
yum remove <package -name>
yum search <keyword>
yum clean packages
yum clean headers
yum clean oldheaders
```

apt (advanced packageing tool 是一个在debian和ubuntu终端额shell前端软件挂你了工具

利用bash Anaconda3-2021.11-Linux-x86_64.s h 来安装对应的文件

安装的位置会默认在root/anaconda的目录下面

从yum安装dnf

### 4.6 Docker使用

```
yum list installed | grep docker

卸载安装
yum -y remove docker-ce-cli.x86_64
yum -y remove docker-ce.x86_64
yum -y remove containerd.io

安装依赖
yum install -y yum-utils device-mapper-persistent-data lvm2
安装yum圆头

yum-config-manager --add-repo <https://mirrors.cloud.tencent.com/docker-ce/linux/centos/docker-ce.repo>

安装Docker

yum install -y docker-ce docker-ce-cli containerd.io

docker version
```

启动

```
systemctl start docker

设置为开机启动
systemctl enable docker

查看运行状态
```

配置

```
mkdir -p /etc/docker

//配置加速
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["<https://mirror.ccs.tencentyun.com>"]
}
EOF

// 重启守护进程并重启docker
systemctl daemon-reload && systemctl restart docker
```

运行第一个容器

```
docker run --name=hello hello-world

查看
docker ps -a
```

或者一步一步运行一个容器

```
docker pull johngong/calibre-web

查看现有
docker images

根据创建镜像 
docker create --name=calibre-web -p 80:8083 -v /data/calibre-web/library:/library -e WEBLANGUAGE=zh_CN johngong/calibre-web

docker create 是创建容器的命令
--name=calibre-web 表示创建的容器的名称
-p 80:8083 表示该容器将 80 端口映射到 8083 端口
-v /data/calibre-web/librery:/libray 表示该容器将 /data/calibre-web/library 目录映射为 /library 目录
-e WEBLANGUAGE=zh_CN 表示该容器定义了一个变量，变量名是 WEBLANGUAGE，变量值是 zh_CN
johngong/calibre-web 是容器的镜像，这里也就是我们前面拉取的镜像

启动刚才创建好的镜像

docker start calibre-web
```

停止镜像

```
docker stop calibre-web

或者

docker kill calibre-web

删除容器
docker rm calibre-web

docker rmi hello-world

docker rmi `docker images -q`删除所有
```

Docker部署python 网站

- 安装Docker虚拟机环境
- 学习docke常用命令
- 安装Python SDK和MySQL镜像
- 为Flask添加SQL Alchemy拓展库

为什么要Docker?虚拟化环境的重要性

1. 直接程序安装或者部署在Linux操作系统上面，容易引发资源冲突
2. 程序卸载不干净，导致无法安装或者部署新程序
3. 每次部署之前要安装很多软件，修改复杂的配置文件
4. 无法让服务器硬件资源实现多租户服务
5. 增大了在Linux系统上部署集群和分布式的难度

在Linux上安装docker，了解docker hub的

设置dockerhub的加速器DaoCloud

了解Docker镜像与容器的关系，了解镜像和容器之间的关系，学习常用的docker命令

什么是docker镜像？

为了快速打包和部署软件环境，docker引入image机制

镜像是一个配置好的只读层软件环境

可以通过dockerfile文件创建镜像，也可以从docker仓库中下载到镜像中

什么是docker容器

容器是在镜像基础上创建出的虚拟实例，内容可读可写

一个docker image 可以创建多个container，而且container之间相互隔离，部署的程序之间不会相互干扰，所有的容器直接使用宿主的主机的Linux内核

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-11aa44143b5c4a55178f7e67eb55b57e_1440w.jpg)





添加图片注释，不超过 140 字（可选）

docker run -it —name=p1 python:3.8 bash

docker 会为每个容器创建网址

学习docker网络，docker的容器端口映射，目录挂载技术，FTP程序来管理文件，可以将宿主机挂载在目录挂载

docker network create —subnet=172.18.0.0/16 mynet

网段的ip

```
docker network create --subnet=172.18.0.0/16 mynet
docker network rm mynet
docker run -it --name=p2 --net mynet --ip 172.18.0.2 python:3.8 bash
```

默认情况下，除来宿主机之外，任何主机无法访问远程访问的docker容器，通过端口映射可以把容器端口映射到宿主机的端口，这样其他的主机就能访问容器来，映射到宿主机的端口，不需要设置防火墙规则

```
docker run -it -p9500:5000 -p9600:3306 python3.8:bash
```

为能把一部分业务数据保存在docker环境中，可以使用ftp，但是比较麻烦，因此可以使用目录映射。因为ftp会导致文件删除，只支持文件目录挂载，不支持文件挂载，而且一个容器可以挂载多个目录
