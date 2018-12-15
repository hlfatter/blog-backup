---
title: Redux 基础知识
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/8.jpg 
tags:
  - Redux
  - React
categories: React
date: 2017-10-12
urlname: redux-basic
---

## Redux 基础知识

核心思想：

```
(1)Web 应用是一个状态机，视图与状态是一一对应的。
(2)Redux 专注于状态管理，把所有的状态都存在一个对象中。
```

核心概念包括：`store`、`state`、`action`、`reducer`。

<!-- more -->

### 一、概念介绍

#### 1. store

`store` 就是存放数据的地方，可以把它看作是一个容器。 `Redux` 应用只有一个单一的 `store`。`redux` 提供`createStore`函数来生成 `store`，函数参数是 `reducer`(后面介绍)。

```javascript
import { createStore } from 'redux';
const store = createStore(reducer);
```

#### 2. state

state 是 store 的某个时刻的快照，可以通过 `store.getState()` 取得当前时刻的 `state`

```javascript
const state = store.getState();
```

#### 3. action

`action` 用来改变 `state`。`action` 是一个对象，其中的 `type` 属性是必须的，其他的属性一般用来设置改变 `state` 需要的数据。

```javascript
const action = {
  type: 'ADD_ONE',
  num: 1
};
```

`store.dispatch()` 是发出 `action` 的唯一方法：

```javascript
const action = {
  type: 'ADD_ONE',
  num: 1
};
store.dispatch(action);
```

#### 4. reducer

`store` 收到 `action` 以后，必须给出一个新的 `state`，这样 `view` 才会发生变化。这种 `state` 的计算过程就叫做 `reducer`。它接受 `action` 和当前 `state` 作为参数，返回一个新的 `state` 。

```javascript
import { createStore } from 'redux';
const store = createStore(reducer);
```

```javascript
const reducer = (state = 10, action) => {
  switch (action.type) {
    case 'ADD_ONE':
      return state + action.num;
    default:
      return state;
  }
};
```

当 `store.dispatch` 发送过来一个新的 `action` ，`store` 就会自动调用 `reducer`，得到新的 `state` 。

### 二、简单实例

```javascript
//第一步，创建action
const addOne = {
  type: 'ADD',
  num: 1
};
const addTwo = {
  type: 'ADD',
  num: 2
};
const square = {
  type: 'SQUARE'
};

//第二步，创建reducer
let math = (state = 10, action) => {
  switch (action.type) {
    case ADD:
      return state + action.num;
    case SQUARE:
      return state * state;
    default:
      return state;
  }
};

//第三步，创建store
import { createStore } from 'redux';
const store = createStore(math);

//第四步，测试，通过dispatch发出action，并通过getState()取得当前state值
console.log(store.getState()); //默认值为10

store.dispatch(addOne); //发起'+1'的action
console.log(store.getState()); //当前值为10+1=11

store.dispatch(square); //发起'乘方'的action
console.log(store.getState()); //当前值为11*11=121

store.dispatch(addTwo); //发起'+2'的action
console.log(store.getState()); //当前值为121+2=123
```

### 三、Redux 工作流

