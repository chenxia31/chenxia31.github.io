---
title: CS229 机器学习Vol13 ｜ EM 算法与 HMM 和 CRF
tags:
  - CS229
  - 机器学习
  - EM 算法
  - HMM
  - CRF
categories:
  - 算法基础
abbrlink: 8b4f9c0c
date: 2024-04-29 19:08:53
---

本文主要介绍EM算法，作为统计学习中可以通过迭代的方式估计含有隐变量模型的方法。同时给出两个典型的例子作为注释并给出相关代码的分析作为注解。

### 0x01 摘要

（个人感悟）对于人工智能三大流派的分析，统计学习、神经网络和行为学习的一些新的感悟。首先统计学习依靠概率统计的知识建立起的模型和神经网络的范式并不完全一样，可能在模型、推理、优化等方面有名称的雷同但两者是完全不同的概念。同时以约束编程或者强化学习的代表的行为学习是处于这两者之上层的控制决策手段。

针对统计学习而言，其基本的任务分为参数估计和假设检验。这里的参数估计更多的是使用统计的方法来进行，最常见的MLE为极大似然估计（通过最大化likelihood function）来得到对应的估计。这次介绍的EM算法可以看作是一种对含隐变量的新估计方法。

### 0x02 EM算法

首先回顾一下，对于不含隐变量的方法我们会如何建模，{𝑥𝑖,𝑦𝑖}，最常见的是可以建立起判别式模型（Discriminate）比如逻辑回归、支持向量机、决策树等进行学习得到𝑃(𝑌|𝑋)。但是有的时候这样太直接了，我们可以从另外生成式的角度从每个y进行建模得到𝑎𝑟𝑔𝑚𝑎𝑥𝑦𝑃(𝑋|𝑌)𝑃(𝑌),来实现类似的效果。

但是加入对于没有标签的数据，我们通常会使用非监督学习(unsupervised learning)的方式来得到答案，比如Kmeans的方式。而EM算法是从另外一个角度来对其进行建模。其中心思想分为两步：

1. 随机初始化参数
2. E步 即然不知道隐藏状态，那么计算所有隐藏状态下的概率。也就是计算出不同隐藏状态下，对应的概率 𝑃(𝑋,𝑍;𝜃) 
3. M步 优化参数，最大化似然值来得到答案

假设坐标轴中分为两类点 {𝑥𝑖,𝑧𝑖},其中相同类型点的分布满足高斯分布，也就是𝑥|𝑧=𝑗:𝑁(𝑢𝑗,Σ𝑗) 

，同时假设点的隐藏变量为二项分布，则𝑧:𝐵(𝜙),由此我们可以写出：

𝑝(𝑥𝑖,𝑧𝑖)=𝑝(𝑥𝑖|𝑧𝑖;𝑢,Σ)𝑝(𝑧𝑖,𝜙) 

这样可以很轻松的写出含有隐变量的似然函数

𝑙(𝑢,Σ,𝜙)=𝑙𝑜𝑔∏𝑖𝑝(𝑥𝑖,𝑧𝑖)=Σ𝑖𝑙𝑜𝑔Σ𝑗𝑝(𝑥𝑖|𝑧𝑖=𝑗;𝑢𝑗,Σ𝑗)𝑝(𝑧𝑖=𝑗,𝜙) 

如果我们知道隐变量取值,就可以使用GDA轻松求出上述的变量梯度，并开始下降。

但是我们不知道，因此我们可以先计算m个样本隐变量k中情况下的概率：

𝑄𝑖(𝑍𝑖)=𝑝(𝑧𝑖|𝑥𝑖,𝜙,𝑢,Σ)∈𝑅(𝑚,𝑘) 

之后我们通过琴生不等式的证明来优化上述含有隐变量的似然函数下界

𝑙(𝑢,Σ,𝜙)≥Σ𝑖Σ𝑗𝑄𝑖(𝑧=𝑗)𝑙𝑜𝑔(𝑃(𝑥𝑖,𝑧=𝑗;𝜃)𝑄𝑖(𝑧=𝑗)) 

因为这里的隐变量的分布是已知的，所以必然是可以被优化的。

𝑢^,Σ^,𝜙^=𝑎𝑟𝑔𝑚𝑎𝑥(Σ𝑙𝑜𝑔(𝑃(𝑥𝑖,𝑧=𝑗;𝜃)𝑄𝑖(𝑧=𝑗)) 

例如GMM的标准的求导之后的已经有人解析过：

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-3af821a567b7471d91e5d90a8d3b9a3d_1440w.png)





