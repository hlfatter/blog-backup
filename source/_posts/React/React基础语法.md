---
title: React 基础知识
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/3.jpg 
tags:
  - React
categories: React
date: 2017-08-12
urlname: react-basic
---

<!-- ## React 基础知识点讲解 -->

### 零、介绍

`React.js` 是一个帮助你构建页面 `UI` 的库。如果你熟悉 `MVC` 概念的话，那么 `React` 的组件就相当于 `MVC` 里面的 `View`。如果你不熟悉也没关系，你可以简单地理解为，`React.js`将帮助我们将界面分成了各个独立的小块，每一个块就是组件，这些组件之间可以组合、嵌套，就成了我们的页面。

一个组件的显示形态和行为有可能是由某些数据决定的。而数据是可能发生改变的，这时候组件的显示形态就会发生相应的改变。而 `React.js` 也提供了一种非常高效的方式帮助我们做到了数据和组件显示形态之间的同步。

React.js 不是一个框架，它只是一个库。它只提供 `UI （view`）层面的解决方案。在实际的项目当中，它并不能解决我们所有的问题，需要结合其它的库，例如 `Redux`、`React-router` 等来协助提供完整的解决方法。

<!-- more -->

### 一、Hello World

`create-react-app` 是来自于 `Facebook` 出品的零配置命令行工具，能够帮你自动创建基于`jpgack+ES6` 的最简易的 `React` 项目模板。

```shell
npm install -g create-react-app

create-react-app my-app
cd my-app/
npm start
```

执行完上述命令之后，你可以直接打开 http://localhost:3000，即可以看到你 `React APP` 的运行效果：

