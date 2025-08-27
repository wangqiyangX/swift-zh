# 类型

> 使用内置的命名类型和复合类型。

在 Swift 中，有两种类型：命名类型和复合类型。命名类型是指在定义时可以给定特定名称的类型。命名类型包括类、结构、枚举和协议。例如，用户定义的名为 `MyClass` 的类的实例具有类型 `MyClass` 。除了用户定义的命名类型外，Swift 标准库还定义了许多常用的命名类型，包括表示数组、字典和可选值的类型。

在其他语言中通常被认为是基本或原始的数据类型——例如表示数字、字符和字符串的类型——实际上是命名类型，在 Swift 标准库中使用结构定义和实现。因为它们是命名类型，所以您可以使用扩展声明来扩展它们的行为，以满足程序的需求，扩展声明在《扩展和扩展声明》中讨论。

复合类型是没有名称的类型，在 Swift 语言本身中定义。有两种复合类型：函数类型和元组类型。复合类型可以包含命名类型和其他复合类型。例如，元组类型 `(Int, (Int, Int))` 包含两个元素：第一个是命名类型 `Int` ，第二个是另一个复合类型 `(Int, Int)` 。

您可以在命名类型或复合类型周围加上括号。然而，在类型周围添加括号没有任何效果。例如， `(Int)` 等同于 `Int` 。

本章讨论在 Swift 语言本身中定义的类型，并描述 Swift 的类型推断行为。

类型的语法

_type_ → _function-type_\
&#xNAN;_&#x74;ype_ → _array-type_\
&#xNAN;_&#x74;ype_ → _dictionary-type_\
&#xNAN;_&#x74;ype_ → _type-identifier_\
&#xNAN;_&#x74;ype_ → _tuple-type_\
&#xNAN;_&#x74;ype_ → _optional-type_\
&#xNAN;_&#x74;ype_ → _implicitly-unwrapped-optional-type_\
&#xNAN;_&#x74;ype_ → _protocol-composition-type_\
&#xNAN;_&#x74;ype_ → _opaque-type_\
&#xNAN;_&#x74;ype_ → _boxed-protocol-type_\
&#xNAN;_&#x74;ype_ → _metatype-type_\
&#xNAN;_&#x74;ype_ → _any-type_\
&#xNAN;_&#x74;ype_ → _self-type_\
&#xNAN;_&#x74;ype_ → **`(`** _type_ **`)`**

## [类型注解](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Type-Annotation)

类型注解明确指定变量或表达式的类型。类型注解以冒号 ( `:` ) 开始，并以类型结束，如以下示例所示：

```swift
let someTuple: (Double, Double) = (3.14159, 2.71828)
func someFunction(a: Int) { /* ... */ }
```

在第一个示例中，表达式 `someTuple` 被指定为具有元组类型 `(Double, Double)` 。在第二个示例中，函数 `someFunction` 的参数 `a` 被指定为具有类型 `Int` 。

类型注解可以在类型之前包含一个可选的类型属性列表。

类型注释的语法

_type-annotation_ → **`:`** _attributes?_ _type_

## [类型标识符](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Type-Identifier)

类型标识符指的是命名类型或命名或复合类型的类型别名。

大多数情况下，类型标识符直接指向与标识符同名的命名类型。例如， `Int` 是一个类型标识符，直接指向命名类型 `Int` ，而类型标识符 `Dictionary<String, Int>` 直接指向命名类型 `Dictionary<String, Int>` 。

有两种情况，类型标识符不指向同名的类型。在第一种情况下，类型标识符指向命名或复合类型的类型别名。例如，在下面的示例中，类型注释中使用的 `Point` 指的是元组类型 `(Int, Int)` 。

```swift
typealias Point = (Int, Int)
let origin: Point = (0, 0)
```

在第二种情况下，类型标识符使用点 ( `.` ) 语法来引用在其他模块中声明或嵌套在其他类型中的命名类型。例如，以下代码中的类型标识符引用了在 `ExampleModule` 模块中声明的命名类型 `MyType` 。

```swift
var someValue: ExampleModule.MyType
```

类型标识符的语法

_type-identifier_ → _type-name_ _generic-argument-clause?_ | _type-name_ _generic-argument-clause?_ **`.`** _type-identifier_\
&#xNAN;_&#x74;ype-name_ → _identifier_

## [元组类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Tuple-Type)

