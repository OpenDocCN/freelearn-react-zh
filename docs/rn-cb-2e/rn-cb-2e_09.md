# 第九章：实施 Redux

在本章中，我们将逐步介绍将 Redux 添加到我们的应用程序的过程。我们将涵盖以下教程：

+   安装 Redux 并准备我们的项目

+   定义动作

+   定义减速器

+   设置存储

+   与远程 API 通信

+   将存储连接到视图

+   使用 Redux 存储离线内容

+   显示网络连接状态

# 介绍

在大多数应用程序的开发过程中，我们都需要更好地处理整个应用程序的状态的方法。这将简化在组件之间共享数据，并为将来扩展我们的应用程序提供更健壮的架构。

为了更好地理解 Redux，本章的结构将与以前的章节不同，因为我们将通过所有这些教程创建一个应用程序。本章中的每个教程都将依赖于上一个教程。

我们将构建一个简单的应用程序来显示用户帖子，并使用`ListView`组件来显示从 API 返回的数据。我们将使用位于[`jsonplaceholder.typicode.com`](https://jsonplaceholder.typicode.com)的优秀模拟数据 API。

# 安装 Redux 并准备我们的项目

在这个教程中，我们将在一个空应用程序中安装 Redux，并定义我们应用程序的基本文件夹结构。

# 入门

我们将需要一个新的空应用程序来完成这个教程。让我们称之为`redux-app`。

我们还需要两个依赖项：`redux`用于处理状态管理和`react-redux`用于将 Redux 和 React Native 粘合在一起。您可以使用 yarn 从命令行安装它们：

```jsx
yarn add redux react-redux
```

或者您可以使用`npm`：

```jsx
npm install --save redux react-redux
```

# 如何做...

1.  作为这个教程的一部分，我们将构建应用程序将使用的文件夹结构。让我们添加一个`components`文件夹，里面有一个`Album`文件夹，用来保存相册组件。我们还需要一个`redux`文件夹来保存所有 Redux 代码。

1.  在`redux`文件夹中，让我们添加一个`index.js`文件进行 Redux 初始化。我们还需要一个`photos`目录，里面有一个`actions.js`文件和一个`reducer.js`文件。

1.  目前，`App.js`文件将只包含一个`Album`组件，我们稍后会定义：

```jsx
import React, { Component } from 'react';
import { StyleSheet, SafeAreaView } from 'react-native';

import Album from './components/Album';

const App = () => (
  <SafeAreaView style={styles.container}>
    <Album />
  </SafeAreaView>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});

export default App;
```

# 它是如何工作的...

在*入门*中，我们安装了`redux`和`react-redux`库。`react-redux`库包含了将 Redux 与 React 集成的必要绑定。Redux 并不是专门设计用于与 React 一起工作的。您可以将 Redux 与任何其他 JavaScript 库一起使用。通过使用`react-redux`，我们将能够无缝地将 Redux 集成到我们的 React Native 应用程序中。

在*步骤 2*中，我们创建了我们应用程序将使用的主要文件夹：

+   `components`文件夹将包含我们的应用程序组件。在这种情况下，我们只添加了一个`Album`组件，以使本教程简单。

+   `redux`文件夹将包含所有与 Redux 相关的代码（初始化、操作和减速器）。

在中等到大型的应用程序中，您可能希望进一步分离您的 React Native 组件。React 社区的标准是将应用程序的组件分为三种不同的类型：

+   `Components`：社区称它们为展示性组件。简单来说，这些是不知道任何业务逻辑或 Redux 操作的组件。这些组件只通过 props 接收数据，并且应该可以在任何其他项目中重复使用。按钮或面板将是展示性组件的完美例子。

+   `Containers`：这些是直接从 Redux 接收数据并能够调用操作的组件。在这里，我们将定义诸如显示已登录用户的标题之类的组件。通常，这些组件在内部使用展示性组件。

+   `Pages/Views`：这些是应用程序中使用容器和展示性组件的主要模块。

有关构建 Redux 支持组件的更多信息，我建议阅读以下链接的优秀文章，*为可扩展性和可维护性构建您的 React-Redux 项目*：

[`levelup.gitconnected.com/structure-your-react-redux-project-for-scalability-and-maintainability-618ad82e32b7`](https://levelup.gitconnected.com/structure-your-react-redux-project-for-scalability-and-maintainability-618ad82e32b7)

我们还需要创建一个`redux/photos`文件夹。在这个文件夹中，我们将创建以下内容：

+   `actions.js`文件，其中将包含应用程序可以执行的所有操作。我们将在下一个教程中更多地讨论操作。

+   `reducer.js`文件，其中将包含管理 Redux 存储中数据的所有代码。我们将在以后的教程中更深入地探讨这个主题。

# 定义操作

一个 action 是发送数据到 store 的信息载荷。使用这些 actions 是组件请求或发送数据到 Redux store 的*唯一*方式，Redux store 作为整个应用程序的全局状态对象。一个 action 只是一个普通的 JavaScript 对象。我们将定义返回这些 actions 的函数。返回 action 的函数称为 action creator。

在这个教程中，我们将创建加载图库初始图片的 actions。在这个教程中，我们将添加硬编码的数据，但以后，我们将从 API 请求这些数据，以创建更真实的场景。

# 准备工作

让我们继续在上一个教程中的代码上工作。确保按照这些步骤安装 Redux 并构建我们将在此项目中使用的文件夹结构。

# 如何做...

1.  我们需要为每个 action 定义类型。打开`redux/photos/actions.js`文件。action 类型被定义为常量，以便稍后在 actions 和 reducers 中引用，如下所示：

```jsx
export const FETCH_PHOTOS = 'FETCH_PHOTOS';
```

1.  现在让我们创建我们的第一个 action creator。每个 action 都需要一个`type`属性来定义它，而且 actions 通常会有一个`payload`属性，用于传递数据。在这个教程中，我们将硬编码一个由两个照片对象组成的模拟 API 响应，如下所示：

```jsx
export const fetchPhotos = () => {
  return {
    type: FETCH_PHOTOS,
    payload: {
      "photos": [
        {
          "albumId": 2,
          "title": "dolore esse a in eos sed",
          "url": "http://placehold.it/600/f783bd",
          "thumbnailUrl": "http://placehold.it/150/d83ea2",
          "id": 2
        },
        {
          "albumId": 2,
          "title": "dolore esse a in eos sed",
          "url": "http://placehold.it/600/8e6eef",
          "thumbnailUrl": "http://placehold.it/150/bf6d2a",
          "id": 3
        }
      ]
    }
  }
}
```

1.  我们将需要为每个我们希望应用程序能够执行的 action 创建一个 action creator，并且我们希望这个应用程序能够添加和删除图片。首先，让我们添加`addBookmark` action creator，如下所示：

```jsx
export const ADD_PHOTO = 'ADD_PHOTO';
export const addPhoto = (photo) => {
  return {
    type: ADD_PHOTO,
    payload: photo
  };
}
```

1.  同样，我们还需要另一个用于删除照片的 action creator：

```jsx
export const REMOVE_PHOTO = 'REMOVE_PHOTO';
export const removePhoto = (photo) => {
  return {
    type: REMOVE_PHOTO,
    payload: photo
  };
}
```

# 它是如何工作的...

在*步骤 1*中，我们定义了 action 的类型来指示它的作用，这种情况下是获取图片。我们使用常量，因为它将在多个地方使用，包括 action creators、reducers 和测试。

在*步骤 2*中，我们声明了一个 action creator。Actions 是简单的 JavaScript 对象，定义了在我们的应用程序中发生的事件，这些事件将影响应用程序的状态。我们使用 actions 与 Redux 存储中的数据进行交互。

只有一个单一的要求：每个 action 必须有一个`type`属性。此外，一个 action 通常会包括一个`payload`属性，其中包含与 action 相关的数据。在这种情况下，我们使用了一个照片对象的数组。

只要`type`属性被定义，一个 action 就是有效的。如果我们想发送其他内容，使用`payload`属性是一种常见的约定，这是 flux 模式所推广的。然而，name 属性并不是固有特殊的。我们可以将其命名为`params`或`data`，行为仍然相同。

# 还有更多...

目前，我们已经定义了动作创建者，它们是简单的返回动作的函数。为了使用它们，我们需要使用 Redux `store`提供的`dispatch`方法。我们将在后面的配方中了解更多关于 store 的内容。

# 定义 reducers

到目前为止，我们已经为我们的应用创建了一些动作。正如前面讨论的，动作定义了应该发生的事情，但我们还没有为执行动作创建任何内容。这就是 reducers 的作用。Reducers 是定义动作如何影响 Redux `store`中的数据的函数。在 reducer 中访问`store`中的数据。

Reducers 接收两个参数：`state`和`action`。`state`参数表示应用的全局状态，`action`参数是 reducer 使用的动作对象。Reducers 返回一个新的`state`参数，反映了与给定`action`参数相关的更改。在这个配方中，我们将介绍一个用于通过在前一个配方中定义的动作来获取照片的 reducer。

# 准备工作

这个配方依赖于前一个配方*定义动作*。确保从本章的开头开始，以避免任何问题或混淆。

# 如何做...

1.  让我们从打开`photos/reducer.js`文件开始，并导入我们在前一个配方中定义的所有动作类型，如下所示：

```jsx
import {
  FETCH_PHOTOS,
  ADD_PHOTO,
  REMOVE_PHOTO
} from './actions';
```

1.  我们将为这个 reducer 中的状态定义一个初始状态对象。它有一个`photos`属性，初始化为一个空数组，用于当前加载的照片，如下所示：

```jsx
const initialState = () => return {
 photos: []
};
```

1.  现在我们可以定义`reducer`函数。它将接收两个参数，当前状态和已经被分发的动作，如下所示：

```jsx
export default (state = initialState, action) => {
  // Defined in next steps 
} 
```

React Native 组件也可以有一个`state`对象，但这是一个完全独立于 Redux 使用的`state`。在这个上下文中，`state`指的是存储在 Redux `store`中的全局状态。

1.  状态是不可变的，所以在 reducer 函数内部，我们需要返回当前动作的新状态，而不是操纵状态，如下所示：

```jsx
export default (state = initialState, action) => {
 switch (action.type) {
 case FETCH_PHOTOS:
 return {
 ...state,
 photos: [...action.payload],
 };
  // Defined in next steps
}
```

1.  为了将新的书签添加到数组中，我们只需要获取操作的有效负载并将其包含在新数组中。我们可以使用展开运算符在`state`上展开当前的照片数组，然后将`action.payload`添加到新数组中，如下所示：

```jsx
    case ADD_PHOTO:
      return {
        ...state,
        photos: [...state.photos, action.payload],
      };
```

1.  如果我们想从数组中删除一个项目，我们可以使用 filter 方法，如下所示：

```jsx
    case REMOVE_PHOTO:
      return {
        ...state,
        photos: state.photos.filter(photo => {
          return photo.id !== action.payload.id
        })
      };
```

1.  最后一步是将我们拥有的所有 reducer 组合在一起。在一个更大的应用程序中，您可能有理由将您的 reducer 拆分成单独的文件。由于我们只使用一个 reducer，这一步在技术上是可选的，但它说明了如何使用 Redux 的`combineReducers`助手将多个 reducer 组合在一起。让我们在`redux/index.js`文件中使用它，我们还将在下一个示例中用它来初始化 Redux 存储，如下所示：

```jsx
import { combineReducers } from 'redux';
import photos from './photos/reducers';
const reducers = combineReducers({
  photos,
});
```

# 工作原理...

在*步骤 1*中，我们导入了在上一个示例中声明的所有操作类型。我们使用这些类型来确定应该采取什么操作以及`action.payload`应该如何影响 Redux 状态。

在*步骤 2*中，我们定义了`reducer`函数的初始状态。目前，我们只需要一个空数组来存储我们的照片，但我们可以向状态添加其他属性，例如`isLoading`和`didError`的布尔属性来跟踪加载和错误状态。这些可以反过来用于在`async`操作期间和响应`async`操作时更新 UI。

在*步骤 3*中，我们定义了`reducer`函数，它接收两个参数：当前状态和正在分派的操作。如果没有提供初始状态，我们将初始状态设置为`initialState`。这样，我们可以确保照片数组始终存在于应用程序中，这将有助于避免在分派不影响 Redux 状态的操作时出现错误。

在*步骤 4*中，我们定义了一个用于获取照片的操作。请记住，状态永远不会被直接操作。如果操作的类型与 case 匹配，那么通过将当前的`state.photos`数组与`action.payload`上的传入照片组合在一起，将创建一个新的状态对象。

`reducer`函数应该是纯的。这意味着任何输入值都不应该有副作用。改变状态或操作是不好的做法，应该始终避免。突变可能导致数据不一致或无法正确触发渲染。此外，为了防止副作用，我们应该避免在 reducer 内部执行任何 AJAX 请求。

在*步骤 5*中，我们创建了向 photos 数组添加新元素的 action，但我们没有使用`Array.push`，而是返回一个新数组，并将传入的元素附加到最后一个位置，以避免改变状态中的原始数组。

在*步骤 6*中，我们添加了一个从状态中删除书签的 action。这样做的最简单方法是使用`filter`方法，这样我们就可以忽略在 action 的 payload 中收到的 ID 对应的元素。

在*步骤 7*中，我们使用`combineReducers`函数将所有的 reducers 合并成一个单一的全局状态对象，该对象将保存在 store 中。这个函数将使用与 reducer 对应的状态中的键调用每个 reducer；这个函数与下面的函数完全相同：

```jsx
import photosReducer from './photos/reducer'; 

const reducers = function(state, action) { 
  return { 
    photos: photosReducer(state.photos, action), 
  }; 
} 
```

photos reducer 只被调用了关心 photos 的状态的部分。这将帮助你避免在单个 reducer 中管理所有状态数据。

# 设置 Redux store

Redux store 负责更新 reducers 内部计算的状态信息。它是一个单一的全局对象，可以通过 store 的`getState`方法访问。

在这个食谱中，我们将把之前创建的 actions 和 reducer 联系在一起。我们将使用现有的 actions 来影响存储在 store 中的数据。我们还将学习如何通过订阅 store 的更改来记录状态的变化。这个食谱更多地作为一个概念的证明，说明了 actions、reducers 和 store 是如何一起工作的。我们将在本章后面更深入地了解 Redux 在应用程序中更常见的用法。

# 如何做...

1.  让我们打开`redux/index.js`文件，并从`redux`中导入`createStore`函数，如下所示：

```jsx
import { combineReducers, createStore } from 'redux';
```

1.  创建 store 非常简单；我们只需要调用函数

在*步骤 1*中导入并将 reducers 作为第一个参数发送，如下所示：

```jsx
const store = createStore(reducers);
export default store;
```

1.  就是这样！我们已经设置好了 store，现在让我们分发一些 actions。这个食谱中的下一步将从最终项目中删除，因为它们是用来测试我们的设置。让我们首先导入我们想要分发的 action creators：

```jsx
import { 
  loadPhotos, 
  addPhotos, 
  removePhotos, 
} from './photos/actions'; 
```

1.  在分发任何 actions 之前，让我们订阅 store，这将允许我们监听 store 中发生的任何更改。对于我们当前的目的，我们只需要`console.log` `store.getState()`的结果，如下所示：

```jsx
const unsubscribe = store.subscribe(() => {
  console.log(store.getState());
});
```

1.  让我们分发一些 actions，并在开发者控制台中查看结果状态：

```jsx
store.dispatch(loadPhotos());
```

1.  为了添加一个新的书签，我们需要使用照片对象作为参数来分派`addBookmark`操作创建者：

```jsx
store.dispatch(addPhoto({
  "albumId": 2,
  "title": "dolore esse a in eos sed",
  "url": `http://placehold.it/600/`,
  "thumbnailUrl": `http://placehold.it/150/`
}));
```

1.  要删除一个项目，我们将要删除的照片的`id`传递给操作创建者，因为这是减速器用来查找应该被删除的项目的内容：

```jsx
store.dispatch(removePhoto({ id: 1 }));
```

1.  执行完所有这些操作后，我们可以通过运行我们在*步骤 4*中订阅 store 时创建的取消订阅函数来停止监听 store 上的更改，如下所示：

```jsx
unsubscribe(); 
```

1.  我们需要将`redux/index.js`文件导入到`App.js`文件中，这将运行本示例中的所有代码，以便我们可以在开发者控制台中看到相关的`console.log`消息：

```jsx
import store from './redux'; 
```

# 工作原理...

在*步骤 3*中，我们导入了我们在之前的示例*定义操作*中创建的操作创建者。即使我们还没有 UI，我们也可以使用 Redux 存储并观察更改的发生。只需调用一个操作创建者，然后分派生成的操作即可。

在*步骤 5*中，我们从`store`实例中调用了`dispatch`方法。`dispatch`接受一个由`loadBookmarks`操作创建者创建的操作。然后将依次调用减速器，这将在状态上设置新的照片。

一旦我们的 UI 就位，我们将以类似的方式从我们的组件中分发操作，这将更新状态，最终触发组件的重新渲染，显示新数据。

# 与远程 API 通信

我们目前正在从操作中的硬编码数据中加载书签。在真实的应用程序中，我们更有可能从 API 中获取数据。在这个示例中，我们将使用 Redux 中间件来帮助从 API 中获取数据的过程。

# 准备工作

在这个示例中，我们将使用`axios`来进行所有的 AJAX 请求。使用`npm`安装它：

```jsx
npm install --save axios
```

或者你可以使用`yarn`安装它：

```jsx
yarn add axios
```

对于这个示例，我们将使用 Redux 中间件`redux-promise-middleware`。使用`npm`安装该软件包：

```jsx
npm install --save redux-promise-middleware
```

或者你可以使用`yarn`安装它：

```jsx
yarn add redux-promise-middleware
```

这个中间件将为我们应用程序中进行的每个 AJAX 请求创建并自动分派三个相关的动作：一个是在请求开始时，一个是在请求成功时，一个是在请求失败时。使用这个中间件，我们能够定义一个返回带有*promise*负载的动作创建者。在我们的情况下，我们将创建`async`动作`FETCH_PHOTOS`，其负载是一个 API 请求。中间件将创建并分派一个`FETCH_PHOTOS_PENDING`类型的动作。当请求解析时，中间件将创建并分派一个`FETCH_PHOTOS_FULFILLED`类型的动作，如果请求成功，则将解析的数据作为`payload`，如果请求失败，则将错误作为`payload`的`FETCH_PHOTOS_REJECTED`类型的动作。

# 如何做到...

1.  让我们首先将新的中间件添加到我们的 Redux 存储中。在`redux/index.js`文件中，让我们添加 Redux 方法`applyMiddleware`。我们还将添加我们刚刚安装的新中间件，如下所示：

```jsx
import { combineReducers, createStore, applyMiddleware } from 'redux';
import promiseMiddleware from 'redux-promise-middleware';
```

1.  在我们之前定义的`createStore`调用中，我们可以将`applyMiddleware`作为第二个参数传递。`applyMiddleware`接受一个参数，即我们要使用的中间件`promiseMiddleware`：

```jsx
const store = createStore(reducers, applyMiddleware(promiseMiddleware()));
```

与其他一些流行的 Redux 中间件解决方案（如`redux-thunk`）不同，`promiseMiddleware`在传递给`applyMiddleware`时必须被调用。它是一个返回中间件的函数。

1.  我们现在将在我们的动作中进行真正的 API 请求，因此我们需要将`axios`导入到`redux/photos/actions`中。我们还将添加 API 的基本 URL。我们使用的是前几章中使用的相同的虚拟数据 API，托管在[`jsonplaceholder.typicode.com`](http://jsonplaceholder.typicode.com)，如下所示：

```jsx
import axios from 'axios';
const API_URL='http://jsonplaceholder.typicode.com'; 
```

1.  接下来，我们将更新我们的动作创建者。我们将首先更新我们处理 AJAX 请求所需的类型，如下所示：

```jsx
export const FETCH_PHOTOS = 'FETCH_PHOTOS';
export const FETCH_PHOTOS_PENDING = 'FETCH_PHOTOS_PENDING';
export const FETCH_PHOTOS_FULFILLED = 'FETCH_PHOTOS_FULFILLED';
export const FETCH_PHOTOS_REJECTED = 'FETCH_PHOTOS_REJECTED';
```

1.  与其为这个动作返回虚拟数据作为`payload`，我们将返回一个`GET`请求。由于这是一个`Promise`，它将触发我们的新中间件。另外，请注意动作的类型是`FETCH_PHOTOS`。这将导致中间件自动创建`FETCH_PHOTOS_PENDING`，`FETCH_PHOTOS_FULFILLED`，如果成功则带有解析数据的`payload`，以及`FETCH_PHOTOS_REJECTED`，带有发生的错误的`payload`，如下所示：

```jsx
export const fetchPhotos = () => {
  return {
    type: FETCH_PHOTOS,
    payload: axios.get(`${API_URL}/photos?_page=1&_limit=20`)
  }
}
```

1.  就像`FETCH_PHOTOS`动作一样，我们将利用相同的中间件提供的类型来处理`ADD_PHOTO`动作，如下所示：

```jsx
export const ADD_PHOTO = 'ADD_PHOTO';
export const ADD_PHOTO_PENDING = 'ADD_PHOTO_PENDING';
export const ADD_PHOTO_FULFILLED = 'ADD_PHOTO_FULFILLED';
export const ADD_PHOTO_REJECTED = 'ADD_PHOTO_REJECTED';
```

1.  action creator 本身将不再只返回传入的照片作为`payload`，而是将通过 API 传递一个`POST`请求的 promise 来添加图片，如下所示：

```jsx
export const addPhoto = (photo) => {
  return {
    type: ADD_PHOTO,
    payload: axios.post(`${API_URL}/photos`, photo)
  };
}
```

1.  我们可以按照相同的模式将`REMOVE_PHOTO`动作转换为使用 API 进行*删除*照片的 AJAX 请求。像`ADD_PHOTO`和`FETCH_PHOTOS`这两个 action creator 一样，我们将为每个动作定义动作类型，然后将删除`axios`请求作为动作的`payload`返回。由于在我们从 Redux 存储中删除图像对象时，我们将需要`photoId`在 reducer 中，因此我们还将其作为动作的`meta`属性上的对象传递，如下所示：

```jsx
export const REMOVE_PHOTO = 'REMOVE_PHOTO';
export const REMOVE_PHOTO_PENDING = 'REMOVE_PHOTO_PENDING';
export const REMOVE_PHOTO_FULFILLED = 'REMOVE_PHOTO_FULFILLED';
export const REMOVE_PHOTO_REJECTED = 'REMOVE_PHOTO_REJECTED';
export const removePhoto = (photoId) => {
  console.log(`${API_URL}/photos/${photoId}`);
  return {
    type: REMOVE_PHOTO,
    payload: axios.delete(`${API_URL}/photos/${photoId}`),
    meta: { photoId }
  };
}
```

1.  我们还需要重新审视我们的 reducers 以调整预期的 payload。在`redux/reducers.js`中，我们将首先导入我们将使用的所有动作类型，并更新`initialState`。由于在下一个步骤中将会显而易见的原因，让我们将`state`对象上的照片数组重命名为`loadedPhotos`，如下所示：

```jsx
import {
  FETCH_PHOTOS_FULFILLED,
  ADD_PHOTO_FULFILLED,
  REMOVE_PHOTO_FULFILLED,
} from './actions';

