# 并发

> 执行异步操作。

Swift 内置支持以结构化的方式编写异步和并行代码。异步代码可以暂停并稍后恢复，尽管在任何时候只有一部分程序执行。在程序中暂停和恢复代码使其能够在更新 UI 等短期操作上继续进展，同时继续处理长时间运行的操作，例如通过网络获取数据或解析文件。并行代码意味着多个代码片段同时运行——例如，一台四核处理器的计算机可以同时运行四个代码片段，每个核心执行其中一个任务。使用并行和异步代码的程序同时执行多个操作，并暂停等待外部系统的操作。

来自并行或异步代码的额外调度灵活性也带来了复杂性增加的代价。Swift 让您以一种方式表达您的意图，这种方式支持某些编译时检查 — 例如，您可以使用 actors 安全地访问可变状态。然而，将并发添加到慢或有错误的代码并不能保证它会变快或变正确。实际上，添加并发甚至可能使您的代码更难调试。然而，在需要并发的代码中使用 Swift 的语言级并发支持意味着 Swift 可以帮助您在编译时捕获问题。

本章其余部分使用术语并发来指代这种异步和并行代码的常见组合。

> 注意
>
> 如果您以前编写过并发代码，您可能习惯于使用线程。Swift 中的并发模型建立在线程之上，但您并不直接与它们交互。在 Swift 中，一个异步函数可以放弃它正在运行的线程，这样就允许另一个异步函数在该线程上运行，而第一个函数则被阻塞。当异步函数恢复时，Swift 不对该函数将在哪个线程上运行做出任何保证。

虽然可以在不使用 Swift 语言支持的情况下编写并发代码，但这样的代码往往更难以阅读。例如，以下代码下载一系列照片名称，下载该列表中的第一张照片，并将该照片显示给用户：

```swift
listPhotos(inGallery: "Summer Vacation") { photoNames in
    let sortedNames = photoNames.sorted()
    let name = sortedNames[0]
    downloadPhoto(named: name) { photo in
        show(photo)
    }
}
```

即使在这个简单的案例中，由于代码必须写成一系列完成处理程序，您最终会写出嵌套的闭包。在这种风格中，更复杂的深度嵌套代码可能迅速变得难以处理。

## [定义和调用异步函数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency#Defining-and-Calling-Asynchronous-Functions)

异步函数或异步方法是一种特殊类型的函数或方法，在执行过程中可以被挂起。这与普通的同步函数和方法形成对比，后者要么运行到完成，要么抛出错误，或者永不返回。异步函数或方法仍然会执行这三件事情中的一种，但它也可以在等待某些事情时在中间暂停。在异步函数或方法的主体内，您标记每一个可以挂起执行的地方。

为了表明一个函数或方法是异步的，您在其参数后的声明中写入 `async` 关键字，类似于如何使用 `throws` 来标记一个抛出异常的函数。如果函数或方法返回一个值，您在返回箭头 ( `->` ) 前写 `async` 。例如，这里是您可能如何获取画廊中照片的名称：

```swift
func listPhotos(inGallery name: String) async -> [String] {
    let result = // ... some asynchronous networking code ...
    return result
}
```

对于既是异步又是抛出异常的函数或方法，您在 `throws` 前写 `async` 。

当调用一个异步方法时，执行会暂停，直到该方法返回。您在调用前写 `await` 来标记可能的暂停点。这就像在调用抛出异常的函数时写 `try` ，以标记如果发生错误程序流可能的变化。在异步方法内部，只有当您调用另一个异步方法时，执行流才会暂停——暂停绝不是隐式或抢占式的——这意味着每个可能的暂停点都用 `await` 标记。在您的代码中标记所有可能的暂停点有助于使并发代码更易于阅读和理解。

例如，下面的代码获取画廊中所有图片的名字，然后展示第一张图片：

