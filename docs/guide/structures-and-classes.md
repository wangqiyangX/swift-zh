# 结构与类

> 建模封装数据的自定义类型。

结构和类是通用的、灵活的构造，它们成为您程序代码的基础。您使用与定义常量、变量和函数相同的语法来定义属性和方法，为您的结构和类添加功能。

与其他编程语言不同，Swift 不需要您为自定义结构和类创建单独的接口和实现文件。在 Swift 中，您在一个文件中定义结构或类，该类或结构的外部接口会自动提供给其他代码使用。

> 注意
>
> 类的实例通常被称为对象。然而，Swift 的结构体和类在功能上比其他语言更为接近，本章的大部分内容描述了适用于类或结构类型实例的功能。因此，使用了更通用的术语实例。

## [比较结构和类](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures#Comparing-Structures-and-Classes)

Swift 中的结构和类有许多**共同之处**。两者都可以：

- 定义属性以存储值
- 定义方法以提供功能
- 定义下标以使用下标语法访问其值
- 定义初始化器以设置其初始状态
- 可以扩展以超越默认实现扩展其功能
- 遵循协议以提供某种标准功能

有关更多信息，请参见属性、方法、下标、初始化、扩展和协议。

类具有结构所不具备的**额外功能**：

- **继承**使一个类能够继承另一个类的特征。
- **类型转换**使您能够在运行时检查和解释类实例的类型。
- **析构函数**使类的实例能够释放分配的任何资源。
- **引用计数**允许多个引用指向一个类实例。

有关更多信息，请参见继承、类型转换、析构和自动引用计数。

类支持的额外功能以增加复杂性为代价。一般指南是，优先使用结构，因为它们更容易理解，而在适当或必要时使用类。在实践中，这意味着您定义的大多数自定义类型将是结构和枚举。有关更详细的比较，请参阅《选择结构与类》。

> 注意
>
> 类和 Actor 共享许多相同的特征和行为。有关 Actor 的信息，请参见并发。

### [定义语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures#Definition-Syntax)

结构和类有类似的定义语法。您通过 `struct` 关键字引入结构，通过 `class` 关键字引入类。两者的整个定义都在一对大括号内：

```swift
struct SomeStructure {
    // 结构定义
}
class SomeClass {
    // 类定义
}
```

> 注意
>
> 每当您定义一个新的结构或类时，您就定义了一种新的 Swift 类型。类型依照 `UpperCamelCase` 起名（如这里的 `SomeStructure` 和 `SomeClass` ），以匹配标准 Swift 类型的大小写（如 `String` 、 `Int` 和 `Bool` ）。属性和方法依照 `lowerCamelCase` 起名（如 `frameRate` 和 `incrementCount` ），以区分它们与类型名称。

这是一个结构定义和类定义的示例：

```swift
struct Resolution {
    var width = 0
    var height = 0
}
class VideoMode {
    var resolution = Resolution()
    var interlaced = false
    var frameRate = 0.0
    var name: String?
}
```

上面的示例定义了一个名为 `Resolution` 的新结构，以描述基于像素的显示分辨率。该结构有两个存储属性，分别称为 `width` 和 `height` 。存储属性是以常量或变量的形式被打包并存储为结构或类的一部分。这两个属性在通过将它们设置为初始整数值 `0` 时被推断为类型 `Int` 。

上面的示例还定义了一个名为 `VideoMode` 的新类，以描述特定的视频显示模式。该类有四个变量存储属性。第一个 `resolution` 用一个新的 `Resolution` 结构实例初始化，这推断出属性类型为 `Resolution` 。对于其他三个属性，将使用 `VideoMode` 的新实例初始化，并将 `interlaced` 设置为 `false` （表示“非交错视频”），播放帧率为 `0.0` ，并且还有一个可选的 `String` 值称为 `name` 。 `name` 属性被自动赋予默认值 `nil` ，或“没有 `name` 值”，因为它是一个可选类型。

### [结构和类实例](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures#Structure-and-Class-Instances)

`Resolution` 结构定义和 `VideoMode` 类定义仅描述 `Resolution` 或 `VideoMode` 的外观。它们本身并没有描述特定的分辨率或视频模式。要做到这一点，您需要创建结构或类的实例。

创建实例的语法在结构和类之间非常相似：

```swift
let someResolution = Resolution()
let someVideoMode = VideoMode()
```

结构和类都使用初始化语法来创建新实例。初始化语法的最简单形式使用类或结构的类型名称，后跟空括号，例如 `Resolution()` 或 `VideoMode()` 。这将创建类或结构的新实例，所有属性初始化为其默认值。类和结构的初始化在《初始化》中有更详细的描述。

