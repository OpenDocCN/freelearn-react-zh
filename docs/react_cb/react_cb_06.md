# 第六章：使用 Redux Form 创建表单

在本章中，将涵盖以下配方：

+   使用本地状态创建受控表单

+   使用 Redux Form 构建表单

+   在表单中实现验证

# 介绍

表单是任何 Web 应用程序的基本部分，在接下来的配方中，我们将学习如何使用带有或不带有 Redux Form 的表单。

# 使用本地状态创建受控表单

对于这个配方，我们将创建一个简单的待办事项列表，使用我们的本地状态来使用表单。

# 准备工作

对于这个配方，我们需要安装`uuid`包来生成随机 ID，如下面的代码所示：

```jsx
npm install uuid
```

# 如何做…

让我们按照以下步骤创建我们的受控表单：

1.  首先，对于待办事项列表，我们将在`src/components/Todo/index.jsx`中创建一个名为`Todo`的新组件。我们将使用的框架如下所示：

```jsx
import React, { Component } from 'react';
import uuidv4 from 'uuid/v4';
import './Todo.css';

class Todo extends Component {
  constructor() {
    super();

    // Initial state...
    this.state = {
      task: '',
      items: []
    };
  }

  render() {
    return (
      <div className="Todo">
        <h1>New Task:</h1>

        <form onSubmit={this.handleOnSubmit}>
          <input value={this.state.task} />
        </form>
      </div>
    );
  }
}

export default Todo;
```

文件：src/components/Todo/index.jsx

1.  请记住，我们需要将组件添加到我们的`src/routes.jsx`中，如下面的代码所示：

```jsx
// Dependencies
import React from 'react';
import { Route, Switch } from 'react-router-dom';

// Components
import App from './components/App';
import About from './components/About';
import Coins from './components/Coins';
import Contact from './components/Contact';
import Home from './components/Home';
import Notes from './components/Notes';
import Todo from './components/Todo';
import Error404 from './components/Error/404';

const AppRoutes = () => (
  <App>
    <Switch>
      <Route path="/" component={Home} exact />
      <Route path="/about" component={About} exact />
      <Route path="/coins" component={Coins} exact />
      <Route path="/contact" component={Contact} exact />
      <Route path="/notes" component={Notes} exact />
      <Route path="/notes/:noteId" component={Notes} exact />
      <Route path="/todo" component={Todo} exact />
      <Route component={Error404} />
    </Switch>
  </App>
);

export default AppRoutes;
```

文件：src/routes.jsx

1.  如果你去`/todo`，你会看到输入框，但你可能会注意到无法在上面写任何东西，这是因为我们将我们的本地状态(`this.state.task`)连接到我们的输入值，但我们需要一个`onChange`函数来更新我们的状态，如下面的代码所示：

```jsx
import React, { Component } from 'react';
import uuidv4 from 'uuid/v4';
import './Todo.css';

class Todo extends Component {
  constructor() {
    super();

    // Initial state...
    this.state = {
      task: '',
      items: []
    };
  }

  handleOnChange = e => {
    const { target: { value } } = e;

    // Updating our task state with the input value...
    this.setState({
      task: value
    });
  }

  render() {
    return (
      <div className="Todo">
        <h1>New Task:</h1>

        <form onSubmit={this.handleOnSubmit}>
          <input 
 value={this.state.task} 
 onChange={this.handleOnChange} 
          />
        </form>
      </div>
    );
  }
}

export default Todo;
```

文件：src/components/Todo/index.jsx

1.  现在我们可以在输入框中写任何东西，如下截图所示：

![](img/5f6a8fe8-212c-425e-9885-da4300381960.png)

1.  为了保存输入的项目，我们需要在`form`标签中添加一个`onSubmit`函数，在这里我们需要更新我们的本地状态以将项目推送到`items`数组中。此外，我们需要包括我们的`List`组件，我们将在其中显示所有的项目。完整的代码如下：

```jsx
import React, { Component } from 'react';
import uuidv4 from 'uuid/v4';
import List from './List';
import './Todo.css';

class Todo extends Component {
  constructor() {
    super();

    // Initial state...
    this.state = {
      task: '',
      items: []
    };
  }

  handleOnChange = e => {
    const { target: { value } } = e;

    // Updating our task state with the input value...
    this.setState({
      task: value
    });
  }

  handleOnSubmit = e => {
    // Prevent default to avoid the actual form submit...
    e.preventDefault();

    // Once is submitted we reset the task value and we push 
    // task to the items array.
    this.setState({
      task: '',
      items: [
        ...this.state.items,
        {
          id: uuidv4(),
          task: this.state.task,
          complete: false
        }
      ]
    });
  }

  render() {
    return (
      <div className="Todo">
        <h1>New Task:</h1>

        <form onSubmit={this.handleOnSubmit}>
          <input 
            value={this.state.task} 
 onChange={this.handleOnChange} 
          />
        </form>

        <List items={this.state.items} />
      </div>
    );
  }
}

export default Todo;
```

