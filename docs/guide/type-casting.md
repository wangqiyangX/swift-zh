# 类型转换

> 确定一个值的运行时类型，并提供更具体的类型信息。

类型转换是一种检查实例类型的方法，或将该实例视为其自身类层次结构中其他位置的不同超类或子类。

在 Swift 中，类型转换是通过 `is` 和 `as` 运算符实现的。这两个运算符提供了一种简单而直观的方式来检查值的类型或将值转换为不同的类型。

您还可以使用类型转换来检查某个类型是否符合协议，如《检查协议符合性》中所述。

## [为类型转换定义类层次结构](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/typecasting#Defining-a-Class-Hierarchy-for-Type-Casting)

您可以使用类和子类的层次结构进行类型转换，以检查特定类实例的类型，并将该实例转换为同一层次结构中的另一个类。下面的三个代码片段定义了一个类的层次结构和一个包含这些类实例的数组，以便在类型转换的示例中使用。

第一个代码片段定义了一个新的基类，称为 `MediaItem` 。这个类为出现在数字媒体库中的任何类型的项目提供基本功能。具体来说，它声明了一个类型为 `String` 的 `name` 属性，以及一个 `init(name:)` 初始化器。（假设所有媒体项目，包括所有电影和歌曲，都将有一个名称。）

```swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}
```

下一个代码片段定义了两个 `MediaItem` 的子类。第一个子类 `Movie` 封装了关于电影或影片的附加信息。它在基类 `MediaItem` 之上添加了一个 `director` 属性，并具有相应的初始化器。第二个子类 `Song` 在基类上添加了一个 `artist` 属性和初始化器：

```swift
class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}


class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}
```

最后一个代码片段创建了一个名为 `library` 的常量数组，其中包含两个 `Movie` 实例和三个 `Song` 实例。 `library` 数组的类型通过用数组字面量的内容初始化它来推断。Swift 的类型检查器能够推断出 `Movie` 和 `Song` 具有共同的超类 `MediaItem` ，因此它推断出 `library` 数组的类型为 `[MediaItem]` ：

```swift
let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
]
// the type of "library" is inferred to be [MediaItem]
```

存储在 `library` 中的项目仍然是 `Movie` 和 `Song` 实例在后台。然而，如果您遍历这个数组的内容，您收到的项目被类型化为 `MediaItem` ，而不是 `Movie` 或 `Song` 。为了以它们的原生类型进行操作，您需要检查它们的类型，或者将它们转换为不同的类型，如下所述。

## [检查类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/typecasting#Checking-Type)

使用类型检查运算符 ( `is` ) 来检查一个实例是否属于某个子类类型。如果实例属于该子类类型，类型检查运算符返回 `true` ，如果不属于，则返回 `false` 。

下面的示例定义了两个变量， `movieCount` 和 `songCount` ，它们计算 `Movie` 和 `Song` 实例在 `library` 数组中的数量：

```swift
var movieCount = 0
var songCount = 0


for item in library {
    if item is Movie {
        movieCount += 1
    } else if item is Song {
        songCount += 1
    }
}


print("Media library contains \(movieCount) movies and \(songCount) songs")
// Prints "Media library contains 2 movies and 3 songs"
```

此示例遍历 `library` 数组中的所有项。在每次迭代中， `for` - `in` 循环将 `item` 常量设置为数组中的下一个 `MediaItem` 。

`item is Movie` 如果当前 `MediaItem` 是 `Movie` 实例则返回 `true` ，如果不是则返回 `false` 。类似地， `item is Song` 检查该项是否是 `Song` 实例。在 `for` - `in` 循环结束时， `movieCount` 和 `songCount` 的值包含每种类型的 `MediaItem` 实例的计数。

## [向下转型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/typecasting#Downcasting)

某个类类型的常量或变量实际上可能在幕后引用一个子类的实例。在您认为这种情况存在时，可以尝试使用类型转换操作符 ( `as?` 或 `as!` ) 向下转型为子类类型。

因为下转可能会失败，因此类型转换操作符有两种不同的形式。条件形式， `as?` ，返回您试图下转到的类型的可选值。强制形式， `as!` ，尝试下转并强制解包结果，作为一个单一的复合操作。

当您不确定下转是否会成功时，请使用类型转换操作符的条件形式 ( `as?` )。此形式的操作符将始终返回一个可选值，如果下转不可能，则该值将是 `nil` 。这使您能够检查下转是否成功。

只有在您确定下转总会成功时，才使用类型转换操作符的强制形式 ( `as!` )。如果您尝试下转到不正确的类类型，则此形式的操作符将触发运行时错误。

下面的例子对 `library` 中的每个 `MediaItem` 进行迭代，并为每个项目打印适当的描述。为此，它需要将每个项目作为真正的 `Movie` 或 `Song` 进行访问，而不仅仅是作为 `MediaItem` 。这是必要的，以使其能够访问 `Movie` 或 `Song` 的 `director` 或 `artist` 属性，以便在描述中使用。

