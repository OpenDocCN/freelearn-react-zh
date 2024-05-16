# 第十二章：使用 Redux 完善 Flux 应用程序

前一章向您介绍了在 Flux 架构之上构建的完整的 React 应用程序的实现。在本章中，您将对此应用程序进行一些修改，以便它使用 Redux 库来实现 Flux 架构。本章的组织方式如下：

+   Redux 的简要概述

+   实现控制状态的减速器功能

+   构建 Redux 动作创建者

+   将组件连接到 Redux 存储库

+   Redux 进入应用程序状态的入口点

# 为什么选择 Redux？

在开始重构应用程序之前，我们将花几分钟时间高层次地了解 Redux。足够激发您的兴趣。准备好了吗？

## 一切由一个存储库控制

传统 Flux 应用程序和 Redux 之间的第一个主要区别是，使用 Redux 时，您只有一个存储库。传统的 Flux 架构可能也只需要一个存储库，但可能有几个存储库。您可能会认为拥有多个存储库实际上可以简化架构，因为您可以通过应用程序的不同部分分离状态。的确，这是一个不错的策略，但在实践中并不一定成立。创建多个存储库可能会导致混乱。存储库是架构中的移动部件；如果您有更多的存储库，就会有更多的可能出现问题的地方。

Redux 通过只允许一个存储库来消除了这一因素。您可能会认为这会导致一个庞大的数据结构，难以供各种应用程序功能使用。但事实并非如此，因为您可以自由地按照自己的意愿构建存储库。

## 更少的移动部件

通过只允许一个存储库，Redux 将移动部件排除在外。Redux 简化架构的另一个地方是消除了对专用调度程序的需求。在传统的 Flux 架构中，调度程序是一个独立的组件，用于向存储库发送消息。由于 Redux 架构中只有一个存储库，您可以直接将操作分派到存储库。换句话说，存储库就是调度程序。

Redux 在代码中减少移动部件数量的最终位置是事件监听器。在传统的 Flux 应用程序中，您必须手动订阅和取消订阅存储事件，以正确地连接一切。当您可以让一个库处理连接工作时，这会分散注意力。这是 Redux 擅长的事情。

## 使用 Flux 的最佳部分

Redux 并不是传统意义上的 Flux。Flux 有一个规范和一个实现它的库。Redux 不是这样的。正如前面所提到的，Redux 是对 Flux 的简化。它保留了所有导致健壮应用架构的 Flux 概念，同时忽略了那些让 Flux 难以实现和最终难以采用的繁琐部分。

# 用减速器控制状态

Redux 的旗舰概念是，状态由减速器函数控制。在本节中，我们将让你了解减速器是什么，然后实现在你的 Snapterest 应用中的减速器函数。

## 什么是减速器？

减速器是函数，它接受一个数据集合，比如对象或数组，并返回一个新的集合。返回的集合可以包含与初始集合相同的数据，也可以包含完全不同的数据。在 Redux 应用中，减速器函数接受一个状态片段，并返回一个新的状态片段。就是这样！你刚刚学会了 Redux 架构的关键。现在让我们看看减速器函数的实际应用。

Redux 应用中的减速器函数可以分成代表它们所处理的应用状态部分的模块。我们将先看看 Snapterest 应用的集合减速器，然后是推文减速器。

## 集合减速器

现在让我们来看看改变应用状态部分的集合减速器函数。首先，让我们来看看完整的函数：

```jsx
const collectionReducer = (
  state = initialState,
  action
) => {
  let tweet;
  let collectionTweets;

  switch (action.type) {
    case 'add_tweet_to_collection':
      tweet = {};
      tweet[action.tweet.id] = action.tweet;

      return {
        ...state,
        collectionTweets: {
          ...state.collectionTweets,
          ...tweet
        }
      };

    case 'remove_tweet_from_collection':
      collectionTweets = { ...state.collectionTweets };
      delete collectionTweets[action.tweetId];

      return {
        ...state,
        collectionTweets
      };

    case 'remove_all_tweets_from_collection':
      collectionTweets = {};

      return {
        ...state,
        collectionTweets
      };

    case 'set_collection_name':
      return {
        ...state,
        collectionName: state.editingName,
        isEditingName: false
      };

    case 'toggle_is_editing_name':
      return {
        ...state,
        isEditingName: !state.isEditingName
      };

    case 'set_editing_name':
      return {
        ...state,
        editingName: action.editingName
      };

    default:
      return state;
  }
};
```

