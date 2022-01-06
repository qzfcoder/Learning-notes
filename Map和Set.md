# set

set是es6中新增的数据结构，可以用来保存数据，但是set中元素不能重复

```js
//创建一个set结构
const set = new set()
set.add(10)
set.add({})
...

//给数组去重
const arr = [1,2,1,2,3,3,4,5,6,4,5,8]
const newArr = []
for(const item of arr) {
    if(newArr.indexof(item) !== -1){
		newArr.push(item)
    }
}

const arrSet = new Set(arr)
console.log(arrSet)
const newArr = Array.from(arrSet) 
//或者
const newArr = [...arrSet] 
console.log(newArr)

//set中的一些方法
arrSet.deldet(1) // 删除
arrSet.add(22) // 增加
arrSet.has(1) // 判断是否存在
arrSet.clear() // 清楚
//对set进行遍历
arrSet.forEach(item=< {
	console.log(item)
})
```

# WeakSet的使用

WeakSet内部元素也是不能重复的，内部只能存放对象类型

对对象是一个弱引用

不能遍历

```js
const weakSet = new WeakSet

// weakSet对对象是一个弱引用, 可以获取里面的东西，gc还是可以回收掉的
//在这个obj这个对象中，obj是在栈中的，然后在内存中开辟了一个内存空间，存放name等变量，obj是通过一个内存地址指向内存中的数据。垃圾回收器会不定时的查看对象是否有指针指向或者说引用它，若没有，则会被垃圾回收器回收掉
let obj = {name: 'qqq'} 

weakSet.add(obj)
```

```js
// weakmap的应用场景
const personSet = new WeakSet() // 不使用set的原因，对象不会被销毁了
class Person {
    constructor() {
        personSet.add(this)
    }
    running() {
        if(personSet.has(this)){
            throw new Error('不能通过非构造方法创建出来对象，调用方法')
		}
        console.log('runnning', this)
    }
}
const p = new Person()
p.running()
p.running.call({name: 'qqq'}) // 会报错 使用weakSet不允许通过call等方法来调用


```



# Map

Map用于存储映射关系

```js
const obj1 = {name: '111'}
const obj2 = {name: 'kobe'}

const info = {
    [obj1]: 'a',// 这样书写的话，并不是以对象为key，会被转化为字符串'[object object]'
    [obj2]: 'b'
}
// Map允许对象类型存贮key
const map = new Map()
map.set('obj1', 'a') // 打印出来Map（1）{{name:'111'}=>'aaa'}

// 传入数组总要求entries，数组中依然要是数组，数组中为key value
const map2 = new Map([[obj1,'a'], [obj2,'2']])

//map的属性
map.size()
map.set(key,value)
map.get(key)
map.has(key)
map.delete(key)
map.clear()

// 遍历map  item,key,arr
map.forEach(item=>{
	console.log(item)
})

for(const item of map) {
    console.log(item) // item为数组
}
for(const [key, value] of map) {
    console.log(key, value)
}
```

## weakMap

weakMap的key只能是对象类型，weakMap中的key对对象使弱引用

```js
const obj1 = {name: '111'}
const obj2 = {name: 'kobe'}

const weakmap = new WeakMap()
weakmap.set(obj, 'a')
//WeakMap没有foreach，不能进行遍历

//应用场景，在vue3响应式原理中使用了
```

```js

// 应用场景(vue3响应式原理)
const obj1 = {
  name: "qqq",
  age: 18
}

function obj1NameFn1() {
  console.log("obj1NameFn1被执行")
}

function obj1NameFn2() {
  console.log("obj1NameFn2被执行")
}

function obj1AgeFn1() {
  console.log("obj1AgeFn1")
}

function obj1AgeFn2() {
  console.log("obj1AgeFn2")
}

const obj2 = {
  name: "kobe",
  height: 1.88,
  address: "广州市"
}

function obj2NameFn1() {
  console.log("obj1NameFn1被执行")
}

function obj2NameFn2() {
  console.log("obj1NameFn2被执行")
}

// 1.创建WeakMap
const weakMap = new WeakMap()

// 2.收集依赖结构
// 2.1.对obj1收集的数据结构
const obj1Map = new Map()
obj1Map.set("name", [obj1NameFn1, obj1NameFn2])
obj1Map.set("age", [obj1AgeFn1, obj1AgeFn2])
weakMap.set(obj1, obj1Map)

// 2.2.对obj2收集的数据结构
const obj2Map = new Map()
obj2Map.set("name", [obj2NameFn1, obj2NameFn2])
weakMap.set(obj2, obj2Map)

// 3.如果obj1.name发生了改变
// Proxy/Object.defineProperty
obj1.name = "james"
const targetMap = weakMap.get(obj1)
const fns = targetMap.get("name")
fns.forEach(item => item())



```