const initialState = {
  loadedPhotos: []
};
```

1.  在 reducer 本身中，更新每个 case 以采用基本动作的`FULFILLED`变体：`FETCH_PHOTOS`变为`FETCH_PHOTOS_FULFILLED`，`ADD_PHOTOS`变为`ADD_PHOTOS_FULFILLED`，`REMOVE_PHOTOS`变为`REMOVE_PHOTOS_FULFILLED`。我们还将更新所有对`state`的`photos`数组的引用，将其从`photos`更新为`loadedPhotos`。在使用`axios`时，所有响应对象都将包含一个`data`参数，其中包含从 API 接收到的实际数据，这意味着我们还需要将所有对`action.payload`的引用更新为`action.payload.data`。在`REMOVE_PHOTO_FULFILLED` reducer 中，我们无法再在`action.payload.id`中找到`photoId`，这就是为什么我们在*步骤 8*中在动作的`meta`属性上传递了`photoId`，因此`action.payload.id`变为`action.meta.photoId`，如下所示：

```jsx
export default (state = initialState, action) => {
  switch (action.type) {
    case FETCH_PHOTOS_FULFILLED:
      return {
        ...state,
        loadedPhotos: [...action.payload.data],
      };
    case ADD_PHOTO_FULFILLED:
      return {
        ...state,
        loadedPhotos: [action.payload.data, ...state.loadedPhotos],
      };
    case REMOVE_PHOTO_FULFILLED:
      return {
        ...state,
        loadedPhotos: state.loadedPhotos.filter(photo => {
          return photo.id !== action.meta.photoId
        })
      };
    default:
      return state;
  }
}
```

# 工作原理...

在*步骤 2*中，我们应用了在*入门*部分安装的中间件。如前所述，这个中间件将允许我们为自动创建`PENDING`、`FULFILLED`和`REJECTED`请求状态的单个动作创建 AJAX 动作的动作创建者。

在*步骤 5*中，我们定义了`fetchPhotos`动作创建者。您会回忆起前面的食谱，动作是普通的 JavaScript 对象。由于我们在动作的 payload 属性上定义了一个 Promise，`redux-promise-middleware`将拦截此动作并自动为三种可能的请求状态创建三个关联的动作。

在*步骤 7*和*步骤 8*中，我们定义了`addPhoto`动作创建器和`removePhoto`动作创建器，就像`fetchPhotos`一样，它们的操作负载是一个 AJAX 请求。

通过使用这个中间件，我们能够避免重复使用相同的样板来进行不同的 AJAX 请求。

在这个配方中，我们只处理了应用程序中进行的 AJAX 请求的成功条件。在真实的应用程序中，明智的做法是还要处理以`_REJECTED`结尾的操作类型表示的错误状态。这将是一个处理错误的好地方，通过将其保存到 Redux 存储器中，以便在发生错误时视图可以显示错误信息。

# 将存储器连接到视图

到目前为止，我们已经设置了状态，包括了中间件，并为与远程 API 交互定义了动作、动作创建器和减速器。然而，我们无法在屏幕上显示任何这些数据。在这个配方中，我们将使我们的组件能够访问我们创建的存储器。

# 准备工作

这个配方依赖于之前的所有配方，所以确保按照本配方之前的每个配方进行操作。

在本章的第一个配方中，我们安装了`react-redux`库以及其他依赖项。在这个配方中，我们终于要开始使用它了。

我们还将使用第三方库来生成随机颜色十六进制值，我们将使用它来从占位图像服务[`placehold.it/`](https://placehold.it/)请求彩色图像。在开始之前，使用`npm`安装`randomcolor`：

```jsx
npm install --save randomcolor
```

或者您也可以使用`yarn`安装它：

```jsx
yarn add randomcolor
```

# 如何做...

1.  让我们从将 Redux 存储器连接到 React Native 应用程序的`App.js`开始。我们将从导入开始，从`react-redux`导入`Provider`和我们之前创建的存储器。我们还将导入我们即将定义的`Album`组件，如下所示：

```jsx
import React, { Component } from 'react';
import { StyleSheet, SafeAreaView } from 'react-native';
import { Provider } from 'react-redux';
import store from './redux';

