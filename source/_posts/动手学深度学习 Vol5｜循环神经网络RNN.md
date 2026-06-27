---
title: 动手学深度学习 Vol5｜循环神经网络RNN
tags:
  - RNN
  - 深度学习
categories:
  - 算法基础
abbrlink: fca5cf9
date: 2024-04-29 17:14:05
---

承接上一会对Attention中seq2seq的模型的困惑，这里从RNN到介绍到更传统的Seq2Seq模型。同时结合最近接触的花书来利用更有利的工具解释RNN中的梯度。并给出序列学习中对于gradient或者hidden state转变的过程，在针对hidden state中的转变中引申出GRU、LSTM之类的变种，并结合D@L给出代码的实现。

### 0x01 序列学习与RNN

### 1.1 序列学习Seq2Seq

输入与输出均为序列数据的模型叫做Seq2Seq，注意这里不在按照传统的监督或者非监督的方式来限定模型的类型。常见序列模型的应用包括：语音识别Speech recognition、音乐生成Music generation、情感分析 Sentiment classification、DNA序列分析DNA sequence analysis、机器翻译Machine translation、视频行为识别Video activity recognition、命名实体识别Name entity recognition。

在Seq2Seq过程中，我们很难像MLP或者CNN得到一个明确的 feature，label；并且序列数据大多是按照一定的顺序来进行的，比如文本序列、视频信号、网站浏览行为等等，并不满足之前的独立同分布的假设。

为什么RNN类似的网络在Seq中会取得好的效果？主要是解决20世纪80年代机器学习和统计模型的思想的优点：在模型的不同部分共享参数。**参数共享**使得模型能够拓展到不同形式的样本并进行泛化。specially：假如在seq的每一个时间点都具有一个单独的参数，但是我们不能泛化我们没有见过的类型，也不能在时间上共享不同序列长度和不同位置的统计强度。同时我们也不能理解不同顺序seq的含义（比如In 2009，I went to Nepal和I went to Nepal in 2009之间的区别）

Shortly：RNN的优势主要是

1. 跨时间权重共享
2. 时间平移不对称
3. 同一架构适应不同长度

### 1.2 RNN结构前向传播 Forward Propagation

这里先不管RNN内部的具体结构的原因，只要知道RNN包括时间步的input和output以及hidden state就可以，原因在后面会说明。计算图是形式化一组计算结构的方式，如果设计将输入和参数映射到输出和损失的计算。

先假设数据集为$X=(x^1,x^2,..,x^t,...)\mathbb{R}^{n \times d}$

通常的训练过程是将$H^t\in \mathbb{R^{1 \times h}}$作为hidden state来进行分析，它是根据下式子进行更新

𝐻𝑡=𝑓(𝐻𝑡−1,𝑥𝑡;𝜃) 

如果采用神经网络，格式通常是

 

其中各项的分别为$W_{xh}\in \mathbb{R}^{d\times h},W_{hh}\in\mathbb{R}^{h\times h},b_h\in\mathbb{R}^{1\times h}$

在得到hidden state我们可以进行下一步状态转换，也可以输出当前的值

 

 

其中各项的形状为$W_{oh}\in\mathbb{R}^{h\times o},b_o\in\mathbb{R}^{1\times o}$

得到模型的输入和输出之后就可以计算模型的损失

 

得到

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-c7b0f36d46c644fe0c999b2839ee82eb_1440w.jpg)





image-20221019135246524

### 1.3 反向传播的梯度下降 Back Propagation

在之前中我们包括至少五个参数，$[W_{xh},W_{hh},b_h,W_{ho},b_o]$,通过上处更新的式子我们计算不同的梯度,比如针对$W_{hh}$

 

注意这里的第一项我们是知道的，第二项我们也是知道的，但是第三项它是会一直Recurrent计算下去的

 

这样梯度其实是会随着时间t不断拉长，这里通常称为**长期依赖**的问题这样我们可能出现梯度消失、或者梯度爆炸的情况。常见的操作有三种

