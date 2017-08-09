title: CocoaPods本地多级依赖
date: 2017-8-9 21:12:12
tags: CocoaPods
toc: true
---

# pod 使用本地文件以及多级依赖

## 一级指定本地pod文件
```
target 'XXDemo' do
	platform :ios, "7.0"
    pod 'Masonry'
    pod 'RealReachability'
    pod 'XXSdk',:path => 'XXSdkLib'
    project 'XXDemo/XXDemo.project'
end
```
xxsdk 是本地的podspec里面定义的name
:path => 指定本地路径
XXSdkLib 存放podspec文件的文件夹

<!--more-->
## 2级本地pod
1、第一级，建立好XX.podspec 文件，正常新建就行
2、第二级本地文件
新建XX.podspec 和podfile文件
XX.podspec 里面正常加上

```
  s.dependency     'Gonfig'
```

podfile

```
target 'XXSdkLib' do
	platform :ios, "7.0"
    pod 'Gonfig',:path => '../Gonfig'
    project 'XXSdkLib/XXSdkLib.project'
end
```

关键的path 要指定到XX.podspec（上一级podspec）文件夹

## demo使用第二级pod

```
    pod 'A',:path => 'A podspec 文件夹'
    pod 'B',:path => 'B podspec 文件夹'
```
要加上两级的pod指向，不然会找不到A


## 注意：
单独编译B是不行的，这个时候找不到A,运行demo没有问题

## 参考：
https://stackoverflow.com/questions/16905112/cocoapods-dependency-in-pod-spec-not-working

http://feihu.me/blog/2016/unittest-for-framework-in-big-project/

