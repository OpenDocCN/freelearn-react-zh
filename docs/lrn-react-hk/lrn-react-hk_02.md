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

```
const input = ['a', 'b', 'c']
let result = []
for (let i = 0; i < input.length; i++) {
    result.push(input[i] + input[i])
}
console.log(result) // prints: [ 'aa', 'bb', 'cc' ]
```

正如我们所看到的，命令式代码中，我们需要一步一步地告诉计算机要做什么。然而，使用声明式代码，我们只需告诉计算机我们想要什么，如下所示：

```
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

```
class Example extends React.Component {
```

1.  然后，我们定义`componentDidMount`生命周期方法，在这里我们从 API 中获取数据：

```
        componentDidMount () {
            fetch(`http://my.api/${this.props.name}`)
                .then(...)
        }
```

1.  然而，我们还需要定义`componentDidUpdate`生命周期方法，以防`name`属性发生变化。此外，我们需要在这里添加一个手动检查，以确保只有在`name`属性发生变化时才重新获取数据，而不是在其他属性发生变化时：

```
    componentDidUpdate (prevProps) {
        if (this.props.name !== prevProps.name) {
            fetch(`http://my.api/${this.props.name}`)
                .then(...)
        }
    }
}
```

1.  为了使我们的代码更少重复，我们可以定义一个名为`fetchData`的单独方法，以便获取我们的数据，如下所示：

```
        fetchData () {
            fetch(`http://my.api/${this.props.name}`)
                .then(...)
        }
```

1.  然后，我们可以在`componentDidMount`和`componentDidUpdate`中调用该方法：

```
        componentDidMount () {
            this.fetchData()
        }

        componentDidUpdate (prevProps) {
            if (this.props.name !== prevProps.name) {
                this.fetchData()
```

```
            }
        }
```

然而，即使这样，我们仍然需要在两个地方调用`fetchData`。每当我们更新传递给方法的参数时，我们需要在两个地方更新它们，这使得这种模式非常容易出现错误和未来的 bug。

# 包装地狱

在 Hooks 之前，如果我们想要封装状态管理逻辑，我们必须使用高阶组件和渲染属性。例如，我们创建一个使用上下文来处理用户认证的 React 组件如下：

1.  我们首先通过导入`authenticateUser`函数来包装我们的组件与上下文，以及`AuthenticationContext`组件来访问上下文：

```
import authenticateUser, { AuthenticationContext } from './auth'
```

1.  然后，我们定义了我们的`App`组件，在这里我们使用了`AuthenticationContext.Consumer`组件和`user`渲染属性：

```
const App = () => (
    <AuthenticationContext.Consumer>
        {user =>
```

1.  现在，我们根据用户是否已登录来显示不同的文本：

```
                user ? `${user} logged in` : 'not logged in'
```

在这里，我们使用了两个 JavaScript 概念：

+   +   一个三元运算符，它是`if`条件的内联版本。它看起来如下：`ifThisIsTrue ? returnThis : otherwiseReturnThis`。

+   一个模板字符串，它可以用来将变量插入到字符串中。它用反引号（`` ` ``) 而不是普通的单引号（`'`）。 变量可以通过`${ variableName}`语法插入。我们还可以在`${}`括号内使用任何JavaScript表达式，例如`${someValue + 1}`。

4.  Finally, we export our component after wrapping it with the `authenticateUser` context:

```

}

</AuthenticationContext.Consumer>

)

export default authenticateUser(App)

```

In the previous example, we used the higher-order `authenticateUser` component to add authentication logic to our existing component. We then used `AuthenticationContext.Consumer` to inject the `user` object into our component through its render props.

As you can imagine, using many contexts will result in a large tree with many sub-trees, also called **wrapper hell**. For example, when we want to use three contexts, the wrapper hell looks as follows:

```

<AuthenticationContext.Consumer>

{user => (

<LanguageContext.Consumer>

{language => (

<StatusContext.Consumer>

{status => (

...

)}

</StatusContext.Consumer>

)}

</LanguageContext.Consumer>

)}

</AuthenticationContext.Consumer>

```

This is not very easy to read or write, and it is also prone to errors if we need to change something later on. Furthermore, the wrapper hell makes debugging hard, because we need to look at a large component tree, with many components just acting as wrappers.

# Hooks to the rescue!

React Hooks are based on the same fundamental principles as React. They try to encapsulate state management by using existing JavaScript features. As a result, we do not need to learn and understand specialized React features anymore; we can simply tap into our existing JavaScript knowledge in order to use Hooks.

Using Hooks, we can solve all the previously mentioned problems. We do not need to use class components anymore, because Hooks are simply functions that can be called in function components. We also do not need to use higher-order components and render props for contexts anymore, because we can simply use a Context Hook to get the data that we need. Furthermore, Hooks allow us to reuse stateful logic between components, without creating higher-order components.

For example, the aforementioned problems with life cycle methods could be solved using Hooks, as follows:

```

function Example ({ name }) {

useEffect(() => {

fetch(`http://my.api/${this.props.name}`)

然后...

}, [ name ])

