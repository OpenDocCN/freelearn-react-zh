# 第一章：使用 React

在本章中，将涵盖以下示例：

+   介绍

+   在 React 中使用最新的 JS 功能

+   React 中有什么新功能？

+   在 Windows 上使用 React

# 介绍

React 是一个由 Facebook 制作的 JavaScript 库（MIT 许可证），用于创建交互式 UI。它用于创建动态和可重用的组件。React 最强大的地方在于它可以在客户端、服务器、移动应用程序甚至 VR 应用程序中使用。

在现代网络中，我们需要不断地操作 DOM；问题是这样做可能会严重影响我们应用程序的性能。React 使用虚拟 DOM，这意味着所有更新都发生在内存中（这比直接操作真实 DOM 更快）。与其他 JavaScript 框架（如 Angular、Vue 或 Backbone）相比，React 的学习曲线较短，主要是因为 React 代码大多是用现代 JavaScript（类、箭头函数、字符串模板等）编写的，而没有太多用于编写代码的模式，比如依赖注入，或者像 Angular 中的模板系统。

像 Airbnb、Microsoft、Netflix、Disney、Dropbox、Twitter、PayPal、Salesforce、Tesla 和 Uber 这样的公司在他们的项目中广泛使用 React。在这本书中，你将学习如何像他们一样使用最佳实践来开发你的 React 应用程序。

# 在 React 中使用最新的 JS 功能

正如我在介绍中所说，React 主要是用现代 JavaScript（ES6、ES7 和 ES8）编写的。如果你想利用 React，有一些现代 JS 功能是你应该掌握的，以获得你的 React 应用程序的最佳结果。在这个第一个示例中，我们将涵盖基本的 JS 功能，这样你就可以准备好开始在你的第一个 React 应用程序上工作了。

# 如何做到…

在本节中，我们将看到如何在 React 中使用最重要的 JS 功能：

1.  `let`和`const`：在 JavaScript 中声明变量的新方法是使用`let`或`const`。你可以使用`let`来声明可以在块作用域中改变其值的变量。`let`和`var`的区别在于，`let`是一个块作用域变量，不能是全局的，而用`var`可以声明一个全局变量，例如：

```jsx
 var name = 'Carlos Santana';
    let age = 30;

    console.log(window.name); // Carlos Santana
    console.log(window.age);  // undefined
```

1.  理解“块作用域”的最好方法是通过使用`var`和`let`声明`for`循环。首先，让我们使用`var`并看看它的行为：

```jsx
 for (var i = 1 ; i <= 10; i++) {
      console.log(i); // 1, 2, 3, 4... 10
    }

    console.log(i); // Will print the last value of i: 10
```

1.  如果我们用`let`写相同的代码，会发生这种情况：

```jsx
 for (let i = 1 ; i <= 10; i++) {
      console.log(i); // 1, 2, 3, 4... 10
    }

    console.log(i); // Uncaught ReferenceError: i is not defined
```

1.  使用`const`，我们可以声明常量，这意味着值不能被改变（除了数组和对象）：

```jsx
 const pi = 3.1416;
    pi = 5; // Uncaught TypeError: Assignment to constant variable.
```

1.  如果我们用`const`声明一个数组，我们可以操作数组元素（添加、删除或修改元素）：

```jsx
 const cryptoCurrencies = ['BTC', 'ETH', 'XRP'];

    // Adding ERT: ['BTC', 'ETH', 'XRP', 'ERT'];
    cryptoCurrencies.push('ERT'); 

 // Will remove the first element: ['ETH', 'XRP', 'ERT']; cryptoCurrencies.**shift**();

    **// Modifying an element**
    cryptoCurrencies[1] = 'LTC'; **// ['ETH', 'LTC', 'ERT'];**
```

1.  此外，使用对象，我们可以添加、删除或修改节点：

```jsx
 const person = {
      name: 'Carlos Santana',
      age: 30,
      email: 'carlos@milkzoft.com'
    };

    // Adding a new node...
    person.website = 'https://www.codejobs.com';

    // Removing a node...
    delete person.email;

    // Updating a node...
    person.age = 29;
```

1.  扩展运算符：扩展运算符（...）将可迭代对象分割成单独的值。在 React 中，它可以用于将值推入另一个数组，例如当我们想要通过利用`setState`向待办事项列表添加新项目时（这将在下一章中解释）：

```jsx
    this.setState({
      items: [
        ...this.state.items, // Here we are spreading the current items
        {
      task: 'My new task', // This will be a new task in our Todo list.
        }
      ]
    });
```

