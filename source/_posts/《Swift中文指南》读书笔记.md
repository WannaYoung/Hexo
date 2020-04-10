---
title: 《Swift中文指南》读书笔记
date: 2019-06-16 17:23:18
---

<!-- toc -->

## 关于Swift

Swift 通过采用现代编程模式来避免见编程错误：

- 变量始终在使用前初始化
- 检查数组索引超出范围的错误
- 检查整数是否溢出
- 可选值确保明确处理 nil 值
- 内存被自动管理
- 错误处理允许从意外故障控制恢复

## 基础部分

在一行中声明多个常量或者多个变量，用逗号隔开：

```swift
var x = 0.0, y = 0.0, z = 0.0
```

在一行中定义多个同样类型的变量，用逗号分割：

```swift
var red, green, blue: Double
```

Double 精确度很高，至少有15位数字，而 Float 只有6位数字。

> Swift 是一个类型安全（type safe）的语言。类型安全的语言可以让你清楚地知道代码要处理的值的类型

### 元组

> 元组（tuples）把多个值组合成一个复合值。元组内的值可以是任意类型，并不要求是相同类型

可以将一个元组的内容分解成单独的常量和变量

```swift
let http404Error = (404, "Not Found")
let (statusCode, statusMessage) = http404Error
print("The status code is \(statusCode)")
print("The status message is \(statusMessage)")”
```

通过下标来访问元组中的元素

```swift
print("The status code is \(http404Error.0)")
print("The status message is \(http404Error.1)")
```

给元组中的元素命名后，可以通过名字来获取这些元素

```swift
print("The status code is \(http200Status.statusCode)")
print("The status message is \(http200Status.statusMessage)")
```

### 可选类型

> 使用可选类型（optionals）来处理值可能缺失的情况。可选类型表示两种可能： 或者有值， 你可以解析可选类型访问这个值， 或者根本没有值。
>

- 如果代码中有常量或者变量需要处理值缺失的情况，请把它们声明成对应的可选类型。

- 在 OC 中，nil 是指向不存在对象的指针。在 Swift 中，nil 不是指针，它是一个确定的值，用来表示值缺失。

### 可选绑定

> 使用可选绑定来判断可选类型是否包含值，如果包含就把值赋给一个临时常量或者变量。
>

```swift
if let constantName = someOptional {
    statements
}
```

### 错误处理

一个函数可以通过在声明中添加 throws 关键词来抛出错误消息

```swift
func canThrowAnError() throws {
    // 这个函数有可能抛出错误
}

do {
    try canThrowAnError()
} catch {
    // 有一个错误消息抛出
}
```

## 基本运算符

### 空合运算符

空合运算符（a ?? b）对可选类型 a 进行空判断，如果 a 包含值就进行解包，否则就返回默认值 b。

### 区间运算符

- 闭区间运算符（a...b）定义一个包含从 a 到 b（包括 a 和 b）的所有值的区间
- 半开区间运算符（a..<b）定义一个从 a 到 b 但不包括 b 的区间

- 单侧区间运算符（2...）定义一个从 2 往一侧无限延伸的区间

## 字符串

> String 类型是值类型。当其进行常量、变量赋值操作，或在函数/方法中传递时，会进行值拷贝。
>

### 字符串插值

字符串插值是一种构建新字符串的方式

```swift
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)
```

### 访问修改字符串

 String 值都有一个关联的索引（index）类型，String.Index，它对应着字符串中的每一个 Character 的位置。

```swift
// 插入字符
var welcome = "hello"
welcome.insert("!", at: welcome.endIndex)
//移除字符
let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
```

## 集合类型

> Swift 语言提供数组（Array）、集合（Set）和字典（Dictionary）三种基本的集合类型用来存储集合数据。
>

