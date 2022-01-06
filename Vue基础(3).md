# Vue.js 基础

## Vue.js 简介

> 传统开发，主要的工作是请求数据、修改 DOM 展示数据，DOM 操作与逻辑操作混合在一起，让代码变得十分臃肿。
>
> Vue.js 是一个前端框架，提供了一套开发规则，学习 Vue.js 可以让代码变得更加简洁、高效。

### Vue.js 核心特性

#### 数据驱动视图

MVVM ( Model-View-ViewModel ) 是一种软件架构思想，MVVM 指的是将应用划分为3层，分别为 Model 层、View 层、ViewModel 层。

- Model 层：数据模型，是代码中主要操作的部分。
- View 层：视图模板，对应要显示的标签结构。
- ViewModel 层：用于处理业务逻辑，会自动将 Model 与 View 结合，暴露 Model 中被 View 使用的数据，并将 Model 变化自动更新到 View，实现数据绑定；对于可输入元素（输入框、文本域等）还可将 View 变化自动更新到 Model ，实现双向数据绑定。

优点：

- View 与 Model 分离，降低耦合度。
- 解放 DOM 操作。

缺点：

- Bug 调试难度相对变大。
- 大型项目的 Model 与 View 过多，维护成本较高。

#### 组件化开发

将需要复用的网页功能扩展为 HTML 元素，大大提高开发效率与可维护性。

## 基础操作

### 安装

本地引入：

- 开发版本：https://cn.vuejs.org/js/vue.js
- 生产版本：https://cn.vuejs.org/js/vue.min.js

cdn：

- 最新版：https://cdn.jsdelivr.net/npm/vue/dist/vue.js
- 生产环境（指定版本）：https://cdn.jsdelivr.net/npm/vue@2.6.12

npm：

- 最新稳定版：

  ```js
  npm install vue
  ```

- 制定版本：

  ```js
  npm install vue@版本号
  ```


### Vue实例

> Vue 实例，代表的是MVVM 中的 ViewModel，是 Vue 应用的基础。

创建 Vue 实例后，通过对选项对象进行配置，来设置不同的 Vue 功能。接下来我们将讲解 Vue 实例选项内容。

```js
var vm = new Vue({
   // 选项对象 
});
```

### el

> 实例选项 el 代表 MVVM 中 View 的容器，用于选择要被 Vue 操作的元素。

el 可以为 `选择器字符串` 或 ` DOM 对象` ，代表一个元素，但不能为 html 或 body 。

可通过 vm.$el 访问。

为设置 el 的 vue 实例，也可以通过 vm.$mount() 进行挂载，参数形式与 el 规则相同

```js
var vm = new Vue({
    el: '#app'
});
```

### data

> 实例选项 data 代表 MVVM 中的 Model，  用于存储数据。

数据可通过 vm.$data.数据 或 vm.数据 访问。

```js
var vm = new Vue({
    el: '#app',
    data: {
        title: '标题文本',
        content: '内容文本'
    }
});
console.log(vm.$data.title);
console.log(vm.title);
```

只有声明在 data 中的数据才能在修改后自动更新到 View 中，称为响应式数据。

```html
<div id="app">
    <p>{{ content }}</p>
</div>
```

```js
var vm = new Vue({
    el: '#app',
    data: {
        title: '标题文本',
        content: '内容文本'
    }
});
console.log(vm.$data.title);
console.log(vm.title);
```

data 中存在数组时，索引操作与 length 操作无法自动更新视图，这时可以借助 Vue.set() 方法替代操作。

- Vue.set() 参数1为要设置的数据（例如数组），参数2为键（例如索引），参数3为新值。

```js
var vm = new Vue({
  el: '#app',
  data: {
    contentArr: ['内容1', '内容2', '内容3']
  }
});

// 在控制台中测试代码：
vm.contentArr.length = 0; // 无效
vm.contentArr[1] = '新内容'; // 无效
Vue.set(vm.contentArr, 1, '生效的新内容'); // 生效
```

### 插值表达式

> 插值表达式用于在 View 中进行模板语法操作，可在标签内容中插入数据或操作代码。

书写方式为：{{}}。

内部可以书写表达式，但不能书写语句。

```html
<div id="app">
    <ul>
        <li>计算结果为：{{ 1 + 2 +　3 }}</li>
        <li>比较结果为：{{ 2 > 1 ? 2 : 1 }}</li>
    </ul>
</div>
```

### methods

> methods 用于封装需要在实例中使用的函数。

methods 中的函数内的 this 代表 vm 。

```js
var vm = new Vue({
    el: '#app',
    data: {
        title: '标题文本',
        content: '内容文本'
    },
    methods: {
        output () {
            console.log('标题为' + this.title + ',' + '内容为' + this.content);
        }
    }
});
```

## 指令

> 在 Vue.js 中，指令的就是以 v- 开头的 html 自定义属性，不同的指令用于设置不同功能。

### 内容处理

#### v-once 指令

>  用于让元素内部的插值表达式只生效一次。

```html
<div id="app">
    <p>此内容会随数据变化自动更改：{{content}}</p>
    <p v-once>此内容不会随数据变化自动更改：{{content}}</p>
</div>
```

#### v-text 指令

> 用于给标签设置纯文本内容，设置后原内容被覆盖。

```html
<div id="app">
    <p v-text="content"></p>
</div>
```

#### v-html 指令

> 用于给标签设置结构文本，设置后原内容被覆盖。

```html
<div id="app">
    <p v-html="content"></p>
</div>
```

### 属性绑定

#### v-bind 指令

> 插值表达式只能在内容区域使用，如果要动态设置属性，需要使用 v-bind 指令。

书写为 v-bind:属性 或简写为 :属性

```html
<div id="app">
  <p v-bind:title="title1">这是一段文本内容</p>
  <p :title="title2">这是一段文本内容</p>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    title1: '这是第一个p标签',
    title2: '这是第二个p标签'
  }
});
```

除了可以单个绑定，还可以通过对象同时绑定多个属性

```html
<div id="app">
  <p v-bind="attrObj">这是 p 标签的内容</p>
</div>
```

```js
new Vue({
  el: '#app',
  data: {
    attrObj: {
      id: 'box',
      title: '示例内容',
      class: 'clearFix',
      'data-title': '这是 data-title 的内容'
    }
  }
});
```

#### class 绑定

> 由于 class 可绑定多个值，v-bind 为 class 增加了更多功能。

class 与 :class 可以共存

```html
<div id="app">
  <p class="a" :class="cls"></p>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    cls: 'b'
  }
});
```

:class 可以设置对象，对象的键表示类名，值为布尔类型，用于控制类名是否显示。

- 带 - 的类型需要加 ' '

```html
<div id="app">
  <p :class="{ b:isB, c: isC, 'class-d': true }"></p>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    isB: true,
    isC: false
  }
});
```

:class 设置数组时，可以结合前面两种功能的特点

```html
<div id="app">
  <p :class="['a', {b: isB} ,'c']"></p>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    isB: true
  }
});
```

#### style 绑定

> style 可以设置多个值，v-bind 为 style 增加了更多功能。

style 与 :style 可以共存，重复时 :style 会覆盖 style, :style 的值为对象，键为属性名，值为属性值。

```html
<div id="app">
  <p style="width:100px;" :style="styleObj">标签内容</p>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    styleObj: {
      width: '200px',
      height: '100px',
      border: '1px solid #ccc'
    }
  }
});
```

设置数组值时，可以传入多个样式对象

```html
<p style="width:100px;" :style="[styleObj1, styleObj2]">标签内容</p>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    styleObj1: {
      height: '100px',
      width: '200px'
    },
    styleObj2: {
      border: '1px solid #ccc',
      color: 'blue'
    }
  }
});
```

### 渲染指令

#### v-for 指令

> 用于遍历数据生成结构，数组与对象均可操作。

```html
<div id="app">
    <!-- 数组遍历 -->
    <ul>
        <li v-for="item in arr">{{item}}</li>
    </ul>
    <!-- 数组遍历：含索引 -->
    <ul>
        <li v-for="(item, index) in arr">{{item}}</li>
    </ul>
    <!-- 对象遍历 -->
    <ul>
        <li v-for="value in obj">{{value}}</li>
    </ul>
    <!-- 对象遍历：含键名 -->
    <ul>
        <li v-for="(value, key) in obj">{{value}}</li>
    </ul>
    <!-- 对象遍历：含键名、索引值 -->
    <ul>
        <li v-for="(value, key, index) in obj">{{value}}</li>
    </ul>
</div>
```

```js
new Vue({
    el: '#app',
    data: {
    	arr: ['内容1', '内容2', '内容3'],
        obj: {
            content1: '内容1',
            content2: '内容2',
            content3: '内容3'
        }
    }
});
```

- 使用 v-for 的同时，应始终指定唯一的 key 属性，可以提高渲染性能并避免问题

  - 可通过调整数据位置来演示渲染问题。

  ```html
  <div id="app">
      <ul>
          <li v-for="item in items" :key="item.id">{{value}}</li>
      </ul>
  </div>
  ```

  ```js
  new Vue({
      el: '#app',
      data: {
      	items: [
              {
                  id: 1,
                  content: '内容1'
              },
              {
                  id: 2,
                  content: '内容2'
              },
              {
                  id: 3,
                  content: '内容3'
              }
          ]
      }
  });
  ```

- 通过 <template> 标签设置模板占位符，可以将部分元素或内容作为整体进行操作。

  ```html
  <div id="app">
      <template>
          <span>奇数行</span>
          <span>偶数行</span>
      </template>
  </div>
  ```

- 遍历对象生成元素后，如希望给对象添加新的属性并自动更新，则需要使用 Vue.set() 设置，普通赋值无效。

  - 建议：最好将所有数据提前初始化好

  ```html
  <div id="app">
  	<ul>
    		<li v-for="value in obj">{{value}}</li>
  	</ul>
  </div>
  ```

  ```js
  var vm = new Vue({
    el: '#app',
    data: {
      obj: {
          content1: '内容1',
          content2: '内容2',
          content3: '内容3'
      }
    }
  });

  // 在控制台中测试代码：
  vm.obj.content4 = '内容4'; // 无效，注：将错误写法注释后再测试正确代码
  Vue.set(vm.obj, 'content4', '内容4')； // 生效
  ```