GMM中的M步

由此不断的迭代求解得到答案，或者是解析解得到答案

到这一步其实发现EM算法并不是一个单纯的模型，而更多是一种解决手段，它是类似MLE一样单纯作为估计的方式，如何将其应用在不同的建模场景中可以得到不同的模型，比如应用在序列标注中的HMM，CRF。个人的直觉上觉得它很多的代表一种类似蒙特卡洛的思想，这里的猜测或者掷骰子是一种没有先验知识而去探索exploration的感觉，在GAN、知识蒸馏、强化学习、序列预测中感觉都会用到相关的概念。

### 0x03 隐马尔可夫模型 HMM

隐马尔可夫模型是用于标记问题的统计学习模型，比如说给照片流进行标记，同时在这类随机过程的研究中主要问题有：1. 如何计算概率  2. 如何根据观测值确定参数 3. 如何根据给定的观测值确定背后的状态

### 3.1 HMM定义与模拟

隐马尔可夫模型是关于时序 的概率模型，描述由一个隐藏的马尔可夫链随机生成不可观测的状态随机序列，再由各状态生成不同观测随机序列的过程，其中常见的参数和集合的描述如下：

状态序列 State/Track : state sequence(which is latent)

观察序列 Observation : observation sequence

状态空间 Q: state spcae 1,..,N

观测空间 V: observation spae 1,...,M

I: state sequence

O: observation sequence

转移矩阵 A: transition matrix P(I|I) R(N,N)

状态观测矩阵B: observation matrix P(O|I) R(N,M)

初始概率分布 Pi: init state distribution Pi(I) R(1,N])

在这之中包含两个重要的基础假设（随机过程 is all you need）：

1. 齐次马尔可夫假设，假设隐藏的马尔可夫链在任意时刻t的状态只依赖于其前一时刻的状态，与其他时刻的状态及观测无关，也和时刻无关
2. 观测独立性假设，假设任意时刻表的观测只依赖于该时刻的马尔可夫链的状态，与其他的观测和状态无关

下面介绍一个例子，我们将 “Health”和“Fever“作为状态，以不同的表现形式”normal“，‘cold’，‘dizzy”作为观测，同时定义转移概率和转移矩阵等参数，注意这里给出的map形式后面用了函数调整，让解释性变差：

```
import numpy as np

# state space
Q = {'Healthy','Fever'}

# observation space
V = {'normal','cold','dizzy'}

# init state distribution
Pi = {
    'Healthy':0.6,
    'Fever':0.4
}

# state transition
A = {
    'Healthy': {'Healthy': 0.7, 'Fever': 0.3},
    'Fever': {'Healthy': 0.4, 'Fever': 0.6},
}

# observation transition
B = {
    'Healthy': {'normal': 0.5, 'cold': 0.4, 'dizzy': 0.1},
    'Fever': {'normal': 0.1, 'cold': 0.3, 'dizzy': 0.6},
}

def generate_index_map(labels):
    index2label = {}
    label2index = {}
    i = 0
    for label in labels:
        index2label[i] = label
        label2index[label] = i
        i += 1
    return index2label,label2index

Qlabel,Qindex = generate_index_map(Q)
Vlabel,Vindex = generate_index_map(V)
print('Qlabel,Qindex,Vlabel,Vindex:')
print(Qlabel,Qindex)
print(Vlabel,Vindex)

def gen_Pi(pi_dict,Qlabel):
    v = np.zeros(len(Qlabel),dtype=float)
    for key in pi_dict:
        v[Qlabel[key]] = pi_dict[key]
    return v 

def gen_matrix(trans_dict,Qlabel,Vlabel):
    res = np.zeros((len(Qlabel),len(Vlabel)),dtype=float)
    for si in trans_dict:
        for sj in trans_dict[si]:
            res[Qlabel[si]][Vlabel[sj]] =  trans_dict[si][sj]
    return res 

A = gen_matrix(A,Qindex,Qindex)
B = gen_matrix(B,Qindex,Vindex)
pi = gen_Pi(Pi,Qindex)
print('state transition matrix')
print(A)
print('state-observation matrix')
print(B)
print('state prob distribution')
print(pi)
```

之后我们来模拟生成

