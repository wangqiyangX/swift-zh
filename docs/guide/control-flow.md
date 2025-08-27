# 控制流

> 使用分支、循环和提前退出来结构化代码。

Swift 提供了多种控制流语句。这些包括 `while` 循环以多次执行任务； `if` 、 `guard` 和 `switch` 语句根据特定条件执行不同的代码分支；以及像 `break` 和 `continue` 这样的语句将执行流转移到代码中的另一个点。Swift 提供了一个 `for` - `in` 循环，使得遍历数组、字典、范围、字符串和其他序列变得简单。Swift 还提供了 `defer` 语句，这些语句在离开当前作用域时包装要执行的代码。

Swift 的 `switch` 语句比许多 C 类语言中的对应语句强大得多。案例可以匹配许多不同的模式，包括区间匹配、元组和转换为特定类型。在 `switch` 案例中匹配的值可以绑定到临时常量或变量，以便在案例的主体中使用，并且可以通过每个案例的 `where` 子句表达复杂的匹配条件。

## [For-In 循环](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#For-In-Loops)

您使用 `for` - `in` 循环遍历序列，例如数组中的项目、数字范围或字符串中的字符。

此示例使用一个 `for` - `in` 循环来迭代数组中的项目：

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
for name in names {
    print("Hello, \(name)!")
}
// Hello, Anna!
// Hello, Alex!
// Hello, Brian!
// Hello, Jack!
```

您还可以迭代字典以访问其键值对。字典中的每个项目在迭代字典时作为 `(key, value)` 元组返回，您可以将 `(key, value)` 元组的成员分解为显式命名的常量，以便在 `for` - `in` 循环的主体中使用。在下面的代码示例中，字典的键被分解为一个名为 `animalName` 的常量，字典的值被分解为一个名为 `legCount` 的常量。

```swift
let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
for (animalName, legCount) in numberOfLegs {
    print("\(animalName)s have \(legCount) legs")
}
// cats have 4 legs
// ants have 6 legs
// spiders have 8 legs
```

`Dictionary` 的内容本质上是无序的，迭代它们并不保证它们将被检索的顺序。特别是，您向 `Dictionary` 中插入项目的顺序并不定义它们被迭代的顺序。有关数组和字典的更多信息，请参阅集合类型。

您还可以使用 `for` - `in` 循环与数字范围。此示例打印五倍数表中的前几个条目：

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

被迭代的序列是从 `1` 到 `5` 的数字范围，包括在内，如使用闭区间运算符 ( `...` ) 所示。 `index` 的值被设置为范围内的第一个数字 ( `1` )，并执行循环内的语句。在这种情况下，循环只包含一个语句，该语句打印当前值 `index` 的五倍数表中的一个条目。语句执行后， `index` 的值被更新为范围内的第二个值 ( `2` )，并再次调用 `print(_:separator:terminator:)` 函数。这个过程持续进行，直到达到范围的末尾。

在上面的例子中， `index` 是一个常量，其值在每次循环迭代开始时自动设置。因此， `index` 不必在使用之前声明。它通过在循环声明中的包含隐式声明，而无需 `let` 声明关键字。

如果您不需要序列中的每个值，可以通过使用下划线代替变量名来忽略这些值。

```swift
let base = 3
let power = 10
var answer = 1
for _ in 1...power {
    answer *= base
}
print("\(base) to the power of \(power) is \(answer)")
// Prints "3 to the power of 10 is 59049"
```

上述示例计算一个数字的幂（在本例中， `3` 的 `10` 次幂）。它将一个起始值 `1` （也就是 `3` 的 `0` 次幂）乘以 `3` ，十次，使用一个闭合范围，从 `1` 开始，到 `10` 结束。对于此计算，每次循环中的单独计数值并不必要——代码只是以正确的次数执行循环。代替循环变量使用的下划线字符（ `_` ）导致单独的值被忽略，并且在每次循环迭代期间无法访问当前值。

在某些情况下，您可能不希望使用包括两个端点的闭合范围。考虑在钟表表面绘制每分钟的刻度线。您想要绘制 `60` 个刻度线，从 `0` 分钟开始。使用半开区间运算符（ `..<` ）来包括下界，而不包括上界。有关范围的更多信息，请参阅范围运算符。

```swift
let minutes = 60
for tickMark in 0..<minutes {
    // render the tick mark each minute (60 times)
}
```

一些用户可能希望在其 UI 中更少的刻度线。他们可能更喜欢每 `5` 分钟一个刻度线。使用 `stride(from:to:by:)` 函数来跳过不需要的刻度线。

```swift
let minuteInterval = 5
for tickMark in stride(from: 0, to: minutes, by: minuteInterval) {
    // render the tick mark every 5 minutes (0, 5, 10, 15 ... 45, 50, 55)
}
```

闭合范围也可以使用 `stride(from:through:by:)` 来实现：

```swift
let hours = 12
let hourInterval = 3
for tickMark in stride(from: 3, through: hours, by: hourInterval) {
    // render the tick mark every 3 hours (3, 6, 9, 12)
}
```

上面的示例使用 `for` - `in` 循环来迭代范围、数组、字典和字符串。然而，您可以使用此语法来迭代任何集合，包括您自己的类和集合类型，只要这些类型符合 `Sequence` 协议。

## [while 循环](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#While-Loops)

一个 `while` 循环执行一组语句，直到条件变为 `false` 。这种类型的循环最适合在第一次迭代开始之前不知道迭代次数的情况下使用。Swift 提供了两种类型的 `while` 循环：

- `while` 在每次循环开始时评估其条件。
- `repeat` - `while` 在每次循环结束时评估其条件。

### [while](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#While)

一个 `while` 循环通过评估一个单一条件开始。如果条件是 `true` ，则一组语句会重复执行，直到条件变为 `false` 。

这里是 `while` 循环的一般形式：

```swift
while <#condition#> {
   <#statements#>
}
```

这个例子玩一个简单的蛇梯游戏（也称为滑梯和梯子）：

![snakesAndLadders](https://docs.swift.org/swift-book/images/org.swift.tspl/snakesAndLadders@2x.png){.light-only}
![snakesAndLadders~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/snakesAndLadders~dark@2x.png){.dark-only}

游戏规则如下：

- 棋盘有 25 个方格，目标是在方格 25 上或超出方格 25。
- 玩家的起始方格是“零方格”，位于棋盘左下角外侧。
- 每次轮到您时，您掷一个六面骰子，并按照上方虚线箭头指示的水平路径移动相应的格数。
- 如果您的回合结束在梯子的底部，您就向上移动那根梯子。
- 如果您的回合结束在蛇的头部，您就向下移动那条蛇。

游戏板由一个包含 `Int` 值的数组表示。它的大小基于一个称为 `finalSquare` 的常量，该常量用于初始化数组，并在示例中稍后检查胜利条件。由于玩家从棋盘外开始，在“零格”上，棋盘初始化为 26 个零 `Int` 值，而不是 25。

```swift
let finalSquare = 25
var board = [Int](repeating: 0, count: finalSquare + 1)
```

一些方格被设置为具有更具体的蛇和梯子的值。带有梯子底部的方格有一个正数来让您向上移动棋盘，而带有蛇头的方格有一个负数来让您向下移动棋盘。

```swift
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
```

正方形 3 包含一个梯子的底部，该梯子将您移动到正方形 11。为了表示这一点， `board[03]` 等于 `+08` ，这相当于 `8` 的整数值（ `3` 与 `11` 之间的差）。为了对齐值和语句，明确使用了一元加法运算符（ `+i` ）与一元减法运算符（ `-i` ），并且小于 `10` 的数字用零填充。（这两种风格化技巧并不是严格必要的，但它们会导致更整洁的代码。）

```swift
var square = 0
var diceRoll = 0
while square < finalSquare {
    // roll the dice
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
    if square < board.count {
        // if we're still on the board, move up or down for a snake or a ladder
        square += board[square]
    }
}
print("Game over!")
```

上面的例子使用了一种非常简单的掷骰子方法。它不是生成一个随机数，而是从 `diceRoll` 值为 `0` 开始。每次通过 `while` 循环时， `diceRoll` 增加一，并检查其是否变得过大。每当这个返回值等于 `7` 时，骰子的结果变得过大并被重置为 `1` 的值。结果是一个始终为 `diceRoll` 的值序列， `1` ， `2` ， `3` ， `4` ， `5` ， `6` ， `1` ， `2` ，等等。

掷骰子后，玩家向前移动 `diceRoll` 格。骰子的结果可能使玩家超出第 25 格，这种情况下游戏结束。为了应对这种情况，代码检查 `square` 是否小于 `board` 数组的 `count` 属性。如果 `square` 有效，则将存储在 `board[square]` 中的值加到当前 `square` 值，以使玩家上下移动任何梯子或蛇。

> 注意
>
> 如果没有执行此检查， `board[square]` 可能会尝试访问 `board` 数组边界之外的值，这会导致运行时错误。

当前的 `while` 循环执行结束，然后检查循环的条件以确定是否应该再次执行循环。如果玩家已经移动到或超过方块编号 `25` ，则循环的条件评估为 `false` ，游戏结束。

在这种情况下， `while` 循环是合适的，因为游戏的长度在 `while` 循环开始时并不明确。相反，循环会执行直到满足特定条件。

### [重复-直到](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Repeat-While)

`while` 循环的另一种变体，称为 `repeat` - `while` 循环，首先对循环块进行一次遍历，然后再考虑循环的条件。然后，它继续重复循环直到条件为 `false` 。

Note

The `repeat`-`while` loop in Swift is analogous to a `do`-`while` loop in other languages.

这是 `repeat` - `while` 循环的一般形式：

```swift
repeat {
   <#statements#>
} while <#condition#>
```

这是蛇梯游戏的示例，再次以 `repeat` - `while` 循环的形式编写，而不是 `while` 循环。 `finalSquare` 、 `board` 、 `square` 和 `diceRoll` 的值以与 `while` 循环完全相同的方式初始化。

```swift
let finalSquare = 25
var board = [Int](repeating: 0, count: finalSquare + 1)
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
var square = 0
var diceRoll = 0
```

在这个版本的游戏中，循环中的第一个动作是检查是否有梯子或蛇。棋盘上没有梯子会直接将玩家带到第 25 格，因此不可能通过爬梯子赢得游戏。因此，作为循环中的第一个动作，检查蛇或梯子是安全的。

在游戏开始时，玩家位于“零号方格”。 `board[0]` 总是等于 `0` 并且没有影响。

```swift
repeat {
    // move up or down for a snake or ladder
    square += board[square]
    // roll the dice
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
} while square < finalSquare
print("Game over!")
```

在代码检查蛇和梯子后，掷骰子并将玩家向前移动 `diceRoll` 个方格。当前循环执行然后结束。

循环的条件 ( `while square < finalSquare` ) 与之前相同，但这次在第一次通过循环结束之前不会进行评估。 `repeat` - `while` 循环的结构比之前示例中的 `while` 循环更适合这个游戏。在上面的 `repeat` - `while` 循环中， `square += board[square]` 总是在循环的 `while` 条件确认 `square` 仍在棋盘上后立即执行。这种行为消除了在之前描述的游戏的 `while` 循环版本中看到的数组边界检查的需要。

## [条件语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Conditional-Statements)

根据某些条件执行不同代码片段通常是有用的。当发生错误时，您可能想要运行额外的代码，或者在值变得过高或过低时显示消息。为此，您需要使代码的某些部分具有条件性。

Swift 提供了两种将条件分支添加到代码中的方法： `if` 语句和 `switch` 语句。通常，您使用 `if` 语句来评估只有少数可能结果的简单条件。 `switch` 语句更适合具有多个可能排列的复杂条件，并且在模式匹配可以帮助选择要执行的适当代码分支的情况下非常有用。

### [如果](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#If)

在其最简单的形式中， `if` 语句具有单个 `if` 条件。仅当该条件为 `true` 时，它才会执行一组语句。

```swift
var temperatureInFahrenheit = 30
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
}
// Prints "It's very cold. Consider wearing a scarf."
```

上述示例检查温度是否小于或等于 32 华氏度（冰点）。如果是，则会打印消息。否则，不会打印消息，代码执行将在 `if` 语句的闭合大括号后继续。

`if` 语句可以提供一组替代语句，称为 else 子句，用于 `if` 条件为 `false` 的情况。这些语句由 `else` 关键字指示。

```swift
temperatureInFahrenheit = 40
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else {
    print("It's not that cold. Wear a T-shirt.")
}
// Prints "It's not that cold. Wear a T-shirt."
```

这两个分支中的一个总是会被执行。由于温度已上升到 `40` 华氏度，已经不再足够冷以建议佩戴围巾，因此触发了 `else` 分支。

您可以将多个 `if` 语句链在一起以考虑额外的子句。

```swift
temperatureInFahrenheit = 90
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
} else {
    print("It's not that cold. Wear a T-shirt.")
}
// Prints "It's really warm. Don't forget to wear sunscreen."
```

在这里，添加了一个额外的 `if` 语句以应对特别高的温度。最终的 `else` 子句仍然存在，它会对任何不太热或不太冷的温度打印响应。

最后一条 `else` 子句是可选的，如果条件集不需要完整，可以省略。

```swift
temperatureInFahrenheit = 72
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
}
```

因为温度不够低，无法触发 `if` 条件，也不够高，无法触发 `else if` 条件，因此没有信息被打印。

Swift 提供了 `if` 的简写形式，您可以在设置值时使用它。例如，考虑以下代码：

```swift
let temperatureInCelsius = 25
let weatherAdvice: String


