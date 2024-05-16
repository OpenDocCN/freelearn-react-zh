# 第十章：应用程序工作流和第三方插件

本章的工作方式有些不同，因此我们将首先了解它，然后再继续覆盖以下示例：

+   React Native 开发工具

+   规划您的应用程序并选择您的工作流程

+   使用 NativeBase 来实现跨平台 UI 组件

+   使用 glamorous-native 来为 UI 组件添加样式

+   使用 react-native-spinkit 添加动画加载指示器

+   使用 react-native-side-menu 添加侧边导航菜单

+   使用 react-native-modalbox 添加模态框

# 本章的工作方式

在本章中，我们将更仔细地了解初始化新的 React Native 应用程序的每种方法的工作原理，以及如何集成可能与 Expo 兼容或不兼容的第三方包。在之前的章节中，重点完全放在构建 React Native 应用程序的功能部分上。因此，在本章中，许多这些示例也将用于说明如何使用不同的工作流程来实现不同的包。

在本章的大多数示例中，我们将从使用 React Native CLI 命令初始化的纯 React Native 项目开始，方法如下：

```jsx
 react-native init
```

创建新的 React Native 应用程序时，您需要选择适合初始化应用程序的正确工具。一般来说，您用于引导和开发 React Native 应用程序的工具将专注于简化开发过程，并故意为了方便和心理负担而模糊化本地代码，或者通过提供对所有本地代码的访问以及允许使用更多第三方插件来保持开发过程的灵活性。

初始化和开发应用有两种方法：Expo 和 React Native CLI。直到最近，还有第三种方法，使用 Create React Native App（CRNA）。CRNA 已经与 Expo 项目合并，只作为一个独立实体继续存在，以提供向后兼容性。

Expo 属于工具的第一类，提供了更强大和开发者友好的开发工作流程，但牺牲了一些灵活性。使用 Expo 引导的应用程序还可以访问由 Expo SDK 提供的大量有用功能，例如`BarcodeScanner`、`MapView`、`ImagePicker`等等。

使用 React Native CLI 通过以下命令初始化应用程序：

```jsx
 react-native init
```

这提供了灵活性，但开发的难度也相应增加。

```jsx
 react-native init 
```

据说这是一个纯 React Native 应用程序，因为没有任何原生代码对开发人员隐藏。

作为一个经验法则，如果使用第三方包的设置需要运行以下命令，则需要一个纯 React Native 应用程序：

```jsx
 react-native link 
```

那么，当您在使用 Expo 构建应用程序时，却发现一个对应用程序要求至关重要的包不受 Expo 开发工作流支持时，该怎么办？幸运的是，Expo 有一种方法可以将 Expo 项目转换为纯 React Native 应用程序，就好像是用以下命令创建的一样：

```jsx
expo eject
```

当项目被弹出时，所有的原生代码都被解压到`ios`和`android`文件夹中，`App.js`文件被拆分为`App.js`和`index.js`，暴露出挂载根 React Native 组件的代码。

但是，如果您的 Expo 应用依赖于 Expo SDK 提供的功能呢？毕竟，使用 Expo 开发的价值很大程度上来自于 Expo 提供的出色功能，包括`AuthSession`、`Permissions`、`WebBrowser`等。

这就是 ExpoKit 发挥作用的地方。当您选择从项目中弹出时，您可以选择将 ExpoKit 包含在弹出的项目中。包含 ExpoKit 将确保您应用中使用的所有 Expo 依赖项将继续工作，并且还可以让您在应用被弹出后继续使用 Expo SDK 的所有功能。

