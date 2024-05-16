# 第四章：使用重定向和切换组件

使用 React-Router 的`<Redirect>`组件可以将用户从一个路由重定向到另一个路由。在传统网站中，页面是在服务器端呈现的，托管应用程序的 Web 服务器配置了重写规则，将用户重定向到不同的 URL。当内容已经移动到新页面或者网站的某些页面仍在建设中时，可以使用此重定向。HTTP 重定向是一项昂贵的操作，因此也会影响应用程序的性能。

在单页应用程序（SPA）中，重定向发生在浏览器上，根据特定条件将用户重定向到不同的路由。这种重定向更快，因为没有涉及 HTTP 往返，并且转换类似于使用`<Link>`或`<NavLink>`组件从一个路由导航到另一个路由。

本章讨论以下主题：

+   `<Redirect>`组件：将用户从一个路由重定向到另一个路由

+   保护路由和授权：一种情况是当用户尝试访问受保护的路由时，将用户重定向到登录页面

+   `<Switch>`组件：渲染第一个匹配的`<Route>`

+   添加 404 页面未找到页面：一种情况是当没有任何`<Route>`组件匹配浏览器的 URL 路径时，使用`<Switch>`和`<Route>`或`<Switch>`和`<Redirect>`组件来渲染 404 页面

# `<Redirect>`组件

`<Redirect>`组件包含在`react-router-dom`包中。它帮助将用户从包含它的组件重定向到`'to'`属性中指定的路由：

```jsx
import { Redirect } from 'react-router-dom';

export class HomeComponent extends Component {
    render() {
        return (
            <Redirect to='/dashboard' />
        )
    }
}
```

在上述情况下，当`HomeComponent`被渲染时（基于`<Route>`匹配），用户将被重定向到`'/dashboard'`路由。例如，当用户访问主页（路径为`'/'`）时，具有路径`'/'`的`<Route>`会渲染先前的组件，然后用户立即被重定向到具有路径值`'/dashboard'`的`<Route>`。这类似于使用带有`'to'`属性的`<Link>`或`<NavLink>`组件将用户导航到不同的路由。在这里，重定向发生在组件被渲染时，而不是作为用户操作的结果触发导航。

先前提到的重定向示例在应用程序中的某些页面已经移动到不同的目录的情况下是理想的。

`<Redirect>`组件类似于 React-Router 中的其他组件，如`<Route>`和`<Link>`。正如之前观察到的那样，它是一个可以包含在渲染函数中的 React 组件。此外，`<Redirect>`组件接受与`<Link>`组件相似的一组 props。

# to 属性

to 属性用于指定用户应该被重定向到的路由。如果找到匹配的`<Route>`，用户将被重定向到指定的路径，并渲染相应的组件。

to 属性还可以接受一个对象，该对象指定了`pathname`、`search`、`hash`和`state`属性的值：

```jsx
<Redirect 
    to={{
        pathname: '/dashboard',
        search: '?q=1',
        hash: '#hash',
        state: { from: match.url }
      }} 
/>
```

与`<Link>`组件类似，前面提到的属性被指定在`<Redirect>`组件的 to 属性中。请注意，状态属性的值为`{ from: match.url }`。在这里，`match.url`提供了浏览器 URL 路径的当前值，然后在重定向发生时将该值提供给渲染的组件。

然后，渲染的组件可以使用`this.props.location.state`来读取状态信息：

```jsx
export class DashboardComponent extends Component {
    render() {
        const { location } = this.props;
        return (
            <div>
                In DashboardComponent <br />
                From : {location.state.from}
            </div>
        )
    }
}
```

在前面的示例中，`DashboardComponent`作为从`HomeComponent`重定向的结果进行渲染。`location.state.from`的值与被重定向的组件共享有关重定向发生的页面的路径信息。当您希望被重定向到一个通用页面，并且被重定向的页面必须显示有关重定向发生的路径的信息时，这将非常有用。例如，当应用程序发生错误时，用户应该被重定向到一个呈现错误消息的页面，提供有关发生错误的页面的信息。在这种情况下，状态信息可以包括属性`errorMessage`和`from`；后者的值为`match.url`，即发生错误的页面。