// ...

}

```

The Effect Hook that was implemented here will automatically trigger when the component mounts, and whenever the `name` prop changes.

Furthermore, the wrapper hell that was mentioned earlier could also be solved using Hooks, as follows:

```

const user = useContext(AuthenticationContext)

const language = useContext(LanguageContext)

const status = useContext(StatusContext)

```

Now that we know which problems Hooks can solve, let's get started using Hooks in practice!

# Getting started with React Hooks

As we can see, React Hooks solve many problems, especially of larger web applications. Hooks were added in React 16.8, and they allow us to use state, and various other React features, without writing a class. In this section, we are going to start out by initializing a project with `create-react-app`, then we will define a class component, and finally we will write the same component as a function component using Hooks. By the end of this section, we will have talked about the advantages of Hooks, and how we would go about migrating to a Hook-based solution.

# Initializing a project with create-react-app

To initialize a React project, we can use the `create-react-app` tool, which sets up the environment for React development, including the following:

*   Babel, so that we can use the JSX and ES6 syntaxes
*   It even includes language extras beyond ES6, such as the object spread operator, which we are going to make use of later
*   Additionally, we could even use TypeScript and Flow syntax

Furthermore, `create-react-app` sets up the following:

*   Autoprefixed **Cascading Style Sheets** (**CSS**), so that we do not need browser-specific prefixes such as `-webkit`
*   A fast interactive unit test runner with code coverage reporting
*   A live development server, which warns us about common mistakes
*   A build script, which bundles JavaScript, CSS, and images for production, including hashes and sourcemaps
*   An offline-first service worker and a web app manifest to meet all criteria of a **Progressive Web App** (**PWA**)
*   Hassle-free updates for all the tools that have been previously listed

As we can see, the `create-react-app` tool makes React development a lot easier for us. It is the perfect tool for us to use in order to learn about React, as well as for deploying React applications in production.

# Creating a new project

In order to set up a new project, we run the following command, which creates a new directory named `<app-name>`:

```

> npx create-react-app <app-name>

```

If you prefer using the `yarn` package manager, you can run `yarn create react-app <app-name>` instead.

We are now going to create a new project using `create-react-app`. Run the following command to create a new React project for the first example of the first chapter:

```

> npx create-react-app chapter1_1

```

Now that we have initialized our project, let's move on to starting the project.

# Starting a project

In order to start a project in development mode, we have to run the `npm start` command. Run the following command:

```

> npm start

```

Now, we can access our project by opening `http://localhost:3000` in our browser:

![](img/0a60f3e7-17d3-415e-bb06-6f135db4f8ec.png)

Our first React app!

As we can see, with `create-react-app`, it is quite easy to set up a new React project!

# Deploying a project

To build a project for production deployments, we simply run the `build` script:

1.  Run the following command to build the project for production deployment:

```

> npm run-script build

```

Using `yarn`, we can simply run `yarn build`. Actually, we can run any package script that does not conflict with the name of an internal `yarn` command in this way: `yarn <script-name>`, instead of `npm run-script <script-name>`.

2.  We can then serve our static build folder with a web server, or by using the `serve` tool. First, we have to install it:

```

> npm install -g serve

```

3.  Then, we can run the `serve` command, as follows:

```

> serve -s build

```

