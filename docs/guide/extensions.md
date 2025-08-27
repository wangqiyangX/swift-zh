# 扩展（Extensions）

> 向现有类型添加功能。

扩展为现有类、结构、枚举或协议类型添加新功能。这包括对无法访问原始源代码的类型进行扩展的能力（称为回溯建模）。扩展类似于 Objective-C 中的 category。（与 Objective-C category 不同，Swift 扩展没有名称。）

Swift 中的扩展可以：

- 添加计算实例属性和计算类型属性
- 定义实例方法和类型方法
- 提供新的初始化器
- 定义下标
- 定义并使用新的嵌套类型
- 使现有类型符合协议

在 Swift 中，您甚至可以扩展一个协议，以提供其要求的实现，或添加额外功能，使符合该协议的类型能够利用这些功能。有关更多详细信息，请参见[协议扩展](protocols.md#协议扩展)。

> 注意
>
> 扩展可以为类型添加新功能，但无法覆盖现有功能。

## [语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/extensions#Extension-Syntax)

使用关键字 `extension` 声明扩展：

```swift
extension SomeType {
    // 在此处添加 SomeType 的新功能。
}
```

扩展可以扩展现有类型，使其遵循一个或多个协议。要添加遵循的协议，您按照为类或结构编写协议名称的方式编写它们：

```swift
extension SomeType: SomeProtocol, AnotherProtocol {
    // 在此处完成协议的要求
}
```

以这种方式添加协议遵循在[使用扩展添加协议遵循](protocols.md#使用扩展添加协议遵循)中进行了描述。

扩展可以用来扩展现有的泛型类型，如在扩展泛型类型中所述。您还可以扩展泛型类型以有条件地添加功能，如在带有泛型 `Where` 子句的扩展中所述。

> 注意
>
> 如果您定义一个扩展以向现有类型添加新功能，则该新功能将在所有现有该类型的实例上可用，即使它们是在定义扩展之前创建的。

## [计算属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/extensions#Computed-Properties)

扩展可以向现有类型添加计算实例属性和计算类型属性。此示例向 Swift 的内置 `Double` 类型添加了五个计算实例属性，以提供对距离单位的基本支持：

```swift
extension Double {
    var km: Double { return self * 1_000.0 }
    var m: Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// 打印 "One inch is 0.0254 meters"
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// Prints "Three feet is 0.914399970739201 meters"
```

这些计算属性表示 `Double` 值应被视为某种长度单位。尽管它们被实现为计算属性，但这些属性的名称可以通过点语法附加到浮点字面量值上，以便使用该字面量值进行距离转换。

在此示例中， `Double` 值 `1.0` 被视为表示“1 米”。这就是 `m` 计算属性返回 `self` 的原因——表达式 `1.m` 被视为计算 `Double` 值 `1.0` 。

其他单位需要一些转换才能以米为单位的值表示。一公里等于 1000 米，因此计算属性 `km` 将值乘以 `1_000.00` 以转换为以米表示的数字。同样，1 米等于 3.28084 英尺，因此计算属性 `ft` 将底层 `Double` 值除以 `3.28084` ，以将其从英尺转换为米。

这些属性是只读计算属性，因此为了简洁起见，它们不使用 `get` 关键字进行表达。它们的返回值类型为 `Double` ，可以在数学计算中用于接受 `Double` 的地方：

```swift
let aMarathon = 42.km + 195.m
print("A marathon is \(aMarathon) meters long")
// Prints "A marathon is 42195.0 meters long"
```

> 注意
>
> 扩展可以添加新的计算属性，但不能添加存储属性，或向现有属性添加属性观察器。

## [初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/extensions#Initializers)

扩展可以向现有类型添加新的初始化器。这使您能够扩展其他类型，以接受您自己的自定义类型作为初始化参数，或提供未包含在类型原始实现中的附加初始化选项。

扩展可以向类添加新的便利初始化器，但它们不能向类添加新的指定初始化器或析构器。指定初始化器和析构器必须始终由原始类实现提供。

如果您使用扩展为值类型添加初始化器，该初始化器为其所有存储属性提供默认值并且不定义任何自定义初始化器，您可以从扩展的初始化器中调用该值类型的默认初始化器和逐成员初始化器。如果您将初始化器作为值类型原始实现的一部分编写，情况就不会是这样，如[值类型的初始化器委托](initialization#值类型的初始化委托)中所述。

如果您使用扩展为在另一个模块中声明的结构添加初始化器，则新的初始化器无法访问 `self` ，直到它调用定义模块中的初始化器。

下面的示例定义了一个自定义 `Rect` 结构来表示一个几何矩形。该示例还定义了两个支持结构，称为 `Size` 和 `Point` ，它们都为其所有属性提供默认值 `0.0` ：

```swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
}
```

因为 `Rect` 结构为其所有属性提供默认值，因此它会自动接收一个默认初始化器和一个成员逐一初始化器，如[默认初始化器](initialization#默认初始化器)中所述。这些初始化器可以用来创建新的 `Rect` 实例：

```swift
let defaultRect = Rect()
let memberwiseRect = Rect(origin: Point(x: 2.0, y: 2.0),
    size: Size(width: 5.0, height: 5.0))
```

您可以扩展 `Rect` 结构，以提供一个额外的初始化程序，该程序接受特定的中心点和大小：

```swift
extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

这个新的初始化器首先根据提供的 `center` 点和 `size` 值计算一个适当的原点。然后，初始化器调用结构的自动成员初始化器 `init(origin:size:)` ，将新的原点和大小值存储在适当的属性中：

```swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
    size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

> 注意
>
> 如果您提供一个带有扩展的新初始化器，您仍然需要负责确保每个实例在初始化器完成后都完全初始化。

## [方法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/extensions#Methods)

扩展可以为现有类型添加新的实例方法和类型方法。以下示例为 `Int` 类型添加了一个名为 `repetitions` 的新实例方法：

```swift
extension Int {
    func repetitions(task: () -> Void) {
        for _ in 0..<self {
            task()
        }
    }
}
```

`repetitions(task:)` 方法接受一个类型为 `() -> Void` 的单一参数，该参数表示一个没有参数并且不返回值的函数。

定义此扩展后，可以在任何整数上调用 `repetitions(task:)` 方法，以执行多次的任务：

```swift
3.repetitions {
    print("Hello!")
}
// Hello!
// Hello!
// Hello!
```

### [修改（变异）实例方法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/extensions#Mutating-Instance-Methods)

通过扩展添加的实例方法也可以修改（或变更）实例本身。修改 `self` 或其属性的结构和枚举方法必须将实例方法标记为 `mutating` ，就像原始实现中的变更方法一样。

下面的示例向 Swift 的 `Int` 类型添加了一个名为 `square` 的新变异方法，该方法将原始值平方：

```swift
extension Int {
    mutating func square() {
        self = self * self
    }
}
var someInt = 3
someInt.square()
// someInt is now 9
```

## [下标](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/extensions#Subscripts)

扩展可以为现有类型添加新的下标。此示例为 Swift 的内置 `Int` 类型添加了一个整数下标。此下标 `[n]` 返回数字从右侧数的 `n` 位的十进制数字：

- `123456789[0]` 返回 `9`
- `123456789[1]` 返回 `8`

……等等：

```swift
extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return (self / decimalBase) % 10
    }
}
746381295[0]
// returns 5
746381295[1]
// returns 9
746381295[2]
// returns 2
746381295[8]
// returns 7
```

如果 `Int` 值没有足够的数字以满足请求的索引，则下标实现返回 `0` ，就像这个数字左侧填充了零一样：

```swift
746381295[9]
// returns 0, as if you had requested:
0746381295[9]
```

## [嵌套类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/extensions#Nested-Types)

扩展可以向现有类、结构和枚举添加新的嵌套类型：

```swift
extension Int {
    enum Kind {
        case negative, zero, positive
    }
    var kind: Kind {
        switch self {
        case 0:
            return .zero
        case let x where x > 0:
            return .positive
        default:
            return .negative
        }
    }
}
```

此示例向 `Int` 添加了一个新的嵌套枚举。这个枚举称为 `Kind` ，表示特定整数所代表的数字类型。具体来说，它表示该数字是负数、零还是正数。

此示例还向 `Int` 添加了一个新的计算实例属性，名为 `kind` ，它返回该整数的适当 `Kind` 枚举情况。

现在可以将嵌套枚举与任何 `Int` 值一起使用：

```swift
func printIntegerKinds(_ numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .negative:
            print("- ", terminator: "")
        case .zero:
            print("0 ", terminator: "")
        case .positive:
            print("+ ", terminator: "")
        }
    }
    print("")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
// Prints "+ + - 0 - 0 + "
```

此函数 `printIntegerKinds(_:)` 接受一个包含 `Int` 值的输入数组，并依次遍历这些值。对于数组中的每个整数，该函数考虑该整数的 `kind` 计算属性，并打印相应的描述。

> 注意
>
> `number.kind` 已经被知道是类型 `Int.Kind` 。基于此，所有 `Int.Kind` 的 case 值都可以在 `switch` 语句中以简写形式书写，例如 `.negative` 而不是 `Int.Kind.negative` 。
