# 第二章：配置路由-使用路由组件中的各种选项

React-Router 允许您使用`<Route>`组件声明性地定义路由。它是 React-Router 的主要构建块，并在`path` prop 中提到的路径值与浏览器的 URL 位置匹配时呈现`component` prop 中提到的组件。`<Route>`组件像任何其他 React 组件一样，接受一组 props。这些 props 可以更精细地控制浏览器的 URL 路径应该如何匹配`<Route>`组件的路径，以及一些其他呈现选项。

在上一章中，我们简要地看到了如何使用`<Route>`组件来匹配 URL 路径并呈现组件。在本章中，我们将看一下以下内容：

+   深入研究可以添加到`<Route>`组件的各种 props，例如`exact`，`strict`，`render`，`children`和`sensitive`。

+   **路由组件 props**：作为`<Route>`路径匹配的结果呈现的组件接收数据作为 props，然后可以用于创建嵌套路由。

+   **路由参数**：`<Route>`组件的路径可以配置为从 URL 段接受附加参数，并且可以在呈现的组件中读取这些参数。

+   **嵌套或动态路由**：可以在呈现的组件中添加`<Route>`组件，而不是在应用程序级别定义路由。因此，呈现的组件为应用程序旅程提供了下一步。

+   **从 JSON 配置生成路由**：JSON 对象中可用的路由信息可用于向应用程序添加路由。

# 路由 props

当您查看 React-Router 的源代码时，`<Route>`组件接受以下 props：

```jsx
Route.propTypes  = { computedMatch:  PropTypes.object, // private, from <Switch> path:  PropTypes.string, exact:  PropTypes.bool, strict:  PropTypes.bool, sensitive:  PropTypes.bool, component:  PropTypes.func, render:  PropTypes.func, children:  PropTypes.oneOfType([PropTypes.func, PropTypes.node]), location:  PropTypes.object };
```

让我们在下一节中看看这些 props 的每一个。

# exact prop

在我们之前的`<Route>`示例中，让我们将`'/home'`路由路径更改为`'/'`，如下所示：

```jsx
<div className="container">
     <Route
         path="/"
         component={HomeComponent} 
     />
     <Route
         path="/dashboard"
         component={DashboardComponent} 
     />
 </div>
```

有了这些路由，当浏览器的 URL 设置为`/dashboard`时，您会注意到两个组件的内容如下显示：

```jsx
Inside Home route
Inside Dashboard route
```

在这里，`'/dashboard'`中的`'/'`匹配`<Route>`的两个路径`'/'`和`'/dashboard'`；因此它从两个组件中呈现内容。要使浏览器的`location.pathname`与`<Route>`组件的路径完全匹配，请向`<Route>`添加 exact prop，如下所示：

```jsx
..
 <Route
     path="/"
     component={HomeComponent}
     exact
 />
 ..
```

类似地，当您尝试访问`'/dashboard'`和`'/dashboard/portfolio'`路径时，您会注意到在两种情况下都会呈现`DashboardComponent`。为了防止`'/dashboard/portfolio'`与具有`'/dashboard'`路径的`<Route>`组件匹配，添加`exact`属性。

React-Router 在内部使用`path-to-regexp`库来确定路由元素的路径属性是否与当前位置匹配。

# 严格属性

当`<Route>`路径有尾随斜杠，并且您希望将此路径与浏览器的 URL 匹配，包括尾随斜杠时，请包括`strict`属性。例如，在将`<Route>`路径从`'/dashboard'`更改为`'/dashboard/'`后，`<Route>`组件仍将匹配不带尾随斜杠的 URL 路径。换句话说，`'/dashboard'`将匹配具有`'/dashboard/'`路径的`<Route>`组件。

但是，在添加`strict`属性之后，React-Router 确保`<Route>`仅在 URL 有尾随斜杠时匹配：

```jsx
<Route
    path="/dashboard/"
    component={DashboardComponent}
    strict
/>
```

有了这个`<Route>`配置，`'/dashboard'`路径将不匹配。但是，当您在 URL 中添加尾随斜杠时，例如`'/dashboard/'`，具有`strict`属性的`<Route>`组件将匹配，并且将呈现`DashboardComponent`。

请注意，如果您提到额外的 URL 段，那么它仍将匹配`<Route>`组件中提到的`path`属性。例如，如果 URL 路径是`'/dashboard/123'`，它将与具有`strict`属性的`<Route>`组件匹配`'/dashboard/'`路径。要匹配包括额外 URL 段的路径，可以在`strict`属性旁边指定`exact`属性。

# 敏感属性

