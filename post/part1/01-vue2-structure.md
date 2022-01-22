# Vue 项目初识

上篇文章创建了一个初始的 Vue2 项目，本文对这个几乎空白的项目框架进行简要介绍。

> [!TIP|style:flat]
> 阅读本文时并不要求理解各部分是如何编写的，只大致了解它们的用途，在下一篇文章将用一个实例来展示如何使用。

<img src="/img/post-intro/structure.png" alt="intro" target="_blank" style="width: 350px">

| 目录/文件 | 说明 |
| - | - |
| node_modules | npm 加载的项目依赖模块 |
| public | 静态资源，build 构建后为根目录，含网站导航栏图标、首页入口文件 |
| src | 开发做的事情基本都在这个目录下，含：<br>&nbsp;• assets: 放置一些图片、字体等资源 <br>&nbsp;• components: 放置组件文件，一般为全局组件<br>&nbsp;• router: 网站路由跳转设置<br>&nbsp;• store: 前端数据存储<br>&nbsp;• views: 放置各页面文件<br>&nbsp;• App.vue: 项目入口文件<br>&nbsp;• main.js: 项目的核心文件，在这里可以导入各种全局依赖 |
| .xxx文件 | 配置文件，包括语法配置、git配置(.gitignore)等 |
| package.json | 项目配置文件 |
| README.md | 项目的说明文档 |

下面对主要模块进行讲解。

## node_modules

node_modules 目录下放置项目所需要的依赖环境，当使用 `npm install packageName --save` 命令给项目添加依赖时，信息会记录于 `package.json` 文件的 dependencies 中，而依赖的包将存储于 node_modules。

在该目录下，可以发现有我们在创建项目时选择的 vue-router 包，这是用于管理路由跳转的依赖。

在编辑器中可以看到该目录名字显示灰色的，因为它在 `.gitignore` 是被忽略的。在合作开发项目中，往往使用 git 来协调配合，而该目录内容庞大，且并非由程序员编写，因此往往不加入 git 中，在新机器上使用 `npm install`，则会根据 `package.json` 的配置信息生成 node_modules。

## public

初始项目中，public 目录下有 `favicon.ico` 和 `index.html` 两个文件，前者是网站图标logo，后者是网站首页入口文件，代码如下：

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

上述代码包含了一个网页最基本的信息，在 `head` 标签中，我们可以编写网站的标题title、编码charset、图标icon、描述description、关键词keywords等信息；在 `body` 标签中，vue 项目通常只需要包含 `<div id="app"></div>`，表示 `App.vue` 文件。

## src

### assets

assets 目录放置项目所需要的资源，如图片、字体等，并由 vue 文件使用。

### components

components 目录放置项目的全局组件。

在项目开发中，存在部分小模块需要多次复用，比如导航栏、某些样式的按钮等，通常被提取出来作为全局组件。与之对应的是局部组件，通常是某个页面内容较多，使用多个 vue 文件编写，其中某个 vue 文件和其它文件为父子关系，父引入子，实现低耦合的特性。

初始项目中，该目录下有 `HelloWorld.vue` 文件，可以看到在 `views/Home.vue` 中，引入了该文件。

> 在这里也简略说明一下 vue 文件的组成。vue 文件主要由三部分组成：
> 
> * template：html，网页结构内容
> * script：该vue文件所需的数据对象格式、js方法等
> * style：css页面样式

### router

`router/index.js` 编写路由规则，即赋予 vue 文件相应的相对路由，核心代码为：

```javascript
const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: () => import('../views/About.vue')
  }
]
```

其中，`path` 为分配的相对路由，`component` 指定 vue 视图页面。

初始项目运行在 `localhost:8080` 时，访问 `localhost:8080/about` 将显示 `About.vue` 的内容。

### store

store 目录下主要放置前端存储数据的格式，利用 vuex 和 localStorage 在用户的网页端保存基本的用户信息。例如用户登录之后，将用户名存于前端，这样在渲染网页时可以直接使用，且向后端发送请求时可以携带用户名，以验证用户的登录信息是否有效。

### views

在 views 目录下，主要是页面或局部组件的 vue 文件。看初始项目，里面有 `About.vue` 和 `Home.vue`，分别对应路由 `/about` 和 `/`。简单看一下这两个文件：

* `About.vue`：内容很简单，仅包含 html 部分，仅仅是一句话。

```html
<template>
  <div class="about">
    <h1>This is an about page</h1>
  </div>
</template>
```

* `Home.vue`：包含 html 和 script 两部分，可以看到在 script 标签中引入了 `HelloWorld` 组件，然后在 html 部分中进行调用，这样该页面的效果为，`logo` 图片加 `HelloWorld` 组件。

```html
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'Home',
  components: {
    HelloWorld
  }
}
</script>
```

### App.vue

`App.vue` 为项目入口文件，该文件描述整个网站将渲染的内容。

```html
<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view/>
  </div>
</template>
```

如上述代码，它由 `nav` 的代码块和 `router-view` 组成。前者是两个超链接，有点类似于网站的导航栏，将在各路由下都展示；后者表示根据路由渲染对应的 vue 组件，如果当前是 `/about` 则渲染 `About.vue` 文件描述的内容。

### main.js

`main.js` 是项目的核心文件，在这里可以导入各种全局依赖。比如全局导入 <a href="https://element.eleme.cn/#/zh-CN" target="_blank">elementUI</a> 等UI组件或 <a href="https://echarts.apache.org/zh/index.html" target="_blank">ECharts</a> 可视化图标库等，`npm install` 后将在该文件中全局引入。

---

以上介绍了 vue2 项目创建初始的各模块用途，掺杂了部分代码编写的指示，并不要求完全理解。在下一篇文章中，将会在初始项目的基础上编写一个登录页面，以帮助初学者更快入门。

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
<script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>
<div id="gitalk-container"></div>
<script>
  var gitalk = new Gitalk({
    "clientID": "27273cfa4e0ffa52e2ac",
    "clientSecret": "ce2b2e78b2cd9dca945adf4d65a3b99248c7b2c4",
    "repo": "Vuebook",
    "owner": "Super-BUAA-2021",
    "admin": ["Super-BUAA-2021","ZewanHuang"],
    "id": window.location.pathname,      
    "distractionFreeMode": false  
  });
  gitalk.render("gitalk-container");
</script>