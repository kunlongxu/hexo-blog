---
title: webpack小记要1
date: 2018-04-10 14:44:30
tags:
---

这两天都在折腾 webpack，以前只是简单的能看懂，现在全要自己折腾，发现还是有很多的坑的，特别是在 ts 下。

> 以下主要是针对 webpack4 的配置情况

## 开发环境主要使用的插件

### html-webpack-plugin

这个插件主要是用来自动把打包好的 js 插入 html 模版中，但是和 webpack4 配合会有一个 bug,在打包后终端会显示一行
`Entrypoint undefined = index.html`,不过可以不用在意这个问题

### NamedModulesPlugin

这个是 webpack 自带的插件主要用来在 hrm 更新时候在浏览器的 console 里显示文件名，而不是 id，便于调试

### (Hot Module Replacement)热模块替换

这个是 webpack 进行热替换的，内置插件。

* 在命令行中运行，增加`--hot`指令即可
* 在 Nodejs API 中运行 inline 模式需要做以下三点
  * 在`entry`中添加`webpack/hot/dev-server`或`webpack/hot/only-dev-server`
  * 在`plugins`中添加`new webpack.HotModuleReplacementPlugin()`
  * 在`webpack-dev-server`的配置中添加`hot:true`

### 自动刷新

自动刷新和 HMR 是两样东西，不要搞混了,自动刷新有两种模式。
_ iframe 模式不需要额外配置，要以下面这个 URL 格式访问即可
`http://«host»:«port»/webpack-dev-server/«path»`
_ inline 模式
_ 以命令行方式使用，需要做两点
_ 在合令行中添加`--inline`
_ 在`webpakc.config.js`中添加`devServer:{inline:true}`
_ 当以 Node.js API 启动`webpack-dev-server`时，也需要做两点：
_ 由于`webpack-dev-server`的配置中无`inline`选项,我们需要添加`webpack-dev-server/client?http://«path»:«port»/`到 webpack 配置的 entry 入口点中.
_ 将`<script src="http://localhost:8080/webpack-dev-server.js"></script>`添加到 html 文件中---一  般会由`html-webpack-plugin`自动完成

### react-hot-loader

其实配置  热加载的主要坑还是这个。

* 使用 react-hot-laoder 必须要用 babel-loader
  如果所以涉及到一个顺序问题如是这个顺序 [ts,babel]会有以下问题
* babel-loader 使用的仍然是 babel-core 这个包，但是这个包是 babel6 的包
* 如果按照官方给出的配置建义，则会出现报错，因为 github 仓库给的配置是基于 babel7 的，而 babel7 的是  使用@bable/core 这个包的。

如是这个顺序虽然没有问题，但是最后使用的是 babel 编绎出来的，同样不完善
[babel,ts]

> 所以具体怎么解决还看大家的选择

另外一个问题是关于 react-router 的
github 上建义这种这方式导出

```js
// ./containers/App.js
import React from "react";
import { hot } from "react-hot-loader";

const App = () => <div>Hello World!</div>;

export default hot(module)(App);
```

但是不能包裹`react-router`的`Router`组件， 不然会报错
