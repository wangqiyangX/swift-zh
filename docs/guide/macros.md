---
description: 使用宏在编译时生成代码。
---

# 宏

> 使用宏在编译时生成代码。

宏在编译时转换您的源代码，让您避免手动编写重复代码。在编译过程中，Swift 会在正常构建您的代码之前展开您代码中的任何宏。

![macro-expansion](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-expansion@2x.png){.light-only}
![macro-expansion~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-expansion~dark@2x.png){.dark-only}{.light-only}
![macro-expansion~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-expansion~dark@2x.png){.light-only}
![macro-expansion~dark~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-expansion~dark~dark@2x.png){.dark-only}{.dark-only}

扩展宏始终是一个附加操作：宏添加新代码，但它们从不删除或修改现有代码。

宏的输入和宏扩展的输出都被检查以确保它们是语法上有效的 Swift 代码。同样，您传递给宏的值和宏生成的代码中的值也会被检查以确保它们具有正确的类型。此外，如果宏的实现遇到错误在扩展该宏时，编译器将其视为编译错误。这些保证使得推理使用宏的代码变得更容易，并且使得识别诸如错误使用宏或宏实现存在错误等问题变得更容易。

Swift 有两种类型的宏：

- 独立宏单独出现，而不附加到声明上。
- 附加宏修改它们附加的声明。

调用附加宏和独立宏的方式略有不同，但它们都遵循相同的宏展开模型，您使用相同的方法实现这两者。以下部分更详细地描述这两种宏。

## [独立宏](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/macros#Freestanding-Macros)

要调用独立宏，您在其名称前写一个数字符号 ( `#` )，并在其名称后用括号写任何参数。例如：

```swift
func myFunction() {
    print("Currently running \(#function)")
    #warning("Something's wrong")
}
```

在第一行， `#function` 调用 Swift 标准库中的 `function()` 宏。当您编译这段代码时，Swift 调用该宏的实现，它将 `#function` 替换为当前函数的名称。当您运行这段代码并调用 `myFunction()` 时，它会打印“当前运行 myFunction()”。在第二行， `#warning` 调用 Swift 标准库中的 `warning(_:)` 宏，以产生自定义的编译时警告。

自由宏可以产生一个值，像 `#function` 所做的那样，或者它们可以在编译时执行一个动作，像 `#warning` 所做的那样。

## [附加宏](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/macros#Attached-Macros)

要调用附加宏，您在其名称前写一个 @ 符号 ( `@` )，并在其名称后用括号写任何传递给宏的参数。

附加的宏修改它们附加到的声明。它们向该声明添加代码，例如定义一个新方法或添加对一个协议的遵从。

例如，考虑以下不使用宏的代码：

```swift
struct SundaeToppings: OptionSet {
    let rawValue: Int
    static let nuts = SundaeToppings(rawValue: 1 << 0)
    static let cherry = SundaeToppings(rawValue: 1 << 1)
    static let fudge = SundaeToppings(rawValue: 1 << 2)
}
```

在这段代码中， `SundaeToppings` 选项集中的每个选项都包含对初始化程序的调用，这样做是重复且手动的。当添加一个新选项时，易于犯错误，例如在行尾输入错误的数字。

这是使用宏的此代码的一个版本：

```swift
@OptionSet<Int>
struct SundaeToppings {
    private enum Options: Int {
        case nuts
        case cherry
        case fudge
    }
}
```

此版本的 `SundaeToppings` 调用 `@OptionSet` 宏。该宏读取私有枚举中的案例列表，为每个选项生成常量列表，并添加对 `OptionSet` 协议的符合性。

作为比较，这里是 `@OptionSet` 宏扩展版本的样子。您不需要编写这段代码，只有在您特意请求 Swift 显示宏的扩展时才会看到它。

```swift
struct SundaeToppings {
    private enum Options: Int {
        case nuts
        case cherry
        case fudge
    }


    typealias RawValue = Int
    var rawValue: RawValue
    init() { self.rawValue = 0 }
    init(rawValue: RawValue) { self.rawValue = rawValue }
    static let nuts: Self = Self(rawValue: 1 << Options.nuts.rawValue)
    static let cherry: Self = Self(rawValue: 1 << Options.cherry.rawValue)
    static let fudge: Self = Self(rawValue: 1 << Options.fudge.rawValue)
}
extension SundaeToppings: OptionSet { }
```

