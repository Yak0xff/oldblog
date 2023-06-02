---
layout: post
author: Yak
title:  10 个 SwiftUI 最佳实践
tags: 
- SwiftUI
feature: true
categories: 
- Swift Learning
cover: https://raw.githubusercontent.com/zycslog/assets-pro/main/10-swiftui-best-practices.jpg
---

SwiftUI is a modern, declarative UI framework for iOS and MacOS development. It’s designed to make it easier to create user interfaces that are both beautiful and functional. SwiftUI is a great tool for developers to use, but it’s important to understand the best practices for using it.  

SwiftUI 是用于 iOS 和 MacOS 开发的现代声明式 UI 框架。它旨在更轻松地创建既美观又实用的用户界面。 SwiftUI 是供开发人员使用的出色工具，但了解使用它的最佳实践很重要。

In this article, we’ll discuss 10 SwiftUI best practices that will help you create better apps and make the most of SwiftUI’s features. We’ll cover topics like using the right view types, leveraging data binding, and more. By following these best practices, you’ll be able to create more efficient and maintainable SwiftUI apps.  

在本文中，我们将讨论 10 个 SwiftUI 最佳实践，它们将帮助您创建更好的应用程序并充分利用 SwiftUI 的功能。我们将涵盖诸如使用正确的视图类型、利用数据绑定等主题。通过遵循这些最佳实践，您将能够创建更高效和可维护的 SwiftUI 应用程序。

#### 1. Use @State and @Binding for managing state changes  使用@State 和@Binding 来管理状态变化

@State is a property wrapper that allows us to store and manage state changes in our SwiftUI views. It’s used for storing values that can be changed by the user, such as text fields or toggle switches. When the value of a @State variable changes, it triggers an update to the view, which causes the UI to re-render with the new value. This makes it easy to keep track of changes in the UI without having to manually call setNeedsDisplay() every time something changes.  

`@State` 是一个属性包装器，允许我们在 `SwiftUI` 视图中存储和管理状态更改。它用于存储可由用户更改的值，例如文本字段或切换开关。当 `@State` 变量的值发生变化时，它会触发对视图的更新，这会导致 UI 使用新值重新呈现。这使得跟踪 UI 中的更改变得容易，而无需在每次发生更改时手动调用 `setNeedsDisplay()`。

@Binding is similar to @State, but instead of creating its own storage, it uses an existing source of truth. This means that when the value of the binding changes, the view will automatically update with the new value. This is useful for passing data between different parts of the app, such as from a parent view to a child view. By using @Binding, we can ensure that all parts of the app are always up to date with the latest information.  

`@Binding` 类似于@State，但它不是创建自己的存储，而是使用现有的真实来源。这意味着当绑定的值发生变化时，视图将自动更新为新值。这对于在应用程序的不同部分之间传递数据很有用，例如从父视图到子视图。通过使用@Binding，我们可以确保应用程序的所有部分始终与最新信息保持同步。

Using @State and @Binding together is a great way to manage state changes in SwiftUI. They make it easy to keep track of changes in the UI and pass data between different parts of the app. Plus, they help ensure that the UI is always up to date with the latest information.  

一起使用 `@State` 和 `@Binding` 是在 SwiftUI 中管理状态变化的好方法。它们使跟踪 UI 中的更改以及在应用程序的不同部分之间传递数据变得容易。此外，它们有助于确保 UI 始终与最新信息同步。

#### 2. Use @EnvironmentObject to share data between views  使用@EnvironmentObject在视图之间共享数据

@EnvironmentObject is a property wrapper that allows us to inject an object into the environment of any view in our SwiftUI hierarchy. This means that we can pass data from one view to another without having to manually pass it through each view in between.  

`@EnvironmentObject` 是一个属性包装器，它允许我们将对象注入到 SwiftUI 层次结构中任何视图的环境中。这意味着我们可以将数据从一个视图传递到另一个视图，而不必手动将数据传递到它们之间的每个视图。

Using @EnvironmentObject also helps keep our code clean and organized, as all of our shared data is stored in one place. We don’t have to worry about passing the same data around multiple times or creating duplicate copies of the same data. Instead, we just need to create one instance of the object and then use @EnvironmentObject to make sure it’s available everywhere.  

使用`@EnvironmentObject` 还有助于保持我们的代码整洁有序，因为我们所有的共享数据都存储在一个地方。我们不必担心多次传递相同的数据或创建相同数据的副本。相反，我们只需要创建该对象的一个实例，然后使用`@EnvironmentObject` 来确保它在任何地方都可用。

