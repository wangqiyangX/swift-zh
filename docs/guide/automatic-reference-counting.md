# 自动引用计数

> 对对象及其关系进行生命周期建模。

Swift 使用自动引用计数（ARC）来跟踪和管理您应用的内存使用。在大多数情况下，这意味着内存管理在 Swift 中“自动完成”，您无需自己考虑内存管理。当类实例不再需要时，ARC 会自动释放其占用的内存。

然而，在少数情况下，ARC 需要更多关于代码各部分之间关系的信息，以便为您管理内存。本章描述了这些情况，并展示了如何启用 ARC 来管理您应用的所有内存。在 Swift 中使用 ARC 与在过渡到 ARC 的发布说明中描述的使用 ARC 与 Objective-C 的方法非常相似。

引用计数仅适用于类的实例。结构和枚举是值类型，而不是引用类型，并且不是通过引用存储和传递的。

## [ARC 的工作原理](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#How-ARC-Works)

每次您创建一个类的新实例时，ARC 会分配一块内存来存储有关该实例的信息。这块内存保存有关实例类型的信息，以及与该实例关联的任何存储属性的值。

此外，当一个实例不再需要时，ARC 会释放该实例使用的内存，以便将内存用于其他目的。这确保了类实例在不再需要时不会占用内存空间。

然而，如果 ARC 试图释放一个仍在使用的实例，则将无法访问该实例的属性或调用该实例的方法。实际上，如果您尝试访问该实例，您的应用程序很可能会崩溃。

为了确保实例在仍然需要时不会消失，ARC 跟踪当前引用每个类实例的属性、常量和变量的数量。只要至少存在一个对该实例的活动引用，ARC 就不会释放该实例。

为了实现这一点，每当您将一个类实例分配给一个属性、常量或变量时，该属性、常量或变量会对该实例进行强引用。该引用被称为“强”引用，因为它对该实例保持牢固的控制，并且只要该强引用存在，就不允许其被释放。

## [ARC in Action（ARC 的应用）](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#ARC-in-Action)

这里是一个自动引用计数（Automatic Reference Counting）如何工作的示例。这个示例从一个简单的类 `Person` 开始，该类定义了一个名为 `name` 的存储常量属性：

```swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}
```

`Person` 类有一个初始化器，用于设置实例的 `name` 属性，并打印一条消息以指示初始化正在进行。 `Person` 类还有一个析构器，当类的实例被释放时打印一条消息。

下一个代码片段定义了三个类型为 `Person?` 的变量，这些变量用于在后续代码片段中设置对新 `Person` 实例的多个引用。因为这些变量是可选类型（ `Person?` ，而不是 `Person` ），它们会自动初始化为 `nil` 的值，并且当前不引用 `Person` 实例。

```swift
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

您现在可以创建一个新的 `Person` 实例并将其分配给这三个变量之一：

```swift
reference1 = Person(name: "John Appleseed")
// Prints "John Appleseed is being initialized"
```

请注意，消息 `"John Appleseed is being initialized"` 在您调用 `Person` 类的初始化器时打印。这确认了初始化已经发生。

因为新的 `Person` 实例已被分配给 `reference1` 变量，所以现在从 `reference1` 到新的 `Person` 实例有一个强引用。因为至少有一个强引用，ARC 确保这个 `Person` 保持在内存中并且不会被释放。

如果您将相同的 `Person` 实例分配给两个更多的变量，将建立对该实例的两个更多的强引用：

```swift
reference2 = reference1
reference3 = reference1
```

现在对这个单一的 `Person` 实例有三个强引用。

如果您通过将 `nil` 赋值给两个变量来打破这两个强引用（包括原始引用），则只剩下一个强引用，而 `Person` 实例不会被释放：

```swift
reference1 = nil
reference2 = nil
```

ARC 直到第三个也是最后一个强引用被打破时才会释放 `Person` 实例，这时很明显您不再使用 `Person` 实例：

```swift
reference3 = nil
// Prints "John Appleseed is being deinitialized"
```

## [类实例之间的强引用循环](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Strong-Reference-Cycles-Between-Class-Instances)

在上面的示例中，ARC 能够跟踪您创建的新 `Person` 实例的引用数量，并在该 `Person` 实例不再需要时将其释放。

然而，可能会编写代码，使得一个类的实例永远不会达到零强引用的状态。如果两个类实例相互持有强引用，则会发生这种情况，从而使每个实例保持对方存活。这被称为强引用循环。

通过将类之间的一些关系定义为弱引用或无主引用，而不是强引用，您可以解决强引用循环。这个过程在《解决类实例之间的强引用循环》中进行了描述。然而，在您学习如何解决强引用循环之前，了解这种循环是如何产生的是很有用的。

这是一个意外创建强引用循环的示例。这个示例定义了两个类，分别叫做 `Person` 和 `Apartment` ，它们模拟了一栋公寓及其居民：

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}


class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}
```

