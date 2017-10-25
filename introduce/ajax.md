## axios

Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。传送门 [Axios](https://www.kancloud.cn/yunye/axios/234845)

### 功能介绍

- 从浏览器中创建 XMLHttpRequests
- 从 node.js 创建 http 请求
- 支持 Promise API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 XSRF

!> 本模板中使用了axios，并且已经封装好其使用方法，如想使用其它插件请在 `main.js` 中关闭

## http.js封装

```js
import Vue from 'vue'
import axios from 'axios'
import qs from "qs"
// 创建实例，可以使用自定义配置新建一个 axios 实例

const Axios = axios.create({
  // 项目基础地址
  baseURL: 'http://rapapi.org',
  // 调用超时时间
  timeout: 10000,
  responseType: "json",
  headers: {'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'}
})

// 添加请求拦截器
Axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    if (config.method === "post" || config.method === "put" || config.method === "delete") {
      // 序列化
      config.data = qs.stringify(config.data);
    }
    // loading显示
    Vue.$vux.loading.show()
    // 若是有做鉴权token , 就给头部带上token
    return config;
  }, function (error) {
    // 对请求错误做些什么
    // loading隐藏
    Vue.$vux.loading.hide()
    return Promise.reject(error);
  });

// 添加响应拦截器
Axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    // loading显示
    Vue.$vux.loading.hide()
    // 成功返回
    if (response.status == 200) {
      return response.data
    }else{
      // 成功返回，但是状态码不是200，返回状态码
      Vue.$vux.toast.text(response.status,'middle')
      return response;
    }
  }, function (error) {
    // 对响应错误做点什么
    // loading隐藏
    Vue.$vux.loading.hide()
    return Promise.reject(error);
  });

// 对axios的实例重新封装成一个plugin ,方便 Vue.use(xxxx)
export default {
  install: function(Vue, Option) {
    Object.defineProperty(Vue.prototype, "$http", { value: Axios });
  }
};
```

### 在main.js里面引入使用

```js
import http from './utils/http.js'
Vue.use(http)
```

### 调用实例

```js
created(){
    this.$http.get('/mockjsdata/27939/test').then(res=>{
      console.log(res)
    },error=>{
      console.log(error)
    })
  }
```

## 请求方法

### get

| name        | type           | default  |description  |
| ------------- |:-------------:| -----:|-----:|
| url   | string |  |接口调用地址 |
| params      | object      |    |请求参数 |

```js
this.$http.get(url,params)
  .then(function (response) {
    // 响应成功
    console.log(response);
  })
  .catch(function (error) {
    // 响应失败 
    console.log(error);
  });
```

### post

| name        | type           | default  |description  |
| ------------- |:-------------:| -----:|-----:|
| url   | string |  |接口调用地址 |
| params      | object      |    |请求参数 |

```js
this.$http.post(url,params)
  .then(function (response) {
    // 响应成功
    console.log(response);
  })
  .catch(function (error) {
    // 响应失败 
    console.log(error);
  });
```

!> 项目中如果碰到多个请求，可以采用并发请求的方式处理

```js
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

this.$http.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // 两个请求现在都执行完成
  }));
```

### 并发

处理并发请求的助手函数

```js
axios.all(iterable)
axios.spread(callback)
```

## 拦截器
在请求或响应被 then 或 catch 处理前拦截它们。

```js
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```

如果你想在稍后移除拦截器，可以这样：

```js
var myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
可以为自定义 axios 实例添加拦截器

var instance = axios.create();
instance.interceptors.request.use(function () {/*...*/});
错误处理
axios.get('/user/12345')
  .catch(function (error) {
    if (error.response) {
      // 请求已发出，但服务器响应的状态码不在 2xx 范围内
      console.log(error.response.data);
      console.log(error.response.status);
      console.log(error.response.headers);
    } else {
      // Something happened in setting up the request that triggered an Error
      console.log('Error', error.message);
    }
    console.log(error.config);
  });
```

可以使用 validateStatus 配置选项定义一个自定义 HTTP 状态码的错误范围。

```js
axios.get('/user/12345', {
  validateStatus: function (status) {
    return status < 500; // 状态码在大于或等于500时才会 reject
  }
})
```