import Album from './components/Album';
```

1.  `Provider`的工作是将我们的 Redux 存储器连接到 React Native 应用程序，以便应用程序的组件可以与存储器通信。`Provider`应该用于包装整个应用程序，由于此应用程序位于`Album`组件中，我们将`Album`组件与`Provider`组件一起包装。`Provider`接受一个`store`属性，我们将传入我们的 Redux 存储器。应用程序和存储器已连接：

```jsx
const App = () => (
  <Provider store={store}>
    <Album />
  </Provider>
);

export default App;
```

1.  让我们转向`Album`组件。该组件将位于`components/Album/index.js`。我们将从导入开始。我们将导入`randomcolor`包以生成随机颜色十六进制值，如*入门*部分所述。我们还将从`react-redux`中导入`connect`，以及我们在之前的示例中定义的 action creators。`connect`将连接我们的应用程序到 Redux 存储，并且我们可以使用 action creators 来影响存储的状态，如下所示：

```jsx
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  SafeAreaView,
  ScrollView,
  Image,
  TouchableOpacity
} from 'react-native';
import randomColor from 'randomcolor';
import { connect } from 'react-redux';
import {
  fetchPhotos,
  addPhoto,
  removePhoto
} from '../../redux/photos/actions';

```

1.  让我们创建`Album`类，但是，我们不会直接将`Album`作为`default`导出，而是使用`connect`将`Album`连接到存储。请注意，`connect`使用了两组括号，并且组件被传递到了第二组括号中，如下所示：

```jsx
class Album extends Component {

}

