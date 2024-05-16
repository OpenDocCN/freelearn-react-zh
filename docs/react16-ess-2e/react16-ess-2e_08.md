# 第八章：构建复杂的 React 组件

在本章中，我们将通过构建应用程序中最复杂的组件，也就是我们`Collection`组件的子组件，将你到目前为止学到的关于 React 组件的一切付诸实践。我们在本章的目标是获得扎实的 React 经验并增强我们的 React 能力。让我们开始吧！

# 创建 TweetList 组件

如你所知，我们的`Collection`组件有两个子组件：`CollectionControls`和`TweetList`。

我们将首先构建`TweetList`组件。创建以下`~/snapterest/source/components/TweetList.js`文件：

```jsx
import React, { Component } from 'react';
import Tweet from './Tweet'; 
import TweetUtils from '../utils/TweetUtils';

const listStyle = {
  padding: '0'
};

const listItemStyle = {
  display: 'inline-block',
  listStyle: 'none'
};

class TweetList extends Component {

  getTweetElement = (tweetId) => {
    const { tweets, onRemoveTweetFromCollection } = this.props;
    const tweet = tweets[tweetId];
    let tweetElement;

    if (onRemoveTweetFromCollection) {
      tweetElement = (
        <Tweet
          tweet={tweet}
          onImageClick={onRemoveTweetFromCollection}
        />
      );
    } else {
      tweetElement = <Tweet tweet={tweet}/>;
    }

    return (
      <li style={listItemStyle} key={tweet.id}>
        {tweetElement}
      </li>
    );
  }

  render() {
    const tweetElements = TweetUtils
      .getListOfTweetIds()
      .map(this.getTweetElement);

    return (
      <ul style={listStyle}>
        {tweetElements}
      </ul>
    );
  }
}

export default TweetList;
```

`TweetList`组件渲染推文列表：

```jsx
render() {
  const tweetElements = TweetUtils
    .getListOfTweetIds()
    .map(this.getTweetElement);

  return (
    <ul style={listStyle}>
      {tweetElements}
    </ul>
  );
}
```

首先，我们创建一个`Tweet`元素列表：

```jsx
const tweetElements = TweetUtils
  .getListOfTweetIds()
  .map(this.getTweetElement);
```

`TweetUtils.getListOfTweetIds()`方法返回一个推文 ID 数组。

然后，对于数组中的每个推文 ID，我们创建一个`Tweet`组件。为此，我们将在推文 ID 数组上调用`map()`方法，并将`this.getTweetElement`方法作为参数传递：

```jsx
getTweetElement = (tweetId) => {
  const { tweets, onRemoveTweetFromCollection } = this.props;
  const tweet = tweets[tweetId];
  let tweetElement;

  if (onRemoveTweetFromCollection) {
    tweetElement = (
      <Tweet
        tweet={tweet}
        onImageClick={onRemoveTweetFromCollection}
      />
    );
  } else {
    tweetElement = <Tweet tweet={tweet} />;
  }

  return (
    <li style={listItemStyle} key={tweet.id}>
      {tweetElement}
    </li>
  );
}
```

`getTweetElement()`方法返回一个包裹在`<li>`元素中的`Tweet`元素。正如我们已经知道的，`Tweet`组件有一个可选的`onImageClick`属性。我们何时想要提供这个可选属性，何时不想要呢？

有两种情况。在第一种情况下，用户将点击推文图像以将其从推文集合中移除。在这种情况下，我们的`Tweet`组件将对`click`事件做出反应，因此我们需要提供`onImageClick`属性。在第二种情况下，用户将导出一个没有用户交互的静态推文集合。在这种情况下，我们不需要提供`onImageClick`属性。

这正是我们在`getTweetElement()`方法中所做的：

```jsx
const { tweets, onRemoveTweetFromCollection } = this.props;
const tweet = tweets[tweetId];
let tweetElement;

if (onRemoveTweetFromCollection) {
  tweetElement = (
    <Tweet
      tweet={tweet}
      onImageClick={onRemoveTweetFromCollection}
    />
  );
} else {
  tweetElement = <Tweet tweet={tweet}/>;
}
```

我们创建一个`tweet`常量，其中存储了一个由`tweetId`参数提供的推文。然后，我们创建一个常量，其中存储了由父`Collection`组件传递的`this.props.onRemoveTweetFromCollection`属性。

接下来，我们检查`this.props.onRemoveTweetFromCollection`属性是否由`Collection`组件提供。如果是，则我们创建一个带有`onImageClick`属性的`Tweet`元素：

