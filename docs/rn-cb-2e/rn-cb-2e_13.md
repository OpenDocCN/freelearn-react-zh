# 第十三章：与原生应用集成

在这一章中，我们将涵盖以下的配方：

+   将 React Native 应用和原生 iOS 应用结合

+   从 iOS 应用到 React Native 的通信

+   从 React Native 到 iOS 应用容器的通信

+   处理外部 iOS 应用的调用

+   将 React Native 应用和原生 Android 应用结合

+   从 Android 应用到 React Native 的通信

+   从 React Native 到 Android 应用容器的通信

+   处理外部 Android 应用的调用

# 介绍

React Native 被引入作为使用 JavaScript 构建原生应用的解决方案，目标是让更多的开发人员能够为多个平台构建真正的原生应用。作为一个团队构建 React Native 应用的结果，JavaScript 开发人员和原生开发人员密切合作是很常见的。

React Native 能够渲染原生 UI 视图的一个优势是它们可以轻松地嵌入到现有的原生应用中。公司已经拥有关键的原生应用对于他们的业务至关重要并不罕见。如果应用程序没有出现问题，可能没有立即需要将整个代码库重写为 React Native。在这种情况下，JavaScript 和原生开发人员都可以利用 React Native 编写的代码，将其集成到现有应用中。

本章将专注于在现有的原生 iOS 和 Android 应用中使用 React Native。我们将涵盖在原生应用中渲染 React Native 应用，如何在 React Native 应用和其原生父应用之间进行通信，以及我们的 React Native 应用如何在用户设备上与其他应用一起调用。

在处理 Android 配方时，建议您在 Android Studio 中启用自动导入设置，或使用*Alt*+*Enter*执行快速修复代码完成类导入。

# 将 React Native 应用和原生 iOS 应用结合

如果您在一家公司工作，或者有一个客户在世界上使用着一个活跃的 iOS 应用，重写它可能并不明智，特别是如果它构建良好，经常被使用，并受到用户的赞扬。如果您只想使用 React Native 构建新功能，React Native 应用可以嵌入并在现有的原生 iOS 应用中渲染。

本教程将介绍如何创建一个空白的 iOS 应用程序，并将其添加到 React Native 应用程序中，以便这两个层可以相互通信。我们将介绍两种呈现 React Native 应用程序的方法：嵌入在应用程序中作为嵌套视图，以及作为全屏实现的另一种方法。本教程讨论的步骤将作为呈现 React Native 应用程序以及原生 iOS 应用程序的基线。

# 准备工作

本教程将引用一个名为`EmbeddedApp`的原生 iOS 应用程序。我们将在本节中介绍如何创建示例 iOS 应用程序。如果您已经有一个打算与 React Native 集成的 iOS 应用程序，可以直接跳转到教程说明。但是，您需要确保已安装`cocoapods`。这个库是 Xcode 项目的包管理器。可以使用以下命令通过 Homebrew 安装它：

```jsx
brew install cocoapods
```

安装了`cocoapods`后，下一步是在 Xcode 中创建一个新的原生 iOS 项目。可以通过打开 Xcode 并选择文件|新建|项目来完成。在随后的窗口中，选择默认的单视图应用程序 iOS 模板开始，并点击下一步。

在新项目的选项屏幕中，确保将产品名称字段设置为`EmbeddedApp`：

![](img/86748b32-0ee0-44e1-82c2-bfb91772e34f.png)

# 如何操作...

1.  我们将首先创建一个新的原始 React Native 应用程序，作为我们项目的根。让我们将新项目命名为`EmbedApp`。您可以使用以下命令使用 CLI 创建新的 React Native 应用程序：

```jsx
react-native init EmbedApp
```

1.  通过使用 CLI 创建新应用程序，`ios`和`android`子文件夹将自动为我们创建，其中包含每个平台的原生代码。让我们将我们在“准备工作”部分中创建的原生应用程序移动到`ios`文件夹中，以便它位于`/EmbedApp/ios/EmbeddedApp`。

1.  现在我们已经为应用程序准备好了基本结构，我们需要添加一个 Podfile。这是一个文件，类似于 Web 开发中的`package.json`，用于跟踪项目中使用的所有 cocoapod 依赖项（称为 pods）。Podfile 应始终位于原始 iOS 项目的根目录中，在我们的情况下是`/EmbedApp/ios/EmbeddedApp`。在终端中，`cd`进入此目录并运行`pod init`命令。这将为您生成一个基本的 Podfile。

1.  接下来，在您喜欢的 IDE 中打开 Podfile。我们将向该文件添加应用程序所需的 pods。以下是最终 Podfile 的内容，其中新增的 React Native 依赖项已用粗体标出：

```jsx
target 'EmbeddedApp' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  # use_frameworks!

  # Pods for EmbeddedApp

  target 'EmbeddedAppTests' do
    inherit! :search_paths
    # Pods for testing
  end

  target 'EmbeddedAppUITests' do
    inherit! :search_paths
    # Pods for testing
  end

 # Pods that will be used in the app
 pod 'React', :path => '../../node_modules/react-native', :subspecs => [
 'Core',
 'CxxBridge', # Include this for RN >= 0.47
 'DevSupport', # Include this to enable In-App Devmenu if RN >= 0.43
 'RCTText',
 'RCTNetwork',
 'RCTWebSocket', # Needed for debugging
 'RCTAnimation', # Needed for FlatList and animations running on native UI thread
 # Add any other subspecs you want to use in your project
 ]

 # Explicitly include Yoga if you are using RN >= 0.42.0
 pod 'yoga', :path => '../../node_modules/react-native/ReactCommon/yoga'

 # Third party deps podspec link
 pod 'DoubleConversion', :podspec => '../../node_modules/react-native/third-party-podspecs/DoubleConversion.podspec'
 pod 'glog', :podspec => '../../node_modules/react-native/third-party-podspecs/glog.podspec'
 pod 'Folly', :podspec => '../../node_modules/react-native/third-party-podspecs/Folly.podspec'

end
```

请注意，我们正在添加的 React Native 依赖项中列出的每个路径都指向 React Native 项目的`/node_modules`文件夹。如果您的本地项目（在我们的情况下是`EmbeddedApp`）位于不同的位置，则必须相应地更新对`/node_modules`的引用。 

1.  有了 Podfile，安装 pod 本身就像在终端中运行`pod install`命令一样容易，我们在创建 Podfile 的同一目录中运行。

1.  接下来，让我们回到项目的根目录`/EmbedApp`中的 React Native 应用程序。我们将首先删除`index.js`中生成的代码，并用我们自己的简单的 React Native 应用程序替换它。在文件底部，我们将在`AppRegistry`组件上使用`registerComponent`方法将`EmbedApp`注册为 React Native 应用程序的根组件。这将是一个非常简单的应用程序，只是渲染文本`Hello in React Native`，以便在后续步骤中可以与本地层区分开来：

```jsx
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  View,
  Text
} from 'react-native';

class EmbedApp extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>Hello in React Native</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  }
});

AppRegistry.registerComponent('EmbedApp', () => EmbedApp);
```

1.  现在我们有了一个 React Native 应用程序，我们可以转到本地代码。当我们在*步骤 3*中初始化 cocoapods 时，它还生成了一个新的`.xcworkspace`文件。确保在 Xcode 中关闭`EmbeddedApp`项目，然后使用`EmbeddedApp.xcworkspace`文件重新在 Xcode 中打开它。

1.  在 Xcode 中，让我们打开`Main.storyboard`：

![](img/8137ae79-365d-4021-92fb-a3bbea5710bb.png)

1.  在 Storyboard 中，我们需要添加两个按钮：一个标记为 Open React Native App，另一个标记为 Open React Native App（Embedded）。我们还需要在两个按钮下方添加一个新的容器视图。最终的 Storyboard 应该看起来像这样：

