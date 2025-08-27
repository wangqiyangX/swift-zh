# 闭包

> 一组一起执行的代码，而不创建命名函数。

闭包是可以在代码中传递和使用的自包含功能块。Swift 中的闭包类似于其他编程语言中的闭包、匿名函数、lambda 和块。

闭包可以捕获并存储其定义上下文中的任何常量和变量的引用。这被称为闭合这些常量和变量。Swift 为您处理捕获的所有内存管理。

> 注意
>
> 如果您不熟悉捕获的概念，请不要担心。它在下面的“捕获值”中详细解释。

全局函数和嵌套函数，如在函数中介绍的，实际上是闭包的特殊情况。闭包有三种形式：

- 全局函数是具有名称且不捕获任何值的闭包。
- 嵌套函数是具有名称并且可以从其封闭函数中捕获值的闭包。
- 闭包表达式是以轻量级语法编写的无名闭包，可以捕获其周围上下文中的值。

Swift 的闭包表达式具有简洁明了的风格，优化鼓励在常见场景中使用简短、无杂乱的语法。这些优化包括：

- 从上下文推断参数和返回值类型
- 单表达式闭包的隐式返回
- 简称参数名称
- 尾随闭包语法

## [闭包表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Closure-Expressions)

嵌套函数，如在嵌套函数中介绍的，是命名和定义自包含代码块作为更大函数的一部分的便捷方法。然而，有时写出不需要完整声明和名称的函数式构造的简短版本是很有用的。特别是在您使用接收函数作为其一个或多个参数的函数或方法时，这一点尤为真实。

闭包表达式是一种以简洁、专注的语法编写内联闭包的方法。闭包表达式提供了几种语法优化，以缩短形式编写闭包，而不失去清晰度或意图。下面的闭包表达式示例通过对 `sorted(by:)` 方法的单个示例进行多次迭代，展示了这些优化，每次迭代都以更简洁的方式表达相同的功能。

### [排序方法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#The-Sorted-Method)

Swift 的标准库提供了一种名为 `sorted(by:)` 的方法，该方法根据您提供的排序闭包的输出，对已知类型的值数组进行排序。一旦完成排序过程， `sorted(by:)` 方法将返回一个与旧数组相同类型和大小的新数组，其元素按正确的排序顺序排列。原始数组不会被 `sorted(by:)` 方法修改。

下面的闭包表达式示例使用 `sorted(by:)` 方法按逆字母顺序对 `String` 值数组进行排序。以下是要排序的初始数组：

```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
```

该 `sorted(by:)` 方法接受一个闭包，该闭包接受两个与数组内容相同类型的参数，并返回一个 `Bool` 值，以指示在排序后第一个值应该出现在第二个值之前还是之后。排序闭包需要返回 `true` ，如果第一个值应该出现在第二个值之前，反之则返回 `false` 。

此示例正在对一个 `String` 值的数组进行排序，因此排序闭包需要是类型为 `(String, String) -> Bool` 的函数。

提供排序闭包的一种方法是编写一个正确类型的普通函数，并将其作为参数传递给 `sorted(by:)` 方法：

```swift
func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reversedNames = names.sorted(by: backward)
// reversedNames is equal to ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
```

如果第一个字符串 ( `s1` ) 大于第二个字符串 ( `s2` )，则 `backward(_:_:)` 函数将返回 `true` ，表示 `s1` 应该在排序数组中出现在 `s2` 之前。对于字符串中的字符，“大于”意味着“在字母表中出现得比……晚”。这意味着字母 `"B"` “大于”字母 `"A"` ，字符串 `"Tom"` 大于字符串 `"Tim"` 。这给出了反向字母排序， `"Barry"` 被放置在 `"Alex"` 之前，依此类推。

然而，这是一种相当冗长的方式来编写本质上是单表达式函数（ `a > b` ）。在这个例子中，最好将排序闭包以内联的方式编写，使用闭包表达式语法。

### [闭包表达式语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Closure-Expression-Syntax)

闭包表达式语法具有以下一般形式：

```swift
{ (<#parameters#>) -> <#return type#> in
   <#statements#>
}
```

闭包表达式语法中的参数可以是输入输出参数，但不能具有默认值。如果给变长参数命名，可以使用变长参数。元组也可以用作参数类型和返回类型。

下面的示例显示了上述 `backward(_:_:)` 函数的闭包表达式版本：

```swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})
```

