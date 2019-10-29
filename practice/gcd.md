# GCD

**1. GCD 简介**

GCD是苹果开发的多线程编程的解决方案，通过简单的API就可以实现创建新线程去执行我们需要执行的任务，不需要我们手动地创建和管理线程，只需要创建队列和相应的函数配合使用就行。它的API包含在libdispatch库中。

GCD全称Grand Central Dispatch，是Apple提供的一套底层API，提供了一种新的方法来进行并发程序编写。GCD有点像NSOperationQueue，但它比NSOpertionQueue更底层更高效，并且它不属于Cocoa框架。GCD的API很大程度上基于block，当然，GCD也可以脱离block来使用，比如使用传统C机制提供函数指针和上下文指针。实践证明，当配合block使用时，GCD非常简单易用且能发挥其最大能力。

**2. GCD 的优势**

* GCD是苹果公司为多核的并行运算提出的解决方案
* GCD会自动利用更多的CPU内核（比如双核、四核）
* GCD会自动管理线程的生命周期（创建线程、调度任务、销毁线程）
* 程序员只需要告诉GCD想要执行什么任务，不需要编写任何线程管理代码

**3. GCD 任务和队列**

**任务（Task）:** 就是执行操作的意思，换句话说就是你在线程中执行的那段代码。在 GCD 中是放在 block 中的。执行任务有两种方式：**同步执行（sync）**和 **异步执行（async）**。两者的主要区别是：**是否等待队列的任务执行结束，以及是否具备开启新线程的能力**。

* 同步执行（sync）：
* 同步添加任务到指定的队列中，在添加的任务执行结束之前，会一直等待，直到队列里面的任务完成之后再继续执行。
* 只能在当前线程中执行任务，不具备开启新线程的能力。
* 异步执行（async）：
* 异步添加任务到指定的队列中，它不会做任何等待，可以继续执行任务。
* 可以在新的线程中执行任务，具备开启新线程的能力\(但是并不一定开启新线程, 跟任务所指定的队列类型有关\)。

**队列（Queue）** 这里的队列指执行任务的等待队列，即用来存放任务的队列。队列是一种特殊的线性表，采用 FIFO（先进先出）的原则，即新任务总是被插入到队列的末尾，而读取任务的时候总是从队列的头部开始读取。每读取一个任务，则从队列中释放一个任务。

**GCD中队列的种类**

* **串行队列（Serial Dispatch Queue）:** 每次只有一个任务被执行。让任务一个接着一个地执行。（只开启一个线程，一个任务执行完毕后，再执行下一个任务）
* **并发队列（Concurrent Dispatch Queue）:** 可以让多个任务并发（同时）执行。（可以开启多个线程，并且同时执行任务）, 并发队列的并发功能只有在异步（dispatch\_async）函数下才有效

**4.GCD 的简单使用**

* 创建一个队列（串行队列或并发队列）
* 将任务追加到任务的等待队列中，然后系统就会根据任务类型执行任务（同步执行或异步执行）

**GCD创建队列**

* 主队列\(串行队列\)

  ```text
    let mainQueue = DispatchQueue.main
  全局并行队列
  ```

* ```text
    let globalQueue = DispatchQueue.global(qos: .default)
  创建串行队列
  ```
* ```text
    let serialQueue = DispatchQueue(label: "vip.mybadge")
  创建并行队列
  ```
* ```text
    let concurQueue = DispatchQueue(label: "vip.mybadge", attributes: .concurrent)
  ```

**执行任务**

```text
func task(i: Int) {
    print("\(i) thread = \(Thread.current)")
}
	
for i in 0..<100 {
    serialQueue.async {
        task(i: i)
    }
}
```

输出结果

```text
...
11 thread = <NSThread: 0x60000373fa00>{number = 5, name = (null)}
12 thread = <NSThread: 0x60000373fa00>{number = 5, name = (null)}
13 thread = <NSThread: 0x60000373fa00>{number = 5, name = (null)}
14 thread = <NSThread: 0x60000373fa00>{number = 5, name = (null)}
15 thread = <NSThread: 0x60000373fa00>{number = 5, name = (null)}
16 thread = <NSThread: 0x60000373fa00>{number = 5, name = (null)}
17 thread = <NSThread: 0x60000373fa00>{number = 5, name = (null)}
18 thread = <NSThread: 0x60000373fa00>{number = 5, name = (null)}
19 thread = <NSThread: 0x60000373fa00>{number = 5, name = (null)}
...
```

可以发现在串行队列中， 等待队列的任务执行结束，不具备开启新线程的能力

