title: git中文换行符
date: 2014-11-04 14:25:24
tags: git
description: fatal LF would be replaced by CRLF
---
如果你的源文件中是换行符是LF，而autocrlf=true, 此时git add就会遇到 fatal: LF would be replaced by CRLF 的错误。有两个解决办法：
1. 将你的源文件中的LF转为CRLF即可【推荐】
2. 将autocrlf 设置为 false

如果你的源文件中是换行符是CRLF，而autocrlf=input,  此时git add也会遇到 fatal: CRLF would be replaced by LF 的错误。有两个解决办法：
1. 将你源文件中的CRLF转为LF【推荐】
2. 将autocrlf 设置为true 或者 false

我的建议：在Mac上设置 autocrlf = input, 在Windows上设置autocrlf = true（默认值）。
----------------------------------------------------------------------------------------------------------------------------------
这样的话，
Windows：（true）
提交时，将CRLF 转成 LF再提交；
切出时，自动将LF 转为 CRLF;

MAC/Linux:	(input)
提交时,   将CRLF 转成 LF再提交；
切出时，保持LF即可

这样即可保证仓库中永远都是LF. 而且在Windows工作空间都是CRLF, 在Mac/Linux工作空间都是LF.
----------------------------------------------------------------------------------------------------------------------------------

core.autocrlf

假如你正在Windows上写程序，又或者你正在和其他人合作，他们在Windows上编程，而你却在其他系统上，在这些情况下，你可能会遇到行尾结束符问题。这是因为Windows使用回车和换行两个字符来结束一行，而Mac和Linux只使用换行一个字符。虽然这是小问题，但它会极大地扰乱跨平台协作。

Git可以在你提交时自动地把行结束符CRLF转换成LF，而在签出代码时把LF转换成CRLF。用core.autocrlf来打开此项功能，如果是在Windows系统上，把它设置成true，这样当签出代码时，LF会被转换成CRLF：
```
$ git config --global core.autocrlf true
```
Linux或Mac系统使用LF作为行结束符，因此你不想 Git 在签出文件时进行自动的转换；当一个以CRLF为行结束符的文件不小心被引入时你肯定想进行修正，把core.autocrlf设置成input来告诉 Git 在提交时把CRLF转换成LF，签出时不转换：
```
$ git config --global core.autocrlf input
```
这样会在Windows系统上的签出文件中保留CRLF，会在Mac和Linux系统上，包括仓库中保留LF。

如果你是Windows程序员，且正在开发仅运行在Windows上的项目，可以设置false取消此功能，把回车符记录在库中：
```
$ git config --global core.autocrlf false
```

