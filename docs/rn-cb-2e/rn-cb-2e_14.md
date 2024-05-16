# 第十四章：部署您的应用程序

在本章中，我们将介绍以下配方：

+   将开发构建部署到 iOS 设备

+   将开发构建部署到 Android 设备

+   将测试构建部署到 HockeyApp

+   将 iOS 测试构建部署到 TestFlight

+   将生产构建部署到 Apple 应用商店

+   将生产构建部署到 Google Play 商店

+   部署空中更新

+   优化 React Native 应用程序大小

# 介绍

如果您是独立开发者，您可能会经历几个不同的开发阶段。第一阶段将使您在个人 iOS 或 Android 设备上测试您的应用程序。在耗尽这个阶段之后，您可能会想要与一小部分人分享它，以获得用户反馈。最终，您将达到一个可以通过应用商店发布应用程序的阶段。本章将逐个介绍这些阶段，并涵盖推送更新到您的应用程序以及一些优化技巧。

# 将开发构建部署到 iOS 设备

在开发过程中，您可能会花费大部分时间使用 Xcode 安装的 iOS 模拟器测试您的 iOS 应用程序。虽然 iOS 模拟器是迄今为止性能最好且最接近在 iOS 设备上运行应用程序的方法，但它仍然不同于真实设备。iOS 模拟器使用计算机的 CPU 和 GPU 来渲染模拟的操作系统，因此根据您的开发机器，它可能表现得比实际设备更好（或更差）。

幸运的是，Expo 能够在实际设备上测试运行代码，这让我们离真正的最终产品更近了一步，但最终应用程序和在 Expo 中运行的开发应用程序之间仍然存在差异。如果您正在构建纯 React Native 应用程序，您将无法像使用 Expo 那样轻松地在设备上运行应用程序。

无论如何，您最终都希望在实际设备上测试真正的应用程序，以便体验最终产品的实际用户体验和性能。

在本配方中，我们将指导您将 React Native 应用程序部署到 iPhone 或 iPad。

# 准备工作

我们只需要一个新的纯 React Native 应用程序，我们将其命名为`TestDeployApp`。您可以通过以下命令创建该应用程序：

```jsx
 react-native init 
```

另外，请确保您的 iOS 设备通过 USB 连接到开发机器。

# 如何做...

1.  让我们首先在 Xcode 中打开新创建的 React Native iOS 项目。通过在左侧面板中选择项目的根目录来打开项目编辑器。

1.  在项目编辑器的“常规”选项卡下，在左侧的“目标”部分中选择 iOS 应用程序。在“签名”部分下，选择您的团队，如下所示：

![](img/17f1f486-4562-4110-8a55-a7129058c416.png)

1.  对于“目标”列表中的每个条目，重复此步骤两次。

1.  在目标选择器中选择您的设备，如下所示：

![](img/b959d5eb-7689-4e8e-8475-3f6e9afcb951.png)

1.  要在连接的设备上开始运行应用程序，只需按下播放按钮。您必须确保您的设备已插入，已解锁，并且已信任，才能在 Xcode 的设备列表中显示出来。如果这是您在此设备上首次运行您开发的应用程序，您还需要调整设置以信任来自您的开发者帐户的应用程序。在 iOS 设备上，此设置可以在“设置”|“常规”|“设备管理”中找到。

# 它是如何工作的...

将我们的开发构建部署到设备上只涉及指定一个团队，然后像在 Xcode 模拟器上使用一样运行应用程序，但是将目标设备更改为已插入的设备。我们使用本地主机打包程序来创建我们的捆绑文件。然后将此文件保存在设备上以供将来使用。请注意，由于这是开发构建，因此代码尚未像在最终发布时那样进行优化。在转到生产版本时，您将看到显着的性能提升。

# 将开发构建部署到 Android 设备

在开发 Android 应用程序时，您最有可能会在 Android 模拟器上运行应用程序。虽然方便，但与真实的 Android 设备相比，模拟器的性能较差。

测试应用程序的最佳方法是使用实际的 Android 设备。本教程将介绍将 React Native 应用程序部署到实际的 Android 设备。

# 准备就绪

我们只需要一个新的纯 React Native 应用程序，我们将其命名为`TestDeployApp`。您可以通过以下命令创建该应用程序：

```jsx
react-native init
```

此外，请确保您的 iOS 设备通过 USB 连接到开发计算机。

# 如何做...

