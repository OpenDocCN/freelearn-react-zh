# 前言

React 是由 Facebook 开发的，旨在为其代码库提供更多结构，并使其能够更好地扩展。React 对 Facebook 非常有效，以至于他们最终将其开源。如今，React 是构建前端最流行的 JavaScript 库之一。它允许我们构建小型、隔离的、高度可重用的组件，这些组件可以组合在一起，以创建复杂的前端。

TypeScript 是由微软开发的，旨在帮助开发人员更轻松地构建大型前端应用程序。它是 JavaScript 的超集，为其带来了丰富的类型系统。这种类型系统帮助开发人员及早发现错误，并允许创建工具来稳健地导航和重构代码。

这本书将教会你如何有效地使用这两种技术来创建易于维护的大型复杂前端。

# 这本书适合谁

这本书主要面向希望使用 React 和 TypeScript 创建大型 Web 应用程序的 Web 开发人员。假定具有 JavaScript 和 HTML 的基本理解。

# 这本书涵盖了什么

第一章，“TypeScript 基础”，介绍了 TypeScript 类型系统，涵盖了基本类型。它继续介绍了如何配置非常灵活的 TypeScript 编译器。还介绍了代码检查和代码格式化，以及它们的配置。

第二章，“TypeScript 3 有什么新功能”，介绍了 TypeScript 3 版本中引入的重要新功能。元组在本章中占据重要地位，以及与之密切相关的剩余和扩展语法，以及我们如何可以使用这些构造与强类型。在移动到改进设置 React 组件中默认属性值之前，还介绍了有效设置多个相关 TypeScript 项目的方法。

第三章，“使用 React 和 TypeScript 入门”，从如何创建同时使用这两种技术的项目开始。该章介绍了如何以基于类和功能的方式构建强类型 React 组件。管理状态和挂接到生命周期事件也是本章的关键主题。

第四章，*使用 React Router 进行路由*，介绍了一个可以帮助我们高效创建具有多个页面的应用程序的库。它涵盖了如何创建页面链接，并声明应该呈现的组件。逐步介绍了如何实现路由参数、查询参数和嵌套路由。该章节还涵盖了如何根据需要从路由加载组件，以优化大量大页面的应用程序性能。

第五章，*高级类型*，专注于 TypeScript 类型。这一次，介绍了更高级但仍然非常有用的类型，如泛型类型、联合类型、重载签名以及 keyof 和查找类型。

第六章，*组件模式*，介绍了构建 React 组件的一些常见模式，同时仍保持强类型。首先介绍了容器组件，然后是复合组件。流行的渲染道具模式和高阶组件也在本章中介绍。

第七章，*使用表单*，介绍了如何使用 React 和 TypeScript 高效实现表单。逐步构建了一个通用的表单组件，包括验证和提交。

第八章，*React Redux*，介绍了这个流行库如何帮助管理应用程序中的状态。使用动作和减速器构建了一个强类型的 Redux 存储。该章节最后讨论了如何使用新的 React 函数在组件中实现 Redux 风格的结构，而无需 Redux。

第九章，*与 RESTful API 交互*，首先详细介绍了异步代码。然后，该章节继续介绍了如何使用原生 JavaScript 函数以及流行的开源库与 RESTful API 进行交互。

第十章，*与 GraphQL API 交互*，首先介绍了读取和写入数据的语法。该章节涵盖了如何使用 HTTP 库与 GraphQL 服务器进行交互，然后转向使用一个流行的专门构建的库。

第十一章，*使用 Jest 进行单元测试*，介绍了如何测试纯函数和 React 组件。还介绍了一个流行的开源库，用于在组件内部重构时使测试更加灵活。还介绍了 Jest 的一些出色功能，如快照测试、模拟和代码覆盖率。

答案，包含本书各章节练习的答案。

# 要充分利用本书

您需要了解 JavaScript 的基础知识，包括以下内容：

+   了解一些原始 JavaScript 类型，如字符串、数字、布尔值、null 和 undefined

+   如何创建变量并引用它们，包括数组和对象

+   如何创建函数并调用它们的理解

+   如何使用 if 和 else 关键字创建条件语句的理解

您需要了解 HTML 的基础知识，包括以下内容：

+   了解基本的 HTML 标签，如 div、ul、p、a、h1 和 h2，以及如何将它们组合在一起创建网页

+   如何引用 CSS 类来为 HTML 元素设置样式的理解

了解基本的 CSS 也有帮助，但不是必需的：

+   如何设置元素的大小并包括边距和填充

+   如何定位元素

+   如何给元素上色

您需要在计算机上安装以下技术：

+   **Google Chrome**：可以在[`www.google.com/chrome/`](https://www.google.com/chrome/)上安装。

+   **Node.js 和** **npm**：本书中都会用到它们。您可以在[`nodejs.org/en/download/`](https://nodejs.org/en/download/)上安装它们。如果您已经安装了它们，请确保 Node.js 至少是 8.2 版本，npm 至少是 5.2 版本。

+   **TypeScript**：可以通过在终端中输入以下命令来安装它：

```jsx
npm install -g typescript
```

+   **Visual Studio Code**：您需要它来编写 React 和 TypeScript 代码。可以从[`code.visualstudio.com/`](https://code.visualstudio.com/)安装。

# 下载示例代码文件

您可以从[www.packt.com](http://www.packt.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[www.packt.com/support](http://www.packt.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以按照以下步骤下载代码文件：

1.  请登录或在[www.packt.com](http://www.packt.com)注册。

1.  选择“支持”选项卡。

1.  点击“代码下载和勘误”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

文件下载后，请确保使用最新版本的解压缩软件解压缩文件夹：

+   Windows 系统使用 WinRAR/7-Zip

+   Mac 系统使用 Zipeg/iZip/UnRarX

+   Linux 系统使用 7-Zip/PeaZip

本书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Learn-React-with-TypeScript-3`](https://github.com/PacktPublishing/Learn-React-with-TypeScript-3)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有来自丰富图书和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。快去看看吧！

# 下载彩色图像

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图像。您可以在这里下载：[`www.packtpub.com/sites/default/files/downloads/9781789610253_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/9781789610253_ColorImages.pdf)。

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。以下是一个例子：“让我们在项目的根目录下创建一个名为`tsconfig.json`的新文件。”

代码块设置如下：

```jsx
import * as React from "react";

const App: React.SFC = () => {
  return <h1>My React App!</h1>;
};
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目将以粗体显示：

```jsx
interface IProps {
 title: string;
  content: string;
  cancelCaption?: string;
 okCaption?: string;
}
```

任何命令行输入或输出都将按以下方式书写：

```jsx
cd my-components
npm install tslint tslint-react tslint-config-prettier --save-dev
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词会以这种方式出现在文本中。以下是一个例子：“我们需要点击“安装”选项来安装扩展。”

警告或重要说明会以这种方式出现。提示和技巧会以这种方式出现。
