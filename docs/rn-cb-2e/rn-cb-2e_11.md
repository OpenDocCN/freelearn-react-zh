# 第十一章：添加原生功能-第一部分

在这一章中，我们将涵盖以下内容：

+   暴露自定义 iOS 模块

+   渲染自定义 iOS 视图组件

+   暴露自定义 Android 模块

+   渲染自定义 Android 视图组件

# 介绍

React Native 开发的核心原则之一是使用 JavaScript 构建真正的原生移动应用程序。为了实现这一点，许多原生 API 和 UI 组件通过抽象层暴露，并通过 React Native 桥访问。虽然 React Native 和 Expo 团队继续改进和扩展已经存在的令人印象深刻的 API，但通过原生 API，我们可以访问其他方式无法获得的功能，比如振动、联系人以及原生警报和提示。

通过暴露原生视图组件，我们能够利用设备提供的所有渲染性能，因为我们不像混合应用程序那样通过 WebView 进行渲染。这给用户提供了原生的外观和感觉，可以适应用户运行应用程序的平台。使用 React Native，我们已经能够渲染许多原生视图组件，包括地图、列表、输入字段、工具栏和选择器。

虽然 React Native 带有许多内置的原生模块和视图组件，但有时我们需要一些自定义功能，利用原生应用程序层，这些功能并不是开箱即用的。幸运的是，有一个非常丰富的开源社区支持 React Native，不仅为库本身做出贡献，还发布了一些导出常见原生模块和视图组件的库。如果找不到满足需求的第一方或第三方库，您总是可以自己构建。

在这一章中，我们将涵盖一些关于在两个平台上暴露自定义原生功能的方法，无论是 API 还是视图组件。

在这些配方中，我们将使用原生部分中的大量生成的代码。本章中提供的代码块将像以前的章节一样，继续显示特定步骤中使用的所有代码，无论是我们添加的还是生成的，除非另有说明。这旨在减轻理解代码片段的上下文的负担，并在需要进一步解释这些生成的代码片段时促进讨论。

# 暴露自定义 iOS 模块

当您开始开发更有趣和复杂的 React Native 应用程序时，可能会达到一个只能在本地层执行某些代码（或显着改进）的点。这允许在本地层执行比 JavaScript 更快的数据处理，并访问某些本地功能，否则这些功能不会暴露，例如文件 I/O，或者利用 React Native 应用程序中其他应用程序或库中的现有本地代码。

这个示例将引导您执行一些本地 Objective-C 或 Swift 代码并与 JavaScript 层进行通信的过程。我们将构建一个本地的`HelloManager`模块来向用户问候。我们还将展示如何执行本地的 Objective-C 和 Swift 代码，传入参数，并展示与 UI（或 JavaScript）层进行多种通信的方式。

# 准备工作

对于这个示例，我们需要一个新的空的纯 React Native 应用程序。让我们称之为`NativeModuleApp`。

在这个示例中，我们还将使用`react-native-button`库。这个库将允许我们使用比 React Native 对应组件更复杂的`Button`组件。它可以使用`npm`进行安装：

```jsx
npm install react-native-button --save
```

或者可以使用`yarn`进行安装：

```jsx
yarn add react-native-button
```

# 如何做到...

1.  我们将从在 Xcode 中打开 iOS 项目开始。项目文件的文件扩展名为`.xcodeproj`，位于项目根目录的`ios/`目录中。在我们的情况下，文件将被称为`NativeModuleApp.xcodeproj`。

1.  我们需要通过选择并右键单击与项目名称匹配的组/文件夹来创建一个新文件，然后点击 New File...，如下所示：

![](img/1776e333-b5bc-43a0-ab3d-71974adfe908.png)

1.  我们将制作一个 Cocoa 类，所以选择 Cocoa Class 并点击 Next。

1.  我们将使用`HelloManager`作为类名，并将子类设置为 NSObject，语言设置为 Objective-C，如下所示：

![](img/708e04f5-1712-4fa8-9e96-aa63650ed06c.png)

1.  点击 Next 后，我们将被提示选择新类的目录。我们希望将其保存到`NativeModuleApp`目录中。

1.  创建这个新的 Cocoa 类已经向项目中添加了两个新文件：一个头文件（`HelloManager.h`）和一个实现文件（`HelloManager.m`）。

1.  在头文件（`HelloManager.h`）中，您应该看到一些生成的代码来实现新的`HelloManager`协议。我们还需要导入 React 的`RCTBridgeModule`库。文件最终应该看起来像这样：