```swift
let photoNames = await listPhotos(inGallery: "Summer Vacation")
let sortedNames = photoNames.sorted()
let name = sortedNames[0]
let photo = await downloadPhoto(named: name)
show(photo)
```

因为 `listPhotos(inGallery:)` 和 `downloadPhoto(named:)` 函数都需要进行网络请求，它们可能需要相对较长的时间来完成。在返回箭头前写 `async` 使它们都变为异步，这样应用程序的其余部分可以继续运行，而这段代码则等待图片准备好。

为了理解上述示例的并发特性，这里是一个可能的执行顺序：

1. 代码从第一行开始运行，并一直运行到第一个 `await` 。它调用 `listPhotos(inGallery:)` 函数，并在等待该函数返回时暂停执行。
2. 当这段代码的执行被暂停时，同一程序中的其他并发代码会运行。例如，也许一个长时间运行的后台任务继续更新新照片画廊的列表。那段代码也会运行，直到下一个暂停点，标记为 `await` ，或者直到它完成。
3. 在 `listPhotos(inGallery:)` 返回后，这段代码从该点继续执行。它将返回的值赋给 `photoNames` 。
4. 定义 `sortedNames` 和 `name` 的行是常规的同步代码。因为这些行上没有标记 `await` ，所以没有任何可能的暂停点。
5. 下一个 `await` 标记对 `downloadPhoto(named:)` 函数的调用。此代码再次暂停执行，直到该函数返回，从而给其他并发代码提供运行的机会。
6. `downloadPhoto(named:)` 返回后，它的返回值被赋值给 `photo` ，然后在调用 `show(_:)` 时作为参数传递。

您的代码中的可能暂停点用 `await` 标记，表示当前代码段在等待异步函数或方法返回时可能会暂停执行。这也称为让出线程，因为在后台，Swift 会挂起您当前线程上代码的执行，并在该线程上运行其他代码。由于带有 `await` 的代码需要能够暂停执行，因此程序中的某些位置才能调用异步函数或方法：

- 异步函数、方法或属性主体中的代码。
- 在标记为 `@main` 的结构、类或枚举的静态 `main()` 方法中的代码。
- 在下面的无结构并发中，代码位于一个无结构的子任务中。

您可以通过调用 `Task.yield()` 方法显式插入一个挂起点。

```swift
func generateSlideshow(forGallery gallery: String) async {
    let photos = await listPhotos(inGallery: gallery)
    for photo in photos {
        // ... render a few seconds of video for this photo ...
        await Task.yield()
    }
}
```

假设渲染视频的代码是同步的，它不包含任何挂起点。渲染视频的工作可能也会很耗时。然而，您可以定期调用 `Task.yield()` 来显式添加挂起点。这样构建长时间运行的代码可以让 Swift 在完成此任务和让程序中其他任务继续进展之间进行平衡。

`Task.sleep(for:tolerance:clock:)` 方法在编写简单代码以学习并发工作原理时非常有用。此方法会将当前任务暂停至少给定的时间。以下是一个使用 `sleep(for:tolerance:clock:)` 来模拟等待网络操作的 `listPhotos(inGallery:)` 函数的版本：

```swift
func listPhotos(inGallery name: String) async throws -> [String] {
    try await Task.sleep(for: .seconds(2))
    return ["IMG001", "IMG99", "IMG0404"]
}
```

上面代码中 `listPhotos(inGallery:)` 的版本是异步的并且会抛出异常，因为对 `Task.sleep(until:tolerance:clock:)` 的调用可能会抛出错误。当您调用这个版本的 `listPhotos(inGallery:)` 时，您需要同时写 `try` 和 `await` ：

```swift
let photos = try await listPhotos(inGallery: "A Rainy Weekend")
```

异步函数与抛出函数有一些相似之处：当您定义一个异步或抛出函数时，您用 `async` 或 `throws` 标记它，并用 `await` 或 `try` 标记对该函数的调用。异步函数可以调用另一个异步函数，就像抛出函数可以调用另一个抛出函数一样。

