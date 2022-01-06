# options API的缺陷：

vue2中是options API ，我们在这种书写很多选项（data，watch，methods，。。。）这些统称为options API。我们在实现一个功能的时候，该功能的逻辑会被拆分到各个属性中，当我们组件变得更大更复杂的时候，同一个逻辑会被拆的很分散，



# Composition API

代码编写到setup函数中

setup(props, context)

context：中**context.attrs.(属性名称)**父组件传来的非props的数据，**context.slots**获取父组件引用时内部的插槽，**context.emit** 发送事件

setup的函数返回值来代替data选项，返回值可以在template的模板中使用



### setup为什么不能用this？

setup没有绑定this。他找不到组件的实例，setup在调用的时候，data，methods等还没有被解析，获取不到的。

