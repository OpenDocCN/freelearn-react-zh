# 前言

本书的部分内容需要 macOS 才能使用的软件。虽然 React Native 开发可以在 Windows 机器上完成，但是某些方面，比如在 iOS 设备和 iOS 模拟器上运行应用程序，或者使用 Xcode 编辑本地代码，只能在 Mac 上完成。

开发人员有许多种方式来构建 iOS 或 Android 应用程序。React Native 是构建混合移动应用程序的最稳定、性能最佳和开发人员友好的选择之一。使用 React Native 开发移动应用程序允许开发人员在单个代码库中构建 iOS 和 Android 应用程序，并且可以在两个平台之间共享代码。

更好的是，有经验在 React 中构建 Web 应用程序的开发人员将会处于领先地位，因为许多相同的模式和约定都被延续到 React Native 中。如果您有使用 React 或其他基于**模型**、**视图**、**组件**（MVC）的框架构建 Web 应用程序的经验，那么在 React Native 中构建移动应用程序会让您感到宾至如归。

目前有两种广泛使用的方法来创建和开发 React Native 应用程序：使用 React Native CLI 进行纯 React Native 开发，或者使用 Expo（[www.expo.io](http://www.expo.io)），这是一套全面的工具、库和服务，用于开发 React Native 应用程序。除非您需要访问 React Native 的某些通常更高级的功能，Expo 是我推荐的 React Native 开发工具。Expo 具有许多功能，可以改善开发体验，例如通过 Expo SDK 访问更多本地功能，更灵活和友好的 CLI，以及用于常见开发任务的基于浏览器的 GUI。这就是为什么本书中不需要纯 React Native 的所有食谱都使用 Expo 实现。有关 React Native 和 Expo 之间的区别，请查看第十章“应用程序工作流程和第三方插件”中的*React Native 开发工具*部分。

本书旨在成为构建各种应用程序时常见问题解决方案的参考手册。每一章都以一系列逐步说明的食谱呈现，每个食谱都解释了如何构建整个应用程序的单个功能。

React Native 是一种不断发展的语言。在撰写本文时，它仍处于开发生命周期的 0.5x 阶段，因此未来几个月甚至几年可能会发生一些变化。最佳实践可能会变成陈旧的想法，或者在这里突出显示的开源软件包可能会不受青睐。本书中的每个示例都经过了更新和修订，以反映开发过程的更新并提高清晰度。我已尽力使本文尽可能保持最新，但技术发展迅速，因此一本书无法单独跟上。本书中涵盖的所有代码存储在 GitHub 上。如果您发现这里的代码有任何问题，请提交问题。或者，如果您有更好的方法来做某事，请考虑提交拉取请求！

无论这本书中的任何内容有何更新，您都可以在 GitHub 存储库中找到详细信息和更改。

希望您在 React Native 的学习过程中找到这本书有所帮助。祝您开发愉快！

# 这本书适合谁

本书旨在面向初学者到中级水平的 React Native 开发人员。即使您没有太多的 Web 开发经验，本书中的 JavaScript 也希望不会超出您的理解范围。我尽量避免复杂性，以便将重点放在每个示例中所教授的课程上。

本书还假设开发人员使用运行 macOS 的计算机。虽然在 Windows 或 Linux 上开发 React Native 应用程序在技术上是可能的，但有许多限制使 macOS 机器在 React Native 开发中更可取，包括通过 Xcode 与本机 iOS 代码一起工作，在 iOS 模拟器上运行 iOS 代码，并使用最强大的 React Native 应用程序开发工具。

# 这本书涵盖了什么

第一章，*设置您的环境*，介绍了我们将安装的不同软件，以便开始开发 React Native 应用程序。

第二章，*创建一个简单的 React Native 应用*，介绍了构建布局和导航的基础知识。本章中的示例作为 React Native 开发的入门，并涵盖了大多数移动应用程序中的基本功能。

第三章《实现复杂用户界面-第一部分》涵盖了包括自定义字体和自定义可重用主题在内的功能。

第四章《实现复杂用户界面-第二部分》继续基于 UI 功能的更多技巧。它涵盖了处理屏幕方向变化和构建用户表单等功能。

第五章《实现复杂用户界面-第三部分》涵盖了构建复杂 UI 时可能需要的其他常见功能。本章涵盖了添加地图支持、实现基于浏览器的身份验证和创建音频播放器。

第六章《为您的应用添加基本动画》涵盖了创建动画的基础知识。

第七章《为您的应用添加高级动画》继续在上一章的基础上构建，增加了更高级的功能。

第八章《处理应用逻辑和数据》向我们介绍了处理数据的应用程序的构建。我们将涵盖一些主题，包括本地存储数据和优雅地处理网络丢失。

第九章《实现 Redux》涵盖了使用 Redux 库实现 Flux 数据模式。Redux 是处理 React 应用程序中的数据流的经过实战检验的方法，在 React Native 中同样有效。

第十章《应用程序工作流程和第三方插件》涵盖了开发人员可以使用的构建应用程序的不同方法，以及如何使用开源代码构建应用程序。这也将涵盖使用纯 React Native（使用 React Native CLI）构建应用程序和使用 Expo（一个全面的开发工具）构建应用程序之间的区别。

第十一章《添加原生功能-第一部分》涵盖了在 React Native 应用程序中使用原生 iOS 和 Android 代码的基础知识。

第十二章《添加原生功能-第二部分》涵盖了在 React Native 和原生层之间进行通信的更复杂的技术。

第十三章《与原生应用集成》涵盖了将 React Native 与现有原生应用集成的内容。并非每个应用都可以从头开始构建。这些技巧对于需要将他们的工作与已经在应用商店中的应用集成的开发人员应该是有帮助的。

第十四章*部署您的应用程序*介绍了部署 React Native 应用程序的基本流程，以及使用 HockeyApp 跟踪应用程序指标的详细信息。

第十五章，*优化您的应用程序的性能*，介绍了编写高性能 React Native 代码的一些技巧和最佳实践。

# 为了充分利用本书

假设您具有以下理解水平：

+   您具有一些基本的编程知识。

+   您熟悉 Web 开发基础知识。

如果您还具有以下内容，将会很有帮助：

+   具有 React、Vue 或 Angular 经验

+   至少具有中级水平的 JavaScript 经验

# 下载示例代码文件

您可以从[www.packt.com](http://www.packt.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[www.packt.com/support](http://www.packt.com/support)并注册，将文件直接发送到您的邮箱。

您可以按照以下步骤下载代码文件：

1.  登录或注册[www.packt.com](http://www.packt.com)。

1.  选择“支持”选项卡。

1.  点击“代码下载和勘误”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

文件下载后，请确保使用最新版本的解压缩或提取文件夹：

+   Windows 上使用 WinRAR/7-Zip

+   Mac 上使用 Zipeg/iZip/UnRarX

+   Linux 上使用 7-Zip/PeaZip

该书的代码包也托管在 GitHub 上，网址为[`github.com/warlyware/react-native-cookbook`](https://github.com/warlyware/react-native-cookbook)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有其他代码包，来自我们丰富的图书和视频目录，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)上找到。去看看吧！

# 下载彩色图片

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图片。您可以在这里下载：[`www.packtpub.com/sites/default/files/downloads/9781788991926_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/9781788991926_ColorImages.pdf)。

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词，数据库表名，文件夹名，文件名，文件扩展名，路径名，虚拟 URL，用户输入和 Twitter 用户名。这是一个例子：“我们将使用一个带有一个`liked`布尔属性的`state`对象来实现这个目的。”

代码块设置如下：

```jsx
export default class App extends React.Component {
  state = {
    liked: false,
  };

  handleButtonPress = () => {
    // We'll define the content on step 6
  }
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目会以粗体显示：

```jsx
onst styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  topSection: {
    flexGrow: 3,
    backgroundColor: '#5BC2C1',
 alignItems: 'center',
  },
```

任何命令行输入或输出都以以下方式编写：

```jsx
expo init project-name
```

**粗体**：表示一个新术语，一个重要的词，或者你在屏幕上看到的词。例如，菜单或对话框中的单词会以这种方式出现在文本中。这是一个例子：“单击 Components 选项卡，并从提供的模拟器列表中安装一个模拟器。”

警告或重要说明会以这种方式出现。提示和技巧会以这种方式出现。

# 部分

在本书中，您会发现一些经常出现的标题（*Getting ready*，*How to do it...*，*How it works...*，*There's more...*，和 *See also*）。

为了清晰地说明如何完成一个食谱，使用以下部分：

# 准备工作

这一部分告诉您在食谱中可以期待什么，并描述如何设置食谱所需的任何软件或任何初步设置。

# 如何做…

这一部分包含了遵循食谱所需的步骤。

# 它是如何工作的…

这一部分通常包括对前一部分发生的事情的详细解释。

# 还有更多…

这一部分包含了有关食谱的额外信息，以使您对食谱更加了解。

# 另请参阅

这一部分为食谱提供了有用的链接到其他有关食谱的信息。
