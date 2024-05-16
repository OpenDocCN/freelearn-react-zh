# 第三章：实现复杂的用户界面-第一部分

在本章中，我们将实现复杂的用户界面。我们将学习如何使用 flexbox 创建适用于不同屏幕尺寸的组件，如何检测方向变化等。

本章将涵盖以下教程：

+   创建具有主题支持的可重用按钮

+   使用 flexbox 为平板电脑构建复杂的布局

+   包括自定义字体

+   使用字体图标

# 创建具有主题支持的可重用按钮

在开发软件时，可重用性非常重要。我们应该避免一遍又一遍地重复相同的事情，而是应该创建可以尽可能多次重用的小组件。

在本教程中，我们将创建一个`Button`组件，并且我们还将定义几个属性来改变其外观和感觉。在学习本教程的过程中，我们将学习如何动态地将不同的样式应用到组件上。

# 准备工作

我们需要创建一个空的应用程序。让我们将其命名为`reusable-button`。

# 如何做...

1.  在我们新应用程序的根目录中，我们需要为可重用的按钮相关代码创建一个新的`Button`文件夹。让我们还在新的`Button`文件夹中创建`index.js`和`styles.js`。

1.  我们将从导入新组件的依赖项开始。在`Button/index.js`文件中，我们将创建一个`Button`组件。这意味着我们需要导入`Text`和`TouchableOpacity`组件。您会注意到我们还在导入尚不存在的样式。我们将在本教程的后续部分中定义这些样式。在`Button/index.js`文件中，我们应该有以下导入：

```jsx
import React, { Component } from 'react';

import {
  Text,
  TouchableOpacity,
} from 'react-native';

import {
  Base,
  Default,
  Danger,
  Info,
  Success
} from './styles';
```

1.  现在我们已经导入了依赖项，让我们为这个组件定义类。我们将需要一些属性和两种方法。还需要导出此组件，以便我们可以在其他地方使用它：

```jsx
export default class Button extends Component {
  getTheme() {
    // Defined in a later step
  }

  render() {
    // Defined in a later step
  }
}
```

1.  我们需要根据给定的`属性`选择要应用于我们组件的样式。为此，我们将定义`getTheme`方法。该方法将检查任何`属性`是否为`true`，并返回相应的样式。如果没有一个是`true`，它将返回`Default`样式：

```jsx
  getTheme() {
    const { danger, info, success } = this.properties;

    if (info) {
      return Info;
    }

    if (success) {
      return Success;
    }

    if (danger) {
      return Danger;
    }

    return Default;
  }
```

1.  所有组件都需要一个`render`方法。在这里，我们需要返回此组件的 JSX 元素。在这种情况下，我们将获取给定`属性`的样式，并将其应用于`TouchableOpacity`组件。

我们还为按钮定义了一个标签。在这个标签内，我们将渲染`children`属性。如果接收到回调函数，那么当用户按下这个组件时它将被执行：

```jsx
  render() {
    const theme = this.getTheme();
    const {
      children,
      onPress,
      style,
      rounded,
    } = this.properties;

    return (
      <TouchableOpacity
        activeOpacity={0.8}
        style={[
          Base.main,
          theme.main,
          rounded ? Base.rounded : null ,
          style,
        ]}
        onPress={onPress}
      >
        <Text style={[Base.label, theme.label]}>{children}</Text>
      </TouchableOpacity>
    );
  }
```

1.  我们的`Button`组件几乎完成了。我们仍然需要定义我们的样式，但首先让我们转到项目根目录下的`App.js`文件。我们需要导入依赖项，包括我们创建的`Button`组件。

当用户点击按钮时，我们将显示警报消息，因此我们还需要导入`Alert`组件：

```jsx
import React from 'react';
import {
  Alert,
  StyleSheet,
  View
} from 'react-native';
import Button from './Button';
```

1.  一旦我们有了所有的依赖项，让我们定义一个无状态组件，渲染几个按钮。第一个按钮将使用默认样式，第二个按钮将使用成功样式，这将为按钮的背景添加一个漂亮的绿色。最后一个按钮将在按下时显示一个警报。为此，我们需要定义使用`Alert`组件的回调函数，只需设置标题和消息：

