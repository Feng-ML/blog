---
title: 'vue 组件v-model的使用与踩坑'
date: 2022-09-08 16:07:41
tags: 
	- v-model
categories:
  - Vue
---

### 一、原理
在组件上使用`v-model`，和在表单标签上使用相似，都是一种`语法糖`。
相当于将绑定的值 `modelValue` 通过 `props` 传递给组件，组件中的值改变后，通过触发 `update` 事件更新 `modelValue` ，以达到数据的双向绑定。
### 二、实现
父组件：
```html
<template>
  <div>
    <children v-model="msg"></children>
    <!-- 等效于下方写法 -->
    <children :modelValue="msg" @update:modelValue="msg = $event"></children>
  </div>
</template>

<script>
export default {
  data() {
    return {
      msg: 123,
    };
  },
};
</script>
```
子组件：
```html
<template>
  <div>
    <el-input v-model="inputValue"></el-input>
  </div>
</template>

<script>
export default {
  props: {
  	// 接收父组件传来的值
    modelValue: String,
  },
  emits: ["update:modelValue"],
  data() {
    return {};
  },
  computed: {
    inputValue: {
      set(newValue) {
      	// 发送事件改变父组件的值
        this.$emit("update:modelValue", newValue);
      },
      get() {
        return this.modelValue;
      },
    },
  },
};
</script>
```

### 三、踩坑
按照网上的写法写完后发现输入框中既没有值，而且也无法输入。这说明值没有从父组件传到子组件，子组件也没有触发对应的事件。  
通过查阅 [vue的官方文档](https://v2.cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model)，找到如下说明：

![组件v-model](https://img-blog.csdnimg.cn/39d926c440fc4de6b62036c78fa08284.png)
文档说的是 `v-model` 的默认值是 `value` 的prop和 `input` 事件，而不是我写的 `modelValue` 和 `update` 事件。而且还提供了 `model` 选项来给我们改写默认值。

 1. 修改默认值（方法1）

```js
export default {
  props: {
    modelValue: String,
  },
  emits: ["update:modelValue"],
  // 在子组件中添加 model 选项
  model: {
    prop: 'modelValue',		// 绑定的 prop 名
    event: 'update:modelValue'	// 通知父组件更新的事件名
  },
};
```

 2. 修改prop与事件名（方法2）

```js
export default {
  props: {
  	// 改变prop
    value: String,
  },
  emits: ["input"],
  data() {
    return {};
  },
  computed: {
    inputValue: {
      set(newValue) {
      	// 改变事件名
        this.$emit("input", newValue);
      },
      get() {
        return this.value;
      },
    },
  },
};
```