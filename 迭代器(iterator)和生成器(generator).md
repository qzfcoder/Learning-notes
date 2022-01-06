# 迭代器(iterator)和生成器(generator)

迭代器：可以帮助我们对某个数据结构进行遍历的对象

在js中，迭代器也是一个具体的对象

```js
// 例如 const obj = {} 我们可以把obj变成一个迭代器，要符合迭代器协议
// 迭代器协议定义了产生一系列值的标准，在js中这个标准就是next方法

// next函数，是一个无参的函数，返回一个应有两个以下属性的对象
	// done（boolean），如果迭代器可以产生序列中的下一个值，则为false
		// 若已经完成了则为true，这情况下，value是可选的，如果他存在就返回默认值
	// value：爹但其返回任何js的值，done为true可以省略

//定义一个迭代器 
const iterator = {
    next: function() {
        return { done:true, value:123 }
    }
}
```

```js
// 数组
const test = [1,2,3,4]
// 创建一个迭代器对象来访问数组
const testIterator = {
    next: function() {
        // return { done: false, value: 1}
        // return { done: false, value: 2}
        // return { done: false, value: 3}
        // return { done: false, value: 4}
        // return { done: true}
        if(index < test.length) {
            return {done:false, value: test[index++]}
        }else {
            return {done: true}
        }
    }
}
testIterator.next()
testIterator.next()
testIterator.next()
```

























