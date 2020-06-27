## 前言
掌握Git命令是每位程序员必备的基础，之前一直是用smartGit工具，直到看到大佬们都是在用Git命令操作的，回想一下，发现有些Git命令我都忘记了，于是写了这篇博文，复习一下~

> https://github.com/whx123/JavaHome

**公众号：捡田螺的小男孩**

**文章目录**
- Git是什么?
- Git的相关理论基础
- 日常开发中，Git的基本常用命令
- Git进阶之分支处理
- Git进阶之处理冲突
- Git进阶之撤销与回退
- Git进阶之标签tag
- Git其他一些经典命令

## Git是什么
在回忆Git是什么的话，我们先来复习这几个概念哈~

### 什么是版本控制？
百度百科定义是酱紫的~
> 版本控制是指对软件开发过程中各种程序代码、配置文件及说明文档等文件变更的管理，是软件配置管理的核心思想之一。

那些年，我们的毕业论文,其实就是版本变更的真实写照...脑洞一下，版本控制就是这些论文变更的管理~
![](https://user-gold-cdn.xitu.io/2020/6/20/172d07667ab32c55?w=783&h=222&f=png&s=42433)

### 什么是集中化的版本控制系统？
那么，集中化的版本控制系统又是什么呢，说白了，就是有一个集中管理的中央服务器，保存着所有文件的修改历史版本，而协同开发者通过客户端连接到这台服务器，从服务器上同步更新或上传自己的修改。

![](https://user-gold-cdn.xitu.io/2020/6/20/172d0caa0af7ca9e?w=1247&h=825&f=png&s=77377)

### 什么是分布式版本控制系统？
分布式版本控制系统，就是远程仓库同步所有版本信息到本地的每个用户。嘻嘻，这里分三点阐述吧：
- 用户在本地就可以查看所有的历史版本信息，但是偶尔要从远程更新一下，因为可能别的用户有文件修改提交到远程哦。
- 用户即使离线也可以本地提交，push推送到远程服务器才需要联网。
- 每个用户都保存了历史版本，所以只要有一个用户设备没问题，就可以恢复数据啦~

![](https://user-gold-cdn.xitu.io/2020/6/21/172d47e8db158f67?w=1453&h=764&f=png&s=95399)

### 什么是Git?
Git是免费、开源的**分布式版本控制**系统，可以有效、高速地处理从很小到非常大的项目版本管理。

![](https://user-gold-cdn.xitu.io/2020/6/26/172efd2d7e5a8b30?w=220&h=92&f=png&s=9300)

## Git的相关理论基础
- Git的四大工作区域
- Git的工作流程
- Git文件的四种状态
- 一张图解释Git的工作原理

### Git的四大工作区域
先复习Git的几个工作区域哈：
![](https://user-gold-cdn.xitu.io/2020/6/25/172ea283b8925e39?w=1314&h=1027&f=png&s=75979)

- **Workspace**：你电脑本地看到的文件和目录，在Git的版本控制下，构成了工作区。
- **Index/Stage**：暂存区，一般存放在 .git目录下，即.git/index,它又叫待提交更新区，用于临时存放你未提交的改动。比如，你执行git add，这些改动就添加到这个区域啦。
- **Repository**：本地仓库，你执行git clone 地址，就是把远程仓库克隆到本地仓库。它是一个存放在本地的版本库，其中**HEAD指向最新放入仓库的版本**。当你执行git commit，文件改动就到本地仓库来了~
- **Remote**：远程仓库，就是类似github，码云等网站所提供的仓库，可以理解为远程数据交换的仓库~

### Git的工作流程
上一小节介绍完Git的四大工作区域，这一小节呢，介绍Git的工作流程咯，把git的操作命令和几个工作区域结合起来，个人觉得更容易理解一些吧，哈哈，看图：

![](https://user-gold-cdn.xitu.io/2020/6/26/172f02fc758ed96d?w=1254&h=1010&f=png&s=113385)
 git 的正向工作流程一般就这样：
 - 从远程仓库拉取文件代码回来；
 - 在工作目录，增删改查文件；
 - 把改动的文件放入暂存区；
 - 将暂存区的文件提交本地仓库；
 - 将本地仓库的文件推送到远程仓库；

### Git文件的四种状态
根据一个文件是否已加入版本控制，可以把文件状态分为：Tracked(已跟踪)和Untracked(未跟踪)，而tracked(已跟踪)又包括三种工作状态：Unmodified，Modified，Staged

![](https://user-gold-cdn.xitu.io/2020/6/21/172d5ecd2d130275?w=1557&h=820&f=png&s=106243)
- **Untracked**: 文件还没有加入到git库，还没参与版本控制，即未跟踪状态。这时候的文件，通过git add 状态，可以变为Staged状态
- **Unmodified**：文件已经加入git库, 但是呢，还没修改, 就是说版本库中的文件快照内容与文件夹中还完全一致。 Unmodified的文件如果被修改, 就会变为Modified. 如果使用git remove移出版本库, 则成为Untracked文件。
- **Modified**：文件被修改了，就进入modified状态啦，文件这个状态通过stage命令可以进入staged状态
- **staged**：暂存状态. 执行git commit则将修改同步到库中, 这时库中的文件和本地文件又变为一致, 文件为Unmodified状态. 

### 一张图解释Git的工作原理

![](https://user-gold-cdn.xitu.io/2020/6/22/172dc85dbffd9ee9?w=1538&h=828&f=png&s=252469)

## 日常开发中，Git的基本常用命令
- git clone
- git checkout -b dev
- git add 
- git commit
- git log
- git diff
- git status
- git pull/git fetch
- git push

这个图只是模拟一下git基本命令使用的大概流程哈~
![](https://user-gold-cdn.xitu.io/2020/6/25/172ec08ede51d056?w=1562&h=692&f=png&s=136113)

### git clone
当我们要进行开发，第一步就是克隆远程版本库到本地呢
```
git clone url  克隆远程版本库
```

![](https://user-gold-cdn.xitu.io/2020/6/25/172eac70ddf0000f?w=606&h=119&f=png&s=16710)

### git checkout -b dev
克隆完之后呢，开发新需求的话，我们需要新建一个开发分支，比如新建开发分支dev

创建分支：
```
git checkout -b dev   创建开发分支dev，并切换到该分支下
```
![](https://user-gold-cdn.xitu.io/2020/6/25/172eac94cbf03f92?w=585&h=118&f=png&s=14677)

### git add
git add的使用格式：
```
git add .	添加当前目录的所有文件到暂存区
git add [dir]	添加指定目录到暂存区，包括子目录
git add [file1]	添加指定文件到暂存区
```

有了开发分支dev之后，我们就可以开始开发啦，假设我们开发完HelloWorld.java，可以把它加到暂存区，命令如下
```
git add Hello.java  把HelloWorld.java文件添加到暂存区去
```

![](https://user-gold-cdn.xitu.io/2020/6/25/172ead69113ecab6?w=536&h=57&f=png&s=7135)


### git commit
git commit的使用格式：
```
git commit -m [message] 提交暂存区到仓库区,message为说明信息
git commit [file1] -m [message] 提交暂存区的指定文件到本地仓库
git commit --amend -m [message] 使用一次新的commit，替代上一次提交
```
把HelloWorld.java文件加到暂存区后，我们接着可以提交到本地仓库啦~
```
git commit -m 'helloworld开发'
```
![](https://user-gold-cdn.xitu.io/2020/6/25/172eae180b28d9e7?w=548&h=90&f=png&s=14447)

### git status 
git status,表示查看工作区状态，使用命令格式：
```
git status  查看当前工作区暂存区变动
git status -s  查看当前工作区暂存区变动，概要信息
git status  --show-stash 查询工作区中是否有stash（暂存的文件）
```
当你忘记是否已把代码文件添加到暂存区或者是否提交到本地仓库，都可以用git status看看哦~
![](https://user-gold-cdn.xitu.io/2020/6/25/172eb5ea0d5fde25?w=724&h=195&f=png&s=22238)

### git log
git log，这个命令用得应该比较多，表示查看提交历史/提交日志~

```
git log  查看提交历史
git log --oneline 以精简模式显示查看提交历史
git log -p <file> 查看指定文件的提交历史
git blame <file> 一列表方式查看指定文件的提交历史
```
嘻嘻，看看dev分支上的提交历史吧~要回滚代码就经常用它喵喵提交历史~
![](https://user-gold-cdn.xitu.io/2020/6/25/172eb79224fae0eb?w=593&h=214&f=png&s=28063)

### git diff
```
git diff 显示暂存区和工作区的差异
git diff filepath   filepath路径文件中，工作区与暂存区的比较差异
git diff HEAD filepath 工作区与HEAD ( 当前工作分支)的比较差异
git diff branchName filepath 当前分支的文件与branchName分支的文件的比较差异
git diff commitId filepath 与某一次提交的比较差异
```
如果你想对比一下你改了哪些内容，可以用git diff对比一下文件修改差异哦
![](https://user-gold-cdn.xitu.io/2020/6/25/172eb8d4ff0cb241?w=747&h=247&f=png&s=29019)

### git pull/git fetch
```
git pull  拉取远程仓库所有分支更新并合并到本地分支。
git pull origin master 将远程master分支合并到当前本地master分支
git pull origin master:master 将远程master分支合并到当前本地master分支，冒号后面表示本地分支

git fetch --all  拉取所有远端的最新代码
git fetch origin master 拉取远程最新master分支代码
```

我们一般都会用git pull拉取最新代码看看的，解决一下冲突，再推送代码到远程仓库的。

![](https://user-gold-cdn.xitu.io/2020/6/25/172ebdbf926f16b6?w=566&h=67&f=png&s=7840)

> 有些伙伴可能对使用git pull还是git fetch有点疑惑，其实
git pull = git fetch+ git merge。pull的话，拉取远程分支并与本地分支合并，fetch只是拉远程分支，怎么合并，可以自己再做选择。

### git push
git push 可以推送本地分支、标签到远程仓库，也可以删除远程分支哦。
```
git push origin master 将本地分支的更新全部推送到远程仓库master分支。
git push origin -d <branchname>   删除远程branchname分支
git push --tags 推送所有标签
```
如果我们在dev开发完，或者就想把文件推送到远程仓库，给别的伙伴看看，就可以使用git push origin dev~
![](https://user-gold-cdn.xitu.io/2020/6/25/172ebe5bab790e17?w=701&h=321&f=png&s=43043)

## Git进阶之分支处理
Git一般都是存在多个分支的，开发分支，回归测试分支以及主干分支等，所以Git分支处理的命令也需要很熟悉的呀~
- git branch 
- git checkout
- git merge  

### git branch
git branch用处多多呢，比如新建分支、查看分支、删除分支等等

**新建分支：**
```
git checkout -b dev2  新建一个分支，并且切换到新的分支dev2
git branch dev2 新建一个分支，但是仍停留在原来分支
```
![](https://user-gold-cdn.xitu.io/2020/6/25/172ec19a8c138ab3?w=530&h=64&f=png&s=8940)

**查看分支：**
```
git branch    查看本地所有的分支
git branch -r  查看所有远程的分支
git branch -a  查看所有远程分支和本地分支
```
![](https://user-gold-cdn.xitu.io/2020/6/25/172ec1f4c26f2847?w=566&h=357&f=png&s=34314)

**删除分支：**
```
git branch -D <branchname>  删除本地branchname分支
```

![](https://user-gold-cdn.xitu.io/2020/6/25/172ec2607fabe1c2?w=635&h=294&f=png&s=32151)

### git checkout
**切换分支：**
```
git checkout master 切换到master分支
```

![](https://user-gold-cdn.xitu.io/2020/6/25/172ec20cf6223d5c?w=540&h=83&f=png&s=10798)

### git merge
我们在开发分支dev开发、测试完成在发布之前，我们一般需要把开发分支dev代码合并到master，所以git merge也是程序员必备的一个命令。

```
git merge master  在当前分支上合并master分支过来
git merge --no-ff origin/dev  在当前分支上合并远程分支dev
git merge --abort 终止本次merge，并回到merge前的状态
```
比如，你开发完需求后，发版全需要把代码合到主干master分支，如下：
![](https://user-gold-cdn.xitu.io/2020/6/26/172ed9c119765d70?w=566&h=135&f=png&s=15550)

## Git进阶之处理冲突
Git版本控制，还是多个人一起搞的，多个分支并存的，这就难免会有冲突出现~

### Git合并分支，冲突出现
同一个文件，在合并分支的时候，如果同一行被多个分支或者不同人都修改了，合并的时候就会出现冲突。

举个粟子吧，我们现在在dev分支，修改HelloWorld.java文件，假设修改了第三行，并且commit提交到本地仓库，修改内容如下：

```
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello，捡田螺的小男孩！");
    }
}
```

我们切回到master分支，也修改HelloWorld.java同一位置内容，如下：

```
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello，jay！！");
    }
}
```
再然后呢，我们提交一下master分支的这个改动，并把dev分支合并过下，就出现冲突啦，如图所示：

![](https://user-gold-cdn.xitu.io/2020/6/26/172f101fc6cf63c9?w=934&h=591&f=png&s=78508)

### Git解决冲突
Git 解决冲突步骤如下：
- 查看冲突文件内容
- 确定冲突内容保留哪些部分，修改文件
- 重新提交，done

#### 1.查看冲突文件内容

git merge提示冲突后，我们切换到对应文件，看看冲突内容哈，，如下：
![](https://user-gold-cdn.xitu.io/2020/6/26/172f0fccbe271602?w=661&h=194&f=png&s=19547)

#### 2.确定冲突内容保留哪些部分，修改文件
- Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容，
- <<<<<<<HEAD是指主分支修改的内容，>>>>>>> dev是指dev分支上修改的内容

所以呢，我们确定到底保留哪个分支内容，还是两个分支内容都保留呢，然后再去修改文件冲突内容~

#### 3.修改完冲突文件内容，我们重新提交，冲突done

![](https://user-gold-cdn.xitu.io/2020/6/26/172f159fc458e088?w=760&h=419&f=png&s=38630)


## Git进阶之撤销与回退
Git的撤销与回退，在日常工作中使用的比较频繁。比如我们想将某个修改后的文件撤销到上一个版本，或者想撤销某次多余的提交，都要用到git的撤销和回退操作。

代码在Git的每个工作区域都是用哪些命令撤销或者回退的呢，如下图所示：
![](https://user-gold-cdn.xitu.io/2020/6/27/172f33c532a96e98?w=1595&h=958&f=png&s=181268)

有关于Git的撤销与回退，一般就以下几个核心命令
- git checkout
- git reset
- git revert

### git checkout
如果文件还在**工作区**，还没添加到暂存区，可以使用git checkout撤销
```
git checkout [file]  丢弃某个文件file
git checkout .  丢弃所有文件
```
以下demo，使用git checkout -- test.txt 撤销了暂存区test.txt的修改
![](https://user-gold-cdn.xitu.io/2020/6/26/172edffb19594acd?w=748&h=600&f=png&s=56693)

### git reset 

#### git reset的理解
> git reset的作用是修改HEAD的位置，即将HEAD指向的位置改变为之前存在的某个版本.

为了更好地理解git reset，我们来回顾一下,Git的版本管理及HEAD的理解
> Git的所有提交，会连成一条时间轴线，这就是分支。如果当前分支是master，HEAD指针一般指向当前分支，如下：

![](https://user-gold-cdn.xitu.io/2020/6/26/172eea4e91126bf2?w=1264&h=580&f=png&s=43485)

假设执行git reset，回退到版本二之后，版本三不见了哦,如下：

![](https://user-gold-cdn.xitu.io/2020/6/26/172eeac7913d8e0e?w=798&h=441&f=png&s=26783)

#### git reset的使用
Git Reset的几种使用模式
![](https://user-gold-cdn.xitu.io/2020/6/26/172ee65cd631720e?w=1185&h=536&f=png&s=53298)
```
git reset HEAD --file
回退暂存区里的某个文件，回退到当前版本工作区状态
git reset –-soft 目标版本号 可以把版本库上的提交回退到暂存区，修改记录保留
git reset –-mixed 目标版本号 可以把版本库上的提交回退到工作区，修改记录保留
git reset –-hard  可以把版本库上的提交彻底回退，修改的记录全部revert。
```

先看一个粟子demo吧，代码**git add到暂存区，并未commit提交**,就以下酱紫回退，如下：

```
git reset HEAD file 取消暂存
git checkout file 撤销修改
```
![](https://user-gold-cdn.xitu.io/2020/6/26/172ee361984a63b7?w=782&h=376&f=png&s=38651)

再看另外一个粟子吧，代码已经git commit了，但是还没有push：

```
git log  获取到想要回退的commit_id
git reset --hard commit_id  想回到过去，回到过去的commit_id
```
![](https://user-gold-cdn.xitu.io/2020/6/27/172f354c0a89d53b?w=808&h=830&f=png&s=107514)

如果代码已经push到远程仓库了呢，也可以使用reset回滚哦(这里大家可以自己操作实践一下哦)~
```
git log
git reset --hard commit_id
git push origin HEAD --force
```

### git revert
> 与git reset不同的是，revert复制了那个想要回退到的历史版本，将它加在当前分支的最前端。

**revert之前：**
![](https://user-gold-cdn.xitu.io/2020/6/27/172f3d1e923ec80f?w=1264&h=580&f=png&s=43485)
**revert 之后：**
![](https://user-gold-cdn.xitu.io/2020/6/26/172eeaf41ad2494a?w=1557&h=547&f=png&s=49240)

当然，如果代码已经推送到远程的话，还可以考虑revert回滚呢
```
git log  得到你需要回退一次提交的commit id
git revert -n <commit_id>  撤销指定的版本，撤销也会作为一次提交进行保存
```

![](https://user-gold-cdn.xitu.io/2020/6/27/172f3d0024e37637?w=1099&h=850&f=png&s=157867)


## Git进阶之标签tag
打tag就是对发布的版本标注一个版本号，如果版本发布有问题，就把该版本拉取出来，修复bug，再合回去。

```
git tag  列出所有tag
git tag [tag] 新建一个tag在当前commit
git tag [tag] [commit] 新建一个tag在指定commit
git tag -d [tag] 删除本地tag
git push origin [tag] 推送tag到远程
git show [tag] 查看tag
git checkout -b [branch] [tag] 新建一个分支，指向某个tag
```

![](https://user-gold-cdn.xitu.io/2020/6/27/172f41c4b0de5804?w=896&h=593&f=png&s=63528)

## Git其他一些经典命令
### git rebase
rebase又称为衍合，是合并的另外一种选择。

假设有两个分支master和test
```
      D---E test
      /
 A---B---C---F--- master
```
执行 git merge test得到的结果
```
       D--------E
      /          \
 A---B---C---F----G---   test, master
```
执行git rebase test，得到的结果

```
A---B---D---E---C‘---F‘---   test, master
```
**rebase好处是：** 获得更优雅的提交树，可以线性的看到每一次提交，并且没有增加提交节点。所以很多时候，看到有些伙伴都是这个命令拉代码：git pull --rebase

### git stash
stash命令可用于临时保存和恢复修改
```
git stash  把当前的工作隐藏起来 等以后恢复现场后继续工作
git stash list 显示保存的工作进度列表
git stash pop stash@{num} 恢复工作进度到工作区
git stash show ：显示做了哪些改动
git stash drop stash@{num} ：删除一条保存的工作进度
git stash clear 删除所有缓存的stash。
```

![](https://user-gold-cdn.xitu.io/2020/6/27/172f438c7afbdb8e?w=658&h=616&f=png&s=70671)

### git reflog
显示当前分支的最近几次提交

![](https://user-gold-cdn.xitu.io/2020/6/27/172f4282186ed3fd?w=827&h=556&f=png&s=124384)

### git blame filepath
git blame 记录了某个文件的更改历史和更改人，可以查看背锅人，哈哈
![](https://user-gold-cdn.xitu.io/2020/6/27/172f429391270576?w=825&h=184&f=png&s=38888)

### git remote
```
git remote   查看关联的远程仓库的名称
git remote add url   添加一个远程仓库
git remote show [remote] 显示某个远程仓库的信息
```

## 参考与感谢
感谢各位前辈的文章：
- [一个小时学会Git](https://www.cnblogs.com/best/p/7474442.html#_label3_4_0_4)
- [【Git】(1)---工作区、暂存区、版本库、远程仓库](https://www.cnblogs.com/qdhxhz/p/9757390.html)
- [Git Reset 三种模式](https://www.jianshu.com/p/c2ec5f06cf1a)
- [Git恢复之前版本的两种方法reset、revert（图文详解）](https://blog.csdn.net/yxlshk/article/details/79944535)
- [Git撤销&回滚操作(git reset 和 get revert)](https://blog.csdn.net/asoar/article/details/84111841)
- [为什么要使用git pull --rebase？](https://www.jianshu.com/p/dc367c8dca8e)

## 公众号
![](https://user-gold-cdn.xitu.io/2020/5/16/1721b50d00331393?w=900&h=500&f=png&s=389569)
- 欢迎关注我个人公众号，交个朋友，一起学习哈~
- 如果文章有错误，欢迎指出哈，感激不尽~

