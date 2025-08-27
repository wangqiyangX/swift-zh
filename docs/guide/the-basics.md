# 基础语法

> 处理常见类型的数据并编写基本语法。

Swift 提供了许多基本数据类型，包括 `Int` 用于整数， `Double` 用于浮点值， `Bool` 用于布尔值，以及 `String` 用于文本。Swift 还提供了三种主要集合类型的强大版本， `Array` ， `Set` 和 `Dictionary` ，如集合类型中所述。

Swift 使用变量通过标识名称来存储和引用值。Swift 还广泛使用值不可更改的变量。这些被称为常量，并在 Swift 中普遍使用，以便在处理不需要更改的值时使代码更安全和意图更清晰。

除了熟悉的类型，Swift 还引入了高级类型，例如元组。元组使您能够创建和传递值的分组。您可以使用元组从函数返回多个值作为单个复合值。

Swift 还引入了可选类型，这些类型处理值的缺失。可选类型要么表示“存在一个值，并且它等于 x”，要么表示“根本不存在值”。

Swift 是一种类型安全的语言，这意味着该语言帮助您清楚地了解您的代码可以处理的值的类型。如果您的代码的某一部分需要一个 `String` ，类型安全可以防止您错误地传递一个 `Int` 。同样，类型安全可以防止您意外地将一个可选的 `String` 传递给需要非可选的 `String` 的代码。类型安全帮助您在开发过程中尽早捕捉和修复错误。

## [常量和变量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Constants-and-Variables)

常量和变量将一个名称（例如 `maximumNumberOfLoginAttempts` 或 `welcomeMessage` ）与特定类型的值（例如数字 `10` 或字符串 `"Hello"` ）关联。常量的值一旦设置就无法更改，而变量可以在将来设置为不同的值。

### [声明常量和变量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Declaring-Constants-and-Variables)

常量和变量必须在使用之前声明。您可以使用 `let` 关键字声明常量，使用 `var` 关键字声明变量。以下是如何使用常量和变量来跟踪用户登录尝试次数的示例：

```swift
let maximumNumberOfLoginAttempts = 10
var currentLoginAttempt = 0
```

该代码可以被理解为：

"“声明一个名为 `maximumNumberOfLoginAttempts` 的新常量，并赋值为 `10` 。然后，声明一个名为 `currentLoginAttempt` 的新变量，并赋一个初始值 `0` 。”"

在这个例子中，允许的最大登录尝试次数被声明为常量，因为最大值永远不会改变。当前的登录尝试计数器被声明为变量，因为这个值必须在每次登录尝试失败后递增。

如果您代码中的存储值不会改变，请始终使用 `let` 关键字将其声明为常量。仅将变量用于存储会改变的值。

当您声明常量或变量时，可以在该声明中给它一个值，如上面的示例所示。或者，您可以在程序中稍后为其分配初始值，只要在第一次读取之前保证它有一个值。

```swift
var environment = "development"
let maximumNumberOfLoginAttempts: Int
// maximumNumberOfLoginAttempts has no value yet.


if environment == "development" {
    maximumNumberOfLoginAttempts = 100
} else {
    maximumNumberOfLoginAttempts = 10
}
// Now maximumNumberOfLoginAttempts has a value, and can be read.
```

在这个例子中，最大登录尝试次数是常量，其值取决于环境。在开发环境中，它的值为 100；在其他任何环境中，它的值为 10。 `if` 语句的两个分支都用某个值初始化 `maximumNumberOfLoginAttempts` ，确保常量始终获得一个值。有关 Swift 如何在您以这种方式设置初始值时检查代码的信息，请参见常量声明。

您可以在一行中声明多个常量或多个变量，用逗号分隔：

```swift
var x = 0.0, y = 0.0, z = 0.0
```

### [类型注解](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Type-Annotations)

您可以在声明常量或变量时提供类型注解，以清楚说明常量或变量可以存储的值的类型。通过在常量或变量名称后面加上冒号，接着是一个空格，再跟上要使用的类型名称来编写类型注解。

该示例为名为 `welcomeMessage` 的变量提供类型注解，以指示该变量可以存储 `String` 类型的值：

```swift
var welcomeMessage: String
```

声明中的冒号表示“…类型为…”，因此上面的代码可理解为：

"“声明一个叫 `welcomeMessage` 的变量，它的类型是 `String` 。”"

短语“类型为 `String` ”意味着“可以存储任何 `String` 值。”可以理解为“事物的类型”（或“事物的种类”）可以被存储。

现在可以将 `welcomeMessage` 变量设置为任何字符串值而不会出错：

```swift
welcomeMessage = "Hello"
```

您可以在单行中定义多个相关变量，它们具有相同的类型，用逗号分隔，并在最后一个变量名称后添加单个类型注释：

```swift
var red, green, blue: Double
```

> **注意**
>
> 在实践中，您很少需要编写类型注释。如果您在定义常量或变量时提供初始值，Swift 几乎总是可以推断出该常量或变量要使用的类型，如《类型安全与类型推断》中所述。在上面的 `welcomeMessage` 示例中，没有提供初始值，因此 `welcomeMessage` 变量的类型是通过类型注释指定的，而不是从初始值推断出来的。

### [命名常量和变量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Naming-Constants-and-Variables)

常量和变量名称可以包含几乎任何字符，包括 Unicode 字符：

```swift
let π = 3.14159
let 您好 = "您好世界"
let 🐶🐮 = "dogcow"
```

