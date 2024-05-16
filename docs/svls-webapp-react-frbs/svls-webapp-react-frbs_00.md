# 前言

实时应用程序多年来一直主导着 Web 应用程序领域。实时不仅仅限于在数据可用时立即显示数据；当与交互式体验一起使用时，它展现出真正的力量，用户和系统可以立即相互通信。借助虚拟 DOM 和声明性视图等功能，React 被证明更适合这样的实时应用程序。Firebase 通过让您专注于应用程序的行为和外观，而不会陷入实时开发的更繁琐的部分，使构建和快速原型设计这种应用程序变得更简单。

本书将涵盖 Firebase 的功能，如云存储、云功能、托管和实时数据库集成 React，以开发丰富、协作、实时的应用程序，仅使用客户端代码。我们还可以看到如何使用 Firebase 身份验证和数据库安全规则来保护我们的应用程序。我们还利用 Redux 的力量来组织前端的数据。Redux 试图通过对状态变化施加一定的限制，使状态变化可预测。在本书的最后，您将通过认识 Firebase 的潜力来提高您的 React 技能，从而创建实时无服务器 Web 应用程序。

本书提供的是更多实用的见解，而不仅仅是理论概念，并包括从 hello world 到实时 Web 应用程序的基础到高级示例。

# 本书的受众

本书的理念是帮助开发人员使用 React 和 Firebase 更快地创建实时无服务器应用程序。我们为那些想要使用 Firebase 验证业务理念创建最小可行产品（MVP）的开发人员编写了本书。本书旨在为那些具有 HTML、CSS、React 和 JavaScript 基础到中级知识的开发人员提供实用知识，并希望了解更多关于 React、Redux 和 Firebase 集成的内容。本书还面向那些不想浪费时间搜索数百个 React、Redux 和 Firebase 教程的开发人员，并希望在一个地方拥有真实示例，快速提高生产力。本书适合任何对学习 Firebase 感兴趣的人。

最后，如果您想开发无服务器应用程序，并想了解从设计到托管的端到端过程，并获得逐步说明，那么本书适合您。

# 要充分利用本书

您应该具有 React、HTML、CSS 和 JavaScript 的基本编程经验，才能有利于阅读本书。假定您已经知道**Node Package Manager**（**npm**）如何工作以安装任何依赖项，并且对 ES6 语法有基本了解。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了本书，可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便文件直接发送到您的邮箱。

您可以按照以下步骤下载代码文件：

1.  在[www.packtpub.com](http://www.packtpub.com/support)上登录或注册。

1.  选择“SUPPORT”选项卡。

1.  单击“代码下载和勘误”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

下载文件后，请确保使用最新版本的解压软件解压文件夹：

+   WinRAR/7-Zip 适用于 Windows

+   Zipeg/iZip/UnRarX 适用于 Mac

+   7-Zip/PeaZip 适用于 Linux

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Serverless-Web-Applications-with-React-and-Firebase`](https://github.com/PacktPublishing/Serverless-Web-Applications-with-React-and-Firebase)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有来自我们丰富的书籍和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。去看看吧！

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。这是一个例子："将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。"

代码块设置如下：

```jsx
constructor(props) {
 super(props);
 this.state = {
 value: props.initialValue
 };
 }
```

任何命令行输入或输出都以以下形式书写：

```jsx
node -v
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词会以这种形式出现在文本中。这是一个例子："从管理面板中选择系统信息。"

警告或重要说明会出现在这样的形式中。提示和技巧会出现在这样的形式中。

# 联系我们

我们随时欢迎读者的反馈。

**一般反馈**：发送电子邮件至`feedback@packtpub.com`，并在主题中提及书名。如果您对本书的任何方面有疑问，请通过`questions@packtpub.com`与我们联系。

**勘误表**：尽管我们已经尽最大努力确保内容的准确性，但错误是难免的。如果您在本书中发现了错误，我们将不胜感激地接受您的报告。请访问[www.packtpub.com/submit-errata](http://www.packtpub.com/submit-errata)，选择您的书籍，点击“勘误提交表格”链接，并输入详细信息。

**盗版**：如果您在互联网上发现我们作品的任何非法副本，请向我们提供位置地址或网站名称，我们将不胜感激。请通过`copyright@packtpub.com`与我们联系，并附上材料链接。

如果您有兴趣成为作者：如果您在某个专业领域有专长，并且有兴趣撰写或为一本书做出贡献，请访问[authors.packtpub.com](http://authors.packtpub.com/)。

# 评论

请留下评论。阅读并使用本书后，为什么不在购买它的网站上留下评论呢？潜在读者可以看到并使用您的客观意见来做出购买决定，我们在 Packt 可以了解您对我们产品的看法，我们的作者也可以看到您对他们书籍的反馈。谢谢！

有关 Packt 的更多信息，请访问[packtpub.com](https://www.packtpub.com/)。
