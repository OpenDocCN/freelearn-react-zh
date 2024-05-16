# 第三章：创建你的第一个 React 元素

今天创建一个简单的网页应用程序涉及编写 HTML、CSS 和 JavaScript 代码。我们使用三种不同的技术的原因是我们想要分离三种不同的关注点：

+   内容（HTML）

+   样式（CSS）

+   逻辑（JavaScript）

这种分离对于创建网页非常有效，因为传统上，我们有不同的人在网页的不同部分工作：一个人使用 HTML 结构化内容并使用 CSS 进行样式设置，然后另一个人使用 JavaScript 实现网页上各种元素的动态行为。这是一种以内容为中心的方法。

今天，我们大多数时候不再把网站看作是一组网页了。相反，我们构建的是可能只有一个网页的网页应用程序，而这个网页并不代表我们内容的布局，而是代表我们网页应用程序的容器。这样一个只有一个网页的网页应用程序称为（不出所料的）**单页应用程序**（**SPA**）。你可能会想知道在 SPA 中如何表示其余的内容？当然，我们需要使用 HTML 标签创建额外的布局。否则，浏览器怎么知道要渲染什么呢？

这些都是合理的问题。让我们看看它是如何工作的。一旦你在浏览器中加载你的网页，它会创建该网页的**文档对象模型**（**DOM**）。DOM 以树结构表示你的网页，此时它反映了你仅使用 HTML 标签创建的布局结构。无论你是在构建传统网页还是 SPA，这都是发生的事情。两者之间的区别在于接下来会发生什么。如果你正在构建传统网页，那么你会完成创建网页的布局。另一方面，如果你正在构建 SPA，那么你需要开始通过 JavaScript 操纵 DOM 来创建额外的元素。浏览器提供了**JavaScript DOM API**来做到这一点。你可以在[`developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model`](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)了解更多信息。

然而，用 JavaScript 操纵（或改变）DOM 有两个问题：

+   如果你决定直接使用 JavaScript DOM API，你的编程风格将是命令式的。正如我们在上一章讨论的那样，这种编程风格会导致更难维护的代码库。

+   DOM 突变很慢，因为它们无法像其他 JavaScript 代码那样进行速度优化。

幸运的是，React 为我们解决了这两个问题。

# 理解虚拟 DOM

我们为什么需要首先操作 DOM 呢？因为我们的 Web 应用程序不是静态的。它们有一个由**用户界面**（**UI**）表示的状态，Web 浏览器呈现，并且当事件发生时，该状态可以改变。我们在谈论什么样的事件？我们感兴趣的有两种类型的事件：

+   **用户事件**：当用户输入、点击、滚动、调整大小等时

+   **服务器事件**：当应用程序从服务器接收数据或错误时，等等

处理这些事件时会发生什么？通常情况下，我们会更新应用程序依赖的数据，并且这些数据代表我们数据模型的状态。反过来，当我们的数据模型状态发生变化时，我们可能希望通过更新 UI 状态来反映这种变化。看起来我们想要的是一种同步两种不同状态的方法：UI 状态和数据模型状态。我们希望其中一种对另一种的变化做出反应，反之亦然。我们如何才能实现这一点？

将应用程序的 UI 状态与基础数据模型状态同步的一种方法是双向数据绑定。有不同类型的双向数据绑定。其中之一是**键值观察**（**KVO**），它在`Ember.js`、Knockout、Backbone 和 iOS 等中使用。另一个是脏检查，它在 Angular 中使用。

React 提供了一种名为**虚拟 DOM**的不同解决方案，而不是双向数据绑定。虚拟 DOM 是真实 DOM 的快速内存表示，它是一种抽象，允许我们将 JavaScript 和 DOM 视为响应式的。让我们看看它是如何工作的：

1.  每当数据模型的状态发生变化时，虚拟 DOM 和 React 将重新渲染您的 UI 以获得虚拟 DOM 表示。