常量和变量名称不能包含空白字符、数学符号、箭头、私用 Unicode 标量值或行和框绘制字符。它们也不能以数字开头，尽管数字可以在名称的其他地方包含。

一旦您声明了某种类型的常量或变量，就不能使用相同的名称再次声明它，或更改它以存储不同类型的值。您也不能将常量更改为变量或将变量更改为常量。

> 注意
>
> 如果您需要给常量或变量使用与保留的 Swift 关键字相同的名称，请在使用它作为名称时用反引号（ `` ` `` ）将关键字括起来。然而，除非您别无选择，否则请避免使用关键字作为名称。

您可以将现有变量的值更改为另一种兼容类型的值。在此示例中， `friendlyWelcome` 的值从 `"Hello!"` 更改为 `"Bonjour!"` :

```swift
var friendlyWelcome = "Hello!"
friendlyWelcome = "Bonjour!"
// friendlyWelcome is now "Bonjour!"
```

与变量不同，常量的值在设置后无法更改。尝试这样做将在编译代码时报告为错误：

```swift
let languageName = "Swift"
languageName = "Swift++"
// This is a compile-time error: languageName cannot be changed.
```

### [打印常量和变量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Printing-Constants-and-Variables)

您可以使用 `print(_:separator:terminator:)` 函数打印常量或变量的当前值：

```swift
print(friendlyWelcome)
// Prints "Bonjour!"
```

`print(_:separator:terminator:)` 函数是一个全局函数，用于将一个或多个值打印到适当的输出。例如，在 Xcode 中， `print(_:separator:terminator:)` 函数在 Xcode 的“控制台”窗格中打印其输出。 `separator` 和 `terminator` 参数有默认值，因此在调用此函数时可以省略它们。默认情况下，函数通过添加换行符来结束其打印的行。要打印一个值而不在其后换行，请将空字符串作为终止符传递 — 例如， `print(someValue, terminator: "")` 。有关具有默认值的参数的信息，请参见默认参数值。

Swift 使用字符串插值将常量或变量的名称作为占位符包含在更长的字符串中，并提示 Swift 用该常量或变量的当前值替换它。将名称用括号括起来，并在开括号前用反斜杠转义：

```swift
print("The current value of friendlyWelcome is \(friendlyWelcome)")
// Prints "The current value of friendlyWelcome is Bonjour!"
```

> 注意
>
> 所有可以与字符串插值一起使用的选项都在字符串插值中描述。

## [注释](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Comments)

使用注释在代码中包含不可执行的文本，作为对自己的备注或提醒。注释在编译代码时会被 Swift 编译器忽略。

Swift 中的注释与 C 中的注释非常相似。单行注释以两个斜杠 ( `//` ) 开头：

```swift
// This is a comment.
```

多行注释以一个斜杠后跟一个星号 ( `/*` ) 开始，以一个星号后跟一个斜杠 ( `*/` ) 结束：

```swift
/* This is also a comment
but is written over multiple lines. */
```

与 C 中的多行注释不同，Swift 中的多行注释可以嵌套在其他多行注释内部。您通过开始一个多行注释块，然后在第一个块内开始第二个多行注释来编写嵌套注释。然后关闭第二个块，接着关闭第一个块：

```swift
/* This is the start of the first multiline comment.
    /* This is the second, nested multiline comment. */
This is the end of the first multiline comment. */
```

嵌套多行注释使您能够快速且轻松地注释掉大块代码，即使代码已经包含多行注释。

## [分号](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Semicolons)

与许多其他语言不同，Swift 不要求您在代码中的每个语句后面写分号 ( `;` )，尽管您可以这样做。如果您想在单行上编写多个独立语句，分号是必需的：

```swift
let cat = "🐱"; print(cat)
// Prints "🐱"
```

## [整数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Integers)

整数是没有小数部分的整数，例如 `42` 和 `-23` 。整数可以是有符号的（正数、零或负数）或无符号的（正数或零）。

Swift 提供 8、16、32 和 64 位形式的有符号和无符号整数。这些整数遵循类似于 C 的命名约定，其中 8 位无符号整数的类型为 `UInt8` ，而 32 位有符号整数的类型为 `Int32` 。与 Swift 中的所有类型一样，这些整数类型的名称都是大写的。

### [整数边界](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Integer-Bounds)

您可以通过其 `min` 和 `max` 属性访问每种整数类型的最小值和最大值：

```swift
let minValue = UInt8.min  // minValue is equal to 0, and is of type UInt8
let maxValue = UInt8.max  // maxValue is equal to 255, and is of type UInt8
```

这些属性的值是适当大小的数字类型（例如上面的 `UInt8` ），因此可以与同类型的其他值一起用于表达式。

### [整型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Int)

在大多数情况下，您不需要选择特定大小的整数在代码中使用。Swift 提供了一种额外的整数类型 `Int` ，其大小与当前平台的本机字大小相同：

- 在 32 位平台上， `Int` 的大小与 `Int32` 相同。
- 在 64 位平台上， `Int` 的大小与 `Int64` 相同。

除非您需要处理特定大小的整数，否则在代码中始终使用 `Int` 作为整数值。这有助于代码的一致性和互操作性。即使在 32 位平台上， `Int` 也可以存储介于 `-2,147,483,648` 和 `2,147,483,647` 之间的任何值，并且足够大以容纳许多整数范围。

### [UInt](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#UInt)