1.  此外，扩展运算符可以在 React 中用于在 JSX 中扩展属性（props）：

```jsx
    render() {
      const props = {};

      props.name = 'Carlos Santana';
      props.age = 30;
      props.email = 'carlos@milkzoft.com';

      return <Person {...props} />;
    }
```

1.  剩余参数：`rest`参数也用`...`表示。在函数中以`...`为前缀的最后一个参数称为剩余参数。剩余参数是一个数组，当参数的数量超过命名参数的数量时，它将包含函数的其余参数：

```jsx
 function setNumbers(param1, param2, ...args) {
      // param1 = 1
      // param2 = 2
      // args = [3, 4, 5, 6];
      console.log(param1, param2, ...args); // Log: 1, 2, 3, 4, 5, 6
    }

    setNumbers(1, 2, 3, 4, 5, 6);
```

1.  解构赋值：解构赋值是 React 中最常用的功能。它是一个表达式，允许我们将可迭代对象的值或属性分配给变量。通常，我们可以将组件的 props 转换为变量（或常量）：

```jsx
 // Imagine we are on our <Person> component and we are 
 // receiving the props (in this.props): name, age and email. render() { // Our props are: 
      // { name: 'Carlos Santana', age: 30, email: 
       'carlos@milkzoft.com' } console.log(this.props); 
      const { name, age, email } = this.props;

      // Now we can use the nodes as constants... console.log(name, age, email);

      return (
        <ul>
          <li>Name: {name}</li>
          <li>Age: {age}</li>
          <li>Email: {email}</li>
        </ul> ); }

    // Also the destructuring can be used on function parameters
    const Person = ({ name, age, email }) => (
      <ul>
        <li>Name: {name}</li>
        <li>Age: {age}</li>
        <li>Email: {email}</li>
      </ul>
    );
```

1.  箭头函数：ES6 提供了一种使用`=>`运算符创建函数的新方法。这些函数称为箭头函数。这种新方法有更短的语法，箭头函数是匿名函数。在 React 中，箭头函数用作绑定方法中的`this`对象的一种方式，而不是在构造函数中绑定它。

```jsx
 class Person extends Component {
      showProps = () => {
        console.log(this.props); // { name, age, email... }
      }

      render() {
        return (
          <div>
            Consoling props: {this.showProps()}
          </div>
        );
      }
    }
```

1.  模板文字：模板文字是使用反引号（` `）而不是单引号（''）或双引号（""）创建字符串的新方法。React 使用模板文字来连接类名或使用三元运算符呈现字符串：

```jsx
 render() {
      const { theme } = this.props;

      return (
        <div 
 className={`base ${theme === 'dark' ? 'darkMode' : 
         'lightMode'}`}        >
          Some content here...
        </div>
      );
    }
```

1.  映射：`map()`方法返回一个新数组，其中包含对调用数组中每个元素的提供函数的结果。在 React 中，映射的使用非常广泛，主要用于在 React 组件内呈现多个元素；例如，它可以用于呈现任务列表：

```jsx
 render() {
      const tasks = [
        { task: 'Task 1' },
        { task: 'Task 2' },
        { task: 'Task 3' }
      ];

      return (
        <ul>
          {tasks.map((item, key) => <li key={key}>{item.task}</li>}
        </ul>
      );
    }
```

1.  **Object.assign()**：`Object.assign()` 方法用于将一个或多个源对象的所有可枚举自有属性的值复制到目标对象中。它将返回目标对象。这个方法主要用于 Redux，用于创建不可变对象并将新状态返回给 reducers（Redux 将在第五章，*精通 Redux*中介绍）：

```jsx
    export default function coinsReducer(state = initialState, action) {
      switch (action.type) {
        case FETCH_COINS_SUCCESS: {
          const { payload: coins } = action;

          return Object.assign({}, state, {
            coins
          });
        }

        default:
         return state;
      }
    };
```

1.  **类**：JavaScript 类是 ES6 中引入的，主要是现有基于原型的继承的新语法。类是函数，不会被提升。React 使用类来创建类 *组件*：

```jsx
    import React, { Component } from 'react';

    class Home extends Component {
      render() {
        return <h1>I'm Home Component</h1>;
      }
    }

    export default Home;
```

1.  **静态方法**：静态方法不是在类的实例上调用的。相反，它们是在类本身上调用的。这些通常是实用函数，比如用来创建或克隆对象的函数。在 React 中，它们可以用来定义组件中的 `PropTypes`：

