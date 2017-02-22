title: IOS手势
date: 2015-12-15 12:06:04
tags: IOS
---


ios手势参数  

```
UITapGestureRecognizer *tapGestureRecognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleTapGestureRecognizer:)];
[self addGestureRecognizer:tapGestureRecognizer];

- (void)handleTapGestureRecognizer:(UITapGestureRecognizer *)tapGestureRecognizer {
    [self hiddenAnimationCompletion:^(BOOL finished) {
        
    }];
}
```
1. initWithTarget:self  后面方法（handleTapGestureRecognizer）所属的对象
2. [self addGestureRecognizer:tapGestureRecognizer];把手势关联到哪个对象上面，目前是self，也可以是self.view.

<!--more-->