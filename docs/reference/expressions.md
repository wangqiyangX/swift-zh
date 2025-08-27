# 表达式

> 访问、修改和赋值。

在 Swift 中，有四种表达式：前缀表达式、中缀表达式、基本表达式和后缀表达式。评估一个表达式返回一个值、产生副作用或两者兼有。

前缀和中缀表达式允许您将运算符应用于较小的表达式。基本表达式在概念上是最简单的表达式，它们提供了一种访问值的方式。后缀表达式，像前缀和中缀表达式一样，让您通过后缀（如函数调用和成员访问）构建更复杂的表达式。每种类型的表达式在下面的部分中都有详细描述。

表达式的语法

_expression_ → _try-operator?_ _await-operator?_ _prefix-expression_ _infix-expressions?_ \\

## [前缀表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Prefix-Expressions)

前缀表达式将可选的前缀运算符与一个表达式结合。前缀运算符接受一个参数，即随后的表达式。

有关这些运算符行为的信息，请参见基本运算符和高级运算符。

有关 Swift 标准库提供的运算符的信息，请参见运算符声明。

前缀表达式的语法

_prefix-expression_ → _prefix-operator?_ _postfix-expression_\
&#xNAN;_&#x70;refix-expression_ → _in-out-expression_

### [输入输出表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#In-Out-Expression)

一个输入输出表达式标记一个作为输入输出参数传递给函数调用表达式的变量。

```swift
&<#expression#>
```

有关输入输出参数的更多信息及示例，请参见输入输出参数。

在提供一个需要指针的上下文中使用非指针参数时，也会使用输入输出表达式，如隐式转换为指针类型中所述。

进出表达式的语法

_in-out-expression_ → **`&`** _primary-expression_

### [尝试运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Try-Operator)

一个尝试表达式由 `try` 运算符和一个可能抛出错误的表达式组成。它具有以下形式：

```swift
try <#expression#>
```

`try` 表达式的值是该表达式的值。

一个可选尝试表达式由 `try?` 运算符和一个可能抛出错误的表达式组成。它具有以下形式：

```swift
try? <#expression#>
```

如果表达式没有抛出错误，optional-try 表达式的值是一个包含表达式值的可选项。否则，optional-try 表达式的值是 `nil` 。

强制尝试表达式由 `try!` 操作符和一个可能抛出错误的表达式组成。它具有以下形式：

```swift
try! <#expression#>
```

强制尝试表达式的值是表达式的值。如果表达式抛出错误，则会产生运行时错误。

当一个中缀操作符左侧的表达式标记为 `try` 、 `try?` 或 `try!` 时，该操作符适用于整个中缀表达式。也就是说，您可以使用括号明确操作符应用的范围。

```swift
// try applies to both function calls
sum = try someThrowingFunction() + anotherThrowingFunction()


// try applies to both function calls
sum = try (someThrowingFunction() + anotherThrowingFunction())


// Error: try applies only to the first function call
sum = (try someThrowingFunction()) + anotherThrowingFunction()
```

A `try` 表达式不能出现在中缀运算符的右侧，除非中缀运算符是赋值运算符或 `try` 表达式被括号包围。

如果一个表达式同时包含 `try` 和 `await` 运算符，则 `try` 运算符必须首先出现。

有关更多信息以及如何使用 `try` 、 `try?` 和 `try!` 的示例，请参阅错误处理。

try 表达式的语法

_try-operator_ → **`try`** | **`try`** **`?`** | **`try`** **`!`**

### [等待运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Await-Operator)

一个 await 表达式由 `await` 运算符和一个使用异步操作结果的表达式组成。它具有以下形式：

```swift
await <#expression#>
```

`await` 表达式的值是该表达式的值。

被 `await` 标记的表达式称为潜在的挂起点。异步函数的执行可以在每个被 `await` 标记的表达式处挂起。此外，执行并发代码在任何其他点都不会挂起。这意味着潜在挂起点之间的代码可以安全地更新需要暂时打破不变性的状态，前提是它在下一个潜在挂起点之前完成更新。

`await` 表达式只能出现在异步上下文中，例如传递给 `async(priority:operation:)` 函数的尾随闭包。它不能出现在 `defer` 语句的主体中，或者在同步函数类型的自动闭包中。

当中缀运算符左侧的表达式标记为 `await` 运算符时，该运算符应用于整个中缀表达式。也就是说，您可以使用括号来明确运算符应用的范围。

```swift
// await applies to both function calls
sum = await someAsyncFunction() + anotherAsyncFunction()


// await applies to both function calls
sum = await (someAsyncFunction() + anotherAsyncFunction())


// Error: await applies only to the first function call
sum = (await someAsyncFunction()) + anotherAsyncFunction()
```

除非中缀运算符是赋值运算符或 `await` 表达式被括号括起来，否则 `await` 表达式不能出现在中缀运算符的右侧。

如果一个表达式同时包含 `await` 和 `try` 运算符，则 `try` 运算符必须首先出现。

await 表达式的语法

_await-operator_ → **`await`**

## [中缀表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Infix-Expressions)

中缀表达式将一个中缀二元运算符与它作为左侧和右侧参数的表达式结合在一起。它具有以下形式：

```swift
<#left-hand argument#> <#operator#> <#right-hand argument#>
```

有关这些运算符行为的信息，请参见基本运算符和高级运算符。

有关 Swift 标准库提供的运算符的信息，请参见运算符声明。

> 注意
>
> 在解析时，由中缀运算符组成的表达式被表示为一个扁平列表。通过应用运算符优先级，这个列表被转换为一棵树。例如，表达式 `2 + 3 * 5` 最初被理解为一个包含五个项的扁平列表， `2` 、 `+` 、 `3` 、 `*` 和 `5` 。这个过程将其转换为树 (2 + (3 \* 5))。

中缀表达式的语法

_infix-expression_ → _infix-operator_ _prefix-expression_\
&#xNAN;_&#x69;nfix-expression_ → _assignment-operator_ _try-operator?_ _await-operator?_ _prefix-expression_\
&#xNAN;_&#x69;nfix-expression_ → _conditional-operator_ _try-operator?_ _await-operator?_ _prefix-expression_\
&#xNAN;_&#x69;nfix-expression_ → _type-casting-operator_\
&#xNAN;_&#x69;nfix-expressions_ → _infix-expression_ _infix-expressions?_

### [赋值操作符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Assignment-Operator)

赋值运算符为给定表达式设置一个新值。它具有以下形式：

```swift
<#expression#> = <#value#>
```

表达式的值被设置为通过计算值获得的值。如果表达式是一个元组，则值必须是具有相同元素数量的元组。（允许嵌套元组。）赋值是从值的每个部分到表达式的相应部分进行的。例如：

```swift
(a, _, (b, c)) = ("test", 9.45, (12, 3))
// a is "test", b is 12, c is 3, and 9.45 is ignored
```

赋值运算符不返回任何值。

赋值运算符的语法

_assignment-operator_ → **`=`**

### [三元条件运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Ternary-Conditional-Operator)

三元条件运算符根据条件的值评估给定的两个值中的一个。它具有以下形式：

```swift
<#condition#> ? <#expression used if true#> : <#expression used if false#>
```

如果条件评估为 `true` , 则条件运算符评估第一个表达式并返回其值。否则，它评估第二个表达式并返回其值。未使用的表达式不会被评估。

有关使用三元条件运算符的示例，请参阅三元条件运算符。

条件运算符的语法

_conditional-operator_ → **`?`** _expression_ **`:`**

### [类型转换运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Type-Casting-Operators)

有四种类型转换运算符： `is` 运算符、 `as` 运算符、 `as?` 运算符和 `as!` 运算符。

它们具有以下形式：

