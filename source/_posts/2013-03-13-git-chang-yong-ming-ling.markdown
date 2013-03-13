---
layout: post
title: "Git 常用命令"
date: 2013-03-13 14:19
comments: true
categories: 
- Git{git}
---


## Git 指令结构图

![Git 指令结构图](/images/post/git-transport.png "Git 指令结构图")

图片中展示了Git Repository结构和执行相关Command后数据的变迁流程，Git维护的代码分成三个部分

* current working directory 当前工作目录 (1)

* index file 暂存区 (2)

* git repository 历史仓库 (3)

<!-- more -->

![Git 指令结构图](/images/post/basic-usage.svg "Git 指令结构图")

`git add`命令会将(1)的改变写到(2)

`git commit` 将(2)的改变写到(3)

`git commit -a` 会直接将(1)中的改动同时写到(2)和(3)

`git reset` 用来撤销最后一次git add files，也可以用来撤销所有暂存区域文件。

`git checkout` 把文件复制到工作目录或者暂存区域，可以用来撤销在当前工作目录中的修改。

`git checkout HEAD --files`将仓库中的文件覆盖到工作目录。

`git diff` 命令用来查看差异：

* git diff 得到的是从(2)到(1)的变化

* git diff –cached 得到的是从(3)到(2)的变化

* git diff HEAD 得到的是从(3)到(1)的变化

* git diff <source_branch> <target_branch>  查看分支间差异


## Git的基本命令举例

{% codeblock lang:sh  %}

# 帮助和配置
git help command                          查看帮助
git command –help                         查看帮助
git config –global user.name <username>   设置用户名
git config –global user.email <email>     设置用户邮箱
git config –list                          查看用户信息


# 新建仓库
git init                                     初始化
git clone /path/to/repository                创建一个本地仓库的克隆
git clone username@host:/path/to/repository  创建一个远端服务器仓库的克隆


# git add 命令  含义
git add file_name                 加单个文件到缓存区
git add .                         加当前目录的所有文件到缓存区
git add -i                        进入交互式add
git add -p                        直接进入补丁模式，可以暂存修改的一部分。
git remote add origin <server>    修改推送到远程的服务器


# git commit 命令 含义
git commit -m “代码提交信息”         提交改动
git commit -a                      第一步：自动地add所有改动的代码，使得所有的开发代码都列于index file中；第二步：自动地删除那些在index file中但不在工作树中的文件；第三步：执行commit命令来提交。
git commit –amend -m “新的提交信息”  提交改动（若有改动） + 修补最近一次提交


# git push 命令 含义
git push origin test:test2      提交本地test分支作为远程test2分支
git push origin test            提交本地test分支作为远程test分支
git push origin :test           删除远程test分支
git push origin :refs/tags/t4   删除远程tag标签t4


# git pull 命令 含义
git pull                    从仓库取回代码，更新本地仓库至最新
git pull origin test:test2  取回远程的test分支作为本地的test2分支


# git checkout 命令 含义
git checkout master         切换回主分支
git checkout test           如果本地无test分支，先需要从远程pull回test分支
git checkout -b feature_x   创建一个叫做“feature_x”的分支，并切换过去


# git branch 命令 含义
git branch b4 t4          以tag t4建立新的分支b4
git branch -d feature_x   删除分支


# git merge 命令  含义
git merge AAA             将分支AAA与当前分枝合并


# git diff 命令 含义
git diff  查看working tree与index file的差别的（这个命令只在git add之前使用有效。如果已经add了，那么此命令输出为空）。
git diff –cached  查看index file与commit的差别的（这个命令在git add之后在git commit之前有效。）。
git diff HEAD 查看working tree和commit的差别的。（你一定没有忘记，HEAD代表的是最近的一次commit的信息）


# git reset 命令  含义
git reset –soft     只撤销commit，保留working tree和index file。
git reset –mixed    撤销commit和index file，保留working tree
git reset –hard     撤销commit、index file和working tree，即撤销销毁最近一次的commit
git reset           和git reset –mixed完全一样
git reset –         用于删除登记在index file里的某个文件。例如：git reset – roc.c


# git revert 命令 含义
git revert  用于回滚（撤销）一些commit。对于一个或者多个已经存在的commit，去除由这些commit引入的改变，并且用一个新的commit来记录这个回滚操作。这个命令要求working tree必须是干净的。
git revert HEAD~3 丢弃最近的三个commit，把状态恢复到最近的第四个commit，并且提交一个新的commit来记录这次改变。
git revert -n master~5..master~2  丢弃从最近的第五个commit（包含）到第二个（不包含）,但是不自动生成commit，这个revert仅仅修改working tree和index。


# 其他命令
git merge <branch>  合并其他分支到当前分支
git log             获取提交 ID
git tag 1.0.0 <ID>  创建一个叫做 1.0.0 的标签
{% endcodeblock %}