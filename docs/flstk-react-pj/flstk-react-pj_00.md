# 前言

本书探讨了通过将 React 的力量与经过行业测试的服务器端技术（如 Node、Express 和 MongoDB）相结合，开发全栈 JavaScript Web 应用的潜力。JavaScript 领域已经快速增长了一段时间。在这个主题上有大量的选择和资源可用，当你需要从这些经常变化的部分中进行选择、了解它们并使它们一起工作来构建自己的 Web 应用时，很容易迷失。为了解决这一痛点，本书采用了一种实用的方法，帮助您设置和构建使用这种流行的 JavaScript 堆栈的各种工作应用程序。

# 本书适合的读者

本书面向有一些 React 经验但没有涉及 Node、Express 和 MongoDB 的全栈开发经验的 JavaScript 开发人员，他们希望获得实用的指南，以开始使用这种堆栈构建不同类型的 Web 应用程序。

# 本书涵盖的内容

第一章，《使用 MERN 释放 React 应用程序》，介绍了 MERN 堆栈技术和本书中开发的应用程序。我们将讨论使用 React、Node、Express 和 MongoDB 开发 Web 应用程序的背景和相关性。

第二章，《准备开发环境》，帮助设置 MERN 堆栈技术以进行开发。我们将探索必要的开发工具，安装 Node、MongoDB、Express、React 和其他所需的库，然后运行代码来检查设置。

第三章，《使用 MongoDB、Express 和 Node 构建后端》，实现了一个骨架 MERN 应用的后端。我们将构建一个独立的服务器端应用程序，其中包括 MongoDB、Express 和 Node，用于存储用户详细信息，并具有用于用户身份验证和 CRUD 操作的 API。

第四章，《添加 React 前端以完成 MERN》，通过集成 React 前端完成了 MERN 骨架应用程序。我们将使用 React 视图实现一个可与服务器上的用户 CRUD 和 auth API 进行交互的工作前端。

第五章，“从简单的社交媒体应用开始”，通过扩展骨架应用程序构建了一个社交媒体应用程序。我们将通过实现社交媒体功能来探索 MERN 堆栈的能力，例如帖子分享、点赞和评论；关注朋友；以及聚合新闻源。

第六章，“通过在线市场锻炼新的 MERN 技能”，在在线市场应用程序中实现了基本功能。我们将实现与买卖相关的功能，支持卖家账户、产品列表和按类别搜索产品。

第七章，“扩展订单和支付的市场”，进一步构建了市场应用程序，包括购物车、订单管理和支付处理。我们将添加购物车功能，允许用户使用购物车中的商品下订单。我们还将集成 Stripe 以收集和处理付款。

第八章，“构建媒体流应用程序”，使用 MongoDB GridFS 实现媒体上传和流媒体。我们将开始构建一个基本的媒体流应用程序，允许注册用户上传视频文件，这些文件将存储在 MongoDB 上并流回，以便观众可以在简单的 React 媒体播放器中播放每个视频。

第九章，“定制媒体播放器和改善 SEO”，通过定制媒体播放器和自动播放媒体列表来升级媒体查看功能。我们将在默认的 React 媒体播放器上实现定制控件，添加可以自动播放的播放列表，并通过为媒体详细信息添加有选择的服务器端渲染和数据来改善 SEO。

第十章，“开发基于 Web 的 VR 游戏”，使用 React 360 开发了一个用于 Web 的 3D 虚拟现实游戏。我们将探索 React 360 的 3D 和 VR 功能，并构建一个简单的基于 Web 的 VR 游戏。

第十一章，*使用 MERN 使 VR 游戏动态化*，通过扩展 MERN 骨架应用程序并集成 React 360，构建了一个动态的 VR 游戏应用程序。我们将实现一个游戏数据模型，允许用户创建自己的 VR 游戏，并将动态游戏数据与使用 React 360 开发的游戏相结合。

第十二章，*遵循最佳实践并进一步开发 MERN*，反思了前几章的教训，并提出了进一步基于 MERN 的应用程序开发的改进建议。我们将扩展一些已经应用的最佳实践，比如应用程序结构中的模块化，其他应该应用的实践，比如编写测试代码，以及可能的改进，比如优化捆绑大小。

# 为了充分利用本书

本书的内容组织假定您熟悉基本的基于 Web 的技术，了解 JavaScript 中的编程构造，并对 React 应用程序的工作原理有一般了解。在阅读本书时，您将了解这些概念在使用 React、Node、Express 和 MongoDB 构建完整的 Web 应用程序时是如何结合在一起的。

为了在阅读各章节时最大限度地提高学习体验，建议您并行运行相关应用程序代码的关联版本，并使用每章提供的相关说明。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便文件直接通过电子邮件发送给您。

您可以按照以下步骤下载代码文件：

1.  登录或注册[www.packtpub.com](http://www.packtpub.com/support)。

1.  选择“支持”选项卡。

1.  单击“代码下载和勘误”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

下载文件后，请确保使用以下最新版本的解压缩或提取文件夹：

+   Windows 上的 WinRAR/7-Zip

+   Mac 上的 Zipeg/iZip/UnRarX

+   Linux 上的 7-Zip/PeaZip

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Full-Stack-React-Projects`](https://github.com/PacktPublishing/Full-Stack-React-Projects)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有来自我们丰富书籍和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。去看看吧！

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。例如："将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。"

代码块设置如下：

```jsx
import path from 'path'
const CURRENT_WORKING_DIR = process.cwd()
app.use('/dist', express.static(path.join(CURRENT_WORKING_DIR, 'dist')))
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项将以粗体显示：

```jsx
{
    "presets": [
      "env",
      "stage-2",
      "react"
    ],
    "plugins": [
 "react-hot-loader/babel"
 ]
}
```

任何命令行输入或输出都将按照以下方式书写：

```jsx
npm install babel-preset-react --save-dev
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词会在文本中以这种方式出现。例如："从管理面板中选择系统信息。"

警告或重要说明会以这种方式出现。提示和技巧会以这种方式出现。
