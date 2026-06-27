---
title: 动手学深度学习 Vol1 ｜ 线性回归实现
tags:
  - 机器学习
  - 深度学习
  - 李沐
categories:
  - 算法基础
abbrlink: 815c2675
date: 2024-04-29 16:56:11
---

在CS229中主要关注的是一些传统机器学习的模型，包括广义线性模型（GLM）包含的Logistic Regression、Softmax Regression，生成模型的高斯判别式、Decision Tree、Support Vector Machine、浅层神经网络；侧重于对于理论的介绍。李沐课程中对于实际操作的代码实现过程给出了详细的介绍，是非常有意思的。其实整个学习过程的框架无论是简单的逻辑回归还是复杂的YOLO之类的代码框架其实都已经给好了，如何高效的利用框架所提供的抽象好的API来实现复杂的功能是值得思考的。

### 0x00 Environment build（with conda）

[环境安装，性能测试与横向对比](https://www.bilibili.com/video/BV1LT411F77M?spm_id_from=333.337.search-card.all.click&vd_source=a43f0eddfeb89c08a00be8485e811ab0)中给出Pytorch安装的三种方式：1. 直接安装、2. conda环境、3. Docker安装。后面有时间可以借鉴一下思路来使用Docker管理环境而不是Conda。本次还是利用conda来管理环境

>  ps：关于conda与docker管理环境之间的差异 -- 个人看法 如果有更好的回答希望可以发送到 chenxia31@outlook.com 交流！ 作为一个计算机小白，在最初接触Python的时候知道一个电脑中会存在不同版本的python，这个需要我们来进行不同环境的隔离，常见的有python的envs、或者conda管理工具。这样不同的python环境可以使用不同的package。 但是docker可以说是一个更高level的工具，实际上他是一个弱化的虚拟机，并不是从硬件层面来虚拟一整套冯诺依曼体系，而是利用部分硬件和软件调度的方式来降低虚拟机所需要的硬件资源而实现虚拟机的操作系统和主机操作系统之间隔离的目的。在这个过程中可以实现某种意义上的硬件方式的环境隔离。 两者可以说是风马牛，但是在管理python环境中却又有着异曲同工之妙，都能起到环境隔离的作用，但是实现的过程是不一样的： 

1. conda是python的package的管理者，它为不同的python提供不同的package的路径。这也就是conda install的package值需要装一份就可以为所有环境使用（如果版本一致的话）
2. docker是操作系统层面的硬件管理者，它为不同的虚拟机提供硬件和软件资源

```
# 新建conda环境
conda create -n 'datastudy' python=3.9

# 设置一下pip镜像
# 清华镜像源https://mirrors.tuna.tsinghua.edu.cn/help/pypi/
python -m pip install --upgrade pip
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 安装pytorch，https://pytorch.org/get-started/locally/
# 默认配置好CUDA
# ps：中文教程很多安装pytorch好复杂，其实很多东西官网都给好了
conda install pytorch torchvision torchaudio cudatoolkit=11.6 -c pytorch -c conda-forge

# 检查是否成功，
import torch
print(torch.__version__)
print(torch.cuda.get_device_name(0))

# 检查代码是否能跑，参考链接如下
# Summary: 使用PyTorch玩转MNIST 
# Author:  Amusi
# Date:    2018-12-20 
# github:  https://github.com/amusi/PyTorch-From-Zero-To-One
# Reference: https://blog.csdn.net/victoriaw/article/details/72354307

from __future__ import print_function
import argparse
import torch
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim
from torchvision import datasets, transforms
from torch.autograd import Variable

# Training settings
parser = argparse.ArgumentParser(description='PyTorch MNIST Example')
parser.add_argument('--batch-size', type=int, default=64, metavar='N',
                    help='input batch size for training (default: 64)')
parser.add_argument('--test-batch-size', type=int, default=1000, metavar='N',
                    help='input batch size for testing (default: 1000)')
parser.add_argument('--epochs', type=int, default=10, metavar='N',
                    help='number of epochs to train (default: 10)')
parser.add_argument('--lr', type=float, default=0.01, metavar='LR',
                    help='learning rate (default: 0.01)')
parser.add_argument('--momentum', type=float, default=0.5, metavar='M',
                    help='SGD momentum (default: 0.5)')
parser.add_argument('--no-cuda', action='store_true', default=False,
                    help='disables CUDA training')
parser.add_argument('--seed', type=int, default=1, metavar='S',
                    help='random seed (default: 1)')
parser.add_argument('--log-interval', type=int, default=10, metavar='N',
                    help='how many batches to wait before logging training status')
args = parser.parse_args()
args.cuda = not args.no_cuda and torch.cuda.is_available()

torch.manual_seed(args.seed) #为CPU设置种子用于生成随机数，以使得结果是确定的
if args.cuda:
    torch.cuda.manual_seed(args.seed)#为当前GPU设置随机种子；如果使用多个GPU，应该使用torch.cuda.manual_seed_all()为所有的GPU设置种子。


kwargs = {'num_workers': 1, 'pin_memory': True} if args.cuda else {}
"""加载数据。组合数据集和采样器，提供数据上的单或多进程迭代器
参数：
dataset：Dataset类型，从其中加载数据
batch_size：int，可选。每个batch加载多少样本
shuffle：bool，可选。为True时表示每个epoch都对数据进行洗牌
sampler：Sampler，可选。从数据集中采样样本的方法。
num_workers：int，可选。加载数据时使用多少子进程。默认值为0，表示在主进程中加载数据。
collate_fn：callable，可选。
pin_memory：bool，可选
drop_last：bool，可选。True表示如果最后剩下不完全的batch,丢弃。False表示不丢弃。
"""
train_loader = torch.utils.data.DataLoader(
    datasets.MNIST('../data', train=True, download=True,
                   transform=transforms.Compose([
                       transforms.ToTensor(),
                       transforms.Normalize((0.1307,), (0.3081,))
                   ])),
    batch_size=args.batch_size, shuffle=True, **kwargs)
test_loader = torch.utils.data.DataLoader(
    datasets.MNIST('../data', train=False, transform=transforms.Compose([
                       transforms.ToTensor(),
                       transforms.Normalize((0.1307,), (0.3081,))
                   ])),
    batch_size=args.batch_size, shuffle=True, **kwargs)


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(1, 10, kernel_size=5)#输入和输出通道数分别为1和10
        self.conv2 = nn.Conv2d(10, 20, kernel_size=5)#输入和输出通道数分别为10和20
        self.conv2_drop = nn.Dropout2d()#随机选择输入的信道，将其设为0
        self.fc1 = nn.Linear(320, 50)#输入的向量大小和输出的大小分别为320和50
        self.fc2 = nn.Linear(50, 10)

    def forward(self, x):
        x = F.relu(F.max_pool2d(self.conv1(x), 2))#conv->max_pool->relu
        x = F.relu(F.max_pool2d(self.conv2_drop(self.conv2(x)), 2))#conv->dropout->max_pool->relu
        x = x.view(-1, 320)
        x = F.relu(self.fc1(x))#fc->relu
        x = F.dropout(x, training=self.training)#dropout
        x = self.fc2(x)
        return F.log_softmax(x)

model = Net()
if args.cuda:
    model.cuda()#将所有的模型参数移动到GPU上

optimizer = optim.SGD(model.parameters(), lr=args.lr, momentum=args.momentum)

def train(epoch):
    model.train()#把module设成training模式，对Dropout和BatchNorm有影响
    for batch_idx, (data, target) in enumerate(train_loader):
        if args.cuda:
            data, target = data.cuda(), target.cuda()
        data, target = Variable(data), Variable(target)#Variable类对Tensor对象进行封装，会保存该张量对应的梯度，以及对生成该张量的函数grad_fn的一个引用。如果该张量是用户创建的，grad_fn是None，称这样的Variable为叶子Variable。
        optimizer.zero_grad()
        output = model(data)
        loss = F.nll_loss(output, target)#负log似然损失
        loss.backward()
        optimizer.step()
        if batch_idx % args.log_interval == 0:
            print('Train Epoch: {} [{}/{} ({:.0f}%)]\tLoss: {:.6f}'.format(
                epoch, batch_idx * len(data), len(train_loader.dataset),
                100. * batch_idx / len(train_loader), loss.item()))

def test(epoch):
    model.eval()#把module设置为评估模式，只对Dropout和BatchNorm模块有影响
    test_loss = 0
    correct = 0
    for data, target in test_loader:
        if args.cuda:
            data, target = data.cuda(), target.cuda()
        data, target = Variable(data, volatile=True), Variable(target)
        output = model(data)
        test_loss += F.nll_loss(output, target).item()#Variable.data
        pred = output.data.max(1)[1] # get the index of the max log-probability
        correct += pred.eq(target.data).cpu().sum()

    test_loss = test_loss
    test_loss /= len(test_loader) # loss function already averages over batch size
    print('\nTest set: Average loss: {:.4f}, Accuracy: {}/{} ({:.0f}%)\n'.format(
        test_loss, correct, len(test_loader.dataset),
        100. * correct / len(test_loader.dataset)))


if __name__ == '__main__':
    for epoch in range(1, args.epochs + 1):
        train(epoch)
        test(epoch)
```

别人总结的 [Pytorch handbook](https://handbook.pytorch.wiki/index.html)，看上去还不错

### 0x01 Review of linear regression

线性回归是非常经典的模型，其求解方法并不是只有梯度下降一种，[CS229-01--线性模型](https://blog.tjdata.site/2022/01/29/CS229-01--线性模型/)之前的blog中给出了相关的介绍，这里再重新复述一下和关于自己在其中新的思考

### 1.1 记号和引言

假设每个**sample**包含**feature（n\*1）**和**label（1\*1）**，也就是$[(x^{(i)},y^{(i)}),i=0:...:m]$,在这种情况下我们希望构建一个**model**（或者说一种**function**）来作为feature和label之间的**映射**关系，这样在一个新的feature到来的时候，可以做出了预测。这个**model**其实就是实例化的**hypothesis**，实例化的过程便是**optimization**确定模型**parameters**的过程，其中包括**loss function**与**optim method**

### 1.2 模型实现的过程

对于线性回归，所做出的**hypothesis**自然是线性的，这也是它属于广义线性模型的原因

 

  

对于优化过程，第一步是确定损失函数，第二步是利用损失函数来找到对应的$w,b$参数值使得最小

损失函数是用来描述预测值和实际值之间的差别，通常模型越好损失函数需要最小，这里采用的是欧氏距离作为损失函数，具体原因在1.3中解释

 

之后便是求解这个优化问题的方式，对于线性回归有两种，一种是利用矩阵的normal equation方法得到**解析解**；二是利用深度学习中常用的**梯度下降（gradient descent）**的**启发式**方法来得到近似解（ps：和做时刻表一样）

- normal equation，利用导数为0的极值点得到取值，推导过程略

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-b600fae2bf4b07fecf9d4aa62de5f337_1440w.jpg)





image-20220911092806762

- gradient descent，利用导数下降的方式来探索的接近极值点

 

 

线性模型的梯度比较好求解

### 1.3 模型解释

机器学习最方便的就是可解释性。首先我们的假设中认为features和labels之间是关于线性分布，但是通常情况下总会有各种各样的因素也就是高斯分布的假设，所以如果有**Ground- truth**的话，那必然是，主义嗷对于不同

 

 

我们可以使用**最大似然法（maximum likelihood）** 的方式来推导我们想要什么？抛弃传统距离的概率，我们用概率的方式来表示.（我们的目的都是预测值和真实值相接近，可以用距离、也可以用分布概率）

由上我们可以看出预测值的概率值（不是概率分布！！）

 

我们希望所有的数据集中的概率都尽可能的大，所以

 

这个时候就可以求导得到等价于1.2中损失函数的式子!

### 1.4 回顾过程

1.1 给出为什么要这么做、1.2 给出理论推导过程、1.3给出模型的解释。这里一个困惑的点在于loss function是先有还是先可以被解释的。我的个人理解是它先有欧氏距离的方式，后面随着发展才逐渐利用概率方式解释，同时这种解释的范式我们也可以推广给其他模型。

比如在逻辑回归中，并不是给出假设就给出损失函数，而是利用概率方式来**推导（！！）**得到损失函数，这里常用的方式是MLP和MAE或者其他的方式，在选择合适的优化算法来寻找参数

不同于机器学习目的是得到一个有效的模型，传统的统计分析会给出参数估计的优劣以及检验是否合理，并就得到的参数给出自变量和因变量之间的关系。

### 0x02  Manual implementation process（eight step）

接下来就是利用代码实现上述功能，这里参考[李沐老师的d2l](https://zh-v2.d2l.ai/chapter_linear-networks/index.html)，在最开始软件模块的设计中就已经给出来代码的框架。

### 2.1 生成数据集/读取数据集

```
# 第一步，生成包含随机噪音的数据
import random
import torch
def synthetic_data(w,b,num_examples):
    '''生成包含随机噪音的线性回归数据'''
    X=torch.normal(0,1,(num_examples,len(w)))
    y=torch.matmul(X,w)+b
    y+=torch.normal(0,0.01,y.shape)
    return X,y.reshape(-1,1)

true_w=torch.tensor([2,-3.4])
true_b=4.2
features,labels=synthetic_data(true_w,true_b,1000)
```

### 2.2 数据观察与预处理（暂无）

```
# 第二步，简单的观察feature与labels之间的关系
# 本文中有feature为（1000，2），因此可以看第二个特征和labels之间的关系
import matplotlib.pyplot as plt
plt.scatter(features[:,1],labels)
```

### 2.3 数据迭代器（Dataloader）

这里采用的小批量（batch）的梯度下降；在梯度下降的过程中最直接的是计算所有数据集的梯度进行更新，这样对于memory的压力比较大，因此可以一个epoch进行划分，划分不同的batch_size，再利用batch进行参数更新。

但是这样的疑问在于batch能代表整个训练集进行更新吗？所以这样子就需要训练很多次，在某种特殊情况下会震荡多次，所有也会有更多的优化算法，来添加噪音或者动量，提升优化算法的健壮性。

```
# 第三部，对数据集进行迭代处理，这样可以保证在随机梯度下降过程中会慢慢转换进去
def data_iter(batch_size,features,labels):
    num_examples=len(features)
    indices=list(range(num_examples))
    random.shuffle(indices)
    for i in range(0,num_examples,batch_size):
        batch_indices=torch.tensor(indices[i:min(i+batch_size,num_examples)])
        yield  features[batch_indices],labels[batch_indices]
# yield的作用这里的迭代的结果是逐步发出来的
batch_size=10
for X,y in data_iter(batch_size,features,labels):
    print(X,'\n',y)
    break
```

### 2.4 初始化参数

```
# 第四步，初始化参数
w=torch.normal(0,0.01,size=(2,1),requires_grad=True)
b=torch.zeros(1,requires_grad=True)
```

### 2.5 定义模型（net）

其实这里net的输入参数应该是X和param，因为在后续的模型中肯定会有参数和训练样本的输入，但是计算方式和linreg并不是相似，如果输入参数规定好为w和b，在后续更改会比较麻烦。

```
# 第五步，定义模型
def linreg(X,w,b):
    '''linear regression'''
    # X M*n
    # w n*1
    return torch.matmul(X,w)+b
```

### 2.6 定义损失函数（loss）

损失函数表征的是预测值和真实值之间的差距，因此函数的输入应该为预测值与真值，这样就可以被复用

```
# 第六步，定义损失函数
def squared_loss(y_hat,y):
    '''均方误差'''
    return (y_hat-y.reshape(y_hat.shape))**2/2
```

### 2.7 定义优化算法（optim）

这里还是使用给好的API实现，同时在优化过程中我们需要设置的除了批量梯度下降的batch-size之外，还需要确定学习率。这个在任何情况下都是需要设置的

```
# 第七步，定义优化算法
def sgd(params,lr,batch_size):
    with torch.no_grad():
        for param in params:
            param-=lr*param.grad/batch_size
            param.grad.zero_()
```

### 2.8 完整的训练过程

```
# 第八步，完整的训练过程
lr=0.03
num_epoch=3
net=linreg
loss=squared_loss

for epoch in range(num_epoch):
    for X,y in data_iter(batch_size,features,labels):
        l=loss(net(X,w,b),y)
        l.sum().backward()
        sgd([w,b],lr,batch_size)
    with torch.no_grad():
        train_l=loss(net(features,w,b),labels)
        print('epoch{},loss{}'.format(str(epoch+1),str(float(train_l.mean()))))
```

### 0x03 Automatic implementation process （Pytorch）

回顾上述的过程，为了2.8最后的训练过程，我们首先需要生成数据集和读取数据集；之后我们写来一个dataloader来将数据集转换为可迭代（iterable）的对象来方便后续的梯度下降，dataloader中我们需要设置数据集、batch-size、是否可以被打乱；后面定义模型、损失函数和优化算法，模型是需要根据param和x来得到预测值、损失函数需要根据预测值和真实值得到结果、优化算法需要根据学习率、batch size来进行参数更新

```
import numpy as np
import torch
from torch.utils import data
# 第一步生成数据集
true_w=torch.tensor([2,-3.4])
true_b=4.2
features,labels=synthetic_data(true_w,true_b,1000)

# 第二步读取数据集
def load_array(data_arrays,batch_size,is_train=True):
    dataset=data.TensorDataset(*data_arrays)
    return data.DataLoader(dataset,batch_size,shuffle=is_train)

batch_size=10
data_iter=load_array((features,labels),batch_size)
# 这里data_iter的方式与之前的方式相似
# 第三步，可视化掠过
# 第四步，定义模型
from torch import nn
net=nn.Sequential(nn.Linear(2,1))
net[0].weight.data.normal_(0,0.01)
net[0].bias.data.fill_(0)

loss=nn.MSELoss()
trainer=torch.optim.SGD(net.parameters(),lr=0.03)

num_epoch=3
for epoch in range(num_epoch):
    for X,y in data_iter:
        l=loss(net(X),y)
        trainer.zero_grad()
        l.backward()
        trainer.step()
    l=loss(net(features),labels)
    print('epoch{} loss is {}'.format(str(epoch+1),str(float(l))))
```

### 0x04 Discussion

在了解整个代码实现过程之后，才能更好的了解到后续的逻辑回归、图像深度学习等代码实现的方式，以及如何学习pytorch的结构