- 同样，删除对象属性通过 delete 操作无法触发视图更新 ，需要通过 Vue.delete() 操作

  - Vue.delete() 参数1为要操作的对象，参数2为属性名

    ```js
    var vm = new Vue({
      el: '#app',
      data: {
        obj: {
            content1: '内容11',
            content2: '内容22',
            content3: '内容33'
        }
      }
    });

    // 在控制台中测试代码：
    delete vm.obj.content4; // 无效，注：将错误写法注释后再测试正确代码
    Vue.delete(vm.obj, 'content4')； // 生效
    ```


#### v-show 指令

> 用于控制元素显示与隐藏，适用于显示隐藏频繁切换时使用。

- 值为 true 时元素显示，false 时隐藏。

```html
<div id="app">
    <p v-show="true">这个元素会显示</p>
    <p v-show="false">这个元素会隐藏</p>
</div>
```

- 由于 v-show 本质为 display 控制的功能，所以无法与 <template> 结合使用。

#### v-if 指令

> 用于控制元素的创建与移除。

```html
<div id="app">
    <p v-if="2>11">这个元素不会创建</p>
    <p v-else-if="2>1">这个元素会创建</p>
    <p v-else>这个元素不会创建</p>
</div>
```


可通过 v-if 结合 <template> 控制多个同级元素。

```html
<div id="app">
	<template v-if="bool">
    	<div>元素1</div>
		<div>元素2</div>
    </template>
</div>
```

给使用 v-if 的同类型元素添加不同的 key 属性

- 由于 Vue.js 默认会尽可能高效的更新 DOM，当通过 v-if 切换两个同类型元素时并不会重新创建，而是对以存在的元素进行修补，但这种操作可能会导致问题，我们可以为元素设置不同的 key 属性。

```html
<div id="app">
  <div v-if="type==='username'">
    请输入用户名: <input type="text">
  </div>
  <div v-else>
    请输入邮箱: <input type="text">
  </div>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    type: 'username'
  }
});
```

v-if 与 v-for 一起使用时， v-for 优先级更高，为了提高执行效率，建议将 v-if 设置给父元素。

```html
<div id="app">
    <ul v-if="bool">
        <li v-for="item in items" :key="item.id">{{value}}</li>
    </ul>
</div>
```


### 事件处理

#### v-on 指令

> 用于进行事件绑定。

书写为 `v-on:事件类型名` 或简写为 `@事件类型名`，值为事件处理程序，可书写简单代码或函数

```html
<div id="app">
	<p>{{ content }}</p>
	<button v-on:click="content='新内容1'">点击修改内容</button>
	<button @click="content='新内容2'">点击修改内容</button>
</div>
```

处理程序代码较多时，可以在 methods 中设置函数，并设置为事件处理程序

- 注意：函数内的 this 为 Vue 实例

```html
<div id="app">
    <p>{{ content }}</p>
	<button @click="fn">点击修改内容</button>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    content: '默认内容',
  },
  methods: {
    fn () {
      this.content = '新内容';
    }
  }
});
```

事件对象访问：

```js
var vm = new Vue({
  el: '#app',
  data: {
    content: '默认内容',
  },
  methods: {
    fn (event) {
      console.log(event);
    }
  }
});
```

设置事件时传参，并手动设置事件对象

```html
<div id="app">
    <p>{{ content }}</p>
	<button @click="fn(content, $event)">按钮</button>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    content: '默认内容',
  },
  methods: {
    fn (content, event) {
      console.log(content);
      console.log(event);
    }
  }
});
```

### 表单输入绑定

> v-model 用于给 <input> 、<textarea> 及 <select> 元素设置双向数据绑定。

#### 文本输入框

```html
<div id="app">
    <p>input内容为：{{ value1 }}</p>
	<input type="text" v-model="value1">
    
	<p>textarea内容为：{{ value2 }}</p>
	<textarea v-model="value2"></textarea>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    value1: '',
    value2: ''
  }
});
```

#### 单选按钮

```html
<div id="app">
    <p>radio数据为： {{ value3 }}</p>
	<input type="radio" id="one" value="1" v-model="value3">
	<label for="one">选项一</label>
	<input type="radio" id="two" value="2" v-model="value3">
	<label for="two">选项二</label>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    value3: ''
  }
});
```

#### 复选框

- 单个复选框绑定时，数据为布尔类型
- 多个复选框绑定时，数组为数组类型，用于接收多个选项数据。

```html
<div id="app">
	<p>单个checkbox选中的数据为： {{ value4 }}</p>
	<input type="checkbox" id="item" value="选项内容" v-model="value4">
	<label for="item">选项</label>
    
	<p>多个checkbox选中的数据为： {{ value5 }}</p>
	<input type="checkbox" id="one" value="选项一内容" v-model="value5">
	<label for="one">选项一</label>
	<input type="checkbox" id="two" value="选项二内容" v-model="value5">
	<label for="two">选项二</label>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    value4: '',
    value5: []
  }
});
```

#### 选择框

```html
<div id="app">
    <p>单选select数据为： {{ value6 }}</p>
    <select v-model="value6">
      <option value="">请选择</option>
      <option value="1">选项一</option>
      <option value="2">选项二</option>
      <option value="3">选项三</option>
    </select>
    <p>多选select数据为： {{ value7 }}</p>
    <select v-model="value7" multiple>
      <option value="1">选项一</option>
      <option value="2">选项二</option>
      <option value="3">选项三</option>
    </select>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    value6: '',
    value7: []
  }
});
```

### 修饰符

> 修饰符是以点开头的指令后缀，用于给当前指令设置特殊操作。

#### 事件修饰符

> 事件修饰符用于修饰 v-on 指令

- .prevent：用于阻止默认事件行为，相当于 event.preventDefault()
- .stop：用于阻止事件传播，相当于 event.stopPropagation()
- .once：用于设置事件只会触发一次
- 修饰符可以连续书写：例如 @click.prevent.once
- 查询文档学习更多

#### 按键修饰符

> 按键修饰符用于键盘事件（例如 keyup ）可以由哪种案件触发

- .enter 点击回车键
- .esc 点击 esc 键
- 查询文档学习更多

#### 系统修饰符

> 用于实现事件在点击系统按钮时触发。

- .ctrl
- .alt
- .shift
- 查询文档学习更多

#### 鼠标修饰符

> 用于实现事件在点击鼠标指定按钮时触发。

- .left 点击鼠标左键
- .right 点击鼠标右键
- .middle 点击鼠标中键

#### v-model 修饰符

- .lazy 用于将 v-model 的触发方式由 input 事件触发更改为 change 事件触发。

- .number 用于自动将用户输入的值转换为数值类型，如无法被 parseFloat() 转换，则返回原始内容。
- .trim 用于自动过滤用户输入首尾两端空格

### 自定义指令

#### 自定义全局指令

> 自定义全局指令可以被任意 Vue 实例或组件使用。

Vue.directive() 用于设置全局自定义指令

- 参数1： 指令名称，无需书写 v- , 在视图中使用时添加 v- 使用
- 参数2： 配置对象，用于设置自定义指令的功能

```html
<div id="app">
    <input type="text" v-focus>
</div>
```

```js
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时执行 inserted
  inserted: function (el) {
    // el 为设置指令的 DOM 元素
    // 通过 DOM 语法设置功能即可，例如设置输入框自动获取焦点
    el.focus();
  }
});
```

#### 自定义局部指令

> 自定义局部指令只能在当前 Vue 实例或组件内部使用。

```js
new Vue({
    // ...省略其他代码
    directives: {
        focus: {
    		inserted (el) {
				el.focus();
        	}
		}
    }
});
```

## 过滤器

>Vue.js 允许自定义过滤器，可被用于对插值表达式与 v-bind 中的文本进行处理。

### 全局过滤器

> 全局过滤器可以被任意 Vue 实例或组件使用。


过滤器本质为函数，数据自动传入到函数中处理，最终展示的数据为返回值。

```js
Vue.filter('过滤器名称', function (value) {
    // value 为传入到过滤器中的数据
	return '处理结果';
});
```

```html
<div id="app">
	<!-- 在 v-bind 中 -->
	<div v-bind:id="id | filterId"></div>
    
    <!-- 在插值表达式中 -->
	<div>{{ content | filterContent }}</div>
    
    <!-- 同时设置多个过滤器: 数据会从左到右依次进行过滤 -->
	<div>{{ content | filterA | filterB }}</div>
    
    <!-- 给过滤器传入多个参数 -->
	<div>{{ content | filterContent(par1, par2) }}</div>
</div>
```

### 局部过滤器

> 局部过滤器只能在当前 Vue 实例或组件内部使用。

```js
new Vue({
    // ...省略其他代码
    filters: {
        过滤器名称: function (value) {
        	return '处理结果';
    	}
    }
});
```

## 计算属性

>Vue.js 的视图不建议书写复杂逻辑，这样不利于维护，所以提供了计算属性来进行处理。

计算属性本质本函数，在视图模板中访问函数名时函数会自动调用。

```js
var vm = new Vue({
  el: '#app',
  data: {
    arr: [1, 2, 3, 4, 5]
  },
  computed: {
    result () {
      var arr = this.arr;
      var sum = 0;
      for (var i = 0; i < arr.length; i++) {
        sum += arr[i];
      }
      return sum;
    }
  }
});
```

```html
<div id="app">
    <p>{{ result }}</p>
    <p>{{ result }}</p>
    <p>{{ result }}</p>
</div>
```

### computed 与 methods 的区别

- computed 用于计算操作，具有缓存性，初次计算后的结果会被缓存，只有当内部依赖的数据改变后，下次获取 computed 时才会重新执行计算；否则，多次获取 computed 值只会读取缓存的结果，而不会重新执行。
- methods 无缓存性，每次调用都会执行，常用于处理非计算操作。

### 常见操作