Swift 还提供了一种无符号整数类型 `UInt` ，其大小与当前平台的本机字大小相同：

- 在 32 位平台上， `UInt` 的大小与 `UInt32` 相同。
- 在 64 位平台上， `UInt` 的大小与 `UInt64` 相同。

Note &#x20;

仅在您特别需要与平台的本地字长大小相同的无符号整数类型时使用 `UInt` 。如果不是这种情况，建议使用 `Int` ，即使已知要存储的值是非负的。对整数值一致使用 `Int` 有助于代码互操作性，避免在不同数值类型之间转换的需要，并与整数类型推断相匹配，如在类型安全和类型推断中所述。

## [浮点数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Floating-Point-Numbers)

浮点数是具有小数部分的数字，例如 `3.14159` , `0.1` , 和 `-273.15` .

浮点类型可以表示比整数类型更广泛的数值范围，并且可以存储的数字比可以存储在 `Int` 中的数字要大得多或小得多。 Swift 提供了两种带符号的浮点数类型：

- `Double` 代表一个 64 位浮点数。
- `Float` 代表一个 32 位浮点数。

Note

`Double` 至少具有 15 位小数的精度，而 `Float` 的精度可以少至 6 位小数。适合使用的浮点类型取决于您在代码中需要处理的值的性质和范围。在任一类型都适用的情况下，建议使用 `Double` 。

## [类型安全与类型推断](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Type-Safety-and-Type-Inference)

Swift 是一种类型安全的语言。类型安全的语言鼓励您清楚地了解您的代码可以处理的值的类型。如果您的代码的某部分需要一个 `String` ，您不能错误地传递一个 `Int` 。

因为 Swift 是类型安全的，它在编译您的代码时会进行类型检查，并将任何不匹配的类型标记为错误。这使您能够在开发过程中尽早捕捉和修复错误。

类型检查帮助您在处理不同类型的值时避免错误。然而，这并不意味着您必须为每个声明的常量和变量指定类型。如果您不指定所需值的类型，Swift 会使用类型推断来确定适当的类型。类型推断使编译器能够在编译您的代码时自动推断特定表达式的类型，仅通过检查您提供的值。

由于类型推断，Swift 需要的类型声明远少于 C 或 Objective-C 等语言。常量和变量仍然是显式类型的，但指定它们类型的大部分工作都是为您完成的。

类型推断在您声明一个带有初始值的常量或变量时尤其有用。这通常通过在声明时将一个文字值（或字面值）赋给常量或变量来完成。（字面值是直接出现在源代码中的值，例如下面示例中的 `42` 和 `3.14159` 。）

例如，如果您将一个字面值 `42` 赋给一个新常量而没有说明它的类型，Swift 推断您希望常量是一个 `Int` ，因为您用一个看起来像整数的数字初始化了它：

```swift
let meaningOfLife = 42
// meaningOfLife is inferred to be of type Int
```

同样，如果您没有为浮点字面值指定类型，Swift 推断您想创建一个 `Double` ：

```swift
let pi = 3.14159
// pi is inferred to be of type Double
```

Swift 在推断浮点数的类型时总是选择 `Double` （而不是 `Float` ）。

如果在表达式中组合整数和浮点文字，则会根据上下文推断出一个类型 `Double` :

```swift
let anotherPi = 3 + 0.14159
// anotherPi is also inferred to be of type Double
```

`3` 的字面值本身没有明确的类型，因此根据作为加法一部分的浮点文字的存在，推断出适当的输出类型 `Double` 。

## [数字字面量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Numeric-Literals)

整数字面量可以写成：

- 一个十进制数字，没有前缀
- 一个二进制数字，带有 `0b` 前缀
- 一个八进制数字，前缀为 `0o`
- 一个十六进制数字，前缀为 `0x`

所有这些整数字面量的十进制值为 `17` :

```swift
let decimalInteger = 17
let binaryInteger = 0b10001       // 17 in binary notation
let octalInteger = 0o21           // 17 in octal notation
let hexadecimalInteger = 0x11     // 17 in hexadecimal notation
```

浮点字面量可以是十进制（没有前缀），或者十六进制（前缀为 `0x` ）。它们必须始终在小数点两侧有数字（或十六进制数字）。十进制浮点数还可以有一个可选的指数，用大写或小写 `e` 表示；十六进制浮点数必须有一个指数，用大写或小写 `p` 表示。

对于指数为 `x` 的十进制数，基数乘以 10ˣ：

- `1.25e2` 意味着 1.25 x 10²，或者 `125.0` 。
- `1.25e-2` 意味着 1.25 x 10⁻²，或者 `0.0125` 。

对于指数为 `x` 的十六进制数，基数乘以 2ˣ：

- `0xFp2` 表示 15 x 2²，或 `60.0` 。
- `0xFp-2` 表示 15 x 2⁻²，或 `3.75` 。

所有这些浮点文字的十进制值为 `12.1875` ：

```swift
let decimalDouble = 12.1875
let exponentDouble = 1.21875e1
let hexadecimalDouble = 0xC.3p0
```

数值文字可以包含额外的格式，以使它们更易于阅读。整数和浮点数都可以用额外的零进行填充，并且可以包含下划线以帮助可读性。这两种格式都不会影响文字的基本值：

```swift
let paddedDouble = 000123.456
let oneMillion = 1_000_000
let justOverOneMillion = 1_000_000.000_000_1
```

## [数值类型转换](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Numeric-Type-Conversion)

