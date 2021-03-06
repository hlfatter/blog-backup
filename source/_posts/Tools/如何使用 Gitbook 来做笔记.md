---
title: 如何使用 Gitbook 来做笔记?
author: Deepspace
top: true
img: https://media-1252448650.cos.ap-guangzhou.myqcloud.com/ImageHosting/bg/6.jpg 
tags:
  - Gitbook
categories: Tool
date: 2017-06-03
urlname: notes-with-gitbook
---

<!-- ## 如何使用 Gitbook 来做笔记? -->

根据[官网说明](https://github.com/GitbookIO/gitbook/blob/master/docs/setup.md) 第一步，先安装

```bash
$ npm install gitbook-cli -g
```

然后，创建一个笔记文件夹

```bash
$ mkdir my-note
```

<!-- more -->

然后执行

```bash
$ cd my-note
$ gitbook init
```

这样，可以生成两个文件

- `README.md` 的内容会显示在书皮上
- `SUMMARY.md` 是目录

### 一、启动服务器，查看和编辑书籍

```bash
$ gitbook serve
```

这样，可以启动一个服务器，然后到 `localhost:4000` 端口，就可以看到这本书了。

可以修改 SUMMARY.md 来添加书籍目录

```bash
# Summary

* [Introduction](README.md)
* 第一章
  - [第一小节：学习 Git](./git/1-hello.md)
  - [第二小节：Git 本地工作流](./git/2-local-git.md)
  - [第三小节：Github 基本操作](./git/3-github.md)
```

创建 git 文件夹，然后里面就可以写笔记了。其实 gitbook 本身的使用技巧基本就是这些了。

### 二、托管我的 gitbook

首先到 `github.com` 上创建 `my-note` 仓库。

为了部署方便，我们把我们的 `my-note` 的内容结构稍微调整一下，把原有的所有内容都放到 content 文件夹中，也就是有这样的目录结构

```bash
➜  my-note ls content
README.md  SUMMARY.md git
➜  my-note
```

然后，把当然项目变成一个 `nodejs` 的项目：

```bash
$ cd my-note
$ npm init
```

然后，`package.json` 中添加这些代码：

```json
"scripts": {
 "start": "gitbook serve ./content ./gh-pages",
 "build": "gitbook build ./content ./gh-pages",
 "deploy": "node ./scripts/deploy-gh-pages.js",
 "publish": "npm run build && npm run deploy",
 "port": "lsof -i :35729"
},
```

有了上面的 `npm` 脚本之后，我们如果我想在本地 `4000` 端口查看本书，需要运行:

```bash
$ npm start
```

在准备上传之前，先来创建一个 `.gitignore` 文件，里面填写

```text
gh-pages
```

然后，运行:

```bash
$ git init
$ git add -A
$ git commit -a -m"hello my book"
$ git remote add origin git@github.com:IDeepspace/my-note.git
$ git push -u origin master
```

上面这些完成后，`gitbook` 的原始代码就被安全的备份到 `master` 分支了。访问 <http://IDeepspace.github.io/my-note> 可以看到这些内容。

### 三、部署书籍到 gh-pages

这一步，可以手动做：

- 第一步：运行 `npm run build` ，来把 `md` 文件翻译成 `html` 放到 `gh-pages` 文件夹
- 第二步，拷贝 `gh-pages` 中的所有文件，到本仓库的 `gh-pages` 分支，然后上传
- 第三步，以后每次修改完都需要拷贝到 `gh-pages` 分支，很麻烦

所以，我们采用一个 `npm` 包，来帮助我们完成上面的操作

```bash
$ cd my-note/
$ npm i --save gh-pages
```

然后创建 `my-note/scripts/deploy-gh-pages.js`

里面的内容是：

```javascript
'use strict';

var ghpages = require('gh-pages');

main();

function main() {
  ghpages.publish('./gh-pages', console.error.bind(console));
}
```

这样，每次书稿有了修改，运行

```bash
$ npm run publish
```

就可以把书稿部署到 <http://IDeepspace.github.io/my-note>
