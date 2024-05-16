# 第十一章：为 Flux 轻松维护准备您的 React 应用程序

我们决定在 React 应用程序中实现 Flux 架构的原因是我们希望拥有更容易维护的数据流。在上一章中，我们实现了`AppDispatcher`、`TweetActionCreators`和`TweetStore`。让我们快速回想一下它们的用途：

+   TweetActionCreators：这创建并分发动作

+   `AppDispatcher`：这将所有动作分发到所有存储

+   `TweetStore`：这存储和管理应用程序数据

我们数据流中唯一缺失的部分如下：

+   使用`TweetActionCreators`创建动作并启动数据流

+   使用`TweetStore`获取数据

以下是一些重要的问题要问：我们的应用程序中数据流从哪里开始？我们的数据是什么？如果我们回答了这些问题，我们将了解从哪里开始重构我们的应用程序以适应 Flux 架构。

Snapterest 允许用户接收和收集最新的推文。我们的应用程序关心的唯一数据是推文。因此，我们的数据流始于接收新推文。目前，我们的应用程序的哪个部分负责接收新推文？您可能还记得我们的`Stream`组件具有以下`componentDidMount()`方法：

```jsx
componentDidMount() {
  SnapkiteStreamClient.initializeStream(this.handleNewTweet);
}
```

是的，目前，在渲染`Stream`组件后，我们启动了一系列新推文。等等，你可能会问，“我们不是学过 React 组件应该只关注渲染用户界面吗？”你是对的。不幸的是，目前，`Stream`组件负责两件不同的事情：

+   渲染`StreamTweet`组件

+   启动数据流

显然，这是未来潜在的维护问题。让我们借助 Flux 来解耦这两个不同的关注点。

# 使用 Flux 解耦关注点

首先，我们将创建一个名为`WebAPIUtils`的新实用程序模块。在`~/snapterest/source/utils/`目录中创建`WebAPIUtils.js`文件：

```jsx
import SnapkiteStreamClient from ‘snapkite-stream-client’;
import { receiveTweet } from ‘../actions/TweetActionCreators’;

function initializeStreamOfTweets() {
  SnapkiteStreamClient.initializeStream(receiveTweet);
}

export { initializeStreamOfTweets };
```

在这个实用程序模块中，我们首先导入`SnapkiteStreamClient`库和`TweetActionCreators`。然后，我们创建`initializeStreamOfTweets()`函数，该函数初始化一系列新推文，就像`Stream`组件的`componentDidMount()`方法一样。除了一个关键的区别：每当`SnapkiteStreamClient`接收到新推文时，它调用`TweetActionCreators.receiveTweet`方法，并将新推文作为参数传递给它：

```jsx
SnapkiteStreamClient.initializeStream(receiveTweet);
```

记住`receiveTweet`函数期望接收一个`tweet`参数：

```jsx
function receiveTweet(tweet) {
  // ... create and dispatch ‘receive_tweet’ action
}
```

这个推文将作为一个新动作对象的属性被分发。`receiveTweet()`函数创建。

然后，`WebAPIUtils`模块导出我们的`initializeStreamOfTweets()`函数。

现在我们有一个模块，其中有一个方法来启动我们的 Flux 架构中的数据流。我们应该在哪里导入并调用它？由于它与`Stream`组件解耦，实际上，它根本不依赖于任何 React 组件，我们甚至可以在 React 渲染任何内容之前使用它。让我们在我们的`app.js`文件中使用它：

```jsx
import React from ‘react’;
import ReactDOM from ‘react-dom’;
import Application from ‘./components/Application’;
import { initializeStreamOfTweets } from ‘./utils/WebAPIUtils’;

initializeStreamOfTweets();

ReactDOM.render(
  <Application/>,
  document.getElementById(‘react-application’)
);
```

正如你所看到的，我们所需要做的就是导入并调用`initializeStreamOfTweets()`方法：

```jsx
import { initializeStreamOfTweets } from ‘./utils/WebAPIUtils’;

initializeStreamOfTweets();
```

在调用 React 的`render()`方法之前我们这样做：

```jsx
ReactDOM.render(
  <Application/>,
  document.getElementById(‘react-application’)
);
```

实际上，作为一个实验，你可以完全删除`ReactDOM.render()`这行代码，并在`TweetActionCreators.receiveTweet`函数中放一个日志声明。例如，运行以下代码：

```jsx
function receiveTweet(tweet) {

  console.log("I’ve received a new tweet and now will dispatch it together with a new action.");

  const action = {
    type: ‘receive_tweet’,
    tweet
  };

  AppDispatcher.dispatch(action);
}
```

现在运行`npm start`命令。然后，在 Web 浏览器中打开`~/snapterest/build/index.html`，你会看到以下文本呈现在页面上：

