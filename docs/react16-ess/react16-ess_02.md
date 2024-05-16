# 第二章：为您的项目安装强大的工具

这里有一句查尔斯·F·凯特林的名言：

> “我对未来感兴趣，因为我将在那里度过余生。”

这位杰出的发明家在我们甚至开始思考如何编写软件之前就给软件工程师留下了最重要的建议。然而，半个世纪后，我们仍在弄清楚为什么最终会得到意大利面代码或“意大利面心智模型”。

你是否曾经处于这样一种情况：你继承了前任开发者的代码，并花费了数周的时间试图理解一切是如何工作的，因为没有提供蓝图，而伪自解释的代码变得太难以调试？更糟糕的是，项目不断增长，复杂性也在增加。做出改变或破坏性的改变是危险的，没有人愿意去碰那些“丑陋”的遗留代码。重写整个代码库成本太高，因此目前的代码通过引入新的错误修复和补丁来支持。维护软件的成本远高于最初开发的成本。

写软件是为了未来而今天就开始。我认为关键在于创建一个简单的心智模型，无论项目在未来变得多么庞大，它都不会改变。当项目规模增长时，复杂性始终保持不变。这个心智模型就是你的蓝图，一旦你理解了它，你就会明白你的软件是如何工作的。

如果你看一下现代的 Web 开发，特别是前端开发，你会注意到我们生活在激动人心的时代。互联网公司和个人开发者正在解决速度和开发成本与代码和用户体验质量之间的问题。