![](img/a5422254-5435-4d34-b043-6a2d199e9c7b.png)

1.  接下来，我们需要一个新的 Cocoa Touch 类。这可以通过菜单选择`File | New | File`来创建。我们将类命名为`EmbeddedViewController`，并将其分配为`UIViewController`的子类：

![](img/0a544b69-116a-491e-baa8-7f3c3e89db2c.png)

1.  让我们回到`Main.storyboard`。在通过上一步添加类创建的新场景（第二个 View Controller 场景）中，选择 View Controller 子项。确保身份检查器在右侧面板中是打开的：

![](img/25acdbd3-b7a1-4297-8988-fdcfb99b9742.png)

选择 View Controller 后，将`Class`值更改为我们新创建的类`EmbeddedViewController`：

![](img/b1431a9d-3d22-43cb-90ac-a676db65613a.png)

1.  接下来，在顶部 View Controller Scene 中，选择 Embed segue 对象：

![](img/83a215de-c779-4dc6-892d-c75beede2ef1.png)

1.  选择 segue 后，从右侧面板中选择属性检查器，并将标识符字段更新为 embed 值。我们将使用此标识符将 React Native 层嵌入到原生应用程序中：

![](img/00ca967c-3df7-4d4c-8e6b-45e58d03b7ab.png)

1.  我们准备构建`ViewController`的实现。打开`ViewController.m`文件。我们将从导入开始：

```jsx
#import "ViewController.h"
#import "EmbeddedViewController.h"
#import <React/RCTRootView.h>
```

1.  在导入下面，我们可以添加一个接口定义，指向我们在*步骤 10*中创建的`EmbeddedViewController`：

```jsx
@interface ViewController () {
 EmbeddedViewController *embeddedViewController;
}

@end
```

1.  接下来是`@interface`，我们将向`@implementation`添加我们需要的方法。第一个方法`openRNAppButtonPressed`将连接到我们在故事板中创建的第一个按钮，标有“打开 React Native 应用程序”。同样，`openRNAppEmbeddedButtonPressed`方法将连接到第二个按钮“打开 React Native 应用程序（嵌入式）”。

您可能会注意到，这两种方法几乎是相同的，第二种方法引用了`embeddedViewController`，与我们在第 10 步中创建的`EmbeddedViewController`类相同（`[embeddedViewController setView:rootView];`）。这两种方法都使用`jsCodeLocation`定义了值为`http://localhost:8081/index.bundle?platform=ios`的 URL，这是 React Native 应用程序将被提供的 URL。另外，请注意，这两种方法中的`moduleName`属性都设置为`EmbedApp`，这是 React Native 应用程序的导出名称，我们在*步骤 6*中定义了它：

```jsx
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

- (IBAction)openRNAppButtonPressed:(id)sender {
 NSURL *jsCodeLocation = [NSURL
 URLWithString:@"http://localhost:8081/index.bundle?platform=ios"];
 RCTRootView *rootView =
 [[RCTRootView alloc] initWithBundleURL : jsCodeLocation
 moduleName : @"EmbedApp"
 initialProperties : nil
 launchOptions : nil];

 UIViewController *vc = [[UIViewController alloc] init];
 vc.view = rootView;
 [self presentViewController:vc animated:YES completion:nil];
}
- (IBAction)openRNAppEmbeddedButtonPressed:(id)sender {
 NSURL *jsCodeLocation = [NSURL
 URLWithString:@"http://localhost:8081/index.bundle?platform=ios"];
 RCTRootView *rootView =
 [[RCTRootView alloc] initWithBundleURL : jsCodeLocation
 moduleName : @"EmbedApp"
 initialProperties : nil
 launchOptions : nil];

 [embeddedViewController setView:rootView];
}

// Defined in next step

@end
```

1.  我们还需要定义`prepareForSegue`方法。在这里，您可以看到`segue.identifier isEqualToString:@"embed"`，这是指我们在*步骤 13*中给 segue 的嵌入标识符：

```jsx
// Defined in previous steps - (void) prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
 if([segue.identifier isEqualToString:@"embed"]) {
 embeddedViewController = segue.destinationViewController;
 }
}

@end
```

1.  在我们的`ViewController`实现就位后，现在我们需要将按钮操作连接到按钮本身。让我们返回到`Main.storyboard`。*Ctrl +*单击第一个按钮以获取可分配给按钮的操作菜单，通过从 Touch Up Inside 返回到故事板，将按钮映射到我们在*步骤 15*中定义的`openRNAppButtonPressed`方法。对于第二个按钮，重复这些步骤，将其链接到`openRNAppEmbeddedButtonPressed`方法：

![](img/7d82272a-9878-4c24-900c-86db62b70e71.png)

1.  为了使 React Native 层能够与原生层通信，我们还需要添加一个安全异常，这将允许我们的代码与`localhost`通信。右键单击`Info.plist`文件，然后选择打开为|源代码。在基本`<dict>`标签内，添加以下条目：

```jsx
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSExceptionDomains</key>
  <dict>
    <key>localhost</key>
    <dict>
      <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
      <true/>
    </dict>
  </dict>
</dict>
```

1.  我们的应用程序完成了！从`/EmbedApp`根目录，使用以下命令通过 CLI 启动 React Native 应用程序：

```jsx
react-native start
```

1.  随着 React Native 应用程序的运行，让我们也从 Xcode 运行原生应用程序`EmbeddedApp`。现在，按下打开 React Native 应用程序按钮应该会全屏打开我们在*步骤 6*中创建的 React Native 应用程序，并且在按下打开 React Native 应用程序（嵌入式）按钮时，相同的 React Native 应用程序应该在我们在*步骤 9*中创建的容器视图中打开。

# 工作原理...

在这个教程中，我们介绍了通过两种不同的方法在原生 iOS 应用程序中渲染 React Native 应用程序。第一种方法是用 React Native 应用程序替换应用程序的主`UIViewController`实例，在原生代码中称为`RCTRootView`。这是在`openRNAppButtonPressed`方法中完成的。第二种方法稍微复杂一些，是将 React Native 应用程序与原生应用程序内联渲染。这是通过创建一个容器视图来实现的，该容器视图链接到不同的`UIViewController`实例。在这种情况下，我们用我们的`RCTRootView`实例替换了`embedViewController`的内容。这就是`openRNAppEmbeddedButtonPressed`方法触发时发生的事情。

# 另见

