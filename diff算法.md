# diff算法

#### VNode（Virtual Node）虚拟节点

先可以理解为html创建出来的VNode。

浏览器会渲染出来很多的body，h2，div等元素，这些就是DOM Node（真实节点）

虚拟节点存在于内存当中，vue会将模板中的元素转换成一个个虚拟节点。

template-->vnode-->真实dom（vnode的好处，可以做多平台适配）

一大堆vnode会形成vnode tree

#### 在v-for进行列表循环的时候，需要绑定一个key。

v-for中key的作用，

​	官网上介绍：为了给vue一个提示，跟踪每个节点的身份，从而重用和重新排序。

​	主要用于vue的虚拟dom算法上面，在新旧nodes对比上标识Vnodes

如果不适用key，vue会使用一种最大限度减少动态元素，并且尽可能尝试就修改、复用相同类型的元素算法。如果使用key他会基于key的变化重新排列元素顺序，并且会移除、销毁key不存在的元素

自己的理解：



#### diff算法

例如一个list：[a,b,c,d] 要在中间插入一个f

获取到原来list中的vnodes与新的vnodes进行对比，查看变化哪个东西。

vue中对于列表更新操作

有key执行patchKeyedChildren方法

没有key执行patchUnkeyedChildren方法

```
https://s2.loli.net/2021/12/28/G2RmE9NzVZlnTpk.png
```

packages/runtime-core/src/renderer.ts

![6b30bac4c5fb046c930a712fa0413e1](C:\Users\86730\OneDrive\桌面\6b30bac4c5fb046c930a712fa0413e1.png)



![f79e7d0ca81f5a421cb839e893837b2.png](https://s2.loli.net/2021/12/28/m3fauKNOMHb9DzI.png)

![f79e7d0ca81f5a421cb839e893837b2](C:\Users\86730\OneDrive\桌面\f79e7d0ca81f5a421cb839e893837b2.png)

![image-20211228171201002](C:\Users\86730\AppData\Roaming\Typora\typora-user-images\image-20211228171201002.png)

获取新旧nodes的长度，用比较小的list进行遍历，从l1中取一个值，从l2中获取一个值，进行比较（patch），若相同则不做更新，若不同则进行更新。然后进行判断，是旧的长还是新的长。若旧的长，则后续全部卸载，若新的长，则创建新的节点，增加上去

![image-20211228171226429](C:\Users\86730\AppData\Roaming\Typora\typora-user-images\image-20211228171226429.png)

#### 有key的时候

用了一个while循环从最开始进行查找（0位置)，对key进行判断，key不变，内容也不变，相同则不需要更新，i++来进行递增，获取到下一个进行判断，直到不是同一个类型的时候，跳出循环。然后开始从尾部开始遍历，直到不是同一个类型的时候，跳出循环。

如果旧节点遍历完了，还有新的节点。用null和那个节点进行patch，将这个节点挂在上去。

如果旧节点较多，卸载那个节点

![image-20211228172105571](C:\Users\86730\AppData\Roaming\Typora\typora-user-images\image-20211228172105571.png)

### 特殊

原有list和现有list中间部分顺序被打乱了，尽量利用旧节点中一致的东西，如果有多余的就新增，顺序问题就移动

![image-20211228172904016](C:\Users\86730\AppData\Roaming\Typora\typora-user-images\image-20211228172904016.png)