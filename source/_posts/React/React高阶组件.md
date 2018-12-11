---
title: React 高阶组件
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/7.jpg 
tags:
  - React
categories: React
date: 2017-08-12
urlname: react-high-order-component
---

## React 高阶组件

高阶组件 `（HOC）` 是 `react` 中对组件逻辑进行重用的高级技术。高阶组件本身并不是 React API，它只是一种模式，这种模式是由 react 自身的组合性质产生的。

具体而言， **高阶组件就是一个函数（而不是组件），且该函数接受一个你传递给它的组件作为参数，并返回一个新的组件** 。

```jsx
const NewComponent = higherOrderComponent(OldComponent);
```

<!-- more -->

### 一、定义高阶组件

我们来看一个很简单的高级组件：

```jsx
import React, { Component } from 'react';

export default WrappedComponent => {
  class NewComponent extends Component {
    // do some things
    render() {
      return <WrappedComponent />;
    }
  }
  return NewComponent;
};
```

看起来这个高级组件什么用都没有，它就是简单的构建了一个新的组件类 `NewComponent` ，然后把传进入去的 `WrappedComponent` 渲染出来。但是我们可以给 `NewCompoent` 做一些数据启动工作：

```jsx
import React, { Component } from 'react';

export default (WrappedComponent, name) => {
  class NewComponent extends Component {
    constructor() {
      super();
      this.state = { data: null };
    }

    componentWillMount() {
      let data = localStorage.getItem(name);
      this.setState({ data });
    }

    render() {
      return <WrappedComponent data={this.state.data} />;
    }
  }
  return NewComponent;
};
```

现在 `NewComponent` 会根据第二个参数 `name` 在组件挂载阶段从 `LocalStorage` 中加载数据，并且 `setState` 到自己的`state.data` 中，而渲染的时候将 `state.data` 通过 `props.data` 传给 `WrappedComponent`。

### 二、使用高级组件

假设上面的代码是在 `src/wrapWithLoadData.js` 文件中的，我们可以在别的地方这么用它：

```jsx
import wrapWithLoadData from './wrapWithLoadData';

class InputWithUserName extends Component {
  render() {
    return <input value={this.props.data} />;
  }
}

InputWithUserName = wrapWithLoadData(InputWithUserName, 'username');
export default InputWithUserName;
```

假如 `InputWithUserName` 的功能需求是挂载的时候从 `LocalStorage` 里面加载`username`字段作为 `<input />` 的`value` 值，现在有了 `wrapWithLoadData`，我们可以很容易地做到这件事情。

只需要定义一个非常简单的 `InputWithUserName`，它会把 `props.data` 作为 `<input />` 的 `value` 值。然把这个组件和 `'username'` 传给 `wrapWithLoadData`，`wrapWithLoadData` 会返回一个新的组件，我们用这个新的组件覆盖原来的 `InputWithUserName`，然后再导出去模块。

这样，别人在使用 `InputWithUserName` 这个组件的时候，实际上是用了被加工的组件。根据 `wrapWithLoadData` 的代码我们可以知道，这个新的组件挂载的时候会先去 `LocalStorage` 加载数据，渲染的时候再通过 `props.data` 传给真正的 `InputWithUserName`。

然后，我又写了一个文本输入框组件，该组件遵循类似的模式：它也需要 LocalStorage 加载 `'content'` 字段的数据。我们只需要定义一个新的 `TextareaWithContent`：

```jsx
import wrapWithLoadData from './wrapWithLoadData';

class TextareaWithContent extends Component {
  render() {
    return <textarea value={this.props.data} />;
  }
}

TextareaWithContent = wrapWithLoadData(TextareaWithContent, 'content');
export default TextareaWithContent;
```

我们根本不需要重复写从 `LocalStorage` 加载数据字段的逻辑，直接用 `wrapWithLoadData` 包装一下就可以了。

### 三、高阶组件的灵活性

高阶组件的这种方式其实就是设计模式里面的装饰者模式。它通过组合的方式达到很高的灵活程度。

假设现在我们需求变化了，现在要的是通过 `Ajax` 加载数据而不是从 `LocalStorage` 加载数据。我们只需要新建一个`wrapWithAjaxData` 高阶组件：

```jsx
import React, { Component } from 'react';

export default (WrappedComponent, name) => {
  class NewComponent extends Component {
    constructor() {
      super();
      this.state = { data: null };
    }

    componentWillMount() {
      ajax.get('/data/' + name, data => {
        this.setState({ data });
      });
    }

    render() {
      return <WrappedComponent data={this.state.data} />;
    }
  }
  return NewComponent;
};
```

其实就是改了一下 `wrapWithLoadData` 的 `componentWillMount` 中的逻辑，改成了从服务器加载数据。现在只需要把`InputWithUserName` 稍微改一下：

```jsx
import wrapWithAjaxData from './wrapWithAjaxData';

class InputWithUserName extends Component {
  render() {
    return <input value={this.props.data} />;
  }
}

InputWithUserName = wrapWithAjaxData(InputWithUserName, 'username');
export default InputWithUserName;
```

只要改一下包装的高阶组件就可以达到需要的效果。而且我们并没有改动 `InputWithUserName` 组件内部的任何逻辑，也没有改动 `Index` 的任何逻辑，只是改动了中间的高阶组件函数。