```jsx
#import <Foundation/Foundation.h>
#import <React/RCTBridgeModule.h>

@interface HelloManager : NSObject <RCTBridgeModule>

@end
```

1.  实现文件（`HelloManager.m`）包含了我们模块的功能。为了让我们的 React Native 应用能够从 JavaScript 层访问这个模块，我们需要在 React Bridge 中注册它。这是通过在`@implementation`标签后添加`RCT_EXPORT_MODULE()`来完成的。还要注意，头文件也应该已经被导入到这个文件中：

```jsx
#import "HelloManager.h"

@implementation HelloManager
RCT_EXPORT_MODULE();

@end
```

1.  我们需要添加我们将要导出到 React Native 应用的函数。我们将创建一个`greetUser`方法，它将接受两个参数，`name`和`isAdmin`。这些参数将用于使用字符串连接创建问候消息，然后通过`callback`发送回 JavaScript 层：

```jsx
#import "HelloManager.h"

@implementation HelloManager
RCT_EXPORT_MODULE();

RCT_EXPORT_METHOD(
 greetUser: (NSString *)name isAdmin:(BOOL *)isAdmin callback: (RCTResponseSenderBlock) callback
) {
 NSString *greeting =
 [NSString stringWithFormat:
 @"Welcome %@, you %@ an administrator.", name, isAdmin ? @"are" : @"are not"];

 callback(@[greeting]);
}

@end
```

1.  我们准备切换到 JavaScript 层，这将有一个 UI，将调用我们刚刚创建的原生`HelloManager greetUser`方法，然后显示其输出。幸运的是，React Native 桥为我们完成了所有繁重的工作，并留下了一个简单易用的 JavaScript 对象，模仿了`NativeModules` API。在这个例子中，我们将使用`TextInput`和`Switch`来为原生模块方法提供`name`和`isAdmin`值。让我们从`App.js`中开始导入：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  NativeModules,
  TextInput,
  Switch
} from 'react-native';
import Button from 'react-native-button';
```

1.  我们可以使用我们导入的`NativeModules`组件来从原生层获取我们创建的`HelloManager`协议：

```jsx
const HelloManager = NativeModules.HelloManager; 
```

1.  让我们创建`App`组件并定义初始的`state`对象。我们将添加一个`greetingMessage`属性来保存从原生模块接收到的消息，`userName`来存储输入的用户名，以及一个`isAdmin`布尔值来表示用户是否是管理员：

```jsx
export default class App extends Component {
  state = {
    greetingMessage: null,
    userName: null,
    isAdmin: false
  }
  // Defined on following steps
}
```

1.  我们准备开始构建`render`方法。首先，我们需要一个`TextInput`组件来从用户那里获取用户名，以及一个`Switch`组件来切换`isAdmin`状态：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.label}>
          Enter User Name
        </Text>
        <TextInput
          ref="userName"
          autoCorrect={false}
          style={styles.inputField}
          placeholder="User Name"
          onChangeText={(text) => this.setState({ userName: text }) }
        />
        <Text style={styles.label}>
          Admin
        </Text>
        <Switch style={styles.radio}
          value={this.state.isAdmin}
          onValueChange={(value) =>
            this.setState({ isAdmin: value })
          }
        />

        // Continued below
      </View>
    );
  }
```

1.  UI 还需要`Button`来提交回调到原生模块，以及一个`Text`组件来显示从原生模块返回的消息：

```jsx
  render() {
    return (
      // Defined above.
 <Button
 disabled={!this.state.userName}
 style={[
 styles.buttonStyle,
 !this.state.userName ? styles.disabled : null
 ]}
 onPress={this.greetUser}
 >
 Greet (callback)
 </Button>
 <Text style={styles.label}>
 Response:
 </Text>
 <Text style={styles.message}>
 {this.state.greetingMessage}
 </Text>
      </View>
    );
  }
```

1.  随着 UI 渲染必要的组件，我们准备将`Button`的`onPress`处理程序连接到本地层的调用。这个函数将`displayResults`类方法作为第三个参数传递，这是本地`greetUser`函数要使用的回调。我们将在下一步中定义`displayResults`：

```jsx
  greetUser = () => {
    HelloManager.greetUser(
      this.state.userName,
      this.state.isAdmin,
      this.displayResults
    );
  }
```

1.  `displayResults`需要做两件事：使用与组件关联的`refs`来`blur` `TextInput`，并将`greetingMessage`设置为从本地模块返回的`results`：

```jsx
  displayResults = (results) => {
    this.refs.userName.blur();
    this.setState({ greetingMessage: results });
  }
```