```swift
<#expression#> is <#type#>
<#expression#> as <#type#>
<#expression#> as? <#type#>
<#expression#> as! <#type#>
```

`is` 运算符在运行时检查表达式是否可以转换为指定类型。如果表达式可以转换为指定类型，它返回 `true` ；否则，它返回 `false` 。

`as` 运算符在编译时已知转换总是成功时进行转换，例如向上转型或桥接。向上转型让您可以将表达式作为其类型超类型的实例使用，而不使用中间变量。以下方法是等效的：

```swift
func f(_ any: Any) { print("Function for Any") }
func f(_ int: Int) { print("Function for Int") }
let x = 10
f(x)
// Prints "Function for Int"


let y: Any = x
f(y)
// Prints "Function for Any"


f(x as Any)
// Prints "Function for Any"
```

桥接允许您使用 Swift 标准库类型的表达式，例如 `String` ，作为其对应的 Foundation 类型，例如 `NSString` ，而无需创建新实例。有关桥接的更多信息，请参见使用 Foundation 类型。

`as?` 运算符将表达式条件性地转换为指定类型。 `as?` 运算符返回指定类型的可选值。在运行时，如果转换成功，表达式的值将被包装在一个可选值中并返回；否则，返回的值是 `nil` 。如果转换为指定类型保证失败或保证成功，将会引发编译时错误。

`as!` 运算符强制将表达式转换为指定类型。 `as!` 运算符返回指定类型的值，而不是可选类型。如果转换失败，将引发运行时错误。 `x as! T` 的行为与 `(x as? T)!` 的行为相同。

有关类型转换的更多信息以及使用类型转换运算符的示例，请参见类型转换。

类型转换运算符的语法

_type-casting-operator_ → **`is`** _type_\
&#xNAN;_&#x74;ype-casting-operator_ → **`as`** _type_\
&#xNAN;_&#x74;ype-casting-operator_ → **`as`** **`?`** _type_\
&#xNAN;_&#x74;ype-casting-operator_ → **`as`** **`!`** _type_

## [主要表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Primary-Expressions)

基本表达式是最基本的表达式类型。它们可以单独作为表达式使用，也可以与其他标记结合形成前缀表达式、中缀表达式和后缀表达式。

初级表达式的语法

_primary-expression_ → _identifier_ _generic-argument-clause?_\
&#xNAN;_&#x70;rimary-expression_ → _literal-expression_\
&#xNAN;_&#x70;rimary-expression_ → _self-expression_\
&#xNAN;_&#x70;rimary-expression_ → _superclass-expression_\
&#xNAN;_&#x70;rimary-expression_ → _conditional-expression_\
&#xNAN;_&#x70;rimary-expression_ → _closure-expression_\
&#xNAN;_&#x70;rimary-expression_ → _parenthesized-expression_\
&#xNAN;_&#x70;rimary-expression_ → _tuple-expression_\
&#xNAN;_&#x70;rimary-expression_ → _implicit-member-expression_\
&#xNAN;_&#x70;rimary-expression_ → _wildcard-expression_\
&#xNAN;_&#x70;rimary-expression_ → _macro-expansion-expression_\
&#xNAN;_&#x70;rimary-expression_ → _key-path-expression_\
&#xNAN;_&#x70;rimary-expression_ → _selector-expression_\
&#xNAN;_&#x70;rimary-expression_ → _key-path-string-expression_

### [字面表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Literal-Expression)

字面表达式由普通字面量（例如字符串或数字）、数组或字典字面量或游乐场字面量组成。

> 注意
>
> 在 Swift 5.9 之前，以下特殊字面量被识别： `#column` 、 `#dsohandle` 、 `#fileID` 、 `#filePath` 、 `#file` 、 `#function` 和 `#line` 。这些现在作为宏在 Swift 标准库中实现： `column()` 、 `dsohandle()` 、 `fileID()` 、 `filePath()` 、 `file()` 、 `function()` 和 `line()` 。

数组字面量是有序值的集合。它具有以下形式：

```swift
[<#value 1#>, <#value 2#>, <#...#>]
```

数组中的最后一个表达式后可以跟一个可选的逗号。数组字面量的值的类型为 `[T]` ，其中 `T` 是其中表达式的类型。如果存在多种类型的表达式， `T` 是它们的最近公共超类型。空数组字面量使用一对空方括号表示，可以用来创建指定类型的空数组。

```swift
var emptyArray: [Double] = []
```

字典字面量是一个无序的键值对集合。它具有以下形式：

```swift
[<#key 1#>: <#value 1#>, <#key 2#>: <#value 2#>, <#...#>]
```

字典中的最后一个表达式可以后面跟一个可选的逗号。字典文字的值类型为 `[Key: Value]` ，其中 `Key` 是其键表达式的类型，而 `Value` 是其值表达式的类型。如果存在多种类型的表达式， `Key` 和 `Value` 是它们各自值的最接近的共同超类型。空字典文字表示为一对方括号中的冒号 ( `[:]` )，以将其与空数组文字区分开。您可以使用空字典文字创建指定键和值类型的空字典文字。

```swift
var emptyDictionary: [String: Double] = [:]
```

A playground literal is used by Xcode to create an interactive representation of a color, file, or image within the program editor. Playground literals in plain text outside of Xcode are represented using a special literal syntax. 叠加字面量用于 Xcode 在程序编辑器中创建颜色、文件或图像的交互式表示。在 Xcode 之外的纯文本中，叠加字面量使用特殊的字面量语法表示。

For information on using playground literals in Xcode, see Add a color, file, or image literal in Xcode Help. 有关在 Xcode 中使用叠加字面量的信息，请参见在 Xcode 帮助中添加颜色、文件或图像字面量。

字面表达式的语法

_literal-expression_ → _literal_\
&#xNAN;_&#x6C;iteral-expression_ → _array-literal_ | _dictionary-literal_ | _playground-literal_

_array-literal_ → **`[`** _array-literal-items?_ **`]`**\
&#xNAN;_&#x61;rray-literal-items_ → _array-literal-item_ **`,`**_?_ | _array-literal-item_ **`,`** _array-literal-items_\
&#xNAN;_&#x61;rray-literal-item_ → _expression_

_dictionary-literal_ → **`[`** _dictionary-literal-items_ **`]`** | **`[`** **`:`** **`]`**\
&#xNAN;_&#x64;ictionary-literal-items_ → _dictionary-literal-item_ **`,`**_?_ | _dictionary-literal-item_ **`,`** _dictionary-literal-items_\
&#xNAN;_&#x64;ictionary-literal-item_ → _expression_ **`:`** _expression_

_playground-literal_ → **`#colorLiteral`** **`(`** **`red`** **`:`** _expression_ **`,`** **`green`** **`:`** _expression_ **`,`** **`blue`** **`:`** _expression_ **`,`** **`alpha`** **`:`** _expression_ **`)`**\
&#xNAN;_&#x70;layground-literal_ → **`#fileLiteral`** **`(`** **`resourceName`** **`:`** _expression_ **`)`**\
&#xNAN;_&#x70;layground-literal_ → **`#imageLiteral`** **`(`** **`resourceName`** **`:`** _expression_ **`)`**

### [自我表达](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Self-Expression)

`self` 表达式是对当前类型或发生它的类型实例的显式引用。它有以下形式：

```swift
self
self.<#member name#>
self[<#subscript index#>]
self(<#initializer arguments#>)
self.init(<#initializer arguments#>)
```

在初始化器、下标或实例方法中， `self` 指的是发生它的类型的当前实例。在类型方法中， `self` 指的是发生它的当前类型。

`self` 表达式用于在访问成员时指定范围，当范围内存在同名变量（如函数参数）时提供消歧义。举例来说：

