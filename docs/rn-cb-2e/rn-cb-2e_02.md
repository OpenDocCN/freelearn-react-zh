# 第二章：创建一个简单的 React Native 应用程序

在本章中，我们将涵盖以下内容：

+   向元素添加样式

+   使用图像模拟视频播放器

+   创建一个切换按钮

+   显示项目列表

+   使用 flexbox 创建布局

+   设置和使用导航

React Native 是一个快速增长的库。在过去的几年里，它在开源社区中变得非常受欢迎。几乎每隔一周就会有一个新版本发布，改进性能，添加新组件，或者提供对设备上新 API 的访问。

在本章中，我们将学习库中最常见的组件。为了逐步完成本章中的所有配方，我们将不得不创建一个新的应用程序，所以确保您的环境已经准备就绪。

# 向元素添加样式

我们有几个组件可供使用，但容器和文本是创建布局或其他组件最常见和有用的组件。在这个配方中，我们将看到如何使用容器和文本，但更重要的是我们将看到样式在 React Native 中是如何工作的。

# 准备工作

按照上一章的说明创建一个新应用程序。我们将把这个应用程序命名为`fake-music-player`。

在使用 Expo 创建新应用程序时，`App.js`文件中的`root`文件夹将添加少量样板代码。这将是您构建的任何 React Native 应用程序的起点。随时在每个配方的开头删除所有样板代码，因为所有代码（包括在`App.js`样板中使用的代码）都将被讨论。

# 如何做...

1.  在`App.js`文件中，我们将创建一个无状态组件。这个组件将模拟一个小型音乐播放器。它只会显示歌曲的名称和一个用来显示进度的条。第一步是导入我们的依赖项：

```jsx
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
```

1.  一旦我们导入了依赖项，我们就可以构建组件：

```jsx
export default class App extends React.Component {
  render() {
    const name = '01 - Blue Behind Green Bloches';

    return (
      <View style={styles.container}>
        <View style={styles.innerContainer} />
        <Text style={styles.title}>
          <Text style={styles.subtitle}>Playing:</Text> {name}
        </Text>
      </View>
    );
  }
}
```

1.  我们的组件已经准备好了，现在我们需要添加一些样式，以添加颜色和字体：

```jsx
const styles = StyleSheet.create({
  container: {
    margin: 10,
    marginTop: 100,
    backgroundColor: '#e67e22',
    borderRadius: 5,
  },
  innerContainer: {
    backgroundColor: '#d35400',
    height: 50,
    width: 150,
    borderTopLeftRadius: 5,
    borderBottomLeftRadius: 5,
  },
  title: {
    fontSize: 18,
    fontWeight: '200',
    color: '#fff',
    position: 'absolute',
    backgroundColor: 'transparent',
    top: 12,
    left: 10,
  },
  subtitle: {
    fontWeight: 'bold',
  },
});
```

1.  只要我们的模拟器和模拟器正在运行我们的应用程序，我们应该看到变化：

![](img/b811e149-433a-4eb0-89bf-57e9676bc390.png)

# 它是如何工作的...

在*步骤 1*中，我们包含了我们组件的依赖项。在这种情况下，我们使用了`View`，它是一个容器。如果您熟悉 Web 开发，`View`类似于`div`。我们可以在其他`View`内添加更多的`View`，`Text`，`List`，以及我们创建或从第三方库导入的任何其他自定义组件。

如果您熟悉 React，您会注意到，这是一个无状态组件，这意味着它没有任何状态；它是一个纯函数，不支持任何生命周期方法。

我们在组件中定义了一个`name`常量，但在实际应用中，这些数据应该来自 props。在返回中，我们定义了我们需要渲染组件的**JavaScript XML **（**JSX**），以及对样式的引用。

每个组件都有一个名为`style`的属性。该属性接收一个包含我们想要应用于给定组件的所有样式的对象。样式不会被子组件继承（除了`Text`组件），这意味着我们需要为每个组件设置单独的样式。

在*步骤 3*中，我们为我们的组件定义了样式。我们正在使用`StyleSheet` API 来创建所有样式。我们本可以使用包含样式的普通对象，但是通过使用`StyleSheet` API 而不是对象，我们可以获得一些性能优化，因为样式将被重用于每个渲染器，而不是在每次执行渲染方法时创建一个对象。

# 还有更多...

我想要引起您对*步骤 3*中`title`样式定义的注意。在这里，我们定义了一个名为`backgroundColor`的属性，并将`transparent`设置为其值。作为一个很好的练习，让我们注释掉这行代码并查看结果：

![](img/92ede04a-de11-4f51-865a-d278c011698c.png)

在 iOS 上，文本将具有橙色背景颜色，这可能不是我们真正想要在我们的 UI 中发生的事情。为了解决这个问题，我们需要将文本的背景颜色设置为透明。但问题是，为什么会发生这种情况？原因是 React Native 通过将颜色从父元素的背景颜色设置为文本添加了一些优化。这将提高渲染性能，因为渲染引擎不必计算文本每个字母周围的像素，渲染将更快地执行。

在将背景颜色设置为`transparent`时要仔细考虑。如果组件将频繁更新内容，特别是如果文本太长，可能会出现一些性能问题。

