# 第十二章：添加本机功能-第二部分

在本章中，我们将涵盖以下食谱：

+   对应用程序状态变化做出反应

+   复制和粘贴内容

+   通过触摸 ID 或指纹传感器进行身份验证

+   在多任务处理时隐藏应用程序内容

+   在 iOS 上进行后台处理

+   在 Android 上进行后台处理

+   在 iOS 上播放音频文件

+   在 Android 上播放音频文件

# 介绍

在本章中，我们将继续介绍更多的食谱，涉及编写与本机 iOS 和 Android 代码交互的 React Native 应用程序的不同方面。我们将涵盖利用内置和社区创建的模块的示例应用程序。这些食谱涵盖了一系列主题，从渲染基本按钮到创建不阻塞主应用程序线程的多线程进程。

# 对应用程序状态变化做出反应

普通移动设备用户通常会经常使用几个应用程序。理想情况下，除了其他社交媒体应用程序、游戏、媒体播放器等，用户还将使用您的 React Native 应用程序。任何特定的用户可能会在每个应用程序中花费很短的时间，因为他们在多任务处理。如果我们想要在用户离开我们的应用程序并重新进入时做出反应怎么办？我们可以利用这个机会与服务器同步数据，或者告诉用户我们很高兴看到他们回来，或者礼貌地要求在应用商店上对应用程序进行评分。

这个食谱将涵盖应用程序状态变化的基础知识，也就是说，对应用程序处于前台（活动）、后台或非活动状态时做出反应。

对于这个食谱，让我们创建一个名为`AppStateApp`的新的纯 React Native 应用程序。

# 如何做...

1.  幸运的是，React Native 提供了对应用程序状态变化的支持，通过`AppState`模块监听。让我们通过向`App.js`文件添加依赖项来开始构建应用程序，如下所示：

```jsx
import React, { Component } from 'react';
import {
  AppState,
  StyleSheet,
  Text,
  View
} from 'react-native';
```

1.  在这个食谱中，我们将跟踪先前的状态，以查看用户来自何处。如果这是他们第一次进入应用程序，我们将欢迎他们，如果他们返回，我们将改为欢迎他们。为此，我们需要保留对先前和当前应用程序状态的引用。我们将使用实例变量`previousAppState`和`currentAppStates`来代替使用状态，只是为了避免潜在的命名混淆。我们将使用`state`来保存向用户的状态消息，如下所示：

```jsx
export default class App extends Component {
  previousAppState = null;
  currentAppState = 'active';
  state = {
    statusMessage: 'Welcome!'
  }
  // Defined on following steps
}
```

1.  当组件挂载时，我们将使用`AppState`组件添加一个`change`事件的监听器。每当应用程序的状态发生变化（例如，当应用程序被置于后台时），将触发`change`事件，然后我们将触发下一步中定义的`handleAppStateChange`处理程序，如下所示：

```jsx
  componentWillMount() {
    AppState.addEventListener('change', this.handleAppStateChange);
  }
```

1.  `handleAppStateChange`方法将接收`appState`作为参数，我们可以期望它是三个字符串中的一个：如果应用程序从内存中卸载，则为`inactive`，如果应用程序在内存中并处于后台，则为`background`，如果应用程序在前台，则为`active`。我们将使用`switch`语句相应地更新`state`上的`statusMessage`：

```jsx
  handleAppStateChange = (appState) => {
    let statusMessage;

    this.previousAppState = this.currentAppState;
    this.currentAppState = appState;
    switch(appState) {
      case 'inactive':
        statusMessage = "Good Bye.";
        break;
      case 'background':
        statusMessage = "App Is Hidden...";
        break;
      case 'active':
        statusMessage = 'Welcome Back!'
        break;
    }
    this.setState({ statusMessage });
  }
```

1.  `render`方法在这个示例中非常基础，因为它只需要向用户显示状态消息，如下所示：

```jsx
 render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          {this.state.statusMessage}
        </Text>
      </View>
    );
  }
```

1.  该应用程序的样式很基础，包括字体大小、颜色和边距，如下所示：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#fff',
  },
  welcome: {
    fontSize: 40,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
});
```

1.  完成的应用程序现在应该根据设备上应用程序的状态显示适当的状态消息。

# 工作原理...

在这个示例中，我们利用了内置的`AppState`模块。该模块监听 Android 上的`Activity`事件，在 iOS 上使用`NSNotificationCenter`在各种`UIApplication`事件上注册监听器。请注意，两个平台都支持`active`和`background`状态；然而，`inactive`状态是 iOS 独有的概念。由于 Android 的多任务处理实现，它不明确支持`inactive`状态，因此只在`background`和`active`状态之间切换应用程序。要在 Android 上实现等效于 iOS 不活动状态的效果，请参见本章后面的*在多任务处理时隐藏应用程序内容*示例。

# 复制和粘贴内容

在桌面和移动操作系统中最常用的功能之一是用于复制和粘贴内容的剪贴板。在移动设备上的常见情况是使用长文本填写表单，例如长电子邮件地址或密码。与其打字并出现几个拼写错误，不如直接打开您的联系人应用程序，从那里复制电子邮件并粘贴到您的`TextInput`字段中会更容易。

这个示例将展示在 Android 和 iOS 上如何在 React Native 应用程序中复制和粘贴文本的基本示例。在我们的示例应用程序中，我们将有一个静态的`Text`视图和一个`TextInput`字段，您可以使用它来将其内容复制到剪贴板。此外，还将有一个按钮，用于将剪贴板的内容输出到视图中。

# 准备工作

对于这个示例，我们将创建一个名为 `CopyPasteApp` 的纯 React Native 应用程序。

在这个示例中，我们将再次使用 `react-native-button`。使用 `npm` 安装它：

```jsx
npm install react-native-button
```

或者，我们可以使用 `yarn`：

```jsx
yarn add react-native-button
```

# 如何做...

1.  让我们首先创建一个 `ClipboardText` 组件，它既使用 `Text` 组件来显示文本，又提供了通过长按将其内容复制到剪贴板的功能。在项目的根目录下创建一个 `component` 文件夹，并在其中创建一个 `ClipboardText.js` 文件。我们将首先导入依赖项，如下所示：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  Clipboard,
  TextInput
} from 'react-native';
import Button from 'react-native-button';
```

1.  接下来，我们将定义 `App` 类和初始的 `state`。我们将使用 `state` 上的 `clipboardContent` 属性来存储从剪贴板粘贴到 UI 中的文本，如下所示：

