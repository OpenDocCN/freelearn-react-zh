# 第八章：处理应用逻辑和数据

在本章中，我们将涵盖以下内容：

+   本地存储和检索数据

+   从远程 API 检索数据

+   向远程 API 发送数据

+   与 WebSockets 建立实时通信

+   将持久数据库功能与 Realm 集成

+   在网络连接丢失时掩盖应用程序

+   将本地持久化数据与远程 API 同步

# 介绍

开发任何应用程序最重要的一个方面是处理数据。这些数据可能来自用户本地，可能由公开 API 的远程服务器提供，或者，与大多数业务应用程序一样，可能是两者的组合。您可能想知道处理数据的最佳策略是什么，或者如何甚至完成简单的任务，比如发出 HTTP 请求。幸运的是，React Native 通过提供易于处理来自各种不同来源的数据的机制，使您的生活变得更加简单。

开源社区已经进一步提供了一些可以与 React Native 一起使用的优秀模块。在本章中，我们将讨论如何处理各个方面的数据，以及如何将其整合到我们的 React Native 应用程序中。

# 本地存储和检索数据

在开发移动应用程序时，我们需要考虑需要克服的网络挑战。一个设计良好的应用程序应该允许用户在没有互联网连接时继续使用应用程序。这需要应用程序在没有互联网连接时在设备上本地保存数据，并在网络再次可用时将数据与服务器同步。

另一个需要克服的挑战是网络连接，可能会很慢或有限。为了提高我们应用的性能，我们应该将关键数据保存在本地设备上，以避免对服务器 API 造成压力。

在这个示例中，我们将学习一种从设备本地保存和检索数据的基本有效策略。我们将创建一个简单的应用程序，其中包含一个文本输入和两个按钮，一个用于保存字段内容，另一个用于加载现有内容。我们将使用`AsyncStorage`类来实现我们的目标。

# 准备工作

我们需要创建一个名为`local-data-storage`的空应用程序。

# 如何做...

1.  我们将从`App`组件开始。让我们首先导入所有的依赖项：

```jsx
import React, { Component } from 'react';
import {
  Alert,
  AsyncStorage,
  StyleSheet,
  Text,
  TextInput,
  TouchableOpacity,
  View,
} from 'react-native';
```

1.  现在，让我们创建`App`类。我们将创建一个`key`常量，以便我们可以设置要用于保存内容的键的名称。在`state`中，我们将有两个属性：一个用于保存来自文本输入组件的值，另一个用于加载和显示当前存储的值：

```jsx
const key = '@MyApp:key';

export default class App extends Component {
  state = {
    text: '',
    storedValue: '',
  };
  //Defined in later steps
}
```

1.  当组件挂载时，如果存在，我们希望加载已存储的值。一旦应用程序加载，我们将显示内容，因此我们需要在`componentWillMount`生命周期方法中读取本地值：

```jsx
  componentWillMount() {
    this.onLoad();
  }
```

1.  `onLoad`函数从本地存储加载当前内容。就像浏览器中的`localStorage`一样，只需使用保存数据时定义的键即可：

```jsx
  onLoad = async () => {
    try {
      const storedValue = await AsyncStorage.getItem(key);
      this.setState({ storedValue });
    } catch (error) {
      Alert.alert('Error', 'There was an error while loading the 
      data');
    }
  }
```

1.  保存数据也很简单。我们将声明一个键，通过`AsyncStorage`的`setItem`方法保存我们想要与该键关联的任何数据：

```jsx
  onSave = async () => {
    const { text } = this.state;

    try {
      await AsyncStorage.setItem(key, text);
      Alert.alert('Saved', 'Successfully saved on device');
    } catch (error) {
      Alert.alert('Error', 'There was an error while saving the
      data');
    }
  }
```

1.  接下来，我们需要一个函数，将输入文本中的值保存到`state`中。当输入的值发生变化时，我们将获取新的值并保存到`state`中：

```jsx
        onChange = (text) => { 
          this.setState({ text }); 
        }  
```

1.  我们的 UI 将很简单：只有一个`Text`元素来呈现保存的内容，一个`TextInput`组件允许用户输入新值，以及两个按钮。一个按钮将调用`onLoad`函数来加载当前保存的值，另一个将保存文本输入的值：

```jsx
  render() {
    const { storedValue, text } = this.state;

    return (
      <View style={styles.container}>
        <Text style={styles.preview}>{storedValue}</Text>
        <View>
          <TextInput
            style={styles.input}
            onChangeText={this.onChange}
            value={text}
            placeholder="Type something here..."
          />
          <TouchableOpacity onPress={this.onSave} style=
          {styles.button}>
            <Text>Save locally</Text>
          </TouchableOpacity>
          <TouchableOpacity onPress={this.onLoad} style=
          {styles.button}>
            <Text>Load data</Text>
          </TouchableOpacity>
        </View>
      </View>
    );
  }
```

