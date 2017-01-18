---
title: git常用命令
date: 2016-12-16 16:18:58
tags: [git]
---

git **config** 配置或读取相应的工作环境变量

git config --global user.name "name"
git config --global user.email "email"

git **init** 创建本地仓库

git **add** file 工作区提交到暂存区

git **commit** -m "instruction" 提交到本地仓库

git **status**  查看是否有文件被修改过

git **diff**  file 工作区与暂存区的比较

git diff --cached 暂存区与本地库比较

git diff HEAD file 工作区域本地库比较

<!--more-->

git **branch** branch-name 创建分支

git branch 查看当前分支

git branch -D branch-name 丢弃一个没有被合并过的分支，强行删除 

git branch --set-upstream branch-name origin/branch-name 建立本地分支和远程分支的关联

git branch -d branch-name 删除分支

git **checkout** --file 用版本库或暂存区版本替换工作区版本

git checkout 切换分支

git checkout -b branch-name 创建并切换分支

git checkout -b branch-name origin/branch-name 在本地创建和远程分支对应的分支

git **log** 查看提交历史，以便确定要回退到哪个版本

git log --graph 可以看到分支合并图

**HEAD** 当前版本 HEAD^ 上一个版本 HEAD~100  上一百个版本

git **reset** --hard commit-id 回退到历史版本

git reset HEAD file 把暂存区的修改回退到工作区

git **reflog** 查看命令历史，以便确定要回到未来的哪个版本

git **rm** file 删除文件

git **remote ** add origin-name repository-url 本地关联远程库

git remote -v 查看远程库信息，默认远程库名称为origin

git **push** [-u] origin branch-name 把本地分支的最新修改推送至远程仓库

git **clone** repository-url 克隆一个本地仓库

git **merge** [--no-ff]  branch-name 合并分支到当前分支，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并

git **stash** 把当前工作现场“储藏”起来

git stash apply 恢复，但是并不删除stash内容

git stash pop 恢复同时删除stash内容

git stash list 查看stash

git **pull** 抓取远程的新提交 