# Swift编码规范

### 一、说明

* 本代码规范主要包含可以快速执行的内容，还会不断完善和修正，但已存在的部分不会有大的修改，主要是增变化主要在于增加新的内容。
* 本代码规范默认为必须执行，只是建议而非强制执行的会用“（不强制）”文字标出。
* 在新版本迭代开发过程，新加代码应严格按照本规范执行，在对之前文件进行修改时，也要积极主动的根据目前代码规范对文件进行局部重构，提高代码质量和可读性。

### 二、代码格式化

#### 1.行数和列数的限制

**1.1 一个函数的长度不应该超过60行 （不强制）**

当函数长度超过80行后，应该将内部一些复杂的逻辑提炼出来，形成新的函数，然后调用之，微型重构工作也应该无处不在，而不是等项目完成后再来重构。

**1.2 每行代码长度建议不超过80 （不强制）**

建议每一行代码的长度超过80字符时做折行处理，处理时请以结构清晰为原则。通过“Xcode =&gt; Preferences =&gt; TextEditing =&gt; 勾选Show PageGuide / 输入80 =&gt; OK”来设置提醒。

**1.3 每个类原则上不超过600行 （不强制）**

一个类不应该将很多复杂的逻辑揉合到一起来实现，我们约定当文件超过600行时，要考虑将这个文件进行拆分，可以使用协议方法来分离功能代码。如果逻辑过于复杂，则应该考虑从设计上将一些内部可以独立的逻辑提炼出来，形成新的类，以减轻单一类的复杂度。

#### 2.代码分段的使用

推荐使用 `// MARK:` 将代码不同的处理段分隔开，分段名称的首母大写，如写中文也是可行，目的是可读性强，让别人一眼知道你的分段是干嘛的。方便在编辑器中快速定位到需要查看的代码。以下是一些建议：

**2.1 Lifecycle分段**

在UIViewController的子类实现文件中，建议添加一个 `// MARK: - LifeCycle`分段，该分段包含，init、loadView、viewDidLoad、viewWillAppear、viewDidAppear、viewWillDisappear、viewDidDisappear、viewWillLayoutSubviews、deinit等方法，并建议将deinit方法放到实现文件最前面。

建议将Lifecycle分段放到最前端，因为对一个类的阅读，往往是从init、viewDidLoad等方法开始的。示例：

```swift
    // MARK: - LifeCycle
    deinit {

    }

    override func viewDidLoad() {
        super.viewDidLoad()
    }
    ...
```

**2.2 代理扩展**

建议为每一个代理添加一个扩展，并且分段的名称应该是代理名称，代理名称务必正确拼写，这样可以通过command+单击来查看代理的定义，示例：

```swift
extension XXView: UITableViewDelegate, UITableViewDataSource {
    func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
        return 0
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {

    }

    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {

    }
}
```

**2.3 Events 分段**

为button的点击事件，geture的响应方法，KVO的回调，Notification的回调方法添加一个events分段，在该分段的方法中，调用对应业务的方法，对应业务的方法放在对应业务的分段。示例：

```swift
     // MARK: - Events
    func submit(_ sender:UIButton) {

    }
```

**2.4 业务分段**

在一个类中，为相对比较独立的业务，或者是实现相对独立功能的方法进行分段，同一个类中业务不宜过多，业务过多时就应该考虑分拆该类了。示例：

```swift
     // MARK: - LoginLogic
     ...
     // MARK: - RegisterLogic
     ...
```

在UIViewController的实现文件中建议的分段及顺序如下图（根据情况选择需要哪些分段，不需要总是包含以下所有分段）：

```swift
    // MARK: - LifeCycle

    // MARK: - Events

    // MARK: - Delegate

    // MARK: - xxx 按照业务划分
```

**2.4 属性分段**

在一个类中，属性一般分为跟view相关和跟数据相关属性，跟view相关一般是subviews这种，比如UILable，与数据相关一般是辅助性，比如 var isCheck: Bool = false 。示例：

```swift
    // MARK: - Data
    private var url: String = ""

    // MARK: - Subviews
    private lazy var webView: MQWKWebView = {
        let webView = MQWKWebView.init()
        webView.backgroundColor = UIColor.white
        return webView
    }()
```

### 三、代码规范

#### 一、命名

**1.类型**

* 类型名称（如 struct, enum, class, typedef, associatedtype, protocol 等）使用**大驼峰命名法**命名。
* 变量和常量则以**小驼峰命名法**命名。

  ```swift
  enum Planet {
    case mercury, venus, earth, mars, jupiter, saturn, uranus,neptune
  }

  struct Person {
    var mobileNo: String
  }
  ```

**2.协议**

根据苹果接口设计指导准则，协议名称用来描述一些东西是什么的时候是名词，例如：Collection, WidgetFactory。若协议名称用来描述能力应该以 -ing, -able, 或 -ible 结尾，例如：Equatable, Resizing。

