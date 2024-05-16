# 第六章：数据传输模式

在本章中，我们将学习如何在 React Native 应用程序中发送和接收数据。首先，我们将使我们的应用程序更加动态，并且依赖于后端服务器。您将了解到 Thunk 模式，它非常适合 Flux。然后，我们将深入研究一个更高级的库，redux-saga，它基于效果模式。这两种解决方案都将使我们的应用程序能够与服务器无缝交换数据。我还会给您一点关于更高级通信模式的介绍，比如`HATEOAS`和`GraphQL`。尽管这两种模式对于 React Native 开发人员来说很少是关键的，但如果有一天这些模式在 React Native 世界中变得流行，您会发现理解起来更容易。

在本章中，您将学习如何做以下事情：

+   创建一个假的 API

+   从后端获取数据并将其存储在应用程序中

+   设计动作创建者并将获取逻辑与容器解耦

+   使用 Redux Thunk 来有条件地分发动作

+   编写自己的迭代器和生成器

+   从大量依赖于生成器的 saga 中受益

# 准备工作

为了在不依赖外部来源的情况下测试各种 API，我们将创建我们自己的本地 API。您不需要了解任何后端语言，也不需要知道如何公开 API。在本章中，我们将使用一个特殊的库，该库根据我们提供的 JSON 文件构建 API。

到目前为止，我们已经制作了一个漂亮的应用程序来显示任务。现在，我们不再加载本地数据文件，而是使用我们自己的 REST API。克隆任务应用程序以开始。（我将使用第五章中示例二的代码目录，*存储模式*。）

**表述性状态转移**（**REST**）是对 Web 服务施加约束的一组规则。其中一个关键要求是无状态性，这保证了服务器不会存储客户端的数据，而是仅依赖于请求数据。这应该足以向客户端发送回复。

为了创建一个假的 API，我们将使用`json-server`库。该库需要一个 JSON 文件；大多数示例都将其称为`db.json`。根据该文件，该库创建一个静态 API，以响应请求发送数据。

让我们从使用以下命令安装库开始：

```jsx
yarn global add json-server
```

如果您喜欢避免使用`global`，请记住在以下脚本中提供`node_modules/json-server/bin`的相对路径。

库的 JSON 文件应该如下所示：

```jsx
{
  "tasks": [
    // task objects separated by comma 
  ]
}
```

幸运的是，我们的`tasks.json`文件符合这个要求。我们现在可以启动我们的本地服务器。打开`package.json`并添加一个名为`server`的新脚本，如下所示：

```jsx
// src / Chapter 6 / Example 1 / package.jsonn
// ...
"scripts": {
  // ...   "server": "json-server --watch ./src/data/tasks.json" },
// ...
```

现在可以输入`yarn run server`来启动它。数据将在`http://localhost:3000/tasks`上公开。只需使用浏览器访问 URL 以检查是否有效。正确设置的服务器应该打印出以下数据：

```jsx
[
  {
    "name": "Task 1",
    "description": "Task 1 description",
    "likes": 239
  },
  // ... other task objects
]
```

我们现在可以进一步学习如何使用端点。

# 使用内置函数获取数据

首先，让我们从一些相当基础的东西开始。React Native 实现了 Fetch API，这在现在是用于进行 REST API 调用的标准。

# 重构为活动指示器

目前，在`taskReducer.js`文件中从文件加载了默认的任务列表。老实说，从文件或 API 加载都可能耗时。最好最初将任务列表设置为空数组，并通过旋转器或文本消息向用户提供反馈，告知他们数据正在加载。我们将在使用 Fetch API 时实现这一点。

首先，从 reducer 中的文件中删除数据导入。将声明更改为以下内容：

```jsx
(state = Immutable.List([...data.tasks]), action) => {
    // ...
}
```

并用此片段中的代码替换它：

```jsx
(state = Immutable.List([]), action) => {
    // ...
}
```

从文件加载数据也是一种副作用，并且应该遵循与数据获取类似的限制模式。不要被我们以前用于同步加载数据的实现所愚弄。这个快捷方式只是为了集中在特定的学习材料上。

启动应用程序以查看空列表。现在让我们添加一个加载指示器，如下所示：

```jsx
import { View, Text, StyleSheet, ActivityIndicator } from 'react-native';
// ...
const TaskList = ({ tasks, isLoading }) => (
    <View>
  {isLoading
            ? <ActivityIndicator size="large" color="#0000ff" />
  : tasks.map((task, index) => (
                // ...   ))
        }
    </View> ); 
```

