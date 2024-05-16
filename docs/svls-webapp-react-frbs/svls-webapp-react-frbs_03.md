# 第三章：使用 Firebase 进行认证

在上一章中，我们学习了如何将 Firebase 与 ReactJS 集成，以及如何在 JSX 中创建组件。我们还看到了如何与 DOM 元素交互以获取`onSubmit`表单值，并将其发送到 Firebase 数据库中以在云中存储和同步表单数据。React 使用快速、内部的合成 DOM 来执行差异，并为您计算最有效的 DOM 变化，其中您的组件活动地存在。

在本章中，我们将使用 React 和 JSX 创建一个`login`组件，以使用 Firebase 认证功能来保护我们的帮助台应用程序，该功能只允许授权用户查看和添加新的工单。

以下是本章我们将重点关注的内容列表：

+   使用 Node.js 进行 React 和 Firebase 设置

+   使用 React 和 JSX 创建复合组件

+   Firebase 认证配置

+   自定义认证

+   使用 Facebook 和 Google 进行第三方认证

# 使用 Node.js 进行 React 和 Firebase 设置

之前，我们使用纯 JavaScript 创建了一个 React 应用程序；现在我们需要使用 React 和 Firebase 设置来使用 node 做同样的事情。为此，我们必须在系统中安装 Node.js 和`npm`；如果没有，请先从[`nodejs.org/en/download/`](https://nodejs.org/en/download/)下载 Node.js。安装完成后，运行以下命令以确保 node 和`npm`已正确安装：

对于 node，使用以下命令：

```jsx
node -v
```

对于`npm`，使用以下命令：

```jsx
npm -v
```

命令的输出应该如下所示：

![](img/c9177530-5920-4bb6-a7c5-c0db7e000ac3.png)

现在我们需要安装`create-react-app`模块，它提供了初始和默认设置，并让我们快速启动 React 应用程序。在 CMD 中运行以下命令，它将全局安装`create-react-app`模块（即在命令后加上`-g`或`--global`）：

```jsx
npm install -g create-react-app 
or 
npm i -g create-react-app
```

安装完成后，在需要创建项目的本地目录中运行下一个命令；这将为 React 生成无需构建配置的快速启动项目：

```jsx
create-react-app <project-name> 
or
create-react-app login-authentication
```

安装完成后，我们的文件夹结构如下所示：

![](img/14f5b27a-0d25-48c3-bced-3c36f661653d.png)

在这里，我们已经完成了 React 的设置；现在，我们安装`firebase npm`包并集成我们现有的应用程序。

运行以下命令安装`firebase npm`包：

```jsx
npm install firebase --save
```

安装 firebase 后，在`src`文件夹内创建一个名为 firebase 的文件夹。

在`src`文件夹中，创建一个名为`firebase-config.js`的文件，其中将托管我们项目的配置详细信息：

```jsx
import  firebase  from  'firebase'; const  config  = {  apiKey:  "AIzaSyDO1VEnd5VmWd2OWQ9NQuh-ehNXcoPTy-w",
  authDomain:  "demoproject-7cc0d.firebaseapp.com",
  databaseURL:  "https://demoproject-7cc0d.firebaseio.com",
  projectId:  "demoproject-7cc0d",
  storageBucket:  "demoproject-7cc0d.appspot.com",
  messagingSenderId:  "41428255556" }; firebase.initializeApp(config); export  default  firebase;
```

同样，我们需要在节点中集成我们现有的组件视图票和`addTicket`，使用导入和导出关键字，并使用`npm`命令，我们需要安装 React 和 firebase 模块及其依赖项。

这是您的`package.json`应该看起来的样子：

```jsx
//package.json
{
 "name": "login-authentication",
 "version": "0.1.0",
 "private": true,
 "dependencies": {
 "firebase": "⁴.8.0",
 "react": "¹⁶.2.0",
 "react-dom": "¹⁶.2.0",
 "react-router-dom": "⁴.2.2",
 "react-scripts": "1.0.17",
 "react-toastr-basic": "¹.1.14"
 },
 "scripts": {
 "start": "react-scripts start",
 "build": "react-scripts build",
 "test": "react-scripts test --env=jsdom",
 "eject": "react-scripts eject"
 }
}
```

此外，在集成现有应用程序后，应用程序文件夹结构如下所示：

![](img/fca7a632-68f0-47ec-b404-0be0a1903c43.png)

# 用于身份验证的 Firebase 配置

Firebase 身份验证是一个非常令人印象深刻的功能，可以通过安全规则授予用户读/写访问权限。我们还没有在我们的帮助台应用程序中涵盖或添加安全规则。Firebase 让我们能够使用其自己的电子邮件/密码和 OAuth 2 集成来进行 Google、Facebook、Twitter 和 GitHub 的认证。我们还将把我们自己的身份验证系统与 Firebase 集成，以便让用户访问帮助台应用程序，并允许用户在我们的系统上创建帐户。

让我们来看看用于身份验证的 Firebase 提供程序列表，并执行以下步骤来为我们的应用程序启用 Firebase 身份验证：

1.  打开[`firebase.google.com`](http://firebase.google.com)并使用您的凭据登录

1.  点击左侧的 DEVELOP 选项卡内的身份验证选项：

![](img/36b9e9a4-e023-43e0-97f8-e40802f235a0.png)

在上述截图中，如果您能看到，我们在身份验证部分有四个可用的选项卡，并且我们已经启用了提供商的身份验证，其中包括自定义的电子邮件/密码选项，我们可以添加到用户选项卡和 Google 身份验证。

+   用户：在这里，我们可以管理并添加多个用户的电子邮件 ID 和密码，以便使用各种提供程序进行身份验证，而无需编写任何服务器端代码。

+   登录方式：在此部分，我们可以看到 Firebase 中可用的提供程序列表。我们还可以管理授权域，防止用户使用相同的电子邮件地址和登录配额。

+   模板：此功能允许我们自定义 Firebase 发送的电子邮件模板，当用户使用电子邮件和密码注册时。我们还可以自定义密码重置、电子邮件地址更改和短信验证的模板。

在本章中，我们将涵盖以下三种身份验证方式：

+   脸书

+   谷歌

+   电子邮件/密码

# 使用 Facebook 进行身份验证

要向我们的帮助台应用程序添加 Facebook 身份验证，如果您还没有 Facebook 帐户，您需要在 Facebook 上创建一个帐户。否则，我们需要登录到 Facebook 开发者论坛[`developers.facebook.com/apps`](https://developers.facebook.com/apps)。一旦我们登录，它会显示应用程序列表和一个“添加新应用程序”按钮，用于创建身份验证的新应用程序 ID。参考以下内容：

![](img/cd96aa89-2743-46cb-b29b-68d3b7c1474f.png)

点击“添加新应用程序”按钮；它会显示弹出窗口以添加应用程序的名称。然后，点击“创建应用程序 ID”，它会将您重定向到我们应用程序的仪表板：

![](img/6176e422-b29a-428d-be05-13d79bf80418.png)这是 Facebook 开发者应用程序仪表板的屏幕截图。图像的目的只是显示 Facebook 提供的 API 或产品列表，以与任何 Web 应用程序集成。

现在，我们需要选择 Facebook 登录进行设置：

![](img/621e6610-baf7-496c-8307-ec7377a94f9a.png)

如果您能看到上述的屏幕截图，我们需要为客户端 OAuth 设置。为此，我们首先需要启用嵌入式浏览器 OAuth 登录功能以控制 OAuth 登录的重定向，然后复制有效的 OAuth 重定向 URL，当我们在 Firebase 中启用 Facebook 提供程序时，我们可以获得它。

要在 Firebase 中启用 Facebook 身份验证，我们需要从 Facebook 应用程序仪表板复制**应用程序 ID**和**应用程序密钥**：

![](img/46baa329-f0cf-4c14-a87a-e0e7a78d8b24.png)

然后，将这些复制的值放入 firebase 输入字段中，复制重定向 URI，并将其粘贴到客户端 OAuth 设置中。还要启用 Facebook 身份验证，然后点击“保存”按钮，如下所示：

![](img/9d6bcc9f-bde3-4c45-9f71-cc4840a94f9b.png)

这是我们在 Facebook 开发者论坛和 Firebase 中进行 Facebook 身份验证的最后一件事情。

点击保存，并注意提供程序的状态现在已启用：

![](img/a92cc442-34ba-4446-9ea7-6ffe92b50130.png)

现在，点击部分左侧的数据库，转到规则面板；它应该看起来像这样：

![](img/cf946010-0267-4b9d-9295-59470d4f2885.png)图像的目的是显示实时数据库部分和规则选项卡下的选项卡列表。在这里，我们可以添加数据库的安全规则来保护我们的数据，并借助模拟器来验证它是否按预期工作。

在我们的应用程序中，以前每个人都有权访问我们的应用程序和数据库以读取和写入数据。现在，我们将更改前面的规则配置，以便只有经过授权的用户才能访问应用程序并向我们的数据库写入数据。查看给定的代码并发布更改：

```jsx
{
 "rules": {
 ".read": "auth != null",
 ".write": "auth != null"
 }
}
```

# 使用 React 创建登录表单进行身份验证

就像我们为 Firebase 和 Facebook 的身份验证配置以及启用其他提供程序的功能一样，现在我们将在 react 中创建一个登录表单，以确保应用程序始终验证用户是否已登录；它将重定向用户到登录页面。因此，让我们创建一个登录页面，并配置 React 路由以根据路径 URL 重定向用户。

打开 firebase 文件夹中的`firebase-config.js`并导出以下不同提供程序的对象，以便我们可以在整个应用程序中访问这些对象：

```jsx
export  const  firebaseApp  =  firebase.initializeApp(config); export  const  googleProvider  =  new  firebase.auth.GoogleAuthProvider(); export  const  facebookProvider  =  new  firebase.auth.FacebookAuthProvider();
```

在上述代码中，`new firebase.auth.GoogleAuthProvider()`将为我们提供通过 Google API 对用户进行身份验证的方法。

同样，`new firebase.auth.FacebookAuthProvider()`将为我们提供通过 Facebook API 对用户进行身份验证的方法。

打开`app.js`并将以下代码添加到构造函数中以初始化应用程序的状态：

```jsx
constructor() { super();   this.state  = {  authenticated :  false,
  data:''
 } }
```

在这里，我们将 authenticated 的默认值设置为 false，因为这是应用程序的初始状态，用户尚未通过 Firebase 进行身份验证；数据的默认值在组件的初始状态下为空。当用户登录时，我们将更改这些状态。

首先，让我们在`login.js`中创建`Login`组件，并在`constructor()`中设置该组件的初始状态：

```jsx
 constructor() {
 super();
   this.state = {
     redirect: false
   }
 }
```

我们在初始状态下将重定向的默认值设置为`false`，但每当用户登录和退出时，它都会更改：

```jsx
if(this.state.redirect === true){
 return <Redirect to = "/" />
 }
 return (
 <div className="wrapper">
 <form className="form-signin" onSubmit={(event)=>{this.authWithEmailPassword(event)}} ref={(form)=>{this.loginForm = form}}> 
 <h2 className="form-signin-heading">Login</h2>
 <input type="email" className="form-control" name="username" placeholder="Email Address" ref={(input)=>{this.emailField = input}} required />
 <input type="password" className="form-control" name="password" placeholder="Password" ref={(input)=>{this.passwordField = input}} required /> 
 <label className="checkbox">
 <input type="checkbox" value="remember-me" id="rememberMe" name="rememberMe"/> Remember me
 </label>
 <button className="btn btn-lg btn-primary btn-block btn-normal" type="submit">Login</button> 
 <br/> 
<!-- Here we will add the buttons for google and facebook authentication
 </form>
 </div>
 );
```

在`render`方法中，我们将检查状态并将用户重定向到不同的路由`<Redirect>`。它将覆盖历史堆栈中的当前路由，就像服务器端重定向（HTTP 3xx）一样。

以下是我们可以与`Redirect`组件一起使用的属性列表：

+   `to:String`：我们还使用的重定向 URL。

+   `to:Object`：带有参数和其他配置（例如状态）的位置 URL。考虑以下示例：

```jsx
<Redirect to={{
 pathname: '/login',
 search: '?utm=your+selection',
 state: { referrer: currentLocation }
}}/>
```

+   `: bool`：当为 true 时，重定向将在历史记录中推送一个新条目，而不是替换当前条目。

+   `from: string`：要重定向的旧 URL。这只能用于匹配`<Switch>`内的位置。考虑这个例子：

```jsx
<Switch>
 <Redirect from='/old-url' to='/new-url'/>
 <Route path='/new-url' component={componentName}/>
</Switch>
```

所有上述的`<Redirect>`功能只在 React Router V4 中可用。

我们已经为我们的登录表单添加了 JSX，并绑定了方法和 ref 属性以访问表单值。我们还添加了 Facebook 和 Google 身份验证的按钮。只需看看以下代码：

```jsx
 <!-- facebook button that we have bind with authWithFacebook()-->
<button className="btn btn-lg btn-primary btn-facebook btn-block" type="button" onClick={()=>{this.authWithFacebook()}}>Login with Facebook</button> 
```

```jsx
<!-- Google button which we have bind with authWithGoogle()-->
 <button className="btn btn-lg btn-primary btn-google btn-block" type="button" onClick={()=>{this.authWithGoogle()}}>Login with Google</button>
```

在`app.js`中，我们已经配置了一个路由器，就像这样：

```jsx
<Router>
<div className="container"> {
this.state.authenticated
?
(
<React.Fragment>
<Header authenticated = {this.state.authenticated}/>
<Route path="/" render={() => (<Home userInfo = {this.state.data} />)} />
<Route path="/view-ticket" component={ViewTicketTable}/>
<Route path="/add-ticket" component={AddTicketForm}/>
</React.Fragment>
)
:
(
<React.Fragment>
<Header authenticated = {this.state.authenticated}/>
<Route exact path="/login" component={Login}/>
</React.Fragment>
)
}
</div>
</Router>
```

在上面的代码中，我们使用的是 React Router 版本 4，这是一个完全重写的用于 react 包的路由器。在以前的 React 路由器版本中，他们使用了非常困难的配置，这将很难理解，而且我们还需要创建一个单独的组件来管理布局。在路由器 V4 中，一切都作为一个组件工作。

在 React 路由器 V4 中，我们需要从 react-router-dom 中导入，而不是从 V3 中的 react-router。如果路由路径匹配，`<Router>`组件和所有其他子组件都会被渲染。

使用`<React.Fragment>`标签，我们可以包装任何 JSX 组件，而不会在 DOM 中添加另一个节点。

在 V4 react 路由器中，不再有`<IndexRoute>`；使用`<Route exact>`将会做同样的事情。

现在我们将更改包含导航的标题组件，并添加登录和注销链接：

```jsx
class Header extends Component {
render() {
 return (
 <div className="navbar navbar-inverse firebase-nav" role="navigation">
 {
 this.props.authenticated
 ?
 (
 <React.Fragment>
 <ul className="nav navbar-nav">
 <li className="active"><Link to="/">Home</Link></li>
 <li><Link to="/view-ticket">Tickets</Link></li>
 <li><Link to="/add-ticket">Add new ticket</Link></li>
 </ul>
 <ul className="nav navbar-nav navbar-right">
 <li><Link to="/logout">Logout</Link></li>
 </ul>
 </React.Fragment>
 ):(
 <React.Fragment>
 <ul className="nav navbar-nav navbar-right">
 <li><Link to="/login">Register/Login</Link></li>
 </ul>
 </React.Fragment>
 )
 }
 </div>
 );
 }
}
```

如果我们使用 React 路由器，这是必要的。让我们在导航中添加`<link>`而不是`<a>`标签，并用`to`替换`href`属性。在 V4 中，我们还可以使用`<NavLink>`；它的工作方式与`<Link>`相同，但可以添加额外的样式。看看这段代码：

```jsx
<li><NavLink to="/view-ticket/" activeClassName="active" activeStyle={{fontWeight: 'bold', color: red'}} exact strict>Tickets</NavLink></li>
```

根据身份验证，我们将更新导航以显示登录和注销链接。

通过在命令提示符中运行以下命令再次启动服务器：

```jsx
npm start
```

一旦服务器启动，打开浏览器快速查看：

![](img/f45b9ea6-6056-4209-9ebf-d5e7fed704ba.png)

如果你只看一下上面的屏幕摘录并注意地址栏，我尝试打开另一个 URL 来查看票据，但除了标题登录链接外，什么都没有显示；所以现在，如果我们点击登录，它将呈现登录表单。请参考以下截图；它应该是这样的：

![](img/b8432ea6-e507-4764-bbca-61c82fcc3060.png)

令人惊讶的是，我们的登录表单看起来很棒，正如预期的那样。

有关 react 路由器的更多信息，您可以查看[`reacttraining.com/react-router/web/api`](https://reacttraining.com/react-router/web/api)。

# 使用 Facebook 进行身份验证

每个按钮的`onClick`将指向三个函数，这些函数将对用户进行身份验证。Facebook 身份验证方法将处理我们与 Firebase 的身份验证，如下所示：

```jsx
 authWithFacebook(){
 console.log("facebook");
 firebaseApp.auth().signInWithPopup(facebookProvider).then((result,error)=>{
 if(error){
   console.log("unable to sign in with facebook");
 }
 else{
   this.setState({redirect:true})
 }}).catch((error)=>{
        ToastDanger(error.message);
    })
 }
```

在这里，我们从 firebase `auth`模块调用`signInWithPopup()`方法，并传递 facebook 提供程序。

为了在 UI 上显示错误消息，我们使用 React Toaster 模块，并将这些消息传递给它（在使用之前不要忘记安装和导入 React Toaster 模块）。我们还需要将`authWithFacebook()`方法绑定到构造函数中。`npm install --save react-toastr-basic`

`//在 app.js 中导入容器`

从' react-toastr-basic '导入 ToastrContainer;

`//在 render 方法内部`

`<ToastrContainer />`

```jsx
constructor() {
 super();
 this.authWithFacebook = this.authWithFacebook.bind(this);
 this.state = {
  redirect: false,
  data:null
 }}
```

现在，当我们点击“使用 Facebook 登录”按钮时，它将打开一个弹出窗口，让我们选择使用 Facebook 帐户登录，如下所示：

！[](Images/f88be669-622c-4468-838c-7dd194946679.png)

`signInWithPopup()`具有一个 promise API，允许我们在其上调用`.then()`并传递回调。此回调将提供一个包含用户的所有信息的名为`user`的对象，其中包括他们刚刚成功登录的姓名、电子邮件和用户照片 URL。我们将使用`setState()`将此对象存储在状态中，并在 UI 上显示用户的姓名、电子邮件和照片：

！[](Images/c4e5d1f7-b041-4aa4-886e-6efcd4205989.png)

# 使用 Google 进行身份验证

同样，我们可以在我们的应用程序中配置 Google 身份验证；只需将以下代码添加到`authWithGoogle()`方法中，它将打开用于使用 Google 登录的弹出窗口：

```jsx
 authWithGoogle(){
 console.log("Google");      
 googleProvider.addScope('profile');
 googleProvider.addScope('email');
 firebaseApp.auth().signInWithPopup(googleProvider).then((result,error)=>{
   if(error){
     console.log("unable to sign in with google");
    }
   else{
     this.setState({redirect:true,data:result.user})
   }}).catch((error)=>{
        ToastDanger(error.message);
     })
}
```

如您所见，我已添加了我们想要从身份验证提供程序请求的额外 OAuth 2.0 范围。要添加范围，请调用添加范围。我们还可以使用`firebase.auth().languageCode = 'pt'`来定义语言代码。如果我们想要在请求中发送特定的自定义参数，可以调用`setCustomParamter()`方法。考虑这个例子：

```jsx
provider.setCustomParameters({
 'login_hint': 'admin'
});
```

因此，一旦您点击“使用 Google 登录”按钮，它将触发弹出窗口以与 Google 进行身份验证：

！[](Images/6f3179e5-4f9f-4951-b3af-5182f374eeee.png)

因此，如果您已经登录并尝试使用不同提供者的相同电子邮件 ID 登录，它会抛出错误，如下所示：

![](img/a2cae460-9223-4599-86d8-64cb7b80c31e.png)

好的，现在让我们看看如何处理这些类型的错误。

# 处理帐户存在的错误

考虑到我们已经在 firebase 设置中启用了“每个电子邮件地址一个帐户”的选项。如前面的截图所示，当我们尝试使用提供者（Google）登录已经存在于 firebase 中的具有不同提供者（如 Facebook）的电子邮件时，它会抛出上述错误——`auth/account-exists-with-different-credential`——我们可以在前面的截图中看到。为了处理这个错误并完成对所选提供者的登录，用户必须首先登录到现有的提供者（Facebook），然后链接到前面的 AuthCredential（带有 Google ID 令牌）。重写`authWithFacebook()`方法后，我们的代码如下：

```jsx
if (error.code === 'auth/account-exists-with-different-credential') {
 // Step 2.
 var pendingCred = error.credential;
 // The provider account's email address.
 var email = error.email;
 // Get registered providers for this email.
 firebaseApp.auth().fetchProvidersForEmail(email).then(function(providers) {
 // Step 3.
 // If the user has several providers,
 // the first provider in the list will be the "recommended" provider to use.
 if (providers[0] === 'password') {
 // Asks the user his password.
 // In real scenario, you should handle this asynchronously.
 var password = promptUserForPassword(); // TODO: implement promptUserForPassword to open the dialog to get the user entered password.
 firebaseApp.auth().signInWithEmailAndPassword(email, password).then(function(user) {
 // Step 4.
 return user.link(pendingCred);
 }).then(function() {
 // Google account successfully linked to the existing Firebase user.
 });
 }
 })}
```

要了解更多错误代码列表，请访问[`firebase.google.com/docs/reference/js/firebase.auth.Auth#signInWithPopup`](https://firebase.google.com/docs/reference/js/firebase.auth.Auth#signInWithPopup)。

# 管理刷新时的登录

目前，每次刷新页面时，我们的应用都会忘记用户已经登录。但是，Firebase 有一个事件监听器——`onAuthStateChange()`——可以在应用加载时检查身份验证状态是否已更改，以及用户上次访问应用时是否已经登录。如果是，那么您可以自动将其重新登录。

我们将把这个方法写在`app.js`的`componentDidMount()`中。只需查看以下代码：

```jsx
 componentWillMount() {
   this.removeAuthListener = firebase.auth().onAuthStateChanged((user) 
   =>{
    if(user){
     console.log("App user data",user);
     this.setState({
       authenticated:true,
       data:user.providerData
     })
  }
 else{
   this.setState({
     authenticated:false,
     data:''
 })}
 })}
```

此外，在`componentWillUnmount()`中，我们将删除该监听器以避免内存泄漏：

```jsx
 componentWillUnmount(){
   this.removeAuthListener();
 }
```

现在，如果您刷新浏览器，它不会影响应用程序的状态；如果您已经登录，它将保持不变。

使用 Facebook API 或其他方式登录后，我们需要在 UI 中显示用户信息。为此，如果再次查看路由器组件，我们将使用`userInfo`属性将此用户信息发送到`Home`组件中：

```jsx
<Route path="/" render={() => (<Home userInfo = {this.state.data} />)} />
```

在`Home`组件的渲染方法中，我们将迭代包含成功登录到系统的用户数据的`userInfo`属性：

```jsx
render() {
 var userPhoto = {width:"80px",height:"80px",margintop:"10px"}; 
 return (
 <div>
 {
 this.props.userInfo.map((profile)=> {
 return (
 <React.Fragment key={profile.uid}>
 <h2>{ profile.displayName } - Welcome to Helpdesk Application</h2>
 <div style={userPhoto}>
 <img src = { profile.photoURL } alt="user"/>
 <br/>
 <span><b>Eamil:</b></span> {profile.email }
 </div>
 </React.Fragment>
 )})
 }
 </div>
 )}
```

在`Logout()`方法中，我们将简单地调用 firebase auth 中的`signOut()`方法；通过使用 Promise API，我们从应用程序状态中删除用户数据。现在`this.state.data`等于 null，用户将看到登录链接而不是注销按钮。它应该是这样的：

```jsx
constructor() {
 super();
  this.state = {
    redirect: false,
    data:''
  }
 }
 componentWillMount(){
   firebaseApp.auth().signOut().then((user)=>{
     this.setState({
      redirect:true,
      data: null
   })
 })}
 render() {
 if(this.state.redirect === true){
 return <Redirect to = "/" />
 }
 return (
 <div style={{textAlign:"center",position:"absolute",top:"25%",left:"50%"}}>
 <h4>Logging out...</h4>
 </div>);
 }
```

# 使用电子邮件和密码进行身份验证

在 Firebase 中，我们还可以将您自己的身份验证系统与 Firebase 身份验证集成，以便用户可以访问数据，而无需强制他们使用现有系统的第三方 API 来创建帐户。Firebase 还允许匿名身份验证会话，通常用于在等待客户端使用永久的`auth`方法进行身份验证时保存少量数据。我们可以配置这个匿名会话，直到用户使用永久的`login`方法登录或清除他们的浏览器缓存的最后几天、几周、几个月，甚至几年。例如，一个购物车应用程序可以为每个将商品添加到购物车的用户创建一个匿名身份验证会话。购物车应用程序将提示用户创建一个帐户以进行结账；在那时，购物车将被持久化到新用户的帐户，并且匿名会话将被销毁。

# 支持的身份验证状态持久性类型

我们可以根据应用程序或用户的要求，在指定的 Firebase 身份验证`instance(.auth())`上使用三种持久性中的一种：

| **Auth 实例** | **值** | **描述** |
| --- | --- | --- |
| `firebase.auth.Auth.Persistence.LOCAL` | 'local' | 它表示即使关闭浏览器窗口或在 React Native 中销毁活动，状态也将被持久化。为此，需要显式注销以清除该状态。 |
| `firebase.auth.Auth.Persistence.SESSION` | 'session' | 在这种情况下，状态将仅持续到当前会话或选项卡，并且在用户进行身份验证的选项卡或窗口关闭时将被清除。 |
| `firebase.auth.Auth.Persistence.NONE` | 'none' | 当我们指定这个时，意味着状态只会存储在内存中，并且在窗口或应用程序刷新时将被清除。 |

考虑这个例子：

```jsx
firebaseApp.auth().setPersistence('session')
 .then(function() {
 // Auth state is now persisted in the current
 // session only. If user directly close the browser window without doing signout then it clear the existing state
 // ...
 // New sign-in will be persisted with session.
 return firebase.auth().signInWithEmailAndPassword(email, password);
 })
 .catch(function(error) {
 // Handle Errors here.
 });
```

让我们创建一个名为`authWithEmailPassword()`的函数，并将以下代码添加到其中：

```jsx
const email = this.emailField.value
const password = this.passwordField.value;
firebaseApp.auth().fetchProvidersForEmail(email).then((provider)=>{
 if(provider.length === 0){
 //Creating a new user
 return firebaseApp.auth().createUserWithEmailAndPassword(email,password);
 } else if(provider.indexOf("password") === -1){
 this.loginForm.reset();
 ToastDanger('Wrong Password. Please try again!!')
 } else {
 //signin user
 return firebaseApp.auth().signInWithEmailAndPassword(email,password);
 }}).then((user) => {
 if(user && user.email){
 this.loginForm.reset();
 this.setState({redirect: true});
 }})
 .catch((error)=>{
 console.log(error);
 ToastDanger(error.message);
 })
```

在上述代码中，首先，我们从表单中获取值。当用户点击提交按钮时，借助`fetchProvidersForEmail(email)`，我们验证电子邮件是否存在于我们当前的 firebase 系统中；如果不存在，它将使用`createUserWithEmailAndPassword()`方法创建一个新用户。如果返回 true，我们将验证密码；如果用户输入了错误的密码，它将提示用户输入错误的密码，否则使用相同的方法—`signInWithEmailAndPassword()`—登录他们，并通过重定向 true 来更新组件的状态。

当我们在`createUserWithEmailAndPassword()`方法中创建新用户时，它会返回以下错误代码：

+   auth/email-already-in-use

+   auth/invalid-email

+   auth/operation-not-allowed（如果在 Firebase 控制台中未启用电子邮件/密码帐户。）

+   auth/weak-password（如果密码不够强大。）

当我们使用`fetchProvidersForEmail(email)`基于电子邮件获取提供程序时，它会返回以下错误代码：

+   auth/invalid-email（如果用户输入了无效的电子邮件）

阅读更多身份验证方法和错误代码的列表，请参考[`firebase.google.com/docs/reference/js/firebase.auth.Auth`](https://firebase.google.com/docs/reference/js/firebase.auth.Auth)。

我们还可以在我们的应用程序中使用以下 firebase 方法来操作用户：

```jsx
var currentUser = firebase.auth().currentUser;
currentUser.updateProfile({
 displayName: “Harmeet Singh”,
 photoURL: “http://www.liferayui.com/g/200/300"
});
currentUser.sendPasswordResetEmail(“harmeetsingh090@gmail.com”); // Sends a temporary password
// Re-authentication is necessary for email, password and delete functions
var credential = firebase.auth.EmailAuthProvider.credential(email, password);
currentUser.reauthenticate(credential);
currentUser.updateEmail(“harmeetsingh090@gmail.com”);
currentUser.updatePassword(“D@#Log123”);
currentUser.delete();
```

成功登录后，我们将被重定向到应用程序仪表板页面，并且我们将能够看到完整的导航，可以添加和查看票务：

![](img/5b2ac244-7041-48ac-9302-9098beb44cf0.png)

现在，如果您点击注销按钮，将不会发生任何事情，因为我们还没有创建任何`logout`组件。因此，在注销按钮中，我们需要做的就是简单地调用 firebase 的`signOut()`方法：

```jsx
class Logout extends Component {
 constructor(props) {
 super();
  this.state = {
    redirect: props.authenticated,
    data:''
 }}
 componentWillMount(){
  firebaseApp.auth().signOut().then((user)=>{
    this.setState({
       redirect:true,
       data: null
   })
 })}
 render() {
 if(this.state.redirect === true){
    return <Redirect to = "/" />
 }
 return (
 <div style={{textAlign:"center",position:"absolute",top:"25%",left:"50%"}}>
   <h4>Logging out...</h4>
 </div>
 );
 }}
```

在上述代码中，我们创建了一个组件，并根据组件 props 中传递的值（authenticated）设置了状态；然后，在组件生命周期挂钩方法`componentWillMount()`中，我们调用了`firebaseApp.auth().signout()`方法，该方法登出用户并将其重定向到登录页面，并从状态中删除数据。

# 摘要

在本章中，我们看到了如何借助 Firebase 的身份验证系统使我们的应用程序免受未知用户的侵害。我们还了解了如何在 node 环境中配置 React-Firebase 应用程序，以及如何在 React 中创建登录表单并集成 Firebase 身份验证的登录方法，如 Google、Facebook 和电子邮件/密码。同样，我们也可以在应用程序中集成其他身份验证登录方法。

我们还介绍了根据 Firebase 身份验证错误代码处理身份验证错误的方法，这有助于我们在应用程序中执行操作。为了“持久化”身份验证状态，我们可以使用`firebaseApp.auth().setPersistence('session')`这个方法，它允许我们维护 Firebase 身份验证状态。

在下一章中，我们将探索 Redux 的强大功能，并使用 React、Redux 和 Firebase 创建一个实时的订票应用程序。