```jsx
export default class App extends Component {
  state = {
    clipboardContent: null
  }
  // Defined in following steps
}
```

1.  UI 将有一个 `Text` 组件，其文本可以通过长按进行复制。让我们定义 `copyToClipboard` 方法。我们将通过它的 `ref`（稍后我们将定义）获取输入，并通过其 `props.children` 属性访问组件的文本。一旦文本被存储在一个本地变量中，我们只需将其传递给 `Clipboard` 的 `setString` 方法，以将文本复制到剪贴板，如下所示：

```jsx
  copyToClipboard = () => {
    const sourceText = this.refs.sourceText.props.children;
    Clipboard.setString(sourceText);
  }
```

1.  同样，我们还需要一个方法，它将从剪贴板中粘贴文本到应用的 UI 中。这个方法将使用 `Clipboard` 的 `getString` 方法，并将返回的字符串保存到 `state` 的 `clipboardContent` 属性中，重新渲染应用的 UI 以反映粘贴的文本，如下所示：

```jsx
  getClipboardContent = async () => {
    const clipboardContent = await Clipboard.getString();
    this.setState({
      clipboardContent
    });
  }
```

1.  `render` 方法将由两个部分组成：第一部分是要复制的内容，第二部分是从剪贴板粘贴文本到 UI 的方法。让我们从第一部分开始，它包括一个 `Text` 输入，其 `onLongPress` 属性连接到我们在 *步骤 3* 中创建的 `copyToClipboard` 方法，以及一个用于正常本地复制/粘贴的文本输入：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.instructions}>
          Tap and Hold the next line to copy it to the Clipboard:
        </Text>
        <Text
          ref="sourceText"
          onLongPress={this.copyToClipboard}
        >
          React Native Cookbook
        </Text>
        <Text style={styles.instructions}>
          Input some text into the TextInput below and Cut/Copy as
          you normally would:
        </Text>
        <TextInput style={styles.textInput} />

        // Defined on next step
      </View>
    );
  }
```

1.  UI 的第二部分包括一个 `Text` 组件，用于显示保存在 `state` 的 `clipboardContent` 中的当前值，并一个按钮，将使用我们在 *步骤 4* 中定义的 `getClipboardContent` 方法从剪贴板中粘贴：

```jsx
  render() {
    return (
      <View style={styles.container}>
        // Defined in previous step
 <View style={styles.row}>
 <Text style={styles.rowText}>
 Clipboard Contents:
 </Text>
 </View>
 <View style={styles.row}>
 <Text style={styles.content}>
 {this.state.clipboardContent}
 </Text>
 </View>
 <Button
 containerStyle={styles.buttonContainer}
 style={styles.buttonStyle}
 onPress={this.getClipboardContent}
 >
 Paste Clipboard
 </Button>
      </View>
    );
  }
```

最终的应用程序应该类似于以下截图：

![](img/77ae1f65-21d0-463c-8387-4dea842f92ce.png)

# 它是如何工作的...

在这个示例中，我们通过使用 React Native 提供的`Clipboard`API 构建了一个简单的复制粘贴应用程序。`Clipboard`模块目前仅支持`String`类型的内容，尽管设备可以复制更复杂的数据。这个模块使得使用剪贴板就像调用`setString`和`getString`方法一样简单。

# 通过指纹识别或指纹传感器进行认证

安全在软件中是一个重要的问题，特别是在任何形式的认证时。数据泄露和密码泄露已经成为日常新闻的一部分，各种规模的公司都在意识到需要在他们的应用程序中实施额外的安全措施。移动设备中的一种措施是生物识别认证，它使用指纹扫描或面部识别技术提供补充的身份验证方法。

这个示例介绍了如何添加指纹扫描和面部识别安全功能。由于`react-native-touch-id`库的存在，这个过程在 React Native 应用程序开发中变得简化和流畅。

# 准备工作

对于这个示例，我们需要一个新的纯 React Native 应用。让我们称之为`BiometricAuth`。

我们将使用`react-native-button`和`react-native-touch-id`库。使用`npm`安装它们：

```jsx
npm install react-native-button react-native-touch-id --save
```

或者，我们可以使用`yarn`：

```jsx
yarn add react-native-button react-native-touch-id
```

安装完成后，`react-native-touch-id` 需要进行链接，所以请务必跟进：

```jsx
react-native link
```

权限也需要手动调整。对于 Android 权限，请在项目中找到`AndroidManifest.xml`文件，应该在`BiometricAuth/android/app/src/main/AndroidManifest.xml`。除了这个文件中的其他权限，你还需要添加以下内容：

```jsx
<uses-permission android:name="android.permission.USE_FINGERPRINT" />
```

对于 iOS 权限，你需要在文本编辑器中更新`Info.plist`文件。`Info.plist`可以在`BiometricAuth/ios/BiometricAuth/Info.plist`找到。除了所有其他条目，添加以下内容：

```jsx
<key>NSFaceIDUsageDescription</key>
<string>Enabling Face ID allows you quick and secure access to your account.</string>
```

# 如何做...

1.  让我们首先在`App.js`文件中添加依赖项，如下所示：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View
} from 'react-native';
import Button from 'react-native-button';
import TouchID from 'react-native-touch-id';
```

1.  接下来，我们将定义`App`类和初始`state`。我们将在`state`的`authStatus`属性上跟踪认证状态，如下所示：

```jsx
export default class App extends Component {
  state = {
    authStatus: null
  }
  // Defined in following steps
}
```

1.  让我们定义`authenticate`方法，它将在按钮按下时触发，并在设备上启动认证。我们可以通过执行`TouchID`组件的`authenticate`方法来启动认证。这个方法的第一个参数是一个可选的字符串，解释请求的原因，如下所示：

```jsx
  authenticate = () => {
    TouchID.authenticate('Access secret information!')
      .then(this.handleAuthSuccess)
      .catch(this.handleAuthFailure);
  }
```

1.  这个方法在成功时触发`handleAuthSuccess`方法。让我们现在来定义它。这个方法简单地将`state`的`authStatus`属性更新为字符串`Authenticated`，如下所示：

```jsx
  handleAuthSuccess = () => {
    this.setState({
      authStatus : 'Authenticated'
    });
  }
```

1.  同样，如果身份验证失败，将调用`handleAuthFailure`函数，该函数将更新相同的`state.authStatus`为字符串`Not Authenticated`，如下所示：