元组类型是一个用逗号分隔的类型列表，括在括号内。

您可以将元组类型用作函数的返回类型，以使函数返回一个包含多个值的单个元组。您还可以为元组类型的元素命名，并使用这些名称来引用单个元素的值。元素名称由一个标识符紧接着一个冒号 (:) 组成。有关演示这两个特性的示例，请参见具有多个返回值的函数。

当元组类型的元素有名称时，该名称是类型的一部分。

```swift
var someTuple = (top: 10, bottom: 12)  // someTuple is of type (top: Int, bottom: Int)
someTuple = (top: 4, bottom: 42) // OK: names match
someTuple = (9, 99)              // OK: names are inferred
someTuple = (left: 5, right: 5)  // Error: names don't match
```

所有元组类型包含两个或更多类型，除了 `Void` ，它是空元组类型 `()` 的类型别名。

元组类型的语法

_tuple-type_ → **`(`** **`)`** | **`(`** _tuple-type-element_ **`,`** _tuple-type-element-list_ **`)`**\
&#xNAN;_&#x74;uple-type-element-list_ → _tuple-type-element_ | _tuple-type-element_ **`,`** _tuple-type-element-list_\
&#xNAN;_&#x74;uple-type-element_ → _element-name_ _type-annotation_ | _type_\
&#xNAN;_&#x65;lement-name_ → _identifier_

## [函数类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Function-Type)

函数类型表示一个函数、方法或闭包的类型，由一个参数和一个返回类型组成，二者通过一个箭头 ( `->` ) 分隔：

```swift
(<#parameter type#>) -> <#return type#>
```

参数类型是以逗号分隔的类型列表。由于返回类型可以是元组类型，因此函数类型支持返回多个值的函数和方法。

函数类型 `() -> T` 的参数（其中 `T` 是任何类型）可以应用 `autoclosure` 属性，以在其调用位置隐式创建闭包。这提供了一种语法上方便的方式，能够延迟表达式的求值，而无需在调用函数时编写显式闭包。有关自动闭包函数类型参数的示例，请参见《自动闭包》。

函数类型的参数类型可以具有可变参数。从语法上讲，可变参数由基本类型名称后跟三个点（ `...` ）组成，如 `Int...` 。可变参数被视为一个数组，其中包含基本类型名称的元素。例如，可变参数 `Int...` 被视为 `[Int]` 。有关使用可变参数的示例，请参见《可变参数》。

要指定一个输入输出参数，请在参数类型前加上 `inout` 关键字。您不能用 `inout` 关键字标记可变参数或返回类型。输入输出参数在《输入输出参数》中进行了讨论。

如果函数类型只有一个参数，而该参数的类型是元组类型，则在编写函数的类型时，元组类型必须用括号括起来。例如， `((Int, Int)) -> Void` 是一个函数的类型，该函数接受一个元组类型的单个参数 `(Int, Int)` ，并且不返回任何值。相比之下，没有括号的话， `(Int, Int) -> Void` 是一个函数的类型，该函数接受两个 `Int` 参数，并且不返回任何值。同样地，因为 `Void` 是 `()` 的类型别名，函数类型 `(Void) -> Void` 与 `(()) -> ()` 是相同的——一个接受一个空元组的单个参数的函数。这些类型与 `() -> ()` 不同——一个不接受任何参数的函数。

函数和方法中的参数名称不是对应函数类型的一部分。例如：

```swift
func someFunction(left: Int, right: Int) {}
func anotherFunction(left: Int, right: Int) {}
func functionWithDifferentLabels(top: Int, bottom: Int) {}


var f = someFunction // The type of f is (Int, Int) -> Void, not (left: Int, right: Int) -> Void.
f = anotherFunction              // OK
f = functionWithDifferentLabels  // OK


func functionWithDifferentArgumentTypes(left: Int, right: String) {}
f = functionWithDifferentArgumentTypes     // Error


func functionWithDifferentNumberOfArguments(left: Int, right: Int, top: Int) {}
f = functionWithDifferentNumberOfArguments // Error
```

因为参数标签不是函数类型的一部分，所以在编写函数类型时可以省略它们。

```swift
var operation: (lhs: Int, rhs: Int) -> Int     // Error
var operation: (_ lhs: Int, _ rhs: Int) -> Int // OK
var operation: (Int, Int) -> Int               // OK
```