1. 完全计算直到收敛
2. 采用N步马尔可夫模型，来截断时间步，到达时间步$\tau$
3. 采用随机截断的思想

还有针对梯度的一些操作，常见的有**截断梯度**和**引导信息流的正则化**，

- 针对梯度爆炸，截断梯度也就是在参数更新之前截断梯度的范数，在时间的过程中被证明是有用的。
- 针对梯度消失，引导信息流的正则化，一种是加入门控单元（如LSTM、GRU），也可以解决梯度爆炸的问题；一种是正则化项，后者被证明不有效

### 1.4 进一步。高级一点的RNN

在单向RNN的基础上，可以自然而然的联想到

1. 双向RNN
2. 深度RNN
3. 递归神经网络，一个潜在的idea是研究推论
4. 增加门控单元的不同变种GRU、LSTM
5. 基于encoder- decoder的seq2seq框架
6. 回声状态网络（echo state network），流体状态机（liquid state machines）

### 1.5 回过头解释RNN

首先我们可以回顾我们在时间序列预测中常用的一些方法：自回归模型、隐变量自回归模型、马尔可夫模型、因果关系模型等等，常用的是

-  

因此我们也可以希望得到

 

也就是假如一个hidden state来作为对之前序列总结，将其送入状态转变中。

### 0x02 变种与思考GRU、LSTM

我们之前分析了长期依赖对于梯度消失或者梯度爆炸的影响，从实际效果来看它的影响是：

1. 早期的预测值对预测所有未来预测值具有非常重要的意义
2. 一些词元没有相关的观测值，比如对网页内容进行情感分析可能有一些辅助HTML代码与网页传达的情绪无关
3. 序列之间的不同部分之间存在逻辑中断

在过去采取过很多方法来进行改进，目前主流的是使用门控的方式来确定什么时候更新hidden state、什么时候重置reset。

[LSTM](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjmi4L93uv6AhVHed4KHVDfBIEQFnoECAwQAQ&url=http%3A%2F%2Fwww.bioinf.jku.at%2Fpublications%2Folder%2F2604.pdf&usg=AOvVaw3mXlGPo2DVtYp8Vue3sl4y)

### 2.1 Gate

设计为（0，1）区间内的向量，这样我们可以进行凸组合：reset-gate允许我们控制可能还想记住的过去状态的数量，更新们能讲允许我们控制新状态中有多少是旧状态的副本

具体的计算过程如图：

-   
-   

注意在求和过程会触发 广播机制,使用sigmoid函数将其转换到0，1区间，在这个基础上更新 候选隐藏状态

-  

使用tanh非线性激活函数来保证hidden state中的值是保持在区间-1，1之间的；Hadamar元素表示按照元素进行乘积我们结合更新门来对候选隐藏状态进行进一步的修正，得到最终hidden state的取值

-  

由此得到门控循环单元具有两个显著的特征

- reset有助于捕获序列中的短期依赖关系      
- updater有助于捕获序列中的长期依赖关系

### 2.2 GRU

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-51dd38ee5c89f01a86397bf7ea65ebde_1440w.jpg)





image-20221019152134663

### 2.3 LSTM

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-f6d2611b6db6d67827408baaf003f595_1440w.jpg)





image-20221019152055705

### 2.4 DRNN

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-9ed8793177103d5dcb43981ab5d51aa9_1440w.jpg)





image-20221019152209098

### 2.5 BRNN

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-4240a2e049f7565b7444b061d7750ab3_1440w.jpg)





image-20221019152224395

### 0x03 逻辑过程分析

### 3.1 序列数据读取（example：文本数据）-token-vocab

首先文本数据集从文本读取出来是这样子的

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-c97369ad8a99f54a2bdf8bc3b7619a8e_1440w.jpg)





文本数据集time machine

我们总的idea是希望将上述不规则的文本，转换为可以用向量表示的数字，且为序列数据。Obviously，我们可以用一个词典vocab来将这些词语转换称为数字。那么这个词典的顺序怎么确定？通常是根据词的出现频率来实现的。同时对于出现频率过少的词可以用或者其他来表示。加入我们使用每个单词来表示，得到的vocab就是

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-bd9217544fa54e2715a1f9c1290675eb_1440w.jpg)





