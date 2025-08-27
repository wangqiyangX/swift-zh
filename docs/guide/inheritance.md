---
description: 子类用于添加或重写功能。
---

# 继承

> 子类用于添加或重写功能。

一个类可以从另一个类继承方法、属性和其他特征。当一个类继承自另一个类时，继承的类被称为子类，而它所继承的类被称为其超类。继承是区分 Swift 中类与其他类型的基本行为。

Swift 中的类可以调用和访问属于其超类的方法、属性和下标，并可以提供自己的重写版本来细化或修改其行为。Swift 通过检查重写定义是否具有匹配的超类定义来帮助确保您的重写是正确的。

类还可以为继承的属性添加属性观察者，以便在属性值发生变化时收到通知。属性观察者可以添加到任何属性，无论它最初是作为存储属性还是计算属性定义的。

## [定义基类](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Defining-a-Base-Class)

任何不从其他类继承的类称为基类。

> 注意
>
> Swift 类不会从一个通用基类继承。您定义的类如果没有指定超类，会自动成为您构建的基类。

下面的示例定义了一个名为 `Vehicle` 的基类。这个基类定义了一个名为 `currentSpeed` 的存储属性，默认值为 `0.0` （推断属性类型为 `Double` ）。 `currentSpeed` 属性的值被一个只读计算 `String` 属性 `description` 使用，以创建车辆的描述。

`Vehicle` 基类还定义了一个名为 `makeNoise` 的方法。这个方法对于基 `Vehicle` 实例实际上并不执行任何操作，但将由 `Vehicle` 的子类稍后进行自定义：

```swift
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
        return "traveling at \(currentSpeed) miles per hour"
    }
    func makeNoise() {
        // do nothing - an arbitrary vehicle doesn't necessarily make a noise
    }
}
```

您可以使用初始化语法创建一个新的 `Vehicle` 实例，该语法写作类型名称后跟空括号：

```swift
let someVehicle = Vehicle()
```

创建了一个新的 `Vehicle` 实例后，您可以访问其 `description` 属性，以打印车辆当前速度的人类可读描述：

```swift
print("Vehicle: \(someVehicle.description)")
// Vehicle: traveling at 0.0 miles per hour
```

`Vehicle` 类定义了任意车辆的共同特征，但本身并没有太大用处。为了使其更有用，您需要对其进行细化，以描述更具体类型的车辆。

## [子类化](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Subclassing)

子类化是基于现有类创建新类的行为。子类继承现有类的特征，然后您可以对其进行细化。您还可以向子类添加新特征。

要表示子类具有超类，请在超类名称之前写子类名称，并用冒号分隔：

```swift
class SomeSubclass: SomeSuperclass {
    // subclass definition goes here
}
```

以下示例定义一个名为 `Bicycle` 的子类，具有 `Vehicle` 的父类：

```swift
class Bicycle: Vehicle {
    var hasBasket = false
}
```

新创建的 `Bicycle` 类自动获得 `Vehicle` 的所有特征，例如它的 `currentSpeed` 和 `description` 属性以及它的 `makeNoise()` 方法。

除了继承的特征外， `Bicycle` 类定义了一个新的存储属性 `hasBasket` ，其默认值为 `false` （推断该属性的类型为 `Bool` ）。

默认情况下，您创建的任何新 `Bicycle` 实例将没有篮子。您可以在该实例创建后将 `hasBasket` 属性设置为 `true` 以特定于一个 `Bicycle` 实例：

```swift
let bicycle = Bicycle()
bicycle.hasBasket = true
```

您还可以修改 `Bicycle` 实例的继承 `currentSpeed` 属性，并查询该实例的继承 `description` 属性：

```swift
bicycle.currentSpeed = 15.0
print("Bicycle: \(bicycle.description)")
// Bicycle: traveling at 15.0 miles per hour
```

子类本身可以被子类化。下一个示例创建了一个适用于两人座自行车的 `Bicycle` 子类，称为“并行”：

```swift
class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}
```

`Tandem` 继承了 `Bicycle` 的所有属性和方法，而 `Bicycle` 又继承了 `Vehicle` 的所有属性和方法。 `Tandem` 子类还添加了一个名为 `currentNumberOfPassengers` 的新存储属性，默认值为 `0` 。

如果您创建 `Tandem` 的实例，您可以使用它的新属性和继承属性，并查询它从 `Vehicle` 继承的只读 `description` 属性：

```swift
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
print("Tandem: \(tandem.description)")
// Tandem: traveling at 22.0 miles per hour
```

## [Overriding](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Overriding) 重试 错误原因

子类可以提供自己的自定义实现，用于实例方法、类型方法、实例属性、类型属性或下标，否则将从超类继承。这被称为重写。

要覆盖本来会被继承的特性，您需要在您的覆盖定义前加上 `override` 关键字。这样可以明确表示您打算提供一个覆盖，并且并不是错误地提供了一个匹配的定义。意外地覆盖可能会导致意外行为，任何没有 `override` 关键字的覆盖在编译代码时会被诊断为错误。

`override` 关键字还会提示 Swift 编译器检查您重写的类的父类（或其父类之一）是否具有与您为重写提供的声明匹配的声明。此检查确保您的重写定义是正确的。

### [访问超类的方法、属性和下标](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Accessing-Superclass-Methods-Properties-and-Subscripts)

当您为一个子类提供方法、属性或下标重写时，有时使用现有的超类实现作为您的重写的一部分是很有用的。例如，您可以改进现有实现的行为，或将修改后的值存储在现有继承变量中。

