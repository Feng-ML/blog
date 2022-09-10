---
title: 'vue3添加动态路由'
date: 2022-04-04 19:57:07
summary: 'Vue3中通过addRoute添加动态路由'
tags: 
	- 动态路由
categories:
  - Vue
---

### 路由方法
Vue2中，有两种方法实现路由权限动态渲染：
```js
  router.addRoutes(parentOrRoute, route)    //添加单个
  router.addRoute(routes)                  //添加多个
```
但在Vue3中，只保留了 `addRoute()` 方法。

首先，假设路由如下：

```js
const menu = [{
  id: 'system-manage',
  name: 'system-manage',
  path: '/system',
  meta:{
    title: '系统管理',
    icon: 'setting',
  },
  compoent: 'layout',
  children: [
    {
      id: 'role',
      name: 'role',
      path: '/system/role',
      meta:{
        title: '角色管理',
        icon: 'user-filled',
      },
      compoent: 'view/system/role',
      children: []
    }
  ]
}]
```

```js
// 递归替换引入component
function dynamicRouter(routers) {
  const list = []
  routers.forEach((itemRouter,index) => {
    list.push({
      ...itemRouter,
      component: ()=>import(`@/${itemRouter.component}`)
    })
    // 是否存在子集
    if (itemRouter.children && itemRouter.children.length) {
      list[index].children = dynamicRouter(itemRouter.children);
    }
  })

  return list
}

// 防止首次或者刷新界面路由失效
let registerRouteFresh = true
router.beforeEach((to, from, next) => {
  if (registerRouteFresh) {
    // addRoute允许带children添加，所以循环第一层即可
    dynamicRouter(menu).forEach((itemRouter) => {
      router.addRoute(itemRouter)
    })
    next({ ...to, replace: true })
    registerRouteFresh = false
  } else {
    next()
  }
})

```

### scss错误处理

使用这种拼接的方式会导致全局scss多次注入错误，而且需要后台返回文件路径。
![scss报错](https://img-blog.csdnimg.cn/e8e689917c57445db8a85f4a80808ad8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAZm1s5rW35qOg,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


所以改用以下方式：
```js
// 在本地建一个路由表
const path = [{
  path: '/system/role',
  name: '角色管理',
  component: () => import('@/views/system/role')
}]

function dynamicRouter(routers) {
  const list = []
  routers.forEach((itemRouter,index) => {
    // 从本地找组件
    const authRoute = path.find(i=>i.path==itemRouter.path)
    list.push({
      ...itemRouter,
      component: authRoute?.component || Layout //没找到则用默认框架组件
    })
    // 是否存在子集
    if (itemRouter.children && itemRouter.children.length) {
      list[index].children = dynamicRouter(itemRouter.children);
    }
  })

  return list
}
```

如果出现 `Error: Invalid route component` ,看下路径是否正确，还有 `addRoute` 里传的是否是对象，一开始传了数组导致找了半天（扶额

![Invalid route](https://img-blog.csdnimg.cn/548a70ca5d0e4b1db2d15622a4ee2689.png#pic_center)
以上。