在某些情况下，如果加载时间很长，你需要处理一个更复杂的情况：数据正在加载，但用户可能同时添加任务。在以前的实现中，直到从服务器检索到数据之前，任务才会显示出来。这个简单的解决方法是，如果我们有任何任务，无论`isLoading`属性如何，都始终显示任务，这意味着期望有其他一些数据：

```jsx
// src / Chapter 6 / Example 2 / src / views / TaskList.js
const TaskList = ({ tasks, isLoading }) => (
    <View>
  {isLoading && <ActivityIndicator size="large" color="#0000ff" />}
 {tasks.map((task, index) => (
            // ...   ))}
    </View> );
```

由于我们有一个根据`isLoading`属性显示的加载指示器，我们需要考虑我们的获取过程可能产生的其他状态。

# 处理错误情况

在大多数情况下，Fetch 将需要三种状态：

+   **开始**：开始获取，应导致`isLoading`为`true`

+   **成功**：成功获取数据

+   **错误**：Fetch 无法检索数据；应显示适当的错误消息

我们需要处理的最后一个状态是错误。在用户体验指南方面，有几种方法可以处理这个问题：

+   在列表中显示错误消息 - 这为那些关心表中数据的人提供了一个清晰的消息。它可能包括一个可点击的链接或一个重试按钮。您可以将此方法与后续的方法混合使用。

+   在失败时显示浮动通知 - 这在一个角落显示有关错误的消息。消息可能在几秒钟后消失。

+   显示错误模态 - 这会阻止用户通知他们有关错误；它可能包含重试和解除等操作。

我想在这里采取的方法是第一种。这种方法相当容易实现 - 我们需要添加一个`error`属性，并根据它显示消息：

```jsx
const TaskList = ({
    tasks, isLoading, hasError, errorMsg
}) => (
    <View>
  {hasError &&
            <View><Text>{errorMsg}</Text></View>}
        {hasError && isLoading &&
            <View><Text>Fetching again...</Text></View>}
        {isLoading && <ActivityIndicator size="large" color="#0000ff" />}
        {tasks.map((task, index) => (
            // ...   ))}
    </View> );
// ... TaskList.defaultProps = {
    errorMsg: 'Error has occurred while fetching tasks.' };
```

# 天真的有状态组件获取

现在，让我们获取一些数据并使我们的标记完全可用。首先，我们将遵循 React 初学者的方法：在一个有状态组件中使用`fetch`。在我们的情况下，它将是`App.js`：

```jsx
// src / Chapter 6 / Example 2 / src / App.js
class TasksFetchWrapper extends React.Component {
    constructor(props) {
        super(props);
        // Default state of the component
  this.state = {
            isLoading: true,
  hasError: false,
  errorMsg: '',
  tasks: props.tasks
  };
  }

    componentDidMount() {
        // Start fetch and on completion set state to either data or
        // error
        return fetch('http://localhost2:3000/tasks')
            .then(response => response.json())
            .then((responseJSON) => {
                this.setState({
                    isLoading: false,
  tasks: Immutable.List(responseJSON)
                });
  })
            .catch((error) => {
                this.setState({
                    isLoading: false,
  hasError: true,
  errorMsg: error.message
  });
  });
  }

    render = () => (
        <AppView
  tasks={this.state.tasks}
            isLoading={this.state.isLoading}
            hasError={this.state.hasError}
            errorMsg={this.state.errorMsg}
        />
  ); }
  // State from redux passed to wrapper. const mapStateToProps = state => ({ tasks: state.tasks }); const AppContainer = connect(mapStateToProps)(TasksFetchWrapper);
```

这种方法有一些缺点。首先，它不遵循 Fetch API 文档。让我们阅读这个关键的引用：

“从 fetch 返回的 Promise 不会在 HTTP 错误状态下拒绝，即使响应是 HTTP 404 或 500。相反，它将正常解析（ok 状态设置为 false），并且只有在网络故障或任何阻止请求完成时才会拒绝。”

- *可用的 Fetch API 文档：*