在您的代码中，对于所有通用整数常量和变量，请使用 `Int` 类型，即使它们已知为非负。 在日常情况下使用默认整数类型意味着整数常量和变量在您的代码中是立即可互操作的，并且将与整数字面值的推断类型匹配。

仅在特定需要时使用其他整数类型，例如由于来自外部源的显式大小数据，或出于性能、内存使用或其他必要的优化。 在这些情况下使用显式大小类型有助于捕获任何意外的值溢出，并隐式记录所使用数据的性质。

### [整数转换](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Integer-Conversion)

整数常量或变量可以存储的数字范围因数字类型而异。一个 `Int8` 常量或变量可以存储介于 `-128` 和 `127` 之间的数字，而一个 `UInt8` 常量或变量可以存储介于 `0` 和 `255` 之间的数字。一个无法适应指定大小整数类型的常量或变量的数字在编译代码时会被报告为错误：

```swift
let cannotBeNegative: UInt8 = -1
// UInt8 can't store negative numbers, and so this will report an error
let tooBig: Int8 = Int8.max + 1
// Int8 can't store a number larger than its maximum value,
// and so this will also report an error
```

因为每种数值类型可以存储不同范围的值，所以您必须逐个案例选择数字类型转换。此选择性的方法可以防止隐藏的转换错误，并帮助在代码中明确转换意图。

要将一种特定的数字类型转换为另一种，您可以使用现有值初始化所需类型的新数字。在下面的示例中，常量 `twoThousand` 的类型是 `UInt16` ，而常量 `one` 的类型是 `UInt8` 。它们不能直接相加，因为它们不是同一类型。相反，此示例调用 `UInt16(one)` 来创建一个以 `one` 的值初始化的新 `UInt16` ，并在原始位置使用此值：

```swift
let twoThousand: UInt16 = 2_000
let one: UInt8 = 1
let twoThousandAndOne = twoThousand + UInt16(one)
```

因为加法的两边现在都是类型 `UInt16` ，所以允许进行加法。输出常量 ( `twoThousandAndOne` ) 推断为类型 `UInt16` ，因为它是两个 `UInt16` 值的和。

`SomeType(ofInitialValue)` 是调用 Swift 类型初始化器并传递初始值的默认方式。在幕后， `UInt16` 有一个接受 `UInt8` 值的初始化器，因此这个初始化器用于从现有的 `UInt8` 创建一个新的 `UInt16` 。然而，您不能在这里传递任何类型——它必须是 `UInt16` 提供初始化器的类型。扩展现有类型以提供接受新类型（包括您自己的类型定义）的初始化器的内容在扩展中进行了说明。

### [整数和浮点数转换](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Integer-and-Floating-Point-Conversion)

整数和浮点数字类型之间的转换必须明确进行:

```swift
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = Double(three) + pointOneFourOneFiveNine
// pi equals 3.14159, and is inferred to be of type Double
```

在这里，常量 `three` 的值用于创建一个新的 `Double` 类型的值，以便加法两边都是相同类型。如果没有这个转换，加法将不被允许。

浮点到整数的转换也必须明确。整数类型可以使用 `Double` 或 `Float` 值进行初始化:

```swift
let integerPi = Int(pi)
// integerPi equals 3, and is inferred to be of type Int
```

浮点值在以这种方式初始化新的整数值时总是被截断。这意味着 `4.75` 变为 `4` ，而 `-3.9` 变为 `-3` 。

Note

将数值常量和变量组合的规则与数值字面量的规则不同。字面值 `3` 可以直接与字面值 `0.14159` 相加，因为数字字面量本身没有明确的类型。它们的类型仅在被编译器评估时推断。

## [类型别名](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Type-Aliases)

类型别名为现有类型定义了一个替代名称。您可以使用 `typealias` 关键字定义类型别名。

当您想用一个在上下文中更合适的名称来引用现有类型时，类型别名非常有用，例如在处理来自外部源的特定大小的数据时：

```swift
typealias AudioSample = UInt16
```

一旦您定义了一个类型别名，就可以在任何可能使用原始名称的地方使用该别名：

```swift
var maxAmplitudeFound = AudioSample.min
// maxAmplitudeFound is now 0
```

在这里， `AudioSample` 被定义为 `UInt16` 的别名。因为它是一个别名，对 `AudioSample.min` 的调用实际上调用了 `UInt16.min` ，它为 `maxAmplitudeFound` 变量提供了初始值 `0` 。

## [布尔值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Booleans)

Swift 有一个基本的布尔类型，称为 `Bool` 。布尔值被称为逻辑值，因为它们只能是真或假。Swift 提供了两个布尔常量值， `true` 和 `false` ：

```swift
let orangesAreOrange = true
let turnipsAreDelicious = false
```

`orangesAreOrange` 和 `turnipsAreDelicious` 的类型已被推断为 `Bool` ，因为它们是用布尔字面量值初始化的。与上面的 `Int` 和 `Double` 一样，如果您在创建它们时立即将它们设置为 `true` 或 `false` ，则不需要将常量或变量声明为 `Bool` 。类型推断有助于在用其他已知类型的值初始化常量或变量时，使 Swift 代码更简洁和可读。

布尔值在处理条件语句时特别有用，例如 `if` 语句：

```swift
if turnipsAreDelicious {
    print("Mmm, tasty turnips!")
} else {
    print("Eww, turnips are horrible.")
}
// Prints "Eww, turnips are horrible."
```