- 通过计算属性进行处理，并结合 v-for 遍历计算属性结果，用于避免 v-if 与 v-for 结合使用。

  ```html
  <div id="app">
      <ul>
  	    <li v-for="item in result" :key="item">
              数值为:{{ item }}
          </li>
  	</ul>
  </div>
  ```

  ```js
  var vm = new Vue({
    el: '#app',
    data: {
      arr: [1, 2, 3, 4, 5, 6, 7, 8]
    },
    computed: {
      result () {
        // 筛选出偶数
        return this.arr.filter((item) => item % 2 === 0);
      }
    }
  });
  ```

### 计算属性的 setter

> 你会发现，计算属性无法像 methods 一样进行传参，那我们要如何针对不同数据进行操作呢？

```js
var vm = new Vue({
   computed: {
     getResult: {
       // getter
       get: function () {
         // 逻辑代码
       },
       // setter
       set: function (newValue) {
          // 逻辑代码
       }
     }
   } 
});
```





## 侦听器

>用于监听数据变化并执行指定操作。

```js
new Vue({
	el: '#app',
    data: {
        value: ''
    },
    watch: {
    	// 侦听器名称为要监视的 data 中的属性名
        value (newValue, oldValue) {
    		// newValue 为新数据
    		// oldValue 为旧数据
			
		}
    }
});
```

## 综合案例：TodoMVC

### 案例说明

- 官网：http://todomvc.com/
- 模板：https://github.com/tastejs/todomvc-app-template.git
  - git 克隆到本地
  - 在项目目录下通过 npm install 安装依赖

### 需求分析

- 事项列表展示
  - 有事项的情况
  - 没有事项的情况
- 状态栏展示
  - 个数展示
  - 单位处理
- 事项状态切换
  - 单个事项状态切换
  - 所有事项状态切换
    - 全部切换按钮操作
    - 单个事项切换按钮操作
- 事项新增
  - 内容检测
  - 回车新增
- 事项删除

  - 单个事项删除
  - 已完成事项删除
- 事项编辑
  - 触发编辑

  - 取消标记

  - 保存编辑
- 事项筛选

  - 点击切换显示类别
  - 更新渲染所有事项
- 事项数据持久化
  - 读取本地存储
  - 更新本地存储

### 功能说明

#### 事项列表展示

- 引入 vue.js 文件，创建 Vue 实例设置挂载元素。

- 在 data 中设置 todos 存储初始数据

  ```js
  new Vue({
  	el: '#app',
  	data: {
  		todos: [
  			{ id: 1, title: '示例内容1', completed: false },
  			{ id: 2, title: '示例内容2', completed: true },
  			{ id: 3, title: '示例内容3', completed: true }
  		]
  	}
  })
  ```

- 设置事项视图

  ```html
  <li v-for="todo in todos" :key="todo.id" :class="{completed:todo.completed}">
  	<div class="view">
  		<!-- 选框 -->
  		<input class="toggle" type="checkbox" v-model="todo.completed">
  		<label>{{ todo.title }}</label>
  		<button class="destroy"></button>
  	</div>
  	<!-- 编辑输入框 -->
  	<input class="edit" value="Rule the web">
  </li>
  ```

- 设置有无事项时

  ```html
  <section class="main" v-show="todos.length">
  	...
  </section>
  <footer class="footer" v-show="todos.length">
  	...
  </footer>
  ```



### 状态栏信息展示

- 个数展示

  ```js
  computed: {
  	remaining () {
  		return this.todos.filter(function (todo) {
  			return !todo.completed;
  		}).length;
  	}
  }
  ```

  ```html
  <footer class="footer" v-show="todos.length">
      <span class="todo-count"><strong>{{ remaining }}</strong> items left</span>
  	...
  </footer>
  ```

- 单位处理

  ```js
  methods: {
  	pluralize: function (word) {
  		return word + (this.remaining === 1 ? '' : 's');
  	}
  },
  ```

  ```html

  <footer class="footer" v-show="todos.length">
      <span class="todo-count"><strong>{{ remaining }}</strong> {{ pluralize('item') }} left</span>
  	...
  </footer>
  ```


### 事项状态切换

#### 单个事项切换

> 单个事项切换可能导致全部切换选框状态变化。

```js
computed: {
	...
	allDone: function () {
		return this.remaining === 0;
	}
},
```

单个选框操作时，全部选框会访问 allDone 的 getter ，也就是读取 allDone 数据

```html
<section class="main">
	<input id="toggle-all" class="toggle-all" type="checkbox" v-model="allDone">
	...
</section>
```

#### 多个事项切换

> 主动操作全部切换选框，需要给 allDone 设置 setter 来实现

```js
computed: {
	...
	allDone: {
		get: function () {
			return this.remaining === 0;
		},
		// 用于设置全部切换选框的切换操作
		set: function (value) {
			this.todos.forEach(function (todo) {
				todo.completed = value;
			})
		}
	}
},
```



### 事项新增

```js
methods: {
	...
	addTodo: function () {
        // 思考此处使用 trim() 与 .trim 修饰符的区别
		var value = this.newTodo;
		if (!this.newTodo) {
			return;
		}
		this.todos.push({ id: this.todos.length + 1, title: value, completed: false });
		this.newTodo = '';
	}
}
```

```html
<header class="header">
	<h1>todos</h1>
	<input class="new-todo" 
           placeholder="准备做点什么?" 
           autofocus 
           v-model.trim="newTodo" 
           @keyup.enter="addTodo">
</header>
```



### 事项删除

#### 单个删除

```html
<li v-for="todo in todos" :key="todo.id" :class="{completed: todo.completed">
	<div class="view">
		...
		<button class="destroy" @click="removeTodo(todo)"></button>
	</div>
	...
</li>
```

```js
methods: {
    ...
    removeTodo: function (todo) {
        var index = this.todos.indexOf(todo);
        this.todos.splice(index, 1);
    },
}
```

#### 已完成事项删除

- 只有待办事项少于总数时，说明存在已完成事项，显示删除按钮	

```html
<footer class="footer" v-show="todos.length">
	...
	<button class="clear-completed" v-show="todos.length > remaining">Clear completed</button>
</footer>
```

```js
methods: {
    ...
    removeCompleted: function () {
        this.todos = this.todos.filter(function (todo) {
            return !todo.completed;
        });
    },
}
```



### 事项编辑

#### 触发编辑

- 双击事项时记录正在编辑的 todo 与 todo 的原始内容，被编辑的 li 需要设置 editing 类名，

  ```js
  data: {
  	...
  	editingTodo: null,
      titleBeforeEdit: '',
  },
  ```

  ```html
  <li v-for="todo in todos" 
      :key="todo.id" 
      :class="{completed: todo.completed, editing: todo === editingTodo}">
  	<div class="view">
  		<input class="toggle" type="checkbox" v-model="todo.completed">
  		<label @dblclick="editTodo(todo)">{{ todo.title }}</label>
  		<button class="destroy"></button>
  	</div>
  	<input class="edit">
  </li>
  ```

- 保存 todo 原内容，用于取消时还原

  ```js
  methods: {
      ...
  	editTodo: function (todo) {
  		this.editingTodo = todo;
  		this.titleBeforeEdit = todo.title;
  	}
  },
  ```

- 触发编辑后，输入框无法自动获取焦点，通过自定义指令进行设置

  ```js
  directives: {
  	'todo-focus': function (el, binding) {
  		if (binding.value) {
  			el.focus();
  		}
  	}
  }
  ```

  ```html
  <li v-for="todo in todos" 
      :key="todo.id" 
      :class="{completed: todo.completed, editing: todo === editingTodo}">
  	...
  	<input class="edit" 
             v-model="todo.title"
             v-todo-focus="todo === editingTodo">
  </li>
  ```

#### 取消编辑

- 点击 esc 键取消编辑。

  ```js
  methods: {
  	...
      // 取消编辑
      cancelEdit: function (todo) {
          this.editingTodo = null;
          todo.title = this.titleBeforeEdit;
      }
  }
  ```

  ```html
  <li v-for="todo in todos" 
      :key="todo.id" 
      :class="{completed: todo.completed, editing: todo === editingTodo}">
  	...
  	<input class="edit" 
             v-todo-focus="todo === editingTodo" 
             v-model="todo.title" 
             @keyup.esc="cancelEdit(todo)">
  </li>
  ```

  

#### 保存编辑

- 点击回车键或失去焦点时保存编辑结果

- 如果内容为空，则删除 todo

  ```html
  <li v-for="todo in todos" 
      :key="todo.id" 
      :class="{completed: todo.completed, editing: todo === editingTodo}">
    	...
    	<input class="edit" 
             v-todo-focus="todo === editingTodo" 
             v-model="todo.title" 
             @keyup.esc="cancelEdit(todo)"
             @keyup.enter="editDone(todo)" 
             @blur="editDone(todo)">
  </li>
  ```

  ```js
  methods: {
  	...
  	// 保存编辑
  	editDone (todo) {
          if (!this.editingTodo) {
              return;
          }
          this.editingTodo = null;
          todo.title = todo.title.trim();
          if (!todo.title) {
              // 删除
              this.removeTodo(todo);
          }
      }
  },
  ```





### 事项筛选

- 记录当前渲染条目类别，点击时切换类别并设置类名

  ```js
  data: {
  	...
  	// 记录当前显示的 todo 类别，可设置3种值：all active completed
  	todoType: 'all'
  },
  ```

  ```html
  <footer class="footer" v-show="todos.length">
  	...
  	<ul class="filters">
  		<li>
  			<a href="javascript:;" 
  			:class="{selected: todoType === 'all'}"
  			@click="todoType = 'all'"
  			>All</a>
  		</li>
  		<li>
  			<a href="javascript:;" 
  			:class="{selected: todoType === 'active'}"
  			@click="todoType = 'active'"
  			>Active</a>
  		</li>
  		<li>
  			<a href="javascript:;" 
  			:class="{selected: todoType === 'completed'}"
  			@click="todoType = 'completed'"
  			>Completed</a>
  		</li>
  	</ul>
  ```

- 更新事项渲染方式为计算属性

  ```html
  <li v-for="todo in filteredTodo" :key="todo.id" :class="{completed:todo.completed, editing: todo === editingTodo}">
  	...
  </li>
  ```

  ```js
  computed: {
  	filteredTodo () {
  		return filters[this.todoType](this.todos);
  	},
      ...
  }
  ```

