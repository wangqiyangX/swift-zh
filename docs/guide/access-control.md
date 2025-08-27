# 访问控制

> 通过声明、文件和模块管理代码的可见性。

访问控制限制了来自其他源文件和模块的代码对您代码某些部分的访问。此功能使您能够隐藏代码的实现细节，并指定一个首选接口，通过该接口可以访问和使用该代码。

您可以为单个类型（类、结构和枚举）以及属于这些类型的属性、方法、初始化器和下标分配特定的访问级别。协议可以限制在特定上下文中，全局常量、变量和函数也可以如此。

除了提供各种级别的访问控制外，Swift 通过为典型场景提供默认访问级别，减少了指定显式访问控制级别的需要。实际上，如果您正在编写单目标应用程序，您可能根本不需要指定显式访问控制级别。

> 注意
>
> 在下面的部分中，您的代码的各个方面（属性、类型、函数等）可以应用访问控制，这些方面被称为“实体”。

## [模块、源文件和包](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Modules-Source-Files-and-Packages)

Swift 的访问控制模型基于模块、源文件和包的概念。

模块是一个单一的代码分发单元——一个作为单一单元构建和发布的框架或应用程序，可以通过 Swift 的 `import` 关键字被另一个模块导入。

Xcode 中的每个构建目标（例如应用程序包或框架）在 Swift 中被视为一个单独的模块。如果您将应用程序代码的某些方面组合在一起作为一个独立的框架——也许是为了封装和在多个应用程序中重用该代码——那么在该框架中定义的所有内容在被导入并在应用程序中使用时，或者在另一个框架中使用时，都会成为一个单独模块的一部分。

源文件是模块内的单个 Swift 源代码文件（实际上是应用程序或框架内的单个文件）。虽然通常在单独的源文件中定义各个类型，但单个源文件可以包含多个类型、函数等的定义。

包是您作为一个单元开发的一组模块。您在使用的构建系统的配置中定义形成一个包的模块，而不是作为您的 Swift 源代码的一部分。例如，如果您使用 Swift 包管理器来构建您的代码，您可以在您的 `Package.swift` 文件中使用 PackageDescription 模块的 API 定义一个包；如果您使用 Xcode，您可以在包访问标识符构建设置中指定包的名称。

## [访问级别](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Access-Levels)

Swift 为您代码中的实体提供六种不同的访问级别。这些访问级别相对于定义实体的源文件、该源文件所属的模块，以及该模块所属的包而定。

- 开放访问和公共访问允许实体在其定义模块的任何源文件中使用，也可以在导入定义模块的另一个模块的源文件中使用。当您指定框架的公共接口时，通常使用开放或公共访问。开放访问与公共访问之间的区别在下面描述。
- 包访问允许实体在其定义包的任何源文件中使用，但不允许在该包外的任何源文件中使用。您通常在结构化为多个模块的应用或框架中使用包访问。
- 内部访问允许实体在其定义模块的任何源文件中使用，但不允许在该模块外的任何源文件中使用。您通常在定义应用或框架的内部结构时使用内部访问。
- 文件私有访问限制实体的使用仅限于其定义的源文件。使用文件私有访问来隐藏特定功能的实现细节，当这些细节在整个文件中使用时。
- 私有访问限制实体的使用仅限于封闭声明，以及位于同一文件中的该声明的扩展。使用私有访问来隐藏特定功能的实现细节，当这些细节仅在单个声明中使用时。

开放访问是最高（限制最少）的访问级别，私有访问是最低（限制最多）的访问级别。

开放访问仅适用于类和类成员，它与公共访问的不同之处在于允许模块外的代码进行子类化和重写，如下文的子类化部分所讨论的。将类标记为开放明确表示您已考虑到其他模块使用该类作为超类的代码的影响，并且您已相应地设计了类的代码。

### [访问级别的指导原则](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Guiding-Principle-of-Access-Levels)

Swift 中的访问级别遵循一个总体指导原则：没有实体可以以另一个具有较低（限制更多）访问级别的实体来定义。

例如：

- 公共变量不能被定义为具有内部、文件私有或私有类型，因为该类型可能不会在公共变量使用的每个地方都可用。
- 一个函数不能比其参数类型和返回类型有更高的访问级别，因为该函数可能在其组成类型对周围代码不可用的情况下被使用。

这个指导原则对语言不同方面的具体影响将在下面详细介绍。

### [默认访问级别](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Default-Access-Levels)