请注意，此内联闭包的参数和返回类型的声明与 `backward(_:_:)` 函数的声明是相同的。在这两种情况下，它都写作 `(s1: String, s2: String) -> Bool` 。然而，对于内联闭包表达式，参数和返回类型是在大括号内写的，而不是在外面。

闭包主体的开始由 `in` 关键字引入。此关键字表示闭包的参数和返回类型的定义已经结束，闭包的主体即将开始。

由于闭包的主体非常简短，它甚至可以写在一行上：

```swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } )
```

这说明对 `sorted(by:)` 方法的整体调用保持不变。一个括号仍然包裹着方法的整个参数。然而，该参数现在是一个内联闭包。

### [从上下文推断类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Inferring-Type-From-Context)

由于排序闭包作为参数传递给方法，Swift 可以推断其参数的类型和返回值的类型。 `sorted(by:)` 方法在一个字符串数组上被调用，因此它的参数必须是类型为 `(String, String) -> Bool` 的函数。这意味着 `(String, String)` 和 `Bool` 类型不需要作为闭包表达式定义的一部分来书写。由于所有类型都可以推断，因此返回箭头 ( `->` ) 和参数名称周围的括号也可以省略：

```swift
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
```

在将闭包作为内联闭包表达式传递给函数或方法时，总是可以推断参数类型和返回类型。因此，当闭包作为函数或方法参数使用时，您永远不需要将内联闭包写成其完整形式。

尽管如此，如果您愿意，仍然可以明确类型，如果这可以避免代码读者的歧义，进行这样做是受鼓励的。在 `sorted(by:)` 方法的情况下，由于正在进行排序，因此闭包的目的很明确，读者可以安全地假设闭包可能正在处理 `String` 值，因为它正在帮助对字符串数组进行排序。

### [单表达式闭包的隐式返回](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Implicit-Returns-from-Single-Expression-Closures)

单表达式闭包可以通过从其声明中省略 `return` 关键字来隐式返回其单个表达式的结果，如前一个示例的此版本所示：

```swift
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )
```

在这里， `sorted(by:)` 方法参数的函数类型清楚地表明闭包必须返回一个 `Bool` 值。因为闭包的主体包含一个返回 `Bool` 值的单个表达式 ( `s1 > s2` )，所以没有歧义，并且可以省略 `return` 关键字。

### [简写参数名称](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Shorthand-Argument-Names)

Swift 自动为内联闭包提供简写参数名称，可以通过名称 `$0` 、 `$1` 、 `$2` 等来引用闭包参数的值。

如果在闭包表达式中使用这些简写参数名称，则可以从其定义中省略闭包的参数列表。简写参数名称的类型是从预期的函数类型推断出来的，您使用的最高编号的简写参数决定了闭包接受的参数数量。 `in` 关键字也可以省略，因为闭包表达式完全由其主体构成：

```swift
reversedNames = names.sorted(by: { $0 > $1 } )
```

在这里， `$0` 和 `$1` 指的是闭包的第一个和第二个 `String` 参数。因为 `$1` 是具有最高数字的简写参数，所以闭包被理解为接受两个参数。因为这里的 `sorted(by:)` 函数期望一个其参数都是字符串的闭包，所以简写参数 `$0` 和 `$1` 都是 `String` 类型。

### [运算符方法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Operator-Methods)

实际上，还有一种更简短的方式来编写上面的闭包表达式。Swift 的 `String` 类型定义了其字符串特定实现的大于运算符 ( `>` )，作为一个具有两个类型为 `String` 的参数的方法，并返回类型为 `Bool` 的值。这正好匹配 `sorted(by:)` 方法所需的方法类型。因此，您可以简单地传入大于运算符，Swift 将推断您想使用其字符串特定实现：

```swift
reversedNames = names.sorted(by: >)
```

有关运算符方法的更多信息，请参见运算符方法。

## [尾随闭包](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Trailing-Closures)

如果您需要将闭包表达式作为函数的最后一个参数传递给函数，并且闭包表达式很长，将其写为尾随闭包可能会很有用。您在函数调用的括号之后写尾随闭包，即使尾随闭包仍然是函数的一个参数。当您使用尾随闭包语法时，您不需要将第一个闭包的参数标签作为函数调用的一部分。一个函数调用可以包含多个尾随闭包；然而，下面的前几个示例使用的是单个尾随闭包。

