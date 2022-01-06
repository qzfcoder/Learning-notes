# Vue 权限控制

### 一、 权限相关概念

### 1.1 权限的分类

前端权限的控制本质上来说， 就是控制端的视图层的展示和前端所发送的请求

#### 1.2 前端权限的意义

##### 降低非法操作的可能性
尽可能排除不必要清求， 减轻服务器压力
提高用户体验
根据用户具备的权限为该用户展现自己权限范围内的内容， 避免在界面上给用户带来困扰， 让用户专注于分内之事

### 二、 前端权限控制思路

#### 2.1 菜单的控制

在登录请求中， 会得到权限数据， 当然， 这个需要后端返回数据的支持． 前端根据权限数据， 展示对应的菜单． 点击菜单， 才能查看相关的界面

#### 2.2 界面的控制

如果用户没有登录， 手动在地址栏敲入管理界面的地址， 则需要跳转到登录界面
如果用户已经登录， 如果手动敲入非权限内的地址， 则需要跳转404 界面

#### 2.3 按钮的控制

控制操作的按钮，比如删除， 修改， 增加

#### 2.4 请求和响应的控制

如果用户通过非常规操作， 比如通过浏览器调试工具将某些禁用的按钮变成启用状态， 此时发的请求， 也应该被前端所拦截

### 三、实现步骤

#### 3.1 权限菜单栏控制

用户登录之后服务端返回一个数据，这个数据有菜单列表和token，我们把这个数据放入到vuex中，然后主页根据vuex中的数据进行菜单列表的渲染

 **将数据存储在sessionStorage中，并让其和vuex中的数据保持同**步

#### 3.2 界面的控制

登录成功后，将token数据存储在sessionStorage中，判断是否登录

### 四、实现操作

#### 1、通过控制前置路由来判断是否有用户登录，若有用户登录，则session存在token信息，若session中不存在token信息，则跳转至登录页面

```js
router.beforeEach((to, from, next) => {
  if (to.path === '/login') {
    next()
  } else {
    const token = sessionStorage.getItem('token')
    if (!token) {
      next('/login')
    } else {
      next()
    }
  }
})
```

#### 2、通过设置动态路由，根据当前登录用户，来动态添加所需要的路由信息。

```js
// 动态路由
const tableRule = {
  path: '/table',
  name: 'table',
  component: () => import('@/views/table/ceshi.vue')
}
const imageRule = {
  path: '/image',
  name: 'image',
  component: () => import('@/views/image')
}
const userRule = {
  path: '/users',
  name: 'users',
  component: () => import('@/views/users')
}
。。。。。
```

```js
// 基本路由信息
const routes = [
  {
    path: '/login',
    // name: 'login', // 这里如果有name 控制台会提醒
    component: () => import('@/views/login')
  },
  {
    path: '/',
    component: Layout,
    children: [
      {
        path: '',
        // name: 'home',
        component: () => import('@/views/home')
      },
      {
        path: '/chart',
        component: () => import('@/views/chart')
      },
      {
        path: '/cesium',
        component: () => import('@/views/cesium')
      }
    ]
  }
  // 没有找到页面刷新动态路由丢失问题的解决方法 先将404页面注释
  // {
  //   path: '*',
  //   component: () => import('@/views/404')
  // }
]
```

##### 登录的时候存储信息

```js
 login(this.form).then(res => {
        // console.log(res, 'login=>res')
        // 将用户身份存入vuex 普通用户身份: student 管理员用户身份: admin
        this.$store.commit('setRole', res.data.role)

        this.$store.commit('setUsername', res.data.username)
        this.$store.commit('setPhoto', res.data.photo)
        sessionStorage.setItem('token', res.data.token)

        getRoles(res.data.role).then(ret => {
          // console.log(ret.data, 'getRoles=>ret.data')
          // 将对应身份下的路由存储到vuex
          this.$store.commit('setRightList', ret.data)
          this.loading = false
          this.$message.success('登陆成功')

          // 根据用户所具备的权限 动态添加路由规则
          initDynamicRoutes()
          this.$router.push('/')
        })
```

