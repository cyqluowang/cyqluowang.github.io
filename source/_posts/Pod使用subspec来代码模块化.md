title: Pod使用subspec来代码模块化
date: 2017-8-11 21:12:12
tags: CocoaPods
toc: true
---

# Pod使用subspec来代码模块化

## 概况
前段时间尝试了组件化，使用本地pod的方式，但是发现如果有多级依赖的情况下，都是本地的pod会有问题。所以找了这样的方式。也可以代码分组。实现良好的代码分层。


<!--more-->


## 例子

```
Pod::Spec.new do |s|
  s.name             = 'FSLib'
  s.version          = '0.1.0'
  s.summary          = 'A short description of BZLib.'

  s.description      = <<-DESC
                        Add long description of the pod here.
                       DESC
  s.homepage         = 'https://github.com/chenc/BZLib'
  s.license          = { :type => 'MIT', :file => 'LICENSE' }
  s.author           = { 'chenyq' => 'chenc@getui.com' }
  s.source           = { :git => 'https://github.com/chenc/BZLib.git', :tag => s.version.to_s }
  
  s.ios.deployment_target = '8.0'
  # 默认的使用模块
  s.default_subspec = 'FSLib'
  # 个个子模块的个自的源码路径
  s.subspec 'FSLib' do |fsLib|
    fsLib.source_files = 'FSLib/**/*'
  end
  # A a 一定要不一样，是别名，添加好暴露的头文件
  s.subspec 'A' do |a|
    a.source_files = 'FSLib/A/**/*'
    a.public_header_files='FSLib/A/A.h'
  end
  # 可以添加内部自己模块，或者外部的依赖
  s.subspec 'B' do |b|
    b.source_files = 'FSLib/B/**/*'
    b.dependency 'FSLib/A'
  end

  s.subspec 'C' do |c|
    c.source_files = 'FSLib/C/**/*'
    c.dependency 'FSLib/B'
  end


end
```

## 使用

```
target 'fsaf' do
	platform :ios, "8.0"
    pod 'FSLib/A',:path => 'FSLib'//具体使用哪个模块，path指定本地文件
    project 'fsaf/fsaf.project'
end
```

## 注意：
* 头文件传递依赖的都能导入使用
* 可以控制自己的暴露头文件

