# 第一章：介绍 React 和 React Hooks

React 是一个可以用于构建高效和可扩展 Web 应用程序的 JavaScript 库。React 由 Facebook 开发，并在许多大型 Web 应用程序中使用，如 Facebook、Instagram、Netflix 和 WhatsApp Web。

在本书中，我们将学习如何使用 React 构建复杂和高效的用户界面，同时保持代码简单和可扩展。使用 React Hooks 的新范式，我们可以极大地简化在 Web 应用程序中处理状态管理和副作用，确保以后应用程序的增长和扩展潜力。我们还将学习有关**React 上下文**和**React 悬挂**，以及它们如何与 Hooks 一起使用。之后，我们将学习如何将**Redux**和**MobX**与 React Hooks 集成。最后，我们将学习如何从现有的 React 类组件、Redux 和 MobX Web 应用程序迁移到 React Hooks。

在本书的第一章中，我们将学习 React 和 React Hooks 的基本原则。我们首先学习 React 和 React Hooks 是什么，以及为什么我们应该使用它们。然后，我们继续学习 Hooks 的功能。最后，我们介绍了 React 提供的各种 Hooks 的类型，以及本书中将要学习的一些 Hooks。通过学习 React 和 React Hooks 的基础知识，我们将更好地理解本书中将介绍的概念。

本章将涵盖以下主题：

+   了解 React 的基本原则

+   激发对 React Hooks 的需求

+   开始使用 React Hooks

+   概述各种 Hooks

# 技术要求

应该已经安装了相当新的 Node.js 版本（v11.12.0 或更高）。还需要安装 Node.js 的`npm`包管理器。