The `-s` flag of the `serve` command rewrites all not-found requests to `index.html`, allowing for client-side routing.

Now, we can access the same app by opening `http://localhost:5000` in our browser. Please note that the `serve` tool does not automatically open the page in your browser.

After learning about `create-react-app`, we are now going to write our first component with React.

# Starting with a class component

First, we start out with a traditional React class component, which lets us enter a name, which we then display in our app.

# Setting up the project 

As mentioned before, we are going to use `create-react-app` to initialize our project. If you have not done so already, run the following command now:

```

> npx create-react-app chapter1_1

```

Next we are going to define our app as a class component.

# Defining the class component

We first write our app as a traditional class component, as follows:

1.  First, we remove all code from the `src/App.js` file.
2.  Next, in `src/App.js`, we import `React`:

```

import React from 'react'

```

3.  We then start defining our own class component—`MyName`:

```

class MyName extends React.Component {

```

4.  Next, we have to define a `constructor` method, where we set the initial `state` object, which will be an empty string. Here, we also need to make sure to call `super(props)`, in order to let the `React.Component` constructor know about the `props` object:

```

constructor (props) {

super(props)

this.state = { name: '' }

}

```

5.  Now, we define a method to set the `name` variable, by using `this.setState`. As we will be using this method to handle input from a text field, we need to use `evt.target.value` to get the value from the input field:

```

handleChange (evt) {

this.setState({ name: evt.target.value })

}

```

6.  Then, we define the `render` method, where we are going to display an input field and the name:

```

render () {

```

7.  To get the `name` variable from the `this.state` object, we are going to use destructuring:

```

const { name } = this.state

```

The previous statement is the equivalent of doing the following:

```

const name = this.state.name

```

8.  Then, we display the currently entered `name` state variable:

```

return (

<div>

<h1>我的名字是：{name}</h1>

```

9.  We display an `input` field, passing the handler method to it:

```

<input type="text" value={name} onChange={this.handleChange} />

</div>

)

}

}

```

10.  Finally, we export our class component:

```

export default MyName

```

If we were to run this code now, we would get the following error when entering text, because passing the handler method to `onChange` changes the `this` context:

Uncaught TypeError: Cannot read property 'setState' of undefined

11.  So, now we need to adjust the `constructor` method and rebind the `this` context of our handler method to the class:

```

constructor (props) {

super(props)

this.state = { name: '' }

this.handleChange = this.handleChange.bind(this)

}

```

There is the possibility of using arrow functions as class methods, to avoid having to re-bind the `this` context. However, to use this feature we need to install the Babel compiler plugin, `@babel/plugin-proposal-class-properties`, as it is not a released JavaScript feature yet.

Finally, our component works! As you can see, there is a lot of code required to get state handling to work properly with class components. We also had to rebind the `this` context, because otherwise our handler method would not work. This is not very intuitive, and is easy to miss while developing, resulting in an annoying developer experience.

# Example code

The example code can be found in the `Chapter01/chapter1_1` folder.

Just run `npm install` in order to install all dependencies, and `npm start` to start the application; then visit `http://localhost:3000` in your browser (if it did not open automatically).

# Using Hooks instead

After using a traditional class component to write our app, we are going to write the same app using Hooks. As before, our app is going to let us enter a name, which we then display in our app.

Please note that it is only possible to use Hooks in React function components. You cannot use Hooks in a React class component!

We now start by setting up the project.

# Setting up the project

Again, we use `create-react-app` to set up our project:

```

> npx create-react-app chapter1_2

```

Let's get started with defining a function component using Hooks now.

# Defining the function component

Now, we define the same component as a function component:

1.  First, we remove all code from the `src/App.js` file.
2.  Next, in `src/App.js`, we import React, and the **`useState` **Hook:

```

import React, { useState } from 'react'

```

3.  We start with the function definition. In our case, we do not pass any arguments, because our component does not have any props:

```

function MyName () {

```

The next step would be to get the `name` variable from the component state. However, we cannot use `this.state` in function components. We have already learned that Hooks are just JavaScript functions, but what does that really mean? It means that we can simply use Hooks from function components, just like any other JavaScript function!

