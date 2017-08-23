title: 浏览器打印iOS日志
date: 2017-8-23 21:12:12
tags: iOS
toc: true
---

# ios 浏览器查看日志
## 思路
1、日志改写到沙盒文件
2、App起一个http服务

## 实现
1、改写日志位置

```
    freopen([self.logFilePath cStringUsingEncoding:NSASCIIStringEncoding], "a+", stdout);
    freopen([self.logFilePath cStringUsingEncoding:NSASCIIStringEncoding], "a+", stderr);
```
注：这里的日志是实时写入的，所以可以读到。

<!-- more -->

2、读取日志(上面写入的日志)

```
    - (NSString *)getFileContent {
          NSString *content = [NSString stringWithContentsOfFile:self.logFilePath encoding:NSUTF8StringEncoding error:nil];
    //    NSLog(@"文件读取成功: %@",content);
          return content;
      }
```
3、起http服务（GCDWebServer开源项目）

```
    _webServer = [[GCDWebServer alloc] init];
    __weak __typeof__(self) weakSelf = self;
    // Add a handler to respond to GET requests on any URL
    [_webServer addDefaultHandlerForMethod:@"GET"
                              requestClass:[GCDWebServerRequest class]
                              processBlock:^GCDWebServerResponse *(GCDWebServerRequest *request) {
                                return [GCDWebServerDataResponse responseWithText:[weakSelf getFileContent]];
                              }];

    [GCDWebServer setLogLevel:4];
    // Start server on port 8080
    [_webServer startWithPort:8080 bonjourName:nil];


```
```
注：
1、这里用responseWithText，这样不用处理日志格式化
2、self.logFilePath 命名时，每次重启写入新的文件，文件名后缀.log
```
```
缺点：
3、每次浏览器刷新就会读取全部的数据，全量更新。
4、没有加上自动刷新，加了就要处理html,不方便
```