1.  最后一步是向布局添加样式并设计应用程序的样式：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  inputField:{
    padding: 20,
    fontSize: 30
  },
  label: {
    fontSize: 18,
    marginTop: 18,
    textAlign: 'center',
  },
  radio: {
    marginBottom: 20
  },
  buttonStyle: {
    padding: 20,
    backgroundColor: '#1DA1F2',
    color: '#fff',
    fontSize: 18
  },
  message: {
    fontSize: 22,
    marginLeft: 50,
    marginRight: 50,
  },
  disabled: {
    backgroundColor: '#3C3C3C'
  }
});
```

1.  我们现在有一个可以直接与本地 iOS 层通信的工作 React Native 应用程序：

![](img/b60dc2de-a7fd-4c7c-a1fd-d757beaa4cb9.png)

# 工作原理...

我们在这个教程中构建的应用程序将成为本章后续许多教程的基础。这也是 Facebook 用来实现许多捆绑的 React Native API 的方法。

在未来，有几个重要的概念需要牢记。我们想要在 JavaScript 层中使用的任何本地模块类都必须扩展`RCTBridgeModule`，因为它包含了将我们的类注册到 React Native 桥上的功能。我们使用`RCT_EXPORT_MODULE`方法调用注册我们的类，一旦模块被注册，就会注册模块上的方法。注册模块以及其相应的方法和属性是允许我们从 JavaScript 层与本地层进行接口的。

当按下按钮时，将执行`greetUser`方法。这个函数反过来调用`HelloManager.greetUser`，传递`state`中的`userName`和`isAdmin`属性以及`displayResults`函数作为回调。`displayResults`设置`state`上的新`greetingMessage`，导致 UI 刷新并显示消息。

# 另请参阅

+   本文解释了 React Native 应用程序如何启动：[`levelup.gitconnected.com/wait-what-happens-when-my-react-native-application-starts-an-in-depth-look-inside-react-native-5f306ef3250f`](https://levelup.gitconnected.com/wait-what-happens-when-my-react-native-application-starts-an-in-depth-look-inside-react-native-5f306ef3250f)

+   深入了解 React Native 事件的实际工作原理：[`levelup.gitconnected.com/react-native-events-in-gory-details-what-happens-on-the-way-to-listeners-2cee6c55940c`](https://levelup.gitconnected.com/react-native-events-in-gory-details-what-happens-on-the-way-to-listeners-2cee6c55940c)

# 渲染自定义 iOS 视图组件

在我们的 React Native 应用程序中，在本地层执行代码时利用设备的处理能力非常重要，同样重要的是利用其渲染能力来显示本地 UI 组件。 React Native 可以在应用程序中呈现任何`UIView`的 UI 组件实现。 这些组件可以是列表、表单字段、表格、图形等等。

对于这个教程，我们将创建一个名为`NativeUIComponent`的 React Native 应用程序。

在这个教程中，我们将采用原生的`UIButton`并将其公开为 React Native 视图组件。 您将能够设置按钮标签并附加一个处理程序以在按下按钮时执行。

# 如何做...

1.  让我们从在 Xcode 中打开 iOS 项目开始。 项目文件位于项目的`ios/`目录中，应该被称为`NativeUIComponent.xcodeproj`。

1.  选择并右键单击与项目名称匹配的组，并单击“新建文件...”：

![](img/0b0b6694-a324-4ad7-8931-abab7e95d2f0.png)

1.  我们将创建一个 Cocoa 类，所以选择`Cocoa Class`并单击`下一步`。

1.  我们将创建一个按钮，所以让我们将类命名为`Button`，将`Subclass of`设置为`UIView`，将`Language`设置为`Objective-C`：

![](img/cbed707c-06cb-4418-b991-ac0cb10187cd.png)

1.  点击“下一步”后，我们将被提示选择新类的目录。 我们要将其保存到`NativeUIComponent`目录以创建该类。

1.  我们还需要一个`ButtonViewManager`类。 您可以将步骤 2 到 5 重复使用`ButtonViewManager`作为类名和`RCTViewManager`作为子类。

1.  首先，我们将实现我们的`Button` UI 类。 在头文件（`Button.h`）中，我们将从 React 中导入`RCTComponent.h`并添加一个`onTap`属性来连接我们的点击事件：

```jsx
#import <UIKit/UIKit.h>
#import "React/RCTComponent.h"

@interface Button : UIView

@property (nonatomic, copy) RCTBubblingEventBlock onTap;

@end
```

1.  让我们在实现文件（`Button.m`）上工作。 我们将首先创建我们的`UIButton`实例和将保存按钮标签的字符串的引用：

```jsx
#import "Button.h"
#import "React/UIView+React.h"

