---
title: 'vue传值的几种方法'
date: 2020-08-12
tags: 
	- Vue
---

## 一、父子组件间传值
### 父组件向子组件传值（props）
父组件代码如下：

```html
<template>
  <div>
    <!-- 子组件 -->
    <Child :value="message"></Child>
  </div>
</template>

<script>
export default {
  components: { Child },
  data () {
    return {
      message: 'Father Message'
    }
  }
}
</script>
```
子组件代码如下：

```html
<template>
  <div>
    <!-- 渲染出父组件传过来的值 -->
    message: {{value}}
  </div>
</template>

<script>
export default {
  props: {
    value: String
  }
}
</script>
```
### 子组件给父组件传值（emit事件）
子组件代码如下：

```html
<template>
  <div @click="onClick">
    abc
  </div>
</template>
<script>
export default {
  methods: {
    // 点击发布消息
    onClick() {
        // 自定义事件input
        this.$emit('input', 'Child Message')
    }
  }
}
</script>
```
父组件代码如下：

```html
<template>
  <div class="content">
    <!-- 用自定义事件input调用onMessage方法 -->
    <Child @input="onMessage"></Child>
  </div>
</template>
<script>
export default {
  components: { Child },
  methods: {
    onMessage (msg) {
        // 接收的子组件传来的消息
        console.log(msg)
    }
  }
}
</script>
```
## 二、Storage缓存
`sessionStorage`和`localStorage`都是HTML5新增的方法，使用它可以在客户端本地建立一个数据库，原本必须保存在服务器端数据库中的内容现在可以直接保存在客户端本地了，这大大减轻了服务器端的负担，同时也加快了访问数据的速度。

### local Storage
用法如下：

```js
// 获取指定key本地存储的值
localStorage.getItem(key)

// 将value存储到key字段
localStorage.setItem(key,value)

// 删除指定key本地存储的值
localStorage.removeItem(key)

// 清除所有本地存储的值
localStorage.clear()
```
优点：

相较于cookie的小容量（4K）来说容量较大（5MB）
简单易用，功能强大，原生支持
可离线浏览与使用 ，减少服务器负载
缺点与限制：

无法跨域，运行在不同域的JavaScript无法调用其他域`localStorage`的数据
只能储存字符串型的数据，无法保存数组和对象，但可以通过`join`、`toString`和`JSON.stringify`等方法先转换成字符串再储存
安全性不高，请勿保存敏感信息
如果多人开发容易造成key重复导致全局污染
### Session Storage
用法特点与local Storage相似：

```js
// 获取指定key本地存储的值
sessionStorage.getItem(key)

// 将value存储到key字段
sessionStorage.setItem(key,value)

// 删除指定key本地存储的值
sessionStorage.removeItem(key)

// 清除所有本地存储的值
sessionStorage.clear()
```
`Session Storage`与`local Storage`的区别在于保存的周期,`Session Storage`为临时保存，当用户关闭浏览器时自动删除数据，`local Storage`则是永久保存，除非主动删除。

## 三、组件之间传值（eventBus）
此方法适用于小项目，页面较少的场景。
`EventBus`又称事件总线,在Vue中可以使用`EventBus`来作为沟通桥梁的概念,就像是所有事件共用相同的事件中心,可以向该中心发送事件或接收事件,所有组件都可以上下平行的通知其他组件。

用法如下：

```js
// 首先在全局中通过new Vue()生成一个vue实例来实现事件总线:
var EventBus = new Vue();

//在需要传递参数的组件中，用$emit发送需要传递的值，键名可以自己定义（可以为对象）
eventBus.$emit('eventBusName', message);
　　
//在需要接受参数的组件中，用$on接受该值（或对象），val即为传递过来的值
eventBus.$on('eventBusName', (val) => {　      
      console.log(val)
})

// 最后记住要在beforeDestroy()中关闭这个eventBus
eventBus.$off('eventBusName');
```
## 四、Vuex
此方法适用于大型项目，页面较多的场景。
因为对于多层嵌套的组件进行传值将会非常繁琐，并且对于兄弟组件间的状态传递也无能为力，所以Vuex就帮我们解决这种困难。

详细用法：[Vuex官方文档](https://vuex.vuejs.org/zh/)