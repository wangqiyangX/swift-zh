---
description: 隐藏关于值类型的实现细节。
---

# 不透明和封装协议类型

> 隐藏关于值类型的实现细节。

Swift 提供两种隐藏值类型细节的方法：不透明类型和封装协议类型。在模块与调用模块的代码之间的边界处，隐藏类型信息是有用的，因为返回值的底层类型可以保持私有。

一个返回不透明类型的函数或方法隐藏了其返回值的类型信息。它不是提供一个具体类型作为函数的返回类型，而是通过支持的协议来描述返回值。不透明类型保留类型标识——编译器可以访问类型信息，但模块的客户端则不能。

一个包装的协议类型可以存储任何符合给定协议的类型的实例。包装协议类型不保留类型标识 — 值的具体类型直到运行时才会被知道，并且随着不同值的存储而变化。

## [不透明类型解决的问题](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/opaquetypes#The-Problem-That-Opaque-Types-Solve)

例如，假设您正在编写一个绘制 ASCII 艺术形状的模块。ASCII 艺术形状的基本特征是一个 `draw()` 函数，它返回该形状的字符串表示，您可以将其用作 `Shape` 协议的要求：

```swift
protocol Shape {
    func draw() -> String
}


struct Triangle: Shape {
    var size: Int
    func draw() -> String {
       var result: [String] = []
       for length in 1...size {
           result.append(String(repeating: "*", count: length))
       }
       return result.joined(separator: "\n")
    }
}
let smallTriangle = Triangle(size: 3)
print(smallTriangle.draw())
// *
// **
// ***
```

您可以使用泛型来实现操作，例如对形状进行垂直翻转，如下面的代码所示。然而，这种方法有一个重要的限制：翻转的结果**暴露了**用于创建它的确切泛型类型。

```swift
struct FlippedShape<T: Shape>: Shape {
    var shape: T
    func draw() -> String {
        let lines = shape.draw().split(separator: "\n")
        return lines.reversed().joined(separator: "\n")
    }
}
let flippedTriangle = FlippedShape(shape: smallTriangle)
print(flippedTriangle.draw())
// ***
// **
// *
```

这种定义一个 `JoinedShape<T: Shape, U: Shape>` 结构的方法，将两个形状垂直连接在一起，如下面的代码所示，会生成像 `JoinedShape<Triangle, FlippedShape<Triangle>>` 这样的类型，源自将一个三角形与一个翻转的三角形结合在一起。

```swift
struct JoinedShape<T: Shape, U: Shape>: Shape {
    var top: T
    var bottom: U
    func draw() -> String {
       return top.draw() + "\n" + bottom.draw()
    }
}
let joinedTriangles = JoinedShape(top: smallTriangle, bottom: flippedTriangle)
print(joinedTriangles.draw())
// *
// **
// ***
// ***
// **
// *
```

暴露有关形状创建的详细信息，使得那些不打算成为 ASCII 艺术模块公共接口一部分的类型因为需要声明完整返回类型而泄露出来。模块内部的代码可以以多种方式构建相同的形状，而使用该形状的模块外部其他代码不应该需要考虑关于变换列表的实现细节。像 `JoinedShape` 和 `FlippedShape` 这样的包装类型对模块的用户来说并不重要，它们不应该是可见的。模块的公共接口包括像连接和翻转形状这样的操作，而这些操作返回另一个 `Shape` 值。

## [返回一个不透明类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/opaquetypes#Returning-an-Opaque-Type)

您可以将**不透明类型视为泛型类型的反面**。泛型类型允许调用函数的代码选择该函数参数和返回值的类型，以一种与函数实现抽象脱离的方式。例如，以下代码中的函数返回一个依赖于其调用者的类型：

```swift
func max<T>(_ x: T, _ y: T) -> T where T: Comparable { ... }
```

调用 `max(_:_:)` 的代码选择 `x` 和 `y` 的值，而这些值的类型决定了 `T` 的具体类型。调用代码可以使用符合 `Comparable` 协议的任何类型。函数内部的代码是以通用的方式编写的，因此可以处理调用者提供的任何类型。 `max(_:_:)` 的实现仅使用所有 `Comparable` 类型共享的功能。

