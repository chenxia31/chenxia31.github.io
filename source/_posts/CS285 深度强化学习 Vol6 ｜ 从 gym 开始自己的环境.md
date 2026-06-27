---
title: CS285 深度强化学习 Vol6 ｜ 从 gym 开始自己的环境
tags:
  - 强化学习
  - gym
categories:
  - 算法设计
abbrlink: '1e270005'
date: 2024-04-29 19:08:59
---

openAI的gym中提供了很多封装好的环境，在此基础上我们可以使用其来跑通深度强化学习的代码，但是更多的时候我们希望调用算法来解决一个实际问题，因此尝试为定制化的问题转换成为MDP六元组《变量、状态、动作、奖励、状态转移、终止条件》后编程为可以交互的环境即可。本文介绍学习gymnasium和stable- baseline3的学习思路并手动实现一个MyCar的环境。

### 0x01 巨人的肩膀 ：调库

根据MDP过程，环境和智能体两个抽象类主要需要包括几个API操作：

1. 环境：参数设置（init），初始化环境（reset），状态更新（step），关闭（closed），显示（render）
2. 智能体：深度学习参数（net），学习行为（learn），生成行为（predict）

所以抽象来看

```
import gymnasium as gym
import stable_baselines3 as sb3
env = gym.make('[env_name]',render_mode='human')
# 定义好参数，已经学习
agent = sb3.load('[saved_model_dir]') 

observation,info = env.reset(seed=42)
terminated = false
while not terminated:
    action = agent.predict(observation)
    observation,reward,terminated,truncated,info = env.step(action)
    if truncated:
            env.reset()
env.closed
```

### 1.1 环境库 gymnasium.env

