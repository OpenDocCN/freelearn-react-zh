# 第七章：为您的应用程序添加高级动画

在本章中，我们将涵盖以下配方：

+   从列表组件中删除项目

+   创建 Facebook 反应小部件

+   在全屏显示图像

# 介绍

在上一章中，我们介绍了在 React Native 中使用两个主要动画助手`Animated`和`LayoutAnimation`的基础知识。在本章中，我们将通过构建更复杂的配方来进一步了解这些概念，展示常见的本地 UX 模式。

# 从列表组件中删除项目

在这个配方中，我们将学习如何在`ListView`中创建带有动画侧向滑动的列表项。如果用户将项目滑动超过阈值，项目将被移除。这是许多具有可编辑列表的移动应用程序中的常见模式。我们还将看到如何使用`PanResponder`来处理拖动事件。

# 准备就绪

我们需要创建一个空的应用程序。对于这个配方，我们将其命名为`removing-list-items`。

我们还需要创建一个新的`ContactList`文件夹，并在其中创建两个文件：`index.js`和`ContactItem.js`。

# 如何做...

1.  让我们从导入主`App`类的依赖项开始，如下所示：

```jsx
import React from 'react';
import {
  Text,
  StyleSheet,
  SafeAreaView,
} from 'react-native';
import ContactList from './ContactList';
```

1.  这个组件将很简单。我们只需要渲染一个`toolbar`和我们在上一步中导入的`ContactList`组件，如下所示：

```jsx
const App = () => (
  <SafeAreaView style={styles.main}>
    <Text style={styles.toolbar}>Contacts</Text>
    <ContactList style={styles.content} />
  </SafeAreaView>
);

const styles = StyleSheet.create({
  main: {
    flex: 1,
  },
  toolbar: {
    backgroundColor: '#2c3e50',
    color: '#fff',
    fontSize: 22,
    padding: 20,
    textAlign: 'center',
  },
  content: {
    padding: 10,
    flex: 1,
  },
});

export default App;
```

1.  这就是我们开始实际工作的全部内容。让我们打开`ContactList/index.js`文件，并导入所有依赖项，如下所示：

```jsx
import React, { Component } from 'react';
import {
  ListView,
  ScrollView,
} from 'react-native';
import ContactItem from './ContactItem';
```

1.  然后我们需要定义一些数据。在真实的应用程序中，我们会从 API 中获取数据，但为了保持简单并且只关注拖动功能，让我们在这个相同的文件中定义数据：

```jsx
const data = [
  { id: 1, name: 'Jon Snow' },
  { id: 2, name: 'Luke Skywalker' },
  { id: 3, name: 'Bilbo Baggins' },
  { id: 4, name: 'Bob Labla' },
  { id: 5, name: 'Mr. Magoo' },
];
```

1.  这个组件的`state`只包含两个属性：列表的数据和一个布尔值，在拖动开始或结束时将更新。如果您不熟悉`ListView`的工作原理，请查看第二章中的*显示项目列表*配方，*创建一个简单的 React Native 应用程序*。让我们定义数据如下：

```jsx
export default class ContactList extends Component {
  ds = new ListView.DataSource({
    rowHasChanged: (r1, r2) => r1 !== r2
  });

  state = {
    dataSource: this.ds.cloneWithRows(data),
    swiping: false,
  };
  // Defined in later steps
} 
```

1.  `render`方法只需要显示列表。在`renderScrollComponent`属性中，我们将仅在用户不在列表上滑动项目时启用滚动。如果用户在滑动，我们希望禁用垂直滚动，如下所示：

```jsx
  render() {
    const { dataSource, swiping } = this.state;

    return (
    <ListView
        key={data}
        enableEmptySections
        dataSource={dataSource}
        renderScrollComponent={
        (props) => <ScrollView {...props} scrollEnabled={!swiping}/>
        }
        renderRow={this.renderItem}
      />
    );
  }
```

1.  `renderItem`方法将返回列表中的每个项目。在这里，我们需要将联系信息作为属性发送，以及三个回调函数：

```jsx
  renderItem = (contact) => (
    <ContactItem
      contact={contact}
      onRemove={this.handleRemoveContact}
      onDragEnd={this.handleToggleSwipe}
      onDragStart={this.handleToggleSwipe}
    />
  );
```

1.  我们需要切换`state`对象上的 swiping 属性的值，这将切换列表上的垂直滚动是否被锁定：