```
def simulate(T,A,B,pi):
    def draw_from(state_prob_distribution):
        return np.where(np.random.multinomial(1,state_prob_distribution)==1)[0][0]
    observations = np.zeros(T,dtype=int)
    states = np.zeros(T,dtype=int)
    states[0] = draw_from(pi)
    observations[0] = draw_from(B[states[0],:])
    for t in range(1, T):
        states[t] = draw_from(A[states[t-1],:])
        observations[t] = draw_from(B[states[t],:])
    return observations, states

# simulate the data 
O,I = simulate(10,A,B,pi)
print([Qlabel[i] for i in I])
print([Vlabel[j] for j in O])
```

### 3.2 HMM概率计算

如何计算一条轨迹的概率 其实是一件非常复杂的事情，这个看上去其实可以使用一个R(m^t)来描述，这里参考李航老师的书中给出前向概率的计算和后向概率,之后引申地给出和的计算方式，注意后者都是未来后面问题2用的

其实前向传播计算和后向传播计算的逻辑很简单，

在给定的观察轨迹情况下：指的是在时刻t的时候状态s=q的概率，所有 R(K,T)

指的是在给定的观察轨迹情况下，时刻t到T给给定轨迹下，t时刻s=q的概率，大小同样为R(K,T)

由此可以得到

 表示给定模型和观测值的情况下，时刻表t处于状态q的概率为：

 

表示给定模型和观察情况下在，在时刻表t处于状态i，在时刻表t+1处于状态j的概率

 

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-72b267e75679db40d6f187296881442e_1440w.jpg)





概率计算

```
# In the study of HMM, there are three main problems
# Track Probability: give the O={o1,o2,...,oT},P(O)
# Learning problem: give the O, estimate the parameters
# Forecasting problem: (decoding problem also),given the observation track, 
#                       show the most likely state track

def forward(obs_seq,A,B,pi):
    '''cal the probability from the observation sequence with forward''' 
    state_len = A.shape[0]
    track_len = len(obs_seq)
    F = np.zeros((state_len,track_len))

    # init the state with the pi
    F[:,0] = pi*B[:,obs_seq[0]]
    for t in range(1,track_len):
        for n in range(state_len):
            F[n,t] = np.dot(F[:,t-1],(A[:,n]))*B[n,obs_seq[t]]
    return F

def backward(obs_seq,A,B,pi):
    '''cal the probability from the observation sequence with backward''' 
    N = A.shape[0]
    T = len(obs_seq)
    # X保存后向概率矩阵
    X = np.zeros((N,T))
    X[:,-1:] = 1

    for t in reversed(range(T-1)):
        for n in range(N):
            X[n,t] = np.sum(X[:,t+1] * A[n,:] * B[:, obs_seq[t+1]])

    return X

print('track probability forward')
print(forward(O,A,B,pi))
print('track probability backward')
print(backward(O,A,B,pi))
```

### 3.3 HMM参数估计

这里使用学习方法，将EM用在HMM中得到Baum Welch算法

首先确定E步中的Q函数

 

=  

之后在M步中更新参数，这里有人求解过了

 

 

 

```
# Based on the EM algrithm, the HMM learning process can be solved by Baum-Weich algorithm
def baum_weich(obs_seq,A,B,pi,criterion=0.05):
    '''
    Unsupervised learning algorithm
    '''
    n_states = A.shape[0]
    n_samples = len(obs_seq)

    done = False

    while not done:
        # Cal the track probability
        # alpha(i) = p(obs_seq | q0=s_i,hmm)
        alpha = forward(obs_seq,A,B,pi)
        # beta(i)=p(obs_seq | q0=s_i,hmm)
        beta = backward(obs_seq,A,B,pi)

        # xi: in the timestamp t, the t state is i and the t+1 state is j
        xi = np.zeros((n_states,n_states,n_samples-1))
        for t in range(n_samples-1):
            denom = np.dot(np.dot(alpha[:,t].T, A) * B[:,obs_seq[t+1]].T, beta[:,t+1])
            for i in range(n_states):
                numer = alpha[i,t] * A[i,:] * B[:,obs_seq[t+1]].T * beta[:,t+1].T
                xi[i,:,t] = numer / denom

        # gamma, in the timestamp t ,the t state is i
        gamma = np.sum(xi,axis=1)
        # prod for what
        prod = (alpha[:,n_samples-1]*beta[:,n_samples-1]).reshape((-1,1))
        gamma = np.hstack((gamma,prod/np.sum(prod)))

        # M
        newpi = gamma[:,0]
        newA = np.sum(xi,2) / np.sum(gamma[:,:-1],axis=1).reshape((-1,1))
        newB = np.copy(B)
        num_levels = B.shape[1]
        sumgamma = np.sum(gamma,axis=1)
        for lev in range(num_levels):
            mask = obs_seq == lev
            newB[:,lev] = np.sum(gamma[:,mask],axis=1) / sumgamma
        # 检查是否满足阈值
        if np.max(abs(pi - newpi)) < criterion and \
                        np.max(abs(A - newA)) < criterion and \
                        np.max(abs(B - newB)) < criterion:
            done = 1
        A[:], B[:], pi[:] = newA, newB, newpi
    return newA, newB, newpi

A = np.array([[0.5, 0.5],[0.5, 0.5]])
B = np.array([[0.3, 0.3, 0.3],[0.3, 0.3, 0.3]])
pi = np.array([0.6, 0.4])

observations_data, states_data = simulate(100,A,B,pi)
newA, newB, newpi = baum_weich(observations_data, A, B, pi)
print("newA: ", newA)
print("newB: ", newB)
print("newpi: ", newpi)
```

