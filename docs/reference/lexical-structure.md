# 词法结构

> 使用语法的最低级组件。

Swift 的词法结构描述了哪些字符序列形成语言的有效标记。这些有效标记构成了语言的最低级构建块，并用于在随后的章节中描述语言的其余部分。一个标记由标识符、关键字、标点符号、字面量或运算符组成。

在大多数情况下，令牌是通过考虑输入文本中可能的最长子字符串，从 Swift 源文件的字符生成的，遵循下面指定的语法约束。这种行为被称为最长匹配或最大贪婪匹配。

## [空白和注释](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure#Whitespace-and-Comments)

空白有两个用途：在源文件中分隔令牌，并区分前缀、后缀和中缀运算符（参见运算符），但其他情况下被忽略。以下字符被视为空白字符：空格 (U+0020)、换行符 (U+000A)、回车符 (U+000D)、水平制表符 (U+0009)、垂直制表符 (U+000B)、换页符 (U+000C) 和空字符 (U+0000)。

注释被编译器视为空白。单行注释以 `//` 开头，并持续到换行符 (U+000A) 或回车符 (U+000D)。多行注释以 `/*` 开头，以 `*/` 结束。可以嵌套多行注释，但注释标记必须平衡。

注释可以包含额外的格式和标记，如《标记格式参考》中所述。

> 空白的语法
>
> _whitespace_ → _whitespace-item_ _whitespace?_\
> &#xNAN;_&#x77;hitespace-item_ → _line-break_\
> &#xNAN;_&#x77;hitespace-item_ → _inline-space_\
> &#xNAN;_&#x77;hitespace-item_ → _comment_\
> &#xNAN;_&#x77;hitespace-item_ → _multiline-comment_\
> &#xNAN;_&#x77;hitespace-item_ → U+0000, U+000B, or U+000C
>
> _line-break_ → U+000A\
> &#xNAN;_&#x6C;ine-break_ → U+000D\
> &#xNAN;_&#x6C;ine-break_ → U+000D followed by U+000A
>
> _inline-spaces_ → _inline-space_ _inline-spaces?_\
> &#xNAN;_&#x69;nline-space_ → U+0009 or U+0020
>
> _comment_ → **`//`** _comment-text_ _line-break_\
> &#xNAN;_&#x6D;ultiline-comment_ → **`/*`** _multiline-comment-text_ **`*/`**
>
> _comment-text_ → _comment-text-item_ _comment-text?_\
> &#xNAN;_&#x63;omment-text-item_ → Any Unicode scalar value except U+000A or U+000D
>
> _multiline-comment-text_ → _multiline-comment-text-item_ _multiline-comment-text?_\
> &#xNAN;_&#x6D;ultiline-comment-text-item_ → _multiline-comment_\
> &#xNAN;_&#x6D;ultiline-comment-text-item_ → _comment-text-item_\
> &#xNAN;_&#x6D;ultiline-comment-text-item_ → Any Unicode scalar value except **`/*`** or **`*/`**

## [标识符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure#Identifiers)

标识符以大写或小写字母 A 到 Z、下划线 ( `_` )、基础多语言平面中的非组合字母数字 Unicode 字符，或不在私有使用区域的基础多语言平面外的字符开头。在第一个字符之后，数字和组合 Unicode 字符也是允许的。

将以下划线开头的标识符、其第一个参数标签以下划线开头的下标，以及其第一个参数标签以下划线开头的初始化器视为内部，即使它们的声明具有 `public` 访问级别修饰符。此约定允许框架作者标记客户端不能与之交互或依赖的 API 的一部分，尽管某些限制要求声明为公共。此外，以两个下划线开头的标识符保留给 Swift 编译器和标准库。

