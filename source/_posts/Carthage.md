title: 项目支持Carthage
date: 2017-8-22 21:12:12
tags: Carthage
toc: true
---

# 项目支持Carthage

## 前提条件
1、只支持IOS8以上（or any version of OS X）
2、只支持dynamic frameworks

## 支持方式
1、新建Cocoa Touch Framework
2、代码无错误，编译成功
3、打开共享 Xcode schemes，这步是关键
4、运行测试

```
carthage build --no-skip-current
```

5、通过后提交代码，带上.xcodeproj
6、打上标签tag

<!--more-->

## 只提供framework方式
1、打包framework

```
carthage build --no-skip-current
carthage archive YourFrameworkName
```

2、新建release，上传打包的zip即可。