为了更好地理解 cocoapods 在 Xcode/React Native 开发中的作用，我建议观看 Google 的*Route 85 Show*在 YouTube 上涵盖该主题的视频。视频可以在[`www.youtube.com/watch?v=iEAjvNRdZa0`](https://www.youtube.com/watch?v=iEAjvNRdZa0)找到。

# 从 iOS 应用程序到 React Native 的通信

在上一个教程中，我们学习了如何将 React Native 应用程序渲染为较大的原生 iOS 应用程序的一部分。除非您正在构建一个华丽的应用程序容器或门户，否则您可能需要在原生层和 React Native 层之间进行通信。这将是接下来两个教程的主题，每个教程都涉及通信的一个方向。

在这个示例中，我们将介绍从本地层到 React Native 层的通信，通过在 iOS 应用程序中使用`UITextField`将数据发送到我们嵌入的 React Native 应用程序。

# 准备工作

由于这个示例需要一个嵌套的 React Native 应用程序的本地应用程序，我们将从上一个示例的结尾开始，有效地接着上次离开的地方。这将帮助您了解基本的跨层通信如何工作，以便您可以在自己的本地应用程序中使用相同的原则，这可能已经存在并具有复杂的功能。因此，跟随这个示例的最简单方法是使用上一个示例的终点作为起点。

# 如何做...

1.  让我们从更新本地层的`ViewController.m`实现文件开始。确保通过上一个示例中项目中`/ios/EmbeddApp`目录中放置的`EmbeddedApp`的`.xcworkspace`文件在 Xcode 中打开项目。我们将从导入开始：

```jsx
#import "ViewController.h"
#import "EmbeddedViewController.h"
#import <React/RCTRootView.h>
#import <React/RCTBridge.h>
#import <React/RCTEventDispatcher.h>
```

1.  下一步是通过`ViewController`接口添加对 React Native 桥的引用，有效地将本地控制器与 React Native 代码链接起来：

```jsx
@interface ViewController () <RCTBridgeDelegate> {
    EmbeddedViewController *embeddedViewController;
    RCTBridge *_bridge;
    BOOL isRNRunning;
}
```

1.  我们还需要一个`@property`引用`userNameField`，我们将在后面的步骤中将其连接到`UITextField`：

```jsx
@property (weak, nonatomic) IBOutlet UITextField *userNameField;

@end
```

1.  在这个参考下面，我们将开始定义类方法。我们将从`sourceURLForBridge`方法开始，该方法定义了 React Native 应用程序的服务位置。在我们的情况下，应用程序的 URL 应该是`http://localhost:8081/index.bundle?platform=ios`，这指向了 React Native 应用程序的`index.js`文件，一旦它使用`react-native start`命令运行：

```jsx
- (NSURL *)sourceURLForBridge:(RCTBridge *)bridge {
    NSURL *jsCodeLocation = [NSURL
                             URLWithString:@"http://localhost:8081/index.bundle?platform=ios"];
    return jsCodeLocation;
}
```

1.  我们将保留`viewDidLoad`和`didReveiveMemoryWarning`方法不变：

```jsx
- (void)viewDidLoad {
    [super viewDidLoad];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}
```

1.  接下来，我们需要更新`openRNAppEmbeddedButtonPressed`方法。注意`moduleName`属性设置为`FromNativeToRN`。这是我们导出 React Native 应用程序时给出的名称的引用，我们将在后面的步骤中定义。这次，我们还定义了一个`userName`属性，用于向 React Native 层传递数据：

```jsx
- (IBAction)openRNAppEmbeddedButtonPressed:(id)sender {
    NSString *userName = _userNameField.text;
    NSDictionary *props = @{@"userName" : userName};

    if(_bridge == nil) {
        _bridge = [[RCTBridge alloc] initWithDelegate:self 
        launchOptions:nil];
    }

    RCTRootView *rootView =
    [[RCTRootView alloc] initWithBridge :_bridge
                             moduleName : @"FromNativeToRN"
                      initialProperties : props];

    isRNRunning = true;
    [embeddedViewController setView:rootView];
}
```

1.  我们还需要一个`onUserNameChanged`方法。这是将数据实际发送到 React Native 层的方法。我们在这里定义的事件名称是`UserNameChanged`，我们将在后面的步骤中在 React Native 层中引用它。这也将传递当前文本输入中的文本，该文本将被命名为`userNameField`：

```jsx
- (IBAction)onUserNameChanged:(id)sender {
    if(isRNRunning == YES && _userNameField.text.length > 3) {
        [_bridge.eventDispatcher sendAppEventWithName:@"UserNameChanged" body:@{@"userName" : _userNameField.text}];
    }
}
```

1.  我们还需要`prepareForSegue`来配置`embeddedViewController`，就在它显示之前：

```jsx
- (void) prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    if([segue.identifier isEqualToString:@"embed"]) {
        embeddedViewController = segue.destinationViewController;
    }
}
@end
```

1.  回到`Main.storyboard`，让我们添加一个文本字段，以及一个定义输入用途的标签。您还可以将输入命名为 User Name Field，以便在视图控制器场景中更容易识别：

![](img/c44f2cfe-f832-489c-8dcf-543f805b85c6.png)

1.  接下来，我们需要为`User Name Field`文本输入的文本更改事件和引用出口进行连接，以便视图控制器知道如何引用它。这两者都可以通过连接检查器完成，连接检查器可以通过右侧面板顶部的最后一个按钮（图标是一个右指向箭头在一个圆圈中）访问。选择文本输入后，从`Editing Changed`拖动到视图控制器（通过主故事板表示），并选择我们在*步骤 7*中定义的`onUserNameChange`方法。然后，通过将项目拖动到`ViewController`来创建以下连接。类似地，通过从新引用出口拖动到视图控制器，这次选择我们在*步骤 7*中定位的 userNameField 值，添加一个新的引用出口。您的连接检查器设置现在应该如下所示：

![](img/e423f6dc-291d-448b-aef0-95862640aa3a.png)

1.  我们现在已经完成了原生应用程序中所需的步骤。让我们继续进行 React Native 层。回到`index.js`文件，我们将从导入开始。请注意，我们现在包括了`NativeAppEventEmitter`。

1.  将以下函数放在类定义内部：

```jsx
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  View,
  Text,
  NativeAppEventEmitter
} from 'react-native';
```

1.  我们将应用程序命名为`FromNativeToRN`，以匹配我们在*步骤 6*中定义的原生层中的模块名称，使用`AppRegistry.registerComponent`来注册具有相同名称的应用程序。我们还将保留基本样式。

```jsx
class FromNativeToRN extends Component {
 // Defined in following steps
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  }
});

AppRegistry.registerComponent('FromNativeToRN', () => FromNativeToRN);
```

1.  我们将设置一个初始的`state`对象，其中包含一个`userName`字符串属性，用于存储和显示从原生层接收到的文本：

```jsx
class FromNativeToRN extends Component {
 state = {
 userName: ''
 }

 // Defined in following steps
}
```

1.  传递到 React Native 层的`userName`值将作为属性接收。当组件挂载时，我们希望做两件事：如果原生层已经定义了`userName`状态属性，则设置`userName`状态属性，并将事件监听器连接到在原生层中更新`userName`时更新`userName`。回想一下，在*步骤 7*中，我们定义了事件的名称为`UserNameChanged`，这就是我们要监听的事件。当接收到事件时，我们将更新`state.userName`为事件传递的文本：

```jsx
  componentWillMount() {
    this.setState({
      userName : this.props.userName
    });

    NativeAppEventEmitter.addListener('UserNameChanged', (body) => {
        this.setState({userName : body.userName});
    });
  }
```

1.  最后，我们可以添加`render`函数，它简单地渲染`state.userName`中存储的值：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Text>Hello {this.state.userName}</Text>
      </View>
    );
  }
```

1.  是时候运行我们的应用程序了！首先，在项目的根目录中，我们可以使用以下命令通过 React Native CLI 启动 React Native 应用程序：

```jsx
react-native start
```

接着我们通过 Xcode 在模拟器中运行原生应用程序：

![](img/f917a790-b142-4636-b6e0-b547fae1b21f.png)

# 从 React Native 通信到 iOS 应用程序容器

上一个示例涵盖了在原生到 React Native 方向上的层间通信。在这个示例中，我们将涵盖在相反方向上的通信：从 React Native 到原生。这次，我们将在 React Native 应用程序中渲染一个用户输入元素，并设置从 React Native 到在原生应用程序中渲染的 UI 组件的单向绑定。

# 准备工作

就像上一个示例一样，这个示例取决于本章第一个应用程序的最终产品，在*将 React Native 应用程序和原生 iOS 应用程序组合*示例中。要跟着做，请确保你已经完成了那个示例。

# 如何做...

1.  让我们从原生层开始。通过`.xcworkspace`文件在 Xcode 中打开`EmbeddedApp`原生应用程序。我们首先要在`ViewController.m`中添加导入：

```jsx
#import "ViewController.h"
#import "EmbeddedViewController.h"
#import <React/RCTRootView.h>
#import <React/RCTBridge.h>
#import <React/RCTEventDispatcher.h>
```

1.  与上一个示例一样，我们需要通过`ViewController`接口添加对 React Native 桥的引用，提供原生控制器和 React Native 代码之间的桥接：

```jsx
@interface ViewController () <RCTBridgeDelegate> {
    EmbeddedViewController *embeddedViewController;
    RCTBridge *_bridge;
    BOOL isRNRunning;
}
```

1.  我们还需要一个`@property`引用`userNameField`，我们将在后面的步骤中将其连接到`UITextField`：

```jsx
@property (weak, nonatomic) IBOutlet UITextField *userNameField;