您代码中的所有实体（有一些特定的例外，稍后在本章中描述）如果您不明确指定访问级别，则默认访问级别为 internal。因此，在许多情况下，您无需在代码中指定明确的访问级别。

### [单目标应用程序的访问级别](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Access-Levels-for-Single-Target-Apps)

当您编写一个简单的单目标应用程序时，应用程序中的代码通常是自包含的，不需要在应用程序模块之外提供。默认的访问级别 internal 已经满足了这个要求。因此，您无需指定自定义访问级别。但是，您可能希望将代码中的某些部分标记为 file private 或 private，以便隐藏它们的实现细节，防止被应用程序模块中的其他代码访问。

### [框架的访问级别](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Access-Levels-for-Frameworks)

当您开发一个框架时，将该框架的公共接口标记为开放或公共，以便其他模块（如导入该框架的应用程序）可以查看和访问。这个公共接口就是该框架的应用程序编程接口（或 API）。

> 注意
>
> 您框架的任何内部实现细节仍然可以使用默认访问级别 internal，或者如果您想将其隐藏在框架的其他内部代码中，可以标记为 private 或 file private。只有当您希望某个实体成为您框架 API 的一部分时，您才需要将其标记为 open 或 public。

### [单元测试目标的访问级别](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Access-Levels-for-Unit-Test-Targets)

当您编写一个具有单元测试目标的应用程序时，您的应用程序中的代码需要对该模块可用以进行测试。默认情况下，只有标记为开放或公共的实体可供其他模块访问。然而，单元测试目标可以访问任何内部实体，只要您使用 `@testable` 属性标记产品模块的导入声明，并以启用测试的方式编译该产品模块。

## [访问控制语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Access-Control-Syntax)

通过在实体声明的开头放置 `open` 、 `public` 、 `internal` 、 `fileprivate` 或 `private` 修饰符来定义实体的访问级别。

```swift
open class SomeOpenClass {}
public class SomePublicClass {}
internal class SomeInternalClass {}
fileprivate class SomeFilePrivateClass {}
private class SomePrivateClass {}


open var someOpenVariable = 0
public var somePublicVariable = 0
internal let someInternalConstant = 0
fileprivate func someFilePrivateFunction() {}
private func somePrivateFunction() {}
```

除非另有说明，默认访问级别为内部，如默认访问级别中所述。这意味着 `SomeInternalClass` 和 `someInternalConstant` 可以在没有显式访问级别修饰符的情况下编写，并且仍将具有内部访问级别：

```swift
class SomeInternalClass {}              // implicitly internal
let someInternalConstant = 0            // implicitly internal
```

## [自定义类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Custom-Types)

如果您想为自定义类型指定一个明确的访问级别，请在定义该类型时进行。然后，可以在其访问级别允许的地方使用新类型。例如，如果您定义了一个文件私有类，则该类只能在定义该文件私有类的源文件中用作属性的类型，或作为函数参数或返回类型。

类型的访问控制级别也会影响该类型成员（其属性、方法、初始化器和下标）的默认访问级别。如果将类型的访问级别定义为 private 或 file private，则其成员的默认访问级别也将是 private 或 file private。如果将类型的访问级别定义为 internal 或 public（或在未明确指定访问级别的情况下使用 internal 的默认访问级别），则该类型成员的默认访问级别将是 internal。

> 重要
>
> 公共类型默认具有内部成员，而不是公共成员。如果您希望类型成员为公共的，必须明确将其标记为公共。这一要求确保了类型的公共 API 是您选择发布的内容，并避免错误地将类型的内部工作呈现为公共 API。

```swift
public class SomePublicClass {                   // explicitly public class
    public var somePublicProperty = 0            // explicitly public class member
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}


class SomeInternalClass {                        // implicitly internal class
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}


fileprivate class SomeFilePrivateClass {         // explicitly file-private class
    func someFilePrivateMethod() {}              // implicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}


private class SomePrivateClass {                 // explicitly private class
    func somePrivateMethod() {}                  // implicitly private class member
}
```

### [元组类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Tuple-Types)

元组类型的访问级别是该元组中所有类型的最严格访问级别。例如，如果您从两种不同的类型组合一个元组，一种具有内部访问权限，另一种具有私有访问权限，则该复合元组类型的访问级别将是私有的。

> 注意
>
> 元组类型没有像类、结构、枚举和函数那样的独立定义。元组类型的访问级别是根据构成元组类型的类型自动确定的，无法显式指定。