vocab-word

除了单词我们也可以使用单个字符char来分割数据集

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-5770cec9107de708ca09e52f07ea9e74_1440w.jpg)





vocab-word

### 3.2 数据预处理-input： batchsize，num_steps--dataloader

在这个基础上我们就可以生成我们的dataloader，因为seq序列天然就有顺序性，而为了提高泛化性我们希望他们可以学习到顺序性，但是学习的又不太多，因此会有两种采样方式来做：

1. 随机采样
2. 顺序分区 

批量的大小由batchsize来确定，同时单次训练的顺序包括num_steps个单词，因此经过这样我们采取送入网络训练的单个batch的形状为

$batch={X_i,y_i},i={X^1,X^2,..,X^{batchsize}};X_i\in\mathbb{R}^{num_steps,len(vocab)}$

总体的shape就是$(batchsize,num_steps,len(vocab))$

这里有个操作是为了后面小批量矩阵运算，会做一个reshape，变成$(num_steps,batchsize,len(vocab))$;可以自行计算梯度变换的方式体会

### 3.3 模型主干-model-loss-optim

初始化参数

选择适合的loss

定义RNN model

### 3.4 训练与可视化- train- predict-ppl

train也就是设置learning rate和epoch训练就好了

### 0x04 代码实现1，2，3

这里完全参考李沐大大的代码，然后将其中的d2l的部分完全剔除得到如下的代码

### 4.1 数据集读取设置

