# 前言

我们都知道 JavaScript 应用程序是 Web 开发的未来，有许多不同的框架可用于构建同构 JavaScript Web 应用程序。然而，随着 Web 开发世界的变化，我们都需要作为开发人员现代化，学习新的框架并构建新的工具。重要的是要分析框架的代码方法，并采用相同的方法，而不是迷失在框架市场中。ReactJS 是一个开源的 JavaScript 库，类似于 Bootstrap，用于构建用户界面，被称为*MVC*中的*V*（视图）。当我们谈论*M*和*C*的定义时，我们可以使用其他框架，如 Redux 和 Flux，来处理远程数据。

Bootstrap 是一个用于开发响应式网站和 Web 应用程序的开源前端框架。它包括 HTML、CSS 和 JavaScript 代码来构建用户界面组件。这是一种更快、更简单的开发强大的移动优先响应式设计的方法。Bootstrap 库包括响应式的 12 列网格和预定义的类，用于简单的布局选项（固定宽度和全宽度）。Bootstrap 有数十个预定义的可重用组件和自定义的 jQuery 插件，如按钮、警报、下拉菜单、模态框、工具提示标签、分页、轮播、徽章和图标。

本书从对 ReactJS 和 Bootstrap 的详细研究开始。本书进一步介绍了如何使用 Twitter Bootstrap、React-Bootstrap 等来创建 ReactJS 的小组件。它还让我们了解了 JSX、Redux 和 Node.js 集成，以及高级概念，如 reducers、actions、store、live reload 和 webpack。目标是帮助读者使用 ReactJS 和 Bootstrap 构建响应式和可扩展的 Web 应用程序。

# 本书内容

第一章 *，使用 React 和 Bootstrap 入门*，介绍了 ReactJS、它的生命周期和 Bootstrap，以及一个小型表单组件。

第二章 *，使用 React-Bootstrap 和 React 构建响应式主题*，介绍了 React-Bootstrap 集成，它的好处以及 Bootstrap 响应式网格系统。

第三章 *，ReactJS-JSX*，讲述了 JSX，它的优势，以及在 React 中的工作原理和示例。

第四章 *，使用 ReactJS 进行 DOM 交互*，深入解释了 props 和 state 以及 React 如何与 DOM 交互，附有示例。

第五章 *，使用 React 的 jQuery Bootstrap 组件*，探讨了我们如何将 Bootstrap 组件与 React 集成，附有示例。

第六章 *，Redux 架构*，涵盖了使用 ReactJS 和 Node.js 的 Redux 架构，并附有示例，以及其优势和集成。

第七章 *，使用 React 进行路由*，展示了 React 路由器与 ReactJS 和 Bootstrap 的导航组件的示例，以及其优势和集成。

第八章 *，ReactJS API*，探讨了我们如何在 ReactJS 中集成 Facebook 等第三方 API 以获取个人资料。

第九章 *，React 与 Node.js*，涵盖了为服务器端 React 应用程序设置的 Node.js，并涵盖了使用 Bootstrap 和 ReactJS npm 模块创建小型应用程序。

第十章，*最佳实践*，列出了创建 React 应用程序的最佳实践，并帮助我们理解 Redux 和 Flux 之间的区别，Bootstrap 自定义以及要关注的项目列表。

# 这本书需要什么

要运行本书中的示例，需要以下工具：

| **ReactJS** | **15.1 及以上** | [`facebook.github.io/react/`](https://facebook.github.io/react/) |
| --- | --- | --- |
| ReactJS DOM | 15.1 及以上 | [`facebook.github.io/react/`](https://facebook.github.io/react/) |
| Babel | 5.8.23 | [`cdnjs.com/libraries/babel-core/5.8.23`](https://cdnjs.com/libraries/babel-core/5.8.23) |
| Bootstrap | 3.3.5 | [getbootstrap.com/](http://getbootstrap.com/) |
| jQuery | 1.10.2 | [`jquery.com/download/`](http://jquery.com/download/) |
| React-Bootstrap | 1.0.0 | [`react-bootstrap.github.io/`](https://react-bootstrap.github.io/) |
| JSX 转换器 | 0.13.3 | [`cdnjs.com/libraries/react/0.13.0`](https://cdnjs.com/libraries/react/0.13.0) |
| React Router 库 | 3.0.0 | [`unpkg.com/react-router@3.0.0/umd/ReactRouter.min.js`](https://unpkg.com/react-router@3.0.0/umd/ReactRouter.min.js) |
| Node.js | 0.12.7 | [`nodejs.org/en/blog/release/v0.12.7/`](https://nodejs.org/en/blog/release/v0.12.7/) |
| MongoDB | 3.2 | [`www.mongodb.org/downloads#production`](https://www.mongodb.org/downloads#production) |

# 这本书适合谁

如果您对 HTML、CSS 和 JavaScript 有中级知识，并且想要学习为什么 ReactJS 和 Bootstrap 是开发人员创建应用程序快速、响应式和可扩展用户界面的第一选择，那么这本书适合您。如果您对模型、视图、控制器（MVC）概念很清楚，那么理解 React 的架构就是一个额外的优势。

# 约定

在本书中，您将找到一些文本样式，用于区分不同类型的信息。以下是这些样式及其解释的一些示例。

文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名显示如下：“现在我们需要在`chapter1`文件夹内创建几个文件夹，分别命名为`images`、`css`和`js`（JavaScript），以便使您的应用程序更易管理。”

代码块设置如下：

```jsx
<section> 
    <h2>Add your Ticket</h2> 
</section> 
<script> 
    var root = document.querySelector
    ('section').createShadowRoot(); 
    root.innerHTML = '<style>h2{ color: red; }</style>' + 
    '<h2>Hello World!</h2>'; 
</script>
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项将以粗体显示：

```jsx
<div className="container"> 
    <h1>Welcome to EIS</h1> 
    <hr/> 
        <div className="row"> 
            <div className="col-md-12 col-lg-12"> 
**{this.props.children}** 
            </div> 
        </div> 
</div>
```

任何命令行输入或输出都按照以下方式编写：

```jsx
**npm install <package name> --save**
```

**新术语**和**重要单词**以粗体显示。您在屏幕上看到的单词，例如菜单或对话框中的单词，会在文本中以这种方式出现：“在**仪表板页面**上，您的左侧导航显示**设置**链接。请点击该链接设置应用程序的**基本**和**高级**设置。”

### 注意

警告或重要提示会以这样的方式出现在一个框中。

### 提示

提示和技巧如下所示。
