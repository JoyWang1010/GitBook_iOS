# 控制器生命周期

alloc：创建对象，分配空间

 init \(xib和非xib用initWithNibName、stroyBoard用initWithCoder\) ：初始化对象，初始化数据 

awakeFromNib：\(若控制器有关联xib才调用这方法）

 loadView：优先从nib载入控制器视图 ，其次代码

 viewDidLoad：载入完成，可以进行自定义数据以及动态创建其他控件。

 viewWillAppear：视图将出现在屏幕之前，马上这个视图就会被展现在屏幕上了 

viewWillLayoutSubviews：控制器的view将要布局子控件 

viewDidLayoutSubviews：控制器的view布局子控件完成 

//这期间系统可能会多次调viewWillLayoutSubviews 、 viewDidLayoutSubviews 俩个方法 

viewDidAppear：视图已在屏幕上渲染完成 

viewWillDisappear：视图将被从屏幕上移除之前执行 

viewDidDisappear：视图已经被从屏幕上移除，用户看不到这个视图了 

dealloc：视图被销毁，此处需要对你在init和viewDidLoad中创建的对象进行释放 

didReceiveMemoryWarning:收到内存警告