1.  最后，让我们添加一些样式。这将是简单的颜色、填充、边距和布局，如第二章中所述，*创建一个简单的 React Native 应用*：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#fff',
  },
  preview: {
    backgroundColor: '#bdc3c7',
    width: 300,
    height: 80,
    padding: 10,
    borderRadius: 5,
    color: '#333',
    marginBottom: 50,
  },
  input: {
    backgroundColor: '#ecf0f1',
    borderRadius: 3,
    width: 300,
    height: 40,
    padding: 5,
  },
  button: {
    backgroundColor: '#f39c12',
    padding: 10,
    borderRadius: 3,
    marginTop: 10,
  },
});
```

1.  最终的应用程序应该类似于以下截图：

![](img/1e7aa953-2b8d-4c6c-be09-463c40c90198.png)

# 它是如何工作的...

`AsyncStorage`类允许我们轻松地在本地设备上保存数据。在 iOS 上，这是通过在文本文件上使用字典来实现的。在 Android 上，它将使用 RocksDB 或 SQLite，具体取决于可用的内容。

不建议使用此方法保存敏感信息，因为数据未加密。

在*步骤 4*中，我们加载了当前保存的数据。`AsyncStorage` API 包含一个`getItem`方法。该方法接收我们要检索的键作为参数。我们在这里使用`await`/`async`语法，因为这个调用是异步的。获取值后，我们只需将其设置为`state`；这样，我们就能在视图上呈现数据。

在*步骤 7*中，我们保存了`state`中的文本。使用`setItem`方法，我们可以设置一个新的`key`和任何我们想要的值。这个调用是异步的，因此我们使用了`await`/`async`语法。

# 另请参阅

关于 JavaScript 中`async`/`await`的工作原理的一篇很棒的文章，可在[`ponyfoo.com/articles/understanding-javascript-async-await`](https://ponyfoo.com/articles/understanding-javascript-async-await)上找到。

# 从远程 API 检索数据

在之前的章节中，我们使用了来自 JSON 文件或直接在源代码中定义的数据。虽然这对我们之前的示例很有用，但在现实世界的应用程序中很少有帮助。

在这个示例中，我们将学习如何从 API 请求数据。我们将从 API 中进行`GET`请求，以获得 JSON 响应。然而，现在，我们只会在文本元素中显示 JSON。我们将使用 Fake Online REST API for Testing and Prototyping，托管在[`jsonplaceholder.typicode.com`](http://jsonplaceholder.typicode.com)，由优秀的开发测试 API 软件 JSON Server（[`github.com/typicode/json-server`](https://github.com/typicode/json-server)）提供支持。

我们将保持这个应用程序简单，以便我们可以专注于数据管理。我们将有一个文本组件，用于显示来自 API 的响应，并添加一个按钮，按下时请求数据。

# 准备工作

我们需要创建一个空的应用。让我们把这个命名为`remote-api`。

# 如何做...

1.  让我们从`App.js`文件中导入我们的依赖项开始：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  TextInput,
  TouchableOpacity,
  View
} from 'react-native';
```

1.  我们将在`state`上定义一个`results`属性。这个属性将保存来自 API 的响应。一旦我们得到响应，我们需要更新视图：

```jsx
export default class App extends Component {
  state = {
    results: '',
  };
  // Defined later
}

const styles = StyleSheet.create({
  // Defined later
});
```

1.  当按钮被按下时，我们将发送请求。接下来，让我们创建一个处理该请求的方法：

```jsx
  onLoad = async () => {
    this.setState({ results: 'Loading, please wait...' });
    const response = await fetch('http://jsonplaceholder.typicode.com/users', {
      method: 'GET',
    });
    const results = await response.text();
    this.setState({ results });
  }
```

1.  在`render`方法中，我们将显示来自`state`的响应。我们将使用`TextInput`来显示 API 数据。通过属性，我们将声明编辑为禁用，并支持多行功能。按钮将调用我们在上一步中创建的`onLoad`函数：

```jsx
  render() {
    const { results } = this.state;

    return (
      <View style={styles.container}>
        <View>
          <TextInput
            style={styles.preview}
            value={results}
            placeholder="Results..."
            editable={false}
            multiline
          />
          <TouchableOpacity onPress={this.onLoad} style=
          {styles.btn}>
            <Text>Load data</Text>
          </TouchableOpacity>
        </View>
      </View>
    );
  }
```

1.  最后，我们将添加一些样式。同样，这只是布局、颜色、边距和填充：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#fff',
  },
  preview: {
    backgroundColor: '#bdc3c7',
    width: 300,
    height: 400,
    padding: 10,
    borderRadius: 5,
    color: '#333',
    marginBottom: 50,
  },
  btn: {
    backgroundColor: '#3498db',
    padding: 10,
    borderRadius: 3,
    marginTop: 10,
  },
});
```

1.  最终的应用程序应该类似于以下截图：

![](img/9374a894-a2e5-4de5-89a2-84ab98ecfb23.png)

# 它是如何工作的...

在*步骤 4*中，我们向 API 发送了请求。我们使用`fetch`方法发出请求。第一个参数是一个包含端点 URL 的字符串，而第二个参数是一个配置对象。对于这个请求，我们需要定义的唯一选项是`request`方法为`GET`，但我们也可以使用这个对象来定义头部、cookie、参数和许多其他内容。

我们还使用`async`/`await`语法来等待响应，并最终将其设置在`state`上。如果您愿意，当然也可以使用承诺来实现这个目的。

还要注意，我们在这里使用箭头函数来正确处理作用域。当将此方法分配给`onPress`回调时，这将自动设置正确的作用域。

# 向远程 API 发送数据

在上一个教程中，我们介绍了如何使用`fetch`从 API 获取数据。在这个教程中，我们将学习如何向同一个 API 发送数据。这个应用程序将模拟创建一个论坛帖子，帖子的请求将具有`title`、`body`和`user`参数。

# 如何做到这一点...准备好

在进行本教程之前，我们需要创建一个名为`remote-api-post`的新空应用程序。

在这个教程中，我们还将使用非常流行的`axios`包来处理我们的 API 请求。您可以通过终端使用`yarn`安装它：

```jsx
yarn add axios
```

或者，您也可以使用`npm`：

```jsx
npm install axios --save
```

# 首先，我们需要从`state`中获取值。我们还可以在这里运行一些验证，以确保`title`和`body`不为空。在`POST`请求中，我们需要定义请求的内容类型，这种情况下将是 JSON。我们将`userId`属性硬编码为`1`。在真实的应用程序中，我们可能会从之前的 API 请求中获取这个值。请求完成后，我们获取 JSON 响应，如果成功，将触发我们之前定义的`onLoad`方法：

1.  首先，我们需要打开`App.js`文件并导入我们将要使用的依赖项：

```jsx
import React, { Component } from 'react';
import axios from 'axios';
import {
  Alert,
  ScrollView,
  StyleSheet,
  Text,
  TextInput,
  TouchableOpacity,
  SafeAreaView,
} from 'react-native';
```

1.  我们将定义`App`类，其中包含一个具有三个属性的`state`对象。`title`和`body`属性将用于发出请求，`results`将保存 API 的响应：

```jsx
const endpoint = 'http://jsonplaceholder.typicode.com/posts';

export default class App extends Component {
  state = {
    results: '',
    title: '',
    body: '',
  };

  const styles = StyleSheet.create({ 
    // Defined later 
  }); 
}
```

1.  保存新帖子后，我们将从 API 请求所有帖子。我们将定义一个`onLoad`方法来获取新数据。这段代码与上一个教程中的`onLoad`方法完全相同，但这次，我们将使用`axios`包来创建请求：

```jsx
  onLoad = async () => {
    this.setState({ results: 'Loading, please wait...' });
    const response = await axios.get(endpoint);
    const results = JSON.stringify(response);
    this.setState({ results });
  }