- 将不同类别数据的筛选函数统一存储在对象中，并根据 todoType 进行调用

  ```js
  let filters = {
  	all (todos) {
  		return todos;
  	},
  	active (todos) {
  		return todos.filter(todo => !todo.completed);
  	},
  	completed (todos) {
  		return todos.filter(todo => todo.completed);
  	}
  };
  ```

- （可选）将前面功能中进行的筛选操作替换为 filters 方法的形式

  ```js
  computed: {
      ...
  	remaining () {
          // return this.todos.filter(todo => !todo.completed).length; // 原始写法
          return filters['active'](this.todos).length; // 筛选后更新的写法
      }, 
      ...
  }
  ```

  ```js
  methods: {
  	...
      removeCompleted () {
          // this.todos = this.todos.filter(todo =>!todo.completed); // 原始写法
          this.todos = filters['active'](this.todos); // 筛选后更新的写法
      },
      ...
  },
  ```


### 事项数据持久化

#### 获取本地存储

- 封装读取本地存储功能

  ```js
  const TODOS_KEY = 'todos-vue';
  let todoStorage = {
  	get () {
  		return JSON.parse(localStorage.getItem(TODOS_KEY)) || [];
  	}
  };
  ```

- 将 todos 更改为本地存储获取

  ```js
  data: {
      /* todos: [ 
          { id: 1, title: '示例内容1', completed: false },
          { id: 2, title: '示例内容2', completed: false },
          { id: 3, title: '示例内容3', completed: true }
      ], */
      todos: todoStorage.get(),
      ...
  },
  ```

#### 更新本地存储

- 封装更新本地存储功能

  ```js
  let todoStorage = {
  	...
  	set (todos) {
  		localStorage.setItem(TODOS_KEY, JSON.stringify(todos));
  	}
  };
  ```

- 设置侦听器监视 todos 变化，将数据更新到本地存储

  ```js
  watch: {
  	todos: {
  		deep: true,
  		handler: todoStorage.set
  	}
  },
  ```


## Vue.js 生命周期

> Vue.js 生命周期指的是 Vue 实例从创建到执行，再到销毁的过程。

Vue.js 提供了用于在生命周期不同阶段执行的`生命周期函数`用来在特点阶段设置操作。生命周期函数也称为生命周期钩子。

### 生命周期阶段

![](./media/lifecycle.png)

### 生命周期函数

> 生命周期函数均直接设置在实例的选项对象中。

 - **创建阶段**：(每个实例只能执行一次)
   + beforeCreate：实例初始化之前调用，此时 data 和 methods 都功能尚未初始化。
   + **created**：data 和 methods 等功能已经创建好，可以被访问了，数据操作通常在这个阶段。
   + beforeMount：实例挂载开始之前调用，模板内容还没有被添加到 DOM 中
   + **mounted**：实例挂载完毕，可以通过 vm.$el 访问挂载元素，DOM 操作通常在这个阶段。
 - **运行阶段**：（按需调用，可执行任意次数）
   + beforeUpdate：数据更新时调用，Vue.js 内部还未进行视图更新处理，可以访问更新前的 DOM。
   + updated：DOM 更新完毕，视图和数据都是最新的。
 - **销毁阶段**：(每个实例只能执行一次)
   + beforeDestroy：实例销毁之前调用。在这一步，实例仍然完全可用。
   + destroyed：实例销毁后调用，实例的所有功能都已移除销毁。

# Vue.js 组件

> 组件是可复用的 Vue 实例，内部封装组件的结构、样式、逻辑代码，可通过自定义的组件名称进行复用，形式为自定义 HTML 标签。

## 组件注册

### 全局注册

> 全局注册的组件在注册之后可以用在任意实例或组件中。

```js
Vue.component('组件名', { /* 选项对象 */ });
```

注意：全局注册必须设置在根 Vue 实例创建之前。

### 组件基础

> 由于组件是可复用的 Vue 实例，所以它们可与 `new Vue` 接收相同的选项，例如 `data`、`computed`、`watch`、`methods` 以及生命周期钩子等。仅有的例外是像 `el` 这样根实例特有的选项。

#### 组件命名规则

> 组件命名有两种方式，但无论采用哪种方式定义，**在 DOM 中都只有 kebab-case 可以使用**。

- kebab-case：'my-component-name'
- PascalCase：'MyComponentName'

```js
Vue.component('my-component-a', { /* 选项对象 */ });
Vue.component('my-component-b', { /* 选项对象 */ });
Vue.component('MyComponentC', { /* 选项对象 */ });
Vue.component('MyComponentD', { /* 选项对象 */ });
```

#### template 选项

> template 选项用于设置组件结构，最终会被根实例或其他组件引入。

下面的示例中使用了模板字符串，考虑兼容性时应改为普通字符串。

```js
// 子组件
Vue.component('MyComponentA', {
  template: `
    <div>
      <h3>组件 A 的标题内容</h3>
    </div>`,
});
```

**注意：组件必须只有一个根元素**，否则会报错。

#### data 选项

> data 选项必须为函数，而不是像根实例一样设置为对象。

这种实现方式是为了确保每个组件实例可以维护一份被返回对象的独立的拷贝，不会相互影响。

```js
Vue.component('MyComA', {
  template: `
	<h3>{{ title }}</h3>
  `,
  data: function () {
    return {
      title: '示例内容'
    }
  }
})
```

### 局部注册

> 局部注册的组件只能用在当前实例或组件中。

```js
new Vue({
    ...
    components: {
        'my-component-a': {
            template: '<h3>{{ title }}</h3>',
            data () {
                return { title: 'a 组件示例内容' }
            }
        },
        'my-component-b': {
            template: '<h3>{{ title }}</h3>',
            data () {
                return { title: 'b 组件示例内容' }
            }
        }
    }
});
```

你可能注意到，上面的方式将根实例与多个组件的选项对象书写在一起非常不清晰，所以我们通常会将组件的选项对象单独书写，再设置到某个实例的 components 属性中。

```js
var MyComponentA = { /* ... */ }
var MyComponentB = { /* ... */ }

new Vue({
  el: '#app',
  components: {
    'my-component-a': MyComponentA,
    'my-component-b': MyComponentB
  }
});
```

或者采用 ES6 的对象属性简写方式

```js
new Vue({
  el: '#app',
  components: {
    MyComponentA,
    MyComponentB
  }
});
```

## 组件通信

> 当我们将页面功能设置为多个子组件后，这些子组件如何使用根实例中的数据呢？子组件如何将操作状态反馈给父实例呢？这时就需要组件间进行传值，这种操作称为组件通信。

### 父组件向子组件传值

> 父向子传值需要通过给子组件设置 props 选项实现。

#### Props

props 选项为数组，子组件通过 props 定义任意属性后，父组件再给子组件设置对应属性即可。

> 注意，props 的属性可以像 data 的属性一样可以在子组件中通过 this 访问或在视图模板中通过名称使用，所以 props **不要**与 data 存在同名属性。

```js
// 子组件
Vue.component('my-component', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
});
```

设置方式有2种：`静态属性值`设置方式与 `v-bind 动态绑定` 方式，静态属性只能为字符串类型，而动态绑定可以为**任意**类型。

```html
<!-- 父组件 -->
<div id="app">
    <my-component-a title="示例内容1"></my-component-a>
	<my-component-a :title="'示例内容2'"></my-component-a>
	<my-component-a :title="item.title"></my-component-a>
</div>
```

```js
new Vue({
  el: '#app',
  data: {
    item: {
      title: '父组件中的数据'
    }
  }
});
```

注意：prop 命名推荐使用 **camelCase**，但由于 HTML 属性不区分大小写，所以 props 中的 camelCase 属性在被父组件设置时需要使用对应的 **kebab-case**。

```js
// 子组件
Vue.component('my-component', {
  props: ['myTitle'],
  template: '<h3>{{ myTitle }}</h3>'
});
```

```html
<!-- 父组件 -->
<div id="app">
	<my-component-a my-title="示例内容1"></my-component-a>
	<my-component-a :my-title="'示例内容2'"></my-component-a>
	<my-component-a :my-title="item.title"></my-component-a>
</div>
```

通过 v-for 指令遍历创建组件并传值练习一下吧（注意：key 需要绑定，但无需 props 接收）

#### 单向数据流

> 父子组件间的所有 prop 都是 **单向下行绑定** 的。

每次父级组件发生变更时，子组件所有的 prop 都会刷新为最新值，但反过来则不行。这样可以防止子组件意外变更父级组件的状态，从而导致应用的数据流向难以理解。

如果子组件需要对父组件传递的数据进行处理，应当**定义本地的 data 属性**以保存需要处理的 prop。

```js
// 子组件
Vue.component('my-component', {
  props: ['initialTitle'],
  template: '<h3>{{ myTitle }}</h3>',
  data () {
    return {
      myTitle: this.initialTitle
    }
  }
});
```

需要**特别注意**的是，由于对象和数组是通过引用传入的，所以对于一个**数组或对象类型的 prop** 来说，在子组件中变更这个对象或数组内部的数据 **将会** 影响到父组件的状态。

#### Props 类型

> Prop 可以设置类型检查，这时需要将 props 更改为一个带有验证需求的对象。

类型检查的设置方式有两种：

- 第一种：指定单个类型，设置为对应的构造函数即可，无需验证时设置 null 与 undefined 。

```js
// 子组件
Vue.component('MyComponentA', {
  props: {
    parStr: String,
    parNum: Number,
    parArr: Array,
    parObj: Object,
    parAny: null // parAny: undefined
  },
  template: `
    <div>
      {{ parStr }}
      {{ parNum }}
      {{ parArr }}
      {{ parObj }}
      {{ parAny }}
    </div>
  `
});
```

```js
// 父组件
var vm = new Vue({
  el: '#app',
  data: {
    str: '示例内容',
    num: 100,
    arr: [1, 2, 3],
    obj: {
      content1: '父组件示例示例内容1',
      content2: '父组件示例示例内容2'
    },
    any: '任意类型均可'
  }
});
```

```html
<!-- 父组件 -->
<div id="app">
  <my-component-a 
    :par-str="str"
    :par-num="num"
    :par-arr="arr"
    :par-obj="obj"
    :par-any="any"
  ></my-component-a>
</div>
```