if temperatureInCelsius <= 0 {
    weatherAdvice = "It's very cold. Consider wearing a scarf."
} else if temperatureInCelsius >= 30 {
    weatherAdvice = "It's really warm. Don't forget to wear sunscreen."
} else {
    weatherAdvice = "It's not that cold. Wear a T-shirt."
}


print(weatherAdvice)
// Prints "It's not that cold. Wear a T-shirt."
```

在这里，每个分支为 `weatherAdvice` 常量设置一个值，该值在 `if` 语句之后打印。

使用称为 `if` 表达式的替代语法，您可以更简洁地编写此代码：

```swift
let weatherAdvice = if temperatureInCelsius <= 0 {
    "It's very cold. Consider wearing a scarf."
} else if temperatureInCelsius >= 30 {
    "It's really warm. Don't forget to wear sunscreen."
} else {
    "It's not that cold. Wear a T-shirt."
}


print(weatherAdvice)
// Prints "It's not that cold. Wear a T-shirt."
```

在这个 `if` 表达式版本中，每个分支包含一个单一的值。如果一个分支的条件为真，则该分支的值将作为整个 `if` 表达式在 `weatherAdvice` 的赋值中使用。每个 `if` 分支都有一个对应的 `else if` 分支或 `else` 分支，确保其中一个分支始终匹配，并且 `if` 表达式始终产生一个值，无论哪些条件为真。

因为赋值的语法在 `if` 表达式外部开始，所以在每个分支内部不需要重复 `weatherAdvice =` 。相反， `if` 表达式的每个分支产生 `weatherAdvice` 的三个可能值之一，赋值使用该值。

一个 `if` 表达式的所有分支需要包含相同类型的值。因为 Swift 分别检查每个分支的类型，像 `nil` 这样的值可以与多种类型一起使用，阻止 Swift 自动确定 `if` 表达式的类型。相反，您需要显式指定类型 — 例如：

```swift
let freezeWarning: String? = if temperatureInCelsius <= 0 {
    "It's below freezing. Watch for ice!"
} else {
    nil
}
```

在上面的代码中， `if` 表达式的一个分支具有字符串值，另一个分支具有 `nil` 值。 `nil` 值可以用作任何可选类型的值，因此您必须明确写出 `freezeWarning` 是一个可选字符串，如类型注释中所述。

提供此类型信息的另一种方法是为 `nil` 提供显式类型，而不是为 `freezeWarning` 提供显式类型：

```swift
let freezeWarning = if temperatureInCelsius <= 0 {
    "It's below freezing. Watch for ice!"
} else {
    nil as String?
}
```

一个 `if` 表达式可以通过抛出错误或调用一个像 `fatalError(_:file:line:)` 这样的永不返回的函数来响应意外失败。例如：

```swift
let weatherAdvice = if temperatureInCelsius > 100 {
    throw TemperatureError.boiling
} else {
    "It's a reasonable temperature."
}
```

在这个例子中， `if` 表达式检查预测温度是否高于 100° C — 水的沸点。如此高的温度会导致 `if` 表达式抛出 `.boiling` 错误，而不是返回文本摘要。尽管这个 `if` 表达式可以抛出错误，但您不会在它之前写 `try` 。有关处理错误的信息，请参见错误处理。

除了在赋值的右侧使用 `if` 表达式，如上述示例所示，您还可以将它们用作函数或闭包返回的值。

### [切换](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Switch)

一个 `switch` 语句考虑一个值并将其与几个可能的匹配模式进行比较。然后，它根据第一个成功匹配的模式执行相应的代码块。一个 `switch` 语句为响应多个潜在状态提供了对 `if` 语句的替代方案。

在最简单的形式中，一个 `switch` 语句将一个值与一个或多个相同类型的值进行比较。

```swift
switch <#some value to consider#> {
case <#value 1#>:
    <#respond to value 1#>