```jsx
 import React, { Component } from 'react';
    import PropTypes from 'prop-types';
    import logo from '../../images/logo.svg';

    class Header extends Component {
      static propTypes = {
        title: PropTypes.string.isRequired,
        url: PropTypes.string
      };

      render() {
        const { 
          title = 'Welcome to React', 
          url = 'http://localhost:3000' 
        } = this.props;

        return (
          <header className="App-header">
            <a href={url}>
              <img src={logo} className="App-logo" alt="logo" />
            </a>
            <h1 className="App-title">{title}</h1>
          </header>
        );
      }
    }

    export default Header;
```

1.  **Promises**：`Promise` 对象表示异步操作的最终完成（或失败）及其结果值。我们将在 React 中使用 promises 来处理请求，例如使用 axios 或 fetch；此外，我们将使用 Promises 来实现服务器端渲染（这将在第十一章*，实现服务器端渲染*中介绍）。

1.  **async/await**：async 函数声明定义了一个异步函数，返回一个 `AsyncFunction` 对象。这也可以用来执行服务器请求，例如使用 axios：

```jsx
    Index.getInitialProps = async () => {
      const url = 'https://api.coinmarketcap.com/v1/ticker/';
      const res = await axios.get(url);

      return {
        coins: res.data
      };
    };
```

# React 有什么新功能？

这段文字是在 2018 年 8 月 14 日写的，当时 React 的最新版本是 16.4.2。React 16 版本有一个名为 Fiber 的新核心架构。

在这个配方中，我们将看到这个版本中最重要的更新，你应该了解这些更新，以充分利用 React。

# 如何做...

让我们看看新的更新：

1.  **组件现在可以从渲染中返回数组和字符串**：以前，React 强制你返回一个包裹在 <div> 或其他标签中的元素；现在可以直接返回数组或字符串：

```jsx
    // Example 1: Returning an array of elements.
 render() {
      // Now you don't need to wrap list items in an extra element
      return [
        <li key="1">First item</li>,
        <li key="2">Second item</li>,
        <li key="3">Third item</li>,
      ];
    }

    // Example 2: Returning a string
    render() {
      return 'Hello World!';
    }
```

1.  此外，React 现在有一个名为 **Fragment** 的新功能，它也可以作为元素的特殊包装器。它可以用空标签（`<></>`）指定，也可以直接使用 `React.Fragment`：

```jsx
    // Example 1: Using empty tags <></>
 render() {
      return (
        <>
          <ComponentA />
          <ComponentB />
          <ComponentC />
        </>
      );
    }

    // Example 2: Using React.Fragment
    render() {
      return (
        <React.Fragment>
          <h1>An h1 heading</h1>
          Some text here.
          <h2>An h2 heading</h2>
          More text here.
          Even more text here.
        </React.Fragment>
      );
    }

 // Example 3: Importing Fragment
    import React, { Fragment } from 'react';
    ...
    render() {
      return (
        <Fragment>
          <h1>An h1 heading</h1>
          Some text here.
          <h2>An h2 heading</h2>
          More text here.
          Even more text here.
        </Fragment>
      );
    }
```

1.  **来自官方网站的错误边界**：

UI 的一部分出现 JavaScript 错误不应该导致整个应用程序崩溃。为了解决 React 用户的这个问题，React 16 引入了一个新概念，即"错误边界"。错误边界是 React 组件，它们捕获其子组件树中任何位置的 JavaScript 错误，记录这些错误，并显示一个替代 UI，而不是崩溃的组件树。错误边界在渲染期间、生命周期方法中以及它们下面整个树的构造函数中捕获错误。如果一个类组件定义了一个名为 componentDidCatch(error, info)的新生命周期方法，它就成为了一个错误边界。

```jsx
    class ErrorBoundary extends React.Component {
      constructor(props) {
        super(props);

        this.state = { 
          hasError: false 
        };
      }

      componentDidCatch(error, info) {
        // Display fallback UI
        this.setState({ 
          hasError: true 
        });

        // You can also log the error to an error reporting service
        logErrorToMyService(error, info);
      }

      render() {
        if (this.state.hasError) {
          // You can render any custom fallback UI
          return <h1>Something went wrong.</h1>;
        }

        return this.props.children;
      }
    }

    // **Then you can use it as a regular component:
**    **render**() {
   **   <ErrorBoundary>**
        <MyComponent />
      **</ErrorBoundary>
**    }
```

1.  **更好的服务器端渲染** 来自官方网站：

React 16 包括一个完全重写的服务器渲染器。它非常快。它支持流式传输，因此您可以更快地开始向客户端发送字节。并且由于一种新的打包策略，它会编译掉 process.env 检查（信不信由你，在 Node 中读取 process.env 真的很慢！），您不再需要捆绑 React 以获得良好的服务器渲染性能。