@end
```

1.  让我们继续定义`@implementation`。同样，我们必须提供 React Native 应用程序的源，它将从`localhost`提供：

```jsx
@implementation ViewController

- (NSURL *)sourceURLForBridge:(RCTBridge *)bridge {
    NSURL *jsCodeLocation = [NSURL
                             URLWithString:@"http://localhost:8081/index.bundle?platform=ios"];
    return jsCodeLocation;
}
```

1.  使用`viewDidLoad`方法，我们还可以将控制器连接到在容器视图中打开 React Native 应用程序的方法(`openRNAppEmbeddedButtonPressed`)。我们将保持`didReveiveMemoryWarning`方法不变：

```jsx
- (void)viewDidLoad {
    [super viewDidLoad];
 [self openRNAppEmbeddedButtonPressed:nil];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}
```

1.  与上一个配方一样，我们需要更新`openRNAppEmbeddedButtonPressed`方法。这次，`moduleName`属性设置为`FromRNToNative`，以反映我们在导出时将给 React Native 应用程序的名称，如后面的步骤中定义的。我们还定义了一个`userName`属性，用于向 React Native 层传递数据：

```jsx
- (IBAction)openRNAppEmbeddedButtonPressed:(id)sender {
    if(_bridge == nil) {
        _bridge = [[RCTBridge alloc] initWithDelegate:self launchOptions:nil];
    }

    RCTRootView *rootView =
    [[RCTRootView alloc] initWithBridge :_bridge
                      moduleName : @"FromRNToNative"
                      initialProperties : nil];

    isRNRunning = true;
    [embeddedViewController setView:rootView];
}
```

1.  我们在这个文件中还需要的最后两个方法是`prepareForSegue`，用于在显示之前配置`embeddedViewController`，以及一个`updateUserNameField`方法，当我们在本地层的文本输入中使用用户的新文本更新时将被触发：

```jsx
- (void) prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    if([segue.identifier isEqualToString:@"embed"]) {
        embeddedViewController = segue.destinationViewController;
    }
}

-(void) updateUserNameField:(NSString *)userName {
    [_userNameField setText:userName];
}
@end
```

1.  与上一个配方不同，我们还需要更新`ViewController`头文件（`ViewController.h`）。在这里引用的方法`updateUserNameField`将在我们定义`ViewController`实现时使用：

```jsx
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController
- (void) updateUserNameField:(NSString *)userName;

@end
```

1.  接下来，我们需要创建一个新的`UserNameManager`本地模块。首先，创建一个名为`UserNameManager`的 Cocoa Touch 类。创建后，让我们打开实现文件（`UserNameManger.m`）并添加我们的导入：

```jsx
#import "UserNameManager.h"
#import "AppDelegate.h"
#import "ViewController.h"
#import <React/RCTBridgeModule.h>
```

要深入了解创建本地模块，请参阅第十一章中的*公开自定义 iOS 模块*配方。

1.  接下来，我们将定义类实现。这里的主要要点是`setUserName`方法，这是我们从本地层导出供 React Native 应用程序使用的方法。我们将在 React Native 应用程序中使用此方法来更新本地文本字段中的值。然而，由于我们正在更新本地 UI 组件，操作必须在主线程上执行。这就是`methodQueue`函数的目的，它指示模块在主线程上执行：

```jsx
@implementation UserNameManager
RCT_EXPORT_MODULE();

- (dispatch_queue_t)methodQueue
{
    return dispatch_get_main_queue();
}

RCT_EXPORT_METHOD(setUserName: (NSString *)userName) {
    AppDelegate *delegate = (AppDelegate *)[[UIApplication sharedApplication] delegate];
    ViewController *controller = (ViewController *)delegate.window.rootViewController;

    [controller updateUserNameField:userName];
}
@end
```

1.  我们还需要更新`UserNameMangager.h`头文件以使用 React Native 桥接模块：

```jsx
#import <Foundation/Foundation.h>
#import <React/RCTBridgeModule.h>

@interface UserNameManager : NSObject <RCTBridgeModule>

@end
```

1.  与上一个配方一样，我们需要为用户名输入添加一个文本字段和标签：

![](img/5e24e43a-1913-49b0-b7c5-0c78dceebc4e.png)

1.  我们还需要从上一组中创建的文本字段到我们的`userNameField`属性添加一个引用输出：

![](img/0d70c124-06e7-4132-b78a-985abbe8592c.png)如果您需要更多关于如何创建引用输出的信息，请查看上一个配方的*步骤 10*。

1.  我们已经完成了这个项目的本地部分，现在让我们转向我们的 React Native 代码。让我们打开项目根目录下的`index.js`文件。我们将从导入开始：

```jsx
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  View,
  Text,
  TextInput,
  NativeModules
} from 'react-native';
```

1.  让我们使用名称`FromRNToNative`来定义应用程序，以便与我们在原生代码中*步骤 6*中声明的`moduleName`对齐，并使用相同名称注册组件。`state`对象只需要一个`userName`字符串属性来保存保存到`TextInput`组件的值，我们将在组件的`render`函数中添加它：

```jsx
class FromRNToNative extends Component {
  state = {
    userName: ''
  }

  // Defined on next step
}

AppRegistry.registerComponent('FromRNToNative', () => FromRNToNative);
```

1.  应用程序的`render`函数使用`TextInput`组件从用户那里获取输入，然后通过 React Native 桥将其发送到原生应用程序。它通过在`TextInput`的值改变时调用`onUserNameChange`方法来实现这一点：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Text>Enter User Name</Text>
        <TextInput
          style={styles.userNameField}
          onChangeText={this.onUserNameChange}
          value={this.state.userName}
        />
      </View>
    );
  }
```

1.  我们需要做的最后一件事是定义`onUserNameChange`方法，该方法由我们在上一步中定义的`TextInput`组件的`onChangeText`属性使用。此方法将`state.userName`更新为文本输入中的值，并通过 React Native 中的`NativeModules`组件将该值发送到原生代码。`NativeModules`具有我们在原生层*步骤 9*中定义为 Cocoa Touch 类的`UserNameManager`类。我们在*步骤 10*中调用了我们在类中定义的`setUserName`方法，将该值传递到原生层，在那里它将显示在我们在*步骤 12*中创建的文本字段中：

```jsx
  onUserNameChange = (userName) => {
    this.setState({userName});
    NativeModules.UserNameManager.setUserName(userName);
  }
```

1.  应用程序完成了！返回到项目的根目录，使用以下命令启动 React Native 应用程序：

```jsx
react-native start
```

然后，启动 React Native 应用程序后，从 Xcode 运行原生`EmbeddedApp`项目。现在，React Native 应用程序中的输入应该将其值传递给父原生应用程序中的输入：

![](img/02a70ade-cb5a-4dd8-bd2e-13ea02e8a899.png)

# 它是如何工作的...