每个 `Person` 实例都有一个类型为 `String` 的 `name` 属性和一个可选的 `apartment` 属性，该属性初始为 `nil` 。 `apartment` 属性是可选的，因为一个人可能并不总有公寓。

类似地，每个 `Apartment` 实例都有一个类型为 `String` 的 `unit` 属性，并且有一个可选的 `tenant` 属性，初始值为 `nil` 。租户属性是可选的，因为公寓可能并不总是有租户。

这两个类还定义了一个析构函数，它会打印出该类的一个实例正在被析构的事实。这使您能够查看 `Person` 和 `Apartment` 的实例是否按预期被释放。

下一个代码片段定义了两个可选类型的变量，分别称为 `john` 和 `unit4A` ，它们将在下面设置为特定的 `Apartment` 和 `Person` 实例。这两个变量的初始值都是 `nil` ，因为它们是可选的：

```swift
var john: Person?
var unit4A: Apartment?
```

现在您可以创建一个特定的 `Person` 实例和 `Apartment` 实例，并将这两个新实例分配给 `john` 和 `unit4A` 变量：

```swift
john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")
```

创建并分配这两个实例后，强引用的样子如下。 `john` 变量现在对新的 `Person` 实例有一个强引用，而 `unit4A` 变量对新的 `Apartment` 实例有一个强引用：