如果未找到被重定向的`<Route>`，浏览器的 URL 将被更新，不会抛出错误。这是有意设计的；理想情况下，如果没有匹配的路由，用户应该被重定向到一个`404`或`Page Not Found`页面。在下一节中将讨论当没有匹配时渲染的`<Route>`。

在组件内部，当您尝试重定向到相同的路由时，React-Router 会抛出警告消息 Warning: You tried to redirect to the same route you're currently on: `"/home"`。这个检查确保重定向不会导致无限循环。

还可能遇到这样的情况，被重定向的组件在其渲染方法中包含一个`<Redirect>`，将用户重定向回相同的路由，也就是说，按照这个路由重定向`path: /home => /dashboard => /home`。这样会一直循环，直到 React 停止渲染组件；然后 React 会抛出一个错误，最大更新深度超过。当组件在`componentWillUpdate`或`componentDidUpdate`中重复调用`setState`时，就会发生这种情况。React 限制了嵌套更新的次数，以防止无限循环。React-Router 使用状态来跟踪用户在应用程序旅程中的位置，因此在重定向时，由于重定向的原因，React 尝试多次更新状态而导致前面的错误发生。在处理重定向时，您需要确保它不会导致无限循环的重定向。

# 推送属性

`<Redirect>`组件通过调用`history.replace(<path>)`将用户重定向到给定的路径，即用新路径替换历史堆栈中的当前条目。通过在`<Redirect>`组件中指定推送属性，将调用`history.push`而不是`history.replace`：

```jsx
<Redirect to="/dashboard" push />
```

# 保护路由和授权

使用`<Route>`组件定义的路由可以通过浏览器的 URL 访问，通过使用`<Link>`或`<NavLink>`导航到路由，或者通过使用`<Redirect>`组件将用户重定向。但是，在大多数应用程序中，一些路由应该只对授权或已登录的用户可用。例如，假设`/user`路径显示已登录用户的数据；这个路径应该受到保护，只有已登录用户才能访问该路径。在这些情况下，当您尝试访问路径`/user`时，`<Redirect>`组件非常有用，它会将用户重定向到登录页面（在路径`/login`）。

为了证明这一点，让我们创建一个名为`UserComponent`的组件，当您尝试访问路径`/user`时，它将被渲染出来：

```jsx
export class UserComponent extends Component {
    render() {
        const { location } = this.props;
        return (
            <div>
                Username: {location && location.state ? location.state.userName 
                 : ''} <br />
                From: {location && location.state ? location.state.from : ''} 
                 <br />
                <button onClick={this.logout}>LOGOUT</button>
            </div>
        )
    }
}
```

从前面的代码片段中，我们可以看到`UserComponent`显示了在`this.props.location`中可用的状态信息和 LOGOUT 按钮。

要检查用户是否已登录，应该向服务器发出请求，以检查用户的会话是否存在。但是，在我们的情况下，通过引用浏览器的`localStorage`中的变量来检查用户是否已登录：

```jsx
export class UserComponent extends Component {
    state = {
       isUserLoggedIn: false
    }
    componentWillMount() {
        const isUserLoggedIn = localStorage.getItem('isUserLoggedIn');
        this.setState({isUserLoggedIn});
    }
    render() {
    ...
    }
}
```

在这里，组件的状态属性`isUserLoggedIn`将使用存储在同名 localStorage 变量中的值进行更新。

下一步是在`UserComponent`类的渲染函数中使用此状态信息，并使用`<Redirect>`组件重定向用户：

```jsx
export class UserComponent extends Component {
    ...
    render() {
        const { location } = this.props;
        if (!this.state.isUserLoggedIn) {
            return (
                <Redirect to="/login" />
            );
        }
        ...
    }
}
```

在这里，将检查状态属性`isUserLoggedIn`的值，如果评估为 false，或者未找到，则将用户重定向到路径`'/login'`。

最后一步是实现`logout`函数，当用户点击 LOGOUT 按钮时调用：

```jsx
export class UserComponent extends Component {
    logout = (event) => {
        localStorage.removeItem('isUserLoggedIn');
        this.setState({ isUserLoggedIn: false });
    }
    ...
}
```

