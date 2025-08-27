# 函数

> 定义和调用函数，标记它们的参数，并使用它们的返回值。

函数是执行特定任务的自包含代码块。您给函数一个名称，以识别它的功能，并在需要时使用该名称来“调用”函数以执行其任务。

Swift 的统一函数语法足够灵活，可以表达从不带参数名称的简单 C 风格函数到带有每个参数名称和参数标签的复杂 Objective-C 风格方法的任何内容。参数可以提供默认值，从而简化函数调用，并且可以作为输入输出参数传递，这会在函数执行完成后修改传入的变量。

在 Swift 中，每个函数都有一个类型，由函数的参数类型和返回类型组成。您可以像使用 Swift 中的任何其他类型一样使用此类型，这使得将函数作为参数传递给其他函数以及从函数返回函数变得简单。函数也可以在其他函数内部编写，以便在嵌套函数作用域内封装有用的功能。

## [定义和调用函数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Defining-and-Calling-Functions)

当您定义一个函数时，可以选择性地定义一个或多个命名的、有类型的值作为该函数的输入，称为参数。您还可以选择性地定义一个值的类型，作为函数完成时将返回的输出，称为其返回类型。

每个函数都有一个函数名称，用于描述该函数执行的任务。要使用一个函数，您需要通过其名称“调用”该函数，并传递与函数参数类型匹配的输入值（称为参数）。函数的参数必须始终按照函数的参数列表中提供的顺序提供。

下面示例中的函数称为 `greet(person:)` ，因为它的功能就是 — 它接受一个人的名字作为输入，并返回该人的问候。为此，您定义一个输入参数 — 一个名为 `person` 的 `String` 值 — 和一个返回类型 `String` ，该返回类型将包含对该人的问候：

```swift
func greet(person: String) -> String {
    let greeting = "Hello, " + person + "!"
    return greeting
}
```

所有这些信息被汇总到函数的定义中，该定义以 `func` 关键字为前缀。您使用返回箭头 `->` 指示函数的返回类型（一个连字符后跟一个右尖括号），后面跟着要返回的类型名称。

该定义描述了函数的功能、它期望接收的内容以及完成时返回的内容。该定义使得可以在代码的其他地方不含糊地调用该函数：

```swift
print(greet(person: "Anna"))
// Prints "Hello, Anna!"
print(greet(person: "Brian"))
// Prints "Hello, Brian!"
```

您通过在 `person` 参数标签后传递 `String` 值来调用 `greet(person:)` 函数，例如 `greet(person: "Anna")` 。因为该函数返回一个 `String` 值， `greet(person:)` 可以被包装在对 `print(_:separator:terminator:)` 函数的调用中，以打印该字符串并查看其返回值，如上所示。

注意

`print(_:separator:terminator:)` 函数没有第一个参数的标签，其他参数是可选的，因为它们有默认值。这些函数语法的变体在下面的“函数参数标签和参数名称”和“默认参数值”中进行了讨论。

`greet(person:)` 函数的主体首先定义一个名为 `greeting` 的新 `String` 常量，并将其设置为一个简单的问候消息。然后使用 `return` 关键字将此问候返回到函数外。在代码行 `return greeting` 中，函数完成其执行并返回 `greeting` 的当前值。

您可以多次调用 `greet(person:)` 函数，使用不同的输入值。上面的示例显示了如果使用输入值 `"Anna"` 和输入值 `"Brian"` 调用时会发生什么。该函数在每种情况下返回一个量身定制的问候。

为了使该函数的主体更简短，您可以将消息创建和返回语句合并为一行：

```swift
func greetAgain(person: String) -> String {
    return "Hello again, " + person + "!"
}
print(greetAgain(person: "Anna"))
// Prints "Hello again, Anna!"
```

## [函数参数和返回值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Function-Parameters-and-Return-Values)

在 Swift 中，函数参数和返回值非常灵活。您可以定义从带有单个未命名参数的简单实用函数到带有表达性参数名称和不同参数选项的复杂函数的任何内容。

### [无参数的函数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Functions-Without-Parameters)

函数不要求定义输入参数。这里是一个没有输入参数的函数，每当被调用时它总是返回相同的 `String` 消息：

```swift
func sayHelloWorld() -> String {
    return "hello, world"
}
print(sayHelloWorld())
// Prints "hello, world"
```

函数定义在函数名称后仍然需要括号，即使它不接受任何参数。当函数被调用时，函数名称后也跟着一对空括号。

### [具有多个参数的函数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Functions-With-Multiple-Parameters)

