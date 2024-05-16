# 前言

框架和库来来去去。设计模式通常会持续更长时间。在这本书中，我们将学习 React Native 和与该生态系统相关的设计模式。当涉及到 React 时，关于设计模式的基本知识散布在各个地方。有时它被埋在专有的代码库中。这本书将把它带给你。我称它们为**思想模式：**通过真实的工作示例来解释的实用设计模式。在这本书中，我们使用 React Native，但你也可以成功地在 React 的 Web 开发中使用大多数这些模式，甚至是其他框架，比如 Angular 或 Vue。希望你能利用这些知识来构建深思熟虑且易于维护的代码库。祝你好运！

# 这本书适合谁

业余程序员和热情的人非常欢迎阅读这本书，但请注意，这可能比初级编程书籍更具挑战性。

我假设你有一些 JavaScript 编程经验，并且对终端窗口并不陌生。理想情况下，你应该是一名开发人员（初级/中级/高级），这样你就会有广阔的视野，并且可以立即将知识应用到你的工作中。不需要有开发移动应用程序的经验。

# 为了充分利用这本书，

花点时间，不要着急。你不需要在一周内读完这本书。

随着你的开发者职业的进步，回到这本书。你将专注于完全不同的事情，这样你将能够充分利用这本书。

玩一下我准备的例子。每个都是一个独立的应用程序，所以你可以在我们进行的过程中玩耍和改进代码。这旨在作为一个游乐场，这样你不仅可以从例子中学习，还可以创建它们的扩展。当你构建时，你将理解在每个部分引入的变化。如果你只是读这本书，你肯定会错过这个视角。

# 下载示例代码文件

你可以从[www.packt.com](http://www.packt.com)的账户中下载这本书的示例代码文件。如果你在其他地方购买了这本书，你可以访问[www.packt.com/support](http://www.packt.com/support)并注册，文件将直接发送到你的邮箱。

你可以按照以下步骤下载代码文件：

1.  在[www.packt.com](http://www.packt.com)上登录或注册。

1.  选择“支持”选项卡。

1.  点击“代码下载和勘误”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

文件下载后，请确保使用最新版本的解压缩或提取文件夹：

+   WinRAR/7-Zip 适用于 Windows

+   Zipeg/iZip/UnRarX 适用于 Mac

+   7-Zip/PeaZip 适用于 Linux

该书的代码包也托管在 GitHub 上，网址为[`github.com/Ajdija/hands-on-design-patterns-with-react-native`](https://github.com/Ajdija/hands-on-design-patterns-with-react-native)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有来自丰富图书和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。去看看吧！

# 下载彩色图片

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图片。您可以在这里下载：`www.packtpub.com/sites/default/files/downloads/9781788994460_ColorImages.pdf`。

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。例如："将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。"

代码块设置如下：

```jsx
export default function() {
    return React.createElement(
        Text,
  {style: {marginTop: 30}},
  'Example Text!'
  ); }
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目将以粗体显示：

```jsx
export default **function** App() {
  return (
      <View style={styles.container}>
  ...
      </View>
  ); }
```

任何命令行输入或输出都将按以下方式编写：

```jsx
yarn test -- --coverage
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词会以这种方式出现在文本中。例如："您现在可以点击 详细 按钮导航到 任务详情 屏幕。"

警告或重要说明会以这种方式出现。提示和技巧会以这种方式出现。