![referenceCycle01](https://docs.swift.org/swift-book/images/org.swift.tspl/referenceCycle01@2x.png){.light-only}
![referenceCycle01~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/referenceCycle01~dark@2x.png){.dark-only}

您现在可以将两个实例链接在一起，使得该人拥有一个公寓，而公寓有一个租户。请注意，使用感叹号 ( `!` ) 来解包并访问存储在 `john` 和 `unit4A` 可选变量中的实例，以便可以设置这些实例的属性：

```swift
john!.apartment = unit4A
unit4A!.tenant = john
```

将两个实例链接在一起后，强引用看起来是这样的：

![referenceCycle02](https://docs.swift.org/swift-book/images/org.swift.tspl/referenceCycle02@2x.png){.light-only}
![referenceCycle02~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/referenceCycle02~dark@2x.png){.dark-only}

不幸的是，链接这两个实例会在它们之间创建一个强引用循环。 `Person` 实例现在对 `Apartment` 实例有一个强引用，而 `Apartment` 实例对 `Person` 实例有一个强引用。因此，当您断开 `john` 和 `unit4A` 变量持有的强引用时，引用计数不会降到零，实例不会被 ARC 释放：

```swift
john = nil
unit4A = nil
```

请注意，当您将这两个变量设置为 `nil` 时，两个析构函数都没有被调用。强引用循环阻止了 `Person` 和 `Apartment` 实例被释放，从而导致您的应用程序出现内存泄漏。

在您将 `john` 和 `unit4A` 变量设置为 `nil` 后，强引用的样子如下：

![referenceCycle03](https://docs.swift.org/swift-book/images/org.swift.tspl/referenceCycle03@2x.png){.light-only}
![referenceCycle03~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/referenceCycle03~dark@2x.png){.dark-only}

`Person` 实例与 `Apartment` 实例之间的强引用保持不变，无法被破坏。

## [解决类实例之间的强引用循环](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Resolving-Strong-Reference-Cycles-Between-Class-Instances)

Swift 提供了两种解决强引用循环的方法，当您处理类类型的属性时：弱引用和无主引用。

弱引用和无主引用使得一个实例在引用循环中可以引用另一个实例，而无需对其保持强持有。这样，这些实例可以相互引用，而不会创建强引用循环。

当另一个实例的生命周期较短时——也就是说，当另一个实例可以首先被释放时，使用弱引用。在上面的 `Apartment` 示例中，公寓在其生命周期的某个时刻可以没有租户，因此在这种情况下，弱引用是打破引用循环的适当方式。相反，当另一个实例的生命周期相同或较长时，使用无主引用。

### [弱引用](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Weak-References)

弱引用是指对它所引用的实例没有保持强引用，因此不会阻止 ARC 处理被引用的实例。这种行为防止了引用成为强引用循环的一部分。通过在属性或变量声明前放置 `weak` 关键字来表示一个弱引用。

因为弱引用不对它所引用的实例保持强引用，所以在弱引用仍然引用它时，那个实例有可能被释放。因此，当引用的实例被释放时，ARC 会自动将弱引用设置为 `nil` 。而且，由于弱引用需要允许它们的值在运行时更改为 `nil` ，所以它们总是被声明为可选类型的变量，而不是常量。

您可以检查弱引用中值的存在，就像检查任何其他可选值一样，并且您永远不会得到指向不再存在的无效实例的引用。

> 注意
>
> 属性观察者在 ARC 将弱引用设置为 `nil` 时不会被调用。

下面的示例与上面的 `Person` 和 `Apartment` 示例完全相同，但有一个重要区别。这一次， `Apartment` 类型的 `tenant` 属性被声明为弱引用：

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}


class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    weak var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}
```

来自两个变量（ `john` 和 `unit4A` ）的强引用以及两个实例之间的链接依旧像之前一样被创建：

```swift
var john: Person?
var unit4A: Apartment?


john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")


john!.apartment = unit4A
unit4A!.tenant = john
```

现在，您将两个实例链接在一起后，引用的样子如下：

![weakReference01](https://docs.swift.org/swift-book/images/org.swift.tspl/weakReference01@2x.png){.light-only}
![weakReference01~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/weakReference01~dark@2x.png){.dark-only}

`Person` 实例仍然对 `Apartment` 实例有一个强引用，但是 `Apartment` 实例现在对 `Person` 实例有一个弱引用。这意味着当您通过将 `john` 变量设置为 `nil` 来断开对 `Person` 实例的强引用时，已没有对 `Person` 实例的强引用：

```swift
john = nil
// Prints "John Appleseed is being deinitialized"
```

因为对 `Person` 实例已没有强引用，因此它被释放， `tenant` 属性被设置为 `nil` ：

![weakReference02](https://docs.swift.org/swift-book/images/org.swift.tspl/weakReference02@2x.png){.light-only}
![weakReference02~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/weakReference02~dark@2x.png){.dark-only}

对 `Apartment` 实例的唯一剩余强引用来自 `unit4A` 变量。如果您打破那个强引用，就没有更多对 `Apartment` 实例的强引用了：

```swift
unit4A = nil
// Prints "Apartment 4A is being deinitialized"
```

因为没有更多对 `Apartment` 实例的强引用，它也被释放了：

![weakReference03](https://docs.swift.org/swift-book/images/org.swift.tspl/weakReference03@2x.png){.light-only}
![weakReference03~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/weakReference03~dark@2x.png){.dark-only}

> 注意
>
> 在使用垃圾回收的系统中，弱引用有时用于实现简单的缓存机制，因为没有强引用的对象仅在内存压力触发垃圾回收时被释放。然而，在 ARC 中，值在其最后一个强引用被移除时立即被释放，这使得弱引用不适合用于此目的。

### [无主引用](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Unowned-References)

与弱引用类似，无主引用不会对它所引用的实例保持强引用。然而，与弱引用不同，无主引用在另一个实例具有相同生命周期或更长生命周期时使用。通过在属性或变量声明前放置 `unowned` 关键字来指示无主引用。

与弱引用不同，无主引用预计始终有一个值。因此，将一个值标记为无主并不会使其变为可选，ARC 从不将无主引用的值设置为 `nil` 。

> 重要
>
> 仅在您确定引用始终指向未被释放的实例时，才使用无主引用。

如果在该实例被释放后尝试访问未拥有引用的值，您将会遇到运行时错误。

以下示例定义了两个类， `Customer` 和 `CreditCard` ，它们模拟一个银行客户以及该客户可能拥有的信用卡。这两个类各自将另一个类的实例作为属性存储。这个关系可能会导致强引用循环。

`Customer` 和 `CreditCard` 之间的关系与上面弱引用示例中 `Apartment` 和 `Person` 之间的关系略有不同。在这个数据模型中，客户可能有也可能没有信用卡，但信用卡将始终与客户相关联。一个 `CreditCard` 实例永远不会超出它所引用的 `Customer` 的生命周期。为了表示这一点， `Customer` 类有一个可选的 `card` 属性，但 `CreditCard` 类有一个无主（并且是非可选的） `customer` 属性。

此外，一个新的 `CreditCard` 实例只能通过传递一个 `number` 值和一个 `customer` 实例给自定义 `CreditCard` 初始化函数来创建。这确保了当 `CreditCard` 实例被创建时， `CreditCard` 实例总是与一个 `customer` 实例相关联。

因为信用卡总是会有一个客户，所以您将其 `customer` 属性定义为无主引用，以避免强引用循环：

```swift
class Customer {
    let name: String
    var card: CreditCard?
    init(name: String) {
        self.name = name
    }
    deinit { print("\(name) is being deinitialized") }
}


class CreditCard {
    let number: UInt64
    unowned let customer: Customer
    init(number: UInt64, customer: Customer) {
        self.number = number
        self.customer = customer
    }
    deinit { print("Card #\(number) is being deinitialized") }
}
```

> 注意
>
> `CreditCard` 类的 `number` 属性被定义为 `UInt64` 类型而不是 `Int` ，以确保 `number` 属性的容量足够大，可以在 32 位和 64 位系统上存储 16 位数字的卡号。

这个下一个代码片段定义了一个名为 `john` 的可选 `Customer` 变量，该变量将用于存储对特定客户的引用。由于是可选的，这个变量的初始值为 nil：

```swift
var john: Customer?
```

您现在可以创建一个 `Customer` 实例，并使用它初始化并分配一个新的 `CreditCard` 实例作为该客户的 `card` 属性：

```swift
john = Customer(name: "John Appleseed")
john!.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)
```

现在就来看一下引用，看起来您已经链接了这两个实例：

![unownedReference01](https://docs.swift.org/swift-book/images/org.swift.tspl/unownedReference01@2x.png){.light-only}
![unownedReference01~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/unownedReference01~dark@2x.png){.dark-only}

`Customer` 实例现在对 `CreditCard` 实例有一个强引用，而 `CreditCard` 实例对 `Customer` 实例有一个未拥有的引用。

由于无主的 `customer` 引用，当您打破由 `john` 变量持有的强引用时，对 `Customer` 实例就没有更多的强引用：

![unownedReference02](https://docs.swift.org/swift-book/images/org.swift.tspl/unownedReference02@2x.png){.light-only}
![unownedReference02~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/unownedReference02~dark@2x.png){.dark-only}

因为没有更多对 `Customer` 实例的强引用，它被释放了。在这之后， `CreditCard` 实例也没有更多的强引用，因此它也被释放：

```swift
john = nil
// Prints "John Appleseed is being deinitialized"
// Prints "Card #1234567890123456 is being deinitialized"
```

上面的最终代码片段显示， `Customer` 实例和 `CreditCard` 实例的去初始化器在 `john` 变量被设置为 `nil` 之后都打印它们的“已去初始化”消息。

> 注意
>
> 上面的示例展示了如何使用安全的无主引用。Swift 还提供了不安全的无主引用，用于需要禁用运行时安全检查的情况——例如，出于性能考虑。与所有不安全操作一样，您需要自行负责检查代码的安全性。

您通过写 `unowned(unsafe)` 来指示一个不安全的无主引用。如果您在引用的实例被释放后尝试访问不安全的无主引用，您的程序将尝试访问实例曾经所在的内存位置，这是一种不安全的操作。

### [非拥有的可选引用](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Unowned-Optional-References)

您可以将对类的可选引用标记为无主。在 ARC 拥有权模型中，无主可选引用和弱引用可以在相同的上下文中使用。区别在于，当您使用无主可选引用时，您需要负责确保它始终引用一个有效的对象或被设置为 `nil` 。

这是一个示例，用于跟踪学校某个部门提供的课程：

```swift
class Department {
    var name: String
    var courses: [Course]
    init(name: String) {
        self.name = name
        self.courses = []
    }
}


