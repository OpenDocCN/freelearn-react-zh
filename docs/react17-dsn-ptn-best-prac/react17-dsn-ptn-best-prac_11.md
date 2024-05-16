# 第八章：使您的组件看起来漂亮

我们的 React 最佳实践和设计模式之旅现在已经达到了我们想要让组件看起来漂亮的地步。为了做到这一点，我们将详细介绍为什么常规 CSS 可能不是样式化组件的最佳方法的所有原因，并且我们将了解各种替代解决方案。

从内联样式开始，然后是 Radium、CSS 模块和`styled-components`，本章将指导您进入 JavaScript 中 CSS 的神奇世界。

在本章中，我们将涵盖以下主题：

+   规模上常见的常规 CSS 问题

+   在 React 中使用内联样式及其缺点

+   Radium 库如何帮助解决内联样式的问题

+   如何使用 Webpack 和 CSS 模块从头开始设置项目

+   CSS 模块的特性以及它们为什么是避免全局 CSS 的绝佳解决方案

+   `styled-components`，一种为 React 组件提供现代样式的新库

# 技术要求

要完成本章，您将需要以下内容：

+   Node.js 12+

+   Visual Studio Code

您可以在书籍的 GitHub 存储库中找到本章的代码：[`github.com/PacktPublishing/React-17-Design-Patterns-and-Best-Practices-Third-Edition/tree/main/Chapter08`](https://github.com/PacktPublishing/React-17-Design-Patterns-and-Best-Practices-Third-Edition/tree/main/Chapter08)。

# JavaScript 中的 CSS

在社区中，每个人都同意在 2014 年 11 月，React 组件的样式发生了革命，当时 Christopher Chedea 在 NationJS 会议上发表了演讲。

在互联网上也被称为**vjeux**，Christopher 在 Facebook 工作并为 React 做出贡献。在他的演讲中，他详细介绍了他们在 Facebook 面临的所有与 CSS 相关的问题。值得理解所有这些问题，因为其中一些问题非常普遍，它们将帮助我们引入内联样式和本地作用域类名等概念。

以下是 CSS 存在的问题清单，基本上是在规模上出现的问题：

+   全局命名空间

+   依赖关系

+   死代码消除

+   最小化

+   共享常量

+   非确定性解决方案

+   隔离

CSS 的第一个众所周知的问题是所有选择器都是全局的。无论我们如何组织我们的样式，使用命名空间或诸如**块**，**元素**，**修饰符**（**BEM**）方法之类的过程，最终我们总是在污染全局命名空间，我们都知道这是错误的。这不仅在原则上是错误的，而且在大型代码库中会导致许多错误，并且在长期内使可维护性非常困难。与大团队合作，要知道特定类或元素是否已经被样式化是非平凡的，大多数情况下，我们倾向于添加更多类而不是重用现有类。

CSS 的第二个问题涉及依赖关系的定义。事实上，很难清楚地说明特定组件依赖于特定的 CSS，并且必须加载 CSS 才能应用样式。由于样式是全局的，任何文件中的任何样式都可以应用于任何元素，失去控制非常容易。

第三个问题是前端开发人员倾向于使用预处理器来将他们的 CSS 拆分成子模块，但最终，会为浏览器生成一个大的全局 CSS 捆绑包。由于 CSS 代码库很快变得庞大，我们失去了对它们的控制，第三个问题与死代码消除有关。很难迅速确定哪些样式属于哪个组件，这使得删除代码非常困难。事实上，由于 CSS 的级联特性，删除选择器或规则可能会导致浏览器中出现意外结果。

与 CSS 工作的另一个痛点涉及选择器和类名在 CSS 和 JavaScript 应用程序中的缩小。这似乎是一项简单的任务，但实际上并非如此，特别是当类在客户端上应用或连接时；这是第四个问题。

无法缩小和优化类名对性能来说非常糟糕，并且它可能会对 CSS 的大小产生巨大影响。另一个常见的非平凡操作是在样式和客户端应用程序之间共享常量。我们经常需要知道标题的高度，例如，以便重新计算依赖于它的其他元素的位置。

通常，我们使用 JavaScript API 在客户端读取值，但最佳解决方案是共享常量并避免在运行时进行昂贵的计算。这代表了 vjeux 和 Facebook 的其他开发人员试图解决的第五个问题。

第六个问题涉及 CSS 的非确定性解析。实际上，在 CSS 中，顺序很重要，如果 CSS 按需加载，顺序就无法保证，这会导致错误的样式应用于元素。

例如，假设我们想优化请求 CSS 的方式，只有在用户导航到特定页面时才加载与该页面相关的 CSS。如果与最后一个页面相关的 CSS 具有一些规则，这些规则也适用于不同页面的元素，那么最后加载它可能会影响应用程序其余部分的样式。例如，如果用户返回到上一个页面，他们可能会看到一个 UI 略有不同于他们第一次访问时的页面。

控制各种样式、规则和导航路径的各种组合非常困难，但是，能够在需要时加载 CSS 可能会对 Web 应用程序的性能产生关键影响。

最后但同样重要的是，根据 Christopher Chedeau 的说法，CSS 的第七个问题与隔离有关。在 CSS 中，几乎不可能在文件或组件之间实现适当的隔离。选择器是全局的，很容易被覆盖。仅仅通过知道应用于元素的类名就很难预测元素的最终样式，因为样式不是隔离的，应用程序其他部分的其他规则可能会影响不相关的元素。这可以通过使用内联样式来解决。

在接下来的部分中，我们将看看在 React 中使用内联样式意味着什么，以及其优缺点。

# 理解并实现内联样式

官方的 React 文档建议开发人员使用内联样式来为他们的 React 组件设置样式。这似乎有点奇怪，因为多年来我们都学到了分离关注点很重要，我们不应该混合标记和 CSS。

React 试图通过将关注点的概念从技术的分离转移到组件的分离来改变。当标记、样式和逻辑紧密耦合且一个不能没有另一个而无法工作时，将它们分离到不同的文件中只是一种幻觉。即使它有助于保持项目结构更清洁，但它并没有提供任何真正的好处。

在 React 中，我们组合组件来创建应用程序，其中组件是我们结构的基本单位。我们应该能够在应用程序中移动组件，并且无论它们被渲染在哪里，它们都应该提供相同的逻辑和 UI 结果。

这是为什么在 React 中将样式与组件放在一起，并使用内联样式在元素上应用它们可能是有意义的原因之一。

首先，让我们看一个例子，看看在 React 中使用节点的样式属性来为我们的组件应用样式意味着什么。我们将创建一个带有文本 `Click me!` 的按钮，并为其应用颜色和背景颜色：

```jsx
const style = { 
  color: 'palevioletred', 
  backgroundColor: 'papayawhip'
};

const Button = () => <button style={style}>Click me!</button>;
```

正如你所看到的，使用内联样式在 React 中很容易为元素设置样式。我们只需要创建一个对象，其中属性是 CSS 规则，值是我们在常规 CSS 文件中使用的值。

唯一的区别是，连字符的 CSS 规则必须转换为驼峰命名以符合 JavaScript 的规范，并且值是字符串，因此它们必须用引号括起来。

关于供应商前缀有一些例外情况。例如，如果我们想在 `webkit` 上定义一个过渡，我们应该使用 `WebkitTransition` 属性，其中 `webkit` 前缀以大写字母开头。这条规则适用于所有供应商前缀，除了 `ms`，它是小写的。

其他用例是数字 - 它们可以不用引号或单位来编写，并且默认情况下被视为像素。

以下规则适用于 `100` 像素的高度：

```jsx
const style = { 
  height: 100
}
```

通过使用内联样式，我们还可以做一些难以用常规 CSS 实现的事情。例如，我们可以在客户端动态重新计算一些 CSS 值，这是一个非常强大的概念，正如你将在下面的例子中看到的。

假设你想创建一个表单字段，其字体大小根据其值改变。因此，如果字段的值为`24`，字体大小将为 24 像素。使用普通的 CSS，这种行为几乎不可能在不付出巨大努力和重复代码的情况下复制。

让我们看看使用内联样式有多容易，首先创建一个`FontSize`组件，然后声明一个值状态：

```jsx
import { useState, ChangeEvent } from 'react'

const FontSize = () => {
  const [value, setValue] = useState<number>(16)
}

export default FontSize
```

我们实现了一个简单的变更处理程序，其中我们使用事件的目标属性来检索字段的当前值：

```jsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => { 
  setValue(Number(e.target.value))
}
```

最后，我们渲染`number`类型的输入文件，这是一个受控组件，因为我们通过使用状态来保持其值更新。它还有一个事件处理程序，每当字段的值改变时就会触发。

最后但并非最不重要的是，我们使用字段的样式属性来设置其`font-size`值。正如你所看到的，我们使用了 CSS 规则的驼峰命名版本，以遵循 React 的约定：

```jsx
return ( 
  <input 
    type="number" 
    value={value} 
    onChange={handleChange} 
    style={{ fontSize: value }} 
  /> 
)
```

渲染前面的组件，我们可以看到一个输入字段，它根据其值更改其字体大小。它的工作方式是，当值改变时，我们将字段的新值存储在状态中。修改状态会强制组件重新渲染，我们使用新的状态值来设置字段的显示值和字体大小；这很简单又很强大。

计算机科学中的每个解决方案都有其缺点，并且总是代表一种权衡。在内联样式的情况下，不幸的是，问题很多。

例如，使用内联样式时，不可能使用伪选择器（例如`:hover`）和伪元素，如果你正在创建具有交互和动画的 UI，这是一个相当重要的限制。

有一些变通方法，例如，你总是可以创建真实的元素而不是伪元素，但对于伪类，需要使用 JavaScript 来模拟 CSS 行为，这并不理想。

同样适用于**媒体查询**，无法使用内联样式来定义，这使得创建响应式 Web 应用程序变得更加困难。由于样式是使用 JavaScript 对象声明的，也不可能使用样式回退：

```jsx
display: -webkit-flex; 
display: flex;
```

JavaScript 对象不能具有相同名称的两个属性。应该避免使用样式回退，但如果需要，总是可以使用它们。

CSS 的另一个特性是**动画**，这是无法使用内联样式来模拟的。在这里的解决方法是全局定义动画，并在元素的 style 属性中使用它们。使用内联样式时，每当我们需要用常规 CSS 覆盖样式时，我们总是被迫使用`!important`关键字，这是一种不好的做法，因为它会阻止任何其他样式被应用到元素上。

使用内联样式最困难的事情是调试。我们倾向于使用类名在浏览器的开发工具中查找元素进行调试，并检查应用了哪些样式。使用内联样式时，所有项目的样式都列在它们的`style`属性中，这使得检查和调试结果非常困难。

例如，我们在本节早些时候创建的按钮以以下方式呈现：

```jsx
<button style="color:palevioletred;background-color:papayawhip;">Click me!</button>
```

单独看起来并不难阅读，但是如果想象一下您有数百个元素和数百种样式，您会意识到问题变得非常复杂。

此外，如果您正在调试一个列表，其中每个项目都具有相同的`style`属性，并且如果您在浏览器中实时修改其中一个以检查结果，您会发现您只将样式应用于该项目，而不是所有其他兄弟项目，即使它们共享相同的样式。

最后但并非最不重要的是，如果我们在服务器端渲染我们的应用程序（我们将在*第九章* *为了乐趣和利润而进行服务器端渲染*中涵盖此主题），那么使用内联样式时页面的大小会更大。

使用内联样式，我们将所有 CSS 内容放入标记中，这会向发送给客户端的文件添加额外的字节数，并使 Web 应用程序显得更慢。压缩算法可以帮助解决这个问题，因为它们可以轻松压缩相似的模式，并且在某些情况下，加载关键路径 CSS 是一个很好的解决方案；但总的来说，我们应该尽量避免使用内联样式。

事实证明，内联样式带来的问题比它们试图解决的问题更多。因此，社区创建了不同的工具来解决内联样式的问题，但同时保持样式在组件内部或局部，以获得两全其美。

在 Christopher Chedeau 的讲话之后，许多开发人员开始谈论内联样式，并进行了许多解决方案和实验，以找到在 JavaScript 中编写 CSS 的新方法。起初，有两三种解决方案，而今天已经有 40 多种。

在接下来的章节中，我们将介绍最受欢迎的解决方案。

# 探索 Radium 库

为了解决我们在前一节中遇到的内联样式问题而创建的最早的库之一是**Radium**。它由 Formidable Labs 的优秀开发人员维护，仍然是最受欢迎的解决方案之一。

在本节中，我们将看看 Radium 是如何工作的，它解决了哪些问题，以及为什么它是与 React 一起用于样式化组件的绝佳库。我们将创建一个非常简单的按钮，类似于本章前面示例中构建的按钮。

我们将从一个没有样式的基本按钮开始，然后添加一些基本样式，以及伪类和媒体查询，以便我们可以了解该库的主要特性。

我们将从以下方式创建按钮开始：

```jsx
const Button = () => <button>Click me!</button>
```

首先，我们必须使用`npm`安装 Radium：

```jsx
npm install --save radium @types/radium
```

安装完成后，我们可以导入库并将按钮包装在其中：

```jsx
import Radium from 'radium'

const Button = () => <button>Click me!</button>

export default Radium(Button)
```

`Radium`函数是一个**高阶组件**（**HOC**）（见*第四章*，*探索所有组合模式*），它扩展了`Button`的功能，返回一个新的增强组件。如果我们在浏览器中渲染按钮，目前不会看到任何特别之处，因为我们没有对其应用任何样式。

让我们从一个简单的样式对象开始，我们在其中设置背景颜色、填充、大小和一些其他 CSS 属性。正如我们在前一节中看到的，React 中的内联样式是使用驼峰式 CSS 属性定义的 JavaScript 对象：

```jsx
const styles = { 
  backgroundColor: '#ff0000', 
  width: 320, 
  padding: 20, 
  borderRadius: 5, 
  border: 'none', 
  outline: 'none'
}
```

前面的片段与 React 中的普通内联样式没有区别，如果我们将其传递给我们的按钮，我们可以在浏览器中看到应用于按钮的所有样式：

```jsx
const Button = () => <button style={styles}>Click me!</button>
```

结果如下标记：

```jsx
<button data-radium="true" style="background-color: rgb(255, 0, 0); width: 320px; padding: 20px; border-radius: 5px; border: none; outline: none;">Click me!</button>
```

您可以在这里看到的唯一区别是元素附加了`data-radium`属性设置为`true`。

现在，我们已经看到内联样式不允许我们定义任何伪类；让我们看看如何使用 Radium 解决这个问题。

使用伪类，比如`：hover`，与 Radium 一起非常简单。我们必须在样式对象内创建一个`:hover`属性，Radium 会完成其余工作：

```jsx
const styles = { 
  backgroundColor: '#ff0000', 
  width: 320, 
  padding: 20, 
  borderRadius: 5, 
  border: 'none', 
  outline: 'none', 
  ':hover': { 
    color: '#fff' 
  } 
}
```

如果您将这个样式对象应用于您的按钮并在屏幕上呈现它，您会看到将鼠标悬停在按钮上会导致按钮变成白色文本，而不是默认的黑色。这太棒了！我们可以同时使用伪类和内联样式。

然而，如果您打开 DevTools 并尝试在`Styles`面板中强制`：hover`状态，您会发现什么也没有发生。您可以看到悬停效果，但无法用 CSS 模拟它的原因是 Radium 使用 JavaScript 来应用和移除`style`对象中定义的悬停状态。

如果您在打开 DevTools 的情况下悬停在元素上，您会看到`style`字符串发生变化，并且颜色会动态添加到其中：

```jsx
<button data-radium="true" style="background-color: rgb(255, 0, 0); width: 320px; padding: 20px; border-radius: 5px; border: none; outline: none; color: rgb(255, 255, 255);">Click me!</button> 
```

Radium 的工作方式是为可以触发伪类行为的每个事件添加事件处理程序并监听它们。

一旦其中一个事件被触发，Radium 会改变组件的状态，这将重新呈现具有正确状态样式的组件。这一开始可能看起来很奇怪，但这种方法没有真正的缺点，而且在性能方面的差异是不可感知的。

我们可以添加新的伪类，例如`:active`，它们也会起作用：

```jsx
const styles = { 
  backgroundColor: '#ff0000', 
  width: 320, 
  padding: 20, 
  borderRadius: 5, 
  border: 'none', 
  outline: 'none', 
  ':hover': { 
    color: '#fff'
  }, 
  ':active': { 
    position: 'relative', 
    top: 2
  } 
}
```

Radium 启用的另一个关键功能是媒体查询。媒体查询对于创建响应式应用程序至关重要，Radium 再次使用 JavaScript 在我们的应用程序中启用了这个 CSS 特性。

让我们看看它是如何工作的 - API 非常相似；我们必须在我们的样式对象上创建一个新属性，并在其中嵌套必须在媒体查询匹配时应用的样式：

```jsx
const styles = { 
  backgroundColor: '#ff0000', 
  width: 320, 
  padding: 20, 
  borderRadius: 5, 
  border: 'none', 
  outline: 'none', 
  ':hover': { 
    color: '#fff' 
  }, 
  ':active': { 
    position: 'relative', 
    top: 2
  }, 
  '@media (max-width: 480px)': { 
    width: 160 
  } 
}
```

我们必须做一件事才能使媒体查询正常工作，那就是将我们的应用程序包装在 Radium 提供的`StyleRoot`组件中。

为了使媒体查询正常工作，特别是在服务器端渲染中，Radium 将在**文档对象模型**（**DOM**）中注入与媒体查询相关的规则，所有属性都设置为`!important`。

这是为了避免在库弄清匹配查询之前应用于文档的不同样式之间闪烁。通过在`style`元素内实现样式，可以通过让浏览器执行其常规工作来防止这种情况。

因此，想法是导入`Radium.StyleRoot`组件：

```jsx
import Radium from 'radium'
```

然后，我们可以将整个应用程序包装在其中：

```jsx
const App = () => { 
  return ( 
    <Radium.StyleRoot> 
      ... 
    </Radium.StyleRoot> 
  ) 
}
```

因此，如果您打开 DevTools，您会看到 Radium 将以下样式注入到 DOM 中：

```jsx
<style>@media (max-width: 480px) { .rmq-1d8d7428{width: 160px !important;}}</style>
```

`rmq-1d8d7428`类也已自动应用于按钮：

```jsx
<button class="rmq-1d8d7428" data-radium="true" style="background-color: rgb(255, 0, 0); width: 320px; padding: 20px; border-radius: 5px; border: none; outline: none;">Click me!</button>
```

如果您现在调整浏览器窗口大小，您会发现按钮在小屏幕上变小，这是预期的。

在下一节中，我们将学习如何使用 CSS 模块。

# 使用 CSS 模块

如果您觉得内联样式不适合您的项目和团队，但仍希望将样式尽可能靠近组件，那么有一个适合您的解决方案，称为**CSS 模块**。CSS 模块是 CSS 文件，默认情况下所有类名和动画名称都是本地作用域的。让我们看看如何在我们的项目中使用它们；但首先，我们需要配置 Webpack。

## Webpack 5

在深入研究 CSS 模块并了解它们的工作原理之前，重要的是要了解它们是如何创建的以及支持它们的工具。

在*第二章* *清理您的代码*中，我们看到了如何编写 ES6 代码并使用 Babel 及其预设进行转译。随着应用程序的增长，您可能还希望将代码库拆分为模块。

你可以使用 Webpack 或 Browserify 将应用程序分成小模块，需要时可以导入它们，同时为浏览器创建一个大捆绑。这些工具被称为**模块捆绑器**，它们的作用是将应用程序的所有依赖项加载到一个可以在浏览器中执行的单个捆绑中，浏览器本身没有任何模块的概念（尚未）。

在 React 世界中，Webpack 特别受欢迎，因为它提供了许多有趣和有用的功能，第一个功能是加载器的概念。使用 Webpack，您可以潜在地加载除 JavaScript 以外的任何依赖项，只要有相应的加载器。例如，您可以在捆绑包中加载 JSON 文件，以及图像和其他资产。

2015 年 5 月，CSS 模块的创作者之一 Mark Dalgleish 发现您也可以在 Webpack 捆绑包中导入 CSS，并推动了这一概念。他认为，由于 CSS 可以在组件中本地导入，所有导入的类名也可以本地作用域，这很棒，因为这将隔离样式。

## 设置项目

在本节中，我们将看看如何设置一个非常简单的 Webpack 应用程序，使用 Babel 来转译 JavaScript 和 CSS 模块以将我们的本地作用域 CSS 加载到捆绑包中。我们还将介绍 CSS 模块的所有特性并看看它们可以解决的问题。首先要做的是移动到一个空文件夹并运行以下命令：

```jsx
npm init
```

这将创建一个`package.json`文件并设置一些默认值。

现在，是时候安装依赖项了，第一个是`webpack`，第二个是`webpack-dev-server`，我们将使用它来在本地运行应用程序并即时创建捆绑包：

```jsx
npm install --save-dev webpack webpack-dev-server webpack-cli
```

一旦安装了 Webpack，就是安装 Babel 及其加载器的时候了。由于我们使用 Webpack 来创建捆绑包，我们将使用 Babel 加载器在 Webpack 内部转译我们的 ES6 代码：

```jsx
npm install --save-dev @babel/core @babel/preset-env @babel/preset-react ts-loader
```

最后，我们安装`style-loader`和 CSS 加载器，这是我们需要启用 CSS 模块的两个加载器：

```jsx
npm install --save-dev style-loader css-loader
```

还有一件事要做，让事情变得更容易，那就是安装`html-webpack-plugin`，这是一个插件，可以通过查看 Webpack 配置来即时创建一个 HTML 页面来托管我们的 JavaScript 应用程序，而无需我们创建一个常规文件。此外，我们需要安装`fork-ts-checker-webpack-plugin`包来使 TypeScript 与 Webpack 一起工作：

```jsx
npm install --save-dev html-webpack-plugin fork-ts-checker-webpack-plugin typescript
```

最后但同样重要的是，我们安装`react`和`react-dom`来在我们的简单示例中使用它们：

```jsx
npm install react react-dom
```

现在所有的依赖都安装好了，是时候配置一切使其工作了。

首先，您需要在根路径下创建一个`.babelrc`文件：

```jsx
{
 "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

首先要做的是在`package.json`中添加一个`npm`脚本来运行`webpack-dev-server`，它将在开发中为应用程序提供服务：

```jsx
"scripts": { 
  "dev": "webpack serve --mode development --port 3000" 
}
```

在 Webpack 5 中，您需要使用这种方式调用`webpack`而不是`webpack-dev-server`，但您仍然需要安装这个包。

Webpack 需要一个配置文件来知道如何处理我们应用程序中使用的不同类型的依赖关系，为此，我们必须创建一个名为`webpack.config.js`的文件，它导出一个对象：

```jsx
module.exports = {}
```

我们导出的对象代表 Webpack 用来创建捆绑包的配置对象，它可以根据项目的大小和特性有不同的属性。

我们希望保持我们的示例非常简单，所以我们将添加三个属性。第一个是`entry`，它告诉 Webpack 我们应用程序的主文件在哪里：

```jsx
entry: './src/index.tsx'
```

第二个是`module`，在那里我们告诉 Webpack 如何加载外部依赖项。它有一个名为`rules`的属性，我们为每种文件类型设置了特定的加载器：

```jsx
module: { 
  rules: [
    {
      test: /\.(tsx|ts)$/,
      exclude: /node_modules/,
      use: {
        loader: 'ts-loader',
        options: {
          transpileOnly: true
        }
      }
    }, 
    { 
      test: /\.css/,
      use: [
        'style-loader',
        'css-loader?modules=true'
      ]
    } 
  ]
}
```

我们说匹配`.ts`或`.tsx`正则表达式的文件将使用`ts-loader`加载，以便它们被转译并加载到捆绑包中。

您可能还注意到我们在`.babelrc`文件中添加了我们的预设。正如我们在*第二章*中看到的*清理您的代码*，预设是一组配置选项，指示 Babel 如何处理不同类型的语法（例如 TSX）。

`rules`数组中的第二个条目告诉 Webpack 在导入 CSS 文件时该怎么做，并且它使用`css-loader`和启用`modules`标志来激活 CSS 模块。转换的结果然后传递给`style-loader`，它将样式注入到页面的头部。

最后，我们启用 HTML 插件来为我们生成页面，自动使用我们之前指定的入口路径添加`script`标签：

```jsx
const HtmlWebpackPlugin = require('html-webpack-plugin')
const ForkTsCheckerWebpackPlugin = require('fork-ts-checker-webpack-plugin')

plugins: [
  new ForkTsCheckerWebpackPlugin(),
 new HtmlWebpackPlugin({
    title: 'Your project name',
    template: './src/index.html',
    filename: './index.html'
  })
]
```

完整的`webpack.config.js`应该如下代码块所示：

```jsx
const HtmlWebpackPlugin = require('html-webpack-plugin')
const path = require('path')
const ForkTsCheckerWebpackPlugin = require('fork-ts-checker-webpack-plugin')

const isProduction = process.env.NODE_ENV === 'production'

module.exports = {
  devtool: !isProduction ? 'source-map' : false, // We generate source maps 
  // only for development
  entry: './src/index.tsx',
  output: { // The path where we want to output our bundles
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash:8].js',
    sourceMapFilename: '[name].[hash:8].map',
    chunkFilename: '[id].[hash:8].js',
    publicPath: '/'
  },
  resolve: {
    extensions: ['.ts', '.tsx', '.js', '.json', '.css'] // Here we add the 
    // extensions we want to support
  },
  target: 'web',
  mode: isProduction ? 'production' : 'development', // production mode 
  // minifies the code
  module: { 
    rules: [
      {
        test: /\.(tsx|ts)$/,
        exclude: /node_modules/,
        use: {
          loader: 'ts-loader',
          options: {
            transpileOnly: true
          }
        }
      }, 
      { 
        test: /\.css/,
        use: [
          'style-loader',
          'css-loader?modules=true'
        ]
      } 
    ]
  }, 
  plugins: [
    new ForkTsCheckerWebpackPlugin(),
 new HtmlWebpackPlugin({
      title: 'Your project name',
      template: './src/index.html',
      filename: './index.html'
    })
  ],
  optimization: { // This is to split our bundles into vendor and main
    splitChunks: {
      cacheGroups: {
        default: false,
        commons: {
          test: /node_modules/,
          name: 'vendor',
          chunks: 'all'    
        }
      }
    }
  }
}
```

然后，要配置 TypeScript，您需要这个`tsconfig.json`文件：

```jsx
{
  "compilerOptions": {
    "allowJs": true,
    "allowSyntheticDefaultImports": true,
    "baseUrl": "src",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "jsx": "react-jsx",
    "lib": ["dom", "dom.iterable", "esnext"],
    "module": "esnext",
    "moduleResolution": "node",
    "noEmit": true,
    "noFallthroughCasesInSwitch": true,
    "noImplicitAny": false,
    "resolveJsonModule": true,
    "skipLibCheck": true,
    "sourceMap": true,
    "strict": true,
    "target": "es6"
  },
  "include": ["src/**/*.ts", "src/**/*.tsx"],
  "exclude": ["node_modules"]
}
```

为了使用 TypeScript 导入`css`文件，您需要在`src/declarations.d.ts`中创建一个声明文件：

```jsx
declare module '*.css' {
  const content: Record<string, string>
  export default content
}
```

然后，您需要在`src/index.tsx`中创建主文件：

```jsx
import { render } from 'react-dom'

