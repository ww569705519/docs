## 路由懒加载

!> 当打包构建应用时，Javascript 包会变得非常大，影响页面加载。如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了。

结合 Vue 的异步组件和 Webpack 的代码分割功能，轻松实现路由组件的懒加载。

首先，可以将异步组件定义为返回一个 Promise 的工厂函数 (该函数返回的 Promise 应该 resolve 组件本身)：

```js
const Foo = () => Promise.resolve({ /* 组件定义对象 */ })
```

第二，在 Webpack 2 中，我们可以使用动态 import语法来定义代码分块点 (split point)：

```js
import('./Foo.vue') // 返回 Promise
```

!>注意：如果您使用的是 Babel，你将需要添加 syntax-dynamic-import 插件，才能使 Babel 可以正确地解析语法。

结合这两者，这就是如何定义一个能够被 Webpack 自动代码分割的异步组件。

```js
const Foo = () => import('./Foo.vue')
```

在路由配置中什么都不需要改变，只需要像往常一样使用 `Foo`：

```js
const router = new VueRouter({
  routes: [
    { path: '/foo', component: Foo }
  ]
})
```

项目源码实例

```js
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)

// 路由懒加载模式，适合中大型SPA应用
const Foo = () => import('@/components/HelloWorld')

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Hello',
      component: Foo
    }
  ]
})
```
### 把组件按组分块

有时候我们想把某个路由下的所有组件都打包在同个异步块 (chunk) 中。只需要使用 命名 chunk，一个特殊的注释语法来提供 chunk name (需要 Webpack > 2.4)。

```js
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
```
Webpack 会将任何一个异步模块与相同的块名称组合到相同的异步块中。

## keep-alive缓存使用

### 项目实例

App.vue代码如下

```VUE
<template>
  <div id="app">
    <x-header>Vue-WeChat</x-header>  
    <keep-alive :include="include">
        <router-view/>
    </keep-alive>
  </div>
</template>

<script>
import {XHeader } from 'vux'

export default {
   components: {
    XHeader
  },
  name: 'app',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      include:[] // 缓存路由数组
    }
  },
  created(){
    // 检测本地路由开启缓存的实例
    let routers = this.$router.options.routes,include=[]
    routers.forEach((v,k)=>{
      if (v.keepAlive) {
        include.push(v.name)
      }
    })
    this.include = include
  }
}
</script>
```

router.js代码如下

```js
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)

// 路由懒加载模式，适合中大型SPA应用
const Foo = () => import('@/components/HelloWorld')
const Demo = () => import('@/template/demo')
export default new Router({
  routes: [
    {
      path: '/',
      name: 'Hello',
      component: Foo,
      keepAlive:true
    },
    {
      path: '/demo',
      name: 'demo',
      component: Demo,
      keepAlive:true
    }
  ]
})

```
	


##### 如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。为此可以添加一个 keep-alive 指令参数：

```html
<keep-alive>
  <component :is="currentView">
    <!-- 非活动组件将被缓存！ -->
  </component>
</keep-alive>
```

<!-- 在 [API](https://cn.vuejs.org/v2/api/#keep-alive) 参考中查看更多 `<keep-alive>` 的细节。 -->

#### Props：

- include - 字符串或正则表达式。只有匹配的组件会被缓存。
- exclude - 字符串或正则表达式。任何匹配的组件都不会被缓存。
用法：

`<keep-alive>` 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和 `<transition>` 相似，`<keep-alive>` 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。

当组件在 `<keep-alive>` 内被切换，它的 `activated` 和 `deactivated` 这两个生命周期钩子函数将会被对应执行。

!> 在 2.2.0 及其更高版本中，activated 和 deactivated 将会在 `<keep-alive>` 树内的所有嵌套组件中触发。	

主要用于保留组件状态或避免重新渲染。

```html
<!-- 基本 -->
<keep-alive>
  <component :is="view"></component>
</keep-alive>
<!-- 多个条件判断的子组件 -->
<keep-alive>
  <comp-a v-if="a > 1"></comp-a>
  <comp-b v-else></comp-b>
</keep-alive>
<!-- 和 `<transition>` 一起使用 -->
<transition>
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>
</transition>
```

#### include and exclude

>Vue 2.1.0 新增

`include` 和 `exclude` 属性允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示： 

```html
<!-- 逗号分隔字符串 -->
<keep-alive include="a,b">
  <component :is="view"></component>
</keep-alive>
<!-- 正则表达式 (使用 `v-bind`) -->
<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>
<!-- 数组 (使用 `v-bind`) -->
<keep-alive :include="['a', 'b']">
  <component :is="view"></component>
</keep-alive>
```

匹配首先检查组件自身的 name 选项，如果 name 选项不可用，则匹配它的局部注册名称 (父组件 components 选项的键值)。匿名组件不能被匹配。

!> `<keep-alive>` 不会在函数式组件中正常工作，因为它们没有缓存实例。