class Course {
    var name: String
    unowned var department: Department
    unowned var nextCourse: Course?
    init(name: String, in department: Department) {
        self.name = name
        self.department = department
        self.nextCourse = nil
    }
}
```

`Department` 对系所提供的每门课程保持强引用。在 ARC 拥有权模型中，一个系所拥有其课程。 `Course` 有两个未拥有的引用，一个指向系所，另一个指向学生应该选修的下一门课程；一门课程并不拥有这两个对象中的任何一个。每门课程都是某个系所的一部分，因此 `department` 属性不是可选的。然而，由于某些课程没有推荐的后续课程， `nextCourse` 属性是可选的。

以下是使用这些类的示例：

```swift
let department = Department(name: "Horticulture")


let intro = Course(name: "Survey of Plants", in: department)
let intermediate = Course(name: "Growing Common Herbs", in: department)
let advanced = Course(name: "Caring for Tropical Plants", in: department)


intro.nextCourse = intermediate
intermediate.nextCourse = advanced
department.courses = [intro, intermediate, advanced]
```

上面的代码创建了一个部门及其三个课程。入门和中级课程都有一个建议的下一个课程存储在它们的 `nextCourse` 属性中，该属性维护一个未拥有的可选引用，该引用指向学生在完成此课程后应修的课程。

![unownedOptionalReference](https://docs.swift.org/swift-book/images/org.swift.tspl/unownedOptionalReference@2x.png){.light-only}
![unownedOptionalReference~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/unownedOptionalReference~dark@2x.png){.dark-only}

一个未拥有的可选引用不会对它所包装的类实例保持强引用，因此不会阻止 ARC 释放该实例。它的行为与 ARC 下的未拥有引用相同，除了未拥有的可选引用可以是 `nil` 。

像非可选的无主引用一样，您有责任确保 `nextCourse` 始终指向一个未被释放的课程。在这种情况下，例如，当您从 `department.courses` 中删除一个课程时，您还需要删除其他课程可能对它的任何引用。

> 注意
>
> 可选值的底层类型是 `Optional` ，这是 Swift 标准库中的一个枚举。然而，可选值是一个例外，因为值类型不能被标记为 `unowned` 。

包装该类的可选项不使用引用计数，因此您不需要维护对可选项的强引用。

### [未拥有的引用和隐式展开的可选属性](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Unowned-References-and-Implicitly-Unwrapped-Optional-Properties)

上面关于弱引用和无主引用的示例涵盖了需要打破强引用循环的两种更常见场景。

The `Person` and `Apartment` 示例显示了一种情况，其中两个属性均允许为 `nil` ，这可能导致强引用循环。最佳解决方案是使用弱引用。

The `Customer` and `CreditCard` 示例显示了一种情况，其中一个属性被允许为 `nil` ，而另一个属性不能为 `nil` ，这可能导致强引用循环。最佳解决方案是使用无主引用。

然而，还有第三种情况，两个属性都应该始终有值，并且在初始化完成后，任何属性都不应该为 `nil` 。在这种情况下，结合一个类的无主属性和另一个类的隐式解包可选属性是有用的。

这使得在初始化完成后可以直接访问两个属性（不需要可选解包），同时避免引用循环。本节将向您展示如何建立这样的关系。

下面的示例定义了两个类， `Country` 和 `City` ，每个类都将另一个类的实例作为属性存储。在这个数据模型中，每个国家必须始终有一个首都，每个城市必须始终属于一个国家。为了表示这一点， `Country` 类有一个 `capitalCity` 属性，而 `City` 类有一个 `country` 属性：

```swift
class Country {
    let name: String
    var capitalCity: City!
    init(name: String, capitalName: String) {
        self.name = name
        self.capitalCity = City(name: capitalName, country: self)
    }
}


