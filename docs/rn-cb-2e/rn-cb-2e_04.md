# 第四章：实现复杂用户界面-第二部分

本章将涵盖更多使用 React Native 构建 UI 的技巧。我们将首次了解如何链接到其他应用程序和网站，处理设备方向的变化，以及如何构建用于收集用户输入的表单。

在本章中，我们将涵盖以下技巧：

+   处理通用应用程序

+   检测方向变化

+   使用 WebView 嵌入外部网站

+   链接到网站和其他应用程序

+   创建一个表单组件

# 处理通用应用程序

使用 React Native 的好处之一是它能够轻松创建通用应用程序。我们可以在手机和平板应用程序之间共享大量代码。布局可能会根据设备而改变，但我们可以在布局之间重用代码片段。

在这个示例中，我们将构建一个可以在手机和平板上运行的应用程序。平板版本将包括不同的布局，但我们将重用相同的内部组件。

# 准备工作

对于这个示例，我们将展示一个联系人列表。目前，我们将从`.json`文件中加载数据。我们将在以后的章节中探讨如何从**表述性状态转移**（**REST**）API 中加载远程数据。

让我们打开以下 URL 并将生成的 JSON 复制到名为`data.json`的文件中，放在项目的根目录。我们将使用这些数据来渲染联系人列表。它返回一个假用户数据的 JSON 对象，网址是[`api.randomuser.me/?results=20`](http://api.randomuser.me/?results=20)。

让我们创建一个名为`universal-app`的新应用。

# 如何做...

1.  让我们打开`App.js`并导入我们在上一节“准备工作”中创建的依赖项以及我们的`data.json`文件。我们还将从`./utils/Device`导入`Device`实用程序，我们将在以后的步骤中构建它：

```jsx
import React, { Component } from 'react';
import { StyleSheet, View, Text } from 'react-native';
import Device from './utils/Device';

import data from './data.json';
```

1.  在这里，我们将创建主要的`App`组件及其基本布局。这个顶层组件将决定是渲染手机还是平板 UI。我们只渲染两个`Text`元素。`renderDetail`文本应该只在平板上显示，而`renderMaster`文本应该在手机和平板上显示：

```jsx
export default class App extends Component {
  renderMaster() {
    return (
      <Text>Render on phone and tablets!!</Text>
    );
  }

  renderDetail() {
    if (Device.isTablet()) {
      return (
        <Text>Render on tablets only!!</Text>
      );
    }
  }

  render() {
    return (
      <View style={styles.content}>
        {this.renderMaster()}
        {this.renderDetail()}
      </View>
    );
  }
}
```

1.  在`App`组件下，我们将添加一些基本样式。这些样式临时包括`paddingTop: 40`，以便我们渲染的文本不会被设备的系统栏覆盖：

```jsx
const styles = StyleSheet.create({
  content: {
    paddingTop: 40,
    flex: 1,
    flexDirection: 'row',
  },
});
```

1.  如果我们尝试按原样运行我们的应用程序，它将失败，并显示错误，告诉我们找不到`Device`模块，所以让我们创建它。这个实用程序类的目的是根据屏幕尺寸计算当前设备是手机还是平板电脑。它将有一个`isTablet`方法和一个`isPhone`方法。我们需要在项目的根目录中创建一个`utils`文件夹，并添加一个`Device.js`作为实用程序。现在我们可以添加实用程序的基本结构：

```jsx
import { Dimensions, Alert } from 'react-native';

// Tablet portrait dimensions
const tablet = {
  width: 552,
  height: 960,
};

class Device {
  // Added in next steps
}

const device = new Device();
export default device;
```

1.  让我们开始构建实用程序，通过创建两种方法：一个用于获取纵向尺寸，另一个用于获取横向尺寸。根据设备旋转，`width`和`height`的值将改变，这就是为什么我们需要这两种方法始终获取正确的值，无论设备是`landscape`还是`portrait`：

```jsx
class Device {
 getPortraitDimensions() {
    const { width, height } = Dimensions.get("window");

    return {
      width: Math.min(width, height),
      height: Math.max(width, height),
    };
  }

  getLandscapeDimensions() {
    const { width, height } = Dimensions.get("window");

    return {
      width: Math.max(width, height),
      height: Math.min(width, height),
    };
  }
}
```

1.  现在让我们创建我们的应用程序将用来确定应用程序是在平板电脑上运行还是在手机上运行的两种方法。为了计算这一点，我们需要获取纵向模式下的尺寸，并将它们与我们为平板电脑定义的尺寸进行比较：

```jsx
  isPhone() {
    const dimension = this.getPortraitDimensions();
    return dimension.height < tablet.height;
  }

  isTablet() {
    const dimension = this.getPortraitDimensions();
    return dimension.height >= tablet.height;
  }
```

1.  现在，如果我们打开应用程序，我们应该看到两种不同的文本被呈现，取决于我们是在手机上还是在平板上运行应用程序：

![](img/4b04e46d-e2f8-4753-b51a-2a5053e3f3a2.png)

1.  实用程序按预期工作！让我们回到主`App.js`的`renderMaster`方法上。我们希望这个方法渲染存储在`data.json`文件中的联系人列表。让我们导入一个新的组件，我们将在接下来的步骤中构建它，并更新`renderMaster`方法以使用我们的新组件：

```jsx
import UserList from './UserList';

export default class App extends Component {
 renderMaster() {
    return (
      <UserList contacts={data.results} />
    );
  }

  //...
}
```

1.  让我们创建一个新的`UserList`文件夹。在这个文件夹里，我们需要为新组件创建`index.js`和`styles.js`文件。我们需要做的第一件事是将依赖项导入到新的`index.js`中，创建`UserList`类，并将其导出为`default`：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  View,
  Text,
  ListView,
  Image,
  TouchableOpacity,
} from 'react-native';
import styles from './styles';