# 使用图像模仿视频播放器

图像是任何 UI 的重要组成部分，无论我们是用它们来显示图标、头像还是图片。在这个食谱中，我们将使用图像来创建一个模拟视频播放器。我们还将显示来自本地设备的图标和来自远程服务器（由 Flickr 托管）的大图像。

# 准备工作

为了按照这个食谱中的步骤，让我们创建一个新的应用程序。我们将把它命名为`fake-video-player`。

我们将在我们的应用程序中显示一些图像，以模仿视频播放器，所以您需要为您的应用程序准备相应的图像。我建议使用我在 GitHub 上的食谱存储库中下载的图标，网址为[`github.com/warlyware/react-native-cookbook/tree/master/chapter-2/fake-video-player/images`](https://github.com/warlyware/react-native-cookbook/tree/master/chapter-2/fake-video-player/images)。

# 如何做...

1.  我们要做的第一件事是在项目的根目录下创建一个名为`Images`的新文件夹。将您下载的图像添加到新文件夹中。

1.  在`App.js`文件中，我们包括了这个组件所需的所有依赖项：

```jsx
import React from 'react'; 
import { StyleSheet, View, Image } from 'react-native';
```

1.  我们需要`require`在我们的组件中显示的图像。通过在常量中定义它们，我们可以在不同的地方使用相同的图像：

```jsx
const playIcon = require('./images/play.png');
const volumeIcon = require('./images/sound.png');
const hdIcon = require('./images/hd-sign.png');
const fullScreenIcon = require('./images/full-screen.png');
const flower = require('./images/flower.jpg');
const remoteImage = { uri: `https://farm5.staticflickr.com/4702/24825836327_bb2e0fc39b_b.jpg` };
```

1.  我们将使用一个无状态组件来渲染 JSX。我们将使用在上一步中声明的所有图像。

```jsx
export default class App extends React.Component {
  render() {
    return (
      <View style={styles.appContainer}>
        <ImageBackground source={remoteImage} style=
         {styles.videoContainer} resizeMode="contain">
          <View style={styles.controlsContainer}>
            <Image source={volumeIcon} style={styles.icon} />
            <View style={styles.progress}>
              <View style={styles.progressBar} />
            </View>
            <Image source={hdIcon} style={styles.icon} />
            <Image source={fullScreenIcon} style={styles.icon} />
          </View>
        </ImageBackground>
      </View>
    );
  }
};
```

1.  一旦我们有了要渲染的元素，我们需要为每个元素定义样式：

```jsx
const styles = StyleSheet.create({
  flower: {
    flex: 1,
  },
  appContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  videoContainer: {
    backgroundColor: '#000',
    flexDirection: 'row',
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  controlsContainer: {
    padding: 10,
    backgroundColor: '#202020',
    flexDirection: 'row',
    alignItems: 'center',
    marginTop: 175,
  },
  icon: {
    tintColor: '#fff',
    height: 16,
    width: 16,
    marginLeft: 5,
    marginRight: 5,
  },
  progress: {
    backgroundColor: '#000',
    borderRadius: 7,
    flex: 1,
    height: 14,
    margin: 4,
  },
  progressBar: {
    backgroundColor: '#bf161c',
    borderRadius: 5,
    height: 10,
    margin: 2,
    paddingTop: 3,
    width: 80,
    alignItems: 'center',
    flexDirection: 'row',
  },
});
```

1.  我们完成了！现在，当您查看应用程序时，您应该看到类似以下的内容：

![](img/2570e533-9f2c-4a85-ae1d-6a02846bfa88.png)

# 它是如何工作的...

在*步骤 2*中，我们需要`Image`组件。这是负责从设备的本地文件系统或远程服务器上渲染图像的组件。

在*步骤 3*中，我们需要所有的图像。最好的做法是在组件外部需要图像，以便只需要一次。在每个渲染器上，React Native 将使用相同的图像。如果我们处理来自远程服务器的动态图像，那么我们需要在每个渲染器上需要它们。

`require`函数接受图像路径作为参数。路径是相对于我们类所在的文件夹的。对于远程图像，我们需要使用一个定义`uri`的对象来指定我们的文件在哪里。

在 *步骤 4* 中，声明了一个无状态组件。我们使用 `remoteImage` 作为我们应用程序的背景，通过一个 `ImageBackground` 元素，因为 `Image` 元素不能有子元素。这个元素类似于 CSS 中的 `background-url` 属性。

`Image` 的 `source` 属性接受一个对象来加载远程图像或所需文件的引用。非常重要的是要明确地要求我们想要使用的每个图像，因为当我们准备我们的应用程序进行分发时，图像将自动添加到捆绑包中。这就是我们应该避免做任何动态操作的原因，比如以下操作：

```jsx
const iconName = playing ? 'pause' : 'play'; 
const icon = require(iconName); 
```

上述代码不会将图像包含在最终的捆绑包中。因此，当尝试访问这些图像时，会出现错误。相反，我们应该将我们的代码重构为类似于这样的东西：

```jsx
const pause = require('pause'); 
const play = require('playing'); 
const icon = playing ? pause : play; 
```

这样，当准备我们的应用程序进行分发时，捆绑包将包括两个图像，并且我们可以在运行时动态决定显示哪个图像。

在 *步骤 5* 中，我们定义了样式。大多数属性都是不言自明的。尽管我们用于图标的图像是白色的，但我添加了 `tintColor` 属性来展示它如何用于着色图像。试一试！将 `tintColor` 改为 `#f00`，看看图标变成红色。

Flexbox 被用来对齐布局的不同部分。在 React Native 中，Flexbox 的行为基本上与 web 开发中的行为相同。我们将在本章后面的 *使用 flexbox 创建布局* 部分更多地讨论 flexbox，但是 flexbox 本身的复杂性超出了本书的范围。

# 创建一个切换按钮

按钮是每个应用程序中必不可少的 UI 组件。在这个部分中，我们将创建一个切换按钮，默认情况下将不被选中。当用户点击它时，我们将改变应用于按钮的样式，使其看起来被选中。

我们将学习如何检测点击事件，使用图像作为 UI，保持按钮的状态，并根据组件状态添加样式。

# 准备工作

让我们创建一个新的应用程序。我们将把它命名为 `toggle-button`。在这个部分中，我们将使用一张图片。您可以从 GitHub 上托管的相应存储库中下载这个部分的资产，网址为 [`github.com/warlyware/react-native-cookbook/tree/master/chapter-2/toggle-button/images`](https://github.com/warlyware/react-native-cookbook/tree/master/chapter-2/toggle-button/images)。

# 如何做...

1.  我们将在项目的根目录中创建一个名为`images`的新文件夹，并将心形图片添加到新文件夹中。

1.  让我们导入这个类的依赖项。

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  View,
  Image,
  Text,
  TouchableHighlight,
} from 'react-native';

const heartIcon = require('./images/heart.png');
```

1.  对于这个示例，我们需要跟踪按钮是否被按下。我们将使用一个带有`liked`布尔属性的`state`对象来实现这个目的。初始类应该是这样的：

```jsx
export default class App extends React.Component {
  state = {
    liked: false,
  };

  handleButtonPress = () => {
    // Defined in a later step
  }

  render() {
    // Defined in a later step
  }
}
```

1.  我们需要在`render`方法中定义我们新组件的内容。在这里，我们将定义`Image`按钮和其下方的`Text`元素：

```jsx
export default class App extends React.Component {
  state = {
    liked: false,
  };

  handleButtonPress = () => {
    // Defined in a later step
  }

  render() {
    return (
      <View style={styles.container}>
        <TouchableHighlight
          style={styles.button}
          underlayColor="#fefefe"
        >
          <Image
            source={heartIcon}
            style={styles.icon}
          />
        </TouchableHighlight>
        <Text style={styles.text}>Do you like this app?</Text>
      </View>
    );
  }
}
```

1.  让我们定义一些样式来设置尺寸、位置、边距、颜色等等：

```jsx
const styles = StyleSheet.create({
  container: {
    marginTop: 50,
    alignItems: 'center',
  },
  button: {
    borderRadius: 5,
    padding: 10,
  },
  icon: {
    width: 180,
    height: 180,
    tintColor: '#f1f1f1',
  },
  liked: {
    tintColor: '#e74c3c',
  },
  text: {
    marginTop: 20,
  },
});
```

1.  当我们在模拟器上运行项目时，我们应该看到类似以下截图的内容：

![](img/48d16278-db2d-4bd2-8c50-b6b59c3153f2.png)

1.  为了响应触摸事件，我们需要定义`handleButtonPress`函数的内容，并将其分配为`onPress`属性的回调函数：

```jsx
  handleButtonPress = () => {
    this.setState({
      liked: !this.state.liked,
    });
  }

  render() {
    return (
      <View style={styles.container}>
        <TouchableHighlight
          onPress={this.handleButtonPress}
          style={styles.button}
          underlayColor="#fefefe"
        >
          <Image
            source={heartIcon}
            style={styles.icon}
          />
        </TouchableHighlight>
        <Text style={styles.text}>Do you like this app?</Text>
      </View>
    );
  }
```

1.  如果我们测试我们的代码，我们不会看到 UI 上的任何变化，即使当我们按下按钮时组件上的状态发生变化。让我们在状态改变时为图片添加不同的颜色。这样，我们就能看到 UI 的响应：

```jsx
  render() {
 const likedStyles = this.state.liked ? styles.liked : undefined; 
    return (
      <View style={styles.container}>
        <TouchableHighlight
          onPress={this.handleButtonPress}
          style={styles.button}
          underlayColor="#fefefe"
        >
          <Image
            source={heartIcon}
 style={[styles.icon, likedStyles]}          />
        </TouchableHighlight>
        <Text style={styles.text}>Do you like this app?</Text>
      </View>
    );
  }
```

# 它是如何工作的...

在*步骤 2*中，我们导入了`TouchableHighlight`组件。这是负责处理触摸事件的组件。当用户触摸活动区域时，内容将根据我们设置的`underlayColor`值进行高亮显示。

在*步骤 3*中，我们定义了`Component`的状态。在这种情况下，状态只有一个属性，但我们可以根据需要添加多个属性。在第三章中，*实现复杂用户界面-第一部分*，我们将看到更多关于在更复杂场景中处理状态的示例。

在*步骤 6*中，我们使用`setState`方法来改变`liked`属性的值。这个方法是从我们正在扩展的`Component`类继承而来的。

在*步骤 7*中，基于`liked`属性的当前状态，我们使用样式将图片的颜色设置为红色，或者返回`undefined`以避免应用任何样式。当将样式分配给`Image`组件时，我们使用数组来分配多个对象。这非常方便，因为组件将所有样式合并为一个单一对象。具有最高索引的对象将覆盖数组中具有最低对象索引的属性：

![](img/7af91424-5d64-4f7d-aa3e-72a9c2e7b3e1.png)

# 还有更多...

在实际应用中，我们将使用多个按钮，有时带有左对齐的图标，标签，不同的大小，颜色等。强烈建议创建一个可重用的组件，以避免在整个应用程序中重复编写代码。在第三章，*实现复杂用户界面-第一部分*中，我们将创建一个按钮组件来处理其中一些情况。

# 显示项目列表

列表随处可见：用户历史记录中的订单列表，商店中可用商品的列表，要播放的歌曲列表。几乎任何应用程序都需要在列表中显示某种信息。

对于这个示例，我们将在`list`组件中显示多个项目。我们将定义一个带有一些数据的 JSON 文件，然后使用简单的`require`加载此文件，最后使用漂亮但简单的布局渲染每个项目。

# 准备工作

让我们从创建一个空应用程序开始。我们将把这个应用程序命名为`list-items`。我们需要一个图标来显示在每个项目上。获取图像的最简单方法是从托管在 GitHub 上的此示例的存储库中下载它们：[`github.com/warlyware/react-native-cookbook/tree/master/chapter-2/list-items/images`](https://github.com/warlyware/react-native-cookbook/tree/master/chapter-2/list-items/images)。

# 如何做...

1.  我们将首先创建一个`images`文件夹，并将`basket.png`添加到其中。还要在项目的根目录中创建一个名为`sales.json`的空文件。

1.  在`sales.json`文件中，我们将定义要在列表中显示的数据。以下是一些示例数据：

```jsx
[
  {
    "items": 5,
    "address": "140 Broadway, New York, NY 11101",
    "total": 38,
    "date": "May 15, 2016"
  }
]
```

1.  为了避免使本书的页面混乱，我只定义了一个记录，但请继续向数组中添加更多内容。多次复制和粘贴相同的对象将起作用。此外，您可以更改数据中的一些值，以便每个项目在 UI 中显示唯一的数据。

1.  在我们的`App.js`文件中，让我们导入我们需要的依赖项：

```jsx
import React, { Component } from 'react'; import {
  StyleSheet,
  View, 
  ListView, 
  Image, 
  Text,
} from 'react-native'; 
import data from './sales.json'; 

const basketIcon = require('./images/basket.png');
```

1.  现在，我们需要创建用于渲染项目列表的类。我们将在状态中保留销售数据；这样，我们可以轻松地插入或删除元素：

```jsx
export default class App extends React.Component {
  constructor(props) {
    super(props);
    const dataSource = new ListView.DataSource({
      rowHasChanged: (r1, r2) => r1 !== r2
    });

    this.state = {
      dataSource: dataSource.cloneWithRows(data),
    };
  }

  renderRow(record) {
    // Defined in a later step
  }

  render() {
    // Defined in a later step
  }
}
```

1.  在`render`方法中，我们需要定义`ListView`组件，并使用`renderRow`方法来渲染每个项目。`dataSource`属性定义了我们将在列表上渲染的元素数组：

```jsx
render() {
  return (
    <View style={styles.mainContainer}>
      <Text style={styles.title}>Sales</Text>
      <ListView dataSource={this.state.dataSource} renderRow={this.renderRow} />
    </View>
  );
}
```

1.  现在，我们可以定义`renderRow`的内容。这个方法接收包含我们需要的所有信息的每个对象。我们将在三列中显示数据。在第一列中，我们将显示一个图标；在第二列中，我们将显示每个销售的物品数量和订单将发货的地址；第三列将显示日期和总计：

```jsx
    return ( 
      <View style={styles.row}> 
        <View style={styles.iconContainer}> 
          <Image source={basketIcon} style={styles.icon} /> 
        </View> 
        <View style={styles.info}> 
          <Text style={styles.items}>{record.items} Items</Text> 
          <Text style={styles.address}>{record.address}</Text> 
        </View> 
        <View style={styles.total}> 
          <Text style={styles.date}>{record.date}</Text> 
          <Text style={styles.price}>${record.total}</Text> 
        </View> 
      </View> 
    ); 
```

1.  一旦我们定义了 JSX，就该添加样式了。首先，我们将为主容器、标题和行容器定义颜色、边距、填充等样式。为了为每一行创建三列，我们需要使用`flexDirection: 'row'`属性。我们将在本章后面的*使用 flexbox 创建布局*中更多了解这个属性：

```jsx
const styles = StyleSheet.create({
  mainContainer: {
    flex: 1,
    backgroundColor: '#fff',
  },
  title: {
    backgroundColor: '#0f1b29',
    color: '#fff',
    fontSize: 18,
    fontWeight: 'bold',
    padding: 10,
    paddingTop: 40,
    textAlign: 'center',
  },
  row: {
    borderColor: '#f1f1f1',
    borderBottomWidth: 1,
    flexDirection: 'row',
    marginLeft: 10,
    marginRight: 10,
    paddingTop: 20,
    paddingBottom: 20,
  },
});
```

1.  如果我们刷新模拟器，应该看到类似于以下截图的东西：

![](img/0eb4c14f-0cae-4506-b78e-c90f26015e80.png)

1.  现在，在`StyleSheet`定义内部，让我们为图标添加样式。我们将添加一个黄色的圆作为背景，并将图标的颜色改为白色：

```jsx
  iconContainer: {
    alignItems: 'center',
    backgroundColor: '#feb401',
    borderColor: '#feaf12',
    borderRadius: 25,
    borderWidth: 1,
    justifyContent: 'center',
    height: 50,
    width: 50,
  },
  icon: {
    tintColor: '#fff',
    height: 22,
    width: 22,
  },
```

1.  在这个改变之后，我们将在每一行的左侧看到一个漂亮的图标，就像下面的截图所示：

![](img/24055416-5280-4b11-b86c-16713a744c15.png)

1.  最后，我们将为文本添加样式。我们需要设置`color`、`size`、`fontWeight`、`padding`和其他一些属性：

```jsx
  info: { 
    flex: 1, 
    paddingLeft: 25, 
    paddingRight: 25, 
  }, 
  items: { 
    fontWeight: 'bold', 
    fontSize: 16, 
    marginBottom: 5, 
  }, 
  address: { 
    color: '#ccc', 
    fontSize: 14, 
  }, 
  total: { 
    width: 80, 
  }, 
  date: { 
    fontSize: 12, 
    marginBottom: 5, 
  }, 
  price: { 
    color: '#1cad61', 
    fontSize: 25, 
    fontWeight: 'bold', 
  }  
```

1.  最终结果应该类似于以下截图：

![](img/3d839666-d353-4e9e-ba3a-320e4fd5f698.png)

# 它是如何工作的...

在*步骤 5*中，我们创建了数据源并向状态添加了数据。`ListView.DataSource`类实现了`ListView`组件的性能数据处理。`rowHasChanged`属性是必需的，它应该是一个比较下一个元素的函数。在我们的情况下，如果变化与当前数据不同，表示为`(r1, r2) => r1 !== r2`，那么 React Native 将知道如何响应并重新渲染 UI。

在用数据填充数据源时，我们需要调用`cloneWithRows`方法并发送一个记录数组。

如果我们想添加更多数据，我们应该再次使用包含先前和新数据的数组调用`cloneWithRows`方法。数据源将确保计算差异并根据需要重新渲染列表。

在*步骤 7*中，我们定义了渲染列表的 JSX。列表只需要两个属性：我们已经从*步骤 6*中得到的数据源和`renderRow`。

`renderRow`属性接受一个函数作为值。这个函数需要返回每一行的 JSX。

# 还有更多...

我们使用 flexbox 创建了一个简单的布局；但是，在本章中还有另一个教程，我们将更详细地介绍如何使用 flexbox。

一旦我们有了我们的列表，很有可能我们需要查看每个订单的详细信息。您可以使用`TouchableHighlight`组件作为每行的主容器，所以继续尝试一下。如果您不确定如何使用`TouchableHighlight`组件，请参阅本章早期的*创建切换按钮*教程。

# 使用 flexbox 创建布局

在这个教程中，我们将学习有关 flexbox 的知识。在本章的先前教程中，我们一直在使用 flexbox 来创建布局，但在这个教程中，我们将专注于我们可以使用的属性，通过重新创建 App Store 上名为*Nominazer*的随机名称生成应用程序的布局（[`itunes.apple.com/us/app/nominazer/id765422087?mt=8`](https://itunes.apple.com/us/app/nominazer/id765422087?mt=8)）。

在 React Native 中使用 flexbox 基本上与在 CSS 中使用 flexbox 相同。这意味着如果您习惯于使用 flexbox 布局开发网站，那么您已经知道如何在 React Native 中创建布局！这个练习将涵盖在 React Native 中使用 flexbox 的基础知识，但是要查看您可以使用的所有布局属性的列表，请参考布局属性的文档（[`facebook.github.io/react-native/docs/layout-props.html`](https://facebook.github.io/react-native/docs/layout-props.html)）。

# 准备工作

让我们从创建一个新的空白应用程序开始。我们将其命名为`flexbox-layout`。

# 如何做...

1.  在`App.js`中，让我们导入我们应用程序所需的依赖项：

```jsx
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
```

1.  我们的应用程序只需要一个`render`方法，因为我们正在构建一个静态布局。渲染的布局包括一个容器`View`元素和应用程序每个彩色部分的三个子`View`元素。

```jsx
export default class App extends React.Component { 
 render() { 
  return ( 
    <View style={styles.container}> 
      <View style={styles.topSection}> </View> 
       <View style={styles.middleSection}></View> 
       <View style={styles.bottomSection}></View> 
    </View> ); 
  } 
 }
```

1.  接下来，我们可以开始添加我们的样式。我们将添加的第一个样式将应用于包裹整个应用程序的`View`元素。将`flex`属性设置为`1`将导致所有子元素填充所有空白空间：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
  }
});
```

1.  现在，我们可以为三个子`View`元素添加样式。每个部分都应用了`flexGrow`属性，这决定了每个元素应该占用多少可用空间。`topSection`和`bottomSection`都设置为`3`，所以它们将占用相同的空间。由于`middleSection`的`flexGrow`属性设置为`1`，这个元素将占用`topSection`和`bottomSection`占用空间的三分之一：

```jsx
  topSection: {
    flexGrow: 3,
    backgroundColor: '#5BC2C1',
  },
  middleSection: {
    flexGrow: 1,
    backgroundColor: '#FFF',
  },
  bottomSection: {
    flexGrow: 3,
    backgroundColor: '#FD909E',
  },
```

1.  如果我们在模拟器中打开我们的应用程序，我们应该已经能够看到基本布局正在形成：

![](img/f05a61bb-3f96-4511-b497-9d169b08efe0.png)

1.  在这里，我们可以在*步骤 2*中创建的三个子`View`元素中的每一个添加一个`Text`元素。请注意，新增的代码已经被突出显示：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <View style={styles.topSection}>
 <Text style={styles.topSectionText}>
            4  N A M E S
          </Text>
        </View>
        <View style={styles.middleSection}>
 <Text style={styles.middleSectionText}>
 I P S U M
 </Text>
        </View>
        <View style={styles.bottomSection}>
 <Text style={styles.bottomSectionText}>
            C O M
          </Text>
        </View>
      </View>
    );
  }
