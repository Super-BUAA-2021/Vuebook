# Vue 开发实战三：重写登录

本文将使用 elementUI 重写登录页面。

代码将同步至 <a href="https://github.com/Super-BUAA-2021/vue-template" target="_blank">Github 仓库</a>。为避免覆盖原登录页面的代码，本文将新登录组件命名为 `NewLogin.vue`，路由指定为 `/new_login`。

## ElementUI 安装和引入

### 安装

```shell
npm i element-ui -S
```

### 全局引入

在 `main.js` 中添加以下内容：

```js
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
```

## ElementUI 重写登录

页面所用组件含 <a target="_blank" href="https://element.eleme.cn/#/zh-CN/component/button">Button 按钮</a>、<a target="_blank" href="https://element.eleme.cn/#/zh-CN/component/input">Input 输入框</a>、<a target="_blank" href="https://element.eleme.cn/#/zh-CN/component/message">Message 消息提示</a>、<a target="_blank" href="https://element.eleme.cn/#/zh-CN/component/form">Form 表单</a>。相信结合官方指南，学习者很容易上手这些组件的使用，所以这里直接放出重写后的源代码。

```html
<template>
  <div id="login" class="login">
    <img class="bgbox" id="bgbox" alt="" src="../../src/assets/background.png">
    <div class="wrap">
      <h1>登 录</h1>
      <el-form :model="form" ref="form" class="form">
        <el-form-item prop="username">
          <el-input placeholder="用户名或邮箱" type="username" v-model="form.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item id="password" prop="password">
          <el-input
              placeholder="密码"
              show-password
              type="password"
              v-model="form.password"
              autocomplete="off"
              @keyup.enter.native="login"
          ></el-input>
        </el-form-item>
        <el-form-item class="btn_login">
          <el-button type="primary" @click="login">登&nbsp;&nbsp;录</el-button>
        </el-form-item>
      </el-form>
      <div class="suffix">
        <p @click="toRegister">注册帐号</p>
      </div>
    </div>
  </div>
</template>

<script>
import qs from "qs";
export default {
  name: "NewLogin",
  data() {
    return {
      form: {
        username: '',
        password: '',
      }
    }
  },
  methods: {
    login: function () {
      // 检查表单是否有填写内容
      if (this.form.username === '' || this.form.password === '') {
        this.$message.warning("请输入用户名和密码！");
        return;
      }

      this.$axios({
        method: 'post',           /* 指明请求方式，可以是 get 或 post */
        url: '/user/login',       /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */
        data: qs.stringify({      /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          username: this.form.username,
          password: this.form.password
        })
      })
      .then(res => {              /* res 是 response 的缩写 */
        switch (res.data.status_code) {
          case 200:
            this.$message.success("登录成功！");
            /* 将后端返回的 user 信息使用 vuex 存储起来 */
            this.$store.dispatch('saveUserInfo', {
              user: {
                'username': res.data.username,
                'token': res.data.token,
                'userId': res.data.user_id
              }
            });

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
            break;
          case 401:
            this.$message.error("用户名不存在！");
            break;
          case 402:
            this.$message.error("密码错误！");
            break;
        }
      })
      .catch(err => {
        console.log(err);         /* 若出现异常则在终端输出相关信息 */
      })
    },
    toRegister: function () {
      // 跳转注册的路由
      this.$router.push('/register');
    }
  }
}
</script>

<style scoped>
#login {
  font-family: 'Noto Serif SC', serif;
  margin-top: 60px;
}
#login >>> .el-input__inner {
  font-family: 'Noto Serif SC', serif;
}
#login .bgbox {
  display: block;
  opacity: 1;
  z-index: -3;
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: opacity 1s,transform .25s,filter .25s;
  backface-visibility: hidden;
}
#login .logo {
  cursor: pointer;
  overflow: hidden;
  height: 150px;
}
#login .wrap {
  width: 300px;
  height: 315px;
  padding: 0 25px 0 25px;
  line-height: 40px;
  position: relative;
  display: inline-block;
  background-color: rgba(255, 255, 255, 0.85);
  border-radius: 20px;
}
#login .btn_login {
  margin-top: 25px;
  text-align: center;
}
#login .btn_login button{
  line-height: 10px;
  font-family: 'Noto Serif SC', serif;
  width: 100%;
  height: 38px;
}
#login .suffix {
  font-size:14px;
  line-height:10px;
  color:#999;
  cursor: pointer;
  float:right;
}
</style>
```

效果图如下：

![新登录页面](/img/post-login/new_login.png)

## 代码提示

### 响应回车事件

在上面代码中，有这样一段代码：

```html
<el-input
    placeholder="密码"
    show-password
    type="password"
    v-model="form.password"
    autocomplete="off"
    @keyup.enter.native="login"></el-input>
```

`@keyup.enter.native="login"` 表示，用户在该输入框中使用键tade盘回车后，将触发 `login` 函数。

### Scoped CSS

可以看到，在目前所写的组件中，`<style>` 标签都有 `scoped` 属性，其作用是，保证它的 CSS 只作用于当前组件中的元素，防止不同组件（包含父组件与子组件之间）定义的类名相同的 CSS 类作用域混乱。

更详细的介绍可见 <a target="_blank" href="https://vue-loader.vuejs.org/zh/guide/scoped-css.html">Scoped CSS - Vue Loader</a>。

然而，使用 ElementUI 等 UI 组件库后，会遇到这样的情况：我们想在本页面文件中修改使用的 UI 组件的内置样式，但 UI 组件是定义于本文件外的，使用 `scoped` 属性后无法通过类名正常的指定和修改。当然，还是有办法修改其样式的，比如上面代码所使用的。

在上面代码的 CSS 部分，有这样的字段：

```css
#login >>> .el-input__inner {}
```

`>>>` 是 CSS 中的深度作用选择器，顾名思义，它能使 `scoped` 样式中的一个选择器作用得更深，例如影响子组件。

```css
<style scoped>
.a >>> .b { /* ... */ }
</style>
```

将编译成：

```css
.a[data-v-f3f3eg9] .b { /* ... */ }
```

由此可见，通过深度作用选择器，我们可以在某个 Vue 文件的 `scoped` 样式中修改指定 UI 子组件的样式，且不会影响其它 Vue 文件中该 UI 子组件的原始样式。

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