![截屏2020-04-07 16.09.18](https://i.loli.net/2020/04/07/YCcurvzVU3yAJL7.png)

### 数组（Array）

> 数组使用有序列表存储同一类型的多个值，相同的值可以多次出现在一个数组的不同位置中。
>

enumerated() 返回一个由索引值和数据值组成的元组数组

```swift
for (index, value) in shoppingList.enumerated() {
    print("Item \(String(index + 1)): \(value)")
}
```

### 集合（Sets）

> 集合用来存储相同类型且没有确定顺序的值，要确保每个元素只出现一次时可以使用集合而不是数组。

调用集合的 insert(_:) 方法来添加一个新元素

```swift
var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]
favoriteGenres.insert("Jazz")
```

调用集合的 remove(_:) 方法去删除一个元素

```swift
if let removedGenre = favoriteGenres.remove("Rock") {
    print("\(removedGenre)? I'm over it.")
} else {
    print("I never much cared for that.")
}
```

### 字典（Dictionary）

> 字典是一种无序的集合，它存储的是键值对之间的关系，其所有键值需要是相同的类型。
>

使用 for-in 循环来遍历某个字典中的键值对

```swift
var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
for (airportCode, airportName) in airports {
    print("\(airportCode): \(airportName)")
}
```

## 控制流

### Switch

#### 复合匹配

```swift
let anotherCharacter: Character = "a"
switch anotherCharacter {
case "a", "A":
    print("The letter A")
default:
    print("Not the letter A")
}
```

#### 区间匹配

```swift
let approximateCount = 62
let naturalCount: String
switch approximateCount {
case 0:
    naturalCount = "no"
case 1..<12:
    naturalCount = "a few"
case 12..<100:
    naturalCount = "dozens of"
default:
    naturalCount = "many"
}
print("There are \(naturalCount).")
```

#### 元组

使用元组在同一个 switch 语句中测试多个值

```swift
let somePoint = (1, 1)
switch somePoint {
case (0, 0):
    print("\(somePoint) is at the origin")
case (_, 0):
    print("\(somePoint) is on the x-axis")
case (-2...2, -2...2):
    print("\(somePoint) is inside the box")
default:
    print("\(somePoint) is outside of the box")
}
```

#### 值绑定

case 分支允许将匹配的值声明为临时常量或变量，并且在 case 分支体内使用

```swift
let anotherPoint = (2, 0)
switch anotherPoint {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
```

#### Where

case 分支的模式可以使用 where 语句来判断额外的条件 

```swift
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
case let (x, y) where x == y:
    print("(\(x), \(y)) is on the line x == y")
case let (x, y):
    print("(\(x), \(y)) is just some arbitrary point")
}
```

#### 贯穿（Fallthrough）

在 Swift 里，switch 语句不会从上一个 case 分支跳转到下一个 case 分支中。如果你确实需要 C 风格的贯穿的特性，你可以在每个需要该特性的 case 分支中使用 fallthrough 关键字。

### 检测 API 可用性

我们在 if 或 guard 语句中使用 可用性条件 去有条件的执行一段代码，来在运行时判断调用的 API 是否可用。

```swift
if #available(iOS 10, macOS 10.12, *) {
    // 在 iOS 使用 iOS 10 的 API, 在 macOS 使用 macOS 10.12 的 API
} else {
    // 使用先前版本的 iOS 和 macOS 的 API
}
```

## 函数

### 多返回值函数

你可以用元组（tuple）类型让多个值作为一个复合值从函数中返回

```swift
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

### 隐式返回的函数

任何一个可以被写成一行 return 语句的函数都可以忽略 return。

```swift
func greeting(for person: String) -> String {
    "Hello, " + person + "!"
}
print(greeting(for: "Dave"))
```

### 忽略参数标签

```swift
func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
     // 在函数体内，firstParameterName 和 secondParameterName 代表参数中的第一个和第二个参数值
}
someFunction(1, secondParameterName: 2)
```

### 默认参数值

```swift
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // 如果你在调用时候不传第二个参数，parameterWithDefault 会值为 12 传入到函数体中。
}
someFunction(parameterWithoutDefault: 3, parameterWithDefault: 6) // parameterWithDefault = 6
someFunction(parameterWithoutDefault: 4) // parameterWithDefault = 12
```

### 可变参数

一个可变参数（variadic parameter）可以接受零个或多个值。

```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
arithmeticMean(3, 8.25, 18.75)
```

### 输入输出参数

想要一个函数可以修改参数的值，并且想要在这些修改在函数调用结束后仍然存在，那么就应该把这个参数定义为输入输出参数。定义一个输入输出参数时，在参数定义前加 inout 关键字。

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

### 使用函数类型

在 Swift 中，使用函数类型就像使用其他类型一样

```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
var mathFunction: (Int, Int) -> Int = addTwoInts
print("Result: \(mathFunction(2, 3))")
```

### 函数类型作为参数类型

```swift
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
```

### 函数类型作为返回类型

```swift
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    return backward ? stepBackward : stepForward
}
```

### 嵌套函数

把函数定义在别的函数体中，称作 嵌套函数（nested functions）。

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
var currentValue = -4
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
while currentValue != 0 {
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
```