第二种：指定多个类型，可以将 prop 对应类型设置为数组。

```js
// 子组件
Vue.component('MyComponentA', {
  props: {
    parData: [String, Number]
  },
  template: `
    <div>
      {{ parData }}
    </div>
  `
});
```

#### Props 验证

> 如果需要给一个 Prop 设置多种规则，可以将某个 Prop 的值设置为选项对象

Prop 值设置为选项对象后，类型检测通过 type 选项进行设置。

```js
// 子组件
Vue.component('MyComponentA', {
  props: {
    parNum: {
      type: Number
    },
    parData: {
      type: [String, Boolean]
    }
  },
  template: `
    <div>
	  {{ parNum }}
      {{ parData }}
    </div>
  `
});
```


required 用于设置数据为必填项。

```js
// 子组件
Vue.component('MyComponentA', {
  props: {
    parNum: {
      type: Number,
      required: true
    }
  },
  template: `
    <div>
      {{ parNum }}
    </div>
  `
});
```

default 用于给可选项指定默认值，当父组件未传递数据时生效。

```js
// 子组件
Vue.component('MyComponentA', {
  props: {
    parNum: {
      type: Number,
      default: 100
    }
  },
  template: `
    <div>
      {{ parNum }}
    </div>
  `
});
```

注意：当默认值为数组或对象时，必须为工厂函数返回的形式（为了确保多个实例数据独立）。

```js
// 子组件
Vue.component('MyComponentA', {
  props: {
    parArr: {
      type: Array,
      default: function () {
        return [1, 2, 3];
      }
    }
  },
  template: `
    <div>
      {{ parArr }}
    </div>
  `
});
```

validator 用于给传入的 prop 设置校验函数，return 值为 false 时 Vue.js 会发出警告。

```js
// 子组件
Vue.component('MyComponentA', {
  props: {
    parStr: {
      type: String,
      validator: function (value) {
        return value.startsWith('lagou');
      }
    }
  },
  template: `
    <div>
      {{ parStr }}
    </div>
  `
});
```

注意：由于 prop 会在实例创建前进行验证，所以 default 与 validator 无法使用例如 data、methods 等功能。




#### 非 Prop 属性

> 当父组件给子组件设置了属性，但此属性在 props 中不存在，这时会自动绑定到子组件的根元素上。

```html
<!-- 父组件 -->
<div id="app">
  <my-component-a
    demo-attr="示例属性"
    title="示例title"
    style="height: 200px"
    class="colorBlue"
  ></my-component-a>
</div>
```

```html
<!-- 子组件 -->
Vue.component('MyComponentA', {
  template: `<p>子组件内容</p>`
});
```

如果组件根元素已经存在了对应属性，则父组件的提供的值会**替换**组件内部的值。但 class 与 style 例外，当内外都设置时，属性会自动**合并**。

```html
/* 
	修改子组件为以下代码后：
		title 被覆盖，class 与 style 则自动合并。
*/
Vue.component('MyComponentA', {
  template: `
    <p title="原始title" class="fl" style="width: 200px;">子组件内容</p>
  `
});
```

如果不希望继承父组件设置的属性，可以设置 inheritAttrs: false，但 class 与 style 不受影响。

```html
Vue.component('MyComponentA', {
  inheritAttrs: false,
  template: `
    <p title="原始title" class="fl" style="width: 200px;">子组件内容</p>
  `
});
```

### 子组件向父组件传值

#### 自定义事件

> 子向父传值需要通过给子组件设置自定义事件来实现。

例如电商网站的购物车功能中，每个商品的个数需要在购物车中进行统计，如果将商品看作子组件，购物车为父组件，那数据的传递过程就是在通过子组件向父组件传值。

```html
<!-- 父组件 -->
<div id="app">
  <h3>购物车</h3>
  <product-item 
    v-for="product in products"
    :title="product.title"
    :key="product.id"
  ></product-item>
  <p>总数为：{{ totalCount }}</p>
</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    products: [
      { id: 1, title: '苹果1斤' },
      { id: 2, title: '橙子2个' },
      { id: 3, title: '香蕉3根' }
    ],
    totalCount: 0
  }
});
```

```js
Vue.component('product-item', {
  props: ['title'],
  template: `
    <div>
      <span>商品名称：{{ title }}，商品个数： {{ count }}</span>
      <button @click="countIns">+1</button>
    </div>
  `,
  data () {
    return { count: 0 }
  },
  methods: {
    countIns () {
      this.count++;
    }
  }
});
```

当商品个数变化时，我们应该将数据传递给父组件，这时可以通过触发自定义事件来实现。

方法 $emit() 用于触发指定的事件，父组件可以通过监听子组件的事件来得知子组件的状态变化。

```json
this.$emit('事件名');
```

注意，为确保事件名在组件与 DOM 模板内均得以生效，推荐**始终使用 kebab-case 的事件名**。

```js
// 子组件
Vue.component('product-item', {
  ...
  methods: {
    countIns () {
      this.$emit('count-change');
      this.count++;
    }
  }
});
```

父组件监听子组件事件，并设置处理代码。 

```html
<div id="app">
  ...
  <product-item 
	...
    @count-change="totalCount++"
  ></product-item>
  ...
</div>
```

#### 事件传值

现在添加一个`+5` 按钮，如果 `+1` 和 `+5` 使用 count-change 事件，就需要准确告知父组件个数的变化，这时就需要通过事件进行传值。

```js
this.$emit('事件名', '事件传值');
```

给两个按钮分别设置事件，并给 count-change 传递不同值。

```js
Vue.component('product-item', {
  props: ['title'],
  template: `
    <div>
      <span>商品名称：{{ title }}，商品个数： {{ count }}</span>
      <button @click="countIns1">+1</button>
      <button @click="countIns5">+5</button>
    </div>
  `,
  ...
  methods: {
    countIns1 () {
      this.$emit('count-change', 1);
      this.count++;
    },
    countIns5 () {
      this.$emit('count-change', 5);
      this.count+= 5;
    }
  }
});
```

父组件需要接受事件传值并进行相应处理。

```html
<div id="app">
  ...
  <product-item
    ...
    @count-change="totalCount += $event"></product-item>
  ...
</div>
```

或

```html
<div id="app">
  <h3>购物车</h3>
  <product-item
    ...
    @count-change="onCountChange"
  ></product-item>
  ...
</div>
```

```js
var vm = new Vue({
  ...
  methods: {
    onCountChange (productCount) {
      this.totalCount += productCount;
    }
  }
});
```

#### 组件与 v-model 

v-model 用于双向数据绑定，但用于组件时，双向绑定的过程需要通过 props 与自定义事件实现。

```html
<div id="app">
  <p>输入内容为：{{ iptValue }}</p>
  <com-input v-model="iptValue"></com-input>
</div>
```

```js
var ComInput = {
  props: ['value'],
  template: `
    <input 
      type="text" 
      :value="value" 
      @input="$emit('input', $event.target.value)">`
};
new Vue({
  el: '#app',
  data: {
    iptValue: ''
  },
  components: {
    ComInput
  }
})
```

### 非父子组件传值

> 非父子组件，例如一个父组件下的两个兄弟组件或两个没有任何关联的组件。

#### 兄弟组件传值

兄弟组件可以将数据通过父组件进行中转，从而实现传值。

```html
<div id="app">
  <com-a 
    @value-change="value = $event"
  ></com-a>
  <com-b
    :value="value"
  ></com-b>
</div>
```

```js
Vue.component('ComA', {
  template: `
    <div>
      组件A的内容：{{ value }}
      <button 
        @click="$emit('value-change', value)"
      >发送</button>
    </div>`,
  data () {
    return {
      value: '示例内容'
    }
  }
});
    
Vue.component('ComB', {
  props: ['value'],
  template: `
    <div>
      组件B接收到：{{ value }}
    </div>`,
});
    
// 根实例
new Vue({
  el: '#app',
  data: {
    value: ''
  }
});
```

但如果不是兄弟呢？这时再逐层传递数据会十分繁琐，且执行效率很低。

#### EventBus

> EventBus（事件总线）是一个独立的事件中心，用来对不同组件间的传值操作进行管理。

EventBus 通过一个独立的 Vue 实例（bus）来管理组件传值，不同组件传值时通过给 bus 设置事件、触发事件以达到数据传递的目的。总的来说，bus 中存储的只有用来传值的事件，而不存储数据。

**操作方式**

数据的传递过程一定会存在 `发送方` 与 `接收方`两个部分，接收方组件给 注册事件，发送方组件触发 bus 的对应事件，即可实现组件传值。

首先创建一个新的 Vue 实例作为 EventBus 的事件中心，通常设置为独立文件。

```js
// EventBus.js
var bus = new Vue();
```

功能中，商品组件会进行个数操作，属于发送方，个数操作同时触发 bus 事件并传值。

```js
// 商品组件
Vue.component('product-item', {
  template: `
    <div>
      <span>商品名称：苹果，商品个数： {{ count }}</span>
      <button @click="countIns">+1</button>
    </div>`,
  data () {
    return { count: 0 }
  },
  methods: {
    countIns () {
      bus.$emit('countChange', 1);
      this.count++;
    },
  }
});
```

计数组件需要统计个数，属于接收方，当组件实例创建后给 bus 注册对应事件接收数据，在 created() 中通过 $on() 进行设置。

```js
// 计数组件
Vue.component('product-total', {
  template: `
    <p>总个数为：{{ totalCount }}</p>
  `,
  data () {
    return { totalCount: 0 }
  },
  created () {
    bus.$on('countChange', (productCount) => {
      this.totalCount += productCount;
    });
  }
});
```

最后创建根实例。

```js
// 根实例
new Vue({
  el: '#app',
});
```

### 其他通信方式

> 下面的方式并不是推荐方式 ，只适合开发简单的 Vue 应用中使用，复杂应用使用可能会导致数据混乱难以维护，非必要情况不建议使用。这里作为补充讲解。

#### $root

> $root 用于访问当前组件树根实例，设置简单的 Vue 应用时可以通过此方式进行组件传值。

```html
<!-- 父组件 -->
<div id="app">
  <p>父组件数据： {{ count }}</p>
  <com-a></com-a>
  <com-b></com-b>
</div>
```

