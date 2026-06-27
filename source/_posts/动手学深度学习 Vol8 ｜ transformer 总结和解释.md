---
title: 动手学深度学习 Vol8 ｜ transformer 总结和解释
tags:
  - attention、
  - transformer
  - 深度学习
  - 机器学习
categories:
  - 算法基础
abbrlink: c2dee8e1
date: 2024-04-29 19:13:53
---

这次详细介绍一下transformer框架，以及潜在的想法。在我心中这个是和Alexnet一样的文章。主要从基于RNN的encoder-decoder缺陷中引入attention，并从并行的角度完全引入attention。在第二章介绍attention的机制，在第三章介绍相关的论文细节。在第四章介绍基于torch的实现过程。参考了很多文章！

### 0x01 从RNN到Attention的Encoder- Decoder结构

在这里还没有介绍Attention之前，请把它看作是一种计算权重的方式！第二章会详细介绍，这里不了解attention不会本章的理解。

在时序信息处理中RNN、GRU、LSTM均体现出卓越的优势，通过隐藏状态可以捕获之前的信息，通过时间迁移（Time shift）来不断的预测下一个时间，通过时间梯度反向传播来弥补其中梯度的传播。同时在RNN系列的基础上，Encoder-Decoder架构完善了序列生成的模型框架，在DNA预测、机器翻译转录中取得非常好的效果。

那么什么是Encoder-Decoder框架呢？最基本、最原始的形式沐神在《动手学深度学习》中已经给出

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-cd7f24a0c38046ebcf917098c5bf3863_1440w.png)





添加图片注释，不超过 140 字（可选）

但是如果直接从基本知识跳跃到《Attention is all you need》论文中会发现存在一个GAP，因为在Transformer提出之前，已经有Attention应用在编码器-解码器框架之中，我们会发现上述编码器和解码器之间的状态太过简短，如何提供一种高效的状态传递方式是值得被研究的。于是在机器翻译中Attention被引入，同是存储对应的隐藏状态

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-0977700629d0a5d848c3bf2c1d31c30e_1440w.png)





Encoder- Decoder+attention

（这里是我的想法）虽然这样引入Attention可以保证信息由Encoder传递给Decoder，但是由于RNN时间步进的限制，无法并行计算导致模型依旧训练缓慢。

那么是否有一种方式：可以得到隐藏表示向量，同时可以并行计算？第一种想法是CNN，感觉应该有相关方面的研究，第二种方法是Attention，也就是本文提出的Transformer结构。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-fac942f68181c809efc96cc4ce340e86_1440w.png)





Transformer

### 0x02 从B站搜索到Attention维度分析

### 2.1 视频检索到Attention

在介绍Attention中需要包括三个部分：查询（Query）、键（Key）、值（Value）。这里以B站的视频为例子，假设这里只有seq-len=5个视频，是我们的value，每一个视频会对应不同的key，当我们给出我们的query，对于不同的query会分别与这些key进行计算得到一个权重，我们由这些权重作为相关度进行排序。（如有错误可以指出）

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-40c22e33946b72dd4eae89bba923b947_1440w.png)





视频检索Attention

### 2.2 Attention维度分析

虽然这样迷迷糊糊的，最终计算的过程中都是一样的，在基于Torch的attention计算中，我们当然不能手动实现QKV的计算，因此我们简单的将原始数据输入，由内部确认权重来转换成为QKV即可。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-812829faabb3450bc9e11c5b5d769ada_1440w.png)





维度分析

### 2.3 Multi-head attention

通过上述算法我们得到一个向量，其形状一般为原始维度（N，D），这里和CNN的单通道卷积很相似，但是我们知道多通道卷积在卷积神经网络中有一定的效果，这样可以一定程度上以ensemble的思路来提高结果的准确性，因此我们可以设置Multi-head attention作为多通道输入。其具体实现是将上述的QKV拆分为按照nhead的数量来拆分，分别计算attention之后在concat

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-1d6ec25bdd4945788a19cf588ef34fa3_1440w.png)