```

1.  让我们开始保存新数据。

```jsx
  onSave = async () => {
    const { title, body } = this.state;
    try {
      const response = await axios.post(endpoint, {
        headers: {
          'Content-Type': 'application/json;charset=UTF-8',
        },
        params: {
          userId: 1,
          title,
          body
        }
      });
      const results = JSON.stringify(response);
      Alert.alert('Success', 'Post successfully saved');
      this.onLoad();
    } catch (error) {
      Alert.alert('Error', `There was an error while saving the 
      post: ${error}`);
    }
  }
```

1.  保存功能已经完成。接下来，我们需要方法来保存`title`和`body`到`state`。这些方法将在用户在输入文本中输入时执行，跟踪`state`对象上的值：

```jsx
  onTitleChange = (title) => this.setState({ title });
  onPostChange = (body) => this.setState({ body });
```

1.  我们已经拥有了功能所需的一切，所以让我们添加 UI。`render`方法将显示一个工具栏、两个输入文本和一个保存按钮，用于调用我们在*步骤 4*中定义的`onSave`方法：

```jsx
  render() {
    const { results, title, body } = this.state;

    return (
      <SafeAreaView style={styles.container}>
        <Text style={styles.toolbar}>Add a new post</Text>
        <ScrollView style={styles.content}>
          <TextInput
            style={styles.input}
            onChangeText={this.onTitleChange}
            value={title}
            placeholder="Title"
          />
          <TextInput
            style={styles.input}
            onChangeText={this.onPostChange}
            value={body}
            placeholder="Post body..."
          />
          <TouchableOpacity onPress={this.onSave} style=
          {styles.button}>
            <Text>Save</Text>
          </TouchableOpacity>
          <TextInput
            style={styles.preview}
            value={results}
            placeholder="Results..."
            editable={false}
            multiline
          />
        </ScrollView>
      </SafeAreaView>
    );
  }
```

1.  最后，让我们添加样式来定义布局、颜色、填充和边距：

```jsx
const styles = StyleSheet.create({
 container: {
  flex: 1,
  backgroundColor: '#fff',
 },
 toolbar: {
  backgroundColor: '#3498db',
  color: '#fff',
  textAlign: 'center',
  padding: 25,
  fontSize: 20,
 },
 content: {
  flex: 1,
  padding: 10,
 },
 preview: {
  backgroundColor: '#bdc3c7',
  flex: 1,
  height: 500,
 },
 input: {
  backgroundColor: '#ecf0f1',
  borderRadius: 3,
  height: 40,
  padding: 5,
  marginBottom: 10,
  flex: 1,
 },
 button: {
  backgroundColor: '#3498db',
  padding: 10,
  borderRadius: 3,
  marginBottom: 30,
 },
});
```

1.  最终的应用程序应该类似于以下截图：

![](img/52d9a6cb-2df6-4112-be62-3f1e3431970b.png)

# 工作原理...

在*步骤 2*中，我们在`state`上定义了三个属性。`results`属性将包含来自服务器 API 的响应，我们稍后将用它来在 UI 中显示值。

我们使用`title`和`body`属性来保存输入文本组件中的值，以便用户可以创建新的帖子。然后，这些值将在按下保存按钮时发送到 API。

在*步骤 6*中，我们在 UI 上声明了元素。我们使用了两个输入来输入数据和一个保存按钮，当按下时调用`onSave`方法。最后，我们使用输入文本来显示结果。

# 使用 WebSockets 建立实时通信

在本教程中，我们将在 React Native 应用程序中集成 WebSockets。我们将使用 WebSockets 应用程序的*Hello World*，也就是一个简单的聊天应用程序。这个应用程序将允许用户发送和接收消息。

# 准备工作

为了在 React Native 上支持 WebSockets，我们需要运行一个服务器来处理所有连接的客户端。服务器应该能够在收到任何连接的客户端的消息时广播消息。

我们将从一个新的空白 React Native 应用程序开始。我们将其命名为`web-sockets`。在项目的根目录下，让我们添加一个带有`index.js`文件的`server`文件夹。如果您还没有它，您将需要 Node 来运行服务器。您可以从[`nodejs.org/`](https://nodejs.org/)获取 Node.js，或者使用 Node 版本管理器([`github.com/creationix/nvm`](https://github.com/creationix/nvm))。

我们将使用优秀的 WebSocket 包`ws`。您可以通过终端使用`yarn`添加该包：

```jsx
yarn add ws
```

或者，您可以使用`npm`：

```jsx
npm install --save ws
```

安装完包后，将以下代码添加到`/server/index.js`文件中。一旦此服务器运行，它将通过`server.on('connection')`监听传入的连接，并通过`socket.on('message')`监听传入的消息。有关`ws`的更多信息，请查看[`github.com/websockets/ws`](https://github.com/websockets/ws)上的文档：

```jsx
const port = 3001;
const WebSocketServer = require('ws').Server;
const server = new WebSocketServer({ port });

server.on('connection', (socket) => {
  socket.on('message', (message) => {
    console.log('received: %s', message);

    server.clients.forEach(client => {
      if (client !== socket) {
        client.send(message);
      }
    });
  });
});

console.log(`Web Socket Server running on port ${port}`);
```

一旦服务器代码就位，您可以通过在项目根目录的终端中运行以下命令来启动服务器：

```jsx
node server/index.js
```

让服务器保持运行，这样一旦我们构建了 React Native 应用程序，我们就可以使用服务器在客户端之间进行通信。

# 如何做到这一点...

1.  首先，让我们创建`App.js`文件并导入我们将使用的所有依赖项：

```jsx
import React, { Component } from 'react';
import {
  Dimensions,
  ScrollView,
  StyleSheet,
  Text,
  TextInput,
  SafeAreaView,
  View,
  Platform
} from 'react-native';
```

1.  在`state`对象上，我们将声明一个`history`属性。该属性将是一个数组，用于保存用户之间来回发送的所有消息：

```jsx
export default class App extends Component {
  state = {
    history: [],
  };
    // Defined in later steps 
} 