正如你所看到的，返回的新状态是基于分发的动作。动作名称作为参数提供给这个函数。现在让我们来看看这个减速器的不同情景。

### 将推文添加到集合中

让我们来看看`add_tweet_to_collection`动作：

```jsx
case 'add_tweet_to_collection':
  tweet = {};
  tweet[action.tweet.id] = action.tweet;

  return {
    ...state,
    collectionTweets: {
      ...state.collectionTweets,
      ...tweet
    }
  };
```

`switch`语句检测到`动作类型`是`add_tweet_to_collection`。动作还有一个包含要添加的实际推文的`推文`属性。这里使用`推文`变量来构建一个以`推文`ID 为键，`推文`为值的对象。这是`collectionTweets`对象期望的格式。

然后我们返回新状态。重要的是要记住，这应该始终是一个新对象，而不是对其他对象的引用。这是你在 Redux 应用中避免意外副作用的方法。幸运的是，我们可以使用对象扩展运算符来简化这个任务。

### 从集合中删除推文

从`collectionTweets`对象中删除推文意味着我们必须删除具有要删除的`tweet` ID 的键。让我们看看这是如何完成的：

```jsx
case 'remove_tweet_from_collection':
  collectionTweets = { ...state.collectionTweets };
  delete collectionTweets[action.tweetId];

  return {
    ...state,
    collectionTweets
  };
```

注意我们如何将一个新对象分配给`collectionTweets`变量？再次，扩展运算符在这里非常有用，可以避免额外的语法。我们这样做的原因是为了使减速器始终返回一个新的引用。一旦我们从`collectionTweets`对象中删除推文，我们可以返回包括`collectionTweets`作为属性的新状态对象。

另一个推文删除动作是`remove_all_tweets_from_collection`。以下是它的样子：

```jsx
case 'remove_all_tweets_from_collection':
  collectionTweets = {};

  return {
    ...state,
    collectionTweets
  };
```

删除所有推文意味着我们可以用新的空对象替换`collectionTweets`值。

### 设置集合名称

当一组推文被重命名时，我们必须更新 Redux 存储。这是通过在调度`set_collection_name`动作时从状态中获取`editingName`来完成的：

```jsx
case 'set_collection_name':
  return {
    ...state,
    collectionName: state.editingName,
    isEditingName: false
  };
```

您可以看到`collectionName`值设置为`editingName`，`isEditingName`设置为`false`。这意味着自从值被设置以来，我们知道用户不再编辑名称。

### 编辑集合名称

您刚刚看到了如何在用户保存更改后设置集合名称。但是，当涉及在 Redux 存储中跟踪状态时，编辑文本还有更多内容。首先，我们必须允许文本首先被编辑；这给用户一些视觉提示：

```jsx
case 'toggle_is_editing_name':
  return {
    ...state,
    isEditingName: !state.isEditingName
  };
```

然后，用户正在文本输入中积极输入的文本。这也必须在存储中找到一个位置：

```jsx
case 'set_editing_name':
  return {
    ...state,
    editingName: action.editingName
  };
```

这不仅会导致适当的 React 组件重新渲染，而且意味着我们在状态中存储了文本，当用户完成编辑时可以使用。

## 推文减速器

推文减速器只需要处理一个动作，但这并不意味着我们不应该在推特减速器中单独设置模块，以预期未来的推文动作。现在，让我们专注于我们的应用当前的功能。

### 接收推文

让我们看一下处理`receive_tweet`动作的推文减速器代码：

```jsx
const tweetReducer = (state = null, action) => {
  switch(action.type) {
    case 'receive_tweet':
      return action.tweet;
    default:
      return state;
  }
};
```

这个减速器非常简单。当调度`receive_tweet`动作时，`action.tweet`值将作为新状态返回。由于这是一个小的减速器函数，这可能是指出所有减速器函数共同点的好地方。

传递给 reducer 函数的第一个参数是旧状态。这个参数有一个默认值，因为第一次调用 reducer 时，没有状态，这个值用于初始化它。在这种情况下，默认状态是 null。

关于 reducer 的第二点是，当调用时它们总是返回一个新的状态。即使它不产生任何新的状态，reducer 函数也需要返回旧状态。Redux 会将 reducer 返回的任何内容设置为新状态，即使你返回 undefined。这就是为什么在你的 switch 语句中有一个 `default` 标签是个好主意。

### 简化的 action 创建者

