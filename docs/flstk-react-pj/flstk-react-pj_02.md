# 第二章：准备开发环境

在使用 MERN 堆栈构建应用程序之前，我们首先需要准备每种技术的开发环境，以及用于辅助开发和调试的工具。本章将指导您了解工作空间选项、基本开发工具、如何在工作空间中设置 MERN 技术以及检查此设置的实际代码步骤。

我们将涵盖以下主题：

+   工作空间选项

+   代码编辑器

+   Chrome 开发者工具

+   Git 设置

+   MongoDB 设置

+   Node 设置

+   npm 模块以完成 MERN 堆栈

+   用于检查 MERN 设置的代码

# 选择开发工具

在选择基本开发工具（如文本编辑器或 IDE、版本控制软件甚至开发工作空间本身）时有很多选择。在本节中，我们将介绍与 MERN Web 开发相关的选项和建议，以便您可以根据个人偏好做出明智的决定。

# 工作空间选项

在本地计算机上开发是程序员中最常见的做法，但随着诸如 Cloud9（[`aws.amazon.com/cloud9/?origin=c9io`](https://aws.amazon.com/cloud9/?origin=c9io)）等优秀的云开发服务的出现，现在可以同时使用本地和云端。您可以使用 MERN 技术设置本地工作空间，并且在本书的其余部分将假定为这种情况，但您也可以选择在配备了 Node 开发的云服务中运行和开发代码。

# 本地和云开发

您可以选择同时使用这两种类型的工作空间，以享受在本地工作的好处，而不必担心带宽/互联网问题，并在没有您喜爱的本地计算机时远程工作。为此，您可以使用 Git 对代码进行版本控制，将最新代码存储在 GitHub 或 BitBucket 等远程 Git 托管服务上，然后在所有工作空间中共享相同的代码。

# IDE 或文本编辑器

大多数云开发环境都将集成源代码编辑器。但是对于您的本地工作空间，您可以根据自己作为程序员的偏好选择任何编辑器，然后为 MERN 开发进行自定义。例如，以下流行选项都可以根据需要进行自定义：

+   **Atom**（[`atom.io/`](https://atom.io/)）：GitHub 的免费开源文本编辑器，有许多其他开发人员提供的与 MERN 堆栈相关的包可用

+   SublimeText（https://www.sublimetext.com/）：一款专有的跨平台文本编辑器，还有许多与 MERN 堆栈相关的软件包可用，支持 JavaScript 开发

+   Visual Studio Code（https://code.visualstudio.com/）：微软开发的功能丰富的源代码编辑器，广泛支持现代 Web 应用程序开发工作流程，包括对 MERN 堆栈技术的支持

+   WebStorm（https://www.jetbrains.com/webstorm/）：由 JetBrains 开发的全功能 JavaScript IDE，支持基于 MERN 堆栈的开发

# Chrome 开发者工具

加载、查看和调试前端是 Web 开发过程中非常关键的一部分。Chrome 开发者工具是 Chrome 浏览器的一部分，具有许多出色的功能，允许调试、测试和实验前端代码，以及 UI 的外观、响应和性能。此外，React 开发者工具扩展可作为 Chrome 插件使用，并将 React 调试工具添加到 Chrome 开发者工具中。

# Git

任何开发工作流程都不完整，如果没有版本控制系统来跟踪代码更改、共享代码和协作。多年来，Git 已成为许多开发人员的事实标准版本控制系统，并且是最广泛使用的分布式源代码管理工具。在本书中，Git 将主要帮助跟踪进度，因为我们逐步构建每个应用程序。

# 安装

要开始使用 Git，首先根据您的系统规格在本地计算机或基于云的开发环境上安装它。有关下载和安装最新 Git 的相关说明，以及使用 Git 命令的文档，可在以下网址找到：https://git-scm.com/downloads。

# 远程 Git 托管服务

基于云的 Git 存储库托管服务，如 GitHub 和 BitBucket，有助于在工作空间和部署环境之间共享最新的代码，并备份代码。这些服务提供了许多有用的功能，以帮助代码管理和开发工作流程。要开始使用，您可以创建一个帐户，并为您的代码库设置远程存储库。

所有这些基本工具将丰富您的 Web 开发工作流程，并在您完成工作区的必要设置并开始构建 MERN 应用程序后提高生产力。

# 设置 MERN 技术栈

MERN 技术栈正在开发和升级，因此在撰写本书时，我们使用的是最新的稳定版本。大多数这些技术的安装指南取决于工作区的系统环境，因此本节指向所有相关的安装资源，并且也作为设置完全功能的 MERN 技术栈的指南。

# MongoDB

在向 MERN 应用程序添加任何数据库功能之前，必须在开发环境中设置并运行 MongoDB。在撰写本文时，MongoDB 的当前稳定版本是 3.6.3，本书中用于开发应用程序的是 MongoDB Community Edition 的这个版本。本节的其余部分提供了有关如何安装和运行 MongoDB 的资源。

# 安装

您需要在工作区安装并启动 MongoDB，以便在开发中使用它。MongoDB 的安装和启动过程取决于工作区的规格：

+   云开发服务将有其自己的安装和设置 MongoDB 的说明。例如，Cloud9 的操作步骤可以在此找到：[`community.c9.io/t/setting-up-mongodb/1717`](https://community.c9.io/t/setting-up-mongodb/1717)。

+   在本地机器上安装的指南详见：[`docs.mongodb.com/manual/installation/`](https://docs.mongodb.com/manual/installation/)。

# 运行 mongo shell

*mongo* shell 是 MongoDB 的交互式工具，是熟悉 MongoDB 操作的好地方。一旦安装并运行了 MongoDB，您可以在命令行上运行 *mongo* shell。在 *mongo* shell 中，您可以尝试查询和更新数据以及执行管理操作的命令。

# Node

MERN 应用程序的后端服务器实现依赖于 Node 和 npm。在撰写本文时，8.11.1 是最新的稳定 Node 版本，并且附带 npm 版本 5.6.0。然而，npm 的最新版本是 5.8.0，因此在安装 Node 后，需要根据下一节的讨论升级 npm。

# 安装

Node 可以通过直接下载、安装程序或 Node 版本管理器进行安装。

+   您可以通过直接下载源代码或针对您的工作平台特定的预构建安装程序来安装 Node。下载地址为[nodejs.org/en/download](https://nodejs.org/en/download/)。

+   云开发服务可能已经预装了 Node，比如 Cloud9，或者会有特定的添加和更新 Node 的说明。

要测试安装是否成功，可以打开命令行并运行`node -v`来查看它是否正确返回版本号。

# 升级 npm 版本

为了安装 npm 版本 5.8.0，可以从命令行运行以下安装命令，并使用**`npm -v`**检查版本：

```jsx
npm install -g npm@5.8.0 
npm -v
```

# 使用 nvm 进行 Node 版本管理

如果您需要为不同的项目维护多个 Node 和 npm 版本，nvm 是一个有用的命令行工具，可以在同一工作空间中安装和管理不同的版本。您必须单独安装 nvm。设置说明可以在[github.com/creationix/nvm](https://github.com/creationix/nvm)找到。

# MERN 的 npm 模块

其余的 MERN 堆栈技术都可以作为 npm 模块使用，并且可以通过`npm install`添加到每个项目中。这些包括关键模块，如 React 和 Express，这些模块是运行每个 MERN 应用程序所必需的，还有在开发过程中将需要的模块。在本节中，我们列出并讨论这些模块，然后在下一节中看如何在一个工作项目中使用这些模块。

# 关键模块

为了集成 MERN 堆栈技术并运行您的应用程序，我们将需要以下 npm 模块：

+   **React**：要开始使用 React，我们将需要两个模块：

+   `react`

+   `react-dom`

+   **Express**：要在代码中使用 Express，您需要`express`模块

+   **MongoDB**：要在 Node 应用程序中使用 MongoDB，还需要添加驱动程序，该驱动程序可作为名为`mongodb`的 npm 模块使用

# devDependency 模块

为了在 MERN 应用程序的开发过程中保持一致性，我们将在整个堆栈中使用 JavaScript ES6。因此，为了辅助开发过程，我们将使用以下额外的 npm 模块来编译和捆绑代码，并在开发过程中更新代码时自动重新加载服务器和浏览器应用程序：

+   Babel 模块用于将 ES6 和 JSX 转换为适合所有浏览器的 JavaScript。需要的模块来使 Babel 工作的有：

+   `babel-core`

+   `babel-loader`用于使用 Webpack 转换 JavaScript 文件

+   `babel-preset-env`，`babel-preset-react`和`babel-preset-stage-2`用于支持 React，最新的 JS 功能以及一些 stage-x 功能，例如声明目前未在`babel-preset-env`下覆盖的类字段

+   Webpack 模块将帮助捆绑编译后的 JavaScript，用于客户端和服务器端代码。需要使 Webpack 工作的模块有：

+   `webpack`

+   `webpack-cli`用于运行 Webpack 命令

+   `webpack-node-externals`在 Webpack 打包时忽略外部 Node 模块文件

+   `webpack-dev-middleware`在开发过程中通过连接的服务器提供从 Webpack 发出的文件

+   `webpack-hot-middleware`将热模块重新加载添加到现有服务器中，通过将浏览器客户端连接到 Webpack 服务器，并在开发过程中接收代码更改的更新

+   `nodemon`在开发过程中监视服务器端的更改，以便重新加载服务器以使更改生效。

+   `react-hot-loader`用于加快客户端的开发。每当 React 前端中的文件更改时，`react-hot-loader`使浏览器应用程序能够在不重新捆绑整个前端代码的情况下更新。

尽管`react-hot-loader`旨在帮助开发流程，但安装此模块作为常规依赖项而不是 devDependency 是安全的。它会自动确保在生产中禁用热重新加载，并且占用空间很小。

# 检查您的开发设置

在这一部分，我们将逐步进行开发工作流程，并编写代码，以确保环境正确设置以开始开发和运行 MERN 应用程序。

我们将在以下文件夹结构中生成这些项目文件以运行一个简单的设置项目：

```jsx
| mern-simplesetup/
  | -- client/
    | --- HelloWorld.js
    | --- main.js
  | -- server/
    | --- devBundle.js
    | --- server.js
  | -- .babelrc
  | -- nodemon.json
  | -- package.json
  | -- template.js
  | -- webpack.config.client.js
  | -- webpack.config.client.production.js
  | -- webpack.config.server.js
```

本节讨论的代码可在 GitHub 的存储库中找到：[github.com/shamahoque/mern-simplesetup](https://github.com/shamahoque/mern-simplesetup)。您可以克隆此代码，并在本章的其余部分中阅读代码解释时运行它。

# 初始化 package.json 并安装 npm 模块

我们将首先使用 npm 安装所有必需的模块。在每个项目文件夹中添加`package.json`文件以维护、记录和共享 MERN 应用程序中使用的 npm 模块是最佳实践。`package.json`文件将包含有关应用程序的元信息，以及列出模块依赖项。

按照以下步骤生成`package.json`文件，修改它，并用它来安装 npm 模块：

+   `npm init`: 从命令行进入项目文件夹，运行`npm init`。您将被问及一系列问题，然后将自动生成一个`package.json`文件，其中包含您的答案。

+   `dependencies`: 在编辑器中打开`package.json`，修改 JSON 对象，添加关键模块和`react-hot-loader`作为常规的`dependencies`。

在代码块之前提到的文件路径表示项目目录中代码的位置。本书始终遵循这一约定，以提供更好的上下文和指导，让您能够跟着代码进行学习。

`mern-simplesetup/package.json`:

```jsx
"dependencies": {
   "express": "⁴.16.3",
    "mongodb": "³.0.7",
    "react": "¹⁶.3.2",
    "react-dom": "¹⁶.3.2",
    "react-hot-loader": "⁴.1.2"
}
```

+   `devDependencies`: 进一步修改`package.json`，添加以下在开发过程中所需的 npm 模块作为`devDependencies`。

`mern-simplesetup/package.json`:

```jsx
"devDependencies": {
    "babel-core": "⁶.26.2",
    "babel-loader": "⁷.1.4",
    "babel-preset-env": "¹.6.1",
    "babel-preset-react": "⁶.24.1",
    "babel-preset-stage-2": "⁶.24.1",
    "nodemon": "¹.17.3",
    "webpack": "⁴.6.0",
    "webpack-cli": "².0.15",
    "webpack-dev-middleware": "³.1.2",
    "webpack-hot-middleware": "².22.1",
    "webpack-node-externals": "¹.7.2"
}
```

+   `npm install`: 保存`package.json`，然后从命令行运行`npm install`，以获取并添加所有这些模块到您的项目中。

# 配置 Babel、Webpack 和 Nodemon

在我们开始编写 Web 应用程序之前，我们需要配置 Babel、Webpack 和 Nodemon，在开发过程中编译、打包和自动重新加载代码更改。

# Babel

在项目文件夹中创建一个`.babelrc`文件，并添加以下 JSON，其中指定了`presets`和`plugins`。

`mern-simplesetup/.babelrc`:

```jsx
{
    "presets": [
      "env",
      "stage-2"
      "react"
    ],
    "plugins": [
      "react-hot-loader/babel"
    ]
}
```

`react-hot-loader/babel`插件是由`react-hot-loader`模块需要编译`React`组件。

# Webpack

我们将不得不为捆绑客户端和服务器端代码以及生产代码分别配置 Webpack。在项目文件夹中创建`webpack.config.client.js`、`webpack.config.server.js`和`webpack.config.client.production.js`文件。所有三个文件都将具有以下代码结构：

```jsx
const path = require('path')
const webpack = require('webpack')
const CURRENT_WORKING_DIR = process.cwd()

const config = { ... }

module.exports = config
```

`config` JSON 对象的值将根据客户端或服务器端代码以及开发与生产代码而有所不同。

# 用于开发的客户端 Webpack 配置

在您的`webpack.config.client.js`文件中更新`config`对象如下，以配置 Webpack 在开发过程中捆绑和热加载 React 代码。

`mern-simplesetup/webpack.config.client.js`:

```jsx
const config = {
    name: "browser",
    mode: "development",
    devtool: 'eval-source-map',
    entry: [
        'react-hot-loader/patch',
        'webpack-hot-middleware/client?reload=true',
        path.join(CURRENT_WORKING_DIR, 'client/main.js')
    ],
    output: {
        path: path.join(CURRENT_WORKING_DIR , '/dist'),
        filename: 'bundle.js',
        publicPath: '/dist/'
    },
    module: {
        rules: [
            {
                test: /\.jsx?$/,
                exclude: /node_modules/,
                use: [
                    'babel-loader'
                ]
            }
        ]
    }, plugins: [
          new webpack.HotModuleReplacementPlugin(),
          new webpack.NoEmitOnErrorsPlugin()
      ]
}
```

+   `mode`将`process.env.NODE_ENV`设置为给定值，并告诉 Webpack 相应地使用其内置的优化。如果没有明确设置，它默认为值`'production'`。也可以通过命令行通过将值作为 CLI 参数传递来设置。

+   `devtool`指定了如何生成源映射，如果有的话。通常，源映射提供了一种将压缩文件中的代码映射回源文件中的原始位置以帮助调试的方法。

+   `entry`指定了 Webpack 开始打包的入口文件，在这种情况下是`client`文件夹中的`main.js`文件。

+   `output`指定了打包代码的输出路径，在这种情况下设置为`dist/bundle.js`。

+   `publicPath`允许指定应用程序中所有资产的基本路径。

+   `module`设置了用于转译的文件扩展名的正则规则，以及要排除的文件夹。这里要使用的转译工具是`babel-loader`。

+   `HotModuleReplacementPlugin`启用了`react-hot-loader`的热模块替换。

+   `NoEmitOnErrorsPlugin`允许在编译错误时跳过输出。

# 服务器端 Webpack 配置

修改代码以要求`nodeExternals`，并在`webpack.config.server.js`文件中更新`config`对象以配置 Webpack 用于打包服务器端代码。

`mern-simplesetup/webpack.config.server.js`：

```jsx
const config = {
    name: "server",
    entry: [ path.join(CURRENT_WORKING_DIR , './server/server.js') ],
    target: "node",
    output: {
        path: path.join(CURRENT_WORKING_DIR , '/dist/'),
        filename: "server.generated.js",
        publicPath: '/dist/',
        libraryTarget: "commonjs2"
    },
    externals: [nodeExternals()],
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: [ 'babel-loader' ]
            }
        ]
    }
}
```

`mode`选项在这里没有明确设置，但在运行 Webpack 命令时，将根据开发或生产的需要进行传递。

Webpack 从`server.js`文件夹开始打包，然后将打包后的代码输出到`dist`文件夹中的`server.generated.js`文件中。

# 用于生产的客户端 Webpack 配置

为了准备客户端代码用于生产，更新`webpack.config.client.production.js`文件中的`config`对象与以下代码。

`mern-simplesetup/webpack.config.client.production.js`：

```jsx
const config = {
    mode: "production",
    entry: [
        path.join(CURRENT_WORKING_DIR, 'client/main.js')
    ],
    output: {
        path: path.join(CURRENT_WORKING_DIR , '/dist'),
        filename: 'bundle.js',
        publicPath: "/dist/"
    },
    module: {
        rules: [
            {
                test: /\.jsx?$/,
                exclude: /node_modules/,
                use: [
                    'babel-loader'
                ]
            }
        ]
    }
}
```

这将配置 Webpack 用于打包用于生产模式的 React 代码，其中将不再需要热重载插件或调试配置。

# Nodemon

在项目文件夹中创建一个`nodemon.js`文件，并添加以下配置。

`mern-simplesetup/nodemon.js`

```jsx
{
    "verbose": false,
    "watch": [ "./server" ],
    "exec": "webpack --mode=development --config 
    webpack.config.server.js 
                && node ./dist/server.generated.js"
}
```

这个配置将设置`nodemon`在开发过程中监视服务器文件的更改，然后根据需要执行编译和构建命令。

# 使用 React 的前端视图

为了开始开发前端，首先在项目文件夹中创建一个名为`template.js`的根模板文件，它将使用`React`组件来渲染 HTML。

`mern-simplesetup/template.js`:

```jsx
export default () => {
    return `<!doctype html>
      <html lang="en">
        <head>
          <meta charset="utf-8">
          <title>MERN Kickstart</title>
        </head>
        <body>
          <div id="root"></div>
          <script type="text/javascript" src="/dist/bundle.js"> 
       </script>
        </body>
      </html>` 
} 
```

当服务器收到对根 URL 的请求时，这个 HTML 模板将在浏览器中被渲染，ID 为`"root"`的`div`元素将包含我们的`React`组件。

接下来，创建一个`client`文件夹，我们将在其中添加两个 React 文件，`main.js`和`HelloWorld.js`。

`main.js`文件简单地在 HTML 文档的`div`元素中渲染顶层入口`React`组件。

`mern-simplesetup/client/main.js`:

```jsx
import React from 'react'
import { render } from 'react-dom'
import HelloWorld from './HelloWorld'

render(<HelloWorld/>, document.getElementById('root'))
```

在这种情况下，入口`React`组件是从`HelloWorld.js`导入的`HelloWorld`组件。

`HelloWorld.js`包含一个基本的`HelloWorld`组件，它被热导出以在开发过程中使用`react-hot-loader`进行热重载。

`mern-simplesetup/client/HelloWorld.js`:

```jsx
import React, { Component } from 'react'
import { hot } from 'react-hot-loader'

class HelloWorld extends Component {
   render() {
     return (
         <div>
             <h1>Hello World!</h1>
         </div>
     ) 
   }
}

export default hot(module)(HelloWorld)
```

为了在服务器收到对根 URL 的请求时在浏览器中看到`React`组件被渲染，我们需要使用 Webpack 和 Babel 设置来编译和打包这段代码，并添加服务器端代码来响应根路由请求并返回打包后的代码。

# 使用 Express 和 Node 构建服务器

在项目文件夹中，创建一个名为`server`的文件夹，并添加一个名为`server.js`的文件来设置服务器。然后，添加另一个名为`devBundle.js`的文件，它将在开发模式下使用 Webpack 配置来编译 React 代码。

# Express 应用程序

在`server.js`中，我们首先将添加代码来导入`express`模块，以初始化一个 Express 应用程序。

`mern-simplesetup/server/server.js`:

```jsx
import express from 'express'

const app = express()
```

然后我们将使用这个 Express 应用程序来构建出 Node 服务器应用程序的其余部分。

# 在开发过程中打包 React 应用程序

为了保持开发流程简单，我们将初始化 Webpack 来在运行服务器时编译客户端代码。在`devBundle.js`中，我们将设置一个编译方法，它接受 Express 应用程序并配置它来使用 Webpack 中间件来编译、打包和提供代码，以及在开发模式下启用热重载。

`mern-simplesetup/server/devBundle.js`:

```jsx
import webpack from 'webpack'
import webpackMiddleware from 'webpack-dev-middleware'
import webpackHotMiddleware from 'webpack-hot-middleware'
import webpackConfig from './../webpack.config.client.js'

const compile = (app) => {
  if(process.env.NODE_ENV == "development"){
    const compiler = webpack(webpackConfig)
    const middleware = webpackMiddleware(compiler, {
      publicPath: webpackConfig.output.publicPath
    })
    app.use(middleware)
    app.use(webpackHotMiddleware(compiler))
  }
}

export default {
  compile
}
```

我们将在开发模式下通过在`server.js`中添加以下行来调用这个编译方法。

`mern-simplesetup/server/server.js`:

```jsx
**import devBundle from './devBundle'**
const app = express()
**devBundle.compile(app)** 
```

这两行突出显示的代码仅用于开发模式，在构建应用程序代码以进行生产时应将其注释掉。在开发模式下，当执行这些行时，Webpack 将编译和捆绑 React 代码并将其放置在`dist/bundle.js`中。

# 从 dist 文件夹中提供静态文件

Webpack 将在开发模式和生产模式下编译客户端代码，然后将捆绑文件放置在`dist`文件夹中。为了使这些静态文件在客户端请求时可用，我们将在`server.js`中添加以下代码来从`dist/folder`中提供静态文件。

`mern-simplesetup/server/server.js`：

```jsx
import path from 'path'
const CURRENT_WORKING_DIR = process.cwd()
app.use('/dist', express.static(path.join(CURRENT_WORKING_DIR, 'dist')))
```

# 在根目录渲染模板

当服务器在根 URL `/` 处收到请求时，我们将在浏览器中呈现`template.js`。在`server.js`中，向 Express 应用程序添加以下路由处理代码，以接收在`/`处的 GET 请求。

`mern-simplesetup/server/server.js`：

```jsx
import template from './../template'
app.get('/', (req, res) => {
     res.status(200).send(template())
})
```

最后，添加服务器代码以侦听指定端口的传入请求。

`mern-simplesetup/server/server.js`：

```jsx
let port = process.env.PORT || 3000
app.listen(port, function onStart(err) {
  if (err) {
    console.log(err) 
  }
  console.info('Server started on port %s.', port)
})
```

# 将服务器连接到 MongoDB

要将 Node 服务器连接到 MongoDB，请在`server.js`中添加以下代码，并确保您的工作区中正在运行 MongoDB。

`mern-simplesetup/server/server.js`：

```jsx
import { MongoClient } from 'mongodb'
const url = process.env.MONGODB_URI || 'mongodb://localhost:27017/mernSimpleSetup'
MongoClient.connect(url, (err, db)=>{
  console.log("Connected successfully to mongodb server")
  db.close()
})
```

在此代码示例中，`MongoClient`是连接到运行中的`MongoDB`实例的驱动程序，使用其`url`，并允许我们在后端实现与数据库相关的代码。

# 运行 npm 脚本

更新`package.json`文件，添加以下 npm 运行脚本以进行开发和生产。

`mern-simplesetup/package.json`：

```jsx
"scripts": {
    "development": "nodemon",
    "build": "webpack --config webpack.config.client.production.js 
```

```jsx
                 && webpack --mode=production --config 
     webpack.config.server.js",
    "start": "NODE_ENV=production node ./dist/server.generated.js"
}
```

+   `npm run development`：此命令将启动 Nodemon、Webpack 和服务器以进行开发

+   `npm run build`：这将为生产模式生成客户端和服务器代码包（在运行此脚本之前，请确保从`server.js`中删除`devBundle.compile`代码）

+   `npm run start`：此命令将在生产环境中运行捆绑代码

# 实时开发和调试

要运行到目前为止开发的代码，并确保一切正常运行，可以按照以下步骤进行：

1.  **从命令行运行应用程序**：`npm run development`。

1.  **在浏览器中加载**：在浏览器中打开根 URL，即`http://localhost:3000`，如果您正在使用本地机器设置。您应该看到一个标题为 MERN Kickstart 的页面，上面只显示 Hello World!。

1.  开发代码并调试实时更改：将`HelloWorld.js`组件文本中的`'Hello World!'`更改为`'hello'`。保存更改以在浏览器中看到即时更新，并检查命令行输出以查看`bundle.js`是否未重新创建。类似地，当您更改服务器端代码时，您也可以看到即时更新，从而提高开发效率。

如果您已经走到了这一步，恭喜您，您已经准备好开始开发令人兴奋的 MERN 应用程序了。

# 总结

在本章中，我们讨论了开发工具选项以及如何安装 MERN 技术，然后编写了代码来检查开发环境是否设置正确。

我们首先看了推荐的工作区、IDE、版本控制软件和适用于 Web 开发的浏览器选项。您可以根据自己作为开发人员的偏好从这些选项中进行选择。

接下来，我们首先安装 MongoDB、Node 和 npm，然后使用 npm 添加其余所需的库，从而设置了 MERN 堆栈技术。

在继续编写代码以检查此设置之前，我们配置了 Webpack 和 Babel 以在开发期间编译和捆绑代码，并构建生产就绪的代码。我们了解到，在在浏览器上打开 MERN 应用程序之前，有必要编译用于开发 MERN 应用程序的 ES6 和 JSX 代码。

此外，我们通过为前端开发包括 React Hot Loader，为后端开发配置 Nodemon，并在开发期间运行服务器时编译客户端和服务器端代码的方式，使开发流程更加高效。

在下一章中，我们将使用此设置开始构建一个骨架 MERN 应用程序，该应用程序将作为功能齐全应用程序的基础。