然而，有一个非常重要的区别。您可以将抛出代码包装在 `do` - `catch` 块中以处理错误，或者使用 `Result` 将错误存储起来以便其他代码处理。这些方法允许您从非抛出代码调用抛出函数。例如：

```swift
func availableRainyWeekendPhotos() -> Result<[String], Error> {
    return Result {
        try listDownloadedPhotos(inGallery: "A Rainy Weekend")
    }
}
```

相反，没有安全的方法将异步代码包装起来，以便可以从同步代码中调用它并等待结果。Swift 标准库故意省略了这种不安全的功能——尝试自己实现可能会导致微妙的竞争、线程问题和死锁。在现有项目中添加并发代码时，从上到下进行具体操作。具体来说，先将最上层的代码转换为使用并发，然后开始转换它调用的函数和方法，逐层处理项目的架构。没有方法可以采取自下而上的方法，因为同步代码永远不能调用异步代码。

## [异步序列](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency#Asynchronous-Sequences)

前一节中的 `listPhotos(inGallery:)` 函数异步地一次性返回整个数组，在数组的所有元素准备好之后。另一种方法是使用异步序列一次等待集合的一个元素。以下是遍历异步序列的样子：

```swift
import Foundation


let handle = FileHandle.standardInput
for try await line in handle.bytes.lines {
    print(line)
}
```

与其使用普通的 `for` - `in` 循环，上面的例子在后面写了 `for` 和 `await` 。就像您调用异步函数或方法时，写 `await` 表示可能的挂起点。一个 `for` - `await` - `in` 循环可能在每次迭代的开始暂停执行，当它等待下一个元素可用时。

以与通过添加对 `Sequence` 协议的遵从性在 `for` - `in` 循环中使用自定义类型相同的方式，您可以通过添加对 `AsyncSequence` 协议的遵从性在 `for` - `await` - `in` 循环中使用自定义类型。

## [并行调用异步函数](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency#Calling-Asynchronous-Functions-in-Parallel)

调用异步函数 `await` 一次只能运行一段代码。在异步代码运行时，调用者会等待该代码完成，然后再继续运行下一行代码。例如，要从画廊中获取前三张照片，可以像下面这样等待三次对 `downloadPhoto(named:)` 函数的调用：

```swift
let firstPhoto = await downloadPhoto(named: photoNames[0])
let secondPhoto = await downloadPhoto(named: photoNames[1])
let thirdPhoto = await downloadPhoto(named: photoNames[2])


let photos = [firstPhoto, secondPhoto, thirdPhoto]
show(photos)
```

这种方法有一个重要的缺点：虽然下载是异步的，并且在下载过程中可以进行其他工作，但一次只能调用 `downloadPhoto(named:)` 一次。每张照片在下一张开始下载之前必须完全下载完成。然而，这些操作并不需要等待——每张照片可以独立下载，甚至可以同时下载。

要调用一个异步函数并让它与周围的代码并行运行，在定义常量时在 `let` 前面写 `async` ，然后在每次使用该常量时写 `await` 。

```swift
async let firstPhoto = downloadPhoto(named: photoNames[0])
async let secondPhoto = downloadPhoto(named: photoNames[1])
async let thirdPhoto = downloadPhoto(named: photoNames[2])


let photos = await [firstPhoto, secondPhoto, thirdPhoto]
show(photos)
```

在这个例子中，所有三个对 `downloadPhoto(named:)` 的调用都在不等待前一个完成的情况下开始。如果有足够的系统资源可用，它们可以同时运行。这些函数调用都没有被标记为 `await` ，因为代码并没有暂停等待函数的结果。相反，执行继续直到 `photos` 被定义的行——在这一点上，程序需要这些异步调用的结果，因此您写 `await` 来暂停执行，直到所有三张照片下载完成。

这里是您可以思考这两种方法之间差异的方式：

