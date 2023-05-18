---
layout: post
author: Robin
title: 机器学习与移动应用开发的未来
tags: 机器学习
categories:
- 机器学习
cover: '/images/MLFeature/cover.jpeg'
---

移动开发者可以从设备上的机器学习（on-device machine learning）所能提供的革命性变化中获益匪浅。这是因为该技术能够支持移动应用程序，即允许通过利用强大的功能来实现更流畅的用户体验，例如提供准确的基于地理位置的建议或即时检测植物疾病等。

移动机器学习（mobile machine learning）的这种快速发展已经成为是对经典机器学习（classical machine learning）所面临的许多常见问题的回应。事实上，这些问题即将发生。未来的移动应用将需要更快的处理速度和更低的延迟。

你可能会疑问为什么人工智能优先的移动应用程序（AI-first mobile applications）不能简单地在云端中进行推理运算。首先，云技术依赖于中央节点（设想一个拥有大量存储空间和计算能力的大型数据中心）。而这种集中式的方式无法满足创建流畅的、基于机器学习驱动的移动用户体验所需的处理速度。因为数据必须在这个集中式数据中心进行处理，然后将结果发送回设备。这需要花费时间和金钱，并且很难保证数据的隐私。

在概述了移动机器学习的这些核心优势之后，下面让我们更详细地探讨为什么作为移动应用开发者，你会希望继续关注即将到来的设备机器学习革命。

<!-- more -->


## 降低延迟

![](/images/MLFeature/speed.jpeg)

移动应用开发者都知道，高延迟将会是导致一个 App 失败的重要原因，无论其功能有多强大或者品牌声誉如何。Android 设备的许多视频类应用在过去曾存在延迟问题，导致观看时音频和视频不同步的体验。同样，一个高延迟的社交应用也会导致非常令人沮丧的糟糕用户体验。

正是由于这些延迟问题，在移动设备上运用机器学习变得越来越重要。考虑到社交媒体图像过滤器和基于位置的用餐建议 —— 这些应用程序功能需要低延迟才能提供最高级别的结果。

如前所述，云处理的时间可能会很慢，最终，开发者需要达到零延迟才能使机器学习功能在其移动应用中正常运行。设备上的机器学习通过其数据处理能力为接近零延迟铺平了道路。

![](/images/MLFeature/mobile.gif)

> 图为实时低延迟的示例：Heartbeat 应用中实时视频的样式转换结果。

智能手机制造商和大型科技公司正在追赶这一目标。Apple 在这方面一直处于领先地位，它正在使用其仿生系统（Bionic system）开发更先进的智能手机芯片，该系统具有一个完整的神经引擎，可帮助神经网络直接在设备上运行，并具有令人难以置信的处理速度。

Apple 还在继续迭代更新 Core ML，这是一个面向移动开发者的机器学习平台；TensorFlow Lite 增加了对 GPU 的支持；Google 继续为其自己的机器学习平台 —— ML Kit 增加预加载特性。这些技术是移动开发者用于开发能够以闪电般的速度处理数据、消除延迟和减少错误的应用程序的技术之一。

这种精确性和无缝衔接的用户体验的结合是移动开发者在创建由 ML 驱动的应用程序时需要考虑的首要因素。为了保证这一点，开发者需要拥抱并接受设备上的机器学习。

## 增强安全性和隐私

![](/images/MLFeature/security.jpeg)

边缘计算（edge computing）的另一个不可低估的巨大优势是它如何提高其用户的安全性和隐私性。确保应用程序数据的受保护和隐私是移动开发者工作中不可或缺的一部分，特别是考虑到需要满足**通用数据保护法规**（General Data Protection Regulations，GDPR），这些新的隐私相关法律肯定将会影响移动开发实践。

由于数据不需要发送到服务器或者云端进行处理，因此网络犯罪分子很少能有机会利用数据传输中的任何漏洞，从而保证了数据的不受侵犯。这使移动开发者可以更轻松地满足 GDPR 中关于数据安全的规定。

设备上的机器学习解决方案也提供了去中心化，这与区块链的做法非常相似。换句话说，与针对集中式服务器的相同攻击相比，黑客更难通过 DDOS 攻击摧毁隐藏设备的网络连接。这项技术也可被证明对无人机和未来的执法工作有用。

