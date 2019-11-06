# 动画

> 所有示例代码均可以在 [Animations-Demo](https://link.jianshu.com/?t=https://github.com/smalldu/Animations-Demo) 下载到

iOS 中实现动画有好几种方式，UIView 无疑是最简单的一种，但是所有的动画归根结底还是 layer 层的动画。UIView 层面的动画只是对 layer 层部分属性的封装。我们可以直接对 UIView 的 `alpha` 、`bounds` 、`center` 、`frame`、`transform`、`backgroundColor`\(如果view没有实现`draw(_:)`\)。上面这些属性看起来不多，但是足够满足大部分日常开发动画。

在 UIView 中执行一个动画非常简单，系统已经帮我们封装好了一切，你只需要将你想要动画的属性放到 `animations` 的闭包中即可。

```text
UIView.animate(withDuration: 0.4) {
  self.v.backgroundColor = UIColor.red
}
```

闭包中可以同时执行多个属性的动画,也可以是多个view的动画

```text
UIView.animate(withDuration: 0.4) {
  self.v.backgroundColor = UIColor.red
  self.v.center.y += 100
  self.v2.alpha = 0
}
```

如果我们想让一个属性在 `animations` 闭包中执行，但是又不要执行动画，可以这样。

```text
UIView.animate(withDuration: 0.4) {
  self.v.backgroundColor = UIColor.red
  UIView.performWithoutAnimation {
    self.v2.alpha = 0
  }
}
```

放在 `performWithoutAnimation` 闭包中就会不执行动画了， 这个在有时候做项目的时候某个功能总是会莫名其妙的调一下或者执行一个很奇怪的动画，这时候可以把那段 code 放在这个闭包中，就不会有动画了。

iOS 9 开始 ， `UIVisualEffectView` 的 `effect` 属性也是可以动画的。动画前设置为 `nil` ，在`animations:` 的block 中设置 `effect`

```text
let effect = UIBlurEffect(style: .dark)
effectView.effect = nil
UIView.animate(withDuration: 1) {
  self.effectView.effect = effect
}
```

![](//upload-images.jianshu.io/upload_images/954071-93bb2abc49d7f0e3.gif?imageMogr2/auto-orient/strip|imageView2/2/w/206/format/webp)示例

#### UIView 动画 options

UIView 动画比较完整的版本并不是上面那么简短，还有很多其他的参数可以配置

```text
animate(withDuration:, delay: , options: , animations: , completion: )
```

* withDuration：动画的持续时间，也可理解为动画的执行速度，持续时间越小速度越快
* delay：动画开始之前的延时，默认是无延时。
* options：一个附加选项，`UIViewAnimationOptions` 可以指定多个
* animations：执行动画的闭包
* completion：动画完成后执行的闭包，可以为nil，可以在这里链接下一个动画。

下面是一些主要的`options` \(`UIViewAnimationOptions`\) :

* 动画执行对应的曲线（缓冲）: 动画执行过程速度的改变，会有一个加速度或者一个减速度。
  * `.curveEaseIn`
  * `.curveEaseOut`
  * `.curveEaseInOut`
  * `.curveLinear`
*  `.repeat` ： 指定这个选项后，动画会无限重复
*  `.autoreverse`：往返动画，从开始执行到结束后，又从结束返回开始。

但是这里会有个问题，如果我们让一个view 移动100pt。使用 `.autoreverse` , 代码如下

```text
let opts = UIViewAnimationOptions.autoreverse
UIView.animate(withDuration: 1, delay: 0, options: opts, animations: {
  self.view2.center.x -= 100
}, completion: nil)
```

![](//upload-images.jianshu.io/upload_images/954071-dfce08bb2702e4e8.gif?imageMogr2/auto-orient/strip|imageView2/2/w/206/format/webp)示例

发现很顺利的往返之后，又跳了一下，是因为我们view2的center 其实已经改变了。如果想让它回到原位，只需要在完成时候指定它的位置即可

```text
let xorig = self.view2.center.x
let opts = UIViewAnimationOptions.autoreverse
UIView.animate(withDuration: 1, delay: 0, options: opts, animations: {
  self.view2.center.x -= 100
}, completion: { _ in
  self.view2.center.x = xorig
})
```

![](//upload-images.jianshu.io/upload_images/954071-e64a9cfb7d9d2ae8.gif?imageMogr2/auto-orient/strip|imageView2/2/w/206/format/webp)示例

如果想让这个动画无限次循环，只需要加一个option

```text
let opts: UIViewAnimationOptions = [.autoreverse , .repeat]
```

如果需要执定循环次数

```text
let xorig = self.view2.center.x
let opts: UIViewAnimationOptions = UIViewAnimationOptions.autoreverse
UIView.animate(withDuration: 1, delay: 0, options: opts, animations: {
  UIView.setAnimationRepeatCount(5)
  self.view2.center.x -= 100
}, completion: { _ in
  self.view2.center.x = xorig
})
```

这样就会只循环五次

还有一些options指定如果另一个动画已经作用在这个view上时，该怎么办。

*  `.beginFromCurrentState` 从上次动画的当前状态继续这次的动画，立即执行上次动画的完成b闭包。会使用 presentation layer 决定从哪里开始。如果可能的化，会混合两次的动画。
*  `.overrideInheritedDuration` 不继承别的动画的持续时间（默认是继承）
*  `.overrideInheritedCurve` 不继承别的动画的曲线（默认是继承）

iOS 8之后`.beginFromCurrentState` 就很少用到了，在 iOS 7 时候下面的动画是会跳一下，现在都很平滑了

```text
UIView.animate(withDuration: 0.5) {
  self.view3.center.x -= 100
}
UIView.animate(withDuration: 0.5) {
  self.view3.center.y += 100
}
```

![](//upload-images.jianshu.io/upload_images/954071-0754012720c578dd.gif?imageMogr2/auto-orient/strip|imageView2/2/w/199/format/webp)示例

#### 取消view的动画

一旦一个动画开始执行，在执行的过程中我们怎么取消呢？下面有一个执行时间很长的动画。

```text
self.pOrig = self.view4.center
self.pFinal = self.view4.center
self.pFinal.x -= 100
UIView.animate(withDuration: 4) {
  self.view4.center = self.pFinal
}
```

这个动画执行很漫长，我中途想取消怎么办 ？

* 调用 layer 层的 `removeAllAnimations`

```text
self.view4.layer.removeAllAnimations()
```

![](//upload-images.jianshu.io/upload_images/954071-8b0e42f74a42e639.gif?imageMogr2/auto-orient/strip|imageView2/2/w/260/format/webp)示例

\[图片上传中...\(pic\_02.gif-92d0a1-1511337092782-0\)\]

这种方式会跳动一下，很不好。

* 我们可以记录用一个0.1秒的动画到终点，先拿到当前的位置。

```text
self.view4.layer.position = self.view4.layer.presentation()!.position
self.view4.layer.removeAllAnimations()
UIView.animate(withDuration: 0.1) {
  self.view4.center = self.pFinal
}
```

![](//upload-images.jianshu.io/upload_images/954071-442c63b63020d2c2.gif?imageMogr2/auto-orient/strip|imageView2/2/w/260/format/webp)示例

这样看起来 smooth 多了。

**transform**

view的 transform 非常简单，也比较常用，就旋转平移缩放，可以叠加在一起使用。

```text
UIView.animate(withDuration: 1.2) {
  self.view5.transform = CGAffineTransform.identity
    .translatedBy(x: -100, y: 0)
    .rotated(by:CGFloat(Double.pi/4))
    .scaledBy(x: 0.5, y: 0.5)
}
```

![](//upload-images.jianshu.io/upload_images/954071-6b5e02ff271d1bdd.gif?imageMogr2/auto-orient/strip|imageView2/2/w/260/format/webp)示例

如果需要回到原来的位置 用 `self.view5.transform = CGAffineTransform.identity` 即可

#### 自定义 animation property

我们可以在自己自定义的view上自定义一个可以动画的属性。例如加一个`swing` 属性，设置为true 则 center.x 加 100 ， false 则 center.x 减 100

```text
class MyView: UIView {
  var swing: Bool = true {
    didSet{
      var p = self.center
      p.x = self.swing ? p.x + 100 : p.x - 100
      UIView.animate(withDuration: 0) {
        self.center = p
      }
    }
  }
}
```

然后在动画的时候只需要使用swing属性就可以了

```text
UIView.animate(withDuration: 0.4) {
  self.view1.swing = !self.view1.swing
}
```

![](//upload-images.jianshu.io/upload_images/954071-adf9168e5a152c1e.gif?imageMogr2/auto-orient/strip|imageView2/2/w/260/format/webp)示例

#### Spring 弹性动画

弹性动画一般有一个很快的初速度，在结束的时候也会有一个摆动。类似弹簧的效果。

```text
UIView.animate(withDuration: 1 , delay: 0 , usingSpringWithDamping: 0.3 , initialSpringVelocity: 8 , options: [] , animations: {
  self.view2.center.x -= 100
}, completion: nil)
```

![](//upload-images.jianshu.io/upload_images/954071-b74230534f1cdfa2.gif?imageMogr2/auto-orient/strip|imageView2/2/w/212/format/webp)示例

`usingSpringWithDamping` 小于1 ，动画在最终位置都会有一个摇摆。值越小摇晃的越缓和。`initialSpringVelocity` 表示一个初始速度， 动画执行快慢由他和duration共同决定。这个需要根绝实际情况多多调试。

#### Keyframe 关键帧动画

什么意思呢？就是我们可以把动画分成一个一个小的阶段，然后在将这些结合在一起。使用 `UIView.animateKeyframes(withDuration:,delay:,options:,animations:,completion:)` 然后在 `animations` 的闭包中调用 `UIView.addKeyframe(withRelativeStartTime: , relativeDuration: , animations:)` 添加关键帧 。可以多次调用指定多个点。 startTime 是从0 - 1 的相对时间 ，相对于整体动画的时间。看个例子 。

```text
var p = self.view3.center
let dur = 0.25
var start = 0.0
let dx: CGFloat = -100
let dy: CGFloat = 50
var dir: CGFloat = 1

UIView.animateKeyframes(withDuration: 4, delay: 0, options: [], animations: {
  UIView.addKeyframe(withRelativeStartTime: start , relativeDuration: dur , animations: {
    p.x += dx*dir
    p.y += dy
    self.view3.center = p
  })
  start += dur
  dir *= -1
  UIView.addKeyframe(withRelativeStartTime: start , relativeDuration: dur , animations: {
    p.x += dx*dir
    p.y += dy
    self.view3.center = p
  })
  start += dur
  dir *= -1
  UIView.addKeyframe(withRelativeStartTime: start , relativeDuration: dur , animations: {
    p.x += dx*dir
    p.y += dy
    self.view3.center = p
  })
  start += dur
  dir *= -1
  UIView.addKeyframe(withRelativeStartTime: start , relativeDuration: dur , animations: {
    p.x += dx*dir
    p.y += dy
    self.view3.center = p
  })
}, completion: nil)
```

![](//upload-images.jianshu.io/upload_images/954071-ab9c33efdb4a1448.gif?imageMogr2/auto-orient/strip|imageView2/2/w/212/format/webp)示例

上面的示例，一共有四个关键帧。每个`relativeDuration` 0.25， 表示占总时长的1/4。四段等时长的帧动画。我们上面并没有指定options，这里使用的是`UIViewKeyframeAnimationOptions`,默认是 `.calculationModeLinear` . 当然我们也可以指定其他的option，这个作用可以自己去尝试。我们的关键帧动画的必报里也是可以指定多个属性、或者多个view的动画。

#### Transitions 过渡

过度动画强调的是view改变内容。一般有两个方法

* `UIView.transition(with:, duration:, options:, animations:, completion:)`
* `UIView.transition(from: , to:, duration:, options:, completion:)`

过渡动画的类型是一个options （`UIViewAnimationOptions`）

*  `.transitionFlipFromLeft`,`.transitionFlipFromRight`
*  `.transitionFlipFromTop`,`.transitionFlipFromBottom`
*  `.transitionCurlUp`,`.transitionCurlDown`
* `.transitionCrossDissolve`

来看一个例子，我们来修改下 `UIImageView` 的内容

```text
UIView.transition(with: self.imageView , duration: 0.6 , options: .transitionFlipFromLeft , animations: {
  if self.imageView.image == #imageLiteral(resourceName: "rabbit") {
    self.imageView.image = #imageLiteral(resourceName: "elephant")
  }else{
    self.imageView.image = #imageLiteral(resourceName: "rabbit")
  }
}, completion: nil)
```

从swift 3 开始 图片对象只要在资源里能找到都可以直接输出来，这里copy过来显示的是`#imageLiteral(resourceName: "rabbit")`  
 实际是这样的![](//upload-images.jianshu.io/upload_images/954071-b1a8aff3aaadbc0b.png?imageMogr2/auto-orient/strip|imageView2/2/w/472/format/webp)示例

运行效果：![](//upload-images.jianshu.io/upload_images/954071-745363df3207bd6f.gif?imageMogr2/auto-orient/strip|imageView2/2/w/212/format/webp)示例

我们也可以对自定义view的draw rect 最transition。

```text
class MyView1: UIView {
  var reverse = false
  override func draw(_ rect: CGRect) {
    let f = self.bounds.insetBy(dx: 10, dy: 10)
    let context = UIGraphicsGetCurrentContext()
    if self.reverse {
      context?.strokeEllipse(in: f)
    }else{
      context?.stroke(f)
    }
  }
}
```

动画部分只需要重新绘制即可。

```text
self.view4.reverse = !self.view4.reverse
UIView.transition(with: self.view4 , duration: 0.6 , options: .transitionFlipFromLeft , animations: {
  self.view4.setNeedsDisplay()
}, completion: nil)
```

![](//upload-images.jianshu.io/upload_images/954071-8b1b65874b630b1d.gif?imageMogr2/auto-orient/strip|imageView2/2/w/212/format/webp)示例

默认情况下，一个视图的子视图在transition动画期间改变layout，这个改变是不会有动画的，将在transition结束的时候直接改变，如果想要做动画改变，需要加上`.allowAnimatedContent` option

`UIView.transition(from: , to:, duration:, options:, completion:)` 这个方法需要两个view ，第一个会被第二个替换掉。整个transition动画会在他们的superview进行动画。有两种可能的情况。

* 删除一个subview ， 添加另一个  如果`.showHideTransitionViews` 不包含在 `options` 中 ，那么第二个view在我们开始动画时，并不在视图层级。transition动画 remove将第一个view从superview上remove掉，将第二个view添加到相同的superview上。
* 隐藏一个subview ， 显示另一个  如果`.showHideTransitionViews` 包含在`options` 中 , 那么两个subview一开始都在视图层级中。第一个view的`isHidden`是false ， 第二个为true 。 transition动画会对调两个view的`isHidden`属性。

```text
let lab2 = UILabel(frame: self.label1.frame)
lab2.text = self.label1.text == "Hello" ? "World" : "Hello"
lab2.textColor = UIColor.white
lab2.sizeToFit()
UIView.transition(from: self.label1 , to: lab2 , duration: 0.8 , options: .transitionFlipFromLeft , completion: { _ in
  self.label1 = lab2
})
```

![](//upload-images.jianshu.io/upload_images/954071-75b979f07f15d7a9.gif?imageMogr2/auto-orient/strip|imageView2/2/w/215/format/webp)示例

#### ImageView 和 Image 动画

在 `UIImageView` 上执行动画非常简单，只需要提供`animationImages` 属性。一个`UIImage` 数组。这个数组代码一个一个的帧，当我们调用 `startAnimating` 方法的时候，这个数组的图片就会轮流播放。`animationDuration` 决定了播放的速度。`animationRepeatCount`指定重复次数 （默认是0 ， 代表无限重复），或者调用`stopAnimating` 方法停止动画。

例子 ：

```text
let rabbit = #imageLiteral(resourceName: "rabbit")
UIGraphicsBeginImageContextWithOptions(rabbit.size , false, 0)
let empty = UIGraphicsGetImageFromCurrentImageContext()!
UIGraphicsEndImageContext()
let arr = [rabbit,empty,rabbit,empty,rabbit]
imageView.animationImages = arr
imageView.animationDuration = 2
imageView.animationRepeatCount = 3
imageView.startAnimating()
```

![](//upload-images.jianshu.io/upload_images/954071-9a1f4711130e8c94.gif?imageMogr2/auto-orient/strip|imageView2/2/w/215/format/webp)示例

`UIImage` 有一些类方法为 `UIImageView` 构造 可以动画的image :

*  `UIImage.animatedImage(with:, duration:)`  直接指定了image数组和duration。
*  `UIImage.animatedImageNamed(, duration: )`  提供一个单个的image name ， 系统会自动在后面加 "0" \(如果失败则"1"\) 。使这个image成为第一个image。最后一位数字累加。（知道没有图片或者到达”1024“）
*  `UIImage.animatedResizableImageNamed(, capInsets: , duration: )`  跟上面的方式差不多，但是同时对每个image做了拉伸或者平铺。 图像本身也有`resizableImage(withCapInsets: , resizingMode: )`方法可以缩放（指定某个区域的拉伸或者平铺）

```text
let im = UIImage.animatedImageNamed("voice", duration: 2)
imageView2.image = im
```

其中voice1-3 已经命名好，放在`Assets.xcassets`![](//upload-images.jianshu.io/upload_images/954071-5b7e2e520f979884.gif?imageMogr2/auto-orient/strip|imageView2/2/w/215/format/webp)示例

我们再在一个 button上画一个圆从大到小的动画

```text
var arr = [UIImage]()
let w : CGFloat = 18
for i in 0 ..< 6 {
  UIGraphicsBeginImageContextWithOptions(CGSize(width: w, height: w), false, 0)
  let context = UIGraphicsGetCurrentContext()!
  context.setFillColor(UIColor.red.cgColor)
  let ii = CGFloat(i)
  let rect = CGRect(x: ii, y:ii, width: w-ii*2, height: w-ii*2)
  context.addEllipse(in: rect)
  context.fillPath()
  let im = UIGraphicsGetImageFromCurrentImageContext()!
  UIGraphicsEndImageContext()
  arr.append(im)
}
let im = UIImage.animatedImage(with: arr, duration: 0.5)
self.button.setImage(im, for: .normal)
```

![](//upload-images.jianshu.io/upload_images/954071-fa30a213cb1c5929.gif?imageMogr2/auto-orient/strip|imageView2/2/w/215/format/webp)  
  
作者：smalldu  
链接：https://www.jianshu.com/p/71f2fa270b9c  
来源：简书