```jsx
  handleToggleSwipe = () => {
    this.setState({ swiping: !this.state.swiping });
  }
```

1.  在移除项目时，我们需要找到给定`contact`的`index`，然后从原始列表中将其移除。之后，我们需要更新`state`上的`dataSource`，以使用生成的数据重新渲染列表：

```jsx
  handleRemoveContact = (contact) => {
    const index = data.findIndex(
      (item) => item.id === contact.id
    );
    data.splice(index, 1);

    this.setState({
        dataSource: this.ds.cloneWithRows(data),
    });
  }
```

1.  列表已经完成，现在让我们专注于列表项。让我们打开`ContactList/ContactItem.js`文件，并导入我们需要的依赖项：

```jsx
import React, { Component } from 'react';
import {
  Animated,
  Easing,
  PanResponder,
  StyleSheet,
  Text,
  TouchableHighlight,
  View,
} from 'react-native';
```

1.  我们需要为这个组件定义`defaultProps`。`defaultProps`对象将需要为从父级`ListView`元素传递给它的四个 props 中的每一个都提供一个空函数。当项目被按下时，`onPress`函数将被执行，当联系人被移除时，`onRemove`函数将被执行，而两个拖动函数将监听拖动事件。在`state`上，我们只需要定义一个动画值来保存拖动的 x 和 y 坐标，如下所示：

```jsx
export default class ContactItem extends Component {
  static defaultProps = {
    onPress: () => {},
    onRemove: () => {},
    onDragEnd: () => {},
    onDragStart: () => {},
  };

  state = {
    pan: new Animated.ValueXY(),
  };
```

1.  当组件被创建时，我们需要配置`PanResponder`。我们将在`componentWillMount`生命周期钩子中进行这个操作。`PanResponder`负责处理手势。它提供了一个简单的 API 来捕获用户手指生成的事件，如下所示：

```jsx
  componentWillMount() {
    this.panResponder = PanResponder.create({
      onMoveShouldSetPanResponderCapture: this.handleShouldDrag,
      onPanResponderMove: Animated.event(
        [null, { dx: this.state.pan.x }]
      ),
      onPanResponderRelease: this.handleReleaseItem,
      onPanResponderTerminate: this.handleReleaseItem,
    });
  }
```

1.  现在让我们定义实际的函数，这些函数将在前一步中定义的每个回调中执行。我们可以从`handleShouldDrag`方法开始，如下所示：

```jsx
  handleShouldDrag = (e, gesture) => {
    const { dx } = gesture;
    return Math.abs(dx) > 2;
  }
```

1.  `handleReleaseItem`有点复杂。我们将把这个方法分成两步。首先，我们需要弄清楚当前项目是否需要被移除。为了做到这一点，我们需要设置一个阈值。如果用户将元素滑动超出我们的阈值，我们将移除该项目，如下所示：

```jsx
  handleReleaseItem = (e, gesture) => {
    const { onRemove, contact,onDragEnd } = this.props;
    const move = this.rowWidth - Math.abs(gesture.dx);
    let remove = false;
    let config = { // Animation to origin position
      toValue: { x: 0, y: 0 },
      duration: 500,
    };

    if (move < this.threshold) {
      remove = true;
      if (gesture.dx > 0) {
        config = { // Animation to the right
          toValue: { x: this.rowWidth, y: 0 },
          duration: 100,
        };
      } else {
        config = { // Animation to the left
          toValue: { x: -this.rowWidth, y: 0 },
          duration: 100,
        };
      }
    }
    // Remainder in next step
  }
```

1.  一旦我们对动画进行了配置，我们就准备好移动项目了！首先，我们将执行`onDragEnd`回调，如果项目应该被移除，我们将运行`onRemove`函数，如下所示：

```jsx
  handleReleaseItem = (e, gesture) => {
    // Code from previous step

    onDragEnd();
    Animated.spring(
      this.state.pan,
      config,
    ).start(() => {
      if (remove) {
        onRemove(contact);
      }
    });
  }
```

1.  拖动系统已经完全就绪。现在我们需要定义`render`方法。我们只需要在`TouchableHighlight`元素内显示联系人姓名，包裹在`Animated.View`中，如下所示：