```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // function body goes here
}


// Here's how you call this function without using a trailing closure:


someFunctionThatTakesAClosure(closure: {
    // closure's body goes here
})


// Here's how you call this function with a trailing closure instead:


someFunctionThatTakesAClosure() {
    // trailing closure's body goes here
}
```

可以将上面“闭包表达式语法”部分中的字符串排序闭包写在 `sorted(by:)` 方法的括号外，作为尾随闭包：

```swift
reversedNames = names.sorted() { $0 > $1 }
```

如果将闭包表达式作为函数或方法的唯一参数提供，并且您将该表达式作为尾随闭包提供，则在调用函数时不需要在函数或方法的名称后写一对括号 `()` ：

```swift
reversedNames = names.sorted { $0 > $1 }
```

尾随闭包在闭包足够长，以至于无法在一行内编写时最为有用。例如，Swift 的 `Array` 类型有一个 `map(_:)` 方法，该方法将闭包表达式作为唯一参数。此闭包针对数组中的每个项目调用一次，并为该项目返回一个替换的映射值（可能是其他类型）。您通过在传递给 `map(_:)` 的闭包中编写代码来指定映射的性质和返回值的类型。

在对每个数组元素应用提供的闭包后， `map(_:)` 方法返回一个新数组，其中包含所有新的映射值，并且顺序与原始数组中的相应值相同。

以下是使用 `map(_:)` 方法和尾随闭包将 `Int` 值的数组转换为 `String` 值数组的方法。数组 `[16, 58, 510]` 用于创建新数组 `["OneSix", "FiveEight", "FiveOneZero"]` ：

```swift
let digitNames = [
    0: "Zero", 1: "One", 2: "Two",   3: "Three", 4: "Four",
    5: "Five", 6: "Six", 7: "Seven", 8: "Eight", 9: "Nine"
]
let numbers = [16, 58, 510]
```

上面的代码创建了一个整数数字与其名称的英文版本之间的映射字典。它还定义了一个整数数组，准备转换为字符串。

您现在可以使用 `numbers` 数组通过将闭包表达式作为尾随闭包传递给数组的 `String` 方法来创建 `String` 值的数组：

```swift
let strings = numbers.map { (number) -> String in
    var number = number
    var output = ""
    repeat {
        output = digitNames[number % 10]! + output
        number /= 10
    } while number > 0
    return output
}
// strings is inferred to be of type [String]
// its value is ["OneSix", "FiveEight", "FiveOneZero"]
```

`map(_:)` 方法对数组中的每个项目调用闭包表达式一次。您不需要指定闭包输入参数的类型 `number` ，因为可以从要映射的数组中的值推断出该类型。

在这个例子中，变量 `number` 被初始化为闭包的 `number` 参数的值，以便在闭包体内可以修改该值。（函数和闭包的参数始终是常量。）闭包表达式还指定了返回类型为 `String` ，以指示将存储在映射输出数组中的类型。

每次调用时，闭包表达式都会构建一个名为 `output` 的字符串。它通过使用余数运算符 ( `number % 10` ) 计算 `number` 的最后一位数字，并使用该数字在 `digitNames` 字典中查找适当的字符串。闭包可以用于创建任何大于零的整数的字符串表示。

> 注意
>
> 对 `digitNames` 字典的下标调用后面带有一个感叹号（ `!` ），因为字典下标返回一个可选值，以表示当键不存在时，字典查找可能会失败。在上面的示例中，保证 `number % 10` 将始终是 `digitNames` 字典的有效下标键，因此使用感叹号来强制解包存储在下标的可选返回值中的 `String` 值。

从 `digitNames` 字典中检索到的字符串被添加到 `output` 的前面，有效地反向构建数字的字符串版本。（表达式 `number % 10` 对于 `16` 给出值 `6` ，对于 `58` 给出值 `8` ，对于 `510` 给出值 `0` 。）

`number` 变量然后被 `10` 除。因为它是一个整数，所以在除法过程中向下取整，因此 `16` 变为 `1` ， `58` 变为 `5` ， `510` 变为 `51` 。

该过程重复进行，直到 `number` 等于 `0` ，此时 `output` 字符串由闭包返回，并通过 `map(_:)` 方法添加到输出数组中。

上面示例中尾随闭包语法的使用，整洁地封装了闭包的功能，紧接在闭包支持的函数之后，而无需将整个闭包包装在 `map(_:)` 方法的外部括号中。