**我即将学习 React.js 的基本知识。**

现在打开 JavaScript 控制台，你会看到这个输出：

```jsx
**[Snapkite Stream Client] Socket connected**
**I’ve received a new tweet and now will dispatch it together with a new action.**

```

这个日志消息将被打印出来，每当我们的应用程序接收到一个新的推文时。即使我们没有渲染任何 React 组件，我们的 Flux 架构仍然存在：

1.  我们的应用程序接收到一个新的推文。

1.  它创建并分发一个新的动作。

1.  没有任何存储器已经向分发器注册，因此没有人可以接收新的动作；因此，什么也没有发生。

现在你可以清楚地看到 React 和 Flux 是两个完全不相互依赖的东西。

然而，我们确实希望渲染我们的 React 组件。毕竟，在前面的十章中，我们已经付出了很多努力来创建它们！为了做到这一点，我们需要让我们的`TweetStore`存储器发挥作用。你能猜到我们应该在哪里使用它吗？这里有一个提示：在一个需要推文来呈现自己的 React 组件中——我们的老朋友`Stream`组件。

# 重构 Stream 组件

现在有了 Flux 架构，我们将重新思考我们的 React 组件如何获取它们需要呈现的数据。如你所知，React 组件通常有两个数据来源：

+   调用另一个库，例如调用`jQuery.ajax()`方法，或者在我们的情况下，`SnapkiteStreamClient.initializeStream()`

+   通过`props`对象从父 React 组件接收数据

我们希望我们的 React 组件不使用任何外部库来接收数据。从现在开始，它们将从商店获取相同的数据。牢记这个计划，让我们重构我们的`Stream`组件。

现在它看起来是这样的：

```jsx
import React from ‘react’;
import SnapkiteStreamClient from ‘snapkite-stream-client’;
import StreamTweet from ‘./StreamTweet’;
import Header from ‘./Header’;

class Stream extends React.Component {
  constructor() {
    super();

    this.state = {
      tweet: null
    };
  }

  componentDidMount() {
    SnapkiteStreamClient.initializeStream(this.handleNewTweet);
  }

  componentWillUnmount() {
    SnapkiteStreamClient.destroyStream();
  }

  handleNewTweet = tweet => {
    this.setState({
      tweet
    });
  }

  render() {
    const { tweet } = this.state;
    const { onAddTweetToCollection } = this.props;
    const headerText = "Waiting for public photos from Twitter...";

    if (tweet) {
      return (
        <StreamTweet
          tweet={tweet}
          onAddTweetToCollection={onAddTweetToCollection}
        />
      );
    }

    return (
      <Header text={headerText} />
    );
  }
}

export default Stream;
```

首先，让我们摆脱`componentDidMount()`、`componentWillUnmount()`和`handleNewTweet()`方法，并导入`TweetStore`商店：

```jsx
import React from ‘react’;
import SnapkiteStreamClient from ‘snapkite-stream-client’;
import StreamTweet from ‘./StreamTweet’;
import Header from ‘./Header’;
import TweetStore from ‘../stores/TweetStore’;

class Stream extends React.Component {
  state = {
    tweet: null
  }

  render() {
    const { tweet } = this.state;
    const { onAddTweetToCollection } = this.props;
    const headerText = "Waiting for public photos from Twitter...";

    if (tweet) {
      return (
        <StreamTweet
          tweet={tweet}
          onAddTweetToCollection={onAddTweetToCollection}
        />
      );
    }

    return (
      <Header text={headerText} />
    );
  }
}

export default Stream;
```

也不再需要导入`snapkite-stream-client`模块。

接下来，我们需要改变`Stream`组件如何获取其初始推文。让我们更新它的初始状态：

```jsx
state = {
  tweet: TweetStore.getTweet()
}
```

从代码上看，这可能看起来是一个小改变，但这是一个重大的架构改进。我们现在使用`getTweet()`方法从`TweetStore`商店获取数据。在上一章中，我们讨论了 Flux 中商店如何公开方法，以允许我们应用程序的其他部分从中获取数据。`getTweet()`方法是这些公共方法的一个例子，被称为*getters*。

你可以从商店获取数据，但不能直接在商店上设置数据。商店没有公共的*setter*方法。它们是有意设计成这样的限制，这样当你用 Flux 编写应用程序时，你的数据只能单向流动。当你需要维护 Flux 应用程序时，这将极大地使你受益。

现在我们知道如何获取我们的初始推文，但是我们如何获取以后到达的所有其他新推文呢？我们可以创建一个定时器并重复调用`TweetStore.getTweet()`；然而，这不是最好的解决方案，因为它假设我们不知道`TweetStore`何时更新其推文。然而，我们知道。

如何？记得在上一章中，我们在`TweetStore`对象上实现了以下公共方法，即`addChangeListener()`方法：