在适当的情况下，您可以通过使用 `super` 前缀访问方法、属性或下标的超类版本：

- 名为 `someMethod()` 的重写方法可以通过在重写方法实现中调用 `super.someMethod()` 来调用 `someMethod()` 的超类版本。
- 一个名为 `someProperty` 的重写属性可以在重写的 getter 或 setter 实现中作为 `super.someProperty` 访问 `someProperty` 的超类版本。
- 重写的下标 `someIndex` 可以从重写下标实现中访问相同下标的超类版本 `super[someIndex]` 。

### [重写方法](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Overriding-Methods)

您可以重写一个继承的实例或类型方法，以在子类中提供定制或替代实现。

以下示例定义了一个新的 `Vehicle` 子类，称为 `Train` ，它重写了 `Train` 从 `Vehicle` 继承的 `makeNoise()` 方法：

```swift
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}
```

如果您创建一个新的 `Train` 实例并调用它的 `makeNoise()` 方法，您可以看到 `Train` 子类版本的方法被调用：

```swift
let train = Train()
train.makeNoise()
// Prints "Choo Choo"
```

### [重写属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Overriding-Properties)

您可以重写继承的实例或类型属性，以提供您自己的自定义 getter 和 setter，或者添加属性观察者，以使重写的属性能够观察基础属性值何时更改。

[**重写属性 Getter 和 Setter**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Overriding-Property-Getters-and-Setters)

您可以提供自定义 getter（如果适用，也可以提供 setter）来重写任何继承的属性，无论继承的属性是在源代码中实现为存储属性还是计算属性。子类并不知道继承属性的存储或计算性质——它只知道继承属性具有特定的名称和类型。您必须始终声明您正在重写的属性的名称和类型，以使编译器能够检查您的重写是否与具有相同名称和类型的超类属性匹配。

您可以通过在子类属性重写中提供 getter 和 setter，将继承的只读属性呈现为读写属性。然而，您无法将继承的读写属性呈现为只读属性。

> 注意
>
> 如果您在属性重写中提供了一个设置器，则必须为该重写提供一个获取器。如果您不想在重写的获取器中修改继承属性的值，可以通过从获取器返回 `super.someProperty` 来简单地传递继承值，其中 `someProperty` 是您正在重写的属性的名称。

以下示例定义了一个名为 `Car` 的新类，该类是 `Vehicle` 的子类。 `Car` 类引入了一个名为 `gear` 的新存储属性，其默认整数值为 `1` 。 `Car` 类还重写了它从 `Vehicle` 继承的 `description` 属性，以提供包含当前齿轮的自定义描述：

```swift
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " in gear \(gear)"
    }
}
```

`description` 属性的重写首先调用 `super.description` ，返回 `Vehicle` 类的 `description` 属性。 `Car` 类的 `description` 版本在此描述的末尾添加了一些额外文本，以提供有关当前齿轮的信息。

如果您创建 `Car` 类的一个实例并设置其 `gear` 和 `currentSpeed` 属性，您可以看到其 `description` 属性返回在 `Car` 类中定义的量身定制的描述：

```swift
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
print("Car: \(car.description)")
// Car: traveling at 25.0 miles per hour in gear 3
```

[**重写属性观察者**](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Overriding-Property-Observers)

您可以使用属性重写为继承的属性添加属性观察者。这使您能够在继承属性的值发生变化时收到通知，无论该属性最初是如何实现的。有关属性观察者的更多信息，请参见属性观察者。

> 注意
>
> 您无法将属性观察者添加到继承的常量存储属性或继承的只读计算属性。无法设置这些属性的值，因此在重写中提供 `willSet` 或 `didSet` 实现是不合适的。

请注意，您不能为同一个属性提供重写的设置器和重写的属性观察器。如果您想观察属性值的变化，并且您已经为该属性提供了自定义设置器，您可以简单地在自定义设置器中观察任何值的变化。

以下示例定义了一个名为 `AutomaticCar` 的新类，它是 `Car` 的子类。 `AutomaticCar` 类表示一辆具有自动变速箱的汽车，该变速箱根据当前速度自动选择适当的档位：

```swift
class AutomaticCar: Car {
    override var currentSpeed: Double {
        didSet {
            gear = Int(currentSpeed / 10.0) + 1
        }
    }
}
```

每当您设置一个 `AutomaticCar` 实例的 `currentSpeed` 属性时，该属性的 `didSet` 观察者会将实例的 `gear` 属性设置为适合新速度的齿轮选择。具体来说，属性观察者选择一个齿轮，该齿轮是新的 `currentSpeed` 值除以 `10` 后向下取整到最接近的整数，再加上 `1` 。速度为 `35.0` 时产生的齿轮为 `4` ：

```swift
let automatic = AutomaticCar()
automatic.currentSpeed = 35.0
print("AutomaticCar: \(automatic.description)")
// AutomaticCar: traveling at 35.0 miles per hour in gear 4
```

## [防止覆盖](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Preventing-Overrides)

您可以通过将方法、属性或下标标记为 final 来防止其被重写。通过在方法、属性或下标的引入关键字（如 `final var` 、 `final func` 、 `final class func` 和 `final subscript` ）之前写入 `final` 修饰符来实现这一点。

在子类中尝试重写 final 方法、属性或下标将被报告为编译时错误。您在扩展中添加到类的方法、属性或下标也可以在扩展的定义中标记为 final。有关更多信息，请参见扩展。

您可以通过在类定义（ `final class` ）中的 `class` 关键字之前写入 `final` 修饰符来将整个类标记为 final。尝试对 final 类进行子类化将被报告为编译时错误。