如果一个函数类型包含多个箭头 ( `->` ), 函数类型将从右到左分组。例如，函数类型 `(Int) -> (Int) -> Int` 理解为 `(Int) -> ((Int) -> Int)` — 即，一个接收 `Int` 并返回另一个接收和返回 `Int` 的函数。

可以抛出或重新抛出错误的函数的函数类型必须包含 `throws` 关键字。您可以在 `throws` 后面以括号形式包括一个类型，以指定函数抛出的错误类型。抛出错误类型必须符合 `Error` 协议。写 `throws` 而不指定类型等同于写 `throws(any Error)` 。省略 `throws` 等同于写 `throws(Never)` 。一个函数抛出的错误类型可以是任何符合 `Error` 的类型，包括泛型类型、装箱的协议类型和不透明类型。

函数抛出的错误类型是该函数类型的一部分，而错误类型之间的子类型关系意味着相应的函数类型也是子类型。例如，如果您声明一个自定义 `MyError` 类型，则某些函数类型之间的关系如下，从超类型到子类型：

1. 抛出任何错误的函数，标记 `throws(any Error)`
2. 抛出特定错误的函数，标记 `throws(MyError)`
3. 不抛出的函数，标记 `throws(Never)`

作为这些子类型关系的结果：

- 您可以在与抛出异常的函数相同的地方使用不抛出异常的函数。
- 您可以在与抛出函数相同的地方使用抛出具体错误类型的函数。
- 您可以在与抛出更一般错误类型的函数相同的地方使用抛出更具体错误类型的函数。

如果您在函数类型中使用关联类型或泛型类型参数作为抛出的错误类型，则该关联类型或泛型类型参数隐式要求符合 `Error` 协议。

抛出和重新抛出函数在《抛出函数和方法》和《重新抛出函数和方法》中进行了描述。

异步函数的函数类型必须使用 `async` 关键字进行标记。 `async` 关键字是函数类型的一部分，同步函数是异步函数的子类型。因此，您可以在与异步函数相同的地方使用同步函数。有关异步函数的信息，请参阅异步函数和方法。

### [非转义闭包的限制](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Restrictions-for-Nonescaping-Closures)

一个非逃逸函数的参数不能存储在类型为 `Any` 的属性、变量或常量中，因为这可能允许值逃逸。

一个非逃逸函数的参数不能作为参数传递给另一个非逃逸函数参数。这个限制帮助 Swift 在编译时而不是运行时执行更多的内存访问冲突检查。例如：

```swift
let external: (() -> Void) -> Void = { _ in () }
func takesTwoFunctions(first: (() -> Void) -> Void, second: (() -> Void) -> Void) {
    first { first {} }       // Error
    second { second {}  }    // Error


    first { second {} }      // Error
    second { first {} }      // Error


    first { external {} }    // OK
    external { first {} }    // OK
}
```

在上面的代码中， `takesTwoFunctions(first:second:)` 的两个参数都是函数。两个参数都没有标记为 `@escaping` ，因此它们都是非逃逸的。

上面示例中标记为“错误”的四个函数调用会导致编译器错误。因为 `first` 和 `second` 参数是非逃逸函数，它们不能作为参数传递给另一个非逃逸函数参数。相比之下，标记为“OK”的两个函数调用不会导致编译器错误。这些函数调用没有违反限制，因为 `external` 不是 `takesTwoFunctions(first:second:)` 的参数之一。

如果您需要避免此限制，请将其中一个参数标记为转义，或通过使用 `withoutActuallyEscaping(_:do:)` 函数暂时将一个非转义函数参数转换为转义函数。有关避免对内存的冲突访问的信息，请参见内存安全。

函数类型的语法

_function-type_ → _attributes?_ _function-type-argument-clause_ **`async`**_?_ _throws-clause?_ **`->`** _type_

_function-type-argument-clause_ → **`(`** **`)`**\
&#xNAN;_&#x66;unction-type-argument-clause_ → **`(`** _function-type-argument-list_ **`...`**_?_ **`)`**

_function-type-argument-list_ → _function-type-argument_ | _function-type-argument_ **`,`** _function-type-argument-list_\
&#xNAN;_&#x66;unction-type-argument_ → _attributes?_ _parameter-modifier?_ _type_ | _argument-label_ _type-annotation_\
&#xNAN;_&#x61;rgument-label_ → _identifier_

