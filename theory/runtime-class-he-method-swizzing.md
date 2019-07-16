# Runtime 、Class 和 Method Swizzing

## 1、OOP（面向对象编程）

要谈OOP，首先要了解POP（面向过程编程）。C语言就是面向过程编程，C语言在编译时只需要通过编译器把代码里的函数名称转化成相对的内存地址，把调用函数的语句转换成一个跳转指令。在程序运行的时候使调用语句可以正确的跳转到内存地址。 但是由于面向过程编程语言复用低、不易维护、不易扩展，所以在此基础上发展出了面向对象编程。面向对象语言包括C++、C\#、Java、Objective-C等。面向对象语言是在编译器的基础上加了一层类处理机制，把函数限制在它的类环境里，每次请求一个函数调用时，会先找到它的对象，其类型、返回值、参数等确定后在跳转到需要的函数。这样极大的增加了函数的灵活性。

## 2、RUNTIME（运行时）

OC作为面向对象语言，把类的实现部分抽象出来，做成了一套完整的**运行时**阶段检测环境，形成**动态开发语言**。OC是一门动态语言，是C语言的扩展，代码在编译之前会经过**运行时系统**的一系列处理从而生成可供编译器识别运行的C语言或汇编代码，而运行时系统的Runtime库基本上是用C和汇编写的，正是这个库使得OC具有面向对象和动态的特性。

**Runtime库主要做了以下几件事**：

1、**封装**：在这个库中，对象可以用C语言中的结构体表示，而方法可以用C函数来实现，另外再加上了一些额外的特性。这些结构体和函数被runtime函数封装后，我们就可以在程序运行时创建，检查，修改类、对象和它们的方法了。

2、**找出方法的最终执行代码**：当程序执行`[object doSomething]`时，会向消息接收者object发送一条消息doSomething，runtime会根据消息接收者是否能响应该消息而做出不同的反应。

## 3、RUNTIME的调用流程

在面向过程的语言中，方法调用其实就是跳到内存中的某一个点并开始执行一段代码。而在Objective-C中，`[obj method]`语法并不会执行method这个方法。它只是在运行时给obj对象发送一条叫method的消息。这个消息，可能会由obj或其父类执行，也有可能假装没收到消息直接丢弃，还可以通过Method Swizzling把method方法替换成其他方法，而这些动作都是在程序**运行时**决定的。

**RUNTIME的调用流程**：