```jsx
  render() {
    const { contact, onPress } = this.props;

    return (
      <View style={styles.row} onLayout={this.setThreshold}>
        <Animated.View
          style={[styles.pan, this.state.pan.getLayout()]}
          {...this.panResponder.panHandlers}
        >
          <TouchableHighlight
            style={styles.info}
            onPress={() => onPress(contact)}
            underlayColor="#ecf0f1"
          >
            <Text>{contact.name}</Text>
          </TouchableHighlight>
        </Animated.View>
      </View>
    );
  }
```

1.  我们需要在这个类上再添加一个方法，这个方法是通过`View`元素的`onLayout`属性在布局改变时触发的。`setThreshold`将获取`row`的当前`width`并设置`threshold`。在这种情况下，我们将其设置为屏幕宽度的三分之一。这些值是必需的，以决定是否移除该项，如下所示：

```jsx
  setThreshold = (event) => {
    const { layout: { width } } = event.nativeEvent;
    this.threshold = width / 3;
    this.rowWidth = width;
  }
```

1.  最后，我们将为行添加一些样式，如下所示：

```jsx
const styles = StyleSheet.create({
  row: {
    backgroundColor: '#ecf0f1',
    borderBottomWidth: 1,
    borderColor: '#ecf0f1',
    flexDirection: 'row',
  },
  pan: {
    flex: 1,
  },
  info: {
    backgroundColor: '#fff',
    paddingBottom: 20,
    paddingLeft: 10,
    paddingTop: 20,
  },
});
```

1.  最终的应用程序应该看起来像这个屏幕截图：

![](img/55bfe95b-94b3-4398-8c7e-660e00cd7dad.png)

# 它是如何工作的...

在*步骤 5*中，我们在`state`上定义了`swiping`属性。这个属性只是一个布尔值，当拖动开始时设置为`true`，当完成时设置为`false`。我们需要这个信息来锁定列表在拖动项目时的垂直滚动。

在*步骤 7*中，我们定义了列表中每行的内容。`onDragStart`属性接收`handleToggleSwipe`方法，当拖动开始时将执行该方法。当拖动完成时，我们也将执行相同的方法。

在同一步骤中，我们还将`handleRemoveContact`方法发送给每个项目。顾名思义，当用户将其滑出时，我们将从列表中移除当前项目。

在*步骤 11*中，我们为项目组件定义了`defaultProps`和`state`。在过去的示例中，我们一直使用单个值来创建动画，但是在这种情况下，我们需要处理*x*和*y*坐标，所以我们需要一个`Animated.ValueXY`的实例。在内部，这个类处理两个`Animated.Value`实例，因此 API 几乎与我们之前看到的那些相同。

在*步骤 12*中，创建了`PanResponder`。React Native 中的手势系统，就像浏览器中的事件系统一样，在触摸事件时处理手势分为两个阶段：捕获和冒泡。在我们的情况下，我们需要使用捕获阶段来确定当前事件是按压项目还是尝试拖动它。`onMoveShouldSetPanResponderCapture`将捕获事件。然后，我们需要通过返回`true`或`false`来决定是否拖动该项。

`onPanResponderMove`属性将在每一帧从动画中获取值，这些值将被应用于`state`中的`pan`对象。我们需要使用`Animated.event`来访问每一帧的动画值。在这种情况下，我们只需要`x`值。稍后，我们将使用这个值来运行不同的动画，将元素返回到其原始位置或将其从屏幕上移除。

当用户释放物品时，`onPanResponderRelease`函数将被执行。如果由于任何其他原因，拖动被中断，将执行`onPanResponderTerminate`。

在*步骤 13*中，我们需要检查当前事件是简单的按压还是拖动。我们可以通过检查*x*轴上的增量来做到这一点。如果触摸事件移动了超过两个像素，那么用户正在尝试拖动物品，否则，他们正在尝试按下按钮。我们将差异评估为绝对数，因为移动可能是从左到右或从右到左，我们希望适应这两种移动。

在*步骤 14*中，我们需要获取物品相对于设备宽度移动的距离。如果这个距离低于我们在`setThreshold`中定义的阈值，那么我们需要移除这些物品。我们为每个动画定义了`config`对象，否则将返回物品到原始位置。但是，如果我们需要移除物品，我们会检查方向并相应地设置配置。

在*步骤 16*中，我们定义了 JSX。我们在`Animated.View`上设置我们想要动画的样式。在这种情况下，它是`left`属性，但是我们可以从我们在`state.pan`中存储的`Animated.ValueXY`实例中调用`getLayout`方法，而不是手动创建对象，该方法返回具有其现有值的 top 和 left 属性。

