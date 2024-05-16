# 第七章：在 React 中使用 Firebase Cloud Messaging 和 Cloud Functions

在之前的章节中，我们探讨了一些 Firebase 产品，比如实时数据库、身份验证、Cloud Firestore 和 Cloud Storage。然而，我们还没有看到一些高级功能，比如实时消息传递和无服务器应用开发。现在我们准备好探索它们了，所以让我们讨论 Firebase 平台的另外两个产品：Firebase Cloud Messaging 和 Cloud Functions。Firebase Cloud Messaging 是一个消息平台，可以在不同平台（Android、iOS 和 Web）上免费发送消息。Cloud Functions 允许你拥有无服务器应用，这意味着你可以在没有服务器的情况下运行自定义应用逻辑。

以下是本章我们将重点关注的主题列表：

+   **Firebase Cloud Messaging**（**FCM**）的主要特点

+   JavaScript Web 应用的 Firebase 设置

+   客户端应用设置以接收通知

+   服务器设置以发送通知

+   Cloud Functions 的主要特点

+   为 Cloud Functions 设置 Firebase SDK

+   Cloud Function 的生命周期

+   触发函数

+   部署和执行函数

+   函数的终止

让我们先从 FCM 开始，然后我们将介绍 Cloud Functions。

# Firebase Cloud Messaging（FCM）

FCM 提供了一个平台，帮助你使用服务工作者实时向应用用户发送消息和通知。你可以免费跨不同平台发送数百亿条消息，包括 Android、iOS 和 Web（JavaScript）。你还可以安排消息的发送时间，立即或在将来。

FCM 实现中有两个主要组件：一个受信任的环境，包括一个应用服务器或 Cloud 函数来发送消息，以及一个 iOS、Android 或 Web（JavaScript）客户端应用来接收消息。

如果你了解**Google Cloud Messaging**（**GCM**），你可能会问 FCM 与 GCM 有何不同。对这个问题的答案是，FCM 是 GCM 的最新和改进版本。它继承了 GCM 的所有基础设施，并对简化客户端开发进行了改进。只是要注意，GCM 并没有被弃用，Google 仍在支持它。然而，新的客户端功能只会出现在 FCM 上，因此根据 Google 的建议，你应该从 GCM 升级到 FCM。

尽管它支持不同的平台，包括 Android、iOS 和 Web，但在本章中我们主要讨论 Web（JavaScript）。现在让我们来看看 FCM 的主要特点。

# FCM 的关键功能

GCM 的关键功能包括下行消息、上行消息和多功能消息。让我们在下一部分简要地看一下这些功能是什么。

# 发送下行消息

下行消息是代表客户端应用程序从服务器发送给用户的。FCM 消息可以分为两类：通知消息和数据消息。通知消息直接显示给用户。通知消息的一些示例是警报消息、聊天消息，或者通知客户端应用程序启动一些处理的消息备份。数据消息需要在客户端应用程序代码中处理。一些示例是聊天消息或任何特定于您的应用程序的消息。我们将在 FCM 消息的下一部分更多地讨论这些消息类型。

# 发送上行消息

上行消息通过 FCM 通道从设备发送回服务器。您可以通过可靠的 FCM 通道将确认、聊天消息和其他消息从设备发送回服务器。

# 多功能消息定位

FCM 非常灵活，允许您向单个设备、一组设备或所有订阅特定主题的订阅者发送消息。

# FCM 消息

使用 FCM，您可以向客户端发送两种类型的消息：通知消息和数据消息。使用 Firebase SDK 时，这两种消息的最大有效负载大小为 4 KB。但是，当您从 Firebase 控制台发送消息时，它会强制执行 1024 个字符的限制。

通知消息由 FCM SDK 自动处理，因为它们只是显示消息。当您希望 FCM 代表您的客户端应用程序显示通知时，可以使用通知消息。通知消息包含一组预定义的键，还可以包含可选的数据有效负载。

通知消息对象如下所示：

```jsx
{
  "message":{
    "token":"bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P1...",
    "notification":{
      "title":"This is an FCM notification message!",
      "body":"FCM message"
    }
  }
}
```

数据消息由客户端应用程序处理，并包含用户定义的键。它们如下所示：

```jsx
{
  "message":{
    "token":"bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P1...",
    "data":{
      "Name" : "MT",
      "Education" : "Ph.D."
    }
  }
}
```