```jsx
addChangeListener(callback) {
  this.on(‘change’, callback);
}
```

我们还实现了`removeChangeListener()`方法：

```jsx
removeChangeListener(callback) {
  this.removeListener(‘change’, callback);
}
```

没错。我们可以要求`TweetStore`告诉我们它何时更改其数据。为此，我们需要调用它的`addChangeListener()`方法，并传递一个回调函数，`TweetStore`将为每个新推文调用它。问题是，在我们的`Stream`组件中，我们在哪里调用`TweetStore.addChangeListener()`方法？

由于我们需要在组件的生命周期中只一次向`TweetStore`添加`change`事件监听器，所以`componentDidMount()`是一个完美的选择。在`Stream`组件中添加以下`componentDidMount()`方法：

```jsx
componentDidMount() {
  TweetStore.addChangeListener(this.onTweetChange);
}
```

在这里，我们向`TweetStore`添加了我们自己的`change`事件监听器`this.onTweetChange`。现在当`TweetStore`改变其数据时，它将触发我们的`this.onTweetChange`方法。我们将很快创建这个方法。

不要忘记在卸载 React 组件之前删除任何事件侦听器。为此，将以下`componentWillUnmount()`方法添加到`Stream`组件中：

```jsx
componentWillUnmount() {
  TweetStore.removeChangeListener(this.onTweetChange);
}
```

删除事件侦听器与添加事件侦听器非常相似。我们调用`TweetStore.removeChangeListener()`方法，并将我们的`this.onTweetChange`方法作为参数传递。

现在，是时候在我们的`Stream`组件中创建`onTweetChange`方法了：

```jsx
onTweetChange = () => {
  this.setState({
    tweet: TweetStore.getTweet()
  });
}
```

正如你所看到的，它使用`TweetStore.getTweet()`方法将新的推文存储在`TweetStore`中，并更新组件的状态。

我们需要在我们的`Stream`组件中进行最后一个更改。在本章的后面，您将了解到我们的`StreamTweet`组件不再需要`handleAddTweetToCollection()`回调函数；因此，在这个组件中，我们将更改以下代码片段：

```jsx
return (
  <StreamTweet
    tweet={tweet}
    onAddTweetToCollection={onAddTweetToCollection}
  />
);
```

用以下代码替换它：

```jsx
return (<StreamTweet tweet={tweet} />);
```

现在让我们来看看我们新重构的`Stream`组件：

```jsx
import React from ‘react’;
import StreamTweet from ‘./StreamTweet’;
import Header from ‘./Header’;
import TweetStore from ‘../stores/TweetStore’;

class Stream extends React.Component {
  state = {
    tweet: TweetStore.getTweet()
  }

  componentDidMount() {
    TweetStore.addChangeListener(this.onTweetChange);
  }

  componentWillUnmount() {
    TweetStore.removeChangeListener(this.onTweetChange);
  }

  onTweetChange = () => {
    this.setState({
      tweet: TweetStore.getTweet()
    });
  }

  render() {
    const { tweet } = this.state;
    const { onAddTweetToCollection } = this.props;
    const headerText = "Waiting for public photos from Twitter...";

    if (tweet) {
      return (<StreamTweet tweet={tweet}/>);
    }

    return (<Header text={headerText}/>);
  }
}

export default Stream;
```

让我们回顾一下，看看我们的`Stream`组件如何始终具有最新的推文：

1.  我们使用`getTweet()`方法将组件的初始推文设置为从`TweetStore`获取的最新推文。

1.  然后，我们监听`TweetStore`的变化。

1.  当`TweetStore`改变其推文时，我们使用`getTweet()`方法从`TweetStore`获取最新的推文，并更新组件的状态。

1.  当组件即将卸载时，我们停止监听`TweetStore`的变化。

这就是 React 组件与 Flux 存储区交互的方式。

在我们继续使我们的应用程序其余部分变得更加 Flux 强大之前，让我们来看看我们当前的数据流：

+   `app.js`：这接收新推文并为每个推文调用`TweetActionCreators`

+   `TweetActionCreators`：这将创建并分发一个带有新推文的新操作

+   `AppDispatcher`：这将所有操作分发到所有存储区

+   `TweetStore`：这将向调度程序注册，并在从调度程序接收到新操作时发出更改事件

+   `Stream`：这监听`TweetStore`的变化，从`TweetStore`获取新的推文，更新状态并重新渲染

你能看到我们如何现在可以扩展 React 组件、动作创建者和存储的数量，仍然能够维护 Snapterest 吗？使用 Flux，它将始终是单向数据流。无论我们实现多少新功能，它都将是相同的思维模式。在长期来看，当我们需要维护我们的应用程序时，我们将获得巨大的好处。