The way this works is that when we create an instance of an object, we wrap it with the @EnvironmentObject property wrapper. Then, whenever we want to access the object, we simply call the .environmentObject() method on the current view. This will return the object that was injected into the environment earlier.  

它的工作方式是当我们创建一个对象的实例时，我们用` @EnvironmentObject `属性包装器包装它。然后，每当我们想要访问该对象时，我们只需在当前视图上调用 `.environmentObject()` 方法即可。这将返回之前注入到环境中的对象。

#### 3. Prefer composition over inheritance  更喜欢组合而不是继承

Composition is a way of combining objects or data types into more complex ones. It allows us to create new objects from existing ones without having to modify the existing objects themselves. This makes it easier to reuse code and keep our codebase DRY (Don’t Repeat Yourself).  

组合是一种将对象或数据类型组合成更复杂的对象或数据类型的方法。它允许我们从现有对象创建新对象，而无需修改现有对象本身。这使得重用代码和保持我们的代码库 **DRY（不要重复自己）**变得更加容易。

By contrast, inheritance is a way of creating a class that inherits properties and behavior from another class. While this can be useful in some cases, it can also lead to tight coupling between classes, making them difficult to maintain and extend.  

相比之下，继承是一种创建从另一个类继承属性和行为的类的方法。虽然这在某些情况下很有用，但它也会导致类之间的紧密耦合，使它们难以维护和扩展。

SwiftUI encourages composition over inheritance by providing powerful tools for composing views. For example, SwiftUI’s ViewBuilder lets you easily compose multiple views together into one view hierarchy. You can also use modifiers like padding(), background(), and font() to apply styling to any view. These features make it easy to build complex user interfaces out of simple components.  

SwiftUI 通过提供用于组合视图的强大工具来鼓励组合而不是继承。例如，SwiftUI 的 `ViewBuilder` 让您可以轻松地将多个视图组合成一个视图层次结构。您还可以使用 `padding()`、`background()` 和 `font()` 等修饰符将样式应用于任何视图。这些特性使得用简单的组件构建复杂的用户界面变得容易。

Furthermore, SwiftUI provides several APIs that make it easy to combine different kinds of data into one view. For example, the List API lets you display an array of items as a list, while the Form API lets you group related fields into a single form. By using these APIs, you can quickly assemble complex user interfaces with minimal effort. 

此外，SwiftUI 提供了多个 API，可以轻松地将不同类型的数据组合到一个视图中。例如，List API 允许您将项目数组显示为列表，而 Form API 允许您将相关字段分组到一个表单中。通过使用这些 API，您可以毫不费力地快速组装复杂的用户界面。

#### 4. Understand how view preferences affect the view hierarchy  了解视图首选项如何影响视图层次结构

View preferences are a way to pass data up the view hierarchy. They allow views lower in the hierarchy to communicate with their parent views, and can be used to customize the behavior of the parent view. For example, if you have a list of items that need to be displayed in a certain order, you could use a view preference to tell the parent view which item should be at the top of the list.  

视图首选项是一种将数据向上传递到视图层次结构的方法。它们允许层次结构中较低的视图与其父视图通信，并可用于自定义父视图的行为。例如，如果您有一个需要按特定顺序显示的项目列表，您可以使用视图首选项来告诉父视图哪个项目应该位于列表的顶部。

The key to understanding how view preferences affect the view hierarchy is knowing when and where to apply them. View preferences should only be applied to views that will be used as parents for other views. This ensures that the view preferences will be passed down to all child views. It also helps keep the view hierarchy organized and easy to understand.  

了解视图首选项如何影响视图层次结构的关键是知道何时何地应用它们。视图首选项应仅应用于将用作其他视图父级的视图。这确保视图首选项将传递给所有子视图。它还有助于保持视图层次结构的组织性和易于理解。

When using SwiftUI, it’s important to think about how view preferences will affect the view hierarchy before adding them to your code. If you add view preferences without considering how they will affect the view hierarchy, you may end up with an unorganized or inefficient view hierarchy. Additionally, if you don’t consider how view preferences will affect the view hierarchy, you may find yourself having to rewrite large sections of code to accommodate changes in the view hierarchy.

