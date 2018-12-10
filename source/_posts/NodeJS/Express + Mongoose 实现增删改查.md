---
title: Express + Mongoose 实现增删改查
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/7.jpg 
tags:
  - Express
  - Mongoose
categories: JavaScript
date: 2017-03-12
urlname: Express-Mongoose
---

<!-- ## Express + Mongoose 实现增删改查 -->

`Express` 是一个基于 `Node.js` 平台的极简、灵活的 web 应用开发框架，它提供一系列强大的特性，帮助你创建各种 Web 和移动设备应用，实现对数据库的增删查改。

数据库我们选择 `MongoDB`，它是一个基于分布式文件存储的开源数据库系统，`Mongoose` 是在 `node.js` 异步环境下对 `mongodb` 进行便捷操作的对象模型工具 。

接下来我们使用 `Express + Mongoose` 来实现简单的增删查改功能。

<!-- more -->

### 一、创建一个 Express 项目

既然 `Express` 是基于 `Node.js` 的框架，那么肯定需要装 `node.js` ，我们还需要安装 `MongoDB` , 关于 MongoDB 的使用，网上的教程有很多，我也写了一个基础的学习笔记，可以做下参考：[MongoDB 学习笔记](http://www.togoblog.cn/mongodb-basic/)。

接下来，我们使用 `express-generator` 来快速生成一个 `Express` 项目 :

```
$ npm install -g express-generator
```

然后初始化一个文件名为 express-mongoose-demo 的项目 :

```
$ express express-mongoose-demo
```

创建好项目之后，我们需要安装下依赖：

```
$ cd express-mongoose-demo
$ npm install
```

启动项目：

```
$ npm start
```

浏览器打开 [http://localhost:3000](http://localhost:3000/) ，就可以看到已经可以访问了。

### 二、介绍目录结构

```
├─bin/      // 启动文件
├─public/   // 资源文件
├─routes/   // 路由
├─views/    // 视图
├─app.js
└─package.json
```

express 创建项目的目录结构还是比较明了的。下面我们详细看看。

#### app.js

`express` 创建的项目默认使用了 `jade` 模板引擎，在 `app.js` 中的设置的 ：

```
// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');
```

`views/` 目录下都是 `.jade` 格式文件。简单说一下：

模板引擎（`Template Engine`）是一个将页面模板和数据结合起来生成 `html` 的工具。模板引擎有很多，在本学习笔记中，我们使用 [ejs 模板引擎](https://www.npmjs.com/package/ejs) ，先安装 ejs :

```
$ npm install ejs --save
```

然后在 `app.js` 中将模板引擎修改成 ejs :

```javascript
const ejs = require('ejs');
// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.engine('html', ejs.__express);
app.set('view engine', 'html');
```

`ejs` 有 3 种常用标签：

- `<% code %>`：运行 `JavaScript` 代码，不输出
- `<%= code %>`：显示转义后的 `HTML` 内容
- `<%- code %>`：显示原始 `HTML` 内容

更多使用方法这里不作详解！

### 三、搭建视图和路由

#### 创建用户列表

把 `view/` 视图目录下的 `.jade` 文件都删除，然后创建 `UserList.html` 文件，内容如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>用户列表页面</title>
  </head>
  <body>
    <table>
      <tr>
        <th>用户名</th>
        <th>邮箱</th>
        <th>操作</th>
      </tr>
      <% for(var i in user){ %>
      <tr>
        <td><%= user[i].username %></td>
        <td><%= user[i].email %></td>
        <td>
          <div>
            <a href="/users/detail/<%= user[i]._id %>"> 查看 </a>
            <a href="/users/edit/<%= user[i]._id %>"> 编辑 </a>
            <a href="#" class="del" data-id="<%= user[i]._id %>"> 删除 </a>
          </div>
        </td>
      </tr>
      <% } %>
    </table>
  </body>
</html>
```

接下来实现上面视图对应的路由，项目中默认已经给我们生成了两个路由。在 `routes/` 路由目录下已经有了两个路由文件：`index.js` 和 `users.js`。

在 `app.js` 文件中，已经帮我们设置好了这两个路由：

```javascript
var index = require('./routes/index');
var users = require('./routes/users');
app.use('/', index);
app.use('/users', users);
```

浏览器访问 <http://localhost:3000/users> ，就能访问到 `users` 对应的页面。我们来看看路由里 `users.js` 是怎么写的：

```javascript
var express = require('express');
var router = express.Router();

/* GET users listing. */
router.get('/', function(req, res, next) {
  res.send('respond with a resource');
});

module.exports = router;
```

`express.Router` 类创建模块化、可挂载的路由句柄。我们修改上面代码来创建用户列表的路由 `users/list` :

```javascript
router.get('/list', (req, res, next) => {
  const list = [{ _id: 1, username: 'Deepspace', email: 'cxin1427@qq.com' }];
  res.render('UserList', {
    user: list
  });
});
```

还记得我们新建的 `UserList.html` 视图文件中的 `user` 变量吗，这里用到了 `res.render()` 方法，功能就是渲染视图模板。

`res.render()` 方法接收两个参数：第一个参数为视图文件名，第二个参数是一个对象，用于向模板中传递数据，`user` 就是在这里传过去的。更改完路由之后我们重启服务器，访问 <http://localhost:3000/users/list> 就可以看到用户列表页面了。

![用户列表](https://github.com/IDeepspace/ImageHosting/raw/master/NodeJS/express-2.png)

但是用户列表中的数据是写死的，我们应该从数据库中获取，下面我们来做这个工作。

### 四、连接 MongoDB 数据库

#### 1. 新建 usersdb 数据库

```
> use usersdb
switch to db usersdb
```

#### 2. 在项目中安装 Mongoose

```
$ npm install mongoose --save
```

安装成功后，就可以通过 `require('mongoose')` 来使用了！

#### 3. 验证是否成功连接

我们根目录下新建一个 `models` 目录，在此目录下创建一个 `db.js` 文件，内容如下：

```javascript
const mongoose = require('mongoose'),
  DB_URL = 'mongodb://localhost:27017/usersdb';

/*连接*/
mongoose.connect(DB_URL);

/*连接成功*/
mongoose.connection.on('connected', function() {
  console.log('Mongoose connection open to ' + DB_URL);
});

/*连接异常*/
mongoose.connection.on('error', function(err) {
  console.log('Mongoose connection error: ' + err);
});

/*连接断开*/
mongoose.connection.on('disconnected', function() {
  console.log('Mongoose connection disconnected');
});
```

然后，我们运行下 `db.js` :

```javascript
$ node db.js
```

从代码中可以看出，监听了几个事件，并且执行触发了`connected` 事件，打印：`Mongoose connection open to mongodb://localhost:27017/usersdb` 则表示连接成功！

#### 4. 创建 Schema

`schema` 是 `mongoose` 里会用到的一种数据模式，可以理解为表结构的定义；每个 `schema` 会映射到 `mongodb` 中的一个 `collection`，它不具备操作数据库的能力 。

我们先改造一下 `db.js`，导出 `mongoose` 对象，在 `db.js` 文件最后一行添加如下代码即可：

```javascript
module.exports = mongoose;
```

下面我们定义一个 Schema，命名为 `userModel.js` , 内容如下：

```javascript
const mongoose = require('./db.js'),
  Schema = mongoose.Schema;

var userSchema = new Schema({
  username: { type: String }, //用户名
  email: { type: String } //邮箱
});
```

定义一个 Schema 就这么简单，指定字段名和类型就可以了。

**Schema Types 内置类型如下：**

![](https://github.com/IDeepspace/ImageHosting/raw/master/NodeJS/Schema-Types.png)

#### 5. 生成 Model

定义好了 Schema，接下就是生成 Model 。

这里的 `userSchema` 还不能对数据库进行操作，只是定义了数据模型属性 `username` , `email` 为字符串类型。需要将该 Schema 发布为 Model。**Model 是由 Schema 发布生成的模型，可以对数据库的操作。**

```javascript
module.exports = mongoose.model('user', userSchema);
```

### 五、实现增删改查

#### 1. 查询

在 `user.js` 路由文件里，我们来引入 `userModel.js` 进行数据库操作。

```javascript
const userModel = require('../models/userModel.js');

router.get('/list', function(req, res, next) {
  userModel.find(function(err, data) {
    if (err) {
      return console.log(err);
    }
    res.render('UserList', {
      user: data
    });
  });
});
```

这里使用 `userModel.find()` **查询**到所有用户。但是现在我们的数据库里还是空的，我们来新增一个添加用户页面向数据库里插入数据。

#### 2. 新增

在 `views/` 目录下新建 `UserAdd.html`添加用户视图

```
<!DOCTYPE html>
<html>
<head>
    <title>用户编辑页面</title>
</head>
<body>
    <form action="/users/add" method="post">
        <input type="text" name="username" value="">
        <input type="email" name="email" value="">
        <button type="submit">submit</button>
    </form>
</body>
</html>

```

在 `user.js` 路由文件里来添加对应视图的路由:

```javascript
router.get('/add', function(req, res, next) {
  res.render('UserAdd');
});
```

这是渲染视图页面的路由，我们需要添加一个 post 方法的路由，在点击提交按钮的时候，把数据存进数据库里。

```javascript
router.post('/add', function(req, res, next) {
  var newUser = new userModel({
    username: req.body.username,
    email: req.body.email
  });
  newUser.save(function(err, data) {
    if (err) {
      return console.log(err);
    }
    res.redirect('/users/list');
  });
});
```

这里使用 `new userModel()` 创建了一个 `Entity`，它是由 `Model` 创建的实体，它的操作也会影响数据库。`newUser` 调用 `save()` 方法将数据保存到数据库中。然后 `res.redirect()` 将页面重定向到用户列表页面，这时就可以看到我们新增的用户显示在列表中了。接下来我们看看如何来编辑用户信息。

#### 3. 修改

依然是创建相应的用户编辑视图：UserEdit.html

```html
<!DOCTYPE html>
<html>
  <head>
    <title>用户编辑页面</title>
  </head>
  <body>
    <form action="/users/update" method="post">
      <input type="hidden" name="id" value="<%= user._id %>" />
      <input type="text" name="username" value="<%= user.username %>" />
      <input type="email" name="email" value="<%= user.email %>" />
      <button type="submit">update</button>
    </form>
  </body>
</html>
```

添加对应的路由：`/users/edit/:id` 来渲染视图，`/users/update` 来修改数据库数据 :

```javascript
router.get('/edit/:id', function(req, res, next) {
  var id = req.params.id;
  userModel.findOne({ _id: id }, function(err, data) {
    res.render('UserEdit', {
      user: data
    });
  });
});

router.post('/update', function(req, res, next) {
  var id = req.body.id;
  userModel.findById(id, function(err, data) {
    if (err) {
      return console.log(err);
    }
    data.username = req.body.username;
    data.email = req.body.email;
    data.save(function(err) {
      res.redirect('/users/list');
    });
  });
});
```

`userModel.findOne()` 会根据查询条件 `{_id: id}` 查询到对应的一条数据，那么同理，查看用户详情的实现也是如此，只是渲染你到另外一个模板而已，这里就不重复写了；`userModel.findById()` 查询到 data 对象，该对象也属于 `Entity`，有 `save()` 操作。`req.body.username` 就可以获取到我们修改后的 `username`，修改 data 对象之后调用 `save()` 方法保存到数据库中。接下来看看如何删除用户吧。

#### 4. 删除

在用户列表中，点击删除按钮，就把该用户从数据库中给删除了，不需要视图，直接写路由。

```javascript
router.delete('/del', function(req, res) {
  var id = req.query.id;
  userModel.remove({ _id: id }, function(err, data) {
    if (err) {
      return console.log(err);
    }
    res.json({ code: 200, msg: '删除成功' });
  });
});
```

点击按钮，发送删除的请求，那我们可以使用 ajax 来实现。在用户列表页面引入 jquery，方便我们操作。然后添加 ajax 请求.

```javascript
$('.del').on('click', function() {
  var id = $(this).data('id');
  $.ajax({
    url: '/users/del?id=' + id,
    type: 'delete',
    success: function(res) {
      console.log(res);
    }
  });
});
```

重启服务器，进入 `users/list`，点击删除按钮，如果看到控制台中已经打印了 `{code: 200, msg: ‘删除成功’}` ，表示已经成功删除了，这时我们刷新页面，看看列表中已经不存在该用户了。

**部分路由对应的页面未创建视图或未做错误处理，但不影响学习本教程！**

### 六、总结

通过对用户的增删查改，学习如何写路由已经如何操作数据库。我们来总结一下：

1. 创建数据库，并验证是否正确连接
2. 定义 Schema，由 Schema 发布 Model 来操作数据库。
3. Model 创建的实体 Entity，可以调用 save() 方法将数据保存到数据库中。
4. `Model.find()` 方法查询到该 `Schema` 下的所有数据，`findOne()` 根据条件查询数据，`findById()` 根据 id 查询数据。
5. Model.remove() 删除数据。

### 代码地址

> https://github.com/IDeepspace/express-mongoose-demo-v1
