# 前言

自第一版 React Essentials 以来，React 生态系统发生了很多变化。越来越多的人正在构建 React 应用程序，有成熟的库和框架支持 React 应用程序，React 16 也已发布。React 在如此短的时间内的爆炸式增长可以归因于许多因素：优秀的社区和相关资源，React 生态系统的广泛性以及某些重要项目的成熟，当然还有 React 团队及其愿意将开发者反馈作为项目持续发展的优先事项。

我很荣幸能参与这样一本重要的 React 书籍。正如书名所示，本书旨在教授 React 的基础知识。这个最新版本反映了 React 最新版本的变化，使用 Redux 来管理状态，以及 JavaScript 语言本身的变化。

加入我吧。让我们成为 React 成为构建用户界面的标准的专家。

# 本书内容

第一章，*React 16 有什么新变化*，介绍了 React 16 的重大变化。这包括了底层渲染和协调工作的基本变化，以及通过 API 公开的其他新功能。

第二章，*为你的项目安装强大的工具*，概述了本书的目标，并解释了你需要安装哪些现代工具才能有效构建 React 应用程序。它介绍了每个工具，并提供了逐步说明如何安装每个工具。然后，它为本书中将要构建的项目创建了一个结构。

第三章，*创建你的第一个 React 元素*，解释了如何安装 React 并介绍了虚拟 DOM。然后，它解释了什么是 React 元素，以及如何使用原生 JavaScript 语法创建和渲染一个。最后，它介绍了 JSX 语法，并展示了如何使用 JSX 创建 React 元素。

第四章，*创建你的第一个 React 组件*，介绍了 React 组件。它解释了无状态和有状态 React 组件之间的区别，以及如何决定使用哪种。然后，它指导你完成创建这两种组件的过程。

第五章，使您的 React 组件具有响应性，解释了如何解决 React 的问题，并引导您规划 React 应用程序的过程。它创建了一个封装了整个本书中构建的 React 应用程序的 React 组件。它解释了父子 React 组件之间的关系。

第六章，使用另一个库使用您的 React 组件，探讨了如何在 React 组件中使用第三方 JavaScript 库。它介绍了 React 组件的生命周期，演示了如何使用挂载方法，并展示了如何为本书的项目创建新的 React 组件。

第七章，更新您的 React 组件，介绍了 React 组件生命周期的更新方法。这涵盖了如何在 JavaScript 中使用 CSS 样式。它解释了如何验证和设置组件的默认属性。

第八章，构建复杂的 React 组件，着重于构建更复杂的 React 组件。它探讨了如何实现不同的 React 组件以及如何将它们组合成一个连贯且完全功能的 React 应用程序的细节。

第九章，使用 Jest 测试您的 React 应用程序，解释了单元测试的概念，以及如何使用 Jest 编写和运行单元测试。它还演示了如何测试您的 React 组件。它讨论了测试套件、规范、期望和匹配器。

第十章，使用 Flux 加速您的 React 架构，讨论了如何改进我们的 React 应用程序的架构。它介绍了 Flux 架构，并解释了调度程序、存储和操作创建者的作用。

第十一章，使用 Flux 为您的 React 应用程序做好无痛维护的准备，解释了如何使用 Flux 在您的 React 应用程序中解耦关注点。它重构了我们的 React 应用程序，以便将来可以轻松地进行维护。

第十二章，*用 Redux 完善您的 Flux 应用*，将带您了解 Flux 库的主要特性，然后完全重构一个应用程序，以使用 Redux 作为控制状态的主要机制。

# 本书所需内容

首先，您需要最新版本的现代 Web 浏览器，如 Google Chrome 或 Mozilla Firefox：

+   Google Chrome：[`www.google.com/chrome/browser`](https://www.google.com/chrome/browser)

+   Mozilla Firefox：[`www.mozilla.org/en-US/firefox/new/`](https://www.mozilla.org/en-US/firefox/new/)

其次，您需要安装 Git、Node.js 和 npm。您将在第二章中找到详细的安装和使用说明，*为您的项目安装强大的工具*。

最后，您需要一个代码编辑器。我推荐*Sublime Text*（[`www.sublimetext.com`](http://www.sublimetext.com)）。或者，您可以使用*Atom*（[`atom.io`](https://atom.io)）、*Brackets*（[`brackets.io`](http://brackets.io)）、*Visual Studio Code*（[`code.visualstudio.com`](https://code.visualstudio.com)）或者您选择的任何其他编辑器。

# 这本书是为谁准备的

本书面向希望为 Web 构建可扩展和可维护用户界面的前端开发人员。了解 JavaScript、HTML 和 CSS 的一些核心知识是您开始从 React.js 带入 Web 开发世界的革命性思想中受益的唯一所需。如果您之前有 jQuery 或 Angular.js 的经验，那么您将受益于了解 React.js 的不同之处以及如何利用集成不同库与它。

# 约定

在本书中，您将找到一些区分不同信息类型的文本样式。以下是一些示例以及它们的含义解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄显示如下：文本中的代码单词显示如下：“我们可以通过使用`include`指令包含其他上下文。”

代码块设置如下：

```jsx
import React from 'react';
import { render } from 'react-dom';

const reactElement = React.createElement(
  'h1', 
  { className: 'header' }
);

render(
  reactElement,
  document.getElementById('react-application')
);
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项将以粗体显示：

```jsx
<!doctype html>
  <html lang="en">
    <head>
      <title>Snapterest</title>
    </head>
    <body>
 **<div id="react-application">**
 **I am about to learn the essentials of React.js.**
 **</div>** <script src="./snapterest.js"></script>
    </body>
  </html>
```

任何命令行输入或输出都以以下方式编写：

```jsx
**cd ~**
**git clone https://github.com/snapkite/snapkite-engine.git**

```

**新术语**和**重要词汇**以粗体显示。屏幕上看到的词语，比如菜单或对话框中的词语，会以这样的方式出现在文本中："点击**下一步**按钮会将您移动到下一个屏幕。"

### 注意

警告或重要提示会出现在这样的框中。

### 提示

提示和技巧会出现在这样。