在 Redux 中，action 创建者函数比传统的 Flux 对应函数更简单。主要区别在于 Redux 的 action 创建者函数只返回动作数据。在传统的 Flux 中，action 创建者还负责调用分发器。让我们来看看 Snapterest 的 Redux action 创建者函数。

```jsx
export const addTweetToCollection = tweet => ({
  type: 'add_tweet_to_collection',
  tweet
});

export const removeTweetFromCollection = tweetId => ({
  type: 'remove_tweet_from_collection',
  tweetId
});

export const removeAllTweetsFromCollection = () => ({
  type: 'remove_all_tweets_from_collection'
});

export const setCollectionName = collectionName => ({
  type: 'set_collection_name',
  collectionName
});

export const toggleIsEditingName = () => ({
  type: 'toggle_is_editing_name'
});

export const setEditingName = editingName => ({
  type: 'set_editing_name',
  editingName
});

export const receiveTweet = tweet => ({
  type: 'receive_tweet',
  tweet
});
```

正如你所看到的，这些函数返回动作对象，然后可以被分发——它们实际上并不调用分发器。当我们开始将我们的 React 组件连接到 Redux 存储时，你会明白为什么会这样。在 Redux 应用中，action 创建者函数的主要责任是确保返回一个带有正确 `type` 属性的对象，以及与动作相关的属性。例如，`addTweetToCollection()` action 创建者接受一个 tweet 参数，然后通过将其作为返回对象的属性传递给动作。

# 将组件连接到应用状态

到目前为止，我们有处理创建新应用状态的 reducer 函数，以及触发我们的 reducer 函数的 action 创建者函数。我们仍然需要将我们的 React 组件连接到 Redux 存储。在本节中，您将学习如何使用 `connect()` 函数来创建一个连接到 Redux 存储的新版本组件。

## 将状态和 action 创建者映射到 props

Redux 和 React 集成的想法是告诉 Redux 用一个有状态的组件包装你的组件，当 Redux 存储改变时，它的状态也会被设置。我们所要做的就是编写一个函数，告诉 Redux 我们希望状态值以 props 的形式传递给我们的组件。此外，我们还需要告诉组件它可能想要分发的任何操作。

以下是我们连接组件时将遵循的一般模式：

```jsx
connect(
  mapStateToProps,
  mapDispatchToProps
)(Component);
```

这是它的工作原理的分解：

+   来自 React-Redux 包的`connect()`函数返回一个新的 React 组件。

+   `mapStateToProps()`函数接受一个状态参数，并返回一个基于该状态的属性值的对象。

+   `mapDispatchToProps()`函数接受一个`dispatch()`参数，用于分发操作，并返回一个包含可以分发操作的函数的对象。这些函数被添加到组件的 props 中。

+   `Component`是一个你想要连接到 Redux 存储的 React 组件。

当你开始连接组件时，你很快就会意识到 Redux 正在为你处理许多 React 组件的生命周期琐事。在你通常需要实现`componentDidMount()`功能的地方，突然间，你不需要了。这导致了清晰简洁的 React 组件。

### 连接流组件

让我们来看看`Stream`组件：

```jsx
import React, { Component } from 'react';
import { connect } from 'react-redux';

import StreamTweet from './StreamTweet';
import Header from './Header';
import TweetStore from '../stores/TweetStore';

class Stream extends Component {
  render() {
    const { tweet } = this.props;
    const { onAddTweetToCollection } = this.props;
    const headerText = 'Waiting for public photos from Twitter...';

    if (tweet) {
      return (<StreamTweet tweet={tweet}/>);
    }

    return (<Header text={headerText}/>);
  }
}

const mapStateToProps = ({ tweet }) => ({ tweet });

const mapDispatchToProps = dispatch => ({});

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(Stream);
```

从先前的实现中，`Stream`并没有太多改变。主要区别在于我们删除了一些生命周期方法。所有的 Redux 连接代码都在组件声明之后。`mapStateToProps()`函数从状态中返回`tweet`属性。所以现在我们的组件有了一个`tweet`属性。`mapDispatchToProps()`函数返回一个空对象，因为`Stream`不分发任何操作。当没有操作时，实际上不需要提供这个函数。然而，这可能会在将来发生变化，如果函数已经存在，你只需要向对象添加属性。

### 连接 StreamTweet 组件

`Stream`组件渲染了`StreamTweet`组件，所以让我们接着看下去：

