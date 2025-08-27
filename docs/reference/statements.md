# 语句

> 分组表达式并控制执行流程。

在 Swift 中，有三种类型的语句：简单语句、编译器控制语句和控制流语句。简单语句是最常见的，由表达式或声明组成。编译器控制语句允许程序改变编译器行为的某些方面，包括条件编译块和行控制语句。

控制流语句用于控制程序中的执行流程。Swift 中有几种类型的控制流语句，包括循环语句、分支语句和控制转移语句。循环语句允许一段代码被重复执行，分支语句仅在满足特定条件时执行某段代码，而控制转移语句提供了一种改变代码执行顺序的方法。此外，Swift 提供了一个 `do` 语句来引入作用域，并捕获和处理错误，以及一个 `defer` 语句在当前作用域退出之前运行清理操作。

分号 ( `;` ) 可以选择性地出现在任何语句之后，并用于分隔出现在同一行上的多个语句。

Grammar of a statement

_statement_ → _expression_ **`;`**_?_\
&#xNAN;_&#x73;tatement_ → _declaration_ **`;`**_?_\
&#xNAN;_&#x73;tatement_ → _loop-statement_ **`;`**_?_\
&#xNAN;_&#x73;tatement_ → _branch-statement_ **`;`**_?_\
&#xNAN;_&#x73;tatement_ → _labeled-statement_ **`;`**_?_\
&#xNAN;_&#x73;tatement_ → _control-transfer-statement_ **`;`**_?_\
&#xNAN;_&#x73;tatement_ → _defer-statement_ **`;`**_?_\
&#xNAN;_&#x73;tatement_ → _do-statement_ **`;`**_?_\
&#xNAN;_&#x73;tatement_ → _compiler-control-statement_\
&#xNAN;_&#x73;tatements_ → _statement_ _statements?_

## [循环语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Loop-Statements)

循环语句允许根据循环中指定的条件重复执行一段代码。Swift 有三种循环语句：一个 `for` - `in` 语句，一个 `while` 语句，以及一个 `repeat` - `while` 语句。

循环语句中的控制流可以通过 `break` 语句和 `continue` 语句进行更改，下面在 Break Statement 和 Continue Statement 中进行了讨论。

Grammar of a loop statement

_loop-statement_ → _for-in-statement_\
&#xNAN;_&#x6C;oop-statement_ → _while-statement_\
&#xNAN;_&#x6C;oop-statement_ → _repeat-while-statement_

### [For-In 语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#For-In-Statement)

一个 `for` - `in` 语句允许对符合 `Sequence` 协议的集合（或任何类型）中的每个项目执行一次代码块。

一个 `for` - `in` 语句具有以下形式：

```swift
for <#item#> in <#collection#> {
   <#statements#>
}
```

在集合表达式上调用 `makeIterator()` 方法以获得迭代器类型的值——即符合 `IteratorProtocol` 协议的类型。程序通过在迭代器上调用 `next()` 方法开始执行循环。如果返回的值不是 `nil` ，则将其分配给项模式，程序执行语句，然后在循环开始处继续执行。否则，程序不执行赋值或执行语句，并且完成了 `for` - `in` 语句的执行。

Grammar of a for-in statement

_for-in-statement_ → **`for`** **`case`**_?_ _pattern_ **`in`** _expression_ _where-clause?_ _code-block_

### [while 语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#While-Statement)

一个 `while` 语句允许代码块重复执行，只要条件保持为真。

一个 `while` 语句具有以下形式：

```swift
while <#condition#> {
   <#statements#>
}
```

一个 `while` 语句的执行如下：

1. 条件被评估。
   如果 `true` ，执行继续到步骤 2。如果 `false` ，程序完成执行 `while` 语句。
2. 程序执行语句，执行返回到步骤 1。

因为条件的值在语句执行之前被评估，所以在 `while` 语句中的语句可以执行零次或多次。

条件的值必须是 `Bool` 类型或桥接到 `Bool` 的类型。条件也可以是可选绑定声明，如可选绑定中所讨论的。

Grammar of a while statement

_while-statement_ → **`while`** _condition-list_ _code-block_

_condition-list_ → _condition_ | _condition_ **`,`** _condition-list_\
&#xNAN;_&#x63;ondition_ → _expression_ | _availability-condition_ | _case-condition_ | _optional-binding-condition_

_case-condition_ → **`case`** _pattern_ _initializer_\
&#xNAN;_&#x6F;ptional-binding-condition_ → **`let`** _pattern_ _initializer?_ | **`var`** _pattern_ _initializer?_

### [重复-当语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Repeat-While-Statement)

一个 `repeat` - `while` 语句允许一段代码在条件保持为真时执行一次或多次。

一个 `repeat` - `while` 语句具有以下形式：

```swift
repeat {
   <#statements#>
} while <#condition#>
```

一个 `repeat` - `while` 语句的执行如下：

1. 程序执行语句，并继续执行到步骤 2。
2. 条件被评估。

   如果 `true` ，执行返回到步骤 1。如果 `false` ，程序完成执行 `repeat` - `while` 语句。

