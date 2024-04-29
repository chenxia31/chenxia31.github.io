---
title: GIT 管理的常见操作总结
tags:
  - 版本控制系统
  - GIT
  - 阶段总结
categories:
  - 计算机科学
abbrlink: 964edeeb
date: 2024-04-29 17:14:18
---

从[Git - Branches in a Nutshell](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)中看Git的一些常见操作，主要是设计理念和本地的操作。更复杂的远程协作和项目管理后续给出。

Git PRO

### What is git

分布式版本控制系统（具有很多优点巴拉巴拉）

几个显著的技术上的特点：

1. snapshot，not differences，no delta- based system
2. Git thinks of tis data more like a series of a snapshots of a miniature filesystem
3. every time you commit, or save the state of your project, Git basically takes a picture of what all your files look like at that moment and stores a reference to that snapshot. If the data is not changed , Git doesn’t store the file again.
4. Nearly every operation is local
5. you have the entire history of the project right there on your local disk, most operations seem almost instantaneous
6. commit to local copy util you get to a network connection to upload
7. Git has integrity
8. you will see these hash values all over the place in Git because it uses them so much. In fact, Git stores everything in its database not by file name but 40 cahrs
9. Git generally only adds data
10. When you do actions in Git, nearly all over the place in Git because it uses them so much.  It is hard to get the system to do anything that is not undoable or to make it erase in any way
11. three state
12. modified
13. staged
14. committed

基本的工作流程可以看作：

1. 修改working directory的文件
2. 当时觉得做的够好之后，add changed to the staging area
3. 将所有暂存区的文件提交，stores that snapshot permanently to your Git directory

### Getting start —- First time git setup

```
# git config 存储的三个地方
[path]/etc/gitconfig：包含应用于系统上每个用户及其所有存储库的值，如果你讲选项 system传递给
它会从专门的文件中读取和写入

    ~/.gitconfig 或者 ～/.config/git/config 特定用户个人的库

        config 当前存储目录的呃设置
```

### Basic topic

### 3.1 获取Git仓库

一种方式是将当前不受版本控制的本地目录转换为Git存储库

第二种方式是从其他地方clone现有的Git仓库

### 3.2 记录更改

```
git status
git status -s # for short
```

工作目录中每个文件都存在 tracked or  untracked

从生命周期来看，文件可以分为untracked、unmodified、modified、staged

   Github将默认转换为main、但是git默认依旧是master  

同时可以设置gitignore来不希望Git自动添加，甚至限制您未追踪

```
# 常见的gitignore规则
1. 忽略空白行或者 #
2. 在整个工作目录中递归
3. 可以使用正斜杠 / 开始模式
4. 可以使用正斜杠 / 结束模式来指定目录
5. 可以通过感叹号 ！ 来否定模式
```

GitHub中维护了一个非常好的示例https://github.com/github/gitignore

仅仅查看更改记录不够，你希望检查更改了哪些东西

```
git diff # 查看更改但是未暂存
git diff --staged # 查看已经提交下一个阶段的内容，或者-- cached
```

提交记录

```
git commit
```

从Git中删除文件必须将其从tracked文件中删除，也就是需要从暂存区中删除

```
git rm
git rm - cached FILENAME # 将文件保存在目录中，依旧保留在硬盘。但是从暂存区中删除。
git mv # 重命名文件
```

### 3.3 查看提交的历史记录

上述操作可以完成基本的工作流。在这个基础上，创建很多次提交之后，我们可以使用现有提交历史记录的存储库，回顾一下发生了什么，我们可以使用git log

```
git log # 按照反向时间顺序列出提交的更改。同时也具有大量选项
git log -p # 显示每个提交中引入的差异
git log --stat # 缩写的统计信息
git log --pretty=oneline # 将日志输出更改为默认格式以为的格式
git log --pretty=format --graph
```

### 3.4 撤销操作

在任何staged，你都困难想撤销一些事情，通过回顾一些基本工具来保存撤销的更改

```
git commit -amend # 如果你想重做该提交，请你忘记额外的更改，暂存他们
```