const App = () => {
  return <div>Hello World</div>
}

render(<App />, document.querySelector('#root'))
```

最后，您需要在`src/index.html`中创建初始 HTML 文件：

```jsx
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" 
      />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

我们完成了，如果我们在终端中运行`npm run dev`命令并将浏览器指向`http://localhost:8080`，我们应该能够看到提供的以下标记：

```jsx
<!DOCTYPE html> 
<html> 
  <head> 
    <meta charset="UTF-8"> 
    <title>Your project name</title>
    <script defer src="/vendor.12472959.js"></script>
    <script defer src="/main.12472959.js"></script> 
  </head> 
 <body>    <div id="root"></div>
  </body> 
</html>
```

完美-我们的 React 应用程序正在运行！现在让我们看看如何向我们的项目添加一些 CSS。

## 本地作用域的 CSS

现在，是时候创建我们的应用程序了，它将由一个简单的按钮组成，与我们在以前的示例中使用的相同类型。我们将用它来展示 CSS 模块的所有功能。

让我们更新`src/index.tsx`文件，这是我们在 Webpack 配置中指定的入口：

```jsx
import { render } from 'react-dom'
```

然后，我们可以创建一个简单的按钮。像往常一样，我们将从一个非样式化的按钮开始，然后逐步添加样式：