`<Route>`组件的路径不区分大小写，也就是说，`<Route>`组件的路径属性值设置为`'/Dashboard'`将匹配`'/dashboard'`或`'/DASHBOARD'`的 URL 路径。要使`<Route>`组件的路径区分大小写，添加`sensitive`属性：

```jsx
<Route
 path="/Dashboard" component={DashboardComponent} **sensitive** />
```

`sensitive`属性确保在将其与浏览器的 URL 路径匹配时，考虑路径属性的大小写。通过添加`sensitive`属性，可以使用不同的大小写定义具有相同路径名的路由。

```jsx
<Route
 path=**"/Dashboard"** component={DashboardComponent} **sensitive** /> <Route path=**"/dashboard"** component={StockListComponent} **sensitive** />
```

这段代码将创建两个不同的路由，并且当`<Route>`组件的区分大小写路径与浏览器的 URL 路径匹配时，将呈现相应的组件。

# 使用 render prop 进行内联渲染

我们已经看过`component`属性如何在`<Route>`路径匹配浏览器的`location.pathname`时用于渲染视图。还有两个其他可用于渲染视图的属性：`render`和`children`。

`render`属性用于内联渲染。作为`render`属性值的函数应返回一个类似于以下的 React 元素：

```jsx
<Route
    path="/user"
    render={() => (
 <div> Inside User Route </div>
 )}
/>
```

从前面的代码片段中，当`'/user'`路径匹配浏览器的 URL 时，作为`render`属性值指定的函数被执行，并且从该函数返回的 React 元素被渲染。

当在同一个`<Route>`组件中同时指定`component`和`render`属性时，`component`属性将优先。

# 使用 children 属性进行内联渲染

`children`属性应该在您想要渲染视图的情况下使用，无论是否有路径匹配。`children`属性的语法与`render`属性类似，如下所示：

```jsx
<Route
    path="/sidenav"
    children={() => (
 <div> Inside Sidenav route </div>
 )}
/>
```

具有`children`属性的`<Route>`组件即使未指定`path`属性也会被渲染。此外，`exact`和`strict`属性对具有`children`属性的`<Route>`组件没有任何影响。

`component`和`render`属性都优先于`children`属性。此外，当`component`或`render`属性被提及时，只有当路径匹配请求的 URL 时才会渲染视图。

基于路由列表中的位置，具有`children`属性的`<Route>`组件被渲染。例如，如果前一个`<Route>`组件被指定为路由列表中的最后一个条目，则在渲染所有先前匹配的路由之后被渲染。此外，如果前一个`<Route>`组件在匹配路由之前列出，则路由的内容在渲染匹配路由的内容之前被渲染，如下所示：

```jsx
<Route
 path="/sidenav"
 children={() => ( <div> Inside Sidenav route </div>
 )} /> <Route path="/user" render={() => ( <div> Inside User route </div> )} />
```

在这里，当您尝试访问`'/user'`路径时，具有`children`属性的`<Route>`组件在渲染`'/user'`路径之前被渲染。

# 路由组件属性

当`<Route>`路径匹配浏览器的 URL 路径时，被渲染的组件接收特定的`props`，例如`history`、`location`、`match`和`staticContext`。这些 props 提供的数据包括与路由相关的信息。这些 props 可用于使用`<Route>`组件的`component`、`render`或`children`属性渲染的组件。

当您在服务器端渲染应用程序时设置`staticContext`属性，并且在客户端路由器中（即使用`<BrowserRouter>`接口时）不可用（即设置为`undefined`）时。

# 历史