@implementation Button {
  UIButton *_button;
  NSString *_buttonText;
}

// Defined in following steps
```

1.  桥梁将寻找`buttonText`属性的 setter。 这是我们将设置`UIButton`实例标题字段的地方：

```jsx
-(void) setButtonText:(NSString *)buttonText {
  NSLog(@"Set text %@", buttonText);
  _buttonText = buttonText;
  if(_button) {
    [_button setTitle:
     buttonText forState:UIControlStateNormal];
    [_button sizeToFit];
  }
}
```

1.  我们的`Button`将从 React Native 应用程序接受一个`onTap`事件处理程序。我们需要通过动作选择器将其连接到我们的`UIButton`实例：

```jsx
- (IBAction)onButtonTap:(id)sender {
  self.onTap(@{});
}
```

1.  我们需要实例化`UIButton`并将其放置在 React `Subview`中。我们将称这个方法为`layoutSubviews`：

```jsx
-(void) layoutSubviews {
  [super layoutSubviews];
  if( _button == nil) {
    _button =
    [UIButton buttonWithType:UIButtonTypeRoundedRect];
    [_button addTarget:self action:@selector(onButtonTap:)
      forControlEvents:UIControlEventTouchUpInside];
    [_button setTitle:
     _buttonText forState:UIControlStateNormal];
    [_button sizeToFit];
    [self insertSubview:_button atIndex:0];
  }
}
```

1.  让我们在`ButtonViewManager.h`头文件中导入 React `RCTViewManager`：

```jsx
#import "React/RCTViewManager.h"

@interface ButtonViewManager : RCTViewManager

@end
```

1.  现在我们需要实现我们的`ButtonViewManager`，它将与我们的 React Native 应用程序进行交互。让我们在实现文件（`ButtonViewManager.m`）上工作，使其发生。我们使用`RCT_EXPORT_VIEW_PROPERTY`来传递`buttonText`属性和`onTap`方法到 React Native 层：

```jsx
#import "ButtonViewManager.h"
#import "Button.h"
#import "React/UIView+React.h"

@implementation ButtonViewManager
RCT_EXPORT_MODULE()

- (UIView *)view {
  Button *button = [[Button alloc] init];
  return button;
}

RCT_EXPORT_VIEW_PROPERTY(buttonText, NSString);
RCT_EXPORT_VIEW_PROPERTY(onTap, RCTBubblingEventBlock);

@end
```

1.  我们准备切换到 React Native 层。我们需要一个自定义的`Button`组件，所以让我们在项目的根目录下创建一个新的`components`文件夹，并在其中创建一个新的`Button.js`文件。我们还需要从 React Native 中导入`requireNativeComponent`组件，以便与我们的原生 UI 组件进行交互：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View
} from 'react-native';
import Button from './components/Button';
```

1.  `Button`组件将通过`requireNativeComponent` React Native 助手获取我们之前创建的原生`Button`模块。调用以字符串作为组件在 React Native 层中的名称作为第一个参数，并且第二个参数将`Button`组件在文件中，有效地将两者连接在一起：

```jsx
export default class Button extends Component {
  render() {
    return <ButtonView {...this.properties} />;
  }
}

const ButtonView = requireNativeComponent('ButtonView', Button);
```

1.  我们准备在项目的根目录下的`App.js`文件中构建主要的`App`组件。我们将从导入开始，其中将包括我们在最后两个步骤中创建的`Button`组件：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View
} from 'react-native';
import Button from './components/Button';
```

1.  让我们定义`App`组件和初始的`state`对象。`count`属性将跟踪`Button`组件被按下的次数：

```jsx
export default class App extends Component {
 state = {
  count: 0
 }
 // Defined on following steps
}
```

1.  我们准备好定义`render`方法，它将由`Button`组件和用于显示当前按钮按下计数的`Text`元素组成：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Button buttonText="Click Me!"
        onTap={this.handleButtonTap}
        style={styles.button}
      />
        <Text>Button Pressed Count: {this.state.count}</Text>
      </View>
    );
  }
```

1.  您可能还记得我们创建的`Button`组件具有一个`onTap`属性，它接受一个回调函数。在这种情况下，我们将使用此函数来增加`state`上的计数器：

```jsx
  handleButtonTap = () => {
    this.setState({
      count: this.state.count + 1
    });
  }
```