```jsx
export default class App extends React.Component {
  handleButtonPress() {
    Alert.alert('Alert', 'You clicked this button!');
  }

  render() {
    return(
      <View style={styles.container}>
        <Button style={styles.button}>
          My first button
        </Button>
        <Button success style={styles.button}>
          Success button
        </Button>
        <Button info style={styles.button}>
          Info button
        </Button>
        <Button danger rounded style={styles.button}
        onPress={this.handleButtonPress}>
          Rounded button
        </Button>
      </View>
    );
  }
}
```

1.  我们将为主要布局的对齐和每个按钮的对齐方式添加一些样式，以及一些边距：

```jsx
const styles = StyleSheet.create({
  container: {
      flex: 1,
      alignItems: 'center',
      justifyContent: 'center',
    },
  button: {
    margin: 10,
  },
});
```

1.  如果我们现在尝试运行应用程序，将会出现一些错误。这是因为我们还没有为按钮声明样式。让我们现在来解决这个问题。在`Button/styles.js`文件中，我们需要定义基本样式。这些样式将应用于按钮的每个实例。在这里，我们将定义半径、填充、字体颜色和我们需要的所有常见样式：

```jsx
import { StyleSheet } from 'react-native';

const Base = StyleSheet.create({
  main: {
    padding: 10,
    borderRadius: 3,
  },
  label: {
    color: '#fff',
  },
  rounded: {
    borderRadius: 20,
  },
});
```

1.  一旦我们有了按钮的常见样式，我们需要为`Danger`、`Info`、`Success`和`Default`主题定义样式。为此，我们将为每个主题定义不同的对象。在每个主题内，我们将使用相同的对象，但具有该主题的特定样式。

为了保持简单，我们只会改变`backgroundColor`，但我们可以使用尽可能多的样式属性：

```jsx
const Danger = StyleSheet.create({
  main: {
    backgroundColor: '#e74c3c',
  },
});

const Info = StyleSheet.create({
  main: {
    backgroundColor: '#3498db',
  },
});

const Success = StyleSheet.create({
  main: {
    backgroundColor: '#1abc9c',
  },
});

const Default = StyleSheet.create({
  main: {
    backgroundColor: 'rgba(0 ,0 ,0, 0)',
  },
  label: {
    color: '#333',
  },
});
```

1.  最后，让我们导出样式。这一步是必要的，这样`Button`组件就可以导入每个主题的所有样式：

```jsx
export {
  Base,
  Danger,
  Info,
  Success,
  Default,
};
```

1.  如果我们打开应用程序，我们应该能够看到我们完成的布局：

![](img/fb784d29-16db-4a64-a1c9-bba06dcfaf0a.png)

# 工作原理...

在这个例子中，我们使用了`TouchableOpacity`组件。这个组件允许我们定义一个漂亮的动画，当用户按下按钮时改变不透明度。

我们可以使用`activeOpacity`属性来设置按钮被按下时的不透明度值。该值可以是`0`到`1`之间的任何数字，其中`0`是完全透明的。

如果我们按下圆形按钮，我们将看到一个原生的警报消息，如下面的截图所示：

![](img/4b6b4ca1-7313-4570-9e82-17758d2b4711.png)

# 使用 flexbox 为平板电脑构建复杂布局

当涉及到创建响应式布局时，flexbox 真的非常方便。React Native 使用 flexbox 作为布局系统，如果你已经熟悉这些概念，那么对于你来说开始创建任何类型的布局将会非常容易。

如前一章所述，在 React Native 中，flexbox 的工作方式与 CSS 中的工作方式有一些不同。有关 React Native 和 CSS flexbox 之间的区别的更多信息，请参阅第二章中*如何工作...*部分的*使用 flexbox 创建布局*教程。

在这个教程中，我们将创建一个布局来显示博客文章列表。每篇文章都将是一个带有图片、摘录和阅读更多按钮的小卡片。我们将使用 flexbox 根据屏幕大小在主容器上排列这些文章。这将允许我们通过正确对齐横向和纵向的卡片来处理屏幕旋转。

# 准备工作

我们需要一个新的应用程序来完成这个教程。让我们把它命名为`tablet-flexbox`。