函数可以有多个输入参数，这些参数写在函数的括号中，用逗号分隔。

该函数接受一个人的名字和他是否已经被问候作为输入，并返回适合该人的问候语：

```swift
func greet(person: String, alreadyGreeted: Bool) -> String {
    if alreadyGreeted {
        return greetAgain(person: person)
    } else {
        return greet(person: person)
    }
}
print(greet(person: "Tim", alreadyGreeted: true))
// Prints "Hello again, Tim!"
```

您通过传递一个标记为 `person` 的 `String` 参数值和一个标记为 `alreadyGreeted` 的 `Bool` 参数值来调用 `greet(person:alreadyGreeted:)` 函数，这两个参数值用逗号分隔在括号中。请注意，此函数与前面部分中显示的 `greet(person:)` 函数不同。尽管这两个函数的名称都以 `greet` 开头，但 `greet(person:alreadyGreeted:)` 函数接受两个参数，而 `greet(person:)` 函数仅接受一个。

### [没有返回值的函数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Functions-Without-Return-Values)

函数不需要定义返回类型。以下是 `greet(person:)` 函数的一个版本，它打印自己的 `String` 值而不是返回它：

```swift
func greet(person: String) {
    print("Hello, \(person)!")
}
greet(person: "Dave")
// Prints "Hello, Dave!"
```

因为它不需要返回一个值，所以函数的定义不包括返回箭头 ( `->` ) 或返回类型。

> 注意
>
> 严格来说，这个版本的 `greet(person:)` 函数确实返回一个值，即使没有定义返回值。没有定义返回类型的函数返回一个特殊类型的值 `Void` 。这只是一个空元组，写作 `()` 。

当函数被调用时，可以忽略其返回值：

```swift
func printAndCount(string: String) -> Int {
    print(string)
    return string.count
}
func printWithoutCounting(string: String) {
    let _ = printAndCount(string: string)
}
printAndCount(string: "hello, world")
// prints "hello, world" and returns a value of 12
printWithoutCounting(string: "hello, world")
// prints "hello, world" but doesn't return a value
```

第一个函数， `printAndCount(string:)` ，打印一个字符串，然后返回其字符计数作为一个 `Int` 。第二个函数， `printWithoutCounting(string:)` ，调用第一个函数，但忽略其返回值。当调用第二个函数时，消息仍然由第一个函数打印，但返回的值没有被使用。

> 注意
>
> 返回值可以被忽略，但声明会返回值的函数必须始终返回。具有定义返回类型的函数不能在不返回值的情况下让控制流脱出函数的底部，尝试这样做会导致编译时错误。

### [具有多个返回值的函数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Functions-with-Multiple-Return-Values)

您可以使用元组类型作为函数的返回类型，以将多个值作为一个复合返回值的一部分返回。

下面的示例定义了一个名为 `minMax(array:)` 的函数，该函数在一个 `Int` 值的数组中找到最小和最大数字：

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

`minMax(array:)` 函数返回一个包含两个 `Int` 值的元组。这些值被标记为 `min` 和 `max` ，以便在查询函数的返回值时可以通过名称访问它们。

`minMax(array:)` 函数的主体首先将两个工作变量 `currentMin` 和 `currentMax` 设置为数组中第一个整数的值。然后，该函数遍历数组中的其余值，并检查每个值是否分别小于或大于 `currentMin` 和 `currentMax` 的值。最后，整体最小值和最大值作为两个 `Int` 值的元组返回。

因为元组的成员值作为函数返回类型的一部分被命名，可以使用点语法访问以检索找到的最小值和最大值：

```swift
let bounds = minMax(array: [8, -6, 2, 109, 3, 71])
print("min is \(bounds.min) and max is \(bounds.max)")
// Prints "min is -6 and max is 109"
```

请注意，元组的成员在从函数返回元组时不需要命名，因为它们的名称已经作为函数返回类型的一部分指定。

[**可选元组返回类型**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Optional-Tuple-Return-Types)

如果要从函数返回的元组类型可能对整个元组有“无值”的情况，您可以使用可选元组返回类型来反映整个元组可以是 `nil` 的事实。您通过在元组类型的闭合括号后放置问号来编写可选元组返回类型，例如 `(Int, Int)?` 或 `(String, Int, Bool)?` 。

> 注意
>
> 可选元组类型，例如 `(Int, Int)?` ，与包含可选类型的元组，例如 `(Int?, Int?)` ，是不同的。使用可选元组类型时，整个元组是可选的，而不仅仅是元组中的每个单独值。

