# 枚举

> 模型自定义类型，定义可能值的列表。

枚举定义了一组相关值的公共类型，并使您能够在代码中以类型安全的方式处理这些值。

如果您熟悉 C，您会知道 C 的枚举将相关名称分配给一组整数值。Swift 中的枚举灵活得多，并且不必为枚举的每个案例提供一个值。如果为每个枚举案例提供了一个值（称为原始值），那么该值可以是字符串、字符或任何整数或浮点类型的值。

或者，枚举案例可以指定与每个不同案例值一起存储的任何类型的关联值，就像其他语言中的联合体或变体一样。您可以将一组相关案例定义为一个枚举的一部分，每个案例都有一组不同的值，这些值与适当的类型相关。

在 Swift 中，枚举是第一类类型。它们采用许多传统上仅由类支持的特性，例如计算属性，以提供有关枚举当前值的附加信息，以及实例方法，以便提供与枚举表示的值相关的功能。枚举还可以定义初始化器，以提供初始案例值；可以扩展以扩展其功能 beyond 原始实现；并且可以遵循协议以提供标准功能。

有关这些功能的更多信息，请参见属性、方法、初始化、扩展和协议。

## [枚举语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations#Enumeration-Syntax)

您可以使用 `enum` 关键字引入枚举，并将整个定义放置在一对大括号内：

```swift
enum SomeEnumeration {
    // enumeration definition goes here
}
```

这是一个关于四个主要方向的示例：

```swift
enum CompassPoint {
    case north
    case south
    case east
    case west
}
```

在枚举中定义的值（例如 `north` 、 `south` 、 `east` 和 `west` ）是其枚举案例。您使用 `case` 关键字来引入新的枚举案例。

> 注意
>
> Swift 枚举案例默认没有设置整数值，与 C 和 Objective-C 等语言不同。在上面的 `CompassPoint` 示例中， `north` 、 `south` 、 `east` 和 `west` 并不隐式等于 `0` 、 `1` 、 `2` 和 `3` 。相反，不同的枚举案例本身就是值，具有明确定义的类型 `CompassPoint` 。

多个案例可以出现在一行中，用逗号分隔：