## 闭包

> 闭包是自包含的函数代码块，可以在代码中被传递和使用
>

### 闭包形式

- 全局函数是一个有名字但不会捕获任何值的闭包
- 嵌套函数是一个有名字并可以捕获其封闭函数域内值的闭包
- 闭包表达式是一个利用轻量级语法所写的可以捕获其上下文中变量或常量值的匿名闭包

### 闭包语法优化

- 利用上下文推断参数和返回值类型
- 单表达式闭包可以省略 return 关键字
- 参数名称缩写
- 尾随闭包语法

### 闭包表达式

闭包表达式是一种构建内联闭包的方式，在不丢失语法清晰明了的同时，闭包表达式提供优化的语法简写形式。

1. 非闭包表达式写法，传入自定义 (String, String) -> Bool 方法

   ```swift
   let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
   func backward(_ s1: String, _ s2: String) -> Bool {
       return s1 > s2
   }
   var reversedNames = names.sorted(by: backward)
   ```

2. 闭包表达式写法，内联闭包的方式

   ```swift
   reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
       return s1 > s2
   })
   ```

3. 上下文推断类型，所有类型可被正确推断

   ```swift
   reversedNames = names.sorted(by: { s1, s2 in 
   		return s1 > s2 
   })
   ```

4. 单表达式隐式返回，省略关键字 return

   ```swift
   reversedNames = names.sorted(by: { s1, s2 in 
   		s1 > s2 
   })
   ```

5. 参数名称缩写，\$0 和 ​\$1 表示第下标为0和1的参数

   ```swift
   reversedNames = names.sorted(by: { $0 > $1 } )
   ```

6. 运算符方法，String 类型定义了关于大于号（>）的字符串实现

   ```swift
   reversedNames = names.sorted(by: >)
   ```

### 尾随闭包

尾随闭包是一个书写在函数圆括号之后的闭包表达式，函数支持将其作为最后一个参数调用。

```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // 函数体部分
}
// 不使用尾随闭包进行函数调用
someFunctionThatTakesAClosure(closure: {
    // 闭包主体部分
})
// 使用尾随闭包进行函数调用
someFunctionThatTakesAClosure() {
    // 闭包主体部分
}
```

尾随包的形式改写 sorted(by:)

```swift
reversedNames = names.sorted() { $0 > $1 }
```

如果闭包表达式是函数或方法的唯一参数，使用尾随闭包时可以把 () 省略

```swift
reversedNames = names.sorted { $0 > $1 }
```

### 逃逸闭包

当一个闭包作为参数传到一个函数中，但是这个闭包在函数返回之后才被执行，我们称该闭包从函数中逃逸。一种能使闭包“逃逸”出函数的方法是，将这个闭包保存在一个函数外部定义的变量中。

```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
    completionHandlers.append(completionHandler)
}
```

将一个闭包标记为 @escaping 意味着你必须在闭包中显式地引用 self。

```swift
class SomeClass {
    var x = 10
    func doSomething() {
        someFunctionWithEscapingClosure { self.x = 100 }
    }
}
```

### 自动闭包

自动闭包是一种自动创建的闭包，用于包装传递给函数作为参数的表达式。这种闭包不接受任何参数，当它被调用的时候，会返回被包装在其中的表达式的值。

```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)
// 打印出“5”

let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)
// 打印出“5”

print("Now serving \(customerProvider())!")
// Prints "Now serving Chris!"
print(customersInLine.count)
// 打印出“4”
```

## 枚举

多个成员值可以出现在同一行上，用逗号隔开

```swift
enum Planet {
    case mercury, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

### 枚举成员的遍历

令枚举遵循 CaseIterable 协议。Swift 会生成一个 allCases 属性，用于表示一个包含枚举所有成员的集合。

```swift
enum Beverage: CaseIterable {
    case coffee, tea, juice
}
let numberOfChoices = Beverage.allCases.count
```

### 关联值

可以定义 Swift 枚举来存储任意类型的关联值

```swift
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}

var productBarcode = Barcode.upc(8, 85909, 51226, 3)
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")

