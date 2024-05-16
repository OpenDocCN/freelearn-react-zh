# 第七章：使用 React 进行动画

在本章中，将涵盖以下食谱：

+   使用 ReactCSSTransitionGroup 对待事项列表进行动画处理

+   使用 react-animations 库

+   使用 React pose 创建我们的第一个动画

# 介绍

动画在任何 Web 应用程序中都很常见。自 CSS3 以来，动画已经变得普遍且易于实现。动画最常见的用途是过渡，您可以在其中更改 CSS 属性并定义持续时间或延迟。React 可以使用名为`ReactCSSTransitionGroup`的动画插件来处理动画。在接下来的食谱中，我们将使用`ReactCSSTransitionGroup`来创建一些动画。`ReactCSSTransitionGroup`是一个用于平滑实现基本 CSS 动画和过渡的附加组件。

# 使用 ReactCSSTransitionGroup 对待事项列表进行动画处理

在这个食谱中，我们将使用`ReactCSSTransitionGroup`来对待事项列表进行动画处理。

# 准备就绪

对于这个食谱，我们需要安装`react-addons-css-transition-group`包：

```jsx
npm install react-addons-css-transition-group
```

# 如何做到这一点...

我们将制作一个带有一些动画效果的待办事项列表：

1.  首先，让我们创建我们的`Todo`组件：

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

  componentWillMount() {
    // Setting default tasks...
    this.setState({
      items: [
        {
          id: uuidv4(),
          task: 'Default Task 1',
          completed: false
        },
        {
          id: uuidv4(),
          task: 'Default Task 2',
          completed: true
        },
        {
          id: uuidv4(),
          task: 'Default Task 3',
          completed: false
        }
      ]
    });
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

    // Once is submited we reset the task value and we push the 
    // new task to the items array.
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

  markAsCompleted = id => {
    // Finding the task by id...
    const foundTask = this.state.items.find(
      task => task.id === id
    );

    // Updating the completed status...
    foundTask.completed = true;

    // Updating the state with the new updated task...
    this.setState({
      items: [
        ...this.state.items,
        ...foundTask
      ]
    });
  }

  removeTask = id => {
    // Filtering the tasks by removing the specific task id...
    const filteredTasks = this.state.items.filter(
      task => task.id !== id
    );

    // Updating items state...
    this.setState({
      items: filteredTasks
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

        <List
          items={this.state.items}
          markAsCompleted={this.markAsCompleted}
          removeTask={this.removeTask}
        />
      </div>
    );
  }
}

export default Todo;
```

文件：src/components/Todo/index.jsx

1.  现在，在我们的`List`组件中，我们需要包括`ReactCSSTransitionGroup`并将其用作列表元素的包装器。我们需要使用`transitionName`属性指定我们的过渡名称，`transitionAppear`在第一次动画挂载时添加过渡。默认情况下为`false`：

```jsx
import React from 'react';
import ReactCSSTransitionGroup from 'react-addons-css-transition-group';
import './List.css';

const List = props => (
  <ul>
    <ReactCSSTransitionGroup 
 transitionName="todo" 
 transitionAppear={true}
    >
      {props.items.map((item, key) => (
        <li 
          key={key} 
          className={`${item.completed ? 'completed' : 'pending'}`}
        >
          {item.task}

          <div className="actions">
            <span 
              className={item.completed ? 'hide' : 'done'} 
              onClick={() => props.markAsCompleted(item.id)}
            >
              <i className="fa fa-check"></i>
            </span>

            <span 
              className="trash" 
              onClick={() => props.removeTask(item.id)}
            >
              <i className="fa fa-trash"></i>
            </span>
          </div>
        </li>
      ))}
    </ReactCSSTransitionGroup>
  </ul>
);

export default List;
```

文件：src/components/Todo/List.jsx

1.  现在，使用`transitionName`，我们将使用由`ReactCSSTransitionGroup`创建的特殊类添加一些样式：

```jsx
.todo-enter {
    opacity: 0.01;
}

.todo-enter.todo-enter-active {
    opacity: 1;
    transition: opacity 0.5s ease;
}

.todo-leave {
    opacity: 1;
}

.todo-leave.todo-leave-active {
    opacity: 0.01;
    transition: opacity .5s ease-in;
}

.todo-appear {
    opacity: 0.01;
    transition: opacity .5s ease-in;
}

.todo-appear.todo-appear-active {
    opacity: 1;
}
```

文件：src/components/Todo/List.css

# 它是如何工作的...

我们需要在`ReactCSSTransitionGroup`组件内包含我们想要动画的元素。每当我们向待办事项列表中添加一个项目时，我们可以看到我们的特殊类（`.todo-enter`和`.todo-enter-active`）被注入一秒钟，以启动我们的动画：

![](img/39d3ce5a-af0e-46ee-942a-6c6cfec5d6e8.png)

如果我们删除一个项目，我们将看到`.todo-leave`和`.todo-leave-active`类持续一秒钟：

![](img/ff4cc9b6-789b-4149-9551-0a0cbbd8701c.png)

正如你所看到的，使用`ReactCSSTransitionGroup`帮助我们处理动画的状态。您可以使用它来在您的 React 应用程序中创建更好的动画。

# 使用 react-animations 库

在这个食谱中，我们将学习如何使用库 react-animations。

# 准备就绪

对于这个食谱，我们需要安装以下软件包：

```jsx
npm install react-animations radium
```

# 如何做...

让我们做一些动画：

1.  我们需要使用`Radium`来创建内联样式，以便使用`react-animations`包中的动画。首先，让我们创建我们的组件：

```jsx
import React, { Component } from 'react';
import { fadeIn } from 'react-animations';
import Radium, { StyleRoot } from 'radium';

const styles = {
  fadeIn: {
    animation: 'x 1s',
    animationName: Radium.keyframes(fadeIn, 'fadeIn')
  }
};

class Animations extends Component {
  render() {
    return (
      <StyleRoot>
        <div className="Animations" style={styles.fadeIn}>
          <h1>This text will be animated</h1>
        </div>
      </StyleRoot>
    );
  }
}

export default Animations;
```

文件：src/components/Animations/index.jsx

1.  在这个例子中，我们使用了`fadeIn`动画。我们需要从`react-animations`中导入我们想要使用的动画，将动画添加到我们的`Radium`样式中，然后使用`<StyleRoot>`作为我们动画的包装器，最后指定内联样式`fadeIn`。

1.  如果你想使用另一个动画，例如`bounce`，那么你需要添加弹跳动画并为其创建一个样式：

```jsx
import React, { Component } from 'react';
import { fadeIn, bounce } from 'react-animations';
import Radium, { StyleRoot } from 'radium';

const styles = {
  fadeIn: {
    animation: 'x 1s',
    animationName: Radium.keyframes(fadeIn, 'fadeIn')
  },
  bounce: {
    animation: 'x 1s',
    animationName: Radium.keyframes(bounce, 'bounce')
  }
};

class Animations extends Component {
  render() {
    return (
      <StyleRoot>
        <div className="Animations" style={styles.bounce}>
          <h1>This text will be animated</h1>
        </div>
      </StyleRoot>
    );
  }
}

export default Animations;
```

文件：src/components/Animations/index.jsx

# 还有更多...

正如你所看到的，使用`react-animations`中的动画非常容易。还有很多其他动画：

+   弹跳

+   淡入

+   淡出

+   闪光

+   翻转

+   滚入

+   滚出

+   淡入旋转

+   旋转出

+   橡皮筋舞蹈

+   摇动

+   摇摆

+   放大

+   缩小

要查看所有可用的动画，请访问官方存储库[`github.com/FormidableLabs/react-animations`](https://github.com/FormidableLabs/react-animations)。

# 使用 React Pose 创建我们的第一个动画

React Pose 是用于 HTML、SVG 和 React 的声明式动作系统。这是一个非常酷的库，你可以用它在 React 中做出惊人的动画。

# 准备工作

对于这个示例，我们需要安装以下软件包并更新我们的`react`和`react-dom`至`16.4.2`或更高版本：

```jsx
 npm install react react-dom react-pose styled-components
```

# 如何做...

按照以下步骤创建 React pose 动画：

1.  首先，让我们创建我们的组件结构：

```jsx
import React, { Component } from 'react';
import posed from 'react-pose';
import styled from 'styled-components';
import './Animations.css';

class Animations extends Component {
  render() {
    return (
      <div class="Animations">

      </div>
    );
  }
}

export default Animations;
```

文件：src/components/Animations/index.jsx

1.  我们需要做的第二件事是创建我们的第一个 posed `div`，并创建一个使用`styled-components`的样式化`div`，并具有我们动画的状态（`normal`和`hover`）：

```jsx
import React, { Component } from 'react';
import posed from 'react-pose';
import styled from 'styled-components';
import './Animations.css';

// Creating our posed div
const Circle = posed.div({
  normal: {
    scale: 1 // Normal state
  },
  hover: {
    scale: 3 // Hover state
  }
});

// Creating styled component
const StyledCircle = styled(Circle)`
  color: white;
  cursor: pointer;
  background: blue;
  line-height: 80px;
  border-radius: 50%;
  height: 80px;
  width: 80px;
`;

class Animations extends Component {
  render() {
    return (
      <div class="Animations">

      </div>
    );
  }
}

export default Animations;
```

文件：src/components/Animations/index.jsx

1.  现在我们需要将我们的`StyledCircle`组件添加到我们的`render`方法中：

```jsx
      render() {
        return (
          <div class="Animations">
            <StyledCircle
              pose={this.state.hover ? 'hover' : 'normal'}
              onMouseEnter={this.handleMouseEnter}
              onMouseLeave={this.handleMouseLeave}
              onClick={this.handleClick}
              style={{ background: this.state.bg }}
            >
              Click me!
            </StyledCircle>
          </div>
        );
      }
```

文件：src/components/Animations/index.jsx

1.  正如你所看到的，我们需要创建一些事件方法，并且当用户点击时，我们将使用本地状态来改变圆圈的大小和颜色：

```jsx
import React, { Component } from 'react';
import posed from 'react-pose';
import styled from 'styled-components';
import './Animations.css';

const Circle = posed.div({
  normal: {
    scale: 1 // Normal state
  },
  hover: {
    scale: 3 // Hover state
  }
});

// Creating styled component
const StyledCircle = styled(Circle)`
  color: white;
  cursor: pointer;
  background: blue;
  line-height: 80px;
  border-radius: 50%;
  height: 80px;
  width: 80px;
`;

class Animations extends Component {
  state = {
    bg: 'blue',
    hover: false
  };

  handleMouseEnter = () => {
    this.setState({
      hover: true
    });
  }

  handleMouseLeave = () => {
    this.setState({
      hover: false
    });
  }

  handleClick = () => {
    // Choosing a random color...
    const colors = ['red', 'green', 'gray', 'orange', 'black', 'pink'];

    this.setState({
      bg: colors[Math.floor(Math.random() * colors.length)]
    });
  }

  render() {
    return (
      <div class="Animations">
        <StyledCircle
          pose={this.state.hover ? 'hover' : 'normal'}
          onMouseEnter={this.handleMouseEnter}
          onMouseLeave={this.handleMouseLeave}
          onClick={this.handleClick}
          style={{ background: this.state.bg }}
        >
          Click me!
        </StyledCircle>
      </div>
    );
  }
}

export default Animations;
```

文件：src/components/Animations/index.jsx

# 它是如何工作的...

我们的第一个视图将是一个带有标签“点击我！”的蓝色圆圈：

![](img/d598d075-98ae-4cf2-b619-93a389c20bc1.png)

如果我们悬停在圆圈上，我们将看到 pose 动画，它会增加圆圈的比例：

![](img/ac3f8e69-19c8-4e0f-b7a2-9f7d40f4a6d5.png)

最后，如果我们点击圆圈，我们会看到我们的圆圈会随机改变背景颜色：

![](img/1eda21d4-cc03-48ea-b429-608a33aedc0f.png)

# 还有更多...

我们甚至可以结合`react-animations`库中的动画。例如，如果我们想要在用户点击圆圈时翻转它，那么我们可以这样做：

```jsx
import React, { Component } from 'react';
import posed from 'react-pose';
import styled, { keyframes } from 'styled-components';
import { flip } from 'react-animations';
import './Animations.css';

const flipAnimation = keyframes`${flip}`;

const Circle = posed.div({
  normal: {
    scale: 1 // Normal state
  },
  hover: {
    scale: 3 // Hover state
  }
});

// Creating styled component
const StyledCircle = styled(Circle)`
  color: white;
  cursor: pointer;
  background: blue;
  line-height: 80px;
  border-radius: 50%;
  height: 80px;
  width: 80px;
`;

class Animations extends Component {
  state = {
    style: {
      background: 'blue'
    },
    hover: false
  };

  handleMouseEnter = () => {
    this.setState({
      hover: true
    });
  }

  handleMouseLeave = () => {
    this.setState({
      hover: false
    });
  }

  handleClick = () => {
    // Choosing a random color...
    const colors = ['red', 'green', 'gray', 'orange', 'black', 'pink'];

    this.setState({
      style: {
        animation: `1s ${flipAnimation}`,
        background: colors[Math.floor(Math.random() * colors.length)]
      }
    });
  }

  render() {
    return (
      <div className="Animations">
        <StyledCircle
          pose={this.state.hover ? 'hover' : 'normal'}
          onMouseEnter={this.handleMouseEnter}
          onMouseLeave={this.handleMouseLeave}
          onClick={this.handleClick}
          style={this.state.style}
         >
           Click me!
         </StyledCircle>
       </div>
    );
  }
}

export default Animations;
```

文件：src/components/Animations/index.jsx