```text
func task(i: Int) {
    print("\(i) thread = \(Thread.current)")
}
	
for i in 0..<100 {
    globalQueue.async {
        task(i: i)
    }
}
```

输出结果

```text
...
75 thread = <NSThread: 0x600002aef1c0>{number = 5, name = (null)}
76 thread = <NSThread: 0x600002aef1c0>{number = 5, name = (null)}
77 thread = <NSThread: 0x600002aef480>{number = 6, name = (null)}
78 thread = <NSThread: 0x600002aef1c0>{number = 5, name = (null)}
79 thread = <NSThread: 0x600002aef480>{number = 6, name = (null)}
80 thread = <NSThread: 0x600002af1280>{number = 8, name = (null)}
81 thread = <NSThread: 0x600002af16c0>{number = 9, name = (null)}
82 thread = <NSThread: 0x600002af1400>{number = 10, name = (null)}
83 thread = <NSThread: 0x600002af1340>{number = 11, name = (null)}
84 thread = <NSThread: 0x600002af1380>{number = 12, name = (null)}
...
```

可以发现在并行队列中， 不等待队列的任务执行结束，具备开启新线程的能力

**5. DispatchGroup**

如果想等到所有的队列的任务执行完毕再进行后序操作时，可以使用**DispatchGroup**来完成。

```text
let group = DispatchGroup()
for i in 0..<5 {
    print("任务\(i+1)下载中...")
    DispatchQueue.global().async(group: group) {
        Thread.sleep(forTimeInterval: 1)
        print("任务\(i+1)下载完成")
    }
}
group.notify(queue: DispatchQueue.main) {
    print("任务都下载完成...去更新UI")
}
```

执行结果

```text
任务1下载中...
任务2下载中...
任务3下载中...
任务4下载中...
任务5下载中...
任务1下载完成
任务3下载完成
任务5下载完成
任务4下载完成
任务2下载完成
任务都下载完成...去更新UI
```

**6. DispatchWorkItem**

Swift3新增的类，可以通过此类设置队列执行的任务。相当于把原来GCD中闭包的代码封装到了这里, 看一个例子:

```text
let workItem = DispatchWorkItem {
    for i in 0..<10 {
        print(i)
    }
}
DispatchQueue.global().async(execute: workItem)
```

看看他的初始化方法

```text
init(qos: DispatchQoS = default, flags: DispatchWorkItemFlags = default,
block: @escaping () -> Void)
```

从初始化方法可以看出，DispatchWorkItem也可以设置优先级，另外还有个参数DispatchWorkItemFlags，来看看DispatchWorkItemFlags的内部组成:

```text
public struct DispatchWorkItemFlags : OptionSet, RawRepresentable {
	// 相当于之前的栅栏函数
    public static let barrier: DispatchWorkItemFlags 

    public static let detached: DispatchWorkItemFlags

    public static let assignCurrentContext: DispatchWorkItemFlags
	// 没有优先级
    public static let noQoS: DispatchWorkItemFlags 
	// 继承Queue的优先级
    public static let inheritQoS: DispatchWorkItemFlags		 
    // 覆盖Queue的优先级
    public static let enforceQoS: DispatchWorkItemFlags
}
```

**7. Dispatch barrier**

可以理解为隔离，还是以文件读写为例，在读取文件时，可以异步访问，但是如果突然出现了异步写入操作，我们想要达到的效果是在进行写入操作的时候，使读取操作暂停，直到写入操作结束，再继续进行读取操作，以保证读取操作获取的是文件的最新内容。

先看看**不使用barrier**的例子

```text
let concurQueue = DispatchQueue(label: "vip.mybadge", attributes: .concurrent)

struct File {
    var content = ""
}

var file = File()
file.content = "This is a file"

let writeFileWorkItem = DispatchWorkItem {
    file.content = "This file has been modified."
    Thread.sleep(forTimeInterval: 1)
    print("write file")
}

let readFileWorkItem = DispatchWorkItem {
    Thread.sleep(forTimeInterval: 1)
    print("file.content=\(file.content)")
}

for _ in 0..<3 {
    concurQueue.async(execute: readFileWorkItem)
}
concurQueue.async(execute: writeFileWorkItem)
for _ in 0..<3 {
    concurQueue.async(execute: readFileWorkItem)
}
```

输出结果

```text
file.content=This file has been modified.
write file
file.content=This file has been modified.
file.content=This file has been modified.
file.content=This file has been modified.
file.content=This file has been modified.
file.content=This file has been modified.
```

我们期望的结果是，在写文件之前，打印 "This is a file", 写文件之后打印的是"This file has been modified.", 上面结果显然不是我们想要的。 看一下使用barrier的效果

