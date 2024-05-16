# 前言

现在存在大量用于 Web 开发的 JavaScript 库、框架和工具。然而，我们应该评估每种技术是否符合我们项目的要求。这就是为什么我想向你介绍 React，这是一个创建动态 UI 的最强大的库之一。它是目前由 Facebook 制作的最流行的库（不是框架）。我在不同的项目中使用过其他 JS 框架，比如 AngularJS（还有新版本的 Angular 2、4、5）、Backbone.js、Ember 和 Vue.js，但我可以告诉你，使用 React 我更喜欢开发新的 Web 应用程序。

React 改变了 Web 应用程序的开发方式，结合 Redux，我们得到了一个强大的前端架构，这不仅对有经验的开发人员有意义，也对刚开始前端之旅的人有意义。

本书介绍了 React 的所有工具和最佳实践，以简单易行的食谱形式呈现，本书中的所有食谱都是 100%实用的，每一个都有必要的代码来理解所有重要的内容。

欢迎来到一个更美好的未来，愿你在阅读和学习本书时玩得开心。

# 本书适合对象

本书可以被任何具有构建 Web 应用程序基础知识的开发人员使用。主要是 JavaScript 开发人员，但不限于任何其他类型的开发人员。

# 本书内容

[第一章]，使用 React 工作，React 是一个由 Facebook 制作的 JavaScript 库（MIT 许可证），用于创建交互式 UI。它用于创建动态和可重用的组件。React 最强大的地方在于它可以在客户端、服务器、移动应用甚至 VR 应用中使用。

[第二章]，征服组件和 JSX，本章包含了有关如何在 React 中创建组件的技巧。我们将学习如何创建 React 组件（类组件、纯组件和函数组件）并组织我们的项目结构。

[第三章]，处理事件、绑定和有用的 React 包，本章包含与处理事件、在 React 中绑定方法以及实现一些最有用的 React 包相关的技巧。

[第四章]，使用 React Router 为我们的应用程序添加路由，本章中，我们将学习如何使用 React Router v4 在我们的项目中添加动态路由。

第五章，《精通 Redux》，Redux 是 JavaScript 应用程序的可预测状态容器。这意味着 Redux 可以与原生 JavaScript 或诸如 Angular 和 jQuery 之类的框架/库一起使用。Redux 主要是一个负责发出状态更新和对操作做出响应的库。

第六章，《使用 Redux Form 创建表单》，表单是任何 Web 应用程序的基本部分，在接下来的教程中，我们将学习如何使用带有或不带有 Redux Form 的表单。

第七章，《使用 React 进行动画》，动画在任何 Web 应用程序中都很常见。自 CSS3 以来，动画已经变得普遍且易于实现。动画最常见的用途是过渡，其中您可以更改 CSS 属性并定义持续时间或延迟。

第八章，《使用 Node.js 和 MongoDB 以及 MySQL 创建 API》，Node.js 广泛用作 Web 应用程序的后端，因为它易于创建 API，并且其性能优于诸如 Java、PHP 和 Ruby 等技术。通常，使用 Node.js 的最流行方式是使用一个名为 Express 的框架。

第九章，《Apollo 和 GraphQL》，GraphQL 是一种应用层查询语言，可以与任何数据库一起使用，也是一个由 Facebook 创建的开源项目（MIT 许可证）。与 REST 的主要区别在于 GraphQL 不使用端点而是使用查询，并且受大多数服务器语言的支持，如 JavaScript（Node.js）、Go、Ruby、PHP、Java、Python 等。

第十章，《精通 Webpack 4.x》，Webpack 4 默认情况下不需要配置文件。在旧版本中，你必须有一个配置文件，但是当然，如果你需要根据项目需求自定义 Webpack 4，你仍然可以创建一个配置文件，这样会更容易配置。

[第十一章]（884074f8-0a8a-4e3f-b9dc-b817e0f3f7c3.xhtml）*，实现服务器端渲染*，如果你不太关心 SEO，可能就不需要担心服务器端渲染（SSR）。目前，Googlebot 支持客户端渲染（CSR），它可以在 Google 中索引我们的网站，但如果你关心 SEO，并且担心在其他搜索引擎（如 Yahoo、Bing 或 DuckDuckGo）上改善 SEO，那么使用服务器端渲染（SSR）是一个好方法。

[第十二章]（1daa68c4-442d-479f-b1b6-3efa80592901.xhtml）*，测试和调试，*测试和调试对于任何希望拥有最佳质量的项目都非常重要。不幸的是，许多开发人员不关心测试（单元测试），因为他们认为这会减慢开发速度，有些人把它留到项目结束时。根据我的个人经验，我可以说，从项目开始就进行测试会节省时间，因为最后你需要修复的错误会更少。

[第十三章]（863541f6-a9a0-4a1b-8d1b-e448571b24e1.xhtml）*，部署到生产环境*，现在是时候将我们的应用部署到生产环境并展示给世界。在这一章中，你将学习如何使用最好的云服务之一：Digital Ocean 来部署我们的 React 应用。

[第十四章]（2b2d55bd-c734-4907-9688-0a9e9a2ff0cb.xhtml）*，使用 React Native 工作*，React Native 是使用 JavaScript 和 React 构建移动应用的框架。许多人认为使用 React Native 可以制作一些“移动 Web 应用”或“混合应用”（如 Ionic、PhoneGap 或 Sencha），但实际上你构建的是原生应用，因为 React Native 会将你的 React 代码转换为 Android 的 Java 和 iOS 应用的 Objective-C。

# 要充分利用这本书

要掌握 React，你需要对 JavaScript 和 Node.js 有基本的了解。这本书主要针对 Web 开发人员，写作时做出了以下假设：

+   读者知道如何安装最新版本的 Node.js。

+   能理解 JavaScript ES6 语法的中级开发人员。

+   对 CLI 工具和 Node.js 语法有一些经验。

这本书也有一些内容适用于使用 React Native 的移动开发人员（iOS 和 Android），如果你是初学者，你应该学习如何安装 Android SDK 或使用 Xcode 安装 iOS 模拟器。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以按照以下步骤下载代码文件：

1.  在[www.packtpub.com](http://www.packtpub.com/support)上登录或注册。

1.  选择“支持”选项卡。

1.  单击“代码下载和勘误”。

1.  在“搜索”框中输入书名，然后按照屏幕上的说明进行操作。

下载文件后，请确保使用最新版本的解压缩软件解压文件夹：

+   Windows 用户可使用 WinRAR/7-Zip

+   Mac 用户可使用 Zipeg/iZip/UnRarX

+   Linux 用户可使用 7-Zip/PeaZip

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/React-Cookbook`](https://github.com/PacktPublishing/React-Cookbook)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有来自丰富图书和视频目录的其他代码包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)上找到。快去看看吧！

警告或重要说明会显示为这样。提示和技巧会显示为这样。