加入在修改两个文件之后，不小心add * 到暂存区

```
$ git add *
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
    modified:   CONTRIBUTING.md
    renamed:    README.md -> README
```

但是只想commit其中的一个，那么需要

```
git reset HEAD [filename you not to commit]
```

注意reset是非常危险的命令，checkout也可以实现

```
git checkout --<file> # 对文件所做的任何本地更改都已经消失，替换成为最后一个分阶段或者提交的版本。
```

在之后常用git restore而不是git reset

```
git restore --staged <file> # 取消暂存
```

### 3.5 远程操作

origin是Git赋予您克隆的服务器的默认名称

```
git clone [your-git-url]
git remote # show your remote git name
git remote -v # 显示存储的URL
git remote add shortname url # git remote add pb <https://github.com/chenxia31/blog>
#  common line中可以使用short name来代替url
```

从远程仓库连接

```
git fetch <remote> # 从远程仓库获取数据
git push origin master # 推送本地的master分支
git remote show origin # 显示一些常见的信息
git remote rename [oldname] [newname]
git remote remove [url]
```

### 3.6 标签

和branch跟踪每次最新的commit 相反，tag是跟踪某一个特定commit的位置，如果不需要改动而仅查看的时候可以直接使用tag。推送到远程服务器需要显式的表达出tagname

```
git tag
git tag -l 'v1.8.5.*'

# 标签分为轻量级和注释
git tag -a v1.4 -m 'my version 1.4'

git tag v1.4

git push origin [tagname] # git push origin v1.5
git push origin --tags

# 删除标签
git tag -d v1.4 # 但是这样不会从任何远程服务器删除标签
git push origin --delete <tagname>

# 查看tag
git checkout v2.0 #这种会导致仓库处于“分离的HEAD状态“，如果进行更改、创建提交、标签将保持不变
```

### 3.7 别名 alias

创建自己snippets

### 3.8.1 （非常重要）分支模式

```
git add [filename]
git commit -m 'message'

#git的默认分支名称是master，只不过很多人懒得改
git branch testing
git log --oneline --decorate
git checkout testing # 更推荐git switch
git commit -a -m 'new commit'
git checkout master
git commit -a -m 'new commit2'
git log --oneline -decorate --graph --asll
```

后序查看一些基本分支和合并的具体操作。想象一个工作流程，在网站上做一些工作，为一个新的故事创建一个分支，之后在另外分支做一些工作。因此可以参考一下的过程

```
git checkout -b [NewBranchName] # git branch and git checkout 缩写
# 注意在签出的分支冲突未提交更改，但是可以允许 stashing and cleaning中了解
git checkout master # 专注你的主线任务
git checkout -b 'newfeature2'

# ~~ newfeature2结束
git checkout master
git merge newfeature2
# 删除分支
git branch -d hotfix
# 处理
git checkout NewBranchName
# 再次合并
git checkout master
git merge master

# 两个合并出现冲突
git status #查看
# 手动打开文件，处理冲突
git mergetool
```

上述导致已经会创建、合并、删除一些分支，这里还有一些分支管理工具

```
git branch -V # 查看每个分支的最后一次提交
git branch --merged or --no-merged# 过滤您已经或尚未合并到您当前使用的分支中的分支
git brach --move # 本地重命名分支
git push --set-upstream origin main
```

### 3.8.2 分支工作流程

已经有branch和merge的基础知识，应该用此做些什么？这里将轻量级分支的常见的工作流程展示，判断是否需要将其纳入自己的开发周期

[Git - Branching Workflows](https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows)

常见的分类包括

- master 分支中完全稳定的代码
- develop或next 工作中用于测试稳定性，不一定总是稳定的。当它稳定之后可以合并到master
- proposed 协议更新
- topic 短生命周期的分支，用于处理一些简单的feature add

### 3.8.3 远程分支

- [ ]  待定

[Git - Remote Branches](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches)

### 3.8.4 分支管理

### 3.9 Git 服务器

### 3.10 分布式工作流