To use state via Hooks, we call `useState()` with our initial state as the argument. This function returns an array with two elements:

*   *   The current state
    *   A setter function to set the state

4.  We can use destructuring to store these two elements in separate variables, as follows:

```

const [ name, setName ] = useState('')

```

The previous code is equivalent to the following:

```

const nameHook = useState('')

const name = nameHook[0]

const setName = nameHook[1]

```

5.  Now, we define the input handler function, where we make use of the `setName` setter function:

```

function handleChange (evt) {

setName(evt.target.value)

}

```

As we are not dealing with classes now, there is no need to rebind `this` anymore!

6.  Finally, we render our user interface by returning it from the function. Then, we export the function component:

```

return (

<div>

<h1>我的名字是：{name}</h1>

<input type="text" value={name} onChange={handleChange} />

</div>

)

}

export default MyName

```

And that's it—we have successfully used Hooks for the first time! As you can see, the `useState` Hook is a drop-in replacement for `this.state` and `this.setState`.

Let's run our app by executing `npm start`, and opening `http://localhost:3000` in our browser:

![](img/df481a51-52a2-4436-901c-f8ceeb300fa6.png)

Our first React app with Hooks

After implementing the same app with a class component and a function component, let's compare the solutions.

# Example code

The example code can be found in the `Chapter01/chapter1_2` folder.

Just run `npm install` in order to install all dependencies, and `npm start` to start the application; then visit `http://localhost:3000` in your browser (if it did not open automatically).

# Comparing the solutions

Let's compare our two solutions, in order to see the differences between class components, and function components using Hooks.

# Class component

The class component makes use of the `constructor` method in order to define state, and needs to rebind `this` in order to pass the handler method to the `input` field. The full class component code looks as follows:

```

import React from 'react'

class MyName extends React.Component {

constructor (props) {

super(props)

this.state = { name: '' }

this.handleChange = this.handleChange.bind(this)

}

handleChange (evt) {

this.setState({ name: evt.target.value })

}

render () {

const { name } = this.state

return (

<div>

<h1>我的名字是：{name}</h1>

<input type="text" value={name} onChange={this.handleChange} />

</div>

)

}

}

export default MyName

```

As we can see, the class component needs a lot of boilerplate code to initialize the `state` object and handler functions.

Now, let's take a look at the function component.

# Function component with Hook

The function component makes use of the `useState` Hook instead, so we do not need to deal with `this` or a `constructor` method. The full function component code looks as follows:

```

import React, { useState } from 'react'

function MyName () {

const [ name, setName ] = useState('')

function handleChange (evt) {

setName(evt.target.value)

}

return (

<div>

<h1>我的名字是：{name}</h1>

<input type="text" value={name} onChange={handleChange} />

</div>

)

}

export default MyName

```

As we can see, Hooks make our code much more concise and easier to reason about. We do not need to worry about how things work internally anymore; we can simply use state, by accessing the `useState` function!

# Advantages of Hooks

Let's remind ourselves about the first principle of React:

Declarative: Instead of telling React how to do things, we tell it what we want it to do. As a result, we can easily design our applications, and React will efficiently update and render just the right components when the data changes.

As we have learned in this chapter, Hooks allow us to write code that tells React what we want. With class components, however, we need to tell React how to do things. As a result, Hooks are much more declarative than class components, making them a better fit in the React ecosystem.

Hooks being declarative also means that React can do various optimizations on our code, since it is easier to analyze functions and function calls rather than classes and their complex `this` behavior. Furthermore, Hooks make it easier to abstract and share common stateful logic between components. By using Hooks, we can avoid render props and higher-order components.

We can see that Hooks not only make our code more concise, and are easier to reason about for developers, but they also make the code easier to optimize for React.

# Migrating to Hooks

Now, you might be wondering: does that mean class components are deprecated, and we need to migrate everything to Hooks now? Of course not—Hooks are completely opt-in. You can try Hooks in some of your components without rewriting any of your other code. The React team also does not plan on removing class components at the moment.

There is no rush to migrate everything to Hooks right now. It is recommended that you gradually adopt Hooks in certain components where they will be most useful. For example, if you have many components that deal with similar logic, you can extract the logic to a Hook. You can also use function components with Hooks side by side with class components.

