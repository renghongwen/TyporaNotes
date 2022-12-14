## Git



#### 1.Git基本介绍

Git：分布式版本控制工具

CVS，SVN：集中式版本控制工具

| 常用命令                             | 作用                                   |
| ------------------------------------ | -------------------------------------- |
| git config --globle user.name 用户名 | 配置全局用户名                         |
| git config --globle user.email 邮箱  | 配置全局邮箱                           |
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