class City {
    let name: String
    unowned let country: Country
    init(name: String, country: Country) {
        self.name = name
        self.country = country
    }
}
```

要设置两个类之间的相互依赖， `City` 的初始化器接受一个 `Country` 实例，并将该实例存储在其 `country` 属性中。

`City` 的初始化器是在 `Country` 的初始化器内部调用的。然而， `Country` 的初始化器在新的 `Country` 实例完全初始化之前无法将 `self` 传递给 `City` 初始化器，如“两阶段初始化”中所述。

为了应对这一要求，您将 `Country` 的 `capitalCity` 属性声明为隐式解包可选属性，通过其类型注释末尾的感叹号 ( `City!` ) 表示。这意味着 `capitalCity` 属性具有默认值 `nil` ，像任何其他可选项一样，但可以在不需要解包其值的情况下访问，如“隐式解包可选项”中所述。

因为 `capitalCity` 有一个默认的 `nil` 值，当 `Country` 实例在其初始化器中设置其 `name` 属性时，一个新的 `Country` 实例被认为是完全初始化的。这意味着 `Country` 初始化器可以在 `name` 属性被设置后，开始引用和传递隐式的 `self` 属性。因此， `Country` 初始化器可以在 `Country` 初始化器设置其自己的 `capitalCity` 属性时，将 `self` 作为 `City` 初始化器的参数之一传递。

所有这些意味着您可以在单个语句中创建 `Country` 和 `City` 实例，而无需创建强引用循环，并且可以直接访问 `capitalCity` 属性，无需使用惊叹号来解包其可选值：

```swift
var country = Country(name: "Canada", capitalName: "Ottawa")
print("\(country.name)'s capital city is called \(country.capitalCity.name)")
// Prints "Canada's capital city is called Ottawa"
```

在上面的示例中，使用隐式解包可选值意味着满足所有两阶段类初始化程序的要求。一旦初始化完成， `capitalCity` 属性可以像非可选值一样使用和访问，同时仍然避免强引用循环。

## [闭包的强引用循环](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Strong-Reference-Cycles-for-Closures)

您上面看到，当两个类实例属性相互持有强引用时，如何创建强引用循环。您还看到如何使用弱引用和未拥有引用来打破这些强引用循环。

如果您将一个闭包赋值给类实例的属性，并且该闭包的主体捕获了该实例，也会发生强引用循环。这种捕获可能是因为闭包的主体访问了实例的一个属性，例如 `self.someProperty` ，或者因为闭包调用了实例上的一个方法，例如 `self.someMethod()` 。在这两种情况下，这些访问会导致闭包“捕获” `self` ，从而创建一个强引用循环。

这个强引用循环发生是因为闭包和类一样是引用类型。当您将一个闭包赋值给一个属性时，您是在将对该闭包的引用赋值。本质上，这与上面的问题是一样的——两个强引用互相保持对方的存活。然而，这次保持对方存活的是一个类实例和一个闭包，而不是两个类实例。

Swift 提供了一个优雅的解决方案，称为闭包捕获列表。然而，在您学习如何使用闭包捕获列表打破强引用周期之前，了解这种周期是如何产生的会很有用。

下面的示例演示了在使用引用 `self` 的闭包时，如何创建一个强引用周期。这个示例定义了一个名为 `HTMLElement` 的类，它为 HTML 文档中的单个元素提供了一个简单的模型：

```swift
class HTMLElement {