1.  然后计算两个虚拟 DOM 表示之间的差异：在数据改变之前计算的先前虚拟 DOM 表示和在数据改变之后计算的当前虚拟 DOM 表示。这两个虚拟 DOM 表示之间的差异实际上是真实 DOM 中需要改变的部分。

1.  只更新真实 DOM 中需要更新的部分。

在真实 DOM 中查找虚拟 DOM 的两个表示之间的差异，并且只重新渲染更新的补丁是很快的。而且，最好的部分是——作为 React 开发人员——您不需要担心实际需要重新渲染什么。React 允许您编写代码，就好像每次应用程序状态发生变化时都重新渲染整个 DOM 一样。

如果您想了解更多关于虚拟 DOM、其背后的原理以及如何与数据绑定进行比较，那么我强烈建议您观看 Facebook 的 Pete Hunt 在[`www.youtube.com/watch?v=-DX3vJiqxm4`](https://www.youtube.com/watch?v=-DX3vJiqxm4)上的这个非常信息丰富的讲座。

现在您已经了解了虚拟 DOM，让我们通过安装 React 并创建我们的第一个 React 元素来改变真实 DOM。

# 安装 React

要开始使用 React 库，我们首先需要安装它。

在撰写本文时，React 库的最新版本是 16.0.0。随着时间的推移，React 会得到更新，因此请确保您使用的是最新版本，除非它引入了与本书提供的代码示例不兼容的破坏性更改。访问[`github.com/PacktPublishing/React-Essentials-Second-Edition`](https://github.com/PacktPublishing/React-Essentials-Second-Edition)了解代码示例与 React 最新版本之间的任何兼容性问题。

在第二章中，*为您的项目安装强大的工具*，我向您介绍了**Webpack**，它允许我们使用`import`函数导入应用程序的所有依赖模块。我们将使用`import`来导入 React 库，这意味着我们不再需要向`index.html`文件添加`<script>`标签，而是使用`npm install`命令来安装 React：

1.  转到`~/snapterest/`目录并运行此命令：

```jsx
**npm install --save react react-dom**

```

1.  然后，打开您的文本编辑器中的`~/snapterest/source/app.js`文件，并将 React 和 ReactDOM 库分别导入到`React`和`ReactDOM`变量中：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
```

`react`包含了与 React 背后的关键思想有关的方法，即以声明方式描述您想要渲染的内容。另一方面，`react-dom`包含了负责渲染到 DOM 的方法。您可以在[`facebook.github.io/react/blog/2015/07/03/react-v0.14-beta-1.html#two-packages`](https://facebook.github.io/react/blog/2015/07/03/react-v0.14-beta-1.html#two-packages)上阅读更多关于为什么 Facebook 的开发人员认为将 React 库分成两个包是一个好主意的内容。

现在我们准备在我们的项目中开始使用 React 库。接下来，让我们创建我们的第一个 React 元素！

# 使用 JavaScript 创建 React 元素

我们将首先熟悉基本的 React 术语。这将帮助我们清晰地了解 React 库的组成。这些术语很可能会随着时间的推移而更新，因此请密切关注官方文档[`facebook.github.io/react/docs/react-api.html`](https://facebook.github.io/react/docs/react-api.html)。

就像 DOM 是节点树一样，React 的虚拟 DOM 是 React 节点树。React 中的核心类型之一称为`ReactNode`。它是虚拟 DOM 的构建块，可以是以下任何一种核心类型之一：

+   `ReactElement`：这是 React 中的主要类型。它是一个轻量级的、无状态的、不可变的、虚拟表示的`DOMElement`。

+   `ReactText`：这是一个字符串或数字。它表示文本内容，是 DOM 中文本节点的虚拟表示。

`ReactElement`和`ReactText`都是`ReactNode`。`ReactNode`的数组称为`ReactFragment`。您将在本章中看到所有这些的示例。

让我们从`ReactElement`的示例开始：

1.  将以下代码添加到您的`~/snapterest/source/app.js`文件中：

```jsx
const reactElement = React.createElement('h1');
ReactDOM.render(reactElement, document.getElementById('react-application'));
```

1.  现在您的`app.js`文件应该完全像这样：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const reactElement = React.createElement('h1');
ReactDOM.render(
  reactElement,
  document.getElementById('react-application')
);
```

1.  转到`~/snapterest/`目录并运行此命令：

```jsx
**npm start**

```

您将看到以下输出：

```jsx
**Hash: 826f512cf95a44d01d39**
**Version: webpack 3.8.1**
**Time: 1851ms**

```

1.  转到`~/snapterest/build/`目录，并在 Web 浏览器中打开`index.html`。您将看到一个空白的网页。在 Web 浏览器中打开**开发者工具**，并检查空白网页的 HTML 标记。您应该在其他内容中看到这一行：

```jsx
<h1 data-reactroot></h1>
```

干得好！我们刚刚渲染了您的第一个 React 元素。让我们看看我们是如何做到的。

React 库的入口点是`React`对象。该对象有一个名为`createElement()`的方法，它接受三个参数：`type`、`props`和`children`：

```jsx
React.createElement(type, props, children);
```

让我们更详细地看看每个参数。

## type 参数

`type`参数可以是字符串或`ReactClass`：

+   字符串可以是 HTML 标记名称，例如`'div'`，`'p'`和`'h1'`。React 支持所有常见的 HTML 标记和属性。有关 React 支持的所有 HTML 标记和属性的完整列表，您可以参考[`facebook.github.io/react/docs/dom-elements.html`](https://facebook.github.io/react/docs/dom-elements.html)。

+   通过`React.createClass()`方法创建了一个`ReactClass`类。我将在第四章中更详细地介绍这个问题，*创建您的第一个 React 组件*。

`type`参数描述了 HTML 标记或`ReactClass`类将如何呈现。在我们的例子中，我们正在呈现`h1` HTML 标记。

## props 参数

`props`参数是从父元素传递给子元素（而不是反过来）的 JavaScript 对象，具有一些被视为不可变的属性，即不应更改的属性。

在使用 React 创建 DOM 元素时，我们可以传递`props`对象，其中包含代表 HTML 属性的属性，例如`class`和`style`。例如，运行以下代码：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const reactElement = React.createElement(
  'h1', { className: 'header' }
);
ReactDOM.render(
  reactElement,
  document.getElementById('react-application')
);
```

上述代码将创建一个`class`属性设置为`header`的`h1` HTML 元素：

```jsx
<h1 data-reactroot class="header"></h1>
```

请注意，我们将属性命名为`className`而不是`class`。这样做的原因是`class`关键字在 JavaScript 中是保留的。如果您将`class`用作属性名称，React 将忽略它，并在 Web 浏览器的控制台上打印有用的警告消息：

**警告：未知的 DOM 属性类。您是指 className 吗？**

**请改用 className。**

您可能想知道我们的`h1`标签中的`data-reactroot`属性是做什么的？我们没有将其传递给我们的`props`对象，那它是从哪里来的？它是由 React 添加并使用的，用于跟踪 DOM 节点。

## children 参数

`children`参数描述了此 HTML 元素应具有哪些子元素（如果有）。子元素可以是任何类型的`ReactNode`：由`ReactElement`表示的虚拟 DOM 元素，由`ReactText`表示的字符串或数字，或者其他`ReactNode`节点的数组，也称为`ReactFragment`。

让我们看看这个例子：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const reactElement = React.createElement(
  'h1',
  { className: 'header' },
  'This is React'
);
ReactDOM.render(
  reactElement,
  document.getElementById('react-application')
);
```

上述代码将创建一个带有`class`属性和文本节点`This is React`的`h1` HTML 元素：

```jsx
<h1 data-reactroot class="header">This is React</h1>
```

`h1`标签由`ReactElement`表示，而`This is React`字符串由`ReactText`表示。

接下来，让我们创建一个 React 元素，它的子元素是一些其他的 React 元素：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const h1 = React.createElement(
  'h1',
  { className: 'header', key: 'header' },
  'This is React'
);
const p = React.createElement(
  'p', 
  { className: 'content', key: 'content' },
  'And that is how it works.' );
const reactFragment = [ h1, p ];
const section = React.createElement(
  'section',
  { className: 'container' },
  reactFragment
);

ReactDOM.render(
  section,
  document.getElementById('react-application')
);
```

我们创建了三个 React 元素：`h1`，`p`和`section`。`h1`和`p`都有子文本节点，分别是`'This is React'`和`'And that is how it works.'`。`section`标签有一个子元素，是两个`ReactElement`类型的数组，`h1`和`p`，称为`reactFragment`。这也是一个`ReactNode`数组。`reactFragment`数组中的每个`ReactElement`类型都必须有一个`key`属性，帮助 React 识别该`ReactElement`类型。结果，我们得到以下 HTML 标记：

```jsx
<section data-reactroot class="container">
  <h1 class="header">This is React</h1>
  <p class="content">And that is how it works.</p>
</section>
```

现在我们明白了如何创建 React 元素。如果我们想要创建多个相同类型的 React 元素呢？这意味着我们需要为每个相同类型的元素一遍又一遍地调用`React.createElement('type')`吗？我们可以，但我们不需要，因为 React 为我们提供了一个名为`React.createFactory()`的工厂函数。工厂函数是一个创建其他函数的函数。这正是`React.createFactory(type)`所做的：它创建一个产生给定类型的`ReactElement`的函数。

考虑以下例子：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const listItemElement1 = React.createElement(
  'li',
  { className: 'item-1', key: 'item-1' },
  'Item 1'
);
const listItemElement2 = React.createElement(
  'li',
  { className: 'item-2', key: 'item-2' },
  'Item 2'
);
const listItemElement3 = React.createElement(
  'li',
  { className:   'item-3', key: 'item-3' },
  'Item 3'
);

const reactFragment = [
  listItemElement1,
  listItemElement2,
  listItemElement3
];
const listOfItems = React.createElement(
  'ul',
  { className: 'list-of-items' },
  reactFragment
);

ReactDOM.render(
  listOfItems,
  document.getElementById('react-application')
);
```

前面的例子产生了这个 HTML：

```jsx
<ul data-reactroot class="list-of-items">
  <li class="item-1">Item 1</li>
  <li class="item-2">Item 2</li>
  <li class="item-3">Item 3</li>
</ul>
```

我们可以通过首先创建一个工厂函数来简化它：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const createListItemElement = React.createFactory('li');

const listItemElement1 = createListItemElement(
  { className: 'item-1', key: 'item-1' },
  'Item 1'
);
const listItemElement2 = createListItemElement(
  { className: 'item-2', key: 'item-2' },
  'Item 2'
);
const listItemElement3 = createListItemElement(
  { className: 'item-3', key: 'item-3' },
  'Item 3'
);

const reactFragment = [
  listItemElement1,
  listItemElement2,
  listItemElement3
];
const listOfItems = React.createElement(
  'ul',
  { className: 'list-of-items' },
  reactFragment
);

ReactDOM.render(
  listOfItems,
  document.getElementById('react-application')
);
```

在前面的例子中，我们首先调用了`React.createFactory()`函数，并将`li` HTML 标签名称作为类型参数传递。然后，`React.createFactory()`函数返回一个新的函数，我们可以将其用作创建`li`类型元素的便捷缩写。我们将这个函数的引用存储在一个名为`createListItemElement`的变量中。然后，我们调用这个函数三次，每次只传递`props`和`children`参数，这些参数对于每个元素都是唯一的。请注意，`React.createElement()`和`React.createFactory()`都期望一个 HTML 标签名称字符串（如`li`）或`ReactClass`对象作为类型参数。

React 为我们提供了许多内置的工厂函数来创建常见的 HTML 标签。您可以从`React.DOM`对象中调用它们；例如，`React.DOM.ul()`，`React.DOM.li()`和`React.DOM.div()`。使用它们，我们甚至可以进一步简化我们之前的例子：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const listItemElement1 = React.DOM.li(
  { className: 'item-1', key: 'item-1' },
  'Item 1'
);
const listItemElement2 = React.DOM.li(
  { className: 'item-2', key: 'item-2' },
  'Item 2'
);
const listItemElement3 = React.DOM.li(
  { className: 'item-3', key: 'item-3' },
  'Item 3'
);

const reactFragment = [
  listItemElement1,
  listItemElement2,
  listItemElement3
];
const listOfItems = React.DOM.ul(
  { className: 'list-of-items' },
  reactFragment
);

ReactDOM.render(
  listOfItems,
  document.getElementById('react-application')
);
```

现在，我们知道如何创建`ReactNode`的树。然而，在我们继续之前，有一行重要的代码需要讨论：

```jsx
ReactDOM.render(
  listOfItems,
  document.getElementById('react-application')
);
```

您可能已经猜到了，它将我们的 `ReactNode` 树呈现到 DOM。让我们更仔细地看看它是如何工作的。

# 渲染 React 元素

`ReactDOM.render()` 方法接受三个参数：`ReactElement`、一个常规的 `DOMElement` 容器和一个 `callback` 函数：

```jsx
ReactDOM.render(ReactElement, DOMElement, callback);
```

`ReactElement` 类型是您创建的 `ReactNode` 树中的根元素。常规的 `DOMElement` 参数是该树的容器 DOM 节点。`callback` 参数是在树被渲染或更新后执行的函数。重要的是要注意，如果此 `ReactElement` 类型先前已呈现到父 `DOMElement` 容器，则 `ReactDOM.render()` 将对已呈现的 DOM 树执行更新，并且仅会改变 DOM，因为需要反映 `ReactElement` 类型的最新版本。这就是为什么虚拟 DOM 需要较少的 DOM 变化。

到目前为止，我们假设我们总是在 web 浏览器中创建我们的虚拟 DOM。这是可以理解的，因为毕竟 React 是一个用户界面库，所有用户界面都是在 web 浏览器中呈现的。您能想到在客户端渲染用户界面会很慢的情况吗？你们中的一些人可能已经猜到了，我说的是初始页面加载。初始页面加载的问题是我在本章开头提到的一个问题——我们不再创建静态网页了。相反，当 web 浏览器加载我们的 web 应用程序时，它只会收到通常用作我们的 web 应用程序的容器或父元素的最少 HTML 标记。然后，我们的 JavaScript 代码创建其余的 DOM，但为了这样做，它通常需要从服务器请求额外的数据。然而，获取这些数据需要时间。一旦收到这些数据，我们的 JavaScript 代码开始改变 DOM。我们知道 DOM 变化很慢。我们如何解决这个问题？

解决方案有些出乎意料。我们不是在 web 浏览器中改变 DOM，而是在服务器上改变它，就像我们在静态网页上做的那样。然后，web 浏览器将接收一个 HTML，它完全代表了我们的 web 应用程序在初始页面加载时的用户界面。听起来很简单，但我们不能在服务器上改变 DOM，因为它在 web 浏览器之外不存在。或者我们可以吗？

我们有一个只是 JavaScript 的虚拟 DOM，并且使用 Node.js，我们可以在服务器上运行 JavaScript。因此，从技术上讲，我们可以在服务器上使用 React 库，并且可以在服务器上创建我们的`ReactNode`树。问题是我们如何将其渲染为一个可以发送给客户端的字符串？

React 有一个名为`ReactDOMServer.renderToString()`的方法来做到这一点：

```jsx
import ReactDOMServer from 'react-dom/server';
ReactDOMServer.renderToString(ReactElement);
```

`ReactDOMServer.renderToString()`方法以`ReactElement`作为参数，并将其渲染为初始 HTML。这不仅比在客户端上改变 DOM 更快，而且还提高了您的 Web 应用的搜索引擎优化（SEO）。

说到生成静态网页，我们也可以用 React 来做到这一点：

```jsx
import ReactDOMServer from 'react-dom/server';
ReactDOMServer.renderToStaticMarkup(ReactElement);
```

与`ReactDOMServer.renderToString()`类似，这个方法也以`ReactElement`作为参数，并输出一个 HTML 字符串。然而，它不会创建 React 在内部使用的额外 DOM 属性，从而产生较短的 HTML 字符串，我们可以快速传输到网络。

现在你不仅知道如何使用 React 元素创建虚拟 DOM 树，还知道如何将其渲染到客户端和服务器。我们接下来的问题是是否可以快速且更直观地完成这个过程。

# 使用 JSX 创建 React 元素

当我们通过不断调用`React.createElement()`方法来构建我们的虚拟 DOM 时，将这些多个函数调用视觉上转换为 HTML 标签的层次结构变得非常困难。不要忘记，即使我们正在使用虚拟 DOM，我们仍然在为我们的内容和用户界面创建一个结构布局。通过简单地查看我们的 React 代码，能够轻松地可视化该布局，这不是很好吗？

**JSX**是一种可选的类似 HTML 的语法，允许我们创建虚拟 DOM 树，而不使用`React.createElement()`方法。

让我们来看看我们之前创建的不使用 JSX 的示例：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const listItemElement1 = React.DOM.li(
  { className: 'item-1', key: 'item-1' },
  'Item 1'
);
const listItemElement2 = React.DOM.li(
  { className: 'item-2', key: 'item-2' },
  'Item 2'
);
const listItemElement3 = React.DOM.li(
  { className: 'item-3', key: 'item-3' },
  'Item 3'
);

const reactFragment = [
  listItemElement1,
  listItemElement2,
  listItemElement3
];
const listOfItems = React.DOM.ul(
  { className: 'list-of-items' },
  reactFragment
);

ReactDOM.render(
  listOfItems,
  document.getElementById('react-application')
);
```

将此转换为 JSX：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const listOfItems = (
  <ul className="list-of-items">
    <li className="item-1">Item 1</li>
    <li className="item-2">Item 2</li>
    <li className="item-3">Item 3</li>
  </ul>
);

ReactDOM.render(
  listOfItems,
  document.getElementById('react-application')
);
```

正如你所看到的，JSX 允许我们在 JavaScript 代码中编写类似 HTML 的语法。更重要的是，我们现在可以清楚地看到我们的 HTML 布局在渲染后会是什么样子。JSX 是一个方便的工具，但它也有一个额外的转换步骤的代价。在我们的“无效”JavaScript 代码被解释之前，必须将 JSX 语法转换为有效的 JavaScript 语法。

在上一章中，我们安装了`babel-preset-react`模块，将我们的 JSX 语法转换为有效的 JavaScript。这种转换发生在我们运行 Webpack 时。转到`~/snapterest/`并运行以下命令：

```jsx
**npm start**

```

为了更好地理解 JSX 语法，我建议您使用 Babel REPL 工具进行实验：[`babeljs.io/repl/`](https://babeljs.io/repl/)——它可以将您的 JSX 语法即时转换为普通的 JavaScript。

使用 JSX，起初可能会感到非常不同寻常，但它可以成为一个非常直观和方便的工具。最好的部分是您可以选择是否使用它。我发现 JSX 可以节省我的开发时间，所以我选择在我们正在构建的项目中使用它。

如果您对我们在本章讨论的内容有疑问，那么您可以参考[`github.com/fedosejev/react-essentials`](https://github.com/fedosejev/react-essentials)并创建一个新的问题。

# 总结

我们从讨论单页面应用程序的问题以及如何解决它们开始了本章。然后，您了解了虚拟 DOM 是什么，以及 React 如何允许我们构建一个虚拟 DOM。我们还安装了 React，并且仅使用 JavaScript 创建了我们的第一个 React 元素。然后，您还学会了如何在 Web 浏览器和服务器上渲染 React 元素。最后，我们看了一种更简单的使用 JSX 创建 React 元素的方法。

在下一章中，我们将更深入地了解 React 组件的世界。