```

1.  每个部分的文本默认显示在该部分的左上角。我们可以使用 flexbox 来使每个元素按照期望的位置进行对齐和排列。所有三个子`View`元素的`alignItems` flex 属性都设置为`'center'`，这将导致每个元素的子元素沿着*x*轴居中。`justifyContent`在中间和底部部分上使用，定义了子元素沿着*y*轴应该如何对齐：

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
  middleSection: {
    flexGrow: 1,
    backgroundColor: '#FFF',
 justifyContent: 'center',
    alignItems: 'center',
  },
  bottomSection: {
    flexGrow: 3,
    backgroundColor: '#FD909E',
 alignItems: 'center',
    justifyContent: 'flex-end'
  }
});
```

1.  唯一剩下的就是为`Text`元素添加基本样式，增加`fontSize`、`fontWeight`和所需的`margin`：

```jsx
  topSectionText: {
    fontWeight: 'bold',
    marginTop: 50
  },
  middleSectionText: {
    fontSize: 30,
    fontWeight: 'bold'
  },
  bottomSectionText: {
    fontWeight: 'bold',
    marginBottom: 30
  }
```

1.  如果我们在模拟器中打开我们的应用程序，我们应该能够看到我们完成的布局：

![](img/9ce98ba3-6356-4317-bd43-74a4596c41c6.png)