条件语句，例如 `if` 语句，在控制流中有更详细的介绍。

Swift 的类型安全性防止非布尔值被替代为 `Bool` 。以下示例报告了一个编译时错误：

```swift
let i = 1
if i {
    // this example will not compile, and will report an error
}
```

然而，下面的替代示例是有效的：

```swift
let i = 1
if i == 1 {
    // this example will compile successfully
}
```

`i == 1` 比较的结果类型为 `Bool` ，因此这个第二个示例通过了类型检查。像 `i == 1` 这样的比较在基本运算符中讨论。

与 Swift 中类型安全的其他示例一样，这种方法避免了意外错误，并确保特定代码段的意图始终清晰。

## [元组](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Tuples)

元组将多个值组合成一个单一的复合值。元组中的值可以是任何类型，并且不必相互具有相同的类型。

在这个例子中， `(404, "Not Found")` 是一个描述 HTTP 状态代码的元组。当您请求一个网页时，HTTP 状态代码是由 Web 服务器返回的特殊值。如果您请求一个不存在的网页，将返回状态代码 `404 Not Found` 。

```swift
let http404Error = (404, "Not Found")
// http404Error is of type (Int, String), and equals (404, "Not Found")
```

`(404, "Not Found")` 元组将 `Int` 和 `String` 组合在一起，以提供 HTTP 状态代码的两个单独值：一个数字和一个人类可读的描述。它可以被描述为“类型为 `(Int, String)` 的元组”。

您可以从任何类型的排列中创建元组，并且它们可以包含您喜欢的多种不同类型。没有什么能阻止您拥有一个类型为 `(Int, Int, Int)` 的元组，或 `(String, Bool)` ，甚至是您所需的任何其他排列。

您可以将元组的内容分解为单独的常量或变量，然后按 usual 方式访问它们：

```swift
let (statusCode, statusMessage) = http404Error
print("The status code is \(statusCode)")
// Prints "The status code is 404"
print("The status message is \(statusMessage)")
// Prints "The status message is Not Found"
```

如果您只需要元组的某些值，则在分解元组时使用下划线 ( `_` ) 忽略元组的部分内容：

```swift
let (justTheStatusCode, _) = http404Error
print("The status code is \(justTheStatusCode)")
// Prints "The status code is 404"
```

或者，使用从零开始的索引号码访问元组中的单个元素值：

```swift
print("The status code is \(http404Error.0)")
// Prints "The status code is 404"
print("The status message is \(http404Error.1)")
// Prints "The status message is Not Found"
```

您可以在定义元组时命名元组中的各个元素：

```swift
let http200Status = (statusCode: 200, description: "OK")
```

如果您给元组中的元素命名，您可以使用元素名称来访问这些元素的值：

```swift
print("The status code is \(http200Status.statusCode)")
// Prints "The status code is 200"
print("The status message is \(http200Status.description)")
// Prints "The status message is OK"
```

元组特别适合作为函数的返回值。一个尝试检索网页的函数可能返回 `(Int, String)` 元组类型，以描述页面检索的成功或失败。通过返回一个具有两个不同类型的不同值的元组，该函数提供了比只能返回单一类型的单一值更有用的信息。有关更多信息，请参见具有多个返回值的函数。

> 注意
>
> 元组对于简单相关值的分组非常有用。它们不适合创建复杂的数据结构。如果您的数据结构可能更复杂，请将其建模为类或结构，而不是元组。有关更多信息，请参见结构和类。

## [可选值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Optionals)

您在值可能缺失的情况下使用可选值。可选值表示两种可能性：要么有一个指定类型的值，您可以解包可选值以访问该值，要么根本没有值。

作为一个可能缺失的值的示例，Swift 的 `Int` 类型有一个初始化器，它尝试将 `String` 值转换为 `Int` 值。然而，只有某些字符串可以转换为整数。字符串 `"123"` 可以转换为数字值 `123` ，但字符串 `"hello, world"` 没有对应的数字值。下面的示例使用初始化器尝试将 `String` 转换为 `Int` ：

```swift
let possibleNumber = "123"
let convertedNumber = Int(possibleNumber)
// The type of convertedNumber is "optional Int"
```

由于上述代码中的初始化器可能失败，因此它返回一个可选的 `Int` ，而不是 `Int` 。

要编写可选类型，您在可选包含的类型名称之后写一个问号 ( `?` ) — 例如，可选 `Int` 的类型是 `Int?` 。可选 `Int` 始终包含一些 `Int` 值或根本没有值。它不能包含其他任何东西，例如 `Bool` 或 `String` 值。

### [nil](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#nil)

通过将可选变量赋值为特殊值 `nil` ，您将其设置为无值状态：

```swift
var serverResponseCode: Int? = 404
// serverResponseCode contains an actual Int value of 404
serverResponseCode = nil
// serverResponseCode now contains no value
```

如果您定义一个可选变量而不提供默认值，该变量将自动设置为 `nil` ：

```swift
var surveyAnswer: String?
// surveyAnswer is automatically set to nil
```

您可以使用 `if` 语句来查找一个可选项是否包含值，通过将可选项与 `nil` 进行比较。您可以使用“等于”运算符 ( `==` ) 或“不等于”运算符 ( `!=` ) 来执行此比较。

如果一个可选值有值，它被视为“不等于” `nil` :