```swift
class SomeClass {
    var greeting: String
    init(greeting: String) {
        self.greeting = greeting
    }
}
```

在值类型的变异方法中，您可以将该值类型的新实例分配给 `self` 。例如：

```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        self = Point(x: x + deltaX, y: y + deltaY)
    }
}
```

自我表达的语法

_self-expression_ → **`self`** | _self-method-expression_ | _self-subscript-expression_ | _self-initializer-expression_

_self-method-expression_ → **`self`** **`.`** _identifier_\
&#xNAN;_&#x73;elf-subscript-expression_ → **`self`** **`[`** _function-call-argument-list_ **`]`**\
&#xNAN;_&#x73;elf-initializer-expression_ → **`self`** **`.`** **`init`**

### [超类 Expression](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Superclass-Expression)

一个超类表达式允许一个类与其超类进行交互。它具有以下形式之一：

```swift
super.<#member name#>
super[<#subscript index#>]
super.init(<#initializer arguments#>)
```

第一种形式用于访问超类的成员。第二种形式用于访问超类的下标实现。第三种形式用于访问超类的初始化器。

子类可以在其成员、下标和初始化器的实现中使用超类表达式，以利用其超类中的实现。

超类表达式的语法

_superclass-expression_ → _superclass-method-expression_ | _superclass-subscript-expression_ | _superclass-initializer-expression_

_superclass-method-expression_ → **`super`** **`.`** _identifier_\
&#xNAN;_&#x73;uperclass-subscript-expression_ → **`super`** **`[`** _function-call-argument-list_ **`]`**\
&#xNAN;_&#x73;uperclass-initializer-expression_ → **`super`** **`.`** **`init`**

### [条件表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Conditional-Expression)

条件表达式根据条件的值评估到给定的几个值之一。它具有以下形式之一：

```swift
if <#condition 1#> {
   <#expression used if condition 1 is true#>
} else if <#condition 2#> {
   <#expression used if condition 2 is true#>
} else {
   <#expression used if both conditions are false#>
}


switch <#expression#> {
case <#pattern 1#>:
    <#expression 1#>
case <#pattern 2#> where <#condition#>:
    <#expression 2#>
default:
    <#expression 3#>
}
```

条件表达式的行为和语法与 `if` 语句或 `switch` 语句相同，但存在以下段落所描述的差异。

条件表达式仅出现在以下上下文中：

- 作为分配给变量的值。
- 作为变量或常量声明中的初始值。
- 作为 `throw` 表达式抛出的错误。
- 作为函数、闭包或属性获取器返回的值。
- 作为条件表达式分支中的值。

条件表达式的分支是穷尽的，确保该表达式始终产生一个值，无论条件如何。这意味着每个 `if` 分支需要一个对应的 `else` 分支。

每个分支包含一个单独的表达式，当该分支的条件为真时，该表达式用作条件表达式的值，或者一个 `throw` 语句，或一个永不返回的函数调用。

每个分支必须产生相同类型的值。由于每个分支的类型检查是独立的，因此有时您需要明确指定值的类型，例如当分支包含不同种类的字面量，或者当分支的值是 `nil` 时。当您需要提供此信息时，请为结果分配的变量添加类型注释，或者为分支的值添加 `as` 强制转换。

```swift
let number: Double = if someCondition { 10 } else { 12.34 }
let number = if someCondition { 10 as Double } else { 12.34 }
```

在结果构建器内部，条件表达式只能作为变量或常量的初始值出现。这种行为意味着当您在结果构建器中写 `if` 或 `switch` 时——在变量或常量声明之外——该代码被理解为一个分支语句，并且结果构建器的某个方法会转换该代码。

不要在 `try` 表达式中放置条件表达式，即使条件表达式的某个分支抛出异常。

条件表达式的语法

_conditional-expression_ → _if-expression_ | _switch-expression_

_if-expression_ → **`if`** _condition-list_ **`{`** _statement_ **`}`** _if-expression-tail_\
&#xNAN;_&#x69;f-expression-tail_ → **`else`** _if-expression_\
&#xNAN;_&#x69;f-expression-tail_ → **`else`** **`{`** _statement_ **`}`**

_switch-expression_ → **`switch`** _expression_ **`{`** _switch-expression-cases_ **`}`**\
&#xNAN;_&#x73;witch-expression-cases_ → _switch-expression-case_ _switch-expression-cases?_\
&#xNAN;_&#x73;witch-expression-case_ → _case-label_ _statement_\
&#xNAN;_&#x73;witch-expression-case_ → _default-label_ _statement_

### [闭包表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Closure-Expression)

闭包表达式创建一个闭包，也称为在其他编程语言中的 lambda 或匿名函数。与函数声明一样，闭包包含语句，并捕获来自其封闭范围的常量和变量。它具有以下形式：

```swift
{ (<#parameters#>) -> <#return type#> in
   <#statements#>
}
```

参数的形式与函数声明中的参数相同，如函数声明中所述。

在闭包表达式中写入 `throws` 或 `async` 明确将闭包标记为抛出或异步。

```swift
{ (<#parameters#>) async throws -> <#return type#> in
   <#statements#>
}
```

如果闭包的主体包含一个 `throws` 语句或一个 `try` 表达式，并且该表达式没有嵌套在一个带有详尽错误处理的 `do` 语句中，则该闭包被理解为抛出错误。如果一个抛出错误的闭包只抛出单一类型的错误，则该闭包被理解为抛出该错误类型；否则，它被理解为抛出 `any Error` 。同样，如果主体包含一个 `await` 表达式，则它被理解为是异步的。

有几种特殊形式允许闭包更简洁地编写：

- 闭包可以省略其参数的类型、返回类型或两者。如果您省略参数名称和两种类型，则在语句之前省略 `in` 关键字。如果省略的类型无法推断，将会引发编译时错误。
- 闭包可以省略参数名称。其参数将隐式命名为 `$` ，后接它们的位置： `$0` 、 `$1` 、 `$2` ，依此类推。
- 仅由单个表达式组成的闭包被理解为返回该表达式的值。在对周围表达式进行类型推断时，也会考虑该表达式的内容。

以下闭包表达式是等价的：

```swift
myFunction { (x: Int, y: Int) -> Int in
    return x + y
}


myFunction { x, y in
    return x + y
}


myFunction { return $0 + $1 }


myFunction { $0 + $1 }
```

有关将闭包作为参数传递给函数的信息，请参见函数调用表达式。

闭包表达式可以在不存储在变量或常量中的情况下使用，例如当您立即将闭包用作函数调用的一部分时。在上面的代码中传递给 `myFunction` 的闭包表达式就是这种即时使用的例子。因此，闭包表达式是逃逸还是非逃逸取决于表达式周围的上下文。如果闭包表达式被立即调用或作为非逃逸函数参数传递，则它是非逃逸的。否则，闭包表达式就是逃逸的。

有关逃逸闭包的更多信息，请参见逃逸闭包。

[**捕获列表**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Capture-Lists)

默认情况下，闭包表达式以强引用捕获其周围作用域中的常量和变量。您可以使用捕获列表显式控制在闭包中如何捕获值。

捕获列表写成用方括号括起来的以逗号分隔的表达式列表，位于参数列表之前。如果使用捕获列表，则必须使用 `in` 关键字，即使省略参数名称、参数类型和返回类型。

捕获列表中的条目在闭包创建时初始化。对于捕获列表中的每个条目，都会将一个常量初始化为在周围作用域中具有相同名称的常量或变量的值。例如，在下面的代码中， `a` 被包含在捕获列表中，但 `b` 没有，这使得它们的行为不同。

```swift
var a = 0
var b = 0
let closure = { [a] in
 print(a, b)
}


a = 10
b = 10
closure()
// Prints "0 10"
```