export default class UserList extends Component {
 // Defined in the following steps 
}
```

1.  我们已经介绍了如何创建列表。如果您不清楚`ListView`组件的工作原理，请阅读第二章中的*显示项目列表*配方，*创建一个简单的 React Native 应用程序*。在类的构造函数中，我们将创建`dataSource`，然后将其添加到`state`中：

```jsx
export default class UserList extends Component {
 constructor(properties) {
    super(properties);
    const dataSource = new ListView.DataSource({
      rowHasChanged: (r1, r2) => r1 !== r2
    });

    this.state = {
      dataSource: dataSource.cloneWithRows(properties.contacts),
    };
  }

  //...
}
```

1.  `render`方法也遵循了在第二章中介绍的`ListView`配方中引入的相同模式，*显示项目列表*：

```jsx
render() {
 return (
  <View style={styles.main}>
   <Text style={styles.toolbar}>
   My contacts!
   </Text>
   <ListView dataSource={this.state.dataSource}
    renderRow={this.renderContact}
    style={styles.main} />
  </View> );
 }
```

1.  正如您所看到的，我们需要定义`renderContact`方法来呈现每一行。我们正在使用`TouchableOpacity`组件作为主要包装器，这将允许我们在列表项被按下时执行一些操作的回调函数。目前，当按钮被按下时我们并没有做任何事情。我们将在第九章中学习如何使用 Redux 在组件之间进行通信，*实现 Redux*：

```jsx
        renderContact = (contact) => { 
          return ( 
            <TouchableOpacity style={styles.row}> 
              <Image source={{uri: `${contact.picture.large}`}} style=
              {styles.img} /> 
              <View style={styles.info}> 
                <Text style={styles.name}> 
                  {this.capitalize(contact.name.first)} 
                  {this.capitalize(contact.name.last)} 
                </Text> 
                <Text style={styles.phone}>{contact.phone}</Text> 
              </View> 
            </TouchableOpacity> 
          ); 
        }
```

1.  我们没有使用样式来使文本大写，所以我们需要使用 JavaScript。`capitalize`函数非常简单，它将给定字符串的第一个字母设置为大写：

```jsx
  capitalize(value) {
    return value[0].toUpperCase() + value.substring(1);
  }
```

1.  我们几乎完成了这个组件。剩下的只有`styles`。让我们打开`/UserList/styles.js`文件，并为主容器和工具栏添加样式：

```jsx
import { StyleSheet } from 'react-native';

export default StyleSheet.create({
  main: {
    flex: 1,
    backgroundColor: '#dde6e9',
  },
  toolbar: {
    backgroundColor: '#2989dd',
    color: '#fff',
    paddingTop: 50,
    padding: 20,
    textAlign: 'center',
    fontSize: 20,
  },
  // Remaining styles added in next step.
});
```

1.  现在，对于每一行，我们希望在左边呈现每个联系人的图像，右边是联系人的姓名和电话号码：

```jsx
  row: {
    flexDirection: 'row',
    padding: 10,
  },
  img: {
    width: 70,
    height: 70,
    borderRadius: 35,
  },
  info: {
    marginLeft: 10,
  },
  name: {
    color: '#333',
    fontSize: 22,
    fontWeight: 'bold',
  },
  phone: {
    color: '#aaa',
    fontSize: 16,
  },
```

1.  让我们切换到`App.js`文件，并删除我们在*步骤 7*中用于使文本可读的`paddingTop`属性；要删除的行已用粗体显示：

```jsx
const styles = StyleSheet.create({
  content: {
 paddingTop: 40,
    flex: 1,
    flexDirection: 'row',
  },
});
```

1.  如果我们尝试运行我们的应用程序，我们应该能够在手机和平板上看到一个非常漂亮的列表，并且在两个不同的设备上看到相同的组件：

![](img/3a0e3972-6f01-4526-855d-67962d131503.png)

1.  我们已经根据当前设备显示了两种不同的布局！现在我们需要在`UserDetail`视图上进行工作，它将显示所选的联系人。让我们打开`App.js`，导入`UserDetail`视图，并更新`renderDetail`方法，如下所示：

```jsx
import UserDetail from './UserDetail'; 
export default class App extends Component {
  renderMaster() {
    return (
      <UserList contacts={data.results} />
    );
  }

 renderDetail() {
    if (Device.isTablet()) {
      return (
        <UserDetail contact={data.results[0]} />
      );
    }
  }
}
```

如前所述，在这个食谱中，我们不专注于从一个组件向另一个组件发送数据，而是专注于在平板电脑和手机上呈现不同的布局。因此，对于这个食谱，我们将始终将第一条记录发送到用户详细信息视图。

1.  为了简化事情并尽可能缩短食谱，对于用户详细信息视图，我们将只显示一个工具栏和一些显示给定记录的名字和姓氏的文本。我们将在这里使用一个无状态组件：

```jsx
import React from 'react';
import {
  View,
  Text,
} from 'react-native';
import styles from './styles';

const UserList = ({ contact }) => (
  <View style={styles.main}>
    <Text style={styles.toolbar}>Details should go here!</Text>
    <Text>
      This is the detail view:{contact.name.first} {contact.name.last}
    </Text>
  </View>
);

export default UserList;
```

1.  最后，我们需要为这个组件设置样式。我们希望将屏幕的四分之三分配给详细页面，四分之一分配给主列表。这可以通过使用 flexbox 轻松实现。由于`UserList`组件具有`flex`属性为`1`，我们可以将`UserDetail`的`flex`属性设置为`3`，允许`UserDetail`占据屏幕的 75%。以下是我们将添加到`/UserDetail/styles.js`文件的样式：

```jsx
import { StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  main: {
    flex: 3,
    backgroundColor: '#f0f3f4',
  },
  toolbar: {
    backgroundColor: '#2989dd',
    color: '#fff',
    paddingTop: 50,
    padding: 20,
    textAlign: 'center',
    fontSize: 20,
  },
});