switch productBarcode {
case .upc(let numberSystem, let manufacturer, let product, let check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
```

### 原始值

原始值可以是字符串、字符，或者任意整型值或浮点型值

```swift
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}
```

不需要显式地为每一个枚举成员设置原始值，Swift 将会自动为你赋值

```swift
enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

如果在定义枚举类型的时候使用了原始值，那么将会自动获得一个初始化方法

```swift
let possiblePlanet = Planet(rawValue: 2)

if let somePlanet = Planet(rawValue: 11) {
    print("There is a planet at position 11")
} else {
    print("There isn't a planet at position 11")
}
```

## 结构体和类

### 结构体和类对比

共同点

- 定义属性用于存储值
- 定义方法用于提供功能
- 定义下标操作用于通过下标语法访问它们的值
- 定义构造器用于设置初始值
- 通过扩展以增加默认实现之外的功能
- 遵循协议以提供某种标准功能

类的附加功能

- 继承允许一个类继承另一个类的特征
- 类型转换允许在运行时检查和解释一个类实例的类型
- 析构器允许一个类实例释放任何其所被分配的资源
- 引用计数允许对一个类的多次引用

> 作为一般准则，优先使用结构体，因为它们更容易理解，仅在适当或必要时才使用类
>

### 结构体成员逐一构造器

```swift
struct Resolution {
    var width = 0
    var height = 0
}
let vga = Resolution(width: 640, height: 480)
```

### 结构体和枚举是值类型

> 值类型被赋值给一个变量、常量或者被传递给一个函数的时候，其值会被拷贝
>

标准库定义的集合，例如数组，字典和字符串，都对复制进行了优化以降低性能成本。新集合不会立即复制，而是跟原集合共享同一份内存，共享同样的元素。在集合的某个副本要被修改前，才会复制它的元素。

```swift
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd”
cinema.width = 2048
// cinema 的 width 属性被改为 2048”，hd 中 width 属性还是 1920”
```

### 类是引用类型

> 引用类型在被赋予到一个变量、常量或者被传递到一个函数时，其值不会被拷贝
>

```swift
let tenEighty = VideoMode()
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0”

let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0
// tenEighty 和 alsoTenEighty 的 frameRate 属性都被修改为 30.0
```

### 恒等运算符

判定两个常量或者变量是否引用同一个类实例

```swift
if tenEighty === alsoTenEighty {
    print("tenEighty and alsoTenEighty refer to the same VideoMode instance.")
}
```

## 属性

> 计算属性可以用于类、结构体和枚举，而存储属性只能用于类和结构体
>

### 存储属性

存储属性就是存储在特定类或结构体实例里的一个常量或变量

```swift
struct FixedLengthRange {
    var firstValue: Int
    let length: Int
}
var rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)
```

值类型的实例被声明为常量的时候，它的所有属性也就成了常量。

```swift
let rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
// 该区间表示整数 0，1，2，3
rangeOfFourItems.firstValue = 6
// 尽管 firstValue 是个可变属性，但这里还是会报错
```

引用类型的类则不一样。把一个引用类型的实例赋给一个常量后，依然可以修改该实例的可变属性。

### 延时加载存储属性

延时加载存储属性是指当第一次被调用的时候才会计算其初始值的属性。在属性前使用 lazy 来声明。

```swift
class DataImporter {
    /*
    DataImporter 是一个负责将外部文件中的数据导入的类。
    这个类的初始化会消耗不少时间。
    */
    var fileName = "data.txt"
}

class DataManager {
    lazy var importer = DataImporter()
    var data = [String]()
}

let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// DataImporter 实例的 importer 属性还没有被创建 
```

### 计算属性

计算属性不直接存储值，而是提供一个 getter 和一个可选的 setter，来间接获取和设置其他属性或变量的值。

- 必须使用 var 关键字定义计算属性，包括只读计算属性


- 如果整个 getter 是单一表达式，getter 会隐式地返回这个表达式结果
- 如果计算属性的 setter 没有定义表示新值的参数名，可以使用默认名称 newValue

```swift
struct Point {
    var x = 0.0, y = 0.0
}
struct Size {
    var width = 0.0, height = 0.0
}
struct CompactRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            Point(x: origin.x + (size.width / 2), y: origin.y + (size.height / 2))
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

### 只读计算属性

只有 getter 没有 setter 的计算属性叫只读计算属性

```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
```

### 属性观察器

- willSet 观察器会将新的属性值作为参数传入，使用默认名称 newValue 表示

- didSet 观察器会将旧的属性值作为参数传入，使用默认名称 oldValue 表示


```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet {
            print("将 totalSteps 的值设置为 \(newValue)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("增加了 \(totalSteps - oldValue) 步")
            }
        }
    }
}
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
stepCounter.totalSteps = 360
```