1.  让我们首先在 Android Studio 中打开我们的 React Native Android 项目。

1.  接下来，按照以下步骤按下运行按钮：

![](img/7c3b453b-faae-4e88-9c3f-0c2b632fdd57.png)

1.  确保选择“选择运行设备”单选按钮，并且您的设备显示在列表中。按 OK 继续，如下所示：

![](img/e716a726-e91d-46c2-b09c-2cd2946e7c81.png)

# 还有更多...

当您运行应用程序时，React Native 打包程序应该会启动。如果没有，您将需要手动启动打包程序。如果您看到一个带有消息“无法获取 BatchedBridge”的错误屏幕，请确保您的捆绑包正确打包，或者“无法连接到开发服务器”，您应该能够通过在终端中运行以下命令来解决这个问题：

```jsx
adb reverse tcp:8081 tcp:8081
```

# 工作原理...

与 Xcode 类似，我们可以通过简单地插入一个真实的设备，按下运行，然后选择应用程序应该运行的设备来运行我们的应用程序。可能出现的唯一复杂情况是设置设备和开发机之间的通信。这些问题通常可以通过以下命令解决：

```jsx
adb reverse
```

这将从设备到主机计算机建立一个端口转发。这是一个开发版本，代码还没有优化，所以一旦应用程序作为生产版本构建，性能将会提高。

# 将测试版本部署到 HockeyApp

在将应用程序发布到市场之前，重要的是对应用程序进行压力测试，并在可能的情况下获得用户反馈。为了实现这一点，您需要创建一个签名的应用程序构建，可以与一组测试用户共享。对于一个强大的测试构建，您需要两样东西：应用程序性能的分析/报告，以及交付的机制。HockeyApp 为 iOS 和 Android 上的测试构建提供了这些以及更多功能。虽然苹果应用商店和谷歌 Play 商店的官方平台都提供了测试和分析功能，但 HockeyApp 提供了一个统一的处理这些问题的地方，以及度量、崩溃报告等的第二来源。