当我们使用 Expo 创建一个新应用程序时，项目的基础部分会创建一个`app.json`，其中提供了一些基本配置。在这个教程中，我们正在构建一个应用程序，我们希望它在平板电脑上看起来很好，特别是在横向模式下。当我们打开`app.json`时，我们应该看到一个`orientation`属性设置为`'portrait'`。该属性确定应用程序内允许哪些方向。`orientation`属性接受`'portrait'`（锁定应用程序为纵向模式）、`'landscape'`（锁定应用程序为横向模式）和`'default'`（允许应用程序根据设备的方向调整屏幕方向）。对于我们的应用程序，我们将把`orientation`设置为`'landscape'`，这样我们就可以支持横向和纵向布局。

我们还将使用一些图片，这些图片需要远程托管，以便为该示例正确模拟加载远程数据并使用`Image`组件显示图片。我已经将这些图片上传到[www.imgur.com](http://www.imgur.com)图像托管服务，并在`data.json`文件中引用了这些远程图片，该文件是该示例用于其可消耗数据的。如果由于任何原因这些远程图片对您来说加载不正常，它们也包含在该示例的存储库中的`/assets`文件夹中。请随意将它们上传到任何服务器或托管服务，并相应地更新`data.json`中的图片 URL。该存储库可以在 GitHub 上找到，网址为[`github.com/warlyware/react-native-cookbook/tree/master/chapter-3/tablet-flexbox`](https://github.com/warlyware/react-native-cookbook/tree/master/chapter-3/tablet-flexbox)。

# 如何做...

1.  首先，我们需要在项目的根目录中创建一个`Post`文件夹。我们还需要在新的`Post`文件夹中创建一个`index.js`和一个`styles.js`文件。我们将使用这个`Post`组件来为我们的应用程序显示每个帖子。最后，我们需要在项目的根目录中添加一个`data.json`文件，我们将使用它来定义一个帖子列表。

1.  现在我们可以继续构建`App.js`组件。首先，我们需要导入这个类的依赖项。我们将使用`ListView`组件来渲染帖子列表。我们还需要`Text`和`View`组件作为内容容器。我们将创建一个自定义的`Post`组件来渲染列表中的每个帖子，并且我们还需要导入`data.json`文件：

```jsx
import React, { Component } from 'react';
import { ListView, StyleSheet, Text, View } from 'react-native';

import Post from './Post';
import data from './data.json';
```

1.  让我们为`App`组件创建类。在这里，我们将使用`.json`文件中的数据来创建列表的`dataSource`。我们将在下一步向我们的`data.json`文件中添加一些实际数据。在`render`方法中，我们将定义一个简单的顶部工具栏和`List`组件。我们将使用`Post`组件来处理每条记录，并从`state`中获取`dataSource`。

如果您对`ListView`组件有任何疑问，您应该查看第二章中的示例，*创建一个简单的 React Native 应用程序*，在那里我们创建了一个订单列表：

```jsx
const dataSource = new ListView.DataSource({
  rowHasChanged: (r1, r2) => r1 !== r2,
});

export default class App extends Component {
  state = {
    dataSource: dataSouce.cloneWithRows(data.posts),
  };

  render() {
    return (
      <View style={styles.container}>
        <View style={styles.toolbar}>
          <Text style={styles.title}>Latest posts</Text>
        </View>
        <ListView
          dataSource={this.state.dataSource}
          renderRow={post => <Post {...post} />}
          style={styles.list}
          contentContainerStyle={styles.content}
        />
      </View>
    );
  }
}
```

1.  还缺少两个文件：包含数据的`.json`文件和`Post`组件。在这一步中，我们将创建我们将用于每个帖子的数据。为了简化事情，在以下代码片段中只有一条数据记录，但我在这个示例中使用的其余`POST`对象可以在本示例的代码存储库中的`data.json`文件中找到，位于[`github.com/warlyware/react-native-cookbook/blob/master/chapter-3/tablet-flexbox/data.json`](https://github.com/warlyware/react-native-cookbook/blob/master/chapter-3/tablet-flexbox/data.json)：

```jsx
{
  "posts": [
    {
      "title": "The Best Article Ever Written",
      "img": "https://i.imgur.com/mf9daCT.jpg",
      "content": "Lorem ipsum dolor sit amet...",
      "author": "Bob Labla"
    },
    // Add more records here.
  ]
}
```

1.  现在我们有了一些数据，我们准备开始处理`Post`组件。在这个组件中，我们需要显示图片、标题和按钮。由于这个组件不需要知道状态，我们将使用一个无状态组件。以下代码使用了我们在第二章中学到的所有组件，*创建一个简单的 React Native 应用*。如果有什么不清楚的地方，请再次查看那一章。这个组件将接收数据作为参数，然后我们将用它来显示组件中的内容。`Image`组件将使用`data.json`文件中每个对象上定义的`img`属性来显示远程图片。

```jsx
import React from 'react';
import {
  Image,
  Text,
  TouchableOpacity,
  View
} from 'react-native';

import styles from './styles';

const Post = ({ content, img, title }) => (
  <View style={styles.main}>
    <Image
      source={{ uri: img }}
      style={styles.image}
    />
    <View style={styles.content}>
      <Text style={styles.title}>{title}</Text>
      <Text>{content}</Text>
    </View>
    <TouchableOpacity style={styles.button} activeOpacity={0.8}>
      <Text style={styles.buttonText}>Read more</Text>
    </TouchableOpacity>
  </View>
);

export default Post;
```

1.  一旦我们定义了组件，我们还需要为每个帖子定义样式。让我们创建一个空的`StyleSheet`导出，以便依赖于`styles.js`的`Post`组件能够正常运行。

```jsx
import { StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  // Defined in later steps
});

export default styles;
```

1.  如果我们尝试运行应用程序，我们应该能够在屏幕上看到来自`.json`文件的数据。不过，它不会很漂亮，因为我们还没有应用任何样式。

1.  我们在屏幕上已经有了所需的一切。现在我们准备开始布局工作。首先，让我们为我们的`Post`容器添加样式。我们将设置`width`、`height`、`borderRadius`和其他一些样式。让我们把它们添加到`/Post/styles.js`文件中。

```jsx
const styles = StyleSheet.create({
  main: {
    backgroundColor: '#fff',
    borderRadius: 3,
    height: 340,
    margin: 5,
    width: 240,
  }
});
```

1.  到目前为止，我们应该看到垂直对齐的小框。这是一些进展，但我们需要为图片添加更多样式，这样我们才能在屏幕上看到它。让我们在上一步的相同`styles`常量中添加一个`image`属性。`resizeMode`属性将允许我们设置我们想要如何调整图片的大小。在这种情况下，通过选择`cover`，图片将保持原始的宽高比。

```jsx
  image: {
    backgroundColor: '#ccc',
    height: 120,
    resizeMode: 'cover',
  }
```

1.  对于帖子的`content`，我们希望占据卡片上所有可用的高度，因此我们需要使其灵活并添加一些填充。我们还将向内容添加`overflow: hidden`以避免溢出`View`元素。对于`title`，我们只需要更改`fontSize`并在底部添加`margin`：

```jsx
  content: {
    padding: 10,
    overflow: 'hidden',
    flex: 1,
  },
  title: {
    fontSize: 18,
    marginBottom: 5,
  },
```

1.  最后，对于按钮，我们将`backgroundColor`设置为绿色，文本设置为白色。我们还需要添加一些`padding`和`margin`来进行间距：

```jsx
  button: {
    backgroundColor: '#1abc9c',
    borderRadius: 3,
    padding: 10,
    margin: 10,
  },
  buttonText: {
    color: '#fff',
    textAlign: 'center',
  }
```

1.  如果我们刷新模拟器，我们应该能够看到我们的帖子以小卡片的形式显示。目前，卡片是垂直排列的，但我们希望将它们全部水平渲染。我们将在以下步骤中解决这个问题：

![](img/9b2adc12-a3f8-4fe4-9a57-1a67d26382d0.png)已为所有帖子元素添加了主要样式

1.  目前，我们只能在列表中以列的形式看到前三个项目，而不是横向排列在屏幕上。让我们返回`App.js`文件并开始添加我们的样式。我们在`container`中添加`flex: 1`，以便我们的布局始终填满屏幕。我们还希望在顶部显示一个工具栏。为此，我们只需要定义一些`padding`和`color`如下：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  toolbar: {
    backgroundColor: '#34495e',
    padding: 10,
    paddingTop: 20,
  },
  title: {
    color: '#fff',
    fontSize: 20,
    textAlign: 'center',
  }
});
```

1.  让我们也为`list`添加一些基本样式。一个漂亮的背景颜色和一些填充。我们还将添加`flex`属性，这将确保列表占据屏幕上所有可用的高度。我们这里只有两个组件：工具栏和列表。工具栏占用大约 50 像素。如果我们使列表灵活，它将占据所有剩余的可用空间，这正是我们在旋转设备或在不同屏幕分辨率下运行应用程序时想要的效果：

```jsx
  list: {
    backgroundColor: '#f0f3f4',
    flex: 1,
    paddingTop: 5,
    paddingBottom: 5,
  }