本章的代码可以在 GitHub 存储库中找到：[`github.com/PacktPublishing/Learn-React-Hooks/tree/master/Chapter01`](https://github.com/PacktPublishing/Learn-React-Hooks/tree/master/Chapter01)。

查看以下视频以查看代码的实际操作：

[`bit.ly/2Mm9yoC`](http://bit.ly/2Mm9yoC)

请注意，强烈建议您自己编写代码。不要简单地运行之前提供的代码示例。重要的是要自己编写代码，以便正确学习和理解它。但是，如果遇到任何问题，您可以随时参考代码示例。

现在，让我们开始这一章。

# React 的原则

在我们开始学习 React Hooks 之前，我们将学习 React 的三个基本原则。这些原则使我们能够轻松编写可扩展的 Web 应用程序。了解这些基本原则很重要，因为它们将帮助我们理解 Hooks 如何以及为什么适用于 React 生态系统。

React 基于三个基本原则：

+   **声明式**：我们告诉 React 我们想要它做什么，而不是告诉它如何做事情。因此，我们可以轻松设计我们的应用程序，当数据发生变化时，React 将高效地更新和渲染正确的组件。例如，下面的代码是命令式的，它是声明式的相反：

```jsx
const input = ['a', 'b', 'c']
let result = []
for (let i = 0; i < input.length; i++) {
    result.push(input[i] + input[i])
}
console.log(result) // prints: [ 'aa', 'bb', 'cc' ]
```

正如我们所看到的，命令式代码中，我们需要一步一步地告诉计算机要做什么。然而，使用声明式代码，我们只需告诉计算机我们想要什么，如下所示：

```jsx
const input = ['a', 'b', 'c']
let result = input.map(str => str + str)
console.log(result) // prints: [ 'aa', 'bb', 'cc' ]
```

在前面的声明式代码中，我们告诉计算机我们想要将`input`数组的每个元素从`str`映射到`str + str`。如我们所见，声明式代码要简洁得多。

+   **基于组件**：React 封装了管理自己状态和视图的组件，然后允许我们组合它们以创建复杂的用户界面。

+   **学一次，随处编写**：React 不对您的技术栈做出假设，并尽量确保您可以开发应用程序而尽量不重写现有代码。

我们刚提到 React 是基于组件的。在 React 中，有两种类型的组件：

+   **函数组件**：以 props 作为参数的 JavaScript 函数，并返回用户界面（通常通过 JSX）

+   **类组件**：提供`render`方法的 JavaScript 类，该方法返回用户界面（通常通过 JSX）

虽然函数组件更容易定义和理解，但是类组件需要处理状态、上下文和 React 的许多高级功能。然而，使用 React Hooks，我们可以处理 React 的高级功能而不需要类组件！

# 使用 React Hooks 的动机

React 的三个基本原则使得编写代码、封装组件和在多个平台上共享代码变得容易。React 总是尽量利用现有的 JavaScript 特性，而不是重复造轮子。因此，我们将学习软件设计模式，这些模式将适用于许多情况，而不仅仅是设计用户界面。

React 始终努力使开发者体验尽可能顺畅，同时确保保持足够的性能，而开发者不必过多担心如何优化性能。然而，在使用 React 的多年中，已经确定了一些问题。

让我们在接下来的章节中详细看看这些问题。

# 混乱的类

过去，我们必须使用带有特殊函数的类组件，称为生命周期方法，例如`componentDidUpdate`，以及特殊的状态处理方法，例如`this.setState`，以处理状态变化。React 类，尤其是 JavaScript 对象的`this`上下文，对人类和机器来说都很难阅读和理解。

`this`是 JavaScript 中的一个特殊关键字，它总是指向它所属的对象：

+   在方法中，`this`指的是类对象（类的实例）。

+   在事件处理程序中，`this`指的是接收到事件的元素。

+   在函数或独立状态下，`this`指的是全局对象。例如，在浏览器中，全局对象是`Window`对象。

+   在严格模式下，`this`在函数中是`undefined`。

+   此外，`call()`和`apply()`等方法可以改变`this`所指的对象，因此它可以指向任何对象。

对于人类来说，类很难，因为`this`总是指向不同的东西，所以有时（例如在事件处理程序中）我们需要手动重新绑定它到类对象。对于机器来说，类很难，因为机器不知道类中的哪些方法将被调用，以及`this`将如何被修改，这使得优化性能和删除未使用的代码变得困难。

此外，类有时要求我们同时在多个地方编写代码。例如，如果我们想在组件渲染时获取数据，或者数据更新时获取数据，我们需要使用两种方法来做到这一点：一次在`componentDidMount`中，一次在`componentDidUpdate`中。

举个例子，让我们定义一个从**应用程序编程接口**（**API**）获取数据的类组件：

1.  首先，我们通过扩展`React.Component`类来定义我们的类组件：

```jsx
class Example extends React.Component {
```

1.  然后，我们定义`componentDidMount`生命周期方法，在这里我们从 API 中获取数据：

```jsx
        componentDidMount () {
            fetch(`http://my.api/${this.props.name}`)
                .then(...)
        }
```

1.  然而，我们还需要定义`componentDidUpdate`生命周期方法，以防`name`属性发生变化。此外，我们需要在这里添加一个手动检查，以确保只有在`name`属性发生变化时才重新获取数据，而不是在其他属性发生变化时：

```jsx
    componentDidUpdate (prevProps) {
        if (this.props.name !== prevProps.name) {
            fetch(`http://my.api/${this.props.name}`)
                .then(...)
        }
    }
}
```

1.  为了使我们的代码更少重复，我们可以定义一个名为`fetchData`的单独方法，以便获取我们的数据，如下所示：

```jsx
        fetchData () {
            fetch(`http://my.api/${this.props.name}`)
                .then(...)
        }
```

1.  然后，我们可以在`componentDidMount`和`componentDidUpdate`中调用该方法：

```jsx
        componentDidMount () {
            this.fetchData()
        }

        componentDidUpdate (prevProps) {
            if (this.props.name !== prevProps.name) {
                this.fetchData()
```

```jsx
            }
        }