```jsx
 const Button = () => <button>Click me!</button>
```

最后，我们可以将按钮呈现到 DOM 中：

```jsx
render(<Button />, document.querySelector('#root'))
```

现在，假设我们想要为按钮应用一些样式-背景颜色，大小等。我们创建一个名为`index.css`的常规 CSS 文件，并将以下类放入其中：

```jsx
.button { 
  background-color: #ff0000; 
  width: 320px; 
  padding: 20px; 
  border-radius: 5px; 
  border: none; 
  outline: none; 
}
```

现在，我们说过使用 CSS 模块可以将 CSS 文件导入到 JavaScript 中；让我们看看它是如何工作的。

在我们定义按钮组件的 `index.js` 文件中，我们可以添加以下行：

```jsx
import styles from './index.css'
```

这个 `import` 语句的结果是一个 `styles` 对象，其中所有属性都是在 `index.css` 中定义的类。

如果我们运行 `console.log(styles)`，我们可以在 DevTools 中看到以下对象：

```jsx
{ 
  button: "_2wpxM3yizfwbWee6k0UlD4" 
}
```

因此，我们有一个对象，其中属性是类名，值是（表面上）随机字符串。我们稍后会看到它们并非随机，但让我们先检查一下该对象可以做什么。

我们可以使用对象来设置按钮的类名属性，如下所示：