有两个不同的东西被称为 `a` ，一个是外部作用域中的变量，另一个是闭包作用域中的常量，但只有一个名为 `b` 的变量。内部作用域中的 `a` 在闭包创建时用外部作用域中的 `a` 的值初始化，但它们的值并没有以任何特殊方式连接。这意味着外部作用域中 `a` 的值的变化不会影响内部作用域中 `a` 的值，闭包内部的 `a` 的变化也不会影响闭包外部的 `a` 的值。相比之下，只有一个名为 `b` 的变量——外部作用域中的 `b` ——因此来自闭包内部或外部的变化在两个地方都是可见的。

当捕获变量的类型具有引用语义时，这种区别是不可见的。例如，下面的代码中有两个名为 `x` 的东西，一个是外部作用域中的变量，另一个是内部作用域中的常量，但由于引用语义，它们都指向同一个对象。

```swift
class SimpleClass {
    var value: Int = 0
}
var x = SimpleClass()
var y = SimpleClass()
let closure = { [x] in
    print(x.value, y.value)
}


x.value = 10
y.value = 10
closure()
// Prints "10 10"
```

如果表达式值的类型是一个类，您可以在捕获列表中使用 `weak` 或 `unowned` 标记表达式，以捕获对表达式值的弱引用或无主引用。

```swift
myFunction { print(self.title) }                    // implicit strong capture
myFunction { [self] in print(self.title) }          // explicit strong capture
myFunction { [weak self] in print(self!.title) }    // weak capture
myFunction { [unowned self] in print(self.title) }  // unowned capture
```

您还可以将任意表达式绑定到捕获列表中的命名值。该表达式在闭包创建时被求值，并且该值以指定的强度被捕获。例如：

```swift
// Weak capture of "self.parent" as "parent"
myFunction { [weak parent = self.parent] in print(parent!.title) }
```

有关闭包表达式的更多信息和示例，请参见闭包表达式。有关捕获列表的更多信息和示例，请参见解决闭包的强引用循环。

Grammar of a closure expression

_closure-expression_ → **`{`** _attributes?_ _closure-signature?_ _statements?_ **`}`**

_closure-signature_ → _capture-list?_ _closure-parameter-clause_ **`async`**_?_ _throws-clause?_ _function-result?_ **`in`**\
&#xNAN;_&#x63;losure-signature_ → _capture-list_ **`in`**

_closure-parameter-clause_ → **`(`** **`)`** | **`(`** _closure-parameter-list_ **`)`** | _identifier-list_\
&#xNAN;_&#x63;losure-parameter-list_ → _closure-parameter_ | _closure-parameter_ **`,`** _closure-parameter-list_\
&#xNAN;_&#x63;losure-parameter_ → _closure-parameter-name_ _type-annotation?_\
&#xNAN;_&#x63;losure-parameter_ → _closure-parameter-name_ _type-annotation_ **`...`**\
&#xNAN;_&#x63;losure-parameter-name_ → _identifier_

_capture-list_ → **`[`** _capture-list-items_ **`]`**\
&#xNAN;_&#x63;apture-list-items_ → _capture-list-item_ | _capture-list-item_ **`,`** _capture-list-items_\
&#xNAN;_&#x63;apture-list-item_ → _capture-specifier?_ _identifier_\
&#xNAN;_&#x63;apture-list-item_ → _capture-specifier?_ _identifier_ **`=`** _expression_\
&#xNAN;_&#x63;apture-list-item_ → _capture-specifier?_ _self-expression_\
&#xNAN;_&#x63;apture-specifier_ → **`weak`** | **`unowned`** | **`unowned(safe)`** | **`unowned(unsafe)`**

### [隐式成员表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Implicit-Member-Expression)

隐式成员表达式是一种简化方式，用于在类型推断可以确定隐含类型的上下文中访问类型的成员，例如枚举案例或类型方法。它具有以下形式：

```swift
.<#member name#>
```

例如：

```swift
var x = MyEnumeration.someValue
x = .anotherValue
```

如果推断的类型是可选的，您还可以在隐式成员表达式中使用非可选类型的成员。

```swift
var someOptional: MyEnumeration? = .someValue
```

隐式成员表达式可以跟在后缀操作符或后缀语法后，如后缀表达式所列。这被称为链式隐式成员表达式。虽然所有链式后缀表达式通常具有相同的类型，但唯一的要求是整个链式隐式成员表达式需要能够转换为其上下文所暗示的类型。具体来说，如果暗示的类型是可选的，则可以使用非可选类型的值；如果暗示的类型是类类型，则可以使用其子类之一的值。例如：

```swift
class SomeClass {
    static var shared = SomeClass()
    static var sharedSubclass = SomeSubclass()
    var a = AnotherClass()
}
class SomeSubclass: SomeClass { }
class AnotherClass {
    static var s = SomeClass()
    func f() -> SomeClass { return AnotherClass.s }
}
let x: SomeClass = .shared.a.f()
let y: SomeClass? = .shared
let z: SomeClass = .sharedSubclass
```

在上面的代码中， `x` 的类型与其上下文隐含的类型完全匹配， `y` 的类型可以从 `SomeClass` 转换为 `SomeClass?` ，而 `z` 的类型可以从 `SomeSubclass` 转换为 `SomeClass` 。

隐式成员表达式的语法

_implicit-member-expression_ → **`.`** _identifier_\
&#xNAN;_&#x69;mplicit-member-expression_ → **`.`** _identifier_ **`.`** _postfix-expression_

### [带括号的表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Parenthesized-Expression)

带括号的表达式由一个被括号包围的表达式组成。您可以使用括号通过明确分组表达式来指定操作的优先级。分组括号不会改变表达式的类型——例如， `(1)` 的类型简单地是 `Int` 。

带括号表达式的语法

_parenthesized-expression_ → **`(`** _expression_ **`)`**

### [元组表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Tuple-Expression)

元组表达式由用逗号分隔的表达式列表组成，列表被括号包围。每个表达式前可以有一个可选的标识符，用冒号分隔 ( `:` )。它具有以下形式：

```swift
(<#identifier 1#>: <#expression 1#>, <#identifier 2#>: <#expression 2#>, <#...#>)
```

元组表达式中的每个标识符在元组表达式的范围内必须是唯一的。在嵌套的元组表达式中，同一层嵌套的标识符必须是唯一的。例如， `(a: 10, a: 20)` 是无效的，因为标签 `a` 在同一层出现了两次。然而， `(a: 10, b: (a: 1, x: 2))` 是有效的——尽管 `a` 出现了两次，但它在外部元组中出现一次，在内部元组中出现一次。

元组表达式可以包含零个表达式，或者可以包含两个或更多表达式。括号内的单个表达式是一个带括号的表达式。

> 注意
>
> 在 Swift 中，空元组表达式和空元组类型都写作 `()` 。因为 `Void` 是 `()` 的类型别名，所以您可以用它来写一个空元组类型。然而，像所有类型别名一样， `Void` 始终是一个类型——您不能用它来写一个空元组表达式。

元组表达式的语法

_tuple-expression_ → **`(`** **`)`** | **`(`** _tuple-element_ **`,`** _tuple-element-list_ **`)`**\
&#xNAN;_&#x74;uple-element-list_ → _tuple-element_ | _tuple-element_ **`,`** _tuple-element-list_\
&#xNAN;_&#x74;uple-element_ → _expression_ | _identifier_ **`:`** _expression_

### [通配符表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Wildcard-Expression)

通配符表达式用于在赋值过程中显式忽略一个值。例如，在以下赋值中，10 被赋值给 `x` ，而 20 被忽略：

```swift
(x, _) = (10, 20)
// x is 10, and 20 is ignored
```

通配符表达式的语法

