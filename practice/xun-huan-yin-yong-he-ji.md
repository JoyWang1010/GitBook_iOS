# 循环引用合集

今天要说的是iOS里面经常容易出现内存泄漏的几个地方，在写代码的过程中，会经常出现pop出页面的时候，控制器的dealloc方法不调用的情况，这时候可以从下面几个方面检查一下代码是不是存在内存泄漏：

### 1、block

如果你在代码中写了block，并像下面的block一样调用了block持有者的其他持有对象，这么你应该已经找到内存泄漏的地方了：  
.h中

```text
MemoryLeakTestViewController中
@interface MemoryLeakTestViewController ()

@property (nonatomic, copy) void(^blockA)(NSString *a);
@property (nonatomic, copy) NSString *str;

@end

@implementation MemoryLeakTestViewController

- (void)doAction {    
    self.blockA = ^(NSString *a) {
        self.str = a;
    };
    
    self.blockA(@"1234");
}

- (void)dealloc {
    NSLog(@"MemoryLeakTestViewController dealloc");
}

//其他控制器调用MemoryLeakTestViewController

MemoryLeakTestViewController *vc = [[MemoryLeakTestViewController alloc] init];
[vc doAction];
```

在上面的代码中，vc对象永远无法被释放，因为在vc的blockA中调用了self对象，在block调用self的过程中，self的引用计数会+1，相当于block持有了vc，而vc对象又始终持有blockA，导致vc和blockA相互持有，vc引用计数不会为0，循环引用发生。

解决这个问题只需要在blockA中调用的vc对象的时候不使vc对象的引用计数+1即可，我们可以声明一个弱引用的vc对象，再把这个弱引用转入blockA即可，但是弱引用的vc对象没有被任何对象持有，可能随时会被释放，所以要在blockA中，强引用传入的弱引用对象，这样的话vc的弱引用对象不会在blockA执行一半的时候就直接被释放，并且不会使vc的引用计数+1。在doAction方法中修改的代码如下：

```text
__weak MemoryLeakTestViewController *weakSelf = self;
self.blockA = ^(NSString *a) {
    __strong MemoryLeakTestViewController *strongSelf = weakSelf;
    strongSelf.str = a;
};
```

下面是我对block的一些理解，不感兴趣可以略过，如有错误欢迎指正。

Block在内存中分为三种类型：

* NSGlobalBlock：类似函数
* NSStackBlock：位于栈内存
* NSMallocBlock：位于堆内存，我们说的block内存泄漏都是源于这里

NSGlobalBlock为没有引用外部变量的block，可以当函数用。NSStackBlock在函数返回后，内存将被回收，对NSStackBlock进行copy操作可以获取NSMallocBlock类型。在ARC中，会默认将实例化的block会自动copy到堆上，即类型变为NSMallocBlock。block在拷贝到堆上的时候，会retain其引用的外部变量，那么如果block中引用了他的宿主对象，就很可能引起循环引用。

### 2、定时器NSTimer

控制器中的NSTimer在创建的过程中需要引用到self作为target，这时如果timer一直是validate的状态，那么引用计数就一直大于0，这时self也不会被释放掉。就会造成循环引用。在这里使用weakSelf是没有效果的，即使建立timer的时候传入weakSelf作为timer的target，timer还是会强引用weakSelf。如果想要释放控制器的内存，就需要在这之前提前释放timer，所以timer的释放需要写到控制器生命周期dealloc的方法之前。比如可以把timer的设置和释放方法分别写在viewWillAppear和viewWillDisappear中。

### 3、委托delegate

这是一个很老很老的问题了，假设有A、B两个控制器，B中有一个声明为retain的delegate，当A调用到B控制器的delegate时，就会形成A-&gt;B&gt;B.delegate-&gt;A的引用链，这个引用链不会被释放，就造成了内存泄漏。所以只要是delegate的内存泄漏，直接把delegate声明成assign就可以了。

### 4、WKWebView

WKWebView在通过WKUserContentController添加MessageHandle方法用于JS调用Native时会导致内存泄漏。代码如下：

```text
    WKWebViewConfiguration *wkConfig = [[WKWebViewConfiguration alloc] init];
    wkConfig.userContentController = [[WKUserContentController alloc] init];
    [wkConfig.userContentController addScriptMessageHandler:self name:@"Native"];
    self.webView = [[WKWebView alloc] initWithFrame:self.view.frame configuration:wkConfig];
    [self.view addSubview:self.webView];
```

如果你写了上面这样的代码，那么你的webview所在控制器的dealloc方法是永远不会被调用到的。

在这里可以迂回处理一下，新建一个承载ScriptMessageHandle的WeakScriptMessageDelegate类，并在新建的类中调用webview所在类的处理scriptMessage的方法。WeakScriptMessageDelegate代码如下：

```text
@interface WeakScriptMessageDelegate : NSObject<WKScriptMessageHandler>

@property (nonatomic, weak) id<WKScriptMessageHandler> scriptDelegate;

- (instancetype)initWithDelegate:(id<WKScriptMessageHandler>)scriptDelegate;

@end

@implementation WeakScriptMessageDelegate

- (instancetype)initWithDelegate:(id<WKScriptMessageHandler>)scriptDelegate {
    self = [super init];
    if (self) {
        _scriptDelegate = scriptDelegate;
    }
    return self;
}

- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
    [self.scriptDelegate userContentController:userContentController didReceiveScriptMessage:message];
}

@end

在webview所在类中应该这样调用：
```

```text
WKWebViewConfiguration *wkConfig = [[WKWebViewConfiguration alloc] init];
wkConfig.userContentController = [[WKUserContentController alloc] init];
[wkConfig.userContentController addScriptMessageHandler:[[WeakScriptMessageDelegate alloc] initWithDelegate:self] name:@"Native"];
```

这样就可以正常调用控制器的dealloc方法了，再在dealloc中释放WeakScriptMessageDelegate

```text
- (void)dealloc {
    [self.wkConfig.userContentController removeScriptMessageHandlerForName:@"Native"];
}
```

