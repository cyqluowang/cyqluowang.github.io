title: hexo建博客
date: 2014-11-03 20:39:10
tags: 技巧
description: hexo建博客
---
##  安装hexo
利用 npm 命令即可安装。（在任意位置点击鼠标右键，选择Git bash）
``` java
npm install -g hexo
```
创建hexo文件夹
安装完成后，在你喜爱的文件夹下（如H:\hexo），执行以下指令(在H:\hexo内点击鼠标右键，选择Git bash)，Hexo 即会自动在目标文件夹建立网站所需要的所有文件。
``` java
hexo init
```

安装依赖包
``` java
npm install
```

本地查看
现在我们已经搭建起本地的hexo博客了，执行以下命令(在H:\hexo)，然后到浏览器输入localhost:4000看看。
``` java
hexo generate
hexo server
```
> hexo generate == hexo g
hexo server ==hexo s
hexo d发布

好了，至此，本地博客已经搭建起来了，只是本地哦，别人看不到的。下面，我们要部署到Github。