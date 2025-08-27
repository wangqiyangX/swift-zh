# 基本运算符

> 执行赋值、算术和比较等操作。

运算符是您用来检查、改变或组合值的特殊符号或短语。例如，加法运算符 ( `+` ) 用于两个数字相加，如 `let i = 1 + 2` ，而逻辑与运算符 ( `&&` ) 将两个布尔值组合在一起，如 `if enteredDoorCode && passedRetinaScan` 。

Swift 支持您可能已经知道的来自 C 等语言的运算符，并改善了几项功能，以消除常见的编码错误。赋值运算符 ( `=` ) 不返回值，以防在意图使用等号运算符 ( `==` ) 时误用。算术运算符 ( `+` , `-` , `*` , `/` , `%` 等) 检测并禁止值溢出，以避免在处理超出其存储类型的允许值范围的数字时出现意外结果。您可以通过使用 Swift 的溢出运算符来选择开启值溢出行为，如溢出运算符中所述。

Swift 还提供了 C 中没有的范围运算符，例如 `a..<b` 和 `a...b` ，作为表达值范围的快捷方式。

本章描述了 Swift 中的常见运算符。高级运算符涵盖了 Swift 的高级运算符，并描述了如何定义您自己的自定义运算符以及如何为您自己的自定义类型实现标准运算符。

## [术语](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Terminology)

运算符是单目、双目或三目：

- 单目运算符作用于单个目标（例如 `-a` ）。单目前缀运算符出现在其目标之前（例如 `!b` ），而单目后缀运算符出现在其目标之后（例如 `c!` ）。
- 双目运算符作用于两个目标（例如 `2 + 3` ），并且是中缀，因为它们出现在两个目标之间。
- 三目运算符作用于三个目标。与 C 语言一样，Swift 只有一个三目运算符，即三目条件运算符（ `a ? b : c` ）。

操作符影响的值是操作数。在表达式 `1 + 2` 中， `+` 符号是一个中缀操作符，它的两个操作数是值 `1` 和 `2` 。

## [赋值操作符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Assignment-Operator)

赋值操作符 ( `a = b` ) 使用 `b` 的值初始化或更新 `a` 的值：

```swift
let b = 10
var a = 5
a = b
// a is now equal to 10
```

如果赋值的右侧是一个包含多个值的元组，它的元素可以一次被分解为多个常量或变量：

```swift
let (x, y) = (1, 2)
// x is equal to 1, and y is equal to 2
```

与 C 和 Objective-C 中的赋值运算符不同，Swift 中的赋值运算符本身不会返回一个值。以下语句无效：

```swift
if x = y {
    // This isn't valid, because x = y doesn't return a value.
}
```

此功能防止赋值运算符（ `=` ）在实际意图使用相等运算符（ `==` ）时被意外使用。通过使 `if x = y` 无效，Swift 帮助您避免代码中的这些类型的错误。

## [算术运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Arithmetic-Operators)

Swift 支持所有数字类型的四个标准算术运算符：

- 加法 ( `+` )
- 减法 ( `-` )
- 乘法 ( `*` )
- 除法 ( `/` )

```swift
1 + 2       // equals 3
5 - 3       // equals 2
2 * 3       // equals 6
10.0 / 2.5  // equals 4.0
```

与 C 和 Objective-C 中的算术运算符不同，Swift 的算术运算符默认不允许值溢出。您可以通过使用 Swift 的溢出运算符（例如 `a &+ b` ）选择启用值溢出行为。请参阅溢出运算符。

也支持用于 `String` 连接的加法运算符：

```swift
"hello, " + "world"  // equals "hello, world"
```

### [余数运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Remainder-Operator)

余数运算符（ `a % b` ）计算 `b` 的多少倍可以放入 `a` 中，并返回剩余的值（称为余数）。

> 注意
>
> 余数运算符 ( `%` ) 在其他语言中也被称为模运算符。然而，它在 Swift 中对负数的行为意味着，严格来说，它是一个余数而不是模运算。

这里是余数运算符的工作原理。要计算 `9 % 4` ，您首先要计算有多少个 `4` 可以放入 `9` 中：