在同一步骤中，我们还通过展开`this.panResponder.panHandlers`来为`Animated.View`设置事件处理程序，使用展开运算符将我们在前面步骤中定义的拖动配置绑定到`Animated.View`。

我们还定义了对`props`中的`onPress`回调的调用，传入当前的`contact`信息。

# 另请参阅

您可以在以下网址找到`PanResponder` API 文档：

[`facebook.github.io/react-native/docs/panresponder.html`](https://facebook.github.io/react-native/docs/panresponder.html)

# 创建一个 Facebook 反应小部件

在这个食谱中，我们将创建一个模拟 Facebook 反应小部件的组件。我们将有一个喜欢按钮图像，当按下时，将显示五个图标。图标行将使用交错的滑入动画，同时从`0`增加到`1`的不透明度。

# 准备工作

让我们创建一个名为`facebook-widget`的空应用程序。

我们需要一些图片来显示一个假时间线。一些你的猫的照片就可以了，或者你可以使用 GitHub 上相应存储库中包含的猫的图片（[`github.com/warlyware/react-native-cookbook/tree/master/chapter-7/facebook-widget`](https://github.com/warlyware/react-native-cookbook/tree/master/chapter-7/facebook-widget)）。我们还需要五个图标来显示五种反应，比如，生气、笑、心、惊讶，这些也可以在相应的存储库中找到。

首先，我们将在空应用程序中创建两个 JavaScript 文件：`Reactions/index.js`和`Reactions/Icon.js`。我们需要将猫的图片复制到应用程序根目录下的`images/`文件夹中，反应图标应放置在`Reactions/images`中。

# 如何做...

1.  我们将在`App`类上创建一个假的 Facebook 时间线。让我们首先导入依赖项，如下所示：

```jsx
import React from 'react';
import {
  Dimensions,
  Image,
  Text,
  ScrollView,
  StyleSheet,
  SafeAreaView,
} from 'react-native';
import Reactions from './Reactions';
```

1.  我们需要导入一些图片来在我们的时间线中渲染。这一步中的 JSX 非常简单：只是一个`toolbar`，一个带有两个`Image`和两个`Reaction`组件的`ScrollView`，如下所示：

```jsx
const image1 = require('./images/01.jpg');
const image2 = require('./images/02.jpg');
const { width } = Dimensions.get('window');

const App = () => (
  <SafeAreaView style={styles.main}>
    <Text style={styles.toolbar}>Reactions</Text>
    <ScrollView style={styles.content}>
      <Image source={image1} style={styles.image} resizeMode="cover" />
      <Reactions />
      <Image source={image2} style={styles.image} resizeMode="cover" />
      <Reactions />
    </ScrollView>
  </SafeAreaView>
);

export default App;
```

1.  我们需要为这个组件添加一些基本的样式，如下所示：

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
    flex: 1,
  },
  image: {
    width,
    height: 300,
  },
});
```

1.  我们准备开始工作在这个食谱的`Reactions`组件。让我们首先导入依赖项，如下所示。我们将在后续步骤中构建导入的`Icon`组件：

```jsx
import React, { Component } from 'react';
import {
  Image,
  Text,
  TouchableOpacity,
  StyleSheet,
  View,
} from 'react-native';
import Icon from './Icon';
```

1.  让我们定义`defaultProps`和初始`state`。我们还需要要求`like`图标图片以在屏幕上显示它，如下所示：

```jsx
const image = require('./images/like.png');

export default class Reactions extends Component {
  static defaultProps = {
    icons: [
      'like', 'heart', 'angry', 'laughing', 'surprised',
    ],
  };

  state = {
    show: false,
    selected: '',
  };

  // Defined at later steps 
}
```

1.  让我们定义两种方法：一种是将`state`的选定值设置为选定的`reaction`，另一种是切换`state`的`show`值以相应地显示或隐藏反应行，如下所示：

```jsx
  onSelectReaction = (reaction) => {
    this.setState({
      selected: reaction,
    });
    this.toggleReactions();
  }

  toggleReactions = () => {
    this.setState({
      show: !this.state.show,
    });
  };
