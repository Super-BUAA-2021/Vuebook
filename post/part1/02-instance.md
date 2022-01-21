# Vue 开发实战一：登录页面

本文将在初始化项目的基础上，编写一个**登录页面**，并为其设定路由和存储登录信息等。在阅读本文时，建议跟着敲敲代码，以加深对 vue 项目的理解。当然，如果觉得现在就开始写代码有点快，难以理解，也可以先看看后面文章再回来看本文。在这里就开始写代码，主要是为了给初学者提供一个快速入门的途径。

本文代码的仓库：<a href="https://github.com/Super-BUAA-2021/vue-template" target="_blank">https://github.com/Super-BUAA-2021/vue-template</a>

> 不熟悉 git 的同学可以看看仓库的 commit 提交记录，理解一次 commit 的时机

## 添加网站基本信息

首先修改 `public/index.html` 的内容，在里面添加上本网站的基本信息，如`keywords`、`description`等，以及修改网站标题。

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <meta name="keywords" content="vue template,vue教程,北航软工大作业">
    <meta name="description" content="Vue template是为Vuebook创建的模板项目">
    <link rel="icon" href="favicon.ico">
    <title>Vue Template</title>
  </head>
  <body style="margin:0; padding:0;">
    <div id="app"></div>
  </body>
</html>
```

注意到，在 `body` 标签中添加了 `style="margin:0; padding:0;"`，作用是使网页整体无边界。如果没加上，开发中会发现网站外围有一道较为明显的白色边框。

## 编写登录页面

在 views 目录下新建 `Login.vue` 文件，用 WebStorm 新建 vue 文件时，会自动生成如下代码框架：

```html
<template>

</template>

<script>
export default {
  name: "Login"
}
</script>

<style scoped></style>
```

* `template` 标签内：编写 html 内容，即网页的基本结构
* `script` 标签内：编写数据对象格式、方法等
* `style` 标签内：编写 css 样式，对应 template 内的内容

在实践中可以更好地理解这三部分，所以先来试着编写登录页面的基本内容，代码如下：

```html
<template>
  <div class="login">
    <form class="form-box">
      <h1>Login</h1>
      <label><input v-model="username" type="text" placeholder="Username" class="username"></label>
      <label><input v-model="password" type="password" placeholder="Password" class="password"></label>
      <button type="submit" @click="click_login" class="login">Login</button>
    </form>
  </div>
</template>

<script>
export default {
  name: "Login",
  data() {
    return {
      username: '',
      password: ''
    }
  },
  methods: {
    click_login() {
      window.alert(this.username + this.password);
    }
  }
}
</script>

<style scoped></style>
```

如上述代码，

* `template` 标签：定义了一个表单 `form`，内部展示 Login 的字样，同时有两个 `input` 供输入和一个 `button` 作登录按钮；
* `script` 标签：
    * 添加 `data`，定义数据对象 `username` 和 `password`，分别**绑定**表单中的两个 `input`
    * 添加 `methods`，定义方法 `click_login`，与按钮绑定，当用户点击按钮时，浏览器将弹窗展示 `username` 和 `password` 的值

## 添加路由

在编写完上面登录页面的基本内容后，还无法看到该网页长啥样，需要在 `router/index.js` 中添加该页面的路由，才能访问到。

只需为 `routes` 数组添加元素，指明 `path`、`name` 等信息：

```js
const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/login',
    name: 'Login',
    component: () => import('../views/Login')
  }
]
```

这样，`npm run serve` 运行项目后，访问 `localhost:8080/login`，可以看到编写的页面：

> 还没写 CSS 样式，是最原生的样貌

<img src="/img/post-instance/login.png" alt="login" style="width: 500px">

## 美化登录页面

一个好的前端，一定要拿捏 CSS。当然，它不是那么好拿捏的，因为涉及的内容太多了。（水太深）接下来，就试着美化一下。

在前面编写 `template` 部分时，已为各元素创建了 `class` 属性，接下来在 `style` 部分中为相应的 `class` 编写特定的 CSS样式。

首先，设置表单样式：

```css
.form-box {
  width: 300px;
  padding: 40px;  /* 内边界宽度  */
  position: absolute;  /* 设置为绝对定位，使下方的top和left生效  */
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);  /* 作用见后方描述  */
  background: #90b9e5;  /* 设置背景颜色  */
  text-align: center;  /* 表单中内容居中  */
  border-radius: 10px;
}
```

`transform: translate(-50%,-50%);` 作用的讲解：当使用`top: 50%; left: 50%;` 时，是以整个表单的左上角为原点，所以表单不处于中心位置，会偏右下一点。而 `translate(-50%,-50%)` 作用是，往上（x轴）、左（y轴）移动自身长宽的 50%，这样表单就刚好位于正中间。

因此上述代码的 `position~transform` 是使表单在页面中央。

接着将 Login 字样全部大写：

> 其实直接在 template 标签内把 login 大写也可

```css
.form-box h1 {
  text-transform: uppercase;  /* 将字体全部设置成大写字母  */
}
```

设置用户名输入框和密码输入框的样式：

```css
.form-box .username, .form-box .password {
  border-radius: 24px;  /* 边框四个角的弧度  */
  border: 2px solid #3498db;  /* 边框厚度和颜色  */
  background: none;
  display: block;
  margin: 20px auto;  /* 外边界  */
  text-align: center;
  padding: 14px 10px;  /* 内边界  */
  width: 200px;
  outline: none;
  color: white;     /* 设置输入框中竖线的颜色 */
  transition: 0.25s;   /* 设置元素过渡效果 */
}
```

设置文本框获得焦点时的样式，即用户点击时输入框的样式，`focus`设置的样式会在原样式基础上进行覆盖修改：

```css
.form-box .username:focus,.form-box .password:focus{
  border-color: #2ecc71;  /* 边框颜色  */
}
```

设置提交按钮的样式：

```css
.form-box .login{
  border-radius: 24px;
  border: 2px solid #0b95f1;
  background: none;
  display: block;
  margin: 20px auto;
  padding: 14px 40px;
  outline: none;
  transition: 0.25s;
  cursor: pointer;    /* 设置光标的样式 */
}
```

刷新网站，可以发现登录页面终于有点拿得出手了。

<a href="https://jsfiddle.net/brky18dt/" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

<img src="/img/post-instance/login-new.png" alt="login">

## 后言

我认为前端的主要任务有两个：

1. **编写和美化页面**
2. **编写函数方法，包括响应用户的交互事件和请求后端的服务**

本文的实战中，只是介绍了第一个任务，未涉及 JavaScript 函数方法的编写，仅用了一个弹框来相应点击登录事件，因此目前还只是空壳一个。

用户输入用户名和密码，点击 Login 按钮，网页获取到点击事件后，调用函数进行处理，也就是本文代码中还未填充的坑 `click_login`。这个函数需要做的事情通常包含：

1. 验证用户输入的 `username` 和 `password` 是否符合规范
2. 将这两个字段发送给后端
3. 获取到后端的响应信息，根据不同的信号进行不同的处理
    1. 用户名不存在或密码错误，则提示用户
    2. 密码正确，则登录成功，保存用户信息，跳转主页

因为这部分涉及 axios 的使用，后面的文章先对本次实战消化讲解一下，再来填上这个坑。

另外，本文的页面编写没有使用elementUI等UI组件库，而实际开发中使用这些组件库能达到事半功倍的效果，因为它帮我们写好了很多东西，比如样式之类的。在后面讲到UI组件库使用时，会改写登录页面。

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