对于具有不透明返回类型的函数，这些角色是相反的。不透明类型允许函数实现选择返回值的类型，以一种与调用该函数的代码抽象脱离的方式。例如，下面示例中的函数返回一个梯形，而不暴露该形状的底层类型。

```swift
struct Square: Shape {
    var size: Int
    func draw() -> String {
        let line = String(repeating: "*", count: size)
        let result = Array<String>(repeating: line, count: size)
        return result.joined(separator: "\n")
    }
}


func makeTrapezoid() -> some Shape {
    let top = Triangle(size: 2)
    let middle = Square(size: 2)
    let bottom = FlippedShape(shape: top)
    let trapezoid = JoinedShape(
        top: top,
        bottom: JoinedShape(top: middle, bottom: bottom)
    )
    return trapezoid
}
let trapezoid = makeTrapezoid()
print(trapezoid.draw())
// *
// **
// **
// **
// **
// *
```

在这个示例中， `makeTrapezoid()` 函数声明其返回类型为 `some Shape` ；因此，该函数返回一个符合 `Shape` 协议的某种给定类型的值，而不指定任何特定的具体类型。以这种方式编写 `makeTrapezoid()` 可以表达其公共接口的基本方面——它返回的值是一个形状——而不将形状所构成的具体类型作为其公共接口的一部分。这个实现使用了两个三角形和一个正方形，但该函数可以被重写为以多种其他方式绘制梯形，而无需更改其返回类型。

这个例子强调了不透明返回类型如何像泛型类型的反面。 `makeTrapezoid()` 内的代码可以返回它需要的任何类型，只要该类型符合 `Shape` 协议，就像调用泛型函数的代码一样。调用该函数的代码需要以一般方式编写，就像泛型函数的实现一样，以便能够处理由 `makeTrapezoid()` 返回的任何 `Shape` 值。

您还可以将不透明返回类型与泛型结合使用。以下代码中的函数都返回符合 `Shape` 协议的某种类型的值。

```swift
func flip<T: Shape>(_ shape: T) -> some Shape {
    return FlippedShape(shape: shape)
}
func join<T: Shape, U: Shape>(_ top: T, _ bottom: U) -> some Shape {
    JoinedShape(top: top, bottom: bottom)
}


let opaqueJoinedTriangles = join(smallTriangle, flip(smallTriangle))
print(opaqueJoinedTriangles.draw())
// *
// **
// ***
// ***
// **
// *
```

在此示例中， `opaqueJoinedTriangles` 的值与本章前面“透明类型解决的问题”部分中的泛型示例中的 `joinedTriangles` 的值相同。然而，与该示例中的值不同， `flip(_:)` 和 `join(_:_:)` 将泛型形状操作返回的基础类型封装在一个不透明的返回类型中，这阻止了这些类型的可见性。两个函数都是泛型的，因为它们依赖的类型是泛型的，函数的类型参数传递给 `FlippedShape` 和 `JoinedShape` 所需的类型信息。

如果一个具有不透明返回类型的函数从多个地方返回，则所有可能的返回值必须具有相同的类型。对于泛型函数，该返回类型可以使用函数的泛型类型参数，但仍然必须是单一类型。例如，这是一个无效的形状翻转函数的版本，它为正方形包含了一个特殊情况：

```swift
func invalidFlip<T: Shape>(_ shape: T) -> some Shape {
    if shape is Square {
        return shape // Error: return types don't match
    }
    return FlippedShape(shape: shape) // Error: return types don't match
}
```

如果您使用 `Square` 调用此函数，它将返回 `Square` ；否则，它将返回 `FlippedShape` 。这违反了只返回一种类型值的要求，并且使 `invalidFlip(_:)` 成为无效代码。修复 `invalidFlip(_:)` 的一种方法是将正方形的特殊情况移入 `FlippedShape` 的实现中，这样可以让此函数始终返回一个 `FlippedShape` 值：

```swift
struct FlippedShape<T: Shape>: Shape {
    var shape: T
    func draw() -> String {
        if shape is Square {
           return shape.draw()
        }
        let lines = shape.draw().split(separator: "\n")
        return lines.reversed().joined(separator: "\n")
    }
}
```