为了从我们的 React Native 应用程序通信到父原生应用程序，我们创建了一个名为`UserNameManager`的原生模块，其中包含一个`setUserName`方法，我们从原生层导出，并在 React Native 应用程序中使用，在其`onUserNameChange`方法中。这是从 React Native 到原生通信的推荐方式。

# 处理被外部 iOS 应用程序调用

原生应用程序之间通过链接进行通信也是一种常见行为，并且通常提示用户使用短语“在...中打开”，以及更好地处理操作的应用程序的名称。这是通过使用特定于您的应用程序的协议来完成的。就像任何网站链接都有`http://`或`https://`的协议一样，我们也可以创建一个自定义协议，允许任何其他应用程序打开并向我们的应用程序发送数据。

在这个教程中，我们将创建一个名为`invoked://`的自定义协议。通过使用`invoked://`协议，任何其他应用程序都可以使用它来运行我们的应用程序并向其传递数据。

# 准备工作

对于这个教程，我们将从一个新的原生 React Native 应用程序开始。让我们将其命名为`InvokeFromNative`。

# 如何操作...

1.  首先，让我们在 Xcode 中打开新项目的本地层。我们需要做的第一件事是调整项目的构建设置。这可以通过在左侧面板中选择根项目，然后选择中间面板顶部的构建设置选项卡来完成：

![](img/d0e90cd7-0f3c-4270-85a1-05792d34f948.png)

1.  我们需要向`Header Search Paths`字段添加一个新条目：

![](img/3e1e1fb4-601b-4bbf-94f1-554e18e332da.png)

为了使项目知道 React Native JavaScript 的位置，它需要`$(SRCROOT)/../node_modules/react-native/Libraries`的值。让我们将其添加为递归条目：

![](img/068fa616-dda9-4c8c-b4ce-d540f1217b06.png)

1.  我们还需要注册我们的自定义协议，这将被其他应用程序使用。打开`Info.plist`文件作为源代码（右键单击然后选择`Open As | Source Code`）。让我们向文件添加一个条目，以注册我们的应用程序在`invoked://`协议下：

```jsx
<key>CFBundleURLTypes</key>
<array>
  <dict>
    <key>CFBundleTypeRole</key>
    <string>Editor</string>
    <key>CFBundleURLSchemes</key>
    <array>
      <string>invoked</string>
    </array>
  </dict>
</array>
```

1.  接下来，我们需要将`RCTLinkingManager`添加到`AppDelegate`实现中，它位于`AppDelegate.m`中，并将其连接到我们的应用程序：

```jsx
#import "AppDelegate.h"

#import <React/RCTBundleURLProvider.h>
#import <React/RCTRootView.h>
#import <React/RCTLinkingManager.h>

@implementation AppDelegate

// The rest of the AppDelegate implementation

- (BOOL)application:(UIApplication *)application
 openURL:(NSURL *)url
 options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
 return [RCTLinkingManager application:application openURL:url options:options];
}

@end
```

1.  现在，让我们继续进行 React Native 层。在`index.js`中，我们将添加我们的导入，其中包括`Linking`组件：

```jsx
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Linking
} from 'react-native';
```

1.  接下来，我们将创建类定义并将组件注册为`InvokeFromNative`。我们还将定义一个初始的`state`对象，其中包含一个`status`字符串属性，其值为`'App Running'`：

```jsx
class InvokeFromNative extends Component {
 state = {
 status: 'App Running'
 }

 // Defined on following steps
}

AppRegistry.registerComponent('InvokeFromNative', () => InvokeFromNative);
```

1.  现在，我们将使用挂载和卸载生命周期钩子来`add`/`remove`对`invoked://`协议的事件监听器。当事件被听到时，将触发下一步中定义的`onAppInvoked`方法：

```jsx
  componentWillMount() {
    Linking.addEventListener('url', this.onAppInvoked);
  }

  componentWillUnmount() {
    Linking.removeEventListener('url', this.onAppInvoked);
  }
```

1.  `onAppInvoked`函数简单地接受事件监听器的事件并更新`state.status`以反映发生了调用，通过`event.url`显示协议：

```jsx
  onAppInvoked = (event) => {
    this.setState({
      status: `App Invoked by ${ event.url }`
    });
  }
```

1.  在这个教程中，`render`方法的唯一真正目的是在状态上呈现`status`属性：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.instructions}>
          App Status:
        </Text>
        <Text style={styles.welcome}>
          {this.state.status}
        </Text>
      </View>
    );
  }
```

1.  我们还将添加一些基本样式来居中和调整文本的大小：

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
});
```

1.  我们的应用程序已经完成。一旦您开始运行应用程序，您应该会看到类似于这样的东西：

![](img/d62bd623-bd8b-495d-a7c1-39a95eedbec4.png)

1.  应用程序运行时，我们可以模拟另一个应用程序打开我们的 React Native 应用程序的操作，使用`invoked://`协议可以通过以下终端命令完成：

```jsx
 xcrun simctl openurl booted invoked://
```

一旦调用，应用程序应更新以反映调用：

![](img/c8a2362a-570a-4888-a103-c418c6f76378.png)

# 工作原理…