export default connect()(Album);
```

1.  在调用`connect`时，第一组括号接受两个函数参数：`mapStateToProps`和`mapDispatchToProps`。我们将首先定义`mapStateToProps`，它以`state`作为参数。这个`state`是我们的全局 Redux 状态对象，包含了所有的数据。该函数返回一个包含我们想在组件中使用的`state`片段的对象。在我们的情况下，我们只需要从`photos` reducer 中的`loadedPhotos`属性。通过将这个值设置为返回对象中的`photos`，我们可以期望`this.props.photos`是存储在`state.photos.loadedPhotos`中的值。当 Redux 存储更新时，它将自动更改：

```jsx
class Album extends Component {

}

const mapStateToProps = (state) => {
 return {
 photos: state.photos.loadedPhotos
 }
}

export default connect(mapStateToProps)(Album);
```

1.  同样，`mapDispatchToProps`函数也将我们的 action creators 映射到组件的 props。该函数接收 Redux 方法`dispatch`，用于执行 action creator。我们将每个 action creator 的执行映射到相同名称的键上，这样`this.props.fetchPhotos()`将执行`dispatch(fetchPhotos())`，依此类推，如下所示：

```jsx
class Album extends Component {

}

const mapStateToProps = (state) => {
  return {
    photos: state.photos.loadedPhotos
  }
}
 const mapDispatchToProps = (dispatch) => {
 return {
 fetchPhotos: () => dispatch(fetchPhotos()),
 addPhoto: (photo) => dispatch(addPhoto(photo)),
 removePhoto: (id) => dispatch(removePhoto(id))
 }
}

