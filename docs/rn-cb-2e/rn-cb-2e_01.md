# 第一章：设置您的环境

自第一版以来，React Native 生态系统已经发生了相当大的变化。特别是，开源工具 Expo.io 已经简化了项目初始化和开发阶段，使得在 React Native 中的工作比 0.36 版本中更加愉快。

使用 Expo 工作流程，您将能够仅使用 JavaScript 构建本机 iOS 和 Android 应用程序，在 iOS 模拟器和 Android 模拟器中进行实时重新加载，并通过 Expo 应用程序轻松测试您的应用程序在任何真实设备上。除非您需要访问原生代码（比如，与来自单独代码库的遗留原生代码集成），否则您可以完全在 JavaScript 中开发应用程序，而无需使用 Xcode 或 Android Studio。如果您的项目最终发展成必须支持原生代码的应用程序，Expo 提供了将您的项目弹出的功能，这将使您的应用程序变成原生代码，以便在 Xcode 和 Android Studio 中使用。有关弹出您的 Expo 项目的更多信息，请参阅第十章，*应用程序工作流程和第三方插件*。

Expo 是一种很棒的方式，可以在 Android 和 iOS 设备上构建功能齐全的应用程序，而无需处理原生代码。让我们开始吧！

在本章中，我们将涵盖以下主题：

+   安装依赖项

+   初始化您的第一个应用程序

+   在模拟器/仿真器中运行您的应用程序

+   在真实设备上运行您的应用程序

# 技术要求

本章将涵盖您在本书中将要使用的工具的安装。它们包括：

+   Expo

+   Xcode（仅适用于 iOS 模拟器，仅限 macOS）

+   Android Studio

+   Node.js

+   看门人

# 安装依赖项

构建我们的第一个 React Native 应用程序的第一步是安装依赖项以开始。

# 安装 Xcode

如本章介绍的，Expo 为我们提供了一种工作流程，可以避免完全在 Xcode 和 Android Studio 中工作，因此我们可以完全使用 JavaScript 进行开发。但是，为了在 iOS 模拟器中运行您的应用程序，您需要安装 Xcode。

Xcode 需要 macOS，因此只有在 macOS 上才能在 iOS 模拟器中运行您的 React Native 应用程序。

Xcode 应该从 App Store 下载。您可以在 App Store 中搜索 Xcode，或使用以下链接：