```

1.  如果我们再次在模拟器中检查应用程序，我们应该能够看到工具栏和列表按预期布局：

![](img/7136ddc4-acb9-4d77-a451-d8b6fa2e90b8.png)已应用样式给每个帖子，使它们看起来像卡片

1.  我们几乎完成了这个应用程序。我们唯一剩下的事情就是将卡片水平排列。这可以通过 flexbox 在三个简单的步骤中实现：

```jsx
        content: { 
          flexDirection: 'row', 
          flexWrap: 'wrap', 
          justifyContent: 'space-around', 
        }, 
```

第一步是通过`ListView`组件中的`contentContainerStyle`属性应用这些`content`样式。在内部，`ListView`组件将这些样式应用于包裹所有子视图的内容容器。

然后我们将`flexDirection`设置为`row`。这将水平对齐列表上的卡片；然而，这提出了一个新问题：我们只能看到一行帖子。为了解决这个问题，我们需要包裹这些项目。我们通过将`flexWrap`属性设置为`wrap`来实现这一点，这将自动将不适合视图的项目移动到下一行。最后，我们使用`justifyContent`属性并将其设置为`center`，这将使我们的`ListView`居中在应用程序的中间。

1.  我们现在有一个响应灵敏的应用程序，在横向模式下在平板电脑上看起来很好：

![](img/cbd691f2-e7a5-484e-8381-4db14dd7ad36.png)

横向模式下 iPad 和 Android 平板电脑截图的并排比较

并且在纵向模式下看起来也很好：

![](img/07bd3013-a9eb-4d81-9bd5-240d3731943b.png)纵向模式下 iPad 和 Android 平板电脑截图的并排比较

# 还有更多...

Expo 还提供了一个`ScreenOrientation`助手，用于更改应用程序的方向配置。该助手还允许更精细的方向设置（例如`ALL_BUT_UPSIDE_DOWN`或`LANDSCAPE_RIGHT`）。如果您的应用程序需要动态、细粒度的屏幕方向控制，请参阅`ScreenOrientation`Expo 文档获取信息：[`docs.expo.io/versions/v24.0.0/sdk/screen-orientation.html`](https://docs.expo.io/versions/v24.0.0/sdk/screen-orientation.html)。

# 另请参阅

有关静态图像资源和`<Image>`组件的官方文档可以在[ https://facebook.github.io/react-native/docs/images.html](https://facebook.github.io/react-native/docs/images.html)找到。

# 包括自定义字体

在某个时候，我们可能会想要使用自定义字体系列显示文本。到目前为止，我们一直在使用默认字体，但我们可以使用任何其他我们喜欢的字体。

在 Expo 之前，添加自定义字体的过程更加困难，需要使用原生代码，并且需要在 iOS 和 Android 中实现不同的方式。幸运的是，通过使用 Expo 的字体助手库，这一切都变得简化和简化了。

在这个示例中，我们将导入一些字体，然后使用每个导入的字体系列显示文本。我们还将使用不同的字体样式，如**粗体**和*斜体*。

# 准备工作

为了在这个示例上工作，我们需要一些字体。你可以使用任何你喜欢的字体。我建议去 Google Fonts（[`fonts.google.com/`](https://fonts.google.com/)）下载你喜欢的字体。在这个示例中，我们将使用 Josefin Sans 和 Raleway 字体。

一旦你下载了字体，让我们创建一个空的应用程序并将其命名为`custom-fonts`。当我们使用 Expo 创建一个空白应用程序时，它会在项目的根目录中创建一个`assets`文件夹，用于放置所有资产（图像、字体等），因此我们将遵循标准，并将我们的字体添加到此文件夹中。让我们创建`/assets/fonts`文件夹并将从 Google Fonts 下载的自定义字体文件添加到此文件夹中。

从 Google Fonts 下载字体时，你会得到一个包含每个字体系列变体的`.ttf`文件的`.zip`文件。我们将使用常规、**粗体**和*斜体*变体，因此将每个系列的对应`.ttf`文件复制到我们的`/assets/fonts`文件夹中。

# 如何做...

1.  放置好我们的字体文件后，第一步是打开`App.js`并添加我们需要的导入：

```jsx
import React from 'react';
import { Text, View, StyleSheet } from 'react-native';
import { Font } from 'expo';
```

1.  接下来，我们将添加一个简单的组件来显示一些我们想要用我们自定义字体样式的文本。我们将从一个`Text`元素开始，显示 Roboto 字体的常规变体：

```jsx
export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.josefinSans}>
          Hello, Josefin Sans!
        </Text>
      </View>
    );
  }
}
```

1.  让我们也为我们刚刚创建的组件添加一些初始样式。现在，我们只会增加我们的`josefinSans`类样式的字体大小：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
  josefinSans: {
    fontSize: 40,
  }
});
```