```
import random
import torch
import math
from torch import nn
from torch.nn import functional as F
## 李沐中d2l服务器中存储的数据
DATA_HUB = dict()
DATA_URL = 'http://d2l-data.s3-accelerate.amazonaws.com/'
dataset='time_machine'
dataset_sha256='090b5e7e70c295757f55df93cb0a180b9691891a'
DATA_HUB[dataset]=(DATA_URL+'timemachine.txt',dataset_sha256)

import os
import hashlib
import requests
import re

def download(name, cache_dir=os.path.join('..', 'data')):  #@save
    """下载一个DATA_HUB中的文件 返回本地文件名"""
    url, sha1_hash = DATA_HUB[name]
    os.makedirs(cache_dir, exist_ok=True)
    fname = os.path.join(cache_dir, url.split('/')[-1])
    if os.path.exists(fname):
        sha1 = hashlib.sha1()
        with open(fname, 'rb') as f:
            while True:
                data = f.read(1048576)
                if not data:
                    break
                sha1.update(data)
        if sha1.hexdigest() == sha1_hash:
            return fname  # 命中缓存
    r = requests.get(url, stream=True, verify=True)
    with open(fname, 'wb') as f:
        f.write(r.content)
    return fname


def read_time_machine():  #@save
    """将时间机器数据集加载到文本行的列表中"""
    with open(download('time_machine'), 'r') as f:
        lines = f.readlines()
    return [re.sub('[^A-Za-z]+', ' ', line).strip().lower() for line in lines]

def tokenize(lines, token='char'):  #@save
    """将文本行拆分为单词或字符"""
    if token == 'word':
        return [line.split(' ') for line in lines]
    elif token == 'char':
        return [list(line) for line in lines]
    else:
        print('错误：未知令牌类型：'+token)

import collections  
class Vocab:  #@save
    """文本词表"""
    def __init__(self, tokens=None, min_freq=0, reserved_tokens=None):
        if tokens is None:
            tokens = []
        if reserved_tokens is None:
            reserved_tokens = []
        # 按出现频率排序
        counter = count_corpus(tokens)
        self._token_freqs = sorted(counter.items(), key=lambda x: x[1],
                                   reverse=True)
        # 未知词元的索引为0
        self.idx_to_token = ['<unk>'] + reserved_tokens
        self.token_to_idx = {token: idx
                             for idx, token in enumerate(self.idx_to_token)}
        for token, freq in self._token_freqs:
            if freq < min_freq:
                break
            if token not in self.token_to_idx:
                self.idx_to_token.append(token)
                self.token_to_idx[token] = len(self.idx_to_token) - 1

    def __len__(self):
        return len(self.idx_to_token)

    def __getitem__(self, tokens):
        if not isinstance(tokens, (list, tuple)):
            return self.token_to_idx.get(tokens, self.unk)
        return [self.__getitem__(token) for token in tokens]

    def to_tokens(self, indices):
        if not isinstance(indices, (list, tuple)):
            return self.idx_to_token[indices]
        return [self.idx_to_token[index] for index in indices]

    @property
    def unk(self):  # 未知词元的索引为0
        return 0

    @property
    def token_freqs(self):
        return self._token_freqs

def count_corpus(tokens):  #@save
    """统计词元的频率"""
    # 这里的tokens是1D列表或2D列表
    if len(tokens) == 0 or isinstance(tokens[0], list):
        # 将词元列表展平成一个列表
        tokens = [token for line in tokens for token in line]
    return collections.Counter(tokens)

def load_corpus_time_machine(max_tokens=-1):  #@save
    """返回时光机器数据集的词元索引列表和词表"""
    lines = read_time_machine()
    tokens = tokenize(lines, 'char')
    vocab = Vocab(tokens)
    print(vocab.idx_to_token)
    # 因为时光机器数据集中的每个文本行不一定是一个句子或一个段落，
    # 所以将所有文本行展平到一个列表中
    corpus = [vocab[token] for line in tokens for token in line]
    if max_tokens > 0:
        corpus = corpus[:max_tokens]
    return corpus, vocab

def seq_data_iter_random(corpus, batch_size, num_steps):  #@save
    """使用随机抽样生成一个小批量子序列"""
    # 从随机偏移量开始对序列进行分区，随机范围包括num_steps-1
    corpus = corpus[random.randint(0, num_steps - 1):]
    # 减去1，是因为我们需要考虑标签
    num_subseqs = (len(corpus) - 1) // num_steps
    # 长度为num_steps的子序列的起始索引
    initial_indices = list(range(0, num_subseqs * num_steps, num_steps))
    # 在随机抽样的迭代过程中，
    # 来自两个相邻的、随机的、小批量中的子序列不一定在原始序列上相邻
    random.shuffle(initial_indices)

    def data(pos):
        # 返回从pos位置开始的长度为num_steps的序列
        return corpus[pos: pos + num_steps]

    num_batches = num_subseqs // batch_size
    for i in range(0, batch_size * num_batches, batch_size):
        # 在这里，initial_indices包含子序列的随机起始索引
        initial_indices_per_batch = initial_indices[i: i + batch_size]
        X = [data(j) for j in initial_indices_per_batch]
        Y = [data(j + 1) for j in initial_indices_per_batch]
        yield torch.tensor(X), torch.tensor(Y)

def seq_data_iter_sequential(corpus, batch_size, num_steps):  #@save
    """使用顺序分区生成一个小批量子序列"""
    # 从随机偏移量开始划分序列
    offset = random.randint(0, num_steps)
    num_tokens = ((len(corpus) - offset - 1) // batch_size) * batch_size
    Xs = torch.tensor(corpus[offset: offset + num_tokens])
    Ys = torch.tensor(corpus[offset + 1: offset + 1 + num_tokens])
    Xs, Ys = Xs.reshape(batch_size, -1), Ys.reshape(batch_size, -1)
    num_batches = Xs.shape[1] // num_steps
    for i in range(0, num_steps * num_batches, num_steps):
        X = Xs[:, i: i + num_steps]
        Y = Ys[:, i: i + num_steps]
        yield X, Y

class SeqDataLoader:  #@save
    """加载序列数据的迭代器"""
    def __init__(self, batch_size, num_steps, use_random_iter, max_tokens):
        if use_random_iter:
            self.data_iter_fn = seq_data_iter_random
        else:
            self.data_iter_fn = seq_data_iter_sequential
        self.corpus, self.vocab = load_corpus_time_machine(max_tokens)
        self.batch_size, self.num_steps = batch_size, num_steps

    def __iter__(self):
        return self.data_iter_fn(self.corpus, self.batch_size, self.num_steps)

def load_data_time_machine(batch_size, num_steps,  #@save
                           use_random_iter=False, max_tokens=10000):
    """返回时光机器数据集的迭代器和词表"""
    data_iter = SeqDataLoader(
        batch_size, num_steps, use_random_iter, max_tokens)
    return data_iter, data_iter.vocab
```