React-Router 依赖于`history`包。`history`是一个 JavaScript 库，用于在任何 JavaScript 应用程序中维护会话。请考虑来自`history`文档的以下引用（[`github.com/ReactTraining/history`](https://github.com/ReactTraining/history)）：

“**history**是一个 JavaScript 库，让您可以轻松地在 JavaScript 运行的任何地方管理会话历史。`history`抽象了各种环境的差异，并提供了一个最小的 API，让您可以管理历史堆栈、导航、确认导航和在会话之间保持状态。”

`history`对象有几个属性和方法：

+   动作：当前动作，`PUSH`、`POP`或`REPLACE`

+   长度：历史堆栈中条目的计数

+   位置：包括`hash`、`pathname`、`search`和`state`属性的当前位置

+   `hash`：哈希片段

+   `pathname`：URL 路径

+   `search`：URL 查询字符串

+   状态：使用`location.pushState`从一个路由导航到另一个路由时提供的状态信息

+   `block()`: 注册一个提示消息的函数，当用户尝试离开当前页面时将显示该消息。

+   `createHref()`: 构造 URL 段的函数；它接受一个带有`pathname`、`search`和`hash`属性的对象。

+   `go(n)`: 导航历史堆栈。`history.go(-1)`将指针向后移动一个位置，`history.go(1)`将指针向前移动一个位置。

+   `goBack()`: 将指针向后移动一个位置在`history`堆栈中；与`history.go(-1)`相同。

+   `goForward()`: 将指针向前移动一个位置在`history`堆栈中；与`history.go(1)`相同。

+   `listen(listenerFn)`: 注册一个监听器函数，每当`history.location`发生变化时就会调用该函数。

+   `push(path, state?)`: 导航到给定的路径名，向`history`堆栈添加一个条目。它可以选择接受一个`state`参数，用于传递应用程序状态数据。

+   `replace(path, state?)`: 一个函数，用于导航到给定的路径名，替换`history`堆栈中的当前条目。它还接受一个可选的`state`参数。

`history`对象由 React-Router 在内部使用，用于在用户尝试在页面之间导航时更新历史堆栈中的条目。它作为 prop 提供给渲染的组件，以便用户可以使用`history`对象中的上述方法导航到不同的页面。在下一章中，我们将看看 React-Router 提供的各种 API，帮助您导航到应用程序中定义的不同路由。

# 位置对象

`location`对象提供了表示应用程序当前状态的数据快照。它包括以下属性：`pathname`、`hash`、`search`和`state`。导航组件可以为这些 prop 提供值，然后由匹配浏览器 URL 的渲染组件读取。如前所述，我们将在第三章中看看各种导航组件，*使用 Link 和 NavLink 组件导航到路由*。

位置信息也可以在`history`对象中找到；但是，`history`对象是可变的，因此应避免在`history`对象中访问位置。

# 匹配对象

`match`对象包含有关`<Route>`路径如何匹配当前 URL 的信息。它包括`url`、`path`、`isExact`和`params`属性。

让我们参考之前使用`render` prop 的路由之一：

```jsx
<Route
 path="/user" render={({ match }) => { console.log(match);
        return ( <div> Inside User route </div> ); }} />
```

当您尝试访问`/user`路径时，`match`对象的属性将具有以下值：

```jsx
url - '/user'
path - '/user'
params - {}
isExact - true
```

+   `url`: 返回 URL 的匹配部分的字符串

+   `path`: 返回路由路径字符串的字符串，即在`<Route>`组件的路径 prop 中提到的路径模式

+   `params`: 包含传递给路由的路径参数列表的对象（在接下来的部分中将更多地介绍参数）

+   `isExact`: 一个布尔值；如果 URL 完全匹配提供的`path` prop，则为`true`

如果 URL 段的部分仅匹配`<Route>`组件的路径，则`isExact`属性为`false`。例如，具有`/user`路径的`<Route>`组件与`/user/123`的 URL 不完全匹配，在这种情况下，`isExact`为 false。

如前所述，带有 `children` 属性的 `<Route>` 组件会被渲染，无论 `path` 属性是否匹配浏览器的 URL 路径。在这种情况下，如果路径不匹配 URL 段，`match` 对象将被设置为 null：

```jsx
<Route
 path="/sidenav" children={({ match }) => { console.log(match) return ( <div> Inside Sidenav route </div> ); }} />
```

使用这个 `<Route>` 配置时，当您尝试访问 `/user` 路径时，将匹配带有 `/sidenav` 路径的 `<Route>` 组件，因为它有一个 `children` 属性。然而，在这里，`match` 对象被设置为 null。这有助于确定带有 `children` 属性的 `<Route>` 组件的路径是否匹配了 URL 段。

# 路由参数

在 React-Router 中，可以配置 `<Route>` 组件来接受给定对象的 URL 参数。例如，要显示给定 `userID` 的用户信息，URL 路径可能看起来像 `'/user/1'`（`userID` 为 `'1'` 的用户）和 `'/user/123'`（`userID` 为 `'123'` 的用户）。URL 的最后部分是动态的；然而，在每种情况下，渲染的组件都会对给定的 `userID` 执行相同的操作。

这样的用例示例是 Twitter 的个人资料页面。该页面接受 `twitterID` 并显示给定用户的动态。

在 `to` 属性中附加一个以冒号 (:) 为前缀的额外路径，可以配置 React-Router 中的 `<Route>` 组件来接受 URL 中的动态部分，如下所示：

```jsx
<Route
 to='/github/**:githubID**'
    component={GitHubComponent}  />
```

在这里，`'/:githubID'` 路径是动态的，可以匹配诸如 `'/github/ryanflorence'` 和 `'/github/mjackson'` 这样的路径（React-Router 的创建者的 GitHub ID）。

然后，可以在渲染的组件中使用 `match.params` 来使用这些匹配的 URL 参数：

```jsx
export  class  GitHubComponent  extends  Component { render() { const { match: { params } } =  this.props; return ( <div> In GitHubComponent <br  /> GitHub ID - {params.githubID} </div> ) } }
```

当您尝试访问 `'/github/mjackson'` URL 路径时，您将看到这条消息：

```jsx
In GitHubComponent
GitHub ID - mjackson
```

`match.params` 对象包含路由中匹配参数的键值对。`<Route>` 组件也可以接受 URL 中的多个参数，如下所示：

```jsx
<Route
 path="/github/**:githubID**/**:twitterID**" component={GitHubComponent} />
```

在这里，`githubID` 和 `twitterID` 参数是动态的，可以匹配 URL 路径，比如 `'/github/ryanflorence/mjackson'`。第二个参数 `twitterID` 可以在组件中使用 `match.params.twitterID` 进行读取。

在之前的 `<Route>` 配置中，`githubID` 和 `twitterID` 参数是必需的参数，也就是说，如果 URL 路径中没有这两个参数，路由就不会匹配。要将参数标记为可选的，可以在参数后面加上问号 (`?`)，如下面的代码片段所示：

```jsx
<Route
 path="/github/:githubID/**:twitterID?**" component={GitHubComponent} />
```

在前面的`<Route>`配置中，`twitterID`参数被标记为可选。这意味着当您尝试访问`'/github/ryanflorence'`路径，即在 URL 中不提供`twitterID`参数的值时，路径将匹配 URL 并渲染组件。然而，当组件尝试使用`match.params.twitterID`访问参数时，它将返回`undefined`。

`<Route>`路径也可以配置为接受与正则表达式匹配的参数，如下所示：

```jsx
...
<Route
 path="/github/**:githubID(\w+)**" component={GitHubComponent} /> <Route path="/user/**:userID(\d+)**" component={UserComponent} />
...
```

在这里，`githubID`参数限制为字母数字字符串，`userID`参数限制为数字值。参数后缀有一个正则表达式模式，用于定义`<Route>`参数将接受的值的类型，即限制可以提供给参数的值的模式。

# 嵌套路由和动态路由

React-Router 的早期版本要求预先定义路由，并将子路由嵌套在另一个路由内，如下所示：

```jsx
<Router>
    <Route path='/' component={Container}>
        <IndexRoute component={Home} />
        <Route path='user' component={User}>
            <IndexRoute component={Twitter} />
            <Route path='instagram' component={Instagram} />
        </Route>
    </Route>
</Router>
```

这段代码可以被认为是静态路由，即在应用程序初始化时，库需要路由配置。在这里，具有`'/'`路径的路由作为所有路由的父路由，具有`'user'`路径的路由是`'/'`的子路由，也是具有`'instagram'`路径的路由的父路由。

在 React-Router v4 中，可以在渲染的组件内定义嵌套路由，也就是说，随着用户在应用程序中导航，路由会被注册。通过 v4 的重写，`<Route>`是一个 React 组件，因此可以包含在任何组件的`render`方法中。

考虑在`App.js`（`<App />`根组件）中定义的父路由：

```jsx
<Route
 path="/category" component={CategoryComponent} />
```

在这里，`'/category'`路径映射到`CategoryComponent`组件。

`CategoryComponent`可以反过来使用相同的`<Route>`组件渲染其他路由。然而，在渲染组件（`CategoryComponent`）内部定义路由时，需要在`<Route>`组件的`to`属性中指定对当前匹配 URL 的引用。例如，可以使用`<Route>`组件创建一个带有`'/pictures'`路径的子路由；然而，在`to`属性中需要指定绝对路径，即`'/category/pictures'`或更一般地，`'/<current_matching_url>/pictures'`。

如前所述，传递给呈现组件的`match`属性包含有关路径如何匹配当前 URL 的信息。`match`属性的 URL 属性可用于引用父 URL：

```jsx
export  const  CategoryComponent  = ({ match }) => { return ( <div  className="nested-route-container"> <div  className="root-info"> <h4> Root: </h4> <h5> path: {match.path}, isExact: {match.isExact.toString()}</h5> </div> <Route path={`${match.url}/pictures`} render={({ match }) => { return ( <div> <h4> Viewing pictures: </h4> <h5> path: {match.path}, 
                                 isExact: {match.isExact.toString**()}** </h5> </div>  ) }} /> <Route path={`${match.url}/books`} render={({ match }) => { return ( <div> <h4> Viewing books: </h4> <h5> path: {match.path},
                                 isExact: {match.isExact.toString()**}** </h5> <Route path={`${match.url}/popular`} render={({ match }) => ( <div> Inside popular, 
                                          path: {match.path} </div> )}  /> </div>  ) }} /> </div> ) }
```

在前面片段中定义的`CategoryComponent`接受`match`属性，并且组件中定义的路由具有`'${match.url}/<child_route_path>'`格式的路径值。`match.url`模板变量包含父路由的 URL 值，在本例中为`/category`。使用相同的原则，还定义了路径为`'/category/pictures'`和`'/category/books'`的路由。

让我们测试这些路由：

+   **场景 1**：`location.pathname` 是 `'/category'`：

在这里，将呈现父路由，并且页面将呈现如下路由信息：

```jsx
         Root:
         path: /category, isExact: true
```

在这里，`match.isExact`为 true，因为在`/category`路径之后没有其他 URL 段。

+   **场景 2**：`location.pathname` 是 `'/category/pictures'` 或 `'/category/books'`：

呈现`'/category'`父路由后，库会查找具有`'/category/pictures'`和`'/category/books'`路径的`<Route>`组件。它找到一个并呈现相应的组件：

```jsx
            Root:
            path: /category, isExact: false
            Viewing pictures:
           path: /category/pictures, isExact: true
```

现在，在父路由（具有`'/category'`路径的`<Route>`组件）中，`match.isExact`为 false；但是在子路由中为 true。

+   **场景 3**：`location.pathname` 是 `'/category/books/popular'`：

您可以嵌套任意多个路由。在这里，`'/books'` 是一个嵌套路由，并且还有另一个嵌套路由，`'/popular'`，它匹配了`'/category/books/popular'`路径：

```jsx
              Root:path: /category, 
              isExact: false
              Viewing books:
             path: /category/books, isExact: false
             Inside popular, 
             path: /category/books/popular
```

`match`属性在创建嵌套路由时非常有用。这些嵌套路由只有在呈现其父路由时才可访问，从而允许您动态添加路由。

# 来自 JSON 的动态路由

还可以通过查找包含路由配置选项集合的数组来生成一组`<Route>`组件。每个路由选项应包含必要的详细信息，如`'path'`和`'component'`。

一组路由可能如下所示：

```jsx
const  STOCK_ROUTES  = [ { path:  'stats', component:  StatsComponent, }, { path:  'news', component:  NewsComponent }, { path:  'trending', component:  TrendingComponent  } ];
```

前面数组中的每个对象都包含一个指定路由路径的`'path'`键，以及包含用户访问路由时要呈现的组件的引用的`'component'`键。然后可以在组件的`render`方法中使用前面的集合来生成一组`<Route>`组件，如下所示：

```jsx
...
render() {
 const { match } =  this.props; return ( <div> Inside Stocks, try /stocks/stats or /stocks/news or /stocks/trending { STOCK_ROUTES.map((route, index) => { return ( <Route key={index} path={`${match.url}/${route.path}`} component={route.component} /> ) **})** } </div> ); }
...
```

在`STOCK_ROUTES`中定义的路由配置用于在`StockComponent`渲染时添加一系列`<Route>`组件。父级`<Route>`组件在`'/stocks'`路径处渲染，因此在生成`'/stocks'`路径下的`<Route>`组件时使用了`match.url`。

# 总结

在本章中，我们了解到`<Route>`组件可以使用各种 props 进行配置。这包括使用`exact` prop 仅在浏览器的 URL 路径与`<Route>`组件中的路径值匹配时才渲染组件；在`<Route>`组件中使用`strict` prop 确保 URL 路径与`path` prop 中指定的尾部斜杠匹配；包括`sensitive` prop 使`path` prop 的值区分大小写；以及使用`render`和`children` props 进行内联渲染。带有`children` prop 的`<Route>`组件会渲染，而不管`path` prop 中指定的值是什么。这在页面布局中有多个视图组件并且这些组件应该渲染时非常有用，而不管`path` prop 中指定的值是什么。

由于`<Route>`路径匹配的结果组件可以接收数据作为 props。这包括 props，如`history`、`location`、`match`和`staticContext`。`match` prop 可用于创建嵌套路由，即`match` prop 中的`url`属性包含的信息可以用于渲染组件中包含的`<Route>`组件的`path` prop 中。`<Route>`组件也可以通过查找对象中指定的配置来添加。然后可以使用包含`path`和`component`信息的数组来在应用程序中添加多个路由。

`<Route>`组件的`path` prop 可以配置为接受 URL 段作为路径参数。然后渲染的组件可以使用`match.params`来读取这些参数。可以通过在`path`参数的后缀中指定正则表达式来配置参数以接受特定值。