multi-head attention

### 2.4 Attention mask

在我们计算最后的输出的时候，在得到权重矩阵的时候要知道在decoder情况下并不能获取之后的信息，也就是在我们没有看到的数据中我们是不能计算权重的，因此我们可以设置一个可选的（Optional）来作为计算。

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-1862876f7fd714fd287e1e7fffa68fa1_1440w.png)





Attention Maskpng

### 2.5 Attention layer实现

我是手撕不了，网上有很多大神手撕的案例。但是我还是推荐观察Torch的官方代码。

### 0x03 魔鬼细节：Embedding，LayerNorm，Add

上述已经大体介绍Transformer的结构，但是这里依旧存在一些细节。

Embedding：将原始变量转换成为可以训练的向量，通常需要做一层语义embedding，但是除此之外，因为Transformer无法感知时序信息，通常需要Position Embedding来覆盖两者之间的顺序

LayerNorm：相比较BatchNorm可以更加容易推理，同时主要是为了模型训练的时候可以收敛。具体原因有相关论文的梯度分析

Add：参考Resnet的分析！yyds

### 0x04 基于Torch实现Transformer

主要是实现encoder layer、decoder layer，encoder、decoder

### 4.1 Embedding

```
import torch
import torch.nn as nn
import math
class TokenEmbedding(nn.Module):
    def __init__(self, vocab_size,embedding_size) -> None:
        super().__init__()
        self.embedding = nn.Embedding(vocab_size,embedding_size)
        self.embedding_size = embedding_size

    def forward(self,x):
        return self.embedding(x.long())*math.sqrt(self.embedding_size)

class PositionalEncoding(nn.Module):
    def __init__(self,dimen,dropout=0.1,max_len=5000):
        super().__init__()
        self.dropout = nn.Dropout(p=dropout)
        PE = torch.zeros(max_len,dimen)
        position = torch.arange(0,max_len,dtype=torch.float).unsqueeze(1)
        div_term = torch.exp(torch.arange(0,dimen,2).float() * (-math.log(10000.0) / dimen))
        PE[:,0::2] = torch.sin(position * div_term)
        PE[:,1::2] = torch.cos(position * div_term)
        PE = PE.unsqueeze(0).transpose(0,1)
        self.register_buffer('PE',PE)

    def forward(self,x):
        x = x + self.PE[:x.size(0),:]
        return self.dropout(x)

x = torch.tensor([[1,2,3,4,5],[5,6,7,8,9]],dtype=torch.float)
x = x.reshape(5,2) # note that the batch size is the second dimension
vocab_size = 10
embedding_size = 512

token_embedding = TokenEmbedding(vocab_size,embedding_size)
x = token_embedding(x)
print('token embedding size is ',x.shape)
pos_embedding = PositionalEncoding(embedding_size)
x = pos_embedding(x)
print('Position embedding size is',x.shape)
print('The size must be [seq_len,batchsize,embedding_size]')
```

### 4.2 Encode layer and encoder

