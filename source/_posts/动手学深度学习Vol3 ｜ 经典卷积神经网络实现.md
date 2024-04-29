---
title: 动手学深度学习Vol3 ｜ 经典卷积神经网络实现
tags:
  - CV
  - 深度学习
  - 李沐
categories:
  - 算法基础
date: 2024-04-29 17:14:02
---

在初等卷积神经网络中，我们可以看出从平移不变和缩放不变性质而得到的卷积性质的基础上，在上个世纪诞生的LeNet成功实现与之前不同的路径，在GPU诞生之后，新的网络AlexNet、VGG、NiN、GoogLeNet、Residual Net、DenseNet逐渐发展。卷积神经网络从变深变多，逐渐到变成块以及一些trick的增加。

### 0x01 introduction

### 1.1 PyTorch

之前在讲座中听到深度学习框架是深度学习时代的操作系统，怎么说这个比喻有点粗旷但是又不失正确性。个人感觉在计算机时代人们协作的能力达到空前的地步，我们可以上千人共同开发app，这种和建造房屋似的过程充分体现了软件工程的魅力。与土建中规划、设计、建造、 验收等流程相似，个人验证计算机成就的结果是建立在一层一层的抽象的基础上的。

从硬件的角度，现在并不是所有人都需要关心芯片上的三极管，也不需要关心加法器的构成。从之前手工焊机，到芯片的模块化设计，到更高级的抽象工艺和建造工艺的诞生，人们设计更高水平的芯片产生了可能。正是有这些抽象我们才能往前走。

从深度学习框架的角度也是如此，从零实现某个网络在学习过程是有意思的，但是从汇编语言包装成高级编程语言，再包装成一个一个package而形成框架，在前人抽象好的基础上，在深度学习框架搭建的基础上，在使用者的角度更应该重视如何高效的使用。

基于torch构建的PyTorch便是抽象好深度学习的框架。

