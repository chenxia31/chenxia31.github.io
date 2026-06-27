---
title: 动手学深度学习 Vol7 ｜ 如何理解 rnn 中的循环
tags:
  - 循环神经网络
  - RNN
  - 深度学习
categories:
  - 算法基础
abbrlink: 3af655a9
date: 2024-04-29 19:13:51
---

相比较MLP和CNN，RNN结构仿佛尤其的神秘。相比较更加直观的可视化，实现端到端的输出，包含隐藏状态（Hidden state）的时序建模方式对于RNN代码的理解真是尤其的困难。虽然Torch为我们提供封装好函数，但是我更加希望去探索其封装借口背后的结构，来提高自己的理解能力。

### 0x01 缘起

相比较MLP和CNN，RNN结构仿佛尤其的神秘。相比较更加直观的可视化，实现端到端的输出，包含隐藏状态（Hidden state）的时序建模方式对于RNN代码的理解真是尤其的困难。虽然Torch为我们提供封装好函数，但是我更加希望去探索其封装借口背后的结构，来提高自己的理解能力。

### 0x02 数学公式

对于神经网络的计算方式可以考虑层层递进，包括单个神经元的前向传播、单个神经元的反向梯度下降、批量梯度下降。由此来理解它是如何使用的、如何学习的以及如何更好的工程化学习。

### 2.1 基本思想

RNN、LSTM、GRU等等这类循环神经网络更多的是参考如何应用在序列的数据中，参考沐神的解释，这一类网络结构的特点优势包括：

1. 跨时间权重共享
2. 时间平移不对称
3. 适用不同长度

这里有点太晦涩了。当我们以时间序列预测（Time series forecasting）的方式来理解一个问题，我们如何从t的状态来预测t+1，经过分析我们会知道t+1状态并不完全取决于t。比如同一支股票在相同的价格均会存在下跌和上涨的状态，因此我们会希望另外一个隐藏值H来表示之前所有序列的状态，但是这个隐藏状态可能不能太长，导致隐含信息被平滑，也不能太短到无法提供信息。因此我们需要选择合适的WINDOW-SIZE。由此我们的估计值应该包括

𝑦𝑡=𝑓(𝑥𝑡,ℎ𝑡−1) 

所以存在部分量一直在被网络输入和输出，一直在循环的迭代。如果我们再抽离整个网络结构，我们会发现我们似乎在对每个步骤重复一些值的计算，只是我们并没有将其作为输出。这一类的基本思想为

>  我们坚信，在相当长的序列中存在不同类别的形式。这样的参数共享可以使得模型在不同输入样本下得到更好的泛化。

### 2.2 前向传播

这个时候我们先定义我们的数据集，是长度为N的维度为D的序列组成$X=(x_1,x_2,...,x_t,..)R^{N*D}$,由此我们根据当前状态和上一状态的隐藏值更新隐藏状态，同时也根据隐藏状态和当前输入得到预测输出。

𝐻𝑡=𝜙(𝑥𝑡𝑊𝑥ℎ+𝐻𝑡−1𝑊ℎℎ+𝑏ℎ) 

𝑌𝑡=𝜙(𝐻𝑡𝑊𝑜ℎ+𝑏𝑜) 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-1eb2577019631a9391a365d83257f6b4_1440w.png)





Untitled

### 2.3 反向梯度下降

这个网络结构看上去可以实现，看上去也非常的诱人。但是如何构造一种学习的方式来对其参数更新并且有效呢？梯度下降（Gradient Decent）已经被证明在MLP和CNN中有效，由此我们可以尝试对RNN的梯度进行分析，来判断我们可以如何来接近真值（Ground-truth）。

 

但是要知道隐藏状态是会循环的，我们发现在计算梯度的时候，似乎它并不会消失，而是会一直计算下去，它可能会迭代消失，但是更多情况下是会迭代爆炸。因为这种无限可能性的存在，导致我们很容易焦虑因此在循环神经网络中往往会有的技巧（Trick）是对其梯度进行裁剪，或者是增加新的控制单元来解决正则化。具体的实现方式并不在这里赘述。

### 2.4 批量梯度下降

如果说上述是理论分析部分，下面希望是实践的角度来说明，如何确定网络构建的参数。首先定义两个基本的原则：

1. 要明白Tensor的维度规则，是自外向内的。同时可以通过折叠维度的方式来升维和降维
2. 矩阵乘法的过程中，M*N和N*Q的结果是M*Q
3. 利用矩阵乘法广播和利用批量乘法是不一样的，因为有的三维数组并不能广播。B*M*N和B*N*Q想乘得到B*M*Q

下文中变量的维度D为输入维度 input_size，其中隐藏状态维度为hidden_size,输出维度D(与输入维度相同）为output_size

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-d666ef1e4ab0b80d3ffb14261652f233_1440w.png)





Untitled

在使用批量梯度下降的过程中，因为同一个batch中的数据是不同的序列，因此隐藏状态并不是1*h的广播，而必须是batchsize*h。因此这里并不像MLP可以使用广播。同时这里的重点也不是想得到多么好的隐藏状态h，而是具有一种可以反应正常状态的规则，也就是权重，这才是真正希望学习的参数

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-82caef534ab1f798c929c78285e7a181_1440w.png)





Untitled

疑问：但是在训练过程中隐藏状态都是随机产生的，虽然可能在一定时间预热（Warm up ）之后得到一个比较好的效果，但是依旧很难反应他是真实有效的反应隐藏状态的？这里的梯度下降似乎没有理论依据

注意这里还会有batch是否设置为first，这样对最终的矩阵的形状还会有不同的影响。

### 0x03 时间序列预测任务