1.  让我们用一些基本的样式结束这个教程：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  button: {
    height: 40,
    width: 80
  }
});
```

1.  应用程序完成了！当按下按钮时，将执行传递给`onTap`的函数，将计数器增加一：

![](img/c4d79bfc-c22f-48dd-95d7-19221383b3e1.png)

# 工作原理...

在这个配方中，我们暴露了一个基本的原生 UI 组件。这是创建 React Native 内置的所有 UI 组件（例如`Slider`、`Picker`和`ListView`）的方法。

创建 UI 组件最重要的要求是，你的`ViewManager`要扩展`RCTViewManager`并返回一个`UIView`的实例。在我们的情况下，我们用 React 特定的`UIView`扩展来包装`UIButton`，这样可以提高我们布局和样式组件的能力。

下一个重要因素是发送属性和对组件事件做出反应。在第 13 步中，我们使用了 React Native 提供的`RCT_EXPORT_VIEW_PROPERTY`方法来注册来自 JavaScript 层的`buttonText`和`onTap`视图属性，这些属性将传递给`Button`组件。然后创建并返回`Button`组件以在 JavaScript 层中使用：

```jsx
- (UIView *)view {
  Button *button = [[Button alloc] init];
  return button;
}
```

# 暴露自定义的 Android 模块

通常，你会发现 React Native 应用程序需要与原生 iOS 和 Android 代码进行接口。在讨论了集成原生 iOS 模块之后，现在是时候覆盖 Android 中的等效配方了。

这个配方将带领我们编写我们的第一个 Android 原生模块。我们将创建一个`HelloManager`原生模块，其中包含一个`greetUser`方法，该方法接受`name`和一个`isAdmin`布尔值作为参数，然后返回一个我们将在 UI 中显示的问候消息。

# 准备工作

对于这个配方，我们需要创建另一个纯 React Native 应用程序。我们也将这个项目命名为`NativeModuleApp`。

我们还将再次使用`react-native-button`库，可以使用`npm`安装：

```jsx
npm install react-native-button --save
```

或者，也可以使用`yarn`进行安装：

```jsx
yarn add react-native-button
```

# 如何做...

1.  我们将首先在 Android Studio 中打开新项目的 Android 代码。从 Android Studio 的欢迎屏幕，你可以选择打开现有的 Android Studio 项目，然后选择项目文件夹内的`android`目录。

1.  项目加载完成后，让我们在 Android Studio 左侧打开项目资源管理器（即目录树），并展开包结构以找到 Java 源文件，它应该位于`app/java/com.nativemoduleapp`中。该文件夹应该已经有两个`.java`文件，`MainActivity`和`MainApplication`：

![](img/f26cf594-e058-4d2d-a1d4-c4fdfca09273.png)

1.  右键单击 com.nativemoduleapp 包，选择 New | Java Class，并命名类为`HelloManager`。还要确保将 Kind 字段设置为 Class: ![](img/013c754f-9fee-45fe-bc62-489f19635a05.png)

1.  我们还需要在同一个目录中创建一个`HelloPackage`类。您可以重复步骤 2 和 3 来创建这个类，只需应用新名称并保持 Kind 字段设置为 Class。

1.  让我们从实现我们的`HelloManager`本机模块开始。我们将从`package`名称和我们在此文件中需要的依赖项开始：

```jsx
package com.nativemoduleapp;

import com.facebook.react.bridge.Callback;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactMethod;
```

1.  `ReactContextBaseJavaModule`是所有 React Native 模块的基类，因此我们将创建`HelloManager`类作为其子类。我们还需要定义一个`getName`方法，该方法用于向 React Native 桥注册本机模块。这是与 iOS 本机模块实现的一个区别，因为那些是通过类名定义的：

```jsx
public class HelloManager extends ReactContextBaseJavaModule {
  public HelloManager(ReactApplicationContext reactContext) {
    super(reactContext);
  }

  @Override
  public String getName() {
    return "HelloManager";
  }
}
```

1.  现在我们已经设置好了`HelloManager`本机模块，是时候向其中添加`greetUser`方法了，该方法将期望作为参数`name`、`isAdmin`和将执行以将消息发送到 React Native 层的回调：

```jsx
public class HelloManager extends ReactContextBaseJavaModule {
  // Defined in previous steps  

 @ReactMethod
 public void greetUser(String name, Boolean isAdmin, Callback callback) {
 System.out.println("User Name: " + name + ", Administrator: " + (isAdmin ? "Yes" : "No"));
 String greeting = "Welcome " + name + ", you " + (isAdmin ? "are" : "are not") + " an administrator";

 callback.invoke(greeting);
 }
}
```

1.  Android 独有的另一个步骤是必须将本机模块注册到应用程序中，这是一个两步过程。第一步是将我们的`HelloManager`模块添加到之前创建的`HelloPackage`类中。我们将从`HelloPackage.java`的依赖项开始：

```jsx
package com.nativemoduleapp;

