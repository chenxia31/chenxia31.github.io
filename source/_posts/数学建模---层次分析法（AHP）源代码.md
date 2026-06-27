---
title: 数学建模---层次分析法（AHP）源代码
tags:
  - AHP
  - 代码
categories:
  - 算法基础
abbrlink: cb438c42
date: 2022-04-29 15:58:08
---

预测与决策分析分为：

- 时间序列预测方法、灰色预测方法
- 随机性决策分析方法、多目标决策

而层次分析法（AHP）属于多目标决策中的一种具体方法。

> 日常生活中经常会遇到多种方案的选择，假如我要购买一款手机，市面可以选择的N款手机，而手机的参数共有M个，这就需要综合这N种手机的M中参数优劣来选择，同时这些参数之间的比较无法简单定量地来表达，这个时候就需要将半定性、半定量问题转化呈定量计算问题

算法步骤（略）：

1. 建立系统的层次结构模型，目标层、准则层、方案层
2. 构建成对比较判断矩阵
3. 计算相对权重向量，并进行一致性检验
4. 层次总排序，并进行层次总排序的一致性检验

```
function[bw,CI_RI,W,CI_RI_B,t]=XCLAHP(A,a1,a2,a3,a4,a5)
%针对五个准则矩阵的层次分析法,A输入判断矩阵，a1~a5输入准则层矩阵
RI=[0 0 0.58 0.96 1.12 1.24 1.32 1.41 1.45];
%计算准则层的lamada和一致性检验
sum_A=sum(A,2);
size_A=size(A,1);
bw=zeros(1,size_A);
for i=1:size_A
    bw(i)=sum_A(i)./sum(sum_A);
end
bw=bw';
lamada_A=sum((A*bw)./bw)/size_A
CI_RI=(lamada_A-size_A)/(size_A-1)/RI(size_A);
if CI_RI<0.1
   fprintf('一致性检验通过');
else 
    fprintf('error');
end
%计算方案层的lamada向量和层次总排序一致性检验
B(:,:,1)=a1;B(:,:,2)=a2;B(:,:,3)=a3;B(:,:,4)=a4;B(:,:,5)=a5;
%输入n=5的准则矩阵
W=zeros(3,size_A);                   %构建l=3方案的n=5准则的归一化矩阵
lamada_B=zeros(1,size_A);            %构建判断矩阵的lamada向量
CI_B=zeros(1,size_A);                %构建CI矩阵
for i=1:5
    sum_B=sum(B(:,:,i),2);
    size_B=size(B(:,:,i),1);
 for j=1:size_B
   W(j,i)=sum_B(j)./sum(sum_B);      %归一化
 end
lamada_B(i)=sum((B(:,:,i)*W(:,i))./W(:,i))/3;
CI_B(i)=(lamada_B(i)-size_B)/(size_B-1)/RI(size_B);
end
CI_RI_B=CI_B*bw;
if CI_RI_B<0.1
   fprintf('一致性检验通过');
else 
    fprintf('error');
end
t=W*bw;
    
```

这个输入的矩阵数目必须是固定五个，不知道咋实现可以根据输入的矩阵数自动调节
