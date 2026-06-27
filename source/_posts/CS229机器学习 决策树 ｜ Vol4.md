---
title: CS229机器学习 决策树 ｜ Vol4
tags:
  - 机器学习
  - CS229
categories:
  - 算法基础
  - 数据科学
abbrlink: fad3d23f
date: 2024-04-29 16:09:20
---

之前《机器学习》选修课被大佬手撕决策树算法给震撼了，因为之前从来没有尝试过数学和代码的角度来看待算法，不过寒假静下来，可以看出作为基模型运用到很多递归、贪心中的思想，写出来发现难度并没有那么大！

```
from math import log
import operator
import numpy as np
class decisionTree():
    def dataLoader(self,filename):
        dataSet = [];
        labels = []
        i=1
        fr = open(filename)
        for line in fr.readlines():  # 逐行读取，滤除空格等
            lineArr = line.strip().split(',')
            if i==1:
                labels=lineArr[1:(len(lineArr)-1)]
                i=i+1
            else:
                dataSet.append(lineArr[1:])
        return dataSet,labels


    def createExampleDataset(self):
        '''
        返回示例数据，包括dataset、labels
        '''
        dataSet = [['白', '高', '男'],
                   ['黑', '高', '男'],
                   ['白', '高', '男'],
                   ['黑', '低', '女'],
                   ['黑', '低', '女'],
                   ['黑', '高', '女'],
                   ['白', '低', '女'],
                   ['黑', '高', '女']]
        labels = ['颜色', '身高']  # 两个特征
        return dataSet, labels

    def calcCrossEntropy(self, dataSet):
        '''
        计算数据集合中的数据熵
        return crossEntropy
        '''
        classList = [example[-1] for example in dataSet]
        unique,counts=np.unique(classList,return_counts=True)
        crossEntropy=0
        for count in counts:
            crossEntropy+=-float(count/len(classList))*log(float(count/len(classList)),2)
        return crossEntropy

    def chooseBestFeature(self, dataSet):
        '''
        从不同特征中选择交叉熵差值最低的作为分离特征
        return bestFeature下标
        '''
        bestInfoGain=-1
        baseCrossEntropy=self.calcCrossEntropy(dataSet)
        numFeature=len(dataSet[0])-1
        for i in range(numFeature):
        # 根据不同特征分开，并重新计算根据每个特征得到的交叉熵的和
            featureList=set([example[i] for example in dataSet])
            newCrossEntropy=0
            for value in featureList:
                subDataSet=self.splitDataSet(dataSet,i,value)

                newCrossEntropy+=len(subDataSet)/len(dataSet)*self.calcCrossEntropy(subDataSet)

            infoGain=baseCrossEntropy-newCrossEntropy
            if infoGain>bestInfoGain:
                bestInfoGain=infoGain
                bestFeature=i
        return bestFeature


    def splitDataSet(self, dataSet, index,value):
        '''
        根据最佳的特征分离数据集，形成新的子集
        '''

        subDataSet=[]
        for example in dataSet:
            if example[index]==value:
                temp=example[:index]
                temp.extend(example[index+1:])
                subDataSet.append(temp)
        return subDataSet

    def ifNodeFeatureIsSame(self,dataSet):
        featureMat = [example[:(len(dataSet[0])-1)] for example in dataSet]
        uniqueFeatureMat=np.unique(featureMat)
        if len(uniqueFeatureMat) == 1:
            return True
        else:
            return False

    def majorityCount(self,classList):
        className,counts=np.unique(np.array(classList))
        return className[np.argmax(counts)]

    def createTree(self,dataSet,labels):
        # 生成结点node
        classList=[example[-1] for example in dataSet]

        # 情况1：当前分支中全部都为同一个属性不需要分类
        # 情况2：当前分支为1集合不需要分类
        # 情况3：最优化划分

        if classList.count(classList[0])==len(classList):
            return classList[0]

        if len(labels)==0 or self.ifNodeFeatureIsSame(dataSet):
            return self.majorityCount(classList)

        bestFeature=self.chooseBestFeature(dataSet)
        bestFeatureLabel=labels[bestFeature]
        myTree={bestFeatureLabel:{}}

        #删除区分的属性
        del(labels[bestFeature])

        featureValues=[example[bestFeature] for example in dataSet]
        uniqueValues=set(featureValues)
        for value in uniqueValues:
            sublabels=labels[:]
            myTree[bestFeatureLabel][value]= \
                self.createTree(self.splitDataSet(dataSet, bestFeature, value), sublabels)
        return myTree




if __name__=='__main__':
    newTree=decisionTree()
    filename='datasets/watermelon.txt'
    dataSet,labels=newTree.dataLoader(filename)
    #或者直接根据例子来
    #dataSet,labels=newTree.createExampleDataset()
    print(newTree.createTree(dataSet,labels))
```

txt文件

```
编号,色泽,根蒂,敲声,纹理,脐部,触感,好瓜
1,青绿,蜷缩,浊响,清晰,凹陷,硬滑,是
2,乌黑,蜷缩,沉闷,清晰,凹陷,硬滑,是
3,乌黑,蜷缩,浊响,清晰,凹陷,硬滑,是
4,青绿,蜷缩,沉闷,清晰,凹陷,硬滑,是
5,浅白,蜷缩,浊响,清晰,凹陷,硬滑,是
6,青绿,稍蜷,浊响,清晰,稍凹,软粘,是
7,乌黑,稍蜷,浊响,稍糊,稍凹,软粘,是
8,乌黑,稍蜷,浊响,清晰,稍凹,硬滑,是
9,乌黑,稍蜷,沉闷,稍糊,稍凹,硬滑,否
10,青绿,硬挺,清脆,清晰,平坦,软粘,否
11,浅白,硬挺,清脆,模糊,平坦,硬滑,否
12,浅白,蜷缩,浊响,模糊,平坦,软粘,否
13,青绿,稍蜷,浊响,稍糊,凹陷,硬滑,否
14,浅白,稍蜷,沉闷,稍糊,凹陷,硬滑,否
15,乌黑,稍蜷,浊响,清晰,稍凹,软粘,否
16,浅白,蜷缩,浊响,模糊,平坦,硬滑,否
17,青绿,蜷缩,沉闷,稍糊,稍凹,硬滑,否
```