登出用户涉及删除`localStorage`变量并将状态属性`isUserLoggedIn`更新为`'false'`。

有了这些更改，当状态属性`isUserLoggedIn`设置为 false 时，`UserComponent`会重新渲染，并将用户重定向到路径`/login`，要求用户提供凭据以访问页面。此外，现在当您尝试通过在浏览器地址栏中输入路径`/user`来访问时，具有路径属性`/user`的`<Route>`将匹配。然而，当`UserComponent`被渲染时，状态属性`isUserLoggedIn`将评估为 false，将用户重定向到`/login`页面。

# 使用回调路由进行重定向

当您尝试访问受保护的`<Route>`时，将被重定向到登录页面以提供凭据。提供凭据后，您应该被重定向到之前尝试访问的页面。例如，当您尝试访问路径`/stocks`的受保护路由时，您将被重定向到路径`/login`，然后，在提供正确的凭据后，您应该被重定向到之前尝试访问的相同路径`/stocks`。然而，根据先前的示例，您将被重定向到路径`/user`，并显示用户的个人资料信息。期望的行为是重定向到受保护的路径`/stocks`，而不是路径`/user`。

这可以通过在重定向用户时提供状态信息来实现。

在`StocksComponent`（作为`<Route>`匹配结果呈现的组件，`/stocks`），当您将用户重定向到登录页面时，在 to 属性中提供状态信息：

```jsx
export class StocksComponent extends Component {
    ...
    render() {
        const {match } = this.props;
        if (!this.state.isUserLoggedIn) {
            return (
                <Redirect 
                    to={{
                        pathname: "/login",
                        state: { callbackURL: match.url }
                    }}
                />
            )
        }

        return (
            <div>
                In StocksComponent
            </div>
        )
    }
}
```

在组件的渲染函数中，用户使用`<Redirect>`组件被重定向到登录页面。这里的`<Redirect>`组件包括一个 to 属性，指定用户应该被重定向到的`pathname`，它还包括一个状态对象，提到了`callbackURL`属性。`callbackURL`属性的值是`match.url`，即当前浏览器的 URL 路径`/stocks`。

然后可以在`LoginComponent`中使用这些状态信息将用户重定向到路径`/stocks`：

```jsx
export class LoginComponent extends Component {
    ...
    render() {
        const { location: { state } } = this.props;
        if (this.state.isUserLoggedIn) {
            return (
                <Redirect 
                    to={{
                        pathname: state && 
                        state.callbackURL || "/user",
                        state: {
                            from: this.props.match.url,
                            userName: this.state.userName
                        }
                    }} 
                />
            )
        }
        ...
    }
}
```

在这里，当用户提供凭据访问受保护的路由时，`<Redirect>`组件将用户重定向到`state.callbackURL`中提到的路径。如果`callbackURL`不可用，用户将被重定向到默认路由，该路由将重定向到路径`/user`。

Route 组件的 props、`match.url`和 location.state 的组合可以用来将用户重定向到之前请求的受保护路由。

# 使用<Switch>组件进行独占路由

当 URL 被提供给`<BrowserRouter>`时，它将寻找使用`<Route>`组件创建的路由，并渲染所有与浏览器 URL 路径匹配的路由。例如，考虑以下路由：

```jsx
<Route
    path="/login"
    component={LoginComponent}
/>
<Route
    path="/:id"
    render={({ match }) => 
        <div> Route with path {match.url}</div>
    }
/>
```

在这里，具有路径`/login`和`/:id`的两个路由都匹配`/login`的 URL 路径。React-Router 渲染所有与 URL 路径匹配的`<Route>`组件。然而，为了只渲染第一个匹配的路由，该库提供了`<Switch>`组件。`<Switch>`组件接受一组`<Route>`组件作为其子组件，并且只渲染与浏览器 URL 匹配的第一个`<Route>`：

```jsx
<Switch>
    <Route
        path="/login"
        component={LoginComponent}
    />
    <Route
        path="/:id"
```

```jsx
        render={({ match }) =>
            <div> Route with path {match.url}</div>
        }
    />
</Switch>
```

