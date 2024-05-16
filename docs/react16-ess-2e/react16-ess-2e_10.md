# 第十章：使用 Flux 加强您的 React 架构

构建 Web 应用程序的过程具有一种与生命本身的演变过程有些相似的特质——它永远不会结束。与建造桥梁不同，构建 Web 应用程序没有代表开发过程结束的自然状态。由您或您的团队决定何时停止开发过程并发布您已经构建的内容。

在这本书中，我们已经达到了可以停止开发 Snapterest 的点。现在，我们有一个基本功能的小型 React.js 应用程序，它只是简单地运行。

这样就够了吗？

并不完全是这样。在本书的早些部分，我们讨论了维护 Web 应用程序的过程在时间和精力方面要比开发过程昂贵得多。如果我们选择在其当前状态下完成 Snapterest 的开发，我们也将选择开始维护它的过程。

我们准备好维护 Snapterest 了吗？我们知道它的当前状态是否允许我们在以后引入新功能而无需进行重大代码重构吗？

# 分析您的 Web 应用程序架构

为了回答这些问题，让我们从实现细节中放大，并探索我们应用程序的架构：

+   `app.js`文件呈现我们的`Application`组件

+   `Application`组件管理 tweet 集合并呈现我们的`Stream`和`Collection`组件

+   `Stream`组件从`SnapkiteStreamClient`库接收新的 tweets 并呈现`StreamTweet`和`Header`组件

+   `Collection`组件呈现`CollectionControls`和`TweetList`组件

停在那里。您能告诉数据在我们的应用程序内部是如何流动的吗？您知道它是如何进入我们的应用程序的吗？新的 tweet 是如何最终进入我们的集合的？让我们更仔细地检查我们的数据流：

1.  我们使用`SnapkiteStreamClient`库在`Stream`组件内接收新 tweet。

1.  然后，这个新的 tweet 从`Stream`传递到`StreamTweet`组件。

1.  `StreamTweet`组件将其传递给`Tweet`组件，后者呈现 tweet 图像。

1.  用户点击该 tweet 图像将其添加到其集合中。

1.  `Tweet`组件通过`handleImageClick(tweet)`回调函数将`tweet`对象传递给`StreamTweet`组件。

1.  `StreamTweet`组件通过`onAddTweetToCollection(tweet)`回调函数将`tweet`对象传递给`Stream`组件。

1.  `Stream`组件通过`onAddTweetToCollection(tweet)`回调函数将`tweet`对象传递给`Application`组件。

1.  `Application`组件将`tweet`添加到`collectionTweets`对象并更新其状态。

1.  状态更新触发`Application`组件重新渲染，进而使用更新后的推文集合重新渲染`Collection`组件。

1.  然后，`Collection`组件的子组件也可以改变我们的推文集合。

你感到困惑吗？你能长期依赖这种架构吗？你认为它容易维护吗？我不这么认为。

让我们识别当前架构的关键问题。我们可以看到新数据通过`Stream`组件进入我们的 React 应用程序。然后，它沿着组件层次结构一直传递到`Tweet`组件。然后，它一直传递到`Application`组件，那里存储和管理它。

为什么我们要在`Application`组件中存储和管理我们的推文集合？因为`Application`是另外两个组件`Stream`和`Collection`的父组件：它们都需要能够改变我们的推文集合。为了适应这一点，我们的`Application`组件需要将回调函数传递给这两个组件：

+   `Stream`组件：

```jsx
<Stream 
  onAddTweetToCollection={this.addTweetToCollection}
/>
```

+   `Collection`组件：

```jsx
<Collection
  tweets={collectionTweets}
  onRemoveTweetFromCollection={this.removeTweetFromCollection} onRemoveAllTweetsFromCollection={this.removeAllTweetsFromCollection}
/>
```

`Stream`组件获取`onAddTweetToCollection()`函数以将推文添加到集合中。`Collection`组件获取`onRemoveTweetFromCollection()`函数以从集合中移除推文，并获取`onRemoveAllTweetsFromCollection()`函数以移除集合中的所有推文。

然后，这些回调函数会一直传播到组件层次结构的底部，直到它们到达实际调用它们的某个组件。在我们的应用程序中，`onAddTweetToCollection()`函数只在`Tweet`组件中被调用。让我们看看在它被调用之前需要从一个组件传递到另一个组件多少次：

```jsx
Application > Stream > StreamTweet > Tweet
```

`onAddTweetToCollection()`函数在`Stream`和`StreamTweet`组件中都没有被使用，但它们都将其作为属性传递下去，目的是将其传递给它们的子组件。