我是否提到我们将在我们的应用程序中更多地使用 Flux？接下来，让我们确实这样做。

# 创建 CollectionStore

Snapterest 不仅存储最新的推文，还存储用户创建的推文集合。让我们用 Flux 重构这个功能。

首先，让我们创建一个集合存储。导航到`~/snapterest/source/stores/`目录并创建`CollectionStore.js`文件：

```jsx
import AppDispatcher from ‘../dispatcher/AppDispatcher’;
import { EventEmitter } from ‘events’;

const CHANGE_EVENT = ‘change’;

let collectionTweets = {};
let collectionName = ‘new’;

function addTweetToCollection(tweet) {
  collectionTweets[tweet.id] = tweet;
}

function removeTweetFromCollection(tweetId) {
  delete collectionTweets[tweetId];
}

function removeAllTweetsFromCollection() {
  collectionTweets = {};
}

function setCollectionName(name) {
  collectionName = name;
}

function emitChange() {
  CollectionStore.emit(CHANGE_EVENT);
}

const CollectionStore = Object.assign(
  {}, EventEmitter.prototype, {
  addChangeListener(callback) {
    this.on(CHANGE_EVENT, callback);
  },

  removeChangeListener(callback) {
    this.removeListener(CHANGE_EVENT, callback);
  },

  getCollectionTweets() {
    return collectionTweets;
  },

  getCollectionName() {
    return collectionName;
  }
}
);

function handleAction(action) {

  switch (action.type) {
    case ‘add_tweet_to_collection’:
      addTweetToCollection(action.tweet);
      emitChange();
      break;

    case ‘remove_tweet_from_collection’:
      removeTweetFromCollection(action.tweetId);
      emitChange();
      break;

    case ‘remove_all_tweets_from_collection’:
      removeAllTweetsFromCollection();
      emitChange();
      break;

    case ‘set_collection_name’:
      setCollectionName(action.collectionName);
      emitChange();
      break;

    default: // ... do nothing

  }
}

CollectionStore.dispatchToken = AppDispatcher.register(handleAction);

export default CollectionStore;
```

CollectionStore 是一个更大的存储，但它具有与 TweetStore 相同的结构。

首先，我们导入依赖项并将`CHANGE_EVENT`变量分配给`change`事件名称：

```jsx
import AppDispatcher from ‘../dispatcher/AppDispatcher’;
import { EventEmitter } from ‘events’;

const CHANGE_EVENT = ‘change’;
```

然后，我们定义我们的数据和四个私有方法来改变这些数据：

```jsx
let collectionTweets = {};
let collectionName = ‘new’;

function addTweetToCollection(tweet) {
  collectionTweets[tweet.id] = tweet;
}

function removeTweetFromCollection(tweetId) {
  delete collectionTweets[tweetId];
}

function removeAllTweetsFromCollection() {
  collectionTweets = {};
}

function setCollectionName(name) {
  collectionName = name;
}
```

正如你所看到的，我们在一个最初为空的对象中存储了一系列推文，并且我们还存储了最初设置为`new`的集合名称。然后，我们创建了三个私有函数来改变`collectionTweets`：

+   将`tweet`对象添加到`collectionTweets`对象

+   从`collectionTweets`对象中删除`tweet`对象

+   从`collectionTweets`中删除所有`tweet`对象，将其设置为空对象

然后，我们定义一个私有函数来改变`collectionName`，名为`setCollectionName`，它将现有的集合名称更改为新的名称。

这些函数被视为私有，因为它们在 CollectionStore 模块之外是不可访问的；例如，你*不能*像在任何其他模块中那样访问它们：

```jsx
CollectionStore.setCollectionName(‘impossible’);
```

正如我们之前讨论的，这是有意为之的，以强制在应用程序中实现单向数据流。

我们创建了`emitChange()`方法来发出`change`事件。

然后，我们创建 CollectionStore 对象：

```jsx
const CollectionStore = Object.assign(
  {}, EventEmitter.prototype, {
  addChangeListener(callback) {
    this.on(CHANGE_EVENT, callback);
  },

  removeChangeListener(callback) {
    this.removeListener(CHANGE_EVENT, callback);
  },

  getCollectionTweets() {
    return collectionTweets;
  },

  getCollectionName() {
    return collectionName;
  }
});
```

这与 TweetStore 对象非常相似，只有两种方法不同：

+   获取推文集合

+   获取集合名称

这些方法可以在 CollectionStore.js 文件之外访问，并且应该在 React 组件中用于从 CollectionStore 获取数据。

然后，我们创建 handleAction（）函数：