const styles = StyleSheet.create({ 
  // Defined in later steps 
}); 
```

1.  现在，我们需要通过连接到服务器并设置用于接收消息、错误以及连接打开或关闭时的回调函数来将 WebSockets 集成到我们的应用中。我们将在组件创建后执行此操作，使用`componentWillMount`生命周期钩子：

```jsx
  componentWillMount() {
    const localhost = Platform.OS === 'android' ? '10.0.3.2' :
    'localhost';

    this.ws = new WebSocket(`ws://${localhost}:3001`);
    this.ws.onopen = this.onOpenConnection;
    this.ws.onmessage = this.onMessageReceived;
    this.ws.onerror = this.onError;
    this.ws.onclose = this.onCloseConnection;
  }
```

1.  让我们定义打开/关闭连接和处理接收到的错误的回调。我们只是要记录这些操作，但这是我们可以在连接关闭时显示警报消息，或者在服务器抛出错误时显示错误消息的地方：

```jsx
  onOpenConnection = () => {
    console.log('Open!');
  }

  onError = (event) => {
    console.log('onerror', event.message);
  }

  onCloseConnection = (event) => {
    console.log('onclose', event.code, event.reason);
  }
```

1.  当从服务器接收到新消息时，我们需要将其添加到`state`上的`history`属性中，以便在消息到达时能够立即呈现新内容：

```jsx
  onMessageReceived = (event) => {
    this.setState({
      history: [
        ...this.state.history,
        { isSentByMe: false, messageText: event.data },
      ],
    });
  }
```

1.  现在，让我们发送消息。我们需要定义一个方法，当用户在键盘上按下*Return*键时将执行该方法。此时我们需要做两件事：将新消息添加到`history`中，然后通过 socket 发送消息：

```jsx
  onSendMessage = () => {
    const { text } = this.state;

    this.setState({
      text: '',
      history: [
        ...this.state.history,
        { isSentByMe: true, messageText: text },
      ],
    });
    this.ws.send(text);
  }
```

1.  在上一步中，我们从`state`中获取了`text`属性。每当用户在输入框中输入内容时，我们需要跟踪该值，因此我们需要一个用于监听按键并将值保存到`state`的函数：

```jsx
  onChangeText = (text) => {
    this.setState({ text });
  }
```

1.  我们已经就位了所有功能，现在让我们来处理 UI。在`render`方法中，我们将添加一个工具栏，一个滚动视图来呈现`history`中的所有消息，以及一个文本输入框，允许用户发送新消息：

```jsx
  render() {
    const { history, text } = this.state;

    return (
      <SafeAreaView style={[styles.container, android]}>
        <Text style={styles.toolbar}>Simple Chat</Text>
        <ScrollView style={styles.content}>
          { history.map(this.renderMessage) }
        </ScrollView>
        <View style={styles.inputContainer}>
          <TextInput
            style={styles.input}
            value={text}
            onChangeText={this.onChangeText}
            onSubmitEditing={this.onSendMessage}
          />
        </View>
      </SafeAreaView>
    );
  }
```

1.  为了渲染来自`history`的消息，我们将遍历`history`数组，并通过`renderMessage`方法渲染每条消息。我们需要检查当前消息是否属于此设备上的用户，以便我们可以应用适当的样式：

```jsx
  renderMessage(item, index){
    const sender = item.isSentByMe ? styles.me : styles.friend;

    return (
      <View style={[styles.msg, sender]} key={index}>
        <Text>{item.msg}</Text>
      </View>
    );
  }
```

1.  最后，让我们来处理样式！让我们为工具栏、`history`组件和文本输入添加样式。我们需要将`history`容器设置为灵活，因为我们希望它占用所有可用的垂直空间：

```jsx
const styles = StyleSheet.create({
  container: {
    backgroundColor: '#ecf0f1',
    flex: 1,
  },
  toolbar: {
    backgroundColor: '#34495e',
    color: '#fff',
    fontSize: 20,
    padding: 25,
    textAlign: 'center',
  },
  content: {
    flex: 1,
  },
  inputContainer: {
    backgroundColor: '#bdc3c7',
    padding: 5,
  },
  input: {
    height: 40,
    backgroundColor: '#fff',
  },
  // Defined in next step
}); 
```

1.  现在，让我们来处理每条消息的样式。我们将为所有消息创建一个名为`msg`的公共样式对象，然后为来自设备上用户的消息创建样式，最后为来自其他人的消息创建样式，相应地更改颜色和对齐方式：

```jsx
  msg: {
    margin: 5,
    padding: 10,
    borderRadius: 10,
  },
  me: {
    alignSelf: 'flex-start',
    backgroundColor: '#1abc9c',
    marginRight: 100,
  },
  friend: {
    alignSelf: 'flex-end',
    backgroundColor: '#fff',
    marginLeft: 100,
  }
```

1.  最终的应用程序应该类似于以下屏幕截图：

![](img/71f2d573-5ccf-46f4-828f-89463af001bb.png)

# 它是如何工作的...

在*步骤 2*中，我们声明了`state`对象，其中包含一个用于跟踪消息的`history`数组。`history`属性将保存表示客户端之间交换的所有消息的对象。每个对象都将有两个属性：包含消息文本的字符串，以及用于确定发送者的布尔标志。我们可以在这里添加更多数据，例如用户的名称、头像图像的 URL，或者我们可能需要的其他任何内容。

在*步骤 3*中，我们连接到 WebSocket 服务器提供的套接字，并设置了处理套接字事件的回调。我们指定了服务器地址以及端口。

在*步骤 5*中，我们定义了当从服务器接收到新消息时要执行的回调。在这里，每次接收到新消息时，我们都会向`state`的`history`数组添加一个新对象。每个消息对象都有`isSentByMe`的属性

`messageText`。

在*步骤 6*中，我们将消息发送到服务器。我们需要将消息添加到历史记录中，因为服务器将向所有其他客户端广播消息，但不包括消息的作者。为了跟踪这条消息，我们需要手动将其添加到历史记录中。

# 将持久数据库功能与 Realm 集成

随着您的应用程序变得更加复杂，您可能会达到需要在设备上存储数据的地步。这可能是业务数据，比如用户列表，以避免不得不进行昂贵的网络连接到远程 API。也许您根本没有 API，您的应用程序作为一个自给自足的实体运行。无论情况如何，您可能会受益于利用数据库来存储您的数据。对于 React Native 应用程序有多种选择。第一个选择是 `AsyncStorage`，我们在本章的 *存储和检索本地数据* 教程中介绍过。您还可以考虑 SQLite，或者您可以编写一个适配器来连接到特定于操作系统的数据提供程序，比如 Core Data。

另一个极好的选择是使用移动数据库，比如 Realm。Realm 是一个非常快速、线程安全、事务性、基于对象的数据库。它主要设计用于移动设备，具有直观的 JavaScript API。它支持其他功能，如加密、复杂查询、UI 绑定等。您可以在 [`realm.io/products/realm-mobile-database/`](https://realm.io/products/realm-mobile-database/) 上阅读有关它的所有信息。

在本教程中，我们将介绍在 React Native 中使用 Realm。我们将创建一个简单的数据库，并执行基本操作，如插入、更新和删除记录。然后我们将在 UI 中显示这些记录。

# 准备工作

让我们创建一个名为 `realm-db` 的新的空白 React Native 应用程序。

安装 Realm 需要运行以下命令：

```jsx
  react-native link