1.  如果我们现在在模拟器中打开应用程序，我们将看到“Hello, Josefin Sans!”文本以默认字体显示在屏幕中央：

![](img/29a14fb0-a5c0-4208-acb2-eb0a0221f62a.png)

1.  让我们加载我们的`JosefinSans-Regular.ttf`字体文件，以便我们可以用它来样式化我们的文本。我们将使用 React Native 提供的`componentDidMount`生命周期钩子来告诉我们的应用程序何时开始加载字体：

```jsx
export default class App extends React.Component {

 componentDidMount() {
    Font.loadAsync({
      'josefin-sans-regular': require('./assets/fonts/JosefinSans-Regular.ttf'),
    });
  }

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.josefinSans}>
          Hello, Josefin Sans!
        </Text>
      </View>
    );
  }
}
```

1.  接下来，我们将添加我们正在加载的字体到应用于我们的`Text`元素的样式中：

```jsx
const styles = StyleSheet.create({
  // Other styles from step 3
  josefinSans: {
    fontSize: 40,
 fontFamily: 'josefin-sans-regular'
  }
});
```

1.  我们现在有样式了，对吗？嗯，并不完全是。如果我们回头看看我们的模拟器，我们会看到我们得到了一个错误：

```jsx
console.error: "fontFamily 'josefin-sans-regular' is not a system font and has not been loaded through Expo.Font.loadAsync"
```