始终返回单一类型的要求并不妨碍您在不透明返回类型中使用泛型。以下是一个将其类型参数融入返回值的基础类型中的函数示例：

```swift
func `repeat`<T: Shape>(shape: T, count: Int) -> some Collection {
    return Array<T>(repeating: shape, count: count)
}
```

在这种情况下，返回值的底层类型取决于 `T` : 无论传入什么形状， `repeat(shape:count:)` 都会创建并返回该形状的数组。然而，返回值始终具有相同的底层类型 `[T]` ，因此它遵循了具有不透明返回类型的函数必须仅返回单一类型值的要求。

## [封装协议类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/opaquetypes#Boxed-Protocol-Types)

封装协议类型有时也称为存在类型，它源自短语“存在一个类型 T，使得 T 遵循该协议”。要创建一个封装协议类型，在协议名称之前写 `any` 。以下是一个示例：

```swift
struct VerticalShapes: Shape {
    var shapes: [any Shape]
    func draw() -> String {
        return shapes.map { $0.draw() }.joined(separator: "\n\n")
    }
}


let largeTriangle = Triangle(size: 5)
let largeSquare = Square(size: 5)
let vertical = VerticalShapes(shapes: [largeTriangle, largeSquare])
print(vertical.draw())
```

在上面的示例中， `VerticalShapes` 将 `shapes` 的类型声明为 `[any Shape]` - 一个装箱 `Shape` 元素的数组。数组中的每个元素可以是不同的类型，并且这些类型中的每一个都必须符合 `Shape` 协议。为了支持这种运行时灵活性，Swift 在必要时添加了一层间接性 - 这种间接性称为盒子，并且它有性能成本。

在 `VerticalShapes` 类型中，代码可以使用 `Shape` 协议所需的方法、属性和下标。例如， `VerticalShapes` 的 `draw()` 方法在数组的每个元素上调用 `draw()` 方法。这个方法是可用的，因为 `Shape` 需要一个 `draw()` 方法。相反，尝试访问三角形的 `size` 属性，或任何其他不被 `Shape` 所需的属性或方法，会产生错误。

对比您可以用于 `shapes` 的三种类型：

- 通过编写 `struct VerticalShapes<S: Shape>` 和 `var shapes: [S]` 使用泛型，创建一个元素为某种特定形状类型的数组，并且该特定类型的身份对与数组交互的任何代码都是可见的。
- 通过编写 `var shapes: [some Shape]` 使用不透明类型，创建一个元素为某种特定形状类型的数组，并且该特定类型的身份是隐藏的。
- 使用一个盒装协议类型，通过写 `var shapes: [any Shape]` ，可以创建一个可以存储不同类型元素的数组，并且这些类型的身份是隐藏的。

在这种情况下，盒装协议类型是唯一允许 `VerticalShapes` 的调用者将不同种类的形状混合在一起的方法。

您可以在知道装箱值的底层类型时使用 `as` 类型转换。例如：

```swift
if let downcastTriangle = vertical.shapes[0] as? Triangle {
    print(downcastTriangle.size)
}
// Prints "5"
```

有关更多信息，请参见 Downcasting。

## [不透明类型和盒装协议类型之间的区别](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/opaquetypes#Differences-Between-Opaque-Types-and-Boxed-Protocol-Types)

返回不透明类型看起来与使用盒装协议类型作为函数的返回类型非常相似，但这两种返回类型的区别在于它们是否保留类型身份。不透明类型指的是一个特定类型，尽管函数的调用者无法看到哪种类型；而盒装协议类型可以指任何符合该协议的类型。一般而言，盒装协议类型在存储的值的基础类型上提供了更多灵活性，而不透明类型则让您对这些基础类型提供更强的保障。

例如，这里有一个版本的 `flip(_:)` 使用了盒装协议类型作为其返回类型，而不是不透明返回类型：

```swift
func protoFlip<T: Shape>(_ shape: T) -> Shape {
    return FlippedShape(shape: shape)
}
```

