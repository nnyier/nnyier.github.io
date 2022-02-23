---
title: call&apply&bind
date: 2019-04-03 11:22:31
tags:
  - 笔记
  - JavaScript
---

# call、apply、bind

## 什么时候是用什么方法

- apply：传递参数不多，传递类数组
- call：确定参数个数，关注参数的对应关系
- bind：不立即执行，生成一个新的函数，长期绑定某个函数给某个对象使用

## call、apply、bind 三者均来自 Function.prototype，被设计用于改变函数内 this 的指向

### **call** call() 方法调用一个函数, 其具有一个指定的 this 值和分别地提供的参数(参数的列表）

**func.call(thisArg, arg1, arg2, ...)**

#### 妙用

- 调用匿名函数

```
(function() {
    ...
    this.__wrapped__=n;
    ...
}).call(this);

// 兼容严格模式，严格模式下，匿名函数里this会报错。
// 实现了匿名函数针对不同的this，做不同的处理。
```

- 字符串分割连接
  **str.split('').join(',')**
  **Array.prototype.join.call(str,',')**

```
var str = 'hellow!';
console.log(str.split('')); // [ 'h', 'e', 'l', 'l', 'o', 'w', '!' ]
console.log(str.split('').join(',')); // h,e,l,l,o,w,!
console.log(str); //hellow!

var temp = Array.prototype.join.call(str, ',');
// var temp = Array.prototype.join.apply(str, [',']);
// var temp = [].join.call(str, ',');
// var temp = [].join.apply(str, [',']);
console.log(temp); // h,e,l,l,o,w,!
console.log(str); //hellow!

```

- 字符串取每一项

```
Array.prototype.map
  .call('foo', item => {
    console.log(item);
    // f
    // o
    // o
  })
  .join('');

// 字符串没有join方法,借用Array
```

### **apply** apply() 方法调用一个具有给定 this 值的函数，以及作为一个数组（或类似数组对象）提供的参数

**func.apply(thisArg, [argsArray])**

- thisArg 可选的参数。在 func 函数运行时使用的 this 值。注意，不一定是该函数执行时真正的 this 值：如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动指向全局对象（浏览器中就是 window 对象），当值为原始值（1，‘string’，true）时 this 会指向该原始值的自动包装对象（Number，String，Boole）。
- argsArray 可选的参数。一个数组或者类数组对象（NodeList），其中的数组元素的每一项将作为单独的参数传给 func 函数。如果该参数的值为 null 或 undefined，则表示不需要传入任何参数。从 ECMAScript 5 开始可以使用类数组对象。

#### 妙用

- 数组拼接

```
var arry = ['a', 'b'];
var elements = [0, 1, 2];

// 拼接数组 concat不会改变原数组
var con = arry.concat(elements);
console.log(con); // [ 'a', 'b', 0, 1, 2 ]
console.log(arry); // [ 'a', 'b' ]

// 如果想改变原数组 可以用apply
arry.push.apply(arry, elements); // ["a", "b", 0, 1, 2]
// 也可以用空数组
// [].push.apply(arry, elements); // ["a", "b", 0, 1, 2]
console.info(arry); // ["a", "b", 0, 1, 2]

// 解决了想修改原数组，不想用循环，还想传递一个数组的问题
```

- 数组求最大值、最小值

```

// 找出数组最大值
var numbers = [1,3,2,5,9]
var max = Math.max.apply(null,numbers)
console.log(max) // 9

var min = Math.min.apply(null,numbers)
console.log(min) // 1

// Math.max/Math.min 接收的参数为一组数值，不接受数组 Math.max(value1[,value2, ...])
// apply的作用就是将 数组展开为一组数值
// 还可以用...展开运算符来解决求最值的问题

console.log(Math.max([1, 2, 3, 7, 4])); // NaN
console.log(Math.max.apply(null, [1, 2, 3, 7, 4])); // 7
console.log(Math.max(...[1, 2, 3, 7, 4])); // 7

```

- 伪数组转换
  **Array.prototype.slice.apply**
  **Array.prototype.slice.call**

```
var arraylike = {
  0: 'zhangsan',
  1: 'lisi',
  2: 'wanger',
  3: 'zhaowu',
  length: 4
};

var arr = Array.prototype.slice.apply(arraylike);
var arr1 = Array.prototype.slice.call(arraylike);

console.log(arr); // [ 'zhangsan', 'lisi', 'wanger', 'zhaowu' ]
console.log(arr1); // [ 'zhangsan', 'lisi', 'wanger', 'zhaowu' ]

```

- 变量类型判断
  **Object.prototype.toString.call**
  **Object.prototype.toString.apply**

```
function isArray(obj) {
  return Object.prototype.toString.call(obj) == '[object Array]';
}
console.log(isArray([])); // true
console.log(isArray('qianduan')); //false

// toString()方法允许被修改，以上假定未被修改
// toString()为Object的原型方法，而Array，function等类型作为Object的实例，都重写了toString方法。
// 不同的对象类型调用toString方法时，调用的是对应的重写之后的toString方法（function类型返回内容为函数体的字符串，Array类型返回元素组成的字符串），
// 而不会去调用Object上原型toString方法（返回对象的具体类型），所以采用obj.toString()不能得到其对象类型，只能将obj转化为字符串类型；
// 因此，在想要得到对象的具体类型时，应该调用Object上原型toString方法。

function isArray1(obj) {
  return Object.prototype.toString.apply(obj) == '[object Array]';
}
console.log(isArray1([])); // true
console.log(isArray1('qianduan')); //false
```

- 构造继承

```
function Animal(name, age) {
  this.name = name;
  this.age = age;
  //   console.log(this); // 指向全局
}

function Dog() {
  // 继承自Animal
  // 方法是 将Animal函数在Dog中调用一下，但是Animal中this的指向全局
  //   Animal();
  //   console.log(this); // Dog {}

  //   需要调用apply，改变this指向Dog,这样Dog就继承了Animal的name和age
  Animal.apply(this, ['cat', '5']);
  // Animal.call(this, 'cat', '5');

  //   console.log(this); // Dog { name: 'cat', age: '5' }
  this.say = function() {
    console.log(this.name + ':' + this.age);
  };
}

var dog = new Dog();
dog.say(); //cat:5
```

### **bind** bind()方法创建一个新的函数，在调用时设置 this 关键字为提供的值。并在调用新函数时，将给定参数列表作为原函数的参数序列的前若干项。

**function.bind(thisArg[, arg1[, arg2[, ...]]])**

- thisArg 调用绑定函数时作为 this 参数传递给目标函数的值。 如果使用 new 运算符构造绑定函数，则忽略该值。当使用 bind 在 setTimeout 中创建一个函数（作为回调提供）时，作为 thisArg 传递的任何原始值都将转换为 object。如果 bind 函数的参数列表为空，执行作用域的 this 将被视为新函数的 thisArg。
- argsArray 当目标函数被调用时，预先添加到绑定函数的参数列表中的参数。
- ES6 新增的方法，这个方法会返回一个新的函数（函数调用的方式），调用新的函数，会将原始函数的方法当做传入对象的方法使用，传入新函数的任何参数也会一并传入原始函数。

```
function f(x) {
  this.a = 1;
  this.b = function() {
    return this.a + x;
  };
}
var obj = {
  a: 10
};
var newObj = new (f.bind(obj, 2))(); //传入了一个实参2
console.log(newObj.a); [注释]: <> 输出 1, 说明返回的函数用作构造函数时obj(this的值)被忽略了

console.log(newObj.b()); [注释]: <> 输出3 ，说明传入的实参2传入了原函数original
```