1.  但是我们刚刚通过`Expo.Font.loadAsync`加载了字体！怎么回事？事实证明我们面临一个竞争条件。我们为`Text`元素定义的`josefinSans`样式被应用在 Josefin Sans 字体加载之前。为了解决这个问题，我们需要使用组件的`state`来跟踪字体的加载状态：

```jsx
export default class App extends React.Component {
 state = {
    fontLoaded: false
  };
```

1.  现在，我们的组件有了一个`state`，一旦字体加载完成，我们就可以将状态的`fontLoaded`属性更新为`true`。使用 ES6 特性`async`/`await`使这变得简洁而直接。让我们在我们的`componentDidMount`代码块中这样做：

```jsx
  async componentDidMount() {
    await Font.loadAsync({
      'josefin-sans-regular': require('./assets/fonts/JosefinSans-
      Regular.ttf'),
    });
  }
```

1.  由于我们现在正在等待`Font.loadAsync()`调用，一旦调用完成，我们可以将`fontLoaded`的状态设置为`true`：

```jsx
  async componentDidMount() {
    await Font.loadAsync({
      'josefin-sans-regular': require('./assets/fonts/JosefinSans-
      Regular.ttf'),
    });

 this.setState({ fontLoaded: true });
  }
```

1.  现在要做的就是更新我们的`render`方法，只有在`fontLoaded`状态属性为`true`时才渲染依赖于自定义字体的`Text`元素：

```jsx
      <View style={styles.container}>
 {
          this.state.fontLoaded ? (
            <Text style={styles.josefinSans}>
              Hello, Josefin Sans!
            </Text>
          ) : null
        }
      </View>
```

1.  现在，当我们在模拟器中查看我们的应用程序时，我们应该看到我们的自定义字体被应用：

![](img/2702c3c8-c45a-4a87-8f45-0d1e93006668.png)

1.  让我们加载其余的字体，这样我们也可以在应用程序中使用它们：

