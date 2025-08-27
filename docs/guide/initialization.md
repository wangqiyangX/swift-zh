---
description: 为类型的存储属性设置初始值并执行一次性设置。
---

# 初始化

> 为类型的存储属性设置初始值并执行一次性设置。

初始化是准备类、结构或枚举实例以供使用的过程。此过程涉及为该实例的每个存储属性设置初始值，并执行任何在新实例准备好使用之前所需的其他设置或初始化。

您通过定义初始化器来实现此初始化过程，初始化器就像可以被调用以创建特定类型的新实例的特殊方法。与 Objective-C 初始化器不同，Swift 初始化器不返回值。它们的主要作用是确保在首次使用之前，类型的新实例被正确初始化。

类类型的实例还可以实现析构函数，在该类的实例被释放之前执行任何自定义清理。有关析构函数的更多信息，请参见析构。

## [为存储属性设置初始值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Setting-Initial-Values-for-Stored-Properties)

类和结构体必须在创建该类或结构体的实例时将其所有存储属性设置为适当的初始值。存储属性不能处于不确定状态。

您可以在初始化器中为存储属性设置初始值，或者通过将默认属性值作为属性定义的一部分进行赋值。这些操作在以下部分中进行了描述。

> 注意
>
> 当您为存储属性分配默认值，或在初始化器中设置其初始值时，该属性的值会直接设置，而不会调用任何属性观察者。

### [初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Initializers)

初始化器被调用以创建特定类型的新实例。在最简单的形式中，初始化器就像一个没有参数的实例方法，使用 `init` 关键字编写：

```swift
init() {
    // perform some initialization here
}
```

下面的示例定义了一个名为 `Fahrenheit` 的新结构，用于存储以华氏温度表示的温度。 `Fahrenheit` 结构有一个存储属性 `temperature` ，其类型为 `Double` ：

```swift
struct Fahrenheit {
    var temperature: Double
    init() {
        temperature = 32.0
    }
}
var f = Fahrenheit()
print("The default temperature is \(f.temperature)° Fahrenheit")
// Prints "The default temperature is 32.0° Fahrenheit"
```

该结构定义了一个单一的初始化器， `init` ，没有参数，它将存储的温度初始化为 `32.0` 的值（水的冰点，单位为华氏度）。

### [默认属性值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Default-Property-Values)

您可以在初始化器中设置存储属性的初始值，如上所示。或者，将默认属性值作为属性声明的一部分进行指定。您通过在定义属性时为其分配初始值来指定默认属性值。

> 注意
>
> 如果一个属性总是采用相同的初始值，提供一个默认值，而不是在初始器中设置一个值。最终结果是相同的，但默认值使属性的初始化与其声明更紧密地联系在一起。这使得初始器更短、更清晰，并使您能够根据默认值推断属性的类型。默认值还使您更容易利用默认初始器和初始器继承，如本章后面所述。

您可以通过在属性声明时为其 `temperature` 属性提供默认值，以更简单的形式编写上述 `Fahrenheit` 结构：

```swift
struct Fahrenheit {
    var temperature = 32.0
}
```

## [自定义初始化](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Customizing-Initialization)

您可以通过输入参数和可选属性类型自定义初始化过程，或者通过在初始化期间分配常量属性，如以下部分所述。

### [初始化参数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Initialization-Parameters)

您可以将初始化参数作为初始化器定义的一部分提供，以定义自定义初始化过程的值的类型和名称。初始化参数具有与函数和方法参数相同的能力和语法。

以下示例定义了一个名为 `Celsius` 的结构，它存储以摄氏度表示的温度。 `Celsius` 结构实现了两个自定义初始化器，分别称为 `init(fromFahrenheit:)` 和 `init(fromKelvin:)` ，它们使用不同温度刻度的值初始化结构的新实例：

```swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
}
let boilingPointOfWater = Celsius(fromFahrenheit: 212.0)
// boilingPointOfWater.temperatureInCelsius is 100.0
let freezingPointOfWater = Celsius(fromKelvin: 273.15)
// freezingPointOfWater.temperatureInCelsius is 0.0
```

第一个初始化器有一个初始化参数，参数标签为 `fromFahrenheit` ，参数名称为 `fahrenheit` 。第二个初始化器有一个初始化参数，参数标签为 `fromKelvin` ，参数名称为 `kelvin` 。两个初始化器都将它们的单个参数转换为相应的摄氏度值，并将该值存储在名为 `temperatureInCelsius` 的属性中。

### [参数名称和参数标签](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Parameter-Names-and-Argument-Labels)

与函数和方法参数一样，初始化参数可以具有在初始化器主体内使用的参数名称和在调用初始化器时使用的参数标签。

然而，初始化器在其括号之前没有像函数和方法那样的标识性名称。因此，初始化器参数的名称和类型在确定应该调用哪个初始化器时发挥了特别重要的作用。因此，Swift 为初始化器中的每个参数提供了一个自动参数标签，如果您没有提供的话。

以下示例定义了一个名为 `Color` 的结构，具有三个名为 `red` 、 `green` 和 `blue` 的常量属性。这些属性存储一个介于 `0.0` 和 `1.0` 之间的值，以指示颜色中的红色、绿色和蓝色的数量。

`Color` 提供了一个初始化器，具有三个适当命名的类型为 `Double` 的参数，用于其红色、绿色和蓝色组件。 `Color` 还提供了一个第二个初始化器，具有一个 `white` 参数，用于为所有三个颜色组件提供相同的值。

```swift
struct Color {
    let red, green, blue: Double
    init(red: Double, green: Double, blue: Double) {
        self.red   = red
        self.green = green
        self.blue  = blue
    }
    init(white: Double) {
        red   = white
        green = white
        blue  = white
    }
}
```

两种初始化器都可以通过为每个初始化器参数提供命名值来创建一个新的 `Color` 实例：

```swift
let magenta = Color(red: 1.0, green: 0.0, blue: 1.0)
let halfGray = Color(white: 0.5)
```

请注意，如果不使用参数标签，则无法调用这些初始化器。如果定义了参数标签，则在初始化器中必须始终使用它们，省略它们将导致编译时错误：

```swift
let veryGreen = Color(0.0, 1.0, 0.0)
// this reports a compile-time error - argument labels are required
```

### [没有参数标签的初始化器参数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Initializer-Parameters-Without-Argument-Labels)

如果您不想为初始化器参数使用参数标签，请在该参数的显式参数标签位置写下一个下划线 ( `_` ) 以覆盖默认行为。