case <#value 2#>,
    <#value 3#>:
    <#respond to value 2 or 3#>
default:
    <#otherwise, do something else#>
}
```

每个 `switch` 语句由多个可能的情况组成，每个情况都以 `case` 关键字开头。除了与特定值进行比较外，Swift 还提供了几种方法让每个情况指定更复杂的匹配模式。这些选项将在本章后面进行描述。

像 `if` 语句的主体一样，每个 `case` 是一个独立的代码执行分支。 `switch` 语句决定应该选择哪个分支。这个过程称为根据正在考虑的值进行切换。

每个 `switch` 语句必须是详尽的。也就是说，被考虑的类型的每个可能值必须由一个 `switch` 案例匹配。如果没有必要为每个可能值提供一个案例，您可以定义一个默认案例来覆盖任何未明确处理的值。这个默认案例由 `default` 关键字表示，并且必须总是出现在最后。

此示例使用 `switch` 语句来考虑一个名为 `someCharacter` 的小写字符：

```swift
let someCharacter: Character = "z"
switch someCharacter {
case "a":
    print("The first letter of the Latin alphabet")
case "z":
    print("The last letter of the Latin alphabet")
default:
    print("Some other character")
}
// Prints "The last letter of the Latin alphabet"
```

`switch` 语句的第一个情况匹配英文字母表中的第一个字母 `a` ，而它的第二个情况匹配最后一个字母 `z` 。因为 `switch` 必须为每个可能的字符都有一个情况，而不仅仅是每个字母字符，所以这个 `switch` 语句使用了一个 `default` 情况来匹配除 `a` 和 `z` 之外的所有字符。这个规定确保了 `switch` 语句是穷尽的。

像 `if` 语句一样， `switch` 语句也有一种表达形式：

```swift
let anotherCharacter: Character = "a"
let message = switch anotherCharacter {
case "a":
    "The first letter of the Latin alphabet"
case "z":
    "The last letter of the Latin alphabet"
default:
    "Some other character"
}