![create-react-app](https://github.com/IDeepspace/ImageHosting/raw/master/React/create-react-app.png)

**目录结构：**

```
my-app
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    └── serviceWorker.js
```

一个简单 `Hello World` 的例子：

```jsx
ReactDOM.render(<h1>Hello, world!</h1>, document.getElementById('root'));
```

### 二、JSX 简介

`JSX —— React.js` 描述页面 `UI` 的方式。看起来，`JSX` 有点像模板语言，其实它是由 `React` 内部实现的。浏览器中，看到的 JSX 内容转换成 `html` 显示了出来。

#### 1、在 JSX 中使用表达式

`index.js` :

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const user = {
  firstName: 'React',
  lastName: 'Hello'
};

const formatName = user => `${user.firstName}  ${user.lastName}`;
const element = <h1>Hello, {formatName(user)}</h1>;

ReactDOM.render(element, document.getElementById('root'));
```

推荐在 JSX 代码的外面扩上一个小括号，这样可以防止 [分号自动插入](http://stackoverflow.com/q/2846283) 的 `bug`。

#### 2、JSX 本身其实也是一种表达式

在编译之后，`JSX` 其实会被转化为普通的 `JavaScript` 对象。那就可以在 `if` 或者 `for` 语句里使用 `JSX`，将它赋值给变量，当作参数传入，作为返回值也可以：

`index.js`：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const user = {
  firstName: 'E2E',
  lastName: 'team'
};

const formatName = user => `${user.firstName}  ${user.lastName}`;

const getGreeting = user => {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
};

const element = <div>{getGreeting(user)}!</div>;

ReactDOM.render(element, document.getElementById('root'));
```

#### 3、JSX 嵌套

如果 `JSX` 标签是闭合式的，那么你需要在结尾处用 `/>`, 就好像 XML/HTML 一样：

```jsx
const element = <img src={user.avatarUrl} />;
```

JSX 标签同样可以相互嵌套：

```jsx
const element = (
  <div>
    <div>头像</div>
    <h1>Hello, {formatName(user)}!</h1>
  </div>
);
```

注意：多行的 jsx 要用小括号包裹，里面如果有多个 `DOM` 节点，也需要用一个 `DOM` 节点包裹起来，所以这里加了最外面的 `div` 。

#### 4、JSX 属性

- 可以使用引号来定义以字符串为值的属性：

```jsx
const element = <div tabIndex="0" />;
```

注意：由于相对`HTML` 而言，`JSX` 更加类似于`JavaScript`, `React DOM` 使用驼峰命名代替`HTML`中的属性名。

- 也可以使用大括号来定义以 `JavaScript` 表达式为值的属性：

```jsx
const element = <img src={user.avatarUrl} />;
```

注意：使用了大括号包裹的 `JavaScript` 表达式时就不能再到外面套引号了，JSX 会将引号中的内容识别为字符串而不是表达式。

#### 5、JSX 能够防注入攻击

```jsx
render() {
    const content = 'First &middot; <i>Second</i>'
    const element = <div>{content}</div>
    return element
}
```

也就是在执行渲染前，`React DOM` 会默认将要显示的内容中有任何的标签或者脚本都会进行转义编码，按照字符串直接显示出来。 这可以避免应用被注入，可以避免`XSS`攻击。所以可以放心地在`JSX` 当中使用用户输入。

如果需要按照 `html` 显示的内容，可以使用 `dangerouslySetInnerHTML` 来实现。

```jsx
const element = <div dangerouslySetInnerHTML={{ __html: content }} />;
```

#### 6、JSX 代表 Objects

`Babel` 转译器会把 `JSX` 转换成一个名为 `React.createElement()` 的方法调用。

下面两种代码的作用是完全相同的：

```jsx
const element = <h1 className="greeting">Hello, world!</h1>;
```

```jsx
const element = React.createElement(
  'h1',
  { className: 'greeting' },
  'Hello, world!'
);
```

`React.createElement()` 这个方法首先会进行一些避免 bug 的检查，之后会返回一个类似下面例子中的对象：

```jsx
// 注意: 以下示例是简化过的（不代表在 React 源码中是这样）
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world'
  }
};
```

这样的对象被称为 **React 元素**。它代表所有可以在屏幕上看到的东西。`React` 通过读取这些对象来构建 `DOM` 并保持数据内容一致。

### 三、元素

元素（ `element` ）是一个 `React` 应用的最小组成单元。

#### 1、将元素渲染到 DOM 中

`index.js`

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const element = <h1>Hello, world</h1>;

ReactDOM.render(element, document.getElementById('root'));
```

这里 `element` 就是一个元素， 元素描述了我们在屏幕上会看到什么。`React` 元素不是组件，组件由元素构成。

可以通过 `ReactDOM.render` 把元素渲染到 `DOM` 中，`id` 为 `root` 的这个节点在 `index.html` 中。

浏览器中，可以看到 `element` 元素显示到了页面上。

#### 2、React 只会更新必要的内容

`Index.js` :

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const tick = () => {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById('root'));
};

setInterval(tick, 1000);
```

![更新必要部分](https://github.com/IDeepspace/ImageHosting/raw/master/React/granular-dom-updates.gif)

### 四、组件&Props

组件（ components ）可以让我们把 UI 分割成独立的可以复用的片段。概念上来讲，组件类似于 JS 的函数，它接收任意的输入（也就是 props ，属性），返回 React 元素。

#### 1、函数式组件

定义一个组件最简单的方式是写一个 JS 的函数：

`index.js` :

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const Welcome = props => {
  return <h1>Hello, {props.name}</h1>;
};
const element = <Welcome name="Sara" />;

ReactDOM.render(element, document.getElementById('root'));
```

这个函数就是一个完整的 React 组件，因为它接收一个 props 对象作为参数，返回一个 React 元素。这样的组件叫做函数式组件。

#### 2、class 式组件

另外一个定义组件的方式就是使用 ES6 的 class：

`index.js`：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

const element = <Welcome name="Sara" />;

ReactDOM.render(element, document.getElementById('root'));
```

从 React 的角度，上面两个组件是等价的。不过 class 式组件功能会多一些。

#### 3、组件的组合

组件可以在它的输出中引用其它组件，这就可以让我们用同一组件来抽象出任意层次的细节。在 React 应用中，按钮、表单、对话框、整个屏幕的内容等，这些通常都被表示为组件。

例如，我们可以创建一个`App`组件，用来多次渲染`Welcome`组件：

`index.js`：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

const App = () => {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
};

ReactDOM.render(<App />, document.getElementById('root'));
```

浏览器中，显示了三个 `Welcome` 。

### 五、State&生命周期

#### 1、class 式组件中才能用 state

`class` 式组件要比函数式组件功能多，使用 `state` 就是只能用在 `class` 式组件中的功能。

`index.js` :

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
const element = <Clock />;

ReactDOM.render(element, document.getElementById('root'));
```

`Clock` 是一个 `class` 式组件。里面初始化了 `state` 值。然后 `render` 函数中，显示出了这个 `state` 值。

#### 2、给 class 添加生命周期方法

每当 `Clock` 组件第一次加载到 `DOM` 中的时候，我们都想[生成定时器](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setInterval)，这在 `React` 中被称为`挂载`

同样，每当 `Clock` 生成的这个 `DOM` 被移除的时候，我们也会想要[清除定时器](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/clearInterval)，这在 `React` 中被称为`卸载`。

`index.js`：

```jsx
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class Clock extends Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }

  componentDidMount() {
    this.timerID = setInterval(this.tick, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick = () => {
    this.setState({
      date: new Date()
    });
  };

  render() {
    return (
      <div>
        <h1>Hello, World</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(<Clock />, document.getElementById('root'));
```

组件初次渲染之后，会自动执行 `componentDidMount` 这个生命周期方法，这里面我们设置一个定时器，每秒钟执行一下 `tick` 方法。这里把定时器 `id` 赋值给了 `this.timerID` 。

组件被从 `DOM` 移除的时候，会自动执行 `componentWillUnmount` ，这里面我们需要清除一下定时器，释放资源。

来定义关键的 `tick` 函数，里面的关键动作就是更新 `state` 值。注意一定要用 `this.setState` 来更新。

浏览器中，可以看到每秒钟界面显示时间都会更新。

### 六、事件处理

#### 1、基本用法

React 元素的事件处理和 DOM 元素的很相似。但是有一点语法上的不同:

- React 事件绑定属性的命名采用驼峰式写法，而不是小写；
- 如果采用 JSX 的语法，需要传入一个函数作为事件处理函数(**推荐使用 ES6 的箭头函数**)，而不是一个字符串(DOM 元素的写法)。

`index.js` :

```jsx
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class Toggle extends Component {
  constructor(props) {
    super(props);
    this.state = { isToggleOn: true };

    // This binding is necessary to make `this` work in the callback
    // this.handleClick = this.handleClick.bind(this);
  }

  handleClick = () => {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  };

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(<Toggle />, document.getElementById('root'));
```

**注意**：类的方法默认是不会[绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) `this` 的。可以将 `handleClick` 直接赋值为一个 `es6` 箭头函数，这样的好处是里面直接使用 `this` 而无需绑定。由于 `this.setState` 的异步性，所以参数不能传入对象，而要传入一个函数，才能稳妥的基于之前的状态来获得最新状态值。

#### 2、给事件处理函数传参

通常我们会为事件处理程序传递额外的参数。例如，若是 `id` 是你要删除那一行的 `id`：

`index.js` :

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

class List extends React.Component {
  deleteRow = id => {
    console.log(id);
  };

  render() {
    return <button onClick={() => this.deleteRow(2)}>Delete Row</button>;
  }
}

ReactDOM.render(<List />, document.getElementById('root'));
```

比如有一个列表，这里封装成 List 组件。里面 `deleteRow` 需要接受行号，这里就是 id ，才能知道要删除哪一行的内容。

如果 `deleteRow` 中，还想要事件对象：

```jsx
  deleteRow = (id, e) => {
    console.log(id)
  }

  render() {
    return <button onClick={e => this.deleteRow(2, e)}>Delete Row</button>
  }
```

`ES6` 参数中拿到 `e` ，把它作为第二个参数传递给 `deleteRow` 即可。

### 七、条件渲染

在 `React` 中，你可以创建不同的组件来封装各种你需要的行为。然后还可以根据应用的状态变化只渲染其中的一部分。

`React` 中的条件渲染和 `JavaScript` 中的一致，使用 `JavaScript` 操作符 [`if`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) 或[条件运算符](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)来创建表示当前状态的元素，然后让 `React` 根据它们来更新 `UI`。

#### 1、if 条件渲染

`index.js` :

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const UserGreeting = () => <h1>Welcome back!</h1>;
const GuestGreeting = () => <h1>Please sign up.</h1>;

const Greeting = props => {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
};

ReactDOM.render(
  <Greeting isLoggedIn={true} />,
  document.getElementById('root')
);
```

先定义两个函数式组件，一个是跟已经登陆的用户打招呼，另一个跟访客打招呼。下面定义 `Greeting` 组件。随着 `isLoggedIn` 的值的不同，会显示出不同的内容。

浏览器中，当 `isLoggedIn` 设置为 `true` 和 `false` ，会分别显示不同的打招呼信息。

#### 2、元素变量

你可以使用变量来储存元素。它可以帮助你有条件的渲染组件的一部分，而输出的其他部分不会更改。

`index.js` :

```jsx
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

const UserGreeting = () => <h1>Welcome Back</h1>;
const GuestGreeting = () => <h1>Please Sign Up</h1>;
const LoginButton = props => <button onClick={props.onClick}>Login</button>;
const LogoutButton = props => <button onClick={props.onClick}>Logout</button>;

const Greeting = props => {
  const { isLoggedIn } = props;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
};

class LoginControl extends Component {
  state = {
    isLoggedIn: false
  };

  handleLoginClick = () => {
    this.setState({
      isLoggedIn: true
    });
  };
  handleLogoutClick = () => {
    this.setState({
      isLoggedIn: false
    });
  };

  render() {
    const { isLoggedIn } = this.state;

    let button = null;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }
    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(<LoginControl />, document.getElementById('root'));
```

添加两个按钮组件进来，一个是登录，一个是登出。创建一个 `LoginControl` 组件，初始化 `isLoggedIn` ，添加登录和登出对应的处理函数，里面对 `isLoggedIn` 状态值进行了修改。

#### 3、与运算符 &&

`JavaScript` 的逻辑与 `&&`，它可以方便地条件渲染一个元素。

`index.js` :

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const Mailbox = props => {
  const { unreadMessages } = props;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 && (
        <h2>You have {unreadMessages.length} unread messages.</h2>
      )}
    </div>
  );
};

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```

定义 `Mailbox` 组件，属性中拿到未读邮件的数组，下面用 `&&` 号实现 `if` 的效果，如果未读邮件数量大于 0，就显示未读邮件的数量；如果数量为 0，那么大括号里面内容就求值为 `undefined` ，也就是什么都不会显示了。

#### 4、三目运算符

在下面的例子中，我们用它来有条件的渲染一小段文本：

```jsx
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}
```

同样它也可以用在较大的表达式中：

```jsx
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}
```

#### 5、阻止组件渲染

在极少数情况下，你可能希望隐藏组件，即使它被其他组件渲染。让 `render` 方法返回 `null` 而不是它的渲染结果即可实现。

`index.js` :

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const WarningBanner = props => {
  if (!props.warn) {
    return null;
  }

  return <div className="warning">Warning!</div>;
};

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = { showWarning: true };
  }

  handleToggleClick = () => {
    this.setState({
      showWarning: !this.state.showWarning
    });
  };

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
}

ReactDOM.render(<Page />, document.getElementById('root'));
```