这是上述初始化参数 `Celsius` 示例的扩展版本，增加了一个初始化器，用于从已经在摄氏度中的 `Double` 值创建新的 `Celsius` 实例：

```swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
    init(_ celsius: Double) {
        temperatureInCelsius = celsius
    }
}
let bodyTemperature = Celsius(37.0)
// bodyTemperature.temperatureInCelsius is 37.0
```

初始化调用 `Celsius(37.0)` 的意图明确，无需参数标签。因此，适合将此初始化写为 `init(_ celsius: Double)` ，以便可以通过提供未命名的 `Double` 值来调用。

### [可选的属性类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Optional-Property-Types)

如果您的自定义类型有一个逻辑上允许“没有值”的存储属性——可能是因为它的值在初始化期间无法设置，或者因为它在某个后期时间允许“没有值”——请将该属性声明为可选类型。可选类型的属性在初始化时自动初始化为值 `nil` ，表示该属性在初始化期间故意设定为“尚无值”。

以下示例定义了一个名为 `SurveyQuestion` 的类，具有一个可选的 `String` 属性，名为 `response` :

```swift
class SurveyQuestion {
    var text: String
    var response: String?
    init(text: String) {
        self.text = text
    }
    func ask() {
        print(text)
    }
}
let cheeseQuestion = SurveyQuestion(text: "Do you like cheese?")
cheeseQuestion.ask()
// Prints "Do you like cheese?"
cheeseQuestion.response = "Yes, I do like cheese."
```

对调查问题的响应在提出之前是无法知道的，因此 `response` 属性被声明为 `String?` 类型，或称为“可选 `String` ”。当新实例 `SurveyQuestion` 被初始化时，它会自动赋值为默认值 `nil` ，意味着“还没有字符串”。

### [在初始化期间分配常量属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Assigning-Constant-Properties-During-Initialization)

您可以在初始化的任何时候为常量属性分配一个值，只要在初始化完成时将其设置为一个确定的值。一旦常量属性被赋值，就无法进一步修改。

> 注意
>
> 对于类实例，常量属性只能由引入它的类在初始化期间修改。子类无法修改它。

您可以修改上述的 `SurveyQuestion` 示例，使用常量属性而不是变量属性来表示问题的 `text` 属性，以指示一旦创建了 `SurveyQuestion` 的实例，问题就不会改变。尽管 `text` 属性现在是一个常量，但仍然可以在类的初始化器中设置：

```swift
class SurveyQuestion {
    let text: String
    var response: String?
    init(text: String) {
        self.text = text
    }
    func ask() {
        print(text)
    }
}
let beetsQuestion = SurveyQuestion(text: "How about beets?")
beetsQuestion.ask()
// Prints "How about beets?"
beetsQuestion.response = "I also like beets. (But not with cheese.)"
```

## [默认初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Default-Initializers)

Swift 为任何提供所有属性的默认值并且自身没有提供至少一个初始化器的结构或类提供了一个默认初始化器。默认初始化器只是创建一个新实例，其所有属性都设置为默认值。

此示例定义了一个名为 `ShoppingListItem` 的类，它封装了购物清单中项目的名称、数量和购买状态：

```swift
class ShoppingListItem {
    var name: String?
    var quantity = 1
    var purchased = false
}
var item = ShoppingListItem()
```

由于 `ShoppingListItem` 类的所有属性都有默认值，并且它是没有超类的基类，因此 `ShoppingListItem` 自动获得一个默认初始化器实现，该实现创建一个新实例，其所有属性都设置为默认值。（ `name` 属性是一个可选的 `String` 属性，因此它自动接收一个默认值 `nil` ，即使这个值在代码中没有被写入。）上面的示例使用 `ShoppingListItem` 类的默认初始化器以初始化器语法创建该类的新实例，书写为 `ShoppingListItem()` ，并将这个新实例分配给名为 `item` 的变量。

### [结构类型的成员初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Memberwise-Initializers-for-Structure-Types)

结构类型如果没有定义任何自定义初始化器，则会自动接收一个成员初始化器。与默认初始化器不同，即使结构具有没有默认值的存储属性，它也会接收一个成员初始化器。

成员按位初始化器是初始化新结构实例的成员属性的一种简写方式。可以通过名称将新实例的属性的初始值传递给成员按位初始化器。

下面的示例定义了一个名为 `Size` 的结构，具有两个名为 `width` 和 `height` 的属性。通过赋予默认值 `0.0` ，这两个属性被推断为类型 `Double` 。

The `Size` 结构自动接收一个 `init(width:height:)` 成员初始化器，您可以使用它来初始化一个新的 `Size` 实例：

```swift
struct Size {
    var width = 0.0, height = 0.0
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```

当您调用成员初始化器时，可以省略任何具有默认值的属性的值。在上面的示例中， `Size` 结构的 `height` 和 `width` 属性都有默认值。您可以省略任一属性或两个属性，初始化器将使用您省略的任何内容的默认值。例如：

```swift
let zeroByTwo = Size(height: 2.0)
print(zeroByTwo.width, zeroByTwo.height)
// Prints "0.0 2.0"


let zeroByZero = Size()
print(zeroByZero.width, zeroByZero.height)
// Prints "0.0 0.0"
```

## [值类型的初始化委托](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Initializer-Delegation-for-Value-Types)

初始化函数可以调用其他初始化函数以执行实例初始化的一部分。这个过程称为初始化函数委托，避免了在多个初始化函数中重复代码。

初始化函数委托的规则，以及允许的委托形式，对于值类型和类类型是不同的。值类型（结构体和枚举）不支持继承，因此它们的初始化函数委托过程相对简单，因为它们只能委托给自己提供的另一个初始化函数。然而，类可以从其他类继承，如继承一节所述。这意味着类在确保它们继承的所有存储属性在初始化期间被分配合适的值方面有额外的责任。这些责任在下面的类继承和初始化中描述。

对于值类型，在编写自定义初始化函数时，您可以使用 `self.init` 来引用同一值类型中的其他初始化函数。您只能在初始化函数内部调用 `self.init` 。

请注意，如果您为值类型定义了自定义初始化器，将不再能够访问该类型的默认初始化器（如果是结构，则为成员初始化器）。这个限制防止了由于有人使用自动初始化器而意外绕过在更复杂的初始化器中提供的其他必要设置的情况。

> 注意
>
> 如果您希望自定义值类型能够使用默认初始化器和成员初始化器进行初始化，并且还能够使用您自己的自定义初始化器，请在扩展中编写自定义初始化器，而不是作为值类型原始实现的一部分。有关更多信息，请参见扩展。