![remainderInteger](https://docs.swift.org/swift-book/images/org.swift.tspl/remainderInteger@2x.png){.light-only}
![remainderInteger~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/remainderInteger~dark@2x.png){.dark-only}

您可以在 `9` 中放入两个 `4` ，余数是 `1` （以橙色显示）。

在 Swift 中，这将写成：

```swift
9 % 4    // equals 1
```

要确定 `a % b` 的答案， `%` 运算符计算以下方程并返回 `remainder` 作为其输出：

`a` = (`b` x `some multiplier`) + `remainder`

其中 `some multiplier` 是可以放入 `a` 中的 `b` 的最大倍数。

将 `9` 和 `4` 插入此方程得出：

`9` = (`4` x `2`) + `1`

计算负值 `a` 的余数时采用相同的方法：

```swift
-9 % 4   // equals -1
```

将 `-9` 和 `4` 代入方程得到：

`-9` = (`4` x `-2`) + `-1`

得到余数值 `-1` 。

对于 `b` 的负值， `b` 的符号被忽略。这意味着 `a % b` 和 `a % -b` 总是给出相同的答案。

### [一元负号运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Unary-Minus-Operator)

数值的符号可以使用前缀 `-` 切换，称为一元负号运算符：

```swift
let three = 3
let minusThree = -three       // minusThree equals -3
let plusThree = -minusThree   // plusThree equals 3, or "minus minus three"
```

一元负号运算符 ( `-` ) 直接放在它所操作的值之前，没有任何空格。

### [一元加法运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Unary-Plus-Operator)

一元加法运算符 ( `+` ) 简单地返回它操作的值，而没有任何变化：

```swift
let minusSix = -6
let alsoMinusSix = +minusSix  // alsoMinusSix equals -6
```

虽然一元加法运算符实际上并不执行任何操作，但您可以使用它为正数提供代码的对称性，同时也为负数使用一元减法运算符。

## [复合赋值运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Compound-Assignment-Operators)

像 C 一样，Swift 提供了复合赋值运算符，它将赋值 ( `=` ) 与其他操作结合在一起。其中一个例子是加法赋值运算符 ( `+=` )：

```swift
var a = 1
a += 2
// a is now equal to 3
```

表达式 `a += 2` 是 `a = a + 2` 的简写。实际上，添加和赋值被合并为一个操作符，同时执行这两个任务。

Note

The compound assignment operators don’t return a value. For example, you can’t write `let b = a += 2`.

有关 Swift 标准库提供的运算符的信息，请参见运算符声明。

## [比较运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Comparison-Operators)

Swift 支持以下比较运算符：

- 等于 ( `a == b` )
- 不等于 ( `a != b` )
- 大于 ( `a > b` )
- 小于 ( `a < b` )
- 大于或等于 ( `a >= b` )
- 小于或等于 ( `a <= b` )

Note

Swift 还提供了两个身份运算符 ( `===` 和 `!==` )，用于测试两个对象引用是否都引用同一个对象实例。有关更多信息，请参阅身份运算符。

每个比较运算符返回一个 `Bool` 值，以指示语句是否为真：

```swift
1 == 1   // true because 1 is equal to 1
2 != 1   // true because 2 isn't equal to 1
2 > 1    // true because 2 is greater than 1
1 < 2    // true because 1 is less than 2
1 >= 1   // true because 1 is greater than or equal to 1
2 <= 1   // false because 2 isn't less than or equal to 1
```

比较运算符常用于条件语句，例如 `if` 语句：

```swift
let name = "world"
if name == "world" {
    print("hello, world")
} else {
    print("I'm sorry \(name), but I don't recognize you")
}
// Prints "hello, world", because name is indeed equal to "world".
```

有关 `if` 语句的更多信息，请参阅控制流。

您可以比较两个元组，如果它们具有相同的类型和相同数量的值。元组是从左到右逐个值进行比较，直到比较找到两个不相等的值。这两个值被比较，比较的结果决定了元组比较的整体结果。如果所有元素都相等，则元组本身也相等。例如：

```swift
(1, "zebra") < (2, "apple")   // true because 1 is less than 2; "zebra" and "apple" aren't compared
(3, "apple") < (3, "bird")    // true because 3 is equal to 3, and "apple" is less than "bird"
(4, "dog") == (4, "dog")      // true because 4 is equal to 4, and "dog" is equal to "dog"
```

在上面的示例中，您可以看到第一行的从左到右的比较行为。因为 `1` 小于 `2` ，所以 `(1, "zebra")` 被认为小于 `(2, "apple")` ，无论元组中的其他值如何。 `"zebra"` 不小于 `"apple"` 并不重要，因为比较已经由元组的第一个元素决定。然而，当元组的第一个元素相同时，它们的第二个元素会被比较——这就是第二行和第三行发生的事情。

只有在运算符可以应用于各自元组中的每个值时，元组才能使用给定的运算符进行比较。例如，如下面的代码所示，您可以比较两个类型为 `(String, Int)` 的元组，因为 `String` 和 `Int` 的值都可以使用 `<` 运算符进行比较。相比之下，两个类型为 `(String, Bool)` 的元组不能使用 `<` 运算符进行比较，因为 `<` 运算符不能应用于 `Bool` 值。

```swift
("blue", -1) < ("purple", 1)        // OK, evaluates to true
("blue", false) < ("purple", true)  // Error because < can't compare Boolean values
```

Note

Swift 标准库包含用于少于七个元素的元组的元组比较运算符。要比较具有七个或更多元素的元组，您必须自己实现比较运算符。

## [三元条件运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Ternary-Conditional-Operator)

三元条件运算符是一个由三部分组成的特殊运算符，形式为 `question ? answer1 : answer2` 。它是根据 `question` 是否为真来评估两个表达式之一的快捷方式。如果 `question` 为真，它会评估 `answer1` 并返回其值；否则，它会评估 `answer2` 并返回其值。

三元条件运算符是下面代码的简写：

```swift
if question {
    answer1
} else {
    answer2
}
```

这是一个示例，它计算表行的高度。如果行有标题，则行高应比内容高度高 50 点；如果行没有标题，则高 20 点：

```swift
let contentHeight = 40
let hasHeader = true
let rowHeight = contentHeight + (hasHeader ? 50 : 20)
// rowHeight is equal to 90
```

上面的示例是下面代码的简写：

```swift
let contentHeight = 40
let hasHeader = true
let rowHeight: Int
if hasHeader {
    rowHeight = contentHeight + 50
} else {
    rowHeight = contentHeight + 20
}
// rowHeight is equal to 90
```

第一个示例使用三元条件运算符意味着 `rowHeight` 可以在一行代码中设置为正确的值，这比第二个示例中使用的代码更简洁。

三元条件运算符提供了一种高效的简写方式来决定考虑哪两个表达式。然而，使用三元条件运算符时要小心。它的简洁性如果过度使用可能导致难以阅读的代码。避免将多个三元条件运算符组合成一个复合语句。

## [空合并运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Nil-Coalescing-Operator)

空合并运算符 ( `a ?? b` ) 解包一个可选的 `a` 如果它包含一个值，或者返回一个默认值 `b` 如果 `a` 是 `nil` 。表达式 `a` 始终是一个可选类型。表达式 `b` 必须与存储在 `a` 中的类型匹配。

空合并运算符是下面代码的简写：

```swift
a != nil ? a! : b
```

上面的代码使用三元条件运算符和强制解包 ( `a!` ) 来访问当 `a` 不是 `nil` 时包裹在 `a` 内的值，并在其他情况下返回 `b` 。nil 合并运算符提供了一种更优雅的方式，以简洁和可读的形式封装这种条件检查和解包。

Note

如果 `a` 的值为非 `nil` ，则 `b` 的值不会被评估。这被称为短路评估。

下面的示例使用 nil 合并运算符在默认颜色名称和可选用户定义颜色名称之间进行选择：

```swift
let defaultColorName = "red"
var userDefinedColorName: String?   // defaults to nil


var colorNameToUse = userDefinedColorName ?? defaultColorName
// userDefinedColorName is nil, so colorNameToUse is set to the default of "red"
```

`userDefinedColorName` 变量被定义为可选的 `String` ，默认值为 `nil` 。因为 `userDefinedColorName` 是可选类型，所以可以使用 nil 合并运算符来考虑它的值。在上面的示例中，该运算符用于确定名为 `colorNameToUse` 的 `String` 变量的初始值。因为 `userDefinedColorName` 是 `nil` ，表达式 `userDefinedColorName ?? defaultColorName` 返回 `defaultColorName` 的值，或者 `"red"` 。

如果您将一个非 `nil` 值赋给 `userDefinedColorName` 并再次执行 nil 合并运算符检查，则使用包裹在 `userDefinedColorName` 内的值，而不是默认值：

```swift
userDefinedColorName = "green"
colorNameToUse = userDefinedColorName ?? defaultColorName
// userDefinedColorName isn't nil, so colorNameToUse is set to "green"
```

## [范围运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Range-Operators)

Swift 包含几个范围运算符，这是表达值范围的快捷方式。

### [闭区间运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Closed-Range-Operator)

闭区间运算符 ( `a...b` ) 定义一个从 `a` 到 `b` 的范围，包括值 `a` 和 `b` 。 `a` 的值不得大于 `b` 。

闭合范围运算符在迭代需要使用所有值的范围时非常有用，例如使用 `for` - `in` 循环：

```swift
for index in 1...5 {
    print("\(index) times 5 is \(index * 5)")
}
// 1 times 5 is 5
// 2 times 5 is 10
// 3 times 5 is 15
// 4 times 5 is 20
// 5 times 5 is 25
```

有关 `for` - `in` 循环的更多信息，请参见控制流。

### [半开区间运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Half-Open-Range-Operator)

半开区间运算符 ( `a..<b` ) 定义了一个从 `a` 到 `b` 的区间，但不包括 `b` 。它被称为半开，因为它包含第一个值，但不包含最后一个值。与封闭区间运算符一样， `a` 的值不得大于 `b` 。如果 `a` 的值等于 `b` ，则结果区间将是空的。

半开区间在处理基于零的列表（例如数组）时特别有用，在这种情况下，计算到（但不包括）列表的长度是有用的：

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
let count = names.count
for i in 0..<count {
    print("Person \(i + 1) is called \(names[i])")
}
// Person 1 is called Anna
// Person 2 is called Alex
// Person 3 is called Brian
// Person 4 is called Jack
```

注意，该数组包含四个项目，但 `0..<count` 仅计算到 `3` （数组中最后一个项目的索引），因为它是一个半开区间。有关数组的更多信息，请参见 Arrays。

### [单侧范围](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#One-Sided-Ranges)

闭合范围操作符有一种替代形式，用于在一个方向上尽可能继续的范围——例如，一个范围包括数组中从索引 2 到数组末尾的所有元素。在这些情况下，您可以省略范围操作符一侧的值。这种范围称为单侧范围，因为操作符只在一侧有值。例如：

```swift
for name in names[2...] {
    print(name)
}
// Brian
// Jack


for name in names[...2] {
    print(name)
}
// Anna
// Alex
// Brian
```

半开区间运算符也有一种单侧形式，只需写出其最终值。就像在两侧都包含一个值时，最终值不属于该范围一样。例如：

```swift
for name in names[..<2] {
    print(name)
}
// Anna
// Alex
```

单侧范围可以在其他上下文中使用，而不仅仅是在下标中。您不能迭代省略第一个值的单侧范围，因为不清楚迭代应该从哪里开始。您可以迭代省略最后一个值的单侧范围；然而，由于该范围无限延续，请确保为循环添加明确的结束条件。您还可以检查单侧范围是否包含特定值，如下面的代码所示。

```swift
let range = ...5
range.contains(7)   // false
range.contains(4)   // true
range.contains(-1)  // true
```

## [逻辑运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Logical-Operators)

逻辑运算符修改或组合布尔逻辑值 `true` 和 `false` 。 Swift 支持 C 语言系中找到的三种标准逻辑运算符：

- 逻辑非 ( `!a` )
- 逻辑与 ( `a && b` )
- 逻辑或 ( `a || b` )

### [逻辑非运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Logical-NOT-Operator)

逻辑非运算符 ( `!a` ) 反转布尔值，使得 `true` 变为 `false` ，并且 `false` 变为 `true` 。

逻辑非运算符是一个前缀运算符，出现在它作用的值之前，没有任何空格。可以读作“非 `a` ”，如下例所示：

```swift
let allowedEntry = false
if !allowedEntry {
    print("ACCESS DENIED")
}
// Prints "ACCESS DENIED"
```

短语 `if !allowedEntry` 可以解释为 “如果不允许进入”。后续行仅在 “不允许进入” 为真时执行；也就是说，如果 `allowedEntry` 是 `false` 。

如本示例所示，谨慎选择布尔常量和变量名称可以帮助保持代码的可读性和简洁性，同时避免双重否定或令人困惑的逻辑语句。

### [逻辑与运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Logical-AND-Operator)

逻辑与运算符 ( `a && b` ) 创建逻辑表达式，其中两个值都必须为 `true` ，整体表达式才能为 `true` 。

如果任一值为 `false` ，则整体表达式也将为 `false` 。事实上，如果第一个值为 `false` ，则第二个值甚至不会被评估，因为它不可能使整体表达式等于 `true` 。这被称为短路求值。

此示例考虑了两个 `Bool` 值，并仅在两个值都为 `true` 时允许访问：

```swift
let enteredDoorCode = true
let passedRetinaScan = false
if enteredDoorCode && passedRetinaScan {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "ACCESS DENIED"
```

### [逻辑或运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Logical-OR-Operator)

逻辑或运算符 ( `a || b` ) 是由两个相邻的管道字符组成的中缀运算符。您可以使用它来创建逻辑表达式，其中只有两个值中的一个必须为 `true` ，以使整体表达式为 `true` 。

与上面的逻辑与运算符一样，逻辑或运算符使用短路求值来考虑其表达式。如果逻辑或表达式的左侧为 `true` ，则右侧不会被评估，因为它无法改变整体表达式的结果。

在下面的示例中，第一个 `Bool` 值 ( `hasDoorKey` ) 是 `false` ，但第二个值 ( `knowsOverridePassword` ) 是 `true` 。因为一个值是 `true` ，所以整体表达式也评估为 `true` ，并且允许访问：

```swift
let hasDoorKey = false
let knowsOverridePassword = true
if hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

### [组合逻辑运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Combining-Logical-Operators)

您可以组合多个逻辑运算符以创建更长的复合表达式：

```swift
if enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

此示例使用多个 `&&` 和 `||` 运算符来创建更长的复合表达式。然而， `&&` 和 `||` 运算符仍然仅对两个值进行操作，因此这实际上是三个较小的表达式连接在一起。该示例可以理解为：

如果我们输入了正确的门禁密码并通过了视网膜扫描，或者我们有有效的门钥匙，或者我们知道紧急覆盖密码，则允许访问。

根据 `enteredDoorCode` 、 `passedRetinaScan` 和 `hasDoorKey` 的值，前两个子表达式为 `false` 。然而，紧急覆盖密码是已知的，因此整体复合表达式仍然计算为 `true` 。

Note

Swift 逻辑运算符 `&&` 和 `||` 是左结合的，这意味着具有多个逻辑运算符的复合表达式首先计算最左边的子表达式。

### [显式括号](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/basicoperators#Explicit-Parentheses)

在不严格需要括号时，有时包括括号是有用的，以使复杂表达式的意图更容易理解。在上述门访问示例中，给复合表达式的第一部分添加括号是有用的，以使其意图明确：

```swift
if (enteredDoorCode && passedRetinaScan) || hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

括号清晰地表明前两个值被视为整体逻辑中一个单独可能状态的一部分。复合表达式的输出没有变化，但整体意图对读者来说更加清晰。可读性始终优于简洁；在有助于明确意图的地方使用括号。