使用 SwiftUI 时，在将视图首选项添加到代码之前考虑视图首选项将如何影响视图层次结构非常重要。如果您添加视图首选项而不考虑它们将如何影响视图层次结构，您最终可能会得到一个无组织或低效的视图层次结构。此外，如果您不考虑视图首选项将如何影响视图层次结构，您可能会发现自己不得不重写大部分代码以适应视图层次结构中的更改。


#### 5\. Create custom modifiers to apply multiple styles at once 创建自定义修改器以一次应用多种样式

Custom modifiers are a great way to apply multiple styles at once because they allow you to group related styling properties together. This makes it easier to maintain and update the code, as all of the related style changes can be made in one place. For example, if you want to change the font size for a text view, you can create a custom modifier that sets both the font size and color, rather than having to make two separate calls to set each property individually.

自定义修饰符是一次应用多种样式的好方法，因为它们允许您将相关的样式属性组合在一起。这使得维护和更新代码变得更加容易，因为所有相关的样式更改都可以在一个地方进行。例如，如果您想更改文本视图的字体大小，您可以创建一个自定义修改器来设置字体大小和颜色，而不必进行两次单独的调用来分别设置每个属性。

Creating custom modifiers is also beneficial when working with complex views, such as lists or grids. By creating a single modifier that applies multiple styles, you can quickly and easily apply those same styles to every item in the list or grid. This saves time and effort compared to manually setting the style for each individual item.
在处理复杂视图（如列表或网格）时，创建自定义修饰符也很有用。通过创建应用多种样式的单个修饰符，您可以快速轻松地将这些相同的样式应用到列表或网格中的每个项目。与手动为每个单独的项目设置样式相比，这可以节省时间和精力。

To create a custom modifier, you first need to define a struct that conforms to the ViewModifier protocol. The body of this struct should contain the styling properties you wish to apply. You then call the modifier() method on your view, passing in an instance of the struct you just created. This will apply the styling properties defined in the struct to the view.

要创建自定义修饰符，首先需要定义一个符合 ViewModifier 协议的结构体。此结构的主体应包含您希望应用的样式属性。然后在视图上调用 modifier() 方法，传入刚创建的结构的实例。这会将结构中定义的样式属性应用于视图。

You can also use custom modifiers to add additional functionality to a view. For example, you could create a modifier that adds a tap gesture recognizer to a view, allowing it to respond to user input. This allows you to keep your view code clean and organized, while still adding extra features.

您还可以使用自定义修饰符向视图添加附加功能。例如，您可以创建一个修改器，将点击手势识别器添加到视图中，使其能够响应用户输入。这允许您保持您的视图代码干净和有条理，同时仍然添加额外的功能。

#### 6. Leverage type-safe identifiers in ForEach loops 在 ForEach 循环中利用类型安全标识符

When using ForEach loops, it is important to use type-safe identifiers. This means that the identifier should be of a type that can uniquely identify each element in the loop. A common example of this would be an array of structs or classes with unique IDs. By leveraging type-safe identifiers, we are able to ensure that each item in the loop has a unique identifier and thus can be referenced correctly when needed.

使用 ForEach 循环时，使用类型安全标识符很重要。这意味着标识符应该是可以唯一标识循环中每个元素的类型。一个常见的例子是具有唯一 ID 的结构或类数组。通过利用类型安全的标识符，我们能够确保循环中的每个项目都有一个唯一的标识符，因此可以在需要时正确引用。

Using type-safe identifiers also helps us avoid potential bugs related to incorrect references. For instance, if we were to use a non-type-safe identifier such as an index number, then there is a chance that two elements could have the same index number, leading to unexpected behavior. By using type-safe identifiers, we can guarantee that each element will have its own unique identifier and thus no confusion will arise.

使用类型安全标识符还可以帮助我们避免与不正确引用相关的潜在错误。例如，如果我们要使用非类型安全的标识符（例如索引号），那么两个元素可能具有相同的索引号，从而导致意外行为。通过使用类型安全的标识符，我们可以保证每个元素都有自己唯一的标识符，因此不会出现混淆。

Furthermore, by using type-safe identifiers, we can make our code more readable and maintainable. Since the identifier is of a specific type, it is easier for other developers to understand what the identifier represents and how it is used. Additionally, since the identifier is of a specific type, it is easier to debug any issues that may arise due to incorrect references.

此外，通过使用类型安全标识符，我们可以使我们的代码更具可读性和可维护性。由于标识符是特定类型的，因此其他开发人员更容易理解标识符代表什么以及如何使用。此外，由于标识符是特定类型的，因此更容易调试由于不正确引用而可能出现的任何问题。