我们将在接下来的部分看到什么是令牌。

# 为 Javascript Web 应用程序设置 Firebase

FCM 允许您在不同浏览器中的 Web 应用程序中接收通知消息，并支持服务工作者。服务工作者是在后台运行的浏览器脚本，提供离线数据功能、后台数据同步、推送通知等功能。服务工作者支持在以下浏览器中使用：

+   Chrome：50+

+   Firefox：44+

+   Opera Mobile：37+

使用服务工作者，人们可以进行一些恶意活动，比如过滤响应或劫持连接。为了避免这种情况，服务工作者只能在通过 HTTPS 提供的页面上使用。因此，如果您想使用 FCM，您将需要在服务器上拥有有效的 SSL 证书。请注意，在本地环境中，您不需要 SSL；它可以在本地主机上正常工作。

# 安装 Firebase 和 Firebase CLI

如果您要开始一个新的 React 项目，最简单的方法是使用 React Starter Kit 开始。您可以使用以下命令创建一个 React 项目，然后安装**firebase**和**firebase-tools**。如果这是一个现有的 React 和 Firebase 项目，您可以跳过安装步骤：

```jsx
npm install -g create-react-app
```

您可以使用以下命令安装 Firebase：

```jsx
npm install firebase --save
```

您还需要安装 Firebase CLI 以在服务器上运行您的项目。可以使用以下命令进行安装：

```jsx
npm install -g firebase-tools
```

现在，我们将使用 FCM 实现来扩展 Helpdesk 应用程序。

# 配置浏览器以接收消息

