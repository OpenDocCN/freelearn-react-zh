# 第六章：使用 Jest 和 Enzyme 构建探索 TDD 的应用程序

为了保持应用的可维护性，最好为项目设置测试。一些开发人员讨厌编写测试，因此试图避免编写测试，而其他开发人员则喜欢将测试作为其开发过程的核心，实施**测试驱动开发**（**TDD**）策略。关于测试应用程序以及如何进行测试有很多不同的观点。幸运的是，在使用 React 构建应用程序时，许多出色的库可以帮助您进行测试。

在本章中，您将使用两个库来对 React 应用程序进行单元测试。第一个是 Jest，由 Facebook 自己维护，并随 Create React App 一起发布。另一个工具叫做 Enzyme，它比 Jest 具有更多的功能，并且可以用来测试组件内的整个生命周期。它们一起非常适合测试大多数 React 应用程序，如果您想要测试函数或组件在给定特定输入时是否表现如预期。

本章将涵盖以下主题：

+   使用 Jest 进行单元测试

+   为测试渲染 React 组件

+   使用 Enzyme 进行测试

# 项目概述

在本章中，我们将创建一个酒店评论应用程序，并使用 Jest 和 Enzyme 进行单元和集成测试。该应用程序已经预先构建，并使用了我们在前几章中看到的相同模式。

构建时间为 2 小时。

# 入门