#### 7. Take advantage of Combine framework for asynchronous operations 利用 Combine 框架进行异步操作

The Combine framework is a reactive programming framework that provides an easy-to-use declarative Swift API for processing values over time. It allows developers to easily create and manipulate asynchronous data streams, which are essential when working with SwiftUI. This is because SwiftUI relies heavily on the use of bindings, which require up-to-date information in order to update the UI accordingly. The Combine framework makes it easier to manage these bindings by providing a unified way to handle asynchronous operations.

Combine 框架是一个反应式编程框架，它提供了一个易于使用的声明式 Swift API，用于随时间处理值。它允许开发人员轻松创建和操作异步数据流，这在使用 SwiftUI 时必不可少。这是因为 SwiftUI 严重依赖绑定的使用，绑定需要最新信息才能相应地更新 UI。 Combine 框架通过提供统一的方式来处理异步操作，使管理这些绑定变得更加容易。

Using the Combine framework also helps keep code clean and organized. By using publishers and subscribers, developers can separate their logic into distinct components, making it easier to debug and maintain. Additionally, since the Combine framework is built on top of Apple’s Grand Central Dispatch (GCD) technology, it offers better performance than other solutions.

使用 Combine 框架还有助于保持代码整洁有序。通过使用发布者和订阅者，开发人员可以将他们的逻辑分离到不同的组件中，从而更容易调试和维护。此外，由于 Combine 框架建立在 Apple 的 Grand Central Dispatch (GCD) 技术之上，因此它提供了比其他解决方案更好的性能。



When using the Combine framework with SwiftUI, there are two main steps: creating a publisher and subscribing to it. To create a publisher, developers must first define a type of Publisher they want to use. For example, if they want to fetch data from a web service, they would use a URLSession.DataTaskPublisher. Once the publisher has been created, developers can then subscribe to it using one of the provided operators such as sink or assign. These operators will take care of updating the UI whenever new data is received.

将 Combine 框架与 SwiftUI 结合使用时，有两个主要步骤：创建发布者和订阅发布者。要创建发布者，开发人员必须首先定义他们要使用的发布者类型。例如，如果他们想从 Web 服务中获取数据，他们会使用 URLSession.DataTaskPublisher。创建发布者后，开发人员可以使用提供的运算符之一（例如 sink 或 assign）订阅它。每当收到新数据时，这些操作员都会负责更新 UI。



#### 8. Use GeometryReader when working with dynamic layout 使用动态布局时使用 GeometryReader

GeometryReader is a container view that defines its own coordinate space for its content. It allows us to access the size and position of its frame, which can be used to create dynamic layouts based on the available space. This makes it ideal for creating adaptive user interfaces that adjust their layout depending on the device or orientation.

GeometryReader 是一个容器视图，它为其内容定义了自己的坐标空间。它允许我们访问其框架的大小和位置，这可用于根据可用空间创建动态布局。这使得它非常适合创建自适应用户界面，根据设备或方向调整布局。



Using GeometryReader also helps keep our code clean and organized by allowing us to separate out the layout logic from the rest of the view’s code. We can define all of our layout calculations in one place, making them easier to maintain and debug.

使用 GeometryReader 还有助于保持我们的代码整洁有序，因为它允许我们将布局逻辑与视图代码的其余部分分开。我们可以在一个地方定义我们所有的布局计算，使它们更容易维护和调试。



When using GeometryReader, we can use the geometry parameter to access the size and position of the view’s frame. This allows us to calculate the sizes and positions of other views relative to the GeometryReader’s frame. For example, if we want to center a view within the GeometryReader’s frame, we can use the width and height properties of the geometry parameter to calculate the exact coordinates needed to do so.

使用 GeometryReader 时，我们可以使用几何参数来访问视图框架的大小和位置。这允许我们计算其他视图相对于 GeometryReader 框架的大小和位置。例如，如果我们想在 GeometryReader 的框架内将视图居中，我们可以使用几何参数的 width 和 height 属性来计算这样做所需的精确坐标。



We can also use the safeAreaInsets property of the geometry parameter to account for any additional insets introduced by the system (such as the notch on an iPhone X). This ensures that our UI elements are always positioned correctly regardless of the device or orientation.

我们还可以使用 geometry 参数的 safeAreaInsets 属性来考虑系统引入的任何额外插入（例如 iPhone X 上的凹口）。这确保我们的 UI 元素始终正确定位，无论设备或方向如何。



