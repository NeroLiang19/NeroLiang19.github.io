---
layout: post
title: Git meger And Rebase
category: 技术
tags: [Git]
keywords: Git meger Rebase
---

# GIT使用 rebase 和 merge 的正确使用方法

### 一、背景
  使用GIT这么久了从来没有深层次的研究过，一般情况下，只要会用pull,commit,push等几个基本提交命令就可以了，
公司的项目分支管理这部分操作一直都是我负责，对于分支的合并我一直都使用merge操作，
也知道还有一个rebase，但是一直不会用，百度了很多，
说的基本都差不多，按照步骤在公司项目里操作，简直就是噩梦，只要rebase就出现噩梦般的冲突，
所以一直不敢用，今天自己捣腾了一番终于领略到一些，不多说直接进入干货。

### 二、使用rebase，merge和只使用merge的对比图

使用 rebase

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture1.jpg)


使用 merge

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture2.jpg)

### 三、使用 rebase 和 merge 的基本原则：

1.下游分支更新上游分支内容的时候使用 rebase

2.上游分支合并下游分支内容的时候使用 merge

3.更新当前分支的内容时一定要使用 --rebase 参数

例如现有上游分支 master，基于 master 分支拉出来一个开发分支 dev，
在 dev 上开发了一段时间后要把 master 分支提交的新内容更新到 dev 分支，此时切换到 dev 分支，使用 git rebase master，
等 dev 分支开发完成了之后，要合并到上游分支 master 上的时候，切换到 master 分支，使用 git merge dev

### 四、例子说明

#### 1.创建两个GIT项目，project1和 project2,同时分别加入三个文件并提交master分支

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture3.jpg)

从代码提交时间轴图看两个项目现在的状态都一致

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture4.jpg)

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture5.jpg)


#### 2.分别给两个项目创建三个分支 B1，B2，B3 (* 对应数字)

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture6.jpg)

从 git log 中我们看到 B1，B2，B3 都基于master最新提交点拉出来的三个新分支，如下图从时间轴也可以看出

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture7.jpg)

#### 3.分别切换分支到 B1，B2，B3，并修改对应的文件 file1，file2，file3，最后切换到 mastet 分支添加一个 README.md 文件：

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture8.jpg)

从上图的结果可以看出，B1, B2, B3, master 四个分支分别在不同的时间点做了代码提交，
那么最后一次 master 上做的修改在 B1, B2, B3 三个分支上肯定没有

#### 4.此时在这三个分支上开发的同学发现他们要做的功能要在 master 最新的基础上开发，或者他们也想要 master 上最新的内容:

重点来了，现在我们怎么办？方法有两种，一种是使用 rebase ，另一种是使用 merge，
我们分别在 project1 和 project2 两个项目上使用这两种方式解决这个问题，
在项目 project1 使用 rebase

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture9.jpg)

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture10.jpg)

在上面的过程中，更新代码我使用的是 git pull origin B1 --rebase 而不是 git pull origin B1 这也是平时使用 rebase 注意的一点，
git pull 这条命令默认使用了 --merge 的方式更新代码，如果你不指定用 --rebase，
有的时候就会发现日志里有这样的一次提交 Merge branch 'dev' of gitlab.xpaas.lenovo.com:liuyy23/lenovo-mbg into dev 什么？
自己分支合并到了自己分支，显然这个没有什么必要，而且在时间轴上也不好看，平白无故多了一条线出来，对于强迫症的我来说看着就难受...

还有就是使用 rebase 之后，如果直接使用 git push origin B1 发现是不好使的，提示也说明了提交失败的原因，
我个人是这么理解的，使用 rebase 之后，master分支上比B1分支上多的修改，
直接“插入”到了B1分支修改的内容之后，也就是 master 分支的修改在 B1 分支上重演了一遍，相对远程 B1 分支而言，
本地仓库的B1分支的“基底”已经变化了，直接 push 是不行的，所以确保没有问题的情况下必须使用 --force 参数才能提交，
这也就是官方对 rebase 作为 “变基” 的解释（个人观点）。

接下来我们接着在 project2 项目上使用 merge 操作

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture11.jpg)

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture12.jpg)

可以看到 merge 之后，在 B1 分支上多出一条合并的log

此时，我们的 B1 分支开发完成了，要合并到 master 分支，根据基本原则，
在 master 分支上都使用 git merge B1 就可以合并，看下图结果：

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture13.jpg)

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture14.jpg)

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture15.jpg)

接下来对 B2，B3 分别在 project1 和 project2 上做相同的操作，我们看结果：

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture16.jpg)

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Git-meger-and-rebase/picture17.jpg)

使用 rebase 就感觉所有人都在同一条直线上开发一样，很干净的log，看着很舒服，而一直使用 merge 的log看起来就很乱，
我这只是4个分支的例子，我们项目每周基本都是十几个分支，真的是看起来乱入一团哇...

这个例子中的操作都没有出现不同分支修改同一个文件导致冲突的情况，实际开发中这种情况非常多，
rebase 的时候出现冲突后 git 也会列出来哪些文件冲突了，等你解决冲突之后使用 git rebase --continue 就会继续处理，
所以为了避免这种冲突太多，而且不好解决，我们项目中基本都是一个需求就一个分支，
而且开发过程中要随时更新上游分支的内容下来，确保在最新的代码基础上开发，
这也是 GIT 推荐的方式，尽可能多建分支开发，而不是在一个开发分支上很多人操作。

如果是这种情况建议不要用 rebase，另外负责分支合并的人在合并下游分支代码的时候要确保你这个上游分支不要在这段时间内提交代码，
有一个方法就是把上游分支设置 protect

### 五、注意事项

1.更新当前分支代码的时候一定要使用 git pull origin xxx --rebase

2.合并代码的时候按照最新分支优先合并为原则

3.要经常从上游分支更新代码，如果长时间不更新上游分支代码容易出现大量冲突
  