如果一个函数接受多个闭包，您省略第一个尾随闭包的参数标签，并为剩余的尾随闭包添加标签。例如，下面的函数为相册加载一张图片：

```swift
func loadPicture(from server: Server, completion: (Picture) -> Void, onFailure: () -> Void) {
    if let picture = download("photo.jpg", from: server) {
        completion(picture)
    } else {
        onFailure()
    }
}
```

当您调用此函数加载一张图片时，您提供了两个闭包。第一个闭包是一个完成处理程序，在下载成功后显示图片。第二个闭包是一个错误处理程序，向用户显示错误。

```swift
loadPicture(from: someServer) { picture in
    someView.currentPicture = picture
} onFailure: {
    print("Couldn't download the next picture.")
}
```

在这个例子中， `loadPicture(from:completion:onFailure:)` 函数将其网络任务分配到后台，并在网络任务完成时调用两个完成处理程序之一。这样编写函数可以让您干净地将负责处理网络故障的代码与在成功下载后更新用户界面的代码分开，而不是使用一个处理两种情况的闭包。

> 注意
>
> 完成处理程序可能变得难以阅读，特别是当您需要嵌套多个处理程序时。另一种方法是使用异步代码，如并发中所述。

## [捕获值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Capturing-Values)

闭包可以捕获其定义的周围上下文中的常量和变量。闭包可以在其主体内引用和修改这些常量和变量的值，即使定义这些常量和变量的原始作用域不再存在。

在 Swift 中，能够捕获值的闭包的最简单形式是嵌套函数，写在另一个函数的主体内。嵌套函数可以捕获其外部函数的任何参数，也可以捕获在外部函数中定义的任何常量和变量。

这是一个名为 `makeIncrementer` 的函数的示例，它包含一个名为 `incrementer` 的嵌套函数。嵌套 `incrementer()` 函数从其周围上下文中捕获两个值， `runningTotal` 和 `amount` 。在捕获这些值之后， `incrementer` 由 `makeIncrementer` 作为一个闭包返回，每次调用时将 `runningTotal` 增加 `amount` 。

```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementer
}
```

`makeIncrementer` 的返回类型是 `() -> Int` 。这意味着它返回的是一个函数，而不是一个简单的值。它返回的函数没有参数，并且每次被调用时都会返回一个 `Int` 值。要了解函数如何返回其他函数，请参阅《函数类型作为返回类型》。

该 `makeIncrementer(forIncrement:)` 函数定义了一个名为 `runningTotal` 的整数变量，用于存储返回的增量计的当前运行总数。该变量初始化为 `0` 的值。

该 `makeIncrementer(forIncrement:)` 函数具有一个名为 `Int` 的单一参数，其参数标签为 `forIncrement` ，参数名称为 `amount` 。传递给该参数的参数值指定每次调用返回的增量计函数时，应该增加多少 `runningTotal` 。 `makeIncrementer` 函数定义了一个名为 `incrementer` 的嵌套函数，该函数执行实际的增量操作。该函数简单地将 `amount` 加到 `runningTotal` ，并返回结果。

当单独考虑时，嵌套的 `incrementer()` 函数可能看起来不寻常：

```swift
func incrementer() -> Int {
    runningTotal += amount
    return runningTotal
}
```

该 `incrementer()` 函数没有任何参数，但它在其函数体内引用了 `runningTotal` 和 `amount` 。它通过捕获对周围函数的 `runningTotal` 和 `amount` 的引用来实现这一点，并在其自身的函数体内使用它们。通过引用捕获确保 `runningTotal` 和 `amount` 在对 `makeIncrementer` 的调用结束时不会消失，同时确保下次调用 `incrementer` 函数时 `runningTotal` 可用。

> 注意
>
> 作为一种优化，如果一个值没有被闭包修改，并且在闭包创建后该值没有被修改，Swift 可以选择捕获并存储该值的副本。

Swift 还处理所有内存管理，涉及在变量不再需要时处置它们。

以下是 `makeIncrementer` 实际操作的示例：

```swift
let incrementByTen = makeIncrementer(forIncrement: 10)
```

这个示例设置了一个名为 `incrementByTen` 的常量，用于引用一个增量函数，该函数每次被调用时将 `10` 加到它的 `runningTotal` 变量上。多次调用该函数展示了这个行为的实际操作：

```swift
incrementByTen()
// returns a value of 10
incrementByTen()
// returns a value of 20
incrementByTen()
// returns a value of 30
```