### 八、列表 & Keys

#### 1、渲染多个组件

先看下在 `Javascript` 中如何转化列表：

我们使用 [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)函数让数组中的每一项翻倍,我们得到了一个新的数列`doubled`

```js
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(number => number * 2);
console.log(doubled);
```

在 `React`中，把数组转化为数列元素的过程是相似的：

`index.js` :

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const messages = ['hello', 'hi', 'how are you'];

const List = props => {
  const { messages } = props;

  const list = messages.map(t => <li>{t}</li>);
  return <ul>{list}</ul>;
};

ReactDOM.render(<List messages={messages} />, document.getElementById('root'));
```

注：此时打开浏览器控制台会有报错信息：`Warning: Each child in an array or iterator should have a unique "key" prop.` 。原因是每一个列表条目都应该有一个独一无二的 `key` 。

#### 2、Key

把数据的 id 作为 key 是非常常见的做法：

`index.js` ：

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

const messages = [
  {
    id: 1,
    text: 'React'
  },
  {
    id: 2,
    text: 'Re: React'
  },
  {
    id: 3,
    text: 'Re:Re: React'
  }
];

const List = props => {
  const { messages } = props;
  const list = messages.map(t => <li key={t.id}>{t.text}</li>);

  return <ul>{list}</ul>;
};

ReactDOM.render(<List messages={messages} />, document.getElementById('root'));
```