[`itunes.apple.com/app/xcode/id497799835`](https://itunes.apple.com/app/xcode/id497799835).

Xcode 是一个庞大的下载，所以这部分需要一些时间。安装 Xcode 后，您可以通过 Finder 中的`应用程序`文件夹运行它：

1.  这是您启动 Xcode 时将看到的第一个屏幕。请注意，如果这是您第一次安装 Xcode，您将看不到最近的项目列在右侧：

![](img/fccac559-1b6f-4fbd-a9e1-df9cf7777be4.png)

1.  从菜单栏中选择`Xcode | 偏好设置...`如下：

![](img/a59eb8ca-7c3e-4103-b906-a6603f12f75f.png)

1.  单击组件选项卡，并从提供的模拟器列表中安装一个模拟器：

![](img/cc9d1bb1-78ee-46a4-b705-067611cfe959.png)

1.  安装完成后，您可以从菜单栏中打开模拟器：`Xcode | 打开开发人员工具`|`模拟器`：

**![](img/786b9447-3a97-44b9-bfa3-b73e1aeb1b03.png)**

# 安装 Android Studio

Android Studio 附带官方的 Android 模拟器，这是 Expo 在开发过程中推荐使用的模拟器。

# 操作步骤如下...

1.  从[`developer.android.com/studio/`](https://developer.android.com/studio/)下载 Android Studio。

1.  打开下载的文件，并将`Android Studio.app`图标拖动到`应用程序`文件夹图标中：

![](img/d969b12d-6b0c-42e6-b833-ec29541b3248.png)

1.  安装完成后，我们需要更改 Android Studio 的偏好设置。打开 Android Studio，然后从系统栏中的 Android Studio 菜单中打开`偏好设置`。在`偏好设置`子菜单中，选择`外观和行为`|`系统设置`|`Android SDK`。在`SDK 工具`选项卡下，确保您已安装某个版本的 Android SDK 构建工具，并且如果尚未安装，则安装它。

1.  我们还需要通过编辑`~/.bash_profile`或`~/.bashrc`将 Android SDK 位置添加到系统的`PATH`中。您可以通过添加以下行来实现：

```jsx
export PATH=$PATH:/Users/MY_USER_NAME/Library/Android/sdk
```

请务必将`MY_USER_NAME`替换为您的系统用户名。

1.  在 macOS 上，您还需要在`~/.bash_profile`或`~/.bashrc`中的`PATH`中添加`platform-tools`。您可以通过添加以下行来实现：

```jsx
  PATH=$PATH:/Users/MY_USER_NAME/Library/Android/platform-tools 
```

请务必将`MY_USER_NAME`替换为您的系统用户名。

如果您以前从未编辑过`.bash_profile`或`.bashrc`文件，或者对`PATH`不熟悉，您可以从以下资源获取有关它们的作用以及如何使用它们的更多信息：

+   [`www.rc.fas.harvard.edu/resources/documentation/editing-your-bashrc/`](https://www.rc.fas.harvard.edu/resources/documentation/editing-your-bashrc/)

+   [`www.cyberciti.biz/faq/appleosx-bash-unix-change-set-path-environment-variable/`](https://www.cyberciti.biz/faq/appleosx-bash-unix-change-set-path-environment-variable/)

1.  如果`PATH`已正确更新，则`adb`命令应在终端中起作用。更改生效可能需要重新启动终端。

1.  在安装 Android Studio 的新环境中，您将看到一个欢迎屏幕。开始一个新的应用程序以完全打开软件。然后，从窗口右上角的按钮中选择 AVD 管理器，如下面的步骤所示：

![](img/6ca12bc5-4d5a-49db-ab25-2c21e2602074.png)

1.  在打开的模态中按“创建虚拟设备”。

1.  在“选择硬件”屏幕中选择一个设备，然后按“下一步”：

![](img/ee381f01-110e-4ccf-af75-b169a486ea64.png)

1.  在“系统映像”屏幕的“推荐”选项卡下下载一个系统映像：

![](img/cc9b9206-b23c-450b-9784-fcd823387812.png)

1.  在最后一个屏幕上按“完成”，Android Studio 将创建您的新虚拟设备。可以通过按右上角按钮行中的播放按钮随时运行该设备：

![](img/9ec76ee1-bcef-4a91-a28d-5b3a261d8be0.png)

在开发过程中在 Android 模拟器上运行您的应用程序，Expo 以前建议使用出色的第三方模拟器 Genymotion。然而，从 Expo 版本 29 开始，他们现在建议使用随 Android Studio 一起提供的官方模拟器。

您可以按照官方 Expo 文档中提供的逐步指南，确保 Android Studio 设置正确，以便与 Expo 开发工作流程正常工作。该指南可以在[`docs.expo.io/versions/latest/workflow/android-studio-emulator`](https://docs.expo.io/versions/latest/workflow/android-studio-emulator)找到。

这就是您开始使用 Expo 开发第一个 React Native 应用程序所需的所有设置！但是，对于纯 React Native 应用程序（非 Expo 应用程序），您需要执行一些额外的步骤。纯 React Native 应用程序开发将在第十章*应用程序工作流和第三方插件*中深入介绍。由于此设置过程涉及的内容较多且可能会更改，我建议参考官方指南。您可以在 React Native: 入门指南中找到这些说明，网址为[`facebook.github.io/react-native/docs/getting-started.html`](https://facebook.github.io/react-native/docs/getting-started.html)，位于使用本地代码构建项目部分。

一旦模拟器打开，通过菜单栏选择您想要的 iOS 设备：硬件|设备| [IOS 版本] | [iOS 设备]。将来在模拟器中运行 Expo 应用程序时，应该会自动使用相同的设备。

如果您在终端中使用 Expo CLI 运行以下命令，即可启动应用程序：

```jsx
 expo start
```

该命令将构建您的应用程序并在 Web 浏览器中打开 Expo 开发者工具。在 Expo 开发者工具中，选择在 iOS 模拟器上运行。

# 还有更多...

一旦您在模拟器中启动了应用程序，您就可以在不从 Xcode 中打开模拟器的情况下按下*在 iOS 模拟器上运行*按钮。它还应该记住您的设备选择。从 Xcode 中打开模拟器提供了一种简单的方式来选择您首选的 iOS 设备进行模拟。

如果您按照 Expo 指南中的步骤进行操作，可以在*安装 Android Studio*部分找到，您还会发现它涵盖了安装虚拟设备，我们可以将其作为模拟器运行。要在模拟器上启动您的应用程序，只需在 Android Studio 中打开您安装的 Android 虚拟设备，在终端中运行`expo start`命令，并选择在 Android 设备/模拟器上运行。

# 安装 Node.js

Node.js 是构建在 Chrome 的 V8 JavaScript 引擎上的 JavaScript 运行时，旨在构建可扩展的网络应用程序。Node 允许在终端中执行 JavaScript，并且是任何 Web 开发人员的必不可少的工具。有关 Node.js 的更多信息，您可以阅读项目的*关于 Node.js*页面，网址为[`nodejs.org/en/about/`](https://nodejs.org/en/about/)。

根据博览会安装文档，Node.js 在技术上并不是必需的，但一旦你开始实际构建东西，你会想要它。Node.js 本身不在本书的范围之内，但你可以在本章末尾的*进一步阅读*部分查看更多关于使用 Node.js 的资源。

有许多安装 Node.js 的方法，因此很难推荐特定的安装方法。在 macOS 上，你可以通过以下方式之一安装 Node.js：

+   从项目网站[`nodejs.org/en/download/`](https://nodejs.org/en/download)下载并安装 Node.js。

+   通过 Homebrew 进行安装。如果你熟悉 Homebrew，这个过程在[`medium.com/@katopz/how-to-install-specific-nodejs-version-c6e1cec8aa11`](https://medium.com/@katopz/how-to-install-specific-nodejs-version-c6e1cec8aa11)中有简洁的说明。

+   通过 Node Version Manager（NVM; [`github.com/creationix/nvm`](https://github.com/creationix/nvm)）进行安装。NVM 允许你安装多个版本的 Node.js，并轻松在它们之间切换。使用存储库的 README 中提供的说明来安装 NVM。这是推荐的方法，因为它灵活，只要你习惯在终端中工作。

# 安装 Expo

Expo 项目曾经有一个基于 GUI 的开发环境叫做 Expo XDE，现在已经被一个名为 Expo Developer Tools 的基于浏览器的 GUI 取代。由于 Expo XDE 已经被弃用，现在创建新的 Expo 应用程序总是使用 Expo CLI。这可以通过终端使用 npm（Node.js 的一部分）安装，使用以下命令：

```jsx
 npm install expo-cli -g
```

在本书中，我们将会大量使用 Expo 来创建和构建 React Native 应用程序，特别是那些不需要访问原生 iOS 或 Android 代码的应用程序。使用 Expo 构建的应用程序在开发中有一些非常好的优势，帮助混淆原生代码，简化应用程序发布和推送通知，并提供许多有用的功能内置到 Expo SDK 中。有关 Expo 如何工作以及它如何适应 React Native 开发的更多信息，请参见第十章*应用程序工作流程和第三方插件*。

# 安装 Watchman

Watchman 是 React Native 内部使用的工具。它的目的是监视文件更新，并在发生更改时触发响应（如实时重新加载）。Expo 文档建议安装 Watchman，因为有报道称一些 macOS 用户在没有它的情况下遇到了问题。安装 Watchman 的推荐方法是通过 Homebrew。作为 macOS 的缺失软件包管理器，Homebrew 允许您直接从终端安装各种有用的程序。这是一个不可或缺的工具，应该在每个开发者的工具包中。

1.  如果您还没有安装 Homebrew，请在终端中运行以下命令进行安装（您可以在[`brew.sh/`](https://brew.sh/)了解更多信息并查看官方文档）：

```jsx
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

1.  安装 Homebrew 后，在终端中运行以下两个命令来安装`watchman`：

```jsx
brew update
brew install watchman
```

# 初始化您的第一个应用程序

这是你开始使用 Expo 开发第一个 React Native 应用所需的所有设置！但是，对于使用纯 React Native 应用（非 Expo 应用）需要执行一些额外的步骤。纯 React Native 应用的开发将在第十章 *应用工作流程和第三方插件*中进行深入讨论。由于这个设置过程有点复杂并且可能会发生变化，我建议参考官方指南。您可以在 React Native | 入门指南中找到这些说明

[`facebook.github.io/react-native/docs/getting-started.html`](https://facebook.github.io/react-native/docs/getting-started.html) 在使用本机代码构建项目选项卡下。从现在开始，我们可以使用 Expo 提供的魔法轻松创建新的开发应用。

我们将使用 Expo 通过 Expo CLI 创建我们的第一个应用程序。创建一个新应用程序就像运行以下命令一样简单：

```jsx
expo init project-name
```

运行此命令将首先提示您要创建哪种类型的应用程序：`blank`应用程序，没有添加功能，或者`tabs`应用程序，将创建一个具有最小标签导航的新应用程序。在本书的示例中，我们将使用`blank`应用程序选项。

选择了首选的应用程序类型后，在新的`project-name`目录中创建了一个新的、空的 Expo-powered React Native 应用，以及开始开发所需的所有依赖项。你只需要开始编辑新项目目录中的`App.js`文件就可以开始工作了。

要运行我们的新应用程序，我们可以`cd`进入目录，然后使用`expo start`命令。这将自动构建和提供应用程序，并在新的浏览器窗口中打开 Expo 开发者工具，用于开发中的 React Native 应用程序。

有关 Expo CLI 的所有可用命令列表，请查看[`docs.expo.io/versions/latest/guides/expo-cli.html`](https://docs.expo.io/versions/latest/guides/expo-cli.html)上的文档。

创建了我们的第一个应用程序后，让我们继续在 iOS 模拟器和/或 Android 模拟器中运行该应用程序。

# 在模拟器/模拟器中运行您的应用程序

您已经创建了一个新项目，并在上一步中使用 Expo 开始运行该项目。一旦我们开始对 React Native 代码进行更改，能够看到这些更改的结果将是件好事，对吧？由于 Expo，运行已安装的 iOS 模拟器或 Android 模拟器中的项目也变得更加简单。

# 在 iOS 模拟器上运行您的应用程序

在 Xcode 模拟器中运行您的应用程序只需点击几下。

1.  打开 Xcode。

1.  从菜单栏中打开模拟器：Xcode | 打开开发者工具 | 模拟器：

**![](img/f777110d-c8e4-438f-833d-330d03c6e695.png)**

1.  如果您运行以下命令，应用程序可以在终端中使用 Expo CLI 启动：

```jsx
expo start
```

该命令将构建您的应用程序并在您的 Web 浏览器中打开 Expo 开发者工具。在 Expo 开发者工具中，选择在 iOS 模拟器上运行。

1.  第一次通过“在 iOS 模拟器上运行”在 iOS 模拟器上运行 React Native 应用程序时，Expo 应用程序将安装在模拟器上，并且您的应用程序将自动在 Expo 应用程序中打开。模拟的 iOS 将询问您是否要“在“Expo”中打开”？选择“打开”：

![](img/beedc679-8089-4313-89bd-183d734aa54d.png)

1.  加载后，您将看到 Expo 开发者菜单。您可以通过在键盘上按下*command*键 + D 来在此菜单和您的 React Native 应用程序之间切换：

![](img/a8edd55d-7cc2-4483-bb52-63930f7c5c1e.png)

# 还有更多...

一旦您在模拟器中启动了应用程序，您将能够按下“在 iOS 模拟器上运行”按钮，而无需从 Xcode 中打开模拟器。它还应该记住您的设备选择。从 Xcode 中打开模拟器提供了一种简单的方式来选择您首选的 iOS 设备进行模拟。

您可以通过在键盘上按下*command*键 + *M*来在您的 React Native 应用程序和 Expo 开发者菜单之间切换，后者是一个列出了开发中有用功能的列表。Expo 开发者菜单应该看起来像这样：

![](img/7cdc2973-e449-46da-b0f8-db3fa04351e0.png)

# 在真实设备上运行您的应用程序

在真实设备上运行您的开发应用程序就像在模拟器上运行应用程序一样简单。通过原生 Expo 应用程序和 QR 码的巧妙组合，只需点击几下，就可以在真实设备上运行应用程序！

# 在 iPhone 或 Android 上运行您的应用程序

你可以通过三个简单的步骤在手机上运行正在开发中的应用程序：

1.  在 iPhone 上打开 App Store，或者在 Android 设备上打开 Google Play 商店。

1.  搜索并下载 Expo Client 应用程序。

1.  当您的应用程序在开发机器上运行时，您还应该在浏览器中打开 Expo 开发者工具。您应该在 Expo 开发者工具左侧菜单底部看到一个 QR 码。使用 iPhone 的原生相机应用程序，或 Android 上 Expo 客户端应用程序中的扫描 QR 码按钮，扫描 QR 码。这将在 Expo 客户端应用程序内打开您正在开发的应用程序。

您的 React Native 应用现在应该在您的真实设备上运行，并配备了实时重新加载！您还可以摇动设备，在 React Native 应用和 Expo 开发者菜单之间切换。

# 摘要

在本章中，我们已经介绍了开发 React Native 应用程序所需的所有步骤，包括初始化新项目，在计算机上模拟运行新项目以及在真实设备上运行开发应用程序。由于 Expo 的强大功能，现在比以往任何时候都更容易开始工作了。

现在您已经设置好了一切，是时候开始构建了！

# 进一步阅读

以下是涵盖类似主题的其他资源列表：

+   Expo 安装文档在[`docs.expo.io/versions/latest/introduction/installation.html`](https://docs.expo.io/versions/latest/introduction/installation.html)。

+   *Node.js Web Development* 在[`www.packtpub.com/mapt/book/web_development/9781785881503`](https://www.packtpub.com/mapt/book/web_development/9781785881503)

+   *介绍热重载 - React Native* 在[`facebook.github.io/react-native/blog/2016/03/24/introducing-hot-reloading.html`](https://facebook.github.io/react-native/blog/2016/03/24/introducing-hot-reloading.html)。这篇来自 React Native 团队的博客文章深入介绍了热重载的工作原理。

+   *使用 Expo 发布*在[`docs.expo.io/versions/latest/guides/publishing.html`](https://docs.expo.io/versions/latest/guides/publishing.html)。Expo 具有发布功能，允许您通过创建持久 URL 与其他开发人员共享正在开发中的 React Native 应用程序。

+   在[`snack.expo.io`](https://snack.expo.io)上体验 Expo Snack。类似于[codepen.io](http://codepen.io)或[jsfiddle.net](https://jsfiddle.net)，Snack 允许您在浏览器中实时编辑 React Native 应用程序！
