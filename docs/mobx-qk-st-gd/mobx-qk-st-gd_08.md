# 第八章：探索 mobx-utils 和 mobx-state-tree

当你开始深入了解 MobX 的世界时，你会意识到某些类型的用例经常重复出现。第一次解决它们时，会有一种明确的成就感。然而，第五次之后，你会想要标准化解决方案。`mobx-utils`是一个 NPM 包，为你提供了几个标准实用程序，用于处理 MobX 中的常见用例。

为了进一步推动标准化水平，我们可以将更多结构化的意见引入我们的 MobX 解决方案中。这些意见是在多年的 MobX 使用中形成的，并包含了快速开发的各种想法。这一切都可以通过`mobx-state-tree` NPM 包实现。

在本章中，我们将更详细地介绍以下包：

+   `mobx-utils`提供了一系列实用功能

+   `mobx-state-tree`（**MST**）是一个有意见的 MobX

# 技术要求

你需要在系统上安装 Node.js。最后，要使用本书的 Git 存储库，用户需要安装 Git。

本章的代码文件可以在 GitHub 上找到：

[`github.com/PacktPublishing/Mobx-Quick-Start-Guide/tree/master/src/Chapter08`](https://github.com/PacktPublishing/Mobx-Quick-Start-Guide/tree/master/src/Chapter08)

查看以下视频，了解代码的实际操作：

[`bit.ly/2LiFSJO`](http://bit.ly/2LiFSJO)

# mobx-utils 的实用功能

`mobx-utils`提供了各种实用功能，可以简化 MobX 中的编程任务。你可以使用`npm`或`yarn`安装`mobx-utils`：

```jsx
$ npm install mobx-utils
```

在本节的其余部分，我们将重点介绍一些经常使用的实用程序。其中包括以下内容：

+   `fromPromise()`

+   `lazyObservable()`

+   `fromResource()`

+   `now()`

+   `createViewModel()`

# 使用 fromPromise()可视化异步操作

在 JavaScript 中，promise 是处理异步操作的好方法。在表示 React UI 上的操作状态时，我们必须确保处理 promise 的三种状态中的每一种。这包括 promise 处于`pending`（操作进行中）状态时，`fulfilled`（操作成功完成）状态时，或者`rejected`（失败）状态时。`fromPromise()`是处理 promise 的一种便利方式，并提供了一个很好的 API 来直观地表示这三种状态：

```jsx
newPromise = fromPromise(promiseLike)
```

`promiseLike`：`Promise`的实例或`(resolve, reject) => { }`

`fromPromise()`包装给定的 promise，并返回一个新的、带有额外可观察属性的、MobX 充电的 promise：

+   `state`：三个字符串值之一：`pending`、`fulfilled`或`rejected`：这些也作为`mobx-utils`包的常量可用：`mobxUtils.PENDING`、`mobxUtils.FULFILLED`和`mobxUtils.REJECTED`。

+   `value`：已解析的`value`或`rejected`错误。使用`state`来区分值。

+   `case({pending, fulfilled, rejected})`：这用于为三种状态提供 React 组件。

让我们通过一个例子来看看所有这些。我们将创建一个简单的`Worker`类，执行一些操作，这些操作可能会随机失败。这是跟踪操作的`Worker`类，通过调用`fromPromise()`来调用操作。请注意，我们将一个`promise`作为参数传递给`fromPromise()`：

```jsx
import { fromPromise, PENDING, FULFILLED, REJECTED } from 'mobx-utils';
class Worker {
    operation = null;
 start() {
 this.operation = fromPromise(this.performOperation());
    }
    performOperation() {
        return new Promise((resolve, reject) => {
            const timeoutId = setTimeout(() => {
                clearTimeout(timeoutId);
                Math.random() > 0.25 ? resolve('200 OK') 
                    : reject(new Error('500 FAIL'));
            }, 1000);
        });
    }
}

```

为了可视化这个操作，我们可以利用`case()` API 来显示每个状态对应的 React 组件。这可以在以下代码中看到。随着操作从`pending`到`fulfilled`或`rejected`的进展，这些状态将以正确的 React 组件呈现。对于`fulfilled`和`rejected`状态，已解析的`value`或`rejected` `error`作为第一个参数传入：

```jsx
import { fromPromise, PENDING, FULFILLED, REJECTED } from 'mobx-utils';
import { observer } from 'mobx-react';

import React, { Fragment } from 'react';
import { CircularProgress, Typography } from '@material-ui/core/es/index';

@observer export class FromPromiseExample extends React.Component {
    worker;

    constructor(props) {
        super(props);

 this.worker = new Worker();
 this.worker.start();
    }

    render() {
        const { operation } = this.worker;
 return operation.case({
            [PENDING]: () => (
                <Fragment>
                    <CircularProgress size={50}  color={'primary'} />
                    <Typography variant={'title'}>
                        Operation in Progress
                    </Typography>
                </Fragment>
            ),
            [FULFILLED]: value => (
                <Typography variant={'title'}  color={'primary'}>
                    Operation completed with result: {value}
                </Typography>
            ),
            [REJECTED]: error => (
                <Typography variant={'title'}  color={'error'}>
                    Operation failed with error: {error.message}
                </Typography>
            ),
        });
    }
}
```

我们也可以手动切换可观察的`state`属性，而不是使用`case()`函数。实际上，`case()`在内部执行这个操作。

# 使用`lazyObservable()`进行延迟更新

对于执行代价高昂的操作，将其推迟到需要时是有意义的。使用`lazyObservable()`，您可以跟踪这些操作的结果，并在需要时更新。它接受一个执行计算并在准备就绪时推送值的函数：

```jsx
result = lazyObservable(sink => { }, initialValue)
```

在这里，`sink`是要调用的回调函数，将值推送到`lazyObservable`上。延迟可观察对象也可以以一些`initialValue`开始。

可以使用`result.current()`来检索`lazyObservable()`的当前值。一旦延迟可观察对象已更新，`result.current()`将有一些值。要再次更新延迟可观察对象，可以使用`result.refresh()`。这将重新调用计算，并最终通过`sink`回调推送新值。请注意，`sink`回调可以根据需要调用多次。

在以下代码片段中，您可以看到使用`lazyObservable()`来更新操作的值：

```jsx
import { lazyObservable } from 'mobx-utils';

class ExpensiveWorker {
    operation = null;

    constructor() {
 this.operation = lazyObservable(async sink => {
 sink(null); // push an empty value before the update
            const result = await this.performOperation();
 sink(result);
        });
    }

    performOperation() {
        return new Promise(resolve => {
            const timeoutId = setTimeout(() => {
                clearTimeout(timeoutId);
                resolve('200 OK');
            }, 1000);
        });
    }
}
```

MobX 跟踪对`current()`方法的调用，因此请确保仅在需要时调用它。在`render()`中使用此方法会导致 MobX 重新渲染组件。毕竟，组件的`render()`在 MobX 中转换为 reaction，每当其跟踪的 observable 发生变化时重新评估。

要在 React 组件（*observer*）中使用 lazy-observable，我们依赖于`current()`方法来获取其值。MobX 将跟踪此值，并在其更改时重新渲染组件。请注意，在按钮的`onClick`处理程序中，我们通过调用其`refresh()`方法来更新 lazy-observable：

```jsx
import { observer } from 'mobx-react';
import React, { Fragment } from 'react';
import {
    Button,
    CircularProgress,
    Typography,
} from '@material-ui/core/es/index'; **@observer** export class LazyObservableExample extends React.Component {
    worker;
    constructor(props) {
        super(props);

 this.worker = new ExpensiveWorker();
    }
   render() {
 const { operation } = this.worker;
 const result = operation.current();
        if (!result) {
            return (
                <Fragment>
                    <CircularProgress size={50}  color={'primary'} />
                    <Typography variant={'title'}>
                        Operation in Progress
                    </Typography>
                </Fragment>
            );
        }
         return (
            <Fragment>
                <Typography variant={'title'}  color={'primary'}>
                    Operation completed with result: {result}
                </Typography>
                <Button
  variant={'raised'}   color={'primary'}  onClick={() => operation.refresh()} >
                    Redo Operation
                </Button>
            </Fragment>
        );
    }
}
```

# 使用 fromResource()的通用 lazyObservable()

还有一种更一般化的`lazyObservable()`形式，称为`fromResource()`。类似于`lazyResource()`，它接受一个带有`sink`回调的函数。这充当*订阅*函数，仅在实际请求资源时调用。此外，它接受第二个参数，*取消订阅*函数，可用于在不再需要资源时进行清理：

```jsx
resource = fromResource(subscriber: sink => {}, unsubscriber: () => {},    
           initialValue)
```

`fromResource()` 返回一个 observable，当第一次调用它的`current()`方法时，它将开始获取值。它返回一个 observable，还具有`dispose()`方法来停止更新值。

在下面的代码片段中，您可以看到一个`DataService`类依赖于`fromResource()`来管理其 WebSocket 连接。数据的值可以使用`data.current()`来检索。这里，*data*充当 lazy-observable。在*订阅*函数中，我们设置了 WebSocket 并订阅了特定频道。我们在`fromResource()`的*取消订阅*函数中取消订阅此频道：

```jsx
import { **fromResource** } from 'mobx-utils';

class DataService {
    data = null;
    socket = null;

    constructor() {
 this.data = fromResource(
            async sink => {
                this.socket = new WebSocketConnection();
                await this.socket.subscribe('data');

                const result = await this.socket.get();

                sink(result);
            },
            () => {
                this.socket.unsubscribe('data');
                this.socket = null;
            },
        );
    }
}

const service = new DataService(); console.log(service.data.current());

// After some time, when no longer needed service.data.dispose();
```

我们可以使用`dispose()`方法显式处理资源。但是，MobX 足够聪明，知道没有更多观察者观察此资源时，会自动调用*取消订阅*函数。

mobx-utils 提供的一种特殊类型的 lazy-observable 是`now(interval: number)`。它将时间视为 observable，并以给定的间隔更新。您可以通过简单调用`now()`来检索其值，默认情况下每秒更新一次。作为 observable，它还会导致任何 reaction 每秒执行一次。在内部，`now()`使用`fromResource()`实用程序来管理计时器。

# 管理编辑的视图模型

在基于数据输入的应用程序中，通常会有表单来接受各种字段。在这些表单中，原始模型直到用户提交表单之前都不会发生变化。这允许用户取消编辑过程并返回到先前的值。这种情况需要创建原始模型的克隆，并在提交时推送编辑。尽管这种技术并不是非常复杂，但它确实增加了一些样板文件。

`mobx-utils`提供了一个方便的实用程序，名为`createViewModel()`，专门为这种情况量身定制：

```jsx
viewModel = createViewModel(model)
```

`model`是包含可观察属性的原始模型。`createViewModel()`包装了这个模型并代理了所有的读取和写入。这个实用程序具有一些有趣的特性，如下：

+   只要`viewModel`的属性没有更改，它将返回原始模型中的值。更改后，它将返回更新后的值，并将`viewModel`视为已修改。

+   要最终确定原始模型上的更新值，必须调用`viewModel`的`submit()`方法。要撤消任何更改，可以调用`reset()`方法。要恢复单个属性，请使用`resetProperty(propertyName: string)`。

+   要检查`viewModel`是否被修改，请使用`isDirty`属性。要检查单个属性是否被修改，请使用`isPropertyDirty(propertyName: string)`。

+   要获取原始模型，请使用方便的`model()`方法。

使用`createViewModel()`的优势在于，您可以将整个编辑过程视为单个事务。只有在调用`submit()`时才是最终的。这允许您过早取消并保留原始模型在其先前状态。

在以下示例中，我们正在创建一个包装`FormData`实例并记录`viewModel`和`model`属性的`viewModel`。您将注意到`viewModel`的代理效果以及值在`submit()`时如何传播回模型：

```jsx
class FormData {
    @observable name = '<Unnamed>';
    @observable email = '';
    @observable favoriteColor = '';
}

const viewModel = createViewModel(new FormData());

autorun(() => {
    console.log(
        `ViewModel: ${viewModel.name}, Model: ${
            viewModel.model.name
  }, Dirty: ${viewModel.isDirty}`,
    );
});

viewModel.name = 'Pavan';
viewModel.email = 'pavan@pixelingene.com';
viewModel.favoriteColor = 'orange';

console.log('About to reset');
viewModel.reset();

viewModel.name = 'MobX';

console.log('About to submit');
viewModel.submit();
```

`autorun()`的日志如下。您可以看到`submit()`和`reset()`对`viewModel.name`属性的影响：

```jsx
ViewModel: <Unnamed>, Model: <Unnamed>, Dirty: false ViewModel: Pavan, Model: <Unnamed>, Dirty: true About to reset... ViewModel: <Unnamed>, Model: <Unnamed>, Dirty: false ViewModel: MobX, Model: <Unnamed>, Dirty: true About to submit... ViewModel: MobX, Model: MobX, Dirty: false
```

# 还有很多可以发现的地方

这里描述的一些实用程序绝不是详尽无遗的。`mobx-utils`提供了更多实用程序，我们强烈建议您查看 GitHub 项目（[`github.com/mobxjs/mobx-utils`](https://github.com/mobxjs/mobx-utils)）以发现其余的实用功能。

有一些函数可以在 RxJS 流和 MobX Observables 之间进行转换，*processor-functions*可以在可观察数组附加时执行操作，MobX 的变体`when()`，它在超时后自动释放，等等。

# 一个有主见的 MobX 与 mobx-state-tree

MobX 在组织状态和应用各种操作和反应方面非常灵活。然而，它确实留下了一些问题需要你来回答：

+   应该使用类还是只是带有`extendObservable()`的普通对象？

+   数据应该如何规范化？

+   在序列化状态时如何处理循环引用？

+   还有很多

`mobx-state-tree`是一个提供了组织和结构化可观察状态的指导性指导的包。采用 MST 的思维方式会让你从一开始就获得几个好处。在本节中，我们将探讨这个包及其好处。

# 模型 - 属性、视图和操作

`mobx-state-tree`正如其名称所示，将状态组织在模型树中。这是一种以模型为先的方法，其中每个模型定义了需要捕获的状态。定义模型添加了在运行时对模型分配进行类型检查的能力，并保护您免受无意的更改。将运行时检查与像 TypeScript 这样的语言的使用结合起来，还可以获得编译时（或者说是设计时）类型安全性。通过严格类型化的模型，`mobx-state-tree`为您提供了安全的保证，并确保了您的类型模型的完整性和约束。这本身就是一个巨大的好处，特别是在处理像 JavaScript 这样的动态语言时。

让我们用一个简单的`Todo`模型来实现 MST：

```jsx
import { types } from 'mobx-state-tree';

const Todo = types.model('Todo', {
    title: types.string,
    done: false,
});
```

模型描述了它所持有的数据的形状。在`Todo`模型的情况下，它只需要一个`title` *string*和一个*boolean* `done`属性。请注意，我们将我们的模型分配给了一个大写的名称（`Todo`）。这是因为 MST 实际上定义了一个类型，而不是一个实例。

MST 中的所有内置类型都是`types`命名空间的一部分。`types.model()`方法接受两个参数：一个可选的字符串*name*（用于调试和错误报告）和一个定义类型各种属性的*object*。所有这些属性都将被严格类型化。让我们尝试创建这个模型的一个实例：

```jsx
const todo = Todo.create({
    title: 'Read a book',
    done: false,
});
```

请注意，我们已经将与模型中定义的相同的数据结构传递给了 `Todo.create()`。传递任何其他类型的数据将导致 MST 抛出类型错误。创建模型的实例也使其所有属性变成了可观察的。这意味着我们现在可以使用 MobX API 的全部功能。

让我们创建一个简单的反应，记录对 `todo` 实例的更改：

```jsx
import { autorun } from 'mobx';

autorun(() => {
    console.log(`${todo.title}: ${todo.done}`);
});

// Toggle the done flag todo.done = !todo.done; 
```

如果您运行此代码，您会注意到一个异常被抛出，如下所示：

```jsx
Error: [mobx-state-tree] Cannot modify 'Todo@<root>', the object is protected and can only be modified by using an action.
```

这是因为我们在动作之外修改了 `todo.done` 属性。您会回忆起前几章中的一个良好实践是将所有可观察的变化封装在一个动作内。事实上，甚至有一个 MobX API：`configure({ enforceActions: 'strict' })`，以确保这种情况发生。MST 对其状态树中的数据非常*保护*，并要求对所有变化使用动作。

这可能听起来非常严格，但它确实带来了额外的好处。例如，使用动作允许 MST 提供对中间件的一流支持。中间件可以*拦截*发生在状态树上的任何更改，并使实现诸如*日志记录、时间旅行*、*撤销*/*重做*、*数据库同步*等功能变得微不足道。

# 在模型上定义动作

我们之前创建的模型类型 `Todo` 可以通过链接的 API 进行扩展。`actions()` 就是这样一个 API，可以用来扩展模型类型的所有动作定义。让我们为我们的 `Todo` 类型做这件事：

```jsx
const Todo = types
  .model('Todo', {
        title: types.string,
        done: false,
    })
 .actions(self => ({
 toggle() {
 self.done = !self.done;
 },
 }));

const todo = Todo.create({
    title: 'Read a book',
    done: false,
});

autorun(() => {
    console.log(`${todo.title}: ${todo.done}`);
});

todo.toggle();
```

`actions()` 方法接受一个函数作为参数，该函数接收模型实例作为其参数。在这里，我们称之为 `self`。这个函数应该返回一个定义所有动作的键值映射。在前面的片段中，我们利用了 ES2015 的对象字面量语法，使动作对象看起来更易读。接受动作的这种风格有一些显著的好处：

+   使用函数允许您创建一个闭包，用于跟踪只被动作使用的私有状态。例如，设置在其中一个动作内部的 WebSocket 连接，不应该暴露给外部世界。

+   通过将模型的实例传递给 `actions()`，您可以保证 `this` 指针始终是正确的。您再也不必担心在 `actions()` 中定义的函数的上下文了。`toggle()` 动作利用 `self` 来改变模型实例。

定义的 actions 可以直接在模型实例上调用，这就是我们在`todo.toggle()`中所做的。MST 不再对直接突变提出异议，当`todo.done`改变时，`autorun()`也会触发。

# 使用视图创建派生信息

与 actions 类似，我们还可以使用`views()`扩展模型类型。在 MST 中，模型中的派生信息是使用`views()`来定义的。就像`actions()`方法一样，它可以链接到模型类型上：

```jsx
const Todo = types
  .model(/* ... */)
    .actions(/* ... */)
 .views(self => ({
 get asMarkdown() {
 return self.done
  ? `* [x] ~~${self.title}~~`
  : `* [ ] ${self.title}`;
 },

 contains(text) {
 return self.title.indexOf(text) !== -1;
 },
 })); const todo = Todo.create({
    title: 'Read a book',
    done: false,
});

autorun(() => {
    console.log(`Title contains "book"?: ${todo.contains('book')}`);
});

console.log(todo.asMarkdown);
// * [ ] Read a book

console.log(todo.contains('book')); // true
```

在`Todo`类型上引入了两个视图：

+   `asMarkdown()`是一个*getter*，它转换为一个 MobX 计算属性。像每个计算属性一样，它的输出被缓存。

+   `contains()`是一个常规函数，其输出不被缓存。然而，它具有在响应式上下文中重新执行的能力，比如`reaction()`或`autorun()`。

`mobx-state-tree`引入了一个非常严格的模型概念，其中明确定义了*state*、*actions*和*derivations*。如果你对在 MobX 中构建代码感到不确定，MST 可以帮助你应用 MobX 的理念并提供清晰的指导。

# 微调原始类型

到目前为止我们所看到的单一模型类型只是一个开始，几乎不能称为树。我们可以扩展领域模型使其更加真实。让我们添加一个`User`类型，他将创建`todo`项目：

```jsx
import { types } from 'mobx-state-tree';

const User = types.model('User', {
    name: types.string,
    age: 42,
    twitter: types.maybe(types.refinement(types.string, v => 
 /^\w+$/.test(v))),
});
```

在前面的定义中有一些有趣的细节，如下所示：

+   `age`属性被定义为常量`42`，这对应于`age`的默认值。当没有为用户提供值时，它将被设置为这个默认值。此外，MST 足够聪明，可以推断类型为`number`。这对于所有原始类型都适用，其中默认值的类型将被推断为属性的类型。此外，通过给出默认值，我们暗示`age`属性是可选的。声明属性的更详细形式是：`types.optional(types.number, 42)`。

+   `twitter`属性有一个更复杂的定义，但可以很容易地分解。`types.maybe()`表明`twitter`句柄是可选的，因此它可能是*undefined*。当提供值时，它必须是字符串类型。但不是任何字符串；只有与提供的正则表达式匹配的字符串。这为您提供了运行时类型安全性，并拒绝无效的 Twitter 句柄，如`Calvin & Hobbes`或空字符串。

MST 提供的类型系统非常强大，可以处理各种复杂的类型规范。它还很好地组合，并为您提供了一种将许多较小类型组合成较大类型定义的功能方法。这些类型规范为您提供了运行时安全性，并确保了您的领域模型的完整性。

# 组合树

现在我们有了`Todo`和`User`类型，我们可以定义顶层的`App`类型，它组合了先前定义的类型。`App`类型代表应用程序的状态。

```jsx
const App = types.model('App', {
 todos: types.array(Todo),
 users: types.map(User),
});

const app = App.create({
    todos: [
        { title: 'Write the chapter', done: false },
        { title: 'Review the chapter', done: false },
    ],
    users: {
        michel: {
            name: 'Michel Westrate',
            twitter: 'mwestrate',
        },
        pavan: {
            name: 'Pavan Podila',
            twitter: 'pavanpodila',
        },
    },
});

app.todos[0].toggle();
```

我们通过使用*高阶类型*（接受类型作为输入并创建新类型的类型）定义了`App`类型。在前面的片段中，`types.map()`和`types.array()`创建了这些高阶类型。

创建`App`类型的实例只是提供正确的 JSON 负载的问题。只要结构与类型规范匹配，MST 在运行时构建模型实例时就不会有问题。

记住：数据的形状始终会被 MST 验证。它永远不会允许不符合模型类型规范的数据更新。

请注意在前面的片段中，我们能够无缝调用`app.todos[0].toggle()`方法。这是因为 MST 能够成功构建`app`实例并用适当的类型包装 JSON 节点。

`mobx-state-tree`提升了对建模应用程序状态的重要性。为应用程序中的各种实体定义适当的类型对于其结构和数据完整性至关重要。一个很好的开始方式是将从服务器接收到的 JSON 编码为 MST 模型。下一步是通过添加更严格的类型、附加操作和视图来***加强***模型。

# 引用和标识符

到目前为止，本章一直在完全讨论在*树*中捕获应用程序的状态。树具有许多有趣的属性，易于理解和探索。但通常，当一个人开始将新技术应用于实际问题领域时，往往会发现树在概念上不足以描述问题领域。例如，*友谊关系*是双向的，不适合单向树。处理不是*组合*性质的关系，而是*关联*性质的关系，通常需要引入新的抽象层和技术，如*数据规范化*。

我们的应用程序中可以通过为`Todo`添加一个`assignee`属性来快速介绍这种关系。现在，很明显`Todo`并不*拥有*它的`assignee`，反之亦然；*todos*不是由单个用户拥有的，因为它们可以稍后被*重新分配*。因此，当组合不足以描述关系时，我们经常会退回到使用*外键*来描述关系。

换句话说，`Todo`项的 JSON 可以像下面的代码一样，其中`Todo`的`assignee`字段对应于`User`对象的`userid`字段：

使用`name`来存储`assignee`关系是一个坏主意，因为一个人的`name`并不是唯一的，而且它可能随时间改变。

```jsx
{
    todos: [
        {
            title: 'Learn MST',
            done: false,
            assignee: '37',
        },
    ],
    users: {
        '37': {
            userid: '37',
            name: 'Michel Weststrate',
            age: 33,
            twitter: 'mweststrate',
        },
    },
}
```

我们最初的想法可能是将`assignee`和`userid`属性类型化为`types.string`字段。然后，每当我们需要时，我们可以在`users`映射中查找指定的用户，因为用户存储在其自己的`userid`下。由于用户查找可能是一个常见的操作，我们甚至可以引入一个*视图*和*操作*来读取或写入该用户。这将使我们的用户模型如下所示的代码所示：

```jsx
import { types, getRoot } from 'mobx-state-tree';

const User = types.model('User', {
 userid: types.string, // uniquely identifies this User  name: types.string,
    age: 42,
    twitter: types.maybe(types.refinement(types.string, v => /^\w+$/.test(v))),
});

const Todo = types
  .model('Todo', {
 assignee: types.string, // represents a User  title: types.string,
        done: false,
    })
    .views(self => ({
 getAssignee() {
            if (!this.assignee) return undefined;
            return getRoot(self).users.get(this.assignee);
        },
    }))
    .actions(self => ({
 setAssignee(user) {
            if (typeof user === 'string') this.assignee = user;
            else if (User.is(user)) this.assignee = user.userid;
            else throw new Error('Not a valid user object or user id');
        },
    }));

const App = {
    /* as is */ };

const app = App.create(/* ... */);

console.log(app.todos[0].getAssignee().name); // Michel Weststrate 
```

在`getAssignee()`视图中，我们方便地利用了每个 MST 节点都知道自己在树中的位置这一事实。通过利用`getRoot()`实用程序，我们可以导航到`users`映射并获取正确的`User`对象。通过使用`getAssignee()`视图，我们获得了一个真正的`User`对象，以便我们可以直接访问和打印其`name`属性。

有几个有用的实用程序可以用来反映或处理树中的位置，例如`getPath()`、`getParent()`、`getParentOfType()`等。作为替代方案，我们可以将`getAssignee()`视图表示为`return resolvePath(self, "../../users/" + self.assignee)`。

我们可以将 MST 树视为状态的文件系统！`getAssignee()`只是将其转换为符号链接。

此外，我们引入了一个更新`assignee`属性的操作。为了确保`setAssignee()`操作可以方便地通过提供`userid`或实际*用户*对象来调用，我们应用了一些*类型区分*。在 MST 中，每种类型不仅公开了`create()`方法，还公开了`is`方法，以检查给定值是否属于相应的类型。

# 通过 types.identifier()和 types.reference()进行引用

我们可以在 MST 中清晰地表达这些查找/更新实用程序，这很好，但是如果您的问题领域很大，这将变得相当重复。幸运的是，这种模式内置在 MST 中。我们可以利用的第一种类型是`types.identifier()`，它表示某个字段唯一标识某个模型类型的实例。因此，在我们的示例中，我们可以将`userid`的类型定义为`types.identifier()`，而不是`types.string`。

其次，还有`types.reference()`。这种类型表示某个字段被序列化为原始值，但实际上表示对树中另一种类型的引用。MST 将自动为我们匹配`identifier`字段和`reference`字段，因此我们可以简化我们之前的状态树模型如下：

```jsx
import { types } from "mobx-state-tree"

const User = types.model("User", {
 userid: types.identifier(), // uniquely identifies this User
  name: types.string,
  age: 42,
  twitter: types.maybe(types.refinement(types.string, (v => /^\w+$/.test(v))))
})

const Todo = types.model("Todo", {
 assignee: types.maybe(types.reference(User)), // a Todo can be assigned to a User
  title: types.string,
  done: false
})

const App = /* as is */

const app = App.create(/* */)
console.log(app.todos[0].assignee.name) // Michel Weststrate
```

由于引用类型，读取`Todo`的`assignee`属性实际上将解析存储的标识符并返回正确的`User`对象。因此，我们可以立即在前面的示例中打印其名称。请注意，我们的状态仍然是一个树。还要注意的是，我们不必指定`User`实例的引用应该在何处或如何解析。MST 将自动维护一个内部的基于*类型+标识符*的查找表来解析引用。通过使用*引用*和*标识符*，MST 具有足够的类型信息来自动处理我们的*数据（去）规范化*。

`types.reference`非常强大，并且可以自定义，例如根据相对路径（就像真正的符号链接！）而不是标识符解析对象。在许多情况下，您将与上述一样结合`types.maybe`，以表达`Todo`不一定有`assignee`。同样，引用的数组和映射可以以类似的方式建模。

# 声明性模型的开箱即用的好处

MST 帮助您以声明性方式组织和建模复杂的问题领域。由于在您的领域中定义类型的一致方法，我们得到了清晰简单的心智模型的好处。这种一致性还为我们带来了许多开箱即用的功能，因为 MST 深入了解状态树。我们之前看到的一个例子是使用标识符和引用进行自动数据规范化。MST 内置了许多更多功能。其中，有一些功能在实际中最为实用。我们将在本节的其余部分简要讨论它们。

# 不可变的快照

MST 始终在内存中保留状态树的不可变版本，可以使用`getSnapshot()`API 检索。基本上，`const snapshot = getSnapshot(tree)`是`const tree = Type.create(snapshot)`的反向操作。`getSnapshot()`使得快速序列化整个树的状态非常方便。由于 MST 由 MobX 支持，我们也可以很好地跟踪这一点。

快照在模型实例上转换为计算属性。

以下代码片段在每次更改时自动将树的状态存储在*local-storage*中，但每秒最多一次：

```jsx
import { reaction } from 'mobx';
import { getSnapshot } from 'mobx-state-tree';

const app = App.create(/* as before */);

reaction(
    () => getSnapshot(app),
    snapshot => {
        window.localStorage.setItem('app', JSON.stringify(snapshot));
    },
    { delay: 1000 },
);
```

应该指出，MST 树中的每个节点本身都是 MST 树。这意味着在根上调用的任何操作也可以在其任何子树上调用。例如，如果我们只想存储整个状态的一部分，我们可以只获取子树的快照。

与`getSnapshot()`搭配使用的相关 API 是`applySnapshot()`。这可以用来以高效的方式使用快照更新树。通过结合`getSnapshot()`和`applySnapshot()`，你可以只用几行代码就构建一个时间旅行者！这留给读者作为练习。

# JSON 补丁

尽管快照有效地捕获了整个应用程序的状态，但它们不适合与服务器或其他客户端频繁通信。这是因为快照的大小与要序列化的状态的大小成线性增长。相反，对于实时更改，最好向服务器发送增量更新。*JSON-patch*（RFC-6902）是关于如何序列化这些增量更新的官方标准，MST 默认支持此标准。

`onPatch()`API 可用于监听作为更改副作用生成的`patches`。另一方面，`applyPatch()`执行相反的过程：给定一个补丁，它可以更新现有树。`onPatch()`监听器会生成由操作所做的状态更改产生的`patches`。它还公开了所谓的*inverse-patches*：一个可以撤消`patches`所做更改的集合。

```jsx
import { onPatch } from 'mobx-state-tree';

const app = App.create(/* see above */);

onPatch(app, (patches, inversePatches) => {
 console.dir(patches, inversePatches);
});

app.todos[0].toggle();
```

切换`todo`的前面代码在控制台上打印如下内容：

```jsx
// patches:   [{
 op: "replace", path: "/todos/0/done", value: true }]   // inverse-patches:   [{
 op: "replace", path: "/todos/0/done", value: false }]
