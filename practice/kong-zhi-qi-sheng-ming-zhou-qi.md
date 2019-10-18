# 控制器生命周期

ARC环境

* _**单个viewController的生命周期**_
  * initWithCoder:\(NSCoder \*\)aDecoder：（如果使用storyboard或者xib）
  * loadView：加载view
  * viewDidLoad：view加载完毕
  * viewWillAppear：控制器的view将要显示
  * viewWillLayoutSubviews：控制器的view将要布局子控件
  * viewDidLayoutSubviews：控制器的view布局子控件完成 `这期间系统可能会多次调用viewWillLayoutSubviews 、 viewDidLayoutSubviews 俩个方法`
  * viewDidAppear:控制器的view完全显示
  * viewWillDisappear：控制器的view即将消失的时候 `这期间系统也会调用viewWillLayoutSubviews 、viewDidLayoutSubviews 两个方法`
  * viewDidDisappear：控制器的view完全消失的时候
* _**多个viewControllers跳转**_
  * 当我们点击push的时候首先会加载下一个界面然后才会调用界面的消失方法
  * initWithCoder:\(NSCoder \*\)aDecoder：`ViewController2` \(如果用xib创建的情况下）
  * loadView：`ViewController2`
  * viewDidLoad：`ViewController2`
  * viewWillDisappear：**ViewController1** 将要消失
  * viewWillAppear：`ViewController2` 将要出现
  * viewWillLayoutSubviews `ViewController2`
  * viewDidLayoutSubviews `ViewController2`
  * viewWillLayoutSubviews:**ViewController1**
  * viewDidLayoutSubviews:**ViewController1**
  * viewDidDisappear:**ViewController1** 完全消失
  * viewDidAppear:`ViewController2` 完全出现
* 小结： -整个控制器声明周期： viewDidLoad -&gt; viewWillAppear -&gt; viewWillLayoutSubviews -&gt; viewDidLayoutSubviews -&gt; viewDidAppear -&gt; viewWillDisappear -&gt; viewDidDisappear

