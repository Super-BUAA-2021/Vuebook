# Vue 语法语句

上篇文章带着编写了基本的登录页面，让初学者对 Vue 项目开发有一定的了解，但没有细致讲解 Vue 的语法、路由等，因此接下来的文章将对这些知识点展开介绍。内容较多，建议快速阅览，留个印象，在实际开发中熟悉。

## 数据绑定

Vue.js 使用了基于 HTML 的模板语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。结合响应系统，在应用状态改变时， Vue 能够智能地计算出重新渲染组件的最小代价并应用到 DOM 操作上。

不太准确地说，当用户输入或其它途径修改了 “template” 中的数据元素时，“script” 内的 Vue 底层数据也会更新；当某些 JS 方法修改了数据时，也会将更新后的数据同步渲染 DOM。

### 插值

#### 文本插值

数据绑定最常见的形式是使用 `{{...}}` 双大括号：

```html
<template>
    <div>
        <p>{{ msg }}</p>
    </div>
</template>
<script>
export default {
    data() {
        return {
            msg: '北航软院yyds'
        }
    },
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=D4023D8672" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

#### v-html

使用 `v-html` 指令渲染 html 内容：

```html
<template>
    <div>
        <h1 v-html="msg"></h1>
    </div>
</template>
<script>
export default {
    data() {
        return {
            msg: '北航软院yyds'
        }
    },
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=E90734000A" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

#### 属性

HTML 属性中的值使用 `v-bind` 指令进行绑定。以下实例判断 `isShow` 的值，为 `true` 时使用 `class1` 的样式，否则不使用：

```html
<template>
    <div>
        <h1 v-bind:class="{'class1': isShow}">GOOD</h1>
    </div>
</template>
<script>
export default {
    data() {
        return {
            isShow: true
        }
    },
}
</script>
<style scoped>
.class1 {
    color: red;
}
</style>
```

<a href="https://zewanhuang.github.io/vue-online/?p=17703362B1" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

### 用户输入

在 input 输入框中我们可以使用 `v-model` 指令来实现双向数据绑定：

```html
<template>
    <div>
        <input v-model="msg">
    </div>
</template>
<script>
export default {
    data() {
        return {
            msg: '北航软院yyds'
        }
    },
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=376C4157DB" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

`v-model` 指令用来在 input、select、textarea、checkbox、radio 等表单控件元素上创建双向数据绑定，根据表单上的值，自动更新绑定的元素的值。

### 过滤器

Vue.js 允许自定义过滤器，用于格式化文本数据并渲染。以下实例将字符串首字母转为大写：

```html
<template>
    <div><h1>{{ msg | capitalize }}</h1></div>
</template>
<script>
export default {
    data() {
        return {
            msg: 'beihang is good!'
        }
    },
    filters: {
        capitalize(value) {
            if (!value) return '';
            value = value.toString();
            return value.charAt(0).toUpperCase() + value.slice(1);
        }
    }
}
</script>
```

## 事件绑定

Vue 主要使用 `v-on` 监听事件，调用相应函数对用户的交互做出响应。

### 点击按钮事件

按钮事件为 `click`，`v-on:click="event"` 表示当用户点击时调用 `event` 方法。以下实例在用户点击按钮后改变字符串：

```html
<template>
    <div>
        <p>{{ msg }}</p>
        <button v-on:click="change">切换</button>
    </div>
</template>
<script>
export default {
    data() {
        return {
            msg: '北航软院yyds',
            msg1: '北航软院yyds',
            msg2: '啊对对对'
        }
    },
    methods: {
        change() {
            this.msg = (this.msg == this.msg1)? this.msg2: this.msg1;
        }
    }
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=15CD89C334" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

在开发中可能会看到 `v-on:click` 和 `@click` 两种用法，实际上这两种是一样的，后者是前者的缩写。

## 条件语句

条件判断使用 `v-if`、`v-else-if`、`v-else` 指令，以下实例通过判断 `flag` 的值展示不同的内容。

```html
<template>
    <div>
        <p v-if="flag==='1'">{{ msg1 }}</p>
        <h1 v-else-if="flag==='2'">{{ msg2 }}</h1>
        <h2 v-else>{{ msg3 }}</h2>
        <input v-model="flag">
    </div>
</template>
<script>
export default {
    data() {
        return {
            msg1: '北航软院yyds',
            msg2: '啊对对对',
            msg3: 'ABC',
            flag: '1',
        }
    }
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=5EE936A797" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

## 循环语句

### 遍历数组

使用 `v-for` 指令绑定数据到数组来渲染一个列表：

```html
<template>
    <div>
        <p v-for="tea in teachers">{{ tea.name }}</p>
    </div>
</template>
<script>
export default {
    data() {
        return {
            teachers: [
                { name: 'ZewanHuang' },
                { name: 'Matrix53' }
            ]
        }
    }
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=1A9439F36F" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

### 遍历对象

```html
<template>
    <div>
        <p v-for="value in obj">{{ value }}</p>
    </div>
</template>
<script>
export default {
    data() {
        return {
            obj: {
                name: 'Vuebook',
                url: 'https://super-buaa-2021.github.io/Vuebook/',
                slogan: '前端的水太深，你要好好把握！'
            }
        }
    }
}
</script>
```

<a href="https://zewanhuang.github.io/vue-online/?p=DA7B1193AD" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

> [!TIP|style:flat]
> 本文介绍的语法语句在 Vue 项目开发中经常使用，建议自己动手熟练熟练。最快速上手的方法还是直接在开发中熟悉，记不清了就回来查一查。


<script src="https://utteranc.es/client.js"
        repo="Super-BUAA-2021/Vuebook"
        issue-term="pathname"
        label="Comment"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>