降雨量数据预测

```
import torch 
import torch.nn as nn
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
df = pd.read_csv('../input/Rainfall_data.csv')
df.info()

plt.subplot(4,1,1)
plt.plot(df['Specific Humidity'])
plt.title('Specific Humidity')
plt.subplot(4,1,2)
plt.plot(df['Relative Humidity'])
plt.title('Relative Humidity')
plt.subplot(4,1,3)
plt.plot(df['Temperature'])
plt.title('Temperature')
plt.subplot(4,1,4)
plt.plot(df['Precipitation'])
plt.title('Precipitation')
plt.tight_layout()
plt.show()
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-764f951c5572d18050c026742a4dc30b_1440w.png)





Untitled

使用自回归进行预测

```
from statsmodels.tsa.ar_model import AutoReg

# Train the model and gets the params
x = df['Precipitation'].values
train, test = x[:len(x)-50], x[len(x)-50:]
p = 12
model = AutoReg(train, lags = p)
model_fit = model.fit()
print('Coefficients: %s' % model_fit.params)
params = model_fit.params

# Walk forward over time steps in test
history = train[len(train)-p:]
history = [history[i] for i in range(len(history))]
predictions = list()
for t in range(len(test)):
    length = len(history)
    lag = [history[i] for i in range(length-p,length)]
    yhat = params[0]
    for d in range(p):
        yhat += params[d+1] * lag[p-d-1]
    obs = test[t]
    predictions.append(yhat)
    history.append(obs)
    print('predicted=%f, expected=%f' % (yhat, obs))

# Evaluate forecasts
from sklearn.metrics import mean_squared_error
error = mean_squared_error(test, predictions)
print('Test MSE: %.3f' % error)
plt.plot(test)
plt.plot(predictions, color='red')
plt.show()
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-3334cd286b13c31caad3d5d151903c62_1440w.png)





Untitled

利用RNN进行序列预测

```
print('The length of the dataset is:',len(train))
print('The length of the test set is:',len(test))
window_size = 12
def input_data(seq,ws):
    output = []
    for i in range(len(seq)-ws):
        output.append(seq[i:i+ws])
    return np.array(output)

train_set = input_data(train,window_size)
test_set = input_data(test,window_size)
print(train_set)

input_tensor = torch.Tensor(train_set)
test_set = torch.Tensor(test_set)

class MyDataset(torch.utils.data.Dataset):
    def __init__(self, train_set):
        self.train_set = train_set

    def __getitem__(self, index):
        return self.train_set[index,:11], self.train_set[index,-1]

    def __len__(self):
        return len(self.train_set)

train_dataset = MyDataset(input_tensor)
train_loader = torch.utils.data.DataLoader(train_dataset, batch_size=16, shuffle=False)

for x,y in train_loader:
    print(x.shape,y.shape)
    break

device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

class MyRnn(nn.Module):
    def __init__(self, input_size,hidden_size,num_layers,output_size):
        '''
        input_size: number of features
        hidden_size: number of hidden units
        num_layers: number of layers
        output_size: number of output
        '''
        super().__init__()
        self.hidden_size = hidden_size
        self.num_layers = num_layers
        self.rnn = nn.RNN(input_size, hidden_size, num_layers, batch_first=True)
        self.fc = nn.Linear(hidden_size, output_size)

    def forward(self, x):
        h0 = torch.zeros(self.num_layers, x.size(0), self.hidden_size).to(device)
        out, _ = self.rnn(x, h0)
        out = self.fc(out[:, -1, :])
        return out

input_size = 1
hidden_size = 128
num_layers = 2
output_size = 1

model = MyRnn(input_size, hidden_size, num_layers, output_size).to(device)
criterion = nn.MSELoss()
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)

num_epochs = 5000
for epoch in range(num_epochs):
    for x,y in train_loader:
        x.unsqueeze_(-1)
        x = x.to(device)
        y = y.to(device)
        output = model(x)
        loss = criterion(output, y.unsqueeze(1))
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()
    if epoch % 100 == 0:
        print('Epoch: {}, Loss: {:.5f}'.format(epoch, loss.item()/len(x)))
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-7b921b5a98bd507118332c5c0c9591b6_1440w.png)





Untitled

利用RNN进行多变量时序预测

```
dataset = df.iloc[:,3:7].values
train_set = dataset[:len(dataset)-50]
test_set = dataset[len(dataset)-50:]

WINDOWS_SIZE = 12
def input_data(seq,ws):
    output = []
    for i in range(len(seq)-ws):
        output.append(seq[i:i+ws])
    return np.array(output)

train_set = input_data(train_set,WINDOWS_SIZE)
test_set = input_data(test_set,WINDOWS_SIZE)

input_tensor = torch.Tensor(train_set)
test_set = torch.Tensor(test_set)

class MyDataset(torch.utils.data.Dataset):
    def __init__(self, train_set):
        self.train_set = train_set

    def __getitem__(self, index):
        return self.train_set[index,:11], self.train_set[index,-1]

    def __len__(self):
        return len(self.train_set)

train_dataset = MyDataset(input_tensor)
train_loader = torch.utils.data.DataLoader(train_dataset, batch_size=16, shuffle=False)

device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

test_dataset = MyDataset(test_set)
test_loader = torch.utils.data.DataLoader(test_dataset, batch_size=1, shuffle=False)

input_size = 4
model = MyRnn(input_size, hidden_size, num_layers, output_size).to(device)
epochs = 5000
for epoch in range(epochs):
    for x,y in train_loader:
        x = x.to(device)
        y = y.to(device)
        output = model(x)
        loss = criterion(output, y.unsqueeze(1))
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()
```