_wildcard-expression_ → **`_`**

### [宏展开表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Macro-Expansion-Expression)

宏展开表达式由宏名称后跟一个用逗号分隔的宏参数列表（括号内）组成。宏在编译时展开。宏展开表达式具有以下形式：

```swift
<#macro name#>(<#macro argument 1#>, <#macro argument 2#>)
```

如果宏不接受任何参数，则宏展开表达式省略宏名称后的括号。

宏展开表达式可以作为参数的默认值。当作为函数或方法参数的默认值使用时，宏是使用调用位置的源代码位置进行评估的，而不是它们在函数定义中出现的位置。然而，当默认值是一个包含宏及其他代码的更大表达式时，这些宏是在函数定义中出现的位置进行评估的。

```swift
func f(a: Int = #line, b: Int = (#line), c: Int = 100 + #line) {
    print(a, b, c)
}
f()  // Prints "4 1 101"
```

在上面的函数中， `a` 的默认值是一个单一的宏表达式，因此该宏是使用 `f(a:b:c:)` 被调用时的源代码位置进行评估的。相比之下， `b` 和 `c` 的值是包含宏的表达式——这些表达式中的宏是在 `f(a:b:c:)` 定义的源代码位置进行评估的。

当您将宏用作默认值时，它会在不展开宏的情况下进行类型检查，以检查以下要求：

- 宏的访问级别与使用它的函数相同或不那么严格。
- 宏要么不接受参数，要么其参数是没有字符串插值的字面量。
- 宏的返回类型与参数的类型匹配。

您使用宏表达式来调用独立宏。要调用附加宏，请使用在属性中描述的自定义属性语法。独立宏和附加宏的展开如下：

1. Swift 解析源代码以生成抽象语法树 (AST)。
2. 宏实现接收 AST 节点作为输入，并执行该宏所需的转换。
3. 宏实现生成的转换后的 AST 节点被添加到原始 AST 中。

每个宏的扩展是独立且自包含的。然而，作为性能优化，Swift 可能会启动一个实现宏的外部进程，并重用同一进程来扩展多个宏。当您实现一个宏时，该代码不得依赖于您的代码之前扩展的宏，或任何其他外部状态，如当前时间。

对于具有多个角色的嵌套宏和附加宏，扩展过程会重复。嵌套宏扩展表达式从外向内扩展。例如，在下面的代码中， `outerMacro(_:)` 首先扩展，未扩展的对 `innerMacro(_:)` 的调用出现在 `outerMacro(_:)` 接收的抽象语法树中。

```swift
#outerMacro(12, #innerMacro(34), "some text")
```

具有多个角色的附加宏会根据每个角色扩展一次。每次扩展都接收相同的原始 AST 作为输入。Swift 通过收集所有生成的 AST 节点并将其放置在 AST 的相应位置来形成整体扩展。

有关 Swift 中宏的概述，请参见 Macros。

宏展开表达式的语法

_macro-expansion-expression_ → **`#`** _identifier_ _generic-argument-clause?_ _function-call-argument-clause?_ _trailing-closures?_

### [关键路径表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Key-Path-Expression)

键路径表达式指的是某种类型的属性或下标。您在动态编程任务中使用键路径表达式，例如键值观察。它们具有以下形式：

```swift
\<#type name#>.<#path#>
```

类型名称是一个具体类型的名称，包括任何泛型参数，例如 `String` 、 `[Int]` 或 `Set<Int>` 。

路径由属性名称、下标、可选链表达式和强制解包表达式组成。这些关键路径组件可以根据需要重复任意次数，顺序不限。

在编译时，关键路径表达式会被替换为 `KeyPath` 类的一个实例。

要使用键路径访问一个值，请将键路径传递给所有类型可用的 `subscript(keyPath:)` 下标。例如：

```swift
struct SomeStructure {
    var someValue: Int
}


let s = SomeStructure(someValue: 12)
let pathToProperty = \SomeStructure.someValue


let value = s[keyPath: pathToProperty]
// value is 12
```

在类型推断可以确定隐含类型的上下文中，可以省略类型名称。以下代码使用 `\.someProperty` 而不是 `\SomeClass.someProperty` :

```swift
class SomeClass: NSObject {
    @objc dynamic var someProperty: Int
    init(someProperty: Int) {
        self.someProperty = someProperty
    }
}


let c = SomeClass(someProperty: 10)
c.observe(\.someProperty) { object, change in
    // ...
}
```

路径可以参考 `self` 来创建身份密钥路径 ( `\.self` )。身份密钥路径指的是整个实例，因此您可以用它在一步中访问和更改存储在变量中的所有数据。例如：

```swift
var compoundValue = (a: 1, b: 2)
// Equivalent to compoundValue = (a: 10, b: 20)
compoundValue[keyPath: \.self] = (a: 10, b: 20)
```

路径可以包含多个属性名称，用点分隔，以引用属性值的属性。此代码使用键路径表达式 `\OuterStructure.outer.someValue` 来访问 `OuterStructure` 类型的 `outer` 属性的 `someValue` 属性：

```swift
struct OuterStructure {
    var outer: SomeStructure
    init(someValue: Int) {
        self.outer = SomeStructure(someValue: someValue)
    }
}


let nested = OuterStructure(someValue: 24)
let nestedKeyPath = \OuterStructure.outer.someValue


let nestedValue = nested[keyPath: nestedKeyPath]
// nestedValue is 24
```

路径可以使用括号包含下标，只要下标的参数类型符合 `Hashable` 协议。此示例使用键路径中的下标来访问数组的第二个元素：

```swift
let greetings = ["hello", "hola", "bonjour", "안녕"]
let myGreeting = greetings[keyPath: \[String].[1]]
// myGreeting is 'hola'
```

在下标中使用的值可以是命名值或字面量。值通过值语义在键路径中被捕获。以下代码在键路径表达式和闭包中都使用了变量 `index` 来访问 `greetings` 数组的第三个元素。当 `index` 被修改时，键路径表达式仍然引用第三个元素，而闭包使用新的索引。

```swift
var index = 2
let path = \[String].[index]
let fn: ([String]) -> String = { strings in strings[index] }


print(greetings[keyPath: path])
// Prints "bonjour"
print(fn(greetings))
// Prints "bonjour"


// Setting 'index' to a new value doesn't affect 'path'
index += 1
print(greetings[keyPath: path])
// Prints "bonjour"


// Because 'fn' closes over 'index', it uses the new value
print(fn(greetings))
// Prints "안녕"
```

路径可以使用可选链和强制解包。此代码在键路径中使用可选链来访问可选字符串的属性：

```swift
let firstGreeting: String? = greetings.first
print(firstGreeting?.count as Any)
// Prints "Optional(5)"


// Do the same thing using a key path.
let count = greetings[keyPath: \[String].first?.count]
print(count as Any)
// Prints "Optional(5)"
```

您可以混合和匹配键路径的组件，以访问类型中深层嵌套的值。以下代码通过使用组合这些组件的键路径表达式来访问字典数组的不同值和属性。

```swift
let interestingNumbers = ["prime": [2, 3, 5, 7, 11, 13, 17],
                          "triangular": [1, 3, 6, 10, 15, 21, 28],
                          "hexagonal": [1, 6, 15, 28, 45, 66, 91]]
print(interestingNumbers[keyPath: \[String: [Int]].["prime"]] as Any)
// Prints "Optional([2, 3, 5, 7, 11, 13, 17])"
print(interestingNumbers[keyPath: \[String: [Int]].["prime"]![0]])
// Prints "2"
print(interestingNumbers[keyPath: \[String: [Int]].["hexagonal"]!.count])
// Prints "7"
print(interestingNumbers[keyPath: \[String: [Int]].["hexagonal"]!.count.bitWidth])
// Prints "64"
```