### [函数类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Function-Types)

函数类型的访问级别是根据函数的参数类型和返回类型的最严格访问级别计算的。如果函数的计算访问级别与上下文默认值不匹配，则必须在函数的定义中明确指定访问级别。

下面的示例定义了一个名为 `someFunction()` 的全局函数，没有为该函数本身提供特定的访问级别修饰符。您可能期望这个函数具有“内部”的默认访问级别，但事实并非如此。实际上， `someFunction()` 将无法按如下所示编译：

```swift
func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // function implementation goes here
}
```

该函数的返回类型是由上述自定义类中两个组成的元组类型。这些类之一被定义为内部，另一个被定义为私有。因此，复合元组类型的整体访问级别是私有（元组组成类型的最小访问级别）。

由于函数的返回类型是私有的，您必须在函数声明中使用 `private` 修饰符来标记函数的整体访问级别，以使其有效：

```swift
private func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // 函数实现
}
```

将 `someFunction()` 的定义标记为 `public` 或 `internal` 修饰符是不合法的，或者使用内部的默认设置，因为函数的公有或内部用户可能无法适当地访问函数返回类型中使用的私有类。

### [枚举类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Enumeration-Types)

枚举的各个案例自动接收与它们所属的枚举相同的访问级别。您不能为单个枚举案例指定不同的访问级别。

在下面的示例中， `CompassPoint` 枚举具有显式的公共访问级别。因此，枚举案例 `north` 、 `south` 、 `east` 和 `west` 也具有公共访问级别：

```swift
public enum CompassPoint {
    case north
    case south
    case east
    case west
}
```

[**原始值和关联值**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Raw-Values-and-Associated-Values)

在枚举定义中用于任何原始值或关联值的类型，其访问级别必须至少与枚举的访问级别相同。例如，您不能使用私有类型作为具有内部访问级别的枚举的原始值类型。

### [嵌套类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Nested-Types)

嵌套类型的访问级别与其包含类型相同，除非包含类型是公共的。在公共类型中定义的嵌套类型具有自动的内部访问级别。如果您希望公共类型中的嵌套类型可以公开可用，您必须明确将嵌套类型声明为公共。

## [子类化](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Subclassing)

您可以子类化在当前访问上下文中可以访问的任何类，并且该类定义在与子类相同的模块中。您还可以子类化在不同模块中定义的任何开放类。子类的访问级别不能比其超类高—例如，您不能编写内部超类的公共子类。

此外，对于在同一模块中定义的类，您可以重写在某个访问上下文中可见的任何类成员（方法、属性、初始化器或下标）。对于在另一个模块中定义的类，您可以重写任何开放类成员。

一个重写可以使继承类成员比其超类版本更易于访问。在下面的示例中，类 `A` 是一个具有文件私有方法 `someMethod()` 的公共类。类 `B` 是 `A` 的子类，其访问级别降低为“internal”。尽管如此，类 `B` 提供了 `someMethod()` 的重写，其访问级别为“internal”，这高于 `someMethod()` 的原始实现：

```swift
public class A {
    fileprivate func someMethod() {}
}


internal class B: A {
    override internal func someMethod() {}
}
```

即使子类成员调用的超类成员的访问权限低于子类成员，这也是有效的，只要对超类成员的调用发生在允许的访问级别上下文中（即，对于文件私有成员调用，发生在与超类相同的源文件中，或者对于内部成员调用，发生在与超类相同的模块中）：

```swift
public class A {
    fileprivate func someMethod() {}
}


internal class B: A {
    override internal func someMethod() {
        super.someMethod()
    }
}
```

因为超类 `A` 和子类 `B` 定义在同一个源文件中，因此 `B` 对 `someMethod()` 的实现可以调用 `super.someMethod()` 。

## [常量、变量、属性和下标](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Constants-Variables-Properties-and-Subscripts)

常量、变量或属性不能比其类型更公开。例如，写一个私有类型的公共属性是不合法的。同样，下标不能比其索引类型或返回类型更公开。

如果常量、变量、属性或下标使用了私有类型，则常量、变量、属性或下标也必须标记为 `private` :

```swift
private var privateInstance = SomePrivateClass()
```

### [获取器和设置器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Getters-and-Setters)

常量、变量、属性和下标的 `getter` 和 `setter` 自动继承其所属常量、变量、属性或下标的访问级别。