通过将一组`<Route>`组件包装在`<Switch>`组件内，React-Router 会顺序搜索与浏览器 URL 路径匹配的`<Route>`。一旦找到匹配的`<Route>`，`<Switch>`就会停止搜索并渲染匹配的`<Route>`。

在上面的例子中，如果浏览器的 URL 路径是/login，那么`<Switch>`中的第一个`<Route>`将被渲染，而除/login 之外的路径（如/123、/products、/stocks 等）将匹配第二个路由并渲染相应的组件。

如果交换前两个`<Route>`组件的顺序（即，将具有路径/:id 的`<Route>`列在具有路径/login 的`<Route>`之上），那么具有路径/login 的`<Route>`将永远不会被渲染，因为`<Switch>`只允许渲染一个第一个匹配的路由。

# <Switch>中<Route>组件的顺序

`<Switch>`中`<Route>`组件的顺序很重要，因为`<Switch>`组件会顺序查找匹配的`<Route>`，一旦找到与浏览器 URL 匹配的`<Route>`，就会停止搜索。这种行为可能不是期望的，您可能希望渲染`<Switch>`中列出的另一个路由。但是，可以通过更改在`<Switch>`中列出`<Route>`的顺序来纠正这一点：

在以下示例中，提到了在`<Switch>`中列出`<Route>`组件时的一些常见错误：

# 带有路径'/'的`<Route>`作为`<Switch>`的第一个子级

考虑以下代码片段：

```jsx
<Switch>
    <Route
        path="/"
        component={LoginComponent}
    />
    <Route
        path="/dashboard"
        component={DashboardComponent}
    />
</Switch>
```

如果浏览器的 URL 路径是`/dashboard`，它将匹配第一个路径为`/`的`<Route>`，而路径为`/dashboard`的`<Route>`将永远不会匹配和渲染。要解决这个问题，要么包括`exact`属性，要么将路径为`/`的`<Route>`列为`<Switch>`中的最后一个条目。

# 带有路径参数的`<Route>`

在以下代码片段中，将带有路径参数的`<Route>`列为第二个条目：

```jsx
<Switch>
    <Route
        path="/github"
        component={LoginComponent}
    />
    <Route
        path="/github/:userId"
        component={DashboardComponent}
    />
</Switch>
```

在上一个示例中，路径为`/github`的`<Route>`将匹配 URL 路径`/github`以及路径`/github/mjackson`；因此，即使有特定路径的`<Route>`可用，第一个`<Route>`也会被渲染。要解决这个问题，要么提供`exact`属性，要么将路径为`/github`的`<Route>`列在路径为`/github/:userId`的`<Route>`下面。

从前一段提到的两种情况中，将具体路径的`<Route>`组件列在通用路径的`<Route>`组件上面，可以避免不良结果。

# 添加 404 - 未找到页面

如前所述，`<Switch>`组件会顺序查找所有`<Route>`组件，一旦找到与浏览器 URL 匹配的`<Route>`，就会停止搜索。这与在页面中列出`<Route>`的列表不同，页面中的每个匹配的`<Route>`都会被渲染。因此，`<Switch>`非常适合渲染`Page Not Found`页面，即在`<Switch>`的子级中没有任何匹配浏览器 URL 的`<Route>`时渲染一个组件。

让我们在`<Switch>`中包含一个没有路径属性的`<Route>`作为最后一个条目：

```jsx
<Switch>
    <Route
        path="/login"
        component={LoginComponent}
    />
    <Route
        path="/user"
        render={({ match }) =>
            <div> Route with path {match.url}</div>
        }
    />
    <Route
        render={({ location }) =>
            <div> 404 - {location.pathname} not 
            found</div>
        }
    />
</Switch>
```

从前面的代码片段中，我们可以看到当没有任何带有路径属性的`<Route>`与浏览器的 URL 匹配时，最后一个没有路径属性的`<Route>`将匹配并渲染。