以下示例定义了一个自定义 `Rect` 结构来表示几何矩形。该示例需要两个支持结构，称为 `Size` 和 `Point` ，这两个结构都为其所有属性提供默认值 `0.0` ：

```swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
```

您可以通过以下三种方式之一初始化下面的 `Rect` 结构——使用其默认的零初始化 `origin` 和 `size` 属性值，提供特定的原点和大小，或提供特定的中心点和大小。这些初始化选项由三个自定义初始化器表示，它们是 `Rect` 结构定义的一部分：

```swift
struct Rect {
    var origin = Point()
    var size = Size()
    init() {}
    init(origin: Point, size: Size) {
        self.origin = origin
        self.size = size
    }
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

第一个 `Rect` 初始化器 `init()` 在功能上与结构体如果没有自己的自定义初始化器时将会接收到的默认初始化器相同。这个初始化器的主体是空的，用一对空的花括号 `{}` 表示。调用这个初始化器会返回一个 `Rect` 实例，其 `origin` 和 `size` 属性都使用来自其属性定义的默认值 `Point(x: 0.0, y: 0.0)` 和 `Size(width: 0.0, height: 0.0)` 进行初始化：

```swift
let basicRect = Rect()
// basicRect's origin is (0.0, 0.0) and its size is (0.0, 0.0)
```

第二个 `Rect` 初始化器 `init(origin:size:)` 在功能上与结构体如果没有自己的自定义初始化器时将会接收到的成员初始化器相同。这个初始化器只是将 `origin` 和 `size` 参数值分配给适当的存储属性：

```swift
let originRect = Rect(origin: Point(x: 2.0, y: 2.0),
    size: Size(width: 5.0, height: 5.0))
// originRect's origin is (2.0, 2.0) and its size is (5.0, 5.0)
```

第三个 `Rect` 初始化器 `init(center:size:)` 稍微复杂一些。它首先根据 `center` 点和 `size` 值计算一个合适的原点。然后它调用（或委托） `init(origin:size:)` 初始化器，将新的原点和大小值存储在适当的属性中：

```swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
    size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

`init(center:size:)` 初始化器本身可以将 `origin` 和 `size` 的新值分配给适当的属性。然而，对于 `init(center:size:)` 初始化器来说，利用一个已经提供确切功能的现有初始化器更方便（并且意图更清晰）。

> 注意
>
> 有关以替代方式编写此示例而不自己定义 `init()` 和 `init(origin:size:)` 初始化器的更多信息，请参见扩展。

## [类继承和初始化](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Class-Inheritance-and-Initialization)

类的所有存储属性——包括类从其超类继承的任何属性——在初始化期间必须分配一个初始值。

Swift 为类类型定义了两种初始器，以帮助确保所有存储属性都获得初始值。这些被称为指定初始器和便利初始器。

### [指定初始化器和便利初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Designated-Initializers-and-Convenience-Initializers)

指定初始化器是类的主要初始化器。指定初始化器完全初始化该类引入的所有属性，并调用适当的超类初始化器以继续超类链上的初始化过程。

类通常只有很少的指定初始化器，类只有一个指定初始化器是相当常见的。指定初始化器是初始化发生的“漏斗”点，初始化过程通过这些点继续向上超类链。

每个类必须至少有一个指定的初始化器。在某些情况下，满足该要求的方法是从超类继承一个或多个指定初始化器，如下文所述的自动初始化器继承。

便利初始化器是类的辅助初始化器。您可以定义一个便利初始化器，以调用同一类中的指定初始化器，并将指定初始化器的一些参数设置为默认值。您还可以定义一个便利初始化器，以便为特定用例或输入值类型创建该类的实例。

如果您的类不需要便利初始化器，则不必提供它们。每当快捷方式可以节省时间或使类的初始化意图更清晰时，请创建便利初始化器。

### [指定和便利初始化器的语法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Syntax-for-Designated-and-Convenience-Initializers)

类的指定初始化器与值类型的简单初始化器的写法相同：

```swift
init(<#parameters#>) {
   <#statements#>
}
```

便利初始化器的写法与此相同，但在 `init` 关键字之前放置 `convenience` 修饰符，并用空格分隔：

```swift
convenience init(<#parameters#>) {
   <#statements#>
}
```

### [类类型的初始化委托](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Initializer-Delegation-for-Class-Types)

为了简化指定初始化器和便利初始化器之间的关系，Swift 对初始化器之间的代理调用应用以下三个规则：

**规则 1**

指定初始化器必须调用其直接超类的指定初始化器。

**规则 2**

便利初始化器必须调用同一类中的另一个初始化器。

**规则 3**

便利初始化器最终必须调用指定初始化器。

记住这一点的简单方法是：

- 指定的初始值设定项必须始终向上委托。
- 便利初始化器必须始终进行委托。

这些规则在下图中说明：