因为条件的值在语句执行后被评估， `repeat` - `while` 语句中的语句至少执行一次。

条件的值必须是 `Bool` 类型或桥接到 `Bool` 的类型。

Grammar of a repeat-while statement

_repeat-while-statement_ → **`repeat`** _code-block_ **`while`** _expression_

## [分支语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Branch-Statements)

分支语句允许程序根据一个或多个条件的值执行某些代码部分。分支语句中指定的条件值控制程序如何分支，因此控制执行哪个代码块。Swift 有三种分支语句：一个 `if` 语句，一个 `guard` 语句和一个 `switch` 语句。

在 `if` 语句或 `switch` 语句中的控制流可以通过 `break` 语句进行更改，具体内容在下面的 Break Statement 中讨论。

Grammar of a branch statement

_branch-statement_ → _if-statement_\
&#xNAN;_&#x62;ranch-statement_ → _guard-statement_\
&#xNAN;_&#x62;ranch-statement_ → _switch-statement_

### [如果语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#If-Statement)

一个 `if` 语句用于根据一个或多个条件的评估执行代码。

一个 `if` 语句有两种基本形式。在每种形式中，开括号和闭括号都是必需的。

第一种形式仅在条件为真时执行代码，具有以下形式：

```swift
if <#condition#> {
   <#statements#>
}
```

`if` 语句的第二种形式提供了一个额外的 else 子句（由 `else` 关键字引入），用于在条件为真时执行一部分代码，在相同条件为假时执行另一部分代码。当存在单个 else 子句时， `if` 语句具有以下形式：

```swift
if <#condition#> {
   <#statements to execute if condition is true#>
} else {
   <#statements to execute if condition is false#>
}
```

`if` 语句的 else 子句可以包含另一个 `if` 语句以测试多个条件。以这种方式链接在一起的 `if` 语句具有以下形式：

```swift
if <#condition 1#> {
   <#statements to execute if condition 1 is true#>
} else if <#condition 2#> {
   <#statements to execute if condition 2 is true#>
} else {
   <#statements to execute if both conditions are false#>
}
```

`if` 语句中任何条件的值必须是 `Bool` 类型或桥接到 `Bool` 的类型。条件也可以是可选绑定声明，如可选绑定中所讨论的。

Grammar of an if statement

_if-statement_ → **`if`** _condition-list_ _code-block_ _else-clause?_\
&#xNAN;_&#x65;lse-clause_ → **`else`** _code-block_ | **`else`** _if-statement_

### [Guard Statement](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Guard-Statement)

A `guard` 语句用于在一个或多个条件不满足时将程序控制转移出一个作用域。

一个 `guard` 语句具有以下形式：

```swift
guard <#condition#> else {
   <#statements#>
}
```

在 `guard` 语句中的任何条件的值必须是 `Bool` 类型或与 `Bool` 相关联的类型。条件还可以是可选绑定声明，如在可选绑定中讨论的。

在 `guard` 语句条件中从可选绑定声明分配值的任何常量或变量可以在 guard 语句的封闭作用域的其余部分中使用。

`guard` 语句的 `else` 子句是必需的，必须调用具有 `Never` 返回类型的函数，或者使用以下语句之一将程序控制转移到 guard 语句的封闭作用域之外：

- `return`
- `break`
- `continue`
- `throw`

控制转移语句在下面的控制转移语句中讨论。有关具有 `Never` 返回类型的函数的更多信息，请参见从不返回的函数。

Grammar of a guard statement

_guard-statement_ → **`guard`** _condition-list_ **`else`** _code-block_

### [开关语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Switch-Statement)

A `switch` 语句允许根据控制表达式的值执行特定代码块。

一个 `switch` 语句具有以下形式：

```swift
switch <#control expression#> {
case <#pattern 1#>:
    <#statements#>
case <#pattern 2#> where <#condition#>:
    <#statements#>
case <#pattern 3#> where <#condition#>,
    <#pattern 4#> where <#condition#>:
    <#statements#>
default:
    <#statements#>
}
```

`switch` 语句的控制表达式会被评估，然后与每个案例中指定的模式进行比较。如果找到匹配，程序会执行该案例范围内列出的语句。每个案例的范围不能是空的。因此，您必须在每个案例标签的冒号 ( `:` ) 后面至少包含一个语句。如果您不打算在匹配案例的主体中执行任何代码，请使用单个 `break` 语句。

您代码可以分支的表达式值非常灵活。例如，除了标量类型的值，如整数和字符，您的代码还可以基于任何类型的值进行分支，包括浮点数、字符串、元组、自定义类的实例和可选值。控制表达式的值甚至可以与枚举中的案例值进行匹配，并检查是否包含在指定的值范围内。有关如何在 `switch` 语句中使用这些不同类型的值的示例，请参见控制流中的开关。

一个 `switch` 语句可以选择性地在每个模式后包含一个 `where` 子句。一个 where 子句由 `where` 关键字引入，后跟一个表达式，旨在在将模式视为与控制表达式匹配之前提供额外的条件。如果存在 `where` 子句，则只有当控制表达式的值与该案例的模式之一匹配，并且 `where` 子句的表达式求值为 `true` 时，相关案例中的语句才会执行。例如，下面示例中的控制表达式只有在它是一个包含两个相同值元素的元组时才与该案例匹配，例如 `(1, 1)` 。