文件：src/components/Todo/index.jsx

1.  我们的`List`组件将是一个功能性组件，我们将在其中渲染一个项目列表，如下面的代码所示：

```jsx
import React from 'react';

const List = props => (
  <ul>
    {props.items.map((item, key) => (
      <li key={key}>
        {item.task}
      </li>
    ))}
  </ul>
);

export default List;
```

文件：src/components/Todo/List.jsx

1.  最后，我们需要添加我们的 CSS 文件，如下面的代码所示：

```jsx
.Todo {
    background-color: #f5f5f5;
    border-radius: 4px;
    border: 1px solid #e3e3e3;
    box-shadow: inset 0 1px 1px rgba(0,0,0,.05);
    margin-bottom: 20px;
    margin: 50px auto;
    min-height: 20px;
    padding: 19px;
    text-align: left;
    width: 70%;
}

.Todo ul {
    margin: 20px 0px;
    padding: 0;
    list-style: none;
}

.Todo ul li {
    background-color: #fff;
    border: 1px solid #ddd;
    display: flex;
    justify-content: space-between;
    margin-bottom: -1px;
    padding: 10px 15px;
    position: relative;
}

.Todo form input {
    background-color: #fff;
    border-radius: 4px;
    border: 1px solid #ccc;
    box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
    color: #555;
    font-size: 14px;
    height: 34px;
    line-height: 34px;
    padding: 6px 12px;
    width: 40%;
}

.Todo form button {
    background: #2ba6cb;
    border: 1px solid #1e728c;
    box-shadow: 0 1px 0 rgba(255, 255, 255, 0.5) inset;
    color: white;
    cursor: pointer;
    display: block;
    font-size: 14px;
    font-weight: bold;
    line-height: 1;
    margin: 20px auto;
    padding: 10px 20px 11px;
    position: relative;
    text-align: center;
    text-decoration: none;
}
```

文件：src/components/Todo/Todo.css

1.  我们的待办事项列表将如下截图所示：

![](img/c5026115-303f-4909-a8bc-410f3a67552b.png)

# 它是如何工作的…

正如您所看到的，从输入表单中检索值的唯一方法是使用本地状态和`onChange`函数来更新输入的值。如果您将状态连接到输入值，但没有添加`onChange`回调来更新它，您将无法写入任何内容，因为虚拟 DOM 没有被更新，唯一的方法是通过更新我们的本地状态。

# 使用 Redux Form 构建表单

**Redux Form**通常用于大型表单或步骤表单，因为它具有 Redux 状态来保持整个表单的值。此外，Redux Form 很方便地验证数据并有效地处理提交。

# 准备工作

对于这个示例，我们需要安装 Redux Form 如下：

```jsx
 npm install redux-form
```

# 如何做到...

对于这个示例，我们将使用 Redux Form 来创建相同的 Todo 列表：

1.  安装了 Redux Form 之后，我们需要对上一个示例的代码进行一些修改以实现 Redux Form。我们需要做的第一件事是为我们的表单添加一个 reducer。为此，我们需要从`redux-form`中导入一个 reducer，并且我们可以将变量的名称更改为`formReducer`以更明确，然后将 reducer 作为一个表单添加到我们的`combineReducers`中，如下面的代码所示：

```jsx
// Dependencies
import { combineReducers } from 'redux';
import { reducer as formReducer } from 'redux-form';

// Components Reducers
import coins from '../../reducers/coinsReducer';

// Shared Reducers
import device from './deviceReducer';

const rootReducer = combineReducers({
  coins,
  device,
  form: formReducer
});

export default rootReducer;
```

文件：src/shared/reducers/index.js

1.  通常，我们使用 Redux Form 创建的所有表单都需要它们的组件，这意味着我们需要创建一个组件来处理我们的 Todo 表单。由于我们需要在`Todo`文件夹中创建一个名为`TodoForm.jsx`的文件，我们组件的代码如下：

```jsx
import React, { Component } from 'react';
import { Field, reduxForm } from 'redux-form';

class TodoForm extends Component {
  // Functional component to render an input...
  renderInput = ({ input }) => <input {...input} type="text" />;

 // This function is useful to handle our 
  onSubmit = values => {
    const { addTask, dispatch, reset } = this.props;

    // Resetting our form, this will clear our input...
    dispatch(reset('todo'));  

    // Executing our addTask method and
    // passing the form values.
    addTask(values);
  }

  render() {
    // handleSubmit is part of Redux Form 
    // to handle the onSubmit event
    const { handleSubmit } = this.props;

    return (
      <form onSubmit={handleSubmit(this.onSubmit)}>
        {/* Field is a Redux Form Component, we need to pass the 
         name of the input and the component we are using to 
         render it */}
        <Field 
          name="task" 
          component={this.renderInput} 
        />
      </form>
    )
  }
}

// With this we named our form reducer for this specific form (todo).  
export default reduxForm({
  form: 'todo'
})(TodoForm);
```