```jsx
import React, { Component } from 'react';
import { connect } from 'react-redux';

import ReactDOM from 'react-dom';
import Header from './Header';
import Tweet from './Tweet';
import store from '../stores';
import { addTweetToCollection } from '../actions';

class StreamTweet extends Component {
  render() {
    const { tweet, onImageClick } = this.props;

    return (
      <section>
        <Header text="Latest public photo from Twitter"/>
        <Tweet
          tweet={tweet}
          onImageClick={onImageClick}
        />
      </section>
    );
  }
}

const mapStateToProps = state => ({});

const mapDispatchToProps = (dispatch, ownProps) => ({
  onImageClick: () => {
    dispatch(addTweetToCollection(ownProps.tweet));
  }
});

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(StreamTweet);
```

`StreamTweet`组件实际上并没有使用 Redux 存储中的任何状态。那么为什么要连接它呢？答案是，这样我们就可以将操作分发函数映射到组件 props 上。记住，在 Redux 应用中，操作创建函数只返回操作对象，而不是分发操作。

在这里的`mapDispatchToProps()`函数中，我们通过将其返回值传递给`dispatch()`来分发一个`addTweetToCollection()`操作。Redux 为我们提供了一个简单的分发函数，它绑定到 Redux 存储。每当我们想要分发一个操作时，我们只需要调用`dispatch()`。现在`StreamTweet`组件将有一个`onImageClick()`函数 prop，可以作为事件处理程序来处理点击事件。

### 连接集合组件

现在我们只需要连接`Collection`组件及其子组件。`Collection`组件的样子如下：

```jsx
import React, { Component } from 'react';
import ReactDOMServer from 'react-dom/server';
import { connect } from 'react-redux';

import CollectionControls from './CollectionControls';
import TweetList from './TweetList';
import Header from './Header';
import CollectionUtils from '../utils/CollectionUtils';

class Collection extends Component {
  createHtmlMarkupStringOfTweetList() {
    const { collectionTweets } = this.props;
    const htmlString = ReactDOMServer.renderToStaticMarkup(
      <TweetList tweets={collectionTweets}/>
    );

    const htmlMarkup = {
      html: htmlString
    };

    return JSON.stringify(htmlMarkup);
  }

  render() {
    const { collectionTweets } = this.props;
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

    return (<Header text="Your collection is empty"/>);
  }
}

const mapStateToProps = state => state.collection;

const mapDispatchToProps = dispatch => ({});

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(Collection);
```

`Collection`组件不会分发任何操作，因此我们的`mapDispatchToProps()`函数返回一个空对象。但它确实使用了 Redux 存储中的状态，所以我们的`mapStateToProps()`实现返回`state.collection`。这是我们如何将整个应用程序的状态切片成组件关心的部分。例如，如果我们的组件除了`Collection`之外还需要访问其他状态，我们将返回一个由整体状态的不同切片组成的新对象。

### 连接集合控件

在`Collection`组件内，我们有`CollectionControls`组件。让我们看看它连接到 Redux 存储后的样子：

```jsx
import React, { Component } from 'react';
import { connect } from 'react-redux';

import Header from './Header';
import Button from './Button';
import CollectionRenameForm from './CollectionRenameForm';
import CollectionExportForm from './CollectionExportForm';
import {
  toggleIsEditingName,
  removeAllTweetsFromCollection
} from '../actions';

class CollectionControls extends Component {
  getHeaderText = () => {
    const { numberOfTweetsInCollection } = this.props;
    const { collectionName } = this.props;
    let text = numberOfTweetsInCollection;

    if (numberOfTweetsInCollection === 1) {
      text = `${text} tweet in your`;
    } else {
      text = `${text} tweets in your`;
    }

    return (
      <span>
        {text} <strong>{collectionName}</strong> collection
      </span>
    );
  }

  render() {
    const {
      collectionName,
      isEditingName,
      htmlMarkup,
      onRenameCollection,
      onEmptyCollection
    } = this.props;

    if (isEditingName) {
      return (
        <CollectionRenameForm name={collectionName}/>
      );
    }

    return (
      <div>
        <Header text={this.getHeaderText()}/>

        <Button
          label="Rename collection"
          handleClick={onRenameCollection}
        />

        <Button
          label="Empty collection"
          handleClick={onEmptyCollection}
        />

        <CollectionExportForm
          html={htmlMarkup}
          title={collectionName}
        />
      </div>
    );
  }
}

const mapStateToProps = state => state.collection;

const mapDispatchToProps = dispatch => ({
  onRenameCollection: () => {
    dispatch(toggleIsEditingName());
  },
  onEmptyCollection: () => {
    dispatch(removeAllTweetsFromCollection());
  }
});

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(CollectionControls);
```

