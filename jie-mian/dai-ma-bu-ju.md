---
description: 主要介绍页面布局的几种方式
---

# 页面布局

界面布局的方法有基于frame的绝对布局和相对布局，以及苹果大力推广的AutoLayout，另外今年新出的swiftUI，也具有很大的潜力。

手写控件，拖拽xib和storyboard是实现布局的三种方式。个人偏爱手写负责页面，xib拖拽简单页面。frame的相对布局绘制复杂页面，AutoLayout布局绘制简单的子视图层级少的页面。手写代码速度是最快的，后期的设计到需求改动修改页面的时候也会比AutoLayout更清晰。AutoLayout在iOS11之前子视图层级变多之后渲染速度极速变慢，iOS12之后才优化的更快。

### Frame

frame指的是当前视图在其父视图中的位置和大小。  
在初始化 view 的时候，可以设置 view 的frame。

**frame相对布局**

在绘制界面时如果要求控件的frame是相对屏幕的大小，就需要根据设计图中的屏幕宽高和实际设备的屏幕宽高计算得出需要显示控件的宽高。

举例说明：1、如果设计图给出的控件frame是`（16，100，187.5，200）`（其中要求origin.x为绝对值，其他均为相对值）， 设计图尺寸为375,高为667。那么这个控件的frame应该是`（16，100.0/667.0)*frame.size.height,  frame.size.width/2, 200.0/667.0*frame.size.height）`

我们可以把上面计算frame的函数抽出来写成方法，这里就不再叙述了。

**bounds**

提到 frame 不得不提 bounds，bounds指的是前视图在其自身坐标系统中的位置和大小。可以看到两者的区别在于坐标系不同。

**layoutSubviews**

需要重新布局视图可以使用 layoutSubviews

1）可以在view里重写layoutSubviews

2）可以在view controller里使用

viewWillLayoutSubviews 在autoresizingMasks前调用

viewDidLayoutSubviews 在autoresizingMasks后调用，肯定会覆盖autoresizingMasks的结果

layoutSubviews可能会在不需要调用的时候调用，如果layoutSubviews的比较复杂，可能会卡顿。

### 自动布局AutoLayout

前面讲到的 frame 主要用于视图的绝对位置，但是 iOS 设备有多个尺寸，如何对不同尺寸进行适应，苹果的解决方案是使用 AutoLayout。

如果是从代码层面开始使用 Autolayout，需要对使用的 View 的translatesAutoresizingMaskIntoConstraints 的属性设置为NO。即可开始通过代码添加Constraint，否则View还是会按照以往的autoresizingMask进行计算。而在 Interface Builder 中勾选了Use Auto layout，translatesAutoresizingMaskIntoConstraints 属性都会被默认设置NO。

#### **约束**

自动布局里最重要的组成部分就是约束。分别可以设置视图相对于另一个视图的 leading、trailing、top、bottom、CenterX、CenterY 等关系。根据这些约束来确定视图的相对位置。

视图的约束之间的关系为线型关系。例如，视图Y 相对于 视图X 的位置可以表示为一个线性变换，即

Y = kX + b

即 Y 是 X 某个方向坐标或大小的 k 倍并偏移 b。k 和 b 的大小可以是0。如果 k = 1， b = 0， 则表示 Y 和 X 分别表示视图的宽，则等式表示 Y 和 X 的宽度相等。

AutoLayout 的核心是：Every view requires at least two constraints along each axis to set position and size. 即在每个坐标轴上至少需要2个约束来确定视图位置。

#### **Ambiguous Layout**

在开发过程中，你可以通过调用hasAmbiguousLayout 来测试你的view约束是否足够的。函数会返回布尔值。如果有一个不同的frame就会返回yes，如果view的约束完全指定了就会返回no。 一个设定了完全约束的view的子view也可能存在ambiguous layout，需要为每一个view单独测试layout是否存在ambiguous layout。

#### **Intrinsic Content Size**

使用autolayout时，view的content扮演着非常重要的角色。每个view的intrinsicContentSize描述了不会剪切的显示完整view content的最小空间。例如一个image view，content size根据image显示的size设置。一个大的image需要一个大的固有的content size。image的大小提供给了view。 对于button，固有的content size根据他的title而有不同。随着title增长或者缩短，button的固有的content size也会调节来做适应，可以根据你自定义的font size和title text而有变化。