上面的 `minMax(array:)` 函数返回一个包含两个 `Int` 值的元组。然而，该函数对传入的数组没有执行任何安全检查。如果 `array` 参数包含一个空数组，则如上所定义的 `minMax(array:)` 函数在尝试访问 `array[0]` 时将触发运行时错误。

为了安全地处理空数组，编写带有可选元组返回类型的 `minMax(array:)` 函数，并在数组为空时返回 `nil` 的值：

```swift
func minMax(array: [Int]) -> (min: Int, max: Int)? {
    if array.isEmpty { return nil }
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

您可以使用可选绑定来检查这个版本的 `minMax(array:)` 函数是否返回一个实际的元组值或 `nil` ：

```swift
if let bounds = minMax(array: [8, -6, 2, 109, 3, 71]) {
    print("min is \(bounds.min) and max is \(bounds.max)")
}
// Prints "min is -6 and max is 109"
```

### [隐式返回的函数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Functions-With-an-Implicit-Return)

如果函数的整个主体是一个单一的表达式，则该函数隐式返回该表达式。例如，下面的两个函数具有相同的行为：

```swift
func greeting(for person: String) -> String {
    "Hello, " + person + "!"
}
print(greeting(for: "Dave"))
// Prints "Hello, Dave!"


func anotherGreeting(for person: String) -> String {
    return "Hello, " + person + "!"
}
print(anotherGreeting(for: "Dave"))
// Prints "Hello, Dave!"
```

`greeting(for:)` 函数的整个定义是它返回的问候消息，这意味着它可以使用这种更短的形式。 `anotherGreeting(for:)` 函数返回相同的问候消息，使用 `return` 关键字就像一个更长的函数。您编写的任何仅为一行 `return` 的函数都可以省略 `return` 。

正如您在简写获取器声明中看到的，属性获取器也可以使用隐式返回。

> 注意
>
> 您作为隐式返回值编写的代码需要返回某个值。例如，您不能使用 `print(13)` 作为隐式返回值。然而，您可以使用一个从不返回的函数，比如 `fatalError("Oh no!")` ，作为隐式返回值，因为 Swift 知道隐式返回不会发生。

## [函数参数标签和参数名称](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Function-Argument-Labels-and-Parameter-Names)

每个函数参数都有一个参数标签和一个参数名称。参数标签在调用函数时使用；每个参数在函数调用中都以其参数标签在前的形式书写。参数名称在函数的实现中使用。默认情况下，参数使用其参数名称作为参数标签。

```swift
func someFunction(firstParameterName: Int, secondParameterName: Int) {
    // In the function body, firstParameterName and secondParameterName
    // refer to the argument values for the first and second parameters.
}
someFunction(firstParameterName: 1, secondParameterName: 2)
```

所有参数必须具有唯一的名称。尽管多个参数可以具有相同的参数标签，但唯一的参数标签有助于提高代码的可读性。

### [指定参数标签](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Specifying-Argument-Labels)

在参数名称之前写一个参数标签，用空格分隔：

```swift
func someFunction(argumentLabel parameterName: Int) {
    // In the function body, parameterName refers to the argument value
    // for that parameter.
}
```

这是一个变体的 `greet(person:)` 函数，它接受一个人的名字和家乡并返回问候：

```swift
func greet(person: String, from hometown: String) -> String {
    return "Hello \(person)!  Glad you could visit from \(hometown)."
}
print(greet(person: "Bill", from: "Cupertino"))
// Prints "Hello Bill!  Glad you could visit from Cupertino."
```

使用参数标签可以使函数以一种富有表现力、类似句子的方式被调用，同时仍然提供一个可读性强、意图明确的函数体。

### [省略参数标签](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Omitting-Argument-Labels)

如果您不想为参数提供参数标签，请写一个下划线 ( `_` ) 代替该参数的显式参数标签。

```swift
func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
    // In the function body, firstParameterName and secondParameterName
    // refer to the argument values for the first and second parameters.
}
someFunction(1, secondParameterName: 2)
```

如果参数有一个参数标签，则在调用函数时必须标记该参数。

### [默认参数值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Default-Parameter-Values)

您可以通过在参数类型后为参数分配一个值来为函数中的任何参数定义默认值。如果定义了默认值，在调用该函数时可以省略该参数。

```swift
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // If you omit the second argument when calling this function, then
    // the value of parameterWithDefault is 12 inside the function body.
}
someFunction(parameterWithoutDefault: 3, parameterWithDefault: 6) // parameterWithDefault is 6
someFunction(parameterWithoutDefault: 4) // parameterWithDefault is 12
```

将没有默认值的参数放在函数参数列表的开头，位于具有默认值的参数之前。没有默认值的参数通常对函数的含义更为重要——首先写出它们可以更容易地识别出调用的是同一个函数，无论是否省略了任何默认参数。

### [可变参数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Variadic-Parameters)

可变参数接受零个或多个指定类型的值。您使用可变参数来指定在调用函数时可以传递不同数量的输入值。通过在参数类型名称后插入三个句点字符 ( `...` ) 来编写可变参数。

传递给可变参数的值在函数体内作为适当类型的数组可用。例如，一个名为 `numbers` 且类型为 `Double...` 的可变参数在函数体内作为一个名为 `numbers` 的常量数组可用，类型为 `[Double]` 。

下面的示例计算任意长度数字列表的算术平均数（也称为平均值）：

```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// returns 3.0, which is the arithmetic mean of these five numbers
arithmeticMean(3, 8.25, 18.75)
// returns 10.0, which is the arithmetic mean of these three numbers
```

一个函数可以有多个可变参数。在可变参数后面出现的第一个参数必须有一个参数标签。参数标签使得传递给可变参数的参数和传递给可变参数后面的参数之间的区分变得明确无误。

### [输入-输出参数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#In-Out-Parameters)

函数参数默认为常量。在该函数主体内尝试更改函数参数的值会导致编译时错误。这意味着您无法错误地更改参数的值。如果您希望函数修改参数的值，并希望这些更改在函数调用结束后持续存在，请将该参数定义为输入-输出参数。

您通过在参数类型之前放置 `inout` 关键字来编写一个输入输出参数。输入输出参数具有一个传入函数的值，该值被函数修改，并在函数外部传回以替换原始值。有关输入输出参数的行为及相关编译器优化的详细讨论，请参见输入输出参数。

您只能将变量作为输入输出参数的参数。您不能将常量或字面值作为参数，因为常量和字面值无法被修改。当您将变量作为输入输出参数的参数传递时，您需要在变量名称前直接放置一个与号 ( `&` )，以指示它可以被函数修改。

> 注意
>
> 输入输出参数不能有默认值，变长参数不能标记为 `inout` 。

这是一个名为 `swapTwoInts(_:_:)` 的函数示例，它有两个输入输出整数参数，分别叫做 `a` 和 `b` ：

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

`swapTwoInts(_:_:)` 函数简单地将 `b` 的值交换到 `a` ，将 `a` 的值交换到 `b` 。该函数通过将 `a` 的值存储在一个名为 `temporaryA` 的临时常量中，先将 `b` 的值赋给 `a` ，然后将 `temporaryA` 赋给 `b` 来执行这个交换。

您可以使用两个类型为 `Int` 的变量调用 `swapTwoInts(_:_:)` 函数以交换它们的值。注意，当 `someInt` 和 `anotherInt` 的名称传递给 `swapTwoInts(_:_:)` 函数时，它们会带上一个&符号前缀：

```swift
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// Prints "someInt is now 107, and anotherInt is now 3"
```

上面的例子表明， `someInt` 和 `anotherInt` 的原始值被 `swapTwoInts(_:_:)` 函数修改，即使它们最初是在函数外部定义的。

> 注意
>
> 输入参数与函数返回值不同。上述 `swapTwoInts` 示例没有定义返回类型或返回值，但仍然修改了 `someInt` 和 `anotherInt` 的值。输入参数是函数在其函数体外产生影响的另一种方式。

## [函数类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Function-Types)

每个函数都有一个特定的函数类型，由参数类型和函数的返回类型组成。

例如：

```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
    return a * b
}
```

此示例定义了两个简单的数学函数，分别称为 `addTwoInts` 和 `multiplyTwoInts` 。这些函数各自接受两个 `Int` 值，并返回一个 `Int` 值，这是执行适当数学运算的结果。

这两个函数的类型是 `(Int, Int) -> Int` 。可以理解为：

“一个具有两个参数的函数，两个参数的类型都是 `Int` ，并且返回一个类型为 `Int` 的值。”

这是另一个例子，适用于没有参数或返回值的函数：

```swift
func printHelloWorld() {
    print("hello, world")
}
```

该函数的类型是 `() -> Void` ，或“一个没有参数并返回 `Void` 的函数。”

### [使用函数类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Using-Function-Types)

您可以像使用 Swift 中的其他类型一样使用函数类型。例如，您可以定义一个常量或变量为函数类型，并将适当的函数分配给该变量：

```swift
var mathFunction: (Int, Int) -> Int = addTwoInts
```

这可以被理解为：

"“定义一个变量叫做 `mathFunction` ，它的类型是‘一个接受两个 `Int` 值并返回一个 `Int` 值的函数。’将这个新变量设置为引用名为 `addTwoInts` 的函数。”"

`addTwoInts(_:_:)` 函数与 `mathFunction` 变量具有相同的类型，因此这个赋值被 Swift 的类型检查器允许。

现在您可以使用名称 `mathFunction` 调用分配的函数：

```swift
print("Result: \(mathFunction(2, 3))")
// Prints "Result: 5"
```

可以以与非函数类型相同的方式，将具有相同匹配类型的不同函数分配给同一个变量：

```swift
mathFunction = multiplyTwoInts
print("Result: \(mathFunction(2, 3))")
// Prints "Result: 6"
```

与任何其他类型一样，当将函数赋值给常量或变量时，您可以让 Swift 推断函数类型：

```swift
let anotherMathFunction = addTwoInts
// anotherMathFunction is inferred to be of type (Int, Int) -> Int
```

### [函数类型作为参数类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Function-Types-as-Parameter-Types)

您可以使用函数类型，例如 `(Int, Int) -> Int` 作为另一个函数的参数类型。这使您能够在调用函数时将函数实现的某些方面留给函数的调用者提供。

下面是打印上述数学函数结果的示例：

```swift
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
// Prints "Result: 8"
```

此示例定义了一个名为 `printMathResult(_:_:_:)` 的函数，该函数具有三个参数。第一个参数名为 `mathFunction` ，类型为 `(Int, Int) -> Int` 。您可以将任何该类型的函数作为第一个参数的参数传递。第二个和第三个参数分别名为 `a` 和 `b` ，它们的类型均为 `Int` 。这些用于提供的数学函数的两个输入值。

当调用 `printMathResult(_:_:_:)` 时，会传递 `addTwoInts(_:_:)` 函数，以及整数值 `3` 和 `5` 。它使用值 `3` 和 `5` 调用提供的函数，并打印 `8` 的结果。

`printMathResult(_:_:_:)` 的角色是打印对适当类型的数学函数调用的结果。该函数的实现实际上做了什么并不重要——重要的是该函数是正确类型的。这使得 `printMathResult(_:_:_:)` 能够以类型安全的方式将其某些功能移交给函数的调用者。

### [函数类型作为返回类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Function-Types-as-Return-Types)

您可以使用函数类型作为另一个函数的返回类型。您可以通过在返回函数的返回箭头 ( `->` ) 之后立即写入完整的函数类型来实现。

下一个示例定义了两个简单的函数，称为 `stepForward(_:)` 和 `stepBackward(_:)` 。 `stepForward(_:)` 函数返回一个比其输入值大一的值，而 `stepBackward(_:)` 函数返回一个比其输入值小一的值。这两个函数的类型为 `(Int) -> Int` :

```swift
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}
```

这是一个名为 `chooseStepFunction(backward:)` 的函数，其返回类型为 `(Int) -> Int` 。 `chooseStepFunction(backward:)` 函数根据一个名为 `backward` 的布尔参数返回 `stepForward(_:)` 函数或 `stepBackward(_:)` 函数：

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    return backward ? stepBackward : stepForward
}
```