```

因此，我们将致力于一个从 Expo 弹出的应用程序。这意味着您可以使用以下命令创建此应用程序：

```jsx
 react-native init 
```

或者，您可以使用以下命令创建一个新的 Expo 应用程序：

```jsx
  expo init 
```

然后，您可以通过以下命令弹出使用 Expo 创建的应用程序：

```jsx
 expo eject 
```

创建 React Native 应用程序后，请务必通过 `cd` 在新应用程序中使用 `ios` 目录安装 CocoaPods 依赖项，并运行以下命令：

```jsx
 pod install
```

有关 CocoaPods 工作原理的详细解释以及弹出（或纯粹的 React Native）应用程序与 Expo React Native 应用程序的区别，请参阅第十章 *应用程序工作流程和第三方插件*。

在*将数据发送到远程 API*的示例中，我们使用了`axios`包来处理我们的 AJAX 调用。在这个示例中，我们将使用原生 JavaScript 的 `fetch` 方法进行 AJAX 调用。两种方法都同样有效，对两种方法都有所了解，希望能让您决定您更喜欢哪种方法来进行您的项目。

一旦您处理了创建一个弹出式应用程序，就可以使用 `yarn` 安装 Realm：

```jsx
yarn add realm
```

或者，您可以使用 `npm`：

```jsx
npm install --save realm
```

安装了包之后，您可以使用以下代码链接本机包：

```jsx
react-native link realm
```

# 如何做...

1.  首先，让我们打开 `App.js` 并导入我们将要使用的依赖项：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  TouchableOpacity
} from 'react-native';
import Realm from 'realm';
```

1.  接下来，我们需要在 `componentWillMount` 方法中实例化我们的 Realm 数据库。我们将使用 `realm` 类变量保留对它的引用：

```jsx
export default class App extends Component {
  realm;
  componentWillMount() {
    const realm = this.realm = new Realm({
     schema: [
      {
        name: 'User',
        properties: {
          firstName: 'string',
          lastName: 'string',
          email: 'string'
        }
      }
   ]
 });
 }
 // Defined in later steps.
}
```