![initializerDelegation01](https://docs.swift.org/swift-book/images/org.swift.tspl/initializerDelegation01@2x.png){.light-only}
![initializerDelegation01~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/initializerDelegation01~dark@2x.png){.dark-only}

这里，超类有一个指定初始化器和两个便利初始化器。一个便利初始化器调用另一个便利初始化器，而后者又调用单一的指定初始化器。这满足上述的规则 2 和 3。超类本身没有进一步的超类，因此规则 1 不适用。

这个图中的子类有两个指定初始化器和一个便利初始化器。便利初始化器必须调用两个指定初始化器中的一个，因为它只能从同一类调用另一个初始化器。这满足上述的规则 2 和 3。两个指定初始化器必须调用超类中的单一指定初始化器，以满足上述的规则 1。

> 注意
>
> 这些规则不会影响您的类的用户如何创建每个类的实例。上面图表中的任何初始化器都可以用来创建其所属类的完全初始化实例。这些规则仅影响您编写类的初始化器实现的方式。

下图显示了四个类的更复杂的类层次结构。它说明了该层次结构中的指定初始化器如何作为类初始化的“漏斗”点，从而简化了链中类之间的相互关系：

![initializerDelegation02](https://docs.swift.org/swift-book/images/org.swift.tspl/initializerDelegation02@2x.png){.light-only}
![initializerDelegation02~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/initializerDelegation02~dark@2x.png){.dark-only}

### [两阶段初始化](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Two-Phase-Initialization)

在 Swift 中，类初始化是一个两阶段的过程。在第一阶段，每个存储属性由引入它的类分配一个初始值。一旦每个存储属性的初始状态确定，第二阶段开始，每个类都有机会进一步自定义其存储属性，然后新实例被认为准备好使用。

使用两阶段初始化过程使初始化安全，同时仍然为类层次结构中的每个类提供完全的灵活性。两阶段初始化防止在属性值初始化之前访问它们，并防止属性值被另一个初始化器意外地设置为不同的值。

> 注意
>
> Swift 的两阶段初始化过程类似于 Objective-C 中的初始化。主要区别在于，在第一阶段，Objective-C 将零或空值（如 `0` 或 `nil` ）分配给每个属性。Swift 的初始化流程更灵活，因为它允许您设置自定义初始值，并且可以处理 `0` 或 `nil` 不是有效默认值的类型。

Swift 的编译器执行四项有用的安全检查，以确保两阶段初始化无错误地完成：

**安全检查 1**

指定初始化程序必须确保其类引入的所有属性在委托给超类初始化程序之前都已初始化。

如上所述，只有在所有存储属性的初始状态已知时，对象的内存才被认为是完全初始化的。为了满足此规则，指定初始化程序必须确保在向上层委托之前，所有自己的属性都已初始化。

**安全检查 2**

指定的初始化器必须在给继承的属性赋值之前委托给一个超类初始化器。如果不这样做，指定的初始化器赋予的新值将被超类的初始化过程覆盖。

**安全检查 3**

便利初始化器必须在给任何属性（包括同一类定义的属性）赋值之前委托给另一个初始化器。如果不这样做，便利初始化器赋予的新值将被其自身类的指定初始化器覆盖。

**安全检查 4**

初始化器在第一阶段初始化完成之前，不能调用任何实例方法，不能读取任何实例属性的值，也不能将 `self` 视为一个值。

类实例在第一阶段结束之前不是完全有效的。只有在第一阶段结束时，类实例被认为是有效的，才可以访问属性和调用方法。

以下是基于上面四个安全检查，双阶段初始化如何进行的：

**阶段 1**

- 在一个类上调用指定的或便利的初始化器。
- 为该类的新实例分配内存。内存尚未初始化。
- 该类的指定初始化器确认该类引入的所有存储属性都有值。这些存储属性的内存现在已初始化。
- 指定初始化器将任务交给超类初始化器，以对其自己的存储属性执行相同的任务。
- 这将沿着类继承链继续，直到到达链的顶部。
- 一旦到达链的顶部，并且链中的最终类确保其所有存储属性都有值，则实例的内存被认为是完全初始化的，阶段 1 完成。

**阶段 2**

- 从链的顶部向下工作，链中的每个指定初始化器都有进一步自定义实例的选项。初始化器现在可以访问 `self` 并且可以修改其属性、调用其实例方法等等。
- 最后，链中的任何便利初始化器都有选择自定义实例并与 `self` 一起工作的选项。

以下是对一个假设的子类和超类的初始化调用的第 1 阶段的样子：

![twoPhaseInitialization01](https://docs.swift.org/swift-book/images/org.swift.tspl/twoPhaseInitialization01@2x.png){.light-only}
![twoPhaseInitialization01~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/twoPhaseInitialization01~dark@2x.png){.dark-only}

在这个例子中，初始化从对子类的便利初始化器的调用开始。这个便利初始化器还不能修改任何属性。它会委托给同一类的指定初始化器。

指定初始化器确保子类的所有属性都有值，符合安全检查 1。然后它调用其超类的指定初始化器以继续向上链的初始化。

超类的指定初始化器确保超类的所有属性都有值。没有更多的超类需要初始化，因此不需要进一步的委托。

一旦超类的所有属性都有了初始值，它的内存就被认为是完全初始化的，阶段 1 完成。

阶段 2 对于相同的初始化调用如下：

![twoPhaseInitialization02](https://docs.swift.org/swift-book/images/org.swift.tspl/twoPhaseInitialization02@2x.png){.light-only}
![twoPhaseInitialization02~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/twoPhaseInitialization02~dark@2x.png){.dark-only}

超类的指定初始化器现在有机会进一步自定义实例（尽管这不是必要的）。

一旦超类的指定初始化器完成，子类的指定初始化器可以执行额外的自定义（尽管再次强调，它并不是必须的）。

最后，一旦子类的指定初始化器完成，最初调用的便利初始化器可以执行额外的自定义。

### [初始化器继承和重写](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Initializer-Inheritance-and-Overriding)

与 Objective-C 中的子类不同，Swift 子类默认不继承其超类的初始化器。Swift 的方法防止了一个情况：超类的简单初始化器被一个更专业的子类继承，并用于创建一个没有完全或正确初始化的子类的新实例。

> 注意
>
> 在某些情况下，超类初始化器会被继承，但仅在安全和适当的情况下才会这样做。有关更多信息，请参见下面的自动初始化器继承。

如果您希望自定义子类呈现一个或多个与其超类相同的初始化器，您可以在子类中提供这些初始化器的自定义实现。

当您编写一个与超类指定初始化器匹配的子类初始化器时，您实际上是在提供对该指定初始化器的重写。因此，您必须在子类的初始化器定义之前编写 `override` 修饰符。即使您重写的是自动提供的默认初始化器，正如在《默认初始化器》中所描述的，这也是正确的。

与重写的属性、方法或下标类似， `override` 修饰符的存在促使 Swift 检查超类是否具有要被重写的匹配指定初始化器，并验证重写初始化器的参数是否如预期所指定。

> 注意
>
> 当重写超类指定初始化器时，您始终写入 `override` 修饰符，即使您的子类对初始化器的实现是一个便利初始化器。

相反，如果您编写一个与超类便利初始化器匹配的子类初始化器，则根据上面在类类型的初始化器委派中描述的规则，该超类便利初始化器将永远不能被子类直接调用。因此，从严格意义上讲，您的子类并没有提供超类初始化器的重写。因此，当提供与超类便利初始化器匹配的实现时，不需要编写 `override` 修饰符。

下面的示例定义了一个名为 `Vehicle` 的基类。该基类声明了一个名为 `numberOfWheels` 的存储属性，默认 `Int` 值为 `0` 。 `numberOfWheels` 属性被一个名为 `description` 的计算属性使用，以创建车辆特征的 `String` 描述：

```swift
class Vehicle {
    var numberOfWheels = 0
    var description: String {
        return "\(numberOfWheels) wheel(s)"
    }
}
```

`Vehicle` 类为其唯一的存储属性提供了默认值，并且自身不提供任何自定义初始化器。因此，它会自动接收一个默认初始化器，如《默认初始化器》中所述。默认初始化器（在可用时）始终是类的指定初始化器，可以用于创建一个新的 `Vehicle` 实例，具有 `numberOfWheels` 为 `0` :

```swift
let vehicle = Vehicle()
print("Vehicle: \(vehicle.description)")
// Vehicle: 0 wheel(s)
```

下一个示例定义了一个名为 `Bicycle` 的 `Vehicle` 子类：

```swift
class Bicycle: Vehicle {
    override init() {
        super.init()
        numberOfWheels = 2
    }
}
```

The `Bicycle` 子类定义了一个自定义指定初始化器， `init()` 。这个指定初始化器与 `Bicycle` 的超类中的一个指定初始化器相匹配，因此 `Bicycle` 版本的这个初始化器被标记为 `override` 修饰符。

对于 `Bicycle` 的 `init()` 初始化器首先调用 `super.init()` ，它调用 `Bicycle` 类的超类 `Vehicle` 的默认初始化器。这确保了 `Vehicle` 在 `numberOfWheels` 有机会修改属性之前初始化了继承的 `numberOfWheels` 属性。调用 `super.init()` 后， `numberOfWheels` 的原始值被替换为新值 `2` 。

如果您创建了一个 `Bicycle` 的实例，您可以调用它的继承 `description` 计算属性来查看它的 `numberOfWheels` 属性是如何更新的：

```swift
let bicycle = Bicycle()
print("Bicycle: \(bicycle.description)")
// Bicycle: 2 wheel(s)
```

如果子类初始化器在初始化过程的第 2 阶段没有进行自定义，并且超类有一个同步的零参数指定初始化器，则在为所有子类的存储属性赋值后，可以省略对 `super.init()` 的调用。如果超类的初始化器是异步的，则需要显式地写出 `await super.init()` 。

这个例子定义了另一个 `Vehicle` 的子类，叫做 `Hoverboard` 。在它的初始化器中， `Hoverboard` 类仅设置其 `color` 属性。这个初始化器依赖于对其父类初始化器的隐式调用来完成这个过程，而不是显式调用 `super.init()` 。

```swift
class Hoverboard: Vehicle {
    var color: String
    init(color: String) {
        self.color = color
        // super.init() implicitly called here
    }
    override var description: String {
        return "\(super.description) in a beautiful \(color)"
    }
}
```

`Hoverboard` 的一个实例使用 `Vehicle` 初始化器提供的默认轮数。

```swift
let hoverboard = Hoverboard(color: "silver")
print("Hoverboard: \(hoverboard.description)")
// Hoverboard: 0 wheel(s) in a beautiful silver
```

> 注意
>
> 子类可以在初始化期间修改继承的变量属性，但不能修改继承的常量属性。

### [自动初始化器继承](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Automatic-Initializer-Inheritance)

如上所述，子类默认不继承其超类的初始化器。然而，如果满足某些条件，超类的初始化器会被自动继承。实际上，这意味着在许多常见场景中，您无需编写初始化器重写，并且在安全的情况下，可以以最小的努力继承超类的初始化器。

假设您为子类中引入的任何新属性提供默认值，则适用以下两个规则：

**规则 1**

如果您的子类没有定义任何指定初始化程序，它会自动继承其超类的所有指定初始化程序。

**规则 2**

如果您的子类提供了其超类所有指定初始化程序的实现——无论是根据规则 1 继承它们，还是作为其定义的一部分提供自定义实现——那么它会自动继承超类的所有便利初始化程序。

即使您的子类添加了更多便利初始化程序，这些规则仍然适用。

> 注意
>
> 子类可以将超类指定的初始化器作为子类便利初始化器来实现，以满足规则 2。

### [指定和便利初始化器的实际应用](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Designated-and-Convenience-Initializers-in-Action)

以下示例展示了指定初始化器、便利初始化器和自动初始化器继承的实际应用。该示例定义了三个类的层次结构，分别称为 `Food` 、 `RecipeIngredient` 和 `ShoppingListItem` ，并演示了它们的初始化器如何相互作用。

层次结构中的基类称为 `Food` ，这是一个简单的类，用于封装食品名称。 `Food` 类引入了一个名为 `name` 的单一 `String` 属性，并提供了两个初始化器用于创建 `Food` 实例：

```swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }
    convenience init() {
        self.init(name: "[Unnamed]")
    }
}
```

下图显示了 `Food` 类的初始化链：

![initializersExample01](https://docs.swift.org/swift-book/images/org.swift.tspl/initializersExample01@2x.png){.light-only}
![initializersExample01~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/initializersExample01~dark@2x.png){.dark-only}

类没有默认的成员初始化器，因此 `Food` 类提供了一个指定初始化器，它接受一个名为 `name` 的单个参数。这个初始化器可以用来创建一个具有特定名称的新 `Food` 实例：

```swift
let namedMeat = Food(name: "Bacon")
// namedMeat's name is "Bacon"
```

`init(name: String)` 类的初始化器被提供为一个指定初始化器，因为它确保新 `Food` 实例的所有存储属性都已完全初始化。 `Food` 类没有超类，因此 `init(name: String)` 初始化器不需要调用 `super.init()` 来完成其初始化。

`Food` 类还提供了一个方便的初始化器 `init()` ，不带参数。 `init()` 初始化器通过委托给 `Food` 类的 `init(name: String)` 提供了一个新的食物的默认占位符名称， `name` 值为 `[Unnamed]` ：

```swift
let mysteryMeat = Food()
// mysteryMeat's name is "[Unnamed]"
```

层级中的第二个类是一个名为 `RecipeIngredient` 的 `Food` 子类。 `RecipeIngredient` 类表示烹饪食谱中的一种成分。它引入了一个名为 `quantity` 的 `Int` 属性（除了从 `Food` 继承的 `name` 属性）并定义了两个初始化器来创建 `RecipeIngredient` 实例：

```swift
class RecipeIngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
        self.quantity = quantity
        super.init(name: name)
    }
    override convenience init(name: String) {
        self.init(name: name, quantity: 1)
    }
}
```

下图显示了 `RecipeIngredient` 类的初始化链：

![initializersExample02](https://docs.swift.org/swift-book/images/org.swift.tspl/initializersExample02@2x.png){.light-only}
![initializersExample02~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/initializersExample02~dark@2x.png){.dark-only}

`RecipeIngredient` 类有一个单一的指定初始化器 `init(name: String, quantity: Int)` ，可以用来填充新 `RecipeIngredient` 实例的所有属性。该初始化器首先将传入的 `quantity` 参数分配给 `quantity` 属性，这是 `RecipeIngredient` 引入的唯一新属性。完成后，初始化器会委托到 `Food` 类的 `init(name: String)` 初始化器。这一过程满足了上述两阶段初始化的安全检查 1。

`RecipeIngredient` 还定义了一个便利初始化器 `init(name: String)` ，它用于仅通过名称创建 `RecipeIngredient` 实例。这个便利初始化器假设任何没有显式数量的 `RecipeIngredient` 实例的数量为 `1` 。这个便利初始化器的定义使得 `RecipeIngredient` 实例的创建更快速、更方便，并避免在创建多个单数量 `RecipeIngredient` 实例时重复代码。这个便利初始化器简单地委托给类的指定初始化器，传入一个 `quantity` 值为 `1` 。

由 `RecipeIngredient` 提供的 `init(name: String)` 便利初始化器与 `Food` 的 `init(name: String)` 指定初始化器具有相同的参数。因为这个便利初始化器覆盖了其超类的一个指定初始化器，所以必须用 `override` 修饰符标记（如在初始化器继承和覆盖中所述）。

尽管 `RecipeIngredient` 提供了 `init(name: String)` 初始化器作为便利初始化器，但 `RecipeIngredient` 仍然提供了其超类所有指定初始化器的实现。因此， `RecipeIngredient` 也自动继承了其超类的所有便利初始化器。

在这个例子中， `RecipeIngredient` 的超类是 `Food` ，它有一个名为 `init()` 的便利初始化器。因此， `RecipeIngredient` 继承了这个初始化器。 `init()` 的继承版本的功能与 `Food` 版本完全相同，除了它委托给 `init(name: String)` 的 `RecipeIngredient` 版本，而不是 `Food` 版本。

这三个初始化器都可以用来创建新的 `RecipeIngredient` 实例：

```swift
let oneMysteryItem = RecipeIngredient()
let oneBacon = RecipeIngredient(name: "Bacon")
let sixEggs = RecipeIngredient(name: "Eggs", quantity: 6)
```

层级中的第三个也是最后一个类是名为 `ShoppingListItem` 的 `RecipeIngredient` 的子类。 `ShoppingListItem` 类对购物清单中出现的配料进行了建模。

购物清单中的每个项目开始时都是“未购买”的。为了表示这一事实， `ShoppingListItem` 引入了一个名为 `purchased` 的布尔属性，默认值为 `false` 。 `ShoppingListItem` 还添加了一个计算的 `description` 属性，它提供了 `ShoppingListItem` 实例的文本描述：

```swift
class ShoppingListItem: RecipeIngredient {
    var purchased = false
    var description: String {
        var output = "\(quantity) x \(name)"
        output += purchased ? " ✔" : " ✘"
        return output
    }
}
```

> 注意
>
> `ShoppingListItem` 没有定义一个初始化器来为 `purchased` 提供初始值，因为购物清单中的项目（如这里所建模的）总是从未购买开始。

因为它为所有引入的属性提供了默认值，并且自身不定义任何初始化器， `ShoppingListItem` 自动继承其超类的所有指定初始化器和便利初始化器。

下图显示了所有三个类的整体初始化器链：

![initializersExample03](https://docs.swift.org/swift-book/images/org.swift.tspl/initializersExample03@2x.png){.light-only}
![initializersExample03~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/initializersExample03~dark@2x.png){.dark-only}

您可以使用所有三个继承的初始化器来创建一个新的 `ShoppingListItem` 实例：

```swift
var breakfastList = [
    ShoppingListItem(),
    ShoppingListItem(name: "Bacon"),
    ShoppingListItem(name: "Eggs", quantity: 6),
]
breakfastList[0].name = "Orange juice"
breakfastList[0].purchased = true
for item in breakfastList {
    print(item.description)
}
// 1 x Orange juice ✔
// 1 x Bacon ✘
// 6 x Eggs ✘
```

在这里，一个名为 `breakfastList` 的新数组是从一个包含三个新 `ShoppingListItem` 实例的数组字面量创建的。数组的类型被推断为 `[ShoppingListItem]` 。数组创建后，数组开头的 `ShoppingListItem` 的名称从 `"[Unnamed]"` 更改为 `"Orange juice"` ，并标记为已购买。打印数组中每个项目的描述显示它们的默认状态已按预期设置。

## [可失败的初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Failable-Initializers)

有时定义一个可能初始化失败的类、结构或枚举是有用的。这个失败可能是由无效的初始化参数值、缺少必需的外部资源或其他阻止初始化成功的条件触发的。

为了应对可能失败的初始化条件，可以将一个或多个可失败的初始化器作为类、结构或枚举定义的一部分进行定义。通过在 `init` 关键字后面放置一个问号来编写可失败的初始化器 ( `init?` )。

> 注意
>
> 您不能定义具有相同参数类型和名称的可失败和不可失败的初始化器。

一个可失败的初始化器创建一个它初始化的类型的可选值。您在可失败的初始化器中写入 `return nil` 以指示可以触发初始化失败的点。

> 注意
>
> 严格来说，初始化器并不返回一个值。相反，它们的作用是确保在初始化结束时 `self` 被完全且正确地初始化。虽然您写 `return nil` 来触发初始化失败，但您并不使用 `return` 关键字来表示初始化成功。

例如，失败的初始化器用于数字类型转换。为了确保数字类型之间的转换精确保持值，请使用 `init(exactly:)` 初始化器。如果类型转换无法保持值，则初始化器会失败。

```swift
let wholeNumber: Double = 12345.0
let pi = 3.14159


if let valueMaintained = Int(exactly: wholeNumber) {
    print("\(wholeNumber) conversion to Int maintains value of \(valueMaintained)")
}
// Prints "12345.0 conversion to Int maintains value of 12345"


let valueChanged = Int(exactly: pi)
// valueChanged is of type Int?, not Int


if valueChanged == nil {
    print("\(pi) conversion to Int doesn't maintain value")
}
// Prints "3.14159 conversion to Int doesn't maintain value"
```

下面的示例定义了一个名为 `Animal` 的结构，具有一个名为 `species` 的常量 `String` 属性。 `Animal` 结构还定义了一个带有单个参数 `species` 的可失败初始化器。该初始化器检查传递给初始化器的 `species` 值是否为空字符串。如果发现空字符串，则会触发初始化失败。否则， `species` 属性的值将被设置，初始化成功：

```swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty { return nil }
        self.species = species
    }
}
```

您可以使用此可失败的初始化器尝试初始化一个新的 `Animal` 实例，并检查初始化是否成功：

```swift
let someCreature = Animal(species: "Giraffe")
// someCreature is of type Animal?, not Animal


if let giraffe = someCreature {
    print("An animal was initialized with a species of \(giraffe.species)")
}
// Prints "An animal was initialized with a species of Giraffe"
```

如果您将一个空字符串值传递给可失败初始化程序的 `species` 参数，初始化程序将触发初始化失败：

```swift
let anonymousCreature = Animal(species: "")
// anonymousCreature is of type Animal?, not Animal


if anonymousCreature == nil {
    print("The anonymous creature couldn't be initialized")
}
// Prints "The anonymous creature couldn't be initialized"
```

> 注意
>
> 检查空字符串值（例如 `""` 而不是 `"Giraffe"` ）与检查 `nil` 以指示可选 `String` 值的缺失并不相同。在上面的示例中，空字符串（ `""` ）是有效的、非可选的 `String` 。然而，对于动物来说， `species` 属性的值为一个空字符串是不合适的。为了对这个限制建模，如果发现空字符串，则可失败的初始化器会触发初始化失败。

### [可失败的枚举初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Failable-Initializers-for-Enumerations)

您可以使用可失败的初始化器根据一个或多个参数选择一个合适的枚举案例。如果提供的参数与合适的枚举案例不匹配，则初始化器可能会失败。

下面的示例定义了一个名为 `TemperatureUnit` 的枚举，其有三种可能的状态（ `kelvin` 、 `celsius` 和 `fahrenheit` ）。使用可失败的初始化器来寻找一个合适的枚举案例，以表示温度符号的 `Character` 值：

```swift
enum TemperatureUnit {
    case kelvin, celsius, fahrenheit
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .kelvin
        case "C":
            self = .celsius
        case "F":
            self = .fahrenheit
        default:
            return nil
        }
    }
}
```

您可以使用此可失败的初始化器来为三种可能状态选择适当的枚举案例，并在参数与这些状态之一不匹配时导致初始化失败：

```swift
let fahrenheitUnit = TemperatureUnit(symbol: "F")
if fahrenheitUnit != nil {
    print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."


let unknownUnit = TemperatureUnit(symbol: "X")
if unknownUnit == nil {
    print("This isn't a defined temperature unit, so initialization failed.")
}
// Prints "This isn't a defined temperature unit, so initialization failed."
```

### [可失败的原始值枚举初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Failable-Initializers-for-Enumerations-with-Raw-Values)

带有原始值的枚举会自动接收一个可失败的初始化器， `init?(rawValue:)` ，它接受一个名为 `rawValue` 的参数，该参数为适当的原始值类型，并在找到匹配的枚举案例时进行选择，如果没有匹配值，则触发初始化失败。

您可以重写上面的 `TemperatureUnit` 示例，使用类型为 `Character` 的原始值，并利用 `init?(rawValue:)` 初始化器：

```swift
enum TemperatureUnit: Character {
    case kelvin = "K", celsius = "C", fahrenheit = "F"
}


let fahrenheitUnit = TemperatureUnit(rawValue: "F")
if fahrenheitUnit != nil {
    print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."


let unknownUnit = TemperatureUnit(rawValue: "X")
if unknownUnit == nil {
    print("This isn't a defined temperature unit, so initialization failed.")
}
// Prints "This isn't a defined temperature unit, so initialization failed."
```

### [初始化失败的传播](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Propagation-of-Initialization-Failure)

类、结构或枚举的可失败初始化器可以委托给另一个来自同一类、结构或枚举的可失败初始化器。类似地，子类的可失败初始化器可以委托给父类的可失败初始化器。

在任何一种情况下，如果您委托给另一个导致初始化失败的初始化器，整个初始化过程会立即失败，并且不会执行进一步的初始化代码。

> 注意
>
> 可失败的初始化器也可以委托给一个非可失败的初始化器。如果您需要向一个并不会失败的现有初始化过程添加一个潜在的失败状态，请使用这种方法。

下面的示例定义了一个名为 `CartItem` 的 `Product` 子类。 `CartItem` 类模拟了在线购物车中的一个项目。 `CartItem` 引入了一个名为 `quantity` 的存储常量属性，并确保该属性始终具有至少 `1` 的值：

```swift
class Product {
    let name: String
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}


class CartItem: Product {
    let quantity: Int
    init?(name: String, quantity: Int) {
        if quantity < 1 { return nil }
        self.quantity = quantity
        super.init(name: name)
    }
}
```

`CartItem` 的可失败初始化器首先验证它是否接收到 `quantity` 值为 `1` 或更高。如果 `quantity` 无效，则整个初始化过程立即失败，且不执行任何进一步的初始化代码。同样， `Product` 的可失败初始化器检查 `name` 值，如果 `name` 是空字符串，则初始化过程立即失败。

如果您使用非空名称和数量为 `1` 或更多的 `CartItem` 实例，则初始化成功：

```swift
if let twoSocks = CartItem(name: "sock", quantity: 2) {
    print("Item: \(twoSocks.name), quantity: \(twoSocks.quantity)")
}
// Prints "Item: sock, quantity: 2"
```

如果您尝试创建一个 `CartItem` 实例，其 `quantity` 值为 `0` ，则 `CartItem` 初始化程序会导致初始化失败：

```swift
if let zeroShirts = CartItem(name: "shirt", quantity: 0) {
    print("Item: \(zeroShirts.name), quantity: \(zeroShirts.quantity)")
} else {
    print("Unable to initialize zero shirts")
}
// Prints "Unable to initialize zero shirts"
```

同样，如果您尝试使用空的 `name` 值创建 `CartItem` 实例，超类 `Product` 初始化器会导致初始化失败：

```swift
if let oneUnnamed = CartItem(name: "", quantity: 1) {
    print("Item: \(oneUnnamed.name), quantity: \(oneUnnamed.quantity)")
} else {
    print("Unable to initialize one unnamed product")
}
// Prints "Unable to initialize one unnamed product"
```

### [重写可失败的初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Overriding-a-Failable-Initializer)

您可以在子类中重写超类的可失败初始化器，就像重写任何其他初始化器一样。或者，您可以用子类的非失败初始化器重写超类的可失败初始化器。这使您能够定义一个初始化不能失败的子类，即使超类的初始化可以失败。

注意，如果您用非失败子类初始化器重写可失败的超类初始化器，委托调用超类初始化器的唯一方法是强制解包可失败超类初始化器的结果。

> 注意
>
> 您可以用一个非失败初始化器覆盖一个可失败初始化器，但不能反过来。

下面的例子定义了一个名为 `Document` 的类。这个类模拟了一个文档，可以用 `name` 属性初始化，该属性可以是一个非空字符串值或 `nil` ，但不能是空字符串：

```swift
class Document {
    var name: String?
    // this initializer creates a document with a nil name value
    init() {}
    // this initializer creates a document with a nonempty name value
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}
```

下一个示例定义了一个名为 `AutomaticallyNamedDocument` 的 `Document` 子类。 `AutomaticallyNamedDocument` 子类重写了 `Document` 引入的两个指定初始化器。这些重写确保如果实例在没有名称的情况下初始化，或者如果将空字符串传递给 `init(name:)` 初始化器，则 `AutomaticallyNamedDocument` 实例具有初始 `name` 值为 `"[Untitled]"` 。

```swift
class AutomaticallyNamedDocument: Document {
    override init() {
        super.init()
        self.name = "[Untitled]"
    }
    override init(name: String) {
        super.init()
        if name.isEmpty {
            self.name = "[Untitled]"
        } else {
            self.name = name
        }
    }
}
```

`AutomaticallyNamedDocument` 以一个不可失败的 `init(name:)` 初始化器覆盖了它的超类的可失败 `init?(name:)` 初始化器。因为 `AutomaticallyNamedDocument` 以不同于其超类的方式处理空字符串情况，它的初始化器不需要失败，因此提供了一个不可失败的初始化器版本。

您可以在初始化器中使用强制解包，以调用超类的可失败初始化器，作为子类的非失败初始化器实现的一部分。例如，下面的 `UntitledDocument` 子类始终命名为 `"[Untitled]"` ，并在初始化期间使用其超类的可失败 `init(name:)` 初始化器。

```swift
class UntitledDocument: Document {
    override init() {
        super.init(name: "[Untitled]")!
    }
}
```

在这种情况下，如果超类的 `init(name:)` 初始化器被调用时传入一个空字符串作为名称，强制解包操作将导致运行时错误。然而，由于它是用字符串常量调用的，可以看出初始化器不会失败，因此在这种情况下不会发生运行时错误。

### [init! 可失败的初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#The-init-Failable-Initializer)

您通常定义一个可失败的初始化器，通过在 `init` 关键字后放置问号 ( `init?` ) 来创建适当类型的可选实例。或者，您可以定义一个可失败的初始化器，通过在 `init` 关键字后放置感叹号 ( `init!` ) 来创建适当类型的隐式解包可选实例，而不是问号。

您可以从 `init?` 委托到 `init!` ，反之亦然，您可以用 `init!` 重写 `init?` ，反之亦然。您还可以从 `init` 委托到 `init!` ，尽管这样做会在 `init!` 初始化器导致初始化失败时触发断言。

## [必需的初始化器](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Required-Initializers)

在类初始化器的定义之前写入 `required` 修饰符，以指示该类的每个子类必须实现该初始化器：

```swift
class SomeClass {
    required init() {
        // initializer implementation goes here
    }
}
```

您还必须在每个必需初始化器的子类实现之前写上 `required` 修饰符，以指示该初始化器要求适用于链中的进一步子类。您在重写必需的指定初始化器时不需要写 `override` 修饰符：

```swift
class SomeSubclass: SomeClass {
    required init() {
        // subclass implementation of the required initializer goes here
    }
}
```

> 注意
>
> 如果您可以通过继承的初始器满足要求，则不必提供所需初始器的显式实现。

## [使用闭包或函数设置默认属性值](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization#Setting-a-Default-Property-Value-with-a-Closure-or-Function)

如果存储属性的默认值需要一些自定义或设置，您可以使用闭包或全局函数为该属性提供自定义默认值。每当初始化该属性所属类型的新实例时，闭包或函数将被调用，其返回值将被赋值为属性的默认值。

这些类型的闭包或函数通常会创建一个与属性相同类型的临时值，将该值定制为代表所需的初始状态，然后返回该临时值作为属性的默认值。

以下是闭包如何用于提供默认属性值的框架大纲：

```swift
class SomeClass {
    let someProperty: SomeType = {
        // create a default value for someProperty inside this closure
        // someValue must be of the same type as SomeType
        return someValue
    }()
}
```

注意闭包的结束大括号后面跟着一对空括号。这告诉 Swift 立即执行闭包。如果省略这些括号，则您试图将闭包本身分配给属性，而不是闭包的返回值。

> 注意
>
> 如果您使用闭包来初始化一个属性，请记住，在闭包执行时，实例的其余部分尚未初始化。这意味着您无法在闭包中访问其他属性的值，即使这些属性有默认值。您也不能使用隐式 `self` 属性，或者调用实例的任何方法。

下面的示例定义了一个名为 `Chessboard` 的结构，它模拟了国际象棋的棋盘。国际象棋在一个 8 x 8 的棋盘上进行，棋盘上交替着黑色和白色的方格。

![chessBoard](https://docs.swift.org/swift-book/images/org.swift.tspl/chessBoard@2x.png){.light-only}
![chessBoard~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/chessBoard~dark@2x.png){.dark-only}

为了表示这个棋盘， `Chessboard` 结构有一个名为 `boardColors` 的单一属性，它是一个包含 64 个 `Bool` 值的数组。数组中的 `true` 值表示一个黑色方格，而 `false` 值表示一个白色方格。数组中的第一个项目表示棋盘的左上方格，数组中的最后一个项目表示棋盘的右下方格。

`boardColors` 数组使用闭包初始化以设置其颜色值：

```swift
struct Chessboard {
    let boardColors: [Bool] = {
        var temporaryBoard: [Bool] = []
        var isBlack = false
        for i in 1...8 {
            for j in 1...8 {
                temporaryBoard.append(isBlack)
                isBlack = !isBlack
            }
            isBlack = !isBlack
        }
        return temporaryBoard
    }()
    func squareIsBlackAt(row: Int, column: Int) -> Bool {
        return boardColors[(row * 8) + column]
    }
}
```

每当创建一个新的 `Chessboard` 实例时，闭包会被执行， `boardColors` 的默认值会被计算并返回。上面示例中的闭包计算并设置棋盘上每个方块的适当颜色，存储在一个名为 `temporaryBoard` 的临时数组中，并在设置完成后将这个临时数组作为闭包的返回值返回。返回的数组值存储在 `boardColors` 中，可以使用 `squareIsBlackAt(row:column:)` 工具函数进行查询：

```swift
let board = Chessboard()
print(board.squareIsBlackAt(row: 0, column: 1))
// Prints "true"
print(board.squareIsBlackAt(row: 7, column: 7))
// Prints "false"
```