Furthermore, Hooks are 100% backward-compatible, and provide a direct API to all the React concepts that you already know about: **props**, **state**, **context**, **refs**, and **life cycle**. Furthermore, Hooks offer new ways to combine these concepts and encapsulate their logic in a much better way that does not lead to wrapper hell or similar problems. We are going to learn more about this later in the book.

# The Hooks mindset

The main goal of Hooks is to decouple stateful logic from rendering logic. They allow us to define logic in separate functions and reuse them across multiple components. With Hooks, we do not need to change our component hierarchy in order to implement stateful logic. There is no need to define a separate component that provides the state logic to multiple components anymore, we can simply use a Hook instead!

However, Hooks require a completely different mindset from classic React development. We should not think about the life cycle of components anymore. Instead, we should think about data flow. For example, we can tell Hooks to trigger when certain props or values from other Hooks change. We are going to learn more about this concept in Chapter 4, *Using the Reducer and Effect Hooks*. We should also not split components based on life cycle anymore. Instead, we can use Hooks to deal with common functionalities, such as fetching data, or setting up a subscription.

# Rules of Hooks

Hooks are very flexible. However, there are certain limitations to using Hooks, which we should always keep in mind:

*   Hooks can only be used in function components, not in class components
*   The order of Hook definitions matters, and needs to stay the same; thus, we cannot put Hooks in `if` conditionals, loops, or nested functions

We are going to discuss these limitations in more detail, as well as how to work around them, throughout this book.

# Overview of various Hooks

As we learned in the previous section, Hooks provide a direct API to all React concepts. Furthermore, we can define our own Hooks in order to encapsulate logic without having to write a higher-order component, which causes a wrapper hell. In this section, we are going to give an overview of various Hooks, which we are going to learn about throughout the book.

# Hooks provided by React

React already provides various Hooks for different functionalities. There are three basic Hooks, and a handful of additional Hooks.

# Basic Hooks

Basic Hooks provide the most commonly needed functionalities in stateful React apps. They are as follows:

*   `useState`
*   `useEffect`
*   `useContext`

Let's take a look at each of these in the following sections.

# useState

We have already used this Hook. It returns a stateful value (`state`) and a setter function (`setState`) in order to update the value.

The `useState` Hook is used to deal with `state` in React. We can use it as follows:

```

import { useState } from 'react'

const [ state, setState ] = useState(initialState)

```

The `useState` Hook replaces `this.state` and `this.setState()`.

# useEffect

This Hook works similarly to adding a function on `componentDidMount` and `componentDidUpdate`. Furthermore, the Effect Hook allows for returning a cleanup function from it, which works similarly to adding a function to `componentWillUnmount`.

The `useEffect` Hook is used to deal with effectful code, such as timers, subscriptions, requests, and so on. We can use it as follows:

```

import { useEffect } from 'react'

useEffect(didUpdate)

```

The `useEffect` Hook replaces the `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` methods.

# useContext

This Hook accepts a context object and returns the current context value.

The `useContext` Hook is used to deal with context in React. We can use it as follows:

```

import { useContext } from 'react'

const value = useContext(MyContext)

```

The `useContext` Hook replaces context consumers.

# Additional Hooks

Additional Hooks are either more generic variants of basic Hooks or are needed for certain edge cases. The additional Hooks we are going to look at are as follows:

*   `useRef`
*   `useReducer`
*   `useMemo`
*   `useCallback`
*   `useLayoutEffect`
*   `useDebugValue`

Let's dive deeper into these additional Hooks in the following sections.

# useRef

This Hook returns a mutable `ref` object, where the `.current` property is initialized to the passed argument (`initialValue`). We can use it as follows:

```

import { useRef } from 'react'

const refContainer = useRef(initialValue)

```

The `useRef` Hook is used to deal with references to elements and components in React. We can set a reference by passing the `ref` prop to an element or a component, as follows: `<ComponentName ref={refContainer} />`

# useReducer

This Hook is an alternative to `useState`, and works similarly to the Redux library. We can use it as follows:

```

import { useReducer } from 'react'

const [ state, dispatch ] = useReducer(reducer, initialArg, init)

```