```jsx
  handleAuthFailure = () => {
    this.setState({
      authStatus : 'Not Authenticated'
    });
  }
```

1.  `render`方法将需要一个按钮来发起身份验证请求，以及两个`Text`组件：一个用于标签，一个用于显示身份验证状态，如下所示：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Button
          containerStyle={styles.buttonContainer}
          style={styles.button}
          onPress={this.authenticate}>
            Authenticate
        </Button>
        <Text style={styles.label}>Authentication Status</Text>
        <Text style={styles.welcome}>{this.state.authStatus}</Text>
      </View>
    );
  }
```

1.  最后，我们将添加样式来设置 UI 的颜色、大小和布局，如下所示：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#fff',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  label: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
  buttonContainer: {
    width: 150,
    padding: 10,
    margin: 5,
    height: 40,
    overflow: 'hidden',
    backgroundColor: '#FF5722'
  },
  button: {
    fontSize: 16,
    color: 'white'
  }
});
```

# 工作原理...

这个教程演示了将原生指纹和面部识别安全性简单地整合到 React Native 应用程序中的方法。调用`TouchID.authenticate`还需要一个可选的选项对象参数，其中包括三个属性：`title`用于确认对话框的标题（仅限 Android），`color`用于对话框的颜色（仅限 Android），以及`fallbackLabel`用于编辑默认的“显示密码”标签（仅限 iOS）。

# 在多任务处理时隐藏应用程序内容

保持应用程序安全主题的进行，有时我们必须警惕不速之客触摸我们的设备，可能获取对我们应用程序的访问权限。为了保护用户在查看敏感信息时免受窥视，我们可以在应用程序隐藏但仍处于活动状态时对应用程序进行遮罩。一旦用户返回到应用程序，我们只需移除遮罩，用户就可以继续正常使用应用程序。这在银行或密码应用程序中隐藏敏感信息时是一个很好的使用案例。

这个教程将向你展示如何渲染一个图像来遮罩你的应用程序，并在应用程序返回到前台或活动状态时将其移除。我们将涵盖 iOS 和 Android；然而，实现方式完全不同。对于 iOS，我们采用纯 Objective-C 实现以获得最佳性能。对于 Android，我们需要对`MainActivity`进行一些修改，以便向 JavaScript 层发送应用程序失去焦点的事件。我们将在那里处理图像遮罩的渲染。

# 准备工作

当应用程序不在前台时，我们需要一个图像来用作遮罩。我选择使用了一张 iPhone 壁纸，你可以在这里找到：