```

1.  我们将为此组件定义`render`方法。我们将显示一张图片，当按下时，将调用我们之前定义的`toggleReactions`方法，如下所示：

```jsx
  render() {
    const { style } = this.props;
    const { selected } = this.state;

    return (
      <View style={[style, styles.container]}>
        <TouchableOpacity onPress={this.toggleReactions}>
          <Image source={image} style={styles.icon} />
        </TouchableOpacity>
        <Text>{selected}</Text>
        {this.renderReactions()}
      </View>
    );
  }
```

1.  在这一步中，您会注意到我们正在调用`renderReactions`方法。接下来，我们将渲染用户按下主反应按钮时要显示的所有图标，如下所示：

```jsx
  renderReactions() {
    const { icons } = this.props;
    if (this.state.show) {
      return (
        <View style={styles.reactions}>
        { icons.map((name, index) => (
            <Icon
              key={index}
              name={name}
              delay={index * 100}
              index={index}
              onPress={this.onSelectReaction}
            />
          ))
        }
        </View>
      );
    }
  }
```

1.  我们需要为这个组件设置 `styles`。我们将为反应图标图像设置大小并定义一些填充。`reactions` 容器的高度将为 `0`，因为图标将浮动，我们不希望添加任何额外的空间：

```jsx
const styles = StyleSheet.create({
  container: {
    padding: 10,
  },
  icon: {
    width: 30,
    height: 30,
  },
  reactions: {
    flexDirection: 'row',
    height: 0,
  },
});
```

1.  `Icon` 组件目前缺失，所以如果我们尝试在这一点上运行我们的应用程序，它将失败。让我们通过打开 `Reactions/Icon.js` 文件并添加组件的导入来构建这个组件，如下所示：

```jsx
import React, { Component } from 'react';
import {
  Animated,
  Dimensions,
  Easing,
  Image,
  StyleSheet,
  TouchableOpacity,
  View,
} from 'react-native';
```

1.  让我们定义我们将要使用的图标。我们将使用一个对象来存储图标，这样我们可以通过键名轻松检索到每个图像，如下所示：

```jsx
const icons = {
  angry: require('./images/angry.png'),
  heart: require('./images/heart.png'),
  laughing: require('./images/laughing.png'),
  like: require('./images/like.png'),
  surprised: require('./images/surprised.png'),
};
```

1.  现在我们应该为这个组件定义 `defaultProps`。我们不需要定义初始状态：

```jsx
export default class Icon extends Component {
  static defaultProps = {
    delay: 0,
    onPress: () => {},
  };

}
```

1.  图标应该通过动画出现在屏幕上，所以当组件挂载时，我们需要创建并运行动画，如下所示：

```jsx
  componentWillMount() {
    this.animatedValue = new Animated.Value(0);
  }

  componentDidMount() {
    const { delay } = this.props;

    Animated.timing(
      this.animatedValue,
      {
        toValue: 1,
        duration: 200,
        easing: Easing.elastic(1),
        delay,
      }
    ).start();
  }
```

1.  当图标被按下时，我们需要执行 `onPress` 回调来通知父组件已选择了一个反应。我们将反应的名称作为参数发送，如下所示：

```jsx
  onPressIcon = () => {
    const { onPress, name } = this.props;
    onPress(name);
  }
```

1.  拼图的最后一块是 `render` 方法，我们将在这个组件中定义 JSX，如下所示：

```jsx
  render() {
    const { name, index, onPress } = this.props;
    const left = index * 50;
    const top = this.animatedValue.interpolate({
      inputRange: [0, 1],
      outputRange: [10, -95],
    });
    const opacity = this.animatedValue;

    return (
      <Animated.View
        style={[
          styles.icon,
          { top, left, opacity },
        ]}
      >
        <TouchableOpacity onPress={this.onPressIcon}>
          <Image source={icons[name]} style={styles.image} />
        </TouchableOpacity>
      </Animated.View>
    );
  }