```jsx
function handleAction(action) {
  switch (action.type) {

    case ‘add_tweet_to_collection’:
      addTweetToCollection(action.tweet);
      emitChange();
      break;

    case ‘remove_tweet_from_collection’:
      removeTweetFromCollection(action.tweetId);
      emitChange();
      break;

    case ‘remove_all_tweets_from_collection’:
      removeAllTweetsFromCollection();
      emitChange();
      break;

    case ‘set_collection_name’:
      setCollectionName(action.collectionName);
      emitChange();
      break;

    default: // ... do nothing

  }
}
```

该函数处理由 AppDispatcher 分发的操作，但与我们 CollectionStore 模块中的 TweetStore 不同，我们可以处理多个操作。实际上，我们可以处理与 Tweet 集合相关的四个操作：

+   add_tweet_to_collection：这将向集合中添加一条 Tweet

+   remove_tweet_from_collection：这将从集合中删除一条 Tweet

+   remove_all_tweets_from_collection：这将从集合中删除所有 Tweet

+   set_collection_name：这将设置集合名称

请记住，所有存储都会接收所有操作，因此 CollectionStore 也将接收 receive_tweet 操作，但是在这个存储中我们只是简单地忽略它，就像 TweetStore 忽略 add_tweet_to_collection，remove_tweet_from_collection，remove_all_tweets_from_collection 和 set_collection_name 一样。

然后，我们使用 AppDispatcher 注册 handleAction 回调，并将 dispatchToken 保存在 CollectionStore 对象中：

```jsx
CollectionStore.dispatchToken = AppDispatcher.register(handleAction);
```

最后，我们将 CollectionStore 作为一个模块导出：

```jsx
export default CollectionStore;
```

现在，由于我们已经准备好了集合存储，让我们创建动作创建函数。

# 创建 CollectionActionCreators

导航到~/snapterest/source/actions/并创建 CollectionActionCreators.js 文件：

```jsx
import AppDispatcher from ‘../dispatcher/AppDispatcher’;

function addTweetToCollection(tweet) {
  const action = {
    type: ‘add_tweet_to_collection’,
    tweet
  };

  AppDispatcher.dispatch(action);
}

function removeTweetFromCollection(tweetId) {
  const action = {
    type: ‘remove_tweet_from_collection’,
    tweetId
  };

  AppDispatcher.dispatch(action);
}

function removeAllTweetsFromCollection() {
  const action = {
    type: ‘remove_all_tweets_from_collection’
  };

  AppDispatcher.dispatch(action);
}

function setCollectionName(collectionName) {
  const action = {
    type: ‘set_collection_name’,
    collectionName
  };

  AppDispatcher.dispatch(action);
}

export default {
  addTweetToCollection,
  removeTweetFromCollection,
  removeAllTweetsFromCollection,
  setCollectionName
};
```

对于我们在 CollectionStore 中处理的每个操作，我们都有一个操作创建函数：

+   将 Tweet 添加到 Collection 中（）：这将创建并分发带有新 Tweet 的 add_tweet_to_collection 动作

+   removeTweetFromCollection（）：这将创建并分发带有必须从集合中删除的 Tweet 的 ID 的 remove_tweet_from_collection 动作

+   removeAllTweetsFromCollection（）：这将创建并分发 remove_all_tweets_from_collection 动作

+   setCollectionName（）：这将创建并分发带有新集合名称的 set_collection_name 动作

现在，当我们创建了 CollectionStore 和 CollectionActionCreators 模块时，我们可以开始重构我们的 React 组件以采用 Flux 架构。

# 重构 Application 组件

我们从哪里开始重构我们的 React 组件？让我们从组件层次结构中的顶层 React 组件 Application 开始。

目前，我们的 Application 组件存储和管理 Tweet 的集合。让我们删除这个功能，因为现在它由集合存储管理。

从`Application`组件中删除`constructor()`、`addTweetToCollection()`、`removeTweetFromCollection()`和`removeAllTweetsFromCollection()`方法：

```jsx
import React from ‘react’;
import Stream from ‘./Stream’;
import Collection from ‘./Collection’;

class Application extends React.Component {
  render() {
    const {
      collectionTweets
    } = this.state;

    return (
      <div className="container-fluid">
        <div className="row">
          <div className="col-md-4 text-center">
            <Stream onAddTweetToCollection={this.addTweetToCollection}/>

          </div>
          <div className="col-md-8">
            <Collection
              tweets={collectionTweets}
              onRemoveTweetFromCollection={this.removeTweetFromCollection}
              onRemoveAllTweetsFromCollection={this.removeAllTweetsFromCollection}
            />
          </div>
        </div>
      </div>
    );
  }
}

export default Application;
```

现在`Application`组件只有`render()`方法来渲染`Stream`和`Collection`组件。由于它不再管理推文集合，我们也不需要向`Stream`和`Collection`组件传递任何属性。

更新`Application`组件的`render()`函数如下：