![Redux工作流](https://github.com/IDeepspace/ImageHosting/raw/master/React/redux-flow.jpg)

### 四、代码组织目录结构

下面对目录结构进行划分

1、一般地，将 `action.type` 设置为常量，这样有个好处：在书写错误时，会得到报错提示

```javascript
// constants/ActionTypes.js
export const ADD = 'ADD';
export const SQUARE = 'SQUARE';
```

2、可以将 `addOne` 对象和 `addTwo` 对象整合成 `add` 函数的形式

```javascript
// action/math.js
import { ADD, SQUARE } from '../constants/ActionTypes';

export const add = num => ({ type: ADD, num });
export const square = { type: SQUARE };
```

3、根据 `action.type` 的分类来拆分 `reducer` ，最终通过 `combineReducers` 方法将拆分的 `reducer` 合并起来。上例中的 `action` 类型都是数字运算，无需拆分，只需进行如下变化:

```javascript
// reducer/math.js
import { ADD, SQUARE } from '../constants/ActionTypes';

const math = (state = 10, action) => {
  switch (action.type) {
    case ADD:
      return state + action.num;
    case SQUARE:
      return state * state;
    default:
      return state;
  }
};
export default math;
```

```javascript
// reducer/index.js
import { combineReducers } from 'redux';
import math from './math';

const rootReducer = combineReducers({
  math
});
export default rootReducer;
```

4、将 `store` 存储到 `store/index.js` 文件中

```javascript
// store/index.js
import { createStore } from 'redux';
import rootReducer from '../reducer';

export default createStore(rootReducer);
```

5、最终，根路径下的 `index.js` 内容如下所示

```javascript
import store from './store';
import { add, square } from './action/math';

console.log(store.getState()); //默认值为10

store.dispatch(add(1)); //发起'+1'的action
console.log(store.getState()); //当前值为10+1=11

store.dispatch(square); //发起'乘方'的action
console.log(store.getState()); //当前值为11*11=121

store.dispatch(add(2)); //发起'+2'的action
console.log(store.getState()); //当前值为121+2=123
```

最终的目录结构：

![目录结构](https://github.com/IDeepspace/ImageHosting/raw/master/React/redux-directory-structure.png)

### 四、UI 层

前面的示例中，只是 `redux` 的状态改变，下面利用 `UI` 层来建立 `view` 和 `state` 的联系，将根目录下的`index.js` 的内容更改如下:

```javascript
import store from './store';
import React from 'react';
import ReactDOM from 'react-dom';
import { add, square } from './action/math';

ReactDOM.render(
  <div store={store}>
    <p>{store.getState().math}</p>
    <input type="button" onClick={() => store.dispatch(add(1))} value="+1" />
    <input type="button" onClick={() => store.dispatch(add(2))} value="+2" />
    <input type="button" onClick={() => store.dispatch(square)} value="乘方" />
  </div>,
  document.getElementById('root')
);
```

虽然可以显示数字，但是点击按钮时，却不能重新渲染页面。

#### 1. store.subscribe()

接下来介绍 `store.subscribe()` 方法了，该方法用来设置监听函数，一旦 `state` 发生变化，就自动执行这个函数。该方法的返回值是一个函数，调用这个函数可以解除监听。

下面将示例代码更改如下：

```javascript
import store from './store';
import React from 'react';
import ReactDOM from 'react-dom';
import { add, square } from './action/math';

const render = () =>
  ReactDOM.render(
    <div store={store}>
      <p>{store.getState().math}</p>
      <input type="button" onClick={() => store.dispatch(add(1))} value="+1" />
      <input type="button" onClick={() => store.dispatch(add(2))} value="+2" />
      <input
        type="button"
        onClick={() => store.dispatch(square)}
        value="乘方"
      />
    </div>,
    document.getElementById('root')
  );

render();
store.subscribe(render);
```

### 五、异步

`redux` 默认只处理同步，对于 `API` 请求这样的异步任务则无能为力，接下来尝试使用`axios`的`get`方法来请求下面这个`API`。

```
https://jsonplaceholder.typicode.com/posts/2
```

获取的数据如下:

```json
{
  "userId": 1,
  "id": 2,
  "title": "qui est esse",
  "body": "est rerum tempore vitae\nsequi sint nihil reprehenderit dolor beatae ea dolores neque\nfugiat blanditiis voluptate porro vel nihil molestiae ut reiciendis\nqui aperiam non debitis possimus qui neque nisi nulla"
}
```

然后，将其 `id` 值设置为 `state.math` 的值，代码修改如下：

```javascript
// constants/ActionTypes.js
export const ADD = 'ADD';
export const SQUARE = 'SQUARE';
export const SET = 'SET';

// action/math.js
import { ADD, SQUARE, SET } from '../constants/ActionTypes';
export const add = num => ({ type: ADD, num });
export const square = { type: SQUARE };
export const setNum = num => ({ type: SET, num });

// reduce/math.js
import { ADD, SQUARE, SET } from '../constants/ActionTypes';
const math = (state = 10, action) => {
  switch (action.type) {
    case ADD:
      return state + action.num;
    case SQUARE:
      return state * state;
    case SET:
      return action.num;
    default:
      return state;
  }
};
export default math;

// index.js
import store from './store';
import React from 'react';
import ReactDOM from 'react-dom';
import { add, square, setNum } from './action/math';
import axios from 'axios';
let uri = 'https://jsonplaceholder.typicode.com/posts/2';
const render = () =>
  ReactDOM.render(
    <div store={store}>
      <p>{store.getState().math}</p>
      <input
        type="button"
        onClick={() => {
          axios.get(uri).then(res => {
            store.dispatch(store.dispatch(setNum(res.data.id)));
          });
        }}
        value="设置Num"
      />
      <input type="button" onClick={() => store.dispatch(add(1))} value="+1" />
      <input type="button" onClick={() => store.dispatch(add(2))} value="+2" />
      <input
        type="button"
        onClick={() => store.dispatch(square)}
        value="乘方"
      />
    </div>,
    document.getElementById('root')
  );
render();
store.subscribe(render);
```

但是，虽然 `API` 是异步操作，但 `store.dispatch` 并不是异步，而 `axios` 通过 `get` 方法请求回来数据后，`store.dispatch` 在 `axios` 中的 `then` 方法中同步取得数据。

如果要使用真正的异步操作，即把 `axios` 方法封装到 `store.dispatch` 中，需要使用 `redux-thunk` 中间件。

#### 1. redux-thunk

首先，使用 `npm` 进行安装：

```shell
$ npm install --save redux-thunk
```

然后，使用 `applyMiddleware` 来使用 `thunk` 中间件:

```javascript
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from '../reducer';
export default createStore(rootReducer, applyMiddleware(thunk));
```

接着来定义 `setNum` 这个 `action creator` ，然后在 `index.js` 文件的 `DOM` 加载完成后就发出 `setNum`

**[注意]: **如果 `action` 是一个对象，则它就是一个 `action` ，如果 `action` 是一个函数，则它是一个`action creator` ，即 `action` 制造器，修改的代码如下：

```javascript
// action/math.js
import { ADD, SQUARE, SET } from '../constants/ActionTypes';
import axios from 'axios';
const uri = 'https://jsonplaceholder.typicode.com/posts/2';
export const add = num => ({ type: ADD, num });
export const square = { type: SQUARE };
export const setNum = () => (dispatch, getState) => {
  return axios.get(uri).then(res => {
    dispatch({
      type: SET,
      num: res.data.id
    });
  });
};

// index.js
import store from './store';
import React from 'react';
import ReactDOM from 'react-dom';
import { add, square, setNum } from './action/math';
const render = () =>
  ReactDOM.render(
    <div store={store}>
      <p>{store.getState().math}</p>
      <input
        type="button"
        onClick={() => store.dispatch(setNum())}
        value="设置Num"
      />
      <input type="button" onClick={() => store.dispatch(add(1))} value="+1" />
      <input type="button" onClick={() => store.dispatch(add(2))} value="+2" />
      <input
        type="button"
        onClick={() => store.dispatch(square)}
        value="乘方"
      />
    </div>,
    document.getElementById('root')
  );
render();
store.subscribe(render);
```

【提示信息】

如果做的更完备一点，应该把异步请求时的提示信息也加上。增加一个 `fetch` 的 `action`，用于控制`fetch` 过程的提示信息及显示隐藏情况，代码更改如下

```javascript
// action/fetch.js
import {
  SET_FETCH_MESSAGE,
  HIDE_FETCH_MESSAGE
} from '../constants/ActionTypes';
export const startFetch = {
  type: SET_FETCH_MESSAGE,
  message: '开始发送异步请求'
};
export const successFetch = {
  type: SET_FETCH_MESSAGE,
  message: '成功接收数据'
};
export const failFetch = { type: SET_FETCH_MESSAGE, message: '接收数据失败' };
export const hideFetchMessage = { type: HIDE_FETCH_MESSAGE };
```

```javascript
// action/math.js
import { ADD, SQUARE, SET } from '../constants/ActionTypes';
import { startFetch, successFetch, failFetch, hideFetchMessage } from './fetch';
import axios from 'axios';
const uri = 'https://jsonplaceholder.typicode.com/posts/2';
export const add = num => ({ type: ADD, num });
export const square = { type: SQUARE };
export const setNum = () => (dispatch, getState) => {
  dispatch(startFetch);
  setTimeout(() => {
    dispatch(hideFetchMessage);
  }, 500);
  return axios
    .get(uri)
    .then(res => {
      setTimeout(() => {
        dispatch(successFetch);
        setTimeout(() => {
          dispatch(hideFetchMessage);
        }, 500);
        dispatch({ type: SET, num: res.data.id });
      }, 1000);
    })
    .catch(err => {
      dispatch(failFetch);
      setTimeout(() => {
        dispatch(hideFetchMessage);
      }, 500);
    });
};
```

```javascript
// constants/ActionTypes.js
export const ADD = 'ADD';
export const SQUARE = 'SQUARE';
export const SET = 'SET';
export const SET_FETCH_MESSAGE = 'SET_FETCH_MESSAGE';
export const HIDE_FETCH_MESSAGE = 'HIDE_FETCH_MESSAGE';
```

```javascript
// reduce/fetch.js
import {
  SET_FETCH_MESSAGE,
  HIDE_FETCH_MESSAGE
} from '../constants/ActionTypes';
const initState = {
  message: '',
  isShow: false
};
const fetch = (state = initState, action) => {
  switch (action.type) {
    case SET_FETCH_MESSAGE:
      return { isShow: true, message: action.message };
    case HIDE_FETCH_MESSAGE:
      return { isShow: false, message: '' };
    default:
      return state;
  }
};
export default fetch;
```

```javascript
// index.js
import store from './store';
import React from 'react';
import ReactDOM from 'react-dom';
import { add, square, setNum } from './action/math';
const render = () =>
  ReactDOM.render(
    <div store={store}>
      <p>{store.getState().math}</p>
      <input
        type="button"
        onClick={() => store.dispatch(setNum())}
        value="设置Num"
      />
      <input type="button" onClick={() => store.dispatch(add(1))} value="+1" />
      <input type="button" onClick={() => store.dispatch(add(2))} value="+2" />
      <input
        type="button"
        onClick={() => store.dispatch(square)}
        value="乘方"
      />
      {store.getState().fetch.isShow && <p>{store.getState().fetch.message}</p>}
    </div>,
    document.getElementById('root')
  );
render();
store.subscribe(render);
```

### 六、React-Redux 基础知识点

前面的代码中，我们是通过 `store.subscribe()` 方法监控 `state` 状态的变化来更新 `UI` 层的。而使用 `react-redux`，可以让组件动态订阅状态树。状态树一旦被修改，组件能自动刷新显示最新数据。

`react-redux` 将所有组件分成两大类：展示组件和容器组件。展示组件只负责 `UI` 呈现，所有数据由参数 `props` 提供；容器组件则负责管理数据和业务逻辑，带有内部状态，可使用 `redux` 的 `API` 。要使用 `react-redux`，就要遵守它的组件拆分规范。

#### 1. provider

`react-redux` 提供 `Provider` 组件，可以让容器组件默认可以拿到 `state`，而不用当容器组件层级很深时，一级级将 `state` 传下去。

将 `index.js` 文件更改如下：

```javascript
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import store from './store';
import MathContainer from './container/MathContainer';
import { Provider } from 'react-redux';
ReactDOM.render(
  <Provider store={store}>
    <MathContainer />
  </Provider>,
  document.getElementById('root')
);
```

按照组件拆分规范，将原来 `index.js` 中相关代码，分拆到 `container/MathContainer` 和 `component/Math` 这两个组件中。

#### 2. connect

`react-redux` 提供 `connect` 方法，用于从展示组件生成容器组件。`connect` 的意思就是将这两种组件(容器组件和展示组件)连接起来：

```javascript
import { connect } from 'react-redux';
const MathContainer = connect()(Math);
```

`Math` 是展示组件，`MathContainer` 就是由 `React-redux` 通过 `connect` 方法自动生成的容器组件.

为了定义业务逻辑，需要给出下面两方面的信息：

- 输入逻辑：外部的数据(即`state`对象)如何转换为展示组件的参数

- 输出逻辑：用户发出的动作如何变为 `Action` 对象，从展示组件传出去

因此，`connect` 方法的完整 `API` 如下:

```javascript
import { connect } from 'react-redux';
const MathContainer = connect(
  mapStateToProps,
  mapDispatchToProps
)(Math);
```

上面代码中，`connect` 方法接受两个参数：`mapStateToProps` 和 `mapDispatchToProps`。它们定义了展示组件的业务逻辑。前者负责输入逻辑，即将 `state` 映射到 `UI` 组件的参数(`props`)，后者负责输出逻辑，即将用户对展示组件的操作映射成 `Action`，下面分别介绍这两个参数。

#### 3. mapStateToProps()

`mapStateToProps` 建立一个从外部的 `state` 对象到展示组件的 `props` 对象的映射关系。作为参数，`mapStateToProps` 执行后应该返回一个对象，里面的每一个键值对就是一个映射。

```javascript
const mapStateToProps = state => {
  return {
    num: getNum(state)
  };
};
```

`mapStateToProps` 的第一个参数总是 `state` 对象，还可以使用第二个参数，代表容器组件的 `props` 对象。使用 `ownProps` 作为参数后，如果容器组件的参数发生变化，也会引发展示组件重新渲染。

```javascript
const mapStateToProps = (state, ownProps) => {
  return {
    num: getNum(state)
  };
};
```

`mapStateToProps` 会订阅 `Store` ，每当 `state` 更新的时候，就会自动执行，重新计算展示组件的参数，从而触发展示组件的重新渲染。`connect` 方法可以省略 `mapStateToProps` 参数，那样，展示组件就不会订阅 `Store`，就是说 `Store` 的更新不会引起展示组件的更新。

#### 4. mapDispatchToProps

`mapDispatchToProps` 是 `connect` 函数的第二个参数，用来建立展示组件的参数到 `store.dispatch` 方法的映射。也就是说，它定义了用户的哪些操作应该当作 `action` ，传给 `Store` 。它可以是一个函数，也可以是一个对象。

如果 `mapDispatchToProps` 是一个函数，会得到 `dispatch` 和 `ownProps` (容器组件的 `props` 对象)两个参数。

```javascript
const mapDispatchToProps = (dispatch, ownProps) => {
  return {
    onSetNumClick: () => dispatch(setNum())
  };
};
```

`mapDispatchToProps` 作为函数，应该返回一个对象，该对象的每个键值对都是一个映射，定义了展示组件的参数怎样发出 `action`。

如果 `mapDispatchToProps` 是一个对象，它的每个键名也是对应展示组件的同名参数，键值应该是一个函数，会被当作 `action creator`，返回的 `action` 会由 `redux` 自动发出。

因此，上面的写法简写如下所示：

```javascript
const mapDispatchToProps = {
  onsetNumClick: () => setNum()
};
```

所以，最终的目录结构就变成下面这个样子（你也可以把 `store.js` 放在单独的 `store` 文件夹里面）：

![目录结构](https://github.com/IDeepspace/ImageHosting/raw/master/React/redux-folder-structure.png)