import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
```

1.  `HelloPackage`的实现只是遵循官方文档提供的模式（[`facebook.github.io/react-native/docs/native-modules-android.html`](https://facebook.github.io/react-native/docs/native-modules-android.html)）。这里最重要的部分是对`modules.add`的调用，其中传入了带有`reactContext`作为参数的`HelloManager`的新实例：

```jsx
public class HelloPackage implements ReactPackage {

  @Override
  public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    return Collections.emptyList();
  }

  @Override
  public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
    List<NativeModule> modules = new ArrayList<>();
    modules.add(new HelloManager(reactContext));

    return modules;
  }
}
```

1.  在将本机模块注册到 React Native 应用程序的第二步是将`HelloPackage`添加到`MainApplication`模块中。这里的大部分代码都是由 React Native 引导过程生成的。`getPackages`方法需要更新，以将`new MainReactPackage()`和`new HelloPackage()`作为传递给`Arrays.asList`的参数：

```jsx
package com.nativemoduleapp;

import android.app.Application;

import com.facebook.react.ReactApplication;
import com.facebook.react.ReactNativeHost;
import com.facebook.react.ReactPackage;
import com.facebook.react.shell.MainReactPackage;
import com.facebook.soloader.SoLoader;

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
 return Arrays.asList(
 new MainReactPackage(),
 new HelloPackage()
 );
 }

    @Override
    protected String getJSMainModuleName() {
      return "index";
    }
  };

  @Override
  public ReactNativeHost getReactNativeHost() {
    return mReactNativeHost;
  }

  @Override
  public void onCreate() {
    super.onCreate();
    SoLoader.init(this, /* native exopackage */ false);
  }
}
```

1.  我们在这个配方的 Java 部分已经完成了。 我们需要构建我们的 UI，这将调用本机的`HelloManager greetUser`方法并显示其输出。 在这个例子中，我们将使用`TextInput`和`Switch`来提供本机模块方法的`name`和`isAdmin`值。 这与我们在*暴露自定义 iOS 模块*配方中在 iOS 上实现的功能相同。 让我们开始构建`App.js`，首先是我们需要的依赖项：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  NativeModules,
  TextInput,
  Switch,
  DeviceEventEmitter
} from 'react-native';
import Button from 'react-native-button';
```

1.  我们需要引用存储在导入的`NativeModules`组件上的`HelloManager`对象：

```jsx
const { HelloManager } = NativeModules;
```

1.  让我们创建`App`类和初始`state`：

```jsx
export default class App extends Component {
  state = {
    userName: null,
    greetingMessage: null,
    isAdmin: false
  }
}
```

1.  我们准备定义组件的`render`函数。 这段代码将不会被详细描述，因为它基本上与本章开头的*暴露自定义 iOS 模块*配方中定义的`render`函数相同：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.label}>
          Enter User Name
        </Text>
        <TextInput
          ref="userName"
          autoCorrect={false}
          style={styles.inputField}
          placeholder="User Name"
          onChangeText={(text) => this.setState({ userName: text })
          }
        />
        <Text style={styles.label}>
          Admin
        </Text>
        <Switch
          style={styles.radio}
          onValueChange={
            value => this.setState({ isAdmin: value })
          }
          value={this.state.isAdmin}
        />
       <Button
          disabled={!this.state.userName}
          style={[
            styles.buttonStyle,
            !this.state.userName ? styles.disabled : null
          ]}
          onPress={this.greetUser}
        >
          Greet
        </Button>
        <Text style={styles.label}>
          Response:
        </Text>
        <Text style={styles.message}>
          {this.state.greetingMessage}
        </Text>
      </View>
    );
  }
```

1.  随着 UI 渲染必要的组件，我们现在需要将`Button`的`onPress`处理程序连接起来，通过`HelloManager.greetUser`进行本机调用：

```jsx
  updateGreetingMessage = (result) => {
    this.setState({
      greetingMessage: result
    });
  }

  greetUser = () => {
    this.refs.userName.blur();
    HelloManager.greetUser(
      this.state.userName,
      this.state.isAdmin,
      this.updateGreetingMessage
    );
  }
```

1.  我们将添加样式来布局和设计应用程序。 再次强调，这些样式与本章开头的*暴露自定义 iOS 模块*配方中使用的样式相同：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  inputField:{
    padding: 20,
    fontSize: 30,
    width: 200
  },
  label: {
    fontSize: 18,
    marginTop: 18,
    textAlign: 'center',
  },
  radio: {
    marginBottom: 20
  },
  buttonStyle: {
    padding: 20,
    backgroundColor: '#1DA1F2',
    color: '#fff',
    fontSize: 18
  },
  message: {
    fontSize: 22,
    marginLeft: 50,
    marginRight: 50,
  },
  disabled: {
    backgroundColor: '#3C3C3C'
  }
});
```