要更深入地了解弹出过程，您可以阅读 Expo 文档，链接为[`docs.expo.io/versions/latest/expokit/eject`](https://docs.expo.io/versions/latest/expokit/eject)。

# React Native 开发工具

与任何开发工具一样，灵活性和易用性之间存在权衡。我鼓励您在进行 React Native 开发工作流时首先使用 Expo，除非您确定需要访问原生代码。

# Expo

这是从[expo.io](http://expo.io)网站上获取的：

"Expo 是围绕 React Native 构建的免费开源工具链，帮助您使用 JavaScript 和 React 构建原生 iOS 和 Android 项目。"

Expo 正在成为一个自己的生态系统，由五个相互连接的工具组成：

+   **Expo CLI**：Expo 的命令行界面。

我们一直在使用 Expo CLI 来创建、构建和提供应用程序。CLI 支持的所有命令列表可以在官方文档中找到，链接如下：

[`docs.expo.io/versions/latest/workflow/expo-cli`](https://docs.expo.io/versions/latest/workflow/expo-cli)

+   **Expo 开发者工具**：这是一个基于浏览器的工具，每当通过`expo start`命令从终端启动 Expo 应用程序时，它会自动运行。它为您的开发中应用程序提供活动日志，并快速访问本地运行应用程序并与其他开发人员共享应用程序。

+   **Expo 客户端**：适用于 Android 和 iOS 的应用程序。这个应用程序允许您在设备上的 Expo 应用程序中运行您的 React Native 项目，而无需安装它。这使开发人员可以在真实设备上进行热重载，或者与其他人共享开发代码，而无需安装它。

+   **Expo Snack**：托管在[`snack.expo.io`](https://snack.expo.io)，这个网络应用程序允许您在浏览器中使用 React Native 应用程序，并实时预览您正在工作的代码。如果您曾经使用过 CodePen 或 JSFiddle，Snack 是将相同的概念应用于 React Native 应用程序。

+   **Expo SDK**：这是一个 SDK，其中包含了一组精彩的 JavaScript API，提供了在基本 React Native 软件包中找不到的本机功能，包括使用设备的加速计、相机、通知、地理位置等。这个 SDK 已经与使用 Expo 创建的每个新项目一起提供。

这些工具共同组成了 Expo 工作流程。使用 Expo CLI，您可以创建并构建具有 Expo SDK 支持的新应用程序。CLI 还提供了一种简单的方式，通过自动将您的代码推送到 Amazon S3 并为项目生成 URL 来为您的开发中应用程序提供服务。然后，CLI 生成一个与托管代码链接的 QR 码。在您的 iPhone 或 Android 设备上打开 Expo Client 应用程序，扫描 QR 码，BOOM，您的应用程序就在那里，配备了热重载！由于应用程序托管在 Amazon S3 上，您甚至可以实时与其他开发人员共享开发中的应用程序。

# React Native CLI

使用命令创建新的 React Native 应用程序的原始引导方法如下：

```jsx
react-native init 
```

这是由 React Native CLI 提供的。如果您确定需要访问应用程序的本机层，则可能只会使用这种引导新应用程序的方法。

在 React Native 社区中，使用这种方法创建的应用程序被称为纯 React Native 应用程序，因为所有的开发和本地代码文件都暴露给开发人员。虽然这提供了最大的自由，但也迫使开发人员维护本地代码。如果你是一个 JavaScript 开发人员，因为你打算仅使用 JavaScript 编写本地应用程序而跳上 React Native 的车，那么在 React Native 项目中维护本地代码可能是这种方法最大的缺点。

另一方面，在使用已经引导的应用程序时，您将可以访问更多的第三方插件。

直接访问代码库的本地部分。您还将能够绕过 Expo 目前的一些限制，特别是无法使用后台音频或后台 GPS 服务。

# CocoaPods

一旦你开始使用具有使用本地代码的组件的应用程序，你也将在开发中使用 CocoaPods。CocoaPods 是 Swift 和 Objective-C Cocoa 项目的依赖管理器。它几乎与 npm 相同，但是管理的是本地 iOS 代码的开源依赖，而不是 JavaScript 代码。

在本书中我们不会经常使用 CocoaPods，但 React Native 在其 iOS 集成中使用 CocoaPods，因此对管理器的基本了解可能会有所帮助。就像`package.json`文件包含了使用 npm 管理的 JavaScript 项目的所有包一样，CocoaPods 使用`Podfile`列出项目的 iOS 依赖关系。同样，这些依赖项可以使用以下命令安装：

```jsx
 pod install
```

CocoaPods 需要 Ruby 才能运行。在命令行上运行以下命令来验证 Ruby 是否已安装：

```jsx
 ruby -v 
```

如果没有，可以使用 Homebrew 命令安装：

```jsx
 brew install ruby
```

一旦 Ruby 被安装，CocoaPods 可以通过命令安装：

```jsx
sudo gem install cocoapods
```

如果在安装过程中遇到任何问题，可以阅读官方 CocoaPods *入门*指南[`guides.cocoapods.org/using/getting-started.html`](https://guides.cocoapods.org/using/getting-started.html)。

# 规划您的应用程序并选择您的工作流程

在尝试选择最适合您的应用程序需求的开发工作流程时，有一些事情您应该考虑：

+   我是否需要访问代码库的本地部分？

+   我是否需要任何 Expo 不支持的第三方包，需要运行 react-native link 命令？

+   当应用程序不在前台时，是否需要播放音频？

+   当应用程序不在前台时，是否需要位置服务？

+   我是否愿意至少在 Xcode 和 Android Studio 中进行工作？

根据我的经验，Expo 通常是最好的起点。它为开发过程提供了许多好处，并且在应用程序超出原始要求时，可以通过退出过程来获得逃生舱。我建议只有在确定您的应用程序需要 Expo 应用程序无法提供的内容，或者确定您将需要处理本机代码时，才使用 React Native CLI 开始开发。

我还建议浏览托管在[`native.directory`](http://native.directory)的 Native Directory。该网站拥有大量用于 React Native 开发的第三方软件包目录。该网站上列出的每个软件包都有估计的稳定性、流行度和链接到文档。然而，Native Directory 最好的功能可能是能够按照它们支持的设备/开发类型（包括 iOS、Android、Expo 和 Web）来过滤软件包。这将帮助您缩小软件包选择范围，并更好地指示应采用哪种工作流程。

# 如何做...

我们将从 React Native CLI 设置我们的应用程序开始，这将创建一个新的纯 React Native 应用程序，使我们可以访问所有本机代码，但也需要安装 Xcode 和 Android Studio。

您可能还记得第一章中*设置您的环境*，其中一些步骤已经详细介绍了。无需重新安装已在那里描述的任何列在此处的内容。

1.  首先，我们将安装所有与纯 React Native 应用程序一起工作所需的依赖项，从 macOS 的 Homebrew（[`brew.sh/`](https://brew.sh/)）软件包管理器开始。如项目主页上所述，Homebrew 可以通过以下命令轻松从终端安装：

```jsx
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

1.  安装 Homebrew 后，可以使用它来安装 React Native 开发所需的依赖项：Node.js 和`nodemon`。如果您是 JavaScript 开发人员，您可能已经安装了 Node.js。您可以通过以下命令检查它是否已安装：

```jsx
node -v
```

这个命令将列出已安装的 Node.js 的版本。请注意，您需要 Node.js 的 8 版本或更高版本来进行 React Native 开发。如果 Node.js 尚未安装，您可以通过以下命令使用 Homebrew 安装它：

```jsx
brew install node
```

1.  我们还需要`nodemon`包，React Native 在幕后使用它来启用开发过程中的实时重新加载等功能。通过以下命令使用 Homebrew 安装`nodemon`：

```jsx
brew install watchman
```

1.  当然，我们还需要 React Native CLI 来运行引导 React Native 应用程序的命令。可以通过以下命令全局安装它：

```jsx
npm install -g react-native-cli
```

1.  安装了 CLI 之后，创建一个新的纯 React Native 应用程序只需要以下命令：

```jsx
react-native init name-of-project
```

这将在一个新的`name-of-project`目录中创建一个新的项目。这个项目暴露了所有的原生代码，并且需要 Xcode 来运行 iOS 应用程序和 Android Studio 来运行 Android 应用程序。幸运的是，为了支持 iOS React Native 开发安装 Xcode 是一个简单的过程。第一步是从 App Store 下载 Xcode 并安装它。第二步是安装 Xcode 命令行工具。要做到这一点，打开 Xcode，从 Xcode 菜单中选择“首选项”，打开位置面板，并从命令行工具下拉菜单中安装最新版本：

![](img/d954acd0-be2c-4e74-aebc-e06fa08acb92.png)

1.  很遗憾，为了支持 Android React Native 开发设置 Android Studio 并不是一件轻而易举的事情，需要一些非常具体的步骤来安装它。由于这个过程特别复杂，并且有可能在你阅读本章时已经发生了变化，我建议参考官方文档，获取安装所有 Android 开发依赖的深入和最新的说明。这些说明托管在以下 URL：

[`facebook.github.io/react-native/docs/getting-started.html#java-development-kit`](https://facebook.github.io/react-native/docs/getting-started.html#java-development-kit)

1.  现在所有的依赖都已经安装好了，我们可以通过命令行运行我们的纯 React Native 项目。iOS 应用程序可以通过以下方式执行：

```jsx
react-native run-ios
```

Android 应用程序可以通过以下方式启动：

```jsx
react-native run-android
```

在尝试打开 Android 应用程序之前，请确保您已经运行 Android 模拟器。这些命令应该在关联的模拟器上启动您的应用程序，安装新应用程序，并在模拟器中运行应用程序。如果您对这些命令的任何一个行为不符合预期遇到任何问题，您可能可以在此处找到答案：React Native 故障排除文档，托管在此处：

[`facebook.github.io/react-native/docs/troubleshooting.html#content`](https://facebook.github.io/react-native/docs/troubleshooting.html#content)

# Expo CLI 设置

可以使用终端通过以下命令使用 npm 安装 Expo CLI：

```jsx
npm install -g expo-cli
```

Expo CLI 可用于执行 Expo GUI 客户端可以执行的所有操作。有关可以使用 CLI 运行的所有命令，请查看此处的文档：

[`docs.expo.io/versions/latest/workflow/expo-cli`](https://docs.expo.io/versions/latest/workflow/expo-cli)

# 使用 NativeBase 进行跨平台 UI 组件

与 Web 上的 Bootstrap 类似，NativeBase 是一组 React Native 组件，用于提高 React Native 应用程序开发的效率。这些组件涵盖了在原生应用程序中构建 UI 的各种用例，包括操作表、徽章、卡片、抽屉和网格布局。

NativeBase 是一个支持纯 React Native 应用程序（使用 React Native CLI 通过`react-native init`创建的应用程序）和 Expo 应用程序的库。有关将 NativeBase 安装到一种项目或另一种项目中的说明在 NativeBase 文档的“入门”部分中概述，托管在此处：

[`github.com/GeekyAnts/NativeBase#4-getting-started`](https://github.com/GeekyAnts/NativeBase#4-getting-started)

由于这种情况，我们将在本教程的“准备就绪”部分中概述这两种情况。

# 准备就绪

无论您使用哪种引导方法来完成此教程，我们都将尽可能保持教程的“如何做…”部分一致。我们需要考虑的一个区别是每种应用程序创建方法的项目命名约定。纯 React Native 应用程序以 Pascal 大小写（MyCoolApp）命名，而 Expo 应用程序以 kebab 大小写（my-cool-app）命名。如果您正在创建纯 React Native 应用程序，可以使用应用程序名称`NativeBase`，如果您正在使用 Expo，可以将其命名为`native-base`。

# 使用纯 React Native 应用程序（React Native CLI）

假设您已经按照本章的介绍安装了 React Native CLI。如果没有，请立即使用`npm`安装：

```jsx
npm install -g react-native-cli
```

要使用 CLI 创建一个新的纯 React 应用程序，我们将使用以下命令：

```jsx
 react-native init NativeBase
```

这将在当前目录中的名为`NativeBase`的文件夹中创建一个新的纯 React Native 应用程序。下一步是安装所需的对等依赖项。让我们`cd`进入新的`NativeBase`目录，并使用`npm`安装`native-base`包：

```jsx
npm install native-base --save
```

或者，您可以使用`yarn`：

```jsx
yarn add native-base
```

最后，我们将使用以下命令安装本机依赖项：

```jsx
react-native link
```

如果我们在 IDE 中打开项目并查看这个纯 React Native 应用程序的文件夹结构，我们会看到与此时习惯的 Expo 应用程序有一些细微的差异。首先，存储库有一个`ios`和一个`android`文件夹，分别包含各自平台的本机代码。项目的根目录还有一个`index.js`文件，这个文件在使用 Expo 引导的应用程序中不包括。在使用 Expo 制作的应用程序中，这个文件会被隐藏起来，就像`ios`和`android`文件夹一样，如下所示：

```jsx
import { AppRegistry } from 'react-native';
import App from './App';

AppRegistry.registerComponent('NativeBase', () => App);
```

这只是在运行时为您的 React Native 应用程序提供引导过程。`AppRegistry`从`react-native`包中导入，主要的`App`组件从目录根目录的`App.js`文件中导入，并且使用两个参数调用`AppRegistry`方法`registerComponent`：我们应用的名称（`NativeBase`）和一个返回`App`组件的匿名函数。有关`AppRegistry`的更多信息，您可以在这里找到文档：

[`facebook.github.io/react-native/docs/appregistry.html`](https://facebook.github.io/react-native/docs/appregistry.html)

另一个小的区别是在`App.js`样板代码中存在两套开发说明，通过使用`Platform`组件显示适当的开发说明。

每当看到第三方 React Native 包的安装说明包括运行以下命令时，请记住停下来思考：

```jsx
 react-native link
```

通常可以安全地假定它与 Expo 应用程序不兼容，除非另有明确说明。在 NativeBase 的情况下，我们有选择使用任一设置，因此让我们接下来介绍使用 Expo 进行引导的其他选项。

# 使用 Expo 应用

在使用 Expo 创建的应用程序中设置 Native Base 就像使用`npm`或`yarn`安装所需的依赖项一样简单。首先，我们可以在命令行上使用 Expo CLI 创建应用程序：

```jsx
 expo init native-base 
```

创建应用程序后，我们可以`cd`进入它，并使用`npm`安装 NativeBase 的依赖项：

```jsx
npm install native-base @expo/vector-icons --save
```

或者，您可以使用`yarn`：

```jsx
yarn add native-base @expo/vector-icons
```

在使用 Expo 时，NativeBase 文档建议在`App.js`组件的`componentWillMount`方法中使用`Expo.Font.loadAsync`方法异步加载字体。我们将在本示例的*如何做*部分的适当步骤中介绍如何做到这一点。您可以使用以下命令从 CLI 启动应用程序：

```jsx
 expo start 
```

# 如何做到这一点...

1.  我们将首先在`App.js`中的`App`组件中添加我们将使用的导入。虽然这个应用程序不会有太多的功能，但我们将使用许多来自 NativeBase 的组件，以了解它们如何帮助改进您的工作流程，如下所示：

```jsx
import React, { Component } from 'react';
import { View, Text, StyleSheet } from 'react-native'
import {
  Spinner,
  Button,
  Body,
  Title,
  Container,
  Header,
  Fab,
  Icon,
} from 'native-base';
```

1.  接下来，让我们声明`App`类并定义一个起始的`state`对象。我们将添加一个 FAB 部分，以展示 NativeBase 如何让您轻松地向应用程序添加弹出菜单按钮。我们将使用`fabActive`布尔值来跟踪是否应该显示此菜单。稍后在`render`方法中，我们还将使用`loading`布尔值，如下所示：

```jsx
export default class App extends Component {
  state = {
    loading: true
    fabActive: false
  }
  // Defined on following steps
}
```

1.  您可能还记得在本示例的*准备工作*部分中，如果您正在使用 Expo 开发应用程序，NativeBase 建议通过`Expo.Font.loadAsync`函数加载 NativeBase 使用的字体。在`componentWillMount`方法中，我们将初始化并等待`require`字体的加载，然后将`state`上的`loading`属性设置为`false`。`loading`属性将在`render`方法中被引用，以确定应用程序是否已经完成加载，如下所示：

```jsx
// Other import statements import { Font, AppLoaded } from 'expo';

export default class App extends Component {
  state = {
    fabActive: false
  }

 async componentWillMount() {
 await Font.loadAsync({
 'Roboto': require('native-base/Fonts/Roboto.ttf'),
 'Roboto_medium': require('native-base/Fonts/Roboto_medium.ttf'),
 'Ionicons': require('@expo/vector-icons/fonts/Ionicons.ttf'),
 });
    this.setState({ loading: false });
 }
  // Defined on following steps
}
```

1.  由于这个应用程序主要是 UI，我们准备开始构建`render`函数。为了确保在使用字体之前加载它们，如果`state`的`loading`属性为 true，我们将返回 App 占位符 Expo 组件`AppLoading`，否则我们将渲染 App UI。`AppLoading`将指示应用程序继续显示应用程序的启动画面，直到组件被移除。

如果您选择使用纯 React Native 项目开始此示例，您将无法访问 Expo 组件。在这种情况下，您可以简单地返回一个空的`View`而不是`AppLoading`。

1.  我们将从`Container`组件开始，以及`Header`、`Body`和`Title`辅助组件。这将作为页面的容器，显示页面顶部带有标题“Header Title”的标题！

```jsx
  render() {
    if (this.state.loading) {
      return <AppLoading />;
    } else {
      return (
        <Container>
          <Header>
            <Body>
              <Title>Header Title!</Title>
            </Body>
          </Header>
        </Container>
      );
    }
  }
```

此时，应用程序应该类似于以下屏幕截图：

！[](assets/0de58427-fa11-43e7-8b9f-33ad9287a412.png)

1.  在以下代码中，`Header`将具有来自 NativeBase 的一些其他 UI 元素。 `Spinner`组件允许轻松显示带有传递的所需颜色的加载旋转器。与原始的`TouchableOpacity`组件相比，`Button`组件提供了更多的内置可定制性。在这里，我们使用`block`属性将按钮扩展到其容器，并在每个按钮上使用`info`和`success`属性来应用它们各自的默认蓝色和绿色背景颜色：

```jsx
      <Container>
        <Header>
          <Body>
            <Title>Header Title!</Title>
          </Body>
        </Header>
 <View style={styles.view}>
 <Spinner color='green' style={styles.spinner} />
 <Button block info
 onPress={() => { console.log('button 1 pressed') }}
 >
 <Text style={styles.buttonText}>Click Me! </Text>
 </Button>
 <Button block success
 onPress={() => { console.log('button 2 pressed') }}
 >
 <Text style={styles.buttonText}>No Click Me!</Text>
 </Button>
 {this.renderFab()}
 </View>
      </Container>
```

1.  前面的渲染函数还引用了我们尚未定义的`renderFab`方法。这利用了`Icon`和`Fab`组件。 NativeBase 在内部使用与 Expo 相同的`vector-icons`包（如果未提供`type`属性，则默认为 Ionicon 字体），这在第三章的“使用字体图标”配方中有介绍，请参考该配方获取更多信息：

```jsx
  renderFab = () => {
    return (
      <Fab active={this.state.fabActive}
        direction="up"
        style={styles.fab}
        position="bottomRight"
        onPress={() => this.setState({ fabActive:
        !this.state.fabActive })}>
        <Icon name="share" />
        <Button style={styles.facebookButton}
          onPress={() => { console.log('facebook button pressed') }}
        >
          <Icon name="logo-facebook" />
        </Button>
        <Button style={styles.twitterButton}
          onPress={() => { console.log('twitter button pressed')}}
        >
          <Icon name="logo-twitter" />
        </Button>
      </Fab>
    );
  }
```

1.  让我们用一些样式来完善这个配方，以便在`View`中对齐事物并将颜色应用到我们的布局中，如下所示：

```jsx
const styles = StyleSheet.create({
  view: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
    paddingBottom: 40
  },
  buttonText: {
    color: '#fff'
  },
  fab: {
    backgroundColor: '#007AFF'
  },
  twitterButton: {
    backgroundColor: '#1DA1F2'
  },
  facebookButton: {
    backgroundColor: '#3B5998'
  },
  spinner: {
    marginBottom: 180
  }
});
```

1.  回顾已完成的应用程序，现在有一个漂亮的跨平台 UI 分布，易于使用：

！[](assets/d5fc973a-9043-4415-a3f9-3c9ad419b6ac.png)

# 工作原理...

虽然这个配方更复杂的部分是设置应用程序本身，但我们快速回顾了 NativeBase 提供的一些组件，这些组件可能有助于您更有效地开发下一个应用程序。如果您喜欢在类似于 Bootstrap（[`getbootstrap.com/`](https://getbootstrap.com/)）或 Semantic-UI（[`semantic-ui.com/`](https://semantic-ui.com/)）在 Web 平台上提供的基于小部件的系统中工作，请务必尝试 NativeBase。有关 NativeBase 提供的所有组件及其使用方法的更多信息，您可以在[`docs.nativebase.io/Components.html`](http://docs.nativebase.io/Components.html)找到官方文档。

# 使用 glamorous-native 来为 UI 组件设置样式

作为 JavaScript 开发人员，您可能熟悉 Web 上的 CSS 以及它如何用于样式化网页和 Web 应用程序。最近，一种称为 CSS-in-JS 的技术出现在 Web 开发中，它利用 JavaScript 的力量来调整 CSS，以实现更模块化、基于组件的样式化方法。CSS-in-JS 工具的主要好处之一是它们能够生成针对给定元素范围的样式，而不是默认的 JavaScript 级联行为。范围 CSS 允许开发人员以更可预测和模块化的方式应用样式。这反过来增加了在较大组织中的可用性，并使打包和发布样式化组件变得更容易。如果您想了解 CSS-in-JS 的工作原理或 CSS-in-JS 的概念来源，我在 gitconnected Medium 博客上写了一篇名为《CSS-in-JS 简史：我们是如何到达这里以及我们将去哪里》的文章，托管在：

[`levelup.gitconnected.com/a-brief-history-of-css-in-js-how-we-got-here-and-where-were-going-ea6261c19f04`](https://levelup.gitconnected.com/a-brief-history-of-css-in-js-how-we-got-here-and-where-were-going-ea6261c19f04)。

React Native 捆绑的`StyleSheet`组件是 CSS-in-JS 的实现。在 Web 上最受欢迎的 CSS-in-JS 实现之一是`glamorous`，这是由备受尊敬的 Kent C. Dodds 创建的库。这个库启发了出色的 React Native 样式库`glamorous-native`，我们将在这个示例中使用它。

# 准备工作

我们需要为这个示例创建一个新的应用程序。在设置期间，此软件包不需要运行以下命令：

```jsx
react-native link
```

因此，应该可以在 Expo 应用程序中正常工作。让我们将示例命名为`glamorous-app`。

我们还需要安装 glamorous-app 包。这可以通过`npm`安装：

```jsx
npm install --save glamorous-native
```

或者，我们可以使用`yarn`：

```jsx
yarn add glamorous-native
```

# 如何做...

1.  让我们首先在`App.js`中导入我们需要的所有依赖项，如下所示：

```jsx
import React from 'react';
import glamorous from 'glamorous-native';
```

1.  我们的应用程序将需要一个包含`View`元素，以容纳应用程序中显示的所有其他组件。我们将使用`glamorous`来为此元素传递样式对象，而不是像我们在所有先前的示例中所做的那样，通过传递给`StyleSheet`组件的对象来对此元素进行样式化。我们将使用`view`方法，它返回一个样式化的`View`组件，我们将其存储在一个名为`Container`的`const`中，以便以后使用，如下所示：

```jsx
const Container = glamorous.view({
  flex: 1,
  justifyContent: 'center',
  alignItems: 'center',
  backgroundColor: '#fff',
});
```

1.  同样地，我们将使用`glamorous.text`添加三个样式化的`Text`组件。通过这样做，我们有了另外三个样式化和明确定义名称的组件，可以在`render`中使用，如下所示：

```jsx
const Headline = glamorous.text({
  fontSize: 30,
  paddingBottom: 8
});

const SubHeading = glamorous.text({
  fontSize: 26,
  paddingBottom: 8
});

const ButtonText = glamorous.text({
  fontSize: 18,
  color: 'white'
});
```

1.  我们还将使用`glamorous.touchableHighlight`方法制作一个可重用的`Button`组件。这种方法展示了`glamorous`组件也可以用不同类型的多个样式声明来创建。在这种情况下，传递给`touchableHighlight`的第二个参数是一个函数，根据元素上定义的`props`来更新`backgroundColor`样式，如下所示：

```jsx
const Button = glamorous.touchableHighlight(
  { padding: 10 },
  props => ({backgroundColor: props.warning ? 'red' : 'blue'})
);
```

1.  我们还可以创建内联样式的组件，这要归功于`glamorous`提供的特殊版本的 React Native 组件。我们将使用一个`Image`组件，但是不是从`react-native`中导入，而是从导入的`glamorous`包中使用`Image`组件，如下所示：

```jsx
const { Image } = glamorous;
```

1.  现在，我们准备声明`App`组件。`App`只需要一个`render`函数来渲染我们所有新样式化的组件，如下所示：

```jsx
export default class App extends React.Component {
  render() {
    // Defined in following steps.
  }
}
```

1.  让我们开始构建`render`函数，通过添加在*步骤 2*中创建的`Container`组件。代码可读性的改进已经显而易见。`Container`被明确定义，并且不需要其他属性或属性来声明样式，如下所示：

```jsx
  render() {
 return (
 <Container>
 // Defined on following steps
 </Container>
 );
  }
```

1.  让我们添加从导入的`glamorous`库中提取的`Image`组件，这是在*步骤 5*中完成的。请注意，我们能够直接在组件上声明样式属性，如`height`、`width`和`borderRadius`，而不是像普通的`Image`组件那样：

```jsx
      <Container>
 <Image
 height={250}
 width={250}
 borderRadius={20}
 source={{ uri: 'http://placehold.it/250/3B5998' }}
 />
        // Defined on following steps
      </Container>
```

1.  现在，我们将添加在*步骤 3*中创建的`Headline`和`Subheading`组件。就像`Container`组件一样，这两个组件的可读性要比一个`View`和两个`Text`元素好得多：

```jsx
      <Container>
        <Image
          height={250}
          width={250}
          borderRadius={20}
          source={{ uri: 'http://placehold.it/250/3B5998' }}
        />
 <Headline>I am a headline</Headline>
 <SubHeading>I am a subheading</SubHeading>
        // Defined in following steps
      <Container>
```

1.  最后，我们将添加在*步骤 4*中创建的`Button`组件，以及在*步骤 3*中创建的`ButtonText`组件。两个按钮都有一个`onPress`方法，就像任何`TouchableOpacity`或`TouchableHighlight`组件一样，但第二个`Button`还有一个`warning`属性，导致它具有红色背景而不是蓝色：

```jsx
        <Button
          onPress={() => console.log('Thanks for clicking me!')}
        >
          <ButtonText>
            Click Me!
          </ButtonText>
        </Button>
        <Button
          warning
          onPress={() => console.log(`You shouldn't have clicked me!`)}
        >
          <ButtonText>
            Don't Click Me!
          </ButtonText>
        </Button>
```

1.  所有我们的`glamorous`组件都已添加到`render`方法中。如果你运行这个应用程序，你应该会看到一个完全样式化的用户界面。

![](img/acf7555d-906d-4fc6-be06-c266bedcdddb.png)

# 它是如何工作的...

在*步骤 2*和*步骤 3*中，我们使用相应的`glamorous`方法创建了带有样式的`View`和`Text`组件，并传入了一个包含应该应用于该特定组件的所有样式的对象。

在*步骤 4*中，我们通过应用与前几步创建`View`和`Text`组件相同的方法，创建了一个可重用的`Button`样式组件。然而，这个组件中声明样式的方式是不同的，并展示了`glamorous-native`在处理样式时的多功能性。您可以将任意数量的样式集合作为参数传递给`glamorous`组件构造函数，它们都将被应用。这包括动态样式，通常采用在组件上定义的 props 来应用不同的样式。在*步骤 10*中，我们使用了我们的`Button`元素。如果存在`warning`属性，就像在`render`中的第一个`Button`上一样，`backgroundColor`将是`red`。否则，它将是`blue`。这为在多种类型的组件上应用简单和可重用的主题提供了一个非常好的系统。

在*步骤 5*中，我们从`glamorous`库中提取了`Image`组件，以替代 React Native 的`Image`组件。这个特殊版本的组件与其 React Native 对应组件的行为相同，同时还能够直接对元素本身应用样式。在*步骤 8*中，我们使用了该组件，我们能够应用`height`、`width`和`borderRadius`样式，而无需使用`style`属性。

# 使用 react-native-spinkit 添加动画加载指示器

无论您正在构建什么样的应用程序，您的应用程序很有可能需要等待某种数据，无论是加载资产还是等待来自 AJAX 请求的响应。当出现这种情况时，您可能还希望您的应用程序能够向用户指示某个必需的数据仍在加载中。解决这个问题的一个易于使用的解决方案是使用`react-native-spinkit`。这个包提供了 15 个（其中四个仅适用于 iOS）专业外观、易于使用的加载指示器，用于在您的应用程序中显示数据加载时。

这个包需要运行以下命令：

```jsx
react-native link
```

因此，可以安全地假设它不会与 Expo 应用程序一起工作（除非随后将该应用程序弹出）。这将为我们提供另一个依赖于纯 React Native 工作流程的配方。

# 入门

现在我们已经确定了这个配方将在纯 React Native 中构建，我们可以通过以下方式从命令行初始化一个名为`SpinKitApp`的新应用程序：

```jsx
react-native init SpinKitApp
```

这个命令将开始搭建过程。完成后，`cd`进入新的`SpinKitApp`目录，并使用`npm`添加`react-native spinkit`：

```jsx
npm install react-native-spinkit@latest --save
```

或者使用`yarn`：

```jsx
yarn add react-native-spinkit@latest
```

安装了库之后，我们必须使用以下命令将其链接起来才能使用：

```jsx
react-native link
```

此时，应用程序已经启动，并且已安装了依赖项。然后可以通过以下方式在 iOS 或 Android 模拟器中运行应用程序：

```jsx
react-native run-ios
```

或者，使用这个：

```jsx
react-native run-android
```

在 iOS 模拟器中启动纯 React Native 项目时，如果希望指定设备，可以传递`simulator`参数设置为所需设备的字符串值。例如，`react-native run-ios --simulator="iPhone X"`将在模拟的 iPhone X 中启动应用程序。

在通过命令行启动纯 React Native 项目的 Android 模拟器时，必须在运行此命令之前打开您打算使用的 Android 模拟器。

在这个配方中，我们还将再次使用`randomcolor`库。使用`npm`安装它：

```jsx
npm install randomcolor --save
```

或者使用`yarn`：

```jsx
yarn add randomcolor
```

# 如何做到这一点...

1.  我们将首先在项目的根目录的`App.js`文件中添加依赖项，如下所示：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  View,
  TouchableOpacity,
  Text
} from 'react-native';
import Spinner from 'react-native-spinkit';
import randomColor from 'randomcolor';
```

1.  在这个配方中，我们将设置应用程序循环显示`react-native-spinkit`提供的所有加载旋转器类型。为此，让我们创建一个包含每种可能的旋转器类型的字符串数组。由于最后四种类型在 Android 中不完全受支持，它们在 Android 上都将显示为相同的`Plane`旋转器，如下所示：

```jsx
const types = [
  'Bounce',
  'Wave',
  'WanderingCubes',
  'Pulse',
  'ChasingDots',
  'ThreeBounce',
  'Circle',
  '9CubeGrid',
  'FadingCircleAlt',
  'FadingCircle',
  'CircleFlip',
  'WordPress',
  'Arc',
  'ArcAlt'
];
```

1.  现在，我们可以开始构建`App`组件。我们将需要一个具有四个属性的`state`对象：一个`isVisible`属性来跟踪是否应该显示旋转器，一个用于保存当前旋转器类型的`type`属性，一个用于保持在`types`数组中的位置的`typeIndex`，以及一个颜色。我们将通过简单调用`randomColor()`来将颜色初始化为随机十六进制代码，如下所示：

```jsx
export default class App extends Component {
  state = {
    isVisible: true,
    typeIndex: 0,
    type: types[0],
    color: randomColor()
  }
}
```

1.  我们将需要一个函数来改变`Spinner`组件的属性，我们将在`render`方法中稍后定义。这个函数简单地将`typeIndex`增加一，或者如果已经到达数组的末尾，则将其设置回`0`，然后相应地更新`state`，如下所示：

```jsx
  changeSpinner = () => {
    const { typeIndex } = this.state;
    let nextType = typeIndex === types.length - 1 ? 0 : typeIndex +
    1;
    this.setState({
      color: randomColor(),
      typeIndex: nextType,
      type: types[nextType]
    });
  }
```

1.  `render`方法将由`Spinner`组件组成，包裹在`TouchableOpacity`组件中，用于改变`Spinner`的类型和颜色。我们还将添加一个`Text`组件来显示当前`Spinner`的类型，如下所示：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <TouchableOpacity onPress={this.changeSpinner}>
          <Spinner
            isVisible={this.state.isVisible}
            size={120}
            type={this.state.type}
            color={this.state.color}
          />
        </TouchableOpacity>
        <Text style={styles.text}>{this.state.type}</Text>
      </View>
    );
  }
```

1.  最后，让我们为中心内容添加一些样式，并通过`text`类增加`Text`元素的字体大小，如下所示：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#fff',
  },
  text: {
    paddingTop: 40,
    fontSize: 25
  }
});
```

1.  完成这个示例后，我们应该看到一个在按下时改变的加载器。感谢`react-native-spinkit`，这就是向我们的 React Native 应用程序添加时髦的加载指示器所需的一切！

![](img/f21b3fc5-4779-4778-923e-f4d33138cfe2.png)

# 它是如何工作的...

在*步骤 5*中，我们定义了应用程序的`render`方法，其中我们使用了`Spinner`组件。`Spinner`组件有四个可选的属性：

+   `isVisible`：一个布尔值，用于确定是否应该显示组件。默认值：`true`

+   `color`：一个十六进制代码，用于确定旋转器的颜色。默认值：`#000000`

+   `size`：以像素为单位确定旋转器的大小。默认值：`37`

+   `type`：一个字符串，确定要使用的旋转器类型。默认值：`Plane`

由于`Spinner`组件上的`isVisible`属性设置为`state`对象上的`isVisible`的值，所以我们可以简单地在长时间运行的过程开始时（例如等待来自 AJAX 请求的响应），将此属性切换为`true`，并在操作完成时将其设置回`false`。

# 还有更多...

尽管我们在这个示例中创建的应用程序相当简单，但它展示了`react-native-spinkit`如何实现，以及如何实际使用需要`react-native link`命令的第三方包。由于无数的开源贡献者的辛勤工作，有各种各样的第三方包可供在下一个 React Native 应用程序中使用。能够利用任何符合应用程序需求的第三方包，无论这些包有什么要求，都将是规划和开发 React Native 项目的重要工具。

# 使用 react-native-side-menu 添加侧边导航菜单

侧边菜单是一种常见的 UX 模式，用于在移动应用程序中显示选项、控件、应用程序设置、导航和其他次要信息。第三方包`react-native-side-menu`提供了一种在 React Native 应用程序中实现侧边菜单的出色且简单的方法。在这个示例中，我们将构建一个具有侧边菜单的应用程序，其中包含可以改变背景的按钮。

# 准备工作

设置`react-native-side-menu`包不需要命令：

```jsx
 react-native link
```

所以请随意使用 Expo 或纯 React Native 应用程序创建此应用。我们需要为这个示例创建一个新的应用程序，并且出于项目命名的目的，我们将假设这个应用程序是使用 Expo 构建的，并将其命名为`side-menu-app`。如果您使用纯 React Native，可以将其命名为`SideMenuApp`。

我们还需要使用`npm`将`react-native-side-menu`安装到我们的项目中。

```jsx
npm install react-native-side-menu --save
```

或者，使用`yarn`：

```jsx
yarn add react-native-side-menu
```

# 如何做...

1.  让我们从在项目根目录的`App.js`文件中添加我们需要的所有导入开始这个示例。其中一个导入是`Menu`组件，我们将在后面的步骤中创建它：

```jsx
import React from 'react';
import { StyleSheet, Text, View, TouchableOpacity } from 'react-native';
import SideMenu from 'react-native-side-menu';
import Menu from './components/Menu';
```

1.  接下来，让我们定义`App`类和初始的`state`。在这个应用程序中，`state`只需要两个属性：一个`isOpen`布尔值，用于跟踪侧边菜单何时应该打开，以及一个`selectedBackgroundColor`属性，其值是表示当前选定的背景颜色的字符串，如下所示：

```jsx
export default class App extends React.Component {
  state = {
    isOpen: false,
    selectedBackgroundColor: 'green'
  }
  // Defined in following steps
}
```

1.  我们的应用程序将需要一个方法来改变`state`上的`selectedBackgroundColor`属性。这个方法以一个`color`字符串作为参数，并将该颜色设置为`selectedBackgroundColor`。它还会将`state.isOpen`设置为`false`，以便在从菜单中选择颜色时关闭侧边菜单，如下所示：

```jsx
  changeBackgroundColor = color => {
    this.setState({
      isOpen: false,
      selectedBackgroundColor: color,
    });
  }
```

1.  我们准备好定义`App`的`render`方法。首先，让我们设置`Menu`组件，以便在下一步中可以被`SideMenu`使用。我们还没有创建`Menu`组件，但我们将使用`onColorSelected`属性来传递`changeBackgroundColor`方法，如下所示：

```jsx
  render() {
    const menu = <Menu onColorSelected={this.changeBackgroundColor}
   />;

    // Defined in next step
  }
```

1.  渲染的 UI 由四个部分组成。第一个是一个`View`组件，它有一个与`state.selectedBackgroundColor`绑定的`style`属性。这个`View`组件包含一个单独的`TouchableOpacity`按钮组件，每当按下它时就会打开侧边菜单。`SideMenu`组件有一个必需的`menu`属性，它接受将充当侧边菜单本身的组件，因此我们将`Menu`组件传递给这个属性，如下所示：

```jsx
  render() {
    const menu = <Menu onColorSelected={this.changeBackgroundColor} />;

    return (
 <SideMenu
 menu={menu}
 isOpen={this.state.isOpen}
 onChange={(isOpen) => this.setState({ isOpen })}
 >
 <View style={[
 styles.container,
 { backgroundColor: this.state.selectedBackgroundColor }
 ]}>
 <TouchableOpacity
 style={styles.button}
 onPress={() => this.setState({ isOpen: true })}
 >
 <Text style={styles.buttonText}>Open Menu</Text>
 </TouchableOpacity>
 </View>
 </SideMenu>
 );
  }
```

1.  作为这个组件的最后一步，让我们添加基本样式来居中布局，并应用颜色和字体大小，如下所示：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  button: {
    backgroundColor: 'black',
    padding: 20,
    borderRadius: 10
  },
  buttonText: {
    color: 'white',
    fontSize: 25
  }
});
```

1.  是时候创建`Menu`组件了。让我们在`component`文件夹中创建一个`Menu.js`文件。我们将从组件导入开始。就像我们在之前的示例中所做的那样，我们还将使用`Dimensions`将应用程序窗口的尺寸存储在一个变量中，以便应用样式，如下所示：

```jsx
import React from 'react';
import {
  Dimensions,
  StyleSheet,
  View,
  Text,
  TouchableOpacity
} from 'react-native';

const window = Dimensions.get('window');
```

1.  `Menu`组件只需要是一个展示性组件，因为它没有状态或生命周期钩子的需求。该组件将接收`onColorSelected`作为属性，我们将在下一步中使用它，如下所示：

```jsx
const Menu = ({ onColorSelected }) => {
  return (
    // Defined on next step
  );
}

export default Menu;
```

1.  `Menu`组件的主体只是一系列`TouchableOpacity`按钮，当按下时，会调用`onColorSelected`，传入相应的颜色，如下所示：

```jsx
    <View style={styles.menu}>
      <Text style={styles.heading}>Select a Color</Text>
      <TouchableOpacity onPress={() => onColorSelected('green')}>
        <Text style={styles.item}>
          Green
        </Text>
      </TouchableOpacity>
      <TouchableOpacity onPress={() => onColorSelected('blue')}>
        <Text style={styles.item}>
          Blue
        </Text>
      </TouchableOpacity>
      <TouchableOpacity onPress={() => onColorSelected('orange')}>
        <Text style={styles.item}>
          Orange
        </Text>
      </TouchableOpacity>
      <TouchableOpacity onPress={() => onColorSelected('pink')}>
        <Text style={styles.item}>
          Pink
        </Text>
      </TouchableOpacity>
      <TouchableOpacity onPress={() => onColorSelected('cyan')}>
        <Text style={styles.item}>
          Cyan
        </Text>
      </TouchableOpacity>
      <TouchableOpacity onPress={() => onColorSelected('yellow')}>
        <Text style={styles.item}>
          Yellow
        </Text>
      </TouchableOpacity>
      <TouchableOpacity onPress={() => onColorSelected('purple')}>
        <Text style={styles.item}>
          Purple
        </Text>
      </TouchableOpacity>
    </View>
```

1.  让我们为`Menu`组件添加一些样式，应用颜色和字体大小。请注意，我们还在*步骤 7*中定义的`window`变量来设置组件的`height`和`width`，使其等于屏幕的大小，如下所示：

```jsx
const styles = StyleSheet.create({
  menu: {
    flex: 1,
    width: window.width,
    height: window.height,
    backgroundColor: '#3C3C3C',
    justifyContent: 'center',
    padding: 20,
  },
  heading: {
    fontSize: 22,
    color: '#f6f6f6',
    fontWeight: 'bold',
    paddingBottom: 20
  },
  item: {
    fontSize: 25,
    paddingTop: 10,
    color: '#f6f6f6'
  }
});
```

1.  我们的应用程序已经完成！当按下“打开菜单”按钮时，一个平滑动画的侧边菜单将从左侧滑出，显示一个供用户选择的颜色列表。当从列表中选择颜色时，应用程序的背景颜色会更改，并且菜单会滑动回关闭状态：

![](img/73ecede4-e54a-465c-af8c-e4e19f2dbe0e.png)

# 工作原理...

在*步骤 4*中，我们为主`App`组件创建了`render`函数。我们将`Menu`组件存储在`menu`变量中，以便可以清晰地将其传递给`SideMenu`的`menu`属性，就像我们在*步骤 5*中所做的那样。我们通过`onColorSelected`属性将`changeBackgroundColor`类方法传递给我们的`Menu`组件，以便我们可以使用它来正确更新`App`组件中的`state`。

然后，我们将`Menu`组件作为`menu`属性传递给`SideMenu`，将这两个组件连接在一起。第二个属性是`isOpen`，它决定侧边菜单是否应该打开。第三个属性`onChange`接受一个回调函数，每次菜单打开或关闭时都会执行。`onChange`回调提供了一个`isOpen`参数，我们用它来更新`state`中`isOpen`的值，以便保持同步。

包含的`View`元素具有一个`style`属性，设置为一个数组，其中包含*步骤 6*中定义的`container`样式和一个具有`backgroundColor`键设置为`state`中的`selectedBackgroundColor`的对象。这将导致`View`组件的背景颜色在更新时更改为此值。

在*步骤 8*和*步骤 9*中，我们构建了`Menu`组件的`render`方法。每个`TouchableOpacity`按钮都连接到`onColorSelected`，传入与按下按钮相关联的颜色。这反过来在父`App`类中运行`changeBackgroundColor`，在设置`state.isOpen`为`false`时更新`state.selectedBackgroundColor`，导致背景颜色改变并关闭侧边菜单。

# 使用 react-native-modalbox 添加模态框

许多移动 UI 的常见部分是模态框。模态框是隔离数据的理想解决方案，以有意义的方式提醒用户更新的信息，显示阻止其他用户交互的必需操作（如登录屏幕）等等。

我们将使用第三方包`react-native-modalbox`。该软件包提供了一个易于理解和多功能的 API，用于创建模态框，选项包括以下内容：

+   `position`：顶部、底部、中心

+   `entry`：模态框进入的方向-顶部或底部？

+   `backdropColor`

+   `backdropOpacity`

有关所有可用选项，请参阅文档：

[`github.com/maxs15/react-native-modalbox`](https://github.com/maxs15/react-native-modalbox)

# 准备就绪

我们将需要一个新的应用程序来完成这个示例。`react-native-modalbox`软件包对 Expo 友好，因此我们可以使用 Expo 创建此应用程序。我们将为这个应用程序命名为`modal-app`。如果使用纯 React Native 项目，可以使用`ModalApp`这样的名称，以匹配命名约定。

我们还需要第三方软件包。可以使用`npm`进行安装：

```jsx
npm install react-native-modalbox --save
```

或者，使用`yarn`：

```jsx
yarn add react-native-modalbox
```

# 如何做...

1.  让我们从在项目的根目录中打开`App.js`文件并添加导入开始，如下所示：

```jsx
import React from 'react';
import Modal from 'react-native-modalbox';
import {
  Text,
  StyleSheet,
  View,
  TouchableOpacity
} from 'react-native';
```

1.  接下来，我们将定义和导出`App`组件，以及初始的`state`对象，如下所示。对于这个应用程序，我们只需要一个`isOpen`布尔值来跟踪我们的模态框是否应该打开或关闭：

```jsx
export default class App extends Component {
  state = {
    isOpen: false
  };
  // Defined on following steps
}
```

1.  让我们跳到下一个构建`render`方法。该模板由两个`TouchableOpacity`按钮组件组成，当按下时，打开它们各自的模态框。我们将在接下来的步骤中定义这两个模态框。这些按钮将调用两种方法来渲染每个模态框的两个模态框组件，如下所示：

```jsx
  render = () => {
    return (
      <View style={styles.container}>
        <TouchableOpacity
          onPress={this.openModal1}
          style={styles.button}
        >
          <Text style={styles.buttonText}>
            Open Modal 1
          </Text>
        </TouchableOpacity>
        <TouchableOpacity
          onPress={this.openModal2}
          style={styles.button}
        >
          <Text style={styles.buttonText}>
            Open Modal 2
          </Text>
        </TouchableOpacity>
        {this.renderModal1()}
        {this.renderModal2()}
      </View>
    );
  }
```

1.  现在，我们准备定义`renderModal1`方法。`Modal`组件需要一个`ref`属性来分配一个字符串，这将用于在我们想要打开或关闭它时引用`Modal`，如下所示：

```jsx
  renderModal1 = () => {
    return(
      <Modal
        style={[styles.modal, styles.modal1]}
        ref={'modal1'}
        onClosed={this.onClose}
        onOpened={this.onOpen}
      >
        <Text style={styles.modalText}>
          Hello from Modal 1
        </Text>
      </Modal>
    )
  }
```

1.  让我们接下来添加`openModal1`方法。这个方法是在*步骤 3*中我们在`render`方法中添加的第一个`TouchableOpacity`组件上通过`onPress`调用的。通过将`modal1`字符串传递给我们在*步骤 4*中定义的`Modal`组件上的`ref`属性，我们能够将模态框访问为`this.refs.modal1`。在这个引用上调用`open`方法将打开模态框。关于这一点，我们将在本教程末尾的*它是如何工作的...*部分详细介绍。添加`openModal1`方法如下：

```jsx
  openModal1 = () => {
    this.refs.modal1.open();
  }
```

1.  我们在*步骤 4*中定义的`Modal`还有`onClosed`和`onOpened`属性，它们分别接受一个在模态框关闭或打开时执行的回调函数。让我们接下来为这些属性定义回调函数。在本教程中，我们将只是简单地使用`console.log`作为概念验证，如下所示：

```jsx
  onClose = () => {
    console.log('modal is closed');
  }

  onOpen = () => {
    console.log('modal is open');
  }
```

1.  我们准备好定义第二个模态框了。这个`Modal`组件的`ref`属性将设置为字符串`modal2`，我们将添加两个其他可选的属性，这些属性在另一个模态框上没有使用。第一个是`position`，可以设置为`top`、`bottom`或`center`（默认）。`isOpen`属性提供了通过布尔值打开和关闭模态框的第二种方法。模态框的内容有一个带有 OK 按钮的`TouchableOpacity`，当按下时，将会将`state`对象上的`isOpen`布尔值设置为`false`，关闭模态框，如下所示：

```jsx
renderModal2 = () => {
    return(
      <Modal
        style={[styles.modal, styles.modal2]}
        ref={'modal2'}
        position={'bottom'}
        onClosed={this.onCloseModal2}
        isOpen={this.state.isOpen}
      >
        <Text style={styles.modalText}>
          Hello from Modal 2
        </Text>
        <TouchableOpacity
          onPress={() => this.setState({isOpen: false})}
          style={styles.button}
        >
          <Text style={styles.buttonText}>
            OK
          </Text>
        </TouchableOpacity>
      </Modal>
    )
  }
```

1.  由于我们使用`state`布尔值`isOpen`来操纵模态框的状态，`openModal2`方法将演示另一种打开和关闭模态框的方法。通过将`state`上的`isOpen`设置为`true`，第二个模态框将打开，如下所示：

```jsx
  openModal2 = () => {
    this.setState({ isOpen: true });
  }
```

1.  您可能还注意到，在*步骤 7*中定义的第二个模态框有一个不同的`onClosed`回调。如果用户按下 OK 按钮，`state`上的`isOpen`值将成功更新为`false`，但如果他们通过触摸背景来关闭模态框，它将不会。添加`onCloseModal2`方法可以确保`state`的`isOpen`值无论用户如何关闭模态框都能正确保持同步，如下所示：

```jsx
  onCloseModal2 = () => {
    this.setState({ isOpen: false });
  }
```

1.  这个教程的最后一步是应用样式。我们将有一个用于共享模态框样式的`modal`类，用于每个模态框独特样式的`modal1`和`modal2`类，以及用于将颜色、填充和边距应用于按钮和文本的类，如下所示：

```jsx
const styles = StyleSheet.create({
  container: {
    backgroundColor: '#f6f6f6',
    justifyContent: 'center',
    alignItems: 'center',
    flex: 1
  },
  modal: {
    width: 300,
    justifyContent: 'center',
    alignItems: 'center'
  },
  modal1: {
    height: 200,
    backgroundColor: "#4AC9B0"
  },
  modal2: {
    height: 300,
    backgroundColor: "#6CCEFF"
  },
  modalText: {
    fontSize: 25,
    padding: 10,
    color: '#474747'
  },
  button: {
    backgroundColor: '#000',
    padding: 16,
    borderRadius: 10,
    marginTop: 20
  },
  buttonText: {
    fontSize: 30,
    color: '#fff'
  }
});
```

1.  这个教程已经完成，我们现在有一个应用程序，其中有两个基本的模态框，通过按钮按下显示，并在同一个组件中和谐共存：

![](img/14001108-37b7-461a-9205-f5b17bf10e78.png)

# 工作原理...

在*步骤 4*中，我们定义了第一个`Modal`组件。我们定义了`onClosed`和`onOpened`属性，将`onClose`和`onOpen`类方法传递给这些属性。每当打开这个`Modal`组件时，`this.onOpen`都会触发，当`Modal`关闭时，`this.onClose`会执行。虽然在这个示例中我们没有对这些方法做任何激动人心的事情，但这些钩子可以作为记录与模态框相关的用户操作的绝佳机会。或者，如果模态框包含一个表单，`onOpen`可以用来预先填充一些表单输入数据，而`onClose`可以将表单数据保存到`state`对象中，以便在关闭模态框时使用。

在*步骤 5*中，我们定义了第一个`TouchableOpacity`按钮组件在按下时执行的方法：`openModal1`。在这个方法中，我们利用了`Modal`组件的引用。引用是 React 本身的一个核心特性，它为组件实例提供了一个存储在组件渲染方法中创建的 DOM 节点和/或 React 元素的位置。就像 React（和 React Native）组件有状态和属性（在类组件中为`this.state`和`this.props`）一样，它们也可以有引用（存储在`this.ref`上）。有关 React 中引用的工作原理，请查看文档：

[`reactjs.org/docs/refs-and-the-dom.html`](https://reactjs.org/docs/refs-and-the-dom.html)

由于我们将第一个`Modal`上的`ref`属性设置为字符串`modal1`，因此我们可以在`openModal1`方法中使用引用`this.ref.modal1`访问同一个组件。由于`Modal`有一个`open`和一个`close`方法，调用`this.ref.modal1.open()`会打开具有`modal1`引用的`Modal`。

这并不是打开和关闭`Modal`组件的唯一方法，就像我们在*步骤 7*中定义的第二个模态框所示。由于这个组件有一个`isOpen`属性，可以通过改变传递给该属性的布尔值来打开或关闭模态框。通过将`isOpen`设置为状态的`isOpen`值，我们可以使用此模态框中的确定按钮来从内部关闭模态框，通过在`state`上将`isOpen`设置为 false。在*步骤 8*中，我们定义了`openModal2`方法，也说明了通过改变`state`上的`isOpen`值来打开第二个模态框。

在*步骤 9*中，我们为保持`state`的`isOpen`值同步定义了一个单独的`isClosed`回调，以防用户通过按下背景而不是模态框的确定按钮来关闭模态框。另一种策略是通过向`Modal`组件添加`backdropPressToClose`属性并将其设置为`false`来禁用用户通过按下背景来关闭模态框。

`react-native-modalbox`包提供了许多其他可选的属性，可以使模态框的创建更加容易。在这个示例中，我们使用了`position`来声明第二个模态框应该放在屏幕底部，您可以在文档中查看`Modal`的所有其他可用属性：

[`github.com/maxs15/react-native-modalbox`](https://github.com/maxs15/react-native-modalbox)

`react-native-modalbox`库支持在单个组件中使用多个模态框；但是，尝试在这些模态框中的多个上使用`isOpen`属性将导致所有这些模态框同时打开，这不太可能是期望的行为。