```jsx
tweetElement = (
  <Tweet
    tweet={tweet}
    onImageClick={onRemoveTweetFromCollection}
  />
);
```

如果没有提供，则创建一个没有`handleImageClick`属性的`Tweet`元素：

```jsx
tweetElement = <Tweet tweet={tweet} />;
```

我们在以下两种情况下使用`TweetList`组件：

+   该组件用于在`Collection`组件中呈现推文集合。在这种情况下，提供了`onRemoveTweetFromCollection`属性。

+   当渲染代表`Collection`组件中一系列推文的 HTML 标记字符串时，将使用这个组件。在这种情况下，`onRemoveTweetFromCollection`属性*不会*被提供。

一旦我们创建了我们的`Tweet`元素，并将其放入`tweetElement`变量中，我们就返回带有内联样式的`<li>`元素：

```jsx
return (
  <li style={listItemStyle} key={tweet.id}>
    {tweetElement}
  </li>
);
```

除了`style`属性，我们的`<li>`元素还有一个`key`属性。它被 React 用来标识动态创建的每个子元素。我建议你阅读更多关于动态子元素的内容，网址是[`facebook.github.io/react/docs/lists-and-keys.html`](https://facebook.github.io/react/docs/lists-and-keys.html)。

这就是`getTweetElement()`方法的工作原理。因此，`TweetList`组件返回一个`Tweet`元素的无序列表：

```jsx
return (
  <ul style={listStyle}>
    {tweetElements}
  </ul>
);
```

# 创建`CollectionControls`组件

现在，既然你了解了`Collection`组件渲染的内容，让我们讨论它的子组件。我们将从`CollectionControls`开始。创建以下`~/snapterest/source/components/CollectionControls.js`文件：

```jsx
import React, { Component } from 'react';
import Header from './Header';
import Button from './Button';
import CollectionRenameForm from './CollectionRenameForm';
import CollectionExportForm from './CollectionExportForm';

class CollectionControls extends Component {
  state = {
    name: 'new',
    isEditingName: false
  };

  getHeaderText = () => {
    const { name } = this.state;
    const { numberOfTweetsInCollection } = this.props;
    let text = numberOfTweetsInCollection;

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

  setCollectionName = (name) => {
    this.setState({
      name,
      isEditingName: false
    });
  }

  render() {
    const { name, isEditingName } = this.state;
    const {
      onRemoveAllTweetsFromCollection,
      htmlMarkup
    } = this.props;

    if (isEditingName) {
      return (
        <CollectionRenameForm
          name={name}
          onChangeCollectionName={this.setCollectionName}
          onCancelCollectionNameChange={this.toggleEditCollectionName}
        />
      );
    }

    return (
      <div>
        <Header text={this.getHeaderText()}/>

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

`CollectionControls`组件，顾名思义，渲染一个用户界面来控制一个集合。这些控件允许用户执行以下操作：

+   重命名一个集合

+   清空一个集合

+   导出一个集合

一个集合有一个名称。默认情况下，这个名称是`new`，用户可以更改它。集合名称显示在由`CollectionControls`组件渲染的标题中。这个组件是存储集合名称的完美候选者，由于更改名称将需要组件重新渲染，我们将把那个名称存储在组件的状态对象中：

```jsx
state = {
  name: 'new',
  isEditingName: false
};
```

`CollectionControls`组件可以渲染集合控制元素，也可以渲染一个改变集合名称的表单。用户可以在两者之间切换。我们需要一种方式来表示这两种状态——我们将使用`isEditingName`属性来实现这个目的。默认情况下，`isEditingName`被设置为`false`；因此，当`CollectionControls`组件被挂载时，用户将看不到改变集合名称的表单。让我们来看一下它的`render()`方法：

```jsx
render() {
  const { name, isEditingName } = this.state;
  const {
    onRemoveAllTweetsFromCollection,
    htmlMarkup
  } = this.props;

  if (isEditingName) {
    return (
      <CollectionRenameForm
        name={name}
        onChangeCollectionName={this.setCollectionName}
        onCancelCollectionNameChange={this.toggleEditCollectionName}
      />
    );
  }

  return (
    <div>
      <Header text={this.getHeaderText()}/>

      <Button
        label="Rename collection"
        handleClick={this.toggleEditCollectionName}
      />

      <Button
        label="Empty collection"
        handleClick={onRemoveAllTweetsFromCollection}
      />

      <CollectionExportForm htmlMarkup={htmlMarkup}/>
    </div>
  );
}
```

首先，我们检查组件状态的`this.state.isEditingName`属性是否设置为`true`。如果是，那么`CollectionControls`组件将返回`CollectionRenameForm`组件，它渲染一个改变集合名称的表单：

```jsx
<CollectionRenameForm
  name={name}
  onChangeCollectionName={this.setCollectionName}
  onCancelCollectionNameChange={this.toggleEditCollectionName}
/>
```

`CollectionRenameForm`组件渲染一个改变集合名称的表单。它接收三个属性：

+   引用当前集合名称的`name`属性

+   引用组件方法的`onChangeCollectionName`和`onCancelCollectionNameChange`属性

我们将在本章后面实现`CollectionRenameForm`组件。现在让我们更仔细地看看`setCollectionName`方法：

```jsx
setCollectionName = (name) => {
  this.setState({
    name,
    isEditingName: false
  });
}
```

`setCollectionName()`方法更新集合的名称，并通过更新组件的状态来隐藏编辑集合名称的表单。当用户提交新的集合名称时，我们将调用此方法。

现在，让我们看一下`toggleEditCollectionName()`方法：

```jsx
toggleEditCollectionName = () => {
  this.setState(prevState => ({
    isEditingName: !prevState.isEditingName
  }));
}
```

通过使用`!`运算符将`isEditingName`属性设置为其当前布尔值的相反值，此方法显示或隐藏集合名称编辑表单。当用户单击**重命名集合**或**取消**按钮时，我们将调用此方法，即显示或隐藏集合名称更改表单。

如果`CollectionControls`组件状态的`this.state.isEditingName`属性设置为`false`，那么它将返回集合控件：

```jsx
return (
  <div>
    <Header text={this.getHeaderText()}/>

    <Button
      label="Rename collection"
      handleClick={this.toggleEditCollectionName}
    />

    <Button
      label="Empty collection"
      handleClick={onRemoveAllTweetsFromCollection}
    />

    <CollectionExportForm htmlMarkup={htmlMarkup}/>
  </div>
);
```

我们将`Header`组件、两个`Button`组件和`CollectionExportForm`组件包装在一个`div`元素中。您已经在上一章中熟悉了`Header`组件。它接收一个引用字符串的`text`属性。但是，在这种情况下，我们不直接传递一个字符串，而是调用`this.getHeaderText()`函数：

```jsx
<Header text={this.getHeaderText()} />
```

反过来，`this.getHeaderText()`返回一个字符串。让我们更仔细地看看`getHeaderText()`方法：

```jsx
getHeaderText = () => {
  const { name } = this.state;
  const { numberOfTweetsInCollection } = this.props;
  let text = numberOfTweetsInCollection;

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
```

该方法根据集合中的推文数量生成标题字符串。该方法的重要特点是它不仅返回一个字符串，而是封装该字符串的 React 元素树。首先，我们创建`numberOfTweetsInCollection`常量。它存储了集合中的推文数量。然后，我们创建一个`text`变量，并将其赋值为集合中的推文数量。此时，`text`变量存储一个整数值。我们的下一个任务是根据该整数值的内容将正确的字符串连接到它上：

+   如果`numberOfTweetsInCollection`为`1`，那么我们需要连接`' tweet in your'`

+   否则，我们需要连接`' tweets in your'`

创建标题字符串后，我们将返回以下元素：

```jsx
return (
  <span>
    {text} <strong>{name}</strong> collection
  </span>
);
```

最终字符串封装在`<span>`元素内，包括`text`变量的值、集合名称和`collection`关键字；考虑以下示例：

```jsx
1 tweet in your new collection.
```

一旦`getHeaderText()`方法返回这个字符串，它就作为一个属性传递给`Header`组件。我们在`CollectionControls`组件的`render()`方法中的下一个收藏控制元素是`Button`：

```jsx
<Button
  label="Rename collection"
  handleClick={this.toggleEditCollectionName}
/>
```

我们将`Rename collection`字符串传递给它的`label`属性，将`this.toggleEditCollectionName`方法传递给它的`handleClick`属性。因此，这个按钮将有`Rename collection`标签，并且它将切换一个表单来改变收藏的名称。

下一个收藏控制元素是我们的第二个`Button`组件：

```jsx
<Button
  label="Empty collection"
  handleClick={onRemoveAllTweetsFromCollection}
/>
```

你可以猜到，它将有一个`Empty collection`标签，并且它将从收藏中删除所有的推文。

我们的最终收藏控制元素是`CollectionExportForm`：

```jsx
<CollectionExportForm htmlMarkup={htmlMarkup} />
```

这个元素接收一个表示我们收藏的 HTML 标记字符串，并且它将渲染一个按钮。我们将在本章后面创建这个组件。

现在，既然你了解了`CollectionControls`组件将渲染什么，让我们更仔细地看一下它的子组件。我们将从`CollectionRenameForm`组件开始。

# 创建`CollectionRenameForm`组件

首先，让我们创建`~/snapterest/source/components/CollectionRenameForm.js`文件：

```jsx
import React, { Component } from 'react';
import Header from './Header';
import Button from './Button';

const inputStyle = {
  marginRight: '5px'
};

class CollectionRenameForm extends Component {
  constructor(props) {
    super(props);

    const { name } = props;

    this.state = {
      inputValue: name
    };
  }

  setInputValue = (inputValue) => {
    this.setState({
      inputValue
    });
  }

  handleInputValueChange = (event) => {
    const inputValue = event.target.value;
    this.setInputValue(inputValue);
  }

  handleFormSubmit = (event) => {
    event.preventDefault();

    const { onChangeCollectionName } = this.props;
    const { inputValue: collectionName } = this.state;

    onChangeCollectionName(collectionName);
  }

  handleFormCancel = (event) => {
    event.preventDefault();

    const {
      name: collectionName,
      onCancelCollectionNameChange
    } = this.props;

    this.setInputValue(collectionName);
    onCancelCollectionNameChange();
  }

  componentDidMount() {
    this.collectionNameInput.focus();
  }

  render() {
    const { inputValue } = this.state;

    return (
      <form className="form-inline" onSubmit={this.handleSubmit}>

        <Header text="Collection name:"/>
        <div className="form-group">
          <input
            className="form-control"
            style={inputStyle}
            onChange={this.handleInputValueChange}
            value={inputValue}
            ref={input => { this.collectionNameInput = input; }}
          />
        </div>

        <Button
          label="Change"
          handleClick={this.handleFormSubmit}
        />
        <Button
          label="Cancel"
          handleClick={this.handleFormCancel}
        />
      </form>
    );
  }
}

export default CollectionRenameForm;
```

这个组件渲染一个表单来改变收藏的名称：

```jsx
render() {
  const { inputValue } = this.state;

  return (
    <form className="form-inline" onSubmit={this.handleSubmit}>

      <Header text="Collection name:"/>
      <div className="form-group">
        <input
          className="form-control"
          style={inputStyle}
          onChange={this.handleInputValueChange}
          value={inputValue}
          ref={input => this.collectionNameInput = input}
        />
      </div>

      <Button
        label="Change"
        handleClick={this.handleFormSubmit}
      />
      <Button
        label="Cancel"
        handleClick={this.handleFormCancel}
      />
    </form>
  );
}
```

我们的`<form>`元素包裹着四个元素，它们分别是：

+   一个`Header`组件

+   一个`<input>`元素

+   两个`Button`组件

`Header`组件渲染`"Collection name:"`字符串。`<input>`元素包裹在一个`<div>`元素内，该元素的`className`属性设置为`form-group`。这个名称是我们在第五章中讨论的 Bootstrap 框架的一部分。它用于布局和样式，并不是我们 React 应用程序逻辑的一部分。

`<input>`元素有相当多的属性。让我们仔细看一下它：

```jsx
<input
  className="form-control"
  style={inputStyle}
  onChange={this.handleInputValueChange}
  value={inputValue}
  ref={input => { this.collectionNameInput = input; }}
/>
```

以下是前面代码中使用的属性的描述：

+   `className`属性设置为`form-control`。这是 Bootstrap 框架的一部分，我们将用它来进行样式设置。

+   此外，我们使用`style`属性将我们自己的样式应用到这个`input`元素，该属性引用了一个包含单个样式规则的`inputStyle`对象，即`marginRight`。

+   `value`属性设置为组件状态中存储的当前值，`this.state.inputValue`。

+   `onChange`属性引用了一个`handleInputValueChange`方法，这是一个`onchange`事件处理程序。

+   `ref`属性是一个特殊的 React 属性，你可以附加到任何组件上。它接受一个回调函数，React 会在组件被挂载和卸载后立即执行。它允许我们访问我们的 React 组件渲染的 DOM `input`元素。

我希望你关注最后三个属性：`value`、`onChange`和`ref`。`value`属性设置为组件状态的属性，改变该值的唯一方法是更新其状态。另一方面，我们知道用户可以与输入字段交互并改变其值。这种行为会应用到我们的组件吗？不会。每当用户键入时，我们的输入字段的值不会改变。这是因为组件控制着`<input>`，而不是用户。在我们的`CollectionRenameForm`组件中，`<input>`的值始终反映`this.state.inputValue`属性的值，而不管用户键入了什么。用户没有控制权，而是`CollectionRenameForm`组件有。

那么，我们如何确保我们的输入字段对用户输入做出反应？我们需要监听用户输入，并更新`CollectionRenameForm`组件的状态，这将重新渲染带有更新值的输入字段。在每个输入的`change`事件上这样做将使我们的输入看起来像是正常工作的，用户可以自由地改变其值。

为此，我们为我们的`<input>`元素提供了引用组件的`this.handleInputValueChange`方法的`onChange`属性：

```jsx
handleInputValueChange = (event) => {
  const inputValue = event.target.value;
  this.setInputValue(inputValue);
}
```

正如我们在第四章中讨论的那样，*创建你的第一个 React 组件*，React 将`SyntheticEvent`的实例传递给事件处理程序。`handleInputValueChange()`方法接收一个带有`target`属性的`event`对象，该属性具有一个`value`属性。这个`value`属性存储了用户在输入字段中键入的字符串。我们将这个字符串传递给我们的`this.setInputValue()`方法：

```jsx
setInputValue = (inputValue) => {
  this.setState({
    inputValue
  });
}
```

`setInputValue()`方法是一个方便的方法，它使用新的输入值更新组件的状态。反过来，这个更新将重新渲染带有更新值的`<input>`元素。

当`CollectionRenameForm`组件被挂载时，初始输入的值是多少？让我们来看一下：

```jsx
constructor(props) {
  super(props);

  const { name } = props;

  this.state = {
    inputValue: name
  };
}
```

正如你所看到的，我们从父组件传递了集合的名称，并且我们用它来设置组件的初始状态。

在挂载此组件后，我们希望将焦点设置在输入字段上，以便用户可以立即开始编辑集合的名称。我们知道一旦组件插入到 DOM 中，React 就会调用它的`componentDidMount()`方法。这个方法是我们设置`focus`的最佳机会：

```jsx
componentDidMount() {
  this.collectionNameInput.focus();
}
```

为了做到这一点，我们通过引用`this.collectionNameInput`获取我们的输入元素，并在其上调用`focus()`函数。

我们如何在`componentDidMount()`方法中引用 DOM 元素？记住，我们为我们的`input`元素提供了`ref`属性。然后我们将一个回调函数传递给该`ref`属性，该回调函数反过来将 DOM 输入元素的引用分配给`this.collectionNameInput`。所以现在我们可以通过访问`this.collectionNameInput`属性来获取该引用。

最后，让我们讨论一下我们的两个表单按钮：

+   `Change`按钮提交表单并更改集合名称

+   `Cancel`按钮提交表单，但不会更改集合名称

我们先从一个`Change`按钮开始：

```jsx
<Button
  label="Change"
  handleClick={this.handleFormSubmit}
/>
```

当用户点击它时，将调用`this.handleFormSubmit`方法：

```jsx
handleFormSubmit = (event) => {
  event.preventDefault();

  const { onChangeCollectionName } = this.props;
  const { inputValue: collectionName } = this.state;

  onChangeCollectionName(collectionName);
}
```

我们取消了`submit`事件，然后从组件的状态中获取集合名称，并将其传递给`this.props.onChangeCollectionName()`函数调用。`onChangeCollectionName`函数是由父`CollectionControls`组件传递的。调用此函数将更改我们的集合名称。

现在让我们讨论一下我们的第二个表单按钮：

```jsx
<Button
  label="Cancel"
  handleClick={this.handleFormCancel}
/>
```

当用户点击它时，将调用`this.handleFormCancel`方法：

```jsx
handleFormCancel = (event) => {
  event.preventDefault();

  const {
    name: collectionName,
    onCancelCollectionNameChange
  } = this.props;

  this.setInputValue(collectionName);
  onCancelCollectionNameChange();
}
```

再一次，我们取消了一个`submit`事件，然后获取由父`CollectionControls`组件作为属性传递的原始集合名称，并将其传递给我们的`this.setInputValue()`函数。然后，我们调用`this.props.onCancelCollectionNameChange()`函数，隐藏集合控件。

这是我们的`CollectionRenameForm`组件。接下来，让我们创建我们的`Button`组件，我们在`CollectionRenameForm`组件中重复使用了两次。

# 创建 Button 组件

创建以下`~/snapterest/source/components/Button.js`文件：

```jsx
import React from 'react';

const buttonStyle = {
  margin: '10px 0'
};

const Button = ({ label, handleClick }) => (
  <button
    className="btn btn-default"
    style={buttonStyle}
    onClick={handleClick}
  >
    {label}
  </button>
);

export default Button;
```

`Button`组件渲染一个按钮。

请注意，我们没有声明一个类，而是定义了一个简单的名为`Button`的函数。这是创建 React 组件的功能性方式。实际上，当您的组件的目的纯粹是渲染一些用户界面元素，有或没有任何 props 时，建议您使用这种方法。

您可以将这个简单的 React 组件看作是一个“纯”函数，它以`props`对象的形式作为输入，并以 JSX 作为输出——无论您调用这个函数多少次，输出都是一致的。

理想情况下，大多数组件都应该以这种方式创建——作为“纯”JavaScript 函数。当然，当您的组件具有状态时，这是不可能的，但对于所有无状态组件——有机会！现在看看我们迄今为止创建的所有组件，看看您是否可以将它们重写为“纯”函数，而不是使用类。

我建议您阅读有关功能性与类组件的更多信息：[`facebook.github.io/r`](https://facebook.github.io/r)

您可能想知道为什么为按钮创建一个专用组件的好处，如果您可以直接使用`<button>`元素？将组件视为`<button>`元素和其他内容的包装器。在我们的情况下，大多数`<button>`元素都具有相同的样式，因此将`<button>`和样式对象封装在组件中，并重用该组件是有意义的。因此，有了专用的`Button`组件。它期望从父组件接收两个属性：

+   `label`属性是按钮的标签

+   `handleClick`属性是一个回调函数，当用户点击此按钮时调用

现在，是时候创建我们的`CollectionExportForm`组件了。

# 创建`CollectionExportForm`组件

`CollectionExportForm`组件负责将集合导出到第三方网站（[`codepen.io`](http://codepen.io)）。一旦您的集合在 CodePen 上，您可以保存它并与朋友分享。让我们看看如何做到这一点。

创建`~/snapterest/source/components/CollectionExportForm.js`文件：

```jsx
import React from 'react';

const formStyle = {
  display: 'inline-block'
};

const CollectionExportForm = ({ htmlMarkup }) => (
  <form
      action="http://codepen.io/pen/define"
      method="POST"
      target="_blank"
      style={formStyle}
    >
      <input type="hidden" name="data" value={htmlMarkup}/>
      <button type="submit" className="btn btn-default">
        Export as HTML
      </button>
    </form>
);

export default CollectionExportForm;
```

`CollectionExportForm`组件呈现一个带有`<input>`和`<button>`元素的表单。`<input>`元素是隐藏的，其值设置为由父组件作为`htmlMarkup`属性传递的 HTML 标记字符串。`<button>`元素是此表单中唯一对用户可见的元素。当用户单击**导出为 HTML**按钮时，将提交一个集合到 CodePen，该集合将在新窗口中打开。然后用户可以修改和共享该集合。

恭喜！到目前为止，您已经使用 React 构建了一个完全功能的 Web 应用程序。让我们看看它是如何工作的。

首先，请确保我们在第二章中安装和配置的 Snapkite Engine 正在运行。导航到`~/snapkite-engine/`并运行以下命令：

```jsx
**npm start**

```

然后，打开一个新的终端窗口，导航到`~/snapterest/`，并运行以下命令：

```jsx
**npm start**

```

现在在您的 Web 浏览器中打开`~/snapterest/build/index.html`。您将看到新的推文出现。单击它们将其添加到您的收藏中。再次单击它们将单个推文从收藏中删除。单击**清空收藏**按钮可从收藏中删除所有推文。单击**重命名收藏**按钮，输入新的收藏名称，然后单击**更改**按钮。最后，单击**导出为 HTML**按钮将您的收藏导出到[CodePen.io](http://CodePen.io)。如果您在本章或之前的章节中遇到任何问题，请转到[`github.com/fedosejev/react-essentials`](https://github.com/fedosejev/react-essentials)并创建一个新问题。

# 摘要

在这一章中，您创建了`TweetList`，`CollectionControls`，`CollectionRenameForm`，`CollectionExportForm`和`Button`组件。您完成了构建一个完全功能的 React 应用程序。

在接下来的章节中，我们将使用 Jest 测试这个应用程序，并使用 Flux 和 Redux 进行增强。