#### 9. Avoid force unwrapping optionals unless absolutely necessary 除非绝对必要，否则避免强行展开可选值

Force unwrapping optionals is a way of accessing the value stored inside an optional without checking if it contains a value or not. This can be done by adding an exclamation mark (!) after the variable name, which will cause the program to attempt to access the value regardless of whether it exists or not. If the optional does not contain a value, this will result in a runtime error and crash the app.

Force unwrapping optionals 是一种访问存储在 optional 中的值而不检查它是否包含值的方法。这可以通过在变量名后添加感叹号 (!) 来完成，这将导致程序尝试访问该值，而不管它是否存在。如果可选不包含值，这将导致运行时错误并使应用程序崩溃。



To avoid force unwrapping optionals, SwiftUI provides several methods for safely accessing values from optionals. The most common method is using the “if let” statement, which checks if the optional contains a value before attempting to access it. If the optional does contain a value, then the code within the “if let” block will execute; otherwise, the code will be skipped. This ensures that the app won’t crash due to trying to access a nil value.

为了避免强制展开可选值，SwiftUI 提供了几种方法来安全地从可选值访问值。最常见的方法是使用“if let”语句，它会在尝试访问可选值之前检查它是否包含一个值。如果可选项确实包含一个值，那么将执行“if let”块中的代码；否则，代码将被跳过。这确保应用程序不会因为尝试访问 nil 值而崩溃。



SwiftUI also provides the “guard let” statement, which works similarly to the “if let” statement but with one key difference: if the optional does not contain a value, the guard let statement will exit the current scope instead of executing any code. This makes it useful for ensuring that certain conditions are met before continuing with the rest of the code.

SwiftUI 还提供了“guard let”语句，它的工作方式与“if let”语句类似，但有一个关键区别：如果可选项不包含值，guard let 语句将退出当前作用域，而不是执行任何代码。这有助于确保在继续执行其余代码之前满足某些条件。



The last method provided by SwiftUI for avoiding force unwrapping optionals is the “nil coalescing operator” (??). This operator allows you to provide a default value that will be used if the optional does not contain a value. This is useful for providing a fallback value in case the optional is nil, thus preventing the app from crashing.


SwiftUI 提供的最后一个避免强制展开可选值的方法是“nil coalescing operator”（？？）。此运算符允许您提供默认值，如果可选不包含值，将使用该默认值。这对于在可选项为 nil 的情况下提供回退值很有用，从而防止应用程序崩溃。


#### 10. Prefer SwiftUI’s native components over UIKit/AppKit equivalents  比 UIKit/AppKit 等价物更喜欢 SwiftUI 的原生组件

SwiftUI is designed to be a declarative UI framework, meaning that it allows developers to define the desired state of their user interface and then have SwiftUI take care of the rest. This means that when using SwiftUI, developers should strive to use native components whenever possible in order to get the most out of the framework.

SwiftUI 被设计成一个声明式 UI 框架，这意味着它允许开发人员定义他们的用户界面的期望状态，然后让 SwiftUI 处理其余的事情。这意味着在使用 SwiftUI 时，开发人员应尽可能使用原生组件，以充分利用框架。



Using native components has several advantages over using UIKit/AppKit equivalents. For one, native components are more likely to be optimized for performance since they are built specifically for SwiftUI. Additionally, native components often provide better integration with other parts of the SwiftUI framework, such as data binding and animation. Finally, native components tend to be easier to work with since they are designed to fit into the SwiftUI paradigm.

使用原生组件比使用 UIKit/AppKit 等价物有几个优势。一方面，原生组件更有可能针对性能进行优化，因为它们是专门为 SwiftUI 构建的。此外，原生组件通常可以更好地与 SwiftUI 框架的其他部分集成，例如数据绑定和动画。最后，原生组件往往更易于使用，因为它们旨在适应 SwiftUI 范例。



When working with SwiftUI, developers should always look for ways to use native components instead of UIKit/AppKit equivalents. If there isn’t an existing component that meets the needs of the project, developers can create custom views by combining existing components or creating new ones from scratch. Custom views can also be used to extend the functionality of existing components, allowing developers to customize them to meet their specific needs.

在使用 SwiftUI 时，开发人员应该始终寻找使用原生组件而不是 UIKit/AppKit 等价物的方法。如果没有满足项目需求的现有组件，开发人员可以通过组合现有组件或从头创建新组件来创建自定义视图。自定义视图也可用于扩展现有组件的功能，允许开发人员自定义它们以满足他们的特定需求。