export default connect(mapStateToProps, mapDispatchToProps)(Album);
```

1.  现在我们已经将 Redux 存储连接到了我们的组件，让我们创建组件本身。我们可以利用`componentDidMount`生命周期钩子来获取我们的照片，如下所示：

```jsx
class Album extends Component {
 componentDidMount() {
 this.props.fetchPhotos();
 }
  // Defined on later steps
}
```

1.  我们还需要一个添加照片的方法。在这里，我们将使用`randomcolor`包（按照惯例导入为`randomColor`）来使用[placehold.it](http://placehold.it)服务创建一张图片。生成的颜色字符串带有一个哈希前缀的十六进制值，而图片服务的请求不需要这个前缀，所以我们可以简单地使用`replace`调用来移除它。要添加照片，我们只需调用映射到`props`的`addPhoto`函数，传入新的`photo`对象，如下所示：

```jsx
  addPhoto = () => {
    const photo = {
      "albumId": 2,
      "title": "dolore esse a in eos sed",
      "url": `http://placehold.it/600/${randomColor().replace('#',
       '')}`,
      "thumbnailUrl": 
  `http://placehold.it/150/${randomColor().replace('#', '')}`
    };
    this.props.addPhoto(photo);
  }
```

1.  我们还需要一个`removePhoto`函数。这个函数所需要做的就是调用已经映射到`props`的`removePhoto`函数，并传入要删除的照片的 ID，如下所示：

```jsx
  removePhoto = (id) => {
    this.props.removePhoto(id);
  }