在这个配方中，我们介绍了如何注册自定义协议（或 URL 模式），以允许我们的应用程序被其他应用程序调用。这个配方的目的是尽可能简单地保持我们的示例，因此我们没有构建通过链接机制传递给应用程序的数据处理。但是，如果您的应用程序需要，完全可以这样做。要深入了解`Linking`组件，请查看官方文档[`facebook.github.io/react-native/docs/linking`](https://facebook.github.io/react-native/docs/linking)。

# 结合 React Native 应用程序和本机 Android 应用程序

由于 Android 平台仍然在智能手机市场占据主导地位，您可能希望为 Android 和 iOS 构建应用程序。React Native 开发的一个重大优势是使这一过程更加容易。但是，当您想要使用 React Native 为已经发布的 Android 应用程序编写新功能时会发生什么？幸运的是，React Native 也可以实现这一点。

本文将介绍在现有 Android 应用程序中嵌入 React Native 应用程序的过程，方法是在容器视图中显示 React Native 应用程序。这些步骤将作为后续配方的基线，这些配方涉及与 React Native 应用程序的通信。

# 准备工作

在本节中，我们将使用 Android Studio 创建一个名为`EmbedApp`的示例 Android 应用程序。如果您有一个基本的 Android 应用程序要使用，可以跳过这些步骤并继续进行实际实现：

1.  打开 Android Studio 并创建一个新项目（文件|新项目）

1.  将应用程序名称设置为`EmbeddedApp`并填写您的公司域。按“下一步”

1.  保留“空活动”作为默认选择，然后按“下一步”

1.  将 Activity 属性保留为默认值，然后按“完成”

# 如何做…

1.  此时，我们的应用程序没有与 React Native 相关的引用，因此我们将从安装它开始。在应用程序的根文件夹中，在终端中使用`yarn`命令安装 React Native：

```jsx
yarn add react-native
```

或者，您可以使用`npm`：

```jsx
 npm install react-native --save
```

1.  我们还需要一个用于启动 React Native 应用程序的 Node.js 脚本。让我们打开`package.json`并将以下属性添加为`scripts`对象的成员：

```jsx
 "start": "node node_modules/react-native/local-cli/cli.js start"
```

1.  对于这个示例，我们只需要一个非常简单的 React Native 应用程序。让我们创建一个带有以下样板应用程序的`index.android.js`文件：

```jsx
import React, { Component } from 'react';
import { AppRegistry, StyleSheet, View, Text } from 'react-native';

export default class EmbedApp extends Component {
  render() {
    return (<View style={styles.container}>
      <Text>Hello in React Native</Text>
    </View>);
  }
}

const styles = StyleSheet.create({
 container: {
   flex: 1,
   justifyContent: 'center',
   alignItems: 'center', backgroundColor: '#F5FCFF'
  }
});

AppRegistry.registerComponent('EmbedApp', () => EmbedApp);
```

将此文件命名为`index.android.js`表示对 React Native 来说，此代码仅适用于此应用程序的 Android 版本。这是官方文档推荐的做法，当平台特定的代码更复杂时。您可以在[`facebook.github.io/react-native/docs/platform-specific-code#platform-specific-extensions`](https://facebook.github.io/react-native/docs/platform-specific-code#platform-specific-extensions)了解更多信息。

1.  让我们返回到 Android Studio 并打开`build.gradle`文件（来自`app`模块），并将以下内容添加到`dependencies`中：

```jsx
dependencies {
    implementation fileTree(dir: "libs", include: ["*.jar"]) 
    implementation "com.android.support:appcompat-v7:27.1.1"
    implementation "com.facebook.react:react-native:+" // From node_modules
}   
```

1.  我们还需要一个对本地 React Native maven 目录的引用。打开另一个`build.gradle`文件，并将以下行添加到`allprojects.repositories`对象中：

```jsx
allprojects {
  repositories {
    mavenLocal()
      maven {
        url "$rootDir/../node_modules/react-native/android"
      }
    google()
    jcenter()
  }
}
```

1.  接下来，让我们更新应用程序的权限以使用互联网和系统警报窗口。我们将打开`AndroidManifest.xml`并将以下权限添加到`<manifest>`节点：

```jsx
<?xml version="1.0" encoding="utf-8"?>
 <manifest 
    package="com.warlyware.embeddedapp">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>

    <application
        android:name=".EmbedApp" 
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

 </manifest>
```

1.  我们准备更新`MainApplication` Java 类。这里的`getUseDeveloperSupport`方法将启用开发菜单。`getPackages`方法是应用程序使用的包的列表，只包括`MainReactPackage()`，因为我们只使用主要的 React 包。`getJSMainModuleName`方法返回`index.android`字符串，它指的是 React Native 层中的`index.android.js`文件：

```jsx
import android.app.Application;

import com.facebook.react.ReactApplication;
import com.facebook.react.ReactNativeHost;
import com.facebook.react.ReactPackage;
import com.facebook.react.shell.MainReactPackage;

import java.util.Arrays;
import java.util.List;

public class MainApplication extends Application implements ReactApplication {
  private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
    @Override
    public boolean getUseDeveloperSupport() {
      return BuildConfig.DEBUG;
    }

    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
        new MainReactPackage()
      );
    }
  };

  @Override
  public ReactNativeHost getReactNativeHost() {
    return mReactNativeHost;
  }
  @Override
  protected String getJSMainModuleName() {
    return "index.android";
  }
}
```

1.  接下来，让我们创建另一个名为`ReactFragment`的新 Java 类。这个类需要三个方法：`OnAttach`在片段附加到主活动时调用，`OnCreateView`实例化片段的视图，`OnActivityCreated`在活动被创建时调用：

```jsx
import android.app.Fragment;
import android.content.Context;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.ViewGroup;

import com.facebook.react.ReactInstanceManager;
import com.facebook.react.ReactRootView;

public abstract class ReactFragment extends Fragment {
  private ReactRootView mReactRootView;
  private ReactInstanceManager mReactInstanceManager;

  // This method returns the name of our top-level component to show
  public abstract String getMainComponentName();

  @Override
  public void onAttach(Context context) {
    super.onAttach(context);
    mReactRootView = new ReactRootView(context);
    mReactInstanceManager =
      ((EmbedApp) getActivity().getApplication())
        .getReactNativeHost()
        .getReactInstanceManager();
  }

  @Override
  public ReactRootView onCreateView(LayoutInflater inflater, ViewGroup group, Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    return mReactRootView;
  }

  @Override
  public void onActivityCreated(Bundle savedInstanceState) {
    super.onActivityCreated(savedInstanceState);
    mReactRootView.startReactApplication(
      mReactInstanceManager,
      getMainComponentName(),
      getArguments()
    );
  }
}
```

1.  最后，创建一个名为`EmbedFragment`的 Java 类，它将扩展`ReactFragment`：

```jsx
import android.os.Bundle;

public class EmbedFragment extends ReactFragment {
  @Override
  public String getMainComponentName() {
    return "EmbedApp";
  }
}
```

1.  让我们打开`MainActivity.java`并在类定义中添加`implements DefaultHardwareBackBtnHandler`以处理硬件返回按钮事件。您可以在此处查看此 React Native 类的带注释源代码：[`github.com/facebook/react-native/blob/master/ReactAndroid/src/main/java/com/facebook/react/modules/core/DefaultHardwareBackBtnHandler.java`](https://github.com/facebook/react-native/blob/master/ReactAndroid/src/main/java/com/facebook/react/modules/core/DefaultHardwareBackBtnHandler.java)。

1.  我们还将向类中添加一些方法。`onCreate`方法将把内容视图设置为主活动，并添加一个 FAB 按钮，当点击时，将实例化我们在*步骤 10*中定义的`EmbedFragment`的新实例。`EmbedFragment`的这个实例由片段管理器用于将 React Native 应用添加到视图中。其余方法处理设备系统按钮被按下时发生的事件（如返回、暂停和恢复按钮）：

```jsx
import android.app.Fragment;
import android.os.Bundle;
import android.support.design.widget.FloatingActionButton;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.view.KeyEvent;
import android.view.View;

import com.facebook.react.ReactInstanceManager;
import com.facebook.react.modules.core.DefaultHardwareBackBtnHandler;

public class MainActivity extends AppCompatActivity implements DefaultHardwareBackBtnHandler {
  private ReactInstanceManager mReactInstanceManager;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
    setSupportActionBar(toolbar);

    FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
    fab.setOnClickListener(new View.OnClickListener() {
      @Override
      public void onClick(View view) {
        Fragment viewFragment = new EmbedFragment();
        getFragmentManager().beginTransaction().add(R.id.reactnativeembed, viewFragment).commit(); }
    });

    mReactInstanceManager = ((EmbedApp) getApplication()).getReactNativeHost().getReactInstanceManager();
  }

  @Override
  public void invokeDefaultOnBackPressed() {
    super.onBackPressed();
  }

  @Override
  protected void onPause() {
    super.onPause();

    if (mReactInstanceManager != null) {
      mReactInstanceManager.onHostPause(this);
    }
  }

  @Override
  protected void onResume() {
    super.onResume();

    if (mReactInstanceManager != null) {
      mReactInstanceManager.onHostResume(this, this);
    }
  }

  @Override
  protected void onDestroy() {
    super.onDestroy();

    if (mReactInstanceManager != null) {
      mReactInstanceManager.onHostDestroy(this);
    }
  }

  @Override
  public void onBackPressed() {
    if (mReactInstanceManager != null) {
      mReactInstanceManager.onBackPressed();
    } else {
      super.onBackPressed();
    }
  }

  @Override
  public boolean onKeyUp(int keyCode, KeyEvent event) {
    if (keyCode == KeyEvent.KEYCODE_MENU && mReactInstanceManager != null) {
      mReactInstanceManager.showDevOptionsDialog();
      return true;
    }
    return super.onKeyUp(keyCode, event);
  }
}
```

1.  最后一步是在片段加载时添加一些布局设置。我们需要编辑位于`/res`文件夹中的`content_main.xml`文件。这是视图的主要内容。它包含我们将附加片段的容器视图（`FrameLayout`），以及其他本机元素应该显示的内容：

```jsx
 <FrameLayout
    android:layout_width="match_parent"
    android:layout_height="300dp"
    android:layout_centerVertical="true"
    android:layout_alignParentStart="true"
    android:id="@+id/reactnativeembed"
    android:background="#FFF">
</FrameLayout>
```

1.  在终端中运行以下命令：

```jsx
 react-native start 
```

这将构建和托管 React Native 应用。现在，我们可以在 Android 模拟器中打开应用。按下 FAB 按钮后，您将看到以下内容：

![](img/6f5ee6bb-0ec1-4474-9748-606437b3d2c8.png)

# 工作原理...

为了在我们的 Android 应用程序中呈现 React Native，我们需要执行一些步骤。首先，我们需要定义一个实现`ReactApplication`接口的`Application`类。然后，我们需要创建一个负责实例化和呈现`ReactRootView`的`Fragment`。通过片段，我们能够在我们的`MainActivity`中呈现 React Native 视图。在这个教程中，我们将片段添加到我们的片段容器视图中。这实质上用 React Native 应用程序替换了所有应用程序内容。

在这个教程中，我们涵盖了大量的集成代码。要更深入地了解每个部分的工作原理，您可以阅读官方文档[`facebook.github.io/react-native/docs/integration-with-existing-apps.html`](https://facebook.github.io/react-native/docs/integration-with-existing-apps.html)。

# 从 Android 应用程序到 React Native 的通信

现在我们已经介绍了如何在*将 React Native 应用程序和本机 Android 应用程序结合*的教程中渲染我们的 React Native 应用程序，我们准备迈出下一步。我们的 React Native 应用程序应该不仅仅是一个虚拟 UI。它应该能够对其父应用程序中正在进行的操作做出反应。

在这个教程中，我们将完成从我们的 Android 应用程序发送数据到我们嵌入的 React Native 应用程序。当 React Native 应用程序首次实例化时，它可以接受数据，然后在运行时。我们将介绍如何完成这两种方法。这个教程将在 Android 应用程序中使用`EditText`，并设置单向绑定到 React Native 应用程序。

# 准备工作

对于这个教程，请确保您有一个嵌入了 React Native 应用程序的 Android 应用程序。如果您需要指导来完成这一点，请完成*将 React Native 应用程序和本机 Android 应用程序结合*的教程。

# 如何做...

1.  在 Android Studio 中，打开 React Native 应用程序的 Android 部分。首先，我们需要编辑`content_main.xml`。

1.  对于这个应用程序，我们只需要一个非常简单的布局。您可以通过按下底部的“文本”选项卡来编辑文件，打开源编辑器并添加/替换以下节点：

```jsx
<TextView android: layout_width = "wrap_content"
android: layout_height = "wrap_content"
android: text = "Press the Mail Icon to start the React Native application"
android: id = "@+id/textView" />
<FrameLayout android: layout_width = "match_parent"
android: layout_height = "300dp"
android: layout_centerVertical = "true"
android: layout_alignParentStart = "true"
android: id = "@+id/reactnativeembed"
android: background = "#FFF" >
</FrameLayout>
<LinearLayout android:orientation="horizontal"
android:layout_width="match_parent"
android:layout_height="75dp"
android:layout_below="@+id/textView"
android:layout_centerHorizontal="true">
  <TextView
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:text="User Name:"
  android:id="@ + id / textView2"
  android:layout_weight="0.14 " />
  <EditText android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:id="@ + id / userName"
  android:layout_weight="0.78"
  android:inputType="text"
  android:singleLine="true"
  android:imeOptions="actionDone"/>
</LinearLayout>
```

1.  打开`MainActivity.java`并添加以下类字段：

```jsx
private ReactInstanceManager mReactInstanceManager;
private EditText userNameField;
private Boolean isRNRunning = false;
```

1.  在“onCreate”方法中，使用以下代码设置`userNameField`属性：

```jsx
  userNameField = (EditText) findViewById(R.id.userName);
```

1.  我们将使用 FAB 按钮来更新 Android 应用程序的内容为我们的 React Native 应用程序。我们需要用以下内容替换`FloatingActionButtononClickListener`：

```jsx
fab.setOnClickListener(new View.OnClickListener() {
  @Override public void onClick(View view) {
    Fragment viewFragment = new EmbedFragment();
    if (userNameField.getText().length() > 0) {
      Bundle launchOptions = new Bundle();
      launchOptions.putString("userName", 
      userNameField.getText().toString());
      viewFragment.setArguments(launchOptions);
    }
    getFragmentManager().beginTransaction().add(R.id.reactnativeembed, viewFragment).commit();
    isRNRunning = true;
  }
});
```

1.  接下来，我们需要在`onCreate`方法中为我们的`userNameField`添加一个`TextChangedListener`：

```jsx
userNameField.addTextChangedListener(new TextWatcher() {
  @Override public void beforeTextChanged(CharSequence s, int start, int count, int after) {}
  @Override public void onTextChanged(CharSequence s, int start, int before, int count) {}
  @Override public void afterTextChanged(Editable s) {
    if (isRNRunning) {
      sendUserNameChange(s.toString());
    }
  }
});
```

1.  我们需要为我们的`Activity`做的最后一项更改是添加方法，将事件发送到 React Native 桥接中：

```jsx
private void sendUserNameChange(String userName) {
  WritableMap params = Arguments.createMap();
  params.putString("userName", userName);
  sendReactEvent("UserNameChanged", params);
}

private void sendReactEvent(String eventName, WritableMap params) {
  mReactInstanceManager.getCurrentReactContext()
    .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
    .emit(eventName, params);
}
```

1.  让我们回到 JavaScript 层。我们将使用`NativeAppEventEmitter`组件的`addListener`方法来监听从本机 Android 代码发送的`UserNameChanged`事件，并使用事件中的数据更新`state.userName`：

```jsx
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  View,
  Text,
  NativeAppEventEmitter
} from 'react-native';

export default class EmbedApp extends Component<{}> {
  componentWillMount() {
    this.setState({
      userName : this.props.userName
    });

    NativeAppEventEmitter.addListener('UserNameChanged', (body) => {
        this.setState({userName : body.userName});
    });
  }
  render() {
    return (
      <View style={styles.container}>
        <Text>Hello {this.state.userName}</Text>
      </View>
    );
  }
}

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
});

AppRegistry.registerComponent('EmbedApp', () => EmbedApp);
```

1.  现在，如果您运行应用程序，您可以在“用户名”字段中输入文本，并启动 React Native 应用程序：

![](img/8eed73c3-6777-48bd-81f6-965e890169aa.png)

# 它是如何工作的...

在这个示例中，我们将片段呈现为内联视图。在*步骤 2*中，我们添加了一个空的`FrameLayout`，我们在*步骤 5*中将其定位为呈现片段。通过使用 React Native 桥接器`RCTDeviceEventEmitter`来实现绑定功能。这最初是设计用于与本地模块一起使用的，但只要您可以访问`ReactContext`实例，就可以将其用于与 React Native JavaScript 层的任何通信。

# 从 React Native 通信到 Android 应用程序容器

正如我们在上一个示例中讨论的那样，让我们的嵌入式应用程序了解其周围发生的事情是非常有益的。我们还应该努力让我们的 Android 父应用程序了解 React Native 应用程序内部发生的事情。应用程序不仅应能执行业务逻辑-还应能更新其 UI 以反映嵌入应用程序中的更改。

这个示例向我们展示了如何利用本地模块来更新在 Android 应用程序内部创建的本地 UI。我们的 React Native 应用程序中将有一个文本字段，用于更新在主机 Android 应用程序中呈现的文本字段。

# 准备工作

对于这个示例，请确保您有一个嵌入了 React Native 应用程序的 Android 应用程序。如果您需要指导来完成这个任务，请完成*组合 React Native 应用程序和本地 Android 应用程序*示例。

# 如何做...

1.  打开 Android Studio 到您的项目并打开`content_main.xml`。

1.  按底部的 Text 标签打开源编辑器，并添加/替换以下节点：

```jsx
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout 

  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:paddingBottom="@dimen/activity_vertical_margin"
  android:paddingLeft="@dimen/activity_horizontal_margin"
  android:paddingRight="@dimen/activity_horizontal_margin"
  android:paddingTop="@dimen/activity_vertical_margin"
  app:layout_behavior="@string/appbar_scrolling_view_behavior"
  tools:context="com.embedapp.MainActivity"
  tools:showIn="@layout/activity_main">

  <TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Press the Mail Icon to start the React Native application"
    android:id="@+id/textView" />

  <FrameLayout
    android:layout_width="match_parent"
    android:layout_height="300dp"
    android:layout_centerVertical="true"
    android:layout_alignParentStart="true"
    android:id="@+id/reactnativeembed"
    android:background="#FFF"></FrameLayout>

  <LinearLayout
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="75dp"
    android:layout_below="@+id/textView"
    android:layout_centerHorizontal="true">

    <TextView
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="User Name:"
      android:id="@+id/textView2"
      android:layout_weight="0.14" />

      <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/userName"
        android:layout_weight="0.78"
        android:inputType="text"
        android:singleLine="true"
        android:imeOptions="actionDone"/>
  </LinearLayout>
</RelativeLayout>
```

1.  创建一个名为`UserNameManager`的 Java 类。这将是一个本地模块，用于更新我们添加到布局中的`EditTextfield`的目的。

如果您不熟悉为 React Native 创建本地模块，请参阅第十一章中的*公开自定义 Android 模块*示例，*添加本地功能*。

1.  `UserNameManager.java`中的大部分工作都是在`setUserName`方法中完成的。在这里，Android 层根据从 React Native 层发送的内容更新视图的文本内容。React 方法不一定会在主 UI 线程上运行，因此我们使用`mainActivity.runOnUiThread`在主 UI 线程准备好时更新视图：

```jsx
public class UserNameManager extends ReactContextBaseJavaModule {
  public UserNameManager(ReactApplicationContext reactApplicationContext) {
    super(reactApplicationContext);
  }
  @Override public String getName() {
    return "UserNameManager";
  }
  @ReactMethod public void setUserName(final String userName) {
    Activity mainActivity = getReactApplicationContext().getCurrentActivity();
    final EditText userNameField = (EditText) mainActivity.findViewById(R.id.userName);
    mainActivity.runOnUiThread(new Runnable() {
      @Override public void run() {
        userNameField.setText(userName);
      }
    });
  }
}
```

1.  要导出`UserNameManager`模块，我们需要编辑`UserNamePackage` Java 类。我们可以通过调用`modules.add`将其导出到 React Native 层，传入一个以`reactContext`为参数的新`UserNameManager`：

```jsx
public class UserNamePackage implements ReactPackage {
  @Override public List < Class << ? extends JavaScriptModule >> createJSModules() {
      return Collections.emptyList();
  }
  @Override public List < ViewManager > createViewManagers(ReactApplicationContext reactContext) {
      return Collections.emptyList();
  }
  @Override public List < NativeModule > createNativeModules(ReactApplicationContext reactContext) {
      List < NativeModule > modules = new ArrayList < > ();
 modules.add(new UserNameManager(reactContext));
      return modules;
  }
}
```

1.  在`MainApplication`的`getPackages`方法中添加`UserNamePackage`：

```jsx
 @Override
 protected List<ReactPackage> getPackages() {
  return Arrays.<ReactPackage>asList(
   new MainReactPackage(),
   new UserNamePackage()
  );
 }
```

1.  现在，我们需要让我们的 React Native UI 渲染一个`TextField`并调用我们的`UserNameManager`本地模块。打开`index.android.js`并从`'react-native'`导入`TextInput`和`NativeModules`模块。

1.  为`UserNameManager`创建一个变量引用：

```jsx
       const UserNameManager = NativeModules.UserNameManager;
```

1.  React Native 应用程序只需要一个`TextInput`来操作`state`对象上的`userName`属性：

```jsx
let state = {
  userName: ''
}

onUserNameChange = (userName) => {
  this.setState({
    userName
  });

  UserNameManager.setUserName(userName);
}

render() {
  return (
    <View style={styles.container}>
      <Text>Embedded RN App</Text>
      <Text>Enter User Name</Text>
      <TextInput style={styles.userNameField}
        onChangeText={this.onUserNameChange}
        value={this.state.userName}
      />
    </View>
  );
}
```

1.  运行应用程序，启动 React Native 嵌入式应用程序，并向文本字段添加文本，您应该看到类似于以下截图所示的内容：

![](img/c3c111f9-4a0b-455c-8784-1e859f69db12.png)

# 工作原理...

为了使我们的 React Native 应用程序更新本机应用程序容器，我们创建了一个本机模块。这是从 JavaScript 通信到本机层的推荐方式。但是，由于我们必须更新本机 UI 组件，因此操作必须在主线程上执行。这是通过获取对`MainActivity`的引用并调用`runOnUiThread`方法来实现的。这是在*步骤 4*的`setUserName`方法中完成的。

# 处理外部 Android 应用程序调用

在本章的前面，我们介绍了如何在 iOS 中处理外部应用程序的调用，在*处理外部 Android 应用程序调用*中。在这个配方中，我们将介绍 Android 中深度链接的相同概念。

# 如何做...

1.  让我们首先在 Android Studio 中打开 React Native Android 项目，并导航到`AndroidManifest.xml`。

1.  对于我们的示例，我们将在`invoked://scheme`下注册我们的应用程序。我们将更新`<activity>`节点如下：

```jsx
<activity
android:name=".MainActivity"
android:label="@string/app_name"
android:configChanges="keyboard|keyboardHidden|orientation|screenSize"
android:windowSoftInputMode="adjustResize"
android:launchMode="singleTask">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

有关此`intent-filter`的工作原理的更多信息，请参阅官方 Android 文档[`developer.android.com/training/app-links/deep-linking`](https://developer.android.com/training/app-links/deep-linking)。

1.  接下来，我们需要创建一个简单的 React Native 应用程序，其 UI 对被调用做出反应。让我们打开`index.android.js`文件。我们将从`'react-native'`的`import`块中导入`Linking`模块：

```jsx
import React from 'react';
import { Platform, Text, Linking } from 'react-native';
```

1.  让我们为 React Native 应用构建`App`类。当组件挂载时，我们将使用一个名为`url`的事件注册一个`Linking`事件监听器。当这个事件发生时，`onAppInvoked`将被触发，更新状态的`status`属性，以及传递给回调函数的事件：

```jsx
export default class App extends React.Component {
  state = {
    status: 'App Running'
  }

  componentWillMount() {
    Linking.addEventListener('url', this.onAppInvoked);
  }

  componentWillUnmount() {
    Linking.removeEventListener('url', this.onAppInvoked);
  }

  onAppInvoked = (event) => {
    this.setState({ status: `App Invoked by ${event.url}` });
  }

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.instructions}>
          App Status:
        </Text>
        <Text style={styles.welcome}>
          {this.state.status}
        </Text>
      </View>
    );
  } 
}
```

1.  运行应用程序并从另一个应用程序调用它将看起来像这样： 

![](img/3c740e41-fd02-4b2a-8be2-32e7a7935dfc.png)

# 工作原理...

在这个教程中，我们通过编辑*步骤 2*中的`AndroidManifest.xml`文件来注册我们的 URL 模式以进行链接。需要注意的一点是将`launchMode`更改为`singleTask`。这可以防止操作系统创建我们的 React 活动的多个实例。如果你想要能够正确捕获随意图传递的数据，这一点非常重要。
