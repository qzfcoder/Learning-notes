# 什么是nextTick

模板

```html
<div class="app">
  <div ref="msgDiv">{{msg}}</div>
  <div v-if="msg1">Message got outside $nextTick: {{msg1}}</div>
  <div v-if="msg2">Message got inside $nextTick: {{msg2}}</div>
  <div v-if="msg3">Message got outside $nextTick: {{msg3}}</div>
  <button @click="changeMsg">
    Change the Message
  </button>
</div>
```

js

```js
new Vue({
  el: '.app',
  data: {
    msg: 'Hello Vue.',
    msg1: '',
    msg2: '',
    msg3: ''
  },
  methods: {
    changeMsg() {
      this.msg = "Hello world."
      this.msg1 = this.$refs.msgDiv.innerHTML
      this.$nextTick(() => {
        this.msg2 = this.$refs.msgDiv.innerHTML
      })
      this.msg3 = this.$refs.msgDiv.innerHTML
    }
  }
})
```

点击后，msg1和msg2的内容还是变换之前的。原因式，vuedom更新是异步的

![image-20211022092013896.png](https://i.loli.net/2021/10/22/vtsfQmVRYi9jEy7.png)

在Vue生命周期中，created()钩子函数在进行的DOM操作一定要放在Vue.nextTick（）的回调函数中。在created函数执行的时候，DOM并没有进行任何渲染。这时候进行DOM操作是没有作用的。这时候一定要将DOM操作放在Vue.next()的回调函数中，也就是放在mounted中。这时候的Dom挂在和渲染已经完成了。

又或者在数据变化后要执行某个操作，这个操作需要使用随数据改变而改变的DOM结构的时候，这个操作都应该放进`Vue.nextTick()`的回调函数中。