### 4.2 模型搭建

```
## define the RNN layer
num_hiddens=256
rnn_layer=nn.RNN(len(vocab),num_hiddens)

## define the hidden state
state=torch.zeros((1,batch_size,num_hiddens))

## define the RNN model
class RNNmodel(nn.Module):
    '''Recurrent nerual netowrk model'''
    def __init__(self,rnn_layer,vocab_size,**kwargs):
        super(RNNmodel,self).__init__(**kwargs)
        self.rnn=rnn_layer
        self.vocab_size=vocab_size
        self.num_hiddens=self.rnn.hidden_size

        # prepare for the GRU
        if not self.rnn.bidirectional:
            self.num_directions=1
            self.linear=nn.Linear(self.num_hiddens,self.vocab_size)
        else:
            self.num_directions=2
            self.linear=nn.Linear(self.num_hiddens*2,self.vocab_size)

    def forward(self,inputs,state):
        X=F.one_hot(inputs.T.long(),self.vocab_size)
        X=X.to(torch.float32)
        Y,state=self.rnn(X,state)

        # 转换y为num_steps,batch_size,num_hiddens
        output=self.linear(Y.reshape((-1,Y.shape[-1])))
        return output,state

    def begin_state(self,device,batch_size=1):
        if not isinstance(self.rnn,nn.LSTM):
            return torch.zeros(self.num_directions*self.rnn.num_layers,batch_size,self.num_hiddens,device=device)
        else:
            # nn.LSTM is hidden state as dict
            return (torch.zeros((
                self.num_directions*self.rnn.num_layers,
                batch_size,
                self.num_hiddens),device=device),
                torch.zeros((self.num_directions*self.rnn.num_layers,
                batch_size,self.num_hiddens),
                device=device
            ))
```

### 4.3 预测与训练

```
def try_gpu(i=0):  #@save
    """如果存在，则返回gpu(i)，否则返回cpu()"""
    if torch.cuda.device_count() >= i + 1:
        return torch.device(f'cuda:{i}')
    return torch.device('cpu')
device=try_gpu()
net=RNNmodel(rnn_layer,vocab_size=len(vocab))
net=net.to(device)

def predict_ch8(prefix, num_preds, net, vocab, device):  #@save
    """在prefix后面生成新字符"""
    state = net.begin_state(batch_size=1, device=device)
    outputs = [vocab[prefix[0]]]

    get_input = lambda: torch.tensor([outputs[-1]], device=device).reshape((1, 1))

    for y in prefix[1:]:  # 预热期
        _, state = net(get_input(), state)
        outputs.append(vocab[y])

    for _ in range(num_preds):  # 预测num_preds步
        y, state = net(get_input(), state)
        outputs.append(int(y.argmax(dim=1).reshape(1)))

    return ''.join([vocab.idx_to_token[i] for i in outputs])

predict_ch8('time traveller',10,net,vocab,device)
```

下面选择一个模型就ok

1. \# 实际训练过程    # params setting    from matplotlib.pyplot import isinteractive    # model setting    net=RNNmodel(rnn_layer,vocab_size=len(vocab))    net=net.to(device) 
2. \# 第二种GRU    num_inputs=len(vocab)    gru_layer=nn.GRU(num_inputs,num_hiddens)    net=RNNmodel(gru_layer,len(vocab))    net=net.to(device) 
3. \# 第三种LSTM    num_inputs=len(vocab)    num_layers=2    lstm_layer=nn.LSTM(num_inputs,num_hiddens,num_layers)    net=RNNmodel(lstm_layer,len(vocab))    net=net.to(device) 
4. \# 第四种 Deep-LSTM    num_inputs=len(vocab)    num_layers=32    lstm_layer=nn.LSTM(num_inputs,num_hiddens,num_layers)    net=RNNmodel(lstm_layer,len(vocab))    net=net.to(device) 