```jsx
render() {
  return (
    <div className="container-fluid">
      <div className="row">
        <div className="col-md-4 text-center">
          <Stream/>
        </div>
        <div className="col-md-8">
          <Collection/>
        </div>
      </div>

    </div>
  );
}
```

Flux 架构的采用允许`Stream`组件管理最新的推文，`Collection`组件管理推文集合，而`Application`组件不再需要管理任何东西，因此它成为一个容器组件，用额外的 HTML 标记包装`Stream`和`Collection`组件。

实际上，您可能已经注意到我们当前版本的`Application`组件是成为一个功能性 React 组件的一个很好的候选：

```jsx
import React from ‘react’;
import Stream from ‘./Stream’;
import Collection from ‘./Collection’;

const Application = () =>(
  <div className="container-fluid">
    <div className="row">
      <div className="col-md-4 text-center">
        <Stream />
      </div>
      <div className="col-md-8">
        <Collection />
      </div>
    </div>
  </div>
);

export default Application;
```

我们的`Application`组件现在更简单，其标记看起来更清洁。这提高了组件的可维护性。干得好！

# 重构集合组件

接下来，让我们重构我们的`Collection`组件。用以下内容替换现有的`Collection`组件：

```jsx
import React, { Component } from ‘react’;
import ReactDOMServer from ‘react-dom/server’;
import CollectionControls from ‘./CollectionControls’;
import TweetList from ‘./TweetList’;
import Header from ‘./Header’;
import CollectionUtils from ‘../utils/CollectionUtils’;
import CollectionStore from ‘../stores/CollectionStore’;

class Collection extends Component {
  state = {
    collectionTweets: CollectionStore.getCollectionTweets()
  }

  componentDidMount() {
    CollectionStore.addChangeListener(this.onCollectionChange);
  }

  componentWillUnmount() {
    CollectionStore.removeChangeListener(this.onCollectionChange);
  }

  onCollectionChange = () => {
    this.setState({
      collectionTweets: CollectionStore.getCollectionTweets()
    });
  }

  createHtmlMarkupStringOfTweetList() {
    const htmlString = ReactDOMServer.renderToStaticMarkup(
      <TweetList tweets={this.state.collectionTweets}/>
    );

    const htmlMarkup = {
      html: htmlString
    };

    return JSON.stringify(htmlMarkup);
  }

  render() {
    const { collectionTweets } = this.state;
    const numberOfTweetsInCollection = CollectionUtils
      .getNumberOfTweetsInCollection(collectionTweets);
    let htmlMarkup;

    if (numberOfTweetsInCollection > 0) {
      htmlMarkup = this.createHtmlMarkupStringOfTweetList();

      return (
        <div>
          <CollectionControls
            numberOfTweetsInCollection={numberOfTweetsInCollection}
            htmlMarkup={htmlMarkup}
          />

          <TweetList tweets={collectionTweets} />
        </div>
      );
    }

    return (<Header text="Your collection is empty" />);
  }
}

export default Collection;
```

我们在这里改变了什么？有几件事。首先，我们导入了两个新模块：

```jsx
import CollectionUtils from ‘../utils/CollectionUtils’;
import CollectionStore from ‘../stores/CollectionStore’;
```

我们在第九章中创建了`CollectionUtils`模块，*使用 Jest 测试您的 React 应用程序*，在本章中，我们正在使用它。`CollectionStore`是我们获取数据的地方。

接下来，您应该能够发现这四种方法的熟悉模式：

+   在初始状态下，我们将推文集合设置为`CollectionStore`中存储的内容。您可能还记得`CollectionStore`提供了`getCollectionTweets()`方法来获取其中的数据。

+   在`componentDidMount()`方法中，我们向`CollectionStore`添加`change`事件监听器`this.onCollectionChange`。每当推文集合更新时，`CollectionStore`将调用我们的`this.onCollectionChange`回调函数来通知`Collection`组件该变化。

+   在`componentWillUnmount()`方法中，我们移除了在`componentDidMount()`方法中添加的`change`事件监听器。

+   在`onCollectionChange()`方法中，我们将组件的状态设置为当前存储在`CollectionStore`中的内容。更新组件的状态会触发重新渲染。

`Collection`组件的`render()`方法现在更简单、更清晰：

```jsx
render() {
  const { collectionTweets } = this.state;
  const numberOfTweetsInCollection = CollectionUtils
    .getNumberOfTweetsInCollection(collectionTweets);
  let htmlMarkup;

  if (numberOfTweetsInCollection > 0) {
    htmlMarkup = this.createHtmlMarkupStringOfTweetList();

    return (
      <div>
        <CollectionControls
          numberOfTweetsInCollection={numberOfTweetsInCollection}
          htmlMarkup={htmlMarkup}
        />

        <TweetList tweets={collectionTweets}/>
      </div>
    );
  }

  return (<Header text="Your collection is empty"/>);
}
```