```swift
let possibleNumber = "123"
let convertedNumber = Int(possibleNumber)


if convertedNumber != nil {
    print("convertedNumber contains some integer value.")
}
// Prints "convertedNumber contains some integer value."
```

您无法将 `nil` 与非可选常量或变量一起使用。如果您代码中的常量或变量需要在某些条件下处理缺少值，则将其声明为适当类型的可选值。声明为非可选值的常量或变量保证永远不会包含 `nil` 值。如果您尝试将 `nil` 赋值给非可选值，您将收到编译时错误。

可选值和非可选值的这种分离使您能够明确标记哪些信息可以缺失，并使编写处理缺失值的代码变得更容易。您无法意外地将可选值视为非可选，因为这个错误会在编译时产生错误。在您解包值后，与该值相关的其他代码不需要检查 `nil` ，因此在您的代码不同部分中不需要反复检查相同的值。

当您访问可选值时，您的代码始终处理 `nil` 和非 `nil` 情况。当值缺失时，您可以做几件事情，如下节所述：

- 跳过对值为 `nil` 的操作代码。
- 通过返回 `nil` 或使用在可选链中描述的 `?.` 运算符来传播 `nil` 值。
- 使用 `??` 运算符提供一个后备值。
- 使用 `!` 运算符停止程序执行。

Note

在 Objective-C 中， `nil` 是指向不存在对象的指针。在 Swift 中， `nil` 不是指针——它是某种类型值的缺失。任何类型的可选值都可以设置为 `nil` ，不仅仅是对象类型。

### [可选绑定](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Optional-Binding)

您使用可选绑定来查找可选项是否包含值，如果包含，则将该值作为临时常量或变量提供。可选绑定可以与 `if` 、 `guard` 和 `while` 语句一起使用，以检查可选项内部是否有值，并将该值提取到常量或变量中，作为单个操作的一部分。有关 `if` 、 `guard` 和 `while` 语句的更多信息，请参阅控制流。

为 `if` 语句编写可选绑定如下：

```swift
if let <#constantName#> = <#someOptional#> {
   <#statements#>
}
```

您可以重写选项部分中的 `possibleNumber` 示例，以使用可选绑定而不是强制解包：

```swift
if let actualNumber = Int(possibleNumber) {
    print("The string \"\(possibleNumber)\" has an integer value of \(actualNumber)")
} else {
    print("The string \"\(possibleNumber)\" couldn't be converted to an integer")
}
// Prints "The string "123" has an integer value of 123"
```

该代码可以被理解为：

"“如果 `Int(possibleNumber)` 返回的可选 `Int` 包含一个值，则将一个名为 `actualNumber` 的新常量设置为可选中包含的值。”"

如果转换成功， `actualNumber` 常量将在 `if` 语句的第一个分支中可用。它已经用可选中包含的值进行了初始化，并具有相应的非可选类型。在这种情况下， `possibleNumber` 的类型是 `Int?` ，因此 `actualNumber` 的类型是 `Int` 。

如果在访问它包含的值后不需要引用原始的可选常量或变量，可以使用相同的名称为新的常量或变量：

```swift
let myNumber = Int(possibleNumber)
// Here, myNumber is an optional integer
if let myNumber = myNumber {
    // Here, myNumber is a non-optional integer
    print("My number is \(myNumber)")
}
// Prints "My number is 123"
```

这段代码首先检查 `myNumber` 是否包含一个值，就像前一个示例中的代码一样。如果 `myNumber` 有一个值，则名为 `myNumber` 的新常量的值被设置为该值。在 `if` 语句的主体内，写 `myNumber` 指的是那个新的非可选常量。在 `if` 语句之前或之后写 `myNumber` 指的是原始的可选整数常量。

因为这类代码非常常见，您可以使用更简短的拼写来解包可选值：只需写出您正在解包的常量或变量的名称。新的解包常量或变量隐式使用与可选值相同的名称。

```swift
if let myNumber {
    print("My number is \(myNumber)")
}
// Prints "My number is 123"
```

您可以在可选绑定中使用常量和变量。如果您想在 `if` 语句的第一个分支中操作 `myNumber` 的值，您可以改为写 `if var myNumber` ，并且所包含的可选值将作为变量而不是常量可用。您在 `if` 语句体内对 `myNumber` 所做的更改仅适用于该局部变量，而不适用于您解包的原始可选常量或变量。

您可以在单个 `if` 语句中根据需要包含多个可选绑定和布尔条件，使用逗号分隔。如果可选绑定中的任何值是 `nil` 或任何布尔条件评估为 `false` ，则整个 `if` 语句的条件被视为 `false` 。以下 `if` 语句是等效的：

```swift
if let firstNumber = Int("4"), let secondNumber = Int("42"), firstNumber < secondNumber && secondNumber < 100 {
    print("\(firstNumber) < \(secondNumber) < 100")
}
// Prints "4 < 42 < 100"


if let firstNumber = Int("4") {
    if let secondNumber = Int("42") {
        if firstNumber < secondNumber && secondNumber < 100 {
            print("\(firstNumber) < \(secondNumber) < 100")
        }
    }
}
// Prints "4 < 42 < 100"
```

在 `if` 语句的主体内创建的常量和变量仅在该主体内有效。相比之下，使用 `guard` 语句创建的常量和变量在 `guard` 语句之后的代码行中可用，如“早期退出”中所述。

### [提供备用值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Providing-a-Fallback-Value)

