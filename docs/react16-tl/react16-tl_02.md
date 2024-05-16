# 第二章：使用 Create React App 高效引导 React 应用程序

本书中您将学习的第一个 React 工具是*Create React App*。它是一个命令行实用程序，帮助您惊人地创建一个 React 应用程序。这可能听起来像是您不需要太多帮助的事情，但当您使用这个工具时，您不再需要考虑很多配置。在本章中，您将学习：

+   在系统上安装*Create React App*工具

+   引导创建您的 React 应用程序

+   创建新应用程序时安装了哪些包

+   应用程序的目录组织和文件

# 安装 Create React App

第一步是安装*Create React App*，这是一个 npm 包：`create-react-app`。这个包应该全局安装，因为它在您的系统上安装了一个用于创建 React 项目的命令。换句话说，`create-react-app`实际上并不是您的 React 项目的一部分，它用于初始化您的 React 项目。

以下是您可以全局安装*Create React App*的方法：

```jsx
$ npm install -g create-react-app
```

注意命令中的`-g`标志—这确保`create-react-app`命令被全局安装。安装完成后，您可以通过运行以下命令来确保该命令可以正常运行：

```jsx
$ create-react-app -V

> 1.4.1 
```

现在，您已经准备好使用这个工具来创建您的第一个 React 应用程序了！

# 创建您的第一个应用程序

我们将在本章的剩余部分使用*Create React App*创建您的第一个 React 应用程序。别担心，这很容易做到，所以这将是一个简短的章节。*Create React App*的目标是尽快开始为您的应用程序构建功能。如果您花费时间配置系统，就无法做到这一点。

*Create React App*提供了所谓的**零配置应用程序**。这意味着我们提供应用程序的名称，然后它将安装我们需要的依赖项，并为我们创建样板目录结构和文件。让我们开始吧。

# 指定项目名称

您需要向*Create React App*提供的唯一配置值是名称，以便它可以引导您的项目。这作为参数传递给`create-react-app`命令：

```jsx
$ create-react-app my-react-app
```

如果当前目录中不存在`my-react-app`目录，它将在其中创建一个，如果已经存在，则将使用该目录。这是你将找到与你的应用程序有关的一切。一旦目录创建完成，它将安装包依赖项并创建项目目录和文件。这是`create-react-app`命令输出的缩短版本可能看起来像：

```jsx
Creating a new React app in 02/my-react-app.

Installing packages. This might take a couple of minutes.
Installing react, react-dom, and react-scripts...

+ react-dom@16.0.0
+ react@16.0.0
+ react-scripts@1.0.14
added 1272 packages in 57.831s

Success! Created my-react-app at 02/my-react-app
Inside that directory, you can run several commands:

  npm start
    Starts the development server.

  npm run build
    Bundles the app into static files for production.

  npm test
    Starts the test runner.

  npm run eject
    Removes this tool and copies build dependencies,
    configuration files and scripts into the app directory.
    If you do this, you can't go back!

We suggest that you begin by typing:

  cd my-react-app
  npm start

Happy hacking!
```

这个输出向你展示了一些有趣的东西。首先，它显示了安装了哪些东西。其次，它向你展示了在你的项目中可以运行的命令。你将在本书的后续章节中学习如何使用这些命令。现在，让我们看看你刚刚创建的项目，并看看它包含了什么。

# 自动依赖处理

接下来，让我们看看在引导过程中安装的依赖项。你可以通过运行`npm ls --depth=0`来列出你的项目包。`--depth=0`选项意味着你只想看到顶层依赖项：

```jsx
├── react@16.0.0 
├── react-dom@16.0.0 
└── react-scripts@1.0.14 
```

这里没有太多东西，只有你需要的两个核心 React 库，还有一个叫做`react-scripts`的东西。后者包含了你想要在这个项目中运行的脚本，比如启动开发服务器和生成生产版本。

接下来，让我们看看*Create React App*创建的`package.json`文件：

```jsx
{ 
  "name": "my-react-app", 
  "version": "0.1.0", 
  "private": true, 
  "dependencies": { 
    "react": "¹⁶.0.0", 
    "react-dom": "¹⁶.0.0", 
    "react-scripts": "1.0.14" 
  }, 
  "scripts": { 
    "start": "react-scripts start", 
    "build": "react-scripts build", 
    "test": "react-scripts test --env=jsdom", 
    "eject": "react-scripts eject" 
  } 
} 
```

这里是跟踪依赖关系的地方，这样你就可以在没有*Create React App*的不同机器上安装你的应用程序。你可以看到`dependencies`部分与`npm ls --depth=0`命令的输出相匹配。`scripts`部分指定了在这个项目中可用的命令。这些都是`react-scripts`命令——`react-scripts`被安装为一个依赖项。

*Create React App*的一个更强大的方面是，它为你简化了`package.json`的配置。你不再需要维护几十个依赖项，而是只有少数几个依赖项。`react-scripts`包为你处理了动态配置方面。

例如，当您运行 React 开发服务器时，通常需要花费大量时间来处理 Webpack 配置，并确保适当的 Babel 插件已安装。由于`react-scripts`会动态创建这些内容的标准配置，您就不必担心了。相反，您可以立即开始编写应用程序代码。

