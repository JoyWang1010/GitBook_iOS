# OperationQueue

NSOperation是基于GCD实现，封装了一些更为简单实用的功能，因为GCD的线程生命周期是自动管理，所以NSOperation也是自动管理。NSOperation配合NSOperationQueue也可以实现多线程。

```text
实现步骤
　　第1步：将一个操作封装到NSOperation对象中
　　第2步：将NSOperation对象放入NSOperationQueue队列
　　第3步：NSOperationQueue自动取出队列中的NSOperation对象放到一条线程中执行

具体实现
　　在swift中的实现方式分2种（oc还多了一个NSInvocationOperation，并且在oc中NSOperation是个抽象类）：
　　1.NSBlockOperation
　　2.自定义子类继承NSOperation
```

**1.NSOoperation常用操作，创建队列，设置最大并发数** 

```text
        //创建队列
        let queue = NSOperationQueue()
        //设置最大并发数
        queue.maxConcurrentOperationCount=2
        
        //创建operation
        let operation = NSBlockOperation { () -> Void in
            print("doSomething1 \(NSThread.currentThread())")
        }
        
        //当operation有多个任务的时候会自动分配多个线程**并发执行**,
        //如果只有一个任务，会自动在主线程同步执行
        //operation.start()
        
        operation.addExecutionBlock { () -> Void in
            print("doSomething2 \(NSThread.currentThread())")
        }
        
        operation.addExecutionBlock { () -> Void in
            print("doSomething3 \(NSThread.currentThread())")
        }
        
        let operation2=NSBlockOperation { () -> Void in
            print("doSomething4 \(NSThread.currentThread())")
        }
        
        //添加到队列中的operation将自动异步执行
        queue.addOperation(operation)
        queue.addOperation(operation2)
        
        //还有一种方式，直接将operation的blcok直接加入到队列
        queue.addOperationWithBlock { () -> Void in
            print("doSomething5 block \(NSThread.currentThread())")
        }
        queue.addOperationWithBlock { () -> Void in
            print("doSomething6 block \(NSThread.currentThread())")
        }
        queue.addOperationWithBlock { () -> Void in
            print("doSomething7 block \(NSThread.currentThread())")
        }
        queue.addOperationWithBlock { () -> Void in
            print("doSomething8 block \(NSThread.currentThread())")
        }
```

**2.NSOperation操作依赖，可设置一个操作在另一个操作完成后在执行**

```text
        //创建队列
        let queue = NSOperationQueue()
        
        let operationA = NSBlockOperation { () -> Void in
            print("print A")
        }
        let operationB = NSBlockOperation { () -> Void in
            print("print B")
        }
        let operationC = NSBlockOperation { () -> Void in
            print("print C")
        }
        
        //B等A执行完才执行
        operationB.addDependency(operationA)
        //C等B执行完才执行
        operationC.addDependency(operationB)
        
        
        queue.addOperation(operationA)
        queue.addOperation(operationB)
        queue.addOperation(operationC)
```

**3.NSOperation操作监听，一个操作完成后调用另一个操作：**

```text
   func operationCompletion(){
        //创建队列
        let queue = NSOperationQueue() 
        let operation = NSBlockOperation { () -> Void in
            print("print A")
        } 
        operation.completionBlock = doSomething
        queue.addOperation(operation)
    }
    func doSomething(){
        print("doSomething")
    }
```

**4.NSOperation线程通信，NSOperationQueue.mainQueue。**

```text
        //创建队列
        let queue = NSOperationQueue()
        queue.addOperationWithBlock { () -> Void in
            print("子线程  \(NSThread.currentThread())")
            NSOperationQueue.mainQueue().addOperationWithBlock({ () -> Void in
                print("主线程  \(NSThread.currentThread())")
            })
        }
```

**注意：**

1.在使用队列任务的时候，内存警告的时候可使用队列的cancelAllOperations函数取消所有操作，注意一旦取消不可恢复。亦可设置队列的suspended属性暂停和恢复队列。

2.在设置操作依赖的时候不能设置循环依赖。  


\*\*\*\*

\*\*\*\*