# 工作原理...

我们的应用程序看起来非常不错，而且通过使用 flexbox 很容易实现。我们通过将`flexGrow`属性分别设置为`3`、`1`和`3`来创建了三个不同的部分，这使得顶部和底部部分的垂直大小相等，而中间部分是顶部和底部部分的三分之一。

在使用 flexbox 时，我们有两个方向来布置子内容，`row`和`column`：

+   `row`：这允许我们水平排列容器的子元素。

+   `column`：这允许我们垂直排列容器的子元素。这是 React Native 中的默认方向。

当我们像对容器`View`元素所做的那样设置`flex: 1`时，我们告诉该元素占用所有可用空间。如果我们移除`flex: 1`或将`flex`设置为`0`，我们会看到布局在自身内部收缩，因为容器不再在所有空白空间中伸展：

![](img/c28d05ad-a247-4f43-9a90-77d41db556a0.png)

Flexbox 也非常适合支持不同的屏幕分辨率。即使不同设备可能有不同的分辨率，我们可以确保一致的布局，使其在任何设备上都看起来很好。

# 还有更多...

React Native 中的 flexbox 工作方式与 CSS 中的工作方式有一些不同。首先，在 CSS 中，默认的`flexDirection`属性是`row`，而在 React Native 中，默认的`flexDirection`属性是`column`。