1.  最终的应用程序应该类似于以下截图：

![](img/7eb97b69-1929-45c0-adb7-a2d689e5b051.png)

# 它是如何工作的...

这个配方涵盖了我们将在未来的配方中添加本机 Android 模块的大部分基础知识。 所有本机模块类都需要扩展`ReactContextBaseJavaModule`，实现构造函数，并定义`getName`方法。 所有应该暴露给 React Native 层的方法都需要有`@ReactMethod`注解。 创建 React Native Android 本机模块的开销比 iOS 更大，因为您还必须将模块包装在实现`ReactPackage`的类中（在这个配方中，那就是`HelloPackage`模块），并将包注册到 React Native 项目中。 这是在步骤 7 和 8 中完成的。

在配方的 JavaScript 部分，当用户按下`Button`组件时，将执行`greetUser`函数。 这反过来又调用`HelloManager.greetUser`，并传递`state`中的`userName`和`isAdmin`属性以及`updateGreetingMessage`方法作为回调。 `updateGreetingMessage`在`state`上设置新的`greetingMessage`，导致 UI 刷新并显示消息。

# 渲染自定义 Android 视图组件

迄今为止，React Native 之所以如此受欢迎的一个原因是它能够渲染真正的本机 UI 组件。在 Android 上使用本机 UI 组件，我们不仅能够利用 GPU 渲染能力，还能获得本机组件的本机外观和感觉，包括本机字体、颜色和动画。在 Android 上，Web 和混合应用程序使用 CSS polyfills 来模拟本机动画，但在 React Native 中，我们可以得到真正的东西。

我们需要一个新的纯 React Native 应用程序来完成这个示例。让我们将其命名为`NativeUIComponent`。在这个示例中，我们将采用本机`Button`并将其公开为 React Native 视图组件。

# 如何做...

1.  让我们从在 Android Studio 中打开 Android 项目开始。在 Android Studio 欢迎屏幕上，选择打开现有的 Android Studio 项目，并打开项目的`android`目录。

1.  打开项目资源管理器，并展开包结构，直到您可以看到 Java 源文件（例如，`app/java/com.nativeuicomponent`）：

![](img/ea40d86e-ce4c-419a-a7fc-6ded42199f2c.png)

1.  右键单击包，然后选择 New | Java Class。使用`ButtonViewManager`作为类名，并将 Kind 字段设置为 Class。

1.  使用相同的方法也创建一个`ButtonPackage`类。

1.  让我们开始实现我们的`ButtonViewManager`类，它必须是`SimpleViewManager<View>`的子类。我们将从导入开始，并定义类本身：

```jsx
package com.nativeuicomponent;

import android.view.View;
import android.widget.Button;

import com.facebook.react.bridge.Arguments;
import com.facebook.react.bridge.ReactContext;
import com.facebook.react.bridge.WritableMap;
import com.facebook.react.uimanager.SimpleViewManager;
import com.facebook.react.uimanager.ThemedReactContext;
import com.facebook.react.uimanager.annotations.ReactProp;
import com.facebook.react.uimanager.events.RCTEventEmitter;

public class ButtonViewManager extends SimpleViewManager<Button> implements View.OnClickListener {
  // Defined on following steps
}
```

文件类名`ButtonViewManager`遵循 Android 命名约定，将后缀`ViewManager`添加到任何`View`组件。

1.  让我们从`getName`方法开始类定义，该方法返回我们为组件分配的字符串名称，在本例中为`ButtonView`：

```jsx
public class ButtonViewManager extends SimpleViewManager<Button> implements View.OnClickListener{
 @Override
 public String getName() {
 return "ButtonView";
 }

  // Defined on following steps.
}
```

1.  `createViewInstance`方法是必需的，用于定义 React 应该如何初始化模块：

```jsx
  @Override
  protected Button createViewInstance(ThemedReactContext reactContext) {
    Button button = new Button(reactContext);
    button.setOnClickListener(this);
    return button;
  }
```

1.  `setButtonText`将从 React Native 元素的属性中使用，以设置按钮上的文本：

```jsx
  @ReactProp(name = "buttonText")
  public void setButtonText(Button button, String buttonText) {
    button.setText(buttonText);
  }
```