```jsx
const Button = () => ( 
  <button className={styles.button}>Click me!</button> 
);
```

如果我们回到浏览器，现在可以看到我们在 `index.css` 中定义的样式已经应用到按钮上。这并不是魔术，因为如果我们在 DevTools 中检查，应用到元素的类与我们在代码中导入的 `style` 对象附加的相同字符串。

```jsx
<button class="_2wpxM3yizfwbWee6k0UlD4">Click me!</button>
```

如果我们查看页面的头部部分，现在可以看到相同的类名也已经被注入到页面中：

```jsx
<style type="text/css"> 
  ._2wpxM3yizfwbWee6k0UlD4 { 
    background-color: #ff0000; 
    width: 320px; 
    padding: 20px; 
    border-radius: 5px; 
    border: none; 
    outline: none; 
  } 
</style>
```

这就是 CSS 和样式加载器的工作原理。

CSS 加载器允许您将 CSS 文件导入到您的 JavaScript 模块中，并且当模块标志被激活时，所有类名都会被局部作用于导入的模块。正如我们之前提到的，我们导入的字符串并非随机，而是使用文件的哈希和一些其他参数生成的，以在代码库中是唯一的。

最后，`style-loader` 接受 CSS 模块转换的结果，并将样式注入到页面的头部部分。这非常强大，因为我们拥有 CSS 的全部功能和表现力，同时又具有局部作用域类名和明确依赖项的优势。