export default styles;
```

1.  如果我们再次尝试运行我们的应用程序，我们会发现在平板上，它将呈现一个漂亮的布局，显示列表视图和详细视图，而在手机上，它只显示联系人列表。

![](img/b50ed65a-c2a9-4d6a-bb60-d8c18e038932.png)

# 工作原理...

在`Device`实用程序中，我们导入了 React Native 提供的名为`Dimension`的依赖项，用于获取当前设备的尺寸。我们还在`Device`实用程序中定义了一个名为`tablet`的常量，它是一个包含`width`和`height`的对象，用于与`Dimension`一起计算设备是否为平板电脑。这个常量的值是基于市场上最小的 Android 平板电脑。

在*步骤 5*中，我们通过调用`Dimensions.get("window")`方法获得了宽度和高度，然后根据我们想要的方向获得了最大值和最小值。

在*步骤 12*中，重要的是要注意我们使用箭头函数来定义`renderContact`方法。使用箭头函数可以保持正确的绑定范围，否则，在调用`this.capitalize`时，`this`将绑定到错误的范围。查看*另请参阅*部分，了解有关`this`关键字和箭头函数工作原理的更多信息。

# 另请参阅

+   从 ponyfoo 的[https://ponyfoo.com/articles/es6-arrow-functions-in-depth]中获得了对 ES6 箭头函数的良好解释。

+   Kyle Simpson 在[https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md]深入探讨了 JavaScript 中`this`的工作原理。

# 检测方向变化

在构建复杂的界面时，根据设备的方向渲染不同的 UI 组件是非常常见的。这在处理平板电脑时尤其如此。

在这个示例中，我们将根据屏幕方向渲染菜单。在横向时，我们将渲染一个带有图标和文本的扩展菜单，而在纵向时，我们只会渲染图标。

# 准备工作

为了支持方向变化，我们将使用 Expo 的辅助工具`ScreenOrientation`。

我们还将使用 Expo 软件包`@expo/vector-icons`提供的`FontAwesome`组件。第二章中的*使用字体图标*一节描述了如何使用这个组件。

在开始之前，让我们创建一个名为`screen-orientation`的新应用程序。我们还需要对 Expo 在目录根目录中创建的`app.json`文件进行微调。这个文件有一些 Expo 在构建应用程序时使用的基本设置。其中之一是`orientation`，它自动设置为`portrait`，用于每个新应用程序。此设置确定应用程序允许的方向，并且可以设置为`portrait`、`landscape`或`default`。如果我们将其更改为`default`，我们的应用程序将允许纵向和横向方向。

要看到这些更改生效，请确保重新启动 Expo 项目。

# 如何做...

1.  我们将首先打开`App.js`并添加我们将使用的导入：

```jsx
import React from 'react';
import {
  Dimensions,
  StyleSheet,
  Text,
  View
} from 'react-native';
```

1.  接下来，我们将添加空的`App`组件类，以及一些基本样式：

```jsx
export default class App extends React.Component {

}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#fff'
  },
  text: {
    fontSize: 40,
  }
});
```

1.  在我们的应用程序框架就位后，我们现在可以添加`render`方法。在`render`方法中，您会注意到我们使用了`View`组件，并使用了`onLayout`属性，这将在设备方向发生变化时触发。然后`onLayout`将运行`this.handleLayoutChange`，我们将在下一步中定义。在`Text`元素中，我们只是显示`state`对象上`orientation`的值：

```jsx
export default class App extends React.Component {
 render() {
    return (
      <View
        onLayout={() => this.handleLayoutChange}
        style={styles.container}
      >
        <Text style={styles.text}>
          {this.state.orientation}
        </Text>
      </View>
    );
  }
}
```

1.  让我们创建组件的`handleLayoutChange`方法，以及`handleLayoutChange`方法调用的`getOrientation`函数。`getOrientation`函数使用 React Native 的`Dimensions`工具来获取屏幕的宽度和高度。如果`height > width`，我们就知道设备处于纵向方向，如果不是，那么它就是横向方向。通过更新`state`，将启动重新渲染，并且`this.state.orientation`的值将反映方向：

```jsx
  handleLayoutChange() {
    this.getOrientation();
  }

  getOrientation() {
    const { width, height } = Dimensions.get('window');
    const orientation = height > width ? 'Portrait' : 'Landscape';
    this.setState({
      orientation
    });
  }
```

1.  如果我们此时运行应用程序，将会得到错误类型错误：null 不是对象：（评估'this.state.orientation'）。这是因为`render`方法试图在`this.state.orientation`值甚至被定义之前读取它。我们可以通过 React 生命周期`componentWillMount`钩子在`render`首次运行之前轻松解决这个问题来获取方向：

```jsx
  componentWillMount() {
    this.getOrientation();
  }
```

1.  这就是我们寻找的基本功能所需的全部内容！再次运行应用程序，您应该看到显示的文本反映了设备的方向。旋转设备，方向文本应该更新：

![](img/23ba5cc4-36e7-4728-ac3a-3bc00b4846fa.png)

1.  现在`orientation`状态值已经正确更新，我们可以专注于 UI。如前所述，我们将创建一个菜单，根据当前方向稍微不同地呈现选项。让我们导入一个`Menu`组件，我们将在接下来的步骤中构建它，并更新我们的`App`组件的`render`方法以使用新的`Menu`组件。请注意，我们现在将`this.state.orientation`传递给`Menu`组件的`orientation`属性：

```jsx
import Menu from './Menu';

export default class App extends React.Component {

  // ...

  render() {
    return (
      <View
        onLayout={() => {this.handleLayoutChange()}}
        style={styles.container}
      >
        <Menu orientation={this.state.orientation} />
        <View style={styles.main}>
          <Text>Main Content</Text>
        </View>
      </View>
    );
  }
}
```

1.  让我们也更新我们的`App`组件的样式。您可以用以下代码替换*步骤 2*中的样式。通过在`container`样式上将`flexDirection`设置为`row`，我们将能够水平显示两个组件：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'row',
  },
  main: {
    flex: 1,
    backgroundColor: '#ecf0f1',
    justifyContent: 'center',
    alignItems: 'center',
  }
});
```