**3.类前缀**

* Swift中类别\(类，结构体\)在编译时会把模块设置为默认的命名空间，但是为了不冲突，相关项目默认使用。

**委托**

在定义委托方法时，第一个未命名参数应是委托数据源。

正确代码：

```swift
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)

func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

错误代码：

```swift
func didSelectName(namePicker: NamePickerViewController, name: String)

func namePickerShouldReload() -> Bool
```

**泛型**

泛型类参数应具有描述性，遵守“大骆驼命名法”。如果一个参数名没有具体的含义，可以使用传统单大写字符，如T, U, 或V等。

正确代码：

```swift
struct Stack<Element>{ ... }
func writeTo <Target: OutputStream>(to Target: inout Target)
func swap(_ a: inout T, _ b: inout T)
```

错误代码：

```swift
struct Stack<T>{ ... }
func write<target: OutputStream> (to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

#### 二、代码逻辑

**1.self推断**

* 让编译器在所有允许的地方推断 self 。
* 在 init 中设置参数时显性地使用 self 。
* 在 non-escaping closures 中显性地使用 self 。
* 在避免命名冲突时使用 self 。

  例：

```swift
class BoardLocation  {
    let row: Int, column: Int
    init(row: Int, column: Int)  {
        self.row = row
        self.column = column
        let closure = {
            print(self.row)    
         }  
    }
}
```

**2.常量**

* 类常量应该在类型里声明为`static`。使用`static`修饰常量可以允许他们在被引用的时候不需要实例化类型。
* 除了单例以外，应尽量避免生成全局常量。
* 定义常量使用 **let** 关键字，定义变量使用 **var** 关键字， 如果变量的值未来不会发生变化要使用常量。

```swift
struct PhysicsModel {
    static var speedOfLightInAVacuum = 299_792_458
}

class Spaceship {
    static let topSpeed = PhysicsModel.speedOfLightInAVacuum
    var speed: Double

    func fullSpeedAhead() {
        speed = Spaceship.topSpeed
    }
}
```

**3.计算型类型属性**

* 当只需要继承 getter 方法时，返回简单的 Computed 属性即可。

正确代码：

```swift
class Example {
    var age: UInt32 {
        return arc4random()
    }
}
```

错误代码：

```swift
class Example {
    var age: UInt32 {
        get {
            return arc4random()
        }
    }
}
```

* 如果你在属性中添加了 set 或者 didSet ，那么你应该显示地提供 get 方法。

```swift
class Person {
    var age: Int {
        get {
            return Int(arc4random())
        }
        set {
            print("That's not your age.")
        }
    }
}
```

**4.单例**

Swift中单例很简单，Swift 的 runtime 会保证单例的创建并且采用线程安全的方式访问：

```swift
class ControversyManager {
    static let sharedInstance = ControversyManager()
    private init() {}
}
```

单例通常只需要访问"sharedInstance"的静态属性，除非你有不得已的原因去重命名它。注意，不要用静态函数或者全局函数去访问你的单例。

**5.错误处理**

可以使用`do/try/catch`机制，避免使用`try!`和`try?`

**6.可选值**

* 声明一个函数的某个参数可以为 nil 时，用`？`
* 当你确定某个变量在使用时已经确定不是 nil 时，在后面加`!`

**7.扩展声明周期**

用 \[weak self\] 和 guard let strongSelf = self else { return } 模式扩展生命周期。用 \[weak self\] 比 \[unowned self\] 更好。 正确代码：

```swift
resource.request().onComplete { 
    [weak self] response in
    guard let `self` = self else { return }
    let model = self.updateModel(response)
    self.updateUI(model)
}

resource.request().onComplete { [weak self] response in
  guard let strongSelf = self else { return }
  let model = strongSelf.updateModel(response)
  strongSelf.updateUI(model)
}
```

错误代码：

```swift
// might crash if self is released before response returns
resource.request().onComplete { [unowned self] response in
  let model = self.updateModel(response)
  self.updateUI(model)
}

// deallocate could happen between updating the model and updating UI
resource.request().onComplete { [weak self] response in
  let model = self?.updateModel(response)
  self?.updateUI(model)
}
```

### 三、代码结构

**1.注释**

* 使用Xcode8自带的注释功能，快捷键`Option+Command+/`
* 使用`// MARK:`分隔代码（类似于OC中的`#pragma mark`）

**2.缩进**

遵守Xcode内置的缩进格式

选中 `control + i` Xcode 自动缩进

**3.类型**

**1.类型声明时:紧跟属性，与类型之间加空格，等号两边须加一个空格**

正确代码：

```swift
let width = 120.0 
let height: Double = 120.0 
var title: String
```

错误代码：

```swift
let width=1.0
let height:Double  =  1.0
var title:String
```

**2.优先使用Swift原生类型，可以根据需要使用Objective-C提供的方法，因为Swift提供了到Objective-C的桥接。**

正确代码：

```swift
let width: Double = 120.0  // Double
let widthString = (width as NSNumber).stringValue // String
```

错误代码：

```swift
let width: NSNumber = 120.0 // NSNumber
let widthString: NSString=width.stringValue  // NSString
```

**4.协议一致性 \(不强制\)**

当一个对象要实现协议一致性时，推荐使用 **extension** 隔离协议中的方法集，这样让相关方法和协议集中显示在一起，也简化了类支持一个协议和实现相关方法的流程。 正确代码：

```swift
class MyViewcontroller: UIViewController {
      // 方法
} 

extension MyViewcontroller: UITableViewDataSource {
      // UITableViewDataSource 方法
}

extension MyViewcontroller: UIScrollViewDelegate {
      // UIScrollViewDelegate 方法
}
```

错误代码：

```swift
class MyViewcontroller: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
       // 所有的方法
}
```

**5.良好的类定义**

* 属性，变量，常量和参数等在声明定义时，其中: 符号后有空格，而: 符号前没有空格，比如x: Int, 和Circle: Shape
* 定义多个变量/数据结构时，出于相同的目的和上下文，可以定义在同一行。（不强制）
* 缩进 getter，setter 的定义和属性观察器的定义。
* 不需要添加`internal`这样的默认的修饰符。也不需要在重写一个方法时添加访问修饰符。
* 给那些不打算被继承的类使用`final`修饰符

```swift
final class Circle: Shape {
  var x: Int, y: Int
  var radius: Double
  var diameter: Double{
      get {
        returnradius * 2
     } 
     set {      
        radius=newValue/2
    }  
  }
  init(x: Int, y: Int, radius: Double) {
      self.x = x
      self.y = y
      self.radius = radius  
  }

  convenience init(x: Int, y: Int, diameter: Double) {
     self.init(x: x, y: y, radius: diameter/2)  
  }

  func describe() -> String{
    return"I am a circle at\(centerString())with an area of\(computeArea())"
  }
  override func computeArea() -> Double{
    return M_PI * radius * radius  
  }

  private func centerString()->String{
    return "(\(x),\(y))"
  }
}
```

**6.控制流程**

* 循环使用for-in表达式，而不使用 while 表达式。

  正确代码：

```swift
for _ in 0..<3 {
  print("Hello three times")
}

for(index, person) in attendeeList.enumerate() {
    print("\(person)is at position #\(index)")
}

for index in 0.stride(from: 0, to: items.count, by: 2) {
  print(index)
}
for index in (0...3).reverse() {
    print(index)
}
```

错误代码：

```swift
var i=0 
while i<3 {
  print("Hello three times") 
  i+=1
}

var i=0
while i<3 {
  let person = attendeeList[i]
  print("\(person)is at position #\(i)")  
  i+=1
}
```

* Switch 模块中不用显式使用break。

**7.黄金路径 （不强制）**

当编码遇到条件判断时，左边的距离是黄金路径或幸福路径，因为路径越短，速度越快。不要嵌套if循环，多个返回语句是可以的。guard 就为此而生的。 正确代码：

```swift
func computeFFT(context: Context?, inputData: InputData?)  throws -> Frequencies {
    guard let context = context else {throwFFTError.NoContext }

    guard let inputData = inputData else { throwFFTError.NoInputData }

    //计算frequencies
    return frequencies
}
```

错误代码：

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {
if let context = context {
    if let inputData = inputData {
           // 计算frequencies
          return frequencies    
    } else {
         throwFFTError.NoInputData    
     }  
   } else {
      throwFFTError.NoContext  
   }
}
```

当有多个条件需要用 guard 或 if let 解包，可用复合语句避免嵌套。

```swift
guard let number1=number1, number2=number2, number3=number3 else{
    fatalError("impossible") 
}
// 处理number
```

**8.语法糖**

```swift
//对空的数据和字典，使用类型注解
var names:  [String] = []
var lookup:  [String: Int] = [:]

var deviceModels: [String]
var employees: [Int:String]
var faxNumber: Int?
```

**9.函数**

自由函数不依附于任何类或类型，应该节制地使用。 正确代码：

```swift
let sorted = items.mergeSort() // 易发现性
rocket.launch()  // 可读性
```

错误代码：

```swift
let sorted = mergeSort(items)// 不易被发现
launch(&rocket)
```

天然的自由函数：

```swift
let value = max(x,y,z)  // another free function that feels natural
```

**10.用扩展只读属性 代替 单参数函数（不强制）**

正确代码：

```swift
extension Int {
    var doubleValue: Double {
        return Double(self)
    }
}

//调用
intValue.doubleValue
```

错误代码：

```swift
func doubleValue(_ value: Int) -> Double {
    return Double(value)
}

//调用
doubleValue(intValue)
```

