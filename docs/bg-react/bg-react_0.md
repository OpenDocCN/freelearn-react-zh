# 前言

诸如 Angular 和 React 之类的项目正在迅速改变开发团队构建和部署 Web 应用程序到生产环境的方式。在本书中，你将学习到使用 React 入门所需的基础知识，并应对现实世界的项目和挑战。本书包含了在开发过程中考虑关键用户需求的实用指导，并展示了如何处理高级概念，如状态管理、数据绑定、路由以及流行的 JSX 组件标记。完成本书中的示例后，你将发现自己已经准备好转向实际的个人或专业前端项目。

完成本书后，你将能够：

+   理解 React 如何在更广泛的应用程序堆栈中工作

+   分析如何将标准界面分解为特定组件

+   成功创建你自己的越来越复杂的 React 组件，无论是使用 HTML 还是 JSX

+   正确处理多个用户事件及其对整体应用程序状态的影响

+   理解组件生命周期以优化应用程序的用户体验

+   配置路由以允许通过你的组件进行轻松、直观的导航

# 本书适合的读者

如果你是一名前端开发者，希望在 JavaScript 中创建真正反应式的用户界面，那么这本书适合你。对于 React，你需要在 JavaScript 语言的基本要素方面有坚实的基础，包括 ES2015 中引入的新 OOP 特性。假设你了解 HTML 和 CSS，并且对 Node.js 有基本了解，这在管理开发工作流程的上下文中将是有用的，但不是必需的。

# 本书涵盖的内容

第一章，*介绍 React 和 UI 设计*，介绍 React 并帮助我们开始构建基于 React 的应用程序的基本基础设施。然后，我们将分析如何设计用户界面，以便它可以轻松映射到 React 组件。

第二章，*创建组件*，教我们如何实现 React 组件，如何将多个组件组合成一个，以及如何管理它们的内部状态。我们将通过构建一个简单的应用程序来探索 React 组件的实现。

第三章，*管理用户交互*，教我们如何管理用户与基于 React 的用户界面组件交互产生的事件。我们将探索在 React 组件生命周期中触发的事件，并学习如何利用它们来创建高效的组件。

# 充分利用本书

本书将需要具有以下最低硬件要求的系统：

+   处理器：Pentium 4（或同等产品）

+   4 GB RAM

+   硬盘空间：10 GB

+   互联网连接

以下软件也应该安装：

+   任何现代操作系统（最好是 Windows 10 版本 1507）

+   最新版本的 Node.js（[`nodejs.org/en/`](https://nodejs.org/en/)）

+   任何现代浏览器的最新版本（最好是 Chrome）

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)上的帐户下载本书的示例代码文件。如果您在其他地方购买了本书，可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便将文件直接发送到您的邮箱。

您可以按照以下步骤下载代码文件：

1.  登录或注册于[www.packtpub.com](http://www.packtpub.com/support)。

1.  选择支持选项卡。

1.  点击代码下载与勘误。

1.  在搜索框中输入书名，并按照屏幕上的指示操作。

下载文件后，请确保使用最新版本的以下软件解压缩或提取文件夹：

+   适用于 Windows 的 WinRAR/7-Zip

+   适用于 Mac 的 Zipeg/iZip/UnRarX

+   适用于 Linux 的 7-Zip/PeaZip

本书的代码包也托管在 GitHub 上，地址为[`github.com/TrainingByPackt/Beginning-React`](https://github.com/TrainingByPackt/Beginning-React)。如果有代码更新，将会在现有的 GitHub 仓库中更新。

我们还有来自丰富图书和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上查看。去看看吧！

# 下载彩色图像

我们还提供了一个包含本书中使用的截图/图表的彩色图像的 PDF 文件。您可以在此处下载：[`www.packtpub.com/sites/default/files/downloads/BeginningReact_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/BeginningReact_ColorImages.pdf)。

# 使用的约定

本书中使用了多种文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。例如：“通过包装`App`组件，`BrowserRouter`组件为其赋予了路由功能。”

代码块设置如下：

```jsx
class Catalog extends React.Component {
  constructor() {
    super();
```

当我们希望引起您对代码块特定部分的注意时，相关行或项目以粗体显示：

```jsx
import { BrowserRouter } from 'react-router-dom'
ReactDOM.render(
 <BrowserRouter>
    <App />
 </BrowserRouter>
  , document.getElementById('root'));
```

任何命令行输入或输出都以下列方式书写：

```jsx
create-react-app --version
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词以这种方式出现在文本中。例如：“现在我们需要创建一个视图来显示**目录**组件或**关于**页面。”

**活动**：这些是基于场景的活动，将让您在完整的部分过程中实际应用所学知识。它们通常是在现实世界问题或情况的背景下。

警告或重要提示以这种方式出现。