所有私有枚举之后的代码都来自 `@OptionSet` 宏。使用宏生成所有静态变量的 `SundaeToppings` 版本比之前手动编码的版本更易读且易于维护。

## [宏声明](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/macros#Macro-Declarations)

在大多数 Swift 代码中，当您实现一个符号，例如一个函数或类型时，并没有单独的声明。然而，对于宏，声明和实现是分开的。宏的声明包含它的名称、它接受的参数、它可以使用的地方以及它生成的代码类型。宏的实现包含通过生成 Swift 代码来扩展宏的代码。

您可以使用 `macro` 关键字引入一个宏声明。例如，以下是之前示例中使用的 `@OptionSet` 宏的部分声明：

```swift
public macro OptionSet<RawType>() =
        #externalMacro(module: "SwiftMacros", type: "OptionSetMacro")
```

第一行指定了宏的名称及其参数——名称是 `OptionSet` ，并且它没有任何参数。第二行使用了 Swift 标准库中的 `externalMacro(module:type:)` 宏告诉 Swift 宏的实现在哪里。在这种情况下， `SwiftMacros` 模块包含一个名为 `OptionSetMacro` 的类型，它实现了 `@OptionSet` 宏。

因为 `OptionSet` 是一个附加宏，它的名称使用大驼峰命名法，像结构和类的名称一样。独立宏使用小驼峰命名法，像变量和函数的名称一样。

Note

宏总是声明为 `public` 。因为声明宏的代码与使用该宏的代码在不同的模块中，所以没有地方可以应用非公共宏。

宏声明定义了宏的角色——在源代码中可以调用该宏的位置，以及宏可以生成的代码类型。每个宏都有一个或多个角色，您可以在宏声明开始时将其作为属性的一部分进行编写。以下是 `@OptionSet` 的声明的更多内容，包括其角色的属性：

```swift
@attached(member)
@attached(extension, conformances: OptionSet)
public macro OptionSet<RawType>() =
        #externalMacro(module: "SwiftMacros", type: "OptionSetMacro")
```

`@attached` 属性在此声明中出现了两次，分别对应每个宏角色。第一次使用 `@attached(member)` 表示该宏向您应用的类型添加新成员。 `@OptionSet` 宏添加了一个 `init(rawValue:)` 初始化器，这是 `OptionSet` 协议所要求的，以及一些额外的成员。第二次使用 `@attached(extension, conformances: OptionSet)` 告诉您 `@OptionSet` 添加了对 `OptionSet` 协议的符合性。 `@OptionSet` 宏扩展了您应用宏的类型，以添加对 `OptionSet` 协议的符合性。

对于独立宏，您可以编写 `@freestanding` 属性来指定其角色：

```swift
@freestanding(expression)
public macro line<T: ExpressibleByIntegerLiteral>() -> T =
        /* ... location of the macro implementation... */
```

上面的 `#line` 宏具有 `expression` 角色。表达式宏生成一个值，或执行一个编译时操作，如生成警告。

除了宏的角色，宏的声明提供了有关宏生成的符号名称的信息。当宏声明提供名称列表时，它保证仅生成使用那些名称的声明，这帮助您理解和调试生成的代码。这是 `@OptionSet` 的完整声明：

```swift
@attached(member, names: named(RawValue), named(rawValue),
        named(`init`), arbitrary)
@attached(extension, conformances: OptionSet)
public macro OptionSet<RawType>() =
        #externalMacro(module: "SwiftMacros", type: "OptionSetMacro")
```

在上面的声明中， `@attached(member)` 宏在 `names:` 标签后包含了每个由 `@OptionSet` 宏生成的符号的参数。宏为名为 `RawValue` 、 `rawValue` 和 `init` 的符号添加了声明——因为这些名称是提前已知的，宏声明将它们明确列出。