本章的应用程序是基于初始版本构建的，可以在[`github.com/PacktPublishing/React-Projects/tree/ch6-initial`](https://github.com/PacktPublishing/React-Projects/tree/ch6-initial)找到。本章的完整代码可以在 GitHub 上找到：[`github.com/PacktPublishing/React-Projects/tree/ch6`](https://github.com/PacktPublishing/React-Projects/tree/ch6)。

从 GitHub 下载初始项目，并进入该项目的根目录，然后运行`npm install`命令。由于该项目是基于 Create React App 构建的，运行此命令将安装`react`、`react-dom`和`react-scripts`。此外，还将安装`styled-components`和`react-router-dom`，以便它们可以处理应用程序的样式和路由。安装过程完成后，可以执行`npm start`命令来运行应用程序，然后在浏览器中访问`http://localhost:3000`来查看项目。就像你在之前章节中构建的应用程序一样，这个应用程序也是一个 PWA。

初始应用程序包括一个简单的标题和酒店列表。这些酒店有标题和缩略图等元信息。该页面将如下所示。如果你点击列表中的任何酒店，将会打开一个新页面，显示该酒店的评论列表。通过点击页面左上角的按钮，你可以返回到上一个页面；通过点击右上角的按钮，将打开一个包含表单的页面，你可以在其中添加评论。如果你添加了新的评论，这些数据将被存储在全局上下文中，并发送到一个模拟 API 服务器。

![](img/c25bf83e-16c2-42a4-a572-8dffff5127ea.png)

如果你查看项目的结构，你会发现它使用了与我们之前创建的项目相同的结构。这个应用程序的入口点是一个名为`src/index.js`的文件，它渲染了一个名为`App`的组件。在这个`App`组件中，所有的路由都被声明并包装在一个路由组件中。此外，这里还声明了持有全局上下文和提供者的组件。与之前创建的应用程序相比，这个应用程序中没有使用容器组件模式。相反，所有的数据获取都是通过上下文组件完成的。生命周期是使用 Hooks 来访问的：

```jsx
hotel-review
|-- node_modules
|-- public
    |-- assets
        |-- beachfront-hotel.jpg
        |-- forest-apartments.jpg
        |-- favicon.ico
        |-- index.html
        |-- manifest.json
|-- src
    |-- components
        |-- Button
            |-- Button.js
        |-- Detail
            |-- Detail.js
            |-- ReviewItem.js
        |-- Form
            |-- Form.js
            |-- FormItem.js
        |-- Header
            |-- Header.js
            |-- SubHeader.js
        |-- Hotels
            |-- Hotels.js
            |-- HotelItem.js
        |-- App.js
    |-- Context
        |-- GlobalContext.js
        |-- HotelsContextProvider.js
        |-- ReviewsContextProvider.js
    |-- api.js
    |-- index.js
    |-- serviceWorker.js
.gitignore
package.json
```

在上述项目结构中，你可以看到`public/assets`目录中还有两个文件，这些文件是酒店的缩略图。为了在渲染的应用程序中使用它们，你可以将它们放在`public`目录中。此外，在`src`中还有一个名为`api.js`的文件，它导出了函数，以便可以向 API 发送`GET`和`POST`请求。

# 酒店评论应用程序

在本节中，我们将为在 Create React App 中创建的酒店评论应用程序添加单元测试和集成测试。这个应用程序允许你向酒店列表中添加评论，并从全局上下文中控制这些数据。Jest 和 Enzyme 将用于在没有 DOM 的情况下渲染 React 组件，并对这些组件进行测试断言。

# 使用 Jest 进行单元测试

单元测试是应用程序的重要部分，因为你希望知道你的函数和组件在进行代码更改时是否按预期行为。为此，你将使用 Jest，这是一个由 Facebook 创建的用于 JavaScript 应用程序的开源测试包。使用 Jest，你可以测试断言，例如，如果函数的输出与你预期的值匹配。

要开始使用 Jest，你无需安装任何东西；它是 Create React App 的一部分。如果你查看`package.json`文件，你会看到已经有一个用于运行测试的脚本。

让我们看看如果你从终端执行以下命令会发生什么：

```jsx
npm run test 
```

这将返回一条消息，说`No tests found related to files changed since last commit.`，这意味着 Jest 正在观察模式下运行，并且只对已更改的文件运行测试。通过按下`a`键，你可以运行所有测试，即使你没有修改任何文件。如果按下这个键，将显示以下消息：

```jsx
No tests found
 26 files checked.
 testMatch: /hotel-review/src/**/__tests__/**/*.{js,jsx,ts,tsx},/hotel-review/src/**/?(*.)(spec|test).{js,jsx,ts,tsx} - 0 matches
 testPathIgnorePatterns: /node_modules/ - 26 matches
Pattern: - 0 matches
```

这条消息说明已经调查了`26`个文件，但没有找到测试。它还说明正在寻找项目中名为`__tests__`的目录中的 JavaScript 或 JSX 文件，以及具有`spec`或`test`后缀的文件。`node_modules`目录，即所有`npm`包安装的地方，将被忽略。从这条消息中，你可能已经注意到 Jest 会自动检测包含测试的文件。

可以使用 Jest 来创建这些测试，这将在本节的第一部分进行演示。

# 创建一个单元测试

由于 Jest 可以以多种方式检测哪个文件包含测试，让我们选择每个组件都有一个单独的测试文件的结构。这个测试文件将与包含组件的文件同名，后缀为`.test`。如果我们选择`SubHeader`组件，我们可以在`src/components/Header`目录中创建一个名为`SubHeader.test.js`的新文件。将以下代码添加到这个文件中：

```jsx
describe('the <SubHeader /> component', () => {
  it('should render', () => {

  });
});
```

这里使用了 Jest 的两个全局函数：

+   `describe`：用于定义一组相关的测试

+   `it`：用于定义测试

在测试的定义中，您可以添加假设，比如`toEqual`或`toBe`，分别检查值是否完全等于某些内容，或者只是类型匹配。假设可以在`it`函数的回调中添加：

```jsx
describe('the <SubHeader /> component', () => {
  it('should render', () => {
+   expect(1+2).toBe(3);
  });
});
```

如果您的终端仍在运行测试脚本，您将看到 Jest 已检测到您的测试。测试成功，因为`1+2`确实是`3`。让我们继续并将假设更改为以下内容：

```jsx
describe('the <SubHeader /> component', () => {
  it('should render', () => {
-    expect(1+2).toBe(3);
+    expect(1+2).toBe('3');
  });
});
```

现在，测试将失败，因为第二个假设不匹配。虽然`1+2`仍然等于`3`，但假设返回了一个值为`3`的字符串类型，而实际上返回的是一个数字类型。这在编写代码时可以帮助您，因为您可以确保应用程序不会更改其值的类型。

然而，这个假设实际上没有用，因为它并没有测试您的组件。要测试您的组件，您需要渲染它。在本节的下一部分将处理渲染组件以便测试它们。

# 渲染 React 组件进行测试

Jest 基于 Node.js，这意味着它无法使用 DOM 来渲染您的组件并测试其功能。因此，您需要向项目添加一个 React 核心软件包，它可以帮助您在没有 DOM 的情况下渲染组件。让我们在这里看一下：

1.  从您的终端执行以下命令，它将在您的项目中安装`react-test-renderer`。它可以作为 devDependency 安装，因为您不需要在应用程序的构建版本上运行测试：

```jsx
npm install react-test-renderer --save-dev
```

1.  安装了`react-test-renderer`后，您现在可以将此软件包导入到`src/components/Header/SubHeader.test.js`文件中。此软件包返回一个名为`ShallowRenderer`的方法，让您可以渲染组件。使用浅渲染，您只在其第一级渲染组件，从而排除任何可能的子组件。您还需要导入 React 和您想要测试的实际组件，因为这些是`react-test-renderer`使用的：

```jsx
+ import React from 'react';
+ import ShallowRenderer from 'react-test-renderer/shallow';
+ import SubHeader from './SubHeader';

describe('the <SubHeader /> component', () => {
 ....
```

1.  在您的测试中，您现在可以使用`ShallowRenderer`来渲染组件，并获得此组件的输出。使用 Jest 的`toMatchSnapshot`假设，您可以测试组件的结构。`ShallowRenderer`将渲染组件，`toMatchSnapshot`将从此渲染创建快照，并在每次运行此测试时将其与实际组件进行比较：

```jsx
import React from 'react';
import ShallowRenderer from 'react-test-renderer/shallow';
import SubHeader from './SubHeader';

describe('the <SubHeader /> component', () => {
  it('should render', () => {
-   expect(1+2).toBe('3');
+    const renderer = new ShallowRenderer();
+    renderer.render(<SubHeader />);
+    const component = renderer.getRenderOutput();

+    expect(component).toMatchSnapshot();
  });
});
```

1.  在`src/components/Header`目录中，Jest 现在创建了一个名为`__snapshots__`的新目录。在这个目录中有一个名为`SubHeader.test.js.snap`的文件，其中包含了快照。如果您打开这个文件，您会看到`SubHeader`组件的渲染版本存储在这里：

```jsx
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`the <SubHeader /> component should render 1`] = `
<ForwardRef>
  <ForwardRef />
</ForwardRef>
`;
```

使用`styled-components`创建的组件无法被`react-test-renderer`渲染，因为它们是由`styled-components`导出的方式。如果您查看`SubHeader`组件的代码，您会看到`ForwardRef`组件代表`SubHeaderWrapper`和`Title`。在本章的后面，我们将使用 Enzyme 进行测试，它可以更好地处理这种测试场景。

1.  由于未向`SubHeader`组件传递任何 props，因此`react-test-renderer`不会呈现任何实际值。您可以通过向`SubHeader`组件传递`title` prop 来检查快照的工作方式。为此，创建一个新的测试场景，应该呈现带有标题的`SubHeader`。此外，将`renderer`常量的创建移动到`describe`函数中，以便它可以被所有的测试场景使用：

```jsx
import React from 'react';
import ShallowRenderer from 'react-test-renderer/shallow';
import SubHeader from './SubHeader';

describe('the <SubHeader /> component', () => {
+  const renderer = new ShallowRenderer();

  it('should render', () => {
-   const renderer = new ShallowRenderer(); 
    renderer.render(<SubHeader />);
    const component = renderer.getRenderOutput();

    expect(component).toMatchSnapshot();
  });

+  it('should render with a dynamic title', () => {
+    renderer.render(<SubHeader title='Test Application' />);
+    const component = renderer.getRenderOutput();

+    expect(component).toMatchSnapshot();
+  }); });
```

1.  下次运行测试时，将会在`src/components/Header/__snapshots__/SubHeader.test.js.snap`文件中添加一个新的快照。这个快照为`title` prop 呈现了一个值。如果您在测试文件中更改了`SubHeader`组件显示的`title` prop 的值，渲染的组件将不再与快照匹配。您可以通过更改测试场景中`title` prop 的值来尝试这一点：

```jsx
import React from 'react';
import ShallowRenderer from 'react-test-renderer/shallow';
import SubHeader from './SubHeader';

describe('the <SubHeader /> component', () => {
  const renderer = new ShallowRenderer();

  ...

  it('should render with a dynamic title', () => {
-   renderer.render(<SubHeader title='Test Application' />);
+   renderer.render(<SubHeader title='Test Application Test' />);
    const component = renderer.getRenderOutput();

    expect(component).toMatchSnapshot();
  });
});
```

Jest 将在终端中返回以下消息，其中指定了与快照相比发生了哪些变化的行。在这种情况下，显示的标题不再是`Test Application`，而是`Test Application Test`，这与快照中的标题不匹配：

```jsx
 • the <SubHeader /> component › should render

 expect(value).toMatchSnapshot()

 Received value does not match stored snapshot "the <SubHeader /> component should render 1".

 - Snapshot
 + Received

 <ForwardRef>
 <ForwardRef>
 - Test Application
 + Test Application Title
 </ForwardRef>
 </ForwardRef>
...
```

通过按下`u`键，您可以更新快照以处理这个新的测试场景。这是测试组件结构的一种简单方法，可以看到标题是否已经被渲染。通过前面的测试，最初创建的快照仍然与第一个测试的渲染组件匹配。此外，还为第二个测试创建了另一个快照，其中向`SubHeader`组件添加了`title` prop。

1.  你可以对传递给`SubHeader`组件的其他属性做同样的操作，如果你传递或不传递某些属性，它会以不同的方式呈现。除了`title`之外，这个组件还接受`goBack`和`openForm`作为属性，其中`openForm`属性的默认值为 false。

就像我们为`title`属性所做的那样，我们也可以为另外两个属性创建测试场景。当`goBack`有值时，会创建一个按钮，让我们返回到上一页，而当`openForm`有值时，会创建一个按钮，让我们可以继续到下一页，这样我们就可以添加新的评论。你需要将这两个新的测试场景添加到`src/components/Header/SubHeader.test.js`文件中：

```jsx
import React from 'react';
import ShallowRenderer from 'react-test-renderer/shallow';
import SubHeader from './SubHeader';

describe('the <SubHeader /> component', () => {
  const renderer = new ShallowRenderer();

  ...

+  it('should render with a goback button', () => {
+   renderer.render(<SubHeader goBack={() => {}} />);
+    const component = renderer.getRenderOutput();
+
+    expect(component).toMatchSnapshot();
+  });

+  it('should render with a form button', () => {
+   renderer.render(<SubHeader openForm={() => {}} />);
+    const result = renderer.getRenderOutput();
+
+    expect(component).toMatchSnapshot();
+  });
});
```

你现在为`SubHeader`组件创建了另外两个快照，总共有四个快照。Jest 还会显示你的测试覆盖了多少行代码。你的测试覆盖率越高，就越有理由认为你的代码是稳定的。你可以通过执行带有`--coverage`标志的`test`脚本命令来检查你的代码的测试覆盖率，或者在终端中使用以下命令：

```jsx
npm run test --coverage
```

这个命令将运行你的测试并生成一个报告，其中包含有关每个文件的代码测试覆盖信息。在为`SubHeader`添加测试之后，这个报告将如下所示：

```jsx
 PASS src/components/Header/SubHeader.test.js
----------------------------|----------|----------|----------|----------|-------------------|
File | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s |
----------------------------|----------|----------|----------|----------|-------------------|
All files | 5 | 6.74 | 4.26 | 5.21 | |
 src | 0 | 0 | 0 | 0 | |
 api.js | 0 | 0 | 0 | 0 |... 20,22,23,26,30 |
 index.js | 0 | 100 | 100 | 0 | 1,2,3,4,5,17 |
 serviceWorker.js | 0 | 0 | 0 | 0 |... 23,130,131,132 |
 src/components | 0 | 100 | 0 | 0 | |
 App.js | 0 | 100 | 0 | 0 |... ,8,10,22,26,27 |
 src/components/Button | 0 | 100 | 0 | 0 | |
 Button.js | 0 | 100 | 0 | 0 | 20 |
 src/components/Detail | 0 | 0 | 0 | 0 | |
 Detail.js | 0 | 0 | 0 | 0 |... 26,27,31,33,35 |
 ReviewItem.js | 0 | 100 | 0 | 0 |... 15,21,26,30,31 |
 src/components/Form | 0 | 0 | 0 | 0 | |
 Form.js | 0 | 0 | 0 | 0 |... 29,30,31,34,36 |
 FormInput.js | 0 | 0 | 0 | 0 |... 17,26,35,40,41 |
 src/components/Header | 100 | 100 | 100 | 100 | |
 Header.js | 100 | 100 | 100 | 100 | |
 SubHeader.js | 100 | 100 | 100 | 100 | |
...
```

测试覆盖只告诉我们关于已经测试过的代码行和函数的信息，而不是它们的实际实现。拥有 100%的测试覆盖并不意味着你的代码中没有任何错误，因为总会有边缘情况。此外，达到 100%的测试覆盖意味着你可能会花更多的时间编写测试而不是实际的代码。通常，80%以上的测试覆盖被认为是良好的实践。

正如你所看到的，组件的测试覆盖率为 100%，这意味着你的测试覆盖了所有的代码行。然而，使用快照测试的这种方法会创建大量新文件和代码行。我们将在本节的下一部分中看看我们可以用其他方法来测试我们的组件。

# 使用断言测试组件

理论上，快照测试并不一定是坏的实践；然而，随着时间的推移，你的文件可能会变得非常庞大。此外，由于你没有明确告诉 Jest 你想测试组件的哪一部分，你可能需要定期更新你的代码。

幸运的是，使用快照并不是我们测试组件是否渲染正确属性的唯一方法。相反，您还可以直接比较组件渲染的属性的值并进行断言。使用断言进行测试的重要优势是，您可以进行大量测试，而无需深入了解正在测试的组件的逻辑。

例如，您可以查看正在渲染的子元素的样子。让我们看看如何做到这一点：

1.  首先，让我们为 `Button` 组件创建一个快照测试，以比较测试覆盖率的影响。创建一个名为 `src/components/Button/Button.test.js` 的新文件。在这个文件中，您需要插入一个创建快照的测试：

```jsx
import React from 'react';
import ShallowRenderer from 'react-test-renderer/shallow';
import Button from './Button';

describe('the <Button /> component', () => {
  const renderer = new ShallowRenderer();

  it('should render', () => {
    const children = 'This is a button';
    renderer.render(<Button>{children</Button>);
    const result = renderer.getRenderOutput();

    expect(result).toMatchSnapshot();
  });
});
```

1.  如果您使用 `--coverage` 标志运行测试，将创建一个新的测试覆盖报告：

```jsx
npm run test --coverage
```

此报告生成以下报告，显示了 `Button` 组件的覆盖率，为 100％：

```jsx
 PASS src/components/Header/SubHeader.test.js
 PASS src/components/Button/Button.test.js
 › 1 snapshot written.
 PASS src/components/Header/Header.test.js
----------------------------|----------|----------|----------|----------|-------------------|
File | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s |
----------------------------|----------|----------|----------|----------|-------------------|
All files | 5.45 | 6.74 | 6.38 | 5.69 | |
 src | 0 | 0 | 0 | 0 | |
 api.js | 0 | 0 | 0 | 0 |... 20,22,23,26,30 |
 index.js | 0 | 100 | 100 | 0 | 1,2,3,4,5,17 |
 serviceWorker.js | 0 | 0 | 0 | 0 |... 23,130,131,132 |
 src/components | 0 | 100 | 0 | 0 | |
 App.js | 0 | 100 | 0 | 0 |... ,8,10,22,26,27 |
 src/components/Button | 100 | 100 | 100 | 100 | |
 Button.js | 100 | 100 | 100 | 100 | |
```

如果您打开 `src/components/Button/__snapshots__/Button.test.js.snap` 文件中 `Button` 组件的快照，您将看到按钮内部渲染的唯一内容（由 `ForwardRef` 表示）是 `children` 属性：

```jsx
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`the <Button /> component should render 1`] = `
<ForwardRef>
  This is a button
</ForwardRef>
`;
```

1.  尽管测试覆盖率达到了 100％，但还有其他方法可以测试正确的子元素是否已被渲染。为此，我们可以创建一个新的测试，也使用 `ShallowRenderer` 并尝试使用子元素渲染 `Button` 组件。这个测试断言渲染的 `children` 属性是否等于 `Button` 渲染的实际 `children` 属性。您可以删除快照测试，因为您只想通过断言测试子元素：

```jsx
import React from 'react';
import ShallowRenderer from 'react-test-renderer/shallow';
import Button from './Button';

describe('the <Button /> component', () => {
  const renderer = new ShallowRenderer();

-  it('should render', () => {
-    const children = 'This is a button';
-    renderer.render(<Button>{children}</Button>);
-    const result = renderer.getRenderOutput();

-    expect(result).toMatchSnapshot();
-  })

+  it('should render the correct children', () => {
+    const children = 'This is a button';
+    renderer.render(<Button>{children}</Button>);
+    const component = renderer.getRenderOutput();

+    expect(component.props.children).toEqual(children);
+  });
});
```

1.  从您的终端运行 `npm run test --coverage` 再次检查这种测试方法对测试覆盖率的影响：

```jsx
 PASS src/components/Header/Header.test.js
 PASS src/components/Header/SubHeader.test.js
 PASS src/components/Button/Button.test.js
 › 1 snapshot obsolete.
 • the <Button /> component should render 1
----------------------------|----------|----------|----------|----------|-------------------|
File | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s |
----------------------------|----------|----------|----------|----------|-------------------|
All files | 5.45 | 6.74 | 6.38 | 5.69 | |
 src | 0 | 0 | 0 | 0 | |
 api.js | 0 | 0 | 0 | 0 |... 20,22,23,26,30 |
 index.js | 0 | 100 | 100 | 0 | 1,2,3,4,5,17 |
 serviceWorker.js | 0 | 0 | 0 | 0 |... 23,130,131,132 |
 src/components | 0 | 100 | 0 | 0 | |
 App.js | 0 | 100 | 0 | 0 |... ,8,10,22,26,27 |
 src/components/Button | 100 | 100 | 100 | 100 | |
 Button.js | 100 | 100 | 100 | 100 | |
...
```

在上述报告中，您可以看到测试覆盖率仍然为 100％，这意味着这种测试方法具有相同的结果。但这次，您特别测试子元素是否等于该值。好处是，您无需在每次进行代码更改时更新快照。

1.  还显示了一个消息，指出 `1 个快照已过时`。通过使用 `-u` 标志运行 `npm run test`，`Button` 组件的快照将被 Jest 删除：

```jsx
npm run test -u
```

这为我们提供了以下输出，显示快照已被移除：

```jsx
 PASS src/components/Button/Button.test.js
 › snapshot file removed.

Snapshot Summary
 › 1 snapshot file removed from 1 test suite.
```

然而，`Button`组件不仅接受`children`属性，还接受`onClick`属性。如果您想测试当单击按钮时是否触发了此`onClick`属性，您需要以不同的方式渲染组件。这可以通过使用`react-test-renderer`来完成，但 React 文档还指出您也可以使用 Enzyme 来实现这一点。

在下一节中，我们将使用 Enzyme 的浅渲染函数，该函数比`ShallowRenderer`有更多选项。

# 使用 Enzyme 进行 React 测试

`react-test-renderer`的`ShallowRenderer`允许我们渲染组件的结构，但不会显示组件在某些场景下的交互方式，例如当触发`onClick`事件时。为了模拟这一点，我们将使用一个更复杂的工具，称为 Enzyme。

# 使用 Enzyme 进行浅渲染

Enzyme 是由 Airbnb 创建的开源 JavaScript 测试库，可以与几乎所有 JavaScript 库或框架一起使用。使用 Enzyme，您还可以浅渲染组件以测试组件的第一级，以及渲染嵌套组件，并模拟集成测试的生命周期。Enzyme 库可以使用`npm`安装，并且还需要一个适配器来模拟 React 功能。让我们开始吧：

1.  安装 Enzyme，您需要从终端运行以下命令，该命令安装 Enzyme 和您正在使用的 React 版本的特定适配器：

```jsx
npm install enzyme enzyme-adapter-react-16 --save-dev
```

1.  安装 Enzyme 后，您需要创建一个设置文件，告诉 Enzyme 应该使用哪个适配器来运行测试。通常，您需要在`package.json`文件中指定保存此配置的文件，但是，当您使用 Create React App 时，这已经为您完成。自动用作测试库配置文件的文件名为`setupTests.js`，应该创建在`src`目录中。创建文件后，将以下代码粘贴到其中：

```jsx
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
```

安装 Enzyme 后，您将无法再使用使用`react-test-renderer`的测试场景。因此，您需要更改`SubHeader`和`Button`组件的测试。正如我们之前提到的，Enzyme 有一个方法允许我们浅渲染组件。让我们先尝试对`SubHeader`组件进行这样的操作：

1.  您需要从 Enzyme 导入`shallow`，而不是导入`react-test-renderer`。`ShallowRender`方法不应再添加到`renderer`常量中，因此您可以删除此行：

```jsx
import React from 'react';
- import ShallowRenderer from 'react-test-renderer/shallow';
+ import { shallow } from 'enzyme';
import SubHeader from './SubHeader';

describe('the <SubHeader /> component', () => {
-  const renderer = new ShallowRenderer();
  it('should render', () => {
    ...
```

1.  每个测试方案都应更改为使用 Enzyme 的浅渲染函数。我们可以通过用`shallow`替换`renderer.render`来实现这一点。我们用于获取此渲染输出的函数也可以删除。Enzyme 的`shallow`渲染将立即创建一个可以由 Jest 测试的结果：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import SubHeader from './SubHeader';

describe('the <SubHeader /> component', () => {
  it('should render', () => {
-    renderer.render(<SubHeader />);
-    const component = renderer.getRenderOutput();
+    const component = shallow(<SubHeader />);

    expect(component).toMatchSnapshot();
  });

  ...
```

1.  就像我们在第一个测试方案中所做的那样，我们必须替换其他测试方案；否则，测试将无法运行。这是因为我们已经删除了`react-test-renderer`的设置：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import SubHeader from './SubHeader';

describe('the <SubHeader /> component', () => {
  ...

  it('should render with a dynamic title', () => {
-    renderer.render(<SubHeader title='Test Application' />);
-    const component = renderer.getRenderOutput();
+    const component = shallow(<SubHeader title='Test Application' />);

    expect(component).toMatchSnapshot();
  });

  it('should render with a goback button', () => {
-    renderer.render(<SubHeader goBack={() => {}} />);
-    const component = renderer.getRenderOutput();
+    const component = shallow(<SubHeader goBack={() => {}} />);

    expect(component).toMatchSnapshot();
  });

  it('should render with a form button', () => {
-    renderer.render(<SubHeader openForm={() => {}} />);
-    const component = renderer.getRenderOutput();
+    const component = shallow(<SubHeader openForm={() => {}} />);

    expect(component).toMatchSnapshot();
  });
});
```

1.  在终端中，您现在可以通过运行`npm run test`再次运行测试。由于测试正在观察模式下运行，`Button`组件的测试可能也会开始运行。您可以通过按下`p`键然后在终端中输入`SubHeader`来指定应该运行哪些测试。现在，Jest 将仅运行`SubHeader`组件的测试。

由于您的快照不再是由`react-test-renderer`创建的快照，测试将失败。Enzyme 的浅渲染对来自`styled-components`的导出有更好的理解，不再将这些组件呈现为`ForwardRef`组件。相反，它返回，例如，名为`styled.div`或`styled.h2`的组件：

```jsx
 FAIL src/components/Header/SubHeader.test.js
 the <SubHeader /> component
 Χ should render (27ms)
 Χ should render with a dynamic title (4ms)
 Χ should render with a goback button (4ms)
 Χ should render with a form button (4ms)

 • the <SubHeader /> component › should render

 expect(value).toMatchSnapshot()

 Received value does not match stored snapshot "the <SubHeader /> component should render 1".

 - Snapshot
 + Received

 - <ForwardRef>
 - <ForwardRef />
 - </ForwardRef>
 + <styled.div>
 + <styled.h2 />
 + </styled.div>
```

通过按下`u`键，所有由`react-test-renderer`创建的快照将被 Enzyme 的新快照替换。

对于`Button`组件，也可以进行相同的操作，不使用快照进行测试。而是使用断言。在您的测试方案中，在`src/components/Button/Button.test.js`文件中，用 Enzyme 的浅渲染替换`ShallowRenderer`。此外，由于 Enzyme 呈现组件的方式，`component.props.children`的值不再存在。相反，您需要使用`props`方法，该方法可用于浅渲染的组件上，以获取`children`属性：

```jsx
import React from 'react';
- import ShallowRenderer from 'react-test-renderer/shallow';
+ import { shallow } from 'enzyme';
import Button from './Button';

describe('the <Button /> component', () => {
-  const renderer = new ShallowRenderer();

  it('should render the correct children', () => {
    const children = 'This is a button';
-   renderer.render(<Button>{children}</Button>);
-   const component = renderer.getRenderOutput();
+   const component = shallow(<Button>{children}</Button>)

-   expect(component.props.children).toEqual(children)
+   expect(component.props().children).toEqual(children)
  })
})
```

现在当您运行测试时，所有测试都应该成功，并且测试覆盖率不应受影响，因为您仍在测试组件上的属性是否被渲染。然而，使用 Enzyme 的快照，您可以获得有关正在呈现的组件结构的更多信息。现在，您甚至可以测试更多内容，并找出例如`onClick`事件是如何处理的。

然而，快照并不是测试 React 组件的唯一方式，正如我们将在本节的下一部分中看到的那样。

# 使用浅渲染进行断言测试

除了`react-test-renderer`之外，Enzyme 可以处理浅渲染组件上的`onClick`事件。为了测试这一点，您必须创建一个模拟版本的函数，该函数应在组件被点击时触发。之后，Jest 可以检查该函数是否被执行。

您之前测试过的`Button`组件不仅接受`children`作为属性 - 它还接受`onClick`函数。让我们尝试看看是否可以使用 Jest 和 Enzyme 来测试这一点，通过在`Button`组件的文件中创建一个新的测试场景：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import Button from './Button';

describe('the <Button /> component', () => {
  ...

+  it('should handle the onClick event', () => {
+    const mockOnClick = jest.fn();
+    const component = shallow(<Button onClick={mockOnClick} />);

+    component.simulate('click');

+    expect(mockOnClick).toHaveBeenCalled();
+  });
});
```

在前面的测试场景中，使用 Jest 创建了一个模拟的`onClick`函数，该函数作为属性传递给了浅渲染的`Button`组件。然后，在该组件上调用了一个带有点击事件处理程序的`simulate`方法。模拟点击`Button`组件应该执行模拟的`onClick`函数，您可以通过检查该测试场景的测试结果来确认这一点。

`SubHeader`组件的测试也可以更新，因为它渲染了两个带有`onClick`事件的按钮。让我们开始吧：

1.  首先，您需要对`src/components/Header/SubHeader.js`中的`SubHeader`组件的文件进行一些更改，因为您需要导出使用`styled-components`创建的组件。通过这样做，它们可以在`SubHeader`的测试场景中用于测试：

```jsx
import React from 'react';
import styled from 'styled-components';
import Button from '../Button/Button';

const SubHeaderWrapper = styled.div`
  width: 100%;
  display: flex;
  justify-content: space-between;
  background: cornflowerBlue;
`;

- const Title = styled.h2`
+ export const Title = styled.h2`
  text-align: center;
  flex-basis: 60%;

  &:first-child {
    margin-left: 20%;
  }

  &:last-child {
    margin-right: 20%;
  }
`;

- const SubHeaderButton = styled(Button)`
+ export const SubHeaderButton = styled(Button)`
  margin: 10px 5%;
`;

...
```

1.  一旦它们被导出，我们就可以将这些组件导入到我们的`SubHeader`测试文件中：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
- import SubHeader from './SubHeader';
+ import SubHeader, { Title, SubHeaderButton } from './SubHeader';

describe('the <SubHeader /> component', () => {
    ...
```

1.  这样可以在任何测试中找到这些组件。在这种情况下，使用快照测试了`title`属性的渲染，但您也可以直接测试`SubHeader`中的`Title`组件是否正在渲染`title`属性。要测试这一点，请更改以下代码行：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import SubHeader, { Title, SubHeaderButton } from './SubHeader';

describe('the <SubHeader /> component', () => {
  it('should render with a dynamic title', () => {
+    const title = 'Test Application';
-    const component = shallow(<SubHeader title='Test Application' />);
+    const component = shallow(<SubHeader title={title} />);

-    expect(component).toMatchSnapshot();

+    expect(component.find(Title).text()).toEqual(title);
  });

  ...
```

在这里创建了一个新的常量用于`title`属性，并将其传递给`SubHeader`组件。不再使用快照作为断言，而是创建一个新的快照，尝试找到`Title`组件，并检查该组件内的文本是否等于`title`属性。

1.  除了`title` prop 之外，您还可以测试`goBack`（或`openForm`）prop。如果存在这个 prop，将渲染一个具有`goBack` prop 作为`onClick`事件的按钮。这个按钮被渲染为`SubHeaderButton`组件。在这里，我们需要改变第二个测试场景，使其具有`goBack` prop 的模拟函数，然后创建一个断言来检查渲染组件中`SubHeaderButton`的存在：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import SubHeader, { Title, SubHeaderButton } from './SubHeader';

describe('the <SubHeader /> component', () => {
  ...

  it('should render with a goback button and handle the onClick event', () => {
+    const mockGoBack = jest.fn();
-    const component = shallow(<SubHeader goBack={() => {}} />);
+    const component = shallow(<SubHeader goBack={mockGoBack} />);

-    expect(component).toMatchSnapshot();

+    const goBackButton = component.find(SubHeaderButton);
+    expect(goBackButton.exists()).toBe(true);
  });
  ...
```

1.  我们不仅要测试带有`goBack` prop 的按钮是否被渲染，还要测试一旦我们点击按钮，这个函数是否被调用。就像我们为`Button`组件测试所做的那样，我们可以模拟点击事件并检查模拟的`goBack`函数是否被调用：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import SubHeader, { Title, SubHeaderButton } from './SubHeader';

describe('the <SubHeader /> component', () => {
  ...

  it('should render with a goback button and handle the onClick event', () => {
    const mockGoBack = jest.fn();
    const component = shallow(<SubHeader goBack={mockGoBack} />);

    const goBackButton = component.find(SubHeaderButton);
    expect(goBackButton.exists()).toBe(true);

+    goBackButton.simulate('click');
+    expect(mockGoBack).toHaveBeenCalled();
  })
  ...
```

1.  如果我们用两个断言替换测试快照的断言，测试按钮的存在以及它是否触发了模拟的`openForm`函数，那么对于`openForm` prop 也可以做同样的事情。我们可以将这个添加到现有的测试场景中，也可以扩展`goBack`按钮的测试场景：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import SubHeader, { Title, SubHeaderButton } from './SubHeader';

describe('the <SubHeader /> component', () => {
  ...

-   it('should render with a goback button and handle the onClick event', () => {
+   it('should render with a buttons and handle the onClick events', () => {
    const mockGoBack = jest.fn();
+    const mockOpenForm = jest.fn();
-    //const component = shallow(<SubHeader goBack={mockGoBack} />);
+    const component = shallow(<SubHeader goBack={mockGoBack} openForm={mockOpenForm} />);

    ...
  });

-  it('should render with a form button', () => {
-    const component = shallow(<SubHeader openForm={() => {}} />);

-    expect(component).toMatchSnapshot();
-  });
});
```

1.  现在为`SubHeader`渲染的组件应该同时具有一个按钮返回到上一页和一个按钮打开表单。然而，它们都使用`SubHeaderButton`组件进行渲染。返回按钮首先在组件树中进行渲染，因为它位于`SubHeader`的左侧。因此，我们需要指定哪个渲染的`SubHeaderButton`是哪个按钮：

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import SubHeader, { Title, SubHeaderButton } from './SubHeader';

describe('the <SubHeader /> component', () => {
  ...

  it('should render with buttons and handle the onClick events', () => {
    const mockGoBack = jest.fn();
    const mockOpenForm = jest.fn();
    const component = shallow(<SubHeader goBack={mockGoBack} openForm={mockOpenForm} />);

-   const goBackButton = component.find(SubHeaderButton);
+   const goBackButton = component.find(SubHeaderButton).at(0);
    expect(goBackButton.exists()).toBe(true);

+   const openFormButton = component.find(SubHeaderButton).at(1);
+   expect(openFormButton.exists()).toBe(true)

    goBackButton.simulate('click');
    expect(mockGoBack).toHaveBeenCalled();

+    openFormButton.simulate('click');
+    expect(mockOpenForm).toHaveBeenCalled();
  });
  ...
```

在这些更改之后，所有使用快照的测试场景都被移除，并替换为更具体的测试，一旦我们改变了任何代码，它们就会变得不太脆弱。除了快照，这些测试将在我们改变任何使重构更容易的 props 时继续工作。

在这一部分，我们已经创建了单元测试，用于测试我们代码的特定部分。然而，测试不同部分的代码如何一起工作可能会很有趣。为此，我们将向我们的项目添加集成测试。

# 使用 Enzyme 进行集成测试

我们创建的所有测试都使用浅渲染来渲染组件，但是在 Enzyme 中，我们也有选项来挂载组件。使用这个选项时，我们可以启用生命周期并测试比第一级更深的更大的组件。当我们想一次测试多个组件时，这被称为集成测试。在我们的应用程序中，由路由直接渲染的组件也会渲染其他组件。`Hotels`组件就是一个很好的例子，它渲染了上下文返回的酒店列表。让我们开始吧：

1.  和往常一样，起点是在与要测试的组件位于同一目录中创建一个带有`.test`后缀的新文件。在这里，我们需要在`src/components/Hotels`目录中创建`Hotels.test.js`文件。在这个文件中，我们需要从 Enzyme 中导入`mount`，导入我们要测试的组件，并创建一个新的测试场景：

```jsx
import React from 'react';
import { mount } from 'enzyme';
import Hotels from './Hotels';

describe('the <Hotels /> component', () => {

});
```

2. `Hotels`组件使用`useContext` Hook 来获取显示酒店所需的数据。然而，由于这是针对特定组件的测试，该数据需要被模拟。在我们可以模拟这些数据之前，我们需要为`useContext` Hook 创建一个模拟函数。如果我们有多个使用此模拟的测试场景，我们还需要使用`beforeEach`和`afterEach`方法为每个场景创建和重置这个模拟函数。

```jsx
import React from 'react';
import { mount } from 'enzyme';
import Hotels from './Hotels';

+ let useContextMock;

+ beforeEach(() => {
+  useContextMock = React.useContext = jest.fn();
+ });

+ afterEach(() => {
+  useContextMock.mockReset();
+ });

describe('the <Hotels /> component', () => {
    ...
```

1.  现在我们可以使用模拟的`useContextMock`函数来生成将用作上下文的模拟数据，该数据将由`Hotels`组件使用。将返回的数据也应该是模拟的，可以通过调用可用于模拟函数的`mockReturnValue`函数来实现。如果我们看一下`Hotels`组件的实际代码，我们会发现它从上下文中获取了四个值：`loading`，`error`，`hotels`和`getHotelsRequest`。这些值应该在我们将创建的第一个测试场景中被模拟和返回，以检查上下文在加载酒店数据时的行为：

```jsx
import React from 'react';
import { mount } from 'enzyme';
import Hotels from './Hotels';

...

describe('the <Hotels /> component', () => {
  it('should handle the first mount', () => {
+    const mockContext = { 
+      loading: true,
+      error: '', 
+      hotels: [], 
+      getHotelsRequest: jest.fn(),
+    }
+    useContextMock.mockReturnValue(mockContext);
+    const wrapper = mount(<Hotels />);
+
+    expect(mockContext.getHotelsRequest).toHaveBeenCalled();
  });
});
```

这个第一个测试场景检查了`Hotels`组件在首次挂载时是否会调用上下文中的`getHotelsRequest`函数。这意味着在`Hotels`中使用的`useEffect` Hook 已经经过了测试。

1.  由于数据仍在加载中，我们还可以测试`Alert`组件是否从上下文中渲染了`loading`值并显示了加载消息。在这里，我们需要从`src/components/Hotels/Hotels.js`中导出这个组件：

```jsx
...

- const Alert = styled.span`
+ export const Alert = styled.span`
  width: 100%;
  text-align: center;
`;

const Hotels = ({ match, history }) => {
    ...
```

现在，我们可以在测试文件中导入这个组件，并编写断言来检查它是否显示了来自上下文的值：

```jsx
import React from 'react';
import { mount } from 'enzyme';
- import Hotels from './Hotels';
+ import Hotels, { Alert } from './Hotels';

...

describe('the <Hotels /> component', () => {
  it('should handle the first mount', () => {
    const mockContext = { 
      loading: true,
      error: '',
      hotels: [], 
      getHotelsRequest: jest.fn(), 
    }
    useContextMock.mockReturnValue(mockContext);
    const wrapper = mount(<Hotels />);

    expect(mockContext.getHotelsRequest).toHaveBeenCalled();
+   expect(wrapper.find(Alert).text()).toBe('Loading...');
  });
```

1.  在`Hotels`组件挂载并且数据被获取后，上下文中的`loading`、`error`和`hotels`的值将被更新。当`loading`和`error`的值为`false`时，`HotelItemsWrapper`组件将被`Hotels`渲染。为了测试这一点，我们需要从`Hotels`中导出`HotelItemsWrapper`：

```jsx
import React from 'react';
import styled from 'styled-components';
import { Link } from 'react-router-dom';
import { HotelsContext } from '../../Context/HotelsContextProvider';
import SubHeader from '../Header/SubHeader';
import HotelItem from './HotelItem';

- const HotelItemsWrapper = styled.div`
+ export const HotelItemsWrapper = styled.div`
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  margin: 2% 5%;
`;

...
```

在测试文件中，现在可以导入这个组件，这意味着我们可以添加新的测试场景，检查这个组件是否被渲染：

```jsx
import React from 'react';
import { mount } from 'enzyme';
- import Hotels, { Alert } from './Hotels';
+ import Hotels, { Alert, HotelItemsWrapper } from './Hotels';

describe('the <Hotels /> component', () => {
  ...

+  it('should render the list of hotels', () => {
+    const mockContext = {
+      loading: false,
+      error: '',
+      hotels: [{
+        id: 123,
+        title: 'Test Hotel',
+        thumbnail: 'test.jpg',
+      }],
+      getHotelsRequest: jest.fn(),
+    }
+    useContextMock.mockReturnValue(mockContext);
+    const wrapper = mount(<Hotels />);

+    expect(wrapper.find(HotelItemsWrapper).exists()).toBe(true);
+  });
});
```

现在，当我们运行测试时，会出现错误，显示“不变式失败：您不应该在<Router>之外使用<Link>”，因为 Enzyme 无法渲染`Link`组件，这是我们点击酒店时用来导航的。因此，我们需要将`Hotels`组件包装在`react-router`的路由器组件中：

```jsx
import React from 'react';
import { mount } from 'enzyme';
+ import { BrowserRouter as Router } from 'react-router-dom';
import Hotels, { Alert, HotelItemsWrapper } from './Hotels';

...

describe('the <Hotels /> component', () => {
  ...

  it('should render the list of hotels', () => {
    const mockContext = {
      loading: false,
      alert: '',
      hotels: [{
        id: 123,
        title: 'Test Hotel',
        thumbnail: 'test.jpg',
      }],
      getHotelsRequest: jest.fn(),
    }
    useContextMock.mockReturnValue(mockContext);
-    const wrapper = mount(<Hotels />);
+    const wrapper = mount(<Router><Hotels /></Router>);

    expect(wrapper.find(HotelItemsWrapper).exists()).toBe(true);
  });
});
```

这个测试现在会通过，因为 Enzyme 可以渲染组件，包括`Link`来导航到酒店。

1.  在`HotelItemsWrapper`组件内部是一个`map`函数，它遍历来自上下文的酒店数据。对于每次迭代，都会渲染一个`HotelItem`组件。在这些`HotelItem`组件中，数据将以某种方式显示，例如一个`Title`组件。我们可以测试这些组件中将显示的数据是否等于模拟的上下文数据。显示酒店标题的组件应该从`src/components/Hotels/HotelItem.js`中导出。

```jsx
- const Title = styled.h3`
+ export const Title = styled.h3`
  margin-left: 2%;
`
```

除了`HotelItem`组件，这应该被导入到`Hotels`的测试中。在测试场景中，我们现在可以检查`<HotelItem`组件是否存在，并检查这个组件是否有`Title`组件。这个组件显示的值应该等于数组`hotels`中第一行的标题的模拟上下文值：

```jsx
import React from 'react';
import { mount } from 'enzyme';
import { BrowserRouter as Router } from 'react-router-dom';
import Hotels, { Alert, HotelItemsWrapper } from './Hotels';
+ import HotelItem, { Title } from './HotelItem';

...

describe('the <Hotels /> component', () => {
  ...

  it('should render the list of hotels', () => {
    const mockContext = {
      loading: false,
      alert: '',
      hotels: [{
        id: 123,
        title: 'Test Hotel',
        thumbnail: 'test.jpg',
      }],
      getHotelsRequest: jest.fn(),
    }
    useContextMock.mockReturnValue(mockContext);
    const wrapper = mount(<Router><Hotels /></Router>);

    expect(wrapper.find(HotelItemsWrapper).exists()).toBe(true);

+   expect(wrapper.find(HotelItem).exists()).toBe(true);
+ expect(wrapper.find(HotelItem).at(0).find(Title).text()).toBe(mockContext.hotels[0].title);
  });
});
```

在使用`--coverage`标志再次运行测试之后，我们将能够看到编写此集成测试对我们的覆盖率产生了什么影响。由于集成测试不仅测试一个特定的组件，而是一次测试多个组件，因此`Hotels`的测试覆盖率将得到更新。此测试还涵盖了`HotelItem`组件，我们将能够在运行`npm run test --coverage`后的覆盖率报告中看到这一点：

```jsx
 PASS src/components/Button/Button.test.js
 PASS src/components/Header/SubHeader.test.js
 PASS src/components/Hotels/Hotels.test.js
----------------------------|----------|----------|----------|----------|-------------------|
File | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s |
----------------------------|----------|----------|----------|----------|-------------------|
All files | 13.27 | 11.24 | 12.77 | 13.73 | |
 ...
 src/components/Hotels | 100 | 83.33 | 100 | 100 | |
 HotelItem.js | 100 | 100 | 100 | 100 | |
 Hotels.js | 100 | 83.33 | 100 | 100 | 33 |
```

`Hotels`的覆盖率接近 100%。`HotelItems`的测试覆盖率也达到了 100%。这意味着我们可以跳过为`HotelItem`编写单元测试，假设我们只在`Hotels`组件中使用此组件。

相对于单元测试，集成测试的唯一缺点是它们更难编写，因为它们通常包含更复杂的逻辑。此外，由于集成测试具有更多的逻辑并将多个组件组合在一起，因此这些集成测试将运行得更慢。

# 摘要

在本章中，我们介绍了使用 Jest 结合`react-test-renderer`或 Enzyme 进行 React 应用程序测试。这两个软件包对于希望为其应用程序添加测试脚本的每个开发人员都是很好的资源，它们也与 React 很好地配合。本章讨论了为应用程序编写测试的优势，希望现在您知道如何为任何项目添加测试脚本。还展示了单元测试和集成测试之间的区别。

由于本章中测试的应用程序与前几章的应用程序具有相同的结构，因此可以将相同的测试原则应用于本书中构建的任何应用程序。

下一章将结合本书中已经使用过的许多模式和库，因为我们将使用 React、GraphQL 和 Apollo 创建一个全栈电子商务商店。

# 进一步阅读

+   Enzyme 浅渲染：[`airbnb.io/enzyme/docs/api/shallow.html`](https://airbnb.io/enzyme/docs/api/shallow.html)

+   Enzyme 挂载：[`airbnb.io/enzyme/docs/api/mount.html`](https://airbnb.io/enzyme/docs/api/mount.html)