### 3.4 预测 维特比算法Viterbi algorithm

本质上是一个用动态规划求解最优路径的问题，很简单～只是和时刻表有点相似,这里不在详细的介绍。参考连接

https://applenob.github.io/machine_learning/HMM/

```
def viterbi(obs_seq, A, B, pi):
    """
    Returns
    -------
    V : numpy.ndarray
        V [s][t] = Maximum probability of an observation sequence ending
                   at time 't' with final state 's'
    prev : numpy.ndarray
        Contains a pointer to the previous state at t-1 that maximizes
        V[state][t]

    V对应δ，prev对应ψ
    """
    N = A.shape[0]
    T = len(obs_seq)
    prev = np.zeros((T - 1, N), dtype=int)

    # DP matrix containing max likelihood of state at a given time
    V = np.zeros((N, T))
    V[:,0] = pi * B[:,obs_seq[0]]

    for t in range(1, T):
        for n in range(N):
            seq_probs = V[:,t-1] * A[:,n] * B[n, obs_seq[t]]
            prev[t-1,n] = np.argmax(seq_probs)
            V[n,t] = np.max(seq_probs)

    return V, prev

def build_viterbi_path(prev, last_state):
    """Returns a state path ending in last_state in reverse order.
    最优路径回溯
    """
    T = len(prev)
    yield(last_state)
    for i in range(T-1, -1, -1):
        yield(prev[i, last_state])
        last_state = prev[i, last_state]

def observation_prob(obs_seq):
    """ P( entire observation sequence | A, B, pi ) """
    return np.sum(forward(obs_seq)[:,-1])

def state_path(obs_seq, A, B, pi):
    """
    Returns
    -------
    V[last_state, -1] : float
        Probability of the optimal state path
    path : list(int)
        Optimal state path for the observation sequence
    """
    V, prev = viterbi(obs_seq, A, B, pi)
    # Build state path with greatest probability
    last_state = np.argmax(V[:,-1])
    path = list(build_viterbi_path(prev, last_state))

    return V[last_state,-1], reversed(path)
```

### 0x04 条件随机场CRF

在之前的分析中HMM中的马尔可夫假设非常强，但是很多时候标准存在序列相关的情况，因此常见的是需要参考前面多个时刻表的隐藏状态作为后续的分析，因此这种马尔可夫随机场的方式给出一种新的求解方。这里看理论过于深奥了，参考Torch官方文档中给出的BiLSTM-CRF的例子，可能会比较好理解一点

