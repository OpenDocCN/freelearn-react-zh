# 第九章：最佳实践

在深入探讨在处理 React 和 Firebase 时应遵循的最佳实践之前，让我们回顾一下之前章节中我们所看到的内容。

在之前的章节中，我们看到了 Firebase 账户设置，Firebase 与 ReactJs 的集成，使用 Firebase 身份验证提供程序进行登录认证，React 组件中的身份验证状态管理，基于角色和配置文件的数据安全，Firebase 与 React-Redux 的集成，Firebase 云消息传递，Firebase 云函数，以及在 React 组件中使用 Firebase Admin SDK API，希望你也享受了这段旅程。现在我们知道从哪里开始以及如何编写代码，但最重要的是如何遵循最佳实践编写标准的代码。

因此，当我们使用 React 和 Firebase 创建应用程序时，我们需要确保 Firebase 数据库中数据的结构以及将数据传递到 React 组件中是应用程序中最重要的部分。

在开发领域中，每个开发人员对于遵循最佳实践都有自己的看法，但我将与您分享我迄今为止观察和经验到的内容；你可能有不同的看法。

以下是本章将涵盖的主题列表：

+   Firebase 的最佳实践

+   React 和 Redux 的最佳实践

# Firebase 的最佳实践

在 Firebase 中，我们都知道数据以 JSON 树格式存储，并且实时同步到每个连接的设备。因此，在使用 Firebase 构建跨平台应用程序（Web、iOS 和 Android）时，我们可以共享一个实例给所有应用程序，以接收来自实时数据库的最新更新和新数据。因此，当我们将数据添加到 JSON 树中时，它将成为现有 JSON 结构中的一个节点，并带有关联的键，因此我们始终需要计划如何保存数据以构建一个结构良好的数据库。

# 写入数据

在 Firebase 中，我们有四种方法可以将数据写入 Firebase 数据库：

| `set( )` | 写入或替换数据到指定路径，比如 `messages/tickets/<uid>`。 |
| --- | --- |
| `update( )` | 更新节点的特定子节点而不替换其他子节点。我们还可以使用 update 方法将数据更新到多个位置。 |
| `push( )` | 要在数据库中添加一系列数据，我们可以使用`push()`方法；每次调用时它都会生成一个唯一的 ID，比如 `helpdesk/tickets/<unique-user-id>/<unique-ticket-id>`。 |
| `transaction( )` | 当我们处理可能会被并发更新破坏的复杂数据时，我们可以使用这种方法，比如增量计数器。 |

现在，让我们看看我们的帮助台应用程序中的数据结构是如何构建的：

```jsx
{
 "tickets": {
 "-L4L1BLYiU-UQdE6lKA_": {
    "comments": "Need extra 4GB RAM in my system"
    "date": "Fri Feb 02 2018 15:51:10 GMT+0530 (India Standa..."
    "department": "IT"
    "email": "harmeet_15_1991@yahoo.com"
    "issueType": "Hardware Request"
    "status": "progress"
 },
 "-L4K01hUSDzPXTIXY9oU": {
 "comments": "Need extra 4GB RAM in my system"
 "date": "Fri Feb 02 2018 15:51:10 GMT+0530 (India Standa..."
 "department": "IT"
 "email": "harmeet_15_1991@yahoo.com"
 "issueType": "Hardware Request"
 "status": "progress"
     }
  }
}
```

现在，让我们以前面的数据结构为例，使用`set()`方法存储具有自增整数的数据：

```jsx
{
 "tickets": {
 "0": {
    "comments": "Need extra 4GB RAM in my system"
    "date": "Fri Feb 02 2018 15:51:10 GMT+0530 (India Standa..."
    "department": "IT"
    "email": "harmeet_15_1991@yahoo.com"
    "issueType": "Hardware Request"
    "status": "progress"
 },
 "1": {
 "comments": "Need extra 4GB RAM in my system"
 "date": "Fri Feb 02 2018 15:51:10 GMT+0530 (India Standa..."
 "department": "IT"
 "email": "harmeet_15_1991@yahoo.com"
 "issueType": "Hardware Request"
 "status": "progress"
     }
  }
}
```