一个元素的 `key` 最好是这个元素在列表中拥有的一个独一无二的字符串。通常，我们使用来自数据的 `id` 作为元素的 key。

实际开发中的数据一般都是配有 `id` 的，将 `id` 作为 `key` 是一个很好的做法。如果用数组 `index` 作为 key 也是勉强可以的，但是由于 `index` 可能会随着数组元素的增减发生变化，如果列表可以重新排序，这会导致渲染变得很慢。

### 九、表单

当用户提交表单时，`HTML` 的默认行为会使这个表单跳转到一个新页面。在 `React` 中亦是如此。

但大多数情况下，我们都会构造一个处理提交表单并可访问用户输入表单数据的函数。实现这一点的标准方法是使用一种称为**受控组件**的技术。

#### 1、受控组件

`<input>` 或 `<select>` 都要绑定一个 `change` 事件，每当表单的状态发生变化，都会被写入组件的 `state` 中,这种组件在 `React` 中被称为受控组件。

#### 2、form 基本用法

`index.js` :

```jsx
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class Form extends Component {
  constructor(props) {
    super(props);
    this.state = { username: '' };
  }

  handleChange = e => {
    this.setState({
      username: e.target.value
    });
  };

  handleSubmit = e => {
    console.log(this.state.username);
    e.preventDefault();
  };

  render() {
    return (
      <div>
        username:
        <input
          type="text"
          value={this.state.username}
          onChange={this.handleChange}
        />
        <button onClick={this.handleSubmit}>提交</button>
      </div>
    );
  }
}

ReactDOM.render(<Form />, document.getElementById('root'));
```

由于 `value` 属性是在我们的表单元素上设置的，因此显示的值将始终为 `React` 数据源上`this.state.value` 的值。由于每次按键都会触发 `handleChange` 来更新当前 `React` 中的 `state`，所展示的值也会随着不同用户的输入而更新。

#### 3、处理多个输入

你有处理多个受控的 `input` 元素时，你可以通过给每个元素添加一个 `name` 属性，来让处理函数根据 `event.target.name` 的值来选择做什么。

```jsx
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class Form extends Component {
  constructor(props) {
    super(props);
    this.state = { username: '', email: '' };
  }

  handleChange = event => {
    const { value, name } = event.target;

    this.setState({
      [name]: value
    });
  };

  handleSubmit = e => {
    console.log(`${this.state.username} ${this.state.email}`);
    e.preventDefault();
  };

  render() {
    return (
      <div>
        Username:
        <input
          name="username"
          type="text"
          value={this.state.username}
          onChange={this.handleChange}
        />
        <br />
        Email:
        <input
          name="email"
          type="text"
          value={this.state.email}
          onChange={this.handleChange}
        />
        <br />
        <button onClick={this.handleSubmit}>提交</button>
      </div>
    );
  }
}

ReactDOM.render(<Form />, document.getElementById('root'));
```