您可以在通常提供函数或闭包的上下文中使用键路径表达式。具体来说，您可以使用根类型为 `SomeType` 且路径产生类型为 `Value` 的键路径表达式，而不使用类型为 `(SomeType) -> Value` 的函数或闭包。

```swift
struct Task {
    var description: String
    var completed: Bool
}
var toDoList = [
    Task(description: "Practice ping-pong.", completed: false),
    Task(description: "Buy a pirate costume.", completed: true),
    Task(description: "Visit Boston in the Fall.", completed: false),
]


// Both approaches below are equivalent.
let descriptions = toDoList.filter(\.completed).map(\.description)
let descriptions2 = toDoList.filter { $0.completed }.map { $0.description }
```

键路径表达式的任何副作用仅在表达式被求值的时刻进行评估。例如，如果在键路径表达式的下标中进行函数调用，则该函数仅在评估表达式时调用一次，而不是每次使用键路径时都调用。

```swift
func makeIndex() -> Int {
    print("Made an index")
    return 0
}
// The line below calls makeIndex().
let taskKeyPath = \[Task][makeIndex()]
// Prints "Made an index"


// Using taskKeyPath doesn't call makeIndex() again.
let someTask = toDoList[keyPath: taskKeyPath]
```

有关在与 Objective-C API 交互的代码中使用键路径的更多信息，请参见《在 Swift 中使用 Objective-C 运行时特性》。有关键值编码和键值观察的信息，请参见《键值编码编程指南》和《键值观察编程指南》。

键路径表达式的语法

_key-path-expression_ → **`\`** _type?_ **`.`** _key-path-components_\
&#xNAN;_&#x6B;ey-path-components_ → _key-path-component_ | _key-path-component_ **`.`** _key-path-components_\
&#xNAN;_&#x6B;ey-path-component_ → _identifier_ _key-path-postfixes?_ | _key-path-postfixes_

_key-path-postfixes_ → _key-path-postfix_ _key-path-postfixes?_\
&#xNAN;_&#x6B;ey-path-postfix_ → **`?`** | **`!`** | **`self`** | **`[`** _function-call-argument-list_ **`]`**

### [选择器表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Selector-Expression)

选择器表达式让您访问用于引用方法或属性的 getter 或 setter 的选择器。在 Objective-C 中，它具有以下形式：

```swift
#selector(<#method name#>)
#selector(getter: <#property name#>)
#selector(setter: <#property name#>)
```

方法名称和属性名称必须引用在 Objective-C 运行时中可用的方法或属性。选择器表达式的值是 `Selector` 类型的实例。例如：

```swift
class SomeClass: NSObject {
    @objc let property: String


    @objc(doSomethingWithInt:)
    func doSomething(_ x: Int) { }


    init(property: String) {
        self.property = property
    }
}
let selectorForMethod = #selector(SomeClass.doSomething(_:))
let selectorForPropertyGetter = #selector(getter: SomeClass.property)
```

在为属性的获取器创建选择器时，属性名称可以是对变量或常量属性的引用。相反，在为属性的设置器创建选择器时，属性名称必须仅是对变量属性的引用。

方法名称可以包含括号用于分组，以及 `as` 运算符以区分共享名称但具有不同类型签名的方法。例如：

```swift
extension SomeClass {
    @objc(doSomethingWithString:)
    func doSomething(_ x: String) { }
}
let anotherSelector = #selector(SomeClass.doSomething(_:) as (SomeClass) -> (String) -> Void)
```

因为选择器是在编译时创建的，而不是在运行时，编译器可以检查方法或属性是否存在，并且它们是否暴露给 Objective-C 运行时。

> 注意
>
> 虽然方法名和属性名是表达式，但它们从未被求值。

有关在与 Objective-C API 交互的 Swift 代码中使用选择器的更多信息，请参见《在 Swift 中使用 Objective-C 运行时特性》。

选择器表达式的语法

_selector-expression_ → **`#selector`** **`(`** _expression_ **`)`**\
&#xNAN;_&#x73;elector-expression_ → **`#selector`** **`(`** **`getter:`** _expression_ **`)`**\
&#xNAN;_&#x73;elector-expression_ → **`#selector`** **`(`** **`setter:`** _expression_ **`)`**

### [键路径字符串表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Key-Path-String-Expression)

键路径字符串表达式让您访问用于引用 Objective-C 中属性的字符串，以便在键值编码和键值观察 API 中使用。它具有以下形式：

```swift
#keyPath(<#property name#>)
```

属性名称必须是对在 Objective-C 运行时中可用属性的引用。在编译时，键路径字符串表达式会被替换为字符串字面量。例如：

```swift
class SomeClass: NSObject {
    @objc var someProperty: Int
    init(someProperty: Int) {
       self.someProperty = someProperty
    }
}


let c = SomeClass(someProperty: 12)
let keyPath = #keyPath(SomeClass.someProperty)


if let value = c.value(forKey: keyPath) {
    print(value)
}
// Prints "12"
```

当您在一个类中使用键路径字符串表达式时，可以通过仅写属性名称而不写类名来引用该类的属性。

```swift
extension SomeClass {
    func getSomeKeyPath() -> String {
        return #keyPath(someProperty)
    }
}
print(keyPath == c.getSomeKeyPath())
// Prints "true"
```

因为键路径字符串是在编译时创建的，而不是在运行时，因此编译器可以检查该属性是否存在，以及该属性是否暴露给 Objective-C 运行时。

有关在与 Objective-C API 交互的 Swift 代码中使用键路径的更多信息，请参见《在 Swift 中使用 Objective-C 运行时特性》。有关键值编码和键值观察的信息，请参见《键值编码编程指南》和《键值观察编程指南》。

> 注意
>
> 虽然属性名称是一个表达式，但它从未被求值。

键路径字符串表达式的语法

_key-path-string-expression_ → **`#keyPath`** **`(`** _expression_ **`)`**

## [后缀表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Postfix-Expressions)

后缀表达式是通过将后缀运算符或其他后缀语法应用于一个表达式来形成的。从语法上讲，每个基本表达式也是一个后缀表达式。

有关这些运算符行为的信息，请参见基本运算符和高级运算符。

有关 Swift 标准库提供的运算符的信息，请参见运算符声明。

后缀表达式的语法

_postfix-expression_ → _primary-expression_\
&#xNAN;_&#x70;ostfix-expression_ → _postfix-expression_ _postfix-operator_\
&#xNAN;_&#x70;ostfix-expression_ → _function-call-expression_\
&#xNAN;_&#x70;ostfix-expression_ → _initializer-expression_\
&#xNAN;_&#x70;ostfix-expression_ → _explicit-member-expression_\
&#xNAN;_&#x70;ostfix-expression_ → _postfix-self-expression_\
&#xNAN;_&#x70;ostfix-expression_ → _subscript-expression_\
&#xNAN;_&#x70;ostfix-expression_ → _forced-value-expression_\
&#xNAN;_&#x70;ostfix-expression_ → _optional-chaining-expression_

### [函数调用表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Function-Call-Expression)

函数调用表达式由函数名称和括号内以逗号分隔的函数参数列表组成。函数调用表达式具有以下形式：

```swift
<#function name#>(<#argument value 1#>, <#argument value 2#>)
```

函数名称可以是任何值为函数类型的表达式。

如果函数定义包含参数的名称，则函数调用必须在其参数值之前包含名称，名称与参数值之间用冒号分隔 ( `:` )。这种类型的函数调用表达式具有以下形式：

```swift
<#function name#>(<#argument name 1#>: <#argument value 1#>, <#argument name 2#>: <#argument value 2#>)
```