要将保留字用作标识符，请在它前后放置反引号（\`）。例如， `class` 不是有效的标识符，但 `` `class` `` 是有效的。反引号不被视为标识符的一部分； `` `x` `` 和 `x` 具有相同的含义。

在没有显式参数名称的闭包中，参数被隐式命名为 `$0` ， `$1` ， `$2` 等。这些名称在闭包的范围内是有效的标识符。

编译器为具有属性包装投影的属性合成以美元符号（ `$` ）开头的标识符。您的代码可以与这些标识符交互，但您不能声明带有该前缀的标识符。有关更多信息，请参阅属性章节的 propertyWrapper 部分。

> 标识符的语法
>
> _identifier_ → _identifier-head_ _identifier-characters?_\
> &#xNAN;_&#x69;dentifier_ → **`` ` ``** _identifier-head_ _identifier-characters?_ **`` ` ``**\
> &#xNAN;_&#x69;dentifier_ → _implicit-parameter-name_\
> &#xNAN;_&#x69;dentifier_ → _property-wrapper-projection_\
> &#xNAN;_&#x69;dentifier-list_ → _identifier_ | _identifier_ **`,`** _identifier-list_
>
> _identifier-head_ → Upper- or lowercase letter A through Z\
> &#xNAN;_&#x69;dentifier-head_ → **`_`**\
> &#xNAN;_&#x69;dentifier-head_ → U+00A8, U+00AA, U+00AD, U+00AF, U+00B2–U+00B5, or U+00B7–U+00BA\
> &#xNAN;_&#x69;dentifier-head_ → U+00BC–U+00BE, U+00C0–U+00D6, U+00D8–U+00F6, or U+00F8–U+00FF\
> &#xNAN;_&#x69;dentifier-head_ → U+0100–U+02FF, U+0370–U+167F, U+1681–U+180D, or U+180F–U+1DBF\
> &#xNAN;_&#x69;dentifier-head_ → U+1E00–U+1FFF\
> &#xNAN;_&#x69;dentifier-head_ → U+200B–U+200D, U+202A–U+202E, U+203F–U+2040, U+2054, or U+2060–U+206F\
> &#xNAN;_&#x69;dentifier-head_ → U+2070–U+20CF, U+2100–U+218F, U+2460–U+24FF, or U+2776–U+2793\
> &#xNAN;_&#x69;dentifier-head_ → U+2C00–U+2DFF or U+2E80–U+2FFF\
> &#xNAN;_&#x69;dentifier-head_ → U+3004–U+3007, U+3021–U+302F, U+3031–U+303F, or U+3040–U+D7FF\
> &#xNAN;_&#x69;dentifier-head_ → U+F900–U+FD3D, U+FD40–U+FDCF, U+FDF0–U+FE1F, or U+FE30–U+FE44\
> &#xNAN;_&#x69;dentifier-head_ → U+FE47–U+FFFD\
> &#xNAN;_&#x69;dentifier-head_ → U+10000–U+1FFFD, U+20000–U+2FFFD, U+30000–U+3FFFD, or U+40000–U+4FFFD\
> &#xNAN;_&#x69;dentifier-head_ → U+50000–U+5FFFD, U+60000–U+6FFFD, U+70000–U+7FFFD, or U+80000–U+8FFFD\
> &#xNAN;_&#x69;dentifier-head_ → U+90000–U+9FFFD, U+A0000–U+AFFFD, U+B0000–U+BFFFD, or U+C0000–U+CFFFD\
> &#xNAN;_&#x69;dentifier-head_ → U+D0000–U+DFFFD or U+E0000–U+EFFFD
>
> _identifier-character_ → Digit 0 through 9\
> &#xNAN;_&#x69;dentifier-character_ → U+0300–U+036F, U+1DC0–U+1DFF, U+20D0–U+20FF, or U+FE20–U+FE2F\
> &#xNAN;_&#x69;dentifier-character_ → _identifier-head_\
> &#xNAN;_&#x69;dentifier-characters_ → _identifier-character_ _identifier-characters?_
>
> _implicit-parameter-name_ → **`$`** _decimal-digits_\
> &#xNAN;_&#x70;roperty-wrapper-projection_ → **`$`** _identifier-characters_

## [关键字和标点符号](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure#Keywords-and-Punctuation)

以下关键字是保留的，不能用作标识符，除非使用反引号进行转义，正如上面在标识符中所描述的那样。除 `inout` 、 `var` 和 `let` 之外的关键字可以在函数声明或函数调用中用作参数名，而无需使用反引号进行转义。当成员的名称与关键字相同时，引用该成员时无需使用反引号进行转义，除非在引用成员和使用关键字之间存在歧义——例如， `self` 、 `Type` 和 `Protocol` 在显式成员表达式中具有特殊含义，因此在该上下文中必须使用反引号进行转义。

- 声明中使用的关键字： `associatedtype` 、 `borrowing` 、 `class` 、 `consuming` 、 `deinit` 、 `enum` 、 `extension` 、 `fileprivate` 、 `func` 、 `import` 、 `init` 、 `inout` 、 `internal` 、 `let` 、 `nonisolated` 、 `open` 、 `operator` 、 `private` 、 `precedencegroup` 、 `protocol` 、 `public` 、 `rethrows` 、 `static` 、 `struct` 、 `subscript` 、 `typealias` 和 `var` 。
- 语句中使用的关键字： `break` 、 `case` 、 `catch` 、 `continue` 、 `default` 、 `defer` 、 `do` 、 `else` 、 `fallthrough` 、 `for` 、 `guard` 、 `if` 、 `in` 、 `repeat` 、 `return` 、 `throw` 、 `switch` 、 `where` 和 `while` 。
- 在表达式和类型中使用的关键字: `Any` , `as` , `await` , `catch` , `false` , `is` , `nil` , `rethrows` , `self` , `Self` , `super` , `throw` , `throws` , `true` 和 `try` .
- 在模式中使用的关键字: `_` .
- 以数字标志 ( `#` ) 开头的关键字: `#available` , `#colorLiteral` , `#else` , `#elseif` , `#endif` , `#fileLiteral` , `#if` , `#imageLiteral` , `#keyPath` , `#selector` , `#sourceLocation` , `#unavailable` .