print(message)
// Prints "The first letter of the Latin alphabet"
```

在这个例子中， `switch` 表达式中的每一个案例包含了当该案例匹配 `anotherCharacter` 时要使用的 `message` 的值。因为 `switch` 总是穷尽的，总是有一个值可以赋值。

与 `if` 表达式一样，您可以抛出一个错误或调用一个像 `fatalError(_:file:line:)` 一样从不返回的函数，而不是为给定案例提供一个值。您可以在赋值的右侧使用 `switch` 表达式，如上面的示例所示，并作为函数或闭包返回的值。

[**不允许隐式贯穿**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#No-Implicit-Fallthrough)

与 C 和 Objective-C 中的 `switch` 语句相比，Swift 中的 `switch` 语句默认不会从每个 case 的底部继续执行到下一个 case。相反，整个 `switch` 语句在第一个匹配的 `switch` case 完成后立即结束执行，而不需要显式的 `break` 语句。这使得 `switch` 语句比 C 中的更安全且更易于使用，并避免错误地执行多个 `switch` case。

> 注意
>
> 虽然 `break` 在 Swift 中不是必需的，但您可以使用 `break` 语句来匹配并忽略特定的情况，或者在该情况完成执行之前跳出匹配的情况。有关详细信息，请参见 Switch 语句中的 Break。

每个 case 的主体必须包含至少一个可执行语句。写如下代码是无效的，因为第一个 case 是空的：

```swift
let anotherCharacter: Character = "a"
switch anotherCharacter {
case "a": // Invalid, the case has an empty body
case "A":
    print("The letter A")
default:
    print("Not the letter A")
}
// This will report a compile-time error.
```

与 C 中的 `switch` 语句不同，这个 `switch` 语句不匹配 `"a"` 和 `"A"` 。相反，它报告一个编译时错误，提示 `case "a":` 不包含任何可执行语句。这种方法避免了意外从一个案例掉落到另一个案例，并使得代码更安全，更清晰其意图。

要创建一个 `switch` ，使其与 `"a"` 和 `"A"` 都匹配，请将这两个值组合成一个复合案例，用逗号分隔这些值。

```swift
let anotherCharacter: Character = "a"
switch anotherCharacter {
case "a", "A":
    print("The letter A")
default:
    print("Not the letter A")
}
// Prints "The letter A"
```

为了提高可读性，复合案例也可以分成多行书写。有关复合案例的更多信息，请参见复合案例。

> 注意
>
> 要在特定的 `switch` case 结束时显式地 fall through，请使用 `fallthrough` 关键字，如 Fallthrough 中所述。

[**区间匹配**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Interval-Matching)

在 `switch` 案例中，可以检查其是否包含在一个区间内。这个例子使用数字区间为任意大小的数字提供自然语言计数：

```swift
let approximateCount = 62
let countedThings = "moons orbiting Saturn"
let naturalCount: String
switch approximateCount {
case 0:
    naturalCount = "no"
case 1..<5:
    naturalCount = "a few"
case 5..<12:
    naturalCount = "several"
case 12..<100:
    naturalCount = "dozens of"
case 100..<1000:
    naturalCount = "hundreds of"
default:
    naturalCount = "many"
}
print("There are \(naturalCount) \(countedThings).")
// Prints "There are dozens of moons orbiting Saturn."
```

在上面的例子中， `approximateCount` 在 `switch` 语句中被评估。每个 `case` 将该值与一个数字或区间进行比较。因为 `approximateCount` 的值在 12 和 100 之间， `naturalCount` 被赋值为 `"dozens of"` ，并且执行被转移出 `switch` 语句。

[**元组**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Tuples)

您可以使用元组在同一个 `switch` 语句中测试多个值。元组的每个元素可以与不同的值或值区间进行测试。或者，使用下划线字符 ( `_` )，也称为通配符模式，以匹配任何可能的值。

下面的示例取一个 (x, y) 点，表示为类型 `(Int, Int)` 的简单元组，并将其分类到随后的图表中。

```swift
let somePoint = (1, 1)
switch somePoint {
case (0, 0):
    print("\(somePoint) is at the origin")
case (_, 0):
    print("\(somePoint) is on the x-axis")
case (0, _):
    print("\(somePoint) is on the y-axis")
case (-2...2, -2...2):
    print("\(somePoint) is inside the box")
default:
    print("\(somePoint) is outside of the box")
}
// Prints "(1, 1) is inside the box"
```

![coordinateGraphSimple](https://docs.swift.org/swift-book/images/org.swift.tspl/coordinateGraphSimple@2x.png){.light-only}
![coordinateGraphSimple~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/coordinateGraphSimple~dark@2x.png){.dark-only}

`switch` 语句确定该点是否位于原点 (0, 0)、红色 x 轴上、绿色 y 轴上、位于以原点为中心的蓝色 4x4 方框内，或在方框外。

与 C 不同，Swift 允许多个 `switch` 情况来考虑相同的值或值。实际上，点 (0, 0) 可以匹配这个示例中的所有四个情况。但是，如果可能存在多个匹配，总是使用第一个匹配的情况。点 (0, 0) 首先会匹配 `case (0, 0)` ，因此所有其他匹配情况都会被忽略。

[**值绑定**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Value-Bindings)

一个 `switch` case 可以将其匹配的值或值绑定到临时常量或变量，以便在 case 的主体中使用。这种行为称为值绑定，因为这些值在 case 的主体中被绑定到临时常量或变量。

下面的示例采用一个 (x, y) 点，表示为类型 `(Int, Int)` 的元组，并将其分类到以下图表中：

```swift
let anotherPoint = (2, 0)
switch anotherPoint {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case (0, let y):
    print("on the y-axis with a y value of \(y)")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
// Prints "on the x-axis with an x value of 2"
```

![coordinateGraphMedium](https://docs.swift.org/swift-book/images/org.swift.tspl/coordinateGraphMedium@2x.png){.light-only}
![coordinateGraphMedium~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/coordinateGraphMedium~dark@2x.png){.dark-only}

`switch` 语句决定该点是否位于红色 x 轴上，绿色 y 轴上，或其他位置（均不在轴上）。

这三个 `switch` 案例声明了占位符常量 `x` 和 `y` ，它们暂时获取 `anotherPoint` 中的一个或两个元组值。第一个案例 `case (let x, 0)` 匹配任何 `y` 值为 `0` 的点，并将该点的 `x` 值赋给临时常量 `x` 。类似地，第二个案例 `case (0, let y)` 匹配任何 `x` 值为 `0` 的点，并将该点的 `y` 值赋给临时常量 `y` 。

在临时常量声明后，它们可以在 case 的代码块中使用。在这里，它们用于打印点的分类。

此 `switch` 语句没有 `default` 情况。最后的情况 `case let (x, y)` 声明了一个包含两个占位符常量的元组，可以匹配任何值。因为 `anotherPoint` 总是包含两个值的元组，所以此情况匹配所有剩余的可能值，而不需要 `default` 情况来使 `switch` 语句穷尽。

[**where**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Where)

A `switch` 案例可以使用一个 `where` 条款来检查额外条件。

下面的例子将 (x, y) 点分类到以下图表中：

```swift
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
case let (x, y) where x == y:
    print("(\(x), \(y)) is on the line x == y")
case let (x, y) where x == -y:
    print("(\(x), \(y)) is on the line x == -y")
case let (x, y):
    print("(\(x), \(y)) is just some arbitrary point")
}
// Prints "(1, -1) is on the line x == -y"
```

![coordinateGraphComplex](https://docs.swift.org/swift-book/images/org.swift.tspl/coordinateGraphComplex@2x.png){.light-only}
![coordinateGraphComplex~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/coordinateGraphComplex~dark@2x.png){.dark-only}

`switch` 语句确定该点是否位于绿色对角线 `x == y` 上，紫色对角线 `x == -y` 上，或两者都不是。

这三个 `switch` 案例声明了占位符常量 `x` 和 `y` ，它们暂时采用来自 `yetAnotherPoint` 的两个元组值。这些常量作为 `where` 子句的一部分，用于创建动态过滤器。只有当 `where` 子句的条件对该值的评估结果为 `true` 时， `switch` 案例才匹配 `point` 的当前值。

与前面的例子一样，最后的情况匹配所有可能的剩余值，因此不需要 `default` 情况来使 `switch` 语句详尽无遗。

[**复合情况**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Compound-Cases)

多个共享相同主体的 switch 情况可以通过在 `case` 后面写几个模式来组合，每个模式之间用逗号分隔。如果任何模式匹配，则该情况被视为匹配。如果列表很长，模式可以跨多行书写。例如：

```swift
let someCharacter: Character = "e"
switch someCharacter {
case "a", "e", "i", "o", "u":
    print("\(someCharacter) is a vowel")
case "b", "c", "d", "f", "g", "h", "j", "k", "l", "m",
    "n", "p", "q", "r", "s", "t", "v", "w", "x", "y", "z":
    print("\(someCharacter) is a consonant")
default:
    print("\(someCharacter) isn't a vowel or a consonant")
}
// Prints "e is a vowel"
```

`switch` 语句的第一个情况匹配英语中的所有五个小写元音。同样，它的第二个情况匹配所有小写英语辅音。最后， `default` 情况匹配任何其他字符。

复合案例还可以包含值绑定。复合案例的所有模式必须包含相同的一组值绑定，并且每个绑定必须从复合案例中的所有模式中获取相同类型的值。这确保了无论复合案例的哪个部分匹配，案例主体中的代码始终可以访问绑定的值，并且该值始终具有相同的类型。

```swift
let stillAnotherPoint = (9, 0)
switch stillAnotherPoint {
case (let distance, 0), (0, let distance):
    print("On an axis, \(distance) from the origin")
default:
    print("Not on an axis")
}
// Prints "On an axis, 9 from the origin"
```

上面的 `case` 有两种模式： `(let distance, 0)` 匹配 x 轴上的点， `(0, let distance)` 匹配 y 轴上的点。这两种模式都包含一个对 `distance` 的绑定，并且 `distance` 在这两种模式中都是一个整数——这意味着 `case` 的主体中的代码始终可以访问 `distance` 的值。

## [控制转移语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Control-Transfer-Statements)

控制转移语句通过将控制从一段代码转移到另一段代码，改变代码的执行顺序。Swift 有五个控制转移语句：

- `continue`
- `break`
- `fallthrough`
- `return`
- `throw`

以下是 `continue` 、 `break` 和 `fallthrough` 语句的描述。 `return` 语句在函数中描述， `throw` 语句在使用抛出函数传播错误中描述。

### [continue](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Continue)

`continue` 语句告诉循环停止当前操作，并在下一次循环迭代的开始处重新开始。它表示“我已经完成了当前的循环迭代”，而不完全离开循环。

以下示例从小写字符串中删除所有元音和空格，以创建一个神秘的谜语短语：

```swift
let puzzleInput = "great minds think alike"
var puzzleOutput = ""
let charactersToRemove: [Character] = ["a", "e", "i", "o", "u", " "]
for character in puzzleInput {
    if charactersToRemove.contains(character) {
        continue
    }
    puzzleOutput.append(character)
}
print(puzzleOutput)
// Prints "grtmndsthnklk"
```

上面的代码在匹配到元音或空格时调用 `continue` 关键字，导致当前循环迭代立即结束，并直接跳到下一次迭代的开始。

### [中断](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Break)

`break` 语句立即结束整个控制流语句的执行。 `break` 语句可以在 `switch` 或循环语句中使用，当您想要比正常情况更早终止 `switch` 或循环语句的执行时。

[**在循环语句中中断**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Break-in-a-Loop-Statement)

当在循环语句内部使用时， `break` 会立即结束循环的执行并将控制权转移到循环结束大括号 ( `}` ) 之后的代码。当前循环迭代的其余代码不会被执行，循环的其余迭代也不会被开始。

[**在 Switch 语句中的 Break**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Break-in-a-Switch-Statement)

当在 `switch` 语句内部使用时， `break` 会导致 `switch` 语句立即结束其执行，并将控制权转移到 `switch` 语句结束大括号 ( `}` ) 之后的代码。

这种行为可以用来匹配并忽略 `switch` 语句中的一个或多个案例。由于 Swift 的 `switch` 语句是详尽的且不允许空案例，有时需要故意匹配并忽略一个案例以使您的意图明确。您可以通过将 `break` 语句写为您想要忽略的案例的整个主体来实现。当 `switch` 语句匹配该案例时，案例内的 `break` 语句会立即结束 `switch` 语句的执行。

> 注意
>
> 一个只包含注释的 `switch` 情况会被报告为编译时错误。注释不是语句，不会导致 `switch` 情况被忽略。始终使用 `break` 语句来忽略 `switch` 情况。

以下示例打开一个 `Character` 值，并确定它是否在四种语言中的一种中表示数字符号。为简洁起见，多个值在一个 `switch` 案例中涵盖。

```swift
let numberSymbol: Character = "三"  // Chinese symbol for the number 3
var possibleIntegerValue: Int?
switch numberSymbol {
case "1", "١", "一", "๑":
    possibleIntegerValue = 1
case "2", "٢", "二", "๒":
    possibleIntegerValue = 2
case "3", "٣", "三", "๓":
    possibleIntegerValue = 3
case "4", "٤", "四", "๔":
    possibleIntegerValue = 4
default:
    break
}
if let integerValue = possibleIntegerValue {
    print("The integer value of \(numberSymbol) is \(integerValue).")
} else {
    print("An integer value couldn't be found for \(numberSymbol).")
}
// Prints "The integer value of 三 is 3."
```

此示例检查 `numberSymbol` 以确定它是数字 `1` 到 `4` 的拉丁文、阿拉伯文、中文或泰文符号。如果找到匹配项， `switch` 语句的一个案例将一个名为 `possibleIntegerValue` 的可选 `Int?` 变量设置为适当的整数值。

在 `switch` 语句完成执行后，该示例使用可选绑定来确定是否找到了值。 `possibleIntegerValue` 变量由于是可选类型，因此拥有隐式初始值 `nil` ，因此可选绑定只有在 `possibleIntegerValue` 被其中一个 `switch` 语句的前四个案例设置为实际值时才会成功。

因为在上面的示例中列出每个可能的 `Character` 值并不实际，所以一个 `default` 案例处理任何未匹配的字符。这个 `default` 案例不需要执行任何操作，因此其主体仅用一个 `break` 语句编写。当 `default` 案例匹配时， `break` 语句结束 `switch` 语句的执行，代码执行从 `if let` 语句继续。

### [fallthrough](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Fallthrough)

在 Swift 中， `switch` 语句不会从每个 case 的底部掉落到下一个 case。这意味着，一旦第一个匹配的 case 完成，整个 `switch` 语句就会完成其执行。相比之下，C 要求您在每个 `switch` case 的末尾插入一个显式的 `break` 语句以防止掉落。避免默认掉落意味着 Swift 的 `switch` 语句比 C 中的对应语句要简洁且可预测得多，从而避免意外执行多个 `switch` case。

如果您需要 C 风格的贯穿行为，可以选择在逐个案例中使用 `fallthrough` 关键字来实现此行为。下面的示例使用 `fallthrough` 创建一个数字的文本描述。

```swift
let integerToDescribe = 5
var description = "The number \(integerToDescribe) is"
switch integerToDescribe {
case 2, 3, 5, 7, 11, 13, 17, 19:
    description += " a prime number, and also"
    fallthrough
default:
    description += " an integer."
}
print(description)
// Prints "The number 5 is a prime number, and also an integer."
```

这个示例声明了一个新的 `String` 变量，名为 `description` 并赋予它一个初始值。然后，函数使用 `switch` 语句考虑 `integerToDescribe` 的值。如果 `integerToDescribe` 的值是列表中的一个质数，函数会将文本附加到 `description` 的末尾，以说明该数字是质数。然后，它还使用 `fallthrough` 关键字“落入” `default` 情况。 `default` 情况在描述的末尾添加了一些额外的文本， `switch` 语句结束。

除非 `integerToDescribe` 的值在已知素数列表中，否则根本不会与第一个 `switch` 案例匹配。因为没有其他特定案例， `integerToDescribe` 与 `default` 案例匹配。

在 `switch` 语句执行完毕后，使用 `print(_:separator:terminator:)` 函数打印该数字的描述。在这个例子中，数字 `5` 被正确识别为一个质数。

> 注意
>
> `fallthrough` 关键字不会检查导致执行进入的 `switch` 情况的大小写条件。 `fallthrough` 关键字只是简单地使代码执行直接移动到下一个 case（或 `default` case）块中的语句，就像 C 的标准 `switch` 语句行为一样。

### [标记语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Labeled-Statements)

在 Swift 中，您可以将循环和条件语句嵌套在其他循环和条件语句中，以创建复杂的控制流结构。然而，循环和条件语句都可以使用 `break` 语句来提前结束其执行。因此，有时明确您希望哪个循环或条件语句被 `break` 语句终止是有用的。同样，如果您有多个嵌套循环，明确 `continue` 语句应影响哪个循环可能是有用的。

为了实现这些目标，您可以用语句标签标记循环语句或条件语句。对于条件语句，您可以使用语句标签与 `break` 语句结合，以结束标记的语句的执行。对于循环语句，您可以使用语句标签与 `break` 或 `continue` 语句结合，以结束或继续标记语句的执行。

标记语句通过将标签放置在与语句引入关键字同一行的位置，然后跟随一个冒号来表示。以下是 `while` 循环的语法示例，尽管所有循环和 `switch` 语句的原则是相同的：

```swift
<#label name#>: while <#condition#> {
   <#statements#>
}
```

以下示例使用带标记的 `while` 循环以及 `break` 和 `continue` 语句，适用于本章前面看到的蛇棋和梯子游戏的改编版本。这次游戏增加了一条额外规则：

- 要胜利，您必须恰好落在第 25 格上。

如果某次掷骰子会使您超过第 25 格，您必须重新掷骰，直到掷出恰好落在第 25 格所需的数字。

游戏棋盘与之前一样。

![snakesAndLadders](https://docs.swift.org/swift-book/images/org.swift.tspl/snakesAndLadders@2x.png){.light-only}
![snakesAndLadders~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/snakesAndLadders~dark@2x.png){.dark-only}

`finalSquare` 、 `board` 、 `square` 和 `diceRoll` 的值以与之前相同的方式初始化：

```swift
let finalSquare = 25
var board = [Int](repeating: 0, count: finalSquare + 1)
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
var square = 0
var diceRoll = 0
```

该版本的游戏使用了一个 `while` 循环和一个 `switch` 语句来实现游戏的逻辑。 `while` 循环有一个语句标签，称为 `gameLoop` ，以指示它是蛇梯游戏的主游戏循环。

`while` 循环的条件是 `while square != finalSquare` ，以反映您必须正好落在第 25 个方格上。

```swift
gameLoop: while square != finalSquare {
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    switch square + diceRoll {
    case finalSquare:
        // diceRoll will move us to the final square, so the game is over
        break gameLoop
    case let newSquare where newSquare > finalSquare:
        // diceRoll will move us beyond the final square, so roll again
        continue gameLoop
    default:
        // this is a valid move, so find out its effect
        square += diceRoll
        square += board[square]
    }
}
print("Game over!")
```

每次循环开始时都会掷骰子。循环并不立即移动玩家，而是使用一个 `switch` 语句来考虑移动的结果并确定该移动是否被允许：

- 如果骰子卷动将玩家移动到最后一格，游戏结束。 `break gameLoop` 语句将控制权转移到 `while` 循环外的第一行代码，这将结束游戏。
- 如果骰子卷动将玩家移动超过最后一格，则这个移动无效，玩家需要重新掷骰。 `continue gameLoop` 语句结束当前的 `while` 循环迭代，并开始下一次循环的迭代。
- 在所有其他情况下，骰子卷动是一个有效的移动。玩家向前移动 `diceRoll` 格，游戏逻辑检查是否有蛇和梯子。循环随即结束，控制权返回到 `while` 条件，以决定是否需要再进行一次轮次。

> 注意
>
> 如果上面的 `break` 语句没有使用 `gameLoop` 标签，它将跳出 `switch` 语句，而不是 `while` 语句。使用 `gameLoop` 标签可以明确应该终止哪个控制语句。

在调用 `continue gameLoop` 跳转到循环的下一次迭代时，严格来说并不需要使用 `gameLoop` 标签。游戏中只有一个循环，因此 `continue` 语句将影响哪个循环没有歧义。然而，使用 `gameLoop` 标签与 `continue` 语句一起并没有坏处。这样做与 `break` 语句旁边使用标签是一致的，并有助于使游戏的逻辑更清晰易懂。

## [提前退出](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Early-Exit)

一个 `guard` 语句，像一个 `if` 语句，依据表达式的布尔值来执行语句。您使用一个 `guard` 语句来要求在执行 `guard` 语句后面的代码之前某个条件必须为真。与 `if` 语句不同， `guard` 语句总是有一个 `else` 子句 —— 如果条件不为真， `else` 子句中的代码将被执行。

```swift
func greet(person: [String: String]) {
    guard let name = person["name"] else {
        return
    }


    print("Hello \(name)!")


    guard let location = person["location"] else {
        print("I hope the weather is nice near you.")
        return
    }


    print("I hope the weather is nice in \(location).")
}


