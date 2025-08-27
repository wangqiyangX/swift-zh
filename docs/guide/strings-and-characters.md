# 字符串和字符

> 存储和操作文本。

字符串是一系列字符，例如 `"hello, world"` 或 `"albatross"` 。Swift 字符串由 `String` 类型表示。可以通过多种方式访问 `String` 的内容，包括作为 `Character` 值的集合。

Swift 的 `String` 和 `Character` 类型提供了一种快速、符合 Unicode 的方式来处理代码中的文本。字符串创建和操作的语法轻量且可读，字符串字面量语法类似于 C。字符串连接就像用 `+` 运算符组合两个字符串一样简单，字符串的可变性通过选择常量或变量来管理，就像 Swift 中的任何其他值一样。您还可以使用字符串将常量、变量、字面量和表达式插入到更长的字符串中，这个过程称为字符串插值。这使得为显示、存储和打印创建自定义字符串值变得简单。

尽管语法如此简单，Swift 的 `String` 类型仍然是一个快速、现代的字符串实现。每个字符串由独立于编码的 Unicode 字符组成，并提供对各种 Unicode 表示形式中这些字符的访问支持。

> 注意
>
> Swift 的 `String` 类型与 Foundation 的 `NSString` 类进行桥接。Foundation 还扩展了 `String` 以暴露由 `NSString` 定义的方法。这意味着，如果您导入 Foundation，您可以在 `String` 上直接访问那些 `NSString` 方法，而无需进行类型转换。

有关使用 `String` 与 Foundation 和 Cocoa 的更多信息，请参见在 String 和 NSString 之间的桥接。

## [字符串字面量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#String-Literals)

您可以在代码中包含预定义的 `String` 值作为字符串字面量。字符串字面量是一系列被双引号（ `"` ）括起来的字符。

将字符串字面量用作常量或变量的初始值：

```swift
let someString = "Some string literal value"
```

注意，Swift 为 `someString` 常量推断类型 `String` ，因为它是用字符串字面值初始化的。

### [多行字符串字面量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Multiline-String-Literals)

如果您需要一个跨越多行的字符串，请使用多行字符串字面量——一系列用三个双引号括起来的字符：

```swift
let quotation = """
The White Rabbit put on his spectacles.  "Where shall I begin,
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
"""
```

多行字符串字面量包括其开头和结尾引号之间的所有行。字符串从开头引号后面的第一行（ `"""` ）开始，在结尾引号之前的行结束，这意味着下面的任何字符串都不以换行符开始或结束：

```swift
let singleLineString = "These are the same."
let multilineString = """
These are the same.
"""
```