_throws-clause_ → **`throws`** | **`throws`** **`(`** _type_ **`)`**

## [数组类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Array-Type)

Swift 语言为 Swift 标准库 `Array<Element>` 类型提供了以下语法糖：

```swift
[<#type#>]
```

换句话说，以下两个声明是等价的：

```swift
let someArray: Array<String> = ["Alex", "Brian", "Dave"]
let someArray: [String] = ["Alex", "Brian", "Dave"]
```

在这两种情况下，常量 `someArray` 被声明为字符串数组。数组的元素可以通过下标访问，通过在方括号中指定有效的索引值： `someArray[0]` 指的是索引 0 处的元素， `"Alex"` 。

您可以通过嵌套成对的方括号来创建多维数组，其中元素的基本类型的名称包含在最内层的方括号中。例如，您可以使用三组方括号创建一个整数的三维数组：

```swift
var array3D: [[[Int]]] = [[[1, 2], [3, 4]], [[5, 6], [7, 8]]]
```

在访问多维数组中的元素时，最左边的下标索引指的是最外层数组中该索引的元素。右侧的下一个下标索引指的是在嵌套一级的数组中该索引的元素。依此类推。这意味着在上述示例中， `array3D[0]` 指的是 `[[1, 2], [3, 4]]` ， `array3D[0][1]` 指的是 `[3, 4]` ，而 `array3D[0][1][1]` 指的是值 4。

For a detailed discussion of the Swift standard library `Array` type, see [Arrays](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Arrays).

数组类型的语法

_array-type_ → **`[`** _type_ **`]`**

## [字典类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Dictionary-Type)

Swift 语言为 Swift 标准库 `Dictionary<Key, Value>` 类型提供了以下语法糖：

```swift
[<#key type#>: <#value type#>]
```

换句话说，以下两个声明是等价的：

```swift
let someDictionary: [String: Int] = ["Alex": 31, "Paul": 39]
let someDictionary: Dictionary<String, Int> = ["Alex": 31, "Paul": 39]
```

在这两种情况下，常量 `someDictionary` 被声明为一个字典，键为字符串，值为整数。

可以通过下标访问字典的值，通过在方括号中指定相应的键： `someDictionary["Alex"]` 指的是与键 `"Alex"` 相关联的值。下标返回字典值类型的可选值。如果指定的键不在字典中，下标将返回 `nil` 。

字典的键类型必须符合 Swift 标准库 `Hashable` 协议。

有关 Swift 标准库 `Dictionary` 类型的详细讨论，请参阅字典。

字典类型的语法

_dictionary-type_ → **`[`** _type_ **`:`** _type_ **`]`**

## [可选类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Optional-Type)

Swift 语言将后缀 `?` 定义为命名类型 `Optional<Wrapped>` 的语法糖，该类型在 Swift 标准库中定义。换句话说，以下两个声明是等价的：

```swift
var optionalInteger: Int?
var optionalInteger: Optional<Int>
```

在这两种情况下，变量 `optionalInteger` 被声明为可选整数类型。请注意，类型与 `?` 之间不能出现空格。

类型 `Optional<Wrapped>` 是一个枚举，具有两个案例 `none` 和 `some(Wrapped)` ，用于表示可能存在或不存在的值。任何类型都可以显式声明为（或隐式转换为）可选类型。如果在声明可选变量或属性时不提供初始值，则其值会自动默认为 `nil` 。

如果可选类型的实例包含一个值，您可以使用后缀运算符 `!` 访问该值，如下所示：

```swift
optionalInteger = 42
optionalInteger! // 42
```

使用 `!` 运算符解包一个值为 `nil` 的可选项会导致运行时错误。

您还可以使用可选链和可选绑定来有条件地对可选表达式执行操作。如果值为 `nil` ，则不执行任何操作，因此不会产生运行时错误。

有关更多信息以及查看如何使用可选类型的示例，请参见可选项。

可选类型的语法

_optional-type_ → _type_ **`?`**

## [隐式解包可选类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Implicitly-Unwrapped-Optional-Type)

Swift 语言将后缀 `!` 定义为命名类型 `Optional<Wrapped>` 的语法糖，该类型在 Swift 标准库中定义，具有在访问时自动解包的附加行为。如果您尝试使用值为 `nil` 的隐式解包可选项，您将会遇到运行时错误。除了隐式解包行为外，以下两个声明是等价的：