```js
// 父组件
new Vue({
  el: '#app',
  data: {
    count: 0
  },
  components: {
    ComA,
    ComB
  }
});
```

```js
// 组件 A: 要书写在根实例创建之前
var ComA = {
  template: `
    <div>
      组件 A : {{ $root.count }}
      <button @click="clickFn">+1</button>
    </div>
  `,
  methods: {
    clickFn () {
      this.$root.count++;
    }
  }
};
```

```js
// 组件 B: 要书写在根实例创建之前
var ComB = {
  template: `
    <div>
      组件 B : {{ $root.count }}
      <button @click="clickFn">+1</button>
    </div>
  `,
  methods: {
    clickFn () {
      this.$root.count++;
    }
  }
};
```

另外，Vue.js 中还提供了 $parent 与 $children 用于进行父子组件便捷访问，可以通过文档查询。

#### $refs

> $refs 用于获取设置了 ref 属性的 HTML 标签或子组件。

给普通  HTML 标签设置 ref 属性，可以通过 $refs 获取 DOM 对象。

```html
<div id="app">
  <input type="text" ref="inp">
  <button @click="fn">按钮</button>
</div>
```

```js
new Vue({
  el: '#app',
  methods: {
    fn () {
      this.$refs.inp.focus();
    }
  }
});
```

给子组件设置 ref 属性，通过 $refs 获取到的是子组件实例，需要在子组件渲染完毕后才能获取。

```html
<!-- 父组件 -->
<div id="app">
  <com-a ref="comA"></com-a>
</div>
```

```js
// 父组件
var vm = new Vue({
  el: '#app',    
  components: {
    ComA
  },
  mounted () {
    console.log(this.$refs);
    this.$refs.comA.value = '修改子组件数据';
  },
});
```

```js
// 子组件
var ComA = {
  template: `<p>组件 A：{{ value }}</p>`,
  data () {
    return {
      value: '这是组件A的数据'
    }
  }
};
```

$refs 只会在组件渲染完成之后生效，且不是响应式数据，所以不要在模板或计算属性中使用。



## 组件插槽

> 组件插槽可以便捷的设置组件内容，让组件可以给 HTML 标签一样设置内容。

### 单个插槽

但平常我们书写的组件，组件首尾标签中书写的内容会被抛弃。

```html
<div id="app">
  <com-a></com-a>
</div>
```

将组件中需要灵活设置内容的部分通过`<slot></slot> `设置为插槽，在组件标签中书写的任意内容都会替换掉模板中的 slot。可以为普通文本、标签或其他组件。

```html
<div id="app">
  <com-a>
   	示例内容
    <span>组件的主体内容</span>
  </com-a>
</div>
```

```js
Vue.component('com-a', {
  template: `
    <div>
	  <h3>组件标题</h3>
      <slot></slot>
    </div>
  `
});
```

需要注意的是，插槽的内容是在父组件模板中书写的，就只能访问父组件数据，而无法访问子组件数据，因为：

> 父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。

```html
<div id="app">
  <com-a>
    这里只能访问父组件的数据
    {{ parValue }}
  </com-a>
</div>
```

```js
new Vue({
  el: '#app',    
  data: { parValue: '父组件数据' },
  components: {
    ComA
  },
});
```

```js
var ComA = {
  template: `
    <div>
      <p>组件 A：</p>
      <slot></slot>
    </div>
  `,
  data () {
    return {
      value: '子组件数据'
    }
  }
};
```

**后备内容（插槽默认值）**

可以在 `<slot>` 中设置后备内容（默认值），以备父组件没有设置插槽的具体数据。

```html
<div id="app">
  <com-a></com-a>
</div>
```

```js
var ComA = {
  template: `
    <div>
      <p>组件 A：</p>
      <slot>这是默认文本</slot>
    </div>
  `,
  data () {
    return {
      value: '子组件数据'
    }
  }
};
```

### 具名插槽

如果希望设置多个插槽，可以给 `<slot>` 设置 name 属性，未设置 name 的插槽默认 name 为 default。

```html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

使用时通过 `<template>` 包裹不同插槽内容，并设置 `v-slot` 指令传入对应的 name 参数即可。

注意：`v-slot` 只能作用于 `<template>`

```html
<com-a>
  <template v-slot:header>
    <h1>组件头部内容</h1>
  </template>

  <template v-slot:default>
    <p>组件主体内容第一段</p>
    <p>组件主体内容第二段</p>
  </template>

  <template v-slot:footer>
    <p>组件底部内容</p>
  </template>
</com-a>
```

任何没有被包裹在带有 `v-slot` 的 `<template>` 中的内容都会被视为默认插槽的内容

```html
<com-a>
  <template v-slot:header>
    <h1>组件头部内容</h1>
  </template>

  <p>组件主体内容第一段</p>
  <p>组件主体内容第二段</p>

  <template v-slot:footer>
    <p>组件底部内容</p>
  </template>
</com-a>
```

Vue.js 为 `v-slot` 指令提供了简写方式，可以将 `v-slot:` 替换为 `#`。

```html
<com-a>
  <template #header>
    <h1>组件头部内容</h1>
  </template>

  <p>组件主体内容第一段</p>
  <p>组件主体内容第二段</p>

  <template #footer>
    <p>组件底部内容</p>
  </template>
</com-a>
```

### 作用域插槽

> 我们可以通过给 `slot` 绑定属性的方式将数据传递到插槽中，这样就可以在插槽中使用组件内的数据了。

首先将需要被插槽访问的数据绑定给 `slot` 的属性，这种属性称为**插槽 prop**。

```js
var ComA = {
  template: `
    <div>
      <p>组件 A：</p>
      <slot :value="value">这是默认文本</slot>
    </div>
  `,
  data () {
    return {
      value: '子组件数据'
    }
  }
};
```

在插槽中通过 `v-slot` 可以给包含了所有插槽 prop 的对象命名。

```html
<div id="app">
  <com-a>
  	<template v-slot:default="dataObj">  
      {{ dataObj.value }}
    </template>
  </com-a>
</div>
```

很多情况下组件只有 default 插槽，这时可以将数据接收简写在组件标签上，这也是唯一一种可以将 `v-slot` 书写在 `<template>` 以外的情况。

```html
<div id="app">
  <com-a v-slot:default="dataObj">  
      {{ dataObj.value }}
  </com-a>
</div>
```

或者简写为：注意，此处不能写成 #=“dataObj”，只有设置了命名的 slot 才能简写为 #。

```html
<div id="app">
  <com-a v-slot="dataObj">  
      {{ dataObj.value }}
  </com-a>
</div>
```

也可以使用 ES6 的解构操作

```html
<div id="app">
  <com-a v-slot:default="{ value }">  
      {{ value }}
  </com-a>
</div>
```

## 内置组件

### 动态组件

> 动态组件 `<component>` 适用于多个组件需要频繁切换的场景。

#### is 属性

用于实现多个组件的快速切换，例如选项卡效果。

```js
// 子组件
var ComA = { template: `<div>A组件内容</div>` };
var ComB = { template: `<div>B组件内容</div>` };
var ComC = { template: `<div>C组件内容</div>` };
```

```js
new Vue({
  el: '#app',
  data: {
    titles: ['ComA', 'ComB', 'ComC'],
    currentCom: 'ComA'
  },
  components: {
    ComA, ComB, ComC
  }
});
```

```html
<div id="app">
  <button 
    v-for="title in titles"
    :key="title"
    @click="currentCom = title"
  >
    {{title}}
  </button>
  <component :is="currentCom"></component>
</div>
```

- is 属性在每次切换组件时会移除旧组件再创建新组件，不会因为组件切换导致状态保留。

  ```html
  // 修改子组件代码为：
  var ComA = {
    template: `<div>A组件内容：<input type="text"></div>`
  };
  var ComB = {
    template: `<div>A组件内容：<input type="text"></div>`
  };
  var ComC = {
    template: `<div>C组件内容：<input type="text"></div>`
  };
  ```

### keep-alive 组件

> 主要用于保留组件状态或避免组件重新渲染。

属性：

- `include` - 字符串或正则表达式。只有名称匹配的组件会被缓存。
- `exclude` - 字符串或正则表达式。任何名称匹配的组件都不会被缓存。
- `max` - 数字。最多可以缓存多少组件实例。

具体效果演示见课程案例。

### 过渡组件

> 用于在 Vue 插入、更新或者移除 DOM 时，提供多种不同方式的应用过渡、动画效果。

#### transition 组件

Vue 提供了 `<transition>` 封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡：

- 条件渲染 (使用 `v-if`)
- 条件展示 (使用 `v-show`)
- 动态组件
- 组件根节点

具体效果演示见课程案例。

##### 过渡的类名

> 过渡动画分为入场动画和离场动画两部分，每部分动画都要指定开始、结束以及过程控制。
>
> 在 `<transition>` 进入/离开的过渡中，提供了 6个 class 切换，用于设置过渡的具体效果。

###### 进入的类名

`v-enter`（入场前的样式）

- 定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。

`v-enter-to`（入场完成后的样式）

- 定义进入过渡的结束状态。在元素被插入之后下一帧生效 (同时 `v-enter` 被移除)，在过渡/动画完成后移除。

`v-enter-active`（入场过程控制）

- 定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。

###### 离开的类名

`v-leave`（离场前的样式）

- 定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。

`v-leave-to`（离开完成后的样式）

- 定义离开过渡的结束状态。触发离开过渡的下一帧生效 (同时 `v-leave` 被删除)，在过渡/动画完成后移除。

`v-leave-active`（离场过程控制）

- 定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。

> 注意：当没有定义 v-enter-to 与 v-leave 时，默认会采用元素在标准流中的样式效果。

##### transition 的属性

**name 属性**

> 给 <transition> 设置 name，可以给多个 transition 组件分别设置不同动画。

当 `<transition>` 设置了 name 时，设置过渡的类名前缀要进行如下变化：

- 当使用没有名字的 `<transition>` 时，所有类名默认为 `v-` 前缀。

- 当使用自定义 name 的 `<transition>` 时，所有类名以 `自定义name-` 为前缀。

**appear 属性**

>  用于设置 <transition> 在初始渲染时的过渡。

#### 自定义过渡类名