- 当后续行的代码依赖于该函数的结果时，使用 `await` 调用异步函数。这会创建顺序执行的工作。
- 当您在代码中稍后才需要结果时，使用 `async` - `let` 调用异步函数。这会创建可以并行执行的工作。
- `await` 和 `async` - `let` 都允许其他代码在它们被挂起时运行。
- 在这两种情况下，您使用 `await` 标记可能的挂起点，以指示执行会暂停（如有必要），直到异步函数返回。

您还可以在同一代码中混合这两种方法。

## [任务和任务组](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency#Tasks-and-Task-Groups)

任务是可以异步运行的工作单元，作为程序的一部分。所有异步代码都作为某个任务的一部分运行。一个任务本身一次只做一件事，但当您创建多个任务时，Swift 可以安排它们同时运行。

在上一节中描述的 `async` - `let` 语法隐式地创建了一个子任务——当您已经知道程序需要运行哪些任务时，此语法效果很好。您还可以创建一个任务组（一个 `TaskGroup` 的实例），并显式地将子任务添加到该组，这样可以让您更好地控制优先级和取消，并允许您创建动态数量的任务。

任务以层次结构排列。给定任务组中的每个任务都有相同的父任务，每个任务可以有子任务。由于任务和任务组之间的显式关系，这种方法被称为结构化并发。任务之间显式的父子关系有几个优点：

- 在父任务中，您不能忘记等待其子任务完成。
- 当子任务设置更高的优先级时，父任务的优先级会自动提升。
- 当父任务被取消时，所有子任务也会自动被取消。
- 任务局部值会高效且自动地传播到子任务。

这是另一个版本的代码，用于下载处理任意数量照片的照片：

```swift
await withTaskGroup(of: Data.self) { group in
    let photoNames = await listPhotos(inGallery: "Summer Vacation")
    for name in photoNames {
        group.addTask {
            return await downloadPhoto(named: name)
        }
    }


    for await photo in group {
        show(photo)
    }
}
```

上面的代码创建了一个新的任务组，然后创建子任务以下载画廊中的每张照片。Swift 会根据条件允许的情况并发运行尽可能多的这些任务。子任务一旦完成下载一张照片，该照片就会被显示。无法保证子任务完成的顺序，因此来自这个画廊的照片可以以任何顺序显示。

> 注意
>
> 如果下载照片的代码可能会抛出错误，您应该调用 `withThrowingTaskGroup(of:returning:body:)` 。

在上面的代码列表中，每张照片被下载后显示，因此任务组不会返回任何结果。对于返回结果的任务组，您需要添加代码，在您传递给 `withTaskGroup(of:returning:body:)` 的闭包内部累积其结果。

```swift
let photos = await withTaskGroup(of: Data.self) { group in
    let photoNames = await listPhotos(inGallery: "Summer Vacation")
    for name in photoNames {
        group.addTask {
            return await downloadPhoto(named: name)
        }
    }


    var results: [Data] = []
    for await photo in group {
        results.append(photo)
    }


    return results
}
```

与前一个示例类似，此示例为每张照片创建一个子任务以下载它。与前一个示例不同， `for` - `await` - `in` 循环等待下一个子任务完成，将该任务的结果附加到结果数组中，然后继续等待直到所有子任务完成。最后，任务组将下载的照片数组作为其整体结果返回。

### [任务取消](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency#Task-Cancellation)

Swift 并发使用协作取消模型。每个任务在执行的适当时刻检查自己是否已被取消，并适当地响应取消。根据任务正在执行的工作，响应取消通常意味着以下之一：

- 抛出一个错误，比如 `CancellationError`
- 返回 `nil` 或一个空集合
- 返回部分完成的工作