```

1.  作为最后一步，我们将为每个 `icon` 添加样式。我们需要图标浮动，所以我们将 `position` 设置为 `absolute`，`width` 和 `height` 设置为 `40` 像素。在这个改变之后，我们应该能够运行我们的应用程序：

```jsx
  icon: {
    position: 'absolute',
  },
  image: {
    width: 40,
    height: 40,
  },
});
```

1.  最终的应用程序应该看起来像这个屏幕截图：

![](img/6667bd05-b197-4847-85db-88e66021c454.png)

# 它是如何工作的...

在 *步骤 2* 中，我们在时间线中定义了 `Reactions` 组件。现在，我们不专注于处理数据，而是专注于显示用户界面。因此，我们不会通过 `Reactions` 属性发送任何回调来获取所选值。

在 *步骤 5* 中，我们定义了 `defaultProps` 和初始 `state`。

我们的状态中有两个属性：

+   `show` 属性是一个布尔值。我们用它来在用户按下主按钮时切换反应图标。当为 `false` 时，我们隐藏反应，当为 `true` 时，我们运行动画来显示每个图标。

+   `selected` 包含当前的选择。每当选择新的反应时，我们将更新这个属性。

在 *步骤 8* 中，我们渲染图标。在这里，我们需要将图标的名称发送到每个创建的实例。我们还为每个图标发送了 100 毫秒的 `delay`，这将创建一个漂亮的交错动画。`onPress` 属性接收了 *步骤 6* 中定义的 `onSelectReaction` 方法，该方法在 `state` 上设置了所选的反应。

在*步骤 13*中，我们创建了动画。首先，我们使用`Animated.Value`助手定义了`animatedValue`变量，正如在之前的配方中提到的那样，这是负责在动画中每一帧中保存值的类。组件一旦挂载，我们就运行动画。动画的进度从`0`到`1`，持续时间为 200 毫秒，使用弹性缓动函数，并根据接收到的`delay`属性延迟动画。

在*步骤 15*中，我们为`Icon`组件定义了 JSX。在这里，我们对`top`和`opacity`属性进行动画处理。对于`top`属性，我们需要从`animatedValue`中插值出值，以便图标从其原始位置向上移动 95 像素。`opacity`属性所需的值从`0`到`1`，由于我们不需要插值任何内容来完成这一点，因此我们可以直接使用`animatedValue`。

`left`值是根据`index`计算的：我们只是将图标向前一个图标的左侧移动 50 像素，这样可以避免将图标全部渲染在同一个位置。

# 在全屏显示图像

在这个配方中，我们将创建一个图像时间轴。当用户按下任何图像时，它将在黑色背景下全屏显示图像。

我们将为背景使用不透明度动画，并将图像从其原始位置滑入。

# 准备工作

让我们创建一个名为`photo-viewer`的空白应用程序。

此外，我们还将创建`PostContainer/index.js`来显示时间轴中的每个图像，以及`PhotoViewer/index.js`来在全屏显示所选图像。

您可以使用此处配方存储库中托管在 GitHub 上的图像（[`github.com/warlyware/react-native-cookbook/tree/master/chapter-7/photo-viewer`](https://github.com/warlyware/react-native-cookbook/tree/master/chapter-7/photo-viewer)）中包含的图像，也可以使用自己的一些照片。将它们放在项目根目录中的`images`文件夹中。

# 如何做...

1.  我们将在`App`类中显示一个带有图像的时间轴。让我们导入所有依赖项，包括我们稍后将构建的另外两个组件，如下所示：

```jsx
import React, { Component } from 'react';
import {
  Dimensions,
  Image,
  Text,
  ScrollView,
  StyleSheet,
  SafeAreaView,
} from 'react-native';
import PostContainer from './PostContainer';
import PhotoViewer from './PhotoViewer';
```

1.  在这一步中，我们将定义要渲染的数据。这只是一个包含`title`和`image`的对象数组。

```jsx
const image1 = require('./images/01.jpg');
const image2 = require('./images/02.jpg');
const image3 = require('./images/03.jpg');
const image4 = require('./images/04.jpg');

const timeline = [
  { title: 'Enjoying the fireworks', image: image1 },
  { title: 'Climbing the Mount Fuji', image: image2 },
  { title: 'Check my last picture', image: image3 },
  { title: 'Sakuras are beautiful!', image: image4 },
];
```

1.  现在我们需要声明此组件的初始`state`。当按下任何图像时，我们将更新`selected`和`position`属性，如下所示：

```jsx
export default class App extends Component {
  state = {
    selected: null,
    position: null,
  };
  // Defined in following steps
}
```

1.  为了更新`state`，我们将声明两个方法：一个用于设置被按下的图像的值，另一个用于在查看器关闭时删除这些值：

```jsx
  showImage = (selected, position) => {
    this.setState({
      selected,
      position,
    });
  }

  closeViewer = () => {
    this.setState({
      selected: null,
      position: null,
    });
  }
