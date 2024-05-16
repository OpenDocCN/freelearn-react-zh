# 第九章：使用 Jest 测试您的 React 应用程序

到目前为止，你已经创建了许多 React 组件。其中一些非常简单，但有些足够复杂。建立了这两种组件后，你可能已经获得了一定的信心，让你相信无论用户界面有多复杂，你都可以用 React 构建它，而不会遇到任何重大问题。这是一个很好的信心。毕竟，这就是我们投入时间学习 React 的原因。然而，许多有信心的 React 开发人员陷入的陷阱是不写单元测试。

什么是**单元测试**？顾名思义，它是对应用程序的单个单元进行测试。应用程序中的单个单元通常是一个函数，这意味着编写单元测试意味着为您的函数编写测试。

# 为什么要写单元测试？

你可能想知道为什么要写单元测试。让我给你讲一个我个人经历的故事。我最近发布了一个我建立的网站。几天后，使用该网站的同事给我发了一封电子邮件，附带了两个网站一直拒绝的文件。我仔细检查了这些文件，确保了它们的 ID 匹配的要求都得到满足。然而，文件仍然被拒绝，并且错误消息显示 ID 不匹配。你能猜到问题是什么吗？

我写了一个函数来检查这两个文件的 ID 是否匹配。该函数检查了 ID 的值和类型，因此如果值相同但类型不同，它将返回不匹配；结果证明这正是我同事的文件的情况。

重要的问题是，我如何防止这种情况发生？答案是为我的函数编写一些单元测试。

# 创建测试套件、规范和期望

如何为 JavaScript 函数编写测试？你需要一个测试框架，幸运的是，Facebook 为 JavaScript 构建了自己的单元测试框架，称为**Jest**。它受**Jasmine**的启发，这是另一个著名的 JavaScript 测试框架。熟悉 Jasmine 的人会发现 Jest 的测试方法非常相似。然而，我不会假设你之前有测试框架的经验，首先讨论基础知识。

单元测试的基本思想是，你只测试应用程序中的一个功能片段，通常由一个函数实现。你在隔离环境中测试它，这意味着函数依赖的应用程序的其他部分不会被测试使用。相反，它们会被测试模拟。模拟 JavaScript 对象是创建一个模拟真实对象行为的虚假对象。在单元测试中，虚假对象称为**mock**，创建它的过程称为**mocking**。

当运行测试时，Jest 会自动模拟依赖项。它会自动找到要在存储库中执行的测试。让我们看下面的例子。

首先，在`~/snapterest/source/utils/`目录中创建一个新的`TweetUtils.js`文件：

```jsx
function getListOfTweetIds(tweets) {
  return Object.keys(tweets);
}

export default { getListOfTweetIds };
```

`TweetUtils.js`文件是一个模块，包含我们的应用程序使用的`getListOfTweetIds()`实用函数。给定一个带有推文的对象，`getListOfTweetIds()`返回一个推文 ID 数组。

现在让我们用 Jest 编写我们的第一个单元测试。我们将测试我们的`getListOfTweetIds()`函数。

在`~/snapterest/source/utils/`目录中创建一个`TweetUtils.test.js`文件：

```jsx
import TweetUtils from './TweetUtils';

describe('TweetUtils', () => {
  test('getListOfTweetIds returns an array of tweet ids', () => {
    const tweetsMock = {
      tweet1: {},
      tweet2: {},
      tweet3: {}
    };
    const expectedListOfTweetIds = [
      'tweet1',
      'tweet2',
      'tweet3'
    ];
    const actualListOfTweetIds = TweetUtils.getListOfTweetIds(
      tweetsMock
    );

    expect(actualListOfTweetIds)
      .toEqual(expectedListOfTweetIds);
  });
});
```

首先，我们需要引入`TweetUtils`模块：

```jsx
import TweetUtils from './TweetUtils';
```

接下来，我们调用全局的`describe()` Jest 函数。理解其背后的概念很重要。在我们的`TweetUtils.test.js`文件中，我们不只是创建一个单一的测试，而是创建了一组测试。一组测试是对一个更大的功能单元进行集体测试的集合。例如，一组测试可以包含多个测试，测试更大模块的所有单独部分。在我们的示例中，我们有一个`TweetUtils`模块，可能有多个实用函数。在这种情况下，我们会为`TweetUtils`模块创建一组测试，然后为每个单独的实用函数创建测试，比如`getListOfTweetIds()`。

`describe()`函数定义了一个测试套件，并接受这两个参数：

+   **套件名称**：这是描述此测试套件正在测试的标题

+   **套件实现**：这是实现此套件的函数

在我们的示例中，套件如下：

```jsx
describe('TweetUtils', () => {
  // Test suite implementation goes here
});
```

如何创建单独的测试？在 Jest 中，通过调用另一个全局的 Jest 函数`test()`来创建单独的测试。就像`describe()`一样，`test()`函数接受两个参数：

+   **测试名称**：这是描述此测试正在测试的标题，例如：`'getListOfTweetIds 返回推文 ID 数组'`