```swift
var implicitlyUnwrappedString: String!
var explicitlyUnwrappedString: Optional<String>
```

请注意，类型和 `!` 之间不得出现空格。

因为隐式解包改变了包含该类型的声明的意义，所以嵌套在元组类型或泛型类型中的可选类型——例如字典或数组的元素类型——不能被标记为隐式解包。例如：

```swift
let tupleOfImplicitlyUnwrappedElements: (Int!, Int!)  // Error
let implicitlyUnwrappedTuple: (Int, Int)!             // OK


let arrayOfImplicitlyUnwrappedElements: [Int!]        // Error
let implicitlyUnwrappedArray: [Int]!                  // OK
```

因为隐式解包可选值与可选值具有相同的 `Optional<Wrapped>` 类型，所以您可以在代码中所有可以使用可选值的地方使用隐式解包可选值。例如，您可以将隐式解包可选值的值赋给可选值的变量、常量和属性，反之亦然。

与可选值一样，如果在声明一个隐式解包可选变量或属性时没有提供初始值，则其值会自动默认为 `nil` 。

使用可选链来有条件地对隐式解包可选表达式执行操作。如果值为 `nil` ，则不执行任何操作，因此不会产生运行时错误。

有关隐式解包可选类型的更多信息，请参见隐式解包可选值。

隐式解包可选类型的语法

_implicitly-unwrapped-optional-type_ → _type_ **`!`**

## [协议组合类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Protocol-Composition-Type)

协议组合类型定义了一种类型，该类型符合指定协议列表中的每个协议，或者是一种子类并符合指定协议列表中的每个协议。协议组合类型仅在类型注释、通用参数子句和通用 `where` 子句中指定类型时使用。

协议组合类型具有以下形式：

```swift
<#Protocol 1#> & <#Protocol 2#>
```

协议组合类型允许您指定一个值，其类型符合多个协议的要求，而无需显式定义一个新的、命名的协议，该协议继承自您希望该类型符合的每个协议。例如，您可以使用协议组合类型 `ProtocolA & ProtocolB & ProtocolC` ，而不是声明一个继承自 `ProtocolA` 、 `ProtocolB` 和 `ProtocolC` 的新协议。同样，您可以使用 `SuperClass & ProtocolA` ，而不是声明一个是 `SuperClass` 的子类并符合 `ProtocolA` 的新协议。

每个协议组合列表中的项目是以下之一；该列表最多可以包含一个类：

- 一个类的名称
- 协议的名称
- 一个其基础类型为协议组合类型、协议或类的类型别名。

当协议组合类型包含类型别名时，允许同一协议在定义中出现多次 — 重复项会被忽略。例如，下面代码中 `PQR` 的定义等价于 `P & Q & R` 。

```swift
typealias PQ = P & Q
typealias PQR = PQ & Q & R
```

协议组合类型的语法

_protocol-composition-type_ → _type-identifier_ **`&`** _protocol-composition-continuation_\
&#xNAN;_&#x70;rotocol-composition-continuation_ → _type-identifier_ | _protocol-composition-type_

## [不透明类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Opaque-Type)

不透明类型定义了一种符合协议或协议组合的类型，而不指定底层具体类型。

不透明类型出现在函数或下标的返回类型，或属性的类型中。不透明类型不能作为元组类型或泛型类型的一部分出现，例如数组的元素类型或可选值的包装类型。

不透明类型具有以下形式：

```swift
some <#constraint#>
```

约束是一个类类型、协议类型、协议组合类型或 `Any` 。一个值只有在它是符合列出协议或协议组合的类型的实例，或者继承自列出类的实例时，才能作为不透明类型的实例使用。与不透明值交互的代码只能以约束定义的接口的方式使用该值。

在编译时，类型为不透明的值具有具体的类型，Swift 可以利用该底层类型进行优化。然而，不透明类型形成一个边界，关于该底层类型的信息无法穿越。

协议声明不能包含不透明类型。类不能将不透明类型用作非最终方法的返回类型。

一个使用不透明类型作为返回类型的函数必须返回共享单一基础类型的值。返回类型可以包括作为函数的泛型类型参数的一部分的类型。例如，一个函数 `someFunction<T>()` 可以返回类型为 `T` 或 `Dictionary<String, T>` 的值。

不透明类型的语法

_opaque-type_ → **`some`** _type_