1.  接下来，让我们构建`Menu`组件。我们需要创建一个新的`/Menu/index.js`文件，其中将定义`Menu`类。这个组件将接收`orientation`属性，并根据`orientation`值决定如何呈现菜单选项。让我们首先导入这个类的依赖项：

```jsx
import React, { Component } from 'react';
import { StyleSheet, View, Text } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';
```

1.  现在我们可以定义`Menu`类。在`state`对象上，我们将定义一个`options`数组。这些`option`对象将用于定义图标。如前一章中的*使用字体图标*中讨论的，我们可以通过关键字来定义图标，如在[`expo.github.io/vector-icons/`](https://expo.github.io/vector-icons/)中的矢量图标目录中定义的那样：

```jsx
export default class Menu extends Component {
  state = {
    options: [
      {title: 'Dashboard', icon: 'dashboard'},
      {title: 'Inbox', icon: 'inbox'},
      {title: 'Graphs', icon: 'pie-chart'},
      {title: 'Search', icon: 'search'},
      {title: 'Settings', icon: 'gear'},
    ],
  };

  // Remainder defined in following steps
}
```

1.  这个组件的`render`方法循环遍历`state`对象中的`options`数组：

```jsx
  render() {
    return (
      <View style={styles.content}>
        {this.state.options.map(this.renderOption)}
      </View>
    );
  }
```

1.  正如您在上一步中的 JSX 中所看到的，有一个对`renderOption`的调用。在这个方法中，我们将为每个选项呈现图标和标签。我们还将使用方向值来切换显示标签，并更改图标的大小：

```jsx
  renderOption = (option, index) => {
    const isLandscape = this.properties.orientation === 'Landscape';
    const title = isLandscape
      ? <Text style={styles.title}>{option.title}</Text>
      : null;
    const iconSize = isLandscape ? 27 : 35;

    return (
      <View key={index} style={[styles.option, styles.landscape]}>
        <FontAwesome name={option.icon} size={iconSize} color="#fff" />
        {title}
      </View>
    );
  }
```

在上一个代码块中，请注意我们正在定义`key`属性。在动态创建新组件时，我们总是需要设置`key`属性。该属性对于每个项目应该是唯一的，因为它在内部被 React 使用。在这种情况下，我们使用循环迭代的索引。这样，我们可以确保每个项目都有一个唯一的`key`值，因为数据是静态的。您可以在官方文档中阅读更多关于它的信息[`reactjs.org/docs/lists-and-keys.html`](https://reactjs.org/docs/lists-and-keys.html)。

1.  最后，我们将为菜单定义样式。首先，我们将把`backgroundColor`设置为深蓝色，然后，对于每个选项，我们将改变`flexDirection`以水平渲染图标和标签。其余的样式添加边距和填充，以便菜单项之间有很好的间距：

```jsx
const styles = StyleSheet.create({
  content: {
    backgroundColor: '#34495e',
    paddingTop: 50,
  },
  option: {
    flexDirection: 'row',
    paddingBottom: 15,
  },
  landscape: {
    paddingRight: 30,
    paddingLeft: 30,
  },
  title: {
    color: '#fff',
    fontSize: 16,
    margin: 5,
    marginLeft: 20,
  },
});
```

1.  如果我们现在运行我们的应用程序，它将根据屏幕的方向不同显示菜单 UI。旋转设备，布局将自动更新：

![](img/12a5ad38-0756-4ed5-a189-2f3e4412e2c1.png)

# 还有更多...

在这个示例中，我们查看了作为每个 Expo 项目一部分存在的`app.json`文件。在这个文件中有许多有用的设置，可以调整这些设置会影响项目的构建过程。您可以使用这个文件来调整方向锁定，定义应用程序图标，并设置启动画面，以及其他许多设置。您可以在 Expo 配置文档中查看`app.json`支持的所有设置，托管在[`docs.expo.io/versions/latest/guides/configuration.html`](https://docs.expo.io/versions/latest/guides/configuration.html)。

Expo 还提供了`ScreenOrientation`实用程序，可以用来声明应用程序的允许方向。使用实用程序的主要方法`ScreenOrientation.allow(orientation)`，将覆盖`app.json`中的相应设置。该实用程序还提供比`app.json`中设置更精细的选项，例如`ALL_BUT_UPSIDE_DOWN`和`LANDSCAPE_RIGHT`。有关此实用程序的更多信息，您可以阅读文档[`docs.expo.io/versions/latest/sdk/screen-orientation.html`](https://docs.expo.io/versions/latest/sdk/screen-orientation.html)。

# 使用 WebView 嵌入外部网站

对于许多应用程序，需要访问和在应用程序中显示外部链接是必需的。这可以用于显示第三方网站、在线帮助以及使用您的应用程序的条款和条件等。

在这个教程中，我们将看到如何在我们的应用程序中通过点击按钮打开一个 WebView，并动态设置 URL 值。我们还将在这个教程中使用`react-navigation`包来创建基本的堆栈导航。请查看第三章中的*设置和使用导航*教程，深入了解构建导航。

如果您的应用程序更适合通过设备浏览器加载外部网站，请参阅下一个教程*链接到网站和其他应用程序*。

# 准备工作

我们需要为基于 WebView 的教程创建一个新的应用程序。让我们将我们的新应用命名为`web-view`。我们还将使用`react-navigation`，所以一定要安装这个包。您可以使用`yarn`或`npm`来安装这个包。在项目的根目录中，运行以下命令：

```jsx
yarn add react-navigation
```

或者，使用`npm`进行安装：

```jsx
npm install --save react-navigation
```

# 如何做...

1.  让我们从打开`App.js`文件开始。在这个文件中，我们将使用`react-navigation`包提供的`StackNavigator`组件。首先，让我们添加在这个文件中将要使用的导入。`HomeScreen`是我们将在本教程中稍后构建的一个组件：

```jsx
import React, { Component } from 'react';
import { StackNavigator } from 'react-navigation';

import HomeScreen from './HomeScreen';
```

1.  现在我们有了导入，让我们使用`StackNavigator`组件来定义第一个路由；我们将使用一个带有链接的`Home`路由，这些链接应该使用 React Native 的`WebView`组件显示。`navigationOptions`属性允许我们定义要在导航标题中显示的标题：

```jsx
const App = StackNavigator({
  Home: {
    screen: HomeScreen,
    navigationOptions: ({ navigation }) => ({
      title: 'Home'
    }),
  },
});

export default App;
```

1.  现在我们准备好创建`HomeScreen`组件了。让我们在项目的根目录中创建一个名为`HomeScreen`的新文件夹，并在文件夹中添加一个`index.js`文件。和往常一样，我们可以从导入开始：

```jsx
import React, { Component } from 'react';
import {
  TouchableOpacity,
  View,
  Text,
  SafeAreaView,
} from 'react-native';

import styles from './styles';
```

1.  现在我们可以声明我们的`HomeScreen`组件。让我们还向组件添加一个`state`对象，其中包含一个`links`数组。这个数组中有一个对象，代表我们将在这个组件中使用的每个链接。我已经为您提供了四个`links`供您使用；但是，您可以编辑每个`links`数组对象中的`title`和`url`到任何您喜欢的网站：

```jsx
export default class HomeScreen extends Component {
  state = {
    links: [
      {
        title: 'Smashing Magazine',
        url: 'https://www.smashingmagazine.com/articles/'
      },
      {
        title: 'CSS Tricks',
        url: 'https://css-tricks.com/'
      },
      {
        title: 'Gitconnected Blog',
        url: 'https://medium.com/gitconnected'
      },
      {
        title: 'Hacker News',
        url: 'https://news.ycombinator.com/'
      }
     ],
  };
}
```

1.  我们准备向该组件添加一个`render`函数。在这里，我们使用`SafeAreaView`作为容器元素。这与普通的`View`元素一样工作，但也考虑了 iPhone X 上的刘海区域，以便我们的布局不会被设备边框遮挡。您会注意到我们正在使用`map`来遍历上一步中的`links`数组，将每个传递给`renderButton`函数：

```jsx
  render() {
    return (
      <SafeAreaView style={styles.container}>
        <View style={styles.buttonList}>
          {this.state.links.map(this.renderButton)}
        </View>
      </SafeAreaView>
    );
  }
```

1.  现在我们已经定义了`render`方法，我们需要创建它正在使用的`renderButton`方法。该方法将每个链接作为名为`button`的参数，并且我们将使用`index`作为`key`的唯一标识符，用于`renderButton`创建的每个元素。有关此点的更多信息，请参见本章第二个食谱中*检测方向更改*的*步骤 12*中的*提示*。

当按下`TouchableOpacity`按钮元素时，将触发`this.handleButtonPress(button)`：

```jsx
  renderButton = (button, index) => {
    return (
      <TouchableOpacity
        key={index}
        onPress={() => this.handleButtonPress(button)}
        style={styles.button}
      >
        <Text style={styles.text}>{button.title}</Text>
      </TouchableOpacity>
    );
  }
```

1.  现在我们需要创建`handleButtonPress`方法，该方法在上一步中使用。此方法使用传入的`button`参数的`url`和`title`属性。然后，我们可以在调用`this.properties.navigation.navigate()`时使用这些属性，传递要导航到的路由的名称和应传递到该路由的参数。我们可以访问名为`navigation`的`property`，因为我们正在使用`StackNavigator`，这是我们在*步骤 2*中设置的：

```jsx
  handleButtonPress(button) {
    const { url, title } = button;
    this.properties.navigation.navigate('Browser', { url, title });
  }
```

1.  `HomeScreen`组件已经完成，除了样式。让我们在`HomeScreen`文件夹中添加一个`styles.js`文件来定义这些样式：

```jsx
import { StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  buttonList: {
    flex: 1,
    justifyContent: 'center',
  },
  button: {
    margin: 10,
    backgroundColor: '#c0392b',
    borderRadius: 3,
    padding: 10,
    paddingRight: 30,
    paddingLeft: 30,
  },
  text: {
    color: '#fff',
    textAlign: 'center',
  },
});

export default styles;
```

1.  现在，如果我们打开应用程序，我们应该看到`HomeScreen`组件呈现为带有四个链接按钮的列表，并且在每个设备上以本机样式呈现标题为 Home。然而，由于我们的`StackNavigator`中没有`Browser`路由，当按下按钮时实际上不会发生任何事情：

![](img/b705e213-90c2-4eb1-b741-7a9a48c1835f.png)

1.  让我们返回`App.js`文件并添加`Browser`路由。首先，我们需要导入`BrowserScreen`组件，我们将在接下来的步骤中创建：

```jsx
import BrowserScreen from './BrowserScreen';
```

1.  现在`BrowserScreen`组件已经被导入，我们可以将它添加到`StackNavigator`对象中，以创建一个`Browser`路由。在`navigationOptions`中，我们正在根据传递给路由的参数定义动态标题。这些参数与我们在*步骤 7*中作为第二个参数传递给`navigation.navigate()`调用的对象相同：

```jsx
const App = StackNavigator({
  Home: {
    screen: HomeScreen,
    navigationOptions: ({ navigation }) => ({
      title: 'Home'
    }),
  },
 Browser: {
    screen: BrowserScreen,
    navigationOptions: ({ navigation }) => ({
      title: navigation.state.params.title
    }),
  },
});
```

1.  我们准备创建`BrowserScreen`组件。让我们在项目的根目录中创建一个名为`BrowserScreen`的新文件夹，并在其中添加一个新的`index.js`文件，然后添加此组件所需的导入：

```jsx
import React, { Component } from 'react';
import { WebView } from 'react-native';
```

1.  `BrowserScreen`组件非常简单。它只包括一个渲染方法，该方法从传递给`navigation.state`属性的`params`属性中读取，以调用在*步骤 7*中定义的`this.properties.navigation.navigate`，当按下按钮时触发。我们只需要渲染`WebView`组件并将其`source`属性设置为具有`uri`属性设置为`params.url`的对象：

```jsx
export default class BrowserScreen extends Component {
  render() {
    const { params } = this.properties.navigation.state;

    return(
      <WebView
        source={{uri: params.url}}
      />
    );
  }
}
```

1.  现在，如果我们回到模拟器中运行的应用程序，我们可以看到我们的 WebView 在运行！

![](img/9fedd5d4-07d4-46c1-bfe4-2d0069f7c5e1.png)Hacker News 和 Smashing Magazine 从我们的应用程序中访问

# 它是如何工作的...

使用`WebView`打开外部网站是让用户在我们的应用程序中消费外部网站的好方法。许多应用程序都这样做，允许用户轻松返回到应用程序的主要部分。

在*步骤 6*中，我们使用箭头函数将`onPress`属性中的函数绑定到当前类实例的范围，因为我们在循环遍历链接数组时使用了这个函数。

在*步骤 7*中，每当按下按钮时，我们使用绑定到该按钮的标题和 URL，将它们作为参数传递，当我们导航到`Browser`屏幕时。 *步骤 11*中的`navigationOptions`使用相同的标题值作为屏幕的标题。`navigationOptions`接受一个函数，其第一个参数是包含`navigation`的对象，该对象在导航时使用参数。在*步骤 11*中，我们从这个对象中构造导航，以便我们可以将视图的标题设置为`navigation.state.params.title`。

由于`react-navigation`提供的`StackNavigator`组件，我们得到了一个具有特定于操作系统的动画和内置返回按钮的标题。您可以阅读`StackNavigation`文档，了解有关此组件的更多信息[`reactnavigation.org/docs/stack-navigator.html`](https://reactnavigation.org/docs/stack-navigator.html)。

*第 13 步*使用传递给`BrowserScreen`组件的 URL 来使用 WebView，在 WebView 的`source`属性中使用 URL 来呈现。您可以在官方文档中找到所有可用的 WebView 属性列表，位于[`facebook.github.io/react-native/docs/webview.html`](https://facebook.github.io/react-native/docs/webview.html)。

# 链接到网站和其他应用程序

我们已经学会了如何使用`WebView`将第三方网站呈现为我们应用程序的嵌入部分。然而，有时候，我们可能希望使用原生浏览器打开网站，链接到其他原生系统应用程序（如电子邮件、电话和短信），甚至深层链接到一个完全独立的应用程序。

在这个配方中，我们将通过原生浏览器和应用程序内的浏览器模态链接到外部网站，创建到电话和消息应用程序的链接，并创建一个深层链接，将打开 Slack 应用程序并自动加载[gitconnected.com](http://gitconnected.com) Slack 群中的#general 频道。

您需要在真实设备上运行此应用程序，以便在此应用程序中打开使用设备系统应用程序的链接，例如电子邮件、电话和短信链接。根据我的经验，这在模拟器中是行不通的。

# 准备工作

让我们为这个配方创建一个新的应用程序。我们将其称为`linking-app`。

# 如何做到...

1.  让我们从打开`App.js`并添加我们将要使用的导入开始：

```jsx
import React from 'react';
import { StyleSheet, Text, View, TouchableOpacity, Platform } from 'react-native';
import { Linking } from 'react-native';
import { WebBrowser } from 'expo';
```

1.  接下来，让我们添加一个`App`组件和一个`state`对象。在这个应用程序中，`state`对象将包含我们在这个配方中将使用的所有链接，放在一个名为`links`的数组中。请注意，每个`links`对象中的`url`属性都附有一个协议（`tel`、`mailto`、`sms`等）。设备使用这些协议来正确处理每个链接：

```jsx
export default class App extends React.Component {
  state = {
    links: [
      {
        title: 'Call Support',
        url: 'tel:+12025550170',
        type: 'phone'
      },
      {
        title: 'Email Support',
        url: 'mailto:support@email.com',
        type: 'email',
      },
      {
        title: 'Text Support',
        url: 'sms:+12025550170',
        type: 'text message',
      },
      {
        title: 'Join us on Slack',
        url: 'slack://channel?team=T5KFMSASF&id=C5K142J57',
        type: 'slack deep link',
      },
      {
        title: 'Visit Site (internal)',
        url: 'https://google.com',
        type: 'internal link'
      },
      {
        title: 'Visit Site (external)',
        url: 'https://google.com',
        type: 'external link'
      }
    ]
  }

}
```

在文本支持和呼叫支持按钮中使用的电话号码是在撰写时未使用的号码，由[`fakenumber.org/`](https://fakenumber.org/)生成。这个号码很可能仍然未被使用，但这可能会发生变化。请随意为这些链接使用不同的虚假号码，只需确保保持协议不变。

1.  接下来，让我们为我们的应用程序添加`render`函数。这里的 JSX 很简单：我们从上一步中的`state.links`数组中映射，将每个传递给我们在下一步中定义的`renderButton`函数：

```jsx
  render() {
    return(
      <View style={styles.container}>
        <View style={styles.buttonList}>
          {this.state.links.map(this.renderButton)}
        </View>
      </View>
    );
  }
```

1.  让我们来构建上一步中使用的`renderButton`方法。对于每个链接，我们使用`TouchableOpacity`创建一个按钮，并将`onPress`属性设置为执行`handleButtonPress`并传递`button`属性：

```jsx
  renderButton = (button, index) => {
    return(
      <TouchableOpacity
        key={index}
        onPress={() => this.handleButtonPress(button)}
        style={styles.button}
      >
        <Text style={styles.text}>{button.title}</Text>
      </TouchableOpacity>
    );
  }
```

1.  接下来，我们可以构建`handleButtonPress`函数。在这里，我们将使用`links`数组中每个对象中添加的`type`属性。如果类型是`'internal link'`，我们希望使用 Expo 的`WebBrowser`组件的`openBrowserAsync`方法在我们的应用程序中打开 URL，并且对于其他所有情况，我们将使用 React Native 的`Linking`组件的`openURL`方法。

如果`openURL`调用出现问题，并且 URL 使用了`slack://`协议，这意味着设备不知道如何处理该协议，可能是因为 Slack 应用未安装。我们将使用`handleMissingApp`函数来处理这个问题，我们将在下一步中添加它：

```jsx
  handleButtonPress(button) {
    if (button.type === 'internal link') {
      WebBrowser.openBrowserAsync(button.url);
    } else {
      Linking.openURL(button.url).catch(({ message }) => {
        if (message.includes('slack://')) {
          this.handleMissingApp();
        }
      });
    }
  }
```

1.  现在我们可以创建我们的`handleMissingApp`函数。在这里，我们使用 React Native 助手`Platform`，它提供有关应用程序运行的平台的信息。`Platform.OS`将始终返回操作系统，对于手机，应该始终解析为`'ios'`或`'android'`。您可以在官方文档中阅读有关`Platform`功能的更多信息[`facebook.github.io/react-native/docs/platform-specific-code.html`](https://facebook.github.io/react-native/docs/platform-specific-code.html)。

如果 Slack 应用的链接不像预期那样工作，我们将再次使用`Linking.openURL`，这次是为了在适合设备的应用商店中打开应用程序：

```jsx
  handleMissingApp() {
    if (Platform.OS === 'ios') {
      Linking.openURL(`https://itunes.apple.com/us/app/id618783545`);
    } else {
      Linking.openURL(
        `https://play.google.com/store/applications/details?id=com.Slack`
      );
    }
  }
```

1.  我们的应用程序还没有任何样式，所以让我们添加一些。这里没有什么花哨的东西，只是将按钮居中对齐在屏幕中，着色和居中文本，并在每个按钮上提供填充：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    justifyContent: 'center',
    alignItems: 'center',
  },
  buttonList: {
    flex: 1,
    justifyContent: 'center',
  },
  button: {
    margin: 10,
    backgroundColor: '#c0392b',
    borderRadius: 3,
    padding: 10,
    paddingRight: 30,
    paddingLeft: 30,
  },
  text: {
    color: '#fff',
    textAlign: 'center',
  },
});
```

1.  这就是这个应用程序的全部内容。一旦我们加载应用程序，应该会有一列按钮，代表我们的每个链接。`Call Support`和`Email Support`按钮在 iOS 模拟器上不起作用。在真实设备上运行此示例，以查看所有链接正常工作。

![](img/1890e7a8-6817-493e-8dc8-4b1be726e421.png)

# 它是如何工作的...

在*步骤 2*中，我们定义了应用程序使用的所有链接。每个链接对象都有一个`type`属性，我们在*步骤 5*中定义的`handleButtonPress`方法中使用它。

这个 `handleButtonPress` 函数使用链接的类型来确定将使用两种策略中的哪一种。如果链接的类型是 `'internal link'`，我们希望在应用程序内部弹出一个模态框，使用设备浏览器打开链接。为此，我们可以使用 Expo 的 `WebBrowser` 助手，将 URL 传递给它的 `openBrowserAsync` 方法。如果链接的类型是 `'external link'`，我们将使用 React Native 的 `Linking` 助手打开链接。这让您可以看到从应用程序中打开网站的不同方式。

`Linking` 助手还可以处理除了 HTTP 和 HTTPS 之外的其他协议。通过在传递给 `Linking.openURL` 的链接中简单地使用适当的协议，我们可以打开电话 (`tel:`)、短信 (`sms:`) 或电子邮件 (`mailto:`)。

`Linking.openURL` 也可以处理到其他应用程序的深层链接，只要您要链接到的应用程序具有相应的协议，例如我们如何使用 `slack://` 协议打开 Slack。有关 Slack 的深层链接协议以及您可以使用它做什么的更多信息，请访问他们的文档 [`api.slack.com/docs/deep-linking`](https://api.slack.com/docs/deep-linking)。

在 *步骤 5* 中，我们通过调用 `Linking.openURL` 引起的任何错误，检查错误是否是由 Slack 协议引起的 `message.includes('slack://')`，如果是，我们知道 Slack 应用程序未安装在设备上。在这种情况下，我们触发 `handleMissingApp`，使用由 `Platform.OS` 确定的适当链接打开 Slack 的应用商店链接。

# 另请参阅

`Linking` 模块的官方文档可以在 [`docs.expo.io/versions/latest/guides/linking.html`](https://docs.expo.io/versions/latest/guides/linking.html) 找到。

# 创建一个表单组件

大多数应用程序都需要一种输入数据的方式，无论是一个简单的注册和登录表单，还是一个具有许多输入字段和控件的更复杂的组件。

在这个示例中，我们将创建一个表单组件来处理文本输入。我们将使用不同的键盘收集数据，并显示包含结果信息的警报消息。

# 准备工作

我们需要创建一个空的应用。让我们把它命名为 `user-form`。

# 如何做...

1.  让我们首先打开 `App.js` 并添加我们的导入。导入包括我们稍后将构建的 `UserForm` 组件：

```jsx
import React from 'react';
import {
 Alert,
 StyleSheet,
 ScrollView,
 SafeAreaView,
 Text,
 TextInput,
} from 'react-native';

import UserForm from './UserForm';
```

1.  由于这个组件将非常简单，我们将为我们的`App`创建一个无状态组件。我们将只在`UserForm`组件的`ScrollView`中渲染一个顶部工具栏：

```jsx
const App = () => (
  <SafeAreaView style={styles.main}>
    <Text style={styles.toolbar}>Fitness App</Text>
    <ScrollView style={styles.content}>
      <UserForm />
    </ScrollView>
  </SafeAreaView>
);

const styles = StyleSheet.create({
  // Defined in a later step
});

export default App; 
```

1.  我们需要为这些组件添加一些样式。我们将添加一些颜色和填充，还将把`main`类设置为`flex: 1`，以填充屏幕的其余部分：

```jsx
const styles = StyleSheet.create({
  main: {
    flex: 1,
    backgroundColor: '#ecf0f1',
  },
  toolbar: {
    backgroundColor: '#1abc9c',
    padding: 20,
    color: '#fff',
    fontSize: 20,
  },
  content: {
    padding: 10,
  },
});
```

1.  我们已经定义了主要的`App`组件。现在让我们开始实际的表单工作。让我们在项目的基础上创建一个名为`UserForm`的新目录，并添加一个`index.js`文件。然后，我们将为这个类导入所有的依赖项：

```jsx
import React, { Component } from 'react';
import {
  Alert,
  StyleSheet,
  View,
  Text,
  TextInput,
  TouchableOpacity,
} from 'react-native';
```

1.  这个类将渲染输入并跟踪数据。我们将把数据保存在`state`对象上，所以我们将从初始化`state`作为空对象开始：

```jsx
export default class UserForm extends Component { 
  state = {}; 

  // Defined in a later step
} 

const styles = StyleSheet.create({ 
 // Defined in a later step
});
```

1.  在`render`方法中，我们将定义我们想要显示的组件，这种情况下是三个文本输入和一个按钮。我们将定义一个`renderTextfield`方法，它接受一个配置对象作为参数。我们将定义字段的`name`、`placeholder`和应该在输入上使用的`keyboard`类型。此外，我们还调用一个`renderButton`方法，它将渲染保存按钮：

```jsx
  render() {
    return (
      <View style={styles.panel}>
        <Text style={styles.instructions}>
          Please enter your contact information
        </Text>
        {this.renderTextfield({ name: 'name', placeholder: 'Your 
        name' })}
        {this.renderTextfield({ name: 'phone', placeholder: 'Your
        phone number', keyboard: 'phone-pad' })}
        {this.renderTextfield({ name: 'email', placeholder: 'Your 
        email address', keyboard: 'email-address'})}
        {this.renderButton()}
      </View>
    );
  }
```

1.  要渲染文本字段，我们将在`renderTextfield`方法中使用`TextInput`组件。这个`TextInput`组件由 React Native 提供，在 iOS 和 Android 上都可以使用。`keyboardType`属性允许我们设置要使用的键盘。在两个平台上有四种可用的键盘，分别是`default`、`numeric`、`email-address`和`phone-pad`：

```jsx
  renderTextfield(options) {
    return (
      <TextInput
        style={styles.textfield}
        onChangeText={(value) => this.setState({ [options.name]: 
        value })}
        placeholder={options.label}
        value={this.state[options.name]}
        keyboardType={options.keyboard || 'default'}
      />
    );
  }
```

1.  我们已经知道如何渲染按钮并响应`Press`操作。如果这不清楚，我建议阅读第三章中的*使用主题支持创建可重用按钮*配方，*实现复杂用户界面-第一部分*：

```jsx
  renderButton() {
    return (
      <TouchableOpacity
        onPress={this.handleButtonPress}
        style={styles.button}
      >
        <Text style={styles.buttonText}>Save</Text>
      </TouchableOpacity>
    );
  }
```

1.  我们需要定义`onPressButton`回调。为简单起见，我们将只显示一个带有我们在`state`对象上的输入数据的警报：

```jsx
  handleButtonPress = () => {
    const { name, phone, email } = this.state;

    Alert.alert(`User's data`,`Name: ${name}, Phone: ${phone}, 
    Email: ${email}`);
  }
```

1.  我们几乎完成了这个配方！我们需要做的就是应用一些样式-一些颜色、填充和边距；真的没什么花哨的：

```jsx
const styles = StyleSheet.create({
 panel: {
  backgroundColor: '#fff',
  borderRadius: 3,
  padding: 10,
  marginBottom: 20,
 },
 instructions: {
  color: '#bbb',
  fontSize: 16,
  marginTop: 15,
  marginBottom: 10,
 },
 textfield: {
  height: 40,
  marginBottom: 10,
 },
 button: {
  backgroundColor: '#34495e',
  borderRadius: 3,
  padding: 12,
  flex: 1,
 },
 buttonText: {
  textAlign: 'center',
  color: '#fff',
  fontSize: 16,
 },
});
```

1.  如果我们运行我们的应用程序，我们应该能够看到一个在 Android 和 iOS 上都使用本机控件的表单，这是预期的：

![](img/d8fe94c6-7280-457d-9e40-bd332add30b9.png)当在模拟器上运行应用程序时，您可能无法看到由`keyboardType`定义的键盘。在真实设备上运行应用程序，以确保`keyboardType`正确地为每个`TextInput`更改键盘。

# 工作原理...

在*步骤 8*中，我们定义了`TextInput`组件。在 React（和 React Native）中，我们可以使用两种类型的输入：受控和未受控组件。在这个示例中，我们正在使用受控输入组件，这是 React 团队推荐的。

受控组件将有一个`value`属性，并且组件将始终显示`value`属性的内容。这意味着我们需要一种方法在用户开始输入时更改值。如果我们不更新该值，那么输入框中的文本永远不会改变，即使用户尝试输入东西。

为了更新`value`，我们可以使用`onChangeText`回调并设置新值。在这个例子中，我们使用状态来跟踪数据，并在状态上设置一个新的键，其中包含输入的内容。

另一方面，一个未受控制的组件将不会有一个分配的`value`属性。我们可以使用`defaultValue`属性分配一个初始值。未受控制的组件有它们自己的状态，我们可以使用`onChangeText`回调来获取它们的值，就像我们可以使用受控组件一样。