您可以为 `setter` 指定一个比相应的 `getter` 低的访问级别，以限制该变量、属性或下标的读写范围。通过在 `var` 或 `subscript` 引入者之前写入 `fileprivate(set)` 、 `private(set)` 、 `internal(set)` 或 `package(set)` 来指定较低的访问级别。

> 注意
>
> 此规则适用于存储属性和计算属性。尽管您不为存储属性编写显式的 `getter` 和 `setter`，Swift 仍然为您合成了隐式的 `getter` 和 `setter`，以提供对存储属性后备存储的访问。使用 `fileprivate(set)` 、 `private(set)` 、 `internal(set)` 和 `package(set)` 以与计算属性中的显式 `setter` 完全相同的方式更改此合成 `setter` 的访问级别。

下面的示例定义了一个名为 `TrackedString` 的结构，该结构跟踪字符串属性被修改的次数：

```swift
struct TrackedString {
    private(set) var numberOfEdits = 0
    var value: String = "" {
        didSet {
            numberOfEdits += 1
        }
    }
}
```

`TrackedString` 结构定义了一个名为 `value` 的存储字符串属性，初始值为 `""` （一个空字符串）。该结构还定义了一个名为 `numberOfEdits` 的存储整数属性，用于跟踪 `value` 被修改的次数。这种修改跟踪通过在 `value` 属性上使用 `didSet` 属性观察器来实现，每当 `value` 属性被设置为新值时， `numberOfEdits` 增加 1。

`TrackedString` 结构和 `value` 属性没有提供显式的访问级别修饰符，因此它们都接收默认的访问级别 `internal`。然而， `numberOfEdits` 属性的访问级别标记为 `private(set)` 修饰符，以指示该属性的 `getter` 仍然具有默认的访问级别 `internal`，但该属性只能在 `TrackedString` 结构内部的代码中设置。这使得 `TrackedString` 可以在内部修改 `numberOfEdits` 属性，但在结构定义外部使用时将该属性呈现为只读属性。

如果您创建一个 `TrackedString` 实例并多次修改其字符串值，您可以看到 `numberOfEdits` 属性值更新以匹配修改次数：

```swift
var stringToEdit = TrackedString()
stringToEdit.value = "This string will be tracked."
stringToEdit.value += " This edit will increment numberOfEdits."
stringToEdit.value += " So will this one."
print("The number of edits is \(stringToEdit.numberOfEdits)")
// Prints "The number of edits is 3"
```

虽然您可以从另一个源文件中查询 `numberOfEdits` 属性的当前值，但不能从另一个源文件修改该属性。这一限制保护了 `TrackedString` 编辑跟踪功能的实现细节，同时仍提供了对该功能某一方面的便捷访问。

请注意，如果需要，您可以为 `getter` 和 `setter` 分配显式访问级别。下面的示例显示了一个 `TrackedString` 结构的版本，其中结构被定义为具有显式的 `public` 访问级别。因此，结构的成员（包括 `numberOfEdits` 属性）默认具有 `internal` 访问级别。您可以通过结合 `public` 和 `private(set)` 访问级别修饰符，使结构的 `numberOfEdits` 属性的 `getter` 为 `public`，而其属性的 `setter` 为 `private`：

```swift
public struct TrackedString {
    public private(set) var numberOfEdits = 0
    public var value: String = "" {
        didSet {
            numberOfEdits += 1
        }
    }
    public init() {}
}
```

## [初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Initializers)

