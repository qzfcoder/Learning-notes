# vue3

### vue3——6大亮点

1. Performance： 性能比vue2块1.2~2倍
2. Tree shaking support：按需编译，体积比vue2小
3. Commpostion API：组合API
4. Better TypeScript support：更好的Ts支持
5. Custom Renderer API：暴露了自定义渲染API
6. Fragment Teleprot Suspense：更先进的组件

## vue3是如何变快的

#### diff方法优化

vue2中虚拟dom是进行全量对比

vue3新增了静态标记PatchFlag，通过增加标记，进行只对标记的进行比较

```vue
<div>
	<p>1</p>
	<p>{{msg}}</p>
</div>
// 这时候产生虚拟dom树，vue2的时候，是会重新渲染出来一颗虚拟dom树，这时候又两颗树：上一次渲染生产的dom树，数据更新后生成的dom树，一一比较。vue3中，也是生成这两颗虚拟dom树，会通过diff算法，在创建dom的时候会根据dom中的内容会不会发生变化，添加一个静态标记，只比较存在标记的
```

PatchFlags

```js
export const enum PatchFlags {
  // 动态文字内容
  TEXT = 1,

  // 动态 class
  CLASS = 1 << 1,

  // 动态样式
  STYLE = 1 << 2,

  // 动态 props
  PROPS = 1 << 3,

  // 有动态的key，也就是说props对象的key不是确定的
  FULL_PROPS = 1 << 4,

  // 合并事件
  HYDRATE_EVENTS = 1 << 5,

  // children 顺序确定的 fragment
  STABLE_FRAGMENT = 1 << 6,

  // children中有带有key的节点的fragment
  KEYED_FRAGMENT = 1 << 7,

  // 没有key的children的fragment
  UNKEYED_FRAGMENT = 1 << 8,

  // 只有非props需要patch的，比如`ref`
  NEED_PATCH = 1 << 9,

  // 动态的插槽
  DYNAMIC_SLOTS = 1 << 10,

  // SPECIAL FLAGS -------------------------------------------------------------

  // 以下是特殊的flag，不会在优化中被用到，是内置的特殊flag

  // 表示他是静态节点，他的内容永远不会改变，对于hydrate的过程中，不会需要再对其子节点进行diff
  HOISTED = -1,

  // 用来表示一个节点的diff应该结束
  BAIL = -2,
}
```



#### hoistStatic静态提升

vue2中无论元素是否参与更新，每次都会重新创建，然后在渲染

vue3中对于不参与更新的元素，会做静态提升，只会被创建一次，在渲染的时候直接复用即可。

#### cacheHandlers事件侦听器缓存

默认情况下onclick会被动态绑定，每次都会去追踪

 