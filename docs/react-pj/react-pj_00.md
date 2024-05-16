# 前言

本书将帮助您将 React 知识提升到下一个水平，教您如何应用基本和高级的 React 模式来创建跨平台应用程序。React 的概念以一种既适合新手又适合有经验的开发人员理解的方式进行描述；虽然不需要有 React 的先前经验，但这将有所帮助。

在本书的 12 章中，您将使用 React、React Native 或 React 360 创建一个项目。这些章节中创建的项目实现了流行的 React 功能，如用于重用逻辑的**高阶组件**（HOCs）、用于状态管理的上下文 API 和用于生命周期的 Hooks。用于路由的流行库，如 React Router 和 React Navigation，以及用于编写应用程序的单元测试的 JavaScript 测试框架 Jest。此外，一些更高级的章节涉及 GraphQL 服务器，并且 Expo 用于帮助您创建 React Native 应用程序。

# 本书适合对象

本书适用于希望探索用于构建跨平台应用程序的 React 工具和框架的 JavaScript 开发人员。对 Web 开发、ECMAScript 和 React 的基本知识将有助于理解本书涵盖的关键概念。

本书支持的 React 版本为：

+   React - v16.10.2

+   React Native - v0.59

+   React 360 - v1.1.0

# 本书涵盖的内容

第一章，“在 React 中创建电影列表应用程序”，将探讨构建可扩展的 React 项目的基础。将讨论和实践如何组织文件、使用的包和工具的最佳实践。通过构建电影列表来展示构建 React 项目的最佳方法。此外，使用 webpack 和 Babel 来编译代码。

第二章，“使用可重用的 React 组件创建渐进式 Web 应用程序”，将解释如何在整个应用程序中设置和重用 React 组件中的样式。我们将构建一个 GitHub 卡片应用程序，以了解如何在 JavaScript 中使用 CSS 并在应用程序中重用组件和样式。

第三章，“使用 React 和 Suspense 构建动态项目管理看板”，将介绍如何创建确定其他组件之间数据流的组件，即所谓的 HOCs。我们将构建一个项目管理看板，以了解数据在整个应用程序中的流动。

第四章，使用 React Router 构建基于 SSR 的社区动态，将讨论路由设置，从设置基本路由、动态路由处理，到如何为服务器端渲染设置路由。

第五章，使用 Context API 和 Hooks 构建个人购物清单应用程序，将向您展示如何使用 React 上下文 API 和 Hooks 处理整个应用程序中的数据流。我们将创建一个个人购物清单，以了解如何使用 Hooks 和上下文 API 从父组件到子组件以及反之访问和更改数据。

第六章，使用 Jest 和 Enzyme 构建探索 TDD 的应用程序，将专注于使用断言和快照进行单元测试。还将讨论测试覆盖率。我们将构建一个酒店评论应用程序，以了解如何测试组件和数据流。

第七章，使用 React Native 和 GraphQL 构建全栈电子商务应用程序，将使用 GraphQL 为应用程序提供后端。本章将向您展示如何设置基本的 GraphQL 服务器并访问该服务器上的数据。我们将构建一个电子商务应用程序，以了解如何创建服务器并向其发送请求。

第八章，使用 React Native 和 Expo 构建房屋列表应用程序，将涵盖 React Native 应用程序的扩展和结构，这与使用 React 创建的 Web 应用程序略有不同。本章将概述开发环境和工具（如 Expo）的差异。我们将构建一个房屋列表应用程序，以检验最佳实践。

第九章，使用 React Native 和 Expo 构建动画游戏，将讨论动画和手势，这正是移动应用程序与 Web 应用程序的真正区别。本章将解释如何实现它们。此外，通过构建一个具有动画并响应手势的纸牌游戏应用程序，将展示 iOS 和 Android 之间手势的差异。

第十章 *使用 React Native 和 Expo 创建实时消息应用程序*，将涵盖通知，这对于让应用程序的用户保持最新状态非常重要。本章将展示如何通过 Expo 从 GraphQL 服务器添加通知并发送通知。我们将通过构建消息应用程序来学习如何实现所有这些。

第十一章 *使用 React Native 和 GraphQL 构建全栈社交媒体应用程序*，将介绍如何使用 React Native 和 GraphQL 构建全栈应用程序。演示服务器和应用程序之间的数据流动，以及如何从 GraphQL 服务器获取数据。

第十二章 *使用 React 360 创建虚拟现实应用程序*，将讨论如何通过创建全景查看器来开始使用 React 360，使用户能够在虚拟世界中四处张望并在其中创建组件。

# 为了充分利用本书

本书中的所有项目都是使用 React、React Native 或 React 360 创建的，需要您具备 JavaScript 的基础知识。虽然本书中描述了 React 和相关技术的所有概念，但我们建议您在想要了解更多功能时参考 React 文档。在接下来的部分，您可以找到关于为本书设置您的计算机以及如何下载每一章的代码的一些信息。

# 设置您的计算机

对于本书中创建的应用程序，您需要至少在您的计算机上安装 Node.js v10.16.3，以便您可以运行 npm 命令。如果您尚未在计算机上安装 Node.js，请访问[`nodejs.org/en/download/`](https://nodejs.org/en/download)，在那里您可以找到 macOS、Windows 和 Linux 的下载说明。

安装 Node.js 后，在命令行中运行以下命令以检查已安装的版本：

+   对于 Node.js（应为 v10.16.3 或更高版本）：

```jsx
node -v
```

+   对于 npm（应为 v6.9.0 或更高版本）：

```jsx
npm -v
```

此外，您还应该安装**React Developer Tools**插件（适用于 Chrome 和 Firefox）并将其添加到您的浏览器中。可以从**Chrome Web Store**([`chrome.google.com/webstore`](https://chrome.google.com/webstore))或 Firefox Addons([`addons.mozilla.org`](https://addons.mozilla.org))安装此插件。

# 下载示例代码文件

您可以从您在[www.packt.com](http://www.packt.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[www.packtpub.com/support](https://www.packtpub.com/support)并注册，以便文件直接发送到您的邮箱。

您可以按照以下步骤下载代码文件：

1.  在[www.packt.com](http://www.packt.com)上登录或注册。

1.  选择“支持”选项卡。

1.  单击“代码下载”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

文件下载后，请确保使用最新版本的解压缩软件解压缩文件夹：

+   Windows 的 WinRAR/7-Zip

+   Mac 的 Zipeg/iZip/UnRarX

+   Linux 的 7-Zip/PeaZip

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/React-Projects`](https://github.com/PacktPublishing/React-Projects)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有来自丰富书籍和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。快去看看吧！

# 下载彩色图像

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图像。您可以在这里下载：[`static.packt-cdn.com/downloads/9781789954937_ColorImages.pdf`](https://static.packt-cdn.com/downloads/9781789954937_ColorImages.pdf)。

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。这里有一个例子：“由于您将在本章中构建一个电影列表应用程序，因此将此目录命名为`movieList`。”

代码块设置如下：

```jsx
{
    "name": "movieList",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC"
}
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目以粗体设置：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
+ import List from './containers/List';

const App = () => {
-   return <h1>movieList</h1>;
+   return <List />;
};

ReactDOM.render(<App />, document.getElementById('root'));
```

任何命令行输入或输出都是这样写的：

```jsx
npm init -y
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词会以这种方式出现在文本中。这里有一个例子：“当用户单击关闭 X 按钮时，组件的显示样式规则将设置为 none。”

警告或重要说明是这样显示的。提示和技巧是这样显示的。
