# iOS禁用返回手势

```
-(void)viewDidAppear:(BOOL)animated{
    [super viewDidAppear:animated];
    // 禁用返回手势
    if ([self.navigationController respondsToSelector:@selector(interactivePopGestureRecognizer)]) {
        self.navigationController.interactivePopGestureRecognizer.enabled = NO;
        self.navigationController.interactivePopGestureRecognizer.delegate = nil;
    }
}// 与FD相互作用下 不起作用

#import "UINavigationController+FDFullscreenPopGesture.h"
#import "UIScrollView+FDFullscreenPopGesture.h"

- (void)viewDidLoad {
  [super viewDidLoad];
  self.fd_interactivePopDisabled = YES;
}
```