[`developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)。

如您所见，前面的实现缺乏 HTTP 错误处理。

第二个问题是状态重复，我们维护了一个 Redux 状态，但然后将任务复制到本地组件状态，并甚至用已获取的内容覆盖它。我们可能更关心我们已经在任务中的内容，通过连接两个数组，并找到一种避免再次存储任务的方法。

此外，如果 Redux 状态发生变化，那么先前的组件将完全忽略更新。这太糟糕了，让我们找到一种解决方法。

# Thunk 模式和 Redux Thunk

在这一部分，我们将学习**Thunk 模式**以及如何在**Redux Thunk**库中使用它。首先，我们需要重构上一节中的天真和有缺陷的实现，改为使用 Redux。

# 将状态提升到 Redux

不要依赖组件状态，让我们将其提升到 Redux 存储中。注意我们在这里使用的`Immutable.Map`。此外，`ADD_TASK`动作现在使用`Immutable.js`的`update`函数：

```jsx
// src / Chapter 6 / Example 3 / src / reducers / taskReducer.js

const taskReducer = (state = Immutable.Map({
    entities: Immutable.List([])**,**
  isLoading: false,
  hasError: false,
  errorMsg: **''** }), action) => {
    switch (action.type) {
    case TasksActionTypes.ADD_TASK:
        if (!action.task.name) {
            return state;
  }
        return state.update('entities', entities => entities.push({
            name: action.task.name,
  description: action.task.description,
  likes: 0
  }));
  default:
        return state;
  }
}; 
```

由于我们已经改变了减速器，我们需要修复有状态的组件。它不应该有自己的状态，而是通过动作委托给 Redux 存储。然而，我们将稍后实现这些动作：

```jsx
// src / Chapter 6 / Example 3 / src / App.js
class TasksFetchWrapper extends React.Component {
    componentDidMount() {
        TaskActions.fetchStart();
  return fetch('http://localhost:3000/tasks')
            .then(response => response.json())
            .then((responseJSON) => {
                TaskActions.fetchComplete(Immutable.List(responseJSON));
  })
            .catch((error) => TaskActions.fetchError(error));
  }

    render = () => <AppView tasks={this.props.tasks} />; }
```

将获取逻辑移动到单独的服务是明智的。这将使其他组件在需要触发获取时共享相同的功能。这是你的作业。

你可以将动作分派到构造函数而不是依赖于`componentDidMount`。然而，这可能会引发重构为函数组件的诱惑。这将是一场灾难，因为你将在每次重新渲染时开始获取。此外，`componentDidMount`对我们来说更安全，因为在动作的上下文中，如果有任何可能减慢应用程序的计算，我们可以 100%确定用户已经看到`ActivityIndicator`。

现在，转到动作的实现。你应该能够自己编写它们。如果遇到任何问题，请参阅`src / Chapter 6 / Example 3 / src / data / TaskActions.js`。现在我们将专注于扩展减速器。这是相当多的工作，因为我们需要处理所有三种动作类型：`FETCH_START`，`FETCH_COMPLETE`和`FETCH_ERROR`，如下所示：

```jsx
// src / Chapter 6 / Example 3 / src / reducers / taskReducer.js
const taskReducer = (state = Immutable.Map({
    // ...  }), action) => {
    switch (action.type) {
    case TasksActionTypes.ADD_TASK: {
        // ...   }
    case TasksActionTypes.TASK_FETCH_START: {
        return state.update('isLoading', () => true);
  }
    case TasksActionTypes.TASK_FETCH_COMPLETE: {
        const noLoading = state.update('isLoading', () => false);
  return noLoading.update('entities', entities => (
            // For every task we update the state
            // Homework: do this in bulk
            action.tasks.reduce((acc, task) => acc.push({
                name: task.name,
  description: task.description,
  likes: 0
  }), entities)
        ));
  }
    case TasksActionTypes.TASK_FETCH_ERROR: {
        const noLoading = state.update('isLoading', () => false);
  const errorState = noLoading.update('hasError', () => true);
  return errorState.update('errorMsg', () => action.error.message);
  }
    default: {
        return state;
  }
    }
};
```

基本上就是这样。最后，你还需要更新视图以使用新的结构`Immutable.Map`，如下所示：

```jsx
// src / Chapter 6 / Example 3 / src / views / AppView.js
// ...
<TaskList
  tasks={props.tasks.get('entities')}
    isLoading={props.tasks.get('isLoading')}
    hasError={props.tasks.get('hasError')}
    errorMsg={props.tasks.get('errorMsg')}
/>
// ... 
```

这段代码需要进行一些改进。我现在不会触及它们，因为那些是高级主题，涉及更一般的 JavaScript 函数式编程概念。你将在第八章中学习有关镜头和选择器的内容，*JavaScript 和 ECMAScript 模式*。

# 重构为 Redux 的好处

可能很难看到先前重构的好处。其中一些重构可能在几天后才会显现出来。例如，需要在特定事件上重新获取任务。此事件发生在应用程序的完全不同部分，并且与任务列表无关。在天真的实现中，您需要处理更新过程并保持一切更新。您还需要向另一个组件公开`fetch`函数。这将紧密耦合这两者。灾难。相反，正如您所看到的，您可能更喜欢将获取逻辑复制到第二个独立的组件中。再次，您最终会出现代码重复。因此，您将创建一个由这两个组件共享的父服务。不幸的是，获取与状态紧密耦合，因此您还将状态移动到服务中。然后，您将进行一些技巧，例如使用闭包在服务中存储数据。正如您所看到的，这是这些问题的一个平稳解决方案。

当使用 Redux 存储时，您只有一个通过 reducer 更新的集中状态。获取是使用精心设计的操作将数据发送到 reducer。获取可以在一个单独的服务中执行，该服务由需要获取任务的组件共享。现在，我们将介绍一个使所有这些事情更清洁的库。

# 使用 Redux Thunk

在经典的 Redux 中，没有中间件，您无法调度不是纯对象的东西。使用 Redux Thunk，您可以通过调度函数延迟调度：

"Redux Thunk 中间件允许您编写返回函数而不是操作的操作创建者。thunk 可以用于延迟操作的调度，或者仅在满足某些条件时进行调度。内部函数接收存储方法`dispatch`和`getState`作为参数。"

- Redux Thunk 官方文档，网址：

[`github.com/reduxjs/redux-thunk`](https://github.com/reduxjs/redux-thunk)。

例如，您可以调度一个函数。这样的函数有两个参数：`dispatch`和`getState`。这个函数尚未到达 Redux reducer。它只延迟了老式的 Redux 调度，直到进行必要的检查，例如基于当前状态的检查。一旦我们准备好调度，我们就使用作为`function`参数提供的`dispatch`函数：

```jsx
function incrementIfOdd() {
  return (dispatch, getState) => {
    const { counter } = getState();

    if (counter % 2 === 0) {
      return;
    }

    dispatch(increment());
  };
}

dispatch(incrementIfOdd())
```

在前一节中，我指出`fetch`调用可以是一个单独的函数。如果你还没有做作业，这里是一个重构的例子：

```jsx
const fetchTasks = () => {
    TaskActions.fetchStart();
  return fetch('http://localhost:3000/tasks')
        .then(response => response.json())
        .then((responseJSON) => {
            TaskActions.fetchComplete(Immutable.List(responseJSON));
  })
        .catch(error => TaskActions.fetchError(error)); };   class TasksFetchWrapper extends React.Component {
 componentDidMount = () => this.props.fetchTasks();
  render = () => <AppView tasks={this.props.tasks} />; }

const mapStateToProps = state => ({ tasks: state.tasks }); const mapDispatchToProps = dispatch => ({ fetchTasks }); const AppContainer = connect(mapStateToProps, mapDispatchToProps)(TasksFetchWrapper);
```

然而，我们所谓的`ActionCreators`与`dispatch`紧密耦合，因此不仅创建动作，还有`dispatch`。让我们通过移除 dispatch 来放松它们的责任：

```jsx
// Before  const Actions = {
addTask(task) {
        AppDispatcher.dispatch({
type: TasksActionTypes.ADD_TASK,
  task
        });
  },
  fetchStart() {
        AppDispatcher.dispatch({
type: TasksActionTypes.TASK_FETCH_START
  });
  },
 // ...
}; 
// After
const ActionCreators = {
 addTask: task => ({
type: TasksActionTypes.ADD_TASK,
  task
   }),
  fetchStart: () => ({
type: TasksActionTypes.TASK_FETCH_START
  }),
 // ...
}; 
```

现在，我们需要确保将前面的动作分发到相关的位置。可以通过以下方式实现：

```jsx
const ActionTriggers = {
 addTask: dispatch => task => dispatch(ActionCreators.addTask(task)),
  fetchStart: dispatch => () => dispatch(ActionCreators.fetchStart()),
  fetchComplete: dispatch =>
        tasks => dispatch(ActionCreators.fetchComplete(tasks)),
  fetchError: dispatch =>
        error => dispatch(ActionCreators.fetchError(error))
};
```

对于有编程经验的人来说，这一步可能看起来有点像我们在重复自己。我们在重复函数参数，唯一得到的是调用分发。我们可以用函数模式来解决这个问题。这些改进将作为《第八章》*JavaScript 和 ECMAScript 模式*的一部分进行。

另外，请注意，在这本书中，我没有写很多测试。一旦你养成了写测试的习惯，你就会很快欣赏到这种易于测试的代码。

完成这些后，我们现在可以调整我们的容器组件，如下所示：

```jsx
// src / Chapter 6 / Example 4 / src / App.js export const fetchTasks = (dispatch) => {
    TaskActions.fetchStart(dispatch)();
  return fetch('http://localhost:3000/tasks')
        .then(response => response.json())
        .then(responseJSON =>
            TaskActions.fetchComplete(dispatch)(Immutable.List(responseJSON)))
        .catch(TaskActions.fetchError(dispatch)); };
// ... const mapDispatchToProps = dispatch => ({
fetchTasks: () => fetchTasks(dispatch),
  addTask: TaskActions.addTask(dispatch)
});  
```

好的，这是一个很好的重构，但 Redux Thunk 在哪里？这是一个非常好的问题。我故意延长了这个例子。在许多 React 和 React Native 项目中，我看到了对 Redux Thunk 和其他库的过度使用。我不希望你成为另一个不理解 Redux Thunk 目的并滥用其功能的开发人员。

Redux Thunk 主要让你有条件地决定是否要分发。通过 Thunk 函数访问`dispatch`并不是什么特别的事情。主要的好处是第二个参数`getState`。这让你可以访问当前状态，并根据那里的值做出决定。

这样强大的工具可能会导致你创建不纯的 reducer。怎么会呢？你会创建一个**setter reducer**，它的工作方式类似于类中的 set 函数。这样的 reducer 只会被调用来设置值；然而，值将在 Thunk 函数中计算，使用`getState`函数。这完全是反模式，可能会导致严重的竞争条件破坏。

现在我们知道了危险，让我们继续讨论 Thunk 的真正用途。想象一种情况，您希望有条件地做出决定。如何访问状态以进行`if`语句？一旦我们在 Redux 中使用`connect()`函数，这就变得复杂起来。我们传递给`connect`的`mapDispatchToProps`函数无法访问状态。但我们需要它，这就是 Redux Thunk 的一个有效用法。

以下是需要知道的：如果我们不能使用 Redux Thunk，我们如何制作一个逃生舱？我们可以将部分状态传递给`render`函数，然后使用预期的状态调用原始函数。`if`语句可以在 JSX 中使用常规的`if`。然而，这可能会导致严重的性能问题。

现在是时候在我们的情况下使用 Redux Thunk 了。您可能已经注意到我们的数据集不包含 ID。如果我们两次获取任务，这将是一个巨大的问题，因为我们没有机制告诉哪些任务已经添加，哪些已经存在于我们的 UI 中。当前的方法是添加所有获取到的任务，这将导致任务重复。我们破碎架构的第一个预防机制是在`isLoading`为`true`时停止获取。

现实生活中的情况要么使用 ID，要么在获取时刷新所有任务。如果是这样，`ADD_TASK`需要保证后端服务器中的更改。

在渐进式 Web 应用程序时代，我们需要进一步强调这个问题。考虑一种情况，即在添加新任务之前失去连接。如果您的 UI 在本地添加任务并安排后端更新，一旦网络连接解决，您可能会遇到竞争条件：这意味着任务在`ADD_TASK`更新在后端系统中传播之前被刷新。结果，您最终会得到一个任务列表，其中不包含添加的任务，直到您从后端重新获取所有任务。这可能是非常误导人的，不应该发生在任何金融机构中。

让我们实现这种天真的预防机制来说明 Redux Thunk 的能力。首先，使用以下命令安装库：

```jsx
yarn add redux-thunk
```

然后，我们需要将`thunk`中间件应用到 Redux 中，如下所示：

```jsx
// src / Chapter 6 / Example 4 / src / data / AppStore.js
import { combineReducers, createStore, applyMiddleware } from 'redux'; import thunk from 'redux-thunk';  // ... const store = createStore(rootReducer, applyMiddleware(thunk));  
```

从现在开始，我们可以调度函数。现在让我们修复我们的`fetch`函数，以避免多次请求：

```jsx
// src / Chapter 6 / Example 5 / src / App.js
export const fetchTasks = (dispatch, getState) => {
    if (!getState().tasks.isLoading) {
        // ...   }
    return null; };
// ... const mapDispatchToProps = dispatch => ({
    fetchTasks: () => dispatch(fetchTasks),
 // ...
});
```

正如您所看到的，这是一个非常简单的用例。请明智地使用 Redux Thunk，不要滥用它给您带来的力量。

# 理解 Thunk 模式

Thunk 是另一种模式，不特定于 React 或 Redux。实际上，在许多复杂的解决方案中，如编译器，它被广泛使用。

Thunk 是一种延迟评估的模式，直到无法避免为止。解释这一点的初学者示例之一是简单的加法。示例如下：

```jsx
// immediate calculation, x equals 3
let x = 1 + 2;