现在，如果你看到前面的数据结构，新的票将被存储为`/tickets/1`。如果只有一个用户添加票，这将起作用，但在我们的应用程序中，许多用户可以同时添加票。如果两个员工同时写入`/tickets/2`，那么其中一个票将被另一个删除。因此，这不是推荐的做法，我们始终建议在处理数据列表时使用`push()`方法生成唯一 ID（参考前面的数据结构）。

# 避免嵌套数据

在 Firebase 实时数据库中，当我们从 JSON 树中获取数据时，我们还将获取该特定节点的所有子节点，因为当我们将数据添加到 JSON 树中时，它就成为现有 JSON 结构中的一个节点，并带有关联的键。 Firebase 实时数据库允许嵌套数据深达 32 级，因此当我们授予某人在特定节点上的读取或写入权限时，我们也在给予该节点下所有子节点的访问权限。因此，始终最佳实践是尽可能保持我们的数据结构扁平化。

让我向你展示为什么嵌套数据是不好的；请参考以下示例：

```jsx
{
 // a poorly nested data architecture, because
 // iterating over "products" to get a list of names requires
 // potentially downloading hundreds of products of mobile
 "products": {
 "electronics": {
 "name": "mobile",
 "types": {
 "samsung": { "name": "Samsung S7 Edge (Black Pearl, 128 GB)", "description": "foo" },
 "apple": { ... },
 // a very long list of mobile products
 }
 }
 }
 }
```

使用这种嵌套数据结构，对数据进行迭代非常困难。即使是像列出产品名称这样的简单操作，也需要将整个产品树，包括所有产品列表和类型，下载到客户端。

# Flattern 数据结构

在 Flattern 结构中，数据被分成不同的路径；只需在需要时轻松下载所需的节点：

```jsx
{
      // products contains only meta info about each product
      // stored under the product's unique ID
      "products": {
        "electronics": {
          "name": "mobile"
        },
        "home_furniture": { ... },
        "sports": { ... }
      },
      // product types are easily accessible (or restricted)
      // we also store these by product Id
      "types": {
          "mobile":{
              "name":"samsung"
           },
      "laptop": {...},
      "computers":{...},
      "television":{...}
      "home_furniture": { ... },
      "sports": { ... }
      },
      // details are separate from data we may want to iterate quickly
      // but still easily paginated and queried, and organized by 
         product ID
      "detail": {
        "electronics": {
          "samsung": { "name": "Samsung S7 Edge (Black Pearl, 128 GB)", 
          "description": "foo" },
          "apple": { ... },
          "mi": { ... }
        },
        "home_furniture": { ... },
        "sports": { ... }
      }
    }
```

在前面的例子中，我们有一些轻度嵌套的数据（例如，每个产品的详细信息本身就是带有子元素的对象），但我们还按照它们将来的迭代和读取方式逻辑地组织了我们的数据。我们存储了重复的数据来定义对象之间的关系；这对于维护双向、多对多或一对多的冗余关系是必要的。这使我们能够快速有效地获取手机，即使产品或产品类型的列表扩展到数百万，或者 Firebase 规则和安全性将阻止访问某些记录。

现在可以通过每个产品只下载少量字节来迭代产品列表，快速获取用于在 UI 中显示产品的元数据。

在看到前面的扁平结构后，如果您认为在 Firebase 中逐个查找每条记录是可以的，那么是的，因为 Firebase 内部使用网络套接字和客户端库进行传入和传出请求的优化。即使我们有数万条记录，这种方法仍然可以，并且非常合理。

始终创建可以在应用用户增长时扩展的数据结构。

# 避免使用数组