1.  从官方网站上减小文件大小："尽管增加了所有这些内容，React 16 实际上比 15.6.1 要小。

+   react 为 5.3 kb（经过 gzip 压缩为 2.2 kb），而之前为 20.7 kb（经过 gzip 压缩为 6.9 kb）

+   react-dom 为 103.7 kb（经过 gzip 压缩为 32.6 kb），而之前为 141 kb（经过 gzip 压缩为 42.9 kb）

+   react + react-dom 为 109 kb（经过 gzip 压缩为 34.8 kb），而之前为 161.7 kb（经过 gzip 压缩为 49.8 kb）

与上一个版本相比，这相当于总体减小了 32%的大小（gzip 后减小了 30%）。

如果您想查看 React 的最新更新，可以访问官方 React 博客：[`reactjs.org/blog`](https://reactjs.org/blog)。

# 在 Windows 上使用 React

我不是 Windows 的开发大粉丝，因为有时配置起来有点问题。我总是更喜欢 Linux 或 Mac，但我知道很多读者会使用 Windows。在这个配方中，我将向您展示在使用 Windows 时可能遇到的最常见问题。

# 如何做...

我们现在将看到在开发中使用 Windows 时最常见的问题：

1.  **终端**：你将面临的第一个问题是使用 Windows 终端（CMD），因为它不支持 Unix 命令（如 Linux 或 Mac）。解决方法是安装一个 Unix 终端；最受推荐的是使用**Git Bash**终端，它在你安装**Git**时就包含在内（[`git-scm.com`](https://git-scm.com)），第二个选择是安装**Cygwin**，它是 Windows 中的 Linux 终端（[`www.cygwin.com`](https://www.cygwin.com)）。

1.  **环境变量**：在 Windows 上使用的另一个常见问题是设置环境变量。通常，当我们编写 npm 脚本时，我们设置诸如`NODE_ENV=production`或`BABEL_ENV=development`之类的环境变量，但在 Windows 中设置这些变量时，你需要使用`SET`命令，这意味着你需要执行`SET NODE_ENV=production`或`SET BABEL_ENV=development`。这样做的问题是，如果你与其他使用 Linux 或 Mac 的人一起工作，他们将会遇到`SET`命令的问题，而且你可能需要忽略这个文件，只为你的本地环境修改它。这可能会很繁琐。解决这个问题的方法是使用一个叫做 cross-env 的包；你可以通过执行`npm install cross-env`来安装它，这将在 Windows、Mac 和 Linux 上都能工作：

```jsx
   "scripts": {
      "start": "cross-env NODE_ENV=development webpack-dev-server --
       mode development --open",
      "start-production": "cross-env NODE_ENV=production webpack-dev-
       server --mode production"
    }
```

1.  **区分大小写的文件或目录**：实际上，在 Linux 上也会发生这种情况，但有时很难识别这个问题，例如，如果你在`components/**home**/Home.jsx`目录中创建一个组件，但在你的代码中尝试像这样导入组件：

```jsx
 import Home from './components/Home/Home';
```

通常，这在 Mac 上不会引起任何问题，但可能会在 Linux 或 Windows 上生成错误，因为我们试图将一个文件以不同的名称（因为它是区分大小写的）导入到目录中。

1.  **路径**：Windows 使用反斜杠（`\`）来定义路径，而在 Mac 或 Linux 中使用正斜杠（`/`）。这是有问题的，因为有时我们需要定义一个路径（在 Node.js 中大多数情况下），我们需要做类似这样的事情：

```jsx
 // In Mac or Linux
    app.use(
      stylus.middleware({
        src: __dirname + '/stylus',
        dest: __dirname + '/public/css',
        compile: (str, path) => {
          return stylus(str)
            .set('filename', path)
            .set('compress', true);
        }
      })
    );

    // In Windows
    app.use(
      stylus.middleware({
        src: __dirname + '\stylus',
        dest: __dirname + '\public\css',
        compile: (str, path) => {
          return stylus(str)
            .set('filename', path)
            .set('compress', true);
        }
      })
    );

    // This can be fixed by using path
    import path from 'path';

   // path.join will generate a valid path for Windows or Linux and Mac
    app.use(
      stylus.middleware({
        src: path.join(__dirname, 'stylus'),
        dest: path.join(__dirname, 'public', 'css'),
        compile: (str, path) => {
          return stylus(str)
            .set('filename', path)
            .set('compress', config().html.css.compress);
        }
      })
    );
```