```
import torch
import torch.autograd as autograd
import torch.nn as nn 
import torch.optim as optim
torch.manual_seed(2023)

# Define the helper functions to make the code more readable
def argmax(vector):
    '''
    return the argmax index 
    '''
    _, idx = torch.max(vector,1)
    return idx.item()

def prepare_sequence(seq,to_ix):
    idxs = [to_ix[w] for w in seq]
    return torch.tensor(idxs,dtype=torch.long)

def log_sum_exp(vector):
    '''
    return the log sum exp in a numerically stable way fot the forward algorithm 
    '''
    max_score = vector[0,argmax(vector)]
    max_score_broadcast = max_score.view(1,-1).expand(1,vector.size()[1])
    return max_score+torch.log(torch.sum(torch.exp(vector-max_score_broadcast)))

class BiLSTM_CRF(nn.Module):

    def __init__(self, vocab_size, tag_to_ix, embedding_dim, hidden_dim):
        super(BiLSTM_CRF, self).__init__()
        self.embedding_dim = embedding_dim
        self.hidden_dim = hidden_dim
        self.vocab_size = vocab_size
        self.tag_to_ix = tag_to_ix
        self.tagset_size = len(tag_to_ix)

        self.word_embeds = nn.Embedding(vocab_size, embedding_dim)
        self.lstm = nn.LSTM(embedding_dim, hidden_dim // 2,
                            num_layers=1, bidirectional=True)

        # Maps the output of the LSTM into tag space.
        self.hidden2tag = nn.Linear(hidden_dim, self.tagset_size)

        # Matrix of transition parameters.  Entry i,j is the score of
        # transitioning *to* i *from* j.
        self.transitions = nn.Parameter(
            torch.randn(self.tagset_size, self.tagset_size))

        # These two statements enforce the constraint that we never transfer
        # to the start tag and we never transfer from the stop tag
        self.transitions.data[tag_to_ix[START_TAG], :] = -10000
        self.transitions.data[:, tag_to_ix[STOP_TAG]] = -10000

        self.hidden = self.init_hidden()

    def init_hidden(self):
        return (torch.randn(2, 1, self.hidden_dim // 2),
                torch.randn(2, 1, self.hidden_dim // 2))

    def _forward_alg(self, feats):
        # Do the forward algorithm to compute the partition function
        init_alphas = torch.full((1, self.tagset_size), -10000.)
        # START_TAG has all of the score.
        init_alphas[0][self.tag_to_ix[START_TAG]] = 0.

        # Wrap in a variable so that we will get automatic backprop
        forward_var = init_alphas

        # Iterate through the sentence
        for feat in feats:
            alphas_t = []  # The forward tensors at this timestep
            for next_tag in range(self.tagset_size):
                # broadcast the emission score: it is the same regardless of
                # the previous tag
                emit_score = feat[next_tag].view(
                    1, -1).expand(1, self.tagset_size)
                # the ith entry of trans_score is the score of transitioning to
                # next_tag from i
                trans_score = self.transitions[next_tag].view(1, -1)
                # The ith entry of next_tag_var is the value for the
                # edge (i -> next_tag) before we do log-sum-exp
                next_tag_var = forward_var + trans_score + emit_score
                # The forward variable for this tag is log-sum-exp of all the
                # scores.
                alphas_t.append(log_sum_exp(next_tag_var).view(1))
            forward_var = torch.cat(alphas_t).view(1, -1)
        terminal_var = forward_var + self.transitions[self.tag_to_ix[STOP_TAG]]
        alpha = log_sum_exp(terminal_var)
        return alpha

    def _get_lstm_features(self, sentence):
        self.hidden = self.init_hidden()
        embeds = self.word_embeds(sentence).view(len(sentence), 1, -1)
        lstm_out, self.hidden = self.lstm(embeds, self.hidden)
        lstm_out = lstm_out.view(len(sentence), self.hidden_dim)
        lstm_feats = self.hidden2tag(lstm_out)
        return lstm_feats

    def _score_sentence(self, feats, tags):
        # Gives the score of a provided tag sequence
        score = torch.zeros(1)
        tags = torch.cat([torch.tensor([self.tag_to_ix[START_TAG]], dtype=torch.long), tags])
        for i, feat in enumerate(feats):
            score = score + \
                self.transitions[tags[i + 1], tags[i]] + feat[tags[i + 1]]
        score = score + self.transitions[self.tag_to_ix[STOP_TAG], tags[-1]]
        return score

    def _viterbi_decode(self, feats):
        backpointers = []

        # Initialize the viterbi variables in log space
        init_vvars = torch.full((1, self.tagset_size), -10000.)
        init_vvars[0][self.tag_to_ix[START_TAG]] = 0

        # forward_var at step i holds the viterbi variables for step i-1
        forward_var = init_vvars
        for feat in feats:
            bptrs_t = []  # holds the backpointers for this step
            viterbivars_t = []  # holds the viterbi variables for this step

            for next_tag in range(self.tagset_size):
                # next_tag_var[i] holds the viterbi variable for tag i at the
                # previous step, plus the score of transitioning
                # from tag i to next_tag.
                # We don't include the emission scores here because the max
                # does not depend on them (we add them in below)
                next_tag_var = forward_var + self.transitions[next_tag]
                best_tag_id = argmax(next_tag_var)
                bptrs_t.append(best_tag_id)
                viterbivars_t.append(next_tag_var[0][best_tag_id].view(1))
            # Now add in the emission scores, and assign forward_var to the set
            # of viterbi variables we just computed
            forward_var = (torch.cat(viterbivars_t) + feat).view(1, -1)
            backpointers.append(bptrs_t)

        # Transition to STOP_TAG
        terminal_var = forward_var + self.transitions[self.tag_to_ix[STOP_TAG]]
        best_tag_id = argmax(terminal_var)
        path_score = terminal_var[0][best_tag_id]

        # Follow the back pointers to decode the best path.
        best_path = [best_tag_id]
        for bptrs_t in reversed(backpointers):
            best_tag_id = bptrs_t[best_tag_id]
            best_path.append(best_tag_id)
        # Pop off the start tag (we dont want to return that to the caller)
        start = best_path.pop()
        assert start == self.tag_to_ix[START_TAG]  # Sanity check
        best_path.reverse()
        return path_score, best_path

    def neg_log_likelihood(self, sentence, tags):
        # Input the sentence with the dirsed tags
        feats = self._get_lstm_features(sentence)
        # Cancaluate the feature from the lsmt layers
        print('FEAT',feats)
        forward_score = self._forward_alg(feats)
        # Calculante the forward scoress, based on the feats
        print('FORWARD_SCORE',forward_score)
        gold_score = self._score_sentence(feats, tags)
        # calculate the score from the sentence
        return forward_score - gold_score

    def forward(self, sentence):  # dont confuse this with _forward_alg above.
        # Get the emission scores from the BiLSTM
        lstm_feats = self._get_lstm_features(sentence)

        # Find the best path, given the features.
        score, tag_seq = self._viterbi_decode(lstm_feats)
        return score, tag_seq

# Training process
START_TAG = '<START>'
STOP_TAG = '<STOP>'
EMBEDDING_DIM = 5
HIDDEN_DEM = 4

# Makding up the training data
training_data = [
    ( "the wall street journal reported today that apple corporation made money".split(),
    "B I I I O O O B I O O".split()),
    ( "georgia tech is a university in georgia".split(),
    "B I O O O O B".split() )
]
word_to_ix = {}
for sentence,tags in training_data:
    for word in sentence:
        if word not in word_to_ix:
            word_to_ix[word] = len(word_to_ix)

tag_to_ix = {"B": 0, "I": 1, "O": 2, START_TAG: 3, STOP_TAG: 4}
model = BiLSTM_CRF(len(word_to_ix),tag_to_ix,EMBEDDING_DIM,HIDDEN_DEM)
optimizer = optim.SGD(model.parameters(),lr=0.01,weight_decay=1e-4)

with torch.no_grad():
    precheck_sent = prepare_sequence(training_data[0][0],word_to_ix)
    precheck_tags = torch.tensor([tag_to_ix[t] for t in training_data[0][1]],dtype=torch.long)

    print(model.neg_log_likelihood(precheck_sent,precheck_tags))

# Make sure prepare_sequence from earlier in the LSTM section is loaded
for epoch in range(300):  # again, normally you would NOT do 300 epochs, it is toy data
    for sentence, tags in training_data:
        # Step 1. Remember that Pytorch accumulates gradients.
        # We need to clear them out before each instance
        model.zero_grad()

        # Step 2. Get our inputs ready for the network, that is,
        # turn them into Tensors of word indices.
        sentence_in = prepare_sequence(sentence, word_to_ix)
        targets = torch.tensor([tag_to_ix[t] for t in tags], dtype=torch.long)

        # Step 3. Run our forward pass.
        loss = model.neg_log_likelihood(sentence_in, targets)

        # Step 4. Compute the loss, gradients, and update the parameters by
        # calling optimizer.step()
        loss.backward()
        optimizer.step()

# Check predictions after training
with torch.no_grad():
    precheck_sent = prepare_sequence(training_data[0][0], word_to_ix)
    print(model(precheck_sent))
```

### 0x05 总结

还是没有看懂EM算法，还在理论推导和代码实现之前做权衡