正如本章开头提到的，CSS 是全局的，这使得在大型应用程序中很难维护。使用 CSS 模块，类名是局部作用域的，它们不会与应用程序不同部分的其他类名冲突，从而强制产生确定性结果。

此外，明确地在组件内部导入 CSS 依赖项有助于清晰地看到哪些组件需要哪些 CSS。它还非常有用，可以消除死代码，因为当我们出于任何原因删除一个组件时，我们可以准确地知道它使用的是哪些 CSS。

CSS 模块是常规的 CSS，因此我们可以使用伪类、媒体查询和动画。

例如，我们可以添加以下 CSS 规则：

```jsx
.button:hover { 
  color: #fff; 
} 

.button:active { 
  position: relative; 
  top: 2px; 
} 

@media (max-width: 480px) { 
  .button { 
    width: 160px 
  } 
}
```

这将被转换为以下代码并注入到文档中：

```jsx
._2wpxM3yizfwbWee6k0UlD4:hover { 
  color: #fff; 
} 

._2wpxM3yizfwbWee6k0UlD4:active { 
  position: relative; 
  top: 2px; 
} 

@media (max-width: 480px) { 
  ._2wpxM3yizfwbWee6k0UlD4 { 
    width: 160px 
  } 
}
```

类名被创建并在按钮使用的所有地方被替换，使其可靠且本地化，正如预期的那样。