```jsx
    await Font.loadAsync({
      'josefin-sans-regular': require('./assets/fonts/JosefinSans-
      Regular.ttf'),
 'josefin-sans-bold': require('./assets/fonts/JosefinSans-
      Bold.ttf'),
      'josefin-sans-italic': require('./assets/fonts/JosefinSans-
      Italic.ttf'),
      'raleway-regular': require('./assets/fonts/Raleway-
      Regular.ttf'),
      'raleway-bold': require('./assets/fonts/Raleway-Bold.ttf'),
      'raleway-italic': require('./assets/fonts/Raleway-
      Italic.ttf'),
    });
```

1.  我们还需要`Text`元素来显示每个新字体系列/变体中的文本。请注意，由于 JSX 表达式要求只有一个父节点，我们还需要将所有的`Text`元素包装在另一个`View`元素中。我们现在还将`style`属性传递给一个样式数组，以便在下一步中应用`fontSize`和`padding`样式：

```jsx
  render() {
    return (
      <View style={styles.container}>
 {
          this.state.fontLoaded ? (
            <View style={styles.container}>
              <Text style={[styles.josefinSans, 
              styles.textFormatting]}>
                Hello, Josefin Sans!
              </Text>
              <Text style={[styles.josefinSansBold,
              styles.textFormatting]}>
                Hello, Josefin Sans!
              </Text>
              <Text style={[styles.josefinSansItalic, 
              styles.textFormatting]}>
                Hello, Josefin Sans!
              </Text>
              <Text style={[styles.raleway, styles.textFormatting]}>
                Hello, Raleway!
              </Text>
              <Text style={[styles.ralewayBold, 
              styles.textFormatting]}>
                Hello, Raleway!
              </Text>
              <Text style={[styles.ralewayItalic, 
              styles.textFormatting]}>
                Hello, Raleway!
              </Text>
            </View>
          ) : null
        }
      </View>
    );
  }
```

1.  剩下的就是将我们的自定义字体应用到`StyleSheet`中的新样式：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
  josefinSans: {
    fontFamily: 'josefin-sans-regular',
  },
  josefinSansBold: {
    fontFamily: 'josefin-sans-bold',
  },
  josefinSansItalic: {
    fontFamily: 'josefin-sans-italic',
  },
  raleway: {
    fontFamily: 'raleway-regular',
  },
  ralewayBold: {
    fontFamily: 'josefin-sans-bold'
  },
  ralewayItalic: {
    fontFamily: 'josefin-sans-italic',
  },
  textFormatting: {
    fontSize: 40,
    paddingBottom: 20
  }
});
```

1.  现在，在我们的应用程序中，我们将看到六个不同的文本元素，每个都使用自己的自定义字体样式：

![](img/ea1e48cb-17e9-490d-bd1a-2eca8180b23c.png)

# 工作原理...

在*步骤 5*和*步骤 6*中，我们使用了`componentDidMount` React 生命周期钩子来告诉我们的应用程序何时完成加载。虽然使用`componentWillMount`可能很诱人，但这也会引发错误，因为`componentWillMount`不能保证等待我们的`Font.loadAsync`完成。通过使用`componentDidMount`，我们还可以确保不阻止应用程序的初始渲染。

在*步骤 9*中，我们使用了 ES6 特性`async`/`await`。如果您是 Web 开发人员，您可能已经熟悉了这种模式，但如果您想了解更多信息，我在本教程末尾的*另请参阅*部分中包含了一篇来自[ponyfoo.com](http://ponyfoo.com)的精彩文章，该文章很好地解释了`async`/`await`的工作原理。

在*步骤 11*中，我们使用了一个三元语句，如果加载了我们的自定义字体样式的`Text`元素，则渲染它，如果没有加载，则返回`null`。

通过 Expo 加载的字体目前不支持`fontWeight`或`fontStyle`属性-您需要加载字体的这些变体，并按名称指定它们，就像我们在这里使用粗体和斜体一样。

# 另请参阅

关于`async`/`await`的一篇很棒的文章可以在[`ponyfoo.com/articles/understanding-javascript-async-await`](https://ponyfoo.com/articles/understanding-javascript-async-await)找到。

# 使用字体图标

图标是几乎任何应用程序的必不可少的部分，特别是在导航和按钮中。与上一章中介绍的 Expo 字体助手类似，Expo 还有一个图标助手，使添加图标字体比使用原始的 React Native 要方便得多。在这个示例中，我们将看到如何使用图标助手模块与流行的`FontAwesome`和`Ionicons`图标字体库。

# 准备工作

我们需要为这个示例创建一个新项目。让我们将这个项目命名为`font-icons`。

# 操作步骤

1.  我们将首先打开`App.js`并导入构建应用程序所需的依赖项：

```jsx
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
import { FontAwesome, Ionicons } from '@expo/vector-icons';
```

1.  接下来，我们可以添加应用程序的外壳，我们将在其中显示图标：

```jsx
export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
      </View>
    );
  }
}
```

1.  在`View`元素内，让我们再添加两个`View`元素来容纳每个图标集中的图标：

```jsx
export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
 <View style={styles.iconRow}>

        </View>
        <View style={styles.iconRow}>

        </View>
      </View>
    );
  }
}
```

1.  现在，让我们为我们声明的每个元素添加样式。正如我们在之前的示例中看到的，`container`样式使用`flex: 1`填充屏幕，并使用`alignItems`和`justifyContent`将项目居中设置为`center`。`iconRow`属性将`flexDirection`设置为`row`，这样我们的图标将排成一行：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
  iconRow: {
    flexDirection: 'row',
  },
});
```