#### **compression resistance**

压缩阻力表示一个视图的抗压缩性。一个有高compression resistance的视图会防止被压缩。也不会允许content被裁剪，而会尝试保存他的最小固有content size。 autolayout经常遇到两个冲突的请求。当只有一个请求会成功时，他就会满足高优先级的那个。可以分别设置水平和垂直方向的Compression Resistance。value从1（最低）到1,000\(请求的优先级\)不等。默认的是750。

```objectivec
[button setContentCompressionResistancePriority:500 forAxis:UILayoutConstraintAxisHorizontal];
```

**content hugging**

抗拉属性表示view防止被拉伸的属性，和压缩阻力类似。默认值为250。

```objectivec
[button setContentHuggingPriority:501 forAxis:UILayoutConstraintAxisHorizontal];
```

**VFL**

Visual Format Language，即“可视化格式语言”。直接手写约束很复杂，使用VFL相对简单很多，但比较难进行调试。

```objectivec
[self.view addConstraints: [NSLayoutConstraint            
constraintsWithVisualFormat:@"V:[view1]-8-[view2]"             
                    options:NSLayoutFormatAlignAllLeading
                    metrics:nil
                      views:NSDictionaryOfVariableBindings(view1, view2)]];
```

**Masonry**

VFL的写法也相当复杂，可以使用第三方框架 Masonry，Masonry 是一个轻量级的布局框架，Masonry 源码：[https://github.com/Masonry/Masonry](https://github.com/Masonry/Masonry)

例如，设置view1相对父View的每个边距离为padding：

```objectivec
[view1 mas_makeConstraints:^(MASConstraintMaker *make) {
    make.edges.equalTo(superview).with.insets(padding);
}];
[view1 mas_remakeConstraints:^(MASConstraintMaker *make) {
    make.edges.equalTo(superview).with.insets(padding);
}];
[view1 mas_updateConstraints:^(MASConstraintMaker *make) {
    make.edges.equalTo(superview).with.insets(padding);
}];
```

需要注意的是，在结构一样的情况下用mas\_updateConstraints，会更新当前的约束，但是如果要覆盖缘由约束重新添加，则需要使用方法用mas\_remakeConstraints。

constraint加到两个view的公共父view上，因此有一个奇怪的现象是一个view不持有自己的约束，而被其他view持有。在 Masonry 中，实际添加constraint的不一定是约束的持有者。

#### 更新布局方法 <a id="h3-u66F4u65B0u5E03u5C40u65B9u6CD5"></a>

**Constraints**

```objectivec
- (void)updateConstraintsIfNeeded    // 立即重新计算约束，如果在这之前addConstraints，就可以更新约束
- (void)setNeedsUpdateConstraints   // 立即返回，标记说需要改变约束值，在当前update cycle结束后更新之前所有标记过要改变的约束，调用updateConstraints方法
```

**Layout**

```objectivec
- (void)layoutIfNeeded     // 立即更新布局，重新计算约束，如果在这之前addConstraints就会立即反应在页面上
- (void)setNeedsLayout    // 同Constraints，不过是更新布局
- (void)layoutSubviews    // 布局当前页面的子页面
```

**Draw**

```objectivec
- (void)setNeedsDisplay   // 同Constraints,不过是重新渲染
```

### **Constraints，Layout，Draw调用顺序**

一个页面更新的顺序一般为  
调用约束计算出frame（Constraints）→ 根据计算出的frame重新布局\(Layout\) → 根据重新布局的结果进行图像渲染（Draw）

layout的改变会导致重新计算Constraints  
layoutIfNeeded会调用updateConstraintsIfNeeded

Constraints的计算顺序是低到上 （从subview到superview）  
layout的更新顺序是从顶到下（从superview到subview）

### **frame和约束区别**

1、frame是不可以累加的，只能被替换掉，但是constraint可以累加

2、frame不可以跨级添加，但是contraint可以跨层级

3、contraint可以设定priority

另外，需要注意的是，在autolayout下使用frame，会把frame转化成autolayout的约束，如果再进行约束的设置，由于多次累加可能会造成冲突