```swift
case let (x, y) where x == y:
```

如上例所示，case 中的模式也可以使用 `let` 关键字绑定常量（它们也可以使用 `var` 关键字绑定变量）。这些常量（或变量）可以在相应的 `where` 子句中以及在 case 的作用域内的其余代码中引用。如果 case 包含多个与控制表达式匹配的模式，则所有模式必须包含相同的常量或变量绑定，并且每个绑定的变量或常量在所有 case 的模式中必须具有相同的类型。

一个 `switch` 语句还可以包含一个默认 case，由 `default` 关键字引入。默认 case 中的代码仅在没有其他 case 匹配控制表达式时执行。一个 `switch` 语句只能包含一个默认 case，该默认 case 必须出现在 `switch` 语句的末尾。

尽管模式匹配操作的实际执行顺序，特别是情况下模式的评估顺序，未指定，但在 `switch` 语句中的模式匹配行为就像评估是按照源码顺序执行的——也就是它们在源代码中出现的顺序。因此，如果多个 case 包含评估为相同值的模式，从而能够匹配控制表达式的值，程序仅会按源码顺序执行第一个匹配 case 中的代码。

[**Switch 语句必须是穷举的**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Switch-Statements-Must-Be-Exhaustive)

在 Swift 中，控制表达式类型的每个可能值必须匹配至少一个 case 的模式值。当这显然不可行时（例如，当控制表达式的类型为 `Int` 时），可以包含一个默认 case 来满足要求。

[**处理未来枚举 case**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Switching-Over-Future-Enumeration-Cases)

非冻结枚举是一种特殊类型的枚举，它可能会在将来的某个时候增加新的枚举案例——即使在您编译并发布应用后。切换到非冻结枚举需要额外的考虑。当库的作者将枚举标记为非冻结时，他们保留添加新枚举案例的权利，任何与该枚举交互的代码都必须能够处理那些未来的案例，而无需重新编译。以库演化模式编译的代码、Swift 标准库中的代码、Apple 框架的 Swift 覆盖以及 C 和 Objective-C 代码可以声明非冻结枚举。有关冻结和非冻结枚举的信息，请参见 frozen。

在切换非冻结枚举值时，您始终需要包含一个默认情况，即使枚举的每个情况已经有相应的 switch 情况。您可以将 `@unknown` 属性应用于默认情况，这表示默认情况应仅匹配将来添加的枚举情况。如果默认情况匹配任何在编译时已知的枚举情况，Swift 会产生警告。此未来警告通知您库的作者向枚举中添加了一个没有相应 switch 情况的新情况。

以下示例切换了 Swift 标准库的 `Mirror.AncestorRepresentation` 枚举的所有三个现有案例。如果将来添加其他案例，编译器会生成警告，指示您需要更新 switch 语句以考虑新案例。

```swift
let representation: Mirror.AncestorRepresentation = .generated
switch representation {
case .customized:
    print("Use the nearest ancestor’s implementation.")
case .generated:
    print("Generate a default mirror for all ancestor classes.")
case .suppressed:
    print("Suppress the representation of all ancestor classes.")
@unknown default:
    print("Use a representation that was unknown when this code was compiled.")
}
// Prints "Generate a default mirror for all ancestor classes."
```

[**执行不会隐式穿透案例**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Execution-Does-Not-Fall-Through-Cases-Implicitly)

在匹配案例中的代码执行完毕后，程序将退出 `switch` 语句。程序执行不会继续或“穿透”到下一个案例或默认案例。也就是说，如果您希望执行从一个案例继续到下一个案例，请在您希望执行继续的案例中显式包含一个 `fallthrough` 语句，该语句仅由 `fallthrough` 关键字组成。有关 `fallthrough` 语句的更多信息，请参见下面的 Fallthrough 语句。

Grammar of a switch statement

_switch-statement_ → **`switch`** _expression_ **`{`** _switch-cases?_ **`}`**\
&#xNAN;_&#x73;witch-cases_ → _switch-case_ _switch-cases?_\
&#xNAN;_&#x73;witch-case_ → _case-label_ _statements_\
&#xNAN;_&#x73;witch-case_ → _default-label_ _statements_\
&#xNAN;_&#x73;witch-case_ → _conditional-switch-case_

_case-label_ → _attributes?_ **`case`** _case-item-list_ **`:`**\
&#xNAN;_&#x63;ase-item-list_ → _pattern_ _where-clause?_ | _pattern_ _where-clause?_ **`,`** _case-item-list_\
&#xNAN;_&#x64;efault-label_ → _attributes?_ **`default`** **`:`**

_where-clause_ → **`where`** _where-expression_\
&#xNAN;_&#x77;here-expression_ → _expression_