    let name: String
    let text: String?


    lazy var asHTML: () -> String = {
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }


    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }


    deinit {
        print("\(name) is being deinitialized")
    }


}
```

`HTMLElement` 类定义了一个 `name` 属性，该属性指示元素的名称，例如 `"h1"` 用于标题元素， `"p"` 用于段落元素，或 `"br"` 用于换行元素。 `HTMLElement` 还定义了一个可选的 `text` 属性，您可以将其设置为表示要在该 HTML 元素中呈现的文本的字符串。

除了这两个简单属性， `HTMLElement` 类定义了一种称为 `asHTML` 的懒属性。该属性引用一个闭包，该闭包将 `name` 和 `text` 组合成一个 HTML 字符串片段。 `asHTML` 属性的类型是 `() -> String` ，即“一个不带参数并返回 `String` 值的函数”。

默认情况下， `asHTML` 属性被分配一个返回 HTML 标签字符串表示的闭包。如果存在，标签包含可选的 `text` 值；如果 `text` 不存在，则没有文本内容。对于段落元素，闭包将返回 `"<p>some text</p>"` 或 `"<p />"` ，具体取决于 `text` 属性是否等于 `"some text"` 或 `nil` 。

`asHTML` 属性的命名和使用方式有点像实例方法。然而，由于 `asHTML` 是一个闭包属性而不是实例方法，您可以用自定义闭包替换 `asHTML` 属性的默认值，如果您想更改特定 HTML 元素的 HTML 渲染。

例如， `asHTML` 属性可以设置为一个闭包，如果 `text` 属性为 `nil` ，则默认为某些文本，以防止表示返回一个空的 HTML 标签：

```swift
let heading = HTMLElement(name: "h1")
let defaultText = "some default text"
heading.asHTML = {
    return "<\(heading.name)>\(heading.text ?? defaultText)</\(heading.name)>"
}
print(heading.asHTML())
// Prints "<h1>some default text</h1>"
```

> 注意
>
> `asHTML` 属性被声明为懒加载属性，因为它仅在元素实际需要作为某个 HTML 输出目标的字符串值进行渲染时才需要。 `asHTML` 是懒加载属性的事实意味着您可以在默认闭包中引用 `self` ，因为懒加载属性在初始化完成并且 `self` 确定存在之后才会被访问。

`HTMLElement` 类提供了一个单一的初始化器，它接受一个 `name` 参数（如果需要）和一个 `text` 参数以初始化一个新元素。该类还定义了一个析构函数，当 `HTMLElement` 实例被释放时，会打印一条消息以显示。

以下是如何使用 `HTMLElement` 类来创建和打印一个新实例：

```swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
print(paragraph!.asHTML())
// Prints "<p>hello, world</p>"
```

> 注意
>
> 上面的 `paragraph` 变量被定义为一个可选的 `HTMLElement` ，因此可以在下面设置为 `nil` 以演示强引用循环的存在。

不幸的是，上述的 `HTMLElement` 类在 `HTMLElement` 实例和用于其默认 `asHTML` 值的闭包之间创建了一个强引用循环。循环的样子如下：

![closureReferenceCycle01](https://docs.swift.org/swift-book/images/org.swift.tspl/closureReferenceCycle01@2x.png){.light-only}
![closureReferenceCycle01~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/closureReferenceCycle01~dark@2x.png){.dark-only}

实例的 `asHTML` 属性持有对其闭包的强引用。然而，由于闭包在其主体内引用了 `self` （作为引用 `self.name` 和 `self.text` 的一种方式），闭包捕获了 self，这意味着它持有对 `HTMLElement` 实例的强引用。两个之间创建了一个强引用循环。（有关在闭包中捕获值的更多信息，请参阅捕获值。）

> 注意
>
> 尽管闭包多次引用了 `self` ，但它只捕获了对 `HTMLElement` 实例的一个强引用。

如果将 `paragraph` 变量设置为 `nil` 并打破其对 `HTMLElement` 实例的强引用，强引用循环将阻止同时释放 `HTMLElement` 实例及其闭包：

```swift
paragraph = nil
```

请注意，在 `HTMLElement` 析构函数中的消息未被打印，这表明 `HTMLElement` 实例未被释放。

## [解决闭包的强引用循环](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Resolving-Strong-Reference-Cycles-for-Closures)

通过在闭包的定义中定义捕获列表，您可以解决闭包和类实例之间的强引用循环。捕获列表定义在闭包主体内捕获一个或多个引用类型时使用的规则。与两个类实例之间的强引用循环一样，您将每个捕获的引用声明为弱引用或无主引用，而不是强引用。弱引用或无主引用的适当选择取决于您代码不同部分之间的关系。

> 注意
>
> Swift 要求您在闭包中引用 `self` 的成员时，写 `self.someProperty` 或 `self.someMethod()` （而不仅仅是 `someProperty` 或 `someMethod()` ）。这有助于您记住可能会意外捕获 `self` 。

### [定义捕获列表](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Defining-a-Capture-List)

捕获列表中的每个项都是 `weak` 或 `unowned` 关键字与对类实例的引用（例如 `self` ）或用某个值初始化的变量（例如 `delegate = self.delegate` ）的配对。这些配对写在一对方括号内，用逗号分隔。

如果提供了参数列表和返回类型，请将捕获列表放在闭包的参数列表和返回类型之前：

```swift
lazy var someClosure = {
        [unowned self, weak delegate = self.delegate]
        (index: Int, stringToProcess: String) -> String in
    // closure body goes here
}
```

如果闭包没有指定参数列表或返回类型，因为它们可以从上下文推断出来，请将捕获列表放在闭包的最开始，后面跟上 `in` 关键字：

```swift
lazy var someClosure = {
        [unowned self, weak delegate = self.delegate] in
    // closure body goes here
}
```

### [弱引用和无主引用](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting#Weak-and-Unowned-References)

当闭包和它捕获的实例总是相互引用，并且总是在同一时间被释放时，将捕获定义为无主引用。

相反，当捕获的引用在未来某个时刻可能变为 `nil` 时，将捕获定义为弱引用。弱引用总是可选类型，并在它们引用的实例被释放时自动变为 `nil` 。这使您能够在闭包的主体内检查它们的存在。

> 注意
>
> 如果捕获的引用永远不会变成 `nil` ，则它应始终作为无所有权引用进行捕获，而不是弱引用。

无主引用是解决上面[闭包的强引用循环](#闭包的强引用循环)中 `HTMLElement` 示例的适当捕获方法。以下是如何编写 `HTMLElement` 类以避免循环：

```swift
class HTMLElement {


