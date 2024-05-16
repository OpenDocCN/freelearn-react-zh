# 第九章：使用单元测试和行为测试进行持续集成

我们成功了！恭喜！我们创建了一个在某个特定域名下运行的全栈应用（在本书中是*reactjs.space*）。整个设置中缺少的部分是部署过程。部署应该是零停机时间的。我们需要一个冗余的服务器来运行我们的应用程序。

我们的应用程序还缺少一些步骤，以使其能够专业地进行缩小、单元和行为测试。

在本章中，我们将向你介绍一些额外的概念，这些概念是掌握全栈开发所必需的。其余的缺失部分留给你作为家庭作业。

# 何时编写单元测试和行为测试

一般来说，有一些建议关于何时编写单元测试和/或行为测试。

我们在 ReactPoland 经常有客户是创业公司。作为对他们的一般管理，我们建议以下内容：

+   如果你的创业公司正在寻求增长，并且需要你的产品来实现它，那就不要担心测试。

+   在创建了**最小可行产品**（**MVP**）之后，当扩展你的应用程序时，*必须*进行这些测试

+   如果你是一家成熟的公司，为客户构建应用程序，并且非常了解他们的需求，那么你必须进行测试。

前两点与初创公司和年轻公司有关。第三点主要与成熟的公司有关。

根据你和你的产品所处的位置，你需要自己决定是否值得编写测试。

# React 惯例

