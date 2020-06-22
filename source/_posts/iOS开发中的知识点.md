---
title: iOS开发中的知识点
categories:
  - iOS
tags:
  - 面试
abbrlink: f096
date: 2020-03-30 15:56:45
---

> iOS开发中的重要知识点，方便自己随时回顾查看，不间断更新。

## OC 相关

### 1、多线程

#### 进程与线程

##### 	进程：

- 进程是一个具有一定独立功能的程序关于某次数据集合的一次运行活动，它是操作系统分配资源的基本单元

- 进程是指在系统中正在运行的一个应用程序，就是一段程序的执行过程,我们可以理解为手机上的一个app

- 每个进程之间是独立的，每个进程均运行在其专用且受保护的内存空间内，拥有独立运行所需的全部资源

  ##### 线程：

- 程序执行流的最小单元，线程是进程中的一个实体

- 一个进程要想执行任务，必须至少有一条线程.应用程序启动的时候，系统会默认开启主线程

  ##### 进程和线程：

- 线程是进程的执行单元，进程的所有任务都在线程中执行

- 线程是 CPU 分配资源和调度的最小单位

- 一个程序可以对应多个进程(多进程),一个进程中可有多个线程,但至少要有一条线程
  同一个进程内的线程共享进程资源

#### 创建线程方式

- NSThread面向对象的，需要手动创建线程，不需要手动销毁，子线程间通信很难
- GCD基于C语言，充分利用设备的多核，自动管理线程生命周期，比NSOperation效率高
- NSOperation基于GCD封装，更加面向对象，比GCD多了一些功能，可以添加线程间的依赖

#### GCD（大中央调度）

- 并发队列（让多个任务并发执行，异步任务下才有效）
- 串行队列（任务一个接一个地执行，按照FIFO顺序执行）
- 同步任务派发(synchronous，阻塞当前线程，等待任务块Block完成后,函数才能返回,后面的代码才能继续执行)
- 异步任务派发(asynchronous，任务添加到队列后，函数立即返回，后面的代码不用等待任务完成后即可执行，异步提交任务执行无序)

#### NSOpertionQueue

1. 初始化NSOpertionQueue
2. 创建线程队列的NSOpertion
3. 添加NSOpertion的相关依赖
4. 添加NSOpertion到NSOpertionQueue


### 2、Runtime

#### Runtime 机制

runtime简称运行时。OC是运行时机制，也就是在运行时才做一些处理。
例如：C语言在编译的时候就知道要调用哪个方法函数，而OC在编译的时候并不知道要调用哪个方法函数，只有在运行的时候才知道调用的方法函数名称，来找到对应的方法函数进行调用。

#### Runtime 应用

- 交换方法实现
  场景：当第三方框架或者系统原生方法功能不能满足我们的时候，我们可以在保持系统原有方法功能的基础上，添加额外的功能。
- 动态添加方法
  场景：如果一个类方法非常多，加载类到内存的时候也比较耗费资源，需要给每个方法生成映射表，可以使用动态给某个类，添加方法解决。
- 利用关联对象（AssociatedObject）给分类添加属性
  场景：分类是不能自定义属性和变量的，这时候可以使用runtime动态添加属性方法
  原理：给一个类声明属性，其实本质就是给这个类添加关联
- 遍历类的所有成员变量
   1)、NSCoding自动归档解档
  场景：如果一个模型有许多个属性，实现自定义模型数据持久化时，需要对每个属性都实现一遍encodeObject 和decodeObjectForKey方法，比较麻烦。我们可以使用Runtime来解决。
  原理：用runtime提供的函数遍历Model自身所有属性，并对属性进行encode和decode操作。
   2)、字典转模型
  原理：利用Runtime，遍历模型中所有属性，根据模型的属性名，去字典中查找key，取出对应的值，给模型的属性赋值
  3)、修改textfield的占位文字颜色
- 利用消息转发机制解决方法找不到的异常问题

### 3、Runloop

#### 什么是 RunLoop？

RunLoop是一种高级的循环机制，让程序持续运行，并处理程序中的各种事件，让线程在需要做事的时候忙起来，不需要的话就让线程休眠。