> 注意
>
> 在 Swift 5.9 之前，以下关键词是保留的： `#column` ， `#dsohandle` ， `#error` ， `#fileID` ， `#filePath` ， `#file` ， `#function` ， `#line` ，和 `#warning` 。这些现在被实现为 Swift 标准库中的宏： `column` ， `dsohandle` ， `error(_:)` ， `fileID` ， `filePath` ， `file` ， `function` ， `line` ，和 `warning(_:)` 。

- 特定上下文中保留的关键词： `associativity` 、 `convenience` 、 `didSet` 、 `dynamic` 、 `final` 、 `get` 、 `indirect` 、 `infix` 、 `lazy` 、 `left` 、 `mutating` 、 `none` 、 `nonmutating` 、 `optional` 、 `override` 、 `package` 、 `postfix` 、 `precedence` 、 `prefix` 、 `Protocol` 、 `required` 、 `right` 、 `set` 、 `some` 、 `Type` 、 `unowned` 、 `weak` 和 `willSet` 。在它们出现在语法的上下文之外时，可以作为标识符使用。

以下标记被保留为标点符号，不能用作自定义运算符： `(` , `)` , `{` , `}` , `[` , `]` , `.` , `,` , `:` , `;` , `=` , `@` , `#` , `&` （作为前缀运算符）， `->` , `` ` `` , `?` 以及 `!` （作为后缀运算符）。

## [字面量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure#Literals)

字面量是值的类型（例如数字或字符串）的源代码表示。

以下是字面量的示例：

```swift
42               // Integer literal
3.14159          // Floating-point literal
"Hello, world!"  // String literal
/Hello, .*/      // Regular expression literal
true             // Boolean literal
```

字面量本身没有类型。相反，字面量被解析为具有无限精度，Swift 的类型推断会尝试推断字面量的类型。例如，在声明 `let x: Int8 = 42` 中，Swift 使用显式类型注释 ( `: Int8` ) 推断整数字面量 `42` 的类型是 `Int8` 。如果没有可用的合适类型信息，Swift 会推断字面量的类型是 Swift 标准库中定义的默认字面量类型之一，并列在下面的表格中。当为字面量值指定类型注释时，注释的类型必须是可以从该字面量值实例化的类型。也就是说，该类型必须符合下面表格中列出的 Swift 标准库协议。

| 文字       | 默认类型 | 协议                                                                                                                                                                                                 |
| ---------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 整数       | `Int`    | `ExpressibleByIntegerLiteral`                                                                                                                                                                        |
| 浮点数     | `Double` | `ExpressibleByFloatLiteral`                                                                                                                                                                          |
| 字符串     | `String` | `ExpressibleByStringLiteral` ， `ExpressibleByUnicodeScalarLiteral` 用于仅包含单个 Unicode 标量的字符串字面量， `ExpressibleByExtendedGraphemeClusterLiteral` 用于仅包含单个扩展字符簇的字符串字面量 |
| 正则表达式 | `Regex`  | 无                                                                                                                                                                                                   |
| 布尔值     | `Bool`   | `ExpressibleByBooleanLiteral`                                                                                                                                                                        |

例如，在声明 `let str = "Hello, world"` 中，字符串字面量 `"Hello, world"` 的默认推断类型是 `String` 。同时， `Int8` 符合 `ExpressibleByIntegerLiteral` 协议，因此它可以在声明 `let x: Int8 = 42` 中用于整数字面量 `42` 的类型注释。

> 字面量的语法
>
> _literal_ → _numeric-literal_ | _string-literal_ | _regular-expression-literal_ | _boolean-literal_ | _nil-literal_
>
> _numeric-literal_ → **`-`**_?_ _integer-literal_ | **`-`**_?_ _floating-point-literal_\
> &#xNAN;_&#x62;oolean-literal_ → **`true`** | **`false`**\
> &#xNAN;_&#x6E;il-literal_ → **`nil`**

### [整数字面量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure#Integer-Literals)

整数字面量表示不确定精度的整数值。默认情况下，整数字面量以十进制表示；您可以使用前缀指定其他进制。二进制字面量以 `0b` 开头，八进制字面量以 `0o` 开头，十六进制字面量以 `0x` 开头。

十进制字面量包含数字 `0` 到 `9` 。二进制字面量包含 `0` 和 `1` ，八进制字面量包含 `0` 到 `7` ，而十六进制字面量包含 `0` 到 `9` 以及 `A` 到 `F` 的大写或小写形式。

负整数字面量通过在整数字面量前加上负号 ( `-` ) 来表示，如 `-42` 。

为了可读性，数字之间允许使用下划线 ( `_` )，但它们会被忽略，因此不会影响字面量的值。整数字面量可以以前导零 ( `0` ) 开头，但它们同样会被忽略，不会影响字面量的基数或值。

除非另有说明，整数字面量的默认推断类型是 Swift 标准库类型 `Int` 。Swift 标准库还定义了各种大小的有符号和无符号整数类型，如整数中所述。

> 整数字面量的语法
>
> _integer-literal_ → _binary-literal_\
> &#xNAN;_&#x69;nteger-literal_ → _octal-literal_\
> &#xNAN;_&#x69;nteger-literal_ → _decimal-literal_\
> &#xNAN;_&#x69;nteger-literal_ → _hexadecimal-literal_
>
> _binary-literal_ → **`0b`** _binary-digit_ _binary-literal-characters?_\
> &#xNAN;_&#x62;inary-digit_ → Digit 0 or 1\
> &#xNAN;_&#x62;inary-literal-character_ → _binary-digit_ | **`_`**\
> &#xNAN;_&#x62;inary-literal-characters_ → _binary-literal-character_ _binary-literal-characters?_
>
> _octal-literal_ → **`0o`** _octal-digit_ _octal-literal-characters?_\
> &#xNAN;_&#x6F;ctal-digit_ → Digit 0 through 7\
> &#xNAN;_&#x6F;ctal-literal-character_ → _octal-digit_ | **`_`**\
> &#xNAN;_&#x6F;ctal-literal-characters_ → _octal-literal-character_ _octal-literal-characters?_
>
> _decimal-literal_ → _decimal-digit_ _decimal-literal-characters?_\
> &#xNAN;_&#x64;ecimal-digit_ → Digit 0 through 9\
> &#xNAN;_&#x64;ecimal-digits_ → _decimal-digit_ _decimal-digits?_\
> &#xNAN;_&#x64;ecimal-literal-character_ → _decimal-digit_ | **`_`**\
> &#xNAN;_&#x64;ecimal-literal-characters_ → _decimal-literal-character_ _decimal-literal-characters?_
>
> _hexadecimal-literal_ → **`0x`** _hexadecimal-digit_ _hexadecimal-literal-characters?_\
> &#xNAN;_&#x68;exadecimal-digit_ → Digit 0 through 9, a through f, or A through F\
> &#xNAN;_&#x68;exadecimal-literal-character_ → _hexadecimal-digit_ | **`_`**\
> &#xNAN;_&#x68;exadecimal-literal-characters_ → _hexadecimal-literal-character_ _hexadecimal-literal-characters?_

### [浮点字面量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure#Floating-Point-Literals)

浮点字面量表示不确定精度的浮点值。

默认情况下，浮点字面量以十进制表示（没有前缀），但它们也可以以十六进制表示（带有 `0x` 前缀）。

十进制浮点文字面量由一系列十进制数字组成，后面跟着十进制小数、十进制指数或两者。十进制小数由一个十进制点 ( `.` ) 后跟一系列十进制数字组成。指数由一个大写或小写的 `e` 前缀和一系列十进制数字组成，表示值前面的 `e` 乘以 10 的幂。例如， `1.25e2` 表示 1.25 x 10²，计算结果为 `125.0` 。类似地， `1.25e-2` 表示 1.25 x 10⁻²，计算结果为 `0.0125` 。

十六进制浮点字面量由一个 `0x` 前缀组成，后跟一个可选的十六进制分数，最后是一个十六进制指数。十六进制分数由小数点后跟一系列十六进制数字组成。指数由一个大写或小写的 `p` 前缀后跟一系列十进制数字组成，指示前面的 `p` 乘以 2 的多少次方。例如， `0xFp2` 代表 15 x 2²，计算结果为 `60` 。类似地， `0xFp-2` 代表 15 x 2⁻²，计算结果为 `3.75` 。

负浮点字面量通过在浮点字面量前添加一个负号 ( `-` ) 来表示，如 `-42.5` 。

数字之间允许使用下划线 ( `_` ) 以提高可读性，但它们会被忽略，因此不会影响字面量的值。浮点字面量可以以前导零 ( `0` ) 开头，但它们同样被忽略，不会影响字面量的基数或值。

除非另有说明，浮点文字的默认推断类型是 Swift 标准库类型 `Double` ，该类型表示 64 位浮点数。Swift 标准库还定义了 `Float` 类型，表示 32 位浮点数。

> 浮点字面量的语法
>
> _floating-point-literal_ → _decimal-literal_ _decimal-fraction?_ _decimal-exponent?_\
> &#xNAN;_&#x66;loating-point-literal_ → _hexadecimal-literal_ _hexadecimal-fraction?_ _hexadecimal-exponent_
>
> _decimal-fraction_ → **`.`** _decimal-literal_\
> &#xNAN;_&#x64;ecimal-exponent_ → _floating-point-e_ _sign?_ _decimal-literal_
>
> _hexadecimal-fraction_ → **`.`** _hexadecimal-digit_ _hexadecimal-literal-characters?_\
> &#xNAN;_&#x68;exadecimal-exponent_ → _floating-point-p_ _sign?_ _decimal-literal_
>
> _floating-point-e_ → **`e`** | **`E`**\
> &#xNAN;_&#x66;loating-point-p_ → **`p`** | **`P`**\
> &#xNAN;_&#x73;ign_ → **`+`** | **`-`**

### [字符串字面量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure#String-Literals)

字符串字面量是由引号包围的一系列字符。单行字符串字面量由双引号包围，具有以下形式：

```swift
"<#characters#>"
```

字符串字面量不能包含未转义的双引号 ( `"` )、未转义的反斜杠 ( `\` )、回车符或换行符。