值得注意的是，HockeyApp 最近被微软收购。他们宣布 HockeyApp 产品将在 2019 年 11 月停止，并转而使用微软的 App Center。您可以在产品过渡页面上阅读更多信息[`hockeyapp.net/transition`](https://hockeyapp.net/transition)。本食谱将介绍如何将 React Native 应用程序部署到 HockeyApp 进行测试。我们将介绍 iOS 和 Android 版本的发布。

# 准备就绪

对于这个食谱，我们将使用上两个食谱中的相同的空的、纯净的 React Native 应用，我们将其命名为`TestDeployApp`。对于 iOS 部署，您需要加入苹果开发者计划，并且需要安装`cocoapods`。安装`cocoapods`的最简单方法是使用 homebrew，通过以下命令：

```jsx
 brew install cocoapods
```

您还需要拥有 HockeyApp 帐户，您可以在他们的网站 [`hockeyapp.net/`](https://hockeyapp.net/) 上注册。

# 如何做到...

1.  首先，我们需要在我们的应用程序中安装 `react-native-hockeyapp` 模块。打开终端，转到应用程序的根项目目录，并输入以下命令：

```jsx
        npm install react-native-hockeyapp --save
```

1.  进入你的 `ios/` 目录并初始化你的 Podfile：

```jsx
        pod init
```

1.  打开你的 Podfile 并在你的目标中添加 `pod "HockeySDK"`。

1.  回到终端，按照以下步骤安装 Podfile：

```jsx
        pod install
```

1.  现在，让我们打开 Xcode 并打开我们的 React Native 项目：(`ios/TestDeployApp.xcodeproj`)。

1.  我建议将您的 Bundle 标识符更改为比默认值更有意义的内容，请在常规设置对话框中更改如下：

![](img/dcb61823-96a7-4a23-9fae-8ec0ceb79d7c.png)

1.  将 `./ios/Pods/Pods.xcodeproj` 拖放到项目导航器中的 Libraries 组中，如下所示：

![](img/a9faaea8-c68a-4626-85a4-a77f0bb0f697.png)

1.  将位于 `./node_modules/react-native-hockeyapp/RNHockeyApp/RNHockeyApp` 的 `RNHockeyApp.h` 和 `RNHockeyApp.m` 文件拖放到相同的 Libraries 组中。

1.  接下来，我们将转到 HockeyApp 网站并在那里创建我们的应用程序。登录并点击新应用。

1.  由于我们的构建还没有准备好，所以在下一个模态中点击手动，而不是上传构建？创建应用程序。

1.  在创建应用程序表单中填写字段时，请确保与我们在 *步骤 6* 中定义的标题和 Bundle 标识符匹配，然后点击保存，如下所示：

![](img/7c49595c-2cde-4474-9626-6ed48f719a08.png)

1.  记下应用程序 ID，因为我们将在下一步中使用它。

1.  打开 `App.js` 并添加以下代码：

```jsx
        import HockeyApp from 'react-native-hockeyapp'; 

        export default class 
        TestDeployApp extends Component { 
          componentWillMount() { 
            HockeyApp.configure(YOUR_APP_ID_HERE, true); 
          } 

          componentDidMount() { 
            HockeyApp.start(); 
            HockeyApp.checkForUpdate(); 
          } 
        } 
```

1.  回到 Xcode，将通用 iOS 设备设置为目标目标并构建（Product | Build）应用程序，如下所示：

![](img/c8579b2c-6812-44fa-b86b-aa9288275ef3.png)

1.  现在，我们需要创建我们的 `.ipa` 文件。这可以通过 Xcode 菜单中的 Product | Archive 完成。

1.  这将打开归档列表。点击分发应用程序按钮开始创建 `.ipa` 的过程。

1.  选择开发选项并点击下一步。

1.  您的配置团队应该会自动选择。选择正确的团队后，点击下一步。

1.  保留默认的导出设置并点击下一步。在摘要页面上，也点击下一步。

1.  选择目标目录并点击导出。

1.  回到 *HockeyApp* 浏览器窗口，点击添加版本。

1.  将刚刚导出的`.ipa`文件拖放到模态窗口中。

1.  我们可以将设置保留为默认设置，所以继续按下一步，直到最后一个模态屏幕，然后在摘要屏幕上按下完成。这就是 iOS 应用的全部内容。您可以向*HockeyApp*应用添加用户，然后您的测试人员应该能够下载您的应用。让我们转到 Android 端。打开 Android Studio，然后打开 React Native 中的 Android 文件夹。

1.  重复*步骤 8*到*步骤 11*，将平台更改为 Android，如下所示：

![](img/dc9b38e8-c6ac-4821-8f03-b3dfe396616b.png)

1.  现在，我们需要构建我们的`.apk`文件。您可以在 React Native 文档中找到构建`.apk`的最新方法，位于：

[`facebook.github.io/react-native/docs/signed-apk-android.html`](https://facebook.github.io/react-native/docs/signed-apk-android.html)

1.  重复从我们的 Android 项目生成的`.apk`的*步骤 21*和*步骤 22*。

# 它是如何工作的...

对于这个教程，我们使用*HockeyApp*的两个主要功能：其 beta 分发和其 HockeySDK（支持崩溃报告、指标、反馈、身份验证和更新通知）。对于 iOS，beta 分发是通过*HockeyApp*托管的 OTA 企业分发机制完成的。当您签署您的应用时，您可以控制哪些设备可以打开它。*HockeyApp*只是发送通知并提供 URL 供 beta 测试人员通过其企业应用商店下载您的应用。Android 更简单，因为不需要担心应用是如何传输的。这意味着*HockeyApp*将`.apk`文件托管在测试人员可以下载和安装的 Web 服务器上。

有关在 Android 上设置*HockeyApp*的更多信息，您可以阅读官方文档[`support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk`](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)。

# 将 iOS 测试构建部署到 TestFlight

在*HockeyApp*出现之前，用于测试移动应用程序的最流行的服务是 TestFlight。事实上，它在这方面做得非常好，以至于苹果收购了其母公司并将其整合到了 iTunes Connect 中。TestFlight 现在作为苹果的官方应用程序测试平台。TestFlight 和*HockeyApp*之间有一些区别需要考虑。首先，TestFlight 在被苹果收购后变成了仅适用于 iOS。其次，在 TestFlight 中有两种测试样式：内部和外部。**内部测试**涉及与团队的开发人员或管理员角色成员共享应用程序，并将分发限制为每个设备 10 个设备上的 25 个测试人员。**外部测试**允许您邀请最多 2,000 名不必是您组织成员的测试人员。这也意味着这些测试人员不会使用您的设备配额。外部测试应用程序需要经过苹果进行的**Beta App Review**，这并不像苹果对将应用程序发布到 App Store 的审查那样严格，但这是一个很好的第一步。

本食谱侧重于将我们的 React Native 应用程序部署到 TestFlight 进行测试构建。我们将设置一个内部测试，因为我们不希望苹果审查我们的示例 React Native 应用程序，但是内部和外部测试的程序是相同的。

# 准备工作

对于这个食谱，我们将使用之前食谱中的相同的 React Native 应用程序模板，我们将其命名为`TestDeployApp`。您还需要加入苹果开发者计划，需要在 Xcode 中设置开发和分发证书，并且您的应用程序需要设置其 AppIcon。

# 操作步骤...

1.  让我们首先通过`ios/TestDeployApp.xcodeproj`文件在 Xcode 中打开我们的项目。

1.  正如上一篇食谱中所述，我还建议将您的 Bundle Identifier 更改为比默认值更有意义的内容，例如：

![](img/6fc38221-6cfa-4128-94c1-667c5811eab1.png)

1.  接下来，让我们登录到苹果开发者计划，并转到位于[https//:developer.apple.com/account/ios/identifier/bundle](https://developer.apple.com/account/ios/identifier/bundle)的 App ID 注册页面。

1.  在这里，填写项目的名称和 Bundle ID，然后按下“继续”按钮，接着按“注册”按钮，最后按“完成”按钮完成应用程序的注册。

1.  接下来，我们将登录到位于[`itunesconnect.apple.com`](https://itunesconnect.apple.com)的 iTunes Connect 网站。

1.  在 iTunes Connect 中，导航到“我的应用程序”，然后按“加号（+）”按钮并选择“新应用程序”以添加新应用程序。

1.  在新应用程序对话框中，填写名称和语言。选择与之前创建的 Bundle ID 匹配的 Bundle ID，并在 SKU 字段中添加一个唯一的应用程序引用，然后按“创建”。

1.  接下来，导航到您的应用程序的 TestFlight 部分，并确保填写本地化信息部分。

1.  让我们返回 Xcode 创建`.ipa`文件。选择通用 iOS 设备作为活动方案，然后通过 Xcode 菜单（产品|存档）创建文件。这将打开存档列表，您可以按“上传到 App Store”按钮上传应用程序。

1.  您的配置团队应该自动选择。确保选择正确的团队，然后按“选择”。创建存档后，按“上传”按钮。

1.  上传应用程序后，您需要等待直到收到来自 iTunes Connect 的电子邮件，通知您构建已完成处理。处理完成后，您可以返回 iTunes Connect 页面并打开内部测试视图。

1.  在内部测试部分，单击“选择要测试的版本”，然后选择您的构建，然后单击“下一步”按钮。在“导出合规性”屏幕上，按“确定”。

1.  我们准备添加内部测试人员。选择您想要测试该应用程序的用户，然后单击“开始测试”按钮，并在随后的模态中确认您的选择。您的用户现在应该收到邀请邮件来测试您的应用程序！

# 工作原理...

TestFlight 在 App Store 发布流程中扮演着一流的角色。苹果已经将其支持应用程序测试分发的支持直接集成到 iTunes Connect 中，为开发人员创建了一个流畅无缝的流程。这个过程与部署到 App Store 基本相同，只是在使用 iTunes Connect 时，您必须启用和配置测试。

对于测试人员来说，这也是一个无缝的体验。一旦您在 iTunes Connect 中添加了测试用户，他们将收到安装 TestFlight 应用程序的通知，从而可以轻松访问他们可以测试的应用程序。TestFlight 还通过不需要开发人员添加任何额外的第三方库或代码来支持 TestFlight，使开发人员的流程更加简单，而这在使用*HockeyApp*时是必需的。

# 将生产版本部署到苹果应用商店

一旦您彻底测试了您的应用程序，您就可以继续进行下一个（可能也是最激动人心的）步骤：发布到 Apple 应用商店。

这个步骤将指导您准备生产版本并将其提交到 Apple 应用商店。我们实际上不会将应用提交到商店，因为我们使用的是示例应用而不是生产就绪的应用。然而，流程的最后几个步骤非常简单。

# 准备工作

对于这个步骤，我们将再次使用之前的示例应用程序`TestDeployApp`。当然，您还需要加入苹果开发者计划，并在 Xcode 中设置开发和分发证书，就像本章前面讨论的那样。对于真正的生产应用程序部署，您还需要设置 AppIcon 并准备用于 iTunes 的应用程序截图。

# 如何做...

1.  让我们从使用`ios/TestDeployApp.xcodeproj`文件打开 Xcode 开始。

1.  如前所述，建议您将 Bundle Identifier 更改为比默认值更有意义的内容，因此请务必在“常规设置”对话框中更改它。

1.  在设备上以生产模式测试应用程序也是一个好主意。这可以通过将应用程序方案的构建配置（通过“产品”|“方案”|“编辑方案”菜单找到）更改为 Release 来完成，如下所示：

![](img/af39d72a-9c2b-48f2-a5c4-9b56602d1a30.png)

1.  接下来，您需要在 App ID 注册页面上注册应用，该页面位于：

[`developer.apple.com/account/ios/identifier/bundle`](https://developer.apple.com/account/ios/identifier/bundle)

这一步需要一个活跃的苹果开发者计划账户。

1.  填写项目的名称和 Bundle ID 字段，然后按 Continue 按钮。

1.  接下来，我们将登录到位于[`itunesconnect.apple.com`](https://itunesconnect.apple.com)的 iTunes Connect 网站。在“My Apps”部分，按加号(+)按钮，然后选择“新建应用”。

1.  您需要在以下对话框中填写名称和语言，然后选择与之前在步骤中创建的 Bundle ID 相匹配的 Bundle ID。此外，添加一个唯一的应用程序引用 SKU，并按 Create 按钮。

1.  让我们返回 Xcode 并创建`.ipa`文件。选择 Generic iOS Device 作为活动方案，并通过菜单（产品|存档）创建文件，这将打开存档列表。最后，按“上传到 App Store”。

1.  选择您的配置团队，然后按“选择”。

1.  一旦存档创建完成，按“上传”按钮。一旦构建完成，您将收到来自 iTunes Connect 的电子邮件。

1.  应用程序处理完毕后，返回到 iTunes Connect。在 App Store 部分，打开 App 信息并选择您的应用所适合的类别。

1.  在 iOS APP 下的 1.0 准备提交部分中打开。填写所有必填字段，包括应用程序截图、描述、关键字和支持 URL。

1.  接下来，在“构建”部分，选择我们在第 8 步中构建的`.ipa`。

1.  最后，填写版权和应用程序审查信息部分，然后点击“提交审核”按钮。

# 它是如何工作的...

在这个食谱中，我们介绍了将 iOS 应用程序发布到 App Store 的标准流程。在这种情况下，我们不需要遵循任何 React Native 特定的步骤，因为最终产品（`.ipa`文件）包含了运行 React Native 打包程序所需的所有代码，这将进而以发布模式构建`main.jsbundle`文件。

# 将生产版本部署到 Google Play 商店

本食谱将介绍准备我们的应用程序的生产版本并将其提交到 Google Play 商店的过程。与上一个食谱一样，我们将在实际提交到 App Store 之前停下来，因为这只是一个示例 React Native 应用程序，但是这个过程的其余部分也很简单。

# 准备工作

对于这个食谱，我们将使用本章节中一直使用的相同简单的 React Native 应用程序`TestDeployApp`。您需要拥有 Google Play 开发者帐户才能将应用程序提交到商店，并且如果您想要实际发布应用程序，还需要准备好所有的图标和 Play 商店的截图。

# 如何做...

1.  让我们从在 Android Studio 中打开 React Native 项目开始。第一步是构建`.apk`文件。正如本章前面提到的，从 React Native 项目创建生产 Android 应用程序的过程是复杂的，而且容易发生变化。访问 React Native 文档以获取有关创建`.apk`的信息：[`facebook.github.io/react-native/docs/signed-apk-android.html`](https://facebook.github.io/react-native/docs/signed-apk-android.html)。

1.  接下来，让我们在 Web 浏览器中打开 Google Play 开发者控制台，网址是[`play.google.com/apps/publish/`](https://play.google.com/apps/publish/)。

1.  让我们通过点击“添加新应用程序”来开始这个过程。填写标题字段，然后点击“上传 APK”按钮，如下所示：

![](img/2082ac1f-1326-45f1-bb3a-2ba990af147a.png)

1.  接下来，您将看到发布屏幕的 APK 部分。点击“上传您的第一个 APK 到生产”，然后拖放（或选择）您的`.apk`文件。

1.  接下来将出现一系列不言自明的模态。浏览左侧侧边菜单中的每个类别（商店列表、内容评级等），并相应地填写所有信息。

1.  一旦满足所有要求，点击“发布应用程序”按钮。

# 它是如何工作的...

在这个教程中，我们介绍了将 Android 应用程序发布到 Google Play 商店的过程。通过按照*步骤*2 中链接的说明，您的 React Native 应用程序将经历 Gradle `assembleRelease`过程。`assemble`过程运行打包程序以创建 JavaScript 捆绑文件，编译 Java 类，将它们与适当的资源打包在一起，最后允许您将应用程序签名为`.apk`。

# 部署 Over-The-Air 更新

我们的 React Native 应用程序以 JavaScript 编写的一个有用的副作用是，代码是在运行时加载的，这类似于 Cordova 混合应用程序的工作方式。我们可以利用这个功能来使用**OTA**（Over-The-Air）推送更新到我们的应用程序。这允许添加功能和修复错误，而无需经过应用商店的批准流程。对于 React Native 的 OTA 更新的唯一限制是，我们不能推送编译的（Objective-C 或 Java）代码，这意味着更新代码必须仅在 JavaScript 层中。有一些流行的服务提供基于云的 OTA 应用程序更新。我们将重点介绍微软的服务`CodePush`。

这个教程将涵盖在 iOS 和 Android 上为我们的 React Native 应用程序设置和推送更新使用`CodePush`。

# 准备工作

对于这个教程，我们将使用相同的简单的 React Native 应用程序，我们在本章中一直在使用的`TestDeployApp`。我们将把应用程序部署到以生产/发布模式运行的物理设备上，这将允许应用程序从 CodePush 服务器接收更新。

# 如何做...

1.  为了使用 CodePush，我们需要安装 CodePush CLI 并创建一个免费帐户。这可以通过在终端中运行以下两个命令来完成：

```jsx
npm install -g code-push-cli
code-push register
```

1.  下一步是向 CodePush 注册我们的应用程序。记下通过运行`code-push register`输出提供的应用程序的部署密钥。我们将在这个示例中使用**暂存密钥**。文档建议为每个平台添加一个应用程序，每个应用程序都带有`-IOS`或`-Android`后缀。要将应用程序添加到 CodePush，请使用此命令：

```jsx
code-push app add TestDeployApp**-IOS**
**code-push app add TestDeployApp-Android** 
```

1.  我们还需要在 React Native 项目目录中安装 React Native CodePush 模块。可以使用`npm`完成这个操作，如下所示：

```jsx
npm install --save react-native-code-push
```

或者，使用`yarn`：

```jsx
yarn add react-native-code-push
```

1.  下一步是将 CodePush 本机模块与我们的项目进行链接。在提示输入 Android 和 iOS 的部署密钥时，请使用*步骤 2*中讨论的暂存密钥。可以使用以下命令链接本机模块：

```jsx
react-native link react-native-code-push
```

1.  接下来，我们需要设置我们的 React Native 应用程序以使用 CodePush。在`index.js`中，我们需要添加三个东西：CodePush 导入，一个选项对象，以及在通过`AppRegistry.registerComponent`注册应用程序时调用导入的`codePush`模块。设置应用程序如下：

```jsx
import {AppRegistry} from 'react-native';
import App from './App';
import codePush from 'react-native-code-push'; 

const codePushOptions = { 
 updateDialog : true 
} 

AppRegistry.registerComponent('TestDeployApp',
 () => codePush(codePushOptions)(App)
)
```

1.  为了测试我们在 iOS 应用程序中的更改，让我们部署到我们的 iOS 设备。在 Xcode 中打开 React Native 项目，将方案的构建配置（产品|方案|编辑方案...）更改为 Release，然后按照以下步骤运行：

![](img/0b2c207c-df88-4ee7-9e38-3b1022ab7b51.png)

1.  接下来，在应用程序中对 React Native 代码进行某种任意更改，然后在终端中运行以下命令以使用新代码更新应用程序：

```jsx
code-push release-react TestDeployApp ios -m --description "Updating using CodePush"
```

1.  接下来，在您的 iOS 设备上关闭并重新打开应用程序。您应该会看到以下提示：

![](img/c759c174-a9f6-49de-b2b5-de304a017076.png)

1.  继续通过提示后，应用程序将自动更新到最新版本！

1.  让我们还在 Android 上测试一下这个功能。您需要按照 React Native 文档中[`facebook.github.io/react-native/docs/signed-apk-android.html`](https://facebook.github.io/react-native/docs/signed-apk-android.html)中概述的步骤将您的 Android 应用程序制作成`.apk`文件。

1.  将 Android 设备连接到开发计算机后，在`android/`目录中运行以下命令：

```jsx
adb install 
app/build/outputs/apk/app-release.apk
```

1.  接下来，对 React Native JavaScript 代码进行更改。只要添加了新代码，我们就可以使用该更改的代码来更新应用程序。然后，在终端中运行以下命令：

```jsx
code-push release-react TestDeployApp android -m --description "Updating using CodePush"
```

1.  再次在 Android 设备上关闭并重新打开应用程序，以获得以下提示：

![](img/321c9e80-f6db-4c2a-8ad3-7dee57827d17.png)

1.  在继续操作提示之后，应用程序将自行更新到最新版本。

# 它是如何工作的...

CodePush（以及其他云托管的 OTA 更新平台）的工作原理与 React Native 自诞生以来一直存在的技术相同。当应用程序初始化时，React Native 会加载一个 JavaScript 捆绑包。在开发过程中，此捆绑包从`localhost:3000`加载。然而，一旦我们部署了一个应用程序，它将寻找一个名为`main.jsbundle`的文件，该文件已包含在最终产品中。通过在*步骤 5*中的`registerComponent`中添加对`codePush`的调用，应用程序将与 CodePush API 进行检查以查看是否有更新。如果有新的更新，它将提示用户。接受提示会下载新的`jsbundle`文件并重新启动应用程序，从而更新代码。

# 优化 React Native 应用程序大小

在将我们的应用程序部署到生产环境之前，将应用程序捆绑包的大小缩小到尽可能小的文件是一个很好的主意，我们可以利用几种技术来实现这一点。这可能涉及支持更少的设备或压缩包含的资产。

这个配方将涵盖一些限制 iOS 和 Android React Native 应用程序生产包文件大小的技术。

# 准备工作

对于这个配方，我们将使用本章节中一直使用的相同简单的 React Native 应用程序，`TestDeployApp`。您还需要在 iOS 上使用代码签名，并且能够创建`.apk`文件，就像在之前的配方中介绍的那样。

# 如何做...

1.  我们将从对我们捆绑的资产进行一些简单的优化开始，这通常包括图像和外部字体：

+   对于 PNG 和 JPEG 压缩，您可以使用诸如[`www.tinypng.com`](http://www.tinypng.com)之类的服务来减小文件大小，几乎不会降低图像质量。

+   如果您使用`react-native-vector-icons`库，您会注意到它捆绑了八种不同的字体图标集。建议您删除应用程序未使用的任何图标字体库。

+   SVG 文件也可以进行压缩和优化。用于此目的的一个服务是[`compressor.io`](http://compressor.io)。

+   打包到您的应用程序中的任何音频资产应该使用可以利用高质量压缩的文件格式，例如 MP3 或 AAC。

1.  对于 iOS，除了默认启用的发布方案设置外，几乎没有什么可以进一步减小文件大小的方法。这些设置包括为应用瘦身启用 Bitcode 和将编译器优化设置为 Fastest, Smallest [-Os]。

1.  对于 Android，有两件事情可以改善文件大小：

+   在 Android Studio 中，打开`android/app/build.gradle`文件，找到以下行，然后将它们的值更新为以下内容：

```jsx
def enableSeparateBuildPerCPUArchitecture = true 
def enableProguardInReleaseBuilds = true 
```

1.  如果您只打算针对基于 ARM 架构的 Android 设备，我们可以完全阻止其构建 x86 支持。在`build.gradle`文件中，找到`splits abi`对象，并添加以下行以不包括 x86 支持：

```jsx
include "armeabi-v7a" 
```

您可以在 Android 文档中阅读有关 ABI 管理的更多信息：

[`developer.android.com/ndk/guides/abis`](https://developer.android.com/ndk/guides/abis)

# 工作原理...

在这个教程中，我们介绍了可以用来减小应用文件大小的技术。JavaScript 捆绑包越小，JavaScript 解释器就能更快地解析代码，从而实现更快的应用加载时间和更快的 OTA 更新。我们能够保持`.ipa`和`.apk`文件越小，用户就能越快地下载应用。