### [访问属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures#Accessing-Properties)

您可以使用**点语法**访问实例的属性。在点语法中，您在实例名称后面直接写属性名称，以句点（ `.` ）分隔，中间不加任何空格：

```swift
print("The width of someResolution is \(someResolution.width)")
// 打印 "The width of someResolution is 0"
```

在这个示例中， `someResolution.width` 指的是 `someResolution` 的 `width` 属性，返回其默认初始值 `0` 。

您可以深入到子属性，例如 `VideoMode` 的 `resolution` 属性中的 `width` 属性：

```swift
print("The width of someVideoMode is \(someVideoMode.resolution.width)")
// Prints "The width of someVideoMode is 0"
```

您还可以使用点语法为变量属性分配新值：

```swift
someVideoMode.resolution.width = 1280
print("The width of someVideoMode is now \(someVideoMode.resolution.width)")
// Prints "The width of someVideoMode is now 1280"
```

### [结构类型的成员初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures#Memberwise-Initializers-for-Structure-Types)

所有结构都有一个自动生成的成员初始化器，您可以使用它来初始化新结构实例的成员属性。新实例的属性的初始值可以通过名称传递给成员初始化器：

```swift
let vga = Resolution(width: 640, height: 480)
```

与结构不同，类实例不会接收默认的成员初始化器。初始化器在初始化中有更详细的描述。

## [结构和枚举是值类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures#Structures-and-Enumerations-Are-Value-Types)

值类型是指在赋值给变量或常量时，或者在传递给函数时，其**值会被复制**的类型。

实际上，在前面的章节中您已经广泛使用了值类型。事实上，Swift 中的所有基本类型——整数、浮点数、布尔值、字符串、数组和字典——都是值类型，并且在后台实现为结构体。

在 Swift 中，所有结构体和枚举都是值类型。这意味着您创建的任何结构体和枚举实例——以及它们作为属性拥有的任何值类型——在您的代码中传递时总是会被复制。

> 注意
>
> 由 Swift 标准库定义的集合，如数组、字典和字符串，使用一种优化来减少复制的性能成本。它们并不是立即进行复制，而是共享原始实例和任何副本之间元素存储的内存。如果对集合的其中一个副本进行了修改，则在修改之前会进行元素的复制。在您的代码中，您看到的行为总是像是立即进行了复制。

考虑这个例子，它使用了之前示例中的 `Resolution` 结构：

```swift
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd
```

这个例子声明了一个名为 `hd` 的常量，并将其设置为一个 `Resolution` 实例，该实例的宽度和高度为全高清（1920 像素宽，1080 像素高）。

然后声明一个名为 `cinema` 的变量，并将其设置为 `hd` 的当前值。因为 `Resolution` 是一个结构体，所以会创建现有实例的副本，这个新副本被分配给 `cinema` 。尽管 `hd` 和 `cinema` 现在具有相同的宽度和高度，但它们在后台是两个**完全不同的实例**。

接下来， `cinema` 的 `width` 属性被修改为稍微宽一点的 2K 标准的宽度，该标准用于数字影院投影（2048 像素宽，1080 像素高）：

```swift
cinema.width = 2048
```

检查 `cinema` 的 `width` 属性显示它确实已更改为 `2048` :

```swift
print("cinema is now \(cinema.width) pixels wide")
// Prints "cinema is now 2048 pixels wide"
```

然而，原始 `hd` 实例的 `width` 属性仍然具有旧值 `1920` :

```swift
print("hd is still \(hd.width) pixels wide")
// Prints "hd is still 1920 pixels wide"
```

当 `cinema` 被赋予 `hd` 的当前值时，存储在 `hd` 中的值被复制到新的 `cinema` 实例中。最终结果是两个完全独立的实例，它们包含相同的数值。然而，由于它们是独立的实例，将 `cinema` 的宽度设置为 `2048` 并不会影响存储在 `hd` 中的宽度，如下图所示：