- RunLoop和线程是绑定在一起的，每条线程都有唯一一个与之对应的RunLoop对象。
- 不能自己创建RunLoop对象，但是可以获取系统提供的RunLoop对象。
- 主线程的RunLoop对象是由系统自动创建自动完成启动，而子线程中的RunLoop对象需要我们手动获取并启动。
- NSRunLoop提供了一个添加NSTimer的方法，可以指定Mode，如果要让任何情况下都回调，需设置Mode为Common模式。

#### 列表Timer停止

原因在于滑动时当前线程的Runloop切换了mode用于列表滑动，导致timer暂停。滑动列表时，Runloop的mode由原来的Default模式切换到了Event Tracking模式，timer停止工作。

Runloop中的mode主要用来指定事件在runloop中的优先级，有以下几种：

- Default：默认，一般情况下使用
- Connection：一般系统用来处理NSConnection相关事件，开发者一般用不到
- Modal：处理modal panels事件
- Event Tracking：用于处理拖拽和用户交互的模式
- Common：包括Default，Modal，Event Tracking三大模式，可以处理几乎所有事件

```objective-c
// 方法一是将timer加入到NSRunloopCommonModes中
[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];

// 方法二是将timer放到另一个线程中，然后开启另一个线程的runloop
dispatch_async(dispatch_get_global_queue(0, 0), ^{
	timer = [NSTimer scheduledTimerWithTimeInterval:1 ...];
	[[NSRunLoop currentRunLoop] run];
});
```

### 4、KVC 和 KVO

#### 什么是 KVC 和 KVO

KVC(key-value-coding)键值编码，是一种间接访问实例变量的方法。提供一种机制来间接访问对象的属性。
给私有变量赋值。

- 给控件的内部属性赋值（如自定义UITextFiled的clearButton、placeholder的颜色，一般可利用runtime获取控件的内部属性名，Ivar *ivar = class_getInstanceVariable获取实例成员变量）
  [textField setValue:[UIColor redColor] forKeyPath:@"placeholderLabel.textColor"];
- 结合Runtime，model和字典的转换（setValuesForKeysWithDictionary，class_copyIvarList获取指定类的Ivar成员列表）

KVO是一种基于KVC实现的观察者模式。当指被观察的对象的属性更改了，KVO会以自动或手动方式通知观察者。
事例：监听 ScrollView 的 contentOffSet属性
[scrollview addObserver:self forKeyPath:@"contentOffset" options: NSKeyValueObservingOptionNew context:nil];

#### KVC的底层实现

- 检查是否存在相应的key的set方法，如果存在，就调用set方法
- 如果set方法不存在，就会查找与key相同名称并且带下划线的成员变量，如果有，则直接给成员变量属性赋值
- 如果没有找到_key，就会查找相同名称的属性key，如果有就直接赋值
- 如果还没有找到，则调用valueForUndefinedKey:和setValue:forUndefinedKey:方法，这些方法的默认实现都是抛出异常，我们可以根据需要重写它们

#### KVO的底层实现

- 当给A类添加KVO的时候，runtime动态的生成了一个子类NSKVONotifying_A，让A类的isa指针指向NSKVONotifying_A类，重写class方法，隐藏对象真实类信息
- 重写监听属性的setter方法，在setter方法内部调用了Foundation 的 _NSSetObjectValueAndNotify 函数
  _NSSetObjectValueAndNotify函数内部
   1) 首先会调用 willChangeValueForKey
   2) 然后给属性赋值
   3) 最后调用 didChangeValueForKey
   4) 最后调用 observer 的 observeValueForKeyPath 去告诉监听器属性值发生了改变 
- 重写了dealloc做一些 KVO 内存释放

## Swift 相关

### 1、与 OC 比较

#### 共同点

- OC出现过的绝大多数概念，在Swift中继续有效（可能换个术语）
- Swift和OC共用一套运行时环境，Swift的类型可以桥接到OC，反之亦然

#### 比较

- Swift注重安全，OC注重灵活
- Swift注重值类型，OC注重指针和引用
- Swift是静态类型语言，OC是动态类型语言
- Swift注重面向协议编程、函数式编程、面向对象编程，OC注重面向对象编程

#### 优点

- Swift容易阅读，文件结构和语法简化
- Swift中有各种方便快捷的高阶函数（函数式编程）
- Swift中的可选类型，用于所有数据类型，而不仅仅局限于类
- Swift中的泛型更加方便和通用，OC中只能为集合类型添加泛型
- Swift细化权限，open > public > internal(默认) > fileprivate > private
- Swift中独有的元组类型(tuples)，把多个值组合成复合值。元组内的值并不要求是相同类型的