包括`Page Not Found <Route>`作为最后一个条目是很重要的，因为`<Switch>`组件一旦找到匹配的`<Route>`就会停止搜索。在前面的情况下，如果没有属性的`<Route>`被包括在其他`<Route>`上面，那么即使列表中存在与浏览器 URL 匹配的`<Route>`，`Page Not Found`路由也会被渲染。

您还可以指定一个`<Route>`，其路径属性值为`*`，而不是没有路径属性的`<Route>`，以渲染`Page Not Found`页面：

```jsx
<Switch>
    ...
    <Route
        path="*"
        render={({ location }) =>
            <div> 404 - {location.pathname} not 
            found</div>
        }
    />
</Switch>
```

在这两种情况下，路径将匹配浏览器的 URL 并渲染`Page Not Found`页面。

# 在`<Switch>`中使用`<Redirect>`重定向到一个 Page Not Found 页面

`<Switch>`组件的子元素也可以包括一系列`<Route>`和`<Redirect>`组件。当包括为`<Switch>`中的最后一个条目时，`<Redirect>`组件将在没有任何在`<Redirect>`组件上面提到的`<Route>`匹配浏览器 URL 时将用户重定向到给定路径：

```jsx
<Switch>
    <Route
        path="/login"
        component={LoginComponent}
    />
    <Route
        path="/user"
        render={({ match }) =>
            <div> Route with path {match.url}</div>
        }
    />
    <Redirect to="/home" />
</Switch>
```

前面提到的`<Redirect>`组件将用户重定向到路径为`/home`的`<Route>`。这类似于显示`404：Page Not Found`页面；而不是在行内显示组件，用户被重定向到不同的路径。

例如，如果浏览器的 URL 路径是`/dashboard`，前两个路径（路径为`/login`和`/user`）不会匹配，因此用户将使用在`<Switch>`中作为最后一个条目提到的`<Redirect>`组件进行重定向。

# 从旧路径重定向到新路径

`<Redirect>`组件也可以用于将用户从给定路径重定向到新路径。`<Redirect>`组件接受一个`from`属性，该属性可用于指定应该匹配用户应该被重定向的浏览器 URL 的路径。此外，应该在`to`属性中指定用户应该被重定向到的路径。

```jsx
<Switch>
    <Route
        path="/login"
        component={LoginComponent}
    />
    <Route
        path="/user"
        render={({ match }) =>
            <div> Route with path {match.url}</div>
        }
    />
    <Redirect
        from="/home"
        to="/login"
    />
    <Redirect to="/home" />
</Switch>
```

从前面的例子中，我们可以看到当浏览器的 URL 路径是`/home`时，具有`from`属性的`<Redirect>`组件将匹配给定路径并将用户重定向到路径为`/login`的`<Route>`。

`<Redirect>`组件的`from`属性在网站上的一些页面已经移动到新目录时非常有用。例如，如果用户页面已经移动到新的目录路径`settings/user`，那么`<Redirect from="/user" to="/settings/user" />`将把用户重定向到新路径。

# 总结

`<Redirect>`组件可用于将用户从当前渲染的路由重定向到新的路由。该组件接受 props：to 和 push。当应用程序中的组件已经移动到不同的目录，或者用户未被授权访问页面时，可以使用此重定向。`<Redirect>`组件在用户访问受保护的路由并且只有授权用户被允许查看页面时非常有用。

`<Switch>`组件用于在一组`<Route>`中只渲染一个`<Route>`。`<Switch>`组件接受`<Route>`和`<Redirect>`组件的列表作为其子组件，并依次搜索匹配的`<Route>`或`<Redirect>`组件。当找到匹配时，`<Switch>`渲染该组件并停止寻找匹配的路径。

`<Switch>`的这种行为可以用来构建一个`404：页面未找到`，当`<Switch>`中列出的`<Route>`组件都不匹配浏览器的 URL 路径时，将会渲染该页面。通过在`<Switch>`的最后一个条目中列出一个没有任何路径属性的`<Route>`，如果上面列出的`<Route>`组件都不匹配浏览器的 URL 路径，那么将会渲染该`<Route>`。另外，也可以将`<Redirect>`组件列为最后一个条目，以在`<Switch>`中没有匹配的`<Route>`组件时将用户重定向到另一个页面。