```

1.  应用程序的模板将需要一个`TouchableOpacity`按钮用于添加照片，一个`ScrollView`用于容纳所有图像的可滚动列表，以及所有我们的图像。每个`Image`组件还将包装在一个`TouchableOpacity`组件中，以在按下图像时调用`removePhoto`方法，如下所示：

```jsx
  render() {
    return (
      <SafeAreaView style={styles.container}>
        <Text style={styles.toolbar}>Album</Text>
        <ScrollView>
          <View style={styles.imageContainer}>
            <TouchableOpacity style={styles.button} onPress=
             {this.addPhoto}>
              <Text style={styles.buttonText}>Add Photo</Text>
            </TouchableOpacity>
            {this.props.photos ? this.props.photos.map((photo) => {
              return(
                <TouchableOpacity onPress={() => 
                 this.removePhoto(photo.id)} key={Math.random()}>
                  <Image style={styles.image}
                    source={{ uri: photo.url }}
                  />
                </TouchableOpacity>
              );
            }) : null}
          </View>
        </ScrollView>
      </SafeAreaView>
    );
  }
```

1.  最后，我们将添加样式，以便应用程序具有布局，如下所示。这里没有我们之前没有多次涵盖过的内容：

```jsx
const styles = StyleSheet.create({
  container: {
    backgroundColor: '#ecf0f1',
    flex: 1,
  },
  toolbar: {
    backgroundColor: '#3498db',
    color: '#fff',
    fontSize: 20,
    textAlign: 'center',
    padding: 20,
  },
  imageContainer: {
    flex: 1,
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
  },
  image: {
    height: 300,
    width: 300
  },
  button: {
    margin: 10,
    padding: 20,
    backgroundColor: '#3498db'
  },
  buttonText: {
    fontSize: 18,
    color: '#fff'
  }
});
```

1.  应用程序已完成！单击“添加照片”按钮将在图像列表的开头添加一个新照片，并按下图像将删除它。请注意，由于我们使用的是虚拟数据 API，因此`POST`和`DELETE`请求将返回给定操作的适当响应。但是，实际上并没有向数据库添加或删除任何数据。这意味着如果应用程序被刷新，图像列表将重置，并且如果您尝试使用“添加照片”按钮添加的任何照片，您可以期望出现错误。随时将此应用程序连接到真实的 API 和数据库以查看预期结果：

![](img/46c604eb-1e47-4f34-af62-5854ca99c453.png)

# 它是如何工作的...

在*步骤 4*中，我们使用了`react-redux`提供的`connect`方法，为`Album`组件赋予了与我们在整个章节中一直在使用的 Redux 存储库的连接。对`connect`的调用返回一个函数，该函数立即通过第二组括号执行。通过将`Album`组件传递到此返回函数中，`connect`将组件和存储库粘合在一起。

在*步骤 5*中，我们定义了`mapStateToProps`函数。此函数中的第一个参数是 Redux 存储库中的`state`，它由`connect`注入到函数中。从`mapStateToProps`返回的对象中定义的任何键都将成为组件`props`上的属性。这些 props 的值将订阅 Redux 存储库中的`state`，因此任何影响这些`state`片段的更改都将在组件内自动更新。

`mapStateToProps` 将 Redux 存储中的 `state` 映射到组件的 props，而 `mapDispatchToProps` 将 *action creators* 映射到组件的 props。在 *步骤 6* 中，我们定义了这个函数。它具有特殊的 Redux 方法 `dispatch`，用于调用存储中的 action creators。`mapDispatchToProps` 返回一个对象，将 actions 的 `dispatch` 调用映射到组件的 props 上指定的键。

在 *步骤 7* 中，我们创建了 `componentDidMount` 方法。组件在挂载时所需的所有照片只需调用映射到 `this.props.fetchPhotos` 的 action creator 即可。就是这样！`fetchPhotos` action creator 将被派发。由于 action creator 返回的 `fetchPhoto` action 具有一个 Promise 存储在其 `payload` 属性中，这个 Promise 是以 `axios` AJAX 请求的形式存储的，因此我们在之前的示例中应用了 `redux-promise-middleware`。中间件将拦截该 action，处理请求，并发送一个带有解析数据的新 action 到 reducers 的 `payload` 属性。如果请求成功，将派发带有解析数据的 `FETCH_PHOTOS_FULFILLED` 类型的 action，如果不成功，将派发带有错误作为 `payload` 的 `FETCH_PHOTOS_REJECTED` action。在成功时，处理 `FETCH_PHOTOS_FULFILLED` 的 reducer 中的情况将执行，`loadedPhotos` 将在存储中更新，进而 `this.props.photos` 也将被更新。更新组件的 props 将触发重新渲染，并且新数据将显示在屏幕上。

在 *步骤 8* 和 *步骤 9* 中，我们遵循相同的模式来定义 `addPhoto` 和 `removePhoto`，它们调用同名的 action creators。action creators 产生的 action 由中间件处理，适当的 reducer 处理生成的 action，如果 Redux 存储中的 `state` 发生变化，所有订阅的 props 将自动更新！

# 使用 Redux 存储离线内容

Redux 是一个很好的工具，可以在应用运行时跟踪应用的状态。但是如果我们有一些数据需要在不使用 API 的情况下存储怎么办？例如，我们可以保存组件的状态，这样当用户关闭并重新打开应用时，该组件的先前状态可以被恢复，从而允许我们在会话之间持久化应用的一部分。Redux 数据持久化也可以用于缓存信息，以避免不必要地调用 API。您可以参考第八章中的*在网络连接丢失时屏蔽应用*教程，了解如何检测和处理网络连接状态的更多信息。

# 做好准备

这个教程依赖于之前的教程，所以一定要跟着之前的所有教程一起进行。在这个教程中，我们将使用`redux-persist`包来持久化我们应用的 Redux 存储中的数据。用`npm`安装它：

```jsx
npm install --save redux-persist
```

或者你可以用`yarn`安装它：

```jsx
yarn add redux-persist
```

# 如何做...

1.  让我们从`redux/index.js`中添加我们需要的依赖项。我们在这里从`redux-persist`导入的`storage`方法将使用 React Native 的`AsyncStorage`方法在会话之间存储 Redux 数据，如下所示：

```jsx
import { persistStore, persistReducer } from 'redux-persist'
import storage from 'redux-persist/lib/storage';
```

1.  我们将使用一个简单的`config`对象来配置我们的`redux-persist`实例。`config`需要一个`key`属性来存储数据与`AsyncStore`的键，并且需要一个 storage 属性，该属性接受`storage`实例，如下所示：

```jsx
const persistConfig = {
  key: 'root',
  storage
}
```

1.  我们将使用我们在*步骤 1*中导入的`persistReducer`方法。这个方法将我们在*步骤 2*中创建的`config`对象作为第一个参数，将我们的 reducers 作为第二个参数：

```jsx
const reducers = combineReducers({
  photos,
});