##### 在创建路由的时候，获取用户的权限信息等，在store中存储

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    role: sessionStorage.getItem('role'),
    rightList: JSON.parse(sessionStorage.getItem('rightList') || '[]'),
    username: sessionStorage.getItem('username'),
    photo: sessionStorage.getItem('photo')
  },
  mutations: {
    setRole (state, data) {
      state.role = data
      sessionStorage.setItem('role', data)
    },
    setRightList (state, data) {
      state.rightList = data
      sessionStorage.setItem('rightList', JSON.stringify(data))
    },
    setUsername (state, data) {
      state.username = data
      sessionStorage.setItem('username', data)
    },
    setPhoto (state, data) {
      state.photo = data
      sessionStorage.setItem('photo', data)
    }
  },
  actions: {},
  getters: {}
})
```

##### 动态添加路由的方法

```js
// 路由规则和字符串的映射关系
const ruleMapping = {
  table: tableRule,
  users: userRule,
  image: imageRule
}
export function initDynamicRoutes () {
  // console.log(router)
  // 根据二级权限 对路由规则进行动态的添加
  const currentRoutes = router.options.routes
  // currentRoutes[2].children.push()
  const rightList = store.state.rightList
  // console.log(rightList)
  rightList.forEach(item => { // 如果是没有子路由的话 就直接添加进去 如果有子路由的话就进入二级权限遍历
    // console.log(item, 'item-1')
    if (item.path) {
      const temp = ruleMapping[item.path]
      // 路由规则中添加元数据meta
      temp.meta = item.rights
      currentRoutes[1].children.push(temp)
    }

    item.children.forEach(item => {
      // item 二级权限
      // console.log(item, 'item-2')
      const temp = ruleMapping[item.path]
      // 路由规则中添加元数据meta
      temp.meta = item.rights
      currentRoutes[1].children.push(temp)
    })
  })
  // console.log(currentRoutes)
  router.addRoutes(currentRoutes)
}
```

 如果我们重新刷新的话动态路由就会消失，动态路由是在登录成功之后才会调用的，刷新的时候并没有调用，所以动态路由没有添加上

可以在`app.vue`中的`created中`调用添加动态路由的方法

```js
export default {
  created () {
    // 解决页面刷新动态路由丢失问题 但是如果写了404页面 此函数无效 还未解决该问题
    initDynamicRoutes()
  }
}
```

#### 按钮的控制

![_MP9_ANNE_`__OPDU0LRKA1.png](https://i.loli.net/2021/10/25/mobKE2t1YWu65zJ.png)

获取到权限信息，也同步增加到了session中，我们在html中自定义permission指令，获取当前页面的权限信息，在与当前页面所需权限进行比较，控制显示隐藏等

```js
// 自定义指令的注册
import Vue from 'vue'
import router from '@/router'
Vue.directive('permission', {
  inserted (el, binding) {
    // console.log(el)
    // console.log(binding)
    const action = binding.value.action
    const effect = binding.value.effect
    // 判断 当前的路由所对应的组件中 如何判断用户是否具备action的权限
    // console.log(router.currentRoute.meta, '按钮权限')
    if (router.currentRoute.meta.indexOf(action) === -1) { // 等于-1说明没找到 不具备权限
      if (effect === 'disabled') {
        el.disabled = true
        el.classList.add('is-disabled')
      } else {
        el.parentNode.removeChild(el)
      }
    }
  }
})

```

```vue

        <el-button
          v-permission="{action: 'edit', effect: 'disabled'}"
          size="mini"
          @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
        <el-button
          v-permission="{action: 'delete', effect: 'disabled'}"
          size="mini"
          type="danger"
          @click="handleDelete(scope.$index, scope.row)">删除</el-button>
```



