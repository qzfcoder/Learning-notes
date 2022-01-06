# Axios的封装

```js
npm install axios; // 安装axios
```

在项目src目录下，新建一个request文件夹，在里面写一个http.js和api.js。http.js来封装axios，api.js来统一管理接口

```js
// 在http.js中引入axios
import axios from 'axios'; // 引入axios
import QS from 'qs'; // 引入qs模块，用来序列化post类型的数据，后面会提到
// vant的toast提示框组件，可根据自己的ui组件更改。
import { Toast } from 'vant'; 
```

### 环境的切换

我们的项目环境可能有开发环境、测试环境和生产环境。我们通过node的环境变量来匹配我们的默认的接口url前缀。axios.defaults.baseURL可以设置axios的默认请求地址就不多说了。

```js
// 环境的切换
if (process.env.NODE_ENV == 'development') {    
    axios.defaults.baseURL = 'https://www.baidu.com';} 
else if (process.env.NODE_ENV == 'debug') {    
    axios.defaults.baseURL = 'https://www.ceshi.com';
} 
else if (process.env.NODE_ENV == 'production') {    
    axios.defaults.baseURL = 'https://www.production.com';
}
```

### 设置请求超时

通过axios.defaults.timeout设置默认的请求超时时间。例如超过了10s，就会告知用户当前请求超时，请刷新等。

```js
const service = axios.create({
    baseURL: process.env.VUE_APP_BASE_API,
    timeout: 5000 // 请求超时时间
});
```

### 根据环境设置 baseURL

```js
// 根据 process.env.NODE_ENV 区分状态，切换不同的 baseURL
const service = axios.create({
	baseURL: process.env.NODE_ENV === 'production' ? `/java` : '/apis',
})
```

### 统一设置请求头

```js
const service = axios.create({
    ...
	headers: {
        get: {
          'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
          // 在开发中，一般还需要单点登录或者其他功能的通用请求头，可以一并配置进来
        },
        post: {
          'Content-Type': 'application/json;charset=utf-8'
          // 在开发中，一般还需要单点登录或者其他功能的通用请求头，可以一并配置进来
        }
  },
})
```

### 跨域、超时、响应码处理

axios中，提供是否允许跨域的属性——withCredentials，以及配置超时时间的属性——timeout，通过这两个属性，可以处理跨域和超时的问题

```js
const service = axios.create({
	// 跨域请求时是否需要使用凭证
	withCredentials: true,
    // 请求 30s 超时
	timeout: 30000,
	validateStatus: function () {
		// 使用async-await，处理reject情况较为繁琐，所以全部返回resolve，在业务代码中处理异常
		return true
	},
})
```

### 请求和响应的处理

在 axios 中， `transformRequest` 允许在向服务器发送请求前，修改请求数据；`transformResponse` 在传递给 then/catch 前，允许修改响应数据。

```js
const service = axios.create({
    // 在向服务器发送请求前，序列化请求数据
    transformRequest: [function (data) {
        data = JSON.stringify(data)
        return data
    }],
    // 在传递给 then/catch 前，修改响应数据
    transformResponse: [function (data) {
        if (typeof data === 'string' && data.startsWith('{')) {
            data = JSON.parse(data)
        }
        return data
    }]
})
```

### 拦截器

拦截器，分为请求拦截器以及响应拦截器，分别在请求或响应被 then 或 catch 处理前拦截它们

```js
// 请求拦截器
service.interceptors.request.use((config) => {
	return config
}, (error) => {
	// 错误抛到业务代码
    error.data = {}
    error.data.msg = '服务器异常，请联系管理员！'
    return Promise.resolve(error)
})

// 根据不同的状态码，生成不同的提示信息
const showStatus = (status) => {
    let message = ''
    // 这一坨代码可以使用策略模式进行优化
    switch (status) {
        case 400:
            message = '请求错误(400)'
            break
        case 401:
            message = '未授权，请重新登录(401)'
            break
        case 403:
            message = '拒绝访问(403)'
            break
			.....
        default:
            message = `连接出错(${status})!`
    }
    return `${message}，请检查网络或联系管理员！`
}

// 响应拦截器
service.interceptors.response.use((response) => {
    const status = response.status
    let msg = ''
    if (status < 200 || status >= 300) {
        // 处理http错误，抛到业务代码
        msg = showStatus(status)
        if (typeof response.data === 'string') {
            response.data = { msg }
        } else {
            response.data.msg = msg
        }
    }
    return response
}, (error) => {
    // 错误抛到业务代码
    error.data = {}
    error.data.msg = '请求超时或服务器异常，请检查网络或联系管理员！'
    return Promise.resolve(error)
})
```

### 封装get、post方法

**get方法**：我们通过定义一个get函数，get函数有两个参数，第一个参数表示我们要请求的url地址，第二个参数是我们要携带的请求参数。get函数返回一个promise对象，当axios其请求成功时resolve服务器返回 值，请求失败时reject错误值。最后通过export抛出get函数。

```js
/**
 * get方法，对应get请求
 * @param {String} url [请求的url地址]
 * @param {Object} params [请求时携带的参数]
 */
export function get(url, params){    
    return new Promise((resolve, reject) =>{        
        axios.get(url, {            
            params: params        
        }).then(res => {
            resolve(res.data);
        }).catch(err =>{
            reject(err.data)        
    })    
});}
```

**post方法：**

```js
/** 
 * post方法，对应post请求 
 * @param {String} url [请求的url地址] 
 * @param {Object} params [请求时携带的参数] 
 */
export function post(url, params) {
    return new Promise((resolve, reject) => {
         axios.post(url, QS.stringify(params))
        .then(res => {
            resolve(res.data);
        })
        .catch(err =>{
            reject(err.data)
        })
    });
}
```



## api接口统一管理

新建api.js引入我们封装的get和post方法

```js
/**   
 * api接口统一管理
 */
import { get, post } from './http'
```

如果现在有一个接口，为post请求

```js
http：//100.12.33.11/api/xx/xx/xx
export const apiAddress = p => post('api/xx/xx/xx', p('所需要携带的参数'));
```

在页面中

```js
import { apiAddress } from '@/request/api';// 导入我们的api接口
export default {        
    name: 'Address',    
    mounted () {
        this.onLoad();
    },
    methods: {            
        // 获取数据            
        onLoad() {
            // 调用api接口，并且提供了两个参数                
            apiAddress({                    
                type: 0,                    
                sort: 1                
            }).then(res => {
                // 获取数据成功后的其他操作
                ………………                
            })            
        }        
    }
}
```