const persistedReducer = persistReducer(persistConfig, reducers);
```

1.  现在让我们更新我们的存储以使用新的`persistedReducer`方法。还要注意，我们不再将`store`作为默认导出，因为我们需要从这个文件中导出两个内容：

```jsx
export const store = createStore(persistedReducer, applyMiddleware(promiseMiddleware()));
```

1.  我们从这个文件中需要的第二个导出是`persistor`。`persistor`将在会话之间持久化 Redux 存储。我们可以通过调用`persistStore`方法并传入`store`来创建`persistor`，如下所示：

```jsx
export const persistor = persistStore(store);
```

1.  现在我们从`redux/index.js`中得到了`store`和`persistor`作为导出，我们准备在`App.js`中应用它们。我们将从中导入它们，并从`redux-persist`中导入`PersistGate`组件。`PersistGate`将确保我们缓存的 Redux 存储在任何组件加载之前加载：

```jsx
import { PersistGate } from 'redux-persist/integration/react'
import { store, persistor } from './redux';
```

1.  让我们更新`App`组件以使用`PersistGate`。该组件接受两个属性：导入的`persistor`属性和一个`loading`属性。我们将向`loading`属性传递`null`，但如果我们有一个加载指示器组件，我们可以将其传递进去，`PersistGate`会在数据恢复时显示这个加载指示器，如下所示：

```jsx
const App = () => (
  <Provider store={store}>
    <PersistGate loading={null} persistor={persistor}>
      <Album />
    </PersistGate>
  </Provider>
);
```

1.  为了测试我们的 Redux 存储的持久性，让我们调整`Album`组件中的`componentDidMount`方法。我们将延迟调用`fetchPhotos`两秒钟，这样我们就可以在从 API 再次获取数据之前看到保存的数据，如下所示：

```jsx
  componentDidMount() {
 setTimeout(() => {
      this.props.fetchPhotos();
 }, 2000);
  }
```

根据您要持久化的数据类型，这种功能可以应用于许多情况，包括持久化用户数据和应用状态，甚至在应用关闭后。它还可以用于改善应用的离线体验，如果无法立即进行 API 请求，则缓存 API 请求，并为用户提供填充数据的视图。

# 工作原理...

在*步骤 2*中，我们创建了用于配置`redux-persist`的配置对象。该对象只需要具有`key`和`store`属性，但也支持其他许多属性。您可以通过此处托管的类型定义查看此配置接受的所有选项：[`github.com/rt2zz/redux-persist/blob/master/src/types.js#L13-L27`](https://github.com/rt2zz/redux-persist/blob/master/src/types.js#L13-L27)。

在*步骤 7*中，我们使用了`PersistGate`组件，这是文档建议的延迟渲染直到恢复持久化数据完成的方法。如果我们有一个加载指示器组件，我们可以将其传递给`loading`属性，以便在数据恢复时显示。