1.  现在我们的应用程序的基本结构已经就位，让我们添加我们的图标。在第一行图标中，我们将使用四个`FontAwesome`组件来显示`FontAwesome`字体库中的四个图标。`name`属性确定应该使用哪个图标，`size`属性设置图标的像素大小，`color`设置图标的颜色：

```jsx
<View style={styles.iconRow}>
 <FontAwesome style={styles.iconPadding} name="glass" size={48} color="green" />
  <FontAwesome style={styles.iconPadding} name="beer" size={48} color="red" />
  <FontAwesome style={styles.iconPadding} name="music" size={48} color="blue" />
  <FontAwesome style={styles.iconPadding} name="taxi" size={48} color="#1CB5AD" />
</View>
```

就像在 CSS 中一样，`color`属性可以是 CSS 规范中定义的颜色关键字（您可以在 MDN 文档的完整列表中查看[`developer.mozilla.org/en-US/docs/Web/CSS/color_value`](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value)），也可以是给定颜色的十六进制代码。

1.  在接下来的`View`元素中，我们将添加来自`Ionicons`字体库的图标。正如您所看到的，`Ionicons`元素接受与上一步中使用的`FontAwesome`元素相同的属性：

```jsx
<View style={styles.iconRow}>
 <Ionicons style={styles.iconPadding} name="md-pizza" size={48} color="orange" />
  <Ionicons style={styles.iconPadding} name="md-tennisball" size={48} color="maroon" />
  <Ionicons style={styles.iconPadding} name="ios-thunderstorm" size={48} color="purple" />
  <Ionicons style={styles.iconPadding} name="ios-happy" size={48} color="#DF7977" />
</View>
```

1.  这个配方的最后一步是添加剩下的样式`iconPadding`，它只是为每个图标添加一些填充，以均匀地间隔开每个图标：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
  iconRow: {
    flexDirection: 'row',
  },
 iconPadding: {
    padding: 8,
  }
});
```

1.  就是这样！当我们查看我们的应用程序时，将会有两行图标，每一行分别展示来自`FontAwesome`和`Ionicons`的图标：

![](img/f7371449-caec-4c4a-9993-256cecd34e81.png)

# 它是如何工作的...

Expo 提供的`vector-icons`包可以访问 11 个完整的图标集。你所需要做的就是导入相关的组件（例如，`FontAwesome`组件用于 Font Awesome 图标），并为其提供与你想要使用的图标集中相对应的图标名称。你可以在`vector-icons`目录中找到一个完整的、可搜索的图标列表，该目录托管在[`expo.github.io/vector-icons/`](https://expo.github.io/vector-icons/)。只需将元素的`name`属性设置为目录中列出的图标名称，添加`size`和`color`属性，就完成了！

正如 GitHub 上`vector-icons`的 README 所述，这个库是为了在 Expo 中使用`react-native-vector-icons`包提供的图标而创建的兼容层。你可以在[`github.com/oblador/react-native-vector-icons`](https://github.com/oblador/react-native-vector-icons)找到这个包。如果你正在构建一个没有 Expo 的 React Native 应用程序，你可以使用`react-native-vector-icons`库来获得相同的功能。

# 另请参阅

`vector-icons`库中所有可用图标的目录可以在[`expo.github.io/vector-icons/`](https://expo.github.io/vector-icons/)找到。