```
num_epochs, lr = 5000, 0.5
# loss function
loss=nn.CrossEntropyLoss()

# optimer or updater
def sgd(params, lr, batch_size):
    """Minibatch stochastic gradient descent.
    Defined in :numref:`sec_utils`"""
    with torch.no_grad():
        for param in params:
            param -= lr * param.grad / batch_size
            param.grad.zero_()

if isinstance(net,nn.Module):
    # what is for??
    updater=torch.optim.SGD(net.parameters(),lr)
else:
    print('net is not as nnModule')
    updater=lambda batch_size:sgd(net.params,lr,batch_size)

def grad_clipping(net, theta):  #@save
    """裁剪梯度"""
    if isinstance(net, nn.Module):
        params = [p for p in net.parameters() if p.requires_grad]
    else:
        params = net.params
    norm = torch.sqrt(sum(torch.sum((p.grad ** 2)) for p in params))
    if norm > theta:
        for param in params:
            param.grad[:] *= theta / norm

predict=lambda prefix:predict_ch8(prefix,50,net,vocab,device)

use_random_iter=False

for epoch in range(num_epoches):
    state=None
    res=[]
    for X,Y in train_iter:
        if state is None or use_random_iter:
            state=net.begin_state(batch_size=X.shape[0],device=device)
        else:
            if isinstance(net,nn.Module) and not isinstance(state,tuple):
                state.detach_()
            else:
                for s in state:
                    s.detach_()

        y=Y.T.reshape(-1)
        X,y=X.to(device),y.to(device)
        y_hat,state=net(X,state)
        l=loss(y_hat,y.long()).mean()

        if isinstance(updater,torch.optim.Optimizer):
            # judge is handSGD
            updater.zero_grad()
            l.backward()
            grad_clipping(net,1)
            updater.step()
        else:
            # define in d2l.sgd
            l.backward()
            grad_clipping(net,1)
            updater(batch_size=1)
        res.append(l)
        print('困惑度{}'.format(str(res[-1])))
```

### 4.5 结果

1. 肯定都能训练出来

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-4288ec2e45151b3e22c38e92752e827c_1440w.jpg)





GRU结果

1. 使用word的训练时间会大于char很多

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-8b40b7fcd7aeccdc306d70f29567523a_1440w.jpg)





word的训练过程

给他一个time traveller，可以得到这个：

time traveller stooping to light a spill at the fire then he turned lighting his pipe to look at the psychologist s face the psychologist to show that he was not unhinged helped himself to a cigar and tried to light it uncut what is more i have a big machine nearly

\-----

时间旅行者弯腰在火上点燃一个溢出物，然后他转动点亮他的烟斗，看着心理学家的脸，心理学家表明他不是不合时宜地帮助自己抽雪茄，并试图点燃它未切割，更重要的是我有一个大机器几乎

1. 深层RNN不一定有效

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-d88257394c9eb89b85df093af2b4b065_1440w.jpg)





32层LSTM的结果

### 0x05 Discussion（个人见识）

1.  MLP或者CNN有点像顺序结构，RNN有点像循环结构，Attention有点像判断结构 
2.  不同结构带来的是不同的感受野，MLP的感受野是个点，CNN的感受野可能是长方形，RNN的感受野可能是很长的序列，Attention则是选择感受野 
3.  RNN很土 

[序列模型——吴恩达深度学习课程笔记（五）](https://cloud.tencent.com/developer/article/1664708)

[动手学深度学习](https://zh-v2.d2l.ai/chapter_natural-language-processing-pretraining/bert.html)

[Deep learning，lan Goodfellow.etal](https://www.deeplearningbook.org/)