宏声明还在名称列表之后包含了 `arbitrary` ，允许宏生成在使用宏之前名称未知的声明。例如，当 `@OptionSet` 宏应用于上面的 `SundaeToppings` 时，它生成的类型属性对应于枚举案例， `nuts` 、 `cherry` 和 `fudge` 。

有关更多信息，包括宏角色的完整列表，请参见附加的和独立的属性。

## [宏扩展](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/macros#Macro-Expansion)

在构建使用宏的 Swift 代码时，编译器调用宏的实现来扩展它们。

![macro-expansion-full](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-expansion-full@2x.png){.light-only}
![macro-expansion-full~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-expansion-full~dark@2x.png){.dark-only}{.light-only}
![macro-expansion-full~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-expansion-full~dark@2x.png){.light-only}
![macro-expansion-full~dark~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-expansion-full~dark~dark@2x.png){.dark-only}{.dark-only}

具体来说，Swift 以以下方式扩展宏：

1. 编译器读取代码，创建语法的内存表示。
2. 编译器将部分内存表示发送给宏实现，宏在此过程中被展开。
3. 编译器用展开后的形式替换宏调用。
4. 编译器继续进行编译，使用展开后的源代码。

要进行具体步骤，请考虑以下内容：

```swift
let magicNumber = #fourCharacterCode("ABCD")
```

`#fourCharacterCode` 宏接收一个长度为四个字符的字符串，并返回一个对应于该字符串中 ASCII 值连接在一起的无符号 32 位整数。一些文件格式使用这样的整数来标识数据，因为它们紧凑但在调试器中仍然可读。下面的实现宏部分展示了如何实现这个宏。

为了展开上述代码中的宏，编译器读取 Swift 文件并创建一个在内存中的代码表示，称为抽象语法树（AST）。AST 使代码的结构显式化，这使得编写与该结构交互的代码变得更容易——例如编译器或宏实现。以下是上述代码的 AST 表示，略微简化，省略了一些额外的细节：

![macro-ast-original](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-original@2x.png){.light-only}
![macro-ast-original~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-original~dark@2x.png){.dark-only}{.light-only}
![macro-ast-original~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-original~dark@2x.png){.light-only}
![macro-ast-original~dark~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-original~dark~dark@2x.png){.dark-only}{.dark-only}

上面的图表显示了这段代码在内存中是如何表示的。AST 中的每个元素对应源代码的一部分。“常量声明”AST 元素下有两个子元素，分别表示常量声明的两个部分：它的名称和它的值。“宏调用”元素有子元素，表示宏的名称和传递给宏的参数列表。

作为构建这个 AST 的一部分，编译器检查源代码是否是有效的 Swift。例如， `#fourCharacterCode` 只接受一个参数，该参数必须是字符串。如果您尝试传递一个整数参数，或者忘记在字符串字面量的末尾加上引号 ( `"` )，您将在这个过程中遇到错误。

编译器找到代码中调用宏的地方，并加载实现这些宏的外部二进制文件。对于每个宏调用，编译器将部分 AST 传递给该宏的实现。以下是该部分 AST 的表示：

![macro-ast-input](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-input@2x.png){.light-only}
![macro-ast-input~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-input~dark@2x.png){.dark-only}{.light-only}
![macro-ast-input~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-input~dark@2x.png){.light-only}
![macro-ast-input~dark~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-input~dark~dark@2x.png){.dark-only}{.dark-only}

`#fourCharacterCode` 宏的实现会在展开宏时将此部分 AST 作为其输入。宏的实现仅对其接收到的部分 AST 进行操作，这意味着无论前后代码是什么，宏总是以相同的方式展开。这一限制有助于使宏展开更易于理解，并且帮助您的代码更快地构建，因为 Swift 可以避免展开未更改的宏。

Swift 帮助宏作者通过限制实现宏的代码，避免意外读取其他输入：

- 传递给宏实现的 AST 仅包含表示宏的 AST 元素，而不包含任何在其之前或之后的代码。
- 宏实现运行在一个沙箱环境中，防止其访问文件系统或网络。

除了这些保护措施，宏的作者有责任不读取或修改宏输入之外的任何内容。例如，宏的扩展不得依赖于当前的时间。

实现 `#fourCharacterCode` 生成一个包含展开代码的新 AST。以下是该代码返回给编译器的内容：