// delayed calculation until function call, x is a thunk
let x = () => 1 + 2;
```

一些更复杂的用法，例如在函数式语言中，可能会在整个语言中依赖于这种模式。因此，计算只有在最终应用层需要它们时才执行。通常情况下，不会进行提前计算，因为这样的优化是开发人员的责任。

# 传说模式和 Redux Saga

到目前为止，我们可以使用`fetch`执行简单的 API 调用，并且知道如何组织我们的代码以实现可重用性。然而，在某些领域，如果我们的应用程序需要，我们可以做得更好。在深入研究 Redux Saga 之前，我想介绍两种新模式：迭代器和生成器。

“处理集合中的每个项目是一个非常常见的操作。JavaScript 提供了许多迭代集合的方法，从简单的 for 循环到 map 和 filter。迭代器和生成器直接将迭代的概念引入核心语言，并提供了自定义 for...of 循环行为的机制。”

- MDN web 文档上的 JavaScript 指南：

[`developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators). [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)

# 迭代器模式简介

顾名思义，迭代器允许您遍历集合。为了能够这样做，集合需要实现一个可迭代接口。在 JavaScript 中，没有接口，因此迭代器只是实现了一个函数。

当对象知道如何一次从集合中访问项目，并在该序列内跟踪其当前位置时，该对象就是一个迭代器。在 JavaScript 中，迭代器是一个提供 next 方法的对象，该方法返回序列中的下一个项目。此方法返回一个具有两个属性的对象：done 和 value。