在这个例子中，数组中的每个项可能是一个 `Movie` ，或者它可能是一个 `Song` 。您无法事先知道每个项实际使用哪个类，因此在循环的每次迭代中使用类型转换运算符的条件形式 ( `as?` ) 来检查下转型是合适的。

```swift
for item in library {
    if let movie = item as? Movie {
        print("Movie: \(movie.name), dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: \(song.name), by \(song.artist)")
    }
}


// Movie: Casablanca, dir. Michael Curtiz
// Song: Blue Suede Shoes, by Elvis Presley
// Movie: Citizen Kane, dir. Orson Welles
// Song: The One And Only, by Chesney Hawkes
// Song: Never Gonna Give You Up, by Rick Astley
```

示例首先尝试将当前的 `item` 强制转换为 `Movie` 。由于 `item` 是一个 `MediaItem` 的实例，因此它可能是一个 `Movie` ；同样，它也可能是一个 `Song` ，甚至只是一个基类 `MediaItem` 。由于这种不确定性，类型转换运算符的 `as?` 形式在尝试强制转换为子类类型时返回一个可选值。 `item as? Movie` 的结果类型为 `Movie?` ，或“可选 `Movie` ”。

向 `Movie` 的下转型在库数组中的 `Song` 实例上失败。为了解决这个问题，上面的示例使用可选绑定来检查可选的 `Movie` 是否实际包含一个值（也就是说，找出下转型是否成功）。这个可选绑定写作“ `if let movie = item as? Movie` ”，可以理解为：

"“尝试将 `item` 作为 `Movie` 访问。如果成功，将一个新的临时常量 `movie` 设置为返回的可选值 `Movie` 中存储的值。”"

如果向下转型成功，则使用 `movie` 的属性打印该 `Movie` 实例的描述，包括其 `director` 的名称。类似的原则用于检查 `Song` 实例，并在库中找到 `Song` 时打印适当的描述（包括 `artist` 名称）。

> 注意
>
> 类型转换实际上并不会修改实例或更改其值。底层实例保持不变；它只是被视为和访问为已转换类型的实例。

## [对 Any 和 AnyObject 的类型转换](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/typecasting#Type-Casting-for-Any-and-AnyObject)

Swift 提供了两种特殊类型用于处理非特定类型：

- `Any` 可以表示任何类型的实例，包括函数类型。
- `AnyObject` 可以表示任何类类型的实例。

仅在您明确需要它们提供的行为和功能时使用 `Any` 和 `AnyObject` 。在您的代码中，明确您期望使用的类型总是更好。

这是一个使用 `Any` 处理不同类型混合的示例，包括函数类型和非类类型。该示例创建了一个名为 `things` 的数组，可以存储类型为 `Any` 的值：

```swift
var things: [Any] = []


things.append(0)
things.append(0.0)
things.append(42)
things.append(3.14159)
things.append("hello")
things.append((3.0, 5.0))
things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
things.append({ (name: String) -> String in "Hello, \(name)" })
```

`things` 数组包含两个 `Int` 值、两个 `Double` 值、一个 `String` 值、一个类型为 `(Double, Double)` 的元组、电影“捉鬼敢死队”，以及一个接受 `String` 值并返回另一个 `String` 值的闭包表达式。

要发现只被知道类型为 `Any` 或 `AnyObject` 的常量或变量的具体类型，可以在 `switch` 语句的 cases 中使用 `is` 或 `as` 模式。以下示例遍历 `things` 数组中的项，并使用 `switch` 语句查询每个项的类型。 `switch` 语句的多个 cases 将其匹配的值绑定到指定类型的常量，以便能够打印其值：

```swift
for thing in things {
    switch thing {
    case 0 as Int:
        print("zero as an Int")
    case 0 as Double:
        print("zero as a Double")
    case let someInt as Int:
        print("an integer value of \(someInt)")
    case let someDouble as Double where someDouble > 0:
        print("a positive double value of \(someDouble)")
    case is Double:
        print("some other double value that I don't want to print")
    case let someString as String:
        print("a string value of \"\(someString)\"")
    case let (x, y) as (Double, Double):
        print("an (x, y) point at \(x), \(y)")
    case let movie as Movie:
        print("a movie called \(movie.name), dir. \(movie.director)")
    case let stringConverter as (String) -> String:
        print(stringConverter("Michael"))
    default:
        print("something else")
    }
}


// zero as an Int
// zero as a Double
// an integer value of 42
// a positive double value of 3.14159
// a string value of "hello"
// an (x, y) point at 3.0, 5.0
// a movie called Ghostbusters, dir. Ivan Reitman
// Hello, Michael
```

> 注意
>
> `Any` 类型表示任何类型的值，包括可选类型。如果在期望类型为 `Any` 的地方使用可选值，Swift 会给出警告。如果确实需要将可选值用作 `Any` 值，可以使用 `as` 运算符将可选值显式转换为 `Any` ，如下所示。
>
> ```swift
> let optionalNumber: Int? = 3
> things.append(optionalNumber)        // Warning
> things.append(optionalNumber as Any) // No warning
> ```