## [封装协议类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Boxed-Protocol-Type)

封装协议类型定义了一个符合协议或协议组合的类型，并且该符合的类型在程序运行时可以变化。

封装协议类型具有以下形式：

```swift
any <#constraint#>
```

约束是协议类型、协议组合类型、协议类型的元类型或协议组合类型的元类型。

在运行时，盒装协议类型的实例可以包含满足约束的任何类型的值。这种行为与不透明类型的工作方式形成对比，在不透明类型中，编译时已知某种特定的符合类型。当使用盒装协议类型时，所使用的额外间接层称为盒装。盒装通常需要单独的内存分配来存储，并且访问时还需额外的间接层，这在运行时会带来性能损耗。

将 `any` 应用于 `Any` 或 `AnyObject` 类型没有效果，因为这些类型已经是已封装的协议类型。

框架协议类型的语法

_boxed-protocol-type_ → **`any`** _type_

## [元类型 类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Metatype-Type)

元类型指的是任何类型的类型，包括类类型、结构类型、枚举类型和协议类型。

类、结构或枚举类型的元类型是该类型的名称后跟 `.Type` 。协议类型的元类型（不是在运行时符合该协议的具体类型）是该协议的名称后跟 `.Protocol` 。例如，类类型 `SomeClass` 的元类型是 `SomeClass.Type` ，协议 `SomeProtocol` 的元类型是 `SomeProtocol.Protocol` 。

您可以使用后缀 `self` 表达式将类型作为值进行访问。例如， `SomeClass.self` 返回 `SomeClass` 本身，而不是 `SomeClass` 的一个实例。并且 `SomeProtocol.self` 返回 `SomeProtocol` 本身，而不是在运行时符合 `SomeProtocol` 的类型的一个实例。您可以使用类型的实例调用 `type(of:)` 函数，以将该实例的动态运行时类型作为值进行访问，如以下示例所示：

```swift
class SomeBaseClass {
    class func printClassName() {
        print("SomeBaseClass")
    }
}
class SomeSubClass: SomeBaseClass {
    override class func printClassName() {
        print("SomeSubClass")
    }
}
let someInstance: SomeBaseClass = SomeSubClass()
// The compile-time type of someInstance is SomeBaseClass,
// and the runtime type of someInstance is SomeSubClass
type(of: someInstance).printClassName()
// Prints "SomeSubClass"
```

有关更多信息，请参见 Swift 标准库中的 `type(of:)` 。

使用初始化表达式从该类型的 metatype 值构造该类型的实例。对于类实例，调用的初始化程序必须标记为 `required` 关键字，或者整个类标记为 `final` 关键字。

```swift
class AnotherSubClass: SomeBaseClass {
    let string: String
    required init(string: String) {
        self.string = string
    }
    override class func printClassName() {
        print("AnotherSubClass")
    }
}
let metatype: AnotherSubClass.Type = AnotherSubClass.self
let anotherInstance = metatype.init(string: "some string")
```

元类型的语法

_metatype-type_ → _type_ **`.`** **`Type`** | _type_ **`.`** **`Protocol`**

## [任何类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Any-Type)

`Any` 类型可以包含来自所有其他类型的值。 `Any` 可以用作以下任意类型实例的具体类型：

- 一个类、结构或枚举
- 一个元类型，如 `Int.self`
- 一个包含任何类型组件的元组
- 一个闭包或函数类型

```swift
let mixed: [Any] = ["one", 2, true, (4, 5.3), { () -> Int in return 6 }]
```

当您将 `Any` 作为实例的具体类型使用时，需要在访问其属性或方法之前将实例转换为已知类型。具有具体类型 `Any` 的实例保持其原始动态类型，并可以使用类型转换运算符中的一个进行转换 — `as` 、 `as?` 或 `as!` 。例如，请使用 `as?` 有条件地将异构数组中的第一个对象降级为 \{{6 \}}，如下所示：

```swift
if let first = mixed.first as? String {
    print("The first item, '\(first)', is a string.")
}
// Prints "The first item, 'one', is a string."
```

有关类型转换的更多信息，请参见类型转换。

`AnyObject` 协议类似于 `Any` 类型。所有类隐式符合 `AnyObject` 。与由语言定义的 `Any` 不同， `AnyObject` 是由 Swift 标准库定义的。如需更多信息，请参阅仅类协议和 `AnyObject` 。