+   **测试实现**：这是实现此测试的函数

在我们的示例中，测试如下：

```jsx
test('getListOfTweetIds returns an array of tweet ids', () => {
  // Test implementation goes here... });
```

让我们更仔细地看一下我们测试的实现：

```jsx
const tweetsMock = {
  tweet1: {},
  tweet2: {},
  tweet3: {}
};
const expectedListOfTweetIds = [
  'tweet1',
  'tweet2',
  'tweet3'
];
const actualListOfTweetIds = TweetUtils.getListOfTweetIds(
  tweetsMock
);

expect(actualListOfTweetIds)
  .toEqual(expectedListOfTweetIds);
```

我们测试`TweetUtils`模块的`getListOfTweetIds()`方法是否在给定带有推文对象的对象时返回推文 ID 数组。

首先，我们将创建一个模拟真实推文对象的模拟对象：

```jsx
const tweetsMock = {
  tweet1: {},
  tweet2: {},
  tweet3: {}
};
```

这个模拟对象的唯一要求是将推文 ID 作为对象键。值并不重要，所以我们选择空对象。键名也不重要，所以我们选择将它们命名为`tweet1`、`tweet2`和`tweet3`。这个模拟对象并不能完全模拟真实的推文对象——它的唯一目的是模拟其键是推文 ID 的事实。

下一步是创建预期的推文 ID 列表：

```jsx
const expectedListOfTweetIds = [
  'tweet1',
  'tweet2',
  'tweet3'
];
```

我们知道要期望什么推文 ID，因为我们用相同的 ID 模拟了推文对象。

下一步是从我们模拟的推文对象中提取实际的推文 ID。为此，我们使用`getListOfTweetIds()`方法，该方法接受推文对象并返回推文 ID 数组：

```jsx
const actualListOfTweetIds = TweetUtils.getListOfTweetIds(
  tweetsMock
);
```

我们将`tweetsMock`对象传递给该方法，并将结果存储在`actualListOfTweetIds`常量中。它被命名为`actualListOfTweetIds`的原因是这个推文 ID 列表是由我们正在测试的`getListOfTweetIds()`函数产生的。

最后一步将向我们介绍一个新的重要概念：

```jsx
expect(actualListOfTweetIds)
  .toEqual(expectedListOfTweetIds);
```

让我们思考一下测试的过程。我们需要取得一个由我们正在测试的方法产生的实际值，即`getListOfTweetIds()`，并将其与我们预先知道的预期值进行匹配。匹配的结果将决定我们的测试是否通过或失败。

我们之所以能预先猜测`getListOfTweetIds()`将会返回什么是因为我们已经为它准备了输入；这就是我们的模拟对象：

```jsx
const tweetsMock = {
  tweet1: {},
  tweet2: {},
  tweet3: {}
};
```

因此，我们可以通过调用`TweetUtils.getListOfTweetIds(tweetsMock)`来期望以下输出：

```jsx
[ 'tweet1', 'tweet2', 'tweet3' ]
```

因为在`getListOfTweetIds()`内部可能出现问题，我们无法保证这个结果；我们只能*期望*它。

这就是为什么我们需要创建一个期望。在 Jest 中，**期望**是使用`expect()`函数构建的，该函数接受一个实际值；例如，`actualListOfTweetIds`对象：`expect(actualListOfTweetIds)`。

然后，我们将它与一个**匹配器**函数链接起来，该函数比较实际值与期望值，并告诉 Jest 期望是否得到满足：

```jsx
expect(actualListOfTweetIds)
  .toEqual(expectedListOfTweetIds);
```