`flex`属性在 React Native 中的行为也有些不同。与将`flex`设置为字符串值不同，它可以设置为正整数、`0`或`-1`。正如官方的 React Native 文档所述：

当 flex 为正数时，它使组件具有灵活性，并且其大小将与其 flex 值成比例。因此，将 flex 设置为 2 的组件将占据比将 flex 设置为 1 的组件多一倍的空间。当 flex 为 0 时，组件的大小根据宽度和高度确定，是不灵活的。当 flex 为-1 时，组件通常根据宽度和高度确定大小。但是，如果空间不足，组件将收缩到其最小宽度和最小高度。

关于 flexbox 还有很多要讨论的，但目前我们已经有所了解。在第三章 *实现复杂用户界面-第一部分*中，我们将学习更多关于布局的知识。我们将学习更多关于布局，并创建一个使用更多可用布局属性的复杂布局。

# 另请参阅

+   React Native 布局属性文档([`facebook.github.io/react-native/docs/layout-props.html`](https://facebook.github.io/react-native/docs/layout-props.html))

+   React Native 文本样式属性文档([`facebook.github.io/react-native/docs/text-style-props.html`](https://facebook.github.io/react-native/docs/text-style-props.html))

+   Yoga ([`github.com/facebook/yoga`](https://github.com/facebook/yoga))——React Native 使用的 Facebook 的 Flexbox 实现。

+   一篇优秀的 Stack Overflow 帖子介绍了 React Native 弹性属性的工作原理和示例-[`stackoverflow.com/questions/43143258/flex-vs-flexgrow-vs-flexshrink-vs-flexbasis-in-react-native`](https://stackoverflow.com/questions/43143258/flex-vs-flexgrow-vs-flexshrink-vs-flexbasis-in-react-native)

# 设置和使用导航

对于任何具有多个视图的应用程序，导航系统至关重要。导航在应用程序开发中是如此普遍，以至于 Expo 在创建新应用程序时提供了两个模板：**空白**或**标签导航**。这个教程是基于 Expo 提供的非常简化的标签导航应用程序模板。我们仍将从一个空白应用程序开始，并从头开始构建我们的基本标签导航应用程序，以更好地理解所有必需的部分。完成此教程后，我鼓励您使用标签导航模板开始一个新应用程序，以查看我们将在后面章节中涵盖的一些更高级的功能，包括推送通知和堆栈导航。

# 准备工作

让我们继续创建一个名为`simple-navigation`的新空白应用程序。我们还需要一个第三方包来处理我们的导航。我们将使用`react-navigation`包的 1.5.9 版本。使用此包的更新版本将无法正确使用此代码，因为该包的 API 最近经历了重大变化。在终端中，转到新项目的根目录，并使用以下命令安装此包：

```jsx
yarn add react-navigation@1.5.9
```

这就是我们需要的所有设置。让我们开始构建吧！

# 如何做到...

1.  在`App.js`文件中，让我们导入我们的依赖项：

```jsx
import  React  from  'react'; import { StyleSheet, View } from  'react-native';
```

1.  这个应用程序的`App`组件将非常简单。我们只需要一个带有渲染我们应用程序容器的`App`类和一个`render`函数。我们还将添加填充窗口和添加白色背景的样式：

```jsx
export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
      </View>
   );
 }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  }
});
```

1.  `App.js`的下一步是导入并使用`MainTabNavigator`组件，这是我们将在*步骤 4*中创建的新组件：

```jsx
React.Component {
  render() {
    return (
      <View style={styles.container}>
        <MainTabNavigator />
      </View>
    );
  }
}
```

1.  我们需要为我们的`MainTabNavigator`组件创建一个新文件。让我们在项目的根目录中创建一个名为`navigation`的新文件夹。在这个新文件夹中，我们将为我们的导航组件创建`MainTabNavigator.js`。

1.  在`MainTabNavigator.js`中，我们可以导入我们需要的所有导航依赖项。这些依赖项包括三个屏幕（`HomeScreen`、`LinksScreen`和`SettingsScreen`）。我们将在后面的步骤中添加这些屏幕：

```jsx
import React from 'react';
import { Ionicons } from '@expo/vector-icons';
import { TabNavigator, TabBarBottom } from 'react-navigation';

import HomeScreen from '../screens/HomeScreen';
import LinksScreen from '../screens/LinksScreen';
import SettingsScreen from '../screens/SettingsScreen';
```

1.  我们的导航组件将使用`react-navigation`提供的`TabNavigator`方法来定义应用程序的路由和导航。`TabNavigator`接受两个参数：一个`RouteConfig`对象来定义每个路由，以及一个`TabNavigatorConfig`对象来定义我们的`TabNavigator`组件的选项：

```jsx
export default TabNavigator({
    // RouteConfig, defined in step 7.
}, {
    // TabNavigatorConfig, defined in steps 8 and 9.
});
```

1.  首先，我们将定义`RouteConfig`对象，它将为我们的应用程序创建一个路由映射。`RouteConfig`对象中的每个键都作为路由的名称。我们为每个路由的屏幕属性设置为我们希望在该路由上显示的相应屏幕组件：

```jsx
export default TabNavigator({
 Home: {
    screen: HomeScreen,
  },
  Links: {
    screen: LinksScreen,
  },
  Settings: {
    screen: SettingsScreen,
  },
}, {
  // TabNavigatorConfig, defined in steps 8 and 9\. 
});
```

1.  `TabNavigatorConfig`还有一些内容。我们将通过将`react-navigation`提供的`TabBarBottom`组件传递给`tabBarComponent`属性来声明我们想要使用什么样的选项卡栏（在本例中，是设计用于屏幕底部的选项卡栏）。`tabBarPosition`定义了栏是在屏幕顶部还是底部。`animationEnabled`指定了过渡是否是动画的，`swipeEnabled`声明了视图是否可以通过滑动来改变：

```jsx
export default TabNavigator({
    // Route Config, defined in step 7\. 
}, {
  navigationOptions: ({ navigation }) => ({
    // navigationOptions, defined in step 9.
  }),
  tabBarComponent: TabBarBottom,
  tabBarPosition: 'bottom',
  animationEnabled: false,
  swipeEnabled: false,
});
```

1.  在`TabNavigatorConfig`对象的`navigationOptions`属性中，我们将通过声明一个函数来为每个路由定义动态的`navigationOptions`，该函数接受当前路由/屏幕的导航 prop。我们可以使用此函数来决定选项卡栏如何针对每个路由/屏幕进行操作，因为它被设计为返回一个为适当屏幕设置`navigationOptions`的对象。我们将使用此模式来定义每个路由的`tabBarIcon`属性的外观：

```jsx
  navigationOptions: ({ navigation }) => ({
    tabBarIcon: ({ focused }) => {
      // Defined in step 10
    },
  }),
```

1.  `tabBarIcon`属性设置为一个函数，其参数是当前路由的 props。我们将使用`focused`属性来决定是渲染有颜色的图标还是轮廓图标，这取决于当前路由。我们通过`navigation.state`从导航 prop 中获取`routeName`，为我们的三条路线定义图标，并返回适当路线的渲染图标。我们将使用 Expo 提供的`Ionicons`组件来创建每个图标，并根据图标的路线是否`focused`来定义图标的颜色：

```jsx
  navigationOptions: ({ navigation }) => ({
    tabBarIcon: ({ focused }) => {
 const { routeName } = navigation.state;

      let iconName;
      switch (routeName) {
        case 'Home':
          iconName = `ios-information-circle`;
          break;
        case 'Links':
          iconName = `ios-link`;
          break;
        case 'Settings':
          iconName = `ios-options`;
      }
      return (
        <Ionicons name={iconName}
          size={28} style={{marginBottom: -3}}
          color={focused ? Colors.tabIconSelected :
          Colors.tabIconDefault}
        />
      );
    },
  }),
```

1.  设置`MainTabNavigator`的最后一步是创建用于给每个图标上色的`Colors`常量：

```jsx
const Colors = {
  tabIconDefault: '#ccc',
  tabIconSelected: '#2f95dc',
}
```

1.  我们的路由现在已经完成！现在剩下的就是为我们导入和定义在`MainTabNavigator.js`中的三个路由创建三个屏幕组件。为简单起见，这三个屏幕将具有相同的代码，除了背景颜色和标识文本不同。

1.  在项目的根目录中，我们需要创建一个`screens`文件夹来存放我们的三个屏幕。在新文件夹中，我们需要创建`HomeScreen.js`、`LinksScreen.js`和`SettingsScreen.js`。

1.  让我们从打开新创建的`HomeScreen.js`并添加必要的依赖项开始：

```jsx
import React from 'react';
import {
  StyleSheet,
  Text,
  View,
} from 'react-native';
```

1.  `HomeScreen`组件本身非常简单，只是一个全彩色页面，屏幕中间有一个`Home`字样，显示我们当前所在的屏幕：

```jsx
export default class HomeScreen extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.headline}>
          Home
        </Text>
      </View>
    );
  }
}
```

1.  我们还需要为我们的`Home`屏幕布局添加样式：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '#608FA0',
  },
  headline: {
    fontWeight: 'bold',
    fontSize: 30,
    color: 'white',
  }
});
```

1.  现在剩下的就是重复*步骤 14*、*步骤 15*和*步骤 16*，为剩下的两个屏幕做一些微小的更改。`LinksScreen.js`应该看起来像`HomeScreen.js`，并更新以下突出显示的部分：

```jsx
import React from 'react';
import {
  StyleSheet,
  Text,
  View,
} from 'react-native';

export default class LinksScreen extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.headline}>
 Links
        </Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
 backgroundColor: '#F8759D',  },
  headline: {
    fontWeight: 'bold',
    fontSize: 30,
    color: 'white',
  }
});
```

1.  同样，在`SettingsScreen.js`内部，我们可以使用与前两个屏幕相同的结构创建第三个屏幕组件：

```jsx
import React from 'react';
import {
  StyleSheet,
  Text,
  View,
} from 'react-native';

export default class SettingsScreen extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.headline}>
          Settings
        </Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
 backgroundColor: '#F0642E',
  },
  headline: {
    fontWeight: 'bold',
    fontSize: 30,
    color: 'white',
  }
});
```

1.  我们的应用程序已经完成！当我们在模拟器中查看我们的应用程序时，屏幕底部应该有一个选项卡栏，可以在三个路由之间切换：

![](img/10300526-2ca1-44b3-a4d7-8c8e94c3bf5a.png)

# 它是如何工作的...

在这个教程中，我们介绍了原生应用中最常见和基本的导航模式之一，即选项卡栏。React Navigation 库是一个非常强大、功能丰富的导航解决方案，很可能能够为您的应用程序提供任何所需的导航。我们将在第三章中介绍更多关于 React Navigation 的用法，*实现复杂的用户

接口 - 第一部分。*

# 另请参阅

+   React Navigation 官方文档（[`reactnavigation.org/`](https://reactnavigation.org/)）

+   Expo 的路由和导航指南（[`docs.expo.io/versions/latest/guides/routing-and-navigation.html`](https://docs.expo.io/versions/latest/guides/routing-and-navigation.html)）
