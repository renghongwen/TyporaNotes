## Git



#### 1.Git基本介绍

Git：分布式版本控制工具

CVS，SVN：集中式版本控制工具

| 常用命令                             | 作用                                   |
| ------------------------------------ | -------------------------------------- |
| git config --global user.name 用户名 | 配置全局用户名                         |
| git config --global user.email 邮箱  | 配置全局邮箱                           |
| git config user.name(user.email)     | 查看配置的全局用户名或邮箱             |
| git init                             | 初始化本地库                           |
| git status                           | 查看本地库状态                         |
| git add 文件名                       | 将文件添加到暂存区                     |
| git rm  --cached 文件名              | 将文件从暂存区中删除                   |
| git commit -m "日志信息"  文件名     | 将暂存区的文件提交到本地库             |
| git reflog                           | 查看本地库版本信息                     |
| git log                              | 查看本地库版本详细信息                 |
| git reset --hard 版本号              | 版本穿梭，也就是进行版本切换           |
|                                      | GIt 切换版本，底层其实是移动的HEAD指针 |

工作区 --> 暂存区 -->本地库 -->远程库

> 工作区和暂存区的文件是可以删除的，一旦代码添加到本地库，文件就不能进行删除，因为本地库保存了所有修改的历史记录。

最好每次提交一个文件，这样回滚的时候就比较方便



#### 2. Git分支操作

> 分支的好处:同时并行推进多个功能开发，提高开发效率。

**Git分支常用操作**

| 常用命令            | 作用                         |
| ------------------- | ---------------------------- |
| git branch 分支名   | 创建分支                     |
| git branch -v       | 查看分支信息                 |
| git checkout 分支名 | 切换分支                     |
| git merge 分支名    | 把指定的分支合并到当前分支上 |

合并分支 （冲突合并的情况下）

> 合并分支时，两个分支在同一文件的同一位置有两套完全不同的修改

这时，就需要我们手动决定新代码的内容

​	   首先，查看本地库状态，查看冲突的文件(git status)，然后，进入文件，决定要保留的内容是哪些，解决完冲突内容，需将修改的文件提交到暂存区，本地库，**注意:在提交到本地库时，使用git commit命令时不能带文件名**



#### 3. 远程仓库常用操作

| 常用命令                     | 作用                       |
| ---------------------------- | -------------------------- |
| git remote -v                | 查看当前所有远程库地址别名 |
| git remote add 别名 远程地址 | 创建远程仓库               |
| git push 别名 分支           | 推送本地分支到远程仓库     |
| git pull 别名 分支           | 拉取远程库到本地库         |
| git clone 远程地址           | 克隆远程仓库到本地         |

#### 4

红色：未加入暂存区

绿色：加入暂存区

蓝色：加入到暂存区，并已提交，但是有修改

白色：加入到暂存区，并已提交，但没有修改

灰色：

黄色：

git rm 文件名 ：把工作区和暂存区的文件都删除

git checkout : 切换分支



- 代码在工作区时

​	使用命令

​		git checkout 文件路径    

​	可以对代码进行回滚 (前提条件是该代码存在远程仓库，且代码是回滚到远程仓库的相应代码)

- 代码在 暂存区时

  使用命令

  ​	git reset head 文件路径

  ​    git checkout 文件路径

  对代码进行回滚

- 代码在本地仓库时

  使用命令

     git reset  hard/mixed/soft    commit号

  对代码进行回滚

  hard模式：是将工作区，暂存区，本地库的代码都进行回滚

  mixed模式：是只将工作区的代码进行回滚

  soft模式：是将暂存区的代码进行回滚

  ####  5.git merge 和 git rebase的区别

​			git merge：采用的是三元合并的方式，该方式可以减少一些手动解决冲突的情况。

其中祖先节点其到了一个参照物作用，当其中一元和参照物相同时，不需要手动解决冲突

​			rebase：变基

git rebase操作 ：

​		将分支切换到要被变基的分支上，然后使用 git rebase 变基分支 进行变基操作

```
rebase黄金原则：共享的public分支不能rebase
```

发生冲突后执行完git add 进入到暂存区后，需要使用 git rebase --continue继续进行变基操作



#### 6. git cherry-pick

git cherry-pick 将少数几个commit合并到其他分支上 （功能类似于git merge和git rebase，区别在于git merge和git rebase是将整个分支的提交合并到指定分支上，而git cherry-pick则是将指定的commits合并到指定分支上）

> 常用命令:
>
> 合并单个提交：git  cherry-pick  commit号
>
> 合并多个提交：git cherry-pick commit号1  commit号2  commit号3
>
> 合并多个连续的提交：git cherry-pick commit号1..commit号2     提交的区间是左开右闭，也就是(commit号1,commit号2]
>
> 合并多个连续的提交：git cherry-pick commit号1^..commit号2    提交的区间是左闭右闭 ，也就是[commit号1,commit号2]

发送冲突后，可以进行三种操作

- 解决冲突： git add      ,     git cherry-pick --continue
- 回滚：git cherry-pick --abort
- 中断：git cherry-pick --quit

#### 7. git stash

作用：保存历史修改代码

git stash相关命令：

**入栈**

> git stash 
>
> git stash save ‘注释’

**出栈**

> git stash pop (会将栈最上面的代码弹出)
>
> git stash apply (栈中最上面的代码不会出栈)

**清除**

> git stash clear
>
> git stash drop + 栈索引

**查看**

> git stash list
>
> git stash show  + 栈索引

git stash 的两种使用场景：

一。当前分支A，Bug分支B

> - git stash 
> - git checkout B
> - 处理完分支B中的Bug
> - git checkout A
> - git stash pop

二。代码开发完准备提交

其他人对相同文件进行了修改，git pull不下来代码，需要进行以下操作

> - git stash
> - git pull
> - git stash pop
> - 本地处理可能出现的冲突
> - git commit + git push



  

























#### git diff 提交范围中 没点，两点，三点的区别



































