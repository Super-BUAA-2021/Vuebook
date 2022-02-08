# Vue 引入 ElementUI

前面的文章介绍了 Vue 的基本用法，并使用纯 HTML+CSS+JS 实现了简易的登录页面。在日常开发中，纯手写 CSS 是比较考验前端的 CSS 能力的，而 UI 组件库提供了一些基本的组件单元，能帮助快速地搭建网站页面。

此处以 <a target="_blank" href="https://element.eleme.cn/#/zh-CN">ElementUI</a> 为例介绍如何使用。

> UI 组件库一般都有官方文档，能很清晰地学会各种组件的用法

## npm 安装 ElementUI

```shell
npm i element-ui -S
```

## 全局引入

在 `main.js` 中添加以下内容：

```js
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
```

## 如何使用

在 <a target="_blank" href="https://element.eleme.cn/#/zh-CN/component/layout">ElementUI 官方文档</a>中，以代码+效果图的形式展现了组件的用法，学习者可以根据需求合理地使用组件。

这里用 elementUI 实现一个简单的表单，涉及组件 Input 输入框、Button 按钮、Select 选择器、DateTimePicker 日期选择器。

```html
<template>
	<div id="my-form">
  	<h1>简易表单</h1>
    <div class="body">
      <div class="username font16 form-item">
        用户名：&emsp;
        <el-input v-model="username" maxlength="15" show-word-limit></el-input>
      </div>
      <div class="realname font16 form-item">
        真实姓名：
        <el-input v-model="realname"></el-input>
      </div>
      <div class="sex font16 form-item">
        性别：&emsp;&emsp;
        <el-select v-model="sex">
        	<el-option v-for="item in sex_options" :key="item.value" :value="item.value"></el-option>
        </el-select>
      </div>
      <div class="birthday font16 form-item">
      	出生日期：
        <el-date-picker v-model="birthday" type="date"></el-date-picker>
      </div>
      <div class="intro font16 form-item">
      	<span style="vertical-align: top">自我介绍：</span>
        <el-input v-model="introduction" type="textarea" placeholder="请简要介绍自己，内容不超过300字" maxlength="300" show-word-limit></el-input>
      </div>
      <el-button type="primary" style="margin-top:20px" icon="el-icon-check" @click="submit">提交</el-button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Form',
  data() {
    return {
      username: '',
      realname: '',
      sex: '',
      birthday: '',
      introduction: '',
      
      sex_options: [{value: '男'}, {value: '女'}]
    }
  },
  methods: {
    submit() {
      alert("提交成功！");
    }
  }
}
</script>

<style scoped>
  .form-item {
    margin-top: 15px;
  }
  .font16 {
    font-size: 16px;
  }
  .el-input {
    position: relative;
    width: 350px;
    display: inline-block;
  }
  .sex .el-input {
    width: 100px;
  }
  .el-textarea {
    position: relative;
    width: 500px;
    display: inline-block;
  }
</style>
```

<a href="https://zewanhuang.github.io/vue-online/?p=5D39F97D0C" target="_blank" style=""><button style="background-color: #409eff; border-color: #409eff; font-size: 0.8em; color: #fff; padding: 6px 10px; border-radius: 10px">查看效果</button></a>

---

<a target="_blank" href="https://element.eleme.cn/#/zh-CN">ElementUI 官方文档</a>详细地介绍了如何使用（含代码和效果图），因此这里不再过多赘述，学习者可以大致了解有哪些好用的组件，在开发时根据原型设计适当地选择和使用即可。