_conditional-switch-case_ → _switch-if-directive-clause_ _switch-elseif-directive-clauses?_ _switch-else-directive-clause?_ _endif-directive_\
&#xNAN;_&#x73;witch-if-directive-clause_ → _if-directive_ _compilation-condition_ _switch-cases?_\
&#xNAN;_&#x73;witch-elseif-directive-clauses_ → _elseif-directive-clause_ _switch-elseif-directive-clauses?_\
&#xNAN;_&#x73;witch-elseif-directive-clause_ → _elseif-directive_ _compilation-condition_ _switch-cases?_\
&#xNAN;_&#x73;witch-else-directive-clause_ → _else-directive_ _switch-cases?_

## [带标签的语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Labeled-Statement)

您可以在循环语句、 `if` 语句、 `switch` 语句或 `do` 语句前加上语句标签，语句标签由标签名称后面紧跟一个冒号 (:) 组成。使用语句标签与 `break` 和 `continue` 语句，以明确您希望如何在循环语句或 `switch` 语句中改变控制流，如下面的 Break Statement 和 Continue Statement 中所讨论的。

带标签语句的作用域是紧随其后的整个语句。您可以嵌套带标签的语句，但每个语句标签的名称必须是唯一的。

有关更多信息以及如何使用语句标签的示例，请参见控制流中的带标签语句。

Grammar of a labeled statement

_labeled-statement_ → _statement-label_ _loop-statement_\
&#xNAN;_&#x6C;abeled-statement_ → _statement-label_ _if-statement_\
&#xNAN;_&#x6C;abeled-statement_ → _statement-label_ _switch-statement_\
&#xNAN;_&#x6C;abeled-statement_ → _statement-label_ _do-statement_

_statement-label_ → _label-name_ **`:`**\
&#xNAN;_&#x6C;abel-name_ → _identifier_

## [控制转移语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Control-Transfer-Statements)

控制转移语句可以通过无条件地将程序控制从一段代码转移到另一段代码来改变程序中代码的执行顺序。Swift 有五个控制转移语句：一个 `break` 语句，一个 `continue` 语句，一个 `fallthrough` 语句，一个 `return` 语句，以及一个 `throw` 语句。

Grammar of a control transfer statement

_control-transfer-statement_ → _break-statement_\
&#xNAN;_&#x63;ontrol-transfer-statement_ → _continue-statement_\
&#xNAN;_&#x63;ontrol-transfer-statement_ → _fallthrough-statement_\
&#xNAN;_&#x63;ontrol-transfer-statement_ → _return-statement_\
&#xNAN;_&#x63;ontrol-transfer-statement_ → _throw-statement_

### [中断语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Break-Statement)

一个 `break` 语句结束循环、一个 `if` 语句或一个 `switch` 语句的程序执行。一个 `break` 语句可以只包含 `break` 关键字，或者可以包含 `break` 关键字后面跟着一个语句标签的名称，如下所示。

```swift
break
break <#label name#>
```

当一个 `break` 语句后面跟着一个语句标签的名称时，它结束由该标签命名的循环、 `if` 语句或 `switch` 语句的程序执行。

当一个 `break` 语句后面没有跟随语句标签的名称时，它会结束 `switch` 语句或其所在的最内层封闭循环语句的程序执行。您不能使用无标签的 `break` 语句来跳出 `if` 语句。

在这两种情况下，程序控制会转移到包含循环或 `switch` 语句后的第一行代码，如果有的话。

有关如何使用 `break` 语句的示例，请参见控制流中的 Break 和 Labeled Statements。

Grammar of a break statement

_break-statement_ → **`break`** _label-name?_

### [Continue Statement](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Continue-Statement)

一个 `continue` 语句结束当前循环语句的执行，但并不会停止循环语句的执行。一个 `continue` 语句可以仅由 `continue` 关键字组成，或者可以由 `continue` 关键字后接语句标签名称组成，如下所示。

```swift
continue
continue <#label name#>
```

当 `continue` 语句后面跟着语句标签的名称时，它结束了由该标签命名的循环语句当前迭代的程序执行。

当 `continue` 语句后面没有跟着语句标签的名称时，它结束了发生该语句的最内层封闭循环语句当前迭代的程序执行。

在这两种情况下，程序控制随后转移到封闭循环语句的条件。

在 `for` 语句中，增量表达式在 `continue` 语句执行后仍然会被评估，因为增量表达式在循环体执行后被评估。

有关如何使用 `continue` 语句的示例，请参见控制流中的 Continue 和 Labeled 语句。

Grammar of a continue statement

_continue-statement_ → **`continue`** _label-name?_

### [掉落语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Fallthrough-Statement)

一个 `fallthrough` 语句由 `fallthrough` 关键字组成，仅在 `switch` 语句的 case 块中出现。一个 `fallthrough` 语句使程序的执行从 `switch` 语句中的一个 case 继续到下一个 case。即使 case 标签的模式与 `switch` 语句的控制表达式的值不匹配，程序的执行仍继续到下一个 case。

一个 `fallthrough` 语句可以出现在 `switch` 语句的任何地方，不仅仅是作为 case 块的最后一个语句，但它不能在最后的 case 块中使用。它也不能将控制转移到包含值绑定模式的 case 块中。