1.  为了创建 `User` 条目，我们将使用[randomuser.me](http://randomuser.me)提供的随机用户生成器 API。让我们创建一个带有 `getRandomUser` 函数的方法。这将 `fetch` 这些数据：

```jsx
  getRandomUser() {
    return fetch('https://randomuser.me/api/')
      .then(response => response.json());
  }
```

1.  我们还需要一个在我们的应用程序中创建用户的方法。`createUser` 方法将使用我们之前定义的函数来获取一个随机用户，然后使用 `realm.write` 方法和 `realm.create` 方法将其保存到我们的 realm 数据库中：

```jsx
  createUser = () => {
    const realm = this.realm;

    this.getRandomUser().then((response) => {
      const user = response.results[0];
      const userName = user.name;
      realm.write(() => {
        realm.create('User', {
          firstName: userName.first,
          lastName: userName.last,
          email: user.email
        });
        this.setState({users:realm.objects('User')});
      });
    });
  }
```

1.  由于我们正在与数据库交互，我们还应该添加一个用于更新数据库中的 `User` 的函数。`updateUser` 将简单地获取集合中的第一条记录并更改其信息：

```jsx
  updateUser = () => {
    const realm = this.realm;
    const users = realm.objects('User');

    realm.write(() => {
      if(users.length) {
        let firstUser = users.slice(0,1)[0];
        firstUser.firstName = 'Bob';
        firstUser.lastName = 'Cookbook';
        firstUser.email = 'react.native@cookbook.com';
        this.setState(users);
      }
    });
  }
```

1.  最后，让我们添加一种删除用户的方法。我们将添加一个 `deleteUsers` 方法来删除所有用户。这是通过调用带有执行 `realm.deleteAll` 的回调函数的 `realm.write` 实现的：

```jsx
  deleteUsers = () => {
    const realm = this.realm;
    realm.write(() => {
      realm.deleteAll();
      this.setState({users:realm.objects('User')});
    });
  }
```

1.  让我们构建我们的用户界面。我们将呈现一个 `User` 对象列表和一个按钮，用于我们的 `create`、`update` 和 `delete` 方法：

```jsx
  render() {
    const realm = this.realm;
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to Realm DB Test!
        </Text>
        <View style={styles.buttonContainer}>
           <TouchableOpacity style={styles.button}
           onPress={this.createUser}>
            <Text style={styles.buttontext}>Add User</Text>
          </TouchableOpacity>
           <TouchableOpacity style={styles.button}
            onPress={this.updateUser}>
            <Text>Update First User</Text>
          </TouchableOpacity>
           <TouchableOpacity style={styles.button}
            onPress={this.deleteUsers}>
            <Text>Remove All Users</Text>
          </TouchableOpacity>
        </View>
        <View style={styles.container}>
        <Text style={styles.welcome}>Users:</Text>
        {this.state.users.map((user, idx) => {
          return <Text key={idx}>{user.firstName} {user.lastName}
         {user.email}</Text>;
        })}
        </View>
      </View>
    );
  }
```

1.  一旦我们在任何平台上运行应用程序，我们与数据库交互的三个按钮应该显示在我们的 Realm 数据库中保存的实时数据上：

![](img/8b14a014-e823-46b5-bf7d-34ba1de2733d.png)

# 它是如何工作的...

Realm 数据库是用 C++构建的，其核心被称为**Realm 对象存储**。有一些产品为每个主要平台（Java、Objective-C、Swift、Xamarin 和 React Native）封装了这个对象存储。React Native 的实现是 Realm 的 JavaScript 适配器。从 React Native 方面来看，我们不需要担心实现细节。相反，我们得到了一个用于持久化和检索数据的清晰 API。*步骤 4*到*步骤 6*演示了使用一些基本的 Realm 方法。如果您想了解 API 的更多用法，请查看此文档，网址为[`realm.io/docs/react-native/latest/api/`](https://realm.io/docs/react-native/latest/api)。

# 在网络连接丢失时对应用进行屏蔽

互联网连接并不总是可用，特别是当人们在城市中移动、乘坐火车或在山上徒步时。良好的用户体验将在用户失去与互联网的连接时通知用户。

在这个示例中，我们将创建一个应用，当网络连接丢失时会显示一条消息。

# 准备工作

我们需要创建一个空的应用。让我们把它命名为`network-loss`。

# 如何做...

1.  让我们从`App.js`中导入必要的依赖项开始：

```jsx
import React, { Component } from 'react';
import {
  SafeAreaView,
  NetInfo,
  StyleSheet,
  Text,
  View,
  Platform
} from 'react-native';
```

1.  接下来，我们将定义`App`类和一个用于存储连接状态的`state`对象。如果连接成功，`online`布尔值将为`true`，如果连接失败，`offline`布尔值将为`true`：

```jsx
export default class App extends Component {
  state = {
    online: null,
    offline: null,
  };

  // Defined in later steps
}
```

1.  组件创建后，我们需要获取初始网络状态。我们将使用`NetInfo`类的`getConnectionInfo`方法来获取当前状态，并且我们还将设置一个回调，当状态改变时将执行该回调：

```jsx
  componentWillMount() {
    NetInfo.getConnectionInfo().then((connectionInfo) => {
      this.onConnectivityChange(connectionInfo);
    });
    NetInfo.addEventListener('connectionChange', 
    this.onConnectivityChange);
  }
```

1.  当组件即将被销毁时，我们需要通过`componentWillUnmount`生命周期来移除监听器：

```jsx
  componentWillUnmount() {
    NetInfo.removeEventListener('connectionChange', 
    this.onConnectivityChange);
  }
```

1.  让我们添加一个在网络状态改变时执行的回调。它只是检查当前的网络类型是否为`none`，并相应地设置`state`：

```jsx
  onConnectivityChange = connectionInfo => {
    this.setState({
      online: connectionInfo.type !== 'none',
      offline: connectionInfo.type === 'none',
    });
  }
```

1.  现在，我们知道网络是开启还是关闭，但我们仍然需要一个用于显示信息的用户界面。让我们渲染一个带有一些虚拟文本的工具栏作为内容：

```jsx
  render() {
    return (
      <SafeAreaView style={styles.container}>
        <Text style={styles.toolbar}>My Awesome App</Text>
        <Text style={styles.text}>Lorem...</Text>
        <Text style={styles.text}>Lorem ipsum...</Text>
        {this.renderMask()}
      </SafeAreaView>
    );
  }
```

1.  正如您从上一步中看到的，有一个`renderMask`函数。当网络离线时，此函数将返回一个模态，如果在线则什么都不返回：

```jsx
  renderMask() {
    if (this.state.offline) {
      return (
        <View style={styles.mask}>
          <View style={styles.msg}>
            <Text style={styles.alert}>Seems like you do not have
             network connection anymore.</Text>
            <Text style={styles.alert}>You can still continue
            using the app, with limited content.</Text>
          </View>
        </View>
      );
    }
  }
```

1.  最后，让我们为我们的应用添加样式。我们将从工具栏和内容开始：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F5FCFF',
  },
  toolbar: {
    backgroundColor: '#3498db',
    padding: 15,
    fontSize: 20,
    color: '#fff',
    textAlign: 'center',
  },
  text: {
    padding: 10,
  },
  // Defined in next step
}
```

1.  对于断开连接的消息，我们将在所有内容的顶部呈现一个黑色蒙版，并在屏幕中央放置一个带有文本的容器。对于`mask`，我们需要将位置设置为`absolute`，然后将`top`、`bottom`、`right`和`left`设置为`0`。我们还将为 mask 的背景颜色添加不透明度，并将内容调整和对齐到中心：

```jsx
const styles = StyleSheet.create({
  // Defined in previous step
  mask: {
    alignItems: 'center',
    backgroundColor: 'rgba(0, 0, 0, 0.5)',
    bottom: 0,
    justifyContent: 'center',
    left: 0,
    position: 'absolute',
    top: 0,
    right: 0,
  },
  msg: {
    backgroundColor: '#ecf0f1',
    borderRadius: 10,
    height: 200,
    justifyContent: 'center',
    padding: 10,
    width: 300,
  },
  alert: {
    fontSize: 20,
    textAlign: 'center',
    margin: 5,
  }
});
```

1.  要在模拟器中看到蒙版显示，模拟设备必须断开与互联网的连接。对于 iOS 模拟器，只需断开 Mac 的 Wi-Fi 或拔掉以太网即可断开模拟器与互联网的连接。在 Android 模拟器上，您可以通过工具栏禁用手机的 Wi-Fi 连接：

![](img/0f528871-6c98-4795-95cf-0d10f0aef02b.png)![](img/0e357b80-81b3-4a0c-8ba1-1f4e00d9fa9b.png)

1.  一旦设备与互联网断开连接，蒙版应该相应地显示：

![](img/23f4c602-c56c-4ad1-9507-825302567b9b.png)

# 工作原理...

在*步骤 2*中，我们创建了初始的`state`对象，其中包含两个属性：`online`在网络连接可用时为`true`，`offline`在网络不可用时为`true`。

在*步骤 3*中，我们检索了初始的网络状态并设置了一个监听器来检查状态的变化。`NetInfo`返回的网络类型可以是`wifi`、`cellular`、`unknown`或`none`。Android 还有额外的选项，如`bluetooth`、`ethernet`和`WiMAX`（用于 WiMAX 连接）。您可以阅读文档以查看所有可用的值：[`facebook.github.io/react-native/docs/netinfo.html`](https://facebook.github.io/react-native/docs/netinfo.html)。

在*步骤 5*中，我们定义了每当网络状态发生变化时执行的方法，并相应地设置了`state`的值`online`和`offline`。更新状态会重新渲染 DOM，如果没有连接，则会显示蒙版。

# 将本地持久化数据与远程 API 同步

在使用移动应用程序时，网络连接通常是理所当然的。但是当您的应用程序需要调用 API 并且用户刚刚失去连接时会发生什么？幸运的是，对于我们来说，React Native 有一个模块可以对网络连接状态做出反应。我们可以以一种支持连接丢失的方式设计我们的应用程序，通过在网络连接恢复后自动同步我们的数据来实现。

这个步骤将展示使用`NetInfo`模块来控制我们的应用是否会进行 API 调用的简单实现。如果失去连接，我们将保留挂起请求的引用，并在网络访问恢复时完成它。我们将再次使用[`jsonplaceholder.typicode.com`](http://jsonplaceholder.typicode.com)来向实时服务器发出`POST`请求。

# 准备工作

对于这个步骤，我们将使用一个名为`syncing-data`的空的 React Native 应用程序。

# 如何做...

1.  我们将从在`App.js`中导入我们的依赖项开始这个步骤：

```jsx
import React from 'react';
import {
  StyleSheet,
  Text,
  View,
  NetInfo,
  TouchableOpacity
} from 'react-native';
```

1.  我们需要添加`pendingSync`类变量，用于在没有可用网络连接时存储挂起的请求。我们还将创建`state`对象，其中包含用于跟踪应用程序是否连接（`isConnected`）、同步状态（`syncStatus`）以及发出`POST`请求后服务器的响应（`serverResponse`）的属性：

```jsx
export default class App extends React.Component {
  pendingSync;