处理缺失值的另一种方法是使用 nil 合并运算符 ( `??` ) 提供默认值。如果 `??` 左侧的可选值不是 `nil` ，则该值被解包并使用。否则，使用 `??` 右侧的值。例如，下面的代码在指定名称时按名称问候某人，当名称为 `nil` 时使用通用问候。

```swift
let name: String? = nil
let greeting = "Hello, " + (name ?? "friend") + "!"
print(greeting)
// Prints "Hello, friend!"
```

有关使用 `??` 提供后备值的更多信息，请参见 nil 合并运算符。

### [强制解包](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Force-Unwrapping)

当 `nil` 表示不可恢复的失败，例如程序员错误或损坏的状态时，可以通过在可选值名称的末尾添加感叹号 ( `!` ) 来访问底层值。这被称为强制解包可选值。当您强制解包一个非 `nil` 值时，结果是其解包值。强制解包一个 `nil` 值会触发运行时错误。

`!` 实际上是 `fatalError(_:file:line:)` 的一种更短的拼写。例如，下面的代码展示了两种等效的方法：

```swift
let possibleNumber = "123"
let convertedNumber = Int(possibleNumber)


let number = convertedNumber!


guard let number = convertedNumber else {
    fatalError("The number was invalid")
}
```

上面两种版本的代码都依赖于 `convertedNumber` 始终包含一个值。将这一要求作为代码的一部分，使用上面任一方法，可以让您的代码在运行时检查这一要求是否为真。

有关强制数据要求和在运行时检查假设的更多信息，请参见断言和前提条件。

### [隐式展开的可选值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Implicitly-Unwrapped-Optionals)

如上所述，选项表示常量或变量允许“没有值”。可以使用 `if` 语句检查选项值是否存在，并可以通过可选绑定有条件地展开以访问选项的值（如果存在的话）。

有时，从程序的结构中可以清楚地看出，选项在首次设置该值后将始终有一个值。在这些情况下，移除每次访问选项值时需要检查和展开的必要性是有用的，因为可以安全地假设它始终有一个值。

这些类型的选项被定义为隐式展开选项。通过在您希望使其为选项的类型后放置一个感叹号（ `String!` ）而不是问号（ `String?` ），来编写隐式展开选项。使用选项时，不是在选项名称后放置感叹号，而是在声明其类型时放置感叹号。

隐式解包可选值在可选值的值在首次定义后立即确认存在，并且在随后的任何时刻都可以确定存在时非常有用。隐式解包可选值在 Swift 中的主要用途是在类初始化期间，如《无主引用》和《隐式解包可选属性》中所述。

当变量在某个后期点有可能变为 `nil` 时，请不要使用隐式解包可选值。如果您需要在变量的生命周期内检查 `nil` 值，请始终使用普通可选类型。

隐式解包可选值在幕后是一个普通的可选值，但可以像非可选值一样使用，无需每次访问时都解包可选值。以下示例显示了在将其包装值作为显式 `String` 访问时，可选字符串和隐式解包可选字符串之间行为的差异：

```swift
let possibleString: String? = "An optional string."
let forcedString: String = possibleString! // Requires explicit unwrapping


let assumedString: String! = "An implicitly unwrapped optional string."
let implicitString: String = assumedString // Unwrapped automatically
```

您可以将隐式解包可选值视为在需要时允许强制解包可选值。当您使用隐式解包可选值时，Swift 首先尝试将其用作普通可选值；如果无法作为可选值使用，Swift 将强制解包该值。在上面的代码中，可选值 `assumedString` 在将其值分配给 `implicitString` 之前被强制解包，因为 `implicitString` 具有显式的非可选类型 `String` 。在下面的代码中， `optionalString` 没有显式类型，因此它是一个普通可选值。

```swift
let optionalString = assumedString
// The type of optionalString is "String?" and assumedString isn't force-unwrapped.
```

如果隐式解包可选值是 `nil` ，并且您尝试访问其包装值，您将触发运行时错误。结果与您写一个感叹号以强制解包一个不包含值的普通可选值完全相同。

您可以通过与检查普通可选值相同的方式检查隐式解包可选值是否是 `nil` ：

```swift
if assumedString != nil {
    print(assumedString!)
}
// Prints "An implicitly unwrapped optional string."
```

您还可以使用隐式解包可选值与可选绑定，在单个语句中检查并解包其值：

```swift
if let definiteString = assumedString {
    print(definiteString)
}
// Prints "An implicitly unwrapped optional string."
```

## [错误处理](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Error-Handling)

您使用错误处理来响应程序在执行过程中可能遇到的错误条件。

与可选值不同，可选值可以使用值的存在或缺失来传达函数的成功或失败，错误处理允许您确定失败的根本原因，并在必要时将错误传播到程序的其他部分。

当一个函数遇到错误条件时，它会抛出一个错误。该函数的调用者可以捕获该错误并做出适当的响应。

```swift
func canThrowAnError() throws {
    // this function may or may not throw an error
}
```

一个函数通过在其声明中包含 `throws` 关键字来表明它可以抛出一个错误。当您调用一个可以抛出错误的函数时，您需要在表达式前加上 `try` 关键字。

Swift 自动将错误传播到当前作用域外，直到它们被 `catch` 子句处理。

```swift
do {
    try canThrowAnError()
    // no error was thrown
} catch {
    // an error was thrown
}
```

一个 `do` 语句创建一个新的包含作用域，允许错误传播到一个或多个 `catch` 子句。

以下是如何使用错误处理来响应不同错误条件的示例：