如果图片很大或网络很慢，下载图片可能会花费很长时间。为了让用户在不等待所有任务完成的情况下停止此工作，任务需要检查取消状态并在被取消时停止运行。任务可以通过两种方式完成此操作：调用 `Task.checkCancellation()` 类型的方法，或者读取 `Task.isCancelled` 类型的属性。如果任务被取消，调用 `checkCancellation()` 会抛出错误；抛出的任务可以将错误传播出任务，停止所有任务的工作。这种方法的优点是简单易于实现和理解。为了获得更多灵活性，可以使用 `isCancelled` 属性，这让您可以在停止任务时执行清理工作，比如关闭网络连接和删除临时文件。

```swift
let photos = await withTaskGroup(of: Optional<Data>.self) { group in
    let photoNames = await listPhotos(inGallery: "Summer Vacation")
    for name in photoNames {
        let added = group.addTaskUnlessCancelled {
            guard !Task.isCancelled else { return nil }
            return await downloadPhoto(named: name)
        }
        guard added else { break }
    }


    var results: [Data] = []
    for await photo in group {
        if let photo { results.append(photo) }
    }
    return results
}
```

上述代码相较于之前的版本进行了几处更改：

- 每个任务使用 `TaskGroup.addTaskUnlessCancelled(priority:operation:)` 方法添加，以避免在取消后开始新的工作。
- 在每次调用 `addTaskUnlessCancelled(priority:operation:)` 后，代码确认新的子任务已被添加。如果组被取消， `added` 的值为 `false` —— 在这种情况下，代码停止尝试下载额外的照片。
- 每个任务在开始下载照片之前都会检查是否被取消。如果已被取消，任务将返回 `nil` 。
- 最后，任务组在收集结果时跳过 `nil` 个值。通过返回 `nil` 来处理取消意味着任务组可以返回部分结果——在取消时已经下载的照片——而不是丢弃已完成的工作。

> 注意
>
> 要检查一个任务是否已从该任务外部取消，请使用 `Task.isCancelled` 实例属性，而不是类型属性。

对于需要立即通知取消的工作，请使用 `Task.withTaskCancellationHandler(operation:onCancel:isolation:)` 方法。例如：

```swift
let task = await Task.withTaskCancellationHandler {
    // ...
} onCancel: {
    print("Canceled!")
}


// ... some time later...
task.cancel()  // Prints "Canceled!"
```

使用取消处理程序时，任务取消仍然是协作式的：任务要么运行到完成，要么检查取消并提前停止。由于当取消处理程序启动时，任务仍在运行，因此避免在任务和其取消处理程序之间共享状态，这可能会造成竞争条件。

### [非结构化并发](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency#Unstructured-Concurrency)

除了前面章节中描述的结构化并发方法，Swift 还支持非结构化并发。与属于任务组的任务不同，非结构化任务没有父任务。您可以完全灵活地按需管理非结构化任务，但您也完全负责它们的正确性。要创建一个在当前演员上运行的非结构化任务，请调用 `Task.init(priority:operation:)` 初始化程序。要创建一个不属于当前演员的非结构化任务，更具体地称为分离任务，请调用 `Task.detached(priority:operation:)` 类方法。这两种操作都返回一个您可以与之交互的任务——例如，等待其结果或取消它。

```swift
let newPhoto = // ... some photo data ...
let handle = Task {
    return await add(newPhoto, toGalleryNamed: "Spring Adventures")
}
let result = await handle.value
```

有关管理分离任务的更多信息，请参见 `Task` 。

## [演员](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency#Actors)

您可以使用任务将程序分解为孤立的并发部分。任务彼此隔离，这使得它们可以安全地同时运行，但有时您需要在任务之间共享一些信息。演员允许您在并发代码之间安全地共享信息。

与类一样，演员是引用类型，因此类是引用类型中值类型和引用类型的比较同样适用于演员和类。与类不同，演员只允许一个任务在同一时间访问其可变状态，这使得多个任务中的代码可以安全地与同一演员实例进行交互。例如，这里有一个记录温度的演员：