[`www.hdiphone7wallpapers.com/2016/09/white-squares-iphone-7-and-7-plus-wallpapers.html`](http://www.hdiphone7wallpapers.com/2016/09/white-squares-iphone-7-and-7-plus-wallpapers.html)

该图像是一种风格化的马赛克图案。它看起来像这样：

![](img/a329bd35-49fe-44b3-bcc3-a6e257a9ed5a.jpg)

当然，您可以使用任何您喜欢的图像。在这个示例中，图像文件将被命名为`hidden.jpg`，因此请相应地重命名您的图像。

我们需要一个新的纯 React Native 应用程序。让我们称之为`HiddenContentApp`。

# 如何做...

1.  让我们首先将面具图像添加到应用程序的 iOS 部分。我们需要在新的 React Native 应用程序的`ios/`目录中的 Xcode 中打开项目的`ios`文件夹。

1.  我们可以通过将图像拖放到 Xcode 项目的`Images.xcassets`文件夹中来将`hidden.jpg`图像添加到项目中，如下图所示：

![](img/7a2e117f-9a1d-4c3f-952c-282f501c07f5.png)

1.  接下来，我们将向`AppDelegate.m`文件添加一个新的实现和两种方法。可以在下面找到整个文件，包括生成的代码。为了清晰起见，我们添加的代码已用粗体标记。我们正在扩展`applicationWillResignActive`方法，每当给定应用程序从前台变为后台时，它都会触发，以添加一个带有`hidden.jpg`作为其图像的`imageView`。同样，我们还需要扩展相反的方法`applicationDidBecomeActive`，以在应用程序重新进入前台时删除图像：

```jsx
#import "AppDelegate.h"

#import <React/RCTBundleURLProvider.h>
#import <React/RCTRootView.h>

@implementation AppDelegate {
 UIImageView *imageView;
}

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  NSURL *jsCodeLocation;

  jsCodeLocation = [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index" fallbackResource:nil];

  RCTRootView *rootView = [[RCTRootView alloc] initWithBundleURL:jsCodeLocation
                                                      moduleName:@"HiddenContentApp"
                                               initialProperties:nil
                                                   launchOptions:launchOptions];
  rootView.backgroundColor = [[UIColor alloc] initWithRed:1.0f green:1.0f blue:1.0f alpha:1];

  self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
  UIViewController *rootViewController = [UIViewController new];
  rootViewController.view = rootView;
  self.window.rootViewController = rootViewController;
  [self.window makeKeyAndVisible];
  return YES;
}

- (void)applicationWillResignActive:(UIApplication *)application {
 imageView = [[UIImageView alloc] initWithFrame:[self.window frame]];
 [imageView setImage:[UIImage imageNamed:@"hidden.jpg"]];
 [self.window addSubview:imageView];
}

- (void)applicationDidBecomeActive:(UIApplication *)application {
 if(imageView != nil) {
 [imageView removeFromSuperview];
 imageView = nil;
 }
}

@end
```

1.  通过前面的三个步骤，iOS 应用程序中显示面具所需的所有工作已经完成。让我们通过在 Android Studio 中打开项目的 Android 部分来继续进行。在 Android Studio 中，选择打开现有的 Android Studio 项目，并打开项目的`android`目录。

1.  我们需要更新 Android 项目中的唯一本地代码位于`MainActivity.java`中，位于此处：

![](img/752fe834-c63d-4a90-8d38-b145c84a551a.png)

我们需要添加一个方法，以及方法使用的来自 React 的三个导入。下面是完整的`MainActivity.java`文件，加粗标记的是添加的代码。我们正在定义一个扩展基本方法功能的`onWindowFocusChanged`方法。基本的`onWindowFocusChanged` Android 方法在给定应用程序的焦点发生变化时触发，传递一个表示应用程序是否具有焦点的`hasFocus`布尔值。我们的扩展将通过我们命名为`focusChange`的事件有效地将该`hasFocus`布尔值从父方法传递到 React Native 层，如下所示：

```jsx
package com.hiddencontentapp;

import com.facebook.react.ReactActivity;
import com.facebook.react.bridge.Arguments;
import com.facebook.react.bridge.WritableMap;
import com.facebook.react.modules.core.DeviceEventManagerModule;

public class MainActivity extends ReactActivity {

  /**
  * Returns the name of the main component registered from JavaScript.
  * This is used to schedule rendering of the component.
  */
  @Override
  protected String getMainComponentName() {
    return "HiddenContentApp";
  }

 @Override
 public void onWindowFocusChanged(boolean hasFocus) {
 super.onWindowFocusChanged(hasFocus);
 if (getReactNativeHost().getReactInstanceManager().getCurrentReactContext() != null) {
 WritableMap params = Arguments.createMap();
 params.putBoolean("appHasFocus", hasFocus);

 getReactNativeHost().getReactInstanceManager()
 .getCurrentReactContext()
 .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
 .emit("focusChange", params);
 }
 }
}
```

1.  要在 Android 中使用`hidden.jpg`遮罩图像，我们还需要将其添加到 React Native 项目中。让我们在 React Native 项目的根目录中创建一个新的`assets`文件夹，并将`hidden.jpg`图像文件添加到新文件夹中。

1.  有了原生部分的基础，我们现在准备转向应用程序的 JavaScript 部分。让我们在`App.js`中添加我们将使用的导入，如下所示：

```jsx
import React, {Component} from 'react';
import {
  StyleSheet,
  Text,
  View,
  DeviceEventEmitter,
  Image
} from 'react-native';
```

1.  接下来，让我们创建`App`类和初始`state`。`state`只需要一个`showMask`布尔值，它将决定是否显示遮罩，如下所示：

```jsx
export default class App extends Component {
  state = {
    showMask: null
  }
  // Defined in following steps
}
```

1.  当组件挂载时，我们希望注册一个事件监听器，以便使用`DeviceEventEmitter`的`addListener`方法监听从原生 Android 层发出的事件，将字符串`focusChange`作为要监听的事件的名称作为第一个参数，并将要执行的回调作为第二个参数。您可能还记得，`focusChange`是我们在`MainActivity.java`中的`onWindowFocusChange`方法中分配的事件名称，在*步骤 5*中注册事件监听器如下：

```jsx
  componentWillMount() {
    this.subscription = DeviceEventEmitter.addListener(
      'focusChange',
      this.onFocusChange
    );
  }
```

1.  在这一步中，我们将把事件监听器保存到类成员`this.subscription`中。这将允许在组件卸载时清理事件监听器。我们只需在组件卸载时通过`componentWillUnmount`生命周期钩子调用`this.subscription`上的`remove`方法，如下所示：

```jsx
  componentWillUnmount() {
    this.subscription.remove();
  }
```

1.  让我们定义在*步骤 9*中使用的`onFocusChange`处理程序。该方法接收一个`params`对象，其中包含通过*步骤 5*中定义的`onWindowFocusChanged`方法从原生层传递的`appHasFocus`布尔值。通过将`state`上的`showMask`布尔值设置为`appHasFocus`布尔值的相反值，我们可以在`render`函数中使用它来切换显示`hidden.jpg`图像，如下所示：

```jsx
  onFocusChange = (params) => {
    this.setState({showMask: !params.appHasFocus})
  }
```

1.  `render`方法的主要内容在这个示例中并不重要，但我们可以使用它来在`state`的`showMask`属性为`true`时应用`hidden.jpg`蒙版图像，如下所示：

```jsx
  render() {
    if(this.state.showMask) {
      return (<Image source={require('./assets/hidden.jpg')} />);
    }
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>Welcome to React Native!</Text>
      </View>
    );
  }
```

1.  应用程序已经完成。一旦应用程序加载完成，您应该能够转到应用程序选择视图（在 iOS 上双击 home，或在 Android 上按方形按钮），并在应用程序不在前台时看到应用的蒙版图像。请注意，Android 模拟器可能无法按预期正确应用蒙版，因此这个功能可能需要使用 Android 设备进行测试。

![](img/c97ae150-6166-44ee-a686-c3758bc11c7f.png)

# 工作原理...

在这个示例中，我们看到了需要使用两种不同的方法来完成相同的任务。对于 iOS，我们在本地层中独占地处理显示图像蒙版，而不需要 React Native 层。对于 Android，我们使用 React Native 来处理图像蒙版。

在*步骤 3*中，我们扩展了两个 Objective-C 方法：`applicationWillResignActive`，当应用程序从前台切换时触发，以及`applicationDidBecomeActive`，当应用程序进入前台时触发。对于每个事件，我们简单地切换显示在 Xcode 项目的`Images.xcassettes`文件夹中存储的`hidden.jpg`图像的`imageView`。

在*步骤 5*中，我们使用了 React 类`RCTDeviceEventEmitter`从`DeviceEventManagerModule`来发出一个名为`focusChange`的事件，传递一个带有`appHasFocus`布尔值的`params`对象到 React Native 层，如下所示：

```jsx
     getReactNativeHost().getReactInstanceManager()
       .getCurrentReactContext()
       .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
       .emit("focusChange", params);
     }
```

在*步骤 9*中，我们定义了`componentWillMount`生命周期钩子，为从本地 Android 层发出的`focusChange`事件设置了一个事件侦听器，触发`onFocusChange`方法，该方法将根据本地`appHasFocus`值更新`state`的`showMask`值，触发重新渲染，适当地显示蒙版。

# iOS 上的后台处理

在过去的几年里，移动设备的处理能力大大增加。用户要求更丰富的体验，实现在现代移动设备上改进性能的一种方法是通过多线程。大多数移动设备今天都由多核处理器驱动，它们的操作系统现在为开发人员提供了在后台执行代码的简单抽象，而不会干扰应用程序 UI 的性能。

这个示例将涵盖 iOS 的**Grand Central Dispatch**（**GCD**）的使用，以在新线程上执行异步后台处理，并在处理完成时与 React Native 层进行通信。

# 准备工作

对于这个示例，我们需要一个新的纯 React Native 应用程序。让我们将其命名为`MultiThreadingApp`。

我们还将使用`react-native-button`库。使用`npm`安装它：

```jsx
npm install react-native-button --save
```

或者，我们可以使用`yarn`：

```jsx
yarn add react-native-button --save
```

# 如何做...

1.  我们将首先在新的 React Native 应用程序的`ios`目录中打开 Xcode 中的 iOS 项目。

1.  让我们添加一个名为`BackgroundTaskManager`的新的 Cocoa 类文件，其子类为`NSObject`。有关在 Xcode 中执行此操作的更多详细信息，请参考本章中的*公开自定义 iOS 模块*示例。

1.  接下来，让我们将新模块连接到 React 的`RCTBrideModule`，在新模块的头文件`BackgroundTaskManager.h`中。要添加的代码在以下片段中用粗体标记出来：

```jsx
#import <Foundation/Foundation.h>
#import <dispatch/dispatch.h>
#import "RCTBridgeModule.h"

@interface BackgroundTaskManager : NSObject <RCTBridgeModule> {
 dispatch_queue_t backgroundQueue;
}

@end
```

1.  我们将在`BackgroundTaskManager.m`文件中实现本机模块。同样，我们要添加的新代码在以下片段中用粗体标记出来：

```jsx
#import "BackgroundTaskManager.h"
#import "RCTBridge.h"
#import "RCTEventDispatcher.h"

@implementation BackgroundTaskManager

@synthesize bridge = _bridge;

RCT_EXPORT_MODULE();

RCT_EXPORT_METHOD(loadInBackground) {
 backgroundQueue = dispatch_queue_create("com.moduscreate.bgqueue", NULL);

 dispatch_async(backgroundQueue, ^{
 NSLog(@"processing background");
 [self.bridge.eventDispatcher sendAppEventWithName:@"backgroundProgress" body:@{@"status": @"Loading"}];
 [NSThread sleepForTimeInterval:5];
 NSLog(@"slept");
 dispatch_async(dispatch_get_main_queue(), ^{
 NSLog(@"Done processing; main thread");
 [self.bridge.eventDispatcher sendAppEventWithName:@"backgroundProgress" body:@{@"status": @"Done"}];
 });
 });
}

@end
```

1.  接下来让我们转向 JavaScript 层。我们将首先在`App.js`文件中添加依赖项。作为依赖项的一部分，我们还需要导入在*步骤 3*和*步骤 4*中定义的`BackgroundTaskManager`本机模块，如下所示：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  NativeModules,
  NativeAppEventEmitter
} from 'react-native';
import Button from 'react-native-button'; 