Firebase 文档已经提到并澄清了这个话题，避免在 Firebase 数据库中使用数组，但我想强调一些使用数组存储数据的用例。

请参考以下几点；如果以下所有条件都成立，我们可以使用数组在 Firebase 中存储数据：

+   如果一个客户端一次只能写入数据

+   在删除键时，我们可以保存数组并进行切割，而不是使用`.remove()`

+   当通过数组索引引用任何内容时，我们需要小心（可变键）

# 日期对象

当我们谈论在 Firebase 中对数据进行排序和过滤时，确保您在每个创建的对象中都添加了`created_date`键，以及日期时间戳，例如`ref.set(new Date().toString())`和`ref.set(new Date().getTime())`，因为 Firebase 不支持 JavaScript 日期对象类型（`ref.set(new Date());`）。

# 自定义声明

Firebase Admin SDK 提供了在配置文件对象中添加自定义属性的功能；借助这一功能，我们可以为用户提供不同的访问控制，包括在 react-firebase 应用中基于角色的控制，因此它们并不是用来存储额外的数据（如配置文件和其他自定义数据）。我们知道这看起来是一个非常方便的方法，但强烈不建议这样做，因为这些声明存储在 ID 令牌中，这会影响性能问题，因为所有经过身份验证的请求都包含与已登录用户对应的 Firebase ID 令牌。

+   自定义声明仅用于存储控制用户访问的数据

+   自定义声明的大小受限，因此传递大于 1000 字节的自定义声明将引发错误

# 管理用户会话

管理用户会话并提示重新验证，因为每次用户登录时，用户凭据都会发送到 Firebase 身份验证后端并交换为 Firebase ID 令牌（JWT）和刷新令牌。

以下是我们需要管理用户会话的常见情况：

+   用户被删除

+   用户被禁用

+   电子邮件地址和密码更改

Firebase Admin SDK 还提供了使用`revokeRefreshToken()`方法撤销特定用户会话的能力。它会撤销给定用户的活动刷新令牌。如果重置密码，Firebase 身份验证后端会自动撤销用户令牌。

当任何数据需要身份验证才能访问时，必须配置以下规则：

```jsx
{
 "rules": {
 "users": {
 "$user_id": {
 ".read": "$user_id === auth.uid && auth.token.auth_time > (root.child('metadata').child(auth.uid).child('revokeTime').val() || 0)",
 ".write": "$user_id === auth.uid && auth.token.auth_time > (root.child('metadata').child(auth.uid).child('revokeTime').val() || 0)"
 }
 }
 }
}
```

# 在 JavaScript 中启用离线功能

当我们使用 Firebase 创建实时应用程序时，还需要监视客户端与数据库的连接和断开连接。Firebase 提供了一个简单的解决方案，可以在客户端从 Firebase 数据库服务器断开连接时写入数据库。我们可以在断开连接时执行所有操作，如写入、设置、更新和删除。

参考 Firebase 的`onDisconnect()`方法示例：

```jsx
var presenceRef = firebase.database().ref("disconnectmessage");
// Write the string when client loses connection
presenceRef.onDisconnect().set("I disconnected!");
```

我们还可以附加回调函数以确保`onDisconnect()`方法被正确附加：

```jsx
presenceRef.onDisconnect().remove(function(err) {
 if (err) {
 console.error('onDisconnect event not attached properly', err);
 }
});
```

要取消`onDisconnect()`方法，我们可以调用`.cancel()`方法`onDisconnectRef.cancel();`。

为了检测连接状态，Firebase 实时数据库提供了特殊位置`/.info/connected`。

每次应用连接状态更改时都会更新；它返回布尔值以检查客户端连接状态是否已连接：

```jsx
var connectedRef = firebase.database().ref(".info/connected");
connectedRef.on("value", function(snap) {
 if (snap.val() === true) {
 alert("connected");
 } else {
 alert("not connected");
 }
});
```

