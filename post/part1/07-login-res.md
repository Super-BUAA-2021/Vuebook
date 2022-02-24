# Vue 开发实战二：登录请求

在[『Vue 开发实战一：登录页面』](02-instance.html)中，实现了较为简单的登录页面，还存在向后端发送登录请求和处理响应的坑，本文将填补该坑。

此次实践将实现：

1. 用户点击登录按钮，前端向后端发送登录请求
2. 前端从后端收到登录成功的信号后，使用 Vuex 保存用户信息
3. 未登录用户访问个人中心，跳转至登录页面（全局路由钩子）
4. 访问某页面被拦截至登录页面，登录成功后自动返回原访问页面

同时，代码将会同步至 <a href="https://github.com/Super-BUAA-2021/vue-template" target="_blank">Github 仓库</a>。

## Vuex 前端状态存储

用户登录和访问网站时，本地需要存储用户的一些基本信息，比如用户名、用户ID、身份认证 token 等。因此，在编写发送请求的代码前，先使用 Vuex 建立数据存储模式，以便在登录成功后保存用户信息。

Vuex 是专为 Vue.js 应用程序开发的状态管理模式，采用集中式存储管理应用所有组件的状态。简而言之，Vuex 可以帮助在前端存储用户数据，且在该网站的所有 Vue 组件、所有页面都能读写。

Vuex 没有作为单独一篇文章进行讲解，原因是其较为复杂，对于简单的单页应用可能有些大材小用。此处我仅用 Vuex 存储用户信息，用法套路固定，可以直接复制粘贴、学会怎么用即可。感兴趣可以参考<a target="_blank" href="https://vuex.vuejs.org/zh/">官方文档</a>进行学习。

### 安装 Vuex

创建项目时若已选中 Vuex，则已安装；否则需输入下列命令进行安装：

```shell
npm install vuex --save
```

### 创建 user 状态管理模式

> 这部分代码想要彻底理解需要学习 <a target="_blank" href="https://vuex.vuejs.org/zh/">Vuex 官方文档</a>，建议初学者对下面代码有个大概了解即可，会用，后面再慢慢了解。

在 store 目录下创建 `user.js`，内容为：

```js
const key = 'user'
const user = {
    /* 定义 user 数据对象和它的初始值 */
    state() {
        return {
            user: null
        }
    },
    /* 定义读取方法 getUser，从 localStorage 中读取 user 数据并转换成 JSON 格式 */
    getters: {
        getUser: function (state) {
            if (!state.user) {
                state.user = JSON.parse(localStorage.getItem(key))
            }
            return state.user
        }
    },
    /* 定义更改 store 状态的事件 */
    mutations: {
        /* 存数据，将 JSON 格式的数据转化为字符串形式存储到以 localStorage 中以 `user` 为键的值中 */
        $_setStorage (state, value) {
            state.user = value
            localStorage.setItem(key, JSON.stringify(value))
        },
        /* 清空数据，将状态恢复初始值，并从 localStorage 中移除对象 */
        $_removeStorage (state) {
            state.user = null
            localStorage.removeItem(key)
        }
    },
    /* 定义调用 mutations 的方法，向上提供调用接口 */
    actions: {
        /* 调用 _setStorage 方法存储数据 */
        saveUserInfo({ commit }, data) {
            commit('$_setStorage', data)
        },
        /* 调用 _removeStorage 方法清空数据 */
        clearUserInfo({ commit }) {
            commit('$_removeStorage');
        }
    }
};

export default user
```

上述代码定义了 `user` 作为 Vuex 模块(<a target="_blank" href="https://vuex.vuejs.org/zh/guide/modules.html">Vuex Module</a>)，该模块有自己的 `state`、`getters`、`mutations`、`actions` 四个属性。

在 store/index.js 文件中引入 `user` 模块：

```js
import Vue from 'vue'
import Vuex from 'vuex'
import user from "./user";

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
  },
  mutations: {
  },
  actions: {
    /* 定义清空 localStorage 的方法 */
    clear({ commit }) {
      commit("$_removeStorage");
    }
  },
  modules: {
    user
  }
})
```

### Vuex 存取命令

上面步骤建立了 user 模块，这里介绍一些基础的用法，将在处理登录请求的返回信息中使用。

保存用户信息(常在登录时使用)：

```js
this.$store.dispatch('saveUserInfo', {
    user: {
        'username': res.data.username,
        'Authorization': res.data.Authorization,
        'userId': res.data.user_id,
    }
});
```

清空本地存储数据(常在退出登录时使用)：

```js
this.$store.dispatch('clear');
```

读取本地存储数据(在向后端发送请求或路由钩子时可使用)：

```js
const userInfo = user.getters.getUser(user.state());
/* userInfo 可用于判断用户登录信息，为 null 时表示用户未登录 */

if (userInfo) userInfo.user.userId /* 获取存储在本地的 userId，需与存储的数据结构对应 */
```

## Axios 发送登录请求

<a href="https://axios-http.com/zh/" target="_blank">Axios</a> 是一个 HTTP 请求库，使用它来完成对后端的请求。

### 安装 Axios

```shell
npm install axios --save
```

### 全局引入

全局引入，方便在各组件中可直接使用而无需重复引入。

在 `main.js` 中添加以下内容：