目前主流的强化学习环境主要是基于[openai-gym](https://www.gymlibrary.dev/index.html)，主要介绍为

>  Gym is an open source Python library for developing and comparing reinforcement learning algorithms by providing a standard API to communicate between learning algorithms and environments, as well as a standard set of environments compliant with that API. Since its release, Gym's API has become the field standard for doing this. Gym是一个开源的Python库，通过提供标准API在学习算法和环境之间进行通信，以及一组符合该API的标准环境，来开发和比较强化学习算法。自发布以来，Gym的API已成为这样做的现场标准。

但是代码安装有点太屎山了，现有另外的一个fork版本[gymnasium](https://gymnasium.farama.org/)更加的简单和包容

> **Gymnasium is a maintained fork of OpenAI’s Gym library.** The Gymnasium interface is simple, pythonic, and capable of representing general RL problems, and has a [compatibility wrapper](https://gymnasium.farama.org/content/gym_compatibility/) for old Gym environments Gymasium是OpenAI gym library的一个维护分支。Gymnasium界面简单，pythonic，能够表示一般的RL问题，并具有旧gym环境的兼容性warp器

```
pip install gymnasium
```

### 1.2 强化学习算法库 stable_baselines3

[Stable_baseline3](https://stable-baselines3.readthedocs.io/en/master/)是基于OpenAI baselines改进的实现，类似gymnasium和gym的关系，主要实现的修改为：

1. 统一算法结构
2. 实现PEP8兼容
3. 文档化函数和类
4. 更多的测试和代码覆盖

```
pip install stable_baseline3
```

另外在stable_baseline3的基础上包括预训练好的智能体平台[RL Baseline zoo](https://stable-baselines.readthedocs.io/en/master/guide/rl_zoo.html)，同时也提供训练、评估智能体行为、微调超参数和录屏的功能，具体的使用可以参考官方文档。

```
apt-get install swig cmake libopenmpi-dev zlib1g-dev ffmpeg
pip install stable-baselines box2d box2d-kengz pyyaml pybullet optuna pytablewriter
```

PS: 如果不习惯用conda管理环境，或者有迁移环境的需求可以参考使用docker创建镜像

另外还有一些其他优秀的RL库，比如[蘑菇书-joyrl](https://github.com/datawhalechina/joyrl)、[Tensorforce](https://github.com/tensorforce/tensorforce)

### 0x02 优秀环境欣赏

在gymnasium的[官网环境](https://gymnasium.farama.org/environments/classic_control/)中给出一些典型的环境，可以分类为：

1. **经典控制（Classic control）**，比如杂技演员（Acrobat）、单臂摆（Cart pole）、小车上山（Mountain car）、钟摆（Pendulum）
2. **二维环境（Box2D）**，双足行走（Bipedal walker）、赛车（Car racing）、登月（Lunar lander）
3. **文本游戏（Toy Text）**，二十一点（Blackjack）、悬崖寻路（Cliff walking）、冰湖（Frozen lake）、出租车（Taxi）
4. **多关节接触动力学（Multi Joint Dynamics with Contact，MoJoCo）**
5. **雅达利（Atari）**，是的就是被任天堂打败的“雅达利大崩溃”的雅达利
6. [**第三方环境**](https://gymnasium.farama.org/environments/third_party_environments/)，[flappy-bird-env](https://github.com/robertoschiavone/flappy-bird-env)，[highway-env](https://github.com/eleurent/highway-env)，[sumo-rl](https://github.com/LucasAlegre/sumo-rl)，等等

### 0x03 gymnasium.env 详细介绍

关于基类的介绍，在[gymnasium.env](https://gymnasium.farama.org/api/env/)中很清楚，但是一堆英文可能看着有点累，这里主要介绍作为一个抽象类它的外部接口和基本常见属性：

方法：

1. Step() ，根据agent的action更新state，同时返回五元组(更新状态obs，奖励信号reward，是否结束terminated，是否中断truncated，信息info)，注意这里对gym.env中的done更加细致
2. reset()，重置环境到初始状态
3. Render() 图形引擎，用于可视化过程，不要也可以
4. close（） 关闭环境

属性

1. actions_space 定义动作环境
2. observation_space 定义状态环境
3. Reward_range 奖励范围
4. spec 
5. metadata
6. np.random

Stable_baseline3也提供一些[教程给出自定义类的属性](https://stable-baselines3.readthedocs.io/en/master/guide/custom_env.html)并且提供了一个[colab-GoLeftEnv](https://colab.research.google.com/github/araffin/rl-tutorial-jnrr19/blob/sb3/5_custom_gym_env.ipynb)

```
import gymnasium as gym
import numpy as np
from gymnasium import spaces


class CustomEnv(gym.Env):
    """Custom Environment that follows gym interface."""

    metadata = {"render_modes": ["human"], "render_fps": 30}

    def __init__(self, arg1, arg2, ...):
        super().__init__()
        # Define action and observation space
        # They must be gym.spaces objects
        # Example when using discrete actions:
        self.action_space = spaces.Discrete(N_DISCRETE_ACTIONS)
        # Example for using image as input (channel-first; channel-last also works):
        self.observation_space = spaces.Box(low=0, high=255,
                                            shape=(N_CHANNELS, HEIGHT, WIDTH), 
                                            dtype=np.uint8)

    def step(self, action):
        ...
        return observation, reward, terminated, truncated, info

    def reset(self, seed=None, options=None):
        ...
        return observation, info

    def render(self):
        ...

    def close(self):
        ...
```

### 0x04 从零开始的MyCar

假设我们现在希望训练一个智能体，可以在出现下列的网格中出现时都会向原点前进，在定义的环境时可以使用gymnaisum.env定义自己的环境类MyCar，之后使用stable_baselines3中的check_env对环境的输入和输出做检查：

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-ce9776d28d3070177014e89dac7de05d_1440w.png)





MyCar env

由此分析环境中的属性：

状态空间：二维的空间和问题的size有关

动作空间：离散的五种动作，暂停和上下左右

是否结束：到达原点

是否中止：跑到环境之外

奖励：当前状态距离原点的距离

```
import gymnasium
from gymnasium import spaces
import numpy as np
# Path: modelTimetable/DRL/myEnv.ipynb
# Implementing the environment
# Reproduction of the cartpole environment
# 
# Discription: 
# Create a car in a two-dimensional plane with a width of 20, and the coordinates of 
# the center point are the destination of the car to reach.
#
# State:
# The state of the car is represented by the coordinates of the center point of the car.(x,y)
# Action:
# The action of the car is represented by the speed of the car.(vx,vy)
# Reward:
# The reward is the distance between the car and the destination.
# Termination:
# The car reaches the destination.(0,0)
# truncation:
# The car is out of the screen.

'''
gymnasium is the main class that we will use to create our environment.

The gymnasium class has the following methods:
__init__(): This method is used to initialize the environment. It takes the following parameters:

step(): This method is used to take an action and return the next state, reward, and whether the episode is over. 
Physical engine
- input: action
- output: observation, reward,terminated,truncated,info

reset(): This method is used to reset the environment to its initial state.
- input: None
- output: observation

render(): This method is used to render the environment:
Image engine
- input: mode(default='human','human','rgb_array','ansi','rgb_array_list)
- output: None
eg:gymnasium.make('CartPole-v0',render_mode='human')

close(): This method is used to close the environment.
'''

class MyCar(gymnasium.Env):
    metadata = {
        'render.modes': ['human', 'rgb_array'],
        'video.frames_per_second': 2
        }
    def __init__(self):
        self.target_x = 0
        self.target_y = 0
        self.size = 10
        self.action_space = spaces.Discrete(5) # 0:stop, 1:up, 2:down, 3:left, 4:right
        self.observation_space = spaces.Box(np.array([-self.size,-self.size]), np.array([self.size,self.size]))
        self.state = None
        self.info = {}

    def step(self, action):
        assert self.action_space.contains(action), "%r (%s) invalid"%(action, type(action))
        # update the state by the action
        x,y = self.state
        if action == 0:
            x += 0
            y += 0
        elif action == 1:
            x += 0
            y += 1
        elif action == 2:
            x += 0
            y += -1
        elif action == 3:
            x += -1
            y += 0
        elif action == 4:
            x += 1
            y += 0
        # the next state
        self.state = np.array([x,y])
        self.state = self.state.astype(np.float32)
        reward = self._get_reward()
        terminated = self._get_terminated()
        terminated = bool(terminated)
        truncated = self._get_truncated()
        truncated = bool(truncated)
        info = {}
        return self.state, reward, terminated,truncated, info

    def reset(self,seed=None):
        self.state = np.ceil(np.random.rand(2)*2*self.size)-self.size
        self.state = self.state.astype(np.float32)
        self.counts = 0
        self.info = {}
        return self.state,self.info

    def render(self, mode='human'):
        print(self.state)

    def close(self):
        return super().close()

    def _get_reward(self):
        return -np.sqrt(self.state[0]**2+self.state[1]**2)

    def _get_terminated(self):
        x,y = self.state
        return x==self.target_x and y==self.target_y

    def _get_truncated(self):
        x,y = self.state
        return x<-self.size or x>self.size or y<-self.size or y>self.size

from stable_baselines3.common.env_checker import check_env
env = MyCar()
check_env(env, warn=True)
```

测试它的输出输出

```
env = MyCar()
env.reset()
state,reward,terminated,truncated,info = env.step(env.action_space.sample())
log = 0
while not terminated:
    env.render()
    state,reward,terminated,truncated,info = env.step(env.action_space.sample())
    if truncated:
        env.reset()
    log += 1
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-dea3f22ab732cf33b0dc59889230d22c_1440w.png)





output

### 0x05 开始训练

这里只是调用stable_baselines的最简单的DQN库，没有调整参数和网络结构

```
from stable_baselines3 import DQN
from stable_baselines3.common import logger
# Train the agent by the stable_baselines3
import os
models_dir = './models/DQN'
logdir = './logs'
if not os.path.exists(models_dir):
    os.makedirs(models_dir)

if not os.path.exists(logdir):
    os.makedirs(logdir)

env = MyCar()
agent = DQN('MlpPolicy', env, verbose=1,tensorboard_log=logdir)
agent.learn(total_timesteps=100000, log_interval=100,tb_log_name='DQN')
agent.save("DQN_MyCar")
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-5cabe79b310b2dcbf3ebb053b135f096_1440w.png)





DQN训练结果

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-57b50edd23ae8580d0786a2bbf069997_1440w.png)





训练最终结果

之后可以通过保存的环境来测试结果

```
env = MyCar()
obs = env.reset()
agent = DQN.load('deepq_cartpole.zip',env=env)
terminated = False
while not terminated:
    action,_state = agent.predict(obs)
    obs,rew,terminated,truncated,info = env.step(action)
    print(env.state)
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-04ef1ce7869e37f9d1d9fc38f612a33c_1440w.png)





解决方案

并使用

```
tensorboard --logdir = logs
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-6bc3eb407754d0f10ac2eb12dae6446c_1440w.png)





参数

### 0x06 总结

最感动的是stable_baselines3提供的custom_gym_env.ipynb中最后给出的be creative！

建立环境又何尝不是一种创造。

参考链接很多，感谢互联网～
