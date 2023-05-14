---
layout: post
author: Yak
title: 使用 SwiftUI 构建大型应用程序---模块化架构指南【译】
tags: 
- SwiftUI
feature: true
categories: 
- Swift Learning
cover: https://raw.githubusercontent.com/zycslog/assets-pro/main/fotis-fotopoulos-SyvsTmuuZyM-unsplash.jpg
---

软件架构始终是一个热门争论的话题，特别是当有这么多不同的选择时。在过去的 8-12 个月里，作者一直在尝试使用 MV 模式来构建客户端/服务器应用程序，并在作者最初的文章中写到了这一点 [SwiftUI 架构 - MV 模式方法的完整指南](https://azamsharp.com/2022/10/06/practical-mv-pattern-crud.html)。在本文中，作者将讨论如何将 MV 模式应用于构建大型客户端/服务器应用程序。从SwiftUI本身的设计模式触发，探索SwiftUI在大型应用程序中如何使用MV模式来构建，以及其合理性等，很值得一读。

> 架构与模式的选择取决于要构建的应用程序的类型。没有哪一种架构适用于所有的项目方案。选择合适你应用程序需求的，才是最佳的架构。

本文大纲：

- 模块化架构
- 理解MV模式
- 屏幕与视图
- 多个聚合模型
- 视图的特定逻辑
- 验证
- 路由
- 分组视图与事件
- 测试

## 模块化架构

软件中的模块化架构是指将软件系统设计和组织成小型、自包含的模块或组件。这些模块可以独立地进行测试和维护。每个模块都有一个特定的目的，解决一个特定的业务需求。

模块化架构在处理由多个团队组成的大型项目时也具有优势。每个团队都可以处理特定的模块，而不会相互干扰。

> 如果您正在处理将由其他团队使用或使用的模块，请确保您正在与他们通信，而不是完全孤立地创建模块。软件开发中存在许多问题仅仅是因为团队之间缺乏沟通。

模块化可以通过几种不同的方式实现。您可以将每个模块公开为包 （SPM），该包可以导入到不同的应用程序中。模块化也可以通过基于特定的分组或文件夹结构来构建应用来实现。请记住，在使用文件夹进行模块化时，您必须特别注意关注点分离和单一责任原则。

> 本文的重点不是 Swift 包管理器，而是如何通过基于应用程序的边界上下文中断应用程序来实现模块化。**Swift Package Manager 可用于将这些依赖项打包到可重用的模块中。**


## 理解MV模式

MV 模式背后的主要思想是允许视图直接与模型对话。这消除了为每个视图创建不必要的视图模型的需要，这只会增加项目的大小，但不提供任何其他好处。

> MV 模式不主张将所有逻辑都放在视图中。该特定模式称为 [容器模式](https://www.patterns.dev/posts/presentational-container-pattern/) .我也在我的博客上谈到过它。你可以在[这里](https://azamsharp.com/2023/01/24/introduction-to-container-pattern.html)阅读它。
> 
关于 SwiftUI 最令人困惑的事情之一是视图。我不怪你，我认为它们不应该被称为观点。它们应该被称为Widgets（Flutter）或Components（React）。SwiftUI 中的视图与传统的 UIKit 视图不同。它们只是您想要在屏幕上显示的内容的声明。

SwiftUI 中的视图让我想起了 ReactJS JSX 语法。让我们看一个非常小的例子。


``` js
function App() {
    return (
        <div>
            <h1>Hello World</h1>
            <button>Save</button>
        </div>
    )
}
```

在上面的 ReactJS 代码中，我们创建了一个名为 `App` 的功能组件。App 组件返回一个包含 `<h1>` 和 `<button>` 的 `<div>` 元素。这里要注意的是，这些不是实际的HTML元素。这些是由 React 框架管理的虚拟 DOM（文档对象模型）元素。主要原因是 React 需要跟踪这些元素的更改，因此它只能渲染已更改的内容。一旦 React 使用差异过程找到更改的元素，这些虚拟 DOM 元素就会用于在屏幕上渲染真实的 HTML 元素。

我相信 SwiftUI 在内部使用相同的概念。body 属性中的视图不是实际视图，而是视图的声明。最终，这些视图被转换为真实视图，然后显示在屏幕上。John Sundell 在他的文章 [SwiftUI 视图与修饰符](https://www.swiftbysundell.com/articles/swiftui-views-versus-modifiers/)中也谈到了这一点。

如果您有兴趣了解有关虚拟DOM概念的更多信息，请查看此演讲标题 [Tom Occhino and Jordan Walke：Facebook上的JS应用程序](https://youtu.be/GW0rj4sNH2w?t=301)。这是Facebook向公众介绍ReactJS的演讲。


苹果在SwiftUI官方文档页面上发布的文章[模型数据](https://developer.apple.com/documentation/swiftui/model-data)中也谈到了这一点。

好的，现在回到MV模式！

在 WWDC 2020 中，题为[SwiftUI 中的数据要点](https://developer.apple.com/videos/play/wwdc2020/10040/)的演讲中，Apple 展示了下图。



![ObservableObject as the data dependency surface](https://azamsharp.com/images/single-source.png)

主要思想是提供对单个层或表面的视图访问，该层或表面用作事实来源，并允许访问应用程序中的所有实体。

Apple 示例项目（包括 [Fruta](https://developer.apple.com/documentation/swiftui/fruta_building_a_feature-rich_app_with_swiftui) 和 [FoodTruck 应用程序](https://developer.apple.com/documentation/swiftui/food_truck_building_a_swiftui_multiplatform_app)）演示了如何针对硬编码数据源使用此模式。但是在WWDC视频标题 [使用Xcode进行服务器端开发](https://developer.apple.com/videos/play/wwdc2022/110360/)中，Apple展示了如何更新现有的FoodTruck应用程序并使用API响应中的数据。

下面的屏幕截图显示了 `FoodTruckModel` 使用 `DonutsServerClient` 检索甜甜圈列表。甜甜圈服务器客户端负责向服务器发出实际请求并下载甜甜圈。下载甜甜圈后，它们将被分配给由FoodTruckModel维护的serverDonuts属性。

![Use Xcode for server-side development](https://azamsharp.com/images/xcode-server.png)

[使用 Xcode 进行服务器端开发](https://developer.apple.com/videos/play/wwdc2022/110360/)

下面是支持网络层的更新图。

![Aggregate Root](https://azamsharp.com/images/aggregate-model-updated.001.jpeg)

> 我知道你在想什么。我们会盲目地根据Apple的代码示例提出建议吗？不！永远不要盲目地接受任何建议。始终投入时间和研究，并权衡每种方法的优缺点。我评估了许多不同的技术和模式，发现这是使用 SwiftUI 构建客户端/服务器应用程序时最好和最简单的选择。**供你的探究！**

根据 Apple 在他们的 WWDC 视频和代码示例中的建议以及我自己的个人经验，我一直在实现一个聚合模型，该模型保存应用程序的整个状态。对于中小型应用，单个聚合模型可能就足够了。对于复杂的应用，可以有多个聚合模型，这些模型会将相关实体组合在一起。本文稍后将讨论多个聚合模型。

> 再次记住，本文是关于客户端/服务器应用程序的。如果您正在使用核心数据或其他任何东西，那么您将不得不进行研究。对于纯粹的核心数据应用程序，我一直在尝试活动记录模式。你可以在[这里](https://azamsharp.com/2023/01/30/active-record-pattern-swiftui-core-data.html)阅读它。


按照[使用 Xcode 进行服务器端开发](https://developer.apple.com/videos/play/wwdc2022/110360/)讲座中讨论的模式，下面是我为我的应用程序实现的 StoreModel。

``` swift
class StoreModel: ObservableObject {
    
    private var storeHTTPClient: StoreHTTPClient
    
    init(storeHTTPClient: StoreHTTPClient) {
        self.storeHTTPClient = storeHTTPClient
    }
    
    @Published var products: [Product] = []
    @Published var categories: [Category] = []
    
    func addProduct(_ product: Product) async throws {
         try await storeHTTPClient.addProduct(product)
    }
    
    func populateProducts() async throws {
        self.products = try await storeHTTPClient.loadProducts()
    }
}
```
`StoreModel` 是一个聚合模型，用于集中应用程序的所有数据。视图直接与 StoreModel 通信以执行查询和暂留操作。StoreModel 也利用 `StoreHTTPClient` ，它用于执行网络操作。StoreHTTPClient 是一个无状态网络层。这意味着它可以用于应用程序的其他部分，而不是SwiftUI，即UIKit，甚至可以在不同的平台（macOS）上使用。

> 在域驱动设计 （DDD） 中，聚合是相关对象的群集，出于数据一致性和事务边界的目的，这些对象被视为单个工作单元。因此，聚合模型是代码中聚合的表示形式，通常为类或类组。


`StoreModel` 可以以各种不同的方式使用。如果只希望数据可用于特定视图，并且希望将对象与视图的生存期相关联，则可以使用 StoreModel 作为@StateObject。但是我经常发现自己将 StoreModel 添加到@EnvironmentObject，以便它可以在注入的视图及其所有子视图中可用。


``` swift
@main
struct StoreAppApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(StoreModel(client: StoreHTTPClient()))
            
        }
    }
}
```

通过@EnvironmentObject注入 StoreModel 后，可以访问 `StoreModel` ，如下面的实现所示。


``` swift
struct ContentView: View {

    @EnvironmentObject private var model: StoreModel
    
    var body: some View {
        ProductListView(products: model.products)
            .task {
                do {
                    try await model.populateProducts()
                } catch {
                    print(error.localizedDescription)
                }
            }
    }
}
```

> 您可能想在所有视图中使用 `@EnvironmentObject` 。虽然，它将按预期工作，但对于较大的应用程序，您需要使演示视图没有任何依赖项。表示视图通常是为实现可重用性而创建的子视图。如果您尝试访问子视图中的 `@EnvironmentObject` ，则会影响它们的可重用性状态，并且它们变得不那么有用。主要原因是现在他们依赖于 `@EnvironmentObject` 向他们提供数据。相反，我们应该遵循自上而下的方法，其中数据从父视图传递到子视图。这也称为[容器/表示模式](https://www.patterns.dev/posts/presentational-container-pattern/)。

除了获取和持久化之外，StoreModel 还可以直接对视图提供排序、过滤、搜索和其他操作。


> 如果我使用传统的 MVVM 模式，那么我将创建多个视图模型来适应每个屏幕。这可以包括 `ProductListViewModel` 、 `ProductViewModel` 、 `AddProductViewModel` 、 `ProductDetailViewModel` 等等。大多数时候，这些视图模型最终只有一个或两个函数，维护单一事实来源可能会变得非常困难。在 MV 模式中，视图本身就是视图模型，因此我们在大多数情况下不需要创建不必要的视图模型。视图也是一个视图模型，它只是向模型（聚合模型）请求数据。


**客户端/服务器应用程序中的事实来源是服务器**。这意味着您不应该仅仅因为添加了新视图而添加符合 ObservableObject（新事实来源）协议的视图模型。该视图的真相来源没有改变，它仍然是服务器。

单个StoreModel非常适合小型甚至中型应用程序。但对于较大的应用，最好根据应用程序的边界上下文引入多个聚合模型。在下一节中，我们将介绍多个聚合模型，以及它们在大型团队中工作时如何受益。

## 多个聚合模型

正如您在前面的部分所学到的，聚合模型的目的是将数据暴露给视图。正如Luca在[Data Essentials in SwiftUI WWDC 2020 (11:30)](https://developer.apple.com/videos/play/wwdc2020/10040/)中所解释的，“聚合模型是一种`ObservableObject`，它充当您数据依赖关系的表面。这使我们能够使用值类型对数据进行建模，并使用引用类型管理其生命周期和副作用。”

随着业务的增长，单个聚合模型可能不足以维护整个应用程序的生命周期和副作用。在这里，我们将介绍多个聚合模型。这些聚合模型基于应用程序的边界上下文。边界上下文是指系统中具有明确边界并旨在服务于特定业务目的的特定区域。

在电子商务应用程序中，我们可以有多个边界上下文，包括结帐流程、库存管理系统、目录、履行、装运、订购、营销和客户管理模块。

定义边界上下文在软件开发中非常重要，它有助于将应用程序分解为可管理的小部分。这也允许团队在系统的不同部分工作，而不会相互干扰。

开发人员通常不擅长为软件应用程序查找边界上下文。主要原因是他们的技术知识没有直接映射到领域知识。领域知识需要不同的技能，领域专家更适合这种角色。领域专家是一个人，他可能不精通技术，但了解业务或特定领域的运作方式。在大型项目中，您可能有多个领域专家，每个专家处理不同的业务领域。这就是为什么开发人员在开始任何开发之前与领域专家沟通并了解领域非常重要的原因。

确定与应用程序关联的不同边界上下文后，可以以聚合模型的形式表示它们。如下图所示。

![Multiple Aggregate Root](https://azamsharp.com/images/aggregate-model-updated.002.jpeg)

网络层也可以划分为多个 HTTP 客户端，或者您可以对整个应用程序使用单个通用网络层。如下图所示。

![Multiple Aggregate Root](https://azamsharp.com/images/aggregate-model-updated.003.jpeg)

目录聚合模型将负责提供与目录关联的所有实体的视图。这可能包括但不限于：

-   Product
-   Category
-   Brand
-   Review

排序聚合模型将负责提供所有排序相关实体的视图。这可能包括但不限于：

-   Order
-   OrderLineItem
-   OrderStatus
-   ShippingMethod
-   Discount

`目录`和`订购`聚合模型将是符合`ObservableObject`协议的引用类型。它们提供的所有实体都将是值类型。

`Catalog`聚合模型和`Product`实体的概要如下所示:

``` swift

struct Product: Codable {
    let productId: Int
    let name: String
    let category: Category
    let price: Double
    let description: String
    let reviews: [Review]?
}

@MainActor 
class Catalog: ObservableObject {
    
    // designated or generic HTTP client 
    let storeHTTPClient: StoreHTTPClient
    
    @Published var products: [Product]
    @Published var categories: [Category]
    
    init(storeHTTPClient: StoreHTTPClient) {
        self.storeHTTPClient = storeHTTPClient
    }
    
    func loadProducts() {
         products = storeHTTPClient.loadProducts
    }
    
    func getProductById(_ productId: Int) -> Product? {
        // fetch product by id 
    }
    
    func getProductsByCategory(_ categoryId: Int) -> [Product] {
       // get products by category
    }
    
    func getCategories() -> [Category] {
        categories = storeHTTPClient.loadCategories()
    }
}
```

目录和订单聚合模型作为环境对象注入到应用程序中。可以直接在应用程序根视图或应用程序每个部分的根视图中注入它们。后者如下所示：


``` swift
@main
struct StoreApp: App {
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(Catalog(client: CatalogHTTPClient()))
                .environmentObject(Ordering(client: OrderingHTTPClient()))
            
        }
    }
}
```

现在，在视图中，您可以通过访问`@EnvironmentObject`来使用Catalog或Ordering模型。具体实现如下所示：


``` swift
struct CatalogListScreen: View {
    
    @EnvironmentObject private var catalog: Catalog
    
    var body: some View {
        List(catalog.products) { product in
            Text(product.name)
        }.task {
            do {
                try await catalog.loadProducts()
            } catch {
                print(error.localizedDescription)
            }
        }
    }
}
```
如果您的视图需要访问订单信息，那么它也可以利用排序聚合模型。

``` swift
struct AdminDashboardScreen: View {
    
    @EnvironmentObject private var catalog: Catalog
    @EnvironmentObject private var ordering: Ordering
    
    var body: some View {
        VStack {
            List(catalog.products) { product in
                Text(product.name)
            }
            List(ordering.allOrders) { order in
                Text(order.status)
            }
        }.task {
            do {
                try await catalog.loadProducts()
                try await ordering.loadOrders()
            } catch {
                print(error.localizedDescription)
            }
        }
    }
}
```

在某些情况下，聚合模型需要访问另一个聚合模型中的信息。在这些情况下，聚合模型将仅使用网络服务来获取所需的信息。


> 重要的是，缓存层是从网络层内部调用的，而不是从聚合模型中调用的。这将允许聚合模型利用通过网络层的缓存，而不是自行实现它。通过从网络层内部访问缓存层，所有聚合模型都可以通过使用缓存资源从更快的响应中受益。

> 如前所述，对于小型甚至中型应用，可能只需要一个聚合模型。对于较大的应用，可以引入新的聚合模型。在创建应用程序边界之前，请确保咨询领域专家。

域边界的概念也可以应用于用户界面。这使我们能够在其他应用程序中重用用户界面元素。


![Factor out common pieces](https://azamsharp.com/images/user-interface.png)

:::tip 图片的原作者已授予在本文中使用它的许可。
:::

> 您可以使用 Swift 包管理器分解出常见的界面元素，并将这些包导入到其他应用程序中。

让我们继续缩小，看看我们的架构在所有部分都到位后的样子。

![Architecture](https://azamsharp.com/images/architecture-model-updated.jpeg)

:::tip 此图像已更新，并且已从图像的原始作者授予在本文中使用它的权限。
:::

如前所述，每个边界上下文都由其自己的模块表示。这些模块可以由文件夹或包依赖项表示。


**CatalogUI:** 表示与目录关联的用户界面。这可以包括所有特定于目录的内容，如AddCatalogScreen，UpdateCatalogScreen等。

**Catalog:** 表示与目录关联的模型。这将包含聚合模型和聚合模型公开的所有实体。

**MyStoreKit**: 表示用于执行网络调用的 HTTP 客户端。

**Foundation Core**: 表示所有模块使用的资源。这可以包括帮助程序类/结构、可重用视图、图像、图标，甚至用于测试的预览内容。

> 每个模块（如运输，库存，订购等）都可以由文件夹结构或包依赖项表示。这实际上取决于您的需求以及您是否希望在其他项目中重用您的模块。

使用此体系结构，可以在不干扰现有需求的情况下添加未来的业务需求和数据访问服务。这也允许更多的协作环境，因为不同的团队可以在不同的模块上工作而不会相互干扰。


## 视图的特定逻辑

在最后一部分中，我讨论了聚合模型如何充当单一事实来源并为视图提供所需的数据。但是视图特定的逻辑呢？应该将该逻辑放在哪里，以及我们必须使用哪些选项来对该逻辑进行测试。

在下面的代码中，我们希望根据最低和最高价格过滤产品。实现如下所示：

``` swift
struct ContentView: View {
    
    let httpClient: HTTPClientProtocol
    @State private var products: [Product] = []
    @State private var min: Double?
    @State private var max: Double?
    @State private var filteredProducts: [Product] = []
    
    private func filterProducts() {
        
        guard let min = min,
              let max = max else { return }
        
        filteredProducts = products.filter {
            $0.price >= min && $0.price <= max
        }
    }
    
    private var isFormValid: Bool {
        
        guard let min = min,
              let max = max else { return false }
        
        return min < max
    }
    
    var body: some View {
        VStack {
            HStack {
                TextField("Min", value: $min, format: .number)
                    .textFieldStyle(.roundedBorder)
                TextField("Max", value: $max, format: .number)
                    .textFieldStyle(.roundedBorder)
            }
            Text("Max must be larger than min.")
                .frame(maxWidth: .infinity, alignment: .leading)
                .font(.caption)
                .padding([.bottom], 20)
            
            Button("Apply") {
                filterProducts()
            }
            
            .disabled(!isFormValid)
          
            List(filteredProducts.isEmpty ? products: filteredProducts) { product in
                HStack {
                    Text(product.title)
                    Spacer()
                    Text(product.price, format: .currency(code: "USD"))
                }
            }
            .task {
                do {
                    products = try await httpClient.loadProducts()
                } catch {
                    print(error)
                }
        }
        }.padding()
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView(httpClient: HTTPClientStub())
    }
}
```

> 如果 filterProducts 或类似函数将涉及任何模型逻辑，那么您也可以将其放在聚合根模型中，而不是视图中。

请注意，我们没有调用真正的服务，而是使用返回预配置响应的 HTTPClient 的存根版本。另一个不错的选择是为每个响应创建单独的 JSON 文件，并在使用 Xcode 预览时从这些文件读取数据。我在我的一个YouTube视频中介绍了这一点，[使用JSON File构建SwiftUI Xcode Previews](https://youtu.be/EycwLxTU-EA)。

> 请记住，在上述方案中，如果在筛选过程中未找到结果，则返回原始产品数组。

我们在视图中有两段构成逻辑的代码， `isFormValid` 和 `filterProducts` 。如果我们想测试该代码，我们有很多方法。

使用 Xcode 预览！我知道这听起来并不花哨，但我鼓励您使用 Xcode 预览来测试基于视图的逻辑。Xcode 预览非常快（取决于您使用的机器），它给你的感觉与红色/绿色/重构循环相同。对于这种特殊情况，Xcode 预览将是我的首选。

> Xcode 预览并不是所有问题的答案。如果您正在处理复杂的视图逻辑，那么最好将所有逻辑移到一个单独的结构中，然后为该代码段编写单元测试。请记住，我们测试的重要方面之一是[获得对我们代码的信心](https://azamsharp.com/2023/02/15/testing-is-about-confidence.html)。

另一种选择是从视图中提取逻辑，然后针对它编写单元测试。这在下面的实现中显示：

``` swift
struct ProductFilterForm {
    
    var min: Double?
    var max: Double?
    
    func filterProducts(_ products: [Product]) -> [Product] {
        
        guard let min = min,
              let max = max else { return [] }
        
        return products.filter {
            $0.price >= min && $0.price <= max
        }
    }
}
```

`ProductFilterForm` 现在可以单独进行单元测试。单元测试如下所示：

``` swift

func test_user_can_filter_products_by_price() throws {
        
        self.continueAfterFailure = false
      
        let products = [
            Product(id: 1, title: "Product 1", price: 10),
            Product(id: 2, title: "Product 2", price: 100),
            Product(id: 3, title: "Product 3", price: 200),
            Product(id: 4, title: "Product 4", price: 500)
        ]
        
        let expectedFilteredProducts = [
            Product(id: 2, title: "Product 2", price: 100),
            Product(id: 3, title: "Product 3", price: 200),
            Product(id: 4, title: "Product 4", price: 500)
        ]
        
        let productFilterForm = ProductFilterForm(min: 100, max: 500)
        let filteredProducts = productFilterForm.filterProducts(products)
        
        for expectedProduct in expectedFilteredProducts {
            
            let product = filteredProducts.first { $0.id == expectedProduct.id }
            
            XCTAssertNotNil(product)
            XCTAssertEqual(product!.title, expectedProduct.title)
            XCTAssertEqual(product!.price, expectedProduct.price)
        }
        
    }

```

> 如上所示，单元测试视图的逻辑隔离对于复杂的用户界面可能很有用。请记住，仅仅因为单元测试通过，并不意味着用户界面按预期工作。

你可以写的最终一种测试是端到端测试。E2E 测试很棒，因为它们从用户的角度测试应用程序，并且最适合防止回归。缺点是 E2E 测试比运行单元测试慢。它们较慢的主要原因是因为它们正在测试完整的应用程序而不是小单元。软件中的大多数问题之所以存在，是因为应用程序是在单元级别而不是系统级别进行测试的。我鼓励你花一些时间编写有意义的E2E测试。

下面是上述方案的 E2E 测试的实现。

``` swift
  func test_user_can_filter_products_based_on_price() {
        
        let app = XCUIApplication()
        app.launchEnvironment = ["ENV": "TEST"]
        app.launch()
        
        app.textFields["minTextField"].tap()
        app.textFields["minTextField"].typeText("100")
        
        app.textFields["maxTextField"].tap()
        app.textFields["maxTextField"].typeText("500")
        
        app.buttons["applyButton"].tap()
        
        // assert that the count is correct
        XCTAssertEqual(3, app.collectionViews["productList"].cells.count)
        // assert that the items are correct
        XCTAssertEqual("Product 2", app.collectionViews["productList"].staticTexts["Product 2"].label)
        XCTAssertEqual("Product 3", app.collectionViews["productList"].staticTexts["Product 3"].label)
        XCTAssertEqual("Product 4", app.collectionViews["productList"].staticTexts["Product 4"].label)
    }
```

最后，您将不得不决定在[测试金字塔](https://martinfowler.com/articles/practical-test-pyramid.html)中要投入时间以获得最佳投资回报。

> 如果你想了解更多关于测试的信息，那么你可以看看我的课程 [使用 Swift 在 iOS 中测试驱动开发](https://www.udemy.com/course/test-driven-development-in-ios-using-swift/?referralCode=07649C41E6E184CE86B3) 。

## 屏幕与视图 

当我使用 Flutter 时，我观察到一种组织小部件的常见模式。Flutter 开发人员根据小部件是否仅代表可重用控件的整个屏幕来分离小部件。由于 React、Flutter 和 SwiftUI 在本质上非常相似，因此我们可以在构建 SwiftUI 应用程序时应用相同的原则。

例如，在显示影片的详细信息时，可以将其称为 MovieDetailScreen，而不是将该视图称为 MovieDetailView。这将清楚地表明详细信息视图是实际屏幕，而不是一些可重用的子视图。下面再举几个例子。


**Screens**

-   MovieDetailScreen
-   HomeScreen
-   LoginScreen
-   RegisterScreen
-   SettingsScreen

**Views**

-   RatingsView
-   MessageView
-   ReminderListView
-   ReminderCellView

我发现密切关注我们友好的邻居 React 和 Flutter 总是一个好主意。你永远不知道你会从其他声明式框架中带来什么想法到 SwiftUI 中。


## 验证

软件开发中有一句名言，垃圾进，垃圾出。这意味着，如果您允许用户通过用户界面输入不正确的信息（垃圾），那么垃圾最终将进入您的数据库。通常，当这种情况发生时，清理数据库变得非常困难和耗时。

您必须采取必要的步骤来防止用户首先提交不正确的信息。

考虑一个简单的 `LoginScreen` 视图，其中包含用户名和密码文本字段。如果我们想仅在视图正确验证时才启用登录按钮，我们可以使用以下实现：

``` swift
struct LoginScreen: View {
    
    @State private var username: String = ""
    @State private var password: String = ""
    
    private var isFormValid: Bool {
        !username.isEmptyOrWhiteSpace && !password.isEmptyOrWhiteSpace
    }
    
    var body: some View {
        Form {
            TextField("Username", text: $username)
            TextField("Password", text: $password)
            Button("Login") {
                
            }.disabled(!isFormValid)
        }
    }
}
```

对于此类简单的逻辑，您可以使用 Xcode 预览快速执行手动测试并验证结果。

如果您正在处理更复杂的表单，则建议将其提取到自己的结构中。此概念显示在下面的实现中。

``` swift
struct LoginFormConfig {
    
    var username: String = ""
    var password: String = ""
    
    var isFormValid: Bool {
        !username.isEmptyOrWhiteSpace && !password.isEmptyOrWhiteSpace
    }
}

struct LoginScreen: View {
    
    @State private var loginFormConfig: LoginFormConfig = LoginFormConfig()
    
    var body: some View {
        Form {
            TextField("Username", text: $loginFormConfig.username)
            TextField("Password", text: $loginFormConfig.password)
            Button("Login") {
                
            }.disabled(!loginFormConfig.isFormValid)
        }
    }
}
```

`LoginFormConfig` 封装表单验证。这也允许我们针对 LoginFormConfig 编写单元测试。下面显示了几个单元测试：

``` swift
final class LearnTests: XCTestCase {

    func test_login_form_validates_successfully() {
        
        let expectedOutputs: [[String: Any]] = [
            ["username": "johndoe", "password": "password", "isFormValid": true],
            ["username": "", "password": "password", "isFormValid": false],
            ["username": "johndoe", "password": " ", "isFormValid": false],
            ["username": "", "password": " ", "isFormValid": false],
            ["username": "   ", "password": "password", "isFormValid": false],
            ["username": " johndoe", "password": " password", "isFormValid": true]
        ]
        
        for expectedOutput in expectedOutputs {
            let username = expectedOutput["username"] as! String
            let password = expectedOutput["password"] as! String
            let isFormValid = expectedOutput["isFormValid"] as! Bool
            
            let loginFormConfig = LoginFormConfig(username: username, password: password)
            XCTAssertEqual(loginFormConfig.isFormValid, isFormValid)
        }
    }
}
```

最后，将表单验证提取到单独的结构中并为其编写单元测试取决于您的置信度。简单的表单可以通过 Xcode 预览轻松测试，不需要额外的结构甚至单元测试。

> 验证帮助程序函数，如isEmptyOrWhiteSpace，isNumeric，isEmail，isLessThan可以移动到单独的Swift包中。这将促进可重用性，其他项目也可以从使用它中受益。

我在之前的一篇文章中介绍了几种处理和显示验证错误的不同方法，您可以在[此处阅读](https://azamsharp.com/2022/08/09/intro-to-mv-state-pattern.html)。


## 显示错误

显示错误是任何应用程序不可或缺的一部分。

在 SwiftUI 中，我们可以将显示错误集中到一个位置。这将防止我们编写重复的代码，并在代码库中提供一个点来更改布局和外观。

我们可以从创建一个 ErrorWrapper 开始，它将负责包装实际错误，并为用户提供后续步骤的指导。

``` swift
struct ErrorWrapper: Identifiable {
    let id = UUID()
    let error: Error
    let guidance: String
}
```

ErrorWrapper 将由 ErrorView 使用。ErrorView将负责以可视格式显示错误的详细信息。您可以在下面找到错误视图的基本实现。

``` swift
struct ErrorView: View {
    
    let errorWrapper: ErrorWrapper
    
    var body: some View {
        VStack {
            Text("Error has occured in the application.")
                .font(.headline)
                .padding([.bottom], 10)
            Text(errorWrapper.error.localizedDescription)
            Text(errorWrapper.guidance)
                .font(.caption)
        }.padding()
    }
}
```

> ErrorView只是一个视图，您可以根据需要对其进行自定义。

为了从应用程序的任何部分设置错误包装器，我们将添加一个 ErrorState 作为 ObservableObject 并将其注入到环境对象中。

``` swift
class ErrorState: ObservableObject {
    @Published var errorWrapper: ErrorWrapper?
}

@main
struct StoreApp: App {
    
    @StateObject private var errorState = ErrorState()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(errorState)
                .sheet(item: $errorState.errorWrapper) { errorWrapper in
                    ErrorView(errorWrapper: errorWrapper)
                }
        }
    }
}

```

每当错误状态更改时，将显示一个包含最新错误的工作表。同样，您可以自由使用不同的方法来显示错误，而不是工作表。

此技术允许您在代码库中有一个点，该点负责显示错误。

## 分组视图与事件

在 SwiftUI 中创建可重用视图的一种方法是将事件委托给父视图。这允许视图在不同的方案中使用，而无需将它们绑定到特定逻辑。实现此目的的一种方法是使用闭包。

考虑一个 `ReminderCellView` ，它允许用户执行检查/取消选中和删除操作。实现如下所示：

``` swift
struct ReminderCellView: View {
    
    let index: Int
    let onChecked: (Int) -> Void
    let onDelete: (Int) -> Void

    var body: some View {
        HStack {
            Image(systemName: "square")
                .onTapGesture {
                    onChecked(index)
                }
            Text("ReminderCellView \(index)")
            Spacer()
            Image(systemName: "trash")
                .onTapGesture {
                    onDelete(index)
                }
        }
    }
}
```

`ReminderCellView` 公开了 `onChecked` 和 `onDelete` 闭包。调用者可以使用这些闭包来执行特定任务。调用方如下:


``` swift
struct ContentView: View {

    var body: some View {
        List(1...20, id: \.self) { index in
            ReminderCellView(index: index, onChecked: { index in
                // do something
            }, onDelete: { index in
                // do something
            })
        }
    }
}
```

随着 `ReminderCellView` 的复杂性增加，它会暴露更多的事件，那么调用方将变得更加复杂。

我们可以将所有事件分组到一个简单的枚举中来解决此问题。如下所示：

``` swift
enum ReminderCellEvents {
    case onChecked(Int)
    case onDelete(Int)
}
```

可以使用`ReminderCellEvents`来对`ReminderCellView`进行操作和更新，如下：

``` swift
struct ReminderCellView: View {
    
    let index: Int
    let onEvent: (ReminderCellEvents) -> Void
    
    var body: some View {
        HStack {
            Image(systemName: "square")
                .onTapGesture {
                    onEvent(.onChecked(index))
                }
            Text("ReminderCellView \(index)")
            Spacer()
            Image(systemName: "trash")
                .onTapGesture {
                    onEvent(.onDelete(index))
                }
        }
    }
}
```

现在，我们不再处理多个闭包，而是只处理单个基于枚举的事件结构。调用站点看起来也干净得多。

``` swift
struct ContentView: View {

    var body: some View {
        List(1...20, id: \.self) { index in
            ReminderCellView(index: index) { event in
                switch event {
                    case .onChecked(let index):
                        print(index)
                    case .onDelete(let index):
                        print(index)
                }
            }
        }
    }
}
```

最后，您必须决定何时要将事件分组到枚举中，以及何时要单独使用它们（多个闭包）。如果我的视图公开了两个以上的闭包，我倾向于使用枚举事件。


## 路由

SwiftUI 在 iOS 16 中引入了 NavigationStack，它允许开发人员为其应用程序配置全局路由。

在 SwiftUI 中，有几种不同的方法可以处理路由。一种可能的方法是处理应用程序根视图中的所有路由。我们将从创建路由枚举开始，它将为整个应用程序设置路由。实现如下所示：

``` swift

enum Routes: Hashable {
    case catalog(CatalogRoutes)
    case inventory(InventoryRoutes)
    
    enum CatalogRoutes: Hashable {
        case home
        case detail(Category)
                
    }
    
    enum InventoryRoutes: Hashable {
        case home
        case detail(Product)
    }
}
```

`Routes` 枚举表示屏幕每个部分的根路由。这包括`catalog`, `inventory`, `shipping` 等。每个部分的路线在其相应的路线枚举（`CatalogRoutes`、`InventoryRoutes`）中声明。

对于编程路由，我们添加了 `NavigationState`。 NavigationState 跟踪所有路线，并将通过 `@EnvironmentObject` 注入到应用程序中

``` swift
class NavigationState: ObservableObject {
    @Published var routes: [Routes] = []
}
```

最后，我们在应用程序的根视图中处理所有路由。如下所示：

``` swift
@main
struct LearnApp: App {
    
    @StateObject private var navigationState = NavigationState()
    
    var body: some Scene {
        WindowGroup {
            NavigationStack(path: $navigationState.routes) {
                ContentView()
                    .navigationDestination(for: Routes.self) { route in
                        switch route {
                            case .catalog(let catalogRoutes):
                                switch catalogRoutes {
                                    case .home:
                                        Text("Home View")
                                    case .detail(let category):
                                        // show category detail view
                                        Text(category.name)
                                }
                            case .inventory(let inventoryRoutes):
                                switch inventoryRoutes {
                                    case .home:
                                        // show home view
                                        Text("Home View")
                                    case .detail(let product):
                                        // show product details View
                                        Text(product.name)
                                }
                        }
                    }
            }.environmentObject(navigationState)
        }
    }
}
```

上述方法可行，但很快就会变得混乱，因为我们将应用程序的所有路由都放在一个地方。控制这个问题的一种方法是为屏幕的每个部分创建单独的路由器，并允许路由器处理特定的路由行为。`CatalogRouter`的实现如下所示。CatalogRouter负责处理与目录屏幕相关的所有路由。

``` swift
struct CatalogRouter {
    
    let routes: CatalogRoutes

    @ViewBuilder
    func configure() -> some View {
        switch routes {
            case .detail(let category):
                return Text(category.name)
        }
    }
}
```

同样，我们可以为清单添加路由器。

``` swift
struct InventoryRouter {
    let routes: InventoryRoutes
    
    // view builder
    @ViewBuilder
    func configure() -> some View {
        switch routes {
            case .detail(let product):
                Text(product.name)
            case .home:
                Text("Home")
        }
    }
}
```

现在，我们的导航目的地看起来更干净。

``` swift
@main
struct LearnApp: App {
    
    @StateObject private var navigationState = NavigationState()
    
    var body: some Scene {
        WindowGroup {
            NavigationStack(path: $navigationState.routes) {
                ContentView()
                    .navigationDestination(for: Routes.self) { route in
                        switch route {
                            case .catalog(let routes):
                                CatalogRouter(routes: routes).configure()
                            case .inventory(let routes):
                                InventoryRouter(routes: routes).configure()
                        }
                    }
            }.environmentObject(navigationState)
        }
    }
}
```

如果需要调用某个路由，可以在 `NavigationState` 中附加该路由。这在下面的实现中显示：

``` swift
struct ContentView: View {
    
    @EnvironmentObject private var navigationState: NavigationState
    
    var body: some View {
       
        VStack {
            
            Button("Go to Catalog") {
                navigationState.routes.append(.catalog(.detail(Category(name: "Category 1"))))
            }
            
            Button("Go to Inventory") {
                navigationState.routes.append(.inventory(.detail(Product(name: "Product 1"))))
            }
           
        } .padding()
    }
}
```

> 我还写了一本关于 SwiftUI 导航 API 的书。如果您有兴趣，可以免费下载 [从这里开始](https://azamsharp.com/books) .

## 测试

> 本文的这一部分摘自我的文章 [务实的测试和避免常见的陷阱](https://azamsharp.com/2012/12/23/pragmatic-unit-testing.html)

编写测试的主要目的是确保软件按预期工作。测试还让您确信，在一个模块中所做的更改不会破坏相同模块或其他模块中的内容。

并非所有应用程序都需要编写测试。如果要构建具有直接域的基本应用程序，则可以使用手动测试来测试完整的应用程序。话虽如此，在大多数专业环境中，您正在使用具有业务规则的复杂域。这些业务规则构成了公司运营和产生收入的基础。

在本文中，我将讨论编写测试的不同技术，以及开发人员如何编写好的测试以获得最大的投资回报(ROI)。

## 并非所有测试都等于

考虑您正在为银行编写应用程序的情况。业务规则之一是在资金不足的情况下收取透支费。[银行仅通过收费就创造了数十亿美元的收入](https://www.depositaccounts.com/blog/banks-income-fees.html)。作为开发人员，您必须编写高质量的测试，以确保透支费用计算按预期工作。

在同一银行应用程序中，您可能具有诸如呈现电子邮件模板或记录某些交互等功能。这些功能很重要，但与收取透支费相比，可能不会产生相同的投资回报。这意味着如果电子邮件模板格式不正确，那么银行就不会损失数百万美元，并且您将不会在半夜接到电话。如果日志记录是为开发人员准备的，那么在大多数情况下，您甚至不需要为其编写测试。这只是一个实现细节。

> > 如果要构建日志记录框架，则必须彻底测试框架公开的公共 API。

下次编写测试时，请问问自己此功能对业务有多重要。如果它是业务不可或缺的一部分，请确保对其进行彻底测试并获得高代码覆盖率。

## 测试行为而不是实现 

开发人员犯的最大错误之一是专注于针对实现细节而不是应用程序的行为编写测试。

> > 添加新测试的触发器是要求，而不是类或函数。

仅仅因为您添加了新类或函数并不意味着您将开始编写测试。这只是一个可以随时间变化的实现细节。测试应针对业务需求，而不是实现细节。

下面是从业务需求派生的几个行为示例：

1.  当客户提取金额且资金不足时，则收取透支费。
2.  一旦达到限制，客户指定的股票数量将以指定价格提交交易。

该行为源于项目的要求。检查实现细节而不是行为的测试往往非常脆弱，并且在实现更改时很容易中断，即使行为保持不变。

让我们考虑一个方案，其中您正在构建一个应用程序以在屏幕上显示产品列表。这些产品是从JSON API获取的，并使用SwiftUI框架呈现，遵循MVVM设计模式的原则。

首先，我们将研究大多数开发人员采用的测试上述场景的常用方法，然后我们将以更务实的方式实现测试。

完整的应用可能类似于下面的实现：

``` swift
class Webservice {
    
    func fetchProducts() async throws -> [Product] {
        // ignore the hard-coded URL. We can inject the URL from using test configuration. 
        let url = URL(string: "https://test.store.com/api/v1/products")!
        let (data, _) = try await URLSession.shared.data(from: url)
        return try JSONDecoder().decode([Product].self, from: data)
    }
    
}

class ProductListViewModel: ObservableObject {
    
    @Published var products: [ProductViewModel] = []
    
    func populateProducts() async {
        do {
            let products = try await Webservice().fetchProducts()
            self.products = products.map(ProductViewModel.init)
        } catch {
            print(error)
        }
    }
    
}

struct ProductViewModel: Identifiable {
    
    private let product: Product
    
    init(product: Product) {
        self.product = product
    }
    
    var id: Int {
        product.id
    }
    
    var title: String {
        product.title
    }
}


struct ProductListScreen: View {
    
    @StateObject private var vm = ProductListViewModel()
    
    var body: some View {
        List(vm.products) { product in
            Text(product.title)
        }.task {
            await vm.populateProducts()
        }
    }
}
```

上述应用程序按预期工作并产生预期的结果。而不是测试 `Webservice` 的具体实现，我们将引入一个接口/合约/协议，以便我们可以注入一个模拟。创建协议的唯一目的是满足测试，即使只有一个符合该协议/接口的具体实现。

> > 这称为 [测试引起的损伤](https://dhh.dk/2014/test-induced-design-damage.html) .测试规定我们应该添加依赖项，以便您可以模拟服务。引入协议/合约/接口的唯一目的是让你最终可以模拟它。请记住，在应用程序中使用协议/协定没有错。它们确实有一个非常重要的目的，即向用户隐藏实现细节并提供抽象，但只是添加合约以满足测试目标，这不是一个好的做法，因为它使实现复杂化，并且您的测试远离测试应用程序的实际行为。

在下面的代码中，我们引入了一个WebserviceProtocol。Web服务和新创建的模拟Web服务都符合Webservice协议，如下所示：

``` swift
protocol WebserviceProtocol {
    func fetchProducts() async throws -> [Product]
}

class Webservice: WebserviceProtocol {
    
    func fetchProducts() async throws -> [Product] {
        
        let url = URL(string: "https://test.store.com/api/v1/products")!
        let (data, _) = try await URLSession.shared.data(from: url)
        return try JSONDecoder().decode([Product].self, from: data)
    }
}

class MockedWebService: WebserviceProtocol {
    func fetchProducts() async throws -> [Product] {
        return [Product(id: 1, title: "Product 1"), Product(id: 2, title: "Product 2")]
    }
}
```

> > 您可能应该使用更好的名称，而不是将其称为WebserviceProtocol。我称之为WebserviceProtocol的主要原因只是为了简单和方便。

Web 服务现在作为依赖项注入到我们的 ProductListViewModel。如下所示：

``` swift
class ProductListViewModel: ObservableObject {
    
    private let webservice: WebserviceProtocol
    @Published var products: [ProductViewModel] = []
    
    init(webservice: WebserviceProtocol) {
        self.webservice = webservice
    }
    
    func populateProducts() async {
        do {
            let products = try await Webservice().fetchProducts()
            self.products = products.map(ProductViewModel.init)
        } catch {
            print(error)
        }
    }
    
}
```

`ProductListScreen`视图也会更新以反映更改。

``` swift
struct ProductListScreen: View {
    
    @StateObject private var vm = ProductListViewModel(webservice: WebserviceFactory.create())
    
    var body: some View {
        List(vm.products) { product in
            Text(product.title)
        }.task {
            await vm.populateProducts()
        }
    }
}
```

> > WebserviceFactory 负责返回 Web 服务或 MockedWebservice，具体取决于应用程序环境。
> 
现在，让我们继续检查测试。

``` swift
final class ProductsTests: XCTestCase {
    
    func test_populate_products() async throws {
        
        let mockedWebService = MockedWebService()
        let productListVM = ProductListViewModel(webservice: mockedWebService)
        
        await productListVM.populateProducts()
        
        // This line is verifying the implementation detail.
        // Implementation details can change
        // fetchProducts can change to getProducts and the test will fail. 
        verify(mockedWebService.fetchProducts()).wasCalled()
        
        XCTAssertEqual(2, productListVM.products.count)
    }
}
```

我们在测试中创建了`MockedWebservice`的一个实例，并将其传递给`ProductListViewModel`。接下来，我们在视图模型上调用`populateProducts`函数，然后检查确保`mockedWebservice`实例上的`fetchProducts`被调用。最后，测试检查`ProductListViewModel`实例的`products`属性以确保其正确填充。

上述测试的问题在于它不是测试行为，而是测试实现。以下代码行是实现详细信息。

``` swift
verify(mockedWebService.fetchProducts()).wasCalled()
```

这意味着，如果您决定重构代码并将`fetchProducts`函数重命名为`getProducts`，则您的测试将失败。这些类型的测试通常被称为脆弱测试，因为当内部实现发生变化时，它们会失败，即使API提供的功能/行为仍然相同。这也是您的测试应该验证行为而不是实现的主要原因。

> 你编写的代码是一种责任，包括测试。编写测试时，请关注测试的质量而不是数量。请记住，您不仅负责编写测试，还负责维护它们。


> 如果使用的是 MVVM 模式，则 VM 可能具有逻辑。针对视图模型中包含的逻辑编写单元测试是完全可以的。

## 端到端测试

在上一节中，您了解到，在大多数情况下，模拟并不能提供投资回报。使用模拟编写的测试通常最终会太脆弱，并且可能会因为重构而失败，即使行为保持不变，也会破坏所有依赖的测试。

在编写测试时，人类心理学也起着重要作用。作为软件开发人员，我们希望快速反馈少量的多巴胺。接收快速反馈并没有错。快速反馈是单元测试的重要特征之一。不幸的是，有时我们走得太快，以至于没有意识到我们走错了路。我们开始表现得像一个测试成瘾者，他希望立即在测试旁边看到绿色复选标记。

如前所述，添加测试实现细节而不是行为的测试不会为您的项目带来任何好处。从长远来看，它甚至可能对您不利，因为现在您将负责维护这些测试用例，并且每当实现细节发生变化时，即使功能保持不变，您的所有测试也会中断。

> 我并不是建议你不应该编写单元测试。单元测试在测试小型代码单元时非常有用。我建议你必须确保你测试的是代码的行为，而不是实现细节。这意味着，如果要为视图模型编写单元测试，则可以。

除了单元测试和集成测试之外，端到端测试最适合防止回归。一个好的端到端将测试一个完整的故事/行为。您可以在下面找到端到端测试的实现。

``` swift
final class ProductTests: XCTestCase {
    
    private var webservice: Webservice!
    // products
    let products = [Product(id: 1, title: "Handmade Fresh Table"),Product(id: 2, title: "Durable Water Bottle")]
    
    override func setUp() {
        // make sure the Webservice is using the TEST server endpoints and not PRODUCTION
        webservice = Webservice()
        
        // add few products // seeding the database
        for product in products {
            await webservice.addProduct(product: product)
        }
    }
    
    func test_display_list_of_all_products() async {
        
        let app = XCUIApplication()
        app.launch()
        
        let productList = app.tables["productList"]
        
        // check if the item numbers is correct
        XCTAssertEqual(productList.tables.cells.count, 2)
        
        // check if the correct items are displayed
        for(index, product) in products.enumerated() {
            let cell = productList.cells.element(boundBy: index)
            XCTAssertEqual(cell.staticTexts["productTitle"].label, product.title)
        }
        
    }
    
    override func tearDown() async throws {
        // make sure to delete ALL records from the database so future test results are not influenced
        await webservice.deleteProductById(productId: 1)
        await webservice.deleteProductById(productId: 2)
    }
    
}
```

> > 开发人员可以在其开发计算机上本地运行 E2E 测试。这将需要初始设置，例如测试框架、测试环境、依赖项（数据库、服务）。E2E 测试可能非常耗时，因此开发人员可能会选择运行 E2E 测试的频率低于单元测试或其他类型的测试。

E2E 测试比本节前面讨论的前面的测试慢，但它们较慢的主要原因是因为它们测试应用程序的所有层。E2E 测试是完整的测试，针对应用的特定行为。

端到端测试还需要一些初始设置，以允许测试运行数据库迁移、插入种子数据、模拟用户界面事件，然后在测试完成后回滚更改。

端到端测试不能替代域模型测试。您必须针对域模型编写测试，特别是如果您的应用是域密集型的并且包含大量业务规则。

> > 您必须在运行端到端测试的频率上找到适当的平衡。如果在每次签入代码时运行它，则持续集成服务器将始终在 100% 的时间内运行。如果每隔几天运行一次，则故障通知将比预期晚得多。请记住，您可以在计算机上本地运行 E2E 测试。获得所需结果后，CI 服务器可以在代码签入过程中运行所有测试。

## 集成测试

执行集成测试以确保两个不同的系统可以协同工作。这些系统可以是外部依赖项，如数据库或API，但也可以是同一系统中的不同模块。

依赖项可分为托管依赖项和非托管依赖项。托管依赖项包括数据库、文件系统等。对于托管依赖项，使用真实实例而不是模拟实例非常重要。非托管依赖项包括SMTP服务器，支付网关等。对于非托管依赖项，请使用模拟来验证其行为。

让我们查看用于用户登录操作的网络服务的示例集成测试。

``` swift
// This test is generated by ChatGPT AI 
import XCTest

class IntegrationTests: XCTestCase {
    func testLogin() {
        // Set up the URL for the login endpoint
        let url = URL(string: "https://api.example.com/login")!

        // Create a URL request
        var request = URLRequest(url: url)
        request.httpMethod = "POST"
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")

        // Set the body of the request to a JSON object with the login credentials
        let body = ["username": "user123", "password": "password"]
        request.httpBody = try! JSONSerialization.data(withJSONObject: body)

        // Create a URLSession and send the request
        let session = URLSession.shared
        let task = session.dataTask(with: request) { data, response, error in
            // Make sure there is no error
            XCTAssertNil(error)

            // Check the response status code
            let httpResponse = response as! HTTPURLResponse
            XCTAssertEqual(httpResponse.statusCode, 200)

            // Check the response data
            XCTAssertNotNil(data)
            let responseBody = try! JSONSerialization.jsonObject(with: data!, options: []) as! [String: Any]
            XCTAssertEqual(responseBody["status"], "success")
        }
        task.resume()
    }
}

```

上述集成测试可确保 HTTP 客户端层按预期工作。集成是在网络客户端和服务器之间进行的。客户端确保响应正确且有效，以便成功执行登录操作。

非托管依赖项（如支付网关，SMTP客户端等）可以在集成测试期间模拟出来。对于托管依赖项，请使用具体的实现。

## 代码覆盖率

代码覆盖率是一个指标，用于计算测试中覆盖了多少代码。让我们举一个非常简单的例子。在下面的代码中，我们有一个 `BankAccount` 类，它由 `deposit` 和 `withdraw` 函数组成。

> > 请记住，在实际场景中，银行帐户不是作为计算器实现的。银行帐户记录在分类帐中，其中保留了所有财务交易。

``` swift
class BankAccount {
    
    private(set) var balance: Double
    
    init(balance: Double) {
        self.balance = balance
    }
    
    func deposit(_ amount: Double) {
        self.balance += amount
    }
    
    func withdraw(_ amount: Double) {
        self.balance -= amount
    }
    
}
```

银行账户的一个可能的测试是检查账户是否成功存款。

``` swift
final class BankAccountTests: XCTestCase {
    
    func test_deposit_amount() {
        
        let bankAccount = BankAccount(balance: 0)
        bankAccount.deposit(100)
        XCTAssertEqual(100, bankAccount.balance)
        
    }
}
```

如果这是我们测试套件中唯一的测试，那么我们的代码覆盖率不是 100%。这意味着并非所有路径/函数都在测试中。这是真的，因为我们从未实现过 `withraw` 函数的测试。

您可能想知道是否始终拥有 100% 的代码覆盖率。简单的答案是否定的。但这也取决于您正在处理的应用程序。如果你正在为NASA编写代码，它将负责火星上的着陆漫游车，那么你最好确保每一行都经过测试，并且你的代码覆盖率是100%。

如果您正在为有助于调节心跳的起搏器设备实现应用程序，那么您最好确保您的代码覆盖率为 100%。一行丢失和未经测试的代码可能会导致某人的生活......按照字面。

那么，理想的代码覆盖率是多少。这实际上取决于应用程序，但任何高于 70% 的数字都被认为是一个不错的代码覆盖率。

> > 在计算代码覆盖率时，请确保忽略第三方库/框架，因为它们的代码覆盖率不是您的责任。

## 单元测试、数据访问和文件访问 

与我交谈过的大多数开发人员都认为单元测试无法访问数据库或文件系统。**这是不正确的，而且是完全错误的**。单元测试可以访问数据库或文件系统。

了解 **单元测试是隔离，而不是被测试的东西** 非常重要。这一点非常重要，我将再次重复一遍。

> > Unit test is the isolation, not the thing under test

在单元测试期间不访问数据库或文件系统的正当原因之一是，测试可能会留下数据，这可能会导致其他测试以意外的方式运行。解决方案是确保在每次测试完成后始终将数据库恢复到初始状态，以便将来的测试获得一个干净的数据库，而没有任何副作用。

某些框架还允许您构造内存数据库。例如，Core Data 默认使用 SQLite，但可以将其配置为使用内存数据库，如下所示：

``` swift
storeDescription.type = NSInMemoryStoreType
```

内存数据库具有多种优势，包括：

-   No removal of test data
-   Run faster
-   Can be initialized before each test run

即使认为这些好处看起来很吸引人，我个人也不建议使用内存数据库进行测试。主要原因是内存中数据库并不代表实际的生产环境。这意味着您在测试期间可能不会遇到相同的问题，在使用实际数据库时可能会遇到这些问题。

> > 确保测试环境和生产环境在本质上几乎相同始终是一个好主意。

## 测试视图模型不验证用户界面 

几周前，我与另一位开发人员进行了讨论，他提到他们通过 SwiftUI 中的视图模型测试他们的用户界面。我不确定他的意思，所以我检查了源代码，发现他们对他们的视图模型有很多单元测试，他们只是假设如果视图模型测试通过，那么用户界面将自动工作。

> 请记住，我并不是建议你不应该为视图模型编写单元测试。我只是说您的视图模型单元测试不会验证用户界面是否按预期工作。

让我们举一个非常简单的构建计数器应用程序的示例。

``` swift
class CounterViewModel: ObservableObject {
    
    @Published var count: Int = 0
    
    func increment() {
        count += 1
    }
}

struct ContentView: View {
    
    @StateObject private var counterVM = CounterViewModel()
    
    var body: some View {
        VStack {
            Text("\(counterVM.count)")
            Button("Increment") {
                counterVM.increment()
            }
        }
    }
}
```

按下递增按钮时，我们在 CounterViewModel 实例上调用递增函数并递增计数。由于 count 属性是用@Published属性包装器修饰的，因此它会通知视图重新计算并最终重新呈现。

为了测试计数是否递增并显示在屏幕上，编写了以下单元测试。

``` swift
import XCTest
@testable import Learn

final class LearnTests: XCTestCase {

    func test_user_updated_count() {
        let vm = CounterViewModel()
        vm.increment()
        XCTAssertEqual(1, vm.count)
    }

}
```

这是一个完全有效的单元测试，但它不会验证计数是否已更新并显示在屏幕上。让我再重复一遍。**视图模型单元测试不会验证计数是否成功显示在屏幕上。这是一个单元测试，而不是 UI 测试。**
 
若要证明视图模型单元测试不验证用户界面元素，只需从 ContentView 中删除按钮视图甚至文本视图。单元测试仍将通过。这可能会让您错误地相信您的界面正在工作。

验证用户界面是否按预期工作的更好方法是实现 UI 测试。看看下面的实现。

``` swift
final class LearnUITests: XCTestCase {

    func testExample() throws {
        // UI tests must launch the application that they test.
        let app = XCUIApplication()
        app.launch()

        app.buttons["incrementButton"].tap()
        XCTAssertEqual("1", app.staticTexts["countLabel"].label)
    }
}
```

此测试将在模拟器中启动应用，并验证按下按钮时标签是否正确更新。

> 根据要测试的行为的复杂性，您甚至可能不需要编写用户界面测试。我发现大多数用户界面都可以使用 Xcode 预览快速测试。

那么什么是正确的平衡呢？与 UI 测试相比，视图模型应有多少单元测试。

答案是**视情况而定**。如果您的视图模型中有复杂的逻辑，那么单元测试会有所帮助。UITest （E2E） 测试提供了针对回归的最佳防御。对于每个故事，您可以编写几个长快乐路径用户界面测试和几个边缘情况。再一次，这真的取决于故事和与故事相关的复杂性。

最后，[测试关乎信心](https://azamsharp.com/2023/02/15/testing-is-about-confidence.html)。有时您可以通过编写较少或不编写测试来获得信心，而其他时候您必须编写更多测试才能达到置信度。

## 理想测试 

我们讨论了几种不同类型的测试。您可能想知道编写哪种测试是最好的。什么是理想的测试？

不幸的是，没有理想的测试。这完全取决于您的项目和要求。如果您的项目是域繁重的，那么您应该进行更多的域级别测试。如果你的项目是UI繁重的，那么你应该有端到端的测试。最后，如果您的项目与托管和非托管依赖项集成，则集成测试将更适合这些方案。

请记住测试模块公开的公共 API，而不是实现细节。通过这种方式，您可以编写有用的质量测试，这也将帮助您捕获错误。

不要创建协议/接口/合约，其目的只是为了嘲笑。如果协议由单个具体实现组成，则使用具体实现并删除接口/合约。体系结构应基于当前的业务需求，而不是基于可能永远不会发生的假设方案。记住YAGNI（你不需要它）。代码少比代码多好。

## 总结

应用程序架构是一个复杂的主题，最终项目的最佳架构取决于许多因素。这些因素可能包括项目的规模和复杂性、团队的技能和经验、项目的目标和要求。

最终，成功的应用程序架构的关键是选择适合项目独特需求的模式，并随着项目的发展不断评估和调整架构。

通过投入时间和资源来设计周到且有效的应用程序架构，团队可以确保其代码库具有可维护性、可扩展性和灵活性，足以适应不断变化的需求和技术趋势。

## 原文链接

* [Building Large-Scale Apps with SwiftUI: A Guide to Modular Architecture](https://azamsharp.com/2023/02/28/building-large-scale-apps-swiftui.html)