```
class TransformerEncoderLayerStractch(nn.Module):
    def __init__(self,dimen,nhead,dim_forward=2048,dropout=0.1):
        super().__init__()
        self.self_attn = nn.MultiheadAttention(dimen,nhead,dropout=dropout)
        self.dropoutAttn = nn.Dropout(dropout)
        self.norm1 = nn.LayerNorm(dimen)
        self.linear1 = nn.Linear(dimen,dim_forward)
        self.dropout1 = nn.Dropout(dropout)
        self.linear2 = nn.Linear(dim_forward,dimen)
        self.activation = nn.ReLU()
        self.dropout2 = nn.Dropout(dropout)
        self.norm2 = nn.LayerNorm(dimen)

    def forward(self,x,mask=None,src_key_padding_mask=None):
        '''
        x: [seq_len,batch_size,embedding_size]
        '''
        x2,_ = self.self_attn(x,x,x,attn_mask=mask,key_padding_mask=src_key_padding_mask)
        # x2: [seq_len,batch_size,embedding_size*nhead]
        x = x + self.dropoutAttn(x2)
        x = self.norm1(x)

        x2 = self.activation(self.linear1(x))
        x2 = self.linear2(self.dropout1(x2))
        x = x + self.dropout2(x2)

        x = self.norm2(x)
        return x 
print('Test the transformer encoder layer')
nhead = 8
layer = TransformerEncoderLayerStractch(embedding_size,nhead)
x = layer(x)
import copy
def get_copy(module,N):
    return nn.ModuleList([copy.deepcopy(module) for _ in range(N)])

class TransformerEncoderStractch(nn.Module):
    def __init__(self,encoder_layer,num_layers,norm=None):
        super().__init__()
        self.layers = nn.ModuleList([encoder_layer for _ in range(num_layers)])
        self.norm = norm
        self.num_layers = num_layers

    def forward(self,x,mask=None,src_key_padding_mask=None):
        output = x
        for layer in self.layers:
            output = layer(output,mask=mask,src_key_padding_mask=src_key_padding_mask)
        if self.norm is not None:
            output = self.norm(output)
        return output 

encoder = TransformerEncoderStractch(layer,6)
x = encoder(x)
print('Test the transformer encoder')
loss = nn.MSELoss()
print(loss(x,encoder(x)))
```

### 4.3 Decoder layer 和Decoder

```
class TransformerDecoderLayerStractch(nn.Module):
    def __init__(self,dimen,nhead,dim_forward=2048,dropout=0.1):
        super().__init__()
        self.self_attn = nn.MultiheadAttention(dimen,nhead,dropout=dropout)
        self.dropoutAttn = nn.Dropout(dropout)
        self.norm1 = nn.LayerNorm(dimen)
        self.multihead_attn = nn.MultiheadAttention(dimen,nhead,dropout=dropout)
        self.dropoutAttn2 = nn.Dropout(dropout)
        self.norm2 = nn.LayerNorm(dimen)
        self.linear1 = nn.Linear(dimen,dim_forward)
        self.dropout1 = nn.Dropout(dropout)
        self.linear2 = nn.Linear(dim_forward,dimen)
        self.dropout2 = nn.Dropout(dropout)
        self.norm3 = nn.LayerNorm(dimen)
        self.activation = nn.ReLU()

    def forward(self,tgt,memory,tgt_mask=None,memory_mask=None,tgt_key_padding_mask=None,memory_key_padding_mask=None):
        ''' 
        tgt = [tgt_len,batch_size,embedding_size]
        memory = [seq_len,batch_size,embedding_size]
        '''
        tgt2,_ = self.self_attn(tgt,tgt,tgt,attn_mask=tgt_mask,key_padding_mask=tgt_key_padding_mask)
        tgt = tgt + self.dropoutAttn(tgt2)
        tgt = self.norm1(tgt)
        # tgt = [tgt_len,batch_size,embedding_size]

        tgt2,_ = self.multihead_attn(tgt,memory,memory,attn_mask=memory_mask,key_padding_mask=memory_key_padding_mask)
        tgt = tgt + self.dropoutAttn2(tgt2)
        tgt = self.norm2(tgt)
        # tgt = [tgt_len,batch_size,embedding_size]

        tgt2 = self.activation(self.linear1(tgt))
        tgt2 = self.linear2(self.dropout1(tgt2))
        tgt = tgt + self.dropout2(tgt2)

        tgt = self.norm3(tgt)
        # tgt = [tgt_len,batch_size,embedding_size]
        return tgt
print('Test the transformer decoder layer')
decoder_layer = TransformerDecoderLayerStractch(embedding_size,nhead)
x = decoder_layer(x,layer(x))
print(loss(x,decoder_layer(x,layer(x))))

class TransformerDecoderStractch(nn.Module):
    def __init__(self,decoder_layer,num_layers,norm=None):
        super().__init__()
        self.layers = nn.ModuleList([copy.deepcopy(decoder_layer) for _ in range(num_layers)])
        self.norm = norm
        self.num_layers = num_layers

    def forward(self,tgt,memory,tgt_mask=None,memory_mask=None,tgt_key_padding_mask=None,memory_key_padding_mask=None):
        output = tgt
        for layer in self.layers:
            output = layer(output,memory,tgt_mask=tgt_mask,memory_mask=memory_mask,tgt_key_padding_mask=tgt_key_padding_mask,memory_key_padding_mask=memory_key_padding_mask)
        if self.norm is not None:
            output = self.norm(output)
        return output
encoder = TransformerEncoderStractch(layer,6)
decoder = TransformerDecoderStractch(decoder_layer,6)
```