```swift
actor TemperatureLogger {
    let label: String
    var measurements: [Int]
    private(set) var max: Int


    init(label: String, measurement: Int) {
        self.label = label
        self.measurements = [measurement]
        self.max = measurement
    }
}
```

您使用 `actor` 关键字引入一个演员，后面跟着一对大括号中的定义。 `TemperatureLogger` 演员具有其他代码可以访问的属性，并限制 `max` 属性，使得只有演员内部的代码可以更新最大值。

您使用与结构体和类相同的初始化语法来创建演员的实例。当您访问演员的属性或方法时，您使用 `await` 来标记潜在的挂起点。例如：

```swift
let logger = TemperatureLogger(label: "Outdoors", measurement: 25)
print(await logger.max)
// Prints "25"
```

在这个例子中，访问 `logger.max` 是一个可能的挂起点。因为该演员只允许一个任务同时访问其可变状态，如果其他任务的代码已经在与日志记录器交互，那么这段代码会在等待访问该属性时挂起。

相反，作为演员一部分的代码在访问演员的属性时不会写入 `await` 。例如，这里有一个方法，用于用新温度更新 `TemperatureLogger` ：

```swift
extension TemperatureLogger {
    func update(with measurement: Int) {
        measurements.append(measurement)
        if measurement > max {
            max = measurement
        }
    }
}
```

`update(with:)` 方法已经在演员上运行，因此它不会用 `await` 标记对属性 `max` 的访问。这个方法还显示了演员为什么只允许一次一个任务与其可变状态交互的原因之一：对演员状态的某些更新会暂时破坏不变量。 `TemperatureLogger` 演员跟踪温度列表和最大温度，当您记录新的测量时，它会更新最大温度。在更新过程中，在追加新测量之后但在更新 `max` 之前，温度记录器处于一个临时不一致的状态。防止多个任务同时与同一实例交互可以避免如下事件序列的问题：

1. 您的代码调用了 `update(with:)` 方法。它首先更新了 `measurements` 数组。
2. 在您的代码可以更新 `max` 之前，其他地方的代码会读取最大值和温度数组。
3. 您的代码通过改变 `max` 来完成更新。

在这种情况下，运行在其他地方的代码将读取到错误的信息，因为它对演员的访问在调用 `update(with:)` 的中间被交错，而数据在此时是暂时无效的。当使用 Swift 演员时，您可以防止此问题，因为它们只允许对其状态执行一次操作，并且只有在 `await` 标记的挂起点处，才能中断该代码。由于 `update(with:)` 不包含任何挂起点，因此在更新的中间，没有其他代码可以访问数据。

如果演员外部的代码尝试直接访问这些属性，比如访问一个结构体或类的属性，您将会收到一个编译时错误。例如：

```swift
print(logger.max)  // Error
```

访问 `logger.max` 而不写入 `await` 会失败，因为一个 actor 的属性是该 actor 隔离的本地状态的一部分。访问此属性的代码需要作为 actor 的一部分运行，这是一项异步操作并且需要写入 `await` 。Swift 保证只有在 actor 上运行的代码才能访问该 actor 的本地状态。这一保证被称为 actor 隔离。

Swift 并发模型的以下方面协同工作，使得推理共享可变状态变得更加容易：

- 在可能的暂停点之间的代码按顺序运行，无法被其他并发代码中断。
- 与演员的本地状态交互的代码仅在该演员上运行。
- 一个演员一次只运行一段代码。

由于这些保证，未包含 `await` 且位于 actor 内部的代码可以进行更新，而不必担心程序中其他地方观察到暂时无效的状态。例如，下面的代码将测量的温度从华氏度转换为摄氏度：

```swift
extension TemperatureLogger {
    func convertFahrenheitToCelsius() {
        measurements = measurements.map { measurement in
            (measurement - 32) * 5 / 9
        }
    }
}
```

