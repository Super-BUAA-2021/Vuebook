# Vue axios 请求

Vue.js 2.0 版本推荐使用 <a href="https://axios-http.com/zh/" target="_blank">axios</a> 来完成对后端的请求。

Axios 是一个基于 promise 的网络请求库(HTTP)，可以用于浏览器和 node.js 中。

## 安装 Axios

```shell
npm install axios --save
```

## 全局引入

网站开发中，向后端发送请求的使用次数高，因此全局引入将方便在各组件中随处使用。

全局引入只需在 `main.js` 文件中添加以下内容：

```js
import axios from 'axios'

/* 引入 axios 并挂载到 Vue 实例上 */
Vue.prototype.$axios = axios

/* 指定 axios 发送请求的目标后端地址的根路径，一般为后端服务器IP+端口，若有部署域名则可以是域名地址 */
axios.defaults.baseURL = 'http://123.12.123.12:8000';
```

全局挂载后，不需要在各组件重复引入。如上述方式全局引入后，在各组件使用 `this.$axios` 就可以使用 axios 的功能。

在 `main.js` 中指定后端地址根路径，这样在各 vue 组件中向后端发送请求时，就不需要重复写明根路径。且当运行或部署的服务器(包括从本地要部署到服务器上)发生变化时，只需要修改此处的根路径即可。

关于后端如何运行部署的，前端开发人员不需要了解。等收到后端人员给的根路径和各 api 的路由地址，只管向这些 api 发送请求了。

## 发送请求

前端向后端发送请求，常用的两种请求方式为 POST 和 GET。

> [!NOTE|style:flat]
> GET 和 POST 是 HTTP 协议中发送请求的方法。GET 方法请求一个指定资源的表示形式，应该只被用于**获取数据**；POST 方法用于提交实体到指定的资源，通常导致在**服务器上的状态变化**，比如涉及**对数据库的写入**。更详细的区别可见<a target="_blank" href="https://vue3js.cn/interview/http/GET_POST.html">『面试官：说一下 GET 和 POST 的区别？』</a>。

axios 请求的 POST 和 GET 两种方式可以有不同的编写，分别是 `axios.post(..)` 和 `axios.get(..)`，也可以使用统一形式的 API 发送请求和处理响应：

示例：发送用户名，请求用户的真实姓名。

> 下面代码中使用了 `qs.stringify`，因此需要在 `<script>` 标签中起始处添加 `import qs from "qs";` 导入 qs 工具。本教程后面的代码如使用该工具，则同样需要添加。

```js
this.$axios({
    method: 'get',              /* 指明请求方式，可以是 get 或 post */
    url: '/user/realname',      /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */
    data: qs.stringify({        /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
        username: 'Zewan',
    })
})
.then(res => {                  /* res 是 response 的缩写 */
    if (res.data.success)       /* res.data 是后端返回的数据对象 */
        this.realname = res.data.realname;
    else
        alert("获取失败！");
})
.catch(err => {                 /* 请求若出现路由找不到等其它异常，则在终端输出错误信息 */
    console.log(err);
})
```

当然，向后端发送请求的代码，是基于后端提供的 api 信息来编写的。比如对于上面这个例子，后端提供的 api 可以是如下格式：

| 条目 | 内容 |
| :-: | :-: |
| 根路径 | `http://123.12.123.12:8000` |
| api路径 | `/user/realname` |
| 请求方式 | GET |
| 携带数据示例 | `{ username: 'Zewan' }` |
| 返回数据示例 | `{ success: true, realname: 'Zehuan Huang' }` 或 `{ success: false }` |

关于前后端传输的数据格式(如上述携带数据和返回数据的示例)，可以由前后端人员一起商量确定。一个基本的网站需要较多的 api，而 api 格式的统一与否会影响前后端传输是否成功，比如后端返回了 `realname`，而前端中接收的是 `real_name`，就会导致网页无法正常显示姓名。

为了保证数据格式在前后端的统一，通常会在共享文档中维护各 api 的数据格式，并由后端提供成功或失败等多种情况的数据示例，以便前端正确地接收和处理数据。当然，后端也可以借助 <a target="_blank" href="https://swagger.io/">Swagger</a> 等工具，编写注释自动生成 API 在线文档(这就要看后端嫌不嫌麻烦了)。

## 并发请求

使用 axios 可以同时向两个或多个 api 发送请求，待请求都得到回复后再进行处理：

```js
methods: {
    getUserEmail: function() {
        return this.$axios({
            method: 'get', url: '/user/getEmail', data: qs.stringify({ username: 'Zewan' })
        })
    },
    getUserPasswd: function() {
        return this.$axios({
            method: 'get', url: '/user/getPasswd', data: qs.stringify({ username: 'Zewan' })
        })
    },
    getAllInfo: function() {
        this.$axios.all([getUserEmail(), getUserPasswd()])
        .then(this.$axios.spread(function(email, passwd) {
            // email 和 passwd 分别表示两个 api 的 response，相当于上一个示例的 res
            // 在这里处理返回的数据
        }))
        .catch(err => {
            console.log(err);
        })
    }
}
```

上述代码中，`getUserEmail` 和 `getUserPasswd` 两个函数定义了向两个 api 发送的 axios 请求，在 `getAllInfo` 中执行并发请求，并对返回数据进行处理。

---

本文仅介绍了 Axios 的基础用法，足以应对常见的开发任务。更多关于 Axios 的细节可以参考<a target="_blank" href="https://axios-http.com/zh/docs/intro">官方文档</a>进行学习。

<script src="https://utteranc.es/client.js"
        repo="Super-BUAA-2021/Vuebook"
        issue-term="pathname"
        label="Comment"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>