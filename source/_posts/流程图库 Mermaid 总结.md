---
title: 流程图库 Mermaid 总结
tags:
  - JS
  - 流程图库
  - 绘图
categories:
  - 计算机科学
abbrlink: 354c5ef8
date: 2024-04-29 17:14:19
---

关于流程图的绘制中有很多工具，比如微软的visio 或者 PowerPoint、贝尔的Graphviz、Lucidchart、OmniGraffle、Draw.io、processOn、Latex的tikZ；但是这种基于图形界面的操作往往会受限于人本身的不精确导致不好看。所以希望借助代码生成流程图来尽可能保持协调一致的美观。

### 0x01 使用教程

可以直接在Typora中使用，只需要输入即可

```
``` mermaid
```

什么是[Mermaid](https://mermaid.js.org/intro/)，按照官网的定义是**基于JavaScript**的图表工具，可以渲染受markdown启发的文本定义，用于动态创建和修改图表。其基本的语法结构包括：

```
# Diagrams definitions begin with a declaration of the diagram type
    # Define the blocks
    # Link the blocks
erDiagram
          CUSTOMER }|..|{ DELIVERY-ADDRESS : has
          CUSTOMER ||--o{ ORDER : places
          CUSTOMER ||--o{ INVOICE : "liable for"
          DELIVERY-ADDRESS ||--o{ ORDER : receives
          INVOICE ||--|{ ORDER : covers
          ORDER ||--|{ ORDER-ITEM : includes
          PRODUCT-CATEGORY ||--|{ PRODUCT : contains
          PRODUCT ||--o{ ORDER-ITEM : "ordered in"
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-7730b9eb5ccc51debe6687378fe9fc80_1440w.png)





添加图片注释，不超过 140 字（可选）

常见的表格可以分为：流程图Flowchart、顺序图Sequence Diagram、类图Class Diagram、状态图State Diagram、实体关系图Entity Relationship Diagram、甘特图Gantt、饼图Pie chart、需求图Requirement Diagram、GitGraph、C4C图、思维导图Mindmaps、时间线Timelines

### 0x02 流程图 Flowchart

流程图由节点 (Nodes)、形状 (Edges)、箭头 （Arrows）和线条（Lines）组成，应用示例

```
flowchart 
    id1(This is the text in the node1)
    id2[This is the text in the node2]
    db1[(database)]
    id1 --> id2
    db1 --> id1
%% TB : top to bottom,TD
%% BT: bottom to top
%% RL: right to left
%% LR: left to right

flowchart LR
  subgraph BT[Define of the nodes]
      id1(圆角)
      id2([更大的圆角])
      id3[长方形]
      id4[(数据库)]
      id5((圆形))
      id6>what]
      id7{方形}
      id8{{角}}
      id10[\梯形 Trapezoid alt/]
    end

    subgraph LR[define of the linkes]
       id11[node1]
       id12[node2]
       id11 --> id12
       id11 --- id12
       id11 -- Text ---id12
       id11 ---|Text|id12
       id11-->|Text|id12
       id11 -.-> id12
       id11 -. text .-> id12
       A == text ==> id12
       a -->b & c-->d
    end

    subgraph LR
        B[start]
        C{Is it?}
        B -->|yes|C
        C-->D[rethink]
        D-->B
        B-->|No|E[End]
        E[perhaps?]


    end
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-f56c727e422977fa5217e6d76bd49aed_1440w.png)





添加图片注释，不超过 140 字（可选）

### 0x03 时序图 Sequence diagrams

时序图表示用户之间相互交流的过程的图表，其中的语法主要包括：参与者（Participant）、演员（Actor）、别名（Alias）、分组（Group and box）、信息（Arrows and lines）、激活（Activate）、备注（Notes）、循环（Loop）

```
sequenceDiagram 
    participant A as Alice
    participant B as Bob
    participant J as John
    A->>B:hello John
    B->>J:Great!
[Actor][Arrow][Actor]:Message Text
-> 实线
--> 没有箭头的点画线
->> 箭头线
-->> 箭头点画线
-X corss
--X 点线带cross
-）
--）
sequenceDiagram
    Alice->John: Hello John, how are you?
    activate John
    loop Every minute
        John-->Alice: Great!
    end
    deactivate John
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-bfe3a5640cc2927500e07003c8f5c244_1440w.png)





添加图片注释，不超过 140 字（可选）

初次之外顺序图还需要表示一些基本的逻辑概念，比如激活（Activate）、循环（Loop）、替代（Alt）、平行（Par）、关键区域（Critical）、Break、高亮显示（Rect）

```
sequenceDiagram
    participant Alice
    participant John

    rect rgb(191, 223, 255)
    note right of Alice: Alice calls John.
    Alice->>+John: Hello John, how are you?
    rect rgb(200, 150, 255)
    Alice->>+John: John, can you hear me?
    John-->>-Alice: Hi Alice, I can hear you!
    end
    John-->>-Alice: I feel great!
    end
    Alice ->>+ John: Did you want to go to the game tonight?
    John -->>- Alice: Yeah! See you there.
sequenceDiagram
    participant C as Client
    participant S as Server
    autonumber
    note left of C: 选择初始seq number=x<br/>发送客户端的TCP SYN段
    C->>S:SYNbit=1,Seq=X
    note right of S:选择初始seq number=y<br/>发送服务端TCP SYN信息<br/>利用SYN+1作为回复
    S->>C:SYNbit=1,Seq=Y<br/>ACKbit=1,ACKnum=x+1
    note left of C:收到SYNACK(x),确认服务器存在<br/>发送ACK回复SYNACK<br/>不包含client-to-server数据
    C->>S:ACKbit=1,ACKnum=y+1
    note right of S:收到 ACK(y)<br/>确认用户存在
```

https://zhuanlan.zhihu.com/p/53374516

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-60dbdeb2a314a6bba52ffa4aede9fd77_1440w.png)





添加图片注释，不超过 140 字（可选）

### 0x04 GitGraph

```
gitGraph
    commit id: "Set up folder"
    branch release
    branch development
    checkout development
    commit
    branch feature01
    commit id:"feature1 add"
    commit id:"feature1 add2"
    checkout development
    branch feature02
    commit id:"fixed feature2"
    checkout development
    merge feature02
    commit id:"feature1 add3"
    checkout development
    merge feature01
    commit
    checkout main
    merge development tag:"Beta1.0"
    checkout release
    merge main tag:"V1.0"
```

![img](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/img/v2-4852e348f84afc85fd575632266e3d10_1440w.png)





添加图片注释，不超过 140 字（可选）