首先，您需要从[`developers.google.com/web/fundamentals/web-app-manifest/file`](https://developers.google.com/web/fundamentals/web-app-manifest/file)中添加一个 Web 应用程序清单到我们的项目中，并将以下内容添加到其中：

```jsx
{
  "gcm_sender_id": "103953800507"
}
```

它告诉浏览器允许 FCM 向此应用程序发送消息。`103953800507`的值是硬编码的，在任何您的应用程序中必须相同。Web 应用程序清单是一个简单的 JSON 文件，将包含与您的项目相关的配置元数据，例如您的应用程序的起始 URL 和应用程序图标详细信息。

我们在代码的根文件夹中创建了一个`manifest.json`文件，并将上述内容添加到其中。

# 客户端应用程序设置以接收通知

为了让您的应用程序在浏览器中接收通知，它将需要从用户那里获得权限。为此，我们将添加一段代码，显示一个同意对话框，让用户授予您的应用程序在浏览器中接收通知的权限。

我们将在主目录下的`index.jsx`文件中添加`componentWillMount()`方法，因为我们希望在用户成功登录到应用程序后显示对话框：

```jsx
 componentWillMount() {
      firebase.messaging().requestPermission()
       .then(function() {
        console.log('Permission granted.');
        // you can write logic to get the registration token
          // _this.getToken();
       })
       .catch(function(err) {
        console.log('Unable to get permission to notify.', err);
      });
  }
```

请注意，您需要使用以下行导入`firebase`对象：

```jsx
import firebase from '../firebase/firebase-config';
```

一旦您添加了上述代码，请重新启动服务器并登录到应用程序。它应该向您的应用程序用户显示以下对话框：

![](img/1bf6efe5-7577-4d8e-83b2-968da0b20687.png)

用户授予权限后，您的浏览器才能接收通知。

现在，让我们编写一个函数来获取注册令牌：

```jsx
 getToken() {
        console.log("get token");
        firebase.messaging().getToken()
            .then(function (currentToken) {
                if (currentToken) {
                    console.log("current token", currentToken)
                   // sendTokenToServer(currentToken);
                   //updateUI(currentToken);
                } else {
                    // Show permission request.
                    console.log('No Instance ID token available. 
                    Request permission to generate one.');
                    // Show permission UI.
                  // updateUIForPushPermissionRequired();
                   // setTokenSentToServer(false);
                }
            })
            .catch(function (err) {
                console.log('An error occurred while retrieving token. 
                ', err);
              // showToken('Error retrieving Instance ID token. ', 
                 err);
               // setTokenSentToServer(false);
            });
   }
```

上述函数将检索当前访问令牌，需要将其发送到服务器以订阅通知。您可以在`sendTokenToServer()`方法中实现将此令牌发送到服务器的逻辑。

注册令牌可能在您的 Web 应用程序删除注册令牌或用户清除浏览器数据时更改。在后一种情况下，将需要调用`getToken()`来检索新令牌。由于注册令牌可能会更改，您还应该监视刷新令牌以获取新令牌。FCM 在生成令牌时触发回调，以便您可以获取新令牌。`onTokenRefresh()`回调在生成新令牌时触发，因此在其上下文中调用`getToken()`方法可以确保您拥有当前的注册令牌。您可以编写一个类似这样的函数：

```jsx
 refreshToken() {
        firebase.messaging().onTokenRefresh(function () {
            firebase.messaging().getToken()
                .then(function (refreshedToken) {
                    console.log('Token refreshed.');
                    // Indicate that the new Instance ID token has not 
                       yet been sent to the
                    // app server.
                    //setTokenSentToServer(false);
                     Send Instance ID token to app server. Implement it 
                     as per your requirement
                    //sendTokenToServer(refreshedToken);
                    // ...
                })
                .catch(function (err) {
                    console.log('Unable to retrieve refreshed token ', 
                    err);
                    //showToken('Unable to retrieve refreshed token ', 
                      err);
                });
        });
    }
```

一旦您获得令牌，您可以通过实现类似`sendTokenToServer(refreshedToken)`的方法将其发送到您的应用服务器以存储它，如果您正在使用 React 和 Firebase 实时数据库，您可以直接将其存储在数据库中。

所有这些函数将被添加到`index.jsx`文件中。我们将从`componentWillMount()`方法中调用`getToken()`函数，而`refreshToken()`将从构造函数中调用。

现在，在完成所有这些设置之后，我们将在客户端应用程序中添加接收消息的实际功能。

根据页面状态，无论是在后台运行还是在前台（具有焦点）运行，还是关闭或隐藏在标签后面，消息的行为都会有所不同。

为了接收消息，页面必须处理`onMessage()`回调，并且要处理`onMessage()`，您的应用程序中必须定义一个 Firebase 消息服务工作者。

我们将在项目的根目录下创建一个名为`firebase-messaging-sw.js`的文件，并在其中编写以下代码：

```jsx
importScripts('https://www.gstatic.com/firebasejs/4.1.1/firebase-app.js');
importScripts('https://www.gstatic.com/firebasejs/4.1.1/firebase-messaging.js');

var config = {
    messagingSenderId: "41428255555"
};
firebase.initializeApp(config);
const messaging = firebase.messaging();

messaging.setBackgroundMessageHandler(function(payload) {
    console.log('[firebase-messaging-sw.js] Received background message ', payload);
    // Customize notification here
    const notificationTitle = 'Background Message Title';
    const notificationOptions = {
        body: 'Background Message body.',
        icon: '/firebase-logo.png'
    };

return self.registration.showNotification(notificationTitle,
    notificationOptions);
});
```

或者，您可以使用`useServiceWorker`指定现有的服务工作者。

请注意，您需要更新消息`senderId`值，您可以从 Firebase 控制台获取您的项目的值。

如果您希望在您的网页处于后台时显示通知消息，您需要设置`setBackgroundMessageHandler`来处理这些消息。您还可以自定义消息，例如设置自定义标题和图标。您可以在上面的代码片段中检查它。在应用程序处于后台时接收到的消息会触发浏览器中的显示通知。

现在您可以在您的网页上处理`OnMessage()`事件。我们将在我们的`index.js`文件中添加一个构造函数，以便在页面加载时注册回调函数：

```jsx
constructor(props) {
        super(props);
        //this.refreshToken();
        firebase.messaging().onMessage(function (payload) {
            console.log("Message received. ", payload);
            // Update the UI to include the received message.
            console.log("msg", payload);
            // appendMessage(payload);
        });
    }
```

现在我们的客户端已准备好接收通知消息。让我们配置后端以发送通知。

# 服务器设置以发送通知

第一步是为您的项目启用 FCM API。您可以转到[`console.developers.google.com/apis/api/fcm.googleapis.com/overview?project=<project-id>`](https://console.developers.google.com/apis/api/fcm.googleapis.com/overview?project=%3Cproject-id%3E)并启用它。

要从受信任的环境发送通知，我们将需要 Oauth2 访问令牌和我们在客户端应用程序中获取的客户端注册令牌。

要获取 Oauth2 访问令牌，我们将需要来自您服务帐户的私钥。一旦生成私钥，请将包含私钥的 JSON 文件保存在某个安全的地方。我们将使用 Google API 客户端库在[`developers.google.com/api-client-library/`](https://developers.google.com/api-client-library/)检索访问令牌，因此请使用以下命令安装`googleapis`的`npm`模块：

```jsx
npm install googleapis --save
```

以下函数需要添加到我们的`main.js`文件中以获取访问令牌：

```jsx
app.get('/getAccessToken', function (req, res) {

  var { google } = require('googleapis');

  var key = require('./firebase/serviceAccountKey.json');
  var jwtClient = new google.auth.JWT(
    key.client_email,
    null,
    key.private_key,
    ['https://www.googleapis.com/auth/firebase.messaging'], // an array 
     of auth scopes
    null
  );
  jwtClient.authorize(function (err, tokens) {
    if (err) {
      console.log(err);
      res.send(JSON.stringify({
        "token": err
      }));
    }
    console.log("tokens", tokens);
    res.send(JSON.stringify({
      "token": tokens.access_token
    }));
  });

});
```

当您访问`http://localhost:3000/getAccessToken` URL 时，它将在浏览器中显示您的访问令牌。

在浏览器中，您将看到类似以下的内容：

![](img/1215aa7b-ea9b-43a1-8680-4368f5efdda8.png)

显然，在实际应用中，出于安全原因，您不会在浏览器中显示此令牌或在浏览器控制台中打印它，并且您将在内部使用它。

此访问令牌将在请求的`Authorization`标头中传递，如下所示：

```jsx
headers: {
  'Authorization': 'Bearer ' + accessToken
}
```

现在您有了访问令牌。此外，如果您还记得，当设置客户端应用程序时，我们谈到了`sendTokenToServer(currentToken)`方法，该方法将令牌发送到服务器。您现在必须已将其存储在数据库或缓存中，现在可以使用。

现在我们准备发送我们的第一条通知消息。要发送消息，我们将使用最新的 HTTP v1“发送”请求。

我们的请求将如下所示：

```jsx
POST https://fcm.googleapis.com/v1/projects/demoproject-7cc0d/messages:send

Content-Type: application/json
Authorization: Bearer ya29.c.ElphBTDpfvg35hKz4nDu9XYn3p1jlTRgw9FD0ubT5h4prOtwC9G9IKslBv8TDaAPohQHY0-O3JmADYfsrk7WWdhZAeOoqWSH4wTsVyijjhE-PWRSL2YI1erT"

{
  "message":{
    "token" : "dWOV8ukFukY:APA91bFIAEkV-9vwAIQNRGt57XX2hl5trWf8YpocOHfkYAkgSZr5wfBsNozYZOEm_N0mbdZmKbvmtVCCWovrng4UYwj-zmpe36ySPcP31HxGGGb3noEkeBFyZRDUpv0TD7HAKxTfDuEx...",
    "notification" : {
      "body" : "This is an FCM notification message!",
      "title" : "FCM Message",
      }
   }
}
```

您将替换所有令牌并使用您的项目 ID 更新 URL，然后您应该能够发送您的第一条消息。

我使用了一个 rest 客户端来发送消息，并且由于我的浏览器在后台运行，它会在系统托盘中显示通知消息。您可以在下一个截图中看到：

！[](Images/4af8d08c-b8c1-4528-9d2c-b842921e4e5a.png)Postman chrome 工具扩展；目的只是显示发送 FCM 通知消息的请求和响应

请求正文如下：

！[](Images/51c64a73-50bf-428d-86c1-fc39315717ad.png)Postman chrome 工具扩展；图片的目的只是显示我们之前发送的请求的正文

以下是关于消息请求的重要注意事项：

URL：`https://fcm.googleapis.com/v1/projects/<projectid>/messages:send`

+   标头：包含两个键值对：

+   `Content-Type`：`application/json`

+   `Authorization`：`Bearer` <访问令牌>

+   请求正文：包含具有以下键值的消息对象：

+   `token`：<注册令牌，用于向客户端应用发送消息>

+   `notification`：它包含您的通知消息的配置

是的，现在我们已经在我们的应用程序中集成了 FCM，以便将通知消息发送到后台运行应用程序的单个设备。但是，您可能希望将通知发送到一组设备，或者可能希望将消息发送到客户端已订阅的主题。基本概念将保持不变，但配置将发生变化。您可以在 firebase 文档中参考这些主题[`firebase.google.com/docs/cloud-messaging`](https://firebase.google.com/docs/cloud-messaging)。

我们将在下一节中看到云函数。

# 云函数

一般来说，任何软件应用都有一些后端逻辑，这些逻辑部署在服务器上，通过互联网访问。在大型企业级应用程序（如银行或金融）的情况下，可能值得管理一个服务器或一组服务器。然而，在小型应用程序或您希望根据某些用户事件执行特定逻辑的应用程序中，例如数据库中的数据更改或来自移动应用程序或 Web 应用程序的 API 请求，管理服务器可能会增加工作量和成本。但是，当您使用 Firebase 平台时，您不需要担心这一点，因为它提供了**云函数**，让您根据特定 Firebase 产品发出的事件运行代码，而无需管理服务器。

# 云函数的关键特性

云函数具有许多功能，包括与其他 Firebase 产品和第三方 API 的轻松集成，以及强大的安全性和隐私。

云函数的关键特性在以下子主题中讨论。

# 与其他 Firebase 产品和第三方 API 的无缝集成

云函数可以与其他 Firebase 产品和第三方 API 无缝集成。

您的自定义函数可以在特定事件上执行，这些事件可以由列出的 Firebase 产品发出：

+   云 Firestore 触发器

+   实时数据库触发器

+   Firebase 身份验证触发器

+   Firebase 分析触发器

+   云存储触发器

+   云 Pub/Sub 触发器

+   HTTP 触发器

您可以使用 Firebase Admin SDK 来实现不同 Firebase 产品的无缝集成。在一些最常见的应用需求中非常有用。假设您想要在实时数据库中的某些内容发生变化时生成数据库索引或审计日志；您可以编写一个基于实时数据库触发器执行的云函数。反过来，您可以根据特定用户行为执行一些数据库操作。同样地，您可以将云函数与 Firebase 云消息传递（FCM）集成，以便在数据库中发生特定事件时通知用户。

云函数的集成不仅限于 Firebase 产品；您还可以通过编写 Webhook 将云函数与一些第三方 API 服务集成。假设您是开发团队的一部分，并且希望在有人提交代码到 Git 时更新 Slack 频道。您可以使用 Git Webhook API，它将触发您的云函数执行逻辑以将消息发送到 Slack 频道。同样，您可以使用第三方 Auth 提供程序 API，例如 LinkedIn，以允许用户登录。

# 无需维护服务器

云函数在无需购买或维护任何服务器的情况下运行您的代码。您可以编写 JavaScript 或 TypeScript 函数，并使用云上的单个命令部署它。您不需要担心服务器的维护或扩展。Firebase 平台将自动为您管理。服务器实例的扩展发生得非常精确，具体取决于工作负载。

# 私密和安全

应用程序的业务逻辑应该对客户端隐藏，并且必须足够安全，以防止对代码的任何操纵或逆向工程。云函数是完全安全的，因此始终保持私密，并始终按照您的意愿执行。

# 函数的生命周期

云函数的生命周期大致可以分为五个阶段，分别是：

1.  编写新函数的代码，并定义函数应在何时执行的条件。函数定义或代码还包含事件提供程序的详细信息，例如实时数据库或 FCM。

1.  使用 Firebase CLI 部署函数，并将其连接到代码中定义的事件提供程序。

1.  当事件提供程序生成与函数中定义的条件匹配的事件时，它将被执行。

1.  Google 会根据工作负载自动扩展实例的数量。

1.  每当您更新函数的代码或删除函数时，Google 都会自动更新或清理实例。

让我们现在使用实时数据库提供程序创建一个简单的云函数并部署它。

# 设置 Firebase SDK 以用于云函数

在继续初始化云函数之前，必须安装 Firebase CLI。如果尚未安装，可以按照下一节中的说明安装 Firebase CLI。

# Firebase CLI

我们已经在[*第五章*]（5697f854-7bc1-4ffb-86a2-8304d0fc73e7.xhtml）*用户配置文件和访问管理*中看到了如何安装它，但这里是命令，仅供参考：

```jsx
npm install -g firebase-tools
```

一旦我们安装了 Firebase CLI，我们将使用以下命令登录到 firebase 控制台：

```jsx
firebase login
```

这个命令将打开一个浏览器 URL，并要求你登录。成功登录后，你可以进行下一步——初始化 Firebase 项目。

# 初始化 Firebase 云项目

让我们创建一个名为 cloud-functions 的空项目目录。我们将从新创建的 cloud-functions 目录中运行以下命令来初始化云函数：

```jsx
firebase init functions
```

这个命令将引导你通过一个包含不同步骤的向导，并将为你的项目创建必要的文件。它会询问你喜欢的语言：Javascript 还是 TypeScript。我们将选择 Typescript 作为示例。它还会问你是否想要关联任何现有的 firebase 项目或者想要创建一个新项目来关联。我们将选择一个现有的项目。它还会问你是否要安装所需的 node 依赖。我们会选择是，这样它就会安装所有必要的 node 包。如果你想自己管理依赖，可以选择否。以下截图显示了向导的外观：

![](img/e455db7d-42ab-45ad-895e-b49c8e8aea67.png)

最终的结构将如下所示：

![](img/a16ae49d-f10b-44ef-bc87-e8075278941f.png)

让我们了解一些特定于云函数的文件：

1.  `firebase.json`：它包含了项目的属性。它包含一个名为"source"的属性，指向`functions`文件夹，用于存放云函数代码。如果你想指向其他文件夹，可以在这里更改。它还包括一个名为"predeploy"的属性，基本上包含了构建和运行代码的命令。

1.  `.firebaserc`：它包含了与该目录相关联的项目。它可以帮助你快速切换项目。

1.  `functions/src/index.ts`：这是主要的源文件，所有的云函数代码都将放在这里。默认情况下，这个文件中已经有一个名为`helloworld`的函数。但是，默认情况下它是被注释掉的。

1.  `functions/package.json`：包含了该项目的 NPM 依赖。

如果你是 Windows 用户，你可能需要将`firebase.json`文件中的"predeploy"属性的值从"`npm` --prefix `$RESOURCE_DIR` run build"改为"`npm` --prefix `%RESOURCE_DIR%` run build"，因为有时在尝试部署函数时会出现错误。

设置完成后，我们就可以部署我们的第一个云函数了。对于这个示例，我们将编写一个简单的函数调用 `greetUser`，它接受 `request` 参数中的用户名称，并在响应中显示问候消息：

```jsx
import * as functions from 'firebase-functions';

export const greetUser = functions.https.onRequest((request, response) => {
        const name = request.query.name;
        response.send("Welcome to Firebase Cloud Function, "+name+"!");
});
```

首先，我们需要导入 f*irebase-functions *来使我们的函数工作。还要注意，云函数是通过调用 `functions.https` 来实现的，这基本上意味着我们正在使用 HTTP 触发器。

`greetUser()` 云函数是一个 HTTP 端点。如果您了解 ExpressJS 编程，您一定注意到语法类似于 ExpressJS 端点，当用户点击端点时，它执行一个带有请求和响应对象的函数。实际上，HTTP 函数的事件处理程序监听 `onRequest()` 事件，支持由 Express web 框架管理的路由器和应用程序。响应对象用于向用户发送响应，在我们的情况下是文本消息，用户将在浏览器中看到。

# 部署和执行云函数

我们需要使用以下命令来部署我们的 `helloworld` 云函数：

```jsx
firebase deploy --only functions
```

这个命令将部署我们的函数，您应该在命令提示符中看到以下响应：

![](img/11742558-0610-45d0-9c6e-ea55e5cf23a2.png)

如果部署成功完成，您将看到函数 URL，例如 `https://us-central1-seat-booking.cloudfunctions.net/greetUser`，现在可以用来触发云函数的执行。

函数 URL 包括以下内容：

+   `us-central1`：这是您的函数部署的区域

+   `seat-booking`：这是 Firebase 项目 ID

+   `cloudfunction.net`：这是默认域

+   `greetUser`：这是部署的函数名称

我们需要将名称属性作为请求参数附加以查看在问候消息中看到的名称。

当您从浏览器中点击该 URL 时，您应该看到以下输出：

![](img/06c84a54-7799-4579-ac0d-8ded26f83360.png)

所以我们成功创建了一个云函数，耶！

大多数开发人员希望在将函数部署到生产或测试环境之前对其进行单元测试。您可以使用以下命令在本地部署和测试您的函数：

```jsx
firebase serve --only functions
```

它将启动一个本地服务器，并显示一个 URL，您可以点击以测试您的函数：

![](img/7f9cb413-d1ac-413e-a1e9-02cf5f0856a9.png)

在这个例子中，我们看到了如何通过 `functions.https` 使用 HTTP 请求触发函数。现在让我们探索所有触发函数。

# 触发函数

Cloud Functions 可以响应其他 Firebase 产品生成的事件而执行。这些事件本质上是 Cloud Functions 的触发器。我们已经在关键特性部分看到了所有触发器的列表。我们将讨论与本书最相关的实时数据库触发器、身份验证触发器、云存储触发器和云 Firestore 触发器。其余三个可以在 Firebase 文档中进行探索：[`firebase.google.com/docs/functions/`](https://firebase.google.com/docs/functions/)。

# 实时数据库触发器

我们可以创建 Cloud Functions，以响应实时数据库更改来执行某些任务。我们可以使用 `functions.database`* 创建一个新的实时数据库事件函数。*为了指定函数何时执行，我们需要使用可用于处理不同数据库事件的事件处理程序之一。我们还需要指定函数将监听事件的数据库路径。

这里列出了 Cloud Functions 支持的事件：

+   `onWrite()`：当实时数据库中的数据被创建、销毁或更改时触发

+   `onCreate()`：当实时数据库中创建新数据时触发

onUpdate()：当实时数据库中的数据更新时触发

onDelete()：当实时数据库中的数据被删除时触发

我们可以看到示例实时数据库函数，它监听数据库中的用户路径，每当任何用户的数据发生变化时，将其名称转换为大写并将其设置为用户数据库的同级。在这里，我们使用了通配符 `{userId}`，它实质上表示任何 `userId`：

```jsx
import * as functions from 'firebase-functions';
import * as admin from 'firebase-admin';

admin.initializeApp(functions.config().firebase);

export const makeUppercase = functions.database.ref('/users/{userId}')
    .onWrite(event => {
        // Grab the current value of what was written to the Realtime 
           Database.
        const original = event.data.val();
        console.log('Uppercasing', original);
        //status is a property
        const uppercase = original.name.toUpperCase();
        // You must return a Promise when performing asynchronous tasks 
           inside a Functions such as
        // writing to the Firebase Realtime Database.
        // Setting an "uppercase" sibling in the Realtime Database 
           returns a Promise.
        return event.data.ref.parent.child('uppercase').set(uppercase);
});
```

在这里，`event.data` 是一个 DeltaSnapshot。它有一个名为 'previous' 的属性，让您可以检查事件发生前保存在数据库中的内容。previous 属性返回一个新的 DeltaSnapshot，其中所有方法都指向先前的值。

# 身份验证触发器

使用身份验证触发器，我们可以在 Firebase 身份验证创建和删除用户时执行函数代码。

要创建一个在创建新用户时执行的 Cloud 函数，我们可以使用以下代码：

```jsx
exports.userCreated = functions.auth.user().onCreate(event => { ... });
```

根据 Firebase 文档，Cloud Functions 的用户创建事件发生在以下情况下：

+   开发人员使用 Firebase Admin SDK 创建帐户

+   用户创建电子邮件帐户和密码

+   用户首次使用联合身份提供者登录

+   用户首次匿名身份验证登录

当用户首次使用自定义令牌登录时，不会触发 Cloud Functions 事件。如果您想要访问新创建用户的属性，可以使用`event.data`对象进行访问。

例如，您可以按以下方式获取用户的电子邮件和姓名：

```jsx
const user = event.data; 

const email = user.email;
const name = user.displayName;
```

除了用户创建，如果您想要在用户删除时触发函数，可以使用`onDelete()`事件处理程序进行操作：

```jsx
exports.deleteUser = functions.auth.user().onDelete(event => {
  // ...
});
```

# Cloud Storage 触发器

使用 Cloud Storage 触发器，您可以对 Cloud Storage 中的文件和文件夹的创建、更新或删除操作执行 Firebase Cloud Function。我们可以使用*functions.storage.*为 Cloud Storage 事件创建一个新的函数。根据需求，您可以创建一个监听默认存储桶上所有更改的函数，或者可以通过指定存储桶名称来限制它：

```jsx
functions.storage.object() - listen for object changes on the default storage bucket.
functions.storage.bucket('test').object() - listen for object changes on a specific bucket called 'test'
```

例如，我们可以编写一个将上传的文件压缩以减小大小的函数：

```jsx
exports.compressFiles = functions.storage.object().onChange(event => {
  // ...
});
```

*change*事件在对象创建、修改或删除时触发。

Cloud Storage 函数公开了以下属性，可用于进一步处理文件：

+   `event.data`：表示存储对象。

+   `event.data.bucket`：文件存储的存储桶。

+   `event.data.name`：存储桶中的文件路径。

+   `event.data.contentType`：文件内容类型。

+   `event.data.resourceState`：两个可能的值：`exists`或`not_exists`。如果文件/文件夹已被删除，则设置为`not_exists`。

+   `event.data.metageneration`：文件元数据生成的次数；对于新对象，初始值为`1`。

Firebase Cloud Function 的最常见用例是进一步处理文件，例如压缩文件或生成图像文件的缩略图。

# HTTP 触发器

我们已经看到了一个名为`greetUser()`的 HTTP 端点的示例，它涵盖了大部分 HTTP 端点的基本部分。只有一个重要的要点需要注意，我们应该始终正确终止我们的函数；否则，它们可能会继续运行，系统将强制终止它。我们可以使用`send()`、`redirect()`或`end()`来结束我们的函数。

考虑以下示例：

```jsx
response.send("Welcome to the Cloud Function");
```

此外，如果您正在使用 Firebase 托管，并希望将您的 HTTP 端点与某个自定义域连接起来，您也可以这样做。

# Cloud Firestore 触发器

使用 Cloud Firestore 触发器，您的云函数可以监听 Cloud Firestore 发出的事件，每当指定路径上的数据发生更改时，您的函数就会监听到。

在高层次上，它的工作方式类似于实时数据库触发器。您可以使用`functions.firestore`对象来监听特定事件。

它支持四个事件：创建、更新、删除和写入，如下所列：

+   `onWrite()`: 当任何文档被创建、更新或删除时触发

+   `onCreate()`: 当创建新文档时触发

+   `onUpdate()`: 当现有文档中的任何值更改时触发

+   `onDelete()`: 当文档被删除时触发

如果您想在特定文档更改时执行函数，可以编写如下函数：

```jsx
exports.updateUser = functions.firestore
  .document('users/{userID}')
  .onWrite(event => {
    // An object with the current document value
    var document = event.data.data();

    // An object with the previous document value (for update or 
       delete)
    var oldDocument = event.data.previous.data();

    // perform desited database operations ...
});
```

现在我们将讨论云函数终止。

# 函数终止

云函数的优势在于您无需自行采购任何服务器，因此您只需要支付云函数运行的时间。这也给了您一个责任，即正确终止您的函数，不要让您的函数陷入无限循环，否则将在您的账单中产生额外费用。

为了正确终止函数并管理其生命周期，您可以遵循以下推荐方法：

1.  通过返回 JavaScript promise 解决异步处理函数

1.  使用`res.redirect()`、`res.send()`或`res.end()`结束 HTTP 函数

1.  使用`return;`语句结束同步函数

# 总结

在本章中，我们讨论了 Firebase 的两个高级功能：Firebase Cloud Messaging 和 Firebase Cloud Functions。使用这两个功能，您可以开发一个高度交互式的无服务器应用程序。

FCM 是一个可靠的消息传递平台，用于下行和上行消息的可靠传递。我们还讨论了不同的消息类型，并看到何时使用其中一种。为了在 FCM 上有实际经验，我们增强了我们的 Helpdesk 应用程序以发送和接收通知。

我们还谈到了 Firebase 云函数，看到它如何帮助实现无服务器应用。我们介绍了如何开发云函数并将其部署到服务器上。我们还探讨了不同类型的触发器，如实时数据库触发器、HTTP 触发器、Cloud Firestore 触发器、Cloud Storage 触发器和 Auth 触发器。

在下一章中，我们将涵盖其他高级和有趣的功能，比如 Firebase 云存储和将 Firebase 应用与 Google Cloud 集成。
