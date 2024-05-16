# 前言

React Native 帮助 Web 和移动开发人员构建与任何其他本地开发的应用性能相同的 iOS 和 Android 应用程序。使用这个库可以构建的应用范围非常广泛。从电子商务到游戏，React Native 都是任何移动项目的良好选择，因为它具有灵活性和可扩展性。它具有良好的性能，可以重用 React 知识，具有导入 npm 包的能力，并且在 iOS 和 Android 上使用相同的代码库。毫无疑问，React Native 不仅是本地开发的一个很好的替代方案，而且也是将 Web 开发人员引入移动项目的一个很好的方式。本书旨在让 JavaScript 和 React 开发人员了解如何使用 React Native 从头开始构建市场上一些最流行的应用。我们将在 iOS 和 Android 上构建所有应用，除非这些应用只在其中一个平台上有意义。

# 本书所需的内容

本书中构建的大多数应用程序将在 Android 和 iOS 上运行，因此需要运行 Linux、Windows 或 OSX 的计算机，尽管我们建议使用任何一台苹果电脑（运行 OSX 10 或更高版本）同时运行两个移动平台，因为一些示例将需要在 XCode 上工作，而 XCode 只能安装在 OSX 上。

我们在示例中将使用的其他软件包括：

+   XCode

+   Android Studio

+   一个 React-ready 的 IDE（如 Atom，VS Code 和 SublimeText）

当然，我们还需要安装 React Native 和 React Native CLI（[`facebook.github.io/react-native/docs/getting-started.html`](https://facebook.github.io/react-native/docs/getting-started.html)）。

# 本书适合的读者是谁？

本书的目标读者是试图了解如何使用 React Native 构建不同类型应用的 JavaScript 开发人员。他们将找到一套可以应用于构建任何类型应用的最佳实践和经过验证的架构策略。

尽管本书不会解释 React 的一些基本概念，但并不需要特定的 React 技能来跟随，因为我们不会深入研究复杂的 React 模式。

# 约定

在本书中，您将找到一些文本样式，用于区分不同类型的信息。以下是这些样式的一些示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名显示如下：“我们必须创建一个`src`文件夹，我们将在其中存储所有的 React 代码。”

此外，在大的代码块中，当一些代码片段不相关或在不同的地方进行了审查时，它们将被省略号（...）替换。

代码块设置如下：

```jsx
/*** index.js ***/

import { AppRegistry } from 'react-native';
import App from './src/main';
AppRegistry.registerComponent('GroceriesList', () => App);
```

任何命令行输入或输出都以以下方式书写：

```jsx
react-native run-ios
```

**新术语**和**重要单词**以粗体显示。屏幕上看到的单词，例如菜单或对话框中的单词，会以这种方式出现在文本中：“在添加产品屏幕上的返回按钮。”

提示和重要说明会出现在这样的框中。技巧和窍门会以这种方式出现。