您现在可以使用 `chooseStepFunction(backward:)` 获取一个在一个方向或另一个方向上执行的函数：

```swift
var currentValue = 3
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the stepBackward() function
```

上述示例确定是否需要正步或负步，以使名为 `currentValue` 的变量逐渐接近零。 `currentValue` 的初始值为 `3` ，这意味着 `currentValue > 0` 返回 `true` ，导致 `chooseStepFunction(backward:)` 返回 `stepBackward(_:)` 函数。返回的函数的引用存储在名为 `moveNearerToZero` 的常量中。

现在 `moveNearerToZero` 已经指向正确的函数，可以用来计数到零：

```swift
print("Counting to zero:")
// Counting to zero:
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// 3...
// 2...
// 1...
// zero!
```

## [嵌套函数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions#Nested-Functions)

本章中您遇到的所有函数都是全局函数的例子，它们在全局范围内定义。您还可以在其他函数的主体内定义函数，这被称为嵌套函数。

嵌套函数默认情况下对外部世界是隐藏的，但仍然可以被其封闭函数调用和使用。封闭函数还可以返回其嵌套函数之一，以允许在另一个范围中使用嵌套函数。

您可以将上述 `chooseStepFunction(backward:)` 示例重写为使用并返回嵌套函数：

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
var currentValue = -4
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the nested stepForward() function
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// -4...
// -3...
// -2...
// -1...
// zero!
```
