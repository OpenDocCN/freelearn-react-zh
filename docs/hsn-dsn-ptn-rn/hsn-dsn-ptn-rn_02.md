# 第二章：查看模式

一个非常苛刻的技能是第一次写好视图代码。这需要经验，并且在某个时候几乎变得自动化。因此，从一开始就做对是至关重要的。在本章中，我们将探讨最佳实践，并深入研究您在上一章中已经使用的 React JSX 模式。我们还将专注于更广泛的内置组件范围，其中包括输入和表单。最后，我将向您展示一个名为 linter 的好工具，它对于任何新的前端项目都是必不可少的。

在本章中，您将学习以下内容：

+   编写简洁的 JSX

+   使用常见的 React Native 内置组件

+   使用`TextInput`创建简单的表单

+   区分受控和不受控输入

+   创建错误边界

+   从代码库中消除混合物

+   设置一个代码风格指南的 linter

# 技术要求

在本章中，您将了解各种模式，以及它们的代码片段。但是，要运行它们，您将需要 Create React Native App 包。我已经将每个示例分成一个独立的应用程序，您可以在手机或模拟器上启动。

要跟着本章的示例，您将需要以下内容：

+   一个 Android/iOS 手机或模拟器

+   Git，获取示例：[`github.com/Ajdija/hands-on-design-patterns-with-react-native`](https://github.com/Ajdija/hands-on-design-patterns-with-react-native)

按照 GitHub 页面上的安装和运行说明开始。

# JSX 简介

到目前为止，我们一直在使用 JSX，但是它是什么意思呢？JSX 代表 JavaScript 扩展。它怎么能是一个扩展呢？

您可能知道，ECMAScript 也是 JavaScript 的一个扩展（有点）。ECMAScript 被转译成 JavaScript。这意味着什么？这意味着它只是将 ECMAScript 代码转换为有效的 JavaScript 代码。JavaScript 缺少我们从 ECMAScript 中喜欢的许多功能，例如箭头函数、类和解构运算符。

JSX 的工作方式也是一样的。JSX 被转译成 JavaScript，它的主要特点是根据您编写的标记创建 React 元素。

我们能只使用 JavaScript 吗？是的。值得吗？很可能不值得。

让我们看看这个实例。这是 JSX *和* ECMAScript：

```jsx
export default () => <Text style={{marginTop: 30}}>Example Text!</Text>
```

现在，将这与纯 JavaScript 进行比较：

```jsx
export default function() {
    return React.createElement(
        Text,
  {style: {marginTop: 30}},
  'Example Text!'
  ); }
```

毫无疑问，第一个代码片段更容易阅读和理解。

Babel 将 JSX 转译为 JavaScript。查看这个交互式工具，以便您可以玩耍并查看更复杂示例中的输出：[`goo.gl/RjMXKC`](https://goo.gl/RjMXKC)。

# JSX 标准技巧

在我们继续之前，我想向您展示在编写 JSX 标记时的最佳实践。这将使您在接下来的示例中更容易理解。

让我们从简单的规则开始：

+   如果您的组件内没有子元素，请使用自闭合标签：

```jsx
// good
<Button onPress={handlePress} />

// bad
<Button onPress={handlePress}></Button> 
```

+   如果需要根据某些条件显示组件，则使用`&&`运算符：

```jsx
// bad
function HelloComponent(props) {   if (isSomeCondition) {
return <p>Hello!</p>;   }
return null;  }

// bad
const HelloComponent = () => {
  return isSomeCondition ? <p>Hello!</p> : null
};

// ok (probably it will require some logic before return)
const HelloComponent = () => { return isSomeCondition && <p>Hello!</p> };

// almost good (isSomeCondition can be passed using props)
const HelloComponent = () => isSomeCondition && <p>Hello!</p>;

// best: use above solution but within encapsulating component
// this way HelloComponent is not tightly tied to isSomeCondition

const HelloComponent = () => <p>Hello!</p>;
const SomeComponent = () => (
    // <== here some component JSX ...
   isSomeCondition && <HelloComponent />
    // <== the rest of encapsulating component markup here
);
```

前述做法仅适用于其他选项为`null`的情况。如果 false 情况也是一个组件，您可以使用`*b ? x : y*`运算符，甚至是简单的`if-else`方法，但是它应符合您项目的最佳实践。

+   如果使用`*b ? x : y*`运算符，则可能会发现大括号（`{}`）很有用：

```jsx
const SomeComponent = (props) => (
<View>
 <Text>{props.isLoggedIn ? 'Log In' : 'Log Out'}</Text>
 </View> );
```

+   您还可以使用大括号（`{}`）来解构 props 对象：

```jsx
const SomeComponent = ({ isLoggedIn, ...otherProps }) => (
<View>
 <Text>{isLoggedIn ? 'Log In' : 'Log Out'}</Text>
 </View> );
```

+   如果要将`isLoggedIn`传递为`true`，只需写入 prop 名称即可：

```jsx
// recommended const OtherComponent = () => (
    <SomeComponent isLoggedIn />
);

// not recommended
const OtherComponent = () => (
    <SomeComponent isLoggedIn={true} />
);
```

+   在某些情况下，您可能希望传递所有其他 props。在这种情况下，您可以使用展开运算符：

```jsx
const SomeButton = ({ type , ...other }) => {
const className = type === "blue" ? "BlueButton" : "GrayButton";
  return <button className={className} {...other} />; }; 
```

# 初学者命名指南

命名可能听起来微不足道，但在 React 中有一些标准做法，您应该遵守。这些做法可能因项目而异，但请记住，您至少应该尊重这里提到的做法。在其他情况下，请检查您项目的样式指南，可能还有您的 linter 配置。

伟大的 React 样式指南之一来自 Airbnb，可以在[`github.com/airbnb/javascript/tree/master/react#naming`](https://github.com/airbnb/javascript/tree/master/react#naming)上查看。

组件名称应以大写字母开头，除非它是 HOC。使用组件名称作为文件名。文件名应为 UpperCamelCase（有关 CamelCase 的更多信息，请参见[`en.wikipedia.org/wiki/Camel_case`](https://en.wikipedia.org/wiki/Camel_case)）：

```jsx
// bad
someSection.js
// good
SomeSection.js or SomeSection.jsx
// Current Airbnb style guide recommends .jsx extension though.
```

以下是有关导入组件的规则：

```jsx
// bad
import App from './App/App';

// bad
import App from './App/index';

// good
import App from './App';
```

如果是 HOC，请使用小写字母的小驼峰命名法开始其名称，例如`makeExpandable`。

Airbnb 还建议您注意内部组件的名称。我们需要指定`displayName`属性，如下所示：

```jsx
// Excerpt from
// https://github.com/airbnb/javascript/tree/master/react#naming
// bad
export default function withFoo(WrappedComponent) {
 return function WithFoo(props) {
 return <WrappedComponent {...props} foo />;
  }
}

// good
export default function withFoo(WrappedComponent) {
  function WithFoo(props) {
 return <WrappedComponent {...props} foo />;
  }

  const wrappedComponentName = WrappedComponent.displayName
 || WrappedComponent.name
 || 'Component';

  WithFoo.displayName = `withFoo(${wrappedComponentName})`;
 return WithFoo;
}
```

这是一个有效的观点，因为在某些工具中，您可能会从看到正确的组件名称中受益。遵循此模式是可选的，并由团队决定。

可以创建一个 HOC 来处理`displayName` prop。这样的 HOC 可以在我们在第一章中创建的 HOC 之上重复使用，*React 组件模式*。

在定义新的 props 时，请避免使用曾经表示其他含义的常见 props。一个例子可能是我们用来将样式传递给组件的 style prop。

请查看以下链接，了解应避免使用哪些 props：

+   与您的应用程序布局对应的 Props：

+   [`facebook.github.io/react-native/docs/layout-props.html`](https://facebook.github.io/react-native/docs/layout-props.html)

+   为组件样式保留的 Props，因为它可能会造成混淆：

+   [`facebook.github.io/react-native/docs/image-style-props.html`](https://facebook.github.io/react-native/docs/image-style-props.html)

+   [`facebook.github.io/react-native/docs/text-style-props.html`](https://facebook.github.io/react-native/docs/text-style-props.html)

+   [`facebook.github.io/react-native/docs/view-style-props.html`](https://facebook.github.io/react-native/docs/view-style-props.html)

不要太害怕。迟早会感觉更自然。

# 使用 PropTypes 进行类型检查

React 带有对基本类型检查的支持。它不需要您升级到 TypeScript 或其他更高级的解决方案。要立即实现类型检查，您可以使用`prop-types`库。

让我们为`Chapter 1/Example 12`中的`HelloBox`组件提供类型定义：

```jsx
import PropTypes from 'prop-types';

// ...  HelloBox.propTypes = {
 isExpanded: PropTypes.bool.isRequired,
  expandOrCollapse: PropTypes.func.isRequired,
  containerStyles: PropTypes.object,
  expandedTextStyles: PropTypes.object }; 
```

这样，我们强制`isExpanded`为布尔类型（`true`或`false`），并且`expandOrCollapse`为函数。我们还让 React 知道两个可选的样式 props（`containerStyles`和`expandedTextStyles`）。如果未提供样式，我们将简单地返回默认样式。

在标记中还有一个很好的功能可以避免显式的`if`——默认 props。看一下：

```jsx
HelloBox.defaultProps = {
    containerStyles: styles.container,
  expandedTextStyles: styles.text }; 
```

太棒了！现在，如果`containerStyles`或`expandedTextStyles`为 null，那么它们将获得相应的默认值。但是，如果您现在运行应用程序，您会注意到一个小警告：

```jsx
Warning: Failed prop type: Invalid prop `containerStyles` of type `number` supplied to `HelloBox`, expected `object`.
```

你现在可能感到恐慌，但这是正确的。这是 React Native 团队做出的一个很好的优化，你可能不知道。它缓存样式表，只是发送缓存的 ID。以下行返回了表示传递的`styles`对象的样式表的数字和 ID：

```jsx
styles.container
```

因此，我们需要调整我们的类型定义：

```jsx
HelloBox.propTypes = {
    isExpanded: PropTypes.bool.isRequired,
  expandOrCollapse: PropTypes.func.isRequired,
  containerStyles: PropTypes.oneOfType([
 PropTypes.object,
        PropTypes.number
 ])**,**
  expandedTextStyles: PropTypes.oneOfType([
 PropTypes.object,
        PropTypes.number
 ])
};
```

现在，您可以在组件标记中删除显式的`if`语句。它应该看起来更或多如下所示：

```jsx
export const HelloBox = ({
    isExpanded,
  expandOrCollapse,
  containerStyles,
  expandedTextStyles
}) => (
    <View style={containerStyles}>
 <HelloText onPress={() => expandOrCollapse()}>...</HelloText>
 <HelloText onPress={() => expandOrCollapse()}>...</HelloText>
  {
            isExpanded &&
            <HelloText style={expandedTextStyles}>
                ...
            </HelloText>
  }
    </View> );
```

干得好！我们已经为我们的组件定义了默认属性和类型检查。请查看`src/chapter 2`目录中的完整工作`Example 2`以获取更多详细信息。

请注意，从现在开始，所有的代码示例都将被拆分成几个模块化的源文件。所有文件将放在各自示例的`./src`目录下。

例如，`Example 2`的组织方式如下：

+   `src`

+   `HelloBox.js`

+   `HelloText.js`

+   `makeExpandable.js`

+   `App.js`

这个结构将随着应用程序的发展而发展。在第十章中，*管理依赖关系*，您将学习如何在拥有一百万行代码的大型项目中组织文件。

# 您需要了解的内置组件

React Native 正在快速发展并经常变化。我已经选择了一系列组件的精选列表，这些组件可能会在 API 中长期存在。我们将花一些时间学习它们，这样我们以后在这本书中就能更快地进行下去。任何进一步的示例都将依赖于这些组件，并假设您知道这些组件的用途。

# ScrollView 组件

到目前为止，我们知道了三个组件：`View`，`Text`和`StyleSheet`。现在，想象一种情况，我们在应用程序中有很多行要显示——比如我脑海中浮现出的信息表。显然，这将是一个很长的表，但屏幕很小，所以我们将使其可滚动——上下滚动，就像在浏览器中一样。这在概念上可能看起来微不足道，但实现起来并不容易，这就是为什么 React Native 提供了`ScrollView`组件。

让我们看看这个问题是如何发生的。从`Chapter 2`文件夹中查看`Example 3_ No ScrollView problem`来开始。

在这里，我们有一个典型的`TaskList`组件，它将每个任务转换为一个`Task`组件。`Task`以`Text`的形式显示其名称和描述。这是一个非常简单的机制，但是一旦任务数量庞大，比如 20 个或更多个任务，它就会填满整个屏幕，突然间你意识到你无法像在浏览器窗口中那样滚动：

```jsx
// Chapter 2 / Example 3 / src / TaskList.js
export const TaskList = ({tasks, containerStyles}) => (
    <View style={containerStyles}>
  {tasks.map(task => // problems if task list is huge
            <ExpandableTask
  key={task.name + task.description}
                name={task.name}
                description={task.description}
            />
  )}
    </View> );
```

为了解决这个问题并使内容可滚动，将`View`替换为`ScrollView`。您还需要将`style`属性重命名为`contentContainerStyle`。请参见完整示例，如下所示：

```jsx
// Chapter 2 / Example 4 / src / TaskList.js import React from 'react'; import Task from './Task'; import PropTypes from 'prop-types'; import {StyleSheet, Text, ScrollView, View} from 'react-native'; import makeExpandable from './makeExpandable';   const ExpandableTask = makeExpandable(Task);   export const TaskList = ({tasks, containerStyles}) => (
     <**ScrollView** contentContainerStyle={containerStyles}>
  {tasks.map(task =>
             <ExpandableTask key={task.name + task.description}
                name={task.name}
                description={task.description}
             />
  )}
 </**ScrollView**> );   const styles = StyleSheet.create({
 container: {
 backgroundColor: '#fff'     }
});   TaskList.propTypes = {
 tasks: PropTypes.arrayOf(PropTypes.shape({
 name: PropTypes.string.isRequired,
 description: PropTypes.string.isRequired
  })),
  containerStyles: PropTypes.oneOfType([
         PropTypes.object,
  PropTypes.number
     ])
};   TaskList.defaultProps = {
 tasks: [],
 containerStyles: styles.container };   export default TaskList;  
```

我还包括了`PropTypes`定义，这样您就可以练习我们在上一节中学到的内容。

注意在`Task`组件上使用`key`属性（`key={task.name + task.description}`）。这在渲染集合时是必需的，以便 React 可以区分元素的属性更改，并在可能的情况下避免不必要的重绘组件。

# 图像组件

你经常会使用的下一个组件是`Image`组件。让我们用 React 标志扩展我们的任务列表。在每个任务之后，我们将展示 React 标志的.png 图片：

```jsx
// Chapter 2_View patterns/ Example 5/src /Task.js // ...
**<Image** // styles just to make it smaller in the example  style={{width: 100, height: 100}}
 source={require("./**react.png**")}
**/>**
// ... 
```

请注意，目前并非所有图像类型都受支持。例如，SVG 图像将需要一个单独的库来工作。

您可以在官方文档中查看`Image`组件消耗的 props：[`facebook.github.io/react-native/docs/image`](https://facebook.github.io/react-native/docs/image)。您会在这里找到有用的 props，比如`loadingIndicatorSource`——这是在加载大源图像时显示的图像。

# 文本输入组件

我们将在下一节经常使用这个组件。总体思路是能够从智能手机键盘传递数据。`TextInput`用于登录和注册表单以及用户需要向应用程序发送文本数据的许多其他地方。

让我们扩展第一章中的`HelloWorld`示例，“React 组件模式”，以接受一个名字：

```jsx
// Chapter 2 / Example 6 / src / TextInputExample.js
export default class TextInputExample extends React.Component {
    state = {
        name: null
  };    render = () => (
        <View style={styles.container}>
  {this.state.name && (
                <Text style={styles.text}>
  Hello {this.state.name}
                </Text>
  )}
            <Text>Hands-On Design Patterns with React Native</Text>
 <Text>Chapter 2: View Patterns</Text>
 <Text style={styles.text}>
  Enter your name below and see what happens.
            </Text>
 <TextInput  style={styles.input}
 onChangeText={name => this.setState({name})}
            **/>**
 </View>  ); }
// ... styles skipped for clarity in a book, check source files.
```

如果用户在`TextInput`组件中输入文本，那么我们会在简短的问候语中显示输入的文本。条件渲染使用`state`来检查名字是否已经定义。当用户输入时，`onChangeText`事件处理程序被调用，并且我们传递的函数会用新的名字更新状态。

有时，本地键盘可能会与您的`View`组件重叠，并隐藏重要信息。如果您的应用程序出现这种情况，请熟悉`KeyboardAvoidingView`组件。

查看[`facebook.github.io/react-native/docs/keyboardavoidingview.html`](https://facebook.github.io/react-native/docs/keyboardavoidingview.html)获取更多信息。

# 按钮组件

`Button`是一个常见的组件，你会发现自己在任何类型的应用程序中使用它。让我们用上下按钮构建一个小的`like`计数器：

```jsx
// Chapter 2 / Example 7 / src / LikeCounter.js
class LikeCounter extends React.Component {
    state = {
        likeCount: 0
  }
    // like/unlike function to increase/decrease like count in state
    like = () => this.setState({likeCount: this.state.likeCount + 1})
    unlike = () => this.setState({likeCount: this.state.likeCount - 1})

    render = () => (
        <View style={styles.container}>
 <Button  onPress={this.unlike}
                title="Unlike"
  />
 <Text style={styles.text}>{this.state.likeCount}</Text>
 <Button  onPress={this.like}
                title="Like"
  />
 </View>  ); }
// Styles omitted for clarity
```

对这个概念的进一步修改可以实现对评论的点赞/踩或者对评论的星级评价系统。

`Button`组件非常有限，习惯于 Web 开发的人可能会感到惊讶。例如，您不能以 Web 方式设置文本，例如`<Button>Like</Button>`，也不能传递样式属性。如果您需要为按钮设置样式，请使用`TouchableXXXX`。查看下一节以获取`TouchableOpacity`的示例。

# 不透明的触摸

当按钮需要自定义外观时，很快似乎需要更好的替代方案。这就是`TouchableOpacity`发挥作用的地方。当内部内容需要变得可触摸时，它可以满足任何目的。因此，我们将制作自己的按钮并根据需要进行样式设置：

```jsx
class LikeCounter extends React.Component {
    state = {
        likeCount: 0
  }
    like = () => this.setState({likeCount: this.state.likeCount + 1})
    unlike = () => this.setState({likeCount: this.state.likeCount - 1})

    render = () => (
        <View style={styles.container}>
 <TouchableOpacity  style={styles.button}
 onPress={this.unlike}
            **>**
 <Text>Unlike</Text>
 **</TouchableOpacity>** <Text style={styles.text}>{this.state.likeCount}</Text>
 <TouchableOpacity  style={styles.button}
 onPress={this.like}
            **>**
 <Text>Like</Text>
 **</TouchableOpacity>** </View>  ); }
```

以下是一些示例样式。我们将在第三章中深入探讨样式模式：

```jsx
const styles = StyleSheet.create({
    container: {
        flexDirection: 'row',
  paddingTop: 20,
  paddingLeft: 20
  },   button: {
 alignItems: 'center', // horizontally centered
  justifyContent: 'center', // vertically centered
  backgroundColor: '#DDDDDD',
  padding: 20
  }**,**
  text: {
        fontSize: 45
  }
}); 
```

按钮的内容在垂直和水平方向上都居中。我们有一个自定义的灰色背景颜色和按钮内的填充。填充是从子元素到组件边框的空间。

现在我们知道了这些简单的组件，我们准备进一步探索如何构建表单以及如何处理更复杂的用例。

# 构建表单

在本节中，我们将探讨如何处理用户的文本输入。从所谓的表单中收集输入的传统方式分为两种主要方式：受控和不受控。在本机环境中，这意味着要么在 React Native 端处理任何按键（*受控输入*），要么让其在本机系统级别上处理并根据需要在 React 中收集数据（*不受控输入*）。

如果您来自 Web 开发背景，请注意，写作本书时，没有表单组件，我也看不到它的出现。对于引用和您可以使用它们的方式也有限制。例如，您不能要求`TextInput`的引用获取其当前值。请查看以下两个小节以获取更多详细信息。您也可以使用自定义库，但我不会在这里讨论这样的解决方案，因为这些解决方案往往经常变化。

# 受控输入

受控输入是在 JavaScript 端处理所有用户输入的输入，很可能是在 React 状态或其他状态替代品中（有关更多信息，请参见第五章 *Store Patterns*）。这意味着，当用户输入时，按键在本地系统级别和 JavaScript 级别都被记住。当然，这可能是低效的，不应该在复杂的 UI 中使用，这在移动世界中似乎是罕见的。

还记得本章前面的*带有你的名字的 hello world*示例吗？这是受控输入的一个完美例子。让我们再看一遍：

```jsx
// Chapter 2_ View patterns/Example 6/src/TextInputExample.js

export default class TextInputExample extends React.Component {
    state = {
 name: null
  }**;**    render = () => (
        <View style={styles.container}>
  {this.state.name && (
                <Text style={styles.text}>
  Hello {this.state.name}
                </Text>
  )}  ...  <TextInput  style={styles.input}
                onChangeText={name => this.setState({name})}
            />
 </View>  ); }
```

我们监听文本的每一次改变（`onChangeText`），然后立即更新组件状态（`this.setState({name})`）。状态成为唯一的真相来源。我们不需要请求本地组件的值。我们只关心状态中的内容。因此，我们使用状态来显示新的`Hello`消息，以及输入的文本。

让我们看看在一个更复杂的例子中它是如何工作的。我们的任务是创建一个登录表单，其中包括登录`TextInput`、密码`TextInput`和一个显示文本为“登录”的`Button`组件。当用户按下按钮时，它应该将信息记录到我们的调试控制台。在一个真实的应用程序中，你会将登录详情传递给服务器进行验证，然后登录用户。你将在第五章 *Store Patterns*中学习如何做到这一点，当我们讨论副作用时：

```jsx
// Chapter 2 / Example 9 / src / LoginForm.js

export default class LoginForm extends React.Component {
    // Initial state for our components
  state = {
        login: this.props.initLogin || '', // remembered login or ''
  password: ''
  };
  // Submit handler when the Login button is pressed
  submit = () => {
        console.log(this.state.login);
  console.log(this.state.password);
  };    render() {
        return (
            <View style={styles.container}>
 <View> <TextInput  style={styles.input}
                        placeholder={'Login'}
                        onChangeText={login => this.setState({login})}
                    />
 </View> <View> <TextInput  style={styles.input}
                        placeholder={'Password'}
                        onChangeText={
                            password => this.setState({password})
                        }
                        secureTextEntry={true} // hide password
  />
 </View> <View> <Button  onPress={this.submit}
                        title="Login"
  />
 </View> </View>  );
  }
}
```

请注意这里的三个重要事项：

+   它提供了传递记住的登录文本的能力。完整的功能需要在物理设备内存中记住登录信息，因此我为了清晰起见省略了这一点。

+   `TextInput`的`secureTextEntry`属性可以将密码隐藏在点后面。

+   在按钮组件上设置`onPress`处理程序，以便它可以对收集到的数据进行操作。在这个简单的例子中，我们只是将日志记录到调试控制台。

# 不受控输入

React Native 中的不受控输入并不是在 Web 开发中的真实情况。事实上，`TextInput`不能完全不受控制。你需要以某种方式监听数值的变化：

+   `onChangeText`在文本输入改变时触发

+   `onSubmitEditing`在按下文本输入的提交按钮时触发

另外，`TextInput`本身就是一个受控组件。进一步查看解释。很久以前，它曾经有一个叫做`controlled`的属性，允许您指定一个布尔值，但是这已经改变了。当时的文档指定了以下内容：

"如果您真的希望它表现得像一个受控组件，您可以将其设置为 true，但是您可能会看到闪烁、丢失的按键和/或输入延迟，这取决于您如何处理 onChange 事件。"

- [`facebook.github.io/react-native/docs/0.7/textinput.html`](https://facebook.github.io/react-native/docs/0.7/textinput.html)。

我意识到 React Native 团队在解决这些问题上付出了很多努力，并且他们修复了`TextInput`。然而，`TextInput`在某种程度上变成了受控输入。例如，`TextInput`上的选择由 React Native 在`componentDidUpdate`函数中进行管理。

"选择也是一个受控属性。如果本地值与 JS 值不匹配，则更新为 JS 值。"

- TextInput 的 React Native 源代码：[`github.com/facebook/react-native/blob/c595509048cc5f6cab360cd2ccbe7c86405baf92/Libraries/Components/TextInput/TextInput.js`](https://github.com/facebook/react-native/blob/c595509048cc5f6cab360cd2ccbe7c86405baf92/Libraries/Components/TextInput/TextInput.js)。

除非您指定`onChangeText`或`value`属性，否则您的组件似乎不会有任何额外的开销。

事实上，您仍然可以使用引用。查看以下示例，了解如何使用 React 的最新 API：

```jsx
// Chapter 2 / Example 10 / App.js

export default class App extends React.Component {
    constructor(props) {
        super(props);    this.inputRef = React.createRef()**;**
  }

    render = () => (
        <TextInput style={{height:50}} ref={ref => this.inputRef = ref} **/>**
  );    componentDidMount() {
        this.inputRef.focus()**;**
  }
}
```

然而，有一些限制。您不能要求输入值的引用。可悲的是，我觉得这种情况不太可能改变。如果你从另一个角度来看，这种情况更自然。你可能只需要受控组件。目前，非受控组件的好处在于性能，并没有太大的不同。因此，我怀疑你在 React Native 中是否需要非受控组件。我甚至无法想出一个需要大量非受控组件的用例，因为性能问题。

我能做的最接近让组件独立的是使用`onSubmitEditing`或`onEndEditing`。这样的回调可以像`onChangeText`属性一样使用。它们直到用户按下本机键盘上的提交/返回按钮才会触发。不幸的是，您可能可以想象到当用户按下预期的按钮而不是按下登录按钮时的情况。在这种情况下，状态不会更新为最新数据，因为本机键盘仍然打开。这样的细微差别可能导致不正确的数据提交和关键错误。要小心。

如果您正在使用 React 开发网站，请不要因为这一部分而感到沮丧。refs 对于棕地网站非常有用，对于那些无法将现有部分重写为 React 的人也很有用。如果这是您的情况，请还要查看 React v16 的门户 API[https://reactjs.org/docs/portals.html]（https://reactjs.org/docs/portals.html）。

# 错误边界介绍

这是 React 版本 16 中带来的一个被忽视的功能。正如您应该已经知道的，JavaScript 可能会抛出错误。这样的错误不应该破坏您的应用程序，特别是如果它来自金融部门。JavaScript 的常规命令式解决方案是`try-catch`块：

```jsx
try {
    // helloWorld function can potentially throw error
    helloWorld(); } catch (error) {
    // If helloWorld throws error
    // we catch it and handle gracefully
    // ... }
```

这种方法在 JSX 中很难使用。因此，React 团队为 React 视图开发了一种替代解决方案。它被称为“错误边界”。任何类组件都可以成为`ErrorBoundary`组件，只要它实现了`componentDidCatch`函数：

```jsx
class AppErrorBoundary extends React.Component {
    state = { hasError: false };    componentDidCatch() {
        this.setState({ hasError: true });
  }

    render = () => (
        this.state.hasError
  ? <Text>Something went wrong.</Text>
  : this.props.children
  )
}

export default () => (
    <AppErrorBoundary>  <LoginForm /> </AppErrorBoundary**>** )
```

如果您跟随这些示例，您可能会看到一个带有错误的红色屏幕。这是开发模式下的默认行为。您将不得不关闭屏幕才能看到应用程序的外观：错误边界将按预期工作。如果切换到发布模式，错误屏幕将不会出现。

`LoginForm`现在被包装在`ErrorBoundary`中。它捕获渲染`LoginForm`时发生的任何错误。如果捕获到`Error`，我们会显示一个简短的消息，说明“出了点问题”。我们可以从错误对象中获取真正的错误消息。但是，与最终用户分享它并不是一个好的做法。相反，将其发送到您的分析服务器：

```jsx
// Chapter 2_View patterns/Example 11/ App.js
...
**componentDidCatch**(error) {
    this.setState({
        hasError: true,
  errorMsg: error
    }); }

render = () => (
    this.state.hasError
  ? (
            <View>
 <Text>Something went wrong.</Text>
 <Text>{this.state.errorMsg.toString()}**</Text>**
 </View>  )
        : this.props.children )
...
```

# 错误边界如何捕获错误

错误边界似乎是用来捕获阻止渲染成功完成的运行时错误的。因此，它们非常特定于 React，并且是使用类组件的特殊生命周期钩子来实现的。

错误边界不会捕获以下错误：

+   事件处理程序

+   异步代码（例如，`setTimeout`或`requestAnimationFrame`回调）

+   服务器端渲染

+   错误边界本身抛出的错误（而不是其子组件）

- React 官方文档网址：[`reactjs.org/docs/error-boundaries.html`](https://reactjs.org/docs/error-boundaries.html)。

让我们进一步讨论之前提到的错误边界的限制：

+   **事件处理程序**：这个限制是由于事件处理程序的异步性质。回调是由外部函数调用的，并且事件对象作为参数传递给回调。我们对此没有任何控制，也不知道何时会发生。代码被执行，永远不会进入 catch 子句。提示：这也以同样的方式影响`try-catch`。

+   **异步代码**：大多数异步代码不会与错误边界一起工作。这个规则的例外是异步渲染函数，这将在未来的 React 版本中推出。

+   **服务器端渲染**：这通常涉及服务器端渲染的网站。这些网站是在服务器上计算并发送到浏览器的。由于这个原因，用户可以立即看到网站的内容。大多数情况下，这样的服务器响应会被缓存和重复使用。

+   **错误边界本身抛出的错误**：您无法捕获发生在同一类组件内部的错误。因此，错误边界应该包含尽可能少的逻辑。我总是建议为它们使用单独的组件。

# 理解错误边界

错误边界可以以许多不同的方式放置，每种方法都有其自己的好处。选择适合您用例的方法。有关想法，请跳转到下一节。在这里，我们将演示应用程序根据错误边界的放置方式而表现出的行为。

这个第一个例子在`LikeCounter`组件周围使用了两个错误边界。如果其中一个`LikeCounter`组件崩溃，另一个仍然会显示出来：

```jsx
...
    <AppErrorBoundary>  <LikeCounter /> </AppErrorBoundary**>** <AppErrorBoundary>  <LikeCounter /> </AppErrorBoundary**>** **...** 
```

这第二个例子在两个`LikeCounter`组件周围使用了一个`ErrorBoundary`。如果一个崩溃，另一个也将被`ErrorBoundary`替换：

```jsx
...
    <AppErrorBoundary>  <LikeCounter /> <LikeCounter /> </AppErrorBoundary**>** **...**
```

# 何时使用错误边界

`ErrorBoundary`绝对是一个很好的模式。它将`try-catch`的概念转化为声明性的 JSX。我第一次看到它时，立刻想到将整个应用程序包装在一个边界中。这没问题，但这不是唯一的用例。

考虑错误边界的以下用例：

+   **小部件**：如果给定一些不正确的数据，您的小部件可能会遇到问题。在最坏的情况下，如果它无法处理数据，它可能会抛出错误。鉴于这个小部件对于应用程序的其余部分并不是至关重要的，您希望其余的应用程序仍然可用。您的分析代码应该收集错误并保存至少一个堆栈跟踪，以便开发人员可以修复它。

+   **模态框**：保护应用程序的其余部分免受错误模态框的影响。这些通常用于显示一些数据和简短的消息。您不希望模态框炸毁您的应用程序。这样的错误应该被认为是非常罕见的，但“宁愿安全也不要后悔”。

+   **功能容器的边界**：假设您的应用程序被划分为由容器组件表示的主要功能。例如，让我们以 Facebook Messenger 这样的消息应用为例。您可以向侧边栏、我的故事栏、页脚、开始新消息按钮和消息历史记录列表视图添加错误边界。这将确保，如果一个功能出现故障，其他功能仍有机会正常工作。

现在我们知道了所有的优点，让我们讨论一下缺点：混合。

# 为什么混合是反模式

使用混合模式，您可以将某种行为与您的 React 组件混合在一起。您可以免费注入一种行为，并且可以在不同的组件中重用相同的混合。这一切听起来都很棒，但实际上并不是这样——您很容易找到关于为什么的文章。在这里，我想通过示例向您展示这种反模式。

# 混合示例

与其大声喊叫*混合是有害的*，不如创建一个正在使用它们的组件，并查看问题所在。混合已经被弃用，因此第一步是找到一种使用它们的方法。事实证明，它们仍然以一种传统的方式创建 React 类组件。以前，除了 ES6 类之外，还有一个特殊的函数叫做`createReactClass`。在一个重大版本发布中，该函数从 React 库中删除，并且现在可以在一个名为`'create-react-class'`的单独库中使用：

```jsx
// Chapter 2_View patterns/Example 12/App.js
...
import createReactClass from **'create-react-class'**;

const LoggerMixin = {
    componentDidMount: function() { // uses lifecycle method to log
        console.log('Component has been rendered successfully!');
  }
};   export default createReactClass({
    mixins: [LoggerMixin]**,**   render: function() {
        return (
            <View>
 <Text>Some text in a component with mixin.</Text>
 </View>  );
  }
});
```

在这里，我们创建了`LoggerMixin`，它负责记录必要的信息。在这个简单的例子中，它只是关于已呈现的组件的信息，但它可以很容易地进一步扩展。

在这个例子中，我们使用了`componentDidMount`，这是组件生命周期钩子之一。这些也可以在 ES6 类中使用。请查看官方文档以了解其他方法的见解：[`reactjs.org/docs/react-component.html#the-component-lifecycle`](https://reactjs.org/docs/react-component.html#the-component-lifecycle)。

如果您需要更多的记录器，可以使用逗号将它们混合到单个组件中：

```jsx
...
mixins: [LoggerMixin, LoggerMixin2],
...
```

这是一本关于模式的书，因此在这里停下来看一下`createReactClass`函数。

为什么它已经被弃用？答案实际上非常简单。React 团队更喜欢显式 API 而不是隐式 API。`CreateReactClass`函数是另一个隐式抽象，它会隐藏实现细节。与其添加一个新函数，不如使用标准方式：ES6 类。ES6 类也有自己的缺点，但这是另一个完全不同的话题。此外，您可以在其他基于 ECMAScript 构建的语言中使用类，例如 TypeScript。这是一个巨大的优势，特别是在现今 TypeScript 变得流行的时代。

要了解更多关于这种思维过程的信息，我建议您观看 Sebastian Markbåge 的一次精彩演讲，名为**Minimal API Surface Area**。它最初是在 2014 年的 JSConf EU 上发布的，可以在[`www.youtube.com/watch?v=4anAwXYqLG8`](https://www.youtube.com/watch?v=4anAwXYqLG8)找到。

# 使用 HOC 代替

我相信您可以轻松地将前面的用例转换为 HOC。让我们一起做这个，然后我们将讨论为什么 HOC 更好：

```jsx
// Chapter 2_View patterns/ Example 13/ App.js
const withLogger = (ComponentToEnrich, logText) =>
    class WithLogger extends React.Component {
        componentDidMount = () => console.log(
            logText || 'Component has been rendered successfully!'
  );    render = () => <ComponentToEnrich {...this.props} />;
  };   const App = () => (
    <View style={styles.container}>
 <Text>Some text in a component with mixin.</Text>
 </View> );   export default withLogger(withLogger(App), 'Some other log msg');
```

您立即注意到的第一件事是 HOC 可以堆叠在一起。HOC 实际上是相互组合的。这样更加灵活，并且可以保护您免受在使用 Mixins 时可能发生的名称冲突。React 开发人员提到`handleChange`函数是一个问题示例：

*"不能保证两个特定的 mixin 可以一起使用。例如，如果`FluxListenerMixin`定义了`handleChange()`，而`WindowSizeMixin`也定义了`handleChange()`，那么您不能将它们一起使用。您也不能在自己的组件上定义一个具有这个名称的方法。*

*如果您控制 mixin 代码，这并不是什么大问题。当出现冲突时，您可以在其中一个 mixin 上重命名该方法。但是，这很棘手，因为一些组件或其他 mixin 可能已经直接调用了这个方法，您需要找到并修复这些调用。"*

*- Dan Abramov 的官方 React 博客文章（[`reactjs.org/blog/2016/07/13/mixins-considered-harmful.html`](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)).*

此外，混入可能会导致添加更多状态。从前面的例子来看，HOCs 可能会做同样的事情，但实际上不应该。这是我在 React 生态系统中遇到的问题。它给您很大的权力，您可能没有意识到您开始使用的模式是如此一般。对我来说，有状态的组件应该很少，有状态的 HOCs 也应该很少。在本书中，我将教您如何避免使用状态对象，而是更倾向于一种更好的解决方案，尽可能地将状态与组件解耦。我们将在第五章中进一步了解这一点，*存储模式*。

# 代码检查工具和代码样式指南

在本节中，我们将看一下完全不同的一组模式，即如何构建代码的模式。多年来，已经有数十种样式的方法，一般规则是：人越多，越多种偏好的方式。

因此，设置项目的**关键点**是**选择您的样式指南**，以及您定义的一套明确的规则。这将为您节省大量时间，因为它消除了任何潜在的讨论。

在高级集成开发环境的时代，可以在几秒钟内快速重新格式化整个代码库。如果您需要允许对代码样式进行小的未来更改，这将非常方便。

# 添加代码检查工具以创建 React Native 应用

按照以下步骤配置您自己的代码检查工具：

1.  打开终端并导航到项目目录。`cd`命令用于更改目录将非常方便。

1.  列出目录中的文件，并确保您位于根目录，并且可以看到`package.json`文件。

1.  使用`yarn add`命令添加以下软件包。新添加的软件包将自动添加到`package.json`中。`--dev`将其安装在`package.json`的开发依赖项中：

```jsx
yarn add --dev eslint eslint-config-airbnb eslint-plugin-import eslint-plugin-react eslint-plugin-jsx-a11y babel-eslint
```

ESLint 是我们将使用的代码检查工具，通过运行上述命令，您将已经将其安装在项目的`node_modules`目录中。

1.  现在，我们准备为您的项目定义一个新的脚本。请编辑`package.json`，并在`scripts`部分下添加以下行：

```jsx
"scripts": {
...
 "lint": "./node_modules/eslint/bin/eslint.js src"
...
}
```

前面的命令运行 ESLint 并向其传递一个参数。这个参数是将包含要进行代码检查的文件的目录的名称。如果你不打算跟着这本书一起学习，我们使用`src`目录来存储源 JavaScript 文件。

1.  下一步是指定代码风格，更准确地说，是实现您的代码风格的代码检查器配置。在本例中，我们将使用一个众所周知的 Airbnb 样式指南。但是，我们还将对其进行调整，以符合我的首选风格。

首先，通过运行以下命令创建您的代码检查器配置：

```jsx
./node_modules/eslint/bin/eslint.js --init
```

1.  接下来将出现一个特殊提示。选择以下选项：

```jsx
How would you like to configure ESLint? Use a popular style guide
Which style guide do you want to follow? Airbnb
Do you use React? Yes
What format do you want your config file to be in? JSON
```

1.  将为您创建一个名为`.eslintrc.json`的配置文件。打开文件并添加以下规则。在下一节中，我将解释这些选择。现在，请使用给定的一组规则：

```jsx
{
  "rules": {
    "react/jsx-filename-extension": [1, { "extensions": [".js"] }],
  "comma-dangle": ["error", "never"],
    "no-use-before-define": ["error", { "variables": false }],
  "indent": ["error", 4],
  "react/jsx-indent": ["error", 4],
    "react/jsx-indent-props": ["error", 4]
  },
  "parser": "babel-eslint", // usage with babel transpiler
  "extends": "airbnb" }
```

1.  现在，您可以通过使用以下命令运行代码检查器：

```jsx
yarn run lint 
```

完整的设置在`第二章 _ 视图模式`文件夹下的`示例 14`中提供。

# Airbnb React 样式指南规则

Airbnb React 样式指南定义了数十个经过深思熟虑的规则。这是一个很好的资源，也是您下一个 React 项目的基础。我强烈建议您深入研究。您可以在[`github.com/airbnb/javascript/tree/master/react`](https://github.com/airbnb/javascript/tree/master/react)找到 Airbnb React 样式指南。

但是，每个人都应该找到自己的风格。我的风格只是从 Airbnb 中调整了一些东西。

+   `comma-dangle`：Airbnb 建议您在数组多行元素、列表或对象多行键值列表的末尾留下一个逗号。这不是我习惯的。我更喜欢 JSON 样式，它不会留下尾随逗号：

```jsx
// My preference
const hero = {
  firstName: 'Dana',
  lastName: 'Scully'
};

const heroes = [
  'Batman',
  'Superman'
];

// Airbnb style guide
const hero = {
  firstName: 'Dana',
  lastName: 'Scully',
};

const heroes = [
  'Batman',
  'Superman',
];
```

+   `react/jsx-filename-extension`：在我看来，这个规则应该在样式指南中进行更改。它试图说服您在使用 JSX 的文件中使用`.jsx`扩展名。我不同意这一点。我想引用 Dan Abramov 在这个问题上的评论：

“.js 和.jsx 文件之间的区别在 Babel 之前是有用的，但现在已经不那么有用了。

还有其他语法扩展（例如 Flow）。如果使用 Flow 的 JS 文件应该如何命名？.flow.js？那使用 Flow 的 JSX 文件呢？.flow.jsx？还有其他一些实验性语法呢？.flow.stage-1.jsx？

大多数编辑器都是可配置的，因此您可以告诉它们在.js 文件中使用 JSX 语法方案。由于 JSX（或 Flow）是 JS 的严格超集，我认为这不是问题。

- Dan Abramov：[`github.com/facebook/create-react-app/issues/87#issuecomment-234627904`](https://github.com/facebook/create-react-app/issues/87#issuecomment-234627904)。

+   `no-use-before-define`：这是一个聪明的规则。它防止您使用稍后定义的变量和函数，尽管 JavaScript 的提升机制允许您这样做。但是，我喜欢将我的 StyleSheets 放在每个组件文件的底部。因此，我放宽了这个规则，允许在定义之前使用变量。

当我将片段复制到这本书中时，我也更喜欢使用四个空格的缩进来提高清晰度。

# 修复错误

由于我们已经设置了 linter，我们可以在以前的项目中尝试它。

如果您想跟着这个例子，只需从第二章中复制`Example 9_Controlled TextInput`，*View Patterns*，并在复制的项目中设置一个 linter。之后，执行以下命令，该命令在源目录上执行您的 linter 脚本。

我在`Example 9_ Controlled TextInput`的`LoginForm.js`上尝试了它。不幸的是，它列出了一些错误：

```jsx
$ yarn run lint
yarn run v1.5.1 $ ./node_modules/eslint/bin/eslint.js src

/Users/mateuszgrzesiukiewicz/Work/reactnativebook/src/Chapter 2: View patterns/Example 14: Linter/src/LoginForm.js
2:8 error    A space is required after '{' object-curly-spacing
2:44 error    A space is required before '}' object-curly-spacing
7:27 error    'initLogin' is missing in props validation    react/prop-types
12:9 warning  Unexpected console statement                  no-console
13:9 warning  Unexpected console statement                  no-console
22:37 error    Curly braces are unnecessary here             react/jsx-curly-brace-presence
23:62 error    A space is required after '{' object-curly-spacing
23:68 error    A space is required before '}' object-curly-spacing
29:37 error    Curly braces are unnecessary here             react/jsx-curly-brace-presence
31:55 error    A space is required after '{' object-curly-spacing
31:64 error    A space is required before '}' object-curly-spacing
33:25 error    Value must be omitted for boolean attributes  react/jsx-boolean-value
49:20 error    Unexpected trailing comma                     comma-dangle

 13 problems (11 errors, 2 warnings)
10 errors, 0 warnings potentially fixable with the `--fix` option.
```

13 个问题！幸运的是，ESLint 可以尝试自动修复它们。让我们试试。执行以下操作：

```jsx
$ yarn run lint -- --fix
```

很好 - 我们将问题减少到了只有三个：

```jsx
7:27 error 'initLogin' is missing in props validation react/prop-types
12:9 warning Unexpected console statement no-console
13:9 warning Unexpected console statement no-console
```

我们可以跳过最后两个。这些警告是相关的，但控制台对于这本书来说很方便：它提供了一个打印信息的简单方法。在生产中不要使用`console.log`。然而，`'initLogin'在 props 验证 react/prop-types 中丢失`是一个有效的错误，我们需要修复它：

```jsx
LoginForm.propTypes = {
    initLogin: PropTypes.string
};
```

`LoginForm`现在已经验证了它的 props。这将修复 linter 错误。要检查这一点，请重新运行 linter。看起来我们又遇到了另一个问题！正确的链接是：

```jsx
error: propType "initLogin" is not required, but has no corresponding defaultProp declaration react/require-default-props
```

这是真的 - 如果未提供`initLogin`，我们应该定义默认的 props：

```jsx
LoginForm.defaultProps = {
    initLogin: '' };
```

从现在开始，如果我们没有明确提供`initLogin`，它将被分配一个默认值，即一个空字符串。重新运行 linter。它现在会显示一个新的错误：

```jsx
error 'prop-types' should be listed in the project's dependencies. Run 'npm i -S prop-types' to add it import/no-extraneous-dependencies
```

至少这是一个简单的问题。它正确地建议您明确维护`prop-types`依赖关系。

通过在控制台中运行以下命令添加`prop-types`依赖项：

```jsx
yarn add prop-types
```

重新运行 linter。太好了！最终，没有错误了。干得好。

# 总结

在本章中，我们学习了以后在本书中会非常有用的视图模式。现在我们知道如何编写简洁的 JSX 和类型检查组件。我们还可以组合来自 React Native 库的常见内置组件。当需要时，我们可以编写简单表单的标记并知道如何处理输入。我们比较了受控和不受控输入，并深入了解了`TextInput`的工作原理。如果出现错误，我们的错误边界将处理这个问题。

最后，我们确保了我们有一个严格的风格指南，告诉我们如何编写 React Native 代码，并且我们通过使用 ESLint 来强制执行这些规则。

在下一章中，我们将致力于为我们学到的组件进行样式设置。由此，我们的应用程序将看起来漂亮而专业。