函数调用表达式可以在闭合括号后立即包含以闭包表达式形式的尾随闭包。尾随闭包被视为函数的参数，在最后一个带括号的参数后添加。第一个闭包表达式是无标签的；任何额外的闭包表达式前面都有它们的参数标签。下面的示例显示了使用和不使用尾随闭包语法的函数调用的等效版本：

```swift
// someFunction takes an integer and a closure as its arguments
someFunction(x: x, f: { $0 == 13 })
someFunction(x: x) { $0 == 13 }


// anotherFunction takes an integer and two closures as its arguments
anotherFunction(x: x, f: { $0 == 13 }, g: { print(99) })
anotherFunction(x: x) { $0 == 13 } g: { print(99) }
```

如果尾随闭包是函数的唯一参数，则可以省略括号。

```swift
// someMethod takes a closure as its only argument
myData.someMethod() { $0 == 13 }
myData.someMethod { $0 == 13 }
```

为了包含尾随闭包作为参数，编译器从左到右检查函数的参数，如下所示：

| 尾随闭包 | 参数         | 动作                                                                           |
| -------- | ------------ | ------------------------------------------------------------------------------ |
| 标记     | 标记         | 如果标签相同，则闭包匹配参数；否则跳过参数。                                   |
| 标记     | 未标记       | 参数被跳过。                                                                   |
| 未标记   | 标记或未标记 | 如果参数在结构上类似于下面定义的函数类型，则闭包匹配该参数；否则，参数被跳过。 |

尾随闭包作为与之匹配的参数的参数传递。扫描过程中跳过的参数没有传递给它们的参数——例如，它们可以使用默认参数。找到匹配后，扫描继续进行下一个尾随闭包和下一个参数。在匹配过程结束时，所有尾随闭包必须有一个匹配。

如果参数不是输入输出参数，并且参数是以下之一，则参数在结构上类似于函数类型：

- 一个类型为函数类型的参数，如 `(Bool) -> Int`
- 一个自动闭包参数，其包装表达式的类型是函数类型，例如 `@autoclosure () -> ((Bool) -> Int)`
- 一个可变参数，其数组元素类型是一个函数类型，如 `((Bool) -> Int)...`
- 一个参数，其类型被一个或多个可选层包装，如 `Optional<(Bool) -> Int>`
- 一个参数，其类型组合了这些允许的类型，如 `(Optional<(Bool) -> Int>)...`

当一个尾随闭包与一个结构上类似于函数类型但不是函数的参数匹配时，闭包会根据需要进行包装。例如，如果参数的类型是一个可选类型，闭包会自动包装在 `Optional` 中。

为了简化从 5.3 之前版本的 Swift 迁移代码的过程——这些版本是从右到左进行匹配的——编译器会检查从左到右和从右到左的顺序。如果扫描方向产生不同的结果，将使用旧的从右到左的顺序，并且编译器会生成警告。未来版本的 Swift 将始终使用从左到右的顺序。

```swift
typealias Callback = (Int) -> Int
func someFunction(firstClosure: Callback? = nil,
                secondClosure: Callback? = nil) {
    let first = firstClosure?(10)
    let second = secondClosure?(20)
    print(first ?? "-", second ?? "-")
}


someFunction()  // Prints "- -"
someFunction { return $0 + 100 }  // Ambiguous
someFunction { return $0 } secondClosure: { return $0 }  // Prints "10 20"
```

在上面的示例中，标记为“模糊”的函数调用打印“- 120”，并在 Swift 5.3 上产生编译器警告。未来版本的 Swift 将打印“110 -”。

一个类、结构或枚举类型可以通过声明几种方法之一来启用函数调用语法的语法糖，如《具有特殊名称的方法》中所述。

[**隐式转换为指针类型**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Implicit-Conversion-to-a-Pointer-Type)

在函数调用表达式中，如果参数和参数的类型不同，编译器会尝试通过应用以下列表中的隐式转换之一来使它们的类型匹配：

- `inout SomeType` 可以变成 `UnsafePointer<SomeType>` 或 `UnsafeMutablePointer<SomeType>`
- `inout Array<SomeType>` 可以变成 `UnsafePointer<SomeType>` 或 `UnsafeMutablePointer<SomeType>`
- `Array<SomeType>` 可以变成 `UnsafePointer<SomeType>`
- `String` 可以变成 `UnsafePointer<CChar>`

以下两个函数调用是等价的：

```swift
func unsafeFunction(pointer: UnsafePointer<Int>) {
    // ...
}
var myNumber = 1234


unsafeFunction(pointer: &myNumber)
withUnsafePointer(to: myNumber) { unsafeFunction(pointer: $0) }
```

由这些隐式转换创建的指针仅在函数调用期间有效。为避免未定义行为，请确保您的代码在函数调用结束后永远不要保持指针。

Note

当隐式将数组转换为不安全指针时，Swift 确保数组的存储是连续的，通过根据需要转换或复制数组。例如，您可以使用此语法与从 `NSArray` 子类桥接到 `Array` 的数组。如果您需要保证数组的存储已经是连续的，以便隐式转换不需要执行此操作，请使用 `ContiguousArray` 而不是 `Array` 。

使用 `&` 而不是像 `withUnsafePointer(to:)` 这样的显式函数可以帮助使对低级 C 函数的调用更具可读性，特别是当函数接受多个指针参数时。然而，在调用其他 Swift 代码中的函数时，避免使用 `&` ，而是显式使用不安全的 API。

函数调用表达式的语法

_function-call-expression_ → _postfix-expression_ _function-call-argument-clause_\
&#xNAN;_&#x66;unction-call-expression_ → _postfix-expression_ _function-call-argument-clause?_ _trailing-closures_

_function-call-argument-clause_ → **`(`** **`)`** | **`(`** _function-call-argument-list_ **`)`**\
&#xNAN;_&#x66;unction-call-argument-list_ → _function-call-argument_ | _function-call-argument_ **`,`** _function-call-argument-list_\
&#xNAN;_&#x66;unction-call-argument_ → _expression_ | _identifier_ **`:`** _expression_\
&#xNAN;_&#x66;unction-call-argument_ → _operator_ | _identifier_ **`:`** _operator_

_trailing-closures_ → _closure-expression_ _labeled-trailing-closures?_\
&#xNAN;_&#x6C;abeled-trailing-closures_ → _labeled-trailing-closure_ _labeled-trailing-closures?_\
&#xNAN;_&#x6C;abeled-trailing-closure_ → _identifier_ **`:`** _closure-expression_

### [初始化表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Initializer-Expression)

初始化表达式提供对类型初始化器的访问。它具有以下形式：

```swift
<#expression#>.init(<#initializer arguments#>)
```

您在函数调用表达式中使用初始化表达式来初始化类型的新实例。您还使用初始化表达式委托给超类的初始化器。

```swift
class SomeSubClass: SomeSuperClass {
    override init() {
        // subclass initialization goes here
        super.init()
    }
}
```

像函数一样，初始化器可以作为值使用。例如：

```swift
// Type annotation is required because String has multiple initializers.
let initializer: (Int) -> String = String.init
let oneTwoThree = [1, 2, 3].map(initializer).reduce("", +)
print(oneTwoThree)
// Prints "123"
```

如果您按名称指定类型，则可以在不使用初始化程序表达式的情况下访问该类型的初始化程序。在所有其他情况下，您必须使用初始化程序表达式。

```swift
let s1 = SomeType.init(data: 3)  // Valid
let s2 = SomeType(data: 1)       // Also valid


let s3 = type(of: someValue).init(data: 7)  // Valid
let s4 = type(of: someValue)(data: 5)       // Error
```

初始化表达式的语法