多行字符串字面量由三个双引号包围，具有以下形式：

```swift
"""
<#characters#>
"""
```

与单行字符串字面量不同，多行字符串字面量可以包含未转义的双引号 ( `"` )、回车符和换行符。它不能包含三个未转义的双引号相邻。

在开始多行字符串字面量的 `"""` 之后的换行符不是字符串的一部分。在结束字面量的 `"""` 之前的换行符也不是字符串的一部分。要创建一个以换行符开头或结尾的多行字符串字面量，请在其第一行或最后一行写一个空行。

多行字符串字面量可以使用空格和制表符的任意组合进行缩进；该缩进不包含在字符串中。结束字面量的 `"""` 决定了缩进：字面量中的每一行非空白行必须以与闭合的 `"""` 前出现的完全相同的缩进开始；制表符和空格之间没有转换。您可以在该缩进后包含额外的空格和制表符；这些空格和制表符将出现在字符串中。

多行字符串字面量中的换行符会被规范化为使用换行符字符。即使您的源文件中混合了回车符和换行符，字符串中的所有换行符也将是相同的。

在多行字符串字面量中，行末写入反斜杠 ( `\` ) 会从字符串中省略该换行符。在反斜杠和换行符之间的任何空白也将被省略。您可以使用此语法在源代码中强制换行多行字符串字面量，而不改变结果字符串的值。

可以使用以下转义序列在单行和多行形式的字符串字面量中包含特殊字符：

- 空字符 ( `\0` )
- 反斜杠 ( `\\` )
- 水平制表符 ( )
- 换行 ( )
- 回车 ( )
- 双引号 ( `\"` )
- 单引号 ( `\'` )
- Unicode 标量 ( `\u{` n `}` )，其中 n 是一个具有一到八位数字的十六进制数

表达式的值可以通过在反斜杠后将表达式放在括号中（ `\` ）插入到字符串字面量中。插入的表达式可以包含字符串字面量，但不能包含未转义的反斜杠、回车符或换行符。

例如，以下所有字符串字面量具有相同的值：

```swift
"1 2 3"
"1 2 \("3")"
"1 2 \(3)"
"1 2 \(1 + 2)"
let x = 3; "1 2 \(x)"
```

由扩展分隔符限定的字符串是被引号包围并且用一个或多个数字符号（ `#` ）平衡的一系列字符。由扩展分隔符限定的字符串具有以下形式：

```swift
#"<#characters#>"#


#"""
<#characters#>
"""#
```

由扩展分隔符限定的字符串中的特殊字符在结果字符串中以普通字符的形式出现，而不是以特殊字符的形式出现。您可以使用扩展分隔符来创建具有通常会产生特殊效果的字符的字符串，例如生成字符串插值、启动转义序列或终止字符串。

以下示例显示了一个字符串字面量和一个由扩展分隔符限定的字符串，它们创建了等效的字符串值：

```swift
let string = #"\(x) \ " \u{2603}"#
let escaped = "\\(x) \\ \" \\u{2603}"
print(string)
// Prints "\(x) \ " \u{2603}"
print(string == escaped)
// Prints "true"
```

如果您使用多个数字符号来形成由扩展分隔符分隔的字符串，请不要在数字符号之间放置空格：

```swift
print(###"Line 1\###nLine 2"###) // OK
print(# # #"Line 1\# # #nLine 2"# # #) // Error
```

使用扩展定界符创建的多行字符串字面量具有与常规多行字符串字面量相同的缩进要求。

字符串字面量的默认推断类型是 `String` 。有关 `String` 类型的更多信息，请参见字符串和字符以及 `String` 。

通过 `+` 运算符连接的字符串字面量在编译时连接。例如，下面示例中的 `textA` 和 `textB` 的值是相同的——不执行运行时连接。

```swift
let textA = "Hello " + "world"
let textB = "Hello world"
```

> 字符串字面量的语法
>
> _string-literal_ → _static-string-literal_ | _interpolated-string-literal_
>
> _string-literal-opening-delimiter_ → _extended-string-literal-delimiter?_ **`"`**\
> &#xNAN;_&#x73;tring-literal-closing-delimiter_ → **`"`** _extended-string-literal-delimiter?_
>
> _static-string-literal_ → _string-literal-opening-delimiter_ _quoted-text?_ _string-literal-closing-delimiter_\
> &#xNAN;_&#x73;tatic-string-literal_ → _multiline-string-literal-opening-delimiter_ _multiline-quoted-text?_ _multiline-string-literal-closing-delimiter_
>
> _multiline-string-literal-opening-delimiter_ → _extended-string-literal-delimiter?_ **`"""`**\
> &#xNAN;_&#x6D;ultiline-string-literal-closing-delimiter_ → **`"""`** _extended-string-literal-delimiter?_\
> &#xNAN;_&#x65;xtended-string-literal-delimiter_ → **`#`** _extended-string-literal-delimiter?_
>
> _quoted-text_ → _quoted-text-item_ _quoted-text?_\
> &#xNAN;_&#x71;uoted-text-item_ → _escaped-character_\
> &#xNAN;_&#x71;uoted-text-item_ → Any Unicode scalar value except **`"`**, **`\`**, U+000A, or U+000D
>
> _multiline-quoted-text_ → _multiline-quoted-text-item_ _multiline-quoted-text?_\
> &#xNAN;_&#x6D;ultiline-quoted-text-item_ → _escaped-character_\
> &#xNAN;_&#x6D;ultiline-quoted-text-item_ → Any Unicode scalar value except **`\`**\
> &#xNAN;_&#x6D;ultiline-quoted-text-item_ → _escaped-newline_
>
> _interpolated-string-literal_ → _string-literal-opening-delimiter_ _interpolated-text?_ _string-literal-closing-delimiter_\
> &#xNAN;_&#x69;nterpolated-string-literal_ → _multiline-string-literal-opening-delimiter_ _multiline-interpolated-text?_ _multiline-string-literal-closing-delimiter_
>
> _interpolated-text_ → _interpolated-text-item_ _interpolated-text?_\
> &#xNAN;_&#x69;nterpolated-text-item_ → **`\(`** _expression_ **`)`** | _quoted-text-item_
>
> _multiline-interpolated-text_ → _multiline-interpolated-text-item_ _multiline-interpolated-text?_\
> &#xNAN;_&#x6D;ultiline-interpolated-text-item_ → **`\(`** _expression_ **`)`** | _multiline-quoted-text-item_
>
> _escape-sequence_ → **`\`** _extended-string-literal-delimiter_\
> &#xNAN;_&#x65;scaped-character_ → _escape-sequence_ **`0`** | _escape-sequence_ **`\`** | _escape-sequence_ **`t`** | _escape-sequence_ **`n`** | _escape-sequence_ **`r`** | _escape-sequence_ **`"`** | _escape-sequence_ **`'`**\
> &#xNAN;_&#x65;scaped-character_ → _escape-sequence_ **`u`** **`{`** _unicode-scalar-digits_ **`}`**\
> &#xNAN;_&#x75;nicode-scalar-digits_ → Between one and eight hexadecimal digits
>
> _escaped-newline_ → _escape-sequence_ _inline-spaces?_ _line-break_

### [正则表达式字面量](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure#Regular-Expression-Literals)

正则表达式字面量是一系列字符，以斜杠（ `/` ）为边界，具有以下形式：

```swift
/<#regular expression#>/
```

正则表达式字面量不得以未转义的制表符或空格开头，并且不能包含未转义的斜杠 ( `/` )、回车符或换行符。

在正则表达式字面量中，反斜杠被理解为该正则表达式的一部分，而不仅仅是像字符串字面量中的转义字符。它表示后面的特殊字符应被字面解释，或者后面的非特殊字符应以特殊方式解释。例如， `/\(/` 匹配一个左括号， `/\d/` 匹配一个数字。

用扩展定界符分隔的正则表达式字面量是由斜杠（ `/` ）和一个或多个井号（ `#` ）所包围的一系列字符。用扩展定界符分隔的正则表达式字面量具有以下形式：

```swift
#/<#regular expression#>/#


#/
<#regular expression#>
/#
```

一个使用扩展定界符的正则表达式字面量可以以未转义的空格或制表符开头，包含未转义的斜杠 ( `/` )，并跨越多行。对于多行正则表达式字面量，开始定界符必须放在行末，结束定界符必须单独放在一行上。在多行正则表达式字面量中，扩展正则表达式语法默认是启用的——具体来说，空格会被忽略，注释是允许的。

如果您使用多个数字符号来形成一个由扩展定界符限定的正则表达式字面量，请勿在数字符号之间放置空格：

```swift
let regex1 = ##/abc/##       // OK
let regex2 = # #/abc/# #     // Error
```

如果您需要创建一个空的正则表达式字面量，必须使用扩展分隔符语法。

> 正则表达式字面量的语法
>
> _regular-expression-literal_ → _regular-expression-literal-opening-delimiter_ _regular-expression_ _regular-expression-literal-closing-delimiter_\
> &#xNAN;_&#x72;egular-expression_ → Any regular expression
>
> _regular-expression-literal-opening-delimiter_ → _extended-regular-expression-literal-delimiter?_ **`/`**\
> &#xNAN;_&#x72;egular-expression-literal-closing-delimiter_ → **`/`** _extended-regular-expression-literal-delimiter?_
>
> _extended-regular-expression-literal-delimiter_ → **`#`** _extended-regular-expression-literal-delimiter?_

## [运算符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/lexicalstructure#Operators)

Swift 标准库定义了许多供您使用的运算符，其中许多在基本运算符和高级运算符中进行了讨论。本节描述了可以用于定义自定义运算符的字符。

自定义运算符可以以 ASCII 字符 `/` , `=` , `-` , `+` , `!` , `*` , `%` , `<` , `>` , `&` , `|` , `^` , `?` , 或 `~` 开头，或者以下面语法中定义的 Unicode 字符开头（包括来自数学运算符、杂项符号和装饰符 Unicode 块的字符等）。在第一个字符之后，也允许组合 Unicode 字符。

您还可以定义以点 ( `.` ) 开头的自定义运算符。这些运算符可以包含额外的点。例如， `.+.` 被视为一个单一的运算符。如果运算符不以点开头，则它不能在其他地方包含点。例如， `+.+` 被视为 `+` 运算符后跟 `.+` 运算符。

虽然您可以定义包含问号 ( `?` ) 的自定义运算符，但它们不能仅由一个问号字符组成。此外，尽管运算符可以包含感叹号 ( `!` )，后缀运算符不能以问号或感叹号开头。

> 注意
>
> 令牌 `=` , `->` , `//` , `/*` , `*/` , `.` , 前缀运算符 `<` , `&` , 和 `?` , 中缀运算符 `?` , 以及后缀运算符 `>` , `!` , 和 `?` 是保留的。这些令牌不能被重载，也不能用作自定义运算符。

操作符周围的空格用于确定操作符是用作前缀操作符、后缀操作符还是中缀操作符。此行为遵循以下规则：

- 如果操作符两侧都有空格或两侧都没有空格，则将其视为中缀操作符。例如， `+++` 操作符在 `a+++b` 和 `a +++ b` 中被视为中缀操作符。
- 如果操作符左侧只有空格，则将其视为前缀一元操作符。例如， `+++` 操作符在 `a +++b` 中被视为前缀一元操作符。
- 如果操作符右侧只有空格，则将其视为后缀一元操作符。例如， `+++` 操作符在 `a+++ b` 中被视为后缀一元操作符。
- 如果一个运算符左侧没有空格，但紧接着有一个点 ( `.` )，它会被当作后缀一元运算符。举例来说， `+++` 运算符在 `a+++.b` 中被视为后缀一元运算符 ( `a+++ .b` 而不是 `a +++ .b` )。

根据这些规则，在运算符之前的字符 `(` 、 `[` 和 `{` ，运算符之后的字符 `)` 、 `]` 和 `}` ，以及字符 `,` 、 `;` 和 `:` 也被视为空格。

如果 `!` 或 `?` 预定义运算符左侧没有空格，那么它将被视为后缀运算符，无论右侧是否有空格。要将 `?` 作为可选链运算符使用，左侧不能有空格。要将其用于三元条件 ( `?` `:` ) 运算符，必须在两侧都有空格。

如果一个中缀运算符的参数之一是正则表达式字面量，那么该运算符的两侧必须有空格。

在某些结构中，具有前导 `<` 或 `>` 的操作符可能会被拆分为两个或多个标记。其余部分以相同方式处理，并可能再次拆分。因此，您无需添加空格以消除像 `Dictionary<String, Array<Int>>` 中的闭合 `>` 字符之间的歧义。在此示例中，闭合 `>` 字符不被视为一个单一标记，因此不会被误解为位移 `>>` 操作符。

要了解如何定义新的自定义运算符，请参阅自定义运算符和运算符声明。要了解如何重载现有运算符，请参阅运算符方法。

运算符的语法

_operator_ → _operator-head_ _operator-characters?_\
&#xNAN;_&#x6F;perator_ → _dot-operator-head_ _dot-operator-characters_

_operator-head_ → **`/`** | **`=`** | **`-`** | **`+`** | **`!`** | **`*`** | **`%`** | **`<`** | **`>`** | **`&`** | **`|`** | **`^`** | **`~`** | **`?`**\
&#xNAN;_&#x6F;perator-head_ → U+00A1–U+00A7\
&#xNAN;_&#x6F;perator-head_ → U+00A9 or U+00AB\
&#xNAN;_&#x6F;perator-head_ → U+00AC or U+00AE\
&#xNAN;_&#x6F;perator-head_ → U+00B0–U+00B1\
&#xNAN;_&#x6F;perator-head_ → U+00B6, U+00BB, U+00BF, U+00D7, or U+00F7\
&#xNAN;_&#x6F;perator-head_ → U+2016–U+2017\
&#xNAN;_&#x6F;perator-head_ → U+2020–U+2027\
&#xNAN;_&#x6F;perator-head_ → U+2030–U+203E\
&#xNAN;_&#x6F;perator-head_ → U+2041–U+2053\
&#xNAN;_&#x6F;perator-head_ → U+2055–U+205E\
&#xNAN;_&#x6F;perator-head_ → U+2190–U+23FF\
&#xNAN;_&#x6F;perator-head_ → U+2500–U+2775\
&#xNAN;_&#x6F;perator-head_ → U+2794–U+2BFF\
&#xNAN;_&#x6F;perator-head_ → U+2E00–U+2E7F\
&#xNAN;_&#x6F;perator-head_ → U+3001–U+3003\
&#xNAN;_&#x6F;perator-head_ → U+3008–U+3020\
&#xNAN;_&#x6F;perator-head_ → U+3030

_operator-character_ → _operator-head_\
&#xNAN;_&#x6F;perator-character_ → U+0300–U+036F\
&#xNAN;_&#x6F;perator-character_ → U+1DC0–U+1DFF\
&#xNAN;_&#x6F;perator-character_ → U+20D0–U+20FF\
&#xNAN;_&#x6F;perator-character_ → U+FE00–U+FE0F\
&#xNAN;_&#x6F;perator-character_ → U+FE20–U+FE2F\
&#xNAN;_&#x6F;perator-character_ → U+E0100–U+E01EF\
&#xNAN;_&#x6F;perator-characters_ → _operator-character_ _operator-characters?_

_dot-operator-head_ → **`.`**\
&#xNAN;_&#x64;ot-operator-character_ → **`.`** | _operator-character_\
&#xNAN;_&#x64;ot-operator-characters_ → _dot-operator-character_ _dot-operator-characters?_

_infix-operator_ → _operator_\
&#xNAN;_&#x70;refix-operator_ → _operator_\
&#xNAN;_&#x70;ostfix-operator_ → _operator_