> Vue.js 允许我们设置比普通类名优先级高的自定义类名，在使用第三方 CSS 动画库时非常有用。

用于设置自定义过渡类名的属性如下：

- `enter-class`
- `enter-active-class`
- `enter-to-class`
- `leave-class`
- `leave-active-class`
- `leave-to-class`

用于设置初始过渡类名的属性如下：

- `appear-class`

- `appear-to-class`

- `appear-active-class`

##### Animate.css

> Animate.css 是一个第三方 CSS 动画库，通过添加类名给元素设置各种动画效果。

官网：https://animate.style/

使用注意：

- 元素需要设置基础类名 animated 才能使用其他动画类名效果
- 4.0 版本后所有类名增加了 animate__ 前缀，让类名使用更加安全
- 提供了 compat 版本

#### transition-group 组件

> <transition-group> 用于给一组元素（列表）统一设置过渡动画。

`<transition-group>` 与 `<transition>` 的不同之处：

- `<transition-group>` 本身会创建为一个元素，默认为 `<span>` ，可通过 tag 属性更改。

- CSS 过渡的类将会应用在内部的元素中，而不是这个组/容器本身。
- 每个 `<transition-group>` 的子节点必须有**独立的 key**，动画才能正常工作

使用注意：

- 所有过渡类名均与 `<transition>` 相同。
- 可通过 class `v-move` 设置列表项移动时的样式。

# Vue Router

> Vue Router 是 Vue.js 的官方插件，用来快速实现单页应用。

## 单页应用

> SPA（Single Page Application）单页面应用程序，简称单页应用。单页应用是网站功能的一种组织形式，与传统多页面网站不同，单页应用指的是网站的 “所有” 功能都在单个页面中进行呈现。

通过观察示例网站可以发现 SPA 具有以下特点：

- 优点：
  - 前后端分离开发，提高了开发效率。
  - 业务场景切换时，局部更新结构。（无需刷新，可实现跳转动画）
  
  - 用户体验好，更加接近本地应用。
  
- 缺点：
  - 不利于 SEO。
  - 初次首屏加载速度较慢。需要单独进行加载时间优化。
    - 但多页共用的资源只需要加载一次
  - 功能集中在单页上，页面复杂度比较高。

## 前端路由

> 后端路由指的是 URL 与处理函数间的映射关系。前端路由与之类似，指的是 URL 与内容间的映射关系。

前端路由的组成部分：

- n 个 URL
- n 段内容（Vue 中为 n 个组件）
- 映射关系（对应关系）

## 前端路由的实现

> 前端路由的原生实现有 Hash 与 History 两种方式。

### Hash 方式

> Hash 方式是通过 hashchange 事件监听 hash 变化，从而实现网页功能更新。明显的标志是 URL 中带有 `#`。

设置如下结构：

```html
<body>
  <div>
    <a href="#/">首页</a>
    <a href="#/category">分类页</a>
    <a href="#/user">用户页</a>
  </div>
  <div id="container">
    这是首页功能
  </div>
</body>
```

通过 onhashchange 事件监听 hash 变化，并进行页面内容更新。

```js
window.onhashchange = function () {
  var hash = location.hash.replace('#', '');
  var str = ''
  switch (hash) {
    case '/':
      str = '这是首页的功能';
      break;
    case '/category':
      str = '这是分类的功能';
      break;
    case '/user':
      str = '这是用户的功能';
      break;
  }
  document.getElementById('container').innerHTML = str;
};
```

对功能进行封装

```js
var router = {
  // 路由存储位置
  routes: {},
  // 定义路由
  route: function  (path, callback) {
    this.routes[path] = callback;
  },
  // 初始化路由
  init: function () {
    var that = this;
    window.onhashchange = function () {
      var hash = location.hash.replace('#', '');
      that.routes[hash] && that.routes[hash]();
    };
  }
};
```

```js
var container = document.getElementById('container');
// 定义路由
router.route('/', function () {
  container.innerHTML = '这是首页的功能';
});
router.route('/category', function () {
  container.innerHTML = '这是分类的功能';
});
router.route('/user', function () {
  container.innerHTML = '这是用户的功能';
});   
// 初始化路由
router.init();
```

- 以上为基础功能，不包含前进后退，分析后可得知前进后退的实现较为繁琐。

这里总结 Hash 方式特点：

- Hash 方式兼容性好。
- 地址中具有 #，不太美观。
- 前进后退功能较为繁琐。

### History 方式

> History 方式实现为 HTML5 提供的新功能，是前端路由实现的新标准。

```html
<body>
  <div>
    <a href="/">首页</a>
    <a href="/category">分类页</a>
    <a href="/user">用户页</a>
  </div>
  <div id="container">
    这是首页功能
  </div>
</body>
```

由于 History 不是 a 标签的默认行为，所以在操作时需要通过 history.pushState() 变更 URL。

pushState() 参数如下

- state ：与指定路径相关的**状态对象**，popstate 事件触发时，可通过事件对象的 state 接收副本。不需要时填 null。
  title：浏览器还不支持，填 '' （空字符串）即可。
  URL：新的网址，必须与当前页面处在同一个域。浏览器的地址栏将显示这个网址。

```js
var router = {
  // 存储路由
  routes: {},
  // 定义路由
  route: function (path, callback) {
    this.routes[path] = callback;
  },
  // 触发指定路由规则
  go: function (path) {
    history.pushState(null, null, path);
    this.routes[path] && this.routes[path]();
  }
}
```

设置测试功能，注意要阻止 a 标签默认跳转。

```js
// 使用测试
var links = document.querySelectorAll('a');
var container = document.querySelector('#container');    
links.forEach(function (ele) {
  ele.onclick = function (e) {
    var path = e.target.getAttribute('href');
    // 调用路由
    router.go(path);
    return false; // 阻止跳转
  }
}); 

// 定义路由规则
router.route('/', function () {
  container.innerHTML = '这是首页的功能';
});
router.route('/category', function () {
  container.innerHTML = '这是分类的功能';
});
router.route('/user', function () {
  container.innerHTML = '这是用户的功能';
});   
```

实现前进后退功能时，需要通过 pushState() 的 state 传递路由标记，并通过 popstate 事件的事件对象访问 state 来获取路由标记，并触发对应功能。

```js
go: function (path) {
  history.pushState({ path: path }, null, path);
  ...
}
```

设置 init 方法，监听前进后退的触发。

```js
init: function () {
  var that = this;
  window.addEventListener('popstate', function (e) {
    var path = e.state ? e.state.path: '/';
    that.routes[path] && that.routes[path]();
  });
}
```

进行初始调用

```js
...
router.init();
...
```

这里总结 History 方式特点：

- 实现方式简洁。
- 在 URL 显示上更符合前端路由格式。
- pushState() 可存储序列化后最大 640k 的数据，hash 方式为 2k。
- 兼容性差，ie10 开始支持。
- 刷新时会访问对应路径，需要后端配合处理。

## Vue Router

> Vue Router 是 Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。

中文官网地址：https://router.vuejs.org/zh/

### 基本使用

直接下载 / CDN

- 最新版本：https://unpkg.com/vue-router/dist/vue-router.js

- 指定版本：https://unpkg.com/vue-router@3.4.9/dist/vue-router.js

npm

```bash
npm install vue-router
```

Vue Router 提供了用于进行路由设置的组件 `<router-link>` 与 `<router-view>`。

- 使用 `<router-link>` 组件来导航，用 `to` 属性指定链接，组件默认会渲染为 `<a>` 标签，可通过 tag 属性设置为其他标签。
- Vue Router 的路由是以组件的单位的，`<router-view>` 用来显示路由匹配到的组件。

```html
<div id="app">
  <router-link to="/">首页</router-link>
  <router-link to="/category">分类</router-link>
  <router-link to="/user">用户</router-link>
  <router-view></router-view>
</div>
```

提前定义路由中的组件（配置对象）。

```html
var Index = {
  template: `<div>这是首页的功能</div>`
};
var Category = {
  template: `<div>这是分类的功能</div>`
};
var User = {
  template: `<div>这是用户的功能</div>`
};
```

定义路由规则

```js
var routes = [
  { path: '/', component: Index },
  { path: '/category', component: Category },
  { path: '/user', component: User }
];
```

创建 Vue Router 实例，通过 routes 属性配置路由

```js
var router = new VueRouter({
  routes: routes
});
```

创建 Vue 实例，通过 router 属性注入路由

```js
var vm = new Vue({
  el: '#app',
  router: router
});
```

> 输出 Vue 实例，观察 $route 与 $router 

#### 命名视图

> 如果导航后，希望同时在同级展示多个视图（组件），这时就需要进行命名视图。

多个视图通过多个 `<router-view>` 表示，并设置不同的 name 属性；如果未设置，默认为 default。

```html
<div id="app">
  <router-link to="/">首页</router-link>
  <router-link to="/user">用户</router-link>
    
  <router-view name="sidebar"></router-view>
  <router-view></router-view>
</div>
```

设置命名视图后，路由中通过 components 属性进行设置（注意不是 component，而是 components）。

```js
var SideBar = {
  template: `<div>这是侧边栏功能</div>`
};
// 定义路由规则
var routes = [
  { 
    path: '/', 
    components: {
      sidebar: SideBar,
      default: Index
    }
  },
  {
    path: '/user', 
    components: {
      sidebar: SideBar,
      default: User
    }
  }
];
```

### 动态路由

> 当我们需要将某一类 URL 都映射到同一个组件，就需要使用动态路由。

定义路由规则时，将路径中的某个部分使用 `:` 进行标记，即可设置为动态路由。

```js
var User = {
  template: `<div>这是用户的功能</div>`
};
var routes = [
  { path: '/user/:id', component: User }
];
```

设置为动态路由后，动态部分为任意内容均跳转到同一组件。（示例中为用户名或用户 ID）

```html
<div id="app">
  <router-link to="/user/1">用户1</router-link>
  <router-link to="/user/2">用户2</router-link>
  <router-link to="/user/3">用户3</router-link>
  <router-view></router-view>
</div>
```

带有 `:` 的部分对应的信息称为 `路径参数`，存储在 vm.$route.params 中。

```js
var User = {
  template: `
	<div>
	  这是用户 {{ $route.params.id }} 的功能
	</div>`
};
```