上述 Apple 智能手机芯片也有助于提高用户安全性和隐私性，例如这些芯片是 Face ID 的支柱。iPhone 的这一功能依赖于设备上的神经网络，它可以收集用户脸部所有不同维度的数据，作为更准确，更安全的识别方法。


* Apple 介绍 iPhone X 上的 Face ID 视频链接：[https://www.youtube.com/watch?v=z-t1h0Y8vuM](https://www.youtube.com/watch?v=z-t1h0Y8vuM)

这类以及未来的人工智能硬件将为用户提供更安全的智能手机体验铺平道路，并为移动开发者提供额外的加密层，以保护用户的数据。

## 无需网络连接

![](/images/MLFeature/internet.jpeg)

除了延迟问题之外，将数据发送到云端以进行推理计算还需要有效的 Internet 连接。通常，在世界上比较发达的地区，这种方式可以很容易实现。但是，在网络连接不发达的地区呢？通过设备上的机器学习，神经网络可以直接在手机上运行。这允许开发者在任何给定时间和在任何设备上使用该技术，而不用管网络连接性如何。此外，它可以使机器学习特性大众化，因为用户不需要 Internet 连接到他们的应用程序。

医疗保健是一个可以从设备上的机器学习中受益匪浅的行业，因为应用开发者能够创建医疗工具来检查生命体征，甚至可以进行远程机器人手术，而无需任何 Internet 连接。该技术还可以帮助那些需要在没有网络连接的地方访问课堂材料的学生，例如在公共交通隧道中。

设备上的机器学习最终将为移动开发者提供创建应用程序的工具，这些应用可以使世界各地的用户受益，无论他们的网络连接情况如何。即使没有互联网连接，但未来新的智能手机功能将非常强大，用户在离线环境中使用应用程序时也不会受到延迟问题的困扰。

## 减少业务开销成本


![](/images/MLFeature/cost.jpeg)

设备上的机器学习还可以为您节省一笔支出，因为您不必为实现或维护这些解决方案而向外部供应商付费。如前所述，您不需要云计算或互联网来提供此类解决方案。

GPU 和人工智能专用芯片将是您可以购买的最昂贵的云服务。在设备上运行模型意味着您不需要为这些集群付费，这要归功于如今智能手机中日益复杂的神经处理单元（Neural Processing Units，NPU）。

避免移动端和云端之间繁重的数据处理噩梦，对于选择设备上的机器学习解决方案的企业来说是一个巨大的成本节省。通过这种设备上的推断计算（on-device inference）也可以降低带宽需求，最终节省大量的成本。

移动开发者还可以大大节省开发过程的开支，因为他们不必构建和维护额外的云基础设施。相反，他们可以通过一个较小的工程团队实现更多目标，从而使他们能够更有效地扩展他们的开发团队。

## 结语

毫无疑问，云计算在 2010 年代一直是数据和计算的福音，但科技行业正以指数级的速度发展，设备上的机器学习（on-device machine learning）可能很快将成为移动应用和物联网开发的标准。

由于其更低的延迟，增强的安全性，离线功能和降低成本，毫无疑问，该行业的所有主要参与者都在大力关注这项技术，它将定义移动开发者如何推进应用程序的创建。

如果你有兴趣了解移动机器学习的更多信息，它的工作原理，以及为什么它在整个移动开发领域中如此重要，这里有一些额外的资源可以帮助您入门：

* Matthijs Holleman 的博客《Machine, Think!》在 Apple 的移动机器学习框架 Core ML 方面有很多不错的教程和其他相关内容：
[https://machinethink.net/blog/](https://machinethink.net/blog/)

* 边缘人工智能（Artificial Intelligence at the Edge）：
[https://youtu.be/6R5pjcqBq6Y](https://youtu.be/6R5pjcqBq6Y)

* 此外，Heartbeat 在移动开发和机器学习的交叉领域也拥有越来越多的资源库：
[http://heartbeat.fritz.ai/](http://heartbeat.fritz.ai/)

> 本文转载自：[KANGZUBIN](https://kangzubin.com/mobile-machine-learning/)
> 
> 原文作者: Karl Utermohlen
> 
> 原文: [Machine Learning and the Future of Mobile App Development](https://heartbeat.fritz.ai/machine-learning-and-the-future-of-mobile-app-development-13dd2aeda533)