```text
let concurQueue = DispatchQueue(label: "vip.mybadge", attributes: .concurrent)
struct File {
    var content = ""
}

var file = File()
file.content = "This is a file"

let writeFileWorkItem = DispatchWorkItem(flags: .barrier) {
    file.content = "This file has been modified."
    Thread.sleep(forTimeInterval: 1)
    print("white file")
}
	
let readFileWorkItem = DispatchWorkItem {
    Thread.sleep(forTimeInterval: 1)
    print("file.content=\(file.content)")
}

for _ in 0..<3 {
    concurQueue.async(execute: readFileWorkItem)
}
concurQueue.async(execute: writeFileWorkItem)
for _ in 0..<3 {
    concurQueue.async(execute: readFileWorkItem)
}
```

输出结果

```text
file.content=This is a file.
file.content=This is a file.
file.content=This is a file.
write file
file.content=This file has been modified.
file.content=This file has been modified.
file.content=This file has been modified.
```

结果符合预期的想法，**barrier**主要用于读写隔离，以保证写入的时候，不被读取。

**8. DispatchSemaphore**

DispatchSemaphore中的信号量,可以解决资源抢占的问题,支持信号的通知和等待.每当发送一个信号通知,则信号量+1;每当发送一个等待信号时信号量-1,如果信号量为0则信号会处于等待状态.直到信号量大于0开始执行.所以我们一般将DispatchSemaphore的value设置为1.

**DispatchSemaphore 线程同步**

**线程同步**: 可理解为**线程A**和**线程B**一块配合, **A**执行到一定程度时要依靠**B**的某个结果, 于是停下来, 示意**B**运行; **B**依言执行, 再将结果给**A**; **A**再继续操作.

```text
/// 信号量的线程同步.
func semaphoreSync() {
    var number = 0
    let semaphoreSignal = DispatchSemaphore(value: 0)
    let globalQueue = DispatchQueue.global()
    
    let workItem = DispatchWorkItem {
        Thread.sleep(forTimeInterval: 1)
        print("change number, thread=\(Thread.current)")
        number = 100
        semaphoreSignal.signal()
    }
    
    print("semaphore begin")
    print("number = \(number), thread=\(Thread.current)")
    globalQueue.async(execute: workItem)
    semaphoreSignal.wait()
    print("number = \(number)")
    print("semaphore end")
}

semaphoreSync()
```

输出

```text
semaphore begin
number = 0, thread=<NSThread: 0x6000007ca900>{number = 1, name = main}
change number, thread=<NSThread: 0x6000007e8180>{number = 5, name = (null)}
number = 100
semaphore end
```

**semaphore end** 是在执行完 number = 100; 之后才打印的。而且输出结果 number 为 100。

* 这是因为异步执行不会做任何等待，可以继续执行任务。
* 异步执行将workItem追加到队列之后，不做等待，接着去执行\*\*semaphoreSignal.wait\(\)\*\*方法。
* 此时 semaphore == 0，当前线程进入等待状态。
* 然后，workItem开始执行。workItem执行到\*\*semaphoreSignal.signal\(\)\*\*之后，
* 信号量+1，此时 semaphore == 1，\*\*semaphoreSignal.wait\(\)\*\*方法使总信号量减1，正在被阻塞的线程（主线程）恢复继续执行。
* 最后打印number = 100，semaphore---end,。

这样就实现了线程同步，将异步执行任务转换为同步执行任务。

**Dispatch Semaphore 线程安全和线程同步（为线程加锁）**

* **线程安全**：如果你的代码所在的进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。否则就是 **非线程安全**的。

下面，我们模拟火车票售卖的方式，实现 NSThread 线程安全和解决线程同步问题。

场景：总共有10张火车票，有两个售卖火车票的窗口，一个是北京火车票售卖窗口，另一个是上海火车票售卖窗口。两个窗口同时售卖火车票，卖完为止。

**非线程安全（不使用semaphore）**

先来看看不考虑线程安全的代码