# 优化数据库性能

还有一些需要关注的事项，比如在您的应用中优化 Firebase 实时数据库性能，以了解如何使用不同的实时数据库监控工具来优化您的实时数据库性能。

# 监控实时数据库

我们可以通过一些不同的工具收集我们的实时数据库性能数据：

+   **高级概述：** 我们可以使用 Firebase 分析工具列出未索引的查询和实时读/写操作的概述。要使用分析工具，请确保已安装 Firebase CLI 并运行以下命令。

+   **计费使用估计：** Firebase 使用指标在 Firebase 控制台中提供您的计费使用和高级性能指标。

+   **详细钻取：** Stackdriver 监控工具为您提供了数据库随时间性能的更细粒度查看。

有关分析的更多详细信息，请访问[`firebase.google.com/docs/database/usage/profile`](https://firebase.google.com/docs/database/usage/profile)

# 通过指标改善性能

收集数据后，根据您想要改进的性能领域，探索以下最佳实践和策略：

| **指标** | **描述** | **最佳实践** |
| --- | --- | --- |

| 负载/利用率 | 优化数据库处理请求时的容量利用率（反映在**负载**或**io/database_load**指标中）。 | 优化数据结构（[`firebase.google.com/docs/database/usage/optimize#data-structure`](https://firebase.google.com/docs/database/usage/optimize#data-structure)）跨数据库共享数据（[`firebase.google.com/docs/database/usage/optimize#shard-data`](https://firebase.google.com/docs/database/usage/optimize#shard-data)）

提高监听器效率（[`firebase.google.com/docs/database/usage/optimize#efficient-listeners`](https://firebase.google.com/docs/database/usage/optimize#efficient-listeners)）

使用基于查询的规则限制下载*(*[`firebase.google.com/docs/database/usage/optimize#query-rules`](https://firebase.google.com/docs/database/usage/optimize#query-rules))

优化连接（[`firebase.google.com/docs/database/usage/optimize#open-connections`](https://firebase.google.com/docs/database/usage/optimize#open-connections)）|

| 活动连接 | 平衡数据库的同时和活动连接数，以保持在 100,000 个连接限制以下。 | 在数据库之间分片数据 ([`firebase.google.com/docs/database/usage/optimize#shard-data`](https://firebase.google.com/docs/database/usage/optimize#shard-data)) 减少新连接 ([`firebase.google.com/docs/database/usage/optimize#open-connections`](https://firebase.google.com/docs/database/usage/optimize#open-connections)) |
| --- | --- | --- |

| 出站带宽 | 如果从数据库下载的数据量比您想要的要高，您可以提高读操作的效率并减少加密开销。 | 优化连接 ([`firebase.google.com/docs/database/usage/optimize#open-connections`](https://firebase.google.com/docs/database/usage/optimize#open-connections)) 优化数据结构 ([`firebase.google.com/docs/database/usage/optimize#data-structure`](https://firebase.google.com/docs/database/usage/optimize#data-structure)) |

使用基于查询的规则限制下载 ([`firebase.google.com/docs/database/usage/optimize#query-rules`](https://firebase.google.com/docs/database/usage/optimize#query-rules))

重用 SSL 会话 ([`firebase.google.com/docs/database/usage/optimize#ssl-sessions`](https://firebase.google.com/docs/database/usage/optimize#ssl-sessions))

提高监听器效率 ([`firebase.google.com/docs/database/usage/optimize#efficient-listeners`](https://firebase.google.com/docs/database/usage/optimize#efficient-listeners))

限制对数据的访问 ([`firebase.google.com/docs/database/usage/optimize#secure-data`](https://firebase.google.com/docs/database/usage/optimize#secure-data)) |

| 存储 | 确保您不存储未使用的数据，或者在其他数据库和/或 Firebase 产品之间平衡存储的数据，以保持在配额范围内。 | 清理未使用的数据 ([`firebase.google.com/docs/database/usage/optimize#cleanup-storage`](https://firebase.google.com/docs/database/usage/optimize#cleanup-storage)) 优化数据结构 ([`firebase.google.com/docs/database/usage/optimize#data-structure`](https://firebase.google.com/docs/database/usage/optimize#data-structure))

在数据库之间分片数据 ([`firebase.google.com/docs/database/usage/optimize#shard-data`](https://firebase.google.com/docs/database/usage/optimize#shard-data))

使用 Firebase 存储([`firebase.google.com/docs/storage`](https://firebase.google.com/docs/storage)) |

来源：[`firebase.google.com/docs/database/usage/optimize`](https://firebase.google.com/docs/database/usage/optimize)

如果我们使用的是 Blaze 定价计划，我们可以创建多个实时数据库实例；然后，我们可以在同一个 Firebase 项目中创建多个数据库实例。

要从 Firebase CLI 编辑和部署规则，请按照以下步骤进行：

```jsx
firebase target:apply database main my-db-1 my-db-2
firebase target:apply database other my-other-db-3
Update firebase.json with the deploy targets:
{
"database": [
{"target": "main", "rules", "foo.rules.json"},
{"target": "other", "rules": "bar.rules.json"}
]
}
```

```jsx
firebase deploy 
```

确保您始终从同一位置编辑和部署规则。

# 将您的应用连接到多个数据库实例

使用数据库引用来访问存储在辅助数据库实例中的数据。您可以通过 URL 或应用程序获取特定数据库实例的引用。如果我们在`.database()`方法中不指定 URL，那么我们将获得应用程序的默认数据库实例的引用：

```jsx
// Get the default database instance for an app
var database = firebase.database();
// Get a secondary database instance by URL
var database = firebase.database('https://reactfirebaseapp-9897.firebaseio.com');
```

要查看 Firebase 示例项目列表，请访问[`firebase.google.com/docs/samples/`](https://firebase.google.com/docs/samples/)。

查看 Firebase 库列表，请参考[`firebase.google.com/docs/libraries/`](https://firebase.google.com/docs/libraries/)。

您也可以订阅[`www.youtube.com/channel/UCP4bf6IHJJQehibu6ai__cg`](https://www.youtube.com/channel/UCP4bf6IHJJQehibu6ai__cg)频道以获取更新。

# React 和 Redux 的最佳实践

每当我们有具有动态功能的组件时，数据就会出现；同样，在 React 中，我们必须处理动态数据，这似乎很容易，但并非每次都是这样。

听起来很混乱！

这很容易，但有时很困难，因为在 React 组件中，很容易通过多种方式传递属性来构建渲染树，但更新视图的清晰度不高。

在前面的章节中，这个声明已经清楚地显示出来了，所以如果你还不清楚，请参考那些。

# Redux 的使用

我们知道，在单页面应用程序（SPA）中，当我们必须处理状态和时间时，难以掌握状态随时间的变化。在这里，Redux 非常有帮助，为什么？这是因为在 JavaScript 应用程序中，Redux 处理两种状态：一种是数据状态，另一种是 UI 状态，它是单页面应用程序（SPA）的标准选项。此外，请记住，Redux 可以与 Angular 或 Jquery 或 React JavaScript 库或框架一起使用。

# Redux 和 Flux 之间的区别

Redux 是一个工具，而 Flux 只是一个模式，你不能像即插即用或下载一样使用它。我并不否认 Redux 受到 Flux 模式的一些影响，但正如我们无法说它百分之百看起来像 Flux 一样。

让我们继续参考一些区别。

Redux 遵循三个指导原则，如所示，这也将涵盖 Redux 和 Flux 之间的区别：

1.  **单一存储器方法：** 我们在之前的图表中看到，存储器在应用程序和 Redux 中充当所有状态修改的“中间人”。它通过存储器控制两个组件之间的直接通信，是通信的单一点。在这里，Redux 和 Flux 之间的区别在于 Flux 有多个存储器方法，而 Redux 有单一存储器方法。

1.  **只读状态：** 在 React 应用程序中，组件不能直接改变状态，而是必须通过“动作”将改变分派给存储器。在这里，存储器是一个对象，它有四种方法，如下所示：

+   store.dispatch(action)

+   store.subscribe(listener)

+   store.getState()

+   replaceReducer(nextReducer)

1.  **改变状态的 Reducer 函数：** Reducer 函数将处理分派动作以改变状态，因为 Redux 工具不允许两个组件直接通信；因此它不仅会改变状态，还会描述状态改变的分派动作。这里的 Reducer 可以被视为纯函数。以下是编写 reducer 函数的一些特点：

+   没有外部数据库或网络调用

+   基于参数返回值

+   参数是“不可变的”

+   相同的参数返回相同的值

Reducer 函数被称为纯函数，因为它们纯粹地根据其设置的参数返回值；它没有任何其他后果。建议它们具有扁平状态。在 Flux 或 Redux 架构中，处理来自 API 返回的嵌套资源总是很困难，因此建议在组件中使用扁平状态，例如 normalize。

**专业提示：** `const data = normalize(response, arrayOf(schema.user))  state = _.merge(state, data.entities)`

# 不可变的 React 状态

在扁平状态中，我们可以处理嵌套资源，并且在不可变对象中，我们可以获得声明的状态不可修改的好处。

不可变对象的另一个好处是，通过它们的引用级别相等检查，我们可以获得出色的改进渲染性能。在不可变中，我们有一个`shouldComponentUpdate`的例子：

```jsx
shouldComponentUpdate(nexProps) { 
 // instead of object deep comparsion
 return this.props.immutableFoo !== nexProps.immutableFoo
}
```

在 JavaScript 中，使用不可变性深冻结节点将帮助您在变异之前冻结节点，然后它将验证结果。以下示例显示了相同的逻辑：

```jsx
return { 
  ...state,
  foo
}
return arr1.concat(arr2) 
```

我希望前面的例子已经清楚地说明了不可变 JS 的用途和好处。它也有一个不复杂的方式，但它的使用率非常低：

```jsx
import { fromJS } from 'immutable'
const state = fromJS({ bar: 'biz' }) 
const newState = foo.set('bar', 'baz') 
```

在我看来，这是一个非常快速和美丽的功能。

# React 路由

我们必须在客户端应用程序中使用路由，并且对于 ReactJS，我们还需要一个或另一个路由库，因此我建议您使用 react-router-dom 而不是 react-router。

**优势：**

+   在标准化结构中声明视图可以帮助我们立即了解我们的应用视图是什么

+   使用 react-router-dom，我们可以轻松处理嵌套视图及其渐进式视图分辨率

+   使用浏览历史功能，用户可以向后/向前导航并恢复视图状态

+   动态路由匹配

+   导航时视图上的 CSS 过渡

+   标准化的应用程序结构和行为，在团队合作时非常有用。

注意：React 路由器没有提供处理数据获取的任何方式。我们需要使用 async-props 或其他 React 数据获取机制。

很少有开发人员知道如何在 webpack 中将应用程序代码拆分成多个 JavaScript 文件：

```jsx
require.ensure([], () => { 
  const Profile = require('./Profile.js')
  this.setState({
    currentComponent: Profile
  })
})
```

代码的拆分是必要的，因为每个代码对每个用户都没有用，也没有必要在每个页面加载该代码块，这对浏览器来说是一种负担，因此为了避免这种情况，我们应该将应用程序拆分成几个块。

现在，你可能会有一个问题，比如如果我们有更多的代码块，我们将不得不有更多的 HTTP 请求，这也会影响性能，但借助 HTTP/2 多路复用，你的问题将得到解决。您还可以将分块代码与分块哈希结合使用，这样每当更改代码时，还可以优化浏览器缓存比例。

# JSX 组件

JSX 无非就是，简单来说，它只是 JavaScript 语法的扩展。此外，如果您观察 JSX 的语法或结构，您会发现它类似于 XML 编码。JSX 正在执行预处理步骤，将 XML 语法添加到 JavaScript 中。虽然您当然可以在没有 JSX 的情况下使用 React，但 JSX 使得 React 更加整洁和优雅。与 XML 类似，JSX 标记具有标记名称，属性和子级，并且在其中，如果属性值用引号括起来，该值将成为字符串。

JSX 的工作方式类似于 XML，具有平衡的开放和关闭标签，并且有助于使大型树更容易阅读，而不是“函数调用”或“对象文字”。

**在 React 中使用 JSX 的优势**：

+   JSX 比 JavaScript 函数更容易理解和思考

+   JSX 的标记更熟悉于设计师和您团队的其他成员

+   您的标记变得更语义化，结构化和更有意义

它有多容易可视化？

正如我所说，结构/语法在 JSX 格式中更容易可视化/注意到，这意味着与 JavaScript 相比更清晰和可读。

# 语义/结构化语法

在我们的应用程序中，我们可以看到 JSX 语法易于理解和可视化；在其背后，有一个具有语义化语法结构的重要原因。JSX 愉快地将您的 JavaScript 代码转换为更具语义和有意义的结构化标记。这使您能够声明组件结构和信息倾注使用类似 HTML 的语法，知道它将转换为简单的 JavaScript 函数。React 概述了您在 React.DOM 命名空间中期望的所有 HTML 元素。好处是它还允许您在标记中使用您自己编写的自定义组件。

# 在 React 组件中使用 PropType

在 React 组件中，我们可以从更高级别的组件传递属性，因此对属性的了解是必须的，因为这将使您能够更灵活地扩展组件并节省时间：

```jsx
MyComponent.propTypes = { 
  isLoading: PropTypes.bool.isRequired,
  items: ImmutablePropTypes.listOf(
    ImmutablePropTypes.contains({
      name: PropTypes.string.isRequired,
    })
  ).isRequired
}
```

您还可以验证您的属性，就像我们可以使用 react 不可变 proptypes 验证不可变 JS 的属性一样。

# 高阶组件的好处

高阶组件只是原始组件的扩展版本：

```jsx
PassData({ foo: 'bar' })(MyComponent) 
```

使用它的主要好处是我们可以在多种情况下使用它，例如身份验证或登录验证：

```jsx
requireAuth({ role: 'admin' })(MyComponent) 
```

另一个好处是，使用高阶组件，您可以单独获取数据并设置逻辑以简单地查看您的视图。

# Redux 架构的好处

与其他框架相比，它有更多的优点：

1.  它可能没有任何其他影响。

1.  正如我们所知，不需要绑定，因为组件不能直接交互。

1.  状态是全局管理的，因此管理不当的可能性较小。

1.  有时，对于中间件，管理其他方式的影响可能会很困难。

从上述观点来看，Redux 架构非常强大，而且具有可重用性。

我们还可以使用 ReactFire 库构建 React-Firebase 应用程序，只需几行 JavaScript。我们可以通过 ReactFireMixin 将 Firebase 数据集成到 React 应用程序中。

# 总结

在本书的最后一章中，我们介绍了在使用 React 和 Firebase 时应遵循的最佳实践。我们还看到了如何使用不同的工具来监视应用程序性能，以减少错误的数量。我们还谈到了 Firebase 实时数据库中数据结构的重要性，并讨论了动态数据传递给 React 组件。我们还研究了其他关键因素，如 JSX、React 路由和 React PropTypes，在 React 应用程序中是最常用的元素。我们还了解到 Redux 在维护**单页应用程序**（**SPAs**）的状态方面有很大帮助。
