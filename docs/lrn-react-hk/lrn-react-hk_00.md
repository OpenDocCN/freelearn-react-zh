# 前言

React 是一个用于构建高效和可扩展的 Web 应用程序的 JavaScript 库。React 由 Facebook 开发，被用于许多大型 Web 应用程序，如 Facebook、Instagram、Netflix 和 WhatsApp Web。

React Hooks 是在 React 16.8 版本中引入的，解决了许多 React 项目中的常见问题。Hooks 使组件变得不那么复杂，更简洁，更易于阅读和重构。此外，它们使许多 React 功能更容易使用和理解，避免了使用包装组件。

本书是学习 React Hooks 的权威指南。您将学习如何使用 React Hooks 管理 React 组件中的状态和效果，以及如何使用其他 React 功能，如上下文，通过 Hooks。通过实际示例，您将学习如何使用易于扩展和理解的代码开发大型高效的应用程序。

本书还涉及高级概念，如将 Hooks 与 Redux 和 MobX 等库结合使用。此外，您将学习如何有效地将现有项目迁移到 React Hooks。

# 本书适合对象

本书适用于任何级别的 JavaScript 和 React 框架的 Web 开发人员。本书还将满足那些因其先进的功能集和能力而迁移到 React 的开发人员的需求。

# 本书涵盖内容

第一章 *介绍 React 和 React Hooks*，介绍了 React 和 React Hooks 的基本原理，它们是什么以及为什么要使用它们。然后，我们通过介绍 State Hook 作为替代类组件中的 React 状态来了解 Hooks 的功能。最后，我们介绍了 React 提供的各种 Hooks，并介绍了本书中将要学习的一些 Hooks。

第二章 *使用 State Hook*，通过重新实现`useState` Hook 来深入讲解 Hook 的工作原理。通过这样做，我们发现了 Hooks 的某些限制。然后，我们将重新实现的 Hook 与真正的 Hooks 进行比较。此外，我们介绍了替代的 Hook API，并讨论了它们所面临的问题。最后，我们学习如何使用 Hooks 解决常见问题，如条件 Hooks 和循环中的 Hooks。

第三章，“使用 React Hooks 编写你的第一个应用程序”，将前两章学到的知识付诸实践，通过使用 React Hooks，特别是 State Hook，开发博客应用程序。在本章中，我们还学习了如何以可扩展的方式构建 React 项目结构。

第四章，“使用 Reducer 和 Effect Hooks”，从学习简单的 State Hook 并将其应用到实践中开始。我们将学习 React 库预定义的另外两个主要 Hooks：Reducer 和 Effect Hooks。首先我们学习何时应该使用 Reducer Hook 而不是 State Hook。然后我们学习如何将现有的 State Hook 转换为 Reducer Hook 以了解概念。最后，我们学习如何使用 Effect Hooks 实现更高级的功能。

第五章，“实现 React Context”，解释了 React 上下文以及如何在我们的应用程序中使用它。然后我们在博客应用程序中实现 React 上下文，以提供主题功能和使用 Context Hooks 的全局状态。

第六章，“实现请求和 React Suspense”，涵盖了使用 Effect Hook 和 State 或 Reducer Hook 从服务器请求资源的内容。然后我们学习如何使用`React.memo`来防止不必要的组件重新渲染。最后，我们了解了 React Suspense，它可以用于推迟渲染直到满足条件，也称为延迟加载。

第七章，“使用 Hooks 进行路由”，解释了如何在我们的博客应用程序中使用 Hooks 来实现路由。我们了解了 Navi，这是一个用于 React 的路由库，它利用了 Hooks 和 Suspense。我们首先在应用程序中实现页面，然后定义路由，最后开始实现路由 Hooks。

第八章《使用社区钩子》解释了 React 社区已经开发了各种利用钩子的库。在本章中，我们将学习如何实现来自社区的各种钩子，以及在哪里找到更多的钩子。我们首先学习了输入处理钩子。接下来，我们学习如何用钩子替换 React 生命周期方法。然后，我们学习了各种有用的钩子和使用钩子进行响应式设计。此外，我们学习了如何使用钩子实现撤销/重做功能。最后，我们学习了在社区提供的其他钩子的位置。

第九章《Hooks 的规则》涵盖了 Hooks 的规则。掌握 Hooks 的规则对于构建我们自己的 Hooks 非常重要，而这将在下一章中进行。我们还深入了解了 Hooks 的限制，并发现了需要注意的事项。最后，我们学习了如何使用代码检查器强制执行 Hooks 的规则。