![macro-ast-output](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-output@2x.png){.light-only}
![macro-ast-output~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-output~dark@2x.png){.dark-only}{.light-only}
![macro-ast-output~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-output~dark@2x.png){.light-only}
![macro-ast-output~dark~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-output~dark~dark@2x.png){.dark-only}{.dark-only}

当编译器接收到此展开时，它将包含宏调用的 AST 元素替换为包含宏展开的元素。宏展开后，编译器再次检查以确保程序在语法上仍然有效 Swift 并且所有类型都是正确的。这将产生一个可以像往常一样编译的最终 AST：

![macro-ast-result](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-result@2x.png){.light-only}
![macro-ast-result~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-result~dark@2x.png){.dark-only}{.light-only}
![macro-ast-result~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-result~dark@2x.png){.light-only}
![macro-ast-result~dark~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/macro-ast-result~dark~dark@2x.png){.dark-only}{.dark-only}

这个 AST 对应于如下的 Swift 代码：

```swift
let magicNumber = 1145258561 as UInt32
```

在这个例子中，输入源代码只有一个宏，但一个真实的程序可能有多个相同宏的实例和对不同宏的多个调用。编译器一次扩展一个宏。

如果一个宏出现在另一个宏内部，外部宏会先被扩展——这让外部宏在内部宏被扩展之前修改内部宏。

## [实现宏](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/macros#Implementing-a-Macro)

要实现一个宏，您需要两个组件：一个执行宏扩展的类型，以及一个声明宏以将其作为 API 暴露的库。这些部分与使用宏的代码是独立构建的，即使您在一起开发宏及其客户端，因为宏实现作为构建宏客户端的一部分运行。

要使用 Swift Package Manager 创建一个新宏，请运行 `swift package init --type macro` — 这将创建多个文件，包括宏实现和声明的模板。

要将宏添加到现有项目中，请按如下方式编辑您的 `Package.swift` 文件的开头：

- 在 `swift-tools-version` 注释中设置 Swift 工具版本为 5.9 或更高。
- 导入 `CompilerPluginSupport` 模块。
- 在 `platforms` 列表中将 macOS 10.15 作为最低部署目标。

下面的代码显示了一个示例 `Package.swift` 文件的开头。

```swift
// swift-tools-version: 5.9


import PackageDescription
import CompilerPluginSupport


let package = Package(
    name: "MyPackage",
    platforms: [ .iOS(.v17), .macOS(.v13)],
    // ...
)
```

接下来，在您现有的 `Package.swift` 文件中添加一个宏实现的目标和一个宏库的目标。例如，您可以添加类似于以下内容，修改名称以匹配您的项目：

```swift
targets: [
    // Macro implementation that performs the source transformations.
    .macro(
        name: "MyProjectMacros",
        dependencies: [
            .product(name: "SwiftSyntaxMacros", package: "swift-syntax"),
            .product(name: "SwiftCompilerPlugin", package: "swift-syntax")
        ]
    ),


    // Library that exposes a macro as part of its API.
    .target(name: "MyProject", dependencies: ["MyProjectMacros"]),
]
```

上面的代码定义了两个目标： `MyProjectMacros` 包含宏的实现，而 `MyProject` 使这些宏可用。

宏的实现使用 SwiftSyntax 模块以结构化方式与 Swift 代码交互，使用 AST。如果您使用 Swift Package Manager 创建了一个新的宏包，生成的 `Package.swift` 文件会自动包含对 SwiftSyntax 的依赖。如果您将宏添加到现有项目中，请在您的 `Package.swift` 文件中添加对 SwiftSyntax 的依赖：

```swift
dependencies: [
    .package(url: "https://github.com/apple/swift-syntax", from: "509.0.0")
],
```

根据宏的角色，有一个对应的来自 SwiftSyntax 的协议，宏实现必须遵循。例如，考虑上一节中的 `#fourCharacterCode` 。以下是实现该宏的结构：