您可能已经注意到，这些类名很棒，但它们使调试变得非常困难，因为我们无法轻松地知道哪些类生成了哈希。在开发模式下，我们可以添加一个特殊的配置参数，通过它我们可以选择用于生成作用域类名的模式。

例如，我们可以将加载程序的值更改如下：

```jsx
{
  test: /\.css/,
  use: [
    { 
      loader: 'style-loader'
    },
    {
      loader: "css-loader",
      options: {
        modules: {
          localIdentName: "[local]--[hash:base64:5]"
        }
      }
    }
  ]
}
```

在这里，`localIdentName`是参数，`[local]`和`[hash:base64:5]`是原始类名值和五个字符哈希的占位符。其他可用的占位符是`[path]`，代表 CSS 文件的路径，以及`[name]`，代表源 CSS 文件的名称。

激活之前的配置选项，我们在浏览器中得到的结果如下：

```jsx
<button class="button--2wpxM">Click me!</button>
```

这样更易读，更容易调试。

在生产环境中，我们不需要这样的类名，我们更关心性能，因此我们可能希望更短的类名和哈希。

使用 Webpack 非常简单，因为我们可以有多个配置文件，可以在应用程序生命周期的不同阶段使用。此外，在生产环境中，我们可能希望提取 CSS 文件，而不是将其从捆绑包中注入到浏览器中，以便我们可以获得更轻的捆绑包，并将 CSS 缓存到内容交付网络以获得更好的性能。

要做到这一点，您需要安装另一个 Webpack 插件，称为`mini-css-extract-plugin`，它可以编写一个实际的 CSS 文件，其中包含从 CSS 模块生成的所有作用域类。

有几个值得一提的 CSS 模块特性。

第一个是`global`关键字。实际上，用`:global`作为任何类的前缀意味着要求 CSS 模块不要在本地范围内对当前选择器进行范围限定。

例如，假设我们将 CSS 更改如下：

```jsx
:global .button { 
  ... 
}
```

输出将如下所示：

```jsx
.button { 
  ... 
}
```