我们使用`CollectionUtils`模块来获取集合中的推文数量，并向子组件`CollectionControls`和`TweetList`传递更少的属性。

# 重构`CollectionControls`组件

`CollectionControls`组件也有一些重大改进。让我们先看一下重构后的版本，然后讨论更新了什么以及为什么更新：

```jsx
import React, { Component } from ‘react’;
import Header from ‘./Header’;
import Button from ‘./Button’;
import CollectionRenameForm from ‘./CollectionRenameForm’;
import CollectionExportForm from ‘./CollectionExportForm’;
import CollectionActionCreators from ‘../actions/CollectionActionCreators’;
import CollectionStore from ‘../stores/CollectionStore’;

class CollectionControls extends Component {
  state = {
    isEditingName: false
  }

  getHeaderText = () => {
    const { numberOfTweetsInCollection } = this.props;
    let text = numberOfTweetsInCollection;
    const name = CollectionStore.getCollectionName();

    if (numberOfTweetsInCollection === 1) {
      text = `${text} tweet in your`;
    } else {
      text = `${text} tweets in your`;
    }

    return (
      <span>
        {text} <strong>{name}</strong> collection
      </span>
    );
  }

  toggleEditCollectionName = () => {
    this.setState(prevState => ({
      isEditingName: !prevState.isEditingName
    }));
  }

  removeAllTweetsFromCollection = () => {
    CollectionActionCreators.removeAllTweetsFromCollection();
  }

  render() {
    const { name, isEditingName } = this.state;
    const onRemoveAllTweetsFromCollection = this.removeAllTweetsFromCollection;
    const { htmlMarkup } = this.props;

    if (isEditingName) {
      return (
        <CollectionRenameForm
          name={name}
          onCancelCollectionNameChange={this.toggleEditCollectionName}
        />
      );
    }

    return (
      <div>
        <Header text={this.getHeaderText()} />

        <Button
          label="Rename collection"
          handleClick={this.toggleEditCollectionName}
        />

        <Button
          label="Empty collection"
          handleClick={onRemoveAllTweetsFromCollection}
        />

        <CollectionExportForm htmlMarkup={htmlMarkup} />
      </div>
    );
  }
}

export default CollectionControls;
```

首先，我们导入另外两个模块：

```jsx
import CollectionActionCreators from ‘../actions/CollectionActionCreators’;
import CollectionStore from ‘../stores/CollectionStore’;
```

注意，我们不再在这个组件中管理集合名称。相反，我们从`CollectionStore`模块中获取它：

```jsx
const name = CollectionStore.getCollectionName();
```

然后，我们进行了一个关键的改变。我们用一个新的`removeAllTweetsFromCollection()`方法替换了`setCollectionName()`方法：

```jsx
removeAllTweetsFromCollection = () => {
  CollectionActionCreators.removeAllTweetsFromCollection();
}
```

当用户点击“清空集合”按钮时，将调用`removeAllTweetsFromCollection()`方法。这个用户操作会触发`removeAllTweetsFromCollection()`动作创建函数，它创建并分发动作到存储中。然后，`CollectionStore`会从集合中删除所有推文并发出`change`事件。

接下来，让我们重构我们的`CollectionRenameForm`组件。

# 重构`CollectionRenameForm`组件

`CollectionRenameForm`是一个受控表单组件。这意味着它的输入值存储在组件的状态中，更新该值的唯一方法是更新组件的状态。它具有应该从`CollectionStore`获取的初始值，所以让我们实现这一点。

首先，导入`CollectionActionCreators`和`CollectionStore`模块：

```jsx
import CollectionActionCreators from ‘../actions/CollectionActionCreators’;
import CollectionStore from ‘../stores/CollectionStore’;
```

现在，我们需要删除它现有的`constructor()`方法：

```jsx
constructor(props) {
  super(props);

  const { name } = props;

  this.state = {
    inputValue: name
  };
}
```

用以下代码替换前面的代码：

```jsx
state = {
  inputValue: CollectionStore.getCollectionName()
}
```

正如你所看到的，唯一的区别是现在我们从`CollectionStore`获取初始的`inputValue`。

接下来，让我们更新`handleFormSubmit()`方法：

```jsx
handleFormSubmit = event => {
  event.preventDefault();

  const { onChangeCollectionName } = this.props;
  const { inputValue: collectionName } = this.state;

  onChangeCollectionName(collectionName);
}
```

用以下代码更新前面的代码：

```jsx
handleFormSubmit = event => {
  event.preventDefault();

  const { onCancelCollectionNameChange } = this.props;
  const { inputValue: collectionName } = this.state;

  CollectionActionCreators.setCollectionName(collectionName);

  onCancelCollectionNameChange();
}
```

