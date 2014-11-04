title: git使用
date: 2014-11-03 19:50:27
tags: 技巧
description: git使用简介
---

git branch coding
git checkout coding
git pull origin coding
git add .
git commit -m ""
git push -u origin master:coding

## git初步
git init  //初始化空的 git 仓库
touch README.md   
git add README.md //这两行添加简单的 README.md 文件
git commit -m "first commit" //提交时附加的信息
git remote add origin https://coding.net/codingTutorial/gitDemo.git  
>  **添加一个名为 origin 的远端（ url 为 git 地址）**

如果在当前分支下
git push -u origin master //将该目录下的文件推送到远端（origin）上的 "master" 分支
如果不是当前分支
git push -u origin master:master   //(本地分支：远端)，如果没有远端分支会创建，

---
如果你是第一次想把github上面的项目克隆到本地或者要克隆别人的项目到地。

$ git clone git@github.com:defnngj/hibernate-demo.git  //在git下面切换到想存放此项目的文件目录下，运行这条命令就可以将项目克隆下来。
git clone -b master https://github.com/cyqluowang/blog  mybolg
clone master 分支，本地文件夹名myblog
---

假如本地已经存在了这个项目，而仓库中又有一新的更新，如何把更的合并到本地的项目中？

$ git fetch origin    //取得远程更新，这里可以看做是准备要取了

$ git merge origin/master  //把更新的内容合并到本地分支/master

---

### 1 查看远程分支


``` java
$ git branch -a  
* br-2.1.2.2  
  master  
  remotes/origin/HEAD -> origin/master  
  remotes/origin/br-2.1.2.1  
  remotes/origin/br-2.1.2.2  
  remotes/origin/br-2.1.3  
  remotes/origin/master 
  ``` 

### 2 查看本地分支

``` java 
$ git branch  
* br-2.1.2.2  
  master  
```

### 3 创建分支
``` java
$ git branch test 
``` 
  
``` java 
$ git branch  
* br-2.1.2.2  
  master  
  test  
```

线面是把分支推到远程分支 

``` java
$ git push origin test  
```

删除本地文件同步到远程
git add -u




## git解决冲突：
1. 想要留下远程和本地的代码
```
git stash//隐藏本地跟新
git pull//拉取服务器上代码
git stash pop//回复本地跟新
git diff -w 文件名 来确认代码自动合并的情况.
```
2. 只留下服务器
```
git reset --hard
git pull
```

> git pull origin gh-pages
等于
git fetch origin gh-pages
//将origin/gh-pages合并到当前分支  
//origin/gh-pages 远端分支名
git merge origin/gh-pages