这个版本的 `protoFlip(_:)` 与 `flip(_:)` 拥有相同的主体，并且它总是返回相同类型的值。与 `flip(_:)` 不同， `protoFlip(_:)` 返回的值不要求始终具有相同的类型 - 它只需遵循 `Shape` 协议。换句话说， `protoFlip(_:)` 与其调用者的 API 合同比 `flip(_:)` 要松动得多。它保留了返回多种类型值的灵活性：

```swift
func protoFlip<T: Shape>(_ shape: T) -> Shape {
    if shape is Square {
        return shape
    }


    return FlippedShape(shape: shape)
}
```

修订后的代码返回一个 `Square` 实例或一个 `FlippedShape` 实例，具体取决于传入的形状。由此函数返回的两个翻转的形状可能具有完全不同的类型。此函数的其他有效版本在翻转多个相同形状的实例时可能会返回不同类型的值。来自 `protoFlip(_:)` 的不够具体的返回类型信息意味着许多依赖于类型信息的操作在返回值上不可用。例如，无法编写一个 `==` 运算符来比较此函数返回的结果。

```swift
let protoFlippedTriangle = protoFlip(smallTriangle)
let sameThing = protoFlip(smallTriangle)
protoFlippedTriangle == sameThing  // Error
```

示例最后一行的错误发生有几个原因。直接的问题是 `Shape` 没有将 `==` 操作符作为其协议要求的一部分。如果您尝试添加一个，接下来您会遇到的问题是 `==` 操作符需要知道其左侧和右侧参数的类型。这种操作符通常接受类型为 `Self` 的参数，匹配采用该协议的任何具体类型，但向协议添加 `Self` 要求并不允许在将协议用作类型时发生的类型擦除。

使用盒装协议类型作为函数的返回类型可以让您灵活地返回任何符合该协议的类型。然而，这种灵活性的代价是某些操作在返回值上不可用。示例显示了 `==` 运算符不可用——它依赖于特定的类型信息，而使用盒装协议类型时这些信息并未被保留。

这种方法的另一个问题是形状变换不嵌套。翻转三角形的结果是类型 `Shape` 的值，而 `protoFlip(_:)` 函数接受符合 `Shape` 协议的某种类型的参数。然而，封装协议类型的值并不符合该协议； `protoFlip(_:)` 返回的值不符合 `Shape` 。这意味着像 `protoFlip(protoFlip(smallTriangle))` 这样应用多个变换的代码是无效的，因为翻转的形状不是 `protoFlip(_:)` 的有效参数。

相反，opaque 类型保留了底层类型的身份。Swift 可以推断关联类型，这使得您可以在无法将装箱协议类型用作返回值的地方使用不透明返回值。例如，这是来自 Generics 的 `Container` 协议的一个版本：

```swift
protocol Container {
    associatedtype Item
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
extension Array: Container { }
```

您不能将 `Container` 用作函数的返回类型，因为该协议具有关联类型。您也不能将其用作泛型返回类型中的约束，因为在函数体外没有足够的信息来推断泛型类型需要是什么。

```swift
// Error: Protocol with associated types can't be used as a return type.
func makeProtocolContainer<T>(item: T) -> Container {
    return [item]
}


// Error: Not enough information to infer C.
func makeProtocolContainer<T, C: Container>(item: T) -> C {
    return [item]
}
```

使用不透明类型 `some Container` 作为返回类型表达了期望的 API 合同——该函数返回一个容器，但拒绝指定容器的类型：

```swift
func makeOpaqueContainer<T>(item: T) -> some Container {
    return [item]
}
let opaqueContainer = makeOpaqueContainer(item: 12)
let twelve = opaqueContainer[0]
print(type(of: twelve))
// Prints "Int"
```

`twelve` 的类型被推断为 `Int` ，这说明类型推断可以与不透明类型一起工作。在 `makeOpaqueContainer(item:)` 的实现中，不透明容器的基础类型是 `[T]` 。在这种情况下， `T` 是 `Int` ，因此返回值是一个整数数组，并且与 `Item` 相关的类型被推断为 `Int` 。对 `Container` 的下标访问返回 `Item` ，这意味着 `twelve` 的类型也被推断为 `Int` 。