```swift
func makeASandwich() throws {
    // ...
}


do {
    try makeASandwich()
    eatASandwich()
} catch SandwichError.outOfCleanDishes {
    washDishes()
} catch SandwichError.missingIngredients(let ingredients) {
    buyGroceries(ingredients)
}
```

在这个例子中， `makeASandwich()` 函数将在没有干净的盘子可用或缺少任何成分时抛出错误。因为 `makeASandwich()` 可能会抛出错误，所以函数调用被包装在 `try` 表达式中。通过将函数调用包装在 `do` 语句中，任何抛出的错误将被传播到提供的 `catch` 条款。

如果没有抛出错误，则调用 `eatASandwich()` 函数。如果抛出错误并且它匹配 `SandwichError.outOfCleanDishes` 情况，则将调用 `washDishes()` 函数。如果抛出错误并且它匹配 `SandwichError.missingIngredients` 情况，则将调用 `buyGroceries(_:)` 函数，并使用 `catch` 模式捕获的相关 `[String]` 值。

抛出、捕获和传播错误的详细信息在错误处理部分中有更深入的讨论。

## [断言和前置条件](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Assertions-and-Preconditions)

断言和前置条件是在运行时发生的检查。您使用它们来确保在执行任何进一步代码之前满足基本条件。如果断言或前置条件中的布尔条件评估为 `true` ，代码执行将照常继续。如果条件评估为 `false` ，程序的当前状态无效；代码执行结束，您的应用程序被终止。

您使用断言和前提条件来表达您在编码时所做的假设和您所期望的，因此您可以将它们作为代码的一部分包含进来。断言帮助您在开发过程中发现错误和不正确的假设，而前提条件帮助您在生产中检测问题。

除了在运行时验证您的期望外，断言和前提条件还成为代码中一种有用的文档形式。与上面讨论的错误处理中的错误条件不同，断言和前提条件不用于可恢复或预期的错误。因为失败的断言或前提条件表示无效的程序状态，因此没有办法捕获失败的断言。从无效状态恢复是不可能的。当断言失败时，至少有一部分程序的数据是无效的—但您不知道它为什么无效或是否还有其他状态也是无效的。

使用断言和前置条件并不能替代以合理的方式设计代码，从而使无效条件不太可能发生。然而，使用它们来强制执行有效数据和状态可以使应用在发生无效状态时更可预测地终止，并有助于简化问题的调试。当假设未被检查时，您可能不会注意到此类问题，直到代码的其他部分开始明显失败，并且用户数据已悄然损坏。在检测到无效状态后立即停止执行也有助于限制由该无效状态造成的损害。

断言和前置条件之间的区别在于它们被检查的时机：断言仅在调试构建中被检查，而前置条件在调试和生产构建中都被检查。在生产构建中，断言内部的条件不会被评估。这意味着在开发过程中，您可以使用任意数量的断言，而不会影响生产中的性能。

### [使用断言进行调试](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Debugging-with-Assertions)

您通过调用 Swift 标准库中的 `assert(_:_:file:line:)` 函数来编写断言。您将一个表达式传递给该函数，该表达式的结果为 `true` 或 `false` ，并且如果条件的结果为 `false` ，则显示一条消息。例如：

```swift
let age = -3
assert(age >= 0, "A person's age can't be less than zero.")
// This assertion fails because -3 isn't >= 0.
```

在这个例子中，如果 `age >= 0` 的结果为 `true` ，即 `age` 的值为非负数，则代码执行继续。如果 `age` 的值为负数，如上面的代码所示，则 `age >= 0` 的结果为 `false` ，断言失败，终止应用程序。

您可以省略断言消息——例如，当它只是以散文形式重复条件时。

```swift
assert(age >= 0)
```

如果代码已经检查了条件，您可以使用 `assertionFailure(_:file:line:)` 函数来指示断言失败。例如：

```swift
if age > 10 {
    print("You can ride the roller-coaster or the ferris wheel.")
} else if age >= 0 {
    print("You can ride the ferris wheel.")
} else {
    assertionFailure("A person's age can't be less than zero.")
}
```

### [强制前置条件](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Enforcing-Preconditions)

每当条件有可能为假，但必须为真以便您的代码继续执行时，请使用前置条件。例如，使用前置条件检查下标是否越界，或检查函数是否传递了有效值。

您通过调用 `precondition(_:_:file:line:)` 函数来编写前置条件。您将一个表达式传递给该函数，该表达式的值为 `true` 或 `false` ，以及一个消息，如果条件的结果为 `false` 则显示该消息。例如：

```swift
// In the implementation of a subscript...
precondition(index > 0, "Index must be greater than zero.")
```

您还可以调用 `preconditionFailure(_:file:line:)` 函数来指示发生了故障——例如，如果选择了 switch 的默认情况，但所有有效输入数据应该由 switch 的其他情况处理。

Note

如果您在未检查模式下编译（ `-Ounchecked` ），则不检查前置条件。编译器假定前置条件始终为真，并相应地优化您的代码。然而，无论优化设置如何， `fatalError(_:file:line:)` 函数始终会终止执行。

您可以在原型设计和早期开发阶段使用 `fatalError(_:file:line:)` 函数，通过将 `fatalError("Unimplemented")` 编写为存根实现来创建尚未实现的功能的存根。因为致命错误永远不会被优化掉，与断言或前置条件不同，您可以确信如果遇到存根实现，执行将始终停止。