greet(person: ["name": "John"])
// Prints "Hello John!"
// Prints "I hope the weather is nice near you."
greet(person: ["name": "Jane", "location": "Cupertino"])
// Prints "Hello Jane!"
// Prints "I hope the weather is nice in Cupertino."
```

如果满足 `guard` 语句的条件，代码执行将在 `guard` 语句的闭合括号后继续。作为条件一部分使用可选绑定赋值的任何变量或常量在 `guard` 语句所在的代码块的其余部分都是可用的。

如果该条件不满足，则执行 `else` 分支中的代码。该分支必须将控制权转移到包含 `guard` 语句的代码块的退出处。它可以使用控制转移语句，如 `return` 、 `break` 、 `continue` 或 `throw` ，或可以调用一个不返回的函数或方法，如 `fatalError(_:file:line:)` 。

使用 `guard` 语句来处理需求比使用 `if` 语句进行相同检查更能提高代码的可读性。它允许您编写通常执行的代码，而无需将其包装在 `else` 块中，并且可以将处理违反需求的代码与需求放在一起。

## [推迟的动作](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Deferred-Actions)

与控制流结构如 `if` 和 `while` 不同，它们让您控制代码的执行部分或执行的次数， `defer` 控制代码的执行时机。您使用 `defer` 块来编写将在稍后执行的代码，当您的程序到达当前作用域的末尾时。例如：

```swift
var score = 1
if score < 10 {
    defer {
        print(score)
    }
    score += 5
}
// Prints "6"
```

在上面的示例中， `defer` 块中的代码在退出 `if` 语句的主体之前执行。首先， `if` 语句中的代码运行，将 `score` 增加五。然后，在退出 `if` 语句的作用域之前，运行延迟代码，打印 `score` 。

在 `defer` 中的代码总是会运行，无论程序如何退出该范围。这包括像函数的早期退出、跳出 `for` 循环或抛出错误这样的代码。这种行为使得 `defer` 在需要保证一对操作发生的情况下非常有用——比如手动分配和释放内存、打开和关闭低级文件描述符，以及在数据库中开始和结束事务——因为您可以将这两个操作并排写在您的代码中。例如，下面的代码通过在一块代码中添加和减去 100 来给分数提供临时加分：

```swift
var score = 3
if score < 100 {
    score += 100
    defer {
        score -= 100
    }
    // Other code that uses the score with its bonus goes here.
    print(score)
}
// Prints "103"
```

如果您在同一范围内写了多个 `defer` 块，最先指定的那个是最后运行的。

```swift
if score < 10 {
    defer {
        print(score)
    }
    defer {
        print("The score is:")
    }
    score += 5
}
// Prints "The score is:"
// Prints "6"
```

如果您的程序停止运行——例如，由于运行时错误或崩溃——延迟代码不会执行。然而，在抛出错误后，延迟代码会执行；有关使用 `defer` 进行错误处理的信息，请参见指定清理操作。

## [检查 API 可用性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow#Checking-API-Availability)

Swift 内置支持检查 API 可用性，这确保您不会意外使用在特定部署目标上不可用的 API。

编译器使用 SDK 中的可用性信息来验证您代码中使用的所有 API 是否在您项目指定的部署目标上可用。如果您尝试使用不可用的 API，Swift 会在编译时报告错误。

您在 `if` 或 `guard` 语句中使用可用性条件，以根据您想要使用的 API 在运行时是否可用来有条件地执行代码块。编译器在验证该代码块中的 API 是否可用时使用可用性条件中的信息。

```swift
if #available(iOS 10, macOS 10.12, *) {
    // Use iOS 10 APIs on iOS, and use macOS 10.12 APIs on macOS
} else {
    // Fall back to earlier iOS and macOS APIs
}
```

上述可用性条件指定在 iOS 中， `if` 语句的主体仅在 iOS 10 及更高版本中执行；在 macOS 中，仅在 macOS 10.12 及更高版本中执行。最后一个参数 `*` 是必需的，并指定在其他任何平台上， `if` 的主体在您目标指定的最低部署目标上执行。

在一般情况下，可用性条件接受一组平台名称和版本。您可以使用平台名称，如 `iOS` 、 `macOS` 、 `watchOS` 、 `tvOS` 和 `visionOS` — 详细列表请参见声明属性。除了指定主要版本号，如 iOS 8 或 macOS 10.10 之外，您还可以指定次要版本号，如 iOS 11.2.6 和 macOS 10.13.3。

```swift
if #available(<#platform name#> <#version#>, <#...#>, *) {
    <#statements to execute if the APIs are available#>
} else {
    <#fallback statements to execute if the APIs are unavailable#>
}
```

当您在 `guard` 语句中使用可用性条件时，它会细化该代码块中其余代码所使用的可用性信息。

```swift
@available(macOS 10.12, *)
struct ColorPreference {
    var bestColor = "blue"
}


func chooseBestColor() -> String {
    guard #available(macOS 10.12, *) else {
       return "gray"
    }
    let colors = ColorPreference()
    return colors.bestColor
}
```

在上面的示例中， `ColorPreference` 结构需要 macOS 10.12 或更高版本。 `chooseBestColor()` 函数以可用性保护开始。如果平台版本太旧而无法使用 `ColorPreference` ，则会回退到始终可用的行为。在 `guard` 语句之后，您可以使用需要 macOS 10.12 或更高版本的 API。

除了 `#available` ，Swift 还支持使用不可用条件进行相反检查。例如，以下两个检查执行相同的操作：

```swift
if #available(iOS 10, *) {
} else {
    // Fallback code
}


if #unavailable(iOS 10) {
    // Fallback code
}
```

使用 `#unavailable` 表单可以使您的代码在检查仅包含回退代码时更具可读性。
