# 第十章：改善应用程序的性能

Web 应用程序的有效性能对于提供良好的用户体验和提高转化率至关重要。React 库实现了不同的技术来快速渲染我们的组件，并尽可能少地触及**文档对象模型**（**DOM**）。对 DOM 进行更改通常是昂贵的，因此最小化操作的数量至关重要。

然而，有一些特定的情景，React 无法优化这个过程，开发人员需要实现特定的解决方案来使应用程序顺利运行。

在本章中，我们将介绍 React 的基本概念，并学习如何使用一些 API 来帮助库找到更新 DOM 的最佳路径，而不会降低用户体验。我们还将看到一些常见的错误，这些错误可能会损害我们的应用程序并使其变慢。

我们应该避免仅仅为了优化而优化我们的组件，并且重要的是只在需要时应用我们将在接下来的章节中看到的技术。

在本章中，我们将涵盖以下主题：

+   协调的工作原理以及我们如何帮助 React 使用键更好地完成工作

+   常见的优化技术和常见的与性能相关的错误

+   使用不可变数据的含义以及如何做到这一点

+   有用的工具和库，使我们的应用程序运行更快

# 技术要求

要完成本章，您将需要以下内容：

+   Node.js 12+

+   Visual Studio Code

您可以在书的 GitHub 存储库中找到本章的代码[`github.com/PacktPublishing/React-17-Design-Patterns-and-Best-Practices-Third-Edition/tree/main/Chapter10`](https://github.com/PacktPublishing/React-17-Design-Patterns-and-Best-Practices-Third-Edition/tree/main/Chapter10)。

# 协调

大多数情况下，React 默认情况下足够快，您无需做任何其他事情来提高应用程序的性能。React 利用不同的技术来优化屏幕上组件的渲染。

当 React 需要显示一个组件时，它会调用其`render`方法以及其子组件的`render`方法。组件的`render`方法返回一棵 React 元素树，React 使用它来决定更新 UI 时必须执行哪些 DOM 操作。

每当组件状态发生变化时，React 都会再次调用节点上的`render`方法，并将结果与 React 元素的先前树进行比较。该库足够聪明，可以找出在屏幕上应用期望的变化所需的最小操作集。这个过程称为**协调**，由 React 透明地管理。由于这一点，我们可以轻松地以声明方式描述我们的组件在特定时间点应该是什么样子，然后让库来处理其余部分。

React 试图在 DOM 上应用尽可能少的操作，因为触及 DOM 是一项昂贵的操作。

然而，比较两个元素树也不是免费的，React 做出了两个假设来减少其复杂性：

+   如果两个元素具有不同的类型，它们将呈现不同的树。

+   开发者可以使用键来标记子元素在不同的渲染调用中保持稳定。

第二点对开发者来说很有趣，因为它给了我们一个工具来帮助 React 更快地渲染我们的视图。

默认情况下，当返回到 DOM 节点的子元素时，React 同时迭代两个子元素列表，每当有差异时，就会创建一个变化。

让我们看一些例子。在将以下两个树之间进行转换时，在子元素末尾添加一个元素将会很好地工作：

```jsx
<ul>
 <li>Carlos</li>
  <li>Javier</li>
</ul>

<ul>
 <li>Carlos</li>
 <li>Javier</li>
 <li>Emmanuel</li>
</ul>
```

两个`<li>Carlos</li>`树与两个`<li>Javier</li>`树匹配，然后它将插入`<li>Emmanuel</li>`树。

如果实现得不够聪明，将元素插入开头会导致性能下降。如果我们看一下示例，当在这两个树之间进行转换时，它的效果非常差：

```jsx
<ul>
 <li>Carlos</li>
  <li>Javier</li>
</ul>

<ul>
  <li>Emmanuel</li>
 <li>Carlos</li>
 <li>Javier</li>
</ul>
```

每个子元素都会被 React 改变，而不是意识到它可以保持子树的连续性，`<li>Carlos</li>`和`<li>Javier</li>`。这可能会成为一个问题。当然，这个问题可以解决，解决方法就是 React 支持的`key`属性。让我们接着看。

# 键

子元素拥有键，这些键被 React 用来匹配后续树和原始树之间的子元素。通过在我们之前的示例中添加一个键，可以使树的转换更加高效：

```jsx
<ul>
 <li key="2018">Carlos</li>
  <li key="2019">Javier</li>
</ul>

<ul>
  <li key="2017">Emmanuel</li>
 <li key="2018">Carlos</li>
 <li key="2019">Javier</li>
</ul>
```

现在 React 知道`2017`键是新的，而`2018`和`2019`键只是移动了。

找到一个键并不难。您将要显示的元素可能已经有一个唯一的 ID。所以键可以直接来自您的数据：

```jsx
<li key={element.id}>{element.title}</li>
```

新的 ID 可以由您添加到您的模型中，或者密钥可以由内容的某些部分生成。密钥只需在其同级中是唯一的；它不必在全局范围内是唯一的。数组中的项目索引可以作为密钥传递，但现在被认为是一种不好的做法。然而，如果项目从未被记录，这可能效果很好。重新排序将严重影响性能。

如果您使用`map`函数渲染多个项目，并且没有指定 key 属性，您将收到此消息：警告：数组或迭代器中的每个子项都应该有一个唯一的“key”属性。

让我们在下一节中学习一些优化技术。

# 优化技术

需要注意的是，在本书中的所有示例中，我们使用的应用程序要么是使用`create-react-app`创建的，要么是从头开始创建的，但始终使用的是 React 的开发版本。

使用 React 的开发版本对编码和调试非常有用，因为它为您提供了修复各种问题所需的所有必要信息。然而，所有的检查和警告都是有成本的，我们希望在生产中避免这些成本。

因此，我们应该对我们的应用程序做的第一个优化是构建捆绑包，将`NODE_ENV`环境变量设置为`production`。这在`webpack`中非常容易，只需使用以下方式中的`DefinePlugin`：

```jsx
new webpack.DefinePlugin({ 
  'process.env': { 
    NODE_ENV: JSON.stringify('production')
  }
})
```

为了实现最佳性能，我们不仅希望使用生产标志来创建捆绑包，还希望将捆绑包拆分为一个用于我们的应用程序，一个用于`node_modules`。

为此，您需要在`webpack`中使用新的优化节点：

```jsx
optimization: {
  splitChunks: {
    cacheGroups: {
      default: false,
      commons: {
        test: /node_modules/,
        name: 'vendor',
        chunks: 'all'
      }
    }
  }
}
```

由于 webpack 4 有两种模式，*开发*和*生产*，默认情况下启用生产模式，这意味着在使用生产模式编译捆绑包时，代码将被最小化和压缩；您可以使用以下代码块指定它：

```jsx
{
  mode: process.env.NODE_ENV === 'production' ? 'production' : 
    'development',
}
```

您的`webpack.config.ts`文件应该如下所示：

```jsx
module.exports = {
  entry: './index.ts',
  optimization: {
    splitChunks: {
      cacheGroups: {
        default: false,
        commons: {
          test: /node_modules/,
          name: 'vendor',
          chunks: 'all'
        }
      }
    }
  },
  plugins: [
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: JSON.stringify('production')
      }
    })
  ],
  mode: process.env.NODE_ENV === 'production' ? 'production' : 
    'development'
}
```

有了这个 webpack 配置，我们将得到非常优化的捆绑包，一个用于我们的供应商，一个用于实际应用程序。

# 工具和库

在下一节中，我们将介绍一些技术、工具和库，我们可以应用到我们的代码库中，以监视和改进性能。

## 不可变性

新的 React Hooks，如 React.memo，使用浅比较方法来比较 props，这意味着如果我们将对象作为 prop 传递，并且我们改变了其中一个值，我们将无法获得预期的行为。

事实上，浅比较无法找到属性的变化，组件永远不会重新渲染，除非对象本身发生变化。解决此问题的一种方法是使用**不可变数据**，一旦创建，就无法改变。

例如，我们可以以以下方式设置状态：

```jsx
const [state, setState] = useState({})

const obj = state.obj

obj.foo = 'bar'

setState({ obj })
```

即使更改对象的 foo 属性的值，对象的引用仍然相同，浅比较无法识别它。

我们可以做的是每次改变对象时创建一个新实例，如下所示：

```jsx
const obj = Object.assign({}, state.obj, { foo: 'bar' })

setState({ obj })
```

在这种情况下，我们得到一个新对象，其 foo 属性设置为 bar，并且浅比较将能够找到差异。使用 ES6 和 Babel，还有另一种更优雅地表达相同概念的方法，即使用对象扩展运算符：

```jsx
const obj = { 
  ...state.obj, 
  foo: 'bar' 
}

setState({ obj })
```

这种结构比以前的更简洁，并且产生相同的结果，但在撰写时，需要对代码进行转译才能在浏览器中执行。

React 提供了一些不可变性帮助器，使得使用不可变对象变得更加容易，还有一个名为 immutable.js 的流行库，它具有更强大的功能，但需要您学习新的 API。

## Babel 插件

还有一些有趣的 Babel 插件，我们可以安装并使用它们来提高 React 应用程序的性能。它们使应用程序更快，优化了构建时的代码部分。

第一个是 React 常量元素转换器，它查找所有不根据 props 更改的静态元素，并从 render（或功能组件）中提取它们，以避免不必要地调用 _jsx。

使用 Babel 插件非常简单。我们首先使用 npm 安装它：

```jsx
npm install --save-dev @babel/plugin-transform-react-constant-elements
```

您需要创建.babelrc 文件，并添加一个 plugins 键，其值为我们要激活的插件列表的数组：

```jsx
{ 
  "plugins": ["@babel/plugin-transform-react-constant-elements"] 
}
```

第二个 Babel 插件，我们可以选择使用以提高性能的是 React 内联元素转换，它用更优化的版本替换所有 JSX 声明（或 _jsx 调用），以加快执行速度。

使用以下命令安装插件：

```jsx
npm install --save-dev @babel/plugin-transform-react-inline-elements
```

接下来，您可以轻松地将插件添加到`.babelrc`文件中插件数组中，如下所示：

```jsx
{
  "plugins": ["@babel/plugin-transform-react-inline-elements"] 
}
```

这两个插件应该只在生产环境中使用，因为它们会使在开发模式下调试变得更加困难。到目前为止，我们已经学会了许多优化技术，以及如何使用 webpack 配置一些插件。

# 总结

我们的性能优化之旅已经结束，现在我们可以优化我们的应用程序，以提供更好的用户体验。

在本章中，我们学习了协调算法的工作原理，以及 React 始终试图采用最短的路径来对 DOM 进行更改。我们还可以通过使用键来帮助库优化其工作。一旦找到了瓶颈，你可以应用本章中所见的技术之一来解决问题。

我们已经学会了如何重构和设计组件的结构，以正确的方式提供性能提升。我们的目标是拥有小的组件，以最佳方式执行单一功能。在本章末尾，我们谈到了不可变性，以及为什么重要的是不要改变数据，以使`React.memo`和`shallowCompare`发挥作用。最后，我们介绍了不同的工具和库，可以使您的应用程序更快。

在下一章中，我们将学习使用 Jest、React Testing Library 和 React DevTools 进行测试和调试。