有一个项目完全展示了全栈开发设置应该是什么样子的，网址是[`React JS.co`](http://reactjs.co/)。

访问这个网站，学习如何将你的应用程序与单元测试和行为测试集成，并了解有关如何制作 React Redux 应用程序的最新最佳惯例。

# 测试的因果报应

在本章中，我们不会指导你设置测试，因为这不在本书的范围内。本章的目的是向你介绍在线资源，这些资源将帮助你理解更大的图景。

Karma 是最流行的用于单元测试和行为测试的工具之一。其主要目标是在开发任何应用程序时提供一个高效的测试环境。

使用这个测试运行器，你可以获得许多功能。有一个很好的视频在[`karma-runner.github.io`](https://karma-runner.github.io)解释了 Karma 的整体情况。

一些主要特性如下：

+   **在真实设备上进行测试**：您可以使用真实浏览器和真实设备，如手机、平板电脑或 PhantomJS 来运行测试（PhantomJS 是一个无头 WebKit，可通过 JavaScript API 进行脚本化；它对各种 Web 标准具有快速和本地支持：DOM 处理、CSS 选择器、JSON、Canvas 和 SVG）。有不同的环境，但有一个工具可以在所有这些环境上运行。

+   **远程控制**：您可以远程运行测试，例如，在 IDE 每次保存时，这样您就不必手动运行。

+   **测试框架不可知**：您可以使用 Jasmine、Mocha、QUnit 和其他框架编写测试。完全取决于您。

+   **持续集成**：Karma 与 Jenkins、Travis 或 CircleCI 等 CI 工具完美配合。

# 如何编写单元测试和行为测试

让我们举个例子，说明如何正确设置项目，以便能够编写测试。

访问非常流行的 Redux starter kit 的 GitHub 仓库，网址为[`github.com/davezuko/react-redux-starter-kit`](https://github.com/davezuko/react-redux-starter-kit)。

然后访问此存储库的`package.json`文件。我们可以在那里找到可能的命令/脚本是什么：

[PRE0]

您可以在 NPM 测试后找到它运行以下命令：

[PRE1]

您可以在`build/karma.conf`中找到 Karma 的配置文件，网址为[`github.com/davezuko/react-redux-starter-kit/blob/master/build/karma.conf.js`](https://github.com/davezuko/react-redux-starter-kit/blob/master/build/karma.conf.js)。

截至内容（2016 年 7 月）如下：

[PRE2]

正如您在`karma.conf.js`中所看到的，他们正在使用 Mocha（检查带有`"frameworks: ['mocha']"`的行）。配置文件中使用的其他选项在文档中有描述，该文档可在[`karma-runner.github.io/1.0/config/configuration-file.html`](http://karma-runner.github.io/1.0/config/configuration-file.html)找到。如果您有兴趣学习 Karma 配置，那么`karma.conf.js`应该是您的起始文件。

# 什么是 Mocha，为什么需要它？

在 Karma 配置文件中，我们发现它使用 Mocha 作为 JS 测试框架（[`mochajs.org/`](https://mochajs.org/)）。让我们分析代码库。

我们可以在`config/index.js`文件中找到`dir_test: 'tests'`，因此基于该变量，Karma 的`config`知道 Mocha 的测试位于`tests/test-bundler.js`文件中。

让我们看看在 https://github.com/davezuko/react-redux-starter-kit/tree/master/tests 的`tests`目录中有什么。正如您在`test-bundler.js`文件中所看到的，有大量的依赖项：

[PRE3]

让我们大致描述一下那里使用了什么：

+   Babel-polyfill 模拟了完整的 ES6 环境

+   Sinon 是一个独立的、与测试框架无关的 JavaScript 测试框架，用于间谍、存根和模拟

如果在被测试的代码中调用了其他外部服务，间谍是很有用的。您可以检查它是否被调用，它有什么参数，它是否返回了东西，甚至它被调用了多少次！

存根的概念与间谍的概念非常相似。最大的区别在于存根替换了目标函数。它们还用自定义行为（替换它）替换了被调用的代码，比如抛出异常或返回一个值。它们还能够调用作为参数提供的回调函数。存根代码返回指定的结果。

Mocks 是一种*更智能的存根*。 Mocks 用于断言数据，不应返回数据，而存根仅用于返回数据，不应断言。 Mocks 可以在断言时破坏您的测试，而存根则不能。

Chai 是用于 Node.js 和浏览器的 BDD/TDD 断言框架。在前面的例子中，它已经与 Mocha 测试框架配对使用了。

# 逐步测试 CoreLayout

让我们分析`CoreLayout.spec.js`的测试。这个组件在发布应用程序中扮演着与 CoreLayout 类似的角色，因此这是描述如何开始为应用程序编写测试的好方法。

CoreLayout 测试文件位置（2016 年 7 月）可在[`github.com/davezuko/react-redux-starter-kit/blob/master/tests/layouts/CoreLayout.spec.js`](https://github.com/davezuko/react-redux-starter-kit/blob/master/tests/layouts/CoreLayout.spec.js)找到。

内容如下：

[PRE4]

`react-addons-test-utils`库使得使用 Mocha 轻松测试 React 组件成为可能。我们在前面的例子中使用的方法是**浅渲染**，可以在[`facebook.github.io/react/docs/test-utils.html#shallow-rendering`](https://facebook.github.io/react/docs/test-utils.html#shallow-rendering)找到。

这个功能帮助我们测试`render`函数，并且是在我们的组件中渲染一级深度的结果。然后我们可以断言关于它的`render`方法返回的事实，如下所示：

[PRE5]

首先，我们在`shallowRender`方法中提供一个组件（在这个例子中，它将是 CoreLayout）。然后，我们使用`method.render`，然后我们使用`renderer.getRenderOutput`返回输出。

在我们的情况下，这个函数在这里被调用（请注意，以下示例中缺少分号，因为我们描述的起始器与我们的不同的 linting 选项）：

[PRE6]

你可以发现`_component`变量包含了`renderer.getRenderOutput`的结果。这个值被断言如下：

[PRE7]

在那个测试中，我们测试我们的代码是否返回`div`。但是如果你访问文档，你可以找到以下代码示例：

[PRE8]

你也可以在下面找到断言的例子：

[PRE9]

在前面的两个例子中，你可以期望一个类型为`div`，或者你可以期望更具体的关于 CoreLayout 返回的信息（取决于你的需求）。

第一个测试断言组件的类型（如果是`div`），第二个例子测试断言 CoreLayout 返回的正确组件如下：

[PRE10]

第一个是单元测试，因为这并不完全是在测试用户是否看到了正确的东西。第二个是行为测试。

通常，Packt 有许多关于**行为驱动开发**（**BDD**）和**测试驱动开发**（**TDD**）的书籍。

# 使用 Travis 进行持续集成

在给定的例子中，你可以在[`github.com/davezuko/react-redux-starter-kit/blob/master/.travis.yml`](https://github.com/davezuko/react-redux-starter-kit/blob/master/.travis.yml)找到一个`.yml`文件。

这是 Travis 的配置文件。这是什么？这是一个托管的 CI 服务，用于构建和测试软件。通常，这是一个免费供开源项目使用的工具。如果你想要一个私人项目的托管 Travis CI，那么就需要支付费用。

如前所述，通过添加`.travis.yml`文件来配置 Travis。YAML 形式是一个文本文件，放置在项目的根目录。这个文件的内容描述了测试、安装和构建项目所需的所有步骤。

Travis CI 的目标是使每次提交到你的 GitHub 账户并运行测试，当测试通过时，你可以将应用部署到 Amazon AWS 上的一个暂存服务器上。持续集成不在本书的范围内，所以如果你有兴趣将这一步添加到整个发布应用项目中，也有相关的书籍。


# 总结

我们的发布应用程序正在运行。与任何数字项目一样，我们仍然有很多可以改进的地方，以便获得更好的最终产品。例如，以下作业是给你的：

+   在前端添加一个压缩，这样在互联网上加载时会更轻。

+   正如前面提到的，您需要开始使用 Karma 和 Mocha 进行单元和行为测试。本章节详细描述了一个示例设置。

+   您需要选择一个 CI 工具，比如 Travis，创建您的 YML 文件，并在 AWS 上准备环境。

这是除了本书 350 多页涵盖的所有内容之外，你可以额外做的。在这本书中，你构建了一个全栈 React + Redux + Falcor + Node + Express + Mongo 应用程序。我希望能和你保持联系；在 Twitter/GitHub 上关注我，以便保持联系，或者如果你有任何额外的问题，给我发电子邮件。

祝你好运，下一个商业全栈应用程序的开发顺利，再见。
