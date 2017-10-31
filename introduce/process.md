## 全局方法

!> 项目中可能会出现重复的方法，可通过`this.$methods`调用

全局方法调用实例

```js
this.$methods.demo()
```

通过在全局Vue挂载methods方法，所有全局方法封装在`utils/methods`中

```js
let methods = {
	getDate(){
		return new Date()
	}
}

export default methods
```

在main中挂载

```js
import methods from './utils/methods'
// 引入全局公共调用方法
Vue.prototype.$methods = methods
```