如果您想应用无法在本地范围内进行范围限定的样式，例如第三方小部件，这是很好的。

CSS 模块的我最喜欢的特性是**组合**。通过组合，我们可以从同一文件或外部依赖中提取类，并将所有样式应用于元素。

例如，将将按钮的背景设置为红色的规则从按钮的规则中提取到一个单独的块中，如下所示：

```jsx
.background-red { 
  background-color: #ff0000; 
}
```

然后，我们可以按照以下方式在我们的按钮中进行组合：

```jsx
.button { 
  composes: background-red; 
  width: 320px; 
  padding: 20px; 
  border-radius: 5px; 
  border: none; 
  outline: none; 
}
```

结果是按钮的所有规则和`composes`声明的所有规则都应用于元素。

这是一个非常强大的功能，它以一种迷人的方式工作。你可能期望所有组合的类在被引用为 SASS `@extend`时会在类内部重复，但事实并非如此。简而言之，所有组合的类名都会依次应用于 DOM 中的组件。

在我们的特定情况下，我们会有以下情况：

```jsx
<button class="_2wpxM3yizfwbWee6k0UlD4 Sf8w9cFdQXdRV_i9dgcOq">Click me!</button>
```

在这里，注入到页面中的 CSS 如下：

```jsx
.Sf8w9cFdQXdRV_i9dgcOq { 
  background-color: #ff0000; 
} 

._2wpxM3yizfwbWee6k0UlD4 { 
  width: 320px; 
  padding: 20px; 
  border-radius: 5px; 
  border: none; 
  outline: none; 
}
```

正如你所看到的，我们的 CSS 类名具有唯一的名称，这有利于隔离我们的样式。现在，让我们来看看原子 CSS 模块。

## 原子 CSS 模块

应该清楚组合是如何工作的，以及为什么它是 CSS 模块的一个非常强大的特性。在我开始写这本书的时候工作的公司 YPlan 中，我们试图将其推向更高一步，结合`composes`的强大功能和**原子 CSS**（也称为**功能性 CSS**）的灵活性。

原子 CSS 是一种使用 CSS 的方式，其中每个类都有一个单一的规则。

例如，我们可以创建一个类来将`margin-bottom`设置为`0`：

```jsx
.mb0 { 
  margin-bottom: 0; 
}
```

我们可以使用另一个类将`font-weight`设置为`600`：

```jsx
.fw6 { 
  font-weight: 600; 
} 
```

然后，我们可以将所有这些原子类应用于元素：

```jsx
<h2 class="mb0 fw6">Hello React</h2>
```

这种技术既有争议，又非常高效。开始使用它很困难，因为最终会在标记中有太多的类，这使得难以预测最终结果。如果你仔细想想，它与内联样式非常相似，因为你每条规则应用一个类，除了你使用更短的类名作为代理。

反对原子 CSS 的最大论点通常是你将样式逻辑从 CSS 移动到标记中，这是错误的。类是在 CSS 文件中定义的，但它们在视图中组合，每当你必须修改元素的样式时，你最终会编辑标记。

另一方面，我们尝试使用原子 CSS 一段时间，发现它使原型设计变得非常快速。

事实上，当所有基本规则都已生成时，将这些类应用到元素并创建新样式是一个非常快速的过程，这是很好的。其次，使用原子 CSS，我们可以控制 CSS 文件的大小，因为一旦我们创建了具有其样式的新组件，我们就使用现有的类，而不需要创建新的类，这对性能来说非常好。

因此，我们尝试使用 CSS 模块解决原子 CSS 的问题，并将这种技术称为**原子 CSS 模块**。

实质上，您开始创建您的基本 CSS 类（例如，`mb0`），然后，而不是在标记中逐个应用类名，您可以使用 CSS 模块将它们组合成占位符类。

让我们看一个例子：

```jsx
.title { 
  composes: mb0 fw6; 
}
```

这里有另一个例子：

```jsx
<h2 className={styles.title}>Hello React</h2>
```

这很棒，因为您仍然将样式逻辑保留在 CSS 中，而 CSS 模块的`composes`会通过在标记中应用所有单个类来为您完成工作。

上述代码的结果如下：

```jsx
<h2 class="title--3JCJR mb0--21SyP fw6--1JRhZ">Hello React</h2>
```

在这里，`title`，`mb0`和`fw6`都会自动应用到元素上。它们也是局部作用域的，因此我们拥有 CSS 模块的所有优势。

## React CSS 模块

最后但同样重要的是，有一个很棒的库可以帮助我们使用 CSS 模块。您可能已经注意到，我们使用`style`对象来加载 CSS 的所有类，因为 JavaScript 不支持连字符属性，我们被迫使用驼峰命名的类名。

此外，如果我们引用了 CSS 文件中不存在的类名，就无法知道它，`undefined`会被添加到类名列表中。出于这些和其他有用的功能，我们可能想尝试一个使使用 CSS 模块更加顺畅的包。

让我们通过回到我们在本节中之前使用普通 CSS 模块的`index.tsx`文件，将其更改为使用 React CSS 模块来看看这意味着什么。

该包名为`react-css-modules`，我们首先必须安装它：

```jsx
npm install react-css-modules
```

安装完包后，我们在`index.tsx`文件中导入它：

```jsx
import cssModules from 'react-css-modules'
```

我们将其作为 HOC 使用，将要增强的`Button`组件和我们从 CSS 中导入的`styles`对象传递给它：

```jsx
const EnhancedButton = cssModules(Button, styles)
```

现在，我们必须改变按钮的实现，避免使用`styles`对象。使用 React CSS 模块，我们使用`styleName`属性，它会转换为常规类。

这样做的好处是，我们可以将类名作为字符串使用（例如，`"button"`）：

```jsx
const Button = () => <button styleName="button">Click me!</button>;
```

如果我们现在将 `EnhancedButton` 渲染到 DOM 中，我们会发现与之前相比，实际上没有什么变化，这意味着库是有效的。