文件：src/components/Todo/TodoForm.jsx

1.  Redux Form 包含许多有用的 props 来处理我们表单中的数据。我标记了我们将在此示例中使用的 props（`addTask`是从另一个组件传递的 prop，因此它不是 Redux Form 的一部分），您可以通过在渲染方法中添加控制台来查看所有的 props `console.log(this.props);`，如下面的截图所示：

![](img/bb4aaf76-a242-46fc-8fab-9ada82003a37.png)

1.  现在让我们修改我们的`Todo`组件，包括`TodoForm`组件，并通过我们的`addTask`方法接收数据，如下面的代码所示：

```jsx
import React, { Component } from 'react';
import uuidv4 from 'uuid/v4';
import List from './List';
import TodoForm from './TodoForm';
import './Todo.css';
class Todo extends Component {
  constructor() {
    super();

    // Initial state...
    this.state = {
      items: []
    };
  }

  addTask = values => {
    // This values are coming from our 
    // onSubmit method in our TodoForm.
    const { task } = values;

    this.setState({
      items: [
        ...this.state.items,
        {
          id: uuidv4(),
          task,
          complete: false
        }
      ]
    });
  }

  render() {
    return (
      <div className="Todo">
        <h1>New Task:</h1>

        <TodoForm addTask={this.addTask} />
        <List items={this.state.items} />
      </div>
    );
  }
}
export default Todo;
```

# 它是如何工作的...

如您所见，Redux Form 很容易实现：

1.  在第一步中，我们将 Redux Form reducer 连接到了我们的 store

1.  在第二步中，我们创建了`TodoForm`组件，在这里我们渲染了表单字段，将表单 reducer 连接到 store，并将值发送回`addTask`回调函数

1.  在最后一步中，我们呈现了我们的`TodoForm`并发送了`addTask`回调，该回调处理任务值并将其插入到本地状态中

最后，我们将看到与上一个示例相同的结果，但现在我们使用 Redux Form，如下面的屏幕截图所示：

![](img/907eed37-f3bc-4e2f-af75-1d527358a1f1.png)

# 在表单中实现验证

我们 Redux Form 实现的最后部分是验证。使用上一个示例，让我们添加对输入任务的验证。

# 如何做...

在任何表单中都需要验证，因此让我们为我们的字段添加一些验证：

1.  首先，我们需要修改我们的`TodoForm.jsx`，并且我们需要创建一个`validate`函数，在这个函数中，我们需要验证我们的任务是否不为空。然后，我们需要创建一个`renderError`方法，以便在尝试添加空任务时呈现我们的错误消息，如下面的代码所示：

```jsx
import React, { Component } from 'react';
import { Field, reduxForm } from 'redux-form';
import './TodoForm.css';

class TodoForm extends Component {
  renderInput = ({ input }) => <input {...input} type="text" />;

  onSubmit = values => {
    const { addTask, dispatch, reset } = this.props;

    // Resetting our form...
    dispatch(reset('todo'));

    addTask(values);
  }

  renderError(field) {
    const { meta: { submitFailed, error } } = field;

    if (submitFailed && error) {
      return (
        <div className="error">
          {error}
        </div>
      );
    }

    return null;
  }

  render() {
    const { handleSubmit, submitting } = this.props;

    return (
      <form onSubmit={handleSubmit(this.onSubmit)}>
        <Field name="task" component={this.renderInput} />
        <Field name="task" component={this.renderError} />
      </form>
    );
  }
}

const validate = values => {
  const errors = {};

  if (!values.task) {
    errors.task = 'Task cannot be empty!';
  }

  return errors;
}

export default reduxForm({
  validate,
  form: 'todo'
})(TodoForm);
```

文件：src/components/Todo/TodoForm.jsx

1.  接下来，我们需要创建一个`TodoForm.css`来为我们的错误消息添加一些样式，如下面的代码所示：

```jsx
.error {
  color: red;
  font-size: small;
  margin-top: 10px;
}
```

文件：src/components/Todo/TodoForm.css

# 它是如何工作的...

如果我们尝试添加一个没有任何值的新任务，并按*Enter*提交表单，我们将看到以下屏幕截图中显示的视图：

![](img/09f9546b-3bcc-4ad2-9268-61b2dd935c15.png)