```

然而，即使这样，我们仍然需要在两个地方调用`fetchData`。每当我们更新传递给方法的参数时，我们需要在两个地方更新它们，这使得这种模式非常容易出现错误和未来的 bug。

# 包装地狱

在 Hooks 之前，如果我们想要封装状态管理逻辑，我们必须使用高阶组件和渲染属性。例如，我们创建一个使用上下文来处理用户认证的 React 组件如下：

1.  我们首先通过导入`authenticateUser`函数来包装我们的组件与上下文，以及`AuthenticationContext`组件来访问上下文：

```jsx
import authenticateUser, { AuthenticationContext } from './auth'
```

1.  然后，我们定义了我们的`App`组件，在这里我们使用了`AuthenticationContext.Consumer`组件和`user`渲染属性：

```jsx
const App = () => (
    <AuthenticationContext.Consumer>
        {user =>
```

1.  现在，我们根据用户是否已登录来显示不同的文本：

```jsx
                user ? `${user} logged in` : 'not logged in'
```

在这里，我们使用了两个 JavaScript 概念：

+   +   一个三元运算符，它是`if`条件的内联版本。它看起来如下：`ifThisIsTrue ? returnThis : otherwiseReturnThis`。

+   一个模板字符串，它可以用来将变量插入到字符串中。它用反引号（`` ` ``) 而不是普通的单引号（`'`）。 变量可以通过`${ variableName}`语法插入。我们还可以在`${}`括号内使用任何JavaScript表达式，例如`${someValue + 1}`。

```jsx
import { useInterval, useTimeout } from 'react-hookedup'

useInterval(() => { ... }, 1000)
useTimeout(() => { ... }, 1000)
```

正如我们所看到的，Hooks 极大地简化了我们在 React 中处理间隔和超时的方式。

# 其他社区 Hooks

正如你可以想象的那样，社区提供了许多其他的 Hooks。我们将深入学习之前提到的社区 Hooks，以及第八章中的其他社区 Hooks：*使用社区 Hooks*。

# 总结

在本书的第一章中，我们首先学习了 React 的基本原则以及它提供的组件类型。然后，我们继续学习了类组件的常见问题，以及如何使用 React 的现有功能，以及它们如何违反基本原则。接下来，我们使用类组件和带有 Hooks 的函数组件实现了一个简单的应用程序，以便能够比较这两种解决方案之间的差异。正如我们所发现的，带有 Hooks 的函数组件更适合 React 的基本原则，因为它们不会遇到类组件的相同问题，并且使我们的代码更加

简洁易懂！最后，我们初次见识了本书中将要学习的各种 Hooks。在本章之后，React 和 React Hooks 的基础知识已经清晰。现在我们可以继续学习更高级的 Hooks 概念。

在下一章中，我们将深入了解 State Hook 的工作原理，通过从头开始重新实现它。通过这样做，我们将了解 Hooks 的内部工作原理以及它们的局限性。之后，我们将使用 State Hook 创建一个小型的博客应用程序！

# 问题

回顾一下我们在本章学到的内容，尝试回答以下问题：

1.  React 的三个基本原则是什么？

1.  React 中有哪两种类型的组件？

1.  React 中类组件存在哪些问题？

1.  在 React 中使用高阶组件的问题是什么？

1.  我们可以使用哪个工具来设置一个 React 项目，我们需要运行什么命令来使用它？

1.  如果我们在类组件中遇到以下错误，我们需要做什么：*TypeError: undefined is not an object (evaluating 'this.setState')*？

1.  我们如何使用 Hooks 访问和设置 React 状态？

1.  使用 Hooks 的函数组件与类组件相比有什么优势？

1.  在更新 React 时，我们是否需要使用 Hooks 替换所有类组件为函数组件？

1.  React 提供的三个基本 Hooks 是什么？

# 进一步阅读

如果您对本章学习的概念感兴趣，可以查看以下阅读材料：

+   在 GitHub 上创建 React 应用程序：[`github.com/facebook/create-react-app#create-react-app--`](https://github.com/facebook/create-react-app#create-react-app--)

+   React Hooks 的 RFC：[`github.com/reactjs/rfcs/blob/master/text/0068-react-hooks.md`](https://github.com/reactjs/rfcs/blob/master/text/0068-react-hooks.md)

+   使用 React 处理输入：[`reactjs.org/docs/forms.html`](https://reactjs.org/docs/forms.html)

+   React 中类组件的状态和生命周期：[`reactjs.org/docs/state-and-lifecycle.html`](https://reactjs.org/docs/state-and-lifecycle.html)

+   解构：[`exploringjs.com/es6/ch_destructuring.html`](http://exploringjs.com/es6/ch_destructuring.html)

+   模板字符串：[`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)

+   三元运算符：[`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)