_initializer-expression_ → _postfix-expression_ **`.`** **`init`**\
&#xNAN;_&#x69;nitializer-expression_ → _postfix-expression_ **`.`** **`init`** **`(`** _argument-names_ **`)`**

### [显式成员表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Explicit-Member-Expression)

显式成员表达式允许访问命名类型、元组或模块的成员。它由项和其成员标识符之间的一个句点 ( `.` ) 组成。

```swift
<#expression#>.<#member name#>
```

命名类型的成员作为类型声明或扩展的一部分被命名。例如：

```swift
class SomeClass {
    var someProperty = 42
}
let c = SomeClass()
let y = c.someProperty  // Member access
```

元组的成员使用整数隐式命名，按出现的顺序，从零开始。例如：

```swift
var t = (10, 20, 30)
t.0 = t.1
// Now t is (20, 20, 30)
```

模块的成员访问该模块的顶层声明。

使用 `dynamicMemberLookup` 属性声明的类型包括在运行时查找的成员，如属性中所述。

要区分仅通过参数名称不同的方法或初始化器，请在括号中包含参数名称，每个参数名称后跟一个冒号 ( `:` )。对于没有名称的参数，请写下一个下划线 ( `_` )。要区分重载方法，请使用类型注释。例如：

```swift
class SomeClass {
    func someMethod(x: Int, y: Int) {}
    func someMethod(x: Int, z: Int) {}
    func overloadedMethod(x: Int, y: Int) {}
    func overloadedMethod(x: Int, y: Bool) {}
}
let instance = SomeClass()


let a = instance.someMethod              // Ambiguous
let b = instance.someMethod(x:y:)        // Unambiguous


let d = instance.overloadedMethod        // Ambiguous
let d = instance.overloadedMethod(x:y:)  // Still ambiguous
let d: (Int, Bool) -> Void  = instance.overloadedMethod(x:y:)  // Unambiguous
```

如果一行的开头出现一个句点，它被理解为显式成员表达式的一部分，而不是隐式成员表达式。例如，以下列表显示了跨多行的链式方法调用：

```swift
let x = [10, 3, 20, 15, 4]
    .sorted()
    .filter { $0 > 5 }
    .map { $0 * 100 }
```

您可以将这种多行链式语法与编译器控制语句结合使用，以控制何时调用每个方法。例如，以下代码在 iOS 上使用了不同的过滤规则：

```swift
let numbers = [10, 20, 33, 43, 50]
#if os(iOS)
    .filter { $0 < 40 }
#else
    .filter { $0 > 25 }
#endif
```

在 `#if` 、 `#endif` 和其他编译指令之间，条件编译块可以包含一个隐式成员表达式，后面跟着零个或多个后缀，以形成后缀表达式。它还可以包含另一个条件编译块，或这些表达式和块的组合。

您可以在任何可以编写显式成员表达式的地方使用这种语法，而不仅仅是在顶级代码中。

在条件编译块中， `#if` 编译指令的分支必须包含至少一个表达式。其他分支可以为空。

显式成员表达式的语法

_explicit-member-expression_ → _postfix-expression_ **`.`** _decimal-digits_\
&#xNAN;_&#x65;xplicit-member-expression_ → _postfix-expression_ **`.`** _identifier_ _generic-argument-clause?_\
&#xNAN;_&#x65;xplicit-member-expression_ → _postfix-expression_ **`.`** _identifier_ **`(`** _argument-names_ **`)`**\
&#xNAN;_&#x65;xplicit-member-expression_ → _postfix-expression_ _conditional-compilation-block_

_argument-names_ → _argument-name_ _argument-names?_\
&#xNAN;_&#x61;rgument-name_ → _identifier_ **`:`**

### [后缀自表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Postfix-Self-Expression)

后缀 `self` 表达式由一个表达式或类型名称组成，紧接着 `.self` 。它具有以下形式：

```swift
<#expression#>.self
<#type#>.self
```

第一种形式的值为表达式的值。例如， `x.self` 的值为 `x` 。

第二种形式计算为类型的值。使用这种形式将类型作为值进行访问。例如，因为 `SomeClass.self` 计算为 `SomeClass` 类型本身，所以您可以将其传递给接受类型级参数的函数或方法。

后缀自表达式的语法

_postfix-self-expression_ → _postfix-expression_ **`.`** **`self`**

### [下标表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Subscript-Expression)

下标表达式提供使用相应下标声明的 getter 和 setter 进行下标访问。它具有以下形式：

```swift
<#expression#>[<#index expressions#>]
```

要计算下标表达式的值，调用表达式类型的下标 getter，并将索引表达式作为下标参数传递。要设置其值，以相同的方式调用下标 setter。

有关下标声明的信息，请参见协议下标声明。

下标表达式的语法

_subscript-expression_ → _postfix-expression_ **`[`** _function-call-argument-list_ **`]`**

### [强制值表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Forced-Value-Expression)

强制值表达式解包一个您确定不是 `nil` 的可选值。它具有以下形式：

```swift
<#expression#>!
```

如果表达式的值不是 `nil` ，则可选值被解包并以相应的非可选类型返回。否则，将引发运行时错误。

强制值表达式的未包装值可以被修改，方法是改变值本身，或者给值的某个成员赋值。例如：

```swift
var x: Int? = 0
x! += 1
// x is now 1


var someDictionary = ["a": [1, 2, 3], "b": [10, 20]]
someDictionary["a"]![0] = 100
// someDictionary is now ["a": [100, 2, 3], "b": [10, 20]]
```

强制值表达式的语法

_forced-value-expression_ → _postfix-expression_ **`!`**

### [可选链表达式](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/expressions/#Optional-Chaining-Expression)

可选链表达式为在后缀表达式中使用可选值提供了简化的语法。其形式如下：

```swift
<#expression#>?
```

后缀 `?` 运算符从一个表达式创建一个可选链式表达式，而不改变该表达式的值。

可选链表达式必须出现在后缀表达式中，并且它们会以特殊方式评估后缀表达式。如果可选链表达式的值是 `nil` ，则后缀表达式中的所有其他操作都将被忽略，整个后缀表达式的值为 `nil` 。如果可选链表达式的值不是 `nil` ，则可选链表达式的值会被解包并用于评估后续的后缀表达式。在这两种情况下，后缀表达式的值仍然是可选类型。

如果包含可选链表达式的后缀表达式嵌套在其他后缀表达式中，则只有最外层的表达式返回可选类型。在下面的示例中，当 `c` 不是 `nil` 时，它的值被解包并用于评估 `.property` ，`.property` 的值用于评估 `.performAction()` 。整个表达式 `c?.property.performAction()` 的值为可选类型。

```swift
var c: SomeClass?
var result: Bool? = c?.property.performAction()
```

以下示例展示了上述示例在不使用可选链的情况下的行为。

```swift
var result: Bool?
if let unwrappedC = c {
    result = unwrappedC.property.performAction()
}
```

可选链表达式的未包装值可以被修改，要么通过改变值本身，要么通过赋值给值的某个成员。如果可选链表达式的值是 `nil` ，则赋值运算符右侧的表达式不会被求值。例如：

```swift
func someFunctionWithSideEffects() -> Int {
    return 42  // No actual side effects.
}
var someDictionary = ["a": [1, 2, 3], "b": [10, 20]]


someDictionary["not here"]?[0] = someFunctionWithSideEffects()
// someFunctionWithSideEffects isn't evaluated
// someDictionary is still ["a": [1, 2, 3], "b": [10, 20]]


someDictionary["a"]?[0] = someFunctionWithSideEffects()
// someFunctionWithSideEffects is evaluated and returns 42
// someDictionary is now ["a": [42, 2, 3], "b": [10, 20]]
```

可选链表达式的语法

_optional-chaining-expression_ → _postfix-expression_ **`?`**