**1、编译器会先将代码`[obj method]`翻译为`objc_megSend(obj,`**[**`@selector`**](https://github.com/selector)**`(method))`函数去执行。**

**2、在`objc_msgSend()`函数中，首先通过obj的isa指针找到对象obj对应的类class。**

**3、在class中会先去cache中通过SEL（SEL就是对方法的一种包装。它包含了相应的方法地址，找到方法地址就可以调用方法。）查找对应函数method（cache中method列表是以SEL为key通过hash表来储存的，这样能提高函数查找速度），若cache中未找到。再去class中的消息列表methodList中查找，若methodlist中未找到，则取superClass中查找。若能找到，则将method加入到cache中，以便下次查找，并通过method中的函数指针跳转到对应的函数中去执行。**

补充：

&gt;在oc中，每一个对象都有一个isa指针变量，这个指针指向的是对象的类，我们可以通过isa指针访问一个对象的类

&gt;方法都保存在类的消息列表中，这个列表其实是一个字典，key是selector，value是IMP（imp是一个指针类型，指向方法的实现），并且selector和IMP之间的关系是在运行时才决定的，而不是编译时。如此们就可以做出一些特别事情来。

## 4、OC类的继承关系

面向对象的三个基本特征是：**封装**、**继承**、**多态**。类的实现实际上体现的是封装特性。下面说说OC类的继承关系：

![&#x7C7B;&#x7684;&#x7EE7;&#x627F;](../.gitbook/assets/1893691773.png)

**OC类的继承关系有以下几个重点**：

**每一个对象本质上都是一个类的实例。其中类定义了成员变量和成员方法的列表。对象通过对象的isa指针指向类。**

**每一个类本质上都是一个对象，类其实是元类（meteClass）的实例。元类定义了类方法的列表。类通过类的isa指针指向元类。**

**所有的元类最终继承一个根元类，根元类isa指针指向本身，形成一个封闭的内循环。**

## 5、类与对象基础数据结构 Class

我们知道OC是C的扩展，OC语言代码实际上在对C语言的封装的基础上形成的面向对象特性。这里有必要了解一下OC的类与对象基础数据结构。

OC的类是由Class类型来表示的，它实际上是一个指向objc\_class结构体的指针。它在objc/objc.h的定义如下：

`typedef struct objc_class *Class;`

在objc/runtime.h中objc\_class结构体的定义如下：

```c
struct objc_class {

    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;

\#if !__OBJC2__

    Class _Nullable super_class                              OBJC2_UNAVAILABLE;

    const char * _Nonnull name                               OBJC2_UNAVAILABLE;

    long version                                             OBJC2_UNAVAILABLE;

    long info                                                OBJC2_UNAVAILABLE;

    long instance_size                                       OBJC2_UNAVAILABLE;

    struct objc_ivar_list * _Nullable ivars                  OBJC2_UNAVAILABLE;

    struct objc_method_list * _Nullable * _Nullable methodLists                    OBJC2_UNAVAILABLE;

    struct objc_cache * _Nonnull cache                       OBJC2_UNAVAILABLE;

    struct objc_protocol_list * _Nullable protocols          OBJC2_UNAVAILABLE;

\#endif

}
```

在这个定义中，下面几个字段是我们感兴趣的

* **isa**：需要注意的是在Objective-C中，所有的类自身也是一个对象，这个对象的Class里面也有一个isa指针，它指向metaClass\(元类\)。
* **super\_class**：指向该类的父类，如果该类已经是最顶层的根类\(如NSObject或NSProxy\)，则super\_class为NULL。
* **cache**：用于缓存最近使用的方法。一个接收者对象接收到一个消息时，它会根据isa指针去查找能够响应这个消息的对象。在实际使用中，这个对象只有一部分方法是常用的，很多方法其实很少用或者根本用不上。这种情况下，如果每次消息来时，我们都是methodLists中遍历一遍，性能势必很差。这时，cache就派上用场了。在我们每次调用过一个方法后，这个方法就会被缓存到cache列表中，下次调用的时候runtime就会优先去cache中查找，如果cache没有，才去methodLists中查找方法。这样，对于那些经常用到的方法的调用，但提高了调用的效率。
* **version**：我们可以使用这个字段来提供类的版本信息。这对于对象的序列化非常有用，它可是让我们识别出不同类定义版本中实例变量布局的改变。
* **objc\_ivar\_list**：对象初始化数据都是放在堆中的，对象的成员变量指针都在objc\_ivar\_list \* ivars中，对象通过指针找到对应的内存取数据。
* **objc\_method\_list**：本质是一个有 objc\_method 元素的可变长度的数组。一个 objc\_method结构体中有函数名，也就是SEL，有表示函数类型的字符串，以及函数的实现IMP。

从这些定义中可以看出调用一个方法\[objc\_foo\]发送一条消息也就是 `objc_msgSend` 做了什么事。举 `objc_msgSend(obj, foo)` 这个例子来说：

1. 首先，通过 obj 的 isa 指针找到它的 class ;
2. 在 class 的 method list 找 foo ;
3. 如果 class 中没到 foo，继续往它的 superclass 中找 ;
4. 一旦找到 foo 这个函数，就去执行它的实现IMP .

但这种实现有个问题，效率低。但一个 class 往往只有 20% 的函数会被经常调用，可能占总调用次数的 80% 。每个消息都需要遍历一次 `objc_method_list` 并不合理。如果把经常被调用的函数缓存下来，那可以大大提高函数查询的效率。这也就是 `objc_class` 中另一个重要成员 `objc_cache` 做的事情 - 再找到 foo 之后，把 foo 的 `method_name` 作为 key ，`method_imp` 作为 value 给存起来。当再次收到 foo 消息的时候，可以直接在 cache 里找到，避免去遍历 `objc_method_list`。

## 6、Method Swizzing

Method Swizzling 利用 Runtime 特性把一个方法的实现与另一个方法的实现进行替换。  
每个类里都有一个 Dispatch Table ，将方法的名字（SEL）跟方法的实现（IMP，指向 C 函数的指针）一一对应。Swizzle 一个方法其实就是在程序运行时在 Dispatch Table 里做点改动，让这个方法的名字（SEL）对应到另个 IMP 。

首先定义一个类别，添加将要 Swizzled 的方法：

```objectivec
@implementation UIViewController (Logging)
- (void)swizzled_viewDidAppear:(BOOL)animated
{    // call original implementation
    [self swizzled_viewDidAppear:animated];
    // Logging
    [Logging logWithEventName:NSStringFromClass([self class])];
}
```

代码看起来可能有点奇怪，像递归不是么。当然不会是递归，因为在 runtime 的时候，函数实现已经被交换了。调用 `viewDidAppear:` 会调用你实现的 `swizzled_viewDidAppear:`，而在 `swizzled_viewDidAppear:` 里调用 swizzled\_viewDidAppear: 实际上调用的是原来的 `viewDidAppear:` 。

接下来实现 swizzle 的方法 ：

```objectivec
@implementation UIViewController (Logging)
void swizzleMethod(Class class, SEL originalSelector, SEL swizzledSelector)  
{    
// the method might not exist in the class, but in its superclass
    Method originalMethod = class_getInstanceMethod(class, originalSelector);
    Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);    
// class_addMethod will fail if original method already exists
    BOOL didAddMethod = class_addMethod(class, originalSelector, 
method_getImplementation(swizzledMethod), method_getTypeEncoding(swizzledMethod));    
// the method doesn’t exist and we just added one
    if (didAddMethod) {
        class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod));
    } 
    else {
        method_exchangeImplementations(originalMethod, swizzledMethod);
    }
}
```

这里唯一可能需要解释的是 class\_addMethod 。要先尝试添加原 selector 是为了做一层保护，因为如果这个类没有实现 originalSelector ，但其父类实现了，那 class\_getInstanceMethod 会返回父类的方法。这样 method\_exchangeImplementations 替换的是父类的那个方法，这当然不是你想要的。所以我们先尝试添加 orginalSelector ，如果已经存在，再用 method\_exchangeImplementations 把原方法的实现跟新的方法实现给交换掉。

最后，我们只需要确保在程序启动的时候调用 swizzleMethod 方法。比如，我们可以在之前 UIViewController 的 Logging 类别里添加 +load: 方法，然后在 +load: 里把 viewDidAppear 给替换掉：

```objectivec
@implementation UIViewController (Logging)
+ (void)load
{
    swizzleMethod([self class], @selector(viewDidAppear:),
 @selector(swizzled_viewDidAppear:));
}
```

一般情况下，类别里的方法会重写掉主类里相同命名的方法。如果有两个类别实现了相同命名的方法，只有一个方法会被调用。但 +load: 是个特例，当一个类被读到内存的时候， runtime 会给这个类及它的每一个类别都发送一个 +load: 消息。

其实，这里还可以更简化点：直接用新的 IMP 取代原 IMP ，而不是替换。只需要有全局的函数指针指向原 IMP 就可以。

```objectivec
void (gOriginalViewDidAppear)(id, SEL, BOOL);
void newViewDidAppear(UIViewController *self, SEL _cmd, BOOL animated)  
{    // call original implementation
    gOriginalViewDidAppear(self, _cmd, animated);    // Logging
    [Logging logWithEventName:NSStringFromClass([self class])];
}

+ (void)load
{
    Method originalMethod = class_getInstanceMethod(self, @selector(viewDidAppear:));
    gOriginalViewDidAppear = (void *)method_getImplementation(originalMethod);    

    if(!class_addMethod(self, @selector(viewDidAppear:), (IMP) newViewDidAppear, method_getTypeEncoding(originalMethod))) {
        method_setImplementation(originalMethod, (IMP) newViewDidAppear);
    }
}
```

像上面例子用 Method Swizzling 动态给指定的方法添加代码，可以实现AOP[Aspect Oriented Programming面向切面编程](http://en.wikipedia.org/wiki/Aspect-oriented_programming)

[Aspects](https://github.com/steipete/Aspects)就是一个不错的 AOP 库，封装了 Runtime ， Method Swizzling 这些黑色技巧。