1.  `onClick`方法定义了按钮按下时会发生什么。此方法使用`RCTEventEmitter`来处理从 React Native 层接收事件：

```jsx
  @Override
  public void onClick(View v) {
    WritableMap map = Arguments.createMap();
    ReactContext reactContext = (ReactContext) v.getContext();
    reactContext.getJSModule(RCTEventEmitter.class).receiveEvent(v.getId(), "topChange", map);
  }
```

1.  就像在上一个示例中一样，我们需要将`ButtonViewManager`添加到`ButtonPackage`；但是，这次，我们将其定义为`ViewManager`而不是`NativeModule`：

```jsx
package com.nativeuicomponent;

import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class ButtonPackage implements ReactPackage {
  @Override
  public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    return Arrays.<ViewManager>asList(new ButtonViewManager());
  }

  @Override
  public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
    return Collections.emptyList();
  }
}
```

1.  Java 层的最后一步是将`ButtonPackage`添加到`MainApplication`。`MainApplication.java`中已经有相当多的样板代码，我们只需要更改`getPackages`方法：

```jsx
    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
        new MainReactPackage(),
        new ButtonPackage()
      );
    }
```

1.  切换到 JavaScript 层，让我们构建我们的 React Native 应用程序。首先，在项目的根目录中的`components/Button.js`中创建一个新的`Button`组件。这是原生按钮将存在于应用程序的 React Native 层内。`render`方法使用原生按钮作为`ButtonView`，我们将在下一步中定义：

```jsx
import React, { Component } from 'react';
import { requireNativeComponent, View } from 'react-native';

export default class Button extends Component {
  onChange = (event) => {
    if (this.properties.onTap) {
      this.properties.onTap(event.nativeEvent.message);
    }
  }

  render() {
    return(
      <ButtonView
        {...this.properties}
        onChange={this.onChange}
      />
    );
  }
}
```

1.  我们可以使用`requireNativeComponent`助手将原生按钮创建为 React Native 组件，它接受三个参数：字符串`ButtonView`来定义组件名称，上一步中定义的`Button`组件，以及选项对象。有关此对象的更多信息，请参阅本教程末尾的*它是如何工作的...*部分：

```jsx
const ButtonView = requireNativeComponent(
  'ButtonView',
  Button, {
    nativeOnly: {
      onChange: true
    }
  }
);
```

1.  我们准备好定义`App`类。让我们从依赖项开始，包括先前创建的`Button`组件：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View
} from 'react-native';

import Button from './components/Button';
```

1.  本教程中的`App`组件本质上与本章前面的*渲染自定义 iOS 视图组件*教程相同。当按下`Button`组件时，自定义的`onTap`属性被触发，将`1`添加到`state`的`count`属性中。

```jsx
export default class App extends Component {
  state = {
    count: 0
  }

  onButtonTap = () => {
    this.setState({
      count : this.state.count + 1
    });
  }

  render() {
    return (
      <View style={styles.container}>
        <Button buttonText="Press Me!"
          onTap={this.onButtonTap}
          style={styles.button}
        />
        <Text>
          Button Pressed Count: {this.state.count}
        </Text>
      </View>
    );
  }
}
```

1.  让我们为布局添加一些样式，调整应用的 UI 大小：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  button: {
    height: 40,
    width: 150
  }
});
```

1.  最终的应用程序应该类似于以下截图：

![](img/c356c27a-7bdc-4589-ba9e-4c510bdf34db.png)

# 它是如何工作的...

当定义原生视图时，就像我们在`ButtonViewManager`类中所做的那样，它必须扩展`SimpleViewManager`并呈现一个扩展`View`的类型。在我们的教程中，我们呈现了一个`Button`视图，并使用了`@ReactProp`注释来定义属性。当我们需要与 JavaScript 层通信时，我们从原生组件触发一个事件，这是我们在本教程的*步骤 9*中实现的。

在*步骤 12*中，我们创建了一个`onChange`监听器，它将执行从 Android 层传递的事件处理程序（`event.nativeEvent.message`）。

关于在*步骤 13*中使用`nativeOnly`选项，来自 React Native 文档：

有时您会有一些特殊属性，您需要为原生组件公开，但实际上不希望它们成为关联的 React 组件 API 的一部分。例如，`Switch`具有用于原始原生事件的自定义`onChange`处理程序，并公开一个`onValueChange`处理程序属性，该属性仅使用布尔值调用，而不是原始事件。由于您不希望这些仅限于原生的属性成为 API 的一部分，因此您不希望将它们放在`propTypes`中，但如果不这样做，就会出错。解决方案很简单，只需通过`nativeOnly`选项调用它们。
