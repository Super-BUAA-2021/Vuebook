# Vue 生命周期与属性

在开发中，常涉及的 Vue 生命周期含 created 和 mounted，常用的属性含方法属性 methods、计算属性 computed、监听属性 watch 等，因此本文仅介绍上述五个知识，完整的 Vue 生命周期可以参考<a href="https://segmentfault.com/a/1190000011381906" target="_blank">该博客</a>进行学习。

## created

Vue 生命周期中的 created 是指 template 渲染成 html 前的阶段。在该阶段中，通常需要初始化某些数据值，比如向后端发送请求以获取页面信息，然后再渲染视图。

示例：进入用户设置页面，需要请求后端，获取用户名、邮箱等基础信息。

```html
<template>
    <div>
        <p>{{ username }}</p>
        <p>{{ email }}</p>
    </div>
</template>
<script>
export default {
    data() {
        return {
            username: '',
            email: '',
        }
    },
    created() {
        // 使用 axios 向后端发送请求，后续文章将介绍到，此处仅表示该意思
        this.$axios.post(...)
        // 将返回值赋值给数据
        this.username = res.data.username;
        this.email = res.data.email;
    }
}
</script>
```

## mounted

mounted 是在 template 渲染成 html 后的阶段。在该阶段中，也就是初始化页面完成后，通常对 html 的某些 DOM 节点进行一些操作，比如绘制图表渲染到某个节点上。这些操作由于涉及 DOM 节点，不能在 created 阶段中完成，因为该阶段还未渲染出相关节点。

> <a target="_blank" href="https://www.runoob.com/htmldom/htmldom-nodes.html">HTML DOM</a> 将 HTML 当作由各标签组成的树结构，DOM 节点可视为各标签渲染出来的结构节点

相比 created，mounted 较为少用，该阶段的操作常需要用下面命令获取 id 为 `xxx` 的 DOM 节点，再将图表渲染到该节点。

```js
var x = document.getElementById("xxx");
```

## methods

在 Vue 的 methods 属性中，常定义事件处理方法，用于绑定和处理点击、回车事件等，当然，也可以将方法作为插值直接渲染。

示例：使用 `login` 函数处理点击按钮事件。

```html
<template>
    <div>
        <input v-model="msg">
        <button @click="login">登录</button>
    </div>    
</template>
<script>
export default {
  data() {
    return {
      msg: ""
    }
  },
  methods: {
    login: function() {
      if (this.msg !== '') alert("登录成功！");
      else alert("登录失败！");
    }
  }
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=D87F739314" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

## computed

在 Vue 中，可以定义计算属性对数据进行处理，并返回处理结果。

下面的例子定义了 `rmsg` 属性，计算 `msg` 的反转字符串：

```html
<template>
	<div>
  	<p>{{ msg }}</p>
    <p>{{ rmsg }}</p>
    <p>{{ rmsg_by_method() }}</p>
  </div>
</template>
<script>
export default {
  data() {
    return {
      msg: "软件学院"
    }
  },
  computed: {
    rmsg: function() {
      return this.msg.split('').reverse().join('');
    }
  },
  methods: {
    rmsg_by_method: function() {
      return this.msg.split('').reverse().join('');
    }
  }
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=B8F92D9C3F" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

在上面的示例中，还定义了一个方法，实现了与计算属性一致的效果。那 `computed` 与 `methods` 的区别是什么？

> [!NOTE|style:flat]
> computed 是基于依赖缓存的，只有相关依赖发生改变时才会重新计算值；而使用 methods，在重新渲染或再次调用的时候，函数总会重新调用执行，可以理解一下<a target="_blank" href="https://c.runoob.com/codedemo/5530/">示例</a>。在没有使用依赖缓存时，推荐使用 methods，它能接受参数，使用更灵活。

目前我们只介绍了计算属性的 getter，即获取值，在需要时可以定义该属性的 setter。

下面例子为 `site` 属性定义了 setter，当为该属性赋值时，将调用定义的 setter。

```html
<template>
  <div>
    <p>{{ site }}</p>
  	<p>{{ name }}</p>
    <p>{{ url }}</p>
  </div>
</template>
<script>
export default {
  created() {
    this.site = "ZewanHuang blog.zewan.cc";
  },
  data() {
    return {
      name: '',
      url: '',
    }
  },
  computed: {
    site: {
      // getter
      get: function () {
        return this.name + ' ' + this.url
      },
      // setter
      set: function (newValue) {
        var names = newValue.split(' ')
        this.name = names[0]
        this.url = names[names.length - 1]
      }
    }
  },
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=59CF13026B" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

## watch

Vue 的监听属性 watch，可以用来监听数据、响应数据的变化。

示例一：利用 watch 实现计数器。

```html
<template>
    <div>
      <p>计数器：{{ counter }}</p>
      <button @click="counter++">加一</button>
    </div>
</template>
<script>
export default {
  data() {
    return {
      counter: 1
    }
  },
  watch: {
    counter(newVal, oldVal) {
      alert("计数器：" + oldVal + "变为" + newVal);
    }
  }
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=D3FB04C693" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

示例二：实现千米和米之间的换算。

```html
<template>
    <div>
      <div>千米：<input v-model="kilometer"></div>
      <div>米：<input v-model="meter"></div>
    </div>
</template>
<script>
export default {
  data() {
    return {
      kilometer: '',
      meter: ''
    }
  },
  watch: {
    kilometer(newVal) {
      this.meter = newVal*1000;
    },
    meter(newVal) {
      this.kilometer = newVal/1000;
    }
  }
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=BC581288E8" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

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