```text
class SaleTicketNotSafe {
    private var ticketSurplusCount = 0
    private let semaphoreSignal = DispatchSemaphore(value: 1)
    private let serialQueue = DispatchQueue(label: "vip.mybadge.dispatch")
    private let serialQueue2 = DispatchQueue(label: "vip.mybadge.dispatch")
    
    init(ticketSurplusCount: Int) {
        self.ticketSurplusCount = ticketSurplusCount
    }
    
    func startSaleNotSave() {
        print("current thread=\(Thread.current)")
        serialQueue.async { [weak self] in
            self?.saleTicketNotSafe()
        }
        serialQueue2.async { [weak self] in
            self?.saleTicketNotSafe()
        }
    }
    
    private func saleTicketNotSafe() {
        while true {
            if ticketSurplusCount > 0 {
                ticketSurplusCount -= 1
                print("剩余票数\(ticketSurplusCount), 窗口:\(Thread.current)")
                Thread.sleep(forTimeInterval: 1)
            } else {
                print("所有票都售完了")
                break
            }
        }
    }
}

let saleTicket = SaleTicketNotSafe(ticketSurplusCount: 10)
saleTicket.startSaleNotSave()
```

输出结果

```text
开始售票  thread=<NSThread: 0x600003802900>{number = 1, name = main}
剩余票数9, 窗口:<NSThread: 0x600003824c00>{number = 6, name = (null)}
剩余票数8, 窗口:<NSThread: 0x6000038157c0>{number = 4, name = (null)}
剩余票数6, 窗口:<NSThread: 0x6000038157c0>{number = 4, name = (null)}
剩余票数7, 窗口:<NSThread: 0x600003824c00>{number = 6, name = (null)}
剩余票数4, 窗口:<NSThread: 0x6000038157c0>{number = 4, name = (null)}
剩余票数4, 窗口:<NSThread: 0x600003824c00>{number = 6, name = (null)}
剩余票数3, 窗口:<NSThread: 0x6000038157c0>{number = 4, name = (null)}
剩余票数2, 窗口:<NSThread: 0x600003824c00>{number = 6, name = (null)}
剩余票数1, 窗口:<NSThread: 0x6000038157c0>{number = 4, name = (null)}
剩余票数0, 窗口:<NSThread: 0x600003824c00>{number = 6, name = (null)}
所有票都售完了
所有票都售完了
```

**线程安全 \(使用 semaphore 加锁）**

线程安全的代码

```text
class SaleTicketSafe {
    private var ticketSurplusCount = 0
    private let semaphoreSignal = DispatchSemaphore(value: 1)
    private let serialQueue = DispatchQueue(label: "vip.mybadge.dispatch")
    private let serialQueue2 = DispatchQueue(label: "vip.mybadge.dispatch")
    init(ticketSurplusCount: Int) {
        self.ticketSurplusCount = ticketSurplusCount
    }
   
    func startSaleSave() {
        print("开始售票 thread=\(Thread.current)")
        serialQueue.async { [weak self] in
            self?.saleTicketSafe()
        }
        serialQueue2.async { [weak self] in
            self?.saleTicketSafe()
        }
    }
    private func saleTicketSafe() {
        while true {
            semaphoreSignal.wait()
            if ticketSurplusCount > 0 {
                ticketSurplusCount -= 1
                print("剩余票数\(ticketSurplusCount), 窗口:\(Thread.current)")
                Thread.sleep(forTimeInterval: 1)
            } else {
                semaphoreSignal.signal()
                print("所有票都售完了")
                break
            }
            semaphoreSignal.signal()
        }
    }
}

let saleTicket = SaleTicketSafe(ticketSurplusCount: 10)
saleTicket.startSaleSave()
```

输出结果

```text
开始售票 thread=<NSThread: 0x600001ac6900>{number = 1, name = main}
剩余票数9, 窗口:<NSThread: 0x600001ad4b80>{number = 4, name = (null)}
剩余票数8, 窗口:<NSThread: 0x600001ad8640>{number = 6, name = (null)}
剩余票数7, 窗口:<NSThread: 0x600001ad4b80>{number = 4, name = (null)}
剩余票数6, 窗口:<NSThread: 0x600001ad8640>{number = 6, name = (null)}
剩余票数5, 窗口:<NSThread: 0x600001ad4b80>{number = 4, name = (null)}
剩余票数4, 窗口:<NSThread: 0x600001ad8640>{number = 6, name = (null)}
剩余票数3, 窗口:<NSThread: 0x600001ad4b80>{number = 4, name = (null)}
剩余票数2, 窗口:<NSThread: 0x600001ad8640>{number = 6, name = (null)}
剩余票数1, 窗口:<NSThread: 0x600001ad4b80>{number = 4, name = (null)}
剩余票数0, 窗口:<NSThread: 0x600001ad8640>{number = 6, name = (null)}
所有票都售完了
所有票都售完了
```

可以看出，在考虑了线程安全的情况下，使用 DispatchSemaphore 机制之后，得到的票数是正确的，没有出现混乱的情况。我们也就解决了线程安全与线程同步的问题。  