Snapterest 是一个小型的 React 应用程序，所以这个问题只是一个不便，但以后，如果你决定添加新功能，这个不便很快就会变成一个维护的噩梦：

```jsx
Application > ComponentA > ComponentB > ComponentC > ComponentD > ComponentE > ComponentF > ComponentG > Tweet
```

为了防止这种情况发生，我们将解决两个问题：

+   我们将改变新数据进入我们的应用程序的方式

+   我们将改变组件如何获取和设置数据

我们将借助 Flux 重新思考应用程序内部数据流。

# 理解 Flux

**Flux**是 Facebook 提供的应用程序架构，它与 React 相辅相成。它不是一个框架或库，而是一个解决常见问题的解决方案——如何构建可扩展的客户端应用程序。

使用 Flux 架构，我们可以重新思考数据在我们的应用程序内部的流动方式。Flux 确保我们的所有数据只在一个**单一方向**中流动。这有助于我们理解我们的应用程序如何工作，无论它有多小或多大。使用 Flux，我们可以添加新功能，而不会使应用程序的复杂性或其心智模型爆炸。

您可能已经注意到，React 和 Flux 都共享相同的核心概念——单向数据流。这就是为什么它们自然而然地很好地配合在一起。我们知道数据在 React 组件内部如何流动，但 Flux 如何实现单向数据流呢？

使用 Flux，我们将应用程序的关注点分为四个逻辑实体：

+   操作

+   分发器

+   存储器

+   视图

**操作**是我们在想要改变应用程序状态时创建的对象。例如，当我们的应用程序接收到新推文时，我们创建一个新操作。操作对象有一个“类型”属性，用于标识它是什么操作，以及我们的应用程序需要过渡到新状态的任何其他属性。以下是一个操作对象的示例：

```jsx
const action = {
  type: 'receive_tweet',
  tweet
};
```

如您所见，这是一个`receive_tweet`类型的操作，它有一个`tweet`属性，这是我们的应用程序接收到的新推文对象。通过查看操作的类型，您可以猜测这个操作代表了应用程序状态的什么变化。对于我们的应用程序接收到的每条新推文，它都会创建一个`receive_tweet`操作。

这个操作去哪里？我们的应用程序的哪个部分会接收到这个操作？操作被分发到存储器。

存储器负责管理应用程序的数据。它们提供了访问数据的方法，但不提供更改数据的方法。如果要更改存储器中的数据，必须创建并分发一个操作。

我们知道如何创建一个操作，但如何分发它呢？顾名思义，您可以使用分发器来做这件事。

分发器负责将所有操作分发到所有存储器：

+   所有存储器都向分发器注册。它们提供一个回调函数。

+   所有操作都由调度程序分派到所有已向调度程序注册的存储。

这就是 Flux 架构中数据流的样子：

```jsx
Actions > Dispatcher > Stores
```

您可以看到调度程序在我们的数据流中扮演着一个中心元素的角色。所有操作都由它分派。存储与它注册。所有操作都是同步分派的。您不能在上一个操作分派的中间分派操作。在 Flux 架构中，没有操作可以跳过调度程序。

# 创建调度程序

现在让我们实现这个数据流。我们将首先创建一个调度程序。Facebook 提供了一个我们可以重用的调度程序的实现。让我们利用一下：

1.  导航到 `~/snapterest` 目录并运行以下命令：

```jsx
**npm install --save flux**

```

`flux` 模块带有一个我们将重用的 `Dispatcher` 函数。

1.  接下来，在我们项目的 `~/snapterest/source/dispatcher` 目录中创建一个名为 `dispatcher` 的新文件夹。然后在其中创建 `AppDispatcher.js` 文件：

```jsx
import { Dispatcher } from 'flux';
export default new Dispatcher();
```

首先，我们导入 Facebook 提供的 `Dispatcher`，然后创建并导出一个新的实例。现在我们可以在我们的应用程序中使用这个实例。

接下来，我们需要一种方便的方式来创建和分派操作。对于每个操作，让我们创建一个函数来创建和分派该操作。在 Flux 架构中，这些函数被称为操作创建者函数。

# 创建操作创建者

在我们项目的 `~/snapterest/source/actions` 目录中创建一个名为 `actions` 的新文件夹。然后，在其中创建 `TweetActionCreators.js` 文件：

```jsx
import AppDispatcher from '../dispatcher/AppDispatcher';

function receiveTweet(tweet) {
  const action = {
    type: 'receive_tweet',
    tweet
  };

  AppDispatcher.dispatch(action);
}

export { receiveTweet };
```

我们的操作创建者将需要一个调度程序来分派操作。我们将导入之前创建的 `AppDispatcher`：

