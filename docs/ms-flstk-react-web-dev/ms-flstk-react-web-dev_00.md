# 前言

近年来 JavaScript 编程语言的创新至关重要。例如，自 2009 年以来，Node 的崛起使开发人员能够在浏览器和后端使用相同的编程语言。2017 年，环境变化也没有放缓。这本书将教你一些新的热门概念，进一步加快全栈开发过程。

在 2016 年和 2017 年，对于使用 Falcor 或 GraphQL 等全栈技术使应用程序更快速的需求更大。这本书不仅仅是关于如何在 Node 中公开 API 端点并开始在客户端应用程序中使用它们的指南。你将学习如何使用 Netflix 的最新技术 Falcor。此外，你还将学习如何使用 React 和 Redux 设置项目。

在这本书中，你将找到一个从头开始构建全栈应用程序的巨大教程，使用 Node.js，Express.js，MongoDB，Mongoose，Falcor 和 Redux 库。你还将学习如何使用 Docker 和亚马逊的 AWS 服务部署你的应用程序。

# 本书涵盖的内容

第一章，*使用 Node.js，Express.js，MongoDB，Mongoose，Falcor 和 Redux 配置全栈*，带你从头开始设置应用程序。它可以帮助你了解 npm 中不同库如何构建一个可用的全栈 React 起始工具包。

第二章，*我们发布应用的全栈登录和注册*，指导你如何设置 JWT 令牌以实现基本的全栈身份验证机制。

第三章，*服务器端渲染*，教你如何将服务器端渲染添加到应用程序中，这有助于加快应用程序的执行速度和搜索引擎优化。

第四章，*客户端高级 Redux 和 Falcor*，向你展示如何为应用程序添加更高级的功能，如集成的所见即所得编辑器和扩展应用程序的 Material-UI 组件，从应用用户的角度扩展应用程序。

第五章，*Falcor 高级概念*，带你深入了解与 Falcor 及其后端 Falcor-Router 相关的更详细的后端开发指南。

第六章，*AWS S3 用于图片上传和关键应用程序功能的封装*，指导你通过发布应用的文章封面照片上传过程。

第七章，*在 mLab 上部署 MongoDB*，教你如何为你的应用程序准备远程数据库。

第八章，*Docker 和 EC2 容器服务*，教你 AWS/Docker 设置。

第九章，*使用单元测试和行为测试进行持续集成*，向您展示了为准备发布的应用程序准备 CI 和测试所需的内容。


# 你需要为这本书做好准备

这本书是在 macOS El Capitan 和 Sierra 上编写的。它在 Linux Ubuntu 和 Windows 10 机器上进行了测试（关于这三个操作系统之间的差异，已添加了一些额外的评论）。

其余的工具安装过程在第一章，*使用 Node.js、Express.js、MongoDB、Mongoose、Falcor 和 Redux 配置全栈*中展示。

# 这本书适合谁

你想要从零开始构建和理解全栈开发吗？那么这本书适合你。

如果你是一名寻求提高全栈开发技能的 React 开发人员，那么你也会感到如鱼得水。你将使用最新技术从头开始构建下一个全栈发布应用程序。在端到端的指导下制作你的第一个全栈应用程序。

我们在书中假设您已经具备了 React 库的基本知识。

# 约定

在这本书中，你会发现许多文本样式，用于区分不同类型的信息。以下是一些这些样式的例子和它们的含义解释。

文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名都显示如下：“在项目目录中，创建一个名为`initData.js`的文件。”

代码块设置如下：

```jsx
[

    {

        articleId: '987654',

        articleTitle: 'Lorem ipsum - article one',

        articleContent: 'Here goes the content of the article'

    },

    {

        articleId: '123456',

        articleTitle: 'Lorem ipsum - article two',

        articleContent: 'Sky is the limit, the content goes here.'

    }

]

```

任何命令行输入或输出都是这样写的：

```jsx
mkdir server

cd server

touch index.js

```

**新术语**和**重要词汇**以粗体显示。例如，屏幕上看到的单词，比如菜单或对话框中的单词，会以这样的形式出现在文本中：“点击创建链接，使用默认设置创建连接。”

警告或重要说明会出现在这样的框中。提示和技巧会出现在这样的形式中。
