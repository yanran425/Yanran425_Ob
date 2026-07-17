---
coding: UTF-8
title: Git基础
date: 2026-03-22T23:03:00
updated: 2026-07-18T03:57:06
tags: []
share: true
---

# Git基础

## 1. 图示

![](../assets/Git%E5%9F%BA%E7%A1%80.png)

![](../assets/Git%E5%9F%BA%E7%A1%80-1.png)

## 2. 指令

| 作用|指令|
|:---:|:---|
|创建用户|git config [--global] user.name "username"|
|       |git config [--global] user.email "email"|
|生成密钥|ssh-keygen -t rsa -C "这里换上你的邮箱"|
|验证连接|ssh -T git@github.com|
|本地仓库处理|git init；git add .；git commit；git commit -m ''；git reset --hard \<commit ID>；|
|日志状态|git status；git log；git reflog；|
|分支处理|git branch；git branch ...；git checkout ...；|
||git branch -v；git branch -vv；git branch -a；|
||git merge ...；git branch -d ...；git branch -D ...；|
|链接远程仓库|git remote add <远端名称> <仓库路径>|
|绑定本地仓库与远程仓库对应关系|git branch --set-upstream-to=[远端名称]/[远端分支名称] [本地分支名称]；|
|查看远程仓库|git remote|
|远程仓库处理|git clone ...；git clone [远端仓库地址] [克隆后本地仓库名称]；git clone [远端仓库地址] [克隆后本地仓库名称] -b [远端分支名]；|
||git fetch；git fetch [远端名称] [远端分支名称]；git fetch [远端名称] --all；|
||git pull；git pull [远端名称] [远端分支名称]；|
||git push [-f] [远端名称] [本地分支名称]；|

> [!note] `git fetch`实际上将**本地仓库中**的远程分支**更新**成了远程仓库相应分支最新的状态

> [!note] `git fetch`并不会改变你本地仓库的状态，也不会修改你磁盘上的文件，`git fetch`为单纯的下载操作。

> [!note] `git pull`相当于`git fetch`+`git merge`。

- alias git-log='git log --all --graph --abbrev-commit --pretty=oneline'

## 3. 冲突

![](../assets/Git%E5%9F%BA%E7%A1%80-2.png)
- 远程分支也是分支，合并冲突的解决方法与本地分支合并冲突的解决方法一致。

> name = Yanran425|WqWang
>
> email = Yanran425@yr.com|WqWang@yr.com
>
> alias git-log='git log --all --graph --abbrev-commit --pretty=oneline'