```swift
enum Planet {
    case mercury, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

每个枚举定义定义了一种新类型。与 Swift 中的其他类型一样，它们的名称（例如 `CompassPoint` 和 `Planet` ）以大写字母开头。给枚举类型使用单数而不是复数名称，以便它们读起来显而易见：

```swift
var directionToHead = CompassPoint.west
```

当使用 `CompassPoint` 的可能值之一初始化时， `directionToHead` 的类型被推断。 一旦 `directionToHead` 被声明为 `CompassPoint` ，您可以使用更短的点语法将其设置为不同的 `CompassPoint` 值：

```swift
directionToHead = .east
```

`directionToHead` 的类型已经知道，因此在设置其值时可以省略类型。 这使得在处理显式类型的枚举值时代码可读性很高。

## [使用 Switch 语句匹配枚举值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations#Matching-Enumeration-Values-with-a-Switch-Statement)

您可以使用 `switch` 语句匹配单个枚举值：

```swift
directionToHead = .south
switch directionToHead {
case .north:
    print("Lots of planets have a north")
case .south:
    print("Watch out for penguins")
case .east:
    print("Where the sun rises")
case .west:
    print("Where the skies are blue")
}
// Prints "Watch out for penguins"
```

您可以将这段代码视为：

"“考虑 `directionToHead` 的值。在它等于 `.north` 的情况下，打印 `"Lots of planets have a north"` 。在它等于 `.south` 的情况下，打印 `"Watch out for penguins"` 。”"

……等等。

如控制流中所述， `switch` 语句在考虑枚举的情况时必须是穷尽的。如果遗漏了 `case` 对 `.west` 的情况，则这段代码无法编译，因为它没有考虑完整的 `CompassPoint` 情况列表。要求穷尽性确保枚举情况不会被意外遗漏。

当不适合为每个枚举案例提供一个 `case` 时，您可以提供一个 `default` 案例来覆盖未明确处理的任何案例：

```swift
let somePlanet = Planet.earth
switch somePlanet {
case .earth:
    print("Mostly harmless")
default:
    print("Not a safe place for humans")
}
// Prints "Mostly harmless"
```

## [遍历枚举案例](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations#Iterating-over-Enumeration-Cases)

对于某些枚举，拥有该枚举所有案例的集合是有用的。您可以通过在枚举名称后编写 `: CaseIterable` 来启用此功能。Swift 将所有案例的集合暴露为枚举类型的 `allCases` 属性。以下是一个示例：

```swift
enum Beverage: CaseIterable {
    case coffee, tea, juice
}
let numberOfChoices = Beverage.allCases.count
print("\(numberOfChoices) beverages available")
// Prints "3 beverages available"
```

在上面的示例中，您写 `Beverage.allCases` 来访问包含 `Beverage` 枚举的所有案例的集合。您可以像使用任何其他集合一样使用 `allCases` —— 集合的元素是枚举类型的实例，因此在这种情况下它们是 `Beverage` 值。上面的示例计算了有多少个案例，下面的示例使用 `for` - `in` 循环遍历所有案例。

```swift
for beverage in Beverage.allCases {
    print(beverage)
}
// coffee
// tea
// juice
```

上述示例中使用的语法将枚举标记为符合 `CaseIterable` 协议。有关协议的信息，请参见协议。

## [关联值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations#Associated-Values)

前面一节的示例展示了枚举的案例如何在其自身中被定义为（并且是类型化的）值。您可以将常量或变量设置为 `Planet.earth` ，并稍后检查此值。然而，有时能够将其他类型的值与这些案例值一起存储是很有用的。这种额外信息称为相关值，每次您在代码中将该案例用作值时，它都会有所不同。

您可以定义 Swift 枚举来存储任何给定类型的相关值，如果需要，值类型可以在枚举的每个案例中不同。与这些相似的枚举在其他编程语言中被称为判别联合、标记联合或变体。

例如，假设一个库存跟踪系统需要通过两种不同类型的条形码来跟踪产品。一些产品用 UPC 格式的 1D 条形码标记，使用数字 `0` 到 `9` 。每个条形码都有一个数字系统数字，后面跟着五个制造商代码数字和五个产品代码数字。最后是一个校验位，以验证代码是否被正确扫描：

![barcode_UPC](https://docs.swift.org/swift-book/images/org.swift.tspl/barcode_UPC@2x.png){.light-only}
![barcode_UPC~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/barcode_UPC~dark@2x.png){.dark-only}

其他产品使用 QR 码格式的 2D 条形码标记，可以使用任何 ISO 8859-1 字符，并且可以编码最长为 2,953 个字符的字符串：

![barcode_QR](https://docs.swift.org/swift-book/images/org.swift.tspl/barcode_QR@2x.png){.light-only}
![barcode_QR~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/barcode_QR~dark@2x.png){.dark-only}

对于库存跟踪系统，将 UPC 条形码存储为四个整数的元组，以及将 QR 码条形码存储为任意长度的字符串是很方便的。

在 Swift 中，定义产品条形码的枚举可能如下所示：

```swift
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}
```

这可以被理解为：

“定义一个枚举类型叫做 `Barcode` ，它可以取一个值为 `upc` 的类型，关联值的类型为 ( `Int` , `Int` , `Int` , `Int` )，或者一个值为 `qrCode` 的类型，关联值的类型为 `String` 。”

这个定义没有提供任何实际的 `Int` 或 `String` 值——它只是定义了当 `Barcode` 常量和变量等于 `Barcode.upc` 或 `Barcode.qrCode` 时可以存储的相关值的类型。

您可以使用任一类型创建新的条形码：

```swift
var productBarcode = Barcode.upc(8, 85909, 51226, 3)
```

此示例创建一个名为 `productBarcode` 的新变量，并将其值设置为 `Barcode.upc` ，并关联一个元组值 `(8, 85909, 51226, 3)` 。

您可以为同一产品分配不同类型的条形码：

```swift
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")
```

此时，原始 `Barcode.upc` 及其整数值被新的 `Barcode.qrCode` 及其字符串值替代。类型为 `Barcode` 的常量和变量可以存储 `.upc` 或 `.qrCode` （连同其关联值），但在任何给定时间只能存储其中之一。

您可以使用 switch 语句检查不同的条形码类型，类似于在用 switch 语句匹配枚举值中的示例。不过，这次，相关值作为 switch 语句的一部分被提取。您可以将每个相关值提取为常量（以 `let` 前缀）或变量（以 `var` 前缀），以便在 `switch` case 的主体中使用：

```swift
switch productBarcode {
case .upc(let numberSystem, let manufacturer, let product, let check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(let productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."
```

如果枚举 case 的所有相关值都提取为常量，或者全部提取为变量，您可以在 case 名称之前放置一个 `let` 或 `var` 注释，以简化代码：

```swift
switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC : \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."
```

## [原始值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations#Raw-Values)

关联值中的条形码示例显示了枚举的案例如何声明它们存储不同类型的关联值。作为关联值的替代，枚举案例可以预先填充默认值（称为原始值），这些值都是相同的类型。

这是一个存储原始 ASCII 值与命名枚举案例的示例：

```swift
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}
```

在这里，名为 `ASCIIControlCharacter` 的枚举的原始值被定义为类型 `Character` ，并设置为一些更常见的 ASCII 控制字符。 `Character` 值在字符串和字符中描述。

原始值可以是字符串、字符或任何整数或浮点数类型。每个原始值在其枚举声明中必须是唯一的。

> 注意
>
> 原始值与关联值并不相同。原始值在您首次在代码中定义枚举时被设置为预填充值，就像上面提到的三个 ASCII 代码。特定枚举案例的原始值始终是相同的。关联值是在您基于枚举的某个案例创建新常量或变量时设置的，并且每次这样做时可以不同。

### [隐式赋值的原始值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations#Implicitly-Assigned-Raw-Values)

当您使用存储整数或字符串原始值的枚举时，您不必为每个案例显式分配一个原始值。当您不这样做时，Swift 会自动为您分配值。

例如，当整数用于原始值时，每个案例的隐式值是比前一个案例多一。如果第一个案例没有设置值，则其值为 `0` 。

下面的枚举是对早期 `Planet` 枚举的细化，使用整数原始值表示每个行星距离太阳的顺序：

```swift
enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

在上面的示例中， `Planet.mercury` 有一个显式原始值 `1` ， `Planet.venus` 有一个隐式原始值 `2` ，依此类推。

当字符串用于原始值时，每个案例的隐式值是该案例名称的文本。

下面的枚举是对早期 `CompassPoint` 枚举的细化，使用字符串原始值来表示每个方向的名称：

```swift
enum CompassPoint: String {
    case north, south, east, west
}
```

在上面的示例中， `CompassPoint.south` 具有隐式原始值 `"south"` ，依此类推。

您可以通过其 `rawValue` 属性访问枚举案例的原始值：

```swift
let earthsOrder = Planet.earth.rawValue
// earthsOrder is 3


let sunsetDirection = CompassPoint.west.rawValue
// sunsetDirection is "west"
```

### [从原始值初始化](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations#Initializing-from-a-Raw-Value)

如果您定义了一个具有原始值类型的枚举，该枚举会自动获得一个初始化器，该初始化器接受原始值类型的值（作为名为 `rawValue` 的参数）并返回一个枚举案例或 `nil` 。您可以使用此初始化器尝试创建枚举的新实例。

此示例通过其原始值 `7` 识别天王星：

```swift
let possiblePlanet = Planet(rawValue: 7)
// possiblePlanet is of type Planet? and equals Planet.uranus
```

并不是所有可能的 `Int` 值都会找到匹配的行星。因此，原始值初始化器总是返回一个可选的枚举案例。在上面的例子中， `possiblePlanet` 的类型是 `Planet?` ，或“可选 `Planet` ”。

> 注意
>
> 原始值初始化器是一个可失败的初始化器，因为并不是每个原始值都会返回一个枚举案例。有关更多信息，请参见可失败的初始化器。

如果您尝试查找位置为 `11` 的行星，则原始值初始化器返回的可选 `Planet` 值将是 `nil` :

```swift
let positionToFind = 11
if let somePlanet = Planet(rawValue: positionToFind) {
    switch somePlanet {
    case .earth:
        print("Mostly harmless")
    default:
        print("Not a safe place for humans")
    }
} else {
    print("There isn't a planet at position \(positionToFind)")
}
// Prints "There isn't a planet at position 11"
```

这个例子使用可选绑定尝试访问具有原始值 `11` 的行星。语句 `if let somePlanet = Planet(rawValue: 11)` 创建一个可选 `Planet` ，并将 `somePlanet` 设置为该可选 `Planet` 的值（如果可以获取的话）。在这种情况下，无法获取位置为 `11` 的行星，因此执行 `else` 分支。

## [递归枚举](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations#Recursive-Enumerations)

递归枚举是一个枚举，它有另一个枚举实例作为一个或多个枚举案例的关联值。您通过在它之前写 `indirect` 来指示一个枚举案例是递归的，这告诉编译器插入必要的间接层。

例如，这里有一个存储简单算术表达式的枚举：

```swift
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

您还可以在枚举开始之前写 `indirect` 以启用所有具有关联值的枚举情况的间接引用：

```swift
indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

此枚举可以存储三种算术表达式：一个普通数字、两个表达式的加法和两个表达式的乘法。 `addition` 和 `multiplication` 情况有相关值，这些相关值也是算术表达式——这些相关值使得嵌套表达式成为可能。例如，表达式 `(5 + 4) * 2` 在乘法的右侧有一个数字，在乘法的左侧有另一个表达式。由于数据是嵌套的，用于存储数据的枚举也需要支持嵌套——这意味着枚举需要是递归的。下面的代码展示了为 `(5 + 4) * 2` 创建的 `ArithmeticExpression` 递归枚举：

```swift
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))
```

递归函数是处理具有递归结构的数据的一种简单方法。例如，这里有一个评估算术表达式的函数：

```swift
func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case let .number(value):
        return value
    case let .addition(left, right):
        return evaluate(left) + evaluate(right)
    case let .multiplication(left, right):
        return evaluate(left) * evaluate(right)
    }
}


print(evaluate(product))
// Prints "18"
```

此函数通过简单地返回相关值来评估一个普通数字。它通过评估左侧的表达式、评估右侧的表达式，然后将它们相加或相乘来评估加法或乘法。