```jsx
import AppDispatcher from '../dispatcher/AppDispatcher';
```

然后，我们将创建我们的第一个操作创建者 `receiveTweet()`：

```jsx
function receiveTweet(tweet) {
  const action = {
    type: 'receive_tweet',
    tweet
  };

  AppDispatcher.dispatch(action);
}
```

`receiveTweet()` 函数以 `tweet` 对象作为参数，并创建具有 `type` 属性设置为 `receive_tweet` 的 `action` 对象。它还将 `tweet` 对象添加到我们的 `action` 对象中，现在每个存储都将接收到这个 `tweet` 对象。

最后，`receiveTweet()` 操作创建者通过在 `AppDispatcher` 对象上调用 `dispatch()` 方法来分派我们的 `action` 对象：

```jsx
AppDispatcher.dispatch(action);
```

`dispatch()` 方法将 `action` 对象分派到所有已向 `AppDispatcher` 调度程序注册的存储。

然后我们导出我们的 `receiveTweet` 方法：

```jsx
export { receiveTweet };
```

到目前为止，我们已经创建了 `AppDispatcher` 和 `TweetActionCreators`。接下来，让我们创建我们的第一个存储。

# 创建存储

正如您之前学到的，存储在您的 Flux 架构中管理数据。它们将这些数据提供给 React 组件。我们将创建一个简单的存储，用于管理我们的应用程序从 Twitter 接收到的新推文。

在项目的 `~/snapterest/source/stores` 目录中创建一个名为 `stores` 的新文件夹。然后，在其中创建 `TweetStore.js` 文件：

```jsx
import AppDispatcher from '../dispatcher/AppDispatcher';
import EventEmitter from 'events';

let tweet = null;

function setTweet(receivedTweet) {
  tweet = receivedTweet;
}

function emitChange() {
  TweetStore.emit('change');
}

const TweetStore = Object.assign({}, EventEmitter.prototype, {
  addChangeListener(callback) {
    this.on('change', callback);
  },

  removeChangeListener(callback) {
    this.removeListener('change', callback);
  },

  getTweet() {
    return tweet;
  }
});

function handleAction(action) {
  if (action.type === 'receive_tweet') {
    setTweet(action.tweet);
    emitChange();
  }
}

TweetStore.dispatchToken = AppDispatcher.register(handleAction);

export default TweetStore;
```

`TweetStore.js` 文件实现了一个简单的存储。我们可以将其分为四个逻辑部分：

+   导入依赖模块并创建私有数据和方法

+   创建具有公共方法的 `TweetStore` 对象

+   创建一个操作处理程序并向调度程序注册存储

+   将 `dispatchToken` 分配给我们的 `TweetStore` 对象并导出它。

在我们存储的第一个逻辑部分中，我们只是导入存储所需的依赖模块：

```jsx
import AppDispatcher from '../dispatcher/AppDispatcher';
import EventEmitter from 'events';
```

因为我们的存储将需要向调度程序注册，所以我们导入 `AppDispatcher` 模块。接下来，我们导入 `EventEmitter` 类，以便能够向我们的存储添加和移除事件监听器：

```jsx
import EventEmitter from 'events';
```

导入所有依赖项后，我们定义存储管理的数据：

```jsx
let tweet = null;
```

`TweetStore` 对象管理一个简单的推文对象，我们最初将其设置为 `null`，以标识我们尚未收到新的推文。

接下来，让我们创建两个私有方法：

```jsx
function setTweet(receivedTweet) {
  tweet = receivedTweet;
}

function emitChange() {
  TweetStore.emit('change');
}
```

`setTweet()` 函数用 `receiveTweet` 对象更新 `tweet`。`emitChange` 函数在 `TweetStore` 对象上发出 `change` 事件。这些方法对于 `TweetStore` 模块是私有的，外部无法访问。

`TweetStore.js` 文件的第二个逻辑部分是创建 `TweetStore` 对象：

```jsx
const TweetStore = Object.assign({}, EventEmitter.prototype, {
  addChangeListener(callback) {
    this.on('change', callback);
  },

  removeChangeListener(callback) {
    this.removeListener('change', callback);
  },

  getTweet() {
    return tweet;
  }
});
```

我们希望我们的存储在状态发生变化时能够通知应用程序的其他部分。我们将使用事件来实现这一点。每当我们的存储更新其状态时，它会发出 `change` 事件。对存储状态变化感兴趣的任何人都可以监听这个 `change` 事件。他们需要添加他们的事件监听器函数，我们的存储将在每个 `change` 事件上触发。为此，我们的存储定义了 `addChangeListener()` 方法，用于添加监听 `change` 事件的事件监听器，以及 `removeChangeListener()` 方法，用于移除 `change` 事件监听器。但是，`addChangeListener()` 和 `removeChangeListener()` 依赖于 `EventEmitter.prototype` 对象提供的方法。因此，我们需要将这些方法从 `EventEmitter.prototype` 对象复制到我们的 `TweetStore` 对象中。这就是 `Object.assign()` 函数的作用：