### 4.4 完整的Transformer

```
# Bulid the transformer
class MyTransformer(nn.Module):
    def __init__(self,dimen=512,nhead=8,num_encoder_layers=6,num_decoder_layers=6,dim_forward=2048,dropout=0.1):
        super().__init__()
        encoder_layer = TransformerEncoderLayerStractch(dimen,nhead,dim_forward,dropout)
        encoder_norm = nn.LayerNorm(dimen)
        self.encoder = TransformerEncoderStractch(encoder_layer,num_encoder_layers,encoder_norm)

        decoder_layer = TransformerDecoderLayerStractch(dimen,nhead,dim_forward,dropout)
        decoder_norm = nn.LayerNorm(dimen)
        self.decoder = TransformerDecoderStractch(decoder_layer,num_decoder_layers,decoder_norm)

        self._reset_parameters()
        self.dimen = dimen
        self.nhead = nhead

    def forward(self,src,tgt,src_mask=None,tgt_mask=None,memory_mask=None,src_key_padding_mask=None,tgt_key_padding_mask=None,memory_key_padding_mask=None):
        memory = self.encoder(src,mask=src_mask,src_key_padding_mask=src_key_padding_mask)
        output = self.decoder(tgt,memory,tgt_mask=tgt_mask,memory_mask=memory_mask,tgt_key_padding_mask=tgt_key_padding_mask,memory_key_padding_mask=memory_key_padding_mask)
        return output

    def _reset_parameters(self):
        for p in self.parameters():
            if p.dim() > 1:
                nn.init.xavier_uniform_(p)

    def generate_square_subsequent_mask(self,sz):
        mask = (torch.triu(torch.ones(sz,sz)) == 1).transpose(0,1)
        mask = mask.float().masked_fill(mask == 0,float('-inf')).masked_fill(mask == 1,float(0.0))
        return mask
```

### 4.5 测试

```
seq_len = 12
batch_size = 2
dimen = 128
tag_len = 10
nhead = 8
input = torch.randn(seq_len,batch_size,dimen)
target = torch.randn(tag_len,batch_size,dimen)

model = MyTransformer(dimen=dimen,nhead=nhead,num_decoder_layers=6,num_encoder_layers=6,dim_forward=2048,dropout=0.1)

tgt_mask = model.generate_square_subsequent_mask(tag_len)
out = model(input,target,tgt_mask=tgt_mask)
print('Test the transformer')
print(loss(out,model(input,target,tgt_mask=tgt_mask)))
```

[transform模型的参数量、计算量规模估算](https://zhuanlan.zhihu.com/[https://zhuanlan.zhihu.com/p/624740065](https://zhuanlan.zhihu.com/p/624740065))

[The post is all you need —- 月来客栈](https://zhuanlan.zhihu.com/[https://www.ylkz.life/deeplearning/p10553832/](https://www.ylkz.life/deeplearning/p10553832/))

[使用Transformer进行概率时间序列预测](https://zhuanlan.zhihu.com/[https://huggingface.co/blog/zh/time-series-transformers](https://huggingface.co/blog/zh/time-series-transformers))