  state = {
    isConnected: null,
    syncStatus: null,
    serverResponse: null
  }

  // Defined in later steps
}
```

1.  在`componentWillMount`生命周期钩子中，我们将通过`NetInfo.isConnected.fetch`方法获取网络连接的状态，并使用响应设置状态的`isConnected`属性。我们还将添加一个`connectionChange`事件的事件侦听器，以跟踪连接的变化：

```jsx
  componentWillMount() {
    NetInfo.isConnected.fetch().then(isConnected => {
      this.setState({isConnected});
    });
    NetInfo.isConnected.addEventListener('connectionChange', 
    this.onConnectionChange);
  }
```

1.  接下来，让我们实现在前一步中定义的事件侦听器将执行的回调。在这个方法中，我们将更新`state`的`isConnected`属性。然后，如果定义了`pendingSync`类变量，这意味着我们有一个缓存的`POST`请求，因此我们将提交该请求并相应地更新状态：

```jsx
  onConnectionChange = (isConnected) => {
    this.setState({isConnected});
    if (this.pendingSync) {
      this.setState({syncStatus : 'Syncing'});
      this.submitData(this.pendingSync).then(() => {
        this.setState({syncStatus : 'Sync Complete'});
      });
    }
  }
```

1.  接下来，我们需要实现一个函数，当有活动网络连接时，将实际进行 API 调用：

```jsx
  submitData(requestBody) {
    return fetch('http://jsonplaceholder.typicode.com/posts', {
      method : 'POST',
      body : JSON.stringify(requestBody)
    }).then((response) => {
      return response.text();
    }).then((responseText) => {
      this.setState({
        serverResponse : responseText
      });
    });
  }
```

1.  在我们可以开始处理用户界面之前，我们需要做的最后一件事是为“提交数据”按钮添加一个处理`onPress`事件的函数，我们将要渲染这个按钮。如果没有网络连接，这将立即执行调用，否则将保存在`this.pendingSync`中：

```jsx
  onSubmitPress = () => {
    const requestBody = {
      title: 'foo',
      body: 'bar',
      userId: 1
    };
    if (this.state.isConnected) {
      this.submitData(requestBody);
    } else {
      this.pendingSync = requestBody;
      this.setState({syncStatus : 'Pending'});
    }
  }
```

1.  现在，我们可以构建我们的用户界面，它将渲染“提交数据”按钮，并显示当前连接状态、同步状态以及来自 API 的最新响应：

```jsx
  render() {
    const {
      isConnected,
      syncStatus,
      serverResponse
    } = this.state;
    return (
      <View style={styles.container}>
        <TouchableOpacity onPress={this.onSubmitPress}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>Submit Data</Text>
          </View>
        </TouchableOpacity>
        <Text style={styles.status}>
          Connection Status: {isConnected ? 'Connected' : 
          'Disconnected'}
        </Text>
        <Text style={styles.status}>
          Sync Status: {syncStatus}
        </Text>
        <Text style={styles.status}>
          Server Response: {serverResponse}
        </Text>
      </View>
    );
  }
```

1.  您可以像在上一个步骤的*步骤 10*中描述的那样在模拟器中禁用网络连接：

![](img/faa23de2-9bd7-4bc8-b2ed-c963e6cd7677.png)

# 它是如何工作的...

这个步骤利用了`NetInfo`模块来控制何时进行 AJAX 请求。

在*步骤 6*中，我们定义了当单击“提交数据”按钮时执行的方法。如果没有连接，我们将请求主体保存到`pendingSync`类变量中。

在*步骤 3*中，我们定义了`componentWillMount`生命周期钩子。在这里，两个`NetInfo`方法调用检索当前的网络连接状态，并附加一个事件监听器到变化事件。

在*步骤 4*中，我们定义了当网络连接发生变化时将执行的函数，该函数适当地通知状态的`isConnected`布尔属性。如果设备已连接，我们还会检查是否有挂起的 API 调用，并在存在时完成请求。

这个教程也可以扩展为支持挂起调用的队列系统，这将允许多个 AJAX 请求延迟，直到重新建立互联网连接。

# 使用 Facebook 登录

Facebook 是全球最大的社交媒体平台，拥有超过 10 亿用户。这意味着您的用户很可能拥有 Facebook 账户。您的应用程序可以注册并链接到他们的账户，从而允许您使用他们的 Facebook 凭据作为应用程序的登录。根据请求的权限，这还将允许您访问用户信息、图片，甚至让您能够访问共享内容。您可以从 Facebook 文档中了解更多关于可用权限选项的信息，网址为[`developers.facebook.com/docs/facebook-login/permissions#reference-public_profile`](https://developers.facebook.com/docs/facebook-login/permissions#reference-public_profile)。

