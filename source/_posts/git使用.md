title: git使用
date: 2014-11-03 19:50:27
tags: 技巧
---



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

哈哈哈





