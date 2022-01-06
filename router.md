# VueRouter路由

Vue框架搭建的项目是SPA（单页面应用），项目中只有一个html。

可以减少页面的刷新，通过改变URL，更新页面视图但不刷新页面

Vue中路由是实现有两种模式，hash模式和history模式

#### hash模式

hash模式是更具url中的hash来实现的，hash模式下的url： 110.102.22.12/#/

通过#后边部分来实现。

hash特点：

- hash在url请求中，但是不包含在http请求中，对服务器没有影响
- 可以监听hash变化（通过onhashchange事件监听）
- 浏览器的历史记录可以保存每一次hash的变化

Vue中hash模式的实现就是监听hashchange来动态修改路由

```js
window.onhashchange = function(event) {
	console.log(event.oldURL, event.newURL) // 监听hash改变的新久路由
	document.getElementById("app").innerHTML = x // 更新页面内容
}
```

#### history模式

H5给history对象提供了History Interface。出现了History路由实现模式（history模式下。没有#号）

window.history对象中包含着浏览器的历史记录。

history中包含着一些常用的history方法

- history.back() 等于点击浏览器回退
- history.forward() 等于点击浏览器前进
- history.go(n) n为前进页面的个数，-1 后退一个页面，1为前进一个页面

Vue中路由的实现，我们是通过History对象在h5新增的两个方法实现的 pushState() 和 replaceState()

**pushState()**

```js
history.pushState(state, title[, url])
```

- state： 一个JavaScript对象，保存页面的状态。当监听到导航的state属性更新时，就会触发一个popstate事件，之前保存页面状态的JavaScript对象会保存在这个event事件的state中
- title：短标题
- url： 页面地址，不设置默认为当前页面地址。表示页面状态对象药作用的页面地址

**pushState**：就是将页面状态保存到history对象的state属性中，通过监听历史记录来改名当前页面视图

**replaceState()**

```js
history.replaceState(stateObj, title, [url])
```

- stateObj：用于保存页面状态的对象
- title： 短标题
- url：页面地址

replaceState和pushState差不多，一个是添加一个是替换

### hash模式与history模式比较

采用hash模式，则路由只能修改#后面的部分，即只可以设置与当前同文档的url；采用history模式的路由可以设置的新的url，可以是与当前url同源的任意url。
hash模式只可以添加短字符串到历史记录中，history模式可以通过pushState添加任意类型的数据到历史记录栈中。
history模式因为是直接修改url，所以为了防止报404错误，需要服务器配置对应的路由处理。而hash模式不需要。



#### vue-router 多个路由复用同一个组件时候会出现问题



```vue
<router-view :key="key"></router-view>
<script>
computed: {
// 使用computed属性和Date()可以保证每一次的key都是不同的，这样就可以如愿刷新数据了
 key() {
 return this.$route.name !== undefined? this.$route.name +new Date(): this.$route +new Date()

 }
}
</script>
```



#### 动态路由匹配

```js
 routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
//一个“路径参数”使用冒号 : 标记。当匹配到一个路由时，参数值会被设置到 this.$route.params，可以在每个组件内使用。于是，我们可以更新 User 的模板，输出当前用户的 ID：
```

| 模式                          | 匹配路径            | $route.params                          |
| ----------------------------- | ------------------- | -------------------------------------- |
| /user/:username               | /user/evan          | `{ username: 'evan' }`                 |
| /user/:username/post/:post_id | /user/evan/post/123 | `{ username: 'evan', post_id: '123' }` |

```js
{
  // 会匹配所有路径
  path: '*'
}
{
  // 会匹配以 `/user-` 开头的任意路径
  path: '/user-*'
}
//当使用一个通配符时，$route.params 内会自动添加一个名为 pathMatch 参数。它包含了 URL 通过通配符被匹配的部分：
// 给出一个路由 { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'
// 给出一个路由 { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```



```js
const userId = '123'
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user
```

#### 嵌套命名视图

```vue
<!-- UserSettings.vue -->
<div>
  <h1>User Settings</h1>
  <NavBar/>
  <router-view/>
  <router-view name="helper"/>
</div>
```

```js
{
  path: '/settings',
  // 你也可以在顶级路由就配置命名视图
  component: UserSettings,
  children: [{
    path: 'emails',
    component: UserEmailsSubscriptions
  }, {
    path: 'profile',
    components: {
      default: UserProfile,
      helper: UserProfilePreview
    }
  }]
}
```

#### router别名

**`/a` 的别名是 `/b`，意味着，当用户访问 `/b` 时，URL 会保持为 `/b`，但是路由匹配则为 `/a`，就像用户访问 `/a` 一样。**

```js
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```

```js
const Foo = {
  template: `...`,
  beforeRouteEnter(to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