2013 年，Facebook 发布了 React——一个用于构建用户界面的开源 JavaScript 库。您可以在[`facebook.github.io/react/`](http://facebook.github.io/react/)上阅读更多信息。2015 年初，来自 Facebook 的 Tom Occhino 总结了 React 的强大之处：

> “React 用声明式 API 包装了一个命令式 API。React 的真正力量在于它让你编写代码。”

声明式编程会导致代码量减少。它告诉计算机要做什么，而不指定如何做，而命令式编程风格描述了如何做。JavaScript 调用 DOM API 就是命令式编程的一个例子。jQuery 就是另一个例子。

Facebook 多年来一直在生产中使用 React，还有 Instagram 和其他公司。它也适用于小型项目；这里有一个使用 React 构建的购物清单的示例：[`fedosejev.github.io/shopping-list-react`](http://fedosejev.github.io/shopping-list-react)。我认为 React 是今天开发人员可以使用的构建用户界面的最好的 JavaScript 库之一。

我的目标是让你理解 React 的基本原则。为了实现这一目标，我将逐步向您介绍 React 的一个概念，解释它，并展示您如何应用它。我们将逐步构建一个实时 Web 应用程序，沿途提出重要问题，并讨论 React 为我们提供的解决方案。

您将了解 Flux/Redux 和数据的单向流动。与 Flux/Redux 和 React 一起，我们将创建一个可预测和可管理的代码库，您将能够通过添加新功能来扩展它，而不会增加其复杂性。您的 Web 应用程序的心智模型将保持不变，无论以后添加了多少新功能。

与任何新技术一样，有些东西的工作方式与您习惯的方式非常不同。React 也不例外。事实上，React 的一些核心概念可能看起来违反直觉，引发思考，甚至看起来像是一种倒退。不要草率下结论。正如您所期望的那样，Facebook 的经验丰富的工程师们在构建和使用 React 的过程中进行了大量思考，这些应用程序在业务关键应用中进行了生产。我给你的建议是，在学习 React 的过程中保持开放的心态，我相信在本书结束时，这些新概念将会让你感到很有意义。

加入我一起学习 React，并遵循查尔斯·F·凯特林的建议。让我们照顾好我们的未来！

# 接近我们的项目

我坚信学习新技术的最好动力是一个激发你兴趣、让你迫不及待地想要构建的项目。作为一名经验丰富的开发者，你可能已经构建了许多成功的商业项目，这些项目共享某些产品特性、设计模式，甚至目标受众。在这本书中，我希望你能建立一个感觉焕然一新的项目。一个你在日常工作中很可能不会构建的项目。它必须是一个有趣的尝试，不仅能教育你，还能满足你的好奇心并拓展你的想象力。然而，假设你是一个忙碌的专业人士，这个项目也不应该成为你长时间的、耗时的承诺。

输入**Snapterest**—一个允许你发现和收集 Twitter 上发布的公共照片的网络应用。把它想象成一个 Pinterest（[www.pinterest.com](http://www.pinterest.com)），唯一的图片来源就是 Twitter。我们将实现一个具有以下核心功能的完全功能的网站：

+   实时接收和显示推文

+   向/从收藏中添加和删除推文

+   审查收集的推文

+   将推文收藏导出为可以分享的 HTML 片段

当你开始着手一个新项目时，你要做的第一件事就是准备好你的工具。对于这个项目，我们将使用一些你可能不熟悉的工具，所以让我们讨论一下它们是什么，以及你如何安装和配置它们。

如果你在安装和配置本章中的工具和模块时遇到任何问题，请访问[`github.com/PacktPublishing/React-Essentials-Second-Edition`](https://github.com/PacktPublishing/React-Essentials-Second-Edition)并创建一个新的问题；描述你正在做什么以及你遇到了什么错误消息。我相信我们的社区会帮助你解决问题。

在这本书中，我假设你正在使用 Macintosh 或 Windows 计算机。如果你是 Unix 用户，那么你很可能非常了解你的软件包管理器，并且应该很容易为你安装本章中将要学习的工具。

让我们从安装 Node.js 开始。

# 安装 Node.js 和 npm

**Node.js**是一个平台，允许我们使用我们都熟悉的客户端语言 JavaScript 编写服务器端应用程序。然而，Node.js 的真正好处在于它使用事件驱动的、非阻塞的 I/O 模型，非常适合构建数据密集型、实时应用程序。这意味着使用 Node.js，我们应该能够处理传入的推文流，并在其到达时立即处理它们；这正是我们项目所需要的。

让我们安装 Node.js。我们将使用 8.7.0 版本，因为在撰写本书时，这是 Node.js 的最新版本。Jest 是 Facebook 的一个测试框架，您将在第九章中了解到，*使用 Jest 测试您的 React 应用程序*。

从以下链接之一下载适用于您操作系统的安装包：

+   OS X：[`nodejs.org/dist/v8.7.0/node-v8.7.0.pkg`](http://nodejs.org/dist/v8.7.0/node-v8.7.0.pkg)

+   Windows 64 位：[`nodejs.org/dist/v8.7.0/node-v8.7.0-x64.msi`](http://nodejs.org/dist/v8.7.0/node-v8.7.0-x64.msi)

+   Windows 32 位：[`nodejs.org/dist/v8.7.0/node-v8.7.0-x86.msi`](http://nodejs.org/dist/v8.7.0/node-v8.7.0-x86.msi)

运行下载的安装包，并按照 Node.js 提示的安装步骤进行操作。完成后，检查是否成功安装了 Node.js。打开终端/命令提示符，并键入以下命令：

```jsx
**node -v**

```

以下是输出结果（如果您的版本不完全匹配，不要担心）：

```jsx
**V8.7.0**

```

Node.js 拥有一个非常丰富的模块生态系统，可供我们使用。模块是一个可以在您自己的 Node.js 应用程序中重复使用的 Node.js 应用程序。在撰写本文时，已有超过 50 万个模块。您如何管理这么广泛的 Node.js 模块？认识一下**npm**，这是一个管理 Node.js 模块的包管理器。事实上，npm 与 Node.js 一起发布，因此您已经安装了它。在终端/命令提示符中键入以下内容：

```jsx
**npm -v**

```

您应该看到以下输出（如果您的版本不完全匹配，不要担心）：

```jsx
**5.5.1**

```

您可以在[www.npmjs.com](http://www.npmjs.com)了解更多关于 npm 的信息。现在我们准备开始安装 Node.js 应用程序。

# 安装 Git

在本书中，我们将使用 Git 来安装 Node.js 模块。如果您还没有安装 Git，请访问[`git-scm.com/book/en/v2/Getting-Started-Installing-Git`](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)并按照您的操作系统的安装说明进行安装。

# 从 Twitter Streaming API 获取数据

我们的 React 应用程序的数据将来自 Twitter。Twitter 有一个**Streaming API**，任何人都可以接入并开始以 JSON 格式接收无尽的公共推文流。

要开始使用 Twitter Streaming API，您需要执行以下步骤：

1.  创建一个 Twitter 账户。为此，转到[`twitter.com`](https://twitter.com)并注册；或者如果您已经有账户，请登录。

1.  通过转到[`apps.twitter.com`](https://apps.twitter.com)创建一个新的 Twitter 应用程序，并点击**创建新应用程序**。您需要填写**应用程序详细信息**表格，同意**开发者协议**，然后点击**创建您的 Twitter 应用程序**。现在您应该看到您的应用程序页面。切换到**Keys and Access Tokens**选项卡。

在本页的**应用程序设置**部分，您会找到两个重要的信息：

+   **Consumer Key (API Key)**，例如，`jqRDrAlKQCbCbu2o4iclpnvem`

+   **Consumer Secret (API Secret)**，例如，`wJcdogJih7uLpjzcs2JtAvdSyCVlqHIRUWI70aHOAf7E3wWIgD`

记下这些；我们以后会用到它们。

现在我们需要生成一个访问令牌。在同一页上，您会看到空的**您的访问令牌**部分。点击**创建我的访问令牌**按钮。它会创建两个信息：

+   **Access Token**，例如，`12736172-R017ah2pE2OCtmi46IAE2n0z3u2DV6IqsEcPa0THR`

+   **Access Token Secret**，例如，`4RTJJWIezIDcs5VX1PMVZolXGZG7L3Ez7Iz1gMdZucDaM`

也记下这些。访问令牌是唯一的，您不应该与任何人分享。保持私密。

现在我们已经拥有了开始使用 Twitter 的 Streaming API 所需的一切。

# 使用 Snapkite Engine 过滤数据

通过 Twitter Streaming API 接收的推文数量超过您所能消费的数量，因此我们需要找到一种方法将数据流过滤为一组有意义的推文，以便我们可以显示和交互。我建议您快速查看 Twitter Streaming API 文档，特别是查看描述如何过滤传入流的页面。您会注意到 Twitter 提供的过滤器非常少，因此我们需要找到一种方法进一步过滤数据流。

幸运的是，有一个专门用于此目的的 Node.js 应用程序。它被称为**Snapkite Engine**。它连接到 Twitter Streaming API，使用可用的过滤器进行过滤，并根据您定义的规则输出经过过滤的推文到 Web 套接字连接。我们提出的 React 应用程序可以监听该套接字连接上的事件，并在推文到达时处理推文。

让我们安装 Snapkite Engine。首先，您需要克隆 Snapkite Engine 存储库。克隆意味着您正在将源代码从 GitHub 服务器复制到本地目录。在本书中，我将假设您的本地目录是您的主目录。打开终端/命令提示符并输入以下命令：

```jsx
**cd ~**
**git clone https://github.com/snapkite/snapkite-engine.git**

```

这应该创建`~/snapkite-engine/`文件夹。现在我们将安装`snapkite-engine`依赖的所有其他节点模块。其中之一是`node-gyp`模块。根据您使用的平台，Unix 或 Windows，您将需要安装列在此网页上的其他工具：

安装完毕后，您可以安装`node-gyp`模块：

```jsx
**npm install -g node-gyp**

```

现在导航到`~/snapkite-engine`目录：

```jsx
**cd snapkite-engine/**

```

然后运行以下命令：

```jsx
**npm install**

```

这个命令将安装 Snapkite Engine 依赖的 Node.js 模块。现在让我们配置 Snapkite Engine。假设你在`~/snapkite-engine/`目录中，通过运行以下命令将`./example.config.json`文件复制到`./config.json`：

```jsx
**cp example.config.json config.json**

```

或者，如果您使用 Windows，请运行此命令：

```jsx
**copy example.config.json config.json**

```

在您喜欢的文本编辑器中打开`config.json`。我们现在将编辑配置属性。让我们从`trackKeywords`开始。这是我们将告诉要跟踪哪些关键字的地方。如果我们想跟踪`"my"`关键字，那么设置如下：

```jsx
"trackKeywords": "my"
```

接下来，我们需要设置 Twitter Streaming API 密钥。将`consumerKey`，`consumerSecret`，`accessTokenKey`和`accessTokenSecret`设置为创建 Twitter 应用程序时保存的密钥。其他属性可以设置为它们的默认值。如果你想了解它们是什么，请查看 Snapkite Engine 文档[`github.com/snapkite/snapkite-engine`](https://github.com/snapkite/snapkite-engine)。

我们的下一步是安装 Snapkite 过滤器。**Snapkite Filter**是一个根据一组规则验证推文的 Node.js 模块。有许多 Snapkite 过滤器可供使用，我们可以根据需要使用任意组合来过滤我们的推文流。您可以在[`github.com/snapkite/snapkite-filters`](https://github.com/snapkite/snapkite-filters)找到所有可用的 Snapkite 过滤器的列表。

在我们的应用程序中，我们将使用以下 Snapkite 过滤器：

+   **可能敏感**：[`github.com/snapkite/snapkite-filter-is-possibly-sensitive`](https://github.com/snapkite/snapkite-filter-is-possibly-sensitive)

+   **有移动照片**：[`github.com/snapkite/snapkite-filter-has-mobile-photo`](https://github.com/snapkite/snapkite-filter-has-mobile-photo)

+   **是转推**：[`github.com/snapkite/snapkite-filter-is-retweet`](https://github.com/snapkite/snapkite-filter-is-retweet)

+   **有文本**：[`github.com/snapkite/snapkite-filter-has-text`](https://github.com/snapkite/snapkite-filter-has-text)

让我们安装它们。导航到`~/snapkite-engine/filters/`目录：

```jsx
**cd ~/snapkite-engine/filters/**

```

然后通过运行以下命令克隆所有 Snapkite 过滤器：

```jsx
**git clone https://github.com/snapkite/snapkite-filter-is-possibly-sensitive.git**
**git clone https://github.com/snapkite/snapkite-filter-has-mobile-photo.git**
**git clone https://github.com/snapkite/snapkite-filter-is-retweet.git**
**git clone https://github.com/snapkite/snapkite-filter-has-text.git**

```

下一步是配置它们。为了这样做，您需要为每个 Snapkite 过滤器创建一个**JSON**格式的配置文件，并在其中定义一些属性。幸运的是，每个 Snapkite 过滤器都附带了一个示例配置文件，我们可以根据需要复制和编辑。假设您在`~/snapkite-engine/filters/`目录中，运行以下命令（在 Windows 上使用`copy`并将正斜杠替换为反斜杠）：

```jsx
**cp snapkite-filter-is-possibly-sensitive/example.config.json snapkite-filter-is-possibly-sensitive/config.json**
**cp snapkite-filter-has-mobile-photo/example.config.json snapkite-filter-has-mobile-photo/config.json**
**cp snapkite-filter-is-retweet/example.config.json snapkite-filter-is-retweet/config.json**
**cp snapkite-filter-has-text/example.config.json snapkite-filter-has-text/config.json**

```

我们不需要更改这些`config.json`文件中的任何默认设置，因为它们已经配置好以适应我们的目的。

最后，我们需要告诉 Snapkite Engine 应该使用哪些 Snapkite Filters。在文本编辑器中打开`~/snapkite-engine/config.json`文件，查找这个：

```jsx
"filters": []
```

现在用以下内容替换它：

```jsx
"filters": [
  "snapkite-filter-is-possibly-sensitive",
  "snapkite-filter-has-mobile-photo",
  "snapkite-filter-is-retweet",
  "snapkite-filter-has-text"
]
```

干得好！你已经成功安装了带有多个 Snapkite Filters 的 Snapkite Engine。现在让我们检查一下是否可以运行它。导航到`~/snapkite-engine/`并运行以下命令：

```jsx
**npm start**

```

你应该看不到错误消息，但如果你看到了并且不确定如何解决，那么去[`github.com/fedosejev/react-essentials/issues`](https://github.com/fedosejev/react-essentials/issues)，创建一个新的问题，并复制粘贴你得到的错误消息。

接下来，让我们设置项目的结构。

# 创建项目结构

现在是时候创建我们的项目结构了。组织源文件可能听起来像一个简单的任务，但深思熟虑的项目结构组织帮助我们理解我们应用的基础架构。在本书的后面，当我们谈论 Flux 应用程序架构时，你将看到这方面的一个例子。让我们从在你的主目录`~/snapterest/`内创建我们的根项目目录`snapterest`开始。

然后，在其中，我们将创建另外两个目录：

+   `~/snapterest/source/`：在这里，我们将存储我们的源 JavaScript 文件

+   `~/snapterest/build/`：在这里，我们将放置编译后的 JavaScript 文件和一个 HTML 文件

现在，在`~/snapterest/source/`中，创建`components/`文件夹，使得你的项目结构看起来像这样：

+   `~/snapterest/source/components/`

+   `~/snapterest/build/`

现在我们的基本项目结构准备好了，让我们开始用我们的应用文件填充它。首先，我们需要在`~/snapterest/source/`目录中创建我们的主应用文件`app.js`。这个文件将是我们应用的入口点，`~/snapterest/source/app.js`。

现在先留空，因为我们有一个更紧迫的问题要讨论。

# 创建 package.json

你以前听说过**D.R.Y.**吗？它代表**不要重复自己**，并且它提倡软件开发中的核心原则之一——代码重用。最好的代码是你不需要写的代码。事实上，我们在这个项目中的一个目标就是尽可能少地编写代码。你可能还没有意识到，但 React 帮助我们实现了这个目标。它不仅节省了我们的时间，而且如果我们决定在将来维护和改进我们的项目，它将在长远来看节省我们更多的时间。

当涉及到不编写我们的代码时，我们可以应用以下策略：

+   以声明式编程风格编写我们的代码

+   重用他人编写的代码

在这个项目中，我们将使用两种技术。第一种技术由 React 本身提供。React 只能让我们以声明式风格编写 JavaScript 代码。这意味着我们不是告诉网页浏览器如何做我们想要的事情（就像我们用 jQuery 做的那样），而是告诉它我们想要它做什么，而 React 解释了如何做。这对我们来说是一个胜利。

Node.js 和 npm 涵盖了第二种技术。我在本章前面提到，有数十万不同的 Node.js 应用程序可供我们使用。这意味着很可能有人已经实现了我们的应用程序所依赖的功能。

问题是，我们从哪里获取所有这些我们想要重用的 Node.js 应用程序？我们可以通过`npm install <package-name>`命令安装它们。在 npm 上下文中，一个 Node.js 应用程序被称为**包**，每个**npm 包**都有一个描述该包相关元数据的`package.json`文件。您可以在[`docs.npmjs.com/files/package.json`](https://docs.npmjs.com/files/package.json)了解有关存储在`package.json`中的字段的更多信息。

在安装依赖包之前，我们将为我们自己的项目初始化一个包。通常，只有当您想要将您的包提交到 npm 注册表以便其他人可以重用您的 Node.js 应用程序时，才需要`package.json`。我们不打算构建 Node.js 应用程序，也不打算将我们的项目提交到 npm。请记住，`package.json`从技术上讲只是`npm`命令理解的元数据文件，因此我们可以使用它来存储我们的应用程序所需的依赖项列表。一旦我们在`package.json`中存储了依赖项列表，我们就可以随时使用`npm install`命令轻松安装它们；npm 将自动找到它们的位置。

我们如何为我们自己的应用程序创建`package.json`文件？幸运的是，npm 带有一个交互式工具，询问我们一系列问题，然后根据我们的答案为我们的项目创建`package.json`。

确保您位于`~/snapterest/`目录中。在终端/命令提示符中，运行以下命令：

```jsx
**npm init**

```

它将首先询问您的软件包名称。 它将建议一个默认名称，即您所在目录的名称。 在我们的情况下，它应该建议`name：（snapterest）`。 按*Enter*接受建议的默认名称（`snapterest`）。 下一个问题是您软件包的版本，即`version：（1.0.0）`。 按*Enter*。 如果我们计划将软件包提交给 npm 供其他人重用，这两个将是最重要的字段。 因为我们不打算将其提交给 npm，所以我们可以自信地接受我们被问到的所有问题的默认值。 继续按*Enter*，直到`npm init`完成执行并退出。 然后，如果您转到`〜/snapterest/`目录，您将在那里找到一个新文件-`package.json`。

现在我们准备安装其他我们将要重用的 Node.js 应用程序。 由多个单独应用程序构建的应用程序称为**模块化**，而单独的应用程序称为**模块**。 从现在开始，这就是我们将称之为我们的 Node.js 依赖项-Node.js 模块。

# 重用 Node.js 模块

正如我之前提到的，我们的开发过程中将有一个称为**构建**的步骤。 在此步骤中，我们的构建脚本将获取我们的源文件和所有 Node.js 依赖包，并将它们转换为 Web 浏览器可以成功执行的单个文件。 这个构建过程中最重要的部分称为**打包**。 但是我们需要打包什么以及为什么呢？ 让我们考虑一下。 我之前简要提到过，我们并不是在创建一个 Node.js 应用程序，但我们正在谈论重用 Node.js 模块。 这是否意味着我们将在非 Node.js 应用程序中重用 Node.js 模块？ 这可能吗？ 原来有一种方法可以做到这一点。

**Webpack**是一种工具，用于以这样一种方式捆绑所有依赖文件，以便您可以在客户端 JavaScript 应用程序中重用 Node.js 模块。 您可以在[`webpack.js.org`](http://webpack.js.org)了解有关 Webpack 的更多信息。 要安装 Webpack，请从`〜/snapterest/`目录内运行以下命令：

```jsx
**npm install --save-dev webpack**

```

注意`--save-dev`标志。它告诉 npm 将 Webpack 添加到我们的`package.json`文件中作为开发依赖项。将模块名称添加到我们的`package.json`文件中作为依赖项允许我们记录我们正在使用的依赖项，并且如果需要的话，我们可以很容易地使用`npm install`命令稍后安装它们。运行应用程序所需的依赖项与开发应用程序所需的依赖项之间有区别。Webpack 在构建时使用，而不是在运行时，因此它是开发依赖项。因此，使用`--save-dev`标志。如果您现在检查您的`package.json`文件的内容，您会看到这个（如果您的 Webpack 版本不完全匹配，不要担心）：

```jsx
"devDependencies": {
  "webpack": "².2.1"
}
```

npm 在您的`〜/snapterest/`目录中创建了一个名为`node_modules`的新文件夹。这是它放置所有本地依赖模块的地方。

恭喜您安装了您的第一个 Node.js 模块！Webpack 将允许我们在客户端 JavaScript 应用程序中使用 Node.js 模块。它将成为我们构建过程的一部分。现在让我们更仔细地看看我们的构建过程。

# 使用 Webpack 构建

今天，任何现代的客户端应用程序都代表了许多由各种技术单独解决的问题的混合。单独解决每个问题简化了管理项目复杂性的整个过程。这种方法的缺点是，在项目的某个时候，您需要将所有单独的部分组合成一个连贯的应用程序。就像汽车工厂中的机器人从单独的零件组装汽车一样，开发人员有一种称为构建工具的东西，可以从单独的模块中组装他们的项目。这个过程被称为**构建**过程，根据项目的大小和复杂性，构建过程可能需要从毫秒到几个小时不等的时间。

Webpack 将帮助我们自动化我们的构建过程。首先，我们需要配置 Webpack。假设您在`〜/snapterest/`目录中，创建一个新的`webpack.config.js`文件。

现在让我们在`webpack.config.js`文件中描述我们的构建过程。在这个文件中，我们将创建一个描述如何捆绑我们的源文件的 JavaScript 对象。我们希望将该配置对象导出为一个 Node.js 模块。是的，我们将把我们的`webpack.config.js`文件视为一个 Node.js 模块。为了做到这一点，我们将把我们的空配置对象分配给一个特殊的`module.exports`属性：

```jsx
const path = require('path'); 
module.exports = {};
```

`module.exports`属性是 Node.js API 的一部分。这是告诉 Node.js，每当有人导入我们的模块时，他们将获得对该对象的访问权限。那么这个对象应该是什么样子的呢？这就是我建议你去查看 Webpack 文档并阅读关于 Webpack 核心概念的链接：[`webpack.js.org/concepts/`](https://webpack.js.org/concepts/)

我们配置对象的第一个属性将是`entry`属性：

```jsx
module.exports = {
  entry: './source/app.js',
};
```

顾名思义，`entry`属性描述了我们 web 应用的入口点。在我们的例子中，这个属性的值是`./source/app.js`—这是启动我们应用的第一个文件。

我们配置对象的第二个属性将是`output`属性：

```jsx
output: {
  path: path.resolve(__dirname, 'build'),
  filename: 'snapterest.js'
},
```

`output`属性告诉 Webpack 在哪里输出生成的捆绑文件。在我们的例子中，我们说我们希望生成的捆绑文件叫做`snapterest.js`，并且应该保存到`./build`目录中。

Webpack 将每个源文件视为一个模块，这意味着所有我们的 JavaScript 源文件将被视为 Webpack 需要捆绑在一起的模块。我们如何向 Webpack 解释这一点呢？

我们通过配置对象的第三个属性`module`来实现这一点：

```jsx
module: {
  rules: [
    {
      test: /\.js$/,
      use: [
        {
          loader: 'babel-loader',
          options: {
            presets: ['react', 'latest'],
            plugins: ['transform-class-properties']
          }
        }
      ],
      exclude: path.resolve(__dirname, 'node_modules')
    }
  ]
}
```

正如你所看到的，我们的`module`属性得到一个对象作为它的值。这个对象有一个叫做`rules`的属性—一个规则数组，其中每个规则描述了如何从不同的源文件创建 Webpack 模块。让我们更仔细地看看我们的规则。

我们有一个单一规则告诉 Webpack 如何处理我们的源 JavaScript 文件：

```jsx
{
  test: /\.js$/,
  use: [
    {
      loader: 'babel-loader',
      options: {
        presets: ['react', 'latest'],
        plugins: ['transform-class-properties']
      }
    }
  ],
  exclude: path.resolve(__dirname, 'node_modules')
}
```

这个规则有三个属性：`test`，`use`和`exclude`。`test`属性告诉 Webpack 这个规则适用于哪些文件。它通过将我们的源文件名与我们指定为`test`属性值的正则表达式进行匹配来实现：`/\.js$/`。如果你熟悉正则表达式，你会认识到`/\.js$/`将匹配所有以`.js`结尾的文件名。这正是我们想要的：打包所有的 JavaScript 文件。

当 Webpack 找到并加载所有源 JavaScript 文件时，它会尝试将它们解释为普通的 JavaScript 文件。然而，我们的 JavaScript 文件不会是普通的 JavaScript 文件，而是具有 ECMAScript 2016 语法以及 React 特定语法。

Webpack 如何理解所有非普通的 JavaScript 语法？借助于 Webpack 加载器，我们可以将非普通的 JavaScript 语法转换为普通的 JavaScript。Webpack 加载器是应用于源文件的转换。我们的`use`属性描述了我们想要应用的转换列表：

```jsx
use: [
  {
    loader: 'babel-loader',
    options: {
      presets: ['react', 'latest'],
      plugins: ['transform-class-properties']
    }
  }
],
```

我们有一个转换负责将我们的 React 特定语法和 ECMAScript 2016 语法转换为普通 JavaScript：

```jsx
{
  loader: 'babel-loader',
  options: {
    presets: ['react', 'latest'],
    plugins: ['transform-class-properties']
  }
}
```

Webpack 转换是用具有`loader`和`options`属性的对象来描述的。`loader`属性告诉 Webpack 哪个加载器执行转换，`options`属性告诉它应该传递给该加载器哪些选项。将我们的 ECMAScript 2016 和特定于 React 的语法转换为普通 JavaScript 的加载器称为`babel-loader`。这个特定的转换过程称为**转译**或**源到源编译**——它将用一种语法编写的源代码转换为另一种语法编写的源代码。我们今天使用的是最流行的 JavaScript 转译器之一，叫做**Babel**：[`babeljs.io`](https://babeljs.io)。Webpack 有一个使用 Babel 转译器来转换我们源代码的 Babel 加载器。Babel 加载器作为一个独立的 Node.js 模块。让我们安装这个模块并将其添加到我们的开发依赖列表中。假设你在`~/snapterest/`目录中，运行以下命令：

```jsx
**npm install babel-core babel-loader --save-dev**

```

我们的 Webpack 加载器的`options`属性有一些 Babel 预设：`latest`和`react`以及一个 Babel`transform-class-properties`插件：

```jsx
options: {
  presets: ['react', 'latest'],
  plugins: ['transform-class-properties']
}
```

这些是负责转换不同语法的 Babel 插件：`latest`插件将 ECMAScript 2015、ECMAScript 2016 和 ECMAScript 2017 的语法转换为旧的 JavaScript 语法，`react`插件将 React 特定的语法转换为普通的 JavaScript 语法，而`transform-class-properties`插件将类属性转换为普通的 JavaScript 语法。

这些 Babel 插件是作为独立的 Node.js 模块分发的，我们需要单独安装它们。假设你在`~/snapterest/`目录中，运行以下命令：

```jsx
**npm install babel-preset-latest babel-preset-react babel-plugin-transform-class-properties --save-dev**

```

最后，我们在 Webpack 规则中有第三个属性叫做`exclude`：

```jsx
exclude: path.resolve(__dirname, 'node_modules')
```

这个属性告诉 Webpack 在转换过程中排除`node_modules`目录。

现在我们的`webpack.config.js`文件已经准备好了。在我们第一次运行打包过程之前，让我们在`package.json`文件中添加一个名为`start`的新脚本：

```jsx
"scripts": {
  "start": "webpack -p --config webpack.config.js",
  "test": "echo \"Error: no test specified\" && exit 1"
},
```

现在如果你运行`npm run start`或者`npm start`，npm 会运行`webpack -p --config webpack.config.js`命令。这个命令会运行 Webpack，用`webpack.config.js`文件打包我们的源文件以供生产使用。

我们已经准备好打包我们的源文件了！转到你的`~/snapterest/`目录并运行这个命令：

```jsx
**npm start**

```

在输出中，你应该会看到以下内容：

```jsx
**Version: webpack 2.2.1**
**Time: 1151ms**
 **Asset       Size  Chunks             Chunk Names**
**app.js  519 bytes       0  [emitted]  main**
 **[0] ./source/app.js 24 bytes {0} [built]**

```

更重要的是，如果你检查你的项目的`~/snapterest/build/`目录，你会注意到现在有一个`snapterest.js`文件，里面已经有一些代码了——那就是我们（空的）JavaScript 应用程序，里面有一些 Node.js 模块，可以在 web 浏览器中运行！

# 创建一个网页

如果你渴望一些 React 的好处，那么我有个好消息告诉你！我们快要完成了。剩下要做的就是创建一个带有指向我们`snapterest.js`脚本的`index.html`。

在`~/snapterest/build/`目录中创建`index.html`文件。添加以下 HTML 标记：

```jsx
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="ie=edge, chrome=1" />
    <title>Snapterest</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
  </head>
  <body>
    <div id="react-application">
      I am about to learn the essentials of React.js. </div>
    <script src="./snapterest.js"></script>
  </body>
</html>
```

在 web 浏览器中打开`~/snapterest/build/index.html`。你应该会看到以下文字：**我即将学习 React.js 的基本知识**。没错，我们已经完成了项目的设置，现在是时候了解 React 了！

# 摘要

在本章中，你学到了为什么我们应该使用 React 来构建现代 web 应用程序的用户界面。然后，我们讨论了这本书中我们将要构建的项目。最后，我们安装了所有正确的工具，并创建了项目的结构。

在下一章中，我们将安装 React，更仔细地了解 React 的工作原理，并创建我们的第一个 React 元素。