有关如何在 `switch` 语句中使用 `fallthrough` 语句的示例，请参见控制流中的控制转移语句。

Grammar of a fallthrough statement

_fallthrough-statement_ → **`fallthrough`**

### [返回语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Return-Statement)

一个 `return` 语句发生在函数或方法定义的主体中，并导致程序执行返回到调用的函数或方法。程序执行在函数或方法调用后立即继续。

一个 `return` 语句可以仅由 `return` 关键字组成，或者可以由 `return` 关键字后跟一个表达式组成，如下所示。

```swift
return
return <#expression#>
```

当一个 `return` 语句后跟一个表达式时，表达式的值会返回给调用的函数或方法。如果表达式的值与函数或方法声明中声明的返回类型的值不匹配，则在返回给调用的函数或方法之前，表达式的值会被转换为返回类型。

Note

As described in [Failable Initializers](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/declarations#Failable-Initializers), a special form of the `return` statement (`return nil`) can be used in a failable initializer to indicate initialization failure.

当一个 `return` 语句后没有跟表达式时，它只能用于从一个不返回值的函数或方法中返回（即，当函数或方法的返回类型是 `Void` 或 `()` 时）。

Grammar of a return statement

_return-statement_ → **`return`** _expression?_

### [抛出语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Throw-Statement)

在抛出函数或方法的主体中，或者在其类型标记为 `throws` 关键字的闭包表达式的主体中，会发生 `throw` 语句。

`throw` 语句导致程序结束当前作用域的执行，并开始向其封闭作用域传播错误。抛出的错误继续传播，直到被 `catch` 语句的 `do` 子句处理。

`throw` 语句由 `throw` 关键字后跟一个表达式组成，如下所示。

```swift
throw <#expression#>
```

表达式的值必须具有符合 `Error` 协议的类型。如果包含 `throw` 语句的 `do` 语句或函数声明了它抛出的错误的类型，则表达式的值必须是该类型的实例。

有关如何使用 `throw` 语句的示例，请参阅在错误处理中使用抛出函数传播错误。

Grammar of a throw statement

_throw-statement_ → **`throw`** _expression_

## [延迟语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Defer-Statement)

在 `defer` 语句中使用它是在转移程序控制到 `defer` 语句出现的作用域外之前执行代码。

一个 `defer` 语句具有以下形式：

```swift
defer {
    <#statements#>
}
```

无论程序控制如何转移， `defer` 语句中的语句都会被执行。这意味着可以使用 `defer` 语句，例如，执行手动资源管理，如关闭文件描述符，并执行即使抛出错误也必须进行的操作。

在围绕 `defer` 语句的作用域结束时执行 `defer` 语句中的语句。

```swift
func f(x: Int) {
  defer { print("First defer") }


  if x < 10 {
    defer { print("Second defer") }
    print("End of if")
  }


  print("End of function")
}
f(x: 5)
// Prints "End of if"
// Prints "Second defer"
// Prints "End of function"
// Prints "First defer"
```

在上面的代码中， `defer` 在 `if` 语句中执行，早于在函数 `f` 中声明的 `defer` ，因为 `if` 语句的作用域在函数的作用域之前结束。

如果多个 `defer` 语句出现在同一作用域中，它们出现的顺序是它们执行顺序的反向。在给定作用域中首先执行最后一个 `defer` 语句意味着该最后一个 `defer` 语句内部的语句可以引用将由其他 `defer` 语句清理的资源。

```swift
func f() {
    defer { print("First defer") }
    defer { print("Second defer") }
    print("End of function")
}
f()
// Prints "End of function"
// Prints "Second defer"
// Prints "First defer"
```

`defer` 语句中的语句不能将程序控制转移到 `defer` 语句之外。

Grammar of a defer statement

_defer-statement_ → **`defer`** _code-block_

## [语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Do-Statement)

`do` 语句用于引入一个新的作用域，并可以选择性地包含一个或多个 `catch` 子句，这些子句包含匹配已定义错误条件的模式。在 `do` 语句的作用域中声明的变量和常量只能在该作用域内访问。

Swift 中的 `do` 语句类似于 C 中用于限定代码块的花括号 ( `{}` )，并且在运行时不会产生性能开销。

一个 `do` 语句具有以下形式：

```swift
do {
    try <#expression#>
    <#statements#>
} catch <#pattern 1#> {
    <#statements#>
} catch <#pattern 2#> where <#condition#> {
    <#statements#>
} catch <#pattern 3#>, <#pattern 4#> where <#condition#> {
    <#statements#>
} catch {
    <#statements#>
}
```

A `do` 语句可以选择性地指定它抛出的错误类型，其形式如下：

```swift
do throws(<#type#>) {
    try <#expression#>
} catch <#pattern> {
    <#statements#>
} catch {
    <#statements#>
}
```

如果 `do` 语句包含 `throws` 子句，则 `do` 块只能抛出指定类型的错误。该类型必须是符合 `Error` 协议的具体类型，符合 `Error` 协议的不透明类型，或被包装的协议类型 `any Error` 。如果 `do` 语句没有指定它抛出的错误类型，Swift 将推断错误类型如下：

- 如果 `throws` 语句和 `try` 表达式在 `do` 代码块中都嵌套在一个详尽的错误处理机制内，则 Swift 推断 `do` 语句是非抛出的。
- 如果 `do` 代码块包含抛出仅单一类型错误的代码，而不在详尽的错误处理之外，除了抛出 `Never` ，则 Swift 推断 `do` 语句抛出该具体错误类型。
- 如果 `do` 代码块包含抛出多于单一类型错误的代码，而不在详尽的错误处理之外，则 Swift 推断 `do` 语句抛出 `any Error` 。

有关处理具有明确类型的错误的更多信息，请参见指定错误类型。

如果 `do` 代码块中的任何语句抛出错误，则程序控制将转移到第一个与该错误模式匹配的 `catch` 子句。如果没有子句匹配，则错误会传播到周围的作用域。如果在顶部级别未处理错误，程序执行将因运行时错误而停止。

像 `switch` 语句一样，编译器尝试推断 `catch` 子句是否是穷尽的。如果可以做出这样的判断，则错误被视为已处理。否则，错误可能会从包含范围中传播，这意味着错误必须由封闭的 `catch` 子句处理，或者包含的函数必须声明为 `throws` 。

一个 `catch` 子句如果有多个模式，则如果其任何模式与错误匹配，则匹配该错误。如果一个 `catch` 子句包含多个模式，则所有模式必须包含相同的常量或变量绑定，并且在所有 `catch` 子句的模式中，每个绑定的变量或常量必须具有相同的类型。

为了确保错误得到处理，请使用一个 `catch` 子句，其模式匹配所有错误，例如通配符模式 ( `_` )。如果 `catch` 子句没有指定模式，则 `catch` 子句匹配并将任何错误绑定到名为 `error` 的局部常量。有关您可以在 `catch` 子句中使用的模式的更多信息，请参见 Patterns。

要查看如何使用 `do` 语句与多个 `catch` 子句的示例，请参见处理错误。

Grammar of a do statement

_do-statement_ → **`do`** _throws-clause?_ _code-block_ _catch-clauses?_\
&#xNAN;_&#x63;atch-clauses_ → _catch-clause_ _catch-clauses?_\
&#xNAN;_&#x63;atch-clause_ → **`catch`** _catch-pattern-list?_ _code-block_\
&#xNAN;_&#x63;atch-pattern-list_ → _catch-pattern_ | _catch-pattern_ **`,`** _catch-pattern-list_\
&#xNAN;_&#x63;atch-pattern_ → _pattern_ _where-clause?_

## [编译器控制语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Compiler-Control-Statements)

编译器控制语句允许程序更改编译器行为的某些方面。Swift 有三种编译器控制语句：条件编译块、行控制语句和编译时诊断语句。

Grammar of a compiler control statement

_compiler-control-statement_ → _conditional-compilation-block_\
&#xNAN;_&#x63;ompiler-control-statement_ → _line-control-statement_\
&#xNAN;_&#x63;ompiler-control-statement_ → _diagnostic-statement_

### [条件编译块](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Conditional-Compilation-Block)

条件编译块允许根据一个或多个编译条件的值有条件地编译代码。

每个条件编译块以 `#if` 编译指令开始，以 `#endif` 编译指令结束。一个简单的条件编译块具有以下形式：

```swift
#if <#compilation condition#>
    <#statements#>
#endif
```

与 `if` 语句的条件不同，编译条件在编译时进行评估。因此，只有当编译条件在编译时评估为 `true` 时，语句才会被编译和执行。

编译条件可以包括 `true` 和 `false` 布尔字面量、与 `-D` 命令行标志一起使用的标识符，或者下表中列出的任何平台条件。

| 平台条件              | 有效参数                                                          |
| --------------------- | ----------------------------------------------------------------- |
| `os()`                | `macOS`, `iOS`, `watchOS`, `tvOS`, `visionOS`, `Linux`, `Windows` |
| `arch()`              | `i386`, `x86_64`, `arm`, `arm64`                                  |
| `swift()`             | `>=` 或 `<` 后跟版本号                                            |
| `compiler()`          | `>=` 或 `<` 后跟版本号                                            |
| `canImport()`         | 模块名称                                                          |
| `targetEnvironment()` | `simulator`, `macCatalyst`                                        |

对于 `swift()` 和 `compiler()` 平台条件的版本号由一个主版本号、可选的次版本号、可选的补丁号等组成，各部分之间用点 ( `.` ) 分隔。比较运算符和版本号之间不得有空格。对于 `compiler()` 的版本是编译器版本，无论传递给编译器的 Swift 版本设置是什么。对于 `swift()` 的版本是当前正在编译的语言版本。例如，如果您在 Swift 4.2 模式下使用 Swift 5 编译器编译代码，则编译器版本为 5，语言版本为 4.2。在这些设置下，以下代码打印所有三条消息：

```swift
#if compiler(>=5)
print("Compiled with the Swift 5 compiler or later")
#endif
#if swift(>=4.2)
print("Compiled in Swift 4.2 mode or later")
#endif
#if compiler(>=5) && swift(<5)
print("Compiled with the Swift 5 compiler or later in a Swift mode earlier than 5")
#endif
// Prints "Compiled with the Swift 5 compiler or later"
// Prints "Compiled in Swift 4.2 mode or later"
// Prints "Compiled with the Swift 5 compiler or later in a Swift mode earlier than 5"
```

`canImport()` 平台条件的参数是一个可能并不在所有平台上存在的模块的名称。该模块的名称中可以包含句点 ( `.` )。此条件测试是否可以导入该模块，但实际上并不导入它。如果模块存在，则平台条件返回 `true` ；否则，返回 `false` 。

当为指定环境编译代码时， `targetEnvironment()` 平台条件返回 `true` ；否则，它返回 `false` 。

Note

The `arch(arm)` platform condition doesn’t return `true` for ARM 64 devices. The `arch(i386)` platform condition returns `true` when code is compiled for the 32–bit iOS simulator.

您可以使用逻辑运算符 `&&` 、 `||` 和 `!` 组合和否定编译条件，并使用括号进行分组。这些运算符具有与用于组合普通布尔表达式的逻辑运算符相同的结合性和优先级。

与 `if` 语句类似，您可以添加多个条件分支以测试不同的编译条件。您可以使用 `#elseif` 子句添加任意数量的其他分支。您还可以使用 `#else` 子句添加最后一个额外分支。包含多个分支的条件编译块具有以下形式：

```swift
#if <#compilation condition 1#>
    <#statements to compile if compilation condition 1 is true#>
#elseif <#compilation condition 2#>
    <#statements to compile if compilation condition 2 is true#>
#else
    <#statements to compile if both compilation conditions are false#>
#endif
```

Note

Each statement in the body of a conditional compilation block is parsed even if it’s not compiled. However, there’s an exception if the compilation condition includes a `swift()` or `compiler()` platform condition: The statements are parsed only if the language or compiler version matches what is specified in the platform condition. This exception ensures that an older compiler doesn’t attempt to parse syntax introduced in a newer version of Swift.

有关如何将显式成员表达式包装在条件编译块中的信息，请参见显式成员表达式。

Grammar of a conditional compilation block

_conditional-compilation-block_ → _if-directive-clause_ _elseif-directive-clauses?_ _else-directive-clause?_ _endif-directive_

_if-directive-clause_ → _if-directive_ _compilation-condition_ _statements?_\
&#xNAN;_&#x65;lseif-directive-clauses_ → _elseif-directive-clause_ _elseif-directive-clauses?_\
&#xNAN;_&#x65;lseif-directive-clause_ → _elseif-directive_ _compilation-condition_ _statements?_\
&#xNAN;_&#x65;lse-directive-clause_ → _else-directive_ _statements?_\
&#xNAN;_&#x69;f-directive_ → **`#if`**\
&#xNAN;_&#x65;lseif-directive_ → **`#elseif`**\
&#xNAN;_&#x65;lse-directive_ → **`#else`**\
&#xNAN;_&#x65;ndif-directive_ → **`#endif`**

_compilation-condition_ → _platform-condition_\
&#xNAN;_&#x63;ompilation-condition_ → _identifier_\
&#xNAN;_&#x63;ompilation-condition_ → _boolean-literal_\
&#xNAN;_&#x63;ompilation-condition_ → **`(`** _compilation-condition_ **`)`**\
&#xNAN;_&#x63;ompilation-condition_ → **`!`** _compilation-condition_\
&#xNAN;_&#x63;ompilation-condition_ → _compilation-condition_ **`&&`** _compilation-condition_\
&#xNAN;_&#x63;ompilation-condition_ → _compilation-condition_ **`||`** _compilation-condition_

_platform-condition_ → **`os`** **`(`** _operating-system_ **`)`**\
&#xNAN;_&#x70;latform-condition_ → **`arch`** **`(`** _architecture_ **`)`**\
&#xNAN;_&#x70;latform-condition_ → **`swift`** **`(`** **`>=`** _swift-version_ **`)`** | **`swift`** **`(`** **`<`** _swift-version_ **`)`**\
&#xNAN;_&#x70;latform-condition_ → **`compiler`** **`(`** **`>=`** _swift-version_ **`)`** | **`compiler`** **`(`** **`<`** _swift-version_ **`)`**\
&#xNAN;_&#x70;latform-condition_ → **`canImport`** **`(`** _import-path_ **`)`**\
&#xNAN;_&#x70;latform-condition_ → **`targetEnvironment`** **`(`** _environment_ **`)`**

_operating-system_ → **`macOS`** | **`iOS`** | **`watchOS`** | **`tvOS`** | **`visionOS`** | **`Linux`** | **`Windows`**\
&#xNAN;_&#x61;rchitecture_ → **`i386`** | **`x86_64`** | **`arm`** | **`arm64`**\
&#xNAN;_&#x73;wift-version_ → _decimal-digits_ _swift-version-continuation?_\
&#xNAN;_&#x73;wift-version-continuation_ → **`.`** _decimal-digits_ _swift-version-continuation?_\
&#xNAN;_&#x65;nvironment_ → **`simulator`** | **`macCatalyst`**

### [行控制语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Line-Control-Statement)

行控制语句用于指定一个行号和文件名，这可以与正在编译的源代码的行号和文件名不同。使用行控制语句可以更改 Swift 用于诊断和调试目的的源代码位置。

行控制语句有以下几种形式：

```swift
#sourceLocation(file: <#file path#>, line: <#line number#>)
#sourceLocation()
```

行控制语句的第一种形式更改 `#line` 、 `#file` 、 `#fileID` 和 `#filePath` 字面量表达式的值，从行控制语句后面的代码行开始。行号更改 `#line` 的值，并且是大于零的任何整数字面量。文件路径更改 `#file` 、 `#fileID` 和 `#filePath` 的值，并且是一个字符串字面量。指定的字符串成为 `#filePath` 的值，字符串的最后路径组件由 `#fileID` 的值使用。有关 `#file` 、 `#fileID` 和 `#filePath` 的信息，请参见字面量表达式。

行控制语句的第二种形式， `#sourceLocation()` ，将源代码位置重置回默认的行编号和文件路径。

Grammar of a line control statement

_line-control-statement_ → **`#sourceLocation`** **`(`** **`file:`** _file-path_ **`,`** **`line:`** _line-number_ **`)`**\
&#xNAN;_&#x6C;ine-control-statement_ → **`#sourceLocation`** **`(`** **`)`**\
&#xNAN;_&#x6C;ine-number_ → A decimal integer greater than zero\
&#xNAN;_&#x66;ile-path_ → _static-string-literal_

### [编译时诊断语句](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Compile-Time-Diagnostic-Statement)

在 Swift 5.9 之前， `#warning` 和 `#error` 语句在编译时会发出诊断。此行为现在由 Swift 标准库中的 `warning(_:)` 和 `error(_:)` 宏提供。

## [可用性条件](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/statements#Availability-Condition)

可用性条件用作 `if` 、 `while` 和 `guard` 语句的条件，以根据指定的平台参数查询 APIs 在运行时的可用性。

可用性条件具有以下形式：

```swift
if #available(<#platform name#> <#version#>, <#...#>, *) {
    <#statements to execute if the APIs are available#>
} else {
    <#fallback statements to execute if the APIs are unavailable#>
}
```

您使用可用性条件来执行代码块，具体取决于您想要使用的 API 在运行时是否可用。编译器在验证该代码块中的 API 是否可用时使用可用性条件中的信息。

可用性条件接受一个以逗号分隔的平台名称和版本列表。使用 `iOS` 、 `macOS` 、 `watchOS` 、 `tvOS` 和 `visionOS` 作为平台名称，并包含相应的版本号。 `*` 参数是必需的，指定在任何其他平台上，由可用性条件保护的代码块的主体在您目标指定的最低部署目标上执行。

与布尔条件不同，您不能使用逻辑运算符如 `&&` 和 `||` 来组合可用性条件。不要使用 `!` 来否定可用性条件，而是使用不可用性条件，其形式如下：

```swift
if #unavailable(<#platform name#> <#version#>, <#...#>) {
    <#fallback statements to execute if the APIs are unavailable#>
} else {
    <#statements to execute if the APIs are available#>
}
```

`#unavailable` 表单是语法糖，用于否定条件。在不可用条件下， `*` 参数是隐式的，必须不包括在内。它与可用条件中的 `*` 参数具有相同的含义。

Grammar of an availability condition

_availability-condition_ → **`#available`** **`(`** _availability-arguments_ **`)`**\
&#xNAN;_&#x61;vailability-condition_ → **`#unavailable`** **`(`** _availability-arguments_ **`)`**\
&#xNAN;_&#x61;vailability-arguments_ → _availability-argument_ | _availability-argument_ **`,`** _availability-arguments_\
&#xNAN;_&#x61;vailability-argument_ → _platform-name_ _platform-version_\
&#xNAN;_&#x61;vailability-argument_ → **`*`**

_platform-name_ → **`iOS`** | **`iOSApplicationExtension`**\
&#xNAN;_&#x70;latform-name_ → **`macOS`** | **`macOSApplicationExtension`**\
&#xNAN;_&#x70;latform-name_ → **`macCatalyst`** | **`macCatalystApplicationExtension`**\
&#xNAN;_&#x70;latform-name_ → **`watchOS`** | **`watchOSApplicationExtension`**\
&#xNAN;_&#x70;latform-name_ → **`tvOS`** | **`tvOSApplicationExtension`**\
&#xNAN;_&#x70;latform-name_ → **`visionOS`** | **`visionOSApplicationExtension`**\
&#xNAN;_&#x70;latform-version_ → _decimal-digits_\
&#xNAN;_&#x70;latform-version_ → _decimal-digits_ **`.`** _decimal-digits_\
&#xNAN;_&#x70;latform-version_ → _decimal-digits_ **`.`** _decimal-digits_ **`.`** _decimal-digits_