自定义初始化器的访问级别可以小于或等于它们初始化的类型。唯一的例外是必需的初始化器（如[必需的初始化器](initialization.md#必需的初始化器)中定义的）。必需的初始化器必须具有与其所属类相同的访问级别。

与函数和方法参数一样，初始化器参数的类型不能比初始化器自身的访问级别更私有。

### [默认初始值设定项](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Default-Initializers)

如[默认初始化器](initialization.md#默认初始化器)中所述，Swift 自动为任何提供所有属性默认值且自身没有提供至少一个初始化器的结构或基类提供一个没有任何参数的默认初始化器。

默认初始化器具有与其初始化的类型相同的访问级别，除非该类型定义为 `public` 。对于定义为 `public` 的类型，默认初始化器被视为内部的。如果您希望一个公共类型在另一个模块中使用无参数初始化器时可被初始化，您必须在类型定义中显式提供一个公共无参数初始化器。

### [结构类型的默认成员初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Default-Memberwise-Initializers-for-Structure-Types)

如果结构的任何存储属性是私有的，则结构类型的默认成员初始化器被视为私有。同样，如果结构的任何存储属性是文件私有的，则初始化器是文件私有的。否则，初始化器的访问级别为内部。

与上述默认初始化器一样，如果您希望公共结构类型在另一个模块中使用时可以通过成员初始化器初始化，则必须作为类型定义的一部分自行提供公共成员初始化器。

## [协议](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Protocols)

如果您想为协议类型分配显式访问级别，请在定义协议时进行。这使您能够创建只能在特定访问上下文中采纳的协议。

协议定义中每个需求的访问级别会自动设置为与协议相同的访问级别。您无法将协议需求设置为与其支持的协议不同的访问级别。这确保了所有协议的需求在任何采用该协议的类型上都是可见的。

> 注意
>
> 如果您定义一个公共协议，则协议的要求在实现时需要公共访问级别。这种行为与其他类型不同，公共类型定义意味着该类型成员的访问级别为内部。

### [协议继承](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Protocol-Inheritance)

如果您定义一个从现有协议继承的新协议，则新协议的访问级别最多可以与其继承的协议相同。例如，您不能编写一个从内部协议继承的公共协议。

### [协议符合性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Protocol-Conformance)

一个类型可以符合一个访问级别低于该类型本身的协议。例如，您可以定义一个公共类型，可以在其他模块中使用，但其对内部协议的符合性只能在内部协议的定义模块内使用。

一个类型符合特定协议的上下文是该类型的访问级别和协议的访问级别中的最小值。例如，如果一个类型是公共的，但它符合的协议是内部的，则该类型对该协议的符合性也是内部的。

当您编写或扩展一个类型以符合一个协议时，必须确保该类型对每个协议要求的实现至少具有与该类型对该协议的符合性相同的访问级别。例如，如果一个公共类型符合一个内部协议，则该类型对每个协议要求的实现必须至少是内部的。

> 注意
>
> 在 Swift 中，与 Objective-C 一样，协议遵循是全局的——在同一个程序中，类型不可能以两种不同的方式遵循一个协议。

## [扩展](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Extensions)

您可以在类、结构或枚举可用的任何访问上下文中扩展它们。 在扩展中添加的任何类型成员具有与被扩展的原始类型中声明的类型成员相同的默认访问级别。如果您扩展公共或内部类型，您添加的任何新类型成员的默认访问级别为内部。如果您扩展文件私有类型，您添加的任何新类型成员的默认访问级别为文件私有。如果您扩展私有类型，您添加的任何新类型成员的默认访问级别为私有。

另外，您可以使用显式访问级别修饰符（例如， `private` ）来标记扩展，以为扩展内定义的所有成员设置新的默认访问级别。这个新的默认值仍然可以在扩展内为个别类型成员被覆盖。

如果您使用扩展来添加协议符合性，则无法为扩展提供显式的访问级别修饰符。相反，协议的自身访问级别用于为扩展中每个协议要求的实现提供默认访问级别。

### [扩展中的私有成员](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Private-Members-in-Extensions)

扩展与其扩展的类、结构或枚举在同一文件中的行为就像扩展中的代码是原始类型声明的一部分。因此，您可以：

- 在原始声明中声明一个私有成员，并在同一文件中的扩展中访问该成员。
- 在一个扩展中声明一个私有成员，并在同一个文件的另一个扩展中访问该成员。
- 在扩展中声明一个私有成员，并从同一文件中的原始声明访问该成员。

这种行为意味着您可以以相同的方式使用扩展来组织代码，无论您的类型是否具有私有实体。例如，给定以下简单协议：

```swift
protocol SomeProtocol {
    func doSomething()
}
```

您可以使用扩展添加协议遵循，如下所示：

```swift
struct SomeStruct {
    private var privateVariable = 12
}


extension SomeStruct: SomeProtocol {
    func doSomething() {
        print(privateVariable)
    }
}
```

## [泛型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Generics)

泛型类型或泛型函数的访问级别是泛型类型或函数本身的访问级别与其类型参数上的任何类型约束的访问级别的最小值。

## [类型别名](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/accesscontrol/#Type-Aliases)

您定义的任何类型别名在访问控制方面被视为不同的类型。类型别名的访问级别可以小于或等于它所别名的类型的访问级别。例如，私有类型别名可以别名私有、文件私有、内部、公共或开放类型，但公共类型别名不能别名内部、文件私有或私有类型。

> 注意
>
> 此规则也适用于用于满足协议一致性的相关类型的类型别名。