const BackgroundTaskManager = NativeModules.BackgroundTaskManager;
```

1.  让我们定义`App`类，初始状态为`backgroundTaskStatus`设置为字符串`Not Started`，并且`doNothingCount`属性初始化为`0`，如下所示：

```jsx
 export default class App extends Component {
  state = {
    backgroundTaskStatus: 'Not Started',
    counter: 0
  }
  // Defined in following steps
}
```

1.  我们需要监听从我们在*步骤 3*和*步骤 4*中创建的自定义模块的本机 iOS 层发出的`backgroundProcess`事件。让我们使用`NativeAppEventEmitter` React Native 组件设置事件监听器，将`state`的`backgroundTaskStatus`属性设置为从本机事件接收到的事件对象上的`status`值，如下所示：

```jsx
  componentWillMount = () => {
    this.subscription = NativeAppEventEmitter.addListener(
      'backgroundProgress',
      event => this.setState({ backgroundTaskStatus: event.status })
    );
  }
```

1.  当组件卸载时，我们需要从上一步中删除事件监听器，如下所示：

```jsx
  componentWillUnmount = () => {
    this.subscription.remove();
  }
```

1.  UI 将有两个按钮，每个按钮在按下时都需要调用一个方法。`runBackgroundTask`将运行我们在本机 iOS 层上定义并导出的`loadInBackground`方法，该方法位于`BackgroundTaskManager`自定义本机模块上。`increaseCounter`按钮将简单地通过`1`增加`state`上的`counter`属性，以显示主线程未被阻塞的情况，如下所示：

```jsx
  runBackgroundTask = () => {
    BackgroundTaskManager.loadInBackground();
  }

  increaseCounter = () => {
    this.setState({
      counter: this.state.counter + 1
    });
  }
```

1.  应用的 UI 将包括两个按钮来显示`Button`组件，以及一个`Text`组件来显示在`state`上保存的值。“Run Task”按钮将执行`runBackgroundTask`方法来启动后台进程，并且`this.state.backgroundTaskStatus`将更新以显示进程的新状态。在后台进程运行的五秒钟内，按下“Increase Counter”按钮仍然会增加计数器 1，证明后台进程是非阻塞的，如下面的代码片段所示：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Button
          containerStyle={styles.buttonContainer}
          style={styles.buttonStyle}
          onPress={this.runBackgroundTask}>
            Run Task
        </Button>
        <Text style={styles.instructions}>
          Background Task Status:
        </Text>
        <Text style={styles.welcome}>
          {this.state.backgroundTaskStatus}
        </Text>
        <Text style={styles.instructions}>
          Pressing "Increase Conter" button shows that the task is
          not blocking the main thread
        </Text>
        <Button
          containerStyle={[
            styles.buttonContainer,
            styles.altButtonContainer
          ]}
          style={styles.buttonStyle}
          onPress={this.increaseCounter}
        >
            Increase Counter
        </Button>
        <Text style={styles.instructions}>
          Current Count:
        </Text>
        <Text style={styles.welcome}>
          {this.state.counter}
        </Text>
      </View>
    );
  }
```

