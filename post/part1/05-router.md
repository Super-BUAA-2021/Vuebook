# Vue 路由

在 Vue 开发实战一中，使用了简单的路由定义，给登录页面设定了路由 `/login`，本篇文章将介绍路由的定义、跳转和更深入的路由钩子(导航守卫)。

## vue-router库

Vue.js 路由需要载入 <a href="https://github.com/vuejs/vue-router" target="_blank">vue-router库</a>。

在创建项目初始，若已选中使用 router，则已自动载入；否则需安装引入：

```shell
npm install vue-router
```

## 路由定义

常在 `router/index.js` 中定义网站的路由：

```js
/* 0. 导入 Vue 和 VueRouter，调用 VueRouter，需要时可导入主要的 Vue 组件 */
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '../views/Home.vue'

Vue.use(VueRouter)

/* 1. 定义路由，每个路由对应一个 Vue 组件 */
const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  /* path 指定路由，name 设定路由标志名，component 指定 Vue 组件，可使用 import 导入 */
  {
    path: '/about',
    name: 'About',
    component: () => import('../views/About.vue')
  },
  {
    path: '/login',
    name: 'Login',
    component: () => import('../views/Login')
  }
]

/* 2. 创建 router 实例，配置参数等 */
const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})

/* 3. 导出 router，在 main.js 中挂载到根实例 */
export default router
```

在创建项目时，该文件的大致框架已自动建立，编写新页面时只需照葫芦画瓢，在 `routes` 数组中添加元素指定路由即可。

* path：指定路由
* name：指定路由标志名，可在跳转命令中用以代替path
* component：指定路由对应的 Vue 组件，常使用 `import` 引入

## `$router` 路由跳转

在 Vue 实例内部，可以在 JavaScript 中通过 `$router` 访问路由实例，因此可以调用 `this.$router.push` 或 `this.$router.replace` 实现编程式路由跳转。

`push` 方法的参数可以是一个字符串路径，或者一个描述地址的对象。例如：

```js
// 字符串，指跳转至 /home
this.$router.push('home')

// 对象，指跳转至 /home
this.$router.push({ path: 'home' })

// 命名的路由，使用 name 代替 path 的指定作用，同时传递 userId 参数
this.$router.push({ name: 'user', params: { userId: '123' } })

// 带查询参数，跳转后路由为 /register?mode=vip
this.$router.push({ path: 'register', query: { mode: 'vip' } })

// params 和 query 方法的区别在于，后者会将参数显式地展示在目的路由中
```

**注意：**

1. 使用 params 传参，对象属性里只能是 `name` 而不能是 `path`，即 params 方法只能使用 `name` 引入路由；
2. 如果目的路由和当前路由相同，只有参数发生了改变，比如从一个用户资料 `/user/1` 跳转到另一个用户 `/user/2`，则可能需要使用 Vue.js 生命周期的 `beforeRouteUpdate` 来响应这个变化，比如重新获取用户信息。

> [!TIP|style:flat]
> 路由跳转后，常需要接收参数，query 方式使用 `this.$route.query.mode` 接收 `mode` 参数，params 方式使用 `this.$route.params.userId` 接收 `userId` 参数。

`router.replace` 用法与 `push` 一样，两者区别在于它不会向 history 添加新纪录，而是将新路由替换掉当前的 history 记录，因此无法通过前进后退进行跳转。

`router.go` 也能实现路由跳转，只不过是在 history 记录中向前或后退多少步，例如：

```js
// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)

// 如果 history 记录不够用，就默默地失败
router.go(-100)
router.go(100)
```

## `<router-link>` 路由跳转

除了在 JS 方法中实现路由跳转，在 HTML 部分也可以使用 `<router-link>` 标签直接渲染，获取到点击事件则跳转。

该标签含有 `to` 和 `replace` 属性，前者对应 `router.push`，后者对应 `router.replace`。例如：

```html
<!-- 字符串 -->
<router-link to="home">Home</router-link>

<!-- 绑定描述目标位置的对象，使用 :to -->
<router-link :to="'home'">Home</router-link>
<router-link :to="{ path: 'home' }">Home</router-link>

<!-- 命名的路由 -->
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>

<!-- 带查询参数，下面的结果为 /register?mode=vip -->
<router-link :to="{ path: 'register', query: { mode: 'vip' }}">Register</router-link>

<!-- replace -->
<router-link :to="{ path: '/abc'}" replace></router-link>
```

> 更多 router-link 的用法见 <a href="https://www.runoob.com/vue2/vue-routing.html" target="_blank">Vue.js 路由 - RUNOOB</a>

事实上，`<router-link>` 标签可以使用 [`@click`](post/part1/03-grammar.html#点击按钮事件) + [`router.push`](post/part1/04-router.html#js-路由跳转) 代替，即在 html 的内容中添加 `@click` 属性调用函数，在函数中使用 `router.push` 实现跳转。

## 路由钩子(导航守卫)

一个完整的网站，可能需要对不同身份的用户进行路由访问的拦截，比如未登录用户无法访问用户中心，非管理员用户无法访问管理中心，当进行“违法”访问时可以设置自动跳转至登录页面。上述功能的实现，可以使用路由钩子。

示例：未登录用户访问用户中心（需要登录后才允许访问的页面），拦截至登录页面。

在 `router/index.js` 中，对需要登录才可访问的路由设置 `meta` 属性信息：

```js
const routes = [
    {
        path: 'center',
        name: 'Center',
        component: () => import('../views/center.vue'),
        meta: {
            requireAuth: true
        }
    }
]
```

继续在该文件中添加以下代码，判断未登录则跳转登录页面：

```js
router.beforeEach((to, from, next) => {
    // 通过 Vuex 获取用户登录信息（在实战篇中会介绍到）
    const userInfo = user.getters.getUser(user.state())

    // 若用户未登录且访问的页面需要登录，则跳转至登录页面
    if (!userInfo && to.meta.requireAuth) {
        next({
            name: 'Login',
        })
    }

    next()
})
```

这样就完成了简单的导航守卫，更多实用的将在实战中使用和讲解。

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