`react-scripts`包还处理了许多通常需要自己处理的依赖关系。您可以使用`npm ls --depth=1`来了解这个包为您处理了哪些依赖关系：

```jsx
└─┬ react-scripts@1.0.14 
     ├── autoprefixer@7.1.2 
     ├── babel-core@6.25.0 
     ├── babel-eslint@7.2.3 
     ├── babel-jest@20.0.3 
     ├── babel-loader@7.1.1 
     ├── babel-preset-react-app@3.0.3 
     ├── babel-runtime@6.26.0 
     ├── case-sensitive-paths-webpack-plugin@2.1.1 
     ├── chalk@1.1.3 
     ├── css-loader@0.28.4 
     ├── dotenv@4.0.0 
     ├── eslint@4.4.1 
     ├── eslint-config-react-app@2.0.1 
     ├── eslint-loader@1.9.0 
     ├── eslint-plugin-flowtype@2.35.0 
     ├── eslint-plugin-import@2.7.0 
     ├── eslint-plugin-jsx-a11y@5.1.1 
     ├── eslint-plugin-react@7.1.0 
     ├── extract-text-webpack-plugin@3.0.0 
     ├── file-loader@0.11.2 
     ├── fs-extra@3.0.1 
     ├── fsevents@1.1.2 
     ├── html-webpack-plugin@2.29.0 
     ├── jest@20.0.4 
     ├── object-assign@4.1.1 deduped 
     ├── postcss-flexbugs-fixes@3.2.0 
     ├── postcss-loader@2.0.6 
     ├── promise@8.0.1 
     ├── react-dev-utils@4.1.0 
     ├── style-loader@0.18.2
```

```jsx
 ├── sw-precache-webpack-plugin@0.11.4 
     ├── url-loader@0.5.9 
     ├── webpack@3.5.1 
     ├── webpack-dev-server@2.8.2 
     ├── webpack-manifest-plugin@1.2.1 
     └── whatwg-fetch@2.0.3 
```

通常，您不会在应用程序代码中与大多数这些包进行交互。当您不得不积极管理自己没有直接使用的依赖关系时，会感觉像是在浪费大量时间。*Create React App*有助于消除这种感觉。

# 目录结构

到目前为止，您已经了解了在使用*Create React App*创建项目时作为其一部分安装的依赖关系。除了依赖关系外，*Create React App*还设置了一些其他样板文件和目录。让我们快速地过一遍这些，这样您就可以在下一章开始编码了。

# 顶层文件

在您的应用程序的顶层只创建了两个文件，您需要关注：

+   `README.md`：这个 Markdown 文件用于描述项目。如果您计划将您的应用程序作为 GitHub 项目，这是一个很好的地方来解释您的项目存在的原因以及人们如何开始使用它。

+   `package.json`：这个文件用于配置分发您的应用程序作为 npm 包的所有方面。例如，这是您可以添加新依赖项或删除过时依赖项的地方。如果您计划将您的应用程序发布到主 npm 注册表，这个文件就非常重要。

# 静态资产

*Create React App*为您创建了一个 public 目录，并在其中放置了一些文件。这是静态应用程序资产的存放位置。默认情况下，它包含以下内容：

+   `favion.ico`：这是在浏览器标签中显示的 React 标志。在发布之前，您会希望用代表您的应用程序的东西替换它。

+   `index.html`：这是提供给浏览器的 HTML 文件，也是您的 React 应用程序的入口点。

+   `manifest.json`：当应用程序添加到主屏幕时，一些移动操作系统会使用这个文件。

# 源代码

`src`目录是由`create-react-app`创建的应用程序中最重要的部分。这是你创建的任何 React 组件的所在地。默认情况下，这个目录中有一些源文件，可以让你开始，尽管随着你的进展，你显然会替换大部分文件。以下是默认情况下你会找到的内容：

+   `App.css`：这定义了一些简单的 CSS 来为`App`组件设置样式

+   `App.js`：这是渲染应用程序 HTML 的默认组件

+   `App.test.js`：这是`App`组件的基本测试

+   `index.css`：这定义了应用程序范围的样式

+   `index.js`：这是你的应用程序的入口点—渲染`App`组件

+   `logo.svg`：一个由`App`组件渲染的动画 React 标志

+   `registerServiceWorker.js`：在生产构建中，这将使组件从离线缓存中加载

有这些默认源文件为你创建有两个好处。首先，你可以快速启动应用程序，确保一切正常运行，而且你没有犯任何基本错误。其次，它为你的组件设定了一个基本模式。在本书中，你将看到如何将模式应用到组件实际上有助于工具化。

# 概要

在本章中，你学会了如何在你的系统上安装*Create React App*工具。*Create React App*是启动现代 React 应用程序的首选工具。*Create React App*的目标是让开发人员在最短的时间内从零开始创建 React 组件。

安装了这个工具后，你使用它创建了你的第一个 React 应用程序。你需要提供的唯一配置是应用程序名称。一旦工具完成安装依赖项并创建样板文件和目录，你就可以开始编写代码了。

然后，我们看了`react-scripts`和这个包所处理的依赖项。然后，你被带领快速浏览了为你创建的应用程序的整体结构。

在接下来的章节中，我们将开始开发一些 React 组件。为此，我们将启动开发服务器。你还将学习如何使用`create-react-app`开发环境快速上手。