```

# 中间件

我们在前面的部分简要提到了中间件，但让我们在这里扩展一下。中间件充当对状态树上调用的操作的拦截器。因为 MST 要求使用操作，我们可以确保每个 *操作* 都会通过中间件。中间件的存在使得实现几个横切面特性变得微不足道，例如以下内容：

+   日志记录

+   认证

+   时间旅行

+   撤销/重做

事实上，`mst-middlewares` NPM 包包含了一些先前提到的中间件，以及一些其他中间件。有关这些中间件的更多详细信息，请参阅：[`github.com/mobxjs/mobx-state-tree/blob/master/packages/mst-middlewares/README.md`](https://github.com/mobxjs/mobx-state-tree/blob/master/packages/mst-middlewares/README.md)。

# 进一步阅读

我们几乎只是触及了 MobX-State-Tree 的表面，但希望它已经在组织和构建 MobX 中的可观察状态方面留下了印象。这是一个明确定义的、社区驱动的方法，它融入了本书中讨论的许多最佳实践。要深入探索 MST，您可以参考官方入门指南：[`github.com/mobxjs/mobx-state-tree/blob/master/docs/getting-started.md#getting-started`](https://github.com/mobxjs/mobx-state-tree/blob/master/docs/getting-started.md#getting-started)。

# 总结

在本章中，我们涵盖了使用 `mobx-utils` 和 `mobx-state-tree` 等包采用 MobX 的实际方面。这些包将社区对于在各种场景中使用 MobX 的智慧编码化。

`mobx-utils` 为您提供了一组用于处理异步任务、处理昂贵的更新、为事务编辑创建视图模型等的实用工具。

`mobx-state-tree` 是一个全面的包，旨在简化使用 MobX 进行应用程序开发。它采用规范化的方法来构建和组织 MobX 中的可观察状态。通过这种声明性的方法，MST 能够更深入地理解状态树，并提供各种功能，例如运行时类型检查、快照、JSON 补丁、中间件等。总的来说，它有助于开发对 MobX 应用程序的清晰的心智模型，并将类型域模型置于前沿。

在下一章中，我们将通过一瞥了解 MobX 的内部工作，来完成 MobX 的旅程。如果 MobX 的某些部分看起来像*黑魔法*，下一章将驱散所有这些神话。
