# 前言

Facebook 的 React 框架重新定义了前端应用程序的构建方式。React Router 已成为使用 React 构建的应用程序的事实标准路由框架。通过其最新的 4 版本发布，该库已经在 React 中重写，并且它允许您以声明方式处理路由。在本书中，您将学习 react-router 库如何在任何 React 应用程序中使用，包括使用 React Native 开发的 Web 和原生移动应用程序。该书还涵盖了诸如服务器端路由和 Redux 与 React Router 集成等主题。

# 这本书适合谁

本书适用于考虑使用 React 和 React Router 构建应用程序的 Web 和原生移动应用程序开发人员。了解 React 框架和 JavaScript 的一些知识将有助于理解本书中讨论的概念。

# 要充分利用这本书

React Router 用于使用 React 开发的 Web 和原生应用程序。本书假定您对 JavaScript 有很好的理解，并且了解 ECMAScript 6 中引入的一些新语言特性，例如类和扩展运算符。

本书简要介绍了 React 和基于组件的架构。React 的一些其他核心概念在[`reactjs.org`](https://reactjs.org)有文档记录。

本书假定读者已经使用 Node.js 和 NPM 从 NPM 存储库安装了库和软件包。

# 下载示例代码文件

您可以从[www.packt.com](http://www.packtpub.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[www.packt.com/support](http://www.packtpub.com/support)并注册，以便文件直接通过电子邮件发送给您。

您可以按照以下步骤下载代码文件：

1.  在[www.packt.com](http://www.packt.com)上登录或注册

1.  选择“支持”选项卡

1.  单击“代码下载和勘误”

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

下载文件后，请确保您使用最新版本的解压缩或提取文件夹：

+   WinRAR/Windows 的 7-Zip

+   Mac 的 Zipeg/iZip/UnRarX

+   7-Zip/PeaZip 适用于 Linux

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/React-Router-Quick-Start-Guide`](https://github.com/PacktPublishing/React-Router-Quick-Start-Guide)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有其他代码包，来自我们丰富的图书和视频目录，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)上找到。去看看吧！

# 下载彩色图片

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图片。您可以在这里下载：`www.packtpub.com/sites/default/files/downloads/9781789532555_ColorImages.pdf.`

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：指示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。这是一个例子：“将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。”

代码块设置如下：

```jsx
In GitHubComponent
GitHub ID - mjackson
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目将以粗体显示：

```jsx
<Route
 to='/github/**:githubID**'
    component={GitHubComponent}  />
```

任何命令行输入或输出都以以下方式编写：

```jsx
 Root:
 path: /category, isExact: true
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词会以这种方式出现在文本中。这是一个例子：“从管理面板中选择系统信息。”

警告或重要说明会出现在这样的地方。提示和技巧会出现在这样的地方。