这里的重要区别在于，当用户提交表单时，我们将创建一个新的操作，在我们的集合存储中设置一个新的名称：

```jsx
CollectionActionCreators.setCollectionName(collectionName);
```

最后，我们需要在`handleFormCancel()`方法中更改集合名称的来源：

```jsx
handleFormCancel = event => {
  event.preventDefault();

  const {
    name: collectionName,
    onCancelCollectionNameChange
  } = this.props;

  this.setInputValue(collectionName);
  onCancelCollectionNameChange();
}
```

用以下代码替换前面的代码：

```jsx
handleFormCancel = event => {
  event.preventDefault();

  const {
    onCancelCollectionNameChange
  } = this.props;

  const collectionName = CollectionStore.getCollectionName();

  this.setInputValue(collectionName);
  onCancelCollectionNameChange();
}
```

再次，我们从集合存储中获取集合名称：

```jsx
const collectionName = CollectionStore.getCollectionName();
```

这就是我们需要在`CollectionRenameForm`组件中更改的全部内容。让我们接下来重构`TweetList`组件。

# 重构`TweetList`组件

`TweetList`组件渲染了一系列推文。每个推文都是一个`Tweet`组件，用户可以点击以将其从集合中移除。听起来好像它可以利用`CollectionActionCreators`吗？

没错。让我们将`CollectionActionCreators`模块添加到其中：

```jsx
import CollectionActionCreators from ‘../actions/CollectionActionCreators’;
```

然后，我们将创建`removeTweetFromCollection()`回调函数，当用户点击推文图片时将被调用：

```jsx
removeTweetFromCollection = tweet => {
  CollectionActionCreators.removeTweetFromCollection(tweet.id);
}
```

正如您所看到的，它使用`removeTweetFromCollection()`函数创建了一个新的动作，并将推文 ID 作为参数传递给它。

最后，我们需要确保实际调用了`removeTweetFromCollection()`。在`getTweetElement()`方法中，找到以下行：

```jsx
const { tweets, onRemoveTweetFromCollection } = this.props;
```

现在用以下代码替换它：

```jsx
const { tweets } = this.props;
const onRemoveTweetFromCollection = this.removeTweetFromCollection;
```

我们已经完成了这个组件。接下来是我们重构之旅中的`StreamTweet`。

# 重构`StreamTweet`组件

`StreamTweet`渲染了用户可以点击以将其添加到推文集合中的推文图片。您可能已经猜到，当用户点击该推文图片时，我们将创建并分发一个新的动作。

首先，将`CollectionActionCreators`模块导入`StreamTweet`组件：

```jsx
import CollectionActionCreators from ‘../actions/CollectionActionCreators’;
```

然后，在其中添加一个新的`addTweetToCollection()`方法：

```jsx
addTweetToCollection = tweet => {
  CollectionActionCreators.addTweetToCollection(tweet);
}
```

当用户点击推文图片时，应调用`addTweetToCollection()`回调函数。让我们看看`render()`方法中的这行代码：

```jsx
<Tweet
  tweet={tweet}
  onImageClick={onAddTweetToCollection}
/>
```

用以下行代码替换前面的代码：

```jsx
<Tweet
  tweet={tweet}
  onImageClick={this.addTweetToCollection}
/>
```

最后，我们需要替换以下行：

```jsx
const { tweet, onAddTweetToCollection } = this.props; 
```

使用这个代替：

```jsx
const { tweet } = this.props;
```

`StreamTweet`组件现在已经完成。

# 构建和超越

这就是将 Flux 架构集成到我们的 React 应用程序中所需的所有工作。如果您比较一下没有 Flux 的 React 应用程序和有 Flux 的 React 应用程序，您很快就会发现当 Flux 成为其中的一部分时，更容易理解应用程序的工作原理。您可以在[`facebook.github.io/flux/`](https://facebook.github.io/flux/)了解更多关于 Flux 的信息。

我认为现在是检查一切是否正常运行的好时机。让我们构建并运行 Snapterest！

导航到`~/snapterest`并在您的终端窗口中运行以下命令：

```jsx
**npm start**

```

确保您正在运行我们在第二章中安装和配置的 Snapkite Engine 应用程序，*为您的项目安装强大的工具*。现在在您的网络浏览器中打开`~/snapterest/build/index.html`文件。您应该会看到新的推文逐个出现在左侧。单击推文将其添加到右侧出现的收藏中。

它是否有效？检查 JavaScript 控制台是否有任何错误。没有错误？

祝贺您将 Flux 架构整合到我们的 React 应用程序中！

# 总结

在这一章中，我们完成了重构我们的应用程序，以使用 Flux 架构。您了解了将 React 与 Flux 结合使用的要求，以及 Flux 所提供的优势。

在下一章中，我们将使用 Redux 库进一步简化我们应用程序的架构。