    let name: String
    let text: String?


    lazy var asHTML: () -> String = {
            [unowned self] in
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }


    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }


    deinit {
        print("\(name) is being deinitialized")
    }


}
```

此实现的 `HTMLElement` 与之前的实现相同，除了在 `asHTML` 闭包中添加了一个捕获列表。在这种情况下，捕获列表是 `[unowned self]` ，这意味着“以无主引用而不是强引用捕获 self”。

您可以像以前一样创建并打印一个 `HTMLElement` 实例：

```swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
print(paragraph!.asHTML())
// Prints "<p>hello, world</p>"
```

这是带有捕获列表的引用的样子：

![closureReferenceCycle02](https://docs.swift.org/swift-book/images/org.swift.tspl/closureReferenceCycle02@2x.png){.light-only}
![closureReferenceCycle02~dark](https://docs.swift.org/swift-book/images/org.swift.tspl/closureReferenceCycle02~dark@2x.png){.dark-only}

这次，闭包对 `self` 的捕获是一个无主引用，并且不会对它捕获的 `HTMLElement` 实例保持强引用。如果您将 `paragraph` 变量的强引用设置为 `nil` ，则 `HTMLElement` 实例会被释放，如下面示例中打印的去初始化器消息所示：

```swift
paragraph = nil
// Prints "p is being deinitialized"
```

有关捕获列表的更多信息，请参见捕获列表。