### 2、高级用法

#### 柯里化（Currying）

把接受多个参数的方法变换成接受第一个参数的方法，并且返回接受余下的参数并且返回结果的新方法

```swift
func add(_ v1:Int,_ v2:Int) -> Int {
    return v1 + v2
}
print(add(1, 2))

// 柯里化(Currying)
func add(_ v:Int) -> (Int) -> Int {
    return {$0 + v}
}
print(add(1)(2))
```

#### 关键字mutating

使用 struct 或 enum 实现某协议时，协议的方法前加 mutating 关键字，可以在此方法中修改 struct 或 enum的变量。使用 class 实现协议时，方法前面是不需要加 mutating 修饰的，因为 class 可以随意更改自己的成员变量。

```swift
protocol Vehicle {
   var numberOfWheels:Int{get}
   mutating func changeNumberOfWheels()
}

struct MyCar:Vehicle {
   var numberOfWheels: Int = 4
   mutating func changeNumberOfWheels() {
       numberOfWheels = 4
   }
}
```

#### 元组（Tuple）

元组是swift编程语言中唯一的一种复合类型，他可以将指定有限个数的任何类型一次整理为一个对象，元组中的每一种类型都可以是任何的结构体、枚举或类类型。

```swift
// 交换输入普通实现：
func swapMe<T>(a: inout T, b: inout T) {
  let temp = a
  a = b
  b = temp
}

// 交换输入元组实现：
func swapMe<T>(a: inout T, b: inout T) {
	(a,b) = (b,a)
}
```

#### 操作符

- precedencegroup：定义操作符的优先级
- associativity：操作符的结合律
- higherThan、lowerThan：运算符的优先级
- prefix、infix、postfix：前缀、中缀、后缀运算符

```swift
infix operator +++  // 中缀
func +++(left: Int, right: Int) -> Int {
    return left+right*2
}
print(2+++3) // 8

prefix operator ==+  // 前缀
prefix func ==+(left: Int) -> Int {
   return left*2
}
print(==+2) // 4

postfix operator +==  // 后缀
postfix func +==(right: Int) -> Int {
   return right*3
}
print(2+==) // 6
```

#### 输入输出参数（inout）

可变形式参数只能在函数的内部做改变。如果你想函数能够修改一个形式参数的值，而且你想这些改变在函数结束之后依然生效，那么就需要将形式参数定义为输入输出形式参数。

```swift
var b = 10
func test(a:inout Int) {
   a = 20
}
test(a: &b)
print(b) //20
```

#### 嵌套函数

我们可以把函数当成参数或者变量来使用，函数内部嵌套函数。

```swift
func forward(_ forward:Bool) -> (Int) -> Int {
    
    func next(_ input:Int) -> Int {
        input + 1
    }

    func previous(_ input:Int) -> Int {
        input - 1
    }
  
    return forward ? next : previous
}
```

#### 命名空间

Swift 的命名空间是基于 module 而不是在代码中显式地指明，每个 module 代表了 Swift 中的一个命名空间。也就是说，同一个 target 里的类型名称还是不能相同的。在我们进行 app 开发时，默认添加到 app 的主 target 的内容都是处于同一个命名空间中的，我们可以通过创建 Cocoa (Touch) Framework 的 target 的方法来新建一个 module，这样我们就可以在两个不同的 target 中添加同样名字的类型了。

#### 别名（typealias）

我们可以给一个复杂的难以理解的类型起一个别名，方便我们使用和理解。

```swift
// Swift 定义 Void 为空的元组
public typealias Void = ()

// 函数别名
typealias IntFn = (Int,Int) -> Int
func difference(v1:Int,v2:Int) -> Int {
   v1 - v2
}
let fn:IntFn = difference
print(fn(2,1))  //1

// 元组别名
typealias Date = (year:Int,month:Int,day:Int)
func test(_ date:Date) {
   print(date.year)
}
test((2019,10,30))
```

#### 可变参数