```swift
import SwiftSyntax
import SwiftSyntaxMacros


public struct FourCharacterCode: ExpressionMacro {
    public static func expansion(
        of node: some FreestandingMacroExpansionSyntax,
        in context: some MacroExpansionContext
    ) throws -> ExprSyntax {
        guard let argument = node.argumentList.first?.expression,
              let segments = argument.as(StringLiteralExprSyntax.self)?.segments,
              segments.count == 1,
              case .stringSegment(let literalSegment)? = segments.first
        else {
            throw CustomError.message("Need a static string")
        }


        let string = literalSegment.content.text
        guard let result = fourCharacterCode(for: string) else {
            throw CustomError.message("Invalid four-character code")
        }


        return "\(raw: result) as UInt32"
    }
}


private func fourCharacterCode(for characters: String) -> UInt32? {
    guard characters.count == 4 else { return nil }


    var result: UInt32 = 0
    for character in characters {
        result = result << 8
        guard let asciiValue = character.asciiValue else { return nil }
        result += UInt32(asciiValue)
    }
    return result
}
enum CustomError: Error { case message(String) }
```

如果您将此宏添加到现有的 Swift Package Manager 项目中，请添加一个类型作为宏目标的入口点，并列出目标定义的宏：

```swift
import SwiftCompilerPlugin


@main
struct MyProjectMacros: CompilerPlugin {
    var providingMacros: [Macro.Type] = [FourCharacterCode.self]
}
```

`#fourCharacterCode` 宏是一个独立的宏，它生成一个表达式，因此实现它的 `FourCharacterCode` 类型符合 `ExpressionMacro` 协议。 `ExpressionMacro` 协议有一个要求，即一个 `expansion(of:in:)` 方法用于扩展 AST。有关宏角色及其对应的 SwiftSyntax 协议的列表，请参见属性中的附加和独立。

为了扩展 `#fourCharacterCode` 宏，Swift 会将使用此宏的代码的 AST 发送到包含宏实现的库。在库内部，Swift 调用 `FourCharacterCode.expansion(of:in:)` ，将 AST 和上下文作为参数传入该方法。 `expansion(of:in:)` 的实现找到作为参数传递给 `#fourCharacterCode` 的字符串，并计算出相应的 32 位无符号整数字面值。

在上面的示例中，第一个 `guard` 块从 AST 中提取字符串字面量，将该 AST 元素分配给 `literalSegment` 。第二个 `guard` 块调用私有的 `fourCharacterCode(for:)` 函数。如果宏使用不当，这两个块都会抛出错误——错误消息会在格式错误的调用位置变成编译器错误。例如，如果您尝试将宏调用为 `#fourCharacterCode("AB" + "CD")` ，编译器会显示错误“需要一个静态字符串”。

`expansion(of:in:)` 方法返回一个 `ExprSyntax` 实例，这是来自 SwiftSyntax 的一个类型，表示 AST 中的一个表达式。因为这个类型遵循 `StringLiteralConvertible` 协议，宏实现使用字符串字面量作为轻量级语法来创建其结果。您从宏实现返回的所有 SwiftSyntax 类型都遵循 `StringLiteralConvertible`，因此在实现任何类型的宏时都可以使用这种方法。

## [开发和调试宏](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/macros#Developing-and-Debugging-Macros)

宏非常适合使用测试进行开发：它们将一个 AST 转换为另一个 AST，既不依赖于任何外部状态，也不对任何外部状态进行更改。此外，您可以从字符串文字创建语法节点，这简化了为测试设置输入。您还可以读取 AST 的 `description` 属性以获取字符串，与预期值进行比较。例如，这里是对前几节中的 `#fourCharacterCode` 宏的测试：

```swift
let source: SourceFileSyntax =
    """
    let abcd = #fourCharacterCode("ABCD")
    """


let file = BasicMacroExpansionContext.KnownSourceFile(
    moduleName: "MyModule",
    fullFilePath: "test.swift"
)


let context = BasicMacroExpansionContext(sourceFiles: [source: file])


let transformedSF = source.expand(
    macros:["fourCharacterCode": FourCharacterCode.self],
    in: context
)


let expectedDescription =
    """
    let abcd = 1145258561 as UInt32
    """


precondition(transformedSF.description == expectedDescription)
```

上面的示例使用前置条件测试宏，但您可以改用测试框架。
