---
title: ES6变量解构赋值
date: 2019-04-01 17:51:31
tags:
  - 笔记
  - ES6
---

## 解构

解构是一种打破数据结构，将其拆分成更小部分的过程。 #解构赋值的由来
在开发过程中，我们经常需要用到数组和对象，而从数组或对象中获取特定的数据并赋值给变量，这就需要编写这样的代码

```
let options = {
	repeat:true,
    save:false
}

let repeat = options.repeat
let save = options.save

// 如果你想提取更多变量，则必须编写类似的代码来给变量赋值，给开发者带来很大的麻烦，为此ES6添加了解构功能，方便我们提取数组或对象的值
```

## 数组的解构赋值

### 基本用法

**解构赋值**：在 ES6 中 ，按照一定模式从数组和对象中提取值，然后对变量进行赋值，这被称为解构赋值。
_本质_：这种写法属于“模式匹配”，只要等号两边的模式相同，**左边的变量**就会被赋予对应的值。

```
let [foo,[bar],baz]=[1,[2],3]
foo // 1
bar // 2
baz // 3
```

**如果结构不成功，变量的值就为 undefined**

```
let [x,y,...z]=['a']
x // 'a'
y // undefined
z // []
```

### 默认值

**解构赋值允许指定默认值**

```
let [foo=true]=[];
foo // true
```

**ES6 内部使用严格相等于运算符（===）来判断一个位置是否有值，如果数组的成员不严格等于 undefined，默认值就不会生效。即，一个位置的值不是 undefined，那么即使设置了默认值，也不会有效。**

```
let [x=1]=[undefined]
x // 1

let [x=1] =[null]

x // null
```

## 对象的解构赋值

```
let {foo,bar}={foo:'abc',bar:'xyz'}

foo // 'abc'
bar // 'xyz'

```

### 当变量名和属性名相同时

```
let {foo,bar}={foo:'abc',bar:'xyz'}
foo // 'abc'
bar // 'xyz'
上述代码的实质应该是：
let {foo:foo,bar:bar}={foo:'abc',bar:'xyz'}
// 当变量名和属性名一样时，可以简写 {foo,bar}来代替{foo:foo,bar:bar}

```

**对象解构赋值的内部机制：**是先找到同名属性，然后再赋值给对应的变量。真正赋值的是后者，而不是前者

### 当变量名与属性名不同时：

```
let {foo:hello,bar:world}={foo:'abc',bar:'xyz'}
hello // 'abc'
world // 'xyz'
foo // error: foo is not defined
```

**foo 是匹配的模式，hello 才是整正的变量**

### **数组解构赋值与对象结构赋值的差异**：

数组的元素是按次序排列的，变量的取值由位置决定；而对象没有次序，变量名必须与属性名相同，才能取到正确的值。

## 字符串的解构赋值

字符串结构赋值的时候，字符串被转换成了一个类似数组的对象

```
const [a,b,c,d,e]='hello'
a // 'h'
b // 'e'
c // 'l'
d // 'l'
e // 'o'

//这种类数组的对象，有length属性，因此也可以对length属性进行解构赋值

let {length:len}='hello'
len // 5
```

## 数值和布尔值的解构赋值

数值和布尔值进行解构赋值的时候，会先转换为对象

```
let {toString:s} = 123
s // Number.prototype.toString

let {toString:s} = true

s // Boolean.prototype.toString

//数值对象和布尔值的包装对象都有toString属性，因为变量s可以取到值
```

**解构赋值的规则：**只要等号右边的值不是数组或者对象，就先将其转化为对象，由于 null 和 undefined 无法转化为对象，所以对他们解构赋值会报错

```
let {proxy:x}=undefined;
x // TypeError

let {proxy:y} = null;
y // TypeError
```

## 函数参数的解构赋值

```
function add ([x,y]){
	return x+y;
}

add([1,2])  // 3
```

## 用途

### 交换变量的值（数组解构赋值）

在排序算法中，值交换是一个非常常见的操作，如果在 ES5 中交换两个变量的值，则必须引入第三个临时变量
没有解构赋值的情况下，交换两个变量需要一个临时变量

```
let x=1;
let y=2;
[x,y] = [y,x]
x // 2
y // 1
// 如果右侧数组结构赋值的表达式的值为null或者undefined，则会导致程序抛出错误
```

## 从函数中返回多个值

从一个函数返回一个数组是十分常见的情况
但是函数只能返回一个值，如果需要返回多个值，只能将他放在数组或者对象里返回，有了结构赋值，取出这些值就非常方便

```
// 返回数组
function example(){
	return [1,2,3]
}

let [a,b,c]=example()
a // 1
b // 2
c // 3

// 返回对象
function example(){
    return {
    foo:1,
    bar:2
    }
}

let [foo,bar]=example()
foo // 1
bar // 2
```

## 函数参数的定义

解构赋值，可以很方便的将一组参数与变量名对应起来 ##提取 JSON 数据
解构赋值在提取 JSON 数据尤为有用

```
let jsonData = {
	id:42,
   status:"OK",
   data:[23,45]
}

let {id,status,data}=jsonData

console.log(id,status,data)
// 42,"OK",[23,45]
```

## 定义函数参数的默认值

避免在函数体内部再写 var foo = config.foo || "default foo"这样的语句

```
   jQuery.ajax= function(url,{
      asyc=true,
      beforeSend=function(){},
      cache=true,
      complete=function(){},
      crossDomain=false,
      global = true,
      //  .... more config
    }){
      // ... do stuff
    }
```

## 遍历 Map 结构

**任何部署了 Iterator 接口的对象都可以使用 for...of 循环遍历**
Map 结构原生支持 Iterator 接口，配合变量的解构赋值，获取键名和键值都非常方便

```
    var map = new Map()
    map.set("first",'hello')
    map.set("second",'world')

    for (const [key,value] of map) {
      console.log(key + "is" +value)
    }

    // first is hello
    // second is world

    // 只想获取键名
     for (const [key] of map) {
      //
    }
     // 只想获取键值
     for (const [,value] of map) {
      //
    }

```

## 引入模块中的某些方法

```
cosnt {sourceMapConsumer,SourceNode} = require('source-map')
```

[阮一峰《ES6 标准》](http://es6.ruanyifeng.com/#docs/destructuring)
