# Vue 项目初识

上篇文章创建了一个初始的 Vue2 项目，本文对这个几乎空白的项目框架进行简要介绍。

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



### views



### App.vue



### main.js



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