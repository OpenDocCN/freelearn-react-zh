# 第六章：向您的应用程序添加基本动画

在本章中，我们将涵盖以下教程：

+   创建简单动画

+   运行多个动画

+   创建动画通知

+   展开和折叠容器

+   创建带有加载动画的按钮

# 介绍

为了提供良好的用户体验，我们可能希望添加一些动画来引导用户的注意力，突出特定的操作，或者只是为我们的应用程序增添独特的风格。

正在进行一个倡议，将所有处理从 JavaScript 移至本地端。在撰写本文时（React Native 版本 0.58），我们可以选择使用本地驱动程序在本地世界中运行所有这些计算。不幸的是，这不能用于所有动画，特别是与布局相关的动画，比如 flexbox 属性。在文档中阅读有关使用本地动画时的注意事项的更多信息[`facebook.github.io/react-native/docs/animations#caveats`](http://facebook.github.io/react-native/docs/animations#caveats)。

本章中的所有教程都使用 JavaScript 实现。React Native 团队承诺在将所有处理移至本地端时使用相同的 API，因此我们不需要担心现有 API 的变化。

# 创建简单动画

在这个教程中，我们将学习动画的基础知识。我们将使用一张图片来创建一个简单的线性移动，从屏幕的右侧移动到左侧。

# 准备工作

为了完成这个教程，我们需要创建一个空的应用程序。让我们称之为`simple-animation`。

我们将使用一个云的 PNG 图像来制作这个教程。您可以在 GitHub 上托管的教程存储库中找到该图像[`github.com/warlyware/react-native-cookbook/tree/master/chapter-6/simple-animation/assets/images`](https://github.com/warlyware/react-native-cookbook/tree/master/chapter-6/simple-animation/assets/images)。将图像放在`/assets/images`文件夹中以供应用程序使用。

# 如何做...

1.  让我们从打开`App.js`并导入`App`类的依赖项开始。`Animated`类将负责创建动画的值。它提供了一些准备好可以进行动画处理的组件，还提供了几种方法和辅助程序来运行平滑的动画。

`Easing`类提供了几种辅助方法，用于计算运动（如`linear`和`quadratic`）和预定义动画（如`bounce`、`ease`和`elastic`）。我们将使用`Dimensions`类来获取当前设备尺寸，以便在动画初始化时知道在哪里放置元素：

```jsx
import React, { Component } from 'react';
import {
  Animated,
  Easing,
  Dimensions,
  StyleSheet,
  View,
} from 'react-native';
```

1.  我们还将初始化一些我们在应用程序中需要的常量。在这种情况下，我们将获取设备尺寸，设置图像的大小，并`require`我们将要进行动画处理的图像：

```jsx
const { width, height } = Dimensions.get('window');
const cloudImage = require('./assets/images/cloud.png');
const imageHeight = 200;
const imageWidth = 300;
```

1.  现在，让我们创建`App`组件。我们将使用组件生命周期系统中的两种方法。如果您对这个概念不熟悉，请查看相关的 React 文档（[`reactjs.cn/react/docs/component-specs.html`](http://reactjs.cn/react/docs/component-specs.html)）。这个页面还有一个关于生命周期钩子如何工作的非常好的教程：

```jsx
export default class App extends Component { 
  componentWillMount() { 
    // Defined on step 4 
  } 

  componentDidMount() { 
    // Defined on step 7 
  } 

  startAnimation () { 
    // Defined on step 5 
  } 

  render() { 
    // Defined on step 6 
  } 
} 

const styles = StyleSheet.create({ 
  // Defined on step 8 
}); 
```

1.  为了创建动画，我们需要定义一个标准值来驱动动画。`Animated.Value`是一个处理每一帧动画值的类。我们需要在组件创建时创建这个类的实例。在这种情况下，我们使用`componentWillMount`方法，但我们也可以使用`constructor`或者属性的默认值：

```jsx
  componentWillMount() {
    this.animatedValue = new Animated.Value();
  }
```

1.  一旦我们创建了动画值，我们就可以定义动画。我们还通过将`Animated.timing`的`start`方法传递给一个箭头函数来创建一个循环，该箭头函数再次执行`startAnimation`函数。现在，当图像达到动画的末尾时，我们将再次开始相同的动画，以创建一个无限循环的动画：

```jsx
  startAnimation() {
    this.animatedValue.setValue(width);
    Animated.timing(
      this.animatedValue,
      {
        toValue: -imageWidth,
        duration: 6000,
        easing: Easing.linear,
        useNativeDriver: true,
      }
    ).start(() => this.startAnimation());
  }
```

1.  我们已经完成了动画，但目前只是计算了每一帧的值，没有对这些值做任何操作。下一步是在屏幕上渲染图像，并设置我们想要动画的样式属性。在这种情况下，我们想要在*x*轴上移动元素；因此，我们应该更新`left`属性：

```jsx
  render() {
    return (
      <View style={styles.background}>
        <Animated.Image
          style={[
            styles.image,
            { left: this.animatedValue },
          ]}
          source={cloudImage}
        />
      </View>
    );
  }
```

1.  如果我们刷新模拟器，我们将看到图像在屏幕上，但它还没有被动画处理。为了解决这个问题，我们需要调用`startAnimation`方法。我们将在组件完全渲染后开始动画，使用`componentDidMount`生命周期钩子：

```jsx
  componentDidMount() {
    this.startAnimation();
  }
```

1.  如果我们再次运行应用程序，我们将看到图像在屏幕顶部移动，就像我们想要的那样！作为最后一步，让我们为应用程序添加一些基本样式：

```jsx
const styles = StyleSheet.create({
  background: {
    flex: 1,
    backgroundColor: 'cyan',
  },
  image: {
    height: imageHeight,
    position: 'absolute',
    top: height / 3,
    width: imageWidth,
  },
});
```

输出如下所示：

![](img/1dda7b4d-ccd4-4a02-851a-feddfc257ab0.png)

# 工作原理...

在*步骤 5*中，我们设置了动画数值。第一行每次调用此方法时都会重置初始值。在本例中，初始值将是设备的`宽度`，这将把图像移动到屏幕的右侧，这是我们想要开始动画的地方。

然后，我们使用`Animated.timing`函数基于时间创建动画，并传入两个参数。对于第一个参数，我们传入了在*步骤 4*中的`componentWillMount`生命周期钩子中创建的`animatedValue`。第二个参数是一个包含动画配置的对象。在这种情况下，我们将把结束值设置为图像宽度的负值，这将把图像放在屏幕的左侧。动画在那里完成。

配置完毕后，`Animated`类将计算所需的所有帧，以在分配的 6 秒内执行从右向左的线性动画（通过将`duration`属性设置为`6000`毫秒）。

React Native 还提供了另一个与`Animated`配对使用的辅助工具，称为`Easing`。在这种情况下，我们使用`Easing`辅助类的`linear`属性。`Easing`提供其他常见的缓动方法，如`elastic`和`bounce`。查看`Easing`类文档，并尝试为`easing`属性设置不同的值，看看每个值的效果。您可以在[`facebook.github.io/react-native/docs/easing.html`](https://facebook.github.io/react-native/docs/easing.html)找到文档。

动画配置正确后，我们需要运行它。我们通过调用`start`方法来实现这一点。此方法接收一个可选的`callback`函数参数，当动画完成时将执行该函数。在这种情况下，我们递归运行相同的`startAnimation`函数。这将创建一个无限循环，这正是我们想要实现的。

在*步骤 6*中，我们正在渲染图像。如果要对图像进行动画处理，应始终使用`Animate.Image`组件。在内部，此组件将处理动画的值，并将为本机组件上的每个帧设置每个值。这避免了在每个帧上在 JavaScript 层上运行渲染方法，从而实现更流畅的动画。

除了`Image`之外，我们还可以对`View`、`Text`和`ScrollView`组件进行动画处理。这四个组件都有内置的支持，但我们也可以创建一个新组件，并通过`Animated.createAnimatedComponent()`添加动画支持。这四个组件都能处理样式更改。我们所要做的就是将`animatedValue`传递给我们想要动画的属性，这种情况下是`left`属性，但我们也可以在每个组件上使用任何可用的样式。

# 运行多个动画

在这个配方中，我们将学习如何在几个元素中使用相同的动画值。这样，我们可以重复使用相同的值，以及插值，为其余的元素获得不同的值。

这个动画将类似于上一个配方。这次，我们将有两朵云：一朵较小，移动较慢，另一朵较大，移动较快。在屏幕中央，我们将有一架静止的飞机。我们不会给飞机添加任何动画，但移动的云会使它看起来像飞机在移动。

# 准备就绪

让我们通过创建一个名为`multiple-animations`的空应用程序来开始这个配方。

我们将使用三种不同的图像：两个云和一架飞机。您可以从 GitHub 上的配方存储库下载图像，地址为[`github.com/warlyware/react-native-cookbook/tree/master/chapter-6/multiple-animations/assets/images`](https://github.com/warlyware/react-native-cookbook/tree/master/chapter-6/multiple-animations/assets/images)。确保将图像放在`/assets/images`文件夹中。

# 如何做...

1.  让我们从打开`App.js`并添加我们的导入开始：

```jsx
import React, { Component } from 'react';
import {
  View,
  Animated,
  Image,
  Easing,
  Dimensions,
  StyleSheet,
} from 'react-native';
```

1.  此外，我们需要定义一些常量，并要求我们将用于动画的图像。请注意，我们将在这个配方中将相同的云图像视为`cloudImage1`和`cloudImage2`，但我们将把它们视为单独的实体：

```jsx
const { width, height } = Dimensions.get('window');
const cloudImage1 = require('./assets/images/cloud.png');
const cloudImage2 = require('./assets/images/cloud.png');
const planeImage = require('./assets/images/plane.gif');
const cloudHeight = 100;
const cloudWidth = 150;
const planeHeight = 60;
const planeWidth = 100;
```

1.  在下一步中，当组件被创建时，我们将创建`animatedValue`实例，然后在组件完全渲染时开始动画。我们正在创建一个在无限循环中运行的动画。初始值将为`1`，最终值将为`0`。如果您对这段代码不清楚，请确保阅读本章的第一个配方：

```jsx
export default class App extends Component { 
  componentWillMount() { 
    this.animatedValue = new Animated.Value(); 
  } 

  componentDidMount() { 
    this.startAnimation(); 
  } 

  startAnimation () { 
    this.animatedValue.setValue(1); 
    Animated.timing( 
      this.animatedValue, 
      { 
        toValue: 0, 
        duration: 6000, 
        easing: Easing.linear, 
      } 
    ).start(() => this.startAnimation()); 
  } 

  render() { 
    // Defined in a later step
  } 
} 

const styles = StyleSheet.create({ 
  // Defined in a later step
}); 
```

1.  在本示例中，`render`方法将与上一个示例有很大不同。在本示例中，我们将使用相同的`animatedValue`来动画两个图像。动画值将返回从`1`到`0`的值；但是，我们希望将云从右向左移动，因此我们需要为每个元素设置`left`值。

为了设置正确的值，我们需要对`animatedValue`进行插值。对于较小的云，我们将把初始的`left`值设为设备的宽度，但对于较大的云，我们将把初始的`left`值设得远离设备的右边缘。这将使移动距离更大，因此移动速度会更快：

```jsx
  render() {
    const left1 = this.animatedValue.interpolate({
      inputRange: [0, 1],
      outputRange: [-cloudWidth, width],
    });

    const left2 = this.animatedValue.interpolate({
      inputRange: [0, 1],
      outputRange: [-cloudWidth*5, width + cloudWidth*5],
    });

    // Defined in a later step
  } 
```

1.  一旦我们有了正确的`left`值，我们需要定义我们想要动画的元素。在这里，我们将把插值值设置为`left`样式属性：

```jsx
  render() {
    // Defined in a later step

    return (
      <View style={styles.background}>
        <Animated.Image
          style={[
            styles.cloud1,
            { left: left1 },
          ]}
          source={cloudImage1}
        />
        <Image
          style={styles.plane}
          source={planeImage}
        />
        <Animated.Image
          style={[
            styles.cloud2,
            { left: left2 },
          ]}
          source={cloudImage2}
        />
      </View>
    );
  }
```

1.  至于最后一步，我们需要定义一些样式，只需设置每朵云的`width`和`height`以及为`top`分配样式即可。

```jsx
const styles = StyleSheet.create({
  background: {
    flex: 1,
    backgroundColor: 'cyan',
  },
  cloud1: {
    position: 'absolute',
    width: cloudWidth,
    height: cloudHeight,
    top: height / 3 - cloudWidth / 2,
  },
  cloud2: {
    position: 'absolute',
    width: cloudWidth * 1.5,
    height: cloudHeight * 1.5,
    top: height/2,
  },
  plane: {
    position: 'absolute',
    height: planeHeight,
    width: planeWidth,
    top: height / 2 - planeHeight,
    left: width / 2 - planeWidth,
  }
});
```

1.  如果我们刷新应用，我们应该能看到动画：

![](img/e752f224-3437-4c01-8bd1-89c74f625a1f.png)

# 工作原理...

在*步骤 4*中，我们定义了插值以获取每朵云的`left`值。`interpolate`方法接收一个具有两个必需配置的对象，`inputRange`和`outputRange`。

`inputRange`配置接收一个值数组。这些值应始终是升序值；您也可以使用负值，只要值是升序的。

`outputRange`应该与`inputRange`中定义的值的数量匹配。这些是我们需要作为插值结果的值。

对于本示例，`inputRange`从`0`到`1`，这些是我们的`animatedValue`的值。在`outputRange`中，我们定义了我们需要的移动的限制。

# 创建动画通知

在本示例中，我们将从头开始创建一个通知组件。在显示通知时，组件将从屏幕顶部滑入。几秒钟后，我们将自动隐藏它，将其滑出。

# 准备工作

我们将创建一个应用。让我们称之为`notification-animation`。

# 如何做...

1.  我们将从`App`组件开始工作。首先，让我们导入所有必需的依赖项：

```jsx
import React, { Component } from 'react';
import {
  Text,
  TouchableOpacity,
  StyleSheet,
  View,
  SafeAreaView,
} from 'react-native';
import Notification from './Notification';
```

1.  一旦我们导入了所有依赖项，我们就可以定义`App`类。在这种情况下，我们将使用`notify`属性等于`false`来初始化`state`。我们将使用此属性来显示或隐藏通知。默认情况下，通知不会显示在屏幕上。为了简化事情，我们将在`state`中定义`message`属性，其中包含我们想要显示的文本：

```jsx
export default class App extends Component {
  state = {
    notify: false,
    message: 'This is a notification!',
  };

  toggleNotification = () => {
    // Defined on later step
  }

  render() {
    // Defined on later step
  }
}

const styles = StyleSheet.create({
    // Defined on later step
});

```

1.  在`render`方法内，我们需要仅在`notify`属性为`true`时显示通知。我们可以通过使用`if`语句来实现这一点：

```jsx
  render() {
    const notify = this.state.notify
      ? <Notification
          autoHide
          message={this.state.message}
          onClose={this.toggleNotification}
        />
    : null;
    // Defined on next step
  }
```

1.  在上一步中，我们只定义了对`Notification`组件的引用，但还没有使用它。让我们定义一个`return`，其中包含此应用程序所需的所有 JSX。为了保持简单，我们只会定义一个工具栏、一些文本和一个按钮，以在按下时切换通知的状态：

```jsx
  render() {
    // Code from previous step
    return (
      <SafeAreaView>
        <Text style={styles.toolbar}>Main toolbar</Text>
        <View style={styles.content}>
          <Text>
            Lorem ipsum dolor sit amet, consectetur adipiscing 
            elit,
            sed do eiusmod tempor incididunt ut labore et 
            dolore magna.
          </Text>
          <TouchableOpacity
            onPress={this.toggleNotification}
            style={styles.btn}
          >
            <Text style={styles.text}>Show notification</Text>
          </TouchableOpacity>
          <Text>
            Sed ut perspiciatis unde omnis iste natus error sit 
            accusantium doloremque laudantium.
          </Text>
          {notify}
        </View>
      </SafeAreaView>
    );
  }
```

1.  我们还需要定义一个方法，用于在`state`上切换`notify`属性，这非常简单：

```jsx
  toggleNotification = () => {
    this.setState({
      notify: !this.state.notify,
    });
  }
```

1.  我们几乎完成了这个类。剩下的只有样式。在这种情况下，我们只会添加基本样式，如`color`、`padding`、`fontSize`、`backgroundColor`和`margin`，没有什么特别的：

```jsx
        const styles = StyleSheet.create({ 
          toolbar: { 
            backgroundColor: '#8e44ad', 
            color: '#fff', 
            fontSize: 22, 
            padding: 20, 
            textAlign: 'center', 
          }, 
          content: { 
            padding: 10, 
            overflow: 'hidden', 
          }, 
          btn: { 
            margin: 10, 
            backgroundColor: '#9b59b6', 
            borderRadius: 3, 
            padding: 10, 
          }, 
          text: { 
            textAlign: 'center', 
            color: '#fff', 
          }, 
        }); 
```

1.  如果我们尝试运行应用程序，我们会看到一个错误，即无法解析`./Notification`模块。让我们通过定义`Notification`组件来解决这个问题。让我们创建一个`Notifications`文件夹，其中包含一个`index.js`文件。然后，我们可以导入我们的依赖项：

```jsx
import React, { Componen } from 'react';
import {
  Animated,
  Easing,
  StyleSheet,
  Text,
} from 'react-native';
```

1.  一旦我们导入了依赖项，让我们定义新组件的 props 和初始状态。我们将定义一些非常简单的东西，只是一个用于接收要显示的消息的属性，以及两个`callback`函数，允许在通知出现在屏幕上和关闭时运行一些操作。我们还将添加一个属性来设置在自动隐藏通知之前显示通知的毫秒数：

```jsx
export default class Notification extends Component {
  static defaultProps = {
    delay: 5000,
    onClose: () => {},
    onOpen: () => {},
  };

  state = {
    height: -1000,
  };
}
```

1.  终于是时候开始处理动画了！我们需要在组件被渲染时立即开始动画。如果以下代码中有什么不清楚的地方，我建议你看一下本章的第一和第二个示例：

```jsx
  componentWillMount() {
    this.animatedValue = new Animated.Value();
  }

  componentDidMount() {
    this.startSlideIn();
  }

  getAnimation(value, autoHide) {
    const { delay } = this.props;
    return Animated.timing(
      this.animatedValue,
      {
        toValue: value,
        duration: 500,
        easing: Easing.cubic,
        delay: autoHide ? delay : 0,
      }
    );
  }
```

1.  到目前为止，我们已经定义了一个获取动画的方法。对于滑入运动，我们需要计算从`0`到`1`的值。动画完成后，我们需要运行`onOpen`回调。如果`autoHide`属性在调用`onOpen`方法时设置为`true`，我们将自动运行滑出动画以删除组件：

```jsx
  startSlideIn () {
    const { onOpen, autoHide } = this.props;

    this.animatedValue.setValue(0);
    this.getAnimation(1)
      .start(() => {
        onOpen();
        if (autoHide){
          this.startSlideOut();
        }
      });
  }
```

1.  与前面的步骤类似，我们需要一个用于滑出运动的方法。在这里，我们需要计算从`1`到`0`的值。我们将`autoHide`值作为参数发送到`getAnimation`方法。这将自动延迟动画，延迟时间由`delay`属性定义（在我们的例子中为 5 秒）。动画完成后，我们需要运行`onClose`回调函数，这将从`App`类中删除组件：

```jsx
  startSlideOut() {
    const { autoHide, onClose } = this.props;

    this.animatedValue.setValue(1);
    this.getAnimation(0, autoHide)
      .start(() => onClose());
  }
```

1.  最后，让我们添加`render`方法。在这里，我们将获取`props`提供的`message`值。我们还需要组件的`height`来将组件移动到动画的初始位置；默认情况下是`-1000`，但我们将在下一步在运行时设置正确的值。`animatedValue`从`0`到`1`或从`1`到`0`，取决于通知是打开还是关闭；因此，我们需要对其进行插值以获得实际值。动画将从组件的负高度到`0`；这将导致一个漂亮的滑入/滑出动画：

```jsx
  render() {
    const { message } = this.props;
    const { height } = this.state;
    const top = this.animatedValue.interpolate({
       inputRange: [0, 1],
       outputRange: [-height, 0],
     });
    // Defined on next step
   }
}
```

1.  为了尽可能简单，我们将返回一个带有一些文本的`Animated.View`。在这里，我们正在使用插值结果设置`top`样式，这意味着我们将对顶部样式进行动画处理。如前所述，我们需要在运行时计算组件的高度。为了实现这一点，我们需要使用视图的`onLayout`属性。此函数将在每次布局更新时调用，并将新的组件尺寸作为参数发送：

```jsx
  render() {
     // Code from previous step
     return (
      <Animated.View
        onLayout={this.onLayoutChange}
        style={[
          styles.main,
          { top }
        ]}
      >
        <Text style={styles.text}>{message}</Text>
      </Animated.View>
    );
   }
}
```

1.  `onLayoutChange`方法将非常简单。我们只需要获取新的`height`并更新`state`。此方法接收一个`event`。从这个对象中，我们可以获取有用的信息。对于我们的目的，我们将在`event`对象的`nativeEvent.layout`中访问数据。`layout`对象包含屏幕的`width`和`height`，以及`Animated.View`调用此函数时屏幕上的*x*和*y*位置：

```jsx
  onLayoutChange = (event) => {
    const {layout: { height } } = event.nativeEvent;
     this.setState({ height });
   }
```

1.  在最后一步，我们将为通知组件添加一些样式。由于我们希望该组件在任何其他内容之上进行动画，我们需要将`position`设置为`absolute`，并将`left`和`right`属性设置为`0`。我们还将添加一些颜色和填充：

```jsx
        const styles = StyleSheet.create({ 
          main: { 
            backgroundColor: 'rgba(0, 0, 0, 0.7)', 
            padding: 10, 
            position: 'absolute', 
            left: 0, 
            right: 0, 
          }, 
          text: { 
            color: '#fff', 
          }, 
       }); 
```

1.  最终应用程序应该看起来像以下截图：

![](img/425fcaa8-645a-4ae2-8d6d-8a97778cdde7.png)

# 工作原理...

在*步骤 3*中，我们定义了`Notification`组件。该组件接收三个参数：一个标志，用于在几秒后自动隐藏组件，我们要显示的消息，以及在通知关闭时将执行的`callback`函数。

当`onClose`回调被执行时，我们将切换`notify`属性以移除`Notification`实例并清除内存。

在*步骤 4*中，我们定义了用于渲染应用程序组件的 JSX。重要的是要在其他组件之后渲染`Notification`组件，以便该组件显示在所有其他组件之上。

在*步骤 6*中，我们定义了组件的`state`。`defaultProps`对象为每个属性设置了默认值。如果给定属性没有赋值，这些值将被应用。

我们将每个`callback`的默认值定义为空函数。这样，我们在尝试执行它们之前不必检查这些 props 是否有值。

对于初始的`state`，我们定义了`height`属性。实际的`height`值将根据`message`属性中接收的内容在运行时计算。这意味着我们需要最初将组件远离原始位置进行渲染。由于在计算布局时存在短暂延迟，我们不希望在移动到正确位置之前显示通知。

在*步骤 9*中，我们创建了动画。`getAnimation`方法接收两个参数：要应用的`delay`和`autoHide`布尔值，用于确定通知是否自动关闭。我们在*步骤 10*和*步骤 11*中使用了这个方法。

在*步骤 13*中，我们为该组件定义了 JSX。`onLayout`函数在更新布局时非常有用，可以获取组件的尺寸。例如，如果设备方向发生变化，尺寸将发生变化，这种情况下，我们希望更新动画的初始和最终坐标。

# 还有更多...

当前的实现效果相当不错，但是我们应该解决一个性能问题。目前，`onLayout`方法在每一帧动画上都会被执行，这意味着我们在每一帧上都在更新`state`，这导致组件在每一帧上重新渲染！我们应该避免这种情况，只更新一次以获得实际的高度。

为了解决这个问题，我们可以添加一个简单的验证，只有在当前值与初始值不同时才更新状态。这将避免在每一帧上更新`state`，我们也不会一遍又一遍地强制渲染：

```jsx
onLayoutChange = (event) => { 
  const {layout: { height } } = event.nativeEvent; 
 if (this.state.height === -1000) { 
    this.setState({ height }); 
 } 
} 
```

虽然这对我们的目的有效，但我们也可以进一步确保在方向改变时`height`也会更新。然而，我们会在这里停下，因为这个方法已经相当长了。

# 展开和折叠容器

在这个方法中，我们将创建一个带有`title`和`content`的自定义容器元素。当用户按下标题时，内容将折叠或展开。这个方法将允许我们探索`LayoutAnimation` API。

# 做好准备

让我们从创建一个新的应用程序开始。我们将其称为`collapsable-containers`。

一旦我们创建了应用程序，让我们还创建一个`Panel`文件夹，里面有一个`index.js`文件，用于存放我们的`Panel`组件。

# 如何做...

1.  让我们首先专注于`Panel`组件。首先，我们需要导入我们将在这个类中使用的所有依赖项：

```jsx
import React, { Component } from 'react';
import {
  View,
  LayoutAnimation,
  StyleSheet,
  Text,
  TouchableOpacity,
} from 'react-native';
```

1.  一旦我们有了依赖项，让我们声明`defaultProps`来初始化这个组件。在这个方法中，我们只需要将`expanded`属性初始化为`false`：

```jsx
export default class Panel extends Component {
  static defaultProps = {
    expanded: false
  };
}

const styles = StyleSheet.create({
  // Defined on later step
});
```

1.  我们将使用`state`对象上的`height`属性来展开或折叠容器。这个组件第一次被创建时，我们需要检查`expanded`属性，以设置正确的初始`height`：

```jsx
  state = {
    height: this.props.expanded ? null : 0,
  };
```

1.  让我们为这个组件渲染所需的 JSX 元素。我们需要从`state`中获取`height`的值，并将其设置为内容的样式视图。当按下`title`元素时，我们将执行`toggle`方法（稍后定义）来改变`state`的`height`值：

```jsx
  render() {
    const { children, style, title } = this.props;
    const { height } = this.state;

    return (
      <View style={[styles.main, style]}>
        <TouchableOpacity onPress={this.toggle}>
          <Text style={styles.title}>
            {title}
          </Text>
        </TouchableOpacity>
        <View style={{ height }}>
          {children}
        </View>
      </View>
    );
  }
```

1.  如前所述，当按下`title`元素时，`toggle`方法将被执行。在这里，我们将在`state`上切换`height`并在下一个渲染周期更新样式时调用我们想要使用的动画：

```jsx
  toggle = () => {
    LayoutAnimation.spring();
    this.setState({
      height: this.state.height === null ? 0 : null,
    })
  }
```

1.  为了完成这个组件，让我们添加一些简单的样式。我们需要将`overflow`设置为`hidden`，否则在组件折叠时内容将被显示出来。

```jsx
const styles = StyleSheet.create({
  main: {
    backgroundColor: '#fff',
    borderRadius: 3,
    overflow: 'hidden',
    paddingLeft: 30,
    paddingRight: 30,
  },
  title: {
    fontWeight: 'bold',
    paddingTop: 15,
    paddingBottom: 15,
  }
```

1.  一旦我们定义了`Panel`组件，让我们在`App`类中使用它。首先，我们需要在`App.js`中要求所有的依赖项：

```jsx
import React, { Component } from 'react';
import {
  Text,
  StyleSheet,
  View,
  SafeAreaView,
  Platform,
  UIManager
} from 'react-native';
import Panel from './Panel';
```

1.  在上一步中，我们导入了`Panel`组件。我们将在 JSX 中声明这个类的三个实例：

```jsx
 export default class App extends Component {
  render() {
    return (
      <SafeAreaView style={[styles.main]}>
        <Text style={styles.toolbar}>Animated containers</Text>
        <View style={styles.content}>
          <Panel
            title={'Container 1'}
            style={styles.panel}
          >
            <Text style={styles.panelText}>
              Temporibus autem quibusdam et aut officiis
              debitis aut rerum necessitatibus saepe
              eveniet ut et voluptates repudiandae sint et
              molestiae non recusandae.
            </Text>
          </Panel>
          <Panel
            title={'Container 2'}
            style={styles.panel}
              >
            <Text style={styles.panelText}>
              Et harum quidem rerum facilis est et expedita 
              distinctio. Nam libero tempore,
              cum soluta nobis est eligendi optio cumque.
            </Text>
          </Panel>
          <Panel
            expanded
            title={'Container 3'}
            style={styles.panel}
           >
            <Text style={styles.panelText}>
              Nullam lobortis eu lorem ut vulputate.
            </Text>
            <Text style={styles.panelText}>
              Donec id elementum orci. Donec fringilla lobortis 
              ipsum, vitae commodo urna.
            </Text>
          </Panel>
        </View>
      </SafeAreaView>
    );
  }
}
```

1.  在这个示例中，我们在 React Native 中使用了`LayoutAnimation` API。在当前版本的 React Native 中，这个 API 在 Android 上默认是禁用的。在`App`组件挂载之前，我们将使用`Platform`助手和`UIManager`在 Android 设备上启用这个功能：

```jsx
  componentWillMount() {
    if (Platform.OS === 'android') {
      UIManager.setLayoutAnimationEnabledExperimental(true);
    }
  }
```

1.  最后，让我们为工具栏和主容器添加一些样式。我们只需要一些你现在可能已经习惯的简单样式：`padding`，`margin`和`color`。

```jsx
const styles = StyleSheet.create({
  main: {
    flex: 1,
  },
  toolbar: {
    backgroundColor: '#3498db',
    color: '#fff',
    fontSize: 22,
    padding: 20,
    textAlign: 'center',
  },
  content: {
    padding: 10,
    backgroundColor: '#ecf0f1',
    flex: 1,
  },
  panel: {
    marginBottom: 10,
  },
  panelText: {
    paddingBottom: 15,
  }
});
```

1.  最终的应用程序应该类似于以下截图：

![](img/6896c6bd-b770-4a25-8423-6aad90ac4964.png)

# 工作原理...

在*步骤 3*中，我们设置了内容的初始`height`。如果`expanded`属性设置为`true`，那么我们应该显示内容。通过将`height`值设置为`null`，布局系统将根据内容计算`height`；否则，我们需要将值设置为`0`，这将在组件折叠时隐藏内容。

在*步骤 4*中，我们为`Panel`组件定义了所有 JSX。这一步中有一些值得介绍的概念。首先，`children`属性是从`props`对象中传入的，当这个组件在`App`类中使用时，它将包含在`<Panel>`和`</Panel>`之间定义的任何元素。这非常有帮助，因为通过使用这个属性，我们允许这个组件接收任何其他组件作为子组件。

在同一步骤中，我们还从`state`对象中获取`height`并将其设置为应用于可折叠内容的`View`的`style`。这将更新`height`，导致组件相应地展开或折叠。我们还声明了`onPress`回调，当按下`title`元素时，它会切换`state`上的`height`。

在*步骤 7*中，我们定义了`toggle`方法，它可以切换`height`值。在这里，我们使用了`LayoutAnimation`类。通过调用`spring`方法，布局系统将在下一次渲染时对布局发生的每一次变化进行动画处理。在这种情况下，我们只改变了`height`，但我们也可以改变任何其他属性，比如`opacity`，`position`或`color`。

`LayoutAnimation`类包含一些预定义的动画。在这个示例中，我们使用了`spring`，但我们也可以使用`linear`或`easeInEaseOut`，或者使用`configureNext`方法创建自己的动画。

如果我们移除`LayoutAnimation`，我们将看不到动画；组件将通过从`0`到总高度跳跃来展开和折叠。但通过添加那一行代码，我们可以轻松地添加一个漂亮、平滑的动画。如果您需要更多对动画的控制，您可能会想使用动画 API。

在*步骤 9*中，我们在`Platform`助手上检查了 OS 属性，它返回了`'android'`或`'ios'`字符串，取决于应用程序运行在哪个设备上。如果应用程序在 Andriod 上运行，我们使用`UIManager`助手的`setLayoutAnimationEnabledExperimental`方法来启用`LayoutAnimation` API。

# 另请参阅

+   `LayoutAnimation` API 文档在[`facebook.github.io/react-native/docs/layoutanimation.html`](https://facebook.github.io/react-native/docs/layoutanimation.html)

+   在[`codeburst.io/a-quick-intro-to-reacts-props-children-cb3d2fce4891`](https://codeburst.io/a-quick-intro-to-reacts-props-children-cb3d2fce4891)快速介绍 React 的`props.children`。

# 创建带有加载动画的按钮

在这个示例中，我们将继续使用`LayoutAnimation`类。在这里，我们将创建一个按钮，当用户按下按钮时，我们将显示一个加载指示器并动画化样式。

# 准备工作

要开始，我们需要创建一个空的应用程序。让我们称之为`button-loading-animation`。

让我们还创建一个`Button`文件夹，里面有一个`index.js`文件，用于我们的`Button`组件。

# 如何做...

1.  让我们从`Button/index.js`文件开始。首先，我们将导入这个组件所需的所有依赖项：

```jsx
import React, { Component } from 'react';
import {
  ActivityIndicator,
  LayoutAnimation,
  StyleSheet,
  Text,
  TouchableOpacity,
  View,
} from 'react-native';
```

1.  对于这个组件，我们将只使用四个 props：一个`label`，一个`loading`布尔值，用于切换显示加载指示器或按钮内的标签，一个在按钮被按下时执行的回调函数，以及自定义样式。在这里，我们将`init`默认的`loading`为`false`，并将`handleButtonPress`设置为空函数：

```jsx
export default class Button extends Component {
  static defaultProps = {
    loading: false,
    onPress: () => {},
  };
  // Defined on later steps
}
```

1.  我们将尽可能简化这个组件的`render`方法。我们将根据`loading`属性的值来渲染标签和活动指示器：

```jsx
  render() {
    const { loading, style } = this.props;

    return (
      <TouchableOpacity
        style={[
          styles.main,
          style,
          loading ? styles.loading : null,
        ]}
        activeOpacity={0.6}
        onPress={this.handleButtonPress}
      >
        <View>
          {this.renderLabel()}
          {this.renderActivityIndicator()}
        </View>
      </TouchableOpacity>
    );
  }
```

1.  为了渲染`label`，我们需要检查`loading`属性是否为`false`。如果是，那么我们只返回一个带有从`props`接收到的`label`的`Text`元素：

```jsx
  renderLabel() {
    const { label, loading } = this.props;
    if(!loading) {
      return (
        <Text style={styles.label}>{label}</Text>
      );
    }
  }
```

1.  同样，`renderActivityIndicator`指示器应该只在`loading`属性的值为`true`时应用。如果是这样，我们将返回`ActivityIndicator`组件。我们将使用`ActivityIndicator`的 props 来定义一个小的`size`和白色的`color`(`#fff`)：

```jsx
  renderActivityIndicator() {
    if (this.props.loading) {
      return (
        <ActivityIndicator size="small" color="#fff" />
      );
    }
  }
```

1.  我们的类中还缺少一个方法：`handleButtonPress`。当按钮被按下时，我们需要通知这个组件的父组件，这可以通过调用通过`props`传递给这个组件的`onPress`回调来实现。我们还将使用`LayoutAnimation`在下一次渲染时排队一个动画：

```jsx
  handleButtonPress = () => {
    const { loading, onPress } = this.props;

    LayoutAnimation.easeInEaseOut();
    onPress(!loading);
  }
```

1.  为了完成这个组件，我们需要添加一些样式。我们将定义一些颜色，圆角，对齐，填充等。对于显示加载指示器时将应用的`loading`样式，我们将更新填充以创建一个围绕加载指示器的圆形：

```jsx
const styles = StyleSheet.create({
  main: {
    backgroundColor: '#e67e22',
    borderRadius: 20,
    padding: 10,
    paddingLeft: 50,
    paddingRight: 50,
  },
  label: {
    color: '#fff',
    fontWeight: 'bold',
    textAlign: 'center',
    backgroundColor: 'transparent',
  },
  loading: {
    padding: 10,
    paddingLeft: 10,
    paddingRight: 10,
  },
});
```

1.  我们已经完成了`Button`组件。现在，让我们来处理`App`类。让我们首先导入所有的依赖项：

```jsx
import React, { Component } from 'react';
import {
  Text,
  StyleSheet,
  View,
  SafeAreaView,
  Platform,
  UIManager
} from 'react-native';
import Button from './Button';
```

1.  `App`类相对简单。我们只需要在`state`对象上定义一个`loading`属性，它将切换`Button`的动画。我们还将渲染一个`toolbar`和一个`Button`：

```jsx
export default class App extends Component {
  state = {
    loading: false,
  };

  // Defined on next step

  handleButtonPress = (loading) => {
    this.setState({ loading });
  }

  render() {
    const { loading } = this.state;

    return (
      <SafeAreaView style={[styles.main, android]}>
        <Text style={styles.toolbar}>Animated containers</Text>
        <View style={styles.content}>
          <Button
            label="Login"
            loading={loading}
            onPress={this.handleButtonPress}
          />
        </View>
      </SafeAreaView>
    );
  }
}
```

1.  与上一个示例一样，我们需要在 Android 设备上手动启用`LayoutAnimation`API：

```jsx
  componentWillMount() {
    if (Platform.OS === 'android') {
      UIManager.setLayoutAnimationEnabledExperimental(true);
    }
  }
```

1.  最后，我们将添加一些`styles`，只是一些颜色，填充和居中对齐按钮在屏幕上：

```jsx
const styles = StyleSheet.create({
  main: {
    flex: 1,
  },
  toolbar: {
    backgroundColor: '#f39c12',
    color: '#fff',
    fontSize: 22,
    padding: 20,
    textAlign: 'center',
  },
  content: {
    padding: 10,
    backgroundColor: '#ecf0f1',
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

1.  最终的应用程序应该类似于以下截图：

![](img/4be877c5-963e-4b57-9685-a03b35244a4c.png)

# 工作原理...

在*步骤 3*中，我们为`Button`组件添加了`render`方法。在这里，我们接收了`loading`属性，并根据该值将相应的样式应用于`TouchableOpacity`按钮元素。我们还使用了两种方法：一种用于渲染标签，另一种用于渲染活动指示器。

在*步骤 6*中，我们执行了`onPress`回调。默认情况下，我们声明了一个空函数，因此我们不必检查值是否存在。

这个按钮的父组件应该负责在调用`onPress`回调时更新`loading`属性。从这个组件中，我们只负责在按下此按钮时通知父组件。

`LayoutAnimation.eadeInEaseOut`方法只是将动画排队到下一个渲染阶段，这意味着动画不会立即执行。我们负责更改我们想要动画的样式。如果我们不改变任何样式，那么我们就看不到任何动画。

`Button`组件不知道`loading`属性是如何更新的。这可能是因为获取请求、超时或任何其他操作。父组件负责更新`loading`属性。无论发生任何变化，我们都会将新样式应用于按钮，并进行平滑的动画。

在*步骤 9*中，我们定义了`App`类的内容。在这里，我们使用了我们的`Button`组件。当按下按钮时，`loading`属性的`state`将被更新，这将导致每次按下按钮时动画运行。

# 结论

在本章中，我们已经介绍了如何为您的 React Native 应用程序添加动画的基础知识。这些示例旨在提供有用的实际代码解决方案，并建立如何使用基本构建块，以便您更好地创建适合您的应用程序的动画。希望到目前为止，您应该已经开始熟悉`Animated`和`LayoutAnimation`动画助手。在第七章中，*为您的应用程序添加高级动画*，我们将结合我们在这里学到的东西来构建更复杂和有趣的应用程序 UI 动画。