上面的代码逐个转换测量值数组。在 map 操作进行时，一些温度是华氏度，另一些是摄氏度。然而，由于代码中没有包含 `await` ，因此该方法中没有潜在的暂停点。该方法修改的状态属于演员，保护它不被其他代码读取或修改，除非该代码在演员上运行。这意味着在单位转换进行时，其他代码无法读取部分转换的温度列表。

除了在一个通过省略潜在挂起点来保护临时无效状态的 actor 中编写代码外，您还可以将该代码移动到一个同步方法中。上面的 `convertFahrenheitToCelsius()` 方法是一个同步方法，因此它保证永远不会包含潜在的挂起点。此函数封装了暂时使数据模型不一致的代码，并使任何阅读代码的人更容易识别在数据一致性通过完成工作恢复之前，无法运行其他代码。将来，如果您尝试向此函数添加并发代码，引入可能的挂起点，您将会在编译时遇到错误，而不是引入一个 bug。

## [可发送类型](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency#Sendable-Types)

任务和演员让您将程序分成可以安全并发运行的部分。在任务或演员的实例内部，包含可变状态（如变量和属性）的程序部分称为并发域。某些类型的数据不能在并发域之间共享，因为这些数据包含可变状态，但它并不能防止重叠访问。

可以从一个并发域共享到另一个并发域的类型称为可发送类型。例如，它可以作为参数传递给调用的 actor 方法或作为任务的结果返回。本章前面的示例没有讨论可发送性，因为这些示例使用的都是对在并发域之间传递的数据始终安全的简单值类型。相比之下，有些类型在并发域之间传递时是不安全的。例如，一个包含可变属性且未序列化访问这些属性的类，在您在不同任务之间传递该类的实例时，可能会产生不可预测和不正确的结果。

您通过声明遵循 `Sendable` 协议来标记一个类型为可发送。该协议没有任何代码要求，但它确实有 Swift 强制执行的语义要求。一般来说，一个类型可发送的方式有三种：

- 该类型是值类型，其可变状态由其他可发送数据构成 — 例如，一个具有可发送存储属性的结构体或一个具有可发送相关值的枚举。
- 该类型没有任何可变状态，其不可变状态由其他可发送数据构成 — 例如，只有只读属性的结构体或类。
- 该类型有代码确保其可变状态的安全性，例如一个标记为 `@MainActor` 的类或者一个在特定线程或队列上序列化访问其属性的类。

有关语义要求的详细列表，参见 `Sendable` 协议参考。

某些类型始终是可发送的，例如仅具有可发送属性的结构体和仅具有可发送关联值的枚举。比如：

```swift
struct TemperatureReading: Sendable {
    var measurement: Int
}


extension TemperatureLogger {
    func addReading(from reading: TemperatureReading) {
        measurements.append(reading.measurement)
    }
}


let logger = TemperatureLogger(label: "Tea kettle", measurement: 85)
let reading = TemperatureReading(measurement: 45)
await logger.addReading(from: reading)
```

因为 `TemperatureReading` 是一个只有可发送属性的结构，并且该结构没有标记 `public` 或 `@usableFromInline` ，所以它是隐式可发送的。这是一个结构的版本，其中符合 `Sendable` 协议是隐含的：

```swift
struct TemperatureReading {
    var measurement: Int
}
```

要明确标记一个类型为不可发送，覆盖对 `Sendable` 协议的隐式符合性，请使用扩展：

```swift
struct FileDescriptor {
    let rawValue: CInt
}


@available(*, unavailable)
extension FileDescriptor: Sendable { }
```

上面的代码显示了围绕 POSIX 文件描述符的一个包装器的部分内容。尽管文件描述符的接口使用整数来识别和与打开的文件交互，并且整数值是可发送的，但文件描述符在并发域之间发送是不安全的。

在上面的代码中， `FileDescriptor` 是一个满足隐式可发送性标准的结构。然而，扩展使其对 `Sendable` 的符合性不可用，防止该类型被发送。