当您的源代码在多行字符串字面量中包含换行符时，该换行符也会出现在字符串的值中。如果您想使用换行符使源代码更易读，但又不希望换行符成为字符串值的一部分，请在这些行的末尾写一个反斜杠 ( `\` )：

```swift
let softWrappedQuotation = """
The White Rabbit put on his spectacles.  "Where shall I begin, \
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on \
till you come to the end; then stop."
"""
```

要创建一个以换行符开头或结尾的多行字符串字面量，请将空行作为第一行或最后一行。例如：

```swift
let lineBreaks = """

This string starts with a line break.
It also ends with a line break.

"""
```

多行字符串可以缩进以匹配周围的代码。关闭引号前的空白 ( `"""` ) 告诉 Swift 忽略所有其他行之前的空白。然而，如果您在行的开头写入空白，除了关闭引号前的空白外，该空白也会被包含在内。

![multilineStringWhitespace](https://docs.swift.org/swift-book/images/org.swift.tspl/multilineStringWhitespace@2x.png){.light-only}
![multilineStringWhitespace~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/multilineStringWhitespace~dark@2x.png){.dark-only}

在上面的示例中，尽管整个多行字符串字面量是缩进的，但字符串中的第一行和最后一行并没有以任何空白开头。中间行的缩进比关闭引号多，因此它以额外的四个空格缩进开始。

### [字符串文字中的特殊字符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Special-Characters-in-String-Literals)

字符串文字可以包含以下特殊字符：

- 转义特殊字符 `\0` （空字符）， `\\` （反斜杠）， （水平制表符）， （换行符）， （回车符）， `\"` （双引号）和 `\'` （单引号）
- 一个任意的 Unicode 标量值，写作 `\u{` n `}` ，其中 n 是一个 1–8 位的十六进制数字（Unicode 在下面的 Unicode 中讨论）

下面的代码显示了这四个特殊字符的示例。 `wiseWords` 常量包含两个转义的双引号。 `dollarSign` 、 `blackHeart` 和 `sparklingHeart` 常量演示了 Unicode 标量格式：

```swift
let wiseWords = "\"Imagination is more important than knowledge\" - Einstein"
// "Imagination is more important than knowledge" - Einstein
let dollarSign = "\u{24}"        // $,  Unicode scalar U+0024
let blackHeart = "\u{2665}"      // ♥,  Unicode scalar U+2665
let sparklingHeart = "\u{1F496}" // 💖, Unicode scalar U+1F496
```

因为多行字符串字面量使用三个双引号而不是一个，所以您可以在多行字符串字面量中包含一个双引号 ( `"` ) 而无需转义。要在多行字符串中包含文本 `"""` ，请转义至少一个引号。例如：

```swift
let threeDoubleQuotationMarks = """
Escaping the first quotation mark \"""
Escaping all three quotation marks \"\"\"
"""
```

### [扩展字符串定界符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Extended-String-Delimiters)

您可以将字符串字面量放在扩展定界符内，以在字符串中包含特殊字符而不引发它们的效果。您将字符串放在引号 ( `"` ) 中，并用数字符号 ( `#` ) 包围它。例如，打印字符串字面量 `#"Line 1Line 2"#` 会打印换行转义序列 ( )，而不是将字符串打印在两行上。

如果您需要字符串字面量中字符的特殊效果，请匹配转义字符 ( `\` ) 后字符串中的井号数量。例如，如果您的字符串是 `#"Line 1Line 2"#` 并且您想换行，可以使用 `#"Line 1\#nLine 2"#` 代替。同样， `###"Line1\###nLine2"###` 也会换行。

使用扩展定界符创建的字符串字面量也可以是多行字符串字面量。您可以使用扩展定界符在多行字符串中包含文本 `"""` ，覆盖结束字面量的默认行为。例如：

```swift
let threeMoreDoubleQuotationMarks = #"""
Here are three more double quotes: """
"""#
```

## [初始化一个空字符串](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Initializing-an-Empty-String)

要创建一个空的 `String` 值作为构建更长字符串的起点，可以将一个空字符串字面量赋值给一个变量，或者使用初始化语法初始化一个新的 `String` 实例：

```swift
var emptyString = ""               // empty string literal
var anotherEmptyString = String()  // initializer syntax
// these two strings are both empty, and are equivalent to each other
```

通过检查其布尔 `isEmpty` 属性来确定 `String` 值是否为空：

```swift
if emptyString.isEmpty {
    print("Nothing to see here")
}
// Prints "Nothing to see here"
```

## [字符串的可变性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#String-Mutability)

您可以通过将特定的 `String` 赋值给变量（在这种情况下可以修改）或常量（在这种情况下不能修改）来指示它是否可以被修改（或变异）

```swift
var variableString = "Horse"
variableString += " and carriage"
// variableString is now "Horse and carriage"


let constantString = "Highlander"
constantString += " and another Highlander"
// this reports a compile-time error - a constant string cannot be modified
```

> 注意
>
> 这种方法与 Objective-C 和 Cocoa 中的字符串变异不同，在那里您可以在两个类（ `NSString` 和 `NSMutableString` ）之间选择，以指示字符串是否可以被变异。

## [字符串是值类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Strings-Are-Value-Types)

Swift 的 `String` 类型是值类型。如果您创建一个新的 `String` 值，当它被传递到一个函数或方法，或者当它被分配给一个常量或变量时，该 `String` 值会被复制。在每种情况下，都会创建现有 `String` 值的新副本，并传递或分配该新副本，而不是原始版本。值类型在结构和枚举是值类型 中进行了描述。

Swift 的默认复制 `String` 行为确保当一个函数或方法传递给您一个 `String` 值时，很清楚您拥有那个确切的 `String` 值，不管它来自哪里。您可以确信，传递给您的字符串不会被修改，除非您自己去修改它。

在后台，Swift 的编译器优化了字符串的使用，以便只有在绝对必要时才会进行实际复制。这意味着在使用字符串作为值类型时，您始终能获得出色的性能。

## [处理字符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Working-with-Characters)

您可以通过使用 `for` - `in` 循环遍历字符串来访问单个 `Character` 值：

```swift
for character in "Dog!🐶" {
    print(character)
}
// D
// o
// g
// !
// 🐶
```

`for` - `in` 循环在 For-In 循环中描述。

或者，您可以通过提供 `Character` 类型注释，从单字符字符串字面量创建一个独立的 `Character` 常量或变量：

```swift
let exclamationMark: Character = "!"
```

`String` 值可以通过将 `Character` 值的数组作为参数传递给其初始化器来构造：

```swift
let catCharacters: [Character] = ["C", "a", "t", "!", "🐱"]
let catString = String(catCharacters)
print(catString)
// Prints "Cat!🐱"
```

## [连接字符串和字符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Concatenating-Strings-and-Characters)

`String` 值可以通过加法运算符 ( `+` ) 相加（或连接）以创建一个新的 `String` 值：

```swift
let string1 = "hello"
let string2 = " there"
var welcome = string1 + string2
// welcome now equals "hello there"
```

您还可以使用加法赋值运算符 ( `+=` ) 将 `String` 值附加到现有的 `String` 变量

```swift
var instruction = "look over"
instruction += string2
// instruction now equals "look over there"
```

您可以使用 `String` 类型的 `append()` 方法将 `Character` 值附加到 `String` 变量

```swift
let exclamationMark: Character = "!"
welcome.append(exclamationMark)
// welcome now equals "hello there!"
```

> 注意
>
> 您无法将 `String` 或 `Character` 附加到现有的 `Character` 变量，因为 `Character` 值必须只包含一个字符。

如果您使用多行字符串字面量来构建较长字符串的行，则希望字符串中的每一行都以换行符结束，包括最后一行。例如：

```swift
let badStart = """
    one
    two
    """
let end = """
    three
    """
print(badStart + end)
// Prints two lines:
// one
// twothree


let goodStart = """
    one
    two

    """
print(goodStart + end)
// Prints three lines:
// one
// two
// three
```

在上面的代码中，将 `badStart` 与 `end` 连接会生成一个两行字符串，这并不是期望的结果。因为 `badStart` 的最后一行没有以换行符结束，所以该行与 `end` 的第一行合并。相反， `goodStart` 的两行都以换行符结束，因此当其与 `end` 合并时，结果有三行，正如预期的那样。

## [字符串插值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#String-Interpolation)

字符串插值是一种通过将常量、变量、字面量和表达式的值包含在字符串字面量中，从混合中构造新 `String` 值的方法。您可以在单行和多行字符串字面量中使用字符串插值。您插入到字符串字面量中的每个项目都用一对括号包装，并由反斜杠 ( `\` ) 前缀：

```swift
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
// message is "3 times 2.5 is 7.5"
```

在上面的示例中， `multiplier` 的值作为 `\(multiplier)` 插入到字符串字面量中。当字符串插值被求值以创建实际字符串时，此占位符将被实际值 `multiplier` 替换。

`multiplier` 的值也是字符串中后面更大表达式的一部分。此表达式计算 `Double(multiplier) * 2.5` 的值并将结果 ( `7.5` ) 插入字符串中。在这种情况下，当该表达式包含在字符串字面量中时，它写作 `\(Double(multiplier) * 2.5)` 。

您可以使用扩展字符串定界符来创建包含本应被视为字符串插值的字符的字符串。例如：

```swift
print(#"Write an interpolated string in Swift using \(multiplier)."#)
// Prints "Write an interpolated string in Swift using \(multiplier)."
```

要在使用扩展定界符的字符串内部使用字符串插值，请确保反斜杠后面的数字符号数量与字符串开头和结尾的数字符号数量匹配。例如：

```swift
print(#"6 times 7 is \#(6 * 7)."#)
// Prints "6 times 7 is 42."
```

> 注意
>
> 您在插值字符串中的括号内编写的表达式不能包含未转义的反斜杠 ( `\` )、回车符或换行符。然而，它们可以包含其他字符串字面量。

## [Unicode](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Unicode)

Unicode 是一种国际标准，用于编码、表示和处理不同书写系统中的文本。它使您能够以标准化形式表示几乎任何语言中的任何字符，以及从外部源（如文本文件或网页）读取和写入这些字符。Swift 的 `String` 和 `Character` 类型完全符合 Unicode，如本节所述。

### [Unicode 标量值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Unicode-Scalar-Values)

在幕后，Swift 的原生 `String` 类型是由 Unicode 标量值构建的。Unicode 标量值是字符或修饰符的唯一 21 位数字，例如 `U+0061` 代表 `LATIN SMALL LETTER A` ( `"a"` )，或者 `U+1F425` 代表 `FRONT-FACING BABY CHICK` ( `"🐥"` )。

请注意，并非所有 21 位 Unicode 标量值都分配给一个字符——一些标量值保留用于将来的分配或用于 UTF-16 编码。已分配给字符的标量值通常也有一个名称，例如上面示例中的 `LATIN SMALL LETTER A` 和 `FRONT-FACING BABY CHICK` 。

### [扩展字形集群](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Extended-Grapheme-Clusters)

每个 Swift 的 `Character` 类型实例表示一个单一的扩展字形集群。扩展字形集群是一系列一个或多个 Unicode 标量的序列，这些标量（组合在一起）产生一个可读性强的人类字符。

这儿有一个例子。字母 `é` 可以表示为单一 Unicode 标量 `é` （ `LATIN SMALL LETTER E WITH ACUTE` ，或 `U+00E9` ）。然而，相同的字母也可以表示为标量对——一个标准字母 `e` （ `LATIN SMALL LETTER E` ，或 `U+0065` ），后面跟着 `COMBINING ACUTE ACCENT` 标量（ `U+0301` ）。 `COMBINING ACUTE ACCENT` 标量在视觉上应用于它前面的标量，当由一个支持 Unicode 的文本渲染系统呈现时，将 `e` 转变为 `é` 。

在这两种情况下，字母 `é` 表示为一个单一的 Swift `Character` 值，代表一个扩展字形集群。在第一种情况下，集群包含一个标量；在第二种情况下，这是一个两个标量的集群：

```swift
let eAcute: Character = "\u{E9}"                         // é
let combinedEAcute: Character = "\u{65}\u{301}"          // e followed by ́
// eAcute is é, combinedEAcute is é
```

扩展字形集群是将许多复杂脚本字符表示为单一 `Character` 值的灵活方式。例如，来自韩文拼音的 Hangul 音节可以表示为预组合或分解序列。这两种表示在 Swift 中都符合单一 `Character` 值:

```swift
let precomposed: Character = "\u{D55C}"                  // 한
let decomposed: Character = "\u{1112}\u{1161}\u{11AB}"   // ᄒ, ᅡ, ᆫ
// precomposed is 한, decomposed is 한
```

扩展字符集允许封闭标记的标量（如 `COMBINING ENCLOSING CIRCLE` 或 `U+20DD` ）将其他 Unicode 标量作为单个 `Character` 值的一部分进行封闭：

```swift
let enclosedEAcute: Character = "\u{E9}\u{20DD}"
// enclosedEAcute is é⃝
```

区域指示符符号的 Unicode 标量可以成对组合以生成单个 `Character` 值，例如这个组合 `REGIONAL INDICATOR SYMBOL LETTER U` ( `U+1F1FA` ) 和 `REGIONAL INDICATOR SYMBOL LETTER S` ( `U+1F1F8` ):

```swift
let regionalIndicatorForUS: Character = "\u{1F1FA}\u{1F1F8}"
// regionalIndicatorForUS is 🇺🇸
```

## [计数字符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Counting-Characters)

要获取字符串中 `Character` 值的计数，请使用字符串的 `count` 属性：

```swift
let unusualMenagerie = "Koala 🐨, Snail 🐌, Penguin 🐧, Dromedary 🐪"
print("unusualMenagerie has \(unusualMenagerie.count) characters")
// Prints "unusualMenagerie has 40 characters"
```

请注意，Swift 对 `Character` 值使用扩展图形簇，这意味着字符串连接和修改可能并不总是影响字符串的字符计数。

例如，如果您用四个字符的单词 `cafe` 初始化一个新字符串，然后在字符串的末尾追加一个 `COMBINING ACUTE ACCENT` ( `U+0301` )，那么结果字符串的字符数仍然是 `4` ，第四个字符是 `é` ，而不是 `e` ：

```swift
var word = "cafe"
print("the number of characters in \(word) is \(word.count)")
// Prints "the number of characters in cafe is 4"


word += "\u{301}"    // COMBINING ACUTE ACCENT, U+0301


print("the number of characters in \(word) is \(word.count)")
// Prints "the number of characters in café is 4"
```

> 注意
>
> 扩展的字形簇可以由多个 Unicode 标量组成。这意味着不同的字符——以及同一字符的不同表示——可能需要不同的内存来存储。因此，Swift 中的字符在字符串表示中并不占用相同的内存量。因此，字符串中的字符数量无法在不遍历字符串以确定其扩展字形簇边界的情况下计算。如果您正在处理特别长的字符串值，请注意 `count` 属性必须遍历整个字符串中的 Unicode 标量，以确定该字符串的字符。
>
> 通过 `count` 属性返回的字符数并不总是与包含相同字符的 `NSString` 的 `length` 属性相同。 `NSString` 的长度是基于字符串的 UTF-16 表示中的 16 位代码单元的数量，而不是字符串中 Unicode 扩展字形集的数量。

## [访问和修改字符串](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Accessing-and-Modifying-a-String)

您可以通过其方法和属性访问和修改字符串，或使用下标语法。

### [字符串索引](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#String-Indices)

每个 `String` 值都有一个相关的索引类型 `String.Index` ，它对应于字符串中每个 `Character` 的位置。

如上所述，不同的字符可能需要不同的内存来存储，因此为了确定特定位置上的 `Character` ，您必须从该 `String` 的开始或结束遍历每个 Unicode 标量。出于这个原因，Swift 字符串不能通过整数值进行索引。

使用 `startIndex` 属性访问 `String` 的第一个 `Character` 的位置。 `endIndex` 属性是 `String` 中最后一个字符之后的位置。因此， `endIndex` 属性不是字符串下标的有效参数。如果 `String` 为空， `startIndex` 和 `endIndex` 是相等的。

您可以使用 `String` 的 `index(before:)` 和 `index(after:)` 方法访问给定索引之前和之后的索引。要访问离给定索引更远的索引，您可以使用 `index(_:offsetBy:)` 方法，而不是多次调用这些方法中的一个。

您可以使用下标语法访问特定 `String` 索引处的 `Character` 。

```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

尝试访问字符串范围外的索引或在字符串范围外的索引访问 \{{0 \}} 将触发运行时错误。

```swift
greeting[greeting.endIndex] // Error
greeting.index(after: greeting.endIndex) // Error
```

使用 `indices` 属性访问字符串中单个字符的所有索引。

```swift
for index in greeting.indices {
    print("\(greeting[index]) ", terminator: "")
}
// Prints "G u t e n   T a g ! "
```

> 注意
>
> 您可以在任何符合 `Collection` 协议的类型上使用 `startIndex` 和 `endIndex` 属性以及 `index(before:)` 、 `index(after:)` 和 `index(_:offsetBy:)` 方法。这包括 `String` ，如下所示，以及集合类型，如 `Array` 、 `Dictionary` 和 `Set` 。

### [插入和删除](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Inserting-and-Removing)

要在指定索引处将单个字符插入字符串中，请使用 `insert(_:at:)` 方法，要在指定索引处插入另一个字符串的内容，请使用 `insert(contentsOf:at:)` 方法。

```swift
var welcome = "hello"
welcome.insert("!", at: welcome.endIndex)
// welcome now equals "hello!"


welcome.insert(contentsOf: " there", at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there!"
```

要从字符串中删除指定索引的单个字符，请使用 `remove(at:)` 方法，要删除指定范围的子字符串，请使用 `removeSubrange(_:)` 方法：

```swift
welcome.remove(at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there"


let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
// welcome now equals "hello"
```

> 注意
>
> 您可以在任何符合 `RangeReplaceableCollection` 协议的类型上使用 `insert(_:at:)` 、 `insert(contentsOf:at:)` 、 `remove(at:)` 和 `removeSubrange(_:)` 方法。这包括 `String` ，如这里所示，以及集合类型，如 `Array` 、 `Dictionary` 和 `Set` 。

## [子字符串](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Substrings)

当您从一个字符串中获取子字符串时——例如，使用下标或像 `prefix(_:)` 这样的方式——结果是 `Substring` 的一个实例，而不是另一个字符串。Swift 中的子字符串具有与字符串大部分相同的方法，这意味着您可以像处理字符串一样处理子字符串。然而，与字符串不同的是，您在对字符串执行操作时只会短暂使用子字符串。当您准备将结果存储更长时间时，您将子字符串转换为 `String` 的一个实例。例如：

```swift
let greeting = "Hello, world!"
let index = greeting.firstIndex(of: ",") ?? greeting.endIndex
let beginning = greeting[..<index]
// beginning is "Hello"


// Convert the result to a String for long-term storage.
let newString = String(beginning)
```

像字符串一样，每个子字符串都有一块内存区域，用于存储构成子字符串的字符。字符串和子字符串之间的区别在于，作为性能优化，子字符串可以重用用于存储原始字符串的部分内存，或用于存储另一个子字符串的部分内存。（字符串也有类似的优化，但如果两个字符串共享内存，它们是相等的。）这种性能优化意味着在修改字符串或子字符串之前，您不必支付复制内存的性能成本。如上所述，子字符串不适合长期存储——因为它们重用原始字符串的存储，只要其任何子字符串被使用，整个原始字符串必须保留在内存中。

在上面的例子中， `greeting` 是一个字符串，这意味着它有一个内存区域，用于存储构成该字符串的字符。因为 `beginning` 是 `greeting` 的子字符串，所以它重用了 `greeting` 使用的内存。相比之下， `newString` 是一个字符串——当它从子字符串创建时，它有自己的存储。下图显示了这些关系：

![stringSubstring](https://docs.swift.org/swift-book/images/org.swift.tspl/stringSubstring@2x.png){.light-only}
![stringSubstring~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/stringSubstring~dark@2x.png){.dark-only}

> 注意
>
> `String` 和 `Substring` 都符合 `StringProtocol` 协议，这意味着字符串操作函数通常方便接受 `StringProtocol` 值。您可以使用 `String` 或 `Substring` 值调用此类函数。

## [比较字符串](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Comparing-Strings)

Swift 提供三种比较文本值的方法：字符串和字符相等、前缀相等和后缀相等。

### [字符串和字符的相等性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#String-and-Character-Equality)

字符串和字符的相等性通过“等于”运算符 ( `==` ) 和“不等于”运算符 ( `!=` ) 进行检查，如比较运算符中所述：

```swift
let quotation = "We're a lot alike, you and I."
let sameQuotation = "We're a lot alike, you and I."
if quotation == sameQuotation {
    print("These two strings are considered equal")
}
// Prints "These two strings are considered equal"
```

如果两个 `String` 值（或两个 `Character` 值）的扩展字形簇在规范上是等价的，则它们被视为相等。扩展字形簇在规范上是等价的，如果它们具有相同的语言意义和外观，即使它们在后台由不同的 Unicode 标量组成。

例如， `LATIN SMALL LETTER E WITH ACUTE` ( `U+00E9` ) 在规范上等价于 `LATIN SMALL LETTER E` ( `U+0065` ) 后跟 `COMBINING ACUTE ACCENT` ( `U+0301` )。这两个扩展字形簇都是表示字符 `é` 的有效方式，因此它们被认为是规范上等价的：

```swift
// "Voulez-vous un café?" using LATIN SMALL LETTER E WITH ACUTE
let eAcuteQuestion = "Voulez-vous un caf\u{E9}?"


// "Voulez-vous un café?" using LATIN SMALL LETTER E and COMBINING ACUTE ACCENT
let combinedEAcuteQuestion = "Voulez-vous un caf\u{65}\u{301}?"


if eAcuteQuestion == combinedEAcuteQuestion {
    print("These two strings are considered equal")
}
// Prints "These two strings are considered equal"
```

相反， `LATIN CAPITAL LETTER A` ( `U+0041` , 或 `"A"` ) 在英语中并不等同于 `CYRILLIC CAPITAL LETTER A` ( `U+0410` , 或 `"А"` ) 在俄语中的用法。这些字符在视觉上相似，但并没有相同的语言意义：

```swift
let latinCapitalLetterA: Character = "\u{41}"


let cyrillicCapitalLetterA: Character = "\u{0410}"


if latinCapitalLetterA != cyrillicCapitalLetterA {
    print("These two characters aren't equivalent.")
}
// Prints "These two characters aren't equivalent."
```

> 注意
>
> Swift 中的字符串和字符比较不受区域设置的影响。

### [前缀和后缀相等](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Prefix-and-Suffix-Equality)

要检查一个字符串是否具有特定的前缀或后缀，可以调用该字符串的 `hasPrefix(_:)` 和 `hasSuffix(_:)` 方法，这两个方法都接受一个类型为 `String` 的单个参数，并返回一个布尔值。

下面的示例考虑了一个字符串数组，表示莎士比亚《罗密欧与朱丽叶》前两幕的场景位置：

```swift
let romeoAndJuliet = [
    "Act 1 Scene 1: Verona, A public place",
    "Act 1 Scene 2: Capulet's mansion",
    "Act 1 Scene 3: A room in Capulet's mansion",
    "Act 1 Scene 4: A street outside Capulet's mansion",
    "Act 1 Scene 5: The Great Hall in Capulet's mansion",
    "Act 2 Scene 1: Outside Capulet's mansion",
    "Act 2 Scene 2: Capulet's orchard",
    "Act 2 Scene 3: Outside Friar Lawrence's cell",
    "Act 2 Scene 4: A street in Verona",
    "Act 2 Scene 5: Capulet's mansion",
    "Act 2 Scene 6: Friar Lawrence's cell"
]
```

您可以使用 `hasPrefix(_:)` 方法与 `romeoAndJuliet` 数组来计算剧本第一幕中的场景数量：

```swift
var act1SceneCount = 0
for scene in romeoAndJuliet {
    if scene.hasPrefix("Act 1 ") {
        act1SceneCount += 1
    }
}
print("There are \(act1SceneCount) scenes in Act 1")
// Prints "There are 5 scenes in Act 1"
```

同样，使用 `hasSuffix(_:)` 方法来计算发生在 Capulet 的大厦和 Friar Lawrence 的小屋内或周围的场景数量：

```swift
var mansionCount = 0
var cellCount = 0
for scene in romeoAndJuliet {
    if scene.hasSuffix("Capulet's mansion") {
        mansionCount += 1
    } else if scene.hasSuffix("Friar Lawrence's cell") {
        cellCount += 1
    }
}
print("\(mansionCount) mansion scenes; \(cellCount) cell scenes")
// Prints "6 mansion scenes; 2 cell scenes"
```

> 注意
>
> `hasPrefix(_:)` 和 `hasSuffix(_:)` 方法对每个字符串中的扩展字符簇执行逐字符的规范等价比较，如字符串和字符相等性中所述。

## [字符串的 Unicode 表示](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Unicode-Representations-of-Strings)

当 Unicode 字符串被写入文本文件或其他存储时，该字符串中的 Unicode 标量会以几种 Unicode 定义的编码形式之一进行编码。每种形式将字符串编码为称为代码单元的小块。这些包括 UTF-8 编码形式（将字符串编码为 8 位代码单元）、UTF-16 编码形式（将字符串编码为 16 位代码单元）和 UTF-32 编码形式（将字符串编码为 32 位代码单元）。

Swift 提供了几种不同的方法来访问字符串的 Unicode 表示。您可以使用 `for` - `in` 语句遍历字符串，以访问其作为 Unicode 扩展字形簇的单个 `Character` 值。该过程在《处理字符》中进行了描述。

另外，可以通过三种其他符合 Unicode 的表示方式访问 `String` 值：

- 一组 UTF-8 代码单元（使用字符串的 `utf8` 属性访问）
- 一组 UTF-16 代码单元（使用字符串的 `utf16` 属性访问）
- 一组 21 位 Unicode 标量值，相当于字符串的 UTF-32 编码形式（使用字符串的 `unicodeScalars` 属性访问）

以下每个示例展示了以下字符串的不同表示，该字符串由字符 `D` 、 `o` 、 `g` 、 `‼` （ `DOUBLE EXCLAMATION MARK` 或 Unicode 标量 `U+203C` ）和 🐶 字符（ `DOG FACE` 或 Unicode 标量 `U+1F436` ）组成：

```swift
let dogString = "Dog‼🐶"
```

### [UTF-8 表示](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#UTF-8-Representation)

您可以通过遍历其 `utf8` 属性来访问 `String` 的 UTF-8 表示。该属性的类型为 `String.UTF8View` ，是一个无符号 8 位 ( `UInt8` ) 值的集合，每个值对应字符串的 UTF-8 表示中的一个字节：

![UTF8](https://docs.swift.org/swift-book/images/org.swift.tspl/UTF8@2x.png){.light-only}
![UTF8~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/UTF8~dark@2x.png){.dark-only}

```swift
for codeUnit in dogString.utf8 {
    print("\(codeUnit) ", terminator: "")
}
print("")
// Prints "68 111 103 226 128 188 240 159 144 182 "
```

在上面的例子中，前三个十进制 `codeUnit` 值 ( `68` , `111` , `103` ) 表示字符 `D` , `o` 和 `g` ，它们的 UTF-8 表示与其 ASCII 表示相同。接下来的三个十进制 `codeUnit` 值 ( `226` , `128` , `188` ) 是 `DOUBLE EXCLAMATION MARK` 字符的三字节 UTF-8 表示。最后四个 `codeUnit` 值 ( `240` , `159` , `144` , `182` ) 是 `DOG FACE` 字符的四字节 UTF-8 表示。

### [UTF-16 表示法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#UTF-16-Representation)

您可以通过迭代其 `utf16` 属性来访问 `String` 的 UTF-16 表示。该属性的类型为 `String.UTF16View` ，它是一个无符号 16 位 ( `UInt16` ) 值的集合，表示字符串的 UTF-16 表示中的每个 16 位代码单元：

![UTF16](https://docs.swift.org/swift-book/images/org.swift.tspl/UTF16@2x.png){.light-only}
![UTF16~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/UTF16~dark@2x.png){.dark-only}

```swift
for codeUnit in dogString.utf16 {
    print("\(codeUnit) ", terminator: "")
}
print("")
// Prints "68 111 103 8252 55357 56374 "
```

同样，前三个 `codeUnit` 值 ( `68` , `111` , `103` ) 表示字符 `D` , `o` 和 `g` ，其 UTF-16 代码单元与字符串的 UTF-8 表示中的值相同 (因为这些 Unicode 标量表示 ASCII 字符)。

第四个 `codeUnit` 值 ( `8252` ) 是十六进制值 `203C` 的十进制等效值，该值表示 `DOUBLE EXCLAMATION MARK` 字符的 Unicode 标量 `U+203C` 。该字符可以在 UTF-16 中表示为单个代码单元。

第五和第六个 `codeUnit` 值 ( `55357` 和 `56374` ) 是 `DOG FACE` 字符的 UTF-16 替代对表示。这些值是高替代值 `U+D83D` (十进制值 `55357` ) 和低替代值 `U+DC36` (十进制值 `56374` )。

### [Unicode 标量表示](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/stringsandcharacters#Unicode-Scalar-Representation)

您可以通过迭代其 `unicodeScalars` 属性来访问 `String` 值的 Unicode 标量表示。该属性的类型为 `UnicodeScalarView` ，它是类型为 `UnicodeScalar` 的值的集合。

每个 `UnicodeScalar` 都有一个 `value` 属性，该属性返回标量的 21 位值，以 `UInt32` 值表示：

![UnicodeScalar](https://docs.swift.org/swift-book/images/org.swift.tspl/UnicodeScalar@2x.png){.light-only}
![UnicodeScalar~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/UnicodeScalar~dark@2x.png){.dark-only}

```swift
for scalar in dogString.unicodeScalars {
    print("\(scalar.value) ", terminator: "")
}
print("")
// Prints "68 111 103 8252 128054 "
```

前三个 `UnicodeScalar` 值 ( `68` , `111` , `103` ) 的 `value` 属性再次表示字符 `D` , `o` , 和 `g` 。

第四个 `codeUnit` 值 ( `8252` ) 再次是十进制形式的十六进制值 `203C` ，代表字符 `DOUBLE EXCLAMATION MARK` 的 Unicode 标量 `U+203C` 。

第五个也是最后一个 `UnicodeScalar` ( `128054` ) 的 `value` 属性是十六进制值 `1F436` 的十进制形式，代表字符 `DOG FACE` 的 Unicode 标量 `U+1F436` 。

作为查询它们的 `value` 属性的替代方法，每个 `UnicodeScalar` 值也可以用于构造一个新的 `String` 值，例如通过字符串插值：

```swift
for scalar in dogString.unicodeScalars {
    print("\(scalar) ")
}
// D
// o
// g
// ‼
// 🐶
```