```js
import axios from 'axios'

/* 引入 axios 并挂载到 Vue 实例上，在各 Vue 组件通过 this.$axios 进行使用 */
Vue.prototype.$axios = axios

// 指定 axios 发送请求的目标后端地址的根路径
// 一般为后端服务器IP+端口；若有部署域名则可以是域名地址；
// 此处假设在本地同时运行前后端，后端地址为 http://localhost:8000
axios.defaults.baseURL = 'http://localhost:8000';
```

### 登录请求

在 [Vue 开发实战一](02-instance.html)中，留下了 `click_login` 这个坑，下面将使用该函数实现登录请求。

假如后端提供的接口格式如下：

* 请求方式：POST
* Api相对路径：`/user/login`
* 请求数据示例：
    ```json
    {
        "username": "ZewanHuang",
        "password": "ZewanH123"
    }
    ```
* 返回数据示例：
    ```json
    // 成功登录的示例
    {
        "status_code": 200,
        "username": "ZewanHuang",
        "user_id": "1",
        "token": "ASDQW1231W3DWEEW12343",
    },
    // 用户名不存在的示例
    {
        "status_code": 401
    },
    // 密码不正确的示例
    {
        "status_code": 402
    }
    ```

则 `click_login` 函数代码如下：

> 下面代码中使用了 `qs.stringify`，因此需要在 `<script>` 标签中起始处添加 `import qs from "qs";` 导入 qs 工具。本教程后面的代码如使用该工具，则同样需要添加。

```js
click_login() {
  this.$axios({
    method: 'post',           /* 指明请求方式，可以是 get 或 post */
    url: '/user/login',       /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */
    data: qs.stringify({      /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
      username: this.username,
      password: this.password
    })
  })
  .then(res => {              /* res 是 response 的缩写 */
    switch (res.data.status_code) {
      case 200:
        window.alert("登录成功！");
        /* 将后端返回的 user 信息使用 vuex 存储起来 */
        this.$store.dispatch('saveUserInfo', {
          user: {
            'username': res.data.username,
            'token': res.data.token,
            'userId': res.data.user_id
          }
        });
        break;
      case 401:
        window.alert("用户名不存在！");
        break;
      case 402:
        window.alert("密码错误！");
        break;
    }
  })
  .catch(err => {
    console.log(err);         /* 若出现异常则在终端输出相关信息 */
  })
}
```

实现逻辑是，使用 axios 指定后端路由地址、携带数据发送请求，收到返回信息后，根据自定义的、代表不同意义的返回码，对各个情况做出不同的处理。

这样就实现了登录请求的发送和处理，当然，没有后端的支持，它还站不起来。

## 路由拦截和返回

### 路由拦截

很多网站都有这一功能，当未登录用户直接输入网址访问个人中心时，会自动跳转到登录页面。

先创建一个新页面，假设是个人中心页面，内容自由发挥(比如显示“个人中心”字样即可)，并在 `router/index.js` 中添加路由，设置为需要登录：

> 对于其它需要登录才能访问的路由，也添加如下的 `meta` 配置

```js
{
  path: '/center',
  name: 'Center',
  component: () => import('../views/UserCenter'),
  meta: {
      requireAuth: true
  }
}
```

继续在 `router/index.js` 中添加以下代码，判断访问页面若需要登录且当前未登录，则拦截至登录路由：

> [!WARNING|style:flat]
> 下面代码中 `router.beforeEach` 这一段需要在 `const router = new VueRouter` 代码段之后！

```js
import user from "@/store/user";

router.beforeEach((to, from, next) => {
  // 通过 Vuex 获取用户登录信息
  const userInfo = user.getters.getUser(user.state());

  // 若用户未登录且访问的页面需要登录，则跳转至登录页面
  if (!userInfo && to.meta.requireAuth) {
    next({
      name: 'Login',
    })
  }

  next()
})
```

### 登录成功后返回原路由

相比于登录成功后不管三七二十一返回首页，用户自然更希望被拦截后登录成功后，能自动返回原先访问的路由地址。

比如访问需要登录才能填写的问卷，当用户访问该问卷时，被拦截到登录页面，当用户登录成功后，正确的逻辑应该是自动返回到问卷页面。

实现逻辑：若前往的是登录路由，则保存当前路由到 localStorage；登录成功后，跳转到本地保存的路由。

在 `router/index.js` 中添加内容如下：

```js
router.beforeEach((to, from, next) => {
  // 通过 Vuex 获取用户登录信息
  const userInfo = user.getters.getUser(user.state());

  // 若前往的是登录路由，则保存当前路由到 preRoute 的键值对中，以便登录成功后跳转
  if (to.path === '/login') {
    localStorage.setItem("preRoute", router.currentRoute.fullPath);
  }

  // 若用户未登录且访问的页面需要登录，则跳转至登录页面
  if (!userInfo && to.meta.requireAuth) {
    next({
      name: 'Login',
    })
  }

  next()
})
```

在登录组件的 `login_click` 函数中，添加登录成功后返回原路由的逻辑：

```js
/* 从 localStorage 中读取 preRoute 键对应的值 */
const history_pth = localStorage.getItem('preRoute');
/* 若保存的路由为空或为注册路由，则跳转首页；否则跳转前路由（setTimeout表示1000ms后执行） */
setTimeout(() => {
  if (history_pth == null || history_pth === '/register') {
    this.$router.push('/');
  } else {
    this.$router.push({ path: history_pth });
  }
}, 1000);
```

至此，登录相关的功能需求已完成。当然，可能还存在一些细节待完善，比如系统获取到点击登录按钮的事件后，前端先检查用户名和密码是否为空，都不为空才向后端发送登录请求。（可自行完善）

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