第十章《构建自己的 Hooks》从 Hooks 的基本概念开始。我们现在将构建自己的 Hooks。我们首先从我们的博客应用程序的现有函数中提取一个自定义的 Hook，然后学习如何使用我们的自定义 Hook。接下来，我们学习了如何在 Hooks 之间传递信息。最后，我们学习了 React Hooks API 以及我们可以使用的其他 Hooks 来构建我们自己的 Hooks。在本章结束时，我们的应用程序将完全由 Hooks 驱动！

第十一章《从 React 类组件迁移》涵盖了使用 React 类组件处理状态。我们首先使用类组件实现了一个简单的 ToDo 应用程序。然后，我们学习如何将使用类组件的现有项目迁移到基于 Hooks 的实现。最后，我们学习了使用类组件与 Hooks 的权衡以及有效迁移现有项目的策略。

第十二章《Redux 和 Hooks》解释了使用 Redux 处理状态。我们首先将现有的 ToDo 应用程序迁移到 Redux，然后学习如何使用 Redux 与 Hooks。此外，我们学习了如何将现有的 Redux 应用程序迁移到 Hooks。最后，我们学习了使用 Redux 的权衡。

第十三章，*MobX 和 Hooks*，涵盖了使用 MobX 进行状态处理。我们首先将现有的 ToDo 应用程序迁移到 MobX。然后我们学习如何使用 Hooks 与 MobX。此外，我们还学习了如何将现有的 MobX 应用程序迁移到 Hooks。最后，我们了解了使用 MobX 的权衡之处。

# 充分利用本书

我们假设您已经以某种方式使用过 React，尽管本书也适合 React 的完全初学者。

请注意，强烈建议您自己编写代码。不要简单地运行提供的代码示例。重要的是要自己编写代码，以便正确学习和理解。但是，如果遇到任何问题，您可以随时参考代码示例。

# 下载示例代码文件

您可以从[www.packt.com](http://www.packt.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了本书，可以访问[www.packtpub.com/support](https://www.packtpub.com/support)并注册，以便文件直接通过电子邮件发送给您。

您可以按照以下步骤下载代码文件：

1.  登录或注册[www.packt.com](http://www.packt.com)。

1.  选择支持选项卡。

1.  单击“代码下载”。

1.  在搜索框中输入书名，并按照屏幕上的说明进行操作。

下载文件后，请确保使用最新版本的以下软件解压缩文件夹：

+   Windows 上的 WinRAR/7-Zip

+   Mac 上的 Zipeg/iZip/UnRarX

+   Linux 上的 7-Zip/PeaZip

本书的代码包也托管在 GitHub 上：[`github.com/PacktPublishing/Learn-React-Hooks`](https://github.com/PacktPublishing/Learn-React-Hooks)。如果代码有更新，将在现有的 GitHub 存储库中进行更新。

我们还有来自丰富书籍和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。去看看吧！

# 下载彩色图像

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图像。您可以在这里下载：[`static.packt-cdn.com/downloads/9781838641443_ColorImages.pdf`](https://static.packt-cdn.com/downloads/9781838641443_ColorImages.pdf)。

# 实际操作中的代码

访问以下链接查看代码运行的视频：

[`bit.ly/2Mm9yoC`](http://bit.ly/2Mm9yoC)

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、文件夹名称、文件名、文件扩展名、路径名、虚拟 URL 和用户输入。以下是一个例子：“JavaScript 类提供了一个`render`方法，该方法返回用户界面（通常通过 JSX）。”

代码块设置如下：

```jsx
class Example extends React.Component {
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目将以粗体显示：

```jsx
    constructor (props) {
        super(props)
        this.state = { name: '' }
        this.handleChange = this.handleChange.bind(this)
    }
```

任何命令行输入或输出都以以下形式书写：

```jsx
> npm run-script build
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。以下是一个例子：“在本章中，我们还将学习有关**JSX**以及**ES6**引入的新 JavaScript 功能，直到**ES2018**。”

在代码块中，我们使用粗体格式来突出代码中的更改。通常，我们使用粗体来突出新代码。如果指定，我们可能还会使用粗体格式来指示应删除的代码部分。

警告或重要提示会出现在这样的形式中。提示和技巧会以这种形式出现。