在这个教程中，我们将介绍通过应用程序登录 Facebook 以获取会话令牌的基本方法。然后，我们将使用该令牌访问 Facebook 的 Graph API 提供的基本`/me`端点，这将给我们用户的姓名和 ID。要与 Facebook Graph API 进行更复杂的交互，您可以查看文档，该文档可以在[`developers.facebook.com/docs/graph-api/using-graph-api`](https://developers.facebook.com/docs/graph-api/using-graph-api)找到。

为了使这个示例简单，我们将构建一个使用`Expo.Facebook.logInWithReadPermissionsAsync`方法来完成 Facebook 登录的 Expo 应用程序，这也将允许我们绕过其他必要的设置。如果您希望在不使用 Expo 的情况下与 Facebook 交互，您可能希望使用 React Native Facebook SDK，这需要更多的步骤。您可以在[`github.com/facebook/react-native-fbsdk`](https://github.com/facebook/react-native-fbsdk)找到 SDK。

# 准备工作

对于这个示例，我们将创建一个名为`facebook-login`的新应用程序。您需要有一个活跃的 Facebook 账户来测试其功能。

这个示例还需要一个 Facebook 开发者账户。如果您还没有，请前往[`developers.facebook.com`](https://developers.facebook.com)注册。一旦您登录，您可以使用仪表板创建一个新的应用程序。一旦创建完成，请记下应用程序 ID，因为我们将在示例中需要它。

# 如何做...

1.  让我们从打开`App.js`文件并添加我们的导入开始：

```jsx
import React from 'react';
import {
  StyleSheet,
  Text,
  View,
  TouchableOpacity,
  Alert
} from 'react-native';
import Expo from 'expo';
```

1.  接下来，我们将声明`App`类并添加`state`对象。`state`将跟踪用户是否使用`loggedIn`布尔值登录，并将在一个名为`facebookUserInfo`的对象中保存从 Facebook 检索到的用户数据：

```jsx
export default class App extends React.Component {
  state = {
    loggedIn: false,
    facebookUserInfo: {}
  }
  // Defined in later steps
}
```

1.  接下来，让我们定义我们的类的`logIn`方法。这将是在按下登录按钮时调用的方法。这个方法使用`Facebook`方法的`logInWithReadPermissionsAsync` Expo 辅助类来提示用户显示 Facebook 登录屏幕。在下面的代码中，用你的应用 ID 替换第一个参数，标记为`APP_ID`：

```jsx
  logIn = async () => {
    const { type, token } = await 
    Facebook.logInWithReadPermissionsAsync(APP_ID, {
      permissions: ['public_profile'],
    });

    // Defined in next step
  }
```

1.  在`logIn`方法的后半部分，如果请求成功，我们将使用从登录中收到的令牌调用 Facebook Graph API 来请求已登录用户的信息。一旦响应解析，我们就相应地设置状态：

```jsx
  logIn = async () => {
    //Defined in step above

 if (type === 'success') {
 const response = await fetch(`https://graph.facebook.com/me?
      access_token=${token}`);
 const facebookUserInfo = await response.json();
 this.setState({
 facebookUserInfo,
 loggedIn: true
 });
 }
  }
```

1.  我们还需要一个简单的`render`函数。我们将显示一个登录按钮用于登录，以及一些`Text`元素，一旦登录成功完成，将显示用户信息：

```jsx
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.headerText}>Login via Facebook</Text>
        <TouchableOpacity
          onPress={this.logIn}
          style={styles.button}
        >
          <Text style={styles.buttonText}>Login</Text>
        </TouchableOpacity>

        {this.renderFacebookUserInfo()}
      </View>
    );
  }
```

1.  如您在前面的`render`函数中所看到的，我们正在调用`this.renderFacebookUserInfo`来渲染用户信息。这个方法简单地检查用户是否通过`this.state.loggedIn`登录。如果是，我们将显示用户的信息。如果不是，我们将返回`null`来显示空白：

```jsx
  renderFacebookUserInfo = () => {
    return this.state.loggedIn ? (
      <View style={styles.facebookUserInfo}>
        <Text style={styles.facebookUserInfoLabel}>Name:</Text>
        <Text style={styles.facebookUserInfoText}>
        {this.state.facebookUserInfo.name}</Text>
        <Text style={styles.facebookUserInfoLabel}>User ID:</Text>
        <Text style={styles.facebookUserInfoText}>
        {this.state.facebookUserInfo.id}</Text>
      </View>
    ) : null;
  }
```

1.  最后，我们将添加样式以完成布局，设置填充、边距、颜色和字体大小：

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
  button: {
    marginTop: 30,
    padding: 10,
    backgroundColor: '#3B5998'
  },
  buttonText: {
    color: '#fff',
    fontSize: 30
  },
  headerText: {
    fontSize: 30
  },
  facebookUserInfo: {
    paddingTop: 30
  },
  facebookUserInfoText: {
    fontSize: 24
  },
  facebookUserInfoLabel: {
    fontSize: 20,
    marginTop: 10,
    color: '#474747'
  }
});
```

1.  现在，如果我们运行应用程序，我们将看到我们的登录按钮，当按下登录按钮时会出现登录模态，以及用户的信息，一旦用户成功登录，将显示在屏幕上：

![](img/dae0c64f-f443-4740-a2c5-ccc23680be55.png)

# 它是如何工作的...

在我们的 React Native 应用中与 Facebook 互动要比以往更容易，通过 Expo 的`Facebook`辅助库。

在*步骤 5*中，我们创建了`logIn`函数，它使用`Facebook.logInWithReadPermissionsAsync`来向 Facebook 发出登录请求。它接受两个参数：一个`appID`和一个选项对象。在我们的情况下，我们只设置了权限选项。权限选项接受一个字符串数组，用于请求每种类型的权限，但是对于我们的目的，我们只使用最基本的权限，`'public_profile'`。

在*步骤 6*中，我们完成了`logIn`函数。在成功登录后，它使用从`logInWithReadPermissionsAsync`返回的数据提供的令牌，向 Facebook 的 Graph API 端点`/me`发出调用。用户的信息和登录状态将保存到状态中，这将触发重新渲染并在屏幕上显示用户的数据。

这个示例故意只调用了一个简单的 API 端点。您可以使用此端点返回的数据来填充应用程序中的用户数据。或者，您可以使用从登录中收到的相同令牌来执行图形 API 提供的任何操作。要查看通过 API 可以获得哪些数据，您可以在[`developers.facebook.com/docs/graph-api/reference`](https://developers.facebook.com/docs/graph-api/reference)上查看参考文档。