这里并没有参照 [PyTorch官网](https://pytorch.org/)

而是找到一个开源的 [PyTorch中文手册](https://handbook.pytorch.wiki/index.html) 来了解这个深度学习框架

```
Torch is not going anywhere. PyTorch and Torch use the same C libraries that contain all the performance: TH, THC, THNN, THCUNN and they will continue to be shared.
We still and will have continued engineering on Torch itself, and we have no immediate plan to remove that.
PyTorch is an open source machine learning library for Python, based on Torch, used for applications such as natural language processing. It is primarily developed by Facebook's artificial-intelligence research group, and Uber's "Pyro" software for probabilistic programming is built on it.
PyTorch is a Python package that provides two high-level features:

    Tensor computation (like NumPy) with strong GPU acceleration

    Deep neural networks built on a tape-based autograd system
You can reuse your favorite Python packages such as NumPy, SciPy and Cython to extend PyTorch when needed.
```

### 1.2 GPU

这里参照wiki所给的解释：[图形处理器GPU解释](https://zh.wikipedia.org/zh-sg/圖形處理器)

**图形处理器**（英语：**G**raphics **P**rocessing **U**nit，缩写：**GPU**；又称**显示核心**、**显卡**、**视觉处理器**、**显示芯片**或**绘图芯片**）是一种专门在[个人电脑](https://zh.wikipedia.org/wiki/個人電腦)、[工作站](https://zh.wikipedia.org/wiki/工作站)、[游戏机](https://zh.wikipedia.org/wiki/遊戲機)和一些[移动设备](https://zh.wikipedia.org/wiki/行動裝置)（如[平板电脑](https://zh.wikipedia.org/wiki/平板電腦)、[智能手机](https://zh.wikipedia.org/wiki/智慧型手機)等）上执行绘图运算工作的[微处理器](https://zh.wikipedia.org/wiki/微處理器)。

GPU不同于传统的CPU，如[Intel](https://zh.wikipedia.org/wiki/Intel) [i5](https://zh.wikipedia.org/wiki/Intel_Core_i5)或[i7](https://zh.wikipedia.org/wiki/Intel_Core_i7)处理器，其[内核](https://zh.wikipedia.org/wiki/内核)数量较少，专为通用计算而设计。相反，GPU是一种特殊类型的处理器，具有数百或数千个内核，经过优化，可并行运行大量计算。虽然GPU在游戏中以[3D渲染](https://zh.wikipedia.org/wiki/3D渲染)而闻名，但它们对运行分析、[深度学习](https://zh.wikipedia.org/wiki/深度学习)和[机器学习](https://zh.wikipedia.org/wiki/机器学习)算法尤其有用。GPU允许某些计算比传统CPU上运行相同的计算速度快10倍至100倍。

### 1.3 数据集输入和结果计算

```
# 读取fashion数据集
def load_fashion_mnist(batchsize,resize=None):
    # resize相当于基本的填充模块
    import torch
    from torch.utils import data
    import torchvision
    from torchvision import transforms

    if resize:
        trans=[transforms.ToTensor()]
        trans.insert(0, transforms.Resize(resize))
        trans = transforms.Compose(trans)
    else:
        trans=transforms.ToTensor()
    train_mnist=torchvision.datasets.FashionMNIST(root='../data/FashionMNIST',train=True,transform=trans,download=True)
    test_mnist=torchvision.datasets.FashionMNIST(root='../data/FashionMNIST',train=False,transform=trans,download=True)
    train_iter=data.DataLoader(train_mnist,batch_size=batchsize,shuffle=True,num_workers=4)
    test_iter=data.DataLoader(test_mnist,batch_size=batchsize,shuffle=True,num_workers=4)
    return train_iter,test_iter

batchsize=256
train_iter,test_iter=load_fashion_mnist(batchsize,224)
import torch
def accuracy(y_hat,y):
    # 数正确的数量
    if len(y_hat.shape)>1 and y_hat.shape[1]>1:
        y_hat=y_hat.argmax(axis=1)
    cmp=y_hat.type(y.dtype)==y
    return float(cmp.type(y.dtype).sum())

def gpu_acc(model,data_iter,device=None):
    acc=0
    num=0
    if isinstance(net,nn.Module):
        net.eval()
        if not device:
            device=next(iter(net.parameters())).device
    with torch.no_grad():
        for X,y in data_iter:
            if isinstance(X,list):
                # 这里的if仅仅是为之后bert微调所需的
                X=[x.to(device) for x in X]
            else:
                X=X.to(device)
            y=y.to(device)
            acc+=accuracy(net(X),y)
            num+=y.numel()
    return acc/num



def trainer(net,train_iter,test_iter,num_epoches,lr,device):
    '''用GPU训练模型'''
    #初始化参数
    def init_weights(m):
        if type(m)==nn.Linear or type(m)==nn.Conv2d:
            nn.init.xavier_uniform_(m.weight)
            #nn.init.normal_(m.weight,std=0.01)
    net.apply(init_weights)

    print('training on',device)
    # print(torch.cuda.get_device_name(0))
    net.to(device)
    optimizer=torch.optim.SGD(net.parameters(),lr=lr)
    loss=nn.CrossEntropyLoss()

    train_acc=[]
    test_acc=[]
    loss_epoches=[]
    for epoch in range(num_epoches):
        temp_train_acc=0
        loss_epoch=0
        num=0
        net.train()
        for i,(X,y) in enumerate(train_iter):
            optimizer.zero_grad()
            X,y=X.to(device),y.to(device)
            with torch.no_grad():
                temp_train_acc+=accuracy(net(X),y)
            y_hat=net(X)
            l=loss(y_hat,y)
            l.backward()
            optimizer.step()
            loss_epoch+=l*X.shape[0]
            num+=y.numel()
        train_acc.append(float(temp_train_acc/num))
        test_acc.append(gpu_acc(net,test_iter))
        loss_epoches.append(float(loss_epoch)/num)
        print('本轮{}训练的结果，test—acc:{},train-acc:{},train-loss:{}'.format(str(epoch+1),str(test_acc[-1]),str(train_acc[-1]),str(loss_epoches[-1])))
    import matplotlib.pyplot as plt
    plt.plot(list(range(1,1+num_epoches)),test_acc,label='test_acc')
    plt.plot(list(range(1,1+num_epoches)),train_acc,label='train_acc')
    plt.plot(list(range(1,1+num_epoches)),loss_epoches,label='loss')
    plt.legend()
    plt.show()


lr,num_epoches=0.2,20


device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
trainer(net,train_iter,test_iter,num_epoches,lr,device)
```

### 0x02 经典的CNN

### 2.1 AlexNet

之前的阅读笔记[paper02-2012-imagenet-classification-with-deep-convolutional-neural-networks](https://blog.tjdata.site/2022/08/27/paper02-2012-imagenet-classification-with-deep-convolutional-neural-networks/)

主要的贡献，以及后面没有用的地方

1. 引入了ReLu激活函数，让训练的更快
2. 利用双GPU训练，注意这里对网络结构进行划分（对网络不同层进行切割而不是切割成两种小的部分），工程能力太强了
3. （没有用）一种local response normalization，但是确实需要在这个方面做trick，在后面的resNet中可以看到
4. （没有用）新的overlapping pooling，一种新的池化方法，这个与双GPU有关
5. （有用，但是解释不对）将dropout引入训练过程，尝试解释为ensemble learning，但现在更偏向于regularization
6. 数据增强，才有随机抽样数据的方式，将256提取得到224来扩充数据集

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-82c974a4743e8a72fe6736f3e8cc3903_1440w.jpg)





image-20220923164858838

```
import torch 
from torch import nn
#AlexNet
net = nn.Sequential(
    # 这里，我们使用一个11*11的更大窗口来捕捉对象。
    # 同时，步幅为4，以减少输出的高度和宽度。
    # 另外，输出通道的数目远大于LeNet
    nn.Conv2d(1, 96, kernel_size=11, stride=4, padding=1), nn.ReLU(),
    nn.MaxPool2d(kernel_size=3, stride=2),
    # 减小卷积窗口，使用填充为2来使得输入与输出的高和宽一致，且增大输出通道数
    nn.Conv2d(96, 256, kernel_size=5, padding=2), nn.ReLU(),
    nn.MaxPool2d(kernel_size=3, stride=2),
    # 使用三个连续的卷积层和较小的卷积窗口。
    # 除了最后的卷积层，输出通道的数量进一步增加。
    # 在前两个卷积层之后，汇聚层不用于减少输入的高度和宽度
    nn.Conv2d(256, 384, kernel_size=3, padding=1), nn.ReLU(),
    nn.Conv2d(384, 384, kernel_size=3, padding=1), nn.ReLU(),
    nn.Conv2d(384, 256, kernel_size=3, padding=1), nn.ReLU(),
    nn.MaxPool2d(kernel_size=3, stride=2),
    nn.Flatten(),
    # 这里，全连接层的输出数量是LeNet中的好几倍。使用dropout层来减轻过拟合
    nn.Linear(6400, 4096), nn.ReLU(),
    nn.Dropout(p=0.5),
    nn.Linear(4096, 4096), nn.ReLU(),
    nn.Dropout(p=0.5),
    # 最后是输出层。由于这里使用Fashion-MNIST，所以用类别数为10，而非论文中的1000
    nn.Linear(4096, 10))
```

### 2.2 VGG

[VERY DEEP CONVOLUTIONAL NETWORKS FOR LARGE-SCALE IMAGE RECOGNITION](https://arxiv.org/pdf/1409.1556.pdf)

AlexNet证明 #卷积神经网络 CNN convolutional- neural-network 的深度是有效的，但并没有给出模版来进行创建网络。这里一个直觉的想法

与芯片设计中工程师从放置晶体管到逻辑元件再到逻辑块的过程类似，神经网络架构设计也逐渐变成更加抽象，研究人员开始从单个神经元的角度思考问题，发展到整个层，转向可以复用层的块的模式 #memo

使用 #循环loop 和 子函数 来实现 #VGG

主要做出的贡献

1. 利用复用的思路构建卷积块
2. 作者尝试各种架构，最终发现深且窄的网络效果是较好的

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-b0e04e336aa7a60b78eced9b95966d58_1440w.jpg)





image-20220923165253079

```
import torch 
from torch import nn
def vgg_block(num_convs, in_channels, out_channels):
    layers = []
    for _ in range(num_convs):
        layers.append(nn.Conv2d(in_channels, out_channels,
                                kernel_size=3, padding=1))
        layers.append(nn.ReLU())
        in_channels = out_channels
    layers.append(nn.MaxPool2d(kernel_size=2,stride=2))
    return nn.Sequential(*layers)

def vgg(conv_arch):
    conv_blks = []
    in_channels = 1
    # 卷积层部分
    for (num_convs, out_channels) in conv_arch:
        conv_blks.append(vgg_block(num_convs, in_channels, out_channels))
        in_channels = out_channels

    return nn.Sequential(
        *conv_blks, nn.Flatten(),
        # 全连接层部分
        nn.Linear(out_channels * 7 * 7, 4096), nn.ReLU(), nn.Dropout(0.5),
        nn.Linear(4096, 4096), nn.ReLU(), nn.Dropout(0.5),
        nn.Linear(4096, 10))

conv_arch = ((1, 64), (1, 128), (2, 256), (2, 512), (2, 512))
ratio = 4
small_conv_arch = [(pair[0], pair[1] // ratio) for pair in conv_arch]
net = vgg(small_conv_arch)
```

### 2.3 NiN

[Network in network](https://arxiv.org/pdf/1312.4400.pdf)

AlexNet提出使用深度卷积神经网络，VGG给出如何复用的构建卷积神经网络，VGG则是在这个基础上利用MLP对多通道输入和多通道输出进行操作，取代之前简单的加权方式，NiN利用这来对网络的扩大和深度。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-b09e6855cc43e71d568df37059a8725a_1440w.jpg)





image-20220923165759261

### 2.4 GoogLeNet

[Going deeper with convolutions](https://arxiv.org/pdf/1409.4842.pdf)

GoogLeNet借用NiN的思想，在它的基础上设置来inception块，用来实现多个卷积核对图形特征的提取

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-8e5eccd09aeec660fae9c5c468070765_1440w.jpg)





image-20220923165859815

```
# 网络3.5 GoogLeNet

import torch
from torch import nn
from torch.nn import functional as F



class Inception(nn.Module):
    # c1--c4是每条路径的输出通道数
    def __init__(self, in_channels, c1, c2, c3, c4, **kwargs):
        super(Inception, self).__init__(**kwargs)
        # 线路1，单1x1卷积层
        self.p1_1 = nn.Conv2d(in_channels, c1, kernel_size=1)
        # 线路2，1x1卷积层后接3x3卷积层
        self.p2_1 = nn.Conv2d(in_channels, c2[0], kernel_size=1)
        self.p2_2 = nn.Conv2d(c2[0], c2[1], kernel_size=3, padding=1)
        # 线路3，1x1卷积层后接5x5卷积层
        self.p3_1 = nn.Conv2d(in_channels, c3[0], kernel_size=1)
        self.p3_2 = nn.Conv2d(c3[0], c3[1], kernel_size=5, padding=2)
        # 线路4，3x3最大汇聚层后接1x1卷积层
        self.p4_1 = nn.MaxPool2d(kernel_size=3, stride=1, padding=1)
        self.p4_2 = nn.Conv2d(in_channels, c4, kernel_size=1)

    def forward(self, x):
        p1 = F.relu(self.p1_1(x))
        p2 = F.relu(self.p2_2(F.relu(self.p2_1(x))))
        p3 = F.relu(self.p3_2(F.relu(self.p3_1(x))))
        p4 = F.relu(self.p4_2(self.p4_1(x)))
        # 在通道维度上连结输出
        return torch.cat((p1, p2, p3, p4), dim=1)

b1 = nn.Sequential(nn.Conv2d(1, 64, kernel_size=7, stride=2, padding=3),
                   nn.ReLU(),
                   nn.MaxPool2d(kernel_size=3, stride=2, padding=1))

b2 = nn.Sequential(nn.Conv2d(64, 64, kernel_size=1),
                   nn.ReLU(),
                   nn.Conv2d(64, 192, kernel_size=3, padding=1),
                   nn.ReLU(),
                   nn.MaxPool2d(kernel_size=3, stride=2, padding=1))
b3 = nn.Sequential(Inception(192, 64, (96, 128), (16, 32), 32),
                   Inception(256, 128, (128, 192), (32, 96), 64),
                   nn.MaxPool2d(kernel_size=3, stride=2, padding=1))
b4 = nn.Sequential(Inception(480, 192, (96, 208), (16, 48), 64),
                   Inception(512, 160, (112, 224), (24, 64), 64),
                   Inception(512, 128, (128, 256), (24, 64), 64),
                   Inception(512, 112, (144, 288), (32, 64), 64),
                   Inception(528, 256, (160, 320), (32, 128), 128),
                   nn.MaxPool2d(kernel_size=3, stride=2, padding=1))
b5 = nn.Sequential(Inception(832, 256, (160, 320), (32, 128), 128),
                   Inception(832, 384, (192, 384), (48, 128), 128),
                   nn.AdaptiveAvgPool2d((1,1)),
                   nn.Flatten())

net = nn.Sequential(b1, b2, b3, b4, b5, nn.Linear(1024, 10))
```

### 2.5 ResNet

[Deep Residual Learning for Image Recognition](https://arxiv.org/abs/1512.03385)

在之前介绍更深、更广的基础上，resnet更多的是解释如何提高神经网络的性能

部分的数学基础，从学习理论的角度出发，如果模型的复杂程度逐渐增加，这样寻找之前的函数集合的范围是比较大，这样寻找到之后的结果是非常困难的，因此可以尝试将恒等映射转换成为包含数量本身和残差之间的关系

借鉴GBDT中梯度提升的思想，残差问题在现实中往往更加容易优化，同时在模型为理想参数更容易捕捉恒等映射的细微波动，；在实际训练中，残差块中输入可以通过跨层数据线路更快的向前传播

```
import torch
from torch import nn
from torch.nn import functional as F
from d2l import torch as d2l


class Residual(nn.Module):  #@save
    def __init__(self, input_channels, num_channels,
                 use_1x1conv=False, strides=1):
        super().__init__()
        self.conv1 = nn.Conv2d(input_channels, num_channels,
                               kernel_size=3, padding=1, stride=strides)
        self.conv2 = nn.Conv2d(num_channels, num_channels,
                               kernel_size=3, padding=1)
        if use_1x1conv:
            self.conv3 = nn.Conv2d(input_channels, num_channels,
                                   kernel_size=1, stride=strides)
        else:
            self.conv3 = None
        self.bn1 = nn.BatchNorm2d(num_channels)
        self.bn2 = nn.BatchNorm2d(num_channels)

    def forward(self, X):
        Y = F.relu(self.bn1(self.conv1(X)))
        Y = self.bn2(self.conv2(Y))
        if self.conv3:
            X = self.conv3(X)
        Y += X
        return F.relu(Y)
b1 = nn.Sequential(nn.Conv2d(1, 64, kernel_size=7, stride=2, padding=3),
                   nn.BatchNorm2d(64), nn.ReLU(),
                   nn.MaxPool2d(kernel_size=3, stride=2, padding=1))

def resnet_block(input_channels, num_channels, num_residuals,
                 first_block=False):
    blk = []
    for i in range(num_residuals):
        if i == 0 and not first_block:
            blk.append(Residual(input_channels, num_channels,
                                use_1x1conv=True, strides=2))
        else:
            blk.append(Residual(num_channels, num_channels))
    return blk
b2 = nn.Sequential(*resnet_block(64, 64, 2, first_block=True))
b3 = nn.Sequential(*resnet_block(64, 128, 2))
b4 = nn.Sequential(*resnet_block(128, 256, 2))
b5 = nn.Sequential(*resnet_block(256, 512, 2))
net = nn.Sequential(b1, b2, b3, b4, b5,
                    nn.AdaptiveAvgPool2d((1,1)),
                    nn.Flatten(), nn.Linear(512, 10))
```

### 2.6 DenseNet

[Densely Connected Convolutional Networks](https://arxiv.org/pdf/1608.06993.pdf)

～还没看不重要捏

### 0x03 Result

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-0c2e26a48b683ad9442614123093bcaf_1440w.jpg)





AlexNet

### 0x04 Discussion

卷积神经网络提取特征的基本在于特征的平移不变和缩放不变，在这个基础上利用互相关操作（错误的被称为卷积）在提取特征。同时在这个过程中围绕了如何构建深度卷积神经网络，主流的blakcbone给出自己自己的发展历史：

1. lenet最早给出卷积神经网络的雏形
2. alexnet从训练硬件、训练网络、训练数据、数据预处理、训练过程多个方面给出了自己的trick，为后面打下基础
3. vgg围绕如何构建更深的网络，引入复用
4. nin提出一个trick来构建更广的网络
5. Googlenet在nin的基础上，受启发采用多个卷积核提取特征
6. 在前人构建好完整的网络的基础上，resnet给出如何保持网络稳定性的方法
7. densenet还没有了解

这个过程非常的有意思，可以看到别人是如何前进的。这些都归功李沐老师的总结！