The `useReducer` Hook is used to deal with complex state logic.

# useMemo

Memoization is an optimization technique where the result of a function call is cached, and is then returned when the same input occurs again. The `useMemo` Hook allows us to compute a value and memoize it. We can use it as follows:

```

import { useMemo } from 'react'

const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b])

```

The `useMemo` Hook is useful for optimization when we want to avoid re-executing expensive operations.

# useCallback

This Hook allows us to pass an inline callback function, and an array of dependencies, and will return a memoized version of the callback function. We can use it as follows:

```

import { useCallback } from 'react'

const memoizedCallback = useCallback(

() => {

doSomething(a, b)

},

[a, b]

)

```

The `useCallback` Hook is useful when passing callbacks to optimized child components. It works similarly to the `useMemo` Hook, but for callback functions.

# useLayoutEffect

This Hook is identical to `useEffect`, but it only fires after all **Document Object Model** (**DOM**) mutations. We can use it as follows:

```

import { useLayoutEffect } from 'react'

useLayoutEffect(didUpdate)

```

The `useLayoutEffect` Hook can be used to read information from the DOM.

Use the `useEffect` Hook when possible, because `useLayoutEffect` will block visual updates and slow down your application.

Finally, we are going to take a look at the last Hook provided by React at the time of writing.

# useDebugValue

This Hook can be used to display a label in React DevTools when creating custom Hooks. We can use it as follows:

```

import { useDebugValue } from 'react'

useDebugValue(value)

```

Make sure to use this Hook in custom Hooks to display the current state of your Hooks, as it will make it easier to debug them.

# Community Hooks

In addition to all the Hooks that React provides, there are already plenty of libraries that have been published by the community. These libraries also provide Hooks. The Hooks we are going to look into are as follows:

*   `useInput`
*   `useResource`
*   `useDimensions`
*   Navigation Hooks
*   Life cycle Hooks
*   Timer Hooks

Let's see an overview of what these Hooks are in the following sections.

# useInput

This Hook is used to easily implement input handling, and to synchronize the state of an `input` field with a variable. It can be used as follows:

```

import { useInput } from 'react-hookedup'

function App () {

const { value, onChange } = useInput('')

return <input value={value} onChange={onChange} />

}

```

As we can see, Hooks greatly simplify dealing with input fields in React.

# useResource

This Hook can be used to implement asynchronous data loading via requests in our application. We can use it as follows:

```

import { useRequest } from 'react-request-hook'

const [profile, getProfile] = useResource(id => ({

url: `/user/${id}`,

方法：'GET'

})

```

As we can see, using a special Hook for dealing with fetching data is quite simple.

# Navigation Hooks

These Hooks are part of the Navi library, and are used to implement routing via Hooks in React. The Navi library provides many more routing-related Hooks. We are going to learn about routing via Hooks, in depth, later on in the book. We can use them as follows:

```

import { useCurrentRoute, useNavigation } from 'react-navi'

const { views, url, data, status } = useCurrentRoute()

const { navigate } = useNavigation()

```

As we can see, Hooks make routing much easier to deal with.

# Life cycle Hooks

The `react-hookedup` library provides various Hooks, including all life cycle listeners for React.

Please note that it is not recommended to think in terms of a component life cycle when developing with Hooks. These Hooks just provide a quick way to refactor existing components to Hooks. However, when developing new components, it is recommended that you think about data flow and dependencies, rather than life cycles.

Here, we list two of them, but the library actually provides many more Hooks, which we will learn about later on. We can use the Hooks provided by `react-hookedup` as follows:

```

import { useOnMount, useOnUnmount } from 'react-hookedup'

useOnMount(() => { ... })

在卸载时使用 useOnUnmount(() => { ... })

```

As we can see, Hooks can directly replace life cycle methods in class components.

# Timer Hooks

The `react-hookedup` library also provides Hooks for `setInterval` and `setTimeout`. These work similarly to calling `setTimeout` or `setInterval` directly, but as a React Hook, which will persist between re-renders. If we directly defined timers in our function component without Hooks, we would be resetting the timer every time the component re-renders.

We can pass the time in milliseconds as a second argument. We can use them as follows:

```

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
