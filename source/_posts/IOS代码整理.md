title: IOS代码整理
date: 2015-12-22 17:12:12
tags:	IOS
toc: true
---

# 删除svn记录
一、在linux下  
```
find . -type d -name ".svn"|xargs rm -rf
```
```
find . -type d -iname ".svn" -exec rm -rf {} /;  
```

二、在windows下用以下法子：

1、在项目平级的目录，执行dos命令： 
```
xcopy project_dir project_dir_1 /s /i
```

2、或者在项目根目录执行以下dos命令 
```
for /r . %%a in (.) do @if exist "%%a/.svn" rd /s /q "%%a/.svn"
```

# 检测是否含IDFA
1、打开终端cd到要检查的文件的根目录。

2、执行下列语句：
```
grep -r advertisingIdentifier .   
```（别少了最后那个点号）。
<!--more-->