Any 类型的语法

_any-type_ → **`Any`**

## [自我类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Self-Type)

`Self` 类型不是一个具体类型，而是让您方便地引用当前类型，而无需重复或知道该类型的名称。

在协议声明或协议成员声明中， `Self` 类型指的是符合该协议的最终类型。

在结构、类或枚举声明中， `Self` 类型指的是声明引入的类型。在类型成员的声明内部， `Self` 类型指的是该类型。在类声明的成员中， `Self` 只能以如下方式出现：

- 作为方法的返回类型
- 作为只读下标的返回类型
- 作为只读计算属性的类型
- 在方法的主体中

例如，下面的代码显示了一个实例方法 `f` ，其返回类型是 `Self` 。

```swift
class Superclass {
    func f() -> Self { return self }
}
let x = Superclass()
print(type(of: x.f()))
// Prints "Superclass"


class Subclass: Superclass { }
let y = Subclass()
print(type(of: y.f()))
// Prints "Subclass"


let z: Superclass = Subclass()
print(type(of: z.f()))
// Prints "Subclass"
```

上面示例的最后一部分显示， `Self` 指的是 `z` 的值的运行时类型 `Subclass` ，而不是变量本身的编译时类型 `Superclass` 。

在嵌套类型声明内， `Self` 类型指的是由最内层类型声明引入的类型。

`Self` 类型指的是 Swift 标准库中与 `type(of:)` 函数相同的类型。编写 `Self.someStaticMember` 来访问当前类型的成员与编写 `type(of: self).someStaticMember` 是一样的。

自我类型的语法

_self-type_ → **`Self`**

## [类型继承条款](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Type-Inheritance-Clause)

类型继承子句用于指定命名类型继承自哪个类以及命名类型符合哪些协议。类型继承子句以冒号 ( `:` ) 开始，后面跟着类型标识符的列表。

类类型可以从单个超类继承并符合任意数量的协议。在定义类时，超类的名称必须首先出现在类型标识符的列表中，后面是类必须符合的任意数量的协议。如果类不继承自其他类，则列表可以以协议开始。有关类继承的详细讨论和几个示例，请参见继承。

其他命名类型只能从协议列表中继承或符合。协议类型可以从任意数量的其他协议继承。当协议类型从其他协议继承时，来自那些其他协议的要求集合会被聚合在一起，任何从当前协议继承的类型必须符合所有这些要求。

在枚举定义中，类型继承子句可以是协议的列表，或者在将原始值分配给其案例的枚举中，是一个指定这些原始值类型的单一命名类型。有关使用类型继承子句指定其原始值类型的枚举定义的示例，请参见原始值。

类型继承子句的语法

_type-inheritance-clause_ → **`:`** _type-inheritance-list_\
&#xNAN;_&#x74;ype-inheritance-list_ → _attributes?_ _type-identifier_ | _attributes?_ _type-identifier_ **`,`** _type-inheritance-list_

## [类型推断](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types#Type-Inference)

Swift 广泛使用类型推断，允许您省略代码中许多变量和表达式的类型或部分类型。例如， instead of writing `var x: Int = 0` , 您可以写 `var x = 0` , 完全省略类型——编译器正确推断出 `x` 表示类型 `Int` 的值。同样，当可以从上下文推断出完整类型时，您可以省略部分类型。例如，如果您写 `let dict: Dictionary = ["A": 1]` ，编译器推断出 `dict` 的类型是 `Dictionary<String, Int>` 。

在上述两个示例中，类型信息从表达式树的叶子传递到其根部。也就是说， `var x: Int = 0` 中 `x` 的类型首先通过检查 `0` 的类型被推断，然后将该类型信息传递到根部（变量 `x` ）。

在 Swift 中，类型信息也可以反向流动——从根部向叶子流动。在以下示例中，例如，常量 `eFloat` 上的显式类型注释 ( `: Float` ) 导致数字字面量 `2.71828` 的推断类型为 `Float` ，而不是 `Double` 。

```swift
let e = 2.71828 // The type of e is inferred to be Double.
let eFloat: Float = 2.71828 // The type of eFloat is Float.
```

Swift 中的类型推断在单个表达式或语句的层面上进行。这意味着推断表达式中省略的类型或类型的一部分所需的所有信息必须可以通过对该表达式或其子表达式之一进行类型检查来访问。