如果您创建第二个增量器，它将拥有自己的存储引用一个新的、独立的 `runningTotal` 变量：

```swift
let incrementBySeven = makeIncrementer(forIncrement: 7)
incrementBySeven()
// returns a value of 7
```

再次调用原始增量器 ( `incrementByTen` ) 会继续增加它自己的 `runningTotal` 变量，并且不会影响由 `incrementBySeven` 捕获的变量：

```swift
incrementByTen()
// returns a value of 40
```

> 注意
>
> 如果您将一个闭包分配给类实例的属性，并且闭包通过引用该实例或其成员来捕获该实例，则会在闭包和该实例之间创建强引用循环。Swift 使用捕获列表来打破这些强引用循环。有关更多信息，请参见闭包的强引用循环。

## [闭包是引用类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Closures-Are-Reference-Types)

在上面的例子中， `incrementBySeven` 和 `incrementByTen` 是常量，但这些常量所引用的闭包仍然能够递增它们捕获的 `runningTotal` 变量。这是因为函数和闭包是引用类型。

每当您将一个函数或闭包赋值给一个常量或变量时，您实际上是在将该常量或变量设置为对该函数或闭包的引用。在上面的例子中， `incrementByTen` 所引用的闭包的选择是常量，而不是闭包本身的内容。

这也意味着，如果您将一个闭包赋值给两个不同的常量或变量，那么这两个常量或变量都引用同一个闭包。

```swift
let alsoIncrementByTen = incrementByTen
alsoIncrementByTen()
// returns a value of 50


incrementByTen()
// returns a value of 60
```

上面的例子表明，调用 `alsoIncrementByTen` 与调用 `incrementByTen` 是一样的。因为它们都引用了相同的闭包，所以它们都增加并返回相同的运行总计。

## [转义闭包](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Escaping-Closures)

当闭包作为参数传递给函数，但在函数返回后被调用时，称该闭包逃逸了函数。当您声明一个函数，该函数以闭包作为其参数之一时，可以在参数类型前写 `@escaping` 来指示该闭包被允许逃逸。

闭包逃逸的一种方式是被存储在函数外部定义的变量中。例如，许多启动异步操作的函数将闭包作为完成处理程序的参数。函数在启动操作后返回，但闭包在操作完成之前不会被调用——闭包需要逃逸，以便稍后被调用。例如：

```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
    completionHandlers.append(completionHandler)
}
```

`someFunctionWithEscapingClosure(_:)` 函数将一个闭包作为参数，并将其添加到函数外声明的数组中。如果您没有用 `@escaping` 标记这个函数的参数，您将会得到一个编译时错误。

一个引用到 `self` 的逃逸闭包需要特别考虑，如果 `self` 引用的是一个类的实例。在逃逸闭包中捕获 `self` 会很容易意外地创建一个强引用循环。有关引用循环的信息，请参见自动引用计数。

通常，闭包通过在闭包的主体中使用变量来隐式捕获变量，但在这种情况下，您需要显式地进行捕获。如果您想捕获 `self` ，在使用时显式地写 `self` ，或者将 `self` 包含在闭包的捕获列表中。显式地写 `self` 让您表达您的意图，并提醒您确认没有引用循环。例如，在下面的代码中，传递给 `someFunctionWithEscapingClosure(_:)` 的闭包显式地引用了 `self` 。相比之下，传递给 `someFunctionWithNonescapingClosure(_:)` 的闭包是一个非逃逸闭包，这意味着它可以隐式地引用 `self` 。

```swift
func someFunctionWithNonescapingClosure(closure: () -> Void) {
    closure()
}


class SomeClass {
    var x = 10
    func doSomething() {
        someFunctionWithEscapingClosure { self.x = 100 }
        someFunctionWithNonescapingClosure { x = 200 }
    }
}


let instance = SomeClass()
instance.doSomething()
print(instance.x)
// Prints "200"


completionHandlers.first?()
print(instance.x)
// Prints "100"
```

这是一个版本的 `doSomething()` ，通过将 `self` 包含在闭包的捕获列表中来捕获它，然后隐式地引用 `self` ：

```swift
class SomeOtherClass {
    var x = 10
    func doSomething() {
        someFunctionWithEscapingClosure { [self] in x = 100 }
        someFunctionWithNonescapingClosure { x = 200 }
    }
}
```