在我们的示例中，我们使用`toEqual()`匹配器函数来比较两个数组。您可以在 Jest 的[`facebook.github.io/jest/docs/expect.html`](https://facebook.github.io/jest/docs/expect.html)中找到所有内置匹配器函数的列表

这就是你编写测试的方式。一个测试包含一个或多个期望。每个期望测试您代码的状态。一个测试可以是**通过的测试**或**失败的测试**。只有当所有期望都得到满足时，测试才是通过的测试；否则，它就是失败的测试。

干得好，您已经编写了您的第一个测试套件，其中包含一个期望的单个测试！您如何运行它？

# 安装和运行 Jest

首先，让我们安装**Jest 命令行界面**（**Jest CLI**）模块：

```jsx
**npm install --save-dev jest**

```

这个命令会将 Jest 模块安装并添加为`~/snapterest/package.json`文件的开发依赖项。

在第二章中，*为您的项目安装强大的工具*，我们安装并讨论了 Babel。我们使用 Babel 将我们的新 JavaScript 语法转译为旧的 JavaScript 语法，并将 JSX 语法编译为普通的 JavaScript 语法。在我们的测试中，我们将测试用 JSX 语法编写的 React 组件，但是 Jest 默认不理解 JSX 语法。我们需要告诉 Jest 自动使用 Babel 编译我们的测试。为此，我们需要安装`babel-jest`模块：

```jsx
**npm install --save-dev babel-jest**

```

现在我们需要配置 Babel。为此，在`~/snapterest/`目录中创建以下`.babelrc`文件：

```jsx
{
  "presets": ["es2015", "react"]
```

接下来，让我们编辑`package.json`文件。我们将替换现有的`"scripts"`对象：

```jsx
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1"
},
```

用以下对象替换前面的对象：

```jsx
"scripts": {
  "test": "jest"
},
```

现在我们准备运行我们的测试套件。转到`~/snapterest/`目录，并运行以下命令：

```jsx
**npm test**

```

您应该在终端窗口中看到以下消息：

```jsx
**PASS  source/utils/TweetUtils.test.js**

```

此输出消息告诉您以下内容：

+   `PASS`：您的测试已通过

+   `source/utils/TweetUtils.test.js`：Jest 从这个文件运行测试

这就是编写和测试一个微小单元测试所需的全部。现在，让我们创建另一个！

# 创建多个测试和期望

这一次，我们将创建并测试集合实用程序模块。在`~/snapterest/source/utils/`目录中创建`CollectionUtils.js`文件：

```jsx
import TweetUtils from './TweetUtils';

function getNumberOfTweetsInCollection(collection) {
  const listOfCollectionTweetIds = TweetUtils
    .getListOfTweetIds(collection);

  return listOfCollectionTweetIds.length;
}

function isEmptyCollection(collection) {
  return getNumberOfTweetsInCollection(collection) === 0;
}

export default {
  getNumberOfTweetsInCollection,
  isEmptyCollection
};
```

`CollectionUtils`模块有两个函数：`getNumberOfTweetsInCollection()`和`isEmptyCollection()`。

首先，让我们讨论`getNumberOfTweetsInCollection()`：

```jsx
function getNumberOfTweetsInCollection(collection) {
  const listOfCollectionTweetIds = TweetUtils
    .getListOfTweetIds(collection);

  return listOfCollectionTweetIds.length;
}
```

正如你所看到的，这个函数调用`TweetUtils`模块的`getListOfTweetIds()`方法，并将`collection`对象作为参数传递。`getListOfTweetIds()`返回的结果存储在`listOfCollectionTweetIds`常量中，由于它是一个数组，`getNumberOfTweetsInCollection()`返回该数组的`length`属性。

现在，让我们来看一下`isEmptyCollection()`方法：

```jsx
function isEmptyCollection(collection) {
  return getNumberOfTweetsInCollection(collection) === 0;
}
```

这个方法重用了我们刚刚讨论的`getNumberOfTweetsInCollection()`方法。它检查调用`getNumberOfTweetsInCollection()`返回的结果是否等于零。然后，它返回该检查的结果，即`true`或`false`。

请注意，我们从这个模块导出了这两个方法：

```jsx
export default {
  getNumberOfTweetsInCollection,
  isEmptyCollection
};
```

我们刚刚创建了我们的`CollectionUtils`模块。我们的下一个任务是测试它。

在`~/snapterest/source/utils/`目录中，创建以下`CollectionUtils.test.js`文件：

```jsx
import CollectionUtils from './CollectionUtils';

describe('CollectionUtils', () => {
  const collectionTweetsMock = {
    collectionTweet7: {},
    collectionTweet8: {},
    collectionTweet9: {}
  };

  test('getNumberOfTweetsInCollection returns a number of tweets in collection', () => {
    const actualNumberOfTweetsInCollection = CollectionUtils
    .getNumberOfTweetsInCollection(collectionTweetsMock);
    const expectedNumberOfTweetsInCollection = 3;

    expect(actualNumberOfTweetsInCollection)
    .toBe(expectedNumberOfTweetsInCollection);
    });

  test('isEmptyCollection checks if collection is not empty', () => {
    const actualIsEmptyCollectionValue = CollectionUtils
      .isEmptyCollection(collectionTweetsMock);

    expect(actualIsEmptyCollectionValue).toBeDefined();
    expect(actualIsEmptyCollectionValue).toBe(false);
    expect(actualIsEmptyCollectionValue).not.toBe(true);
  });
});
```

首先我们定义我们的测试套件：

```jsx
describe('CollectionUtils', () => {
  const collectionTweetsMock = {
    collectionTweet7: {},
    collectionTweet8: {},
    collectionTweet9: {}
  };

// Tests go here... });
```

我们给我们的测试套件命名为我们正在测试的模块的名称—`CollectionUtils`。现在让我们来看一下这个测试套件的实现。与我们之前的测试套件不同，我们不是立即定义测试规范，而是创建了`collectionTweetsMock`对象。那么，我们允许这样做吗？当然可以。测试套件实现函数只是另一个 JavaScript 函数，在定义测试规范之前我们可以做一些工作。

这个测试套件将实现多个测试。我们所有的测试都将使用`collectionTweetsMock`对象，所以在规范范围之外定义它并在规范内重用它是有意义的。你可能已经猜到，`collectionTweetsMock`对象模拟了一组推文。

现在让我们实现单独的测试规范。

我们的第一个规范测试了`CollectionUtils`模块是否返回了集合中的推文数量：

```jsx
test('getNumberOfTweetsInCollection returns a numberof tweets in collection', () => {
  const actualNumberOfTweetsInCollection = CollectionUtils
    .getNumberOfTweetsInCollection(collectionTweetsMock);
  const expectedNumberOfTweetsInCollection = 3;

  expect(actualNumberOfTweetsInCollection)
    .toBe(expectedNumberOfTweetsInCollection);
});
```

我们首先获取我们模拟集合中的实际推文数量：

```jsx
const actualNumberOfTweetsInCollection = CollectionUtils
  .getNumberOfTweetsInCollection(collectionTweetsMock);
```

为此，我们调用`getNumberOfTweetsInCollection()`方法，并将`collectionTweetsMock`对象传递给它。然后，我们定义我们模拟集合中期望的推文数量：

```jsx
const expectedNumberOfTweetsInCollection = 3;
```

最后，我们调用`expect()`全局函数来创建一个期望：

```jsx
expect(actualNumberOfTweetsInCollection)
  .toBe(expectedNumberOfTweetsInCollection);
```

我们使用`toBe()`匹配器函数来匹配实际值和期望值。

如果你现在运行`npm test`命令，你会看到两个测试套件都通过了：

```jsx
**PASS  source/utils/CollectionUtils.test.js**
**PASS  source/utils/TweetUtils.test.js**

```

请记住，要使测试套件通过，它必须只有通过的规范。要使规范通过，它必须满足所有的期望。到目前为止情况就是这样。

怎么样进行一个小小的邪恶实验？

打开你的`~/snapterest/source/utils/CollectionUtils.js`文件，并在`getNumberOfTweetsInCollection()`函数内，找到以下代码行：

```jsx
return listOfCollectionTweetIds.length;
```

现在将其更改为这样：

```jsx
return listOfCollectionTweetIds.length + 1;
```

这个微小的更新将返回任何给定集合中错误的推文数量。现在再次运行`npm test`。你应该看到`CollectionUtils.test.js`中的所有规范都失败了。这是我们感兴趣的一个：

```jsx
**FAIL  source/utils/CollectionUtils.test.js**
 **CollectionUtils › getNumberOfTweetsInCollection returns a number of tweets in collection**

 **expect(received).toBe(expected)**

 **Expected value to be (using ===):**
 **3**
 **Received:**
 **4**

 **at Object.<anonymous> (source/utils/CollectionUtils.test.js:14:46)**

```

我们以前没有看到过失败的测试，所以让我们仔细看看它试图告诉我们什么。

首先，它告诉我们`CollectionUtils.test.js`测试失败了：

```jsx
**FAIL  source/utils/CollectionUtils.test.js**

```

然后，以一种人性化的方式告诉我们哪个测试失败了：

```jsx
 **CollectionUtils › getNumberOfTweetsInCollection returns a number of tweets in collection**

```

然后，出了什么问题-意外的测试结果：

```jsx
**expect(received).toBe(expected)** 
 **Expected value to be (using ===):**
 **3**
 **Received:**
 **4**

```

最后，Jest 打印出一个堆栈跟踪，这应该给我们足够的技术细节，快速确定我们的代码的哪一部分产生了意外的结果：

```jsx
**at Object.<anonymous> (source/utils/CollectionUtils.test.js:14:46)**

```

好了！不要再故意让我们的测试失败了。让我们把`~/snapterest/source/utils/CollectionUtils.js`文件恢复到这个状态：

```jsx
return listOfCollectionTweetIds.length;
```

在 Jest 中，一个测试套件可以有许多规范，测试来自单个模块的不同方法。我们的`CollectionUtils`模块有两种方法。现在让我们讨论第二种方法。

我们在`CollectionUtils.test.js`中的下一个规范检查集合是否不为空：

```jsx
test('isEmptyCollection checks if collection is not empty', () => {
  const actualIsEmptyCollectionValue = CollectionUtils
    .isEmptyCollection(collectionTweetsMock);

  expect(actualIsEmptyCollectionValue).toBeDefined();
  expect(actualIsEmptyCollectionValue).toBe(false);
  expect(actualIsEmptyCollectionValue).not.toBe(true);
});
```

首先，我们调用`isEmptyCollection()`方法，并将`collectionTweetsMock`对象传递给它。我们将结果存储在`actualIsEmptyCollectionValue`常量中。注意我们如何重复使用相同的`collectionTweetsMock`对象，就像在我们之前的规范中一样。

接下来，我们创建了不止一个期望：

```jsx
expect(actualIsEmptyCollectionValue).toBeDefined();
expect(actualIsEmptyCollectionValue).toBe(false);
expect(actualIsEmptyCollectionValue).not.toBe(true);
```

你可能已经猜到我们对`actualIsEmptyCollectionValue`常量的期望。

首先，我们期望我们的集合被定义：

```jsx
expect(actualIsEmptyCollectionValue).toBeDefined();
```

这意味着`isEmptyCollection()`函数必须返回除`undefined`之外的其他东西。

接下来，我们期望它的值是`false`：

```jsx
expect(actualIsEmptyCollectionValue).toBe(false);
```

早些时候，我们使用`toEqual()`匹配器函数来比较数组。`toEqual()`方法进行深度比较，非常适合比较数组，但对于`false`等原始值来说有些过度。

最后，我们期望`actualIsEmptyCollectionValue`不是`true`：

```jsx
expect(actualIsEmptyCollectionValue).not.toBe(true);
```

下一个比较是通过`.not`进行反转的。它将期望与`toBe(true)`的相反值`false`进行匹配。

注意`toBe(false)`和`not.toBe(true)`产生相同的结果。

只有当所有三个期望都得到满足时，这个规范才会通过。

到目前为止，我们已经测试了实用模块，但是如何使用 Jest 测试 React 组件呢？

我们接下来会发现。

# 测试 React 组件

让我们暂时停下来不写代码，谈谈测试用户界面意味着什么。我们究竟在测试什么？我们测试的是我们的用户界面是否按预期呈现。换句话说，如果我们告诉 React 去呈现一个按钮，我们期望它呈现一个按钮，不多，也不少。

现在我们如何检查这一点呢？做到这一点的一种方法是编写一个 React 组件，捆绑我们的应用程序，在 Web 浏览器中运行它，并亲眼看到它显示我们想要显示的内容。这是手动测试，我们至少要做一次。但是这在长期内是耗时且不可靠的。

我们如何自动化这个过程呢？Jest 可以为我们做大部分工作，但是 Jest 没有自己的眼睛，所以它至少需要借用我们的眼睛来测试每个组件一次。如果 Jest“看不到”呈现 React 组件的结果，那么它如何甚至测试 React 组件呢？

在第三章中，*创建您的第一个 React 元素*，我们讨论了 React 元素。它们是描述我们想在屏幕上看到的内容的简单的 JavaScript 对象。

例如，考虑这个 HTML 标记：

```jsx
<h1>Testing</h1>
```

这可以用以下简单的 JavaScript 对象表示：

```jsx
{
  type: 'h1',
  children: 'Testing'
}
```

当我们呈现组件时，拥有代表我们组件产生的输出的简单的 JavaScript 对象，使我们能够描述关于我们组件及其行为的某些期望。让我们看看它的实际效果。

我们将测试的第一个 React 组件将是我们的`Header`组件。在`~/snapterest/source/components/`目录中创建`Header.test.js`文件：

```jsx
import React from 'react';
import renderer from 'react-test-renderer';
import Header, { DEFAULT_HEADER_TEXT } from './Header';

describe('Header', () => {
  test('renders default header text', () => {
    const component = renderer.create(
      <Header/>
    );

    const tree = component.toJSON();
    const firstChild = tree.children[0];

    expect(firstChild).toBe(DEFAULT_HEADER_TEXT);
  });

  test('renders provided header text', () => {
    const headerText = 'Testing';

    const component = renderer.create(
      <Header text={headerText} />
    );

    const tree = component.toJSON();
    const firstChild = tree.children[0];

    expect(firstChild).toBe(headerText);
  });
});
```

到目前为止，您可以认识到我们测试文件的结构。首先，我们定义了测试套件，并给它命名为`Header`。我们的测试套件有两个测试规范，分别命名为`renders default header text`和`renders provided header text`。正如它们的名称所示，它们测试我们的`Header`组件能够呈现默认文本和提供的文本。让我们更仔细地看看这个测试套件。

首先，我们导入 React 模块：

```jsx
import React from 'react';
```

然后，我们导入`react-test-renderer`模块：

```jsx
import renderer from 'react-test-renderer';
```

React 渲染器将 React 组件渲染为纯 JavaScript 对象。它不需要 DOM，因此我们可以使用它在 web 浏览器之外渲染 React 组件。它与 Jest 配合使用效果很好。让我们安装它：

```jsx
**npm install --save-dev react-test-renderer**

```

接下来，为了测试我们的`Header`组件，我们需要导入它：

```jsx
import Header, { DEFAULT_HEADER_TEXT } from './Header';
```

我们还从我们的`Header`模块中导入`DEFAULT_HEADER_TEXT`。我们这样做是因为我们不想硬编码实际的字符串值，即默认的标题文本。这会增加维护这个值的额外工作。相反，由于我们的`Header`组件知道这个值是什么，我们将在测试中导入并重用它。

让我们来看看我们的第一个名为`renders default header text`的测试。我们在这个测试中的第一个任务是将`Header`组件渲染为普通的 JavaScript 对象。`react-test-renderer`模块有一个`create`方法可以做到这一点：

```jsx
const component = renderer.create(
  <Header/>
);
```

我们将`<Header/>`元素作为参数传递给`create()`函数，然后我们得到一个代表我们的`Header`组件实例的 JavaScript 对象。它还不是我们组件的简单表示，所以我们的下一步是使用`toJSON`方法将该对象转换为我们组件的简单树形表示：

```jsx
const tree = component.toJSON();
```

现在，`tree`也是一个 JavaScript 对象，但它也是我们`Header`组件的简单表示，我们可以轻松阅读和理解：

```jsx
{ type: 'h2', props: {}, children: [ 'Default header' ] }
```

我建议你记录`component`和`tree`对象，并看看它们有多不同：

```jsx
console.log(component);
console.log(tree);
```

你会很快发现`component`对象是为了 React 的内部使用而设计的-很难阅读并且难以判断它代表什么。另一方面，`tree`对象非常容易阅读，并且清楚它代表什么。

正如你所看到的，我们目前测试 React 组件的方法是将`<Header/>`转换为`{ type: 'h2', props: {}, children: [ 'Default header' ] }`。现在我们有了一个简单的 JavaScript 对象来代表我们的组件，我们可以检查这个对象是否具有预期的值。如果是，我们可以得出结论，我们的组件将如预期般在 web 浏览器中渲染。如果不是，那么我们可能引入了一个 bug。

当我们渲染我们的`Header`组件没有任何属性时，`<Header/>`，我们期望它渲染出一个默认文本：`'Default header'`。为了检查这是否确实如此，我们需要从我们`Header`组件的树形表示中访问`children`属性：

```jsx
const firstChild = tree.children[0];
```

我们期望我们的`Header`组件只有一个子元素，所以文本元素将是第一个子元素。

现在是时候写我们的期望了：

```jsx
expect(firstChild).toBe(DEFAULT_HEADER_TEXT);
```

在这里，我们期望`firstChild`具有与`DEFAULT_HEADER_TEXT`相同的值。在幕后，`toBe`匹配器使用`===`进行比较。

这就是我们的第一个测试！

在我们名为“渲染提供的标题文本”的第二个测试中，我们正在测试我们的`Header`组件是否具有我们通过`text`属性提供的自定义测试：

```jsx
test('renders provided header text', () => {
  const headerText = 'Testing';

  const component = renderer.create(
    <Header text={headerText}/>
  );

  const tree = component.toJSON();
  const firstChild = tree.children[0];

  expect(firstChild).toBe(headerText);
});
```

现在您理解了测试 React 组件的核心思想：

1.  将您的组件呈现为 JavaScript 对象表示。

1.  在该对象上找到一些值，并检查该值是否符合您的期望。

如您所见，当您的组件很简单时，这是非常直接的。但是，如果您需要测试由其他组件组成的组件等等，会怎样呢？想象一下代表该组件的 JavaScript 对象将会有多复杂。它将具有许多深度嵌套的属性。您可能最终会编写和维护大量用于访问和比较深度嵌套值的代码。这就是写单元测试变得太昂贵的时候，一些开发人员可能选择放弃对其组件进行测试的原因。

幸运的是，我们有两种解决方案可供选择。

以下是其中之一。记住，当直接遍历和修改 DOM 太麻烦时，jQuery 库被创建出来简化这个过程？嗯，对于 React 组件，我们有 Enzyme——这是来自 AirBnB 的 JavaScript 测试实用库，简化了遍历和操作渲染 React 组件产生的输出的过程。

Enzyme 是 Jest 之外的一个独立库。让我们安装它：

```jsx
**npm install --save-dev enzyme jest-enzyme react-addons-test-utils**

```

要与 Jest 一起使用 Enzyme，我们需要安装三个模块。记住，Jest 运行我们的测试，而 Enzyme 将帮助我们编写我们的期望。

现在让我们使用 Enzyme 重写我们的`Header`组件的测试：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import Header, { DEFAULT_HEADER_TEXT } from './Header';

describe('Header', () => {
  test('renders default header text', () => {
    const wrapper = shallow(
      <Header/>
    );

    expect(wrapper.find('h2')).toHaveLength(1);
    expect(wrapper.contains(DEFAULT_HEADER_TEXT)).toBe(true);
  });

  test('renders provided header text', () => {
    const headerText = 'Testing';

    const wrapper = shallow(
      <Header text={headerText} />
    );

    expect(wrapper.find('h2')).toHaveLength(1);
    expect(wrapper.contains(headerText)).toBe(true);
  });
});
```

首先，我们从`enzyme`模块中导入`shallow`函数：

```jsx
import { shallow } from 'enzyme';
```

然后，在我们的测试中，我们调用`shallow`函数并将我们的`Header`组件作为参数传递：

```jsx
const wrapper = shallow(
  <Header/>
);
```

我们得到的是一个包装渲染我们的`Header`组件结果的对象。这个对象是由 Enzyme 的`ShallowWrapper`类创建的，并且对我们来说有一些非常有用的方法。我们将其称为`wrapper`。

现在我们有了这个`wrapper`对象可供我们使用，我们准备写我们的期望。请注意，与`react-test-renderer`不同，使用 Enzyme 时我们不需要将`wrapper`对象转换为我们组件的简化表示。这是因为我们不会直接遍历我们的`wrapper`对象——它不是一个简单的对象，很难让我们阅读；尝试记录该对象并亲自看看。相反，我们将使用 Enzyme 的`ShallowWrapper` API 提供的方法。

让我们写我们的第一个期望：

```jsx
expect(wrapper.find('h2')).toHaveLength(1);
```

正如您所看到的，我们在`wrapper`对象上调用了`find`方法。这就是 Enzyme 的强大之处。我们不需要直接遍历我们的 React 组件输出对象并找到嵌套的元素，我们只需调用`find`方法并告诉它我们要找什么。在这个例子中，我们告诉 Enzyme 在`wrapper`对象内查找所有的`h2`元素，因为它包裹了我们的`Header`组件的输出，我们期望`wrapper`对象有一个`h2`元素。我们使用 Jest 的`toHaveLength`匹配器来检查这一点。

这是我们的第二个期望：

```jsx
**expect(wrapper.contains(DEFAULT_HEADER_TEXT)).toBe(true);**

```

您可以猜到，我们正在检查我们的 wrapper 对象是否包含`DEFAULT_HEADER_TEXT`。这个检查让我们得出结论，当我们没有提供任何自定义文本时，我们的`Header`组件呈现默认文本。我们使用 Enzyme 的`contains`方法，方便地检查我们的组件是否包含任何节点。在这种情况下，我们正在检查文本节点。

Enzyme 的 API 提供了更多方法，方便我们检查组件的输出。我建议您通过阅读官方文档熟悉这些方法：[`airbnb.io/enzyme/docs/api/shallow.html`](http://airbnb.io/enzyme/docs/api/shallow.html)

您可能想知道如何测试您的 React 组件的行为。

这是我们接下来要讨论的内容！

在`~/snapterest/source/components/`目录中创建`Button.test.js`文件：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import Button from './Button';

describe('Button', () => {
  test('calls click handler function on click', () => {
    const handleClickMock = jest.fn();

    const wrapper = shallow(
      <Button handleClick={handleClickMock}/>
    );

    wrapper.find('button').simulate('click');

    expect(handleClickMock.mock.calls.length).toBe(1);
  });
});
```

`Button.test.js`文件将测试我们的`Button`组件，特别是检查当您点击它时是否触发点击事件处理程序函数。话不多说，让我们专注于`'calls click handler function on click'`规范的实现：

```jsx
const handleClickMock = jest.fn();

const wrapper = shallow(
  <Button handleClick={handleClickMock} />
);

wrapper.find('button').simulate('click');

expect(handleClickMock.mock.calls.length).toBe(1);
```

在这个规范中，我们正在测试我们的`Button`组件是否调用我们通过`handleClick`属性提供的函数。这是我们的测试策略：

1.  生成一个模拟函数。

1.  使用我们的模拟函数渲染`Button`组件。

1.  在由 Enzyme 创建的包装对象中找到`button`元素，这是渲染我们的`Button`组件的结果。

1.  在`button`元素上模拟点击事件。

1.  检查我们的模拟函数是否确实被调用了一次。

现在我们有了一个计划，让我们实施它。让我们首先创建一个模拟函数：

```jsx
const handleClickMock = jest.fn();
```

`jest.fn()`函数调用返回新生成的 Jest 模拟函数；我们将其命名为`handleClickMock`。

接下来，我们通过调用 Enzyme 的`shallow`函数来获取我们的`Button`组件的输出：

```jsx
const wrapper = shallow(
  <Button handleClick={handleClickMock}/>
);
```

我们将我们的`handleClickMock`函数作为一个属性传递给我们的`Button`组件。

然后，我们找到`button`元素并在其上模拟点击事件：

```jsx
wrapper.find('button').simulate('click');
```

在这一点上，我们期望我们的按钮元素调用它的`onClick`事件处理程序，这种情况下是我们的`handleClickMock`函数。这个模拟函数应该记录它被调用了一次，或者至少这是我们期望我们的`Button`组件的行为。让我们创建这个期望：

```jsx
expect(handleClickMock.mock.calls.length).toBe(1);
```

我们如何检查我们的`handleClickMock`函数被调用了多少次？我们的`handleClickMock`函数有一个特殊的模拟属性，我们可以检查它来找出`handleClickMock`被调用了多少次：

```jsx
handleClickMock.mock.calls.length
```

反过来，我们的`mock`对象有一个`calls`对象，它知道每次调用我们的`handleClickMock`函数的所有信息。`calls`对象是一个数组，在我们的情况下，我们期望它的`length`属性等于 1。

正如你所看到的，使用 Enzyme 更容易编写期望。我们的测试需要更少的工作来编写它们，并且长期维护它们。这很好，因为现在我们有更多的动力来编写更多的测试。

但是我们能让使用 Jest 编写测试变得更容易吗？

原来我们可以。

现在我们将一个 React 组件渲染为一个对象表示，然后使用 Jest 或 Enzyme 的帮助来检查该对象。这种检查要求我们作为开发人员编写额外的代码来使我们的测试工作。我们如何避免这种情况？

我们可以将一个 React 组件渲染为一个文本字符串，这样我们可以轻松地阅读和理解。然后我们可以将这个文本表示存储在我们的代码库中。稍后，当我们再次运行我们的测试时，我们可以简单地创建一个新的文本表示并将其与我们存储的进行比较。如果它们不同，那么这可能意味着我们有意更新了我们的组件，现在我们需要更新我们的文本表示，或者我们向我们的组件引入了一个错误，以至于它现在产生了一个意外的文本表示。

这个想法在 Jest 中被称为**快照测试**。让我们使用快照测试重写我们的`Header`组件的测试。用这段新代码替换你的`Header.test.js`文件中的现有代码：

```jsx
import React from 'react';
import renderer from 'react-test-renderer';
import Header from './Header';

describe('Header', () => {
  test('renders default header text', () => {
    const component = renderer.create(
      <Header/>
    );

    const tree = component.toJSON();

    expect(tree).toMatchSnapshot();
  });

  test('renders provided header text', () => {
    const headerText = 'Testing';

    const component = renderer.create(
      <Header text={headerText} />
    );

    const tree = component.toJSON();

    expect(tree).toMatchSnapshot();
  });
});
```

正如你所看到的，我们在这种情况下没有使用 Enzyme，这对我们来说应该是有意义的，因为我们不再想要检查任何东西。

另一方面，我们再次使用`react-test-renderer`模块来渲染和转换我们的组件为一个名为`tree`的简单 JavaScript 对象：

```jsx
const component = renderer.create(
  <Header/>
);

const tree = component.toJSON();
```

将快照测试付诸实践的关键代码行是这一行：

```jsx
expect(tree).toMatchSnapshot();
```

我们只是告诉 Jest 我们期望我们的`tree`对象与现有的快照匹配。等一下，但我们没有现有的快照。很好的观察！那么在这种情况下会发生什么？Jest 找不到这个测试的现有快照，而是会为这个测试创建一个第一个快照。

让我们运行我们的测试命令：

```jsx
**npm test**

```

所有测试都应该通过，你应该看到这个输出：

```jsx
**Snapshot Summary**
 **› 2 snapshots written in 1 test suite.**

```

在这里，Jest 告诉我们它创建了两个快照——一个用于我们`Header.test.js`测试套件中找到的每个测试。Jest 把这两个快照存储在哪里？如果你检查`~/snapterest/source/components/`目录，你会发现一个新的文件夹：`__snapshots__`。在里面，你会找到`Header.test.js.snap`文件。打开这个文件并查看它的内容：

```jsx
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`Header renders default header text 1`] = `
<h2
  style={
    Object {
      "display": "inline-block",
      "fontSize": "16px",
      "fontWeight": "300",
      "margin": "20px 10px",
    }
  }
>
  Default header
</h2>
`;

exports[`Header renders provided header text 1`] = `
<h2
  style={
    Object {
      "display": "inline-block",
      "fontSize": "16px",
      "fontWeight": "300",
      "margin": "20px 10px",
    }
  }
>
  Testing
</h2>
`;
```

在这个文件中，你可以看到我们的`Header`组件在使用 Jest 渲染时产生的输出的文本表示。我们很容易读取这个文件并确认这就是我们期望`Header`组件渲染的内容。现在我们的`Header`组件有了自己的快照。将这些快照视为源代码的一部分进行处理和存储是很重要的。

如果你有 Git 仓库，你应该提交它们，并且你应该注意你对它们所做的任何更改。

既然你已经看到了三种不同的编写 React 测试的方式，你需要自己选择如何测试你的 React 组件。现在我建议你使用快照测试和 Enzyme。

太好了，我们已经编写了四个测试套件。现在是时候运行我们所有的测试了。

导航到`~/snapterest/`并运行这个命令：

```jsx
**npm test**

```

你所有的测试套件都应该`通过`。

```jsx
**PASS  source/components/Button.test.js** 
**PASS  source/components/Header.test.js** 
**PASS  source/utils/CollectionUtils.test.js** 
**PASS  source/utils/TweetUtils.test.js** 

**Snapshot Summary**
 **› 2 snapshots written in 1 test suite.** 

**Test Suites: 4 passed, 4 total** 
**Tests:       6 passed, 6 total** 
**Snapshots:   2 added, 2 total** 
**Time:        2.461s** 
**Ran all test suites.**

```

这样的日志消息会帮助你晚上睡得安稳，放假时也不需要不断检查工作邮件。

干得好！

# 总结

现在你知道如何创建 React 组件并对其进行单元测试了。

在本章中，您学习了 Jest 的基本知识——这是 Facebook 推出的一个与 React 配合良好的单元测试框架。您了解了 Enzyme 库，并学会了如何简化编写 React 组件的单元测试。我们讨论了测试套件、规范、期望和匹配器。我们创建了模拟和模拟点击事件。

在下一章中，您将学习 Flux 架构的基本知识，以及如何提高我们的 React 应用程序的可维护性。