```

1.  现在我们准备开始处理`render`方法。在这里，我们需要在`ScrollView`中渲染每个图像，以便列表可以滚动，如下所示：

```jsx
  render() {
    return (
      <SafeAreaView style={styles.main}>
        <Text style={styles.toolbar}>Timeline</Text>
        <ScrollView style={styles.content}>
        {
          timeline.map((post, index) =>
            <PostContainer key={index} post={post}
            onPress={this.showImage} />
          )
        }
        </ScrollView>
        {this.renderViewer()}
      </SafeAreaView>
    );
  }
```

1.  在上一步中，我们调用了`renderViewer`方法。在这里，我们只会在状态中有一个帖子`selected`时显示查看器组件。我们还会发送初始位置以开始动画和一个关闭查看器的回调，如下所示：

```jsx
  renderViewer() {
    const { selected, position } = this.state;

    if (selected) {
      return (
        <PhotoViewer
          post={selected}
          position={position}
          onClose={this.closeViewer}
        />
      );
    }
  }
```

1.  这个组件的样式非常简单，只有一些颜色和填充，如下所示：

```jsx
const styles = StyleSheet.create({
  main: {
    backgroundColor: '#ecf0f1',
    flex: 1,
  },
  toolbar: {
    backgroundColor: '#2c3e50',
    color: '#fff',
    fontSize: 22,
    padding: 20,
    textAlign: 'center',
  },
  content: {
    flex: 1,
  },
});
```

1.  时间轴已经完成，但是如果我们尝试运行我们的应用程序，它将失败。让我们开始处理`PostContainer`组件。我们将首先导入依赖项，如下所示：

```jsx
import React, { Component } from 'react';
import {
  Dimensions,
  Image,
  Text,
  TouchableOpacity,
  StyleSheet,
  View,
} from 'react-native';
```

1.  我们只需要两个`props`来定义这个组件。`post`属性将接收图像数据，`title`和`image`，`onPress`属性是一个回调，当图像被按下时我们将执行它，如下所示：

```jsx
const { width } = Dimensions.get('window');

export default class PostContainer extends Component {
  static defaultProps = {
    onPress: ()=> {},
  };
  // Defined on following steps
}
```

1.  这个组件将在`ScrollView`中。这意味着当用户开始滚动内容时，它的位置将会改变。当按下图像时，我们需要获取屏幕上的当前位置并将这些信息发送给父组件，如下所示：

```jsx
  onPressImage = (event) => {
    const { onPress, post } = this.props;
    this.refs.main.measure((fx, fy, width, height, pageX, pageY) => {
      onPress(post, {
        width,
        height,
        pageX,
        pageY,
      });
    });
  }
```

1.  现在是时候为这个组件定义 JSX 了。为了保持简单，我们只会渲染`image`和`title`：

```jsx
  render() {
    const { post: { image, title } } = this.props;

    return (
      <View style={styles.main} ref="main">
        <TouchableOpacity
           onPress={this.onPressImage}
           activeOpacity={0.9}
            >
          <Image
            source={image}
            style={styles.image}
            resizeMode="cover"
          />
        </TouchableOpacity>
        <Text style={styles.title}>{title}</Text>
      </View>
    );
  }
```

1.  和往常一样，我们需要为这个组件定义一些样式。我们将添加一些颜色和填充，如下所示：

```jsx
const styles = StyleSheet.create({
  main: {
    backgroundColor: '#fff',
    marginBottom: 30,
    paddingBottom: 10,
  },
  content: {
    flex: 1,
  },
  image: {
    width,
    height: 300,
  },
  title: {
    margin: 10,
    color: '#ccc',
  }
});
```

1.  如果现在运行应用程序，我们应该能够看到时间轴，但是如果我们按下任何图像，将会抛出错误。我们需要定义查看器，所以让我们打开`PhotoViewer/index.js`文件并导入依赖项：

```jsx
import React, { Component } from 'react';
import {
  Animated,
  Dimensions,
  Easing,
  Text,
  TouchableOpacity,
  StyleSheet,
} from 'react-native';
```

1.  让我们为这个组件定义`props`。为了将图像居中显示在屏幕上，我们需要知道当前设备的`height`：

```jsx
const { width, height } = Dimensions.get('window');