假设我们尝试将 `styleName` 属性更改为引用一个不存在的类名，如下所示：

```jsx
import { render } from 'react-dom'
import styles from './index.css'
import cssModules from 'react-css-modules'

const Button = () => <button styleName="button1">Click me!</button>

const EnhancedButton = cssModules(Button, styles)

render(<EnhancedButton />, document.querySelector('#root'))
```

通过这样做，我们将在浏览器的控制台中看到以下错误：

```jsx
Uncaught Error: "button1" CSS module is undefined.
```

当代码库不断增长，我们有多个开发人员在不同的组件和样式上工作时，这将特别有帮助。

# 实现 styled-components

有一个非常有前途的库，因为它考虑了其他库在样式化组件方面遇到的所有问题。已经有了不同的路径来编写 JavaScript 中的 CSS，并且尝试了许多解决方案，因此现在是时候使用所有这些经验教训来构建一个库了。

该库由 JavaScript 社区中两位知名的开发人员 *Glenn Maddern* 和 *Max Stoiberg* 构思和维护。它代表了解决问题的一种非常现代的方法，并且使用了 ES2015 的边缘功能和一些已应用于 React 的高级技术，为样式提供了一个完整的解决方案。

让我们看看如何创建与前几节中看到的相同的按钮，并检查我们感兴趣的所有 CSS 特性（例如伪类和媒体查询）是否与 `styled-components` 一起工作。

首先，我们必须通过运行以下命令来安装该库：

```jsx
npm install styled-components
```

安装库后，我们必须在组件文件中导入它：

```jsx
import styled from 'styled-components'
```

在那时，我们可以使用 `styled` 函数通过 `styled.elementName` 来创建任何元素，其中 `elementName` 可以是 `div`、按钮或任何其他有效的 DOM 元素。

第二件事是定义我们正在创建的元素的样式，为此，我们使用了一个名为 **tagged template literals** 的 ES6 特性，这是一种在不被插值的情况下将模板字符串传递给函数的方法。

这意味着函数接收到了包含所有 JavaScript 表达式的实际模板，这使得库能够充分利用 JavaScript 的全部功能来应用样式到元素上。

让我们从创建一个带有基本样式的简单按钮开始：

```jsx
const Button = styled.button`
  backgroundColor: #ff0000; 
  width: 320px; 
  padding: 20px; 
  borderRadius: 5px; 
  border: none; 
  outline: none; 
`;
```

这种*有点奇怪*的语法返回一个名为`Button`的合适的 React 组件，它渲染一个按钮元素，并将模板中定义的所有样式应用于它。样式的应用方式是创建一个唯一的类名，将其添加到元素中，然后将相应的样式注入到文档的头部。

以下是被渲染的组件：

```jsx
<button class="kYvFOg">Click me!</button>
```

添加到页面的样式如下：

```jsx
.kYvFOg { 
  background-color: #ff0000; 
  width: 320px; 
  padding: 20px; 
  border-radius: 5px; 
  border: none; 
  outline: none; 
}
```

`styled-components`的好处是它支持几乎所有 CSS 的功能，这使它成为在实际应用中使用的一个很好的选择。

例如，它使用类似 SASS 的语法支持伪类：

```jsx
const Button = styled.button` 
  background-color: #ff0000; 
  width: 320px; 
  padding: 20px; 
  border-radius: 5px; 
  border: none; 
  outline: none; 
  &:hover { 
    color: #fff; 
  } 
  &:active { 
    position: relative; 
    top: 2px; 
  }
`
```

它还支持媒体查询：

```jsx
const Button = styled.button` 
  background-color: #ff0000; 
  width: 320px; 
  padding: 20px; 
  border-radius: 5px; 
  border: none; 
  outline: none; 
  &:hover { 
    color: #fff; 
  } 
  &:active { 
    position: relative; 
    top: 2px; 
  } 
  @media (max-width: 480px) { 
    width: 160px; 
  } 
`;
```

这个库还有许多其他功能可以为您的项目带来。

例如，一旦您创建了按钮，就可以轻松地覆盖其样式，并多次使用具有不同属性的按钮。在模板内，还可以使用组件接收到的 props，并相应地更改样式。

另一个很棒的功能是**主题**。将您的组件包装在`ThemeProvider`组件中，您可以向三个组件的子组件注入一个主题属性，这样就可以轻松地创建 UI，其中一部分样式在组件之间共享，而另一些属性取决于当前选择的主题。

毫无疑问，`styled-components`库在将样式提升到下一个级别时是一个改变游戏规则的工具，在开始时可能会感觉有点奇怪，因为它是通过组件实现样式，但一旦您习惯了，我保证它会成为您最喜欢的样式包。

# 总结

在本章中，我们涉及了许多有趣的话题。我们首先讨论了在规模上使用 CSS 时遇到的问题，具体来说，Facebook 在处理 CSS 时遇到的问题。我们了解了在 React 中如何使用内联样式，以及为什么将样式与组件共同定位是有益的。我们还看了内联样式的局限性。然后，我们转向了 Radium，它解决了内联样式的主要问题，为我们提供了一个清晰的接口来在 JavaScript 中编写 CSS。对于那些认为内联样式是一个不好的解决方案的人，我们进入了 CSS 模块的世界，从零开始设置了一个简单的项目。

将 CSS 文件导入到我们的组件中可以清晰地表明依赖关系，而在本地范围内命名类名可以避免冲突。我们看到了 CSS 模块的`composes`是一个很棒的功能，以及我们如何可以将其与原子 CSS 结合使用，创建一个快速原型的框架。

最后，我们简要地看了一下`styled-components`，这是一个非常有前途的库，旨在彻底改变我们处理组件样式的方式。

到目前为止，您已经学习了许多在 React 中使用 CSS 样式的方法，从内联样式到 CSS 模块，或者使用诸如`styled-components`之类的库。在下一章中，我们将学习如何实现并从服务器端渲染中获益。