#### 侦听路由参数

> 使用动态路由时，如果仅为路由参数变化，组件实例会被复用而不是销毁后再重新创建。这意味着组件的生命周期钩子不会再调用。

如果希望在路由参数变化时作出响应，可以通过 watch 监听 $route。

```js
var User = {
  template: `<div>这是 {{ $route.params.id }} 功能</div>`,
  watch: {
    $route (route) {
      console.log(route);
    }
  }
};
```

#### 路由传参处理

> 在组件中使用 $route 会让组件与路由高度耦合，如果希望组件可以在其他路由中使用，需要进行解耦。

通过路由的 props 设置数据，并通过组件 props 接收，从而将组件与路由解耦。本质上是将 $route.params 的值传递给组件 props。

```js
var routes = [
  { 
    path: '/user/:id',
    component: User
  },
  { 
    path: '/category/:id', 
    component: Category,
    // props: true 表示将路径参数设置为组件属性（而组件属性可以通过组件的 props 接收）
    props: true
  },
];
```

```js
var User = {
  template: `<div>这是 {{ $route.params.id }} 的功能</div>`
};
var Category = {
  props: ['id'],
  template: `<div>这是 {{ id }} 功能</div>`
};
```

如果路由包含多个命名视图，则需要将路由的 props 设置为对象，以给每个视图添加 props 选项。

```js
{ 
  path: '/category/:id', 
  components: {
    default: Category,
    sidebar: SideBar
  },
  props: {
    default: true,
    sidebar: false 
  }
}
```

```js
var SideBar = {
  template: `<div>这是侧边栏功能</div>`
};
```

如果希望设置静态数据，可将 props 中的某个组件对应的选项设置为对象，内部属性会绑定给组件的 props。

```js
{ 
  path: '/category/:id', 
  components: {
    default: Category,
    sidebar: SideBar,
    sidebar2: SideBar2
  },
  props: {
    default: true,
    sidebar: false,
    sidebar2: { a: '状态1',b: '状态2'}
  }
},
```

```js
var SideBar2 = {
  props: ['a', 'b', 'c', 'id'],
  template: `
    <div>
      这是右侧侧边栏功能: {{ a }} {{ b }} {{ c }}
    </div>
  `
};
```

### 嵌套路由

> 实例场景中，路由通常由多层嵌套的组件组合而成，这时需要使用嵌套路由配置。

使用 children 来进行嵌套路由中的子路由设置。

```js
var routes = [
  { 
    path: '/user', 
    component: User,
    children: [
      { 
        path: 'hobby', 
        component: UserHobby
      },
      { 
        path: 'info', 
        component: UserInfo,
        children: [
          { path: 'age', component: UserInfoAge },
          { path: 'school', component: UserInfoSchool },
        ]
      }
    ]
  }
];
```

### 编程式导航

> 除了可以通过 `<router-link>` 来定义声明式导航，还可以通过 router 实例方法设置编程式导航。

router.push() 用来导航到一个新 URL。

- 参数为路径信息，可以为字符串或对象

  ```js
  vm.$router.push('/user');
  vm.$router.push({path: '/user'});
  vm.$router.push({path: '/user/123'});
  ```

- 可以将 `<router-link>` 的导航设置在 `:to` 属性中，点击时内部也会调用 push()。

  ```html
  <router-link :to="{ path: '/user/10' }">用户10</router-link>
  ```

除了 push 外，Vue Router 还提供了 replace()、go()、forward()、back() 方法，可查询文档学习。

#### 命名路由

> 有时候，通过名称来表示一个路由更加方便，尤其是在导航到一些较长的路由的时候（如嵌套路由时）。

设置路由时添加 name 属性。

```js
var School = {
  template: `<div>School 组件：{{ $route.params }}</div>`
};
var routes = [
  {
    path: '/user/:id/info/school',
    name: 'school',
    component: School
  }
];
```

在 push() 中通过 name 导航到对应路由，参数通过 params 对象设置。（编程式导航）

- params **只能与 name 搭配使用**，如果使用 path 则 params 无效。

```js
vm.$router.push({ name: 'school', params: { id: 20, demo: '其他数据' }});
```

也可以在 `<router-link>` 中使用。（声明式导航）

```html
<router-link :to="{ name: 'school', params: { id: 1 } }">用户学校</router-link>
<router-link :to="{ name: 'school', params: { id: 2 } }">用户学校</router-link>
<router-link :to="{ name: 'school', params: { id: 3 } }">用户学校</router-link>
```

### 其他功能

#### 重定向

> 用户访问 URL1 时，实际被替换为了 URL2 并匹配了对应路由，这就是重定向。

例如，设置路由 `/category/:id` 用来访问某个分类功能，但如果 URL 为 `/category` 则没有任何意义，这时可以进行重定向。

```js
 var routes = [
   { path: '/', component: Index},
   { path: '/category/:id', component: Category },
   { path: '/category', redirect: '/' }
 ];
```

#### 别名

> 别名是一种美化路由的方式。

别名指的是用户访问 URL1 时，实际上匹配了 URL2 对应的路由，但 URL 还保持为 URL1。假定有路由 /user/:id/info/school/intro/:date 这时 URL 看起来十分复杂，我们可以设置为别名为例如 /10/20200910。

```js
var routes = [
  {
    path: '/user/:id/info/school/intro/:date',
    name: 'school',
    component: School,
    alias: '/:id/:date'
  }
];
```

```html
<router-link :to="{ name: 'school', params: { id: 1, date: 0101 } }">用户学校</router-link>
<router-link to="/10/0612">用户学校</router-link>
```

#### 导航守卫

> 导航守卫用于在路由发生改变时，通过跳转或取消的方式来守卫导航。

例如，网站的用户页，如果用户没有登录，则无法访问，这时就可以给 router 使用导航守卫。

```js
router.beforeEach(function (to, from, next) {
  console.log(to, from);
  next();
});
```

next() 用来继续执行后续功能

next(false) 用于阻止本次导航，

`next('/')` 者 `next({ path: '/' })` 用于中止本次导航，并进行一个新的导航。

必须确保 `next` 在任何导航守卫中都被严格调用一次（最多一次，最少也一次）。

#### History 模式

> `vue-router` 默认 hash 模式，如果需要，可通过实例属性设置为 History 模式。

需要通过 Vue Router 实例的 `mode` 选项来设置，这样 URL 会更加美观，但同样需要后端支持避免问题。

```js
var router = new VueRouter({
  mode: 'history',
  routes: [
    ...
  ]
});
```



# Vue CLI

> Vue CLI 是一个基于 Vue.js 进行快速开发的完整系统，称为脚手架工具。

官网：https://cli.vuejs.org/zh/

作用：

- 提供单文件组件（优化组件书写方式）
- 统一项目的架构风格。
- 初始化配置项目依赖

操作方式：命令行工具

## 安装与升级

> Vue CLI 是命令行工具，需要全局安装。

npm

- 安装

  ```js
  npm install -g @vue/cli
  ```

- 升级

  ```js
  npm update -g @vue/cli
  ```

## 项目搭建

- 创建项目

  ```js
  vue create project-demo
  ```

- 选择 Preset（预设）

![](./media/vue-cli/1.png)

- 选择包管理器

  ![](./media/vue-cli/2.png)

- 创建完成

  ![](./media/vue-cli/3.png)

- 运行项目（注意目录）

  ```js
  npm run serve
  ```

- 项目运行成功

  ![](media/vue-cli/4.png)

## 目录与文件

注：下面为主要目录与文件含义

```
└─ 根目录
   ├─ node_modules
   ├─ public 		   预览文件目录
   └─ src    
      ├─ assets 	   静态资源目录
      └─ components    项目组件目录
   	  └─ App.vue       根组件
      └─ main.js       入口文件
```

### 单文件组件

> 组件是 Vue 应用的基础，每个组件都由模板、样式、逻辑 3 部分组成。Vue CLI 中提供了以 .vue 为扩展名的文件作为组件的载体，使得组件更加内聚且更可维护，这种形式称为单文件组件。

传统组件定义方式的缺点：

- 字符串模板缺乏语法高亮，在 HTML 有多行的时候，需要用到`\` 或使用兼容性差的模板字符串。

- 不支持 CSS 意味着当 HTML 和 JavaScript 组件化时，CSS 明显被遗漏。
- 没有构建步骤，限制只能使用 HTML 和 ES5 JavaScript，而不能使用预处理器，如 Babel 等工具。

单文件组件的特点：

- 文件扩展名为 `.vue`
- 内部通过 `<template>` `<script>` `<style>`  分别进行模板、逻辑、样式设置。
- 每个单文件组件均为独立模块，导入导出需要使用模块语法。

单文件组件书写示例：

```vue
<template>
  <p>组件模板</p>
</template>

<script>
  // 组件逻辑（这里采用了 ES6 Module）
  export default {
    name: "MyComponent"
  };
</script>

<style scoped>
  /* 组件样式 */
  p {
    font-size: 20px;
    text-align: center;
  }
</style>
```

使用 Vue CLI 后，每个组件都是独立模块，在操作时需要通过模块语法进行导入导出（如 ES6 Module）

- 导入

  ```js
  import Demo from 'Demo.vue'
  ```

- 导出

  ```js
  export default {
    data () {
      return {
        ...
      }
    },
    methods: {
      ...
    }
  };
  ```

## 打包与部署

### 打包

> 我们通过 Vue CLI 创建的项目中都是使用 .vue 文件开发的，这种文件无法部署上线被用户访问（浏览器不认识），这时需要 Vue CLI 将项目编译为浏览器可识别的文件（HTML、CSS、JS）。

命令：

```js
npm run build
```

### 部署

> 部署指的是将 Vue 项目dist 目录部署到服务器上。

这里我们主要说一下如何在本地部署并预览项目的方式。

因为 dist 目录默认需要启动 HTTP 服务器来访问，所以无法采用 '双击'  以 file 协议运行。比较常用的方式是使用 Node.js 静态文件服务器。

首先安装 serve

```js
npm install -g serve
```

启动服务器

```
工作路径为 dist 父级时的命令：
serve dist 
工作路径为 dist 时的命令：
serve
```