1.  作为最后一步，让我们使用样式块来布局和设计应用，如下所示：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
    marginLeft: 20,
    marginRight: 20
  },
  buttonContainer: {
    width: 150,
    padding: 10,
    margin: 5,
    height: 40,
    overflow: 'hidden',
    borderRadius: 4,
    backgroundColor: '#FF5722'
  },
  altButtonContainer : {
    backgroundColor : '#CDDC39',
    marginTop : 30
  },
  buttonStyle: {
    fontSize: 16,
    color: 'white'
  }
});
```

# 工作原理...

在这个示例中，我们创建了一个类似于本章前面*暴露自定义 iOS 模块*示例中涵盖的模块的本地模块。我们定义了本地模块来在 React Native 应用的后台执行任意操作。在这个示例中，后台进程由以下三个步骤组成：

1.  创建一个新的线程。

1.  在新线程上睡眠五秒钟。

1.  在五秒的睡眠后（模拟运行后台进程的结束），从 iOS 层向 React Native 层分发一个事件，让它知道进程已经完成。这是通过操作系统的 GCD API 实现的。

这个应用的 UI 的目的是展示多线程已经实现。如果后台进程在 React Native 层执行，由于 JavaScript 的单线程特性，应用在后台进程运行时会被锁定五秒钟。当您按下按钮时，桥被调用，然后消息可以被发布到本地层。如果本地线程当前正在忙于睡眠，那么我们无法处理这条消息。通过将处理转移到新线程，两者可以同时执行。

# 在 Android 上进行后台处理

在这个示例中，我们将构建一个 Android 版本的前一个示例的等价物。这个示例还将使用原生的 Android 层来创建一个新的进程，通过睡眠五秒钟来保持该进程运行，并允许用户通过按钮进行交互，以展示应用的主处理线程没有被阻塞。

虽然最终结果将是非常相似的，但在 Android 项目中生成一个新进程与 iOS 处理方式有些不同。这个示例将利用本地的`AsyncTask`函数，专门用于处理短期后台进程，以允许在 React Native 层执行而不阻塞主线程。

# 准备工作

对于这个示例，我们需要创建一个新的纯 React Native 应用。让我们命名它为`MultiThreadingApp`。

我们还将使用`react-native-button`库。使用`npm`安装它：

```jsx
npm install react-native-button --save
```

另外，我们可以使用`yarn`：

```jsx
yarn add react-native-button
```

# 如何做到…

1.  首先在 Android Studio 中打开 Android 项目。在 Android Studio 中，选择打开现有的 Android Studio 项目，并打开新项目的`android`目录。

1.  我们需要两个新的 Java 类：`BackgroundTaskManager`和`BackgroundTaskPackage`。

1.  现在这两个类都已创建，让我们打开`BackgroundTaskManager.java`并开始实现将包装`AsyncTask`操作的本地模块，从导入和定义类开始。此外，像任何其他本地 Android 模块一样，我们需要定义`getName`方法，用于为模块提供一个名称给 React Native，如下所示：

```jsx
package com.multithreadingapp;   import android.os.AsyncTask;   import com.facebook.react.bridge.Arguments; import com.facebook.react.bridge.ReactApplicationContext; import com.facebook.react.bridge.ReactContextBaseJavaModule; import com.facebook.react.bridge.ReactMethod; import com.facebook.react.bridge.WritableMap; import com.facebook.react.modules.core.DeviceEventManagerModule;

public class BackgroundTaskManager extends ReactContextBaseJavaModule {
  public BackgroundTaskManager(ReactApplicationContext reactApplicationContext) {
    super(reactApplicationContext);
  }

  @Override
  public String getName() {
    return "BackgroundTaskManager";
  }

  // Defined in following steps
}
```

1.  为了执行`AsyncTask`，它需要由一个私有类进行子类化。我们需要为此添加一个新的私有内部`BackgroundLoadTask`子类。在我们定义它之前，让我们首先添加一个`loadInBackground`方法，最终将被导出到 React Native 层。这个方法简单地创建一个`BackgroundLoadTask`的新实例并调用它的`execute`方法，如下所示：

```jsx
public class BackgroundTaskManager extends ReactContextBaseJavaModule {
 // Defined in previous step
  @ReactMethod
 public void loadInBackground() {
 BackgroundLoadTask backgroundLoadTask = new BackgroundLoadTask();
 backgroundLoadTask.execute();
 }
}
```

1.  `BackgroundLoadTask`子类还将使用一个辅助函数来来回发送事件，以跨越 React Native 桥通信后台进程的状态。`sendEvent`方法接受`eventName`和`params`作为参数，然后使用 React Native 的`RCTDeviceEventEmitter`类来`emit`事件，如下所示：

```jsx
public class BackgroundTaskManager extends ReactContextBaseJavaModule {
  // Defined in steps above

 private void sendEvent(String eventName, WritableMap params) {
 getReactApplicationContext().getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class).emit(eventName, params);
 }
}
```

1.  现在让我们继续定义`BackgroundLoadTask`子类，它继承自`AsyncTask`。子类将由三个方法组成：`doInBackground`用于启动一个新线程并让其休眠五分钟，`onProgressUpdate`用于向 React Native 层发送`"Loading"`状态，以及`onPostExecute`用于在后台任务完成时发送`"Done"`状态，如下所示：

```jsx
public class BackgroundTaskManager extends ReactContextBaseJavaModule {
  // Defined in above steps

 private class BackgroundLoadTask extends AsyncTask<String, String, String> {
 @Override
 protected String doInBackground(String... params) {
 publishProgress("Loading");
 try {
 Thread.sleep(5000);
 } catch (Exception e) {
 e.printStackTrace();
 }
 return "Done";
 }

 @Override
 protected void onProgressUpdate(String... values) {
 WritableMap params = Arguments.createMap();
 params.putString("status", "Loading");
 sendEvent("backgroundProgress", params);
 }