- MDN web 文档上的 JavaScript 指南

[`developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)

以下是 MDN web 文档中此类函数的示例：

```jsx
function createArrayIterator(array) {
    var nextIndex = 0;    return {
        next: function() {
            return nextIndex < array.length ?
                {value: array[nextIndex++], done: false} :
                {done: true};
  }
    }; }
```

# 生成器模式

生成器类似于迭代器；然而，在这里，你会在函数内部精心设计的断点上进行迭代。生成器返回一个迭代器。返回的迭代器在提到的断点上进行迭代，并且每次从函数中返回一些值。

为了表示该函数是一个生成器，我们使用特殊的`*****`符号，例如，`function* idGenerator()`。请在以下代码片段中找到一个示例生成器函数。生成器使用`yield`关键字来返回当前迭代步骤的值。如果调用了它的`next()`函数，迭代器将在下一行恢复，就像这样：

```jsx
function* numberGenerator(numMax) {
    for (let i = 0; i < numMax; i += 1) {
        yield console.log(i);
  }
}
const threeNumsIterator = numberGenerator(3); // logs 0 threeNumsIterator.next(); // logs 1 threeNumsIterator.next(); // logs 2 threeNumsIterator.next(); // logs nothing, the returned object contains a key 'done' set to true
threeNumsIterator.next(); 
```

首先，我们创建一个`generator`函数。`Generator`函数期望一个参数。根据提供的参数，生成器知道何时停止生成新的数字。在函数之后，我们创建一个示例数字迭代器并迭代其值。

# Redux Saga

Redux Saga 在很大程度上依赖于生成器模式。由于这种方法，我们可以将副作用完全解耦到行为就像是一个独立线程的 sagas 中。这是方便的，并且从长远来看，相对于 Thunk 函数提供了一些优势。其中一些依赖于可组合性，sagas 易于测试，并提供更清晰的流程来执行异步代码。现在这些可能听起来不清楚，所以让我们深入了解一下。

本书并不涉及太多关于 React、Redux 和 React Native 的测试。这个主题会让本书变得很长，我认为它值得有一本单独的书。然而，我会强调测试代码的重要性。这个信息框是为了提醒你在 Redux Sagas 中进行测试。在互联网的不同地方（GitHub、论坛、Stack Overflow）我一遍又一遍地看到这个提到：sagas 比 Thunks 更容易测试。你可以自己验证一下，你不会后悔的。

首先，完成安装库和应用中间件的初学者步骤。这些步骤可以在官方的 Redux Saga README 文件中找到，网址为[`redux-saga.js.org/`](https://redux-saga.js.org/)。

现在是时候创建第一个 saga 并将其添加到我们的`rootSaga`中了。还记得获取任务的情况吗？它们可以从许多地方（许多解耦的小部件或功能）请求。saga 的方法与我们之前的解决方案类似，所以让我们看看它如何在以下示例中实现：

```jsx
// src / Chapter 6 / Example 6 / src / sagas / fetchTasks.js
function* fetchTasks() {
    const tasks = yield call(ApiFetch, 'tasks');
  if (tasks.error) {
        yield put(ActionCreators.fetchError(tasks.error));
  } else {
        const json = yield call([tasks.response, 'json']);
  yield put(ActionCreators.fetchComplete(Immutable.List(json)));
  }
}

// whereas ApiFetch is our own util function
// you will want to make a separate file for it
// and take care of environmental variables to determine right endpoint
const ApiFetch = path => fetch(`http://localhost:3000/${path}`)
    .then(response => ({ response }))
    .catch(error => ({ error }));
```

我们的`fetchTasks` saga 非常简单：首先，它获取任务，然后检查是否发生了错误，然后要么分派一个带有获取的数据附加的错误事件，要么分派一个成功事件。

我们如何触发`fetchTasks` saga？为了说服你 saga 的强大之处，让我们更进一步。假设我们的代码库是解耦的，一些功能几乎同时请求任务。我们如何防止触发多个获取任务的作业？Redux Saga 库为此提供了现成的解决方案：`throttle`函数。

"throttle(ms, pattern, saga, ...args) 在与模式匹配的存储器上分派一个动作，然后在生成任务后，它仍然接受传入的动作到底层缓冲区，最多保留 1 个（最近的一个），但同时在 ms 毫秒内不生成新任务（因此它的名称是 - throttle）。其目的是在处理任务时忽略一定时间内的传入动作。"

- 官方 Redux Saga 文档:

[`redux-saga.js.org/docs/api/`](https://redux-saga.js.org/docs/api/).

我们的用例非常简单：

```jsx
// src / Chapter 6 / Example 6 / src / sagas / fetchTasks.js
function* watchLastFetchTasks() {
    yield throttle(2000, TasksActionTypes.TASK_FETCH_START, fetchTasks); }
```

`fetchTasks`函数将在`TASK_FETCH_START`事件上执行。在两秒内，相同的事件不会导致另一个`fetchTasks`函数的执行。

就是这样。最后的几件事之一是将前面的 saga 添加到`rootSaga`中。这不是一个非常有趣的部分，但是如果你感兴趣，我建议你在代码存储库中查看完整的示例，该示例可在[`github.com/Ajdija/hands-on-design-patterns-with-react-native`](https://github.com/Ajdija/hands-on-design-patterns-with-react-native)上找到。

# Redux Saga 的好处

在更复杂的应用程序中，具有明确定义的例程，Redux Saga 比 Redux Thunk 更出色。一旦你遇到需要取消、重新运行或回复流程的一部分，就不会立即明显地知道如何使用 Thunk 或纯 Redux 来完成这些操作。使用可组合的 saga 和良好维护的迭代器，你可以轻松地完成这些操作。即使官方文档也提供了这些问题的解决方案。（有关参考，请参阅本章末尾的*进一步阅读*部分。）

这样一个强大库的阴暗面在于它在旧应用程序中的使用可能会出现问题。这些应用程序可能以基于 promise 或 Thunk 的方式编写功能，可能需要进行重大重构才能与在新应用程序中找到的与 sagas 的使用方式相匹配。例如，从 Thunk 函数调用 saga 并不容易，也不能像在 sagas 中等待分发的函数那样等待 promise。可能有很好的方法来连接这两个世界，但真的值得吗？

# 摘要

在这一章中，我们重点关注了网络模式和随之而来的副作用。我们经历了简单的模式，然后使用了市场上可用的工具。您已经了解了 Thunk 模式，以及迭代器和生成器模式。这三种模式在您未来的编程生涯中都将非常有用，无论是在 React Native 中还是其他地方。

至于 React 生态系统，您已经了解了 Redux Thunk 和 Redux Saga 库的基础知识。它们都解决了大规模应用程序所面临的一些挑战。明智地使用它们，并牢记我在本章中提出的所有警告。

现在我们知道如何显示数据，样式化数据和获取数据，我们已经准备好学习一些应用程序构建模式。特别是在下一章中，您将学习导航模式。在 React Native 中，有很多解决这些问题的解决方案，我很乐意教您如何选择与您项目需求匹配的解决方案。

# 进一步阅读

+   编写测试-Redux 官方文档：

[`redux.js.org/recipes/writing-tests`](https://redux.js.org/recipes/writing-tests).

+   实现撤销历史-Redux 官方文档：

[`redux.js.org/recipes/implementing-undo-history`](https://redux.js.org/recipes/implementing-undo-history).

+   服务器渲染-Redux 官方文档：

[`redux.js.org/recipes/server-rendering`](https://redux.js.org/recipes/server-rendering).

+   规范化状态-Redux 官方文档：

[`redux.js.org/recipes/structuring-reducers/normalizing-state-shape`](https://redux.js.org/recipes/structuring-reducers/normalizing-state-shape).

这在网络模式的背景下非常重要。从后端系统获取的一些数据将需要进行规范化处理。

+   异步操作-Redux 官方文档：

[`redux.js.org/advanced/async-actions`](https://redux.js.org/advanced/async-actions).

+   Redux Saga 食谱-Redux Saga 官方文档：

[`redux-saga.js.org/docs/recipes/`](https://redux-saga.js.org/docs/recipes/)。

这个资源特别有价值，因为它提供了使用 saga 进行节流、去抖动和撤销的食谱。

+   Redux Saga 通道-Redux Saga 官方文档：

“到目前为止，我们已经使用‘take’和‘put’效果与 Redux Store 进行通信。通道将这些效果泛化为与外部事件源或 Sagas 之间进行通信。它们还可以用于从 Store 中排队特定的操作。”

- Redux Saga 官方文档：

[`redux-saga.js.org/docs/advanced/Channels.html`](https://redux-saga.js.org/docs/advanced/Channels.html)。

+   关于 Thunk、saga、抽象和可重用性的惯用 redux 思想：

[`blog.isquaredsoftware.com/2017/01/idiomatic-redux-thoughts-on-thunks-sagas-abstraction-and-reusability/`](https://blog.isquaredsoftware.com/2017/01/idiomatic-redux-thoughts-on-thunks-sagas-abstraction-and-reusability/)。

+   资源库：React Redux 链接/Redux 副作用：

[`github.com/markerikson/react-redux-links/blob/master/redux-side-effects.md`](https://github.com/markerikson/react-redux-links/blob/master/redux-side-effects.md)。

+   关于 Saga 的 Saga：

“术语‘saga’通常在 CQRS 讨论中用来指代协调和路由有界上下文和聚合之间的消息的一段代码。然而，[...]我们更倾向于使用术语‘过程管理器’来指代这种类型的代码构件。”

关于 Saga 的 Saga-Microsoft 文档：

[`docs.microsoft.com/en-us/previous-versions/msp-n-p/jj591569(v=pandp.10)`](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/jj591569(v=pandp.10))。

+   GraphQL-另一种处理副作用的方法。GraphQL 是一个用于 API 的查询语言，既可以用于前端，也可以用于后端。在这里了解更多：

[`graphql.org/learn/`](https://graphql.org/learn/)。

+   Redux Observable-Thunk 和 saga 的竞争对手。介绍了响应式编程模式：

[`github.com/redux-observable/redux-observable`](https://github.com/redux-observable/redux-observable)。

还请查看 RxJS，这是 JavaScript 的响应式编程库：

[`github.com/reactivex/rxjs`](https://github.com/reactivex/rxjs)。

+   表述性状态转移：

[`en.wikipedia.org/wiki/Representational_state_transfer`](https://en.wikipedia.org/wiki/Representational_state_transfer)。

+   HATEOAS（REST 架构的一个组件）：

https://en.wikipedia.org/wiki/HATEOAS