如果 `self` 是一个结构体或枚举的实例，您总是可以隐式地引用 `self` 。然而，当 `self` 是一个结构体或枚举的实例时，逃逸闭包不能捕获 `self` 的可变引用。正如在《结构体和枚举是值类型》中讨论的那样，结构体和枚举不允许共享可变性。

```swift
struct SomeStruct {
    var x = 10
    mutating func doSomething() {
        someFunctionWithNonescapingClosure { x = 200 }  // Ok
        someFunctionWithEscapingClosure { x = 100 }     // Error
    }
}
```

上面的示例中对 `someFunctionWithEscapingClosure` 函数的调用是一个错误，因为它在一个可变方法内部，因此 `self` 是可变的。这违反了无法对结构体的 `self` 捕获可变引用的逃逸闭包的规则。

## [自动闭包](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures#Autoclosures)

自动闭包是一个自动创建的闭包，用于包装作为参数传递给函数的表达式。它不接受任何参数，当被调用时，返回包装在其中的表达式的值。这种语法便利使您可以通过编写正常表达式而不是显式闭包来省略函数参数周围的花括号。

调用接受自动闭包的函数是很常见的，但实现这种函数并不常见。例如， `assert(condition:message:file:line:)` 函数对其 `condition` 和 `message` 参数接受一个自动闭包；其 `condition` 参数仅在调试构建中评估，而其 `message` 参数仅在 `condition` 为 `false` 时评估。

自动闭包让您延迟评估，因为内部代码在您调用闭包之前不会运行。延迟评估对于具有副作用或计算成本高的代码非常有用，因为它让您控制何时评估该代码。下面的代码展示了闭包如何延迟评估。

```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)
// Prints "5"


let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)
// Prints "5"


print("Now serving \(customerProvider())!")
// Prints "Now serving Chris!"
print(customersInLine.count)
// Prints "4"
```

尽管 `customersInLine` 数组的第一个元素被闭包内的代码移除，但该数组元素在闭包实际被调用之前并不会被移除。如果闭包从未被调用，闭包内的表达式也不会被评估，这意味着数组元素也永远不会被移除。请注意， `customerProvider` 的类型不是 `String` 而是 `() -> String` ——一个没有参数并返回字符串的函数。

当您将一个闭包作为参数传递给一个函数时，您会获得相同的延迟求值行为。

```swift
// customersInLine is ["Alex", "Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: { customersInLine.remove(at: 0) } )
// Prints "Now serving Alex!"
```

上面列表中的 `serve(customer:)` 函数接受一个显式闭包，该闭包返回客户的名称。下面的 `serve(customer:)` 版本执行相同的操作，但它不是接受显式闭包，而是通过用 `@autoclosure` 属性标记其参数类型来接受一个自动闭包。现在，您可以像调用一个 `String` 参数而不是闭包一样调用该函数。该参数会自动转换为闭包，因为 `customerProvider` 参数的类型被标记为 `@autoclosure` 属性。

```swift
// customersInLine is ["Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: @autoclosure () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: customersInLine.remove(at: 0))
// Prints "Now serving Ewa!"
```

> 注意
>
> 过度使用自动闭包可能会使您的代码难以理解。上下文和函数名称应该清楚地表明评估正在被延迟。

如果您想要一个允许逃逸的自动闭包，请同时使用 `@autoclosure` 和 `@escaping` 属性。 `@escaping` 属性在“逃逸闭包”中已描述。

```swift
// customersInLine is ["Barry", "Daniella"]
var customerProviders: [() -> String] = []
func collectCustomerProviders(_ customerProvider: @autoclosure @escaping () -> String) {
    customerProviders.append(customerProvider)
}
collectCustomerProviders(customersInLine.remove(at: 0))
collectCustomerProviders(customersInLine.remove(at: 0))


print("Collected \(customerProviders.count) closures.")
// Prints "Collected 2 closures."
for customerProvider in customerProviders {
    print("Now serving \(customerProvider())!")
}
// Prints "Now serving Barry!"
// Prints "Now serving Daniella!"
```

在上面的代码中， `collectCustomerProviders(_:)` 函数并没有将传递给它的闭包作为 `customerProvider` 参数调用，而是将闭包附加到 `customerProviders` 数组中。该数组在函数的作用域外声明，这意味着数组中的闭包可以在函数返回后执行。因此， `customerProvider` 参数的值必须允许逃逸函数的作用域。