一个可变形式参数可以接受零或者多个`特定类型的值`，可变参数必须是同一类型的。当调用函数的时候你可以利用可变形式参数来声明形式参数可以被传入值的数量是可变的。可以通过在形式参数的类型名称后边插入三个点符号（`...`）来书写可变形式参数。

```swift
func sum(_ numbers:Int...) -> Int{
   var total = 0
   for item in numbers {
       total += item
   }
   return total
}
sum(1,2,3,4)  // 10
```

#### 初始化

不同于 Objective-C 的初始化器，Swift 初始化器不返回值。这些初始化器主要的角色就是确保在第一次使用之前某类型的新实例能够正确初始化。

```swift
class Person {
   var age: Int
   var name: String
   //指定初始化器
   init(age:Int, name:String) {
       self.age = age
       self.name = name
   }
   //便捷初始化器
   convenience init(age:Int){
       self.init(age:age,name:"")
   }
}
```

- 每一个类至少有一个指定初始化器
- 默认初始化器总是类的指定初始化器
- 一个类通常只有一个指定初始化器
- 指定初始化器必须从他的直系父类调用指定初始化器
- 便捷初始化器必须从相同的类里调用另一个初始化器
- 便捷初始化器最终必须调用一个指定初始化器

## 控件使用

### 1、UIView

### 2、UITableView

## 实际应用

### 网络封装

1. 使用xcconfig配置不同环境host
2. 添加通用的请求类和响应类
3. 根据后台设计添加处理加密、验签等方法
4. 封装get、post、upload请求方法
5. 请求方法传入：响应类泛型、请求路径、请求参数
6. 请求完成回调：成功（返回响应类型对象）、异常、失败
7. 异常处理（退出登录、重试机制...）
8. 添加统一的网络请求hud

### 图片加载

1. 显示placeholder占位图
2. 以url为数据索引先在内存中查找是否有缓存
3. 如果没有缓存，通过md5处理过的key来在磁盘查找，找到把的数据加载到内存并显示出来
4. 如果内存和磁盘中都没有找到，就会向远程服务器发送请求，开始下载图片
5. 下载完的图片加入缓存并写入到磁盘中
6. 获取图片的过程是在子线程中进行，在主线程中显示

## APP 架构

### 1、组件化

#### 优点

- 业务分层、解耦，使代码变得可维护
- 拆分、组织庞大的项目，使项目变得可维护
- 便于各业务功能拆分、抽离，实现功能复用
- 业务隔离，实现跨团队开发代码控制和版本风险控制
- 模块化对代码的封装性、合理性都有一定的要求，提升开发人员编码能力
- 在维护好组件的情况下，满足不同客户需求（在新的主App中使用当前组件）

#### 实现

- 基础组件：按功能分库，不涉及业务需求，通过接口供业务组件调用
- 基础UI组件：各个业务模块依赖使用，但需要保持好定制扩展的设计
- 业务组件：业务功能间相对独立，相互间没有Model共享的依赖
- 中间件：target-action，url-block，protocol-class

### 2、MVVM

## App 优化

### 1、启动优化

- 删除无用代码
- 抽象重复代码
- 第三方库延迟初始化
- 业务逻辑延迟或异步执行
- App首页面先展示后渲染
- Time Profiler查找耗时操作和页面

### 2、网络优化

#### 请求优化

- 使用HttpDNS下发ip列表
- 根据不同网络设置超时时间
- 网络环境切换或失败，自动重发

#### 传输优化

- 使用http缓存
- 文件传输启用Gzip
- 采用WebP图片格式
- 采用更好的序列化/反序列化方案

### 3、体积优化

- 开启Bitcode
- 删除无用资源和代码
- 对图片资源进行压缩
- 使用更轻量级的第三方库
- 使用纯代码进行界面布局
- 启动图和引导图使用原生绘制或jpg
- 某些资源文件放在服务器上，按需下载

### 4、性能优化

#### 速度优化

- 耗时操作放入子线程
- 使用图片缓存，数据缓存
- 图文混排使用CoreText
- 使图片适合UIImageView的尺寸
- 减少离屏渲染，子线程绘制，主线程渲染
- 减少subviews个数，用layer绘制元素
- 减少clearColor，maskToBounds，阴影

#### tableview优化

- 使用cell复用机制
- 不要动态添加视图
- 图片异步加载并缓存
- 手动计算高度缓存高度
- 按需刷新，刷新最小列表单元