 @Override
 protected void onPostExecute(String s) {
 WritableMap params = Arguments.createMap();
 params.putString("status", "Done");
 sendEvent("backgroundProgress", params);
 }
 }
}
```

1.  由于 iOS 实现和 Android 实现之间的唯一区别存在于配方的本机层中，因此您可以按照上一个配方中的*步骤 5*至*步骤 11*来实现应用程序的 JavaScript 部分。

1.  最终的应用程序应该在行为和外观上（除了设备上的差异）与上一个配方中的应用程序相同：

！[](assets/cbc6bfc5-4e0f-4d49-ae41-27748267819a.png)

# 它是如何工作的...

在这个配方中，我们模仿了我们在 Android 上创建的*在 iOS 上进行后台处理*配方中创建的功能。我们创建了一个 Android 本机模块，其中一个方法在调用时在后台执行任意操作（休眠五秒）。当进程完成时，它会向 React Native 层发出事件，然后我们更新应用程序 UI 以反映后台进程的状态。Android 有多个选项可以在本机执行多线程操作。在这个配方中，我们使用了`AsyncTask`，因为它适用于短期运行（几秒钟）的进程，相对简单实现，并且操作系统为我们管理线程创建和资源分配。您可以在官方文档中阅读更多关于`AsyncTask`的信息：

[`developer.android.com/reference/android/os/AsyncTask`](https://developer.android.com/reference/android/os/AsyncTask)

# 在 iOS 上播放音频文件

在*实现复杂用户界面-第三部分*章节中，我们使用 Expo SDK 提供的`Audio`组件在*创建音频播放器*配方中构建了一个相对复杂的小型音频播放器。然而，Expo 的`Audio`组件的一个缺点是它无法在应用程序被置于后台时播放音频。目前使用本机层是实现这一点的唯一方法。

在这个配方中，我们将创建一个本机模块来显示 iOS MediaPicker，然后选择要播放的音乐文件。所选文件将通过本机 iOS 媒体播放器播放，允许在应用程序被置于后台时播放音频，并允许用户通过本机 iOS 控制中心控制音频。

# 准备工作

对于这个配方，我们需要创建一个新的纯 React Native 应用。让我们称之为`AudioPlayerApp`。

我们还将使用`react-native-button`库，可以使用`npm`安装：

```jsx
npm install react-native-button --save
```

或者，我们可以使用`yarn`：

```jsx
yarn add react-native-button
```

这是一个只能在真实设备上预期工作的示例。您还需要确保您的 iOS 设备上同步了音乐并且在媒体库中可用。

# 如何做...

1.  让我们首先在新的 React Native 应用程序的`ios`目录中打开 Xcode 中的 iOS 项目。

1.  接下来，我们将创建一个名为`MediaManager`的新的 Objective-C Cocoa 类。

1.  在`MediaManager`头文件（`.h`）中，我们需要导入`MPMediaPickerController`和`MPMusicPlayerController`，以及 React Native 桥（`RCTBridgeModule`），如下所示：

```jsx
#import <Foundation/Foundation.h>
#import <MediaPlayer/MediaPlayer.h>

#import <React/RCTBridgeModule.h>
#import <React/RCTEventDispatcher.h>

@interface MediaManager : NSObject<RCTBridgeModule, MPMediaPickerControllerDelegate>

@property (nonatomic, retain) MPMediaPickerController *mediaPicker;
@property (nonatomic, retain) MPMusicPlayerController *musicPlayer;

@end
```

1.  首先，我们需要开始添加原生`MediaPicker`到`MediaManager`的实现（`MediaManager.m`）中。首先的方法将是用于显示和隐藏`MediaPicker`的：`showMediaPicker`和`hideMediaPicker`，如下所示：

```jsx
#import "MediaManager.h"
#import "AppDelegate.h"

@implementation MediaManager
RCT_EXPORT_MODULE();

@synthesize bridge = _bridge;
@synthesize musicPlayer;

#pragma mark private-methods

-(void)showMediaPicker {
 if(self.mediaPicker == nil) {
 self.mediaPicker = [[MPMediaPickerController alloc] initWithMediaTypes:MPMediaTypeAnyAudio];

 [self.mediaPicker setDelegate:self];
 [self.mediaPicker setAllowsPickingMultipleItems:NO];
 [self.mediaPicker setShowsCloudItems:NO];
 self.mediaPicker.prompt = @"Select song";
 }

 AppDelegate *delegate = (AppDelegate *)[[UIApplication sharedApplication] delegate];

 [delegate.window.rootViewController presentViewController:self.mediaPicker animated:YES completion:nil];
}

void hideMediaPicker() {
 AppDelegate *delegate = (AppDelegate *)[[UIApplication sharedApplication] delegate];
 [delegate.window.rootViewController dismissViewControllerAnimated:YES completion:nil];
}

// Defined on following steps

@end
```

1.  接下来，我们将实现`mediaPicker`需要的两个操作：`didPickMediaItems`用于选择媒体项目，以及`mediaPickerDidCancel`用于取消操作，如下所示：

```jsx
-(void) mediaPicker:(MPMediaPickerController *)mediaPicker didPickMediaItems:(MPMediaItemCollection *)mediaItemCollection {
  MPMediaItem *mediaItem = mediaItemCollection.items[0];
  NSURL *assetURL = [mediaItem valueForProperty:MPMediaItemPropertyAssetURL];

  [self.bridge.eventDispatcher sendAppEventWithName:@"SongPlaying"
                                               body:[mediaItem valueForProperty:MPMediaItemPropertyTitle]];

  if(musicPlayer == nil) {
    musicPlayer = [MPMusicPlayerController systemMusicPlayer];
  }

  [musicPlayer setQueueWithItemCollection:mediaItemCollection];
  [musicPlayer play];

  hideMediaPicker();
}

-(void) mediaPickerDidCancel:(MPMediaPickerController *)mediaPicker {
  hideMediaPicker();
}
```

1.  接下来，我们需要将我们的`MediaManager`暴露给 React Native 桥，并创建一个将被调用以显示`MediaPicker`的方法，如下所示：

```jsx
RCT_EXPORT_MODULE(); 
RCT_EXPORT_METHOD(showSongs) { 
  [self showMediaPicker]; 
} 
```

1.  我们准备继续进行 JavaScript 部分。让我们首先在`App.js`中添加依赖项。我们还需要使用`NativeModules`组件导入我们在*步骤 3*到*步骤 6*中创建的`MediaManager`原生模块，如下所示：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  NativeModules,
  NativeAppEventEmitter
} from 'react-native';
import Button from 'react-native-button';
const MediaManager = NativeModules.MediaManager;
```

1.  让我们定义`App`类和初始`state`。`currentSong`属性将保存当前播放歌曲的曲目信息，如从原生层传递的那样：

```jsx
export default class App extends Component {
  state = {
    currentSong: null
  }

  // Defined on following steps
}
```

1.  当组件挂载时，我们将订阅从原生层发出的`SongPlaying`事件，当歌曲开始播放时。我们将事件监听器保存到本地的`subscription`类变量中，以便在组件卸载时使用`remove`方法清除它，如下所示：

```jsx
  componentWillMount() {
    this.subscription = NativeAppEventEmitter.addListener(
      'SongPlaying',
      this.updateCurrentlyPlaying
    );
  }

  componentWillUnmount = () => {
    this.subscription.remove();
  }
```

1.  我们还需要一种方法来更新`state`上的`currentSong`值，并且需要一种方法来调用我们在*步骤 3*到*步骤 6*中定义的原生`MediaManager`模块上的`showSongs`方法，如下所示：

```jsx
  updateCurrentlyPlaying = (currentSong) => {
    this.setState({ currentSong });
  }

  showSongs() {
    MediaManager.showSongs();
  }
```