```jsx
targetObject = Object.assign(
  targetObject, 
  sourceObject1,
  sourceObject2
);
```

`Object.assign()`将`sourceObject1`和`sourceObject2`拥有的属性复制到`targetObject`，然后返回`targetObject`。在我们的情况下，`sourceObject1`是`EventEmitter.prototype`，`sourceObject2`是一个定义了我们存储器方法的对象字面量：

```jsx
{
  addChangeListener(callback) {
    this.on('change', callback);
  },

  removeChangeListener(callback) {
    this.removeListener('change', callback);
  },

  getTweet() {
    return tweet;
  }
}
```

`Object.assign()`方法返回从所有源对象复制的属性的`targetObject`。这就是我们的`TweetStore`对象所做的。

你是否注意到我们将`getTweet()`函数定义为`TweetStore`对象的一个方法，而对`setTweet()`函数却没有这样做。为什么呢？

稍后，我们将导出`TweetStore`对象，这意味着它的所有属性都将可供应用程序的其他部分使用。我们希望它们能够从`TweetStore`获取数据，但不能直接通过调用`setTweet()`来更新数据。相反，更新任何存储器中的数据的唯一方法是创建一个操作并将其分派（使用调度程序）到已向该调度程序注册的存储器。当存储器收到该操作时，它可以决定如何更新其数据。

这是 Flux 架构非常重要的一个方面。存储器完全控制管理它们的数据。它们只允许应用程序中的其他部分读取数据，但永远不会直接写入数据。只有操作应该改变存储器中的数据。

`TweetStore.js`文件的第三个逻辑部分是创建一个操作处理程序并向调度程序注册存储器。

首先，我们创建操作处理程序函数：

```jsx
function handleAction(action) {
  if (action.type === 'receive_tweet') {
    setTweet(action.tweet);
    emitChange();
  }
}
```

`handleAction()`函数以`action`对象作为参数，并检查其类型属性。在 Flux 中，所有存储器都会收到所有操作，但并非所有存储器都对所有操作感兴趣，因此每个存储器必须决定自己感兴趣的操作。为此，存储器必须检查操作类型。在我们的`TweetStore`存储器中，我们检查操作类型是否为`receive_tweet`，这意味着我们的应用程序已收到一条新推文。如果是这样，那么我们的`TweetStore`调用其私有的`setTweet()`函数来使用来自`action`对象的新推文更新`tweet`对象，即`action.tweet`。当存储器更改其数据时，它需要告诉所有对数据更改感兴趣的人。为此，它调用其私有的`emitChange()`函数，发出`change`事件并触发应用程序中其他部分创建的所有事件侦听器。

我们的下一个任务是将`TweetStore`商店与调度程序注册。要将商店与调度程序注册，您需要调用调度程序的`register()`方法，并将商店的操作处理程序函数作为回调函数传递给它。每当调度程序分派一个操作时，它都会调用该回调函数并将操作对象传递给它。

让我们来看看我们的例子：

```jsx
TweetStore.dispatchToken = AppDispatcher.register(handleAction);
```

我们在`AppDispatcher`对象上调用`register()`方法，并将`handleAction`函数作为参数传递。`register()`方法返回一个标识`TweetStore`商店的令牌。我们将该令牌保存为我们的`TweetStore`对象的属性。

`TweetStore.js`文件的第四个逻辑部分是导出`TweetStore`对象：

```jsx
export default TweetStore;
```

这就是您创建一个简单商店的方式。现在，既然我们已经实现了我们的第一个操作创建者、调度程序和商店，让我们重新审视 Flux 架构，并看看它是如何工作的：

1.  商店向调度程序注册自己。

1.  操作创建者通过调度程序创建和分派操作到商店。

1.  商店检查相关操作并相应地更改它们的数据。

1.  商店通知所有正在听的人数据变化。

这是有道理的，你可能会说，但是是什么触发了操作创建者？谁在监听商店更新？这些都是非常好的问题。答案等着你在我们的下一章中。

# 总结

在本章中，您分析了我们的 React 应用程序的架构。您学习了 Flux 架构背后的核心概念，并实现了调度程序、操作创建者和商店。

在下一章中，我们将把它们整合到我们的 React 应用程序中，并让我们的架构准备好迎接维护的天堂。