export default class PhotoViewer extends Component {
  static defaultProps = {
    onClose: () => {},
  };
  // Defined on following steps
}
```

1.  当显示这个组件时，我们希望运行两个动画，因此我们需要在组件挂载后初始化并运行动画。动画很简单：它只是在`400`毫秒内从`0`到`1`进行一些缓动，如下所示：

```jsx
  componentWillMount() {
    this.animatedValue = new Animated.Value(0);
  }

  componentDidMount() {
    Animated.timing(
      this.animatedValue,
      {
        toValue: 1,
        duration: 400,
        easing: Easing.in,
      }
    ).start();
  }
```

1.  当用户按下关闭按钮时，我们需要执行`onClose`回调来通知父组件需要移除这个组件，如下所示：

```jsx
  onPressBtn = () => {
    this.props.onClose();
  }
```

1.  我们将把`render`方法分为两步。首先，我们需要插入动画的值，如下所示：

```jsx
  render() {
    const { post: { image, title }, position } = this.props;
    const top = this.animatedValue.interpolate({
      inputRange: [0, 1],
      outputRange: [position.pageY, height/2 - position.height/2],
    });
    const opacity = this.animatedValue;
    // Defined on next step 
  } 
```

1.  我们只需要定义三个元素：`Animated.View`来动画显示背景，`Animated.Image`来显示图像，以及一个关闭按钮。我们将`opacity`样式设置为主视图，这将使图像背景从透明变为黑色。图像将同时滑入，产生一个很好的效果：

```jsx
// Defined on previous step
  render() {
    return (
      <Animated.View
        style={[
          styles.main,
          { opacity },
        ]}
      >
        <Animated.Image
          source={image}
          style={[
            styles.image,
            { top, opacity }
          ]}
        />
        <TouchableOpacity style={styles.closeBtn}
          onPress={this.onPressBtn}
        >
          <Text style={styles.closeBtnText}>X</Text>
        </TouchableOpacity>
      </Animated.View>
    );
  }
```

1.  我们几乎完成了！这个食谱中的最后一步是定义样式。我们需要将主容器的位置设置为绝对位置，以便图像位于其他所有内容的顶部。我们还将关闭按钮移动到屏幕的右上角，如下所示：

```jsx
const styles = StyleSheet.create({
  main: {
    backgroundColor: '#000',
    bottom: 0,
    left: 0,
    position: 'absolute',
    right: 0,
    top: 0,
  },
  image: {
    width,
    height: 300,
  },
  closeBtn: {
    position: 'absolute',
    top: 50,
    right: 20,
  },
  closeBtnText: {
    fontSize: 20,
    color: '#fff',
    fontWeight: 'bold',
  },
});
```

1.  最终的应用程序应该类似于以下截图：

![](img/83d6acab-c119-4b20-88a1-d986453c805f.png)

# 它是如何工作的...

在*步骤 4*中，我们在`state`中定义了两个属性：`selected`和`position`。`selected`属性保存了按下图像的图像数据，可以是*步骤 3*中定义的`timeline`对象中的任何一个。`position`属性将保存屏幕上的当前*y*坐标，稍后用于将图像从其原始位置动画到屏幕中心。

在*步骤 5*中，我们对`timeline`数组进行`map`操作，以渲染每个`post`。我们为每个 post 使用`PostContainer`元素，发送`post`信息，并使用`onPress`回调来设置按下的图像。

在*步骤 10*中，我们需要图像的当前位置。为了实现这一点，我们使用所需信息的组件的`measure`方法。该方法接收一个回调函数，并检索，除其他属性外，`width`、`height`和屏幕上的当前位置。

我们正在使用引用来访问在下一步的 JSX 中声明的组件。

在*步骤 11*中，我们声明了组件的 JSX。在主包装容器中，我们设置了`ref`属性，用于获取图像的当前位置。每当我们想要在当前类的任何方法中访问组件时，我们都使用引用。我们可以通过简单地设置`ref`属性并为任何组件分配一个名称来创建引用。

在*步骤 18*中，我们插值动画值以获得每一帧的正确顶部值。插值的输出将从图像的当前位置开始，并向屏幕中间进展。这样，根据值是负数还是正数，动画将从底部向顶部运行，或者反之。

我们不需要插值 `opacity`，因为当前的动画值已经从 `0` 到 `1`。

# 另请参阅

Refs 和 DOM 的深入解释可以在以下链接找到：

[`reactjs.org/docs/refs-and-the-dom.html`](https://reactjs.org/docs/refs-and-the-dom.html)。