1.  `render`方法将由一个`Button`组件组成，用于在按下时执行`showSongs`方法，以及用于显示当前播放歌曲信息的`Text`组件，如下所示：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Button
          containerStyle={styles.buttonContainer}
          style={styles.buttonStyle}
          onPress={this.showSongs}>
            Pick Song
        </Button>
        <Text style={styles.instructions}>Song Playing:</Text>
        <Text style={styles.welcome}>{this.state.currentSong}</Text>
      </View>
    );
  }
```

1.  最后，我们将添加我们的样式来布局和设计应用程序，如下所示：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
  buttonContainer: {
    width: 150,
    padding: 10,
    margin: 5,
    height: 40,
    overflow: 'hidden',
    borderRadius: 4,
    backgroundColor: '#3B5998'
  },
  buttonStyle: {
    fontSize: 16,
    color: '#fff'
  }
});
```

# 它是如何工作的...

在这个教程中，我们介绍了如何在 iOS 中使用`Media Player`，通过将其功能封装在一个本地模块中。媒体播放器框架允许我们访问本机 iPod 库，并使用与本机 iOS 音乐应用相同的功能在设备上播放库中的音频文件。

# 在 Android 上播放音频文件

谷歌喜欢宣称 Android 相对于 iOS 具有处理文件存储的灵活性。Android 设备支持外部 SD 卡，可以存储媒体文件，并不需要像 iOS 那样需要专有的方法来添加多媒体文件。

在这个教程中，我们将使用 Android 的本机`MediaPicker`，它是从一个意图开始的。然后我们将能够选择一首歌并通过我们的应用程序播放它。

# 准备工作

对于这个教程，我们将创建一个名为`AudioPlayer`的 React Native 应用程序。

在这个教程中，我们将使用`react-native-button`库。要安装它，请在项目根目录的终端中运行以下命令：

```jsx
 $ npm install react-native-button --save
```

确保您的 Android 设备或模拟器的`Music/`目录中有音乐文件可用。

# 如何做...

1.  让我们首先使用 Android Studio 打开 Android 项目。在 Android Studio 中，选择“打开现有的 Android Studio 项目”，然后打开项目的`android`目录。

1.  对于这个教程，我们将需要两个新的 Java 类：`MediaManager`和`MediaPackage`。

1.  我们的`MediaManager`将使用意图来显示`mediaPicker`，`MediaPlayer`来播放音乐，以及`MediaMetadataRetriever`来解析音频文件的元数据信息并发送回 JavaScript 层。让我们首先在`MediaManager.java`文件中导入我们需要的所有依赖项，如下所示：

```jsx
import android.app.Activity;
import android.content.Intent;
import android.media.AudioManager;
import android.media.MediaMetadataRetriever;
import android.media.MediaPlayer;
import android.net.Uri;
import android.provider.MediaStore;

import com.facebook.react.bridge.ActivityEventListener;
import com.facebook.react.bridge.Arguments;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactMethod;
import com.facebook.react.bridge.WritableMap;
import com.facebook.react.modules.core.DeviceEventManagerModule;
```

1.  `showSongs`，`getName`，`playSong`，`mediaPlayer`，`onActivityResult`，`mediaMetadataRetreiver`和`SongPlaying`应该以代码格式显示。替换为：

```jsx
public class MediaManager extends ReactContextBaseJavaModule implements ActivityEventListener {
  private MediaPlayer mediaPlayer = null;
  private MediaMetadataRetriever mediaMetadataRetriever = null;

  public MediaManager(ReactApplicationContext reactApplicationContext) {
    super(reactApplicationContext);
    reactApplicationContext.addActivityEventListener(this);
  }

  @Override
  public String getName() {
    return "MediaManager";
  }

  @Override
  public void onCatalystInstanceDestroy() {
    super.onCatalystInstanceDestroy();
    mediaPlayer.stop();
    mediaPlayer.release();
    mediaPlayer = null;
  }

  @ReactMethod
  public void showSongs() {
    Activity activity = getCurrentActivity();
    Intent intent = new Intent(Intent.ACTION_PICK, MediaStore.Audio.Media.EXTERNAL_CONTENT_URI);
    activity.startActivityForResult(intent, 10);
  }

  @Override
  public void onActivityResult(Activity activity, int requestCode, int resultCode, Intent data) {
    if (data != null) {
      playSong(data.getData());
    }
  }

  @Override
  public void onNewIntent(Intent intent) {
  }

  private void playSong(Uri uri) {
    try {
      if (mediaPlayer != null) {
        mediaPlayer.stop();
        mediaPlayer.reset();
      } else {
        mediaMetadataRetriever = new MediaMetadataRetriever();
        mediaPlayer = new MediaPlayer();
        mediaPlayer.setAudioStreamType(AudioManager.STREAM_MUSIC);
      }

      mediaPlayer.setDataSource(getReactApplicationContext(), uri);

      mediaPlayer.prepare();
      mediaPlayer.start();

      mediaMetadataRetriever.setDataSource(getReactApplicationContext(), uri);
      String artist = mediaMetadataRetriever.extractMetadata(MediaMetadataRetriever.METADATA_KEY_ARTIST);
      String songTitle = mediaMetadataRetriever.extractMetadata(MediaMetadataRetriever.METADATA_KEY_TITLE);

      WritableMap params = Arguments.createMap();
      params.putString("songPlaying", artist + " - " + songTitle);

      getReactApplicationContext()
        .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
        .emit("SongPlaying", params);
    } catch (Exception ex) {
      ex.printStackTrace();
    }
  }
}
```

1.  自定义模块还需要添加到`MainApplication.java`文件中的`getPackages`数组中，如下所示：

```jsx
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
 new MediaPackage()
      );
    }
```

1.  正如本章前面的*暴露自定义 Android 模块*教程中所介绍的，我们必须为我们的`MediaManager`自定义模块添加必要的样板，以便将其导出到 React Native 层。有关更详细的解释，请参考该教程。按照以下步骤添加必要的样板：

```jsx
import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class MediaPackage implements ReactPackage {
  @Override
  public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    return Collections.emptyList();
  }

  @Override
  public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
    List<NativeModule> modules = new ArrayList<>();

    modules.add(new MediaManager(reactContext));

    return modules;
  }
}
```

1.  Android 应用程序的 JavaScript 层与之前的 iOS 教程中的相同。使用本教程的*步骤 7*到*步骤 12*来完成应用程序的最后部分。