![sharedStateStruct](https://docs.swift.org/swift-book/images/org.swift.tspl/sharedStateStruct@2x.png){.light-only}
![sharedStateStruct~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/sharedStateStruct~dark@2x.png){.dark-only}

相同的行为适用于枚举：

```swift
enum CompassPoint {
    case north, south, east, west
    mutating func turnNorth() {
        self = .north
    }
}
var currentDirection = CompassPoint.west
let rememberedDirection = currentDirection
currentDirection.turnNorth()


print("The current direction is \(currentDirection)")
print("The remembered direction is \(rememberedDirection)")
// Prints "The current direction is north"
// Prints "The remembered direction is west"
```

当 `rememberedDirection` 被赋值为 `currentDirection` 时，它实际上是设置为该值的副本。此后更改 `currentDirection` 的值不会影响存储在 `rememberedDirection` 中的原始值的副本。

## [类是引用类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures#Classes-Are-Reference-Types)

与值类型不同，引用类型在赋值给变量或常量，或传递给函数时**不会被复制**。使用的是对同一现有实例的引用，而不是副本。

这里有一个使用上面定义的 `VideoMode` 类的例子：

```swift
let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0
```

这个例子声明了一个新的常量叫做 `tenEighty` 并将其设置为指向 `VideoMode` 类的新实例。视频模式被赋值为之前的 `1080` 的 HD 分辨率的副本。它被设置为交错模式，名称被设置为 `"1080i"` ，帧率被设置为 `25.0` 帧每秒。

下一步， `tenEighty` 被分配给一个新的常量，叫做 `alsoTenEighty` ，并且 `alsoTenEighty` 的帧率被修改：

```swift
let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0
```

因为类是引用类型， `tenEighty` 和 `alsoTenEighty` 实际上都引用同一个 `VideoMode` 实例。实际上，它们只是同一个单一实例的两个不同名称，如下图所示：

![sharedStateClass](https://docs.swift.org/swift-book/images/org.swift.tspl/sharedStateClass@2x.png){.light-only}
![sharedStateClass~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/sharedStateClass~dark@2x.png){.dark-only}

检查 `tenEighty` 的 `frameRate` 属性显示它正确报告了来自底层 `VideoMode` 实例的新帧率 `30.0` ：

```swift
print("The frameRate property of tenEighty is now \(tenEighty.frameRate)")
// Prints "The frameRate property of tenEighty is now 30.0"
```

此示例还显示了引用类型可能更难推理的原因。如果 `tenEighty` 和 `alsoTenEighty` 在您程序的代码中相距很远，那么找到所有视频模式变化的方式可能会很困难。无论您在哪里使用 `tenEighty` ，您都必须考虑使用 `alsoTenEighty` 的代码，反之亦然。相比之下，值类型更容易推理，因为与同一值交互的所有代码在您的源文件中相对靠近。

请注意， `tenEighty` 和 `alsoTenEighty` 被声明为常量，而不是变量。然而，您仍然可以改变 `tenEighty.frameRate` 和 `alsoTenEighty.frameRate` ，因为 `tenEighty` 和 `alsoTenEighty` 常量本身的值实际上并没有改变。 `tenEighty` 和 `alsoTenEighty` 本身并没有“存储” `VideoMode` 实例——相反，它们都在后台引用一个 `VideoMode` 实例。改变的是底层 `VideoMode` 的 `frameRate` 属性，而不是对该 `VideoMode` 的常量引用的值。

### [身份运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures#Identity-Operators)

因为类是引用类型，所以多个常量和变量可以在后台引用同一个类的单一实例。（结构和枚举则不然，因为它们在赋值给常量或变量或传递给函数时总是会被复制。）

有时确定两个常量或变量是否引用同一个类的实例是很有用的。为此，Swift 提供了两个身份运算符：

- 与 ( `===` ) 相同
- 与 ( `!==` ) 不相同

使用这些运算符来检查两个常量或变量是否引用同一个单一实例：

```swift
if tenEighty === alsoTenEighty {
    print("tenEighty and alsoTenEighty refer to the same VideoMode instance.")
}
// Prints "tenEighty and alsoTenEighty refer to the same VideoMode instance."
```

请注意，identical to（用三个等号表示，或 `===` ）并不意味着与 equal to（用两个等号表示，或 `==` ）相同。identical to 意味着两个类类型的常量或变量指向完全相同的类实例。equal to 意味着两个实例在某种适当的等价意义上被认为是相等或等价的，这种等价由类型的设计者定义。

当您定义自己的自定义结构和类时，决定两个实例是否相等是您的责任。定义您自己的 `==` 和 `!=` 运算符实现的过程在等价运算符中进行了描述。

### [指针](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures#Pointers)

如果您有 C、C++ 或 Objective-C 的经验，您可能知道这些语言使用指针来引用内存中的地址。一个引用某个引用类型实例的 Swift 常量或变量类似于 C 中的指针，但并不是直接指向内存地址的指针，也不需要您写一个星号 ( `*` ) 来表示您正在创建一个引用。相反，这些引用的定义方式与 Swift 中的其他常量或变量相同。Swift 标准库提供了指针和缓冲区类型，如果您需要直接与指针交互，可以使用它们 — 请参见手动内存管理。