这一次，我们有一个组件需要从`mapStateToProps()`和`mapDispatchToProps()`中获取对象。我们再次需要将集合状态作为 props 传递给这个组件。`onRenameCollection()`事件处理程序分发`toggleIsEditingName()`操作，而`onEmptyCollection()`事件处理程序分发`removeAllTweetsFromCollection()`操作。

### 连接`TweetList`组件

最后，我们有`TweetList`组件；让我们来看一下：

```jsx
import React, { Component } from 'react';
import { connect } from 'react-redux';

import Tweet from './Tweet';
import { removeTweetFromCollection } from '../actions';

const listStyle = {
  padding: '0'
};

const listItemStyle = {
  display: 'inline-block',
  listStyle: 'none'
};

class TweetList extends Component {
  getListOfTweetIds = () =>
    Object.keys(this.props.tweets);

  getTweetElement = (tweetId) => {
    const {
      tweets,
      onRemoveTweetFromCollection
    } = this.props;
    const tweet = tweets[tweetId];

    return (
      <li style={listItemStyle} key={tweet.id}>
        <Tweet
          tweet={tweet}
          onImageClick={onRemoveTweetFromCollection}
        />
      </li>
    );
  }

  render() {
    const tweetElements = this
      .getListOfTweetIds()
      .map(this.getTweetElement);

    return (
      <ul style={listStyle}>
        {tweetElements}
      </ul>
    );
  }
}

const mapStateToProps = () => ({});

const mapDispatchToProps = dispatch => ({
  onRemoveTweetFromCollection: ({ id }) => {
    dispatch(removeTweetFromCollection(id));
  }
});

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(TweetList);
```

这个组件不依赖 Redux 存储的任何状态。但它确实将一个操作分发函数映射到它的 props。我们不一定需要在这里连接分发器。例如，如果这个组件的父组件正在连接函数到分发器，那么函数可以在那里声明并作为 props 传递到这个组件中。好处是`TweetList`将不再需要 Redux。缺点是在一个组件中声明太多的分发函数。幸运的是，您可以使用任何您认为合适的方法来实现您的组件。

### 创建存储并连接您的应用程序

我们几乎完成了将 Snapterest 应用程序从传统的 Flux 架构重构为基于 Redux 的架构。只剩下两件事要做。

首先，我们必须将我们的减速器函数组合成一个单一的函数，以便创建一个存储：

```jsx
import { combineReducers } from 'redux'
import collection from './collection';
import tweet from './tweet';

const reducers = combineReducers({
  collection,
  tweet
})

export default reducers;
```

这使用`combineReducers()`函数来获取我们两个现有的减速器函数，这些函数存在于它们自己的模块中，并产生一个单一的减速器，我们可以用来创建一个 Redux 存储：

```jsx
import { createStore } from 'redux';
import reducers from '../reducers';

export default createStore(reducers);
```

现在我们创建了 Redux 存储库，其中包含默认情况下由减速器函数提供的初始状态。现在我们只需将此存储库传递给我们的顶层 React 组件：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';

import Application from './components/Application';
import { initializeStreamOfTweets } from './utils/WebAPIUtils';
import store from './stores';

initializeStreamOfTweets(store);

ReactDOM.render(
  <Provider store={store}>
    <Application/>
  </Provider>,
  document.getElementById('react-application')
);
```

`Provider`组件包装了我们的顶层应用程序组件，并为其提供了状态更新，以及任何依赖应用程序状态的子组件。

# 总结

在本章中，您学习了如何使用 Redux 库来完善您的 Flux 架构。Redux 应用程序应该只有一个存储库，动作创建者可以很简单，而减速器函数控制着不可变状态的转换。简而言之，Redux 的目标是减少传统 Flux 架构中通常存在的移动部件的数量，同时保留单向数据流。

然后，您使用 Redux 实现了 Snapterest 应用程序。从减速器开始，每当分派有效动作时，您都会为 Redux 存储库返回一个新状态。然后，您构建了动作创建者函数，返回一个带有正确类型属性的对象。最后，您重构了组件，使它们连接到 Redux。您确保组件可以读取存储库数据并分派动作。

这就是这本书的总结。我希望您已经学会了关于 React 开发基础的足够知识，以便通过学习更高级的 React 主题来继续您的发现之旅。更重要的是，我希望您通过构建令人敬畏的 React 应用程序并使其更加完善，从而更多地了解了 React。
