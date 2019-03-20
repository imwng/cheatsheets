# ES6常用特性
ES6是2015年6月发布的一版JavaScript标准，也是现在主流浏览器支持最多的一版。

ES6本身就属于原生JS的范畴，和jQuery这些三方库是完全不同的，ES6需要babel转译成ES5，只是因为浏览器对这些标准的支持还不够。

> 常用特性 [10] ：
* let, const
* 类
* 模块化
* 箭头函数
* 函数参数支持默认值写法
* 模板字符串
* 解构赋值
* 延展操作符
* 对象属性简写
* Promise

## 1. let, const 声明变量
##### ES6之前的JS标准，是不支持块级作用域的，let, const的产生，就是为了补充这一块。
> 块级作用域的优势：
> * 形成独立作用域，避免变量冲突
> * 利于内存回收，块级作用域执行完毕，内部变量会被销毁。

ES6之前var声明的变量，属于函数级作用域，只要通过作用域链，都能访问到对应的变量，从而也会引发一个变量泄露的问题。

```比如最常见的for循环变量泄露
for (var i = 0; i < 5; i++) {
  // pass
}
console.log(i); // 输出5
```

let声明的变量：
```
for (let i = 0; i < 5; i++) {
  // pass
}
console.log(i); // Uncaught ReferenceError: i is not defined

// let, const 通过 {} 也可以产生块级作用域
{
  let a = 3;
}
console.log(a); // Uncaught ReferenceError: a is not defined
```

> let, const 的区别：
> * const变量的值初始化后，不可以被修改
> * 如果修改，会引发错误：Uncaught TypeError: Assignment to constant variable.

##### 暂时性死区
暂时性死区：指在这个区间内引用变量，会引发错误。

暂时性死区的产生原因：let, const 变量提升，但没有初始化。

```暂时性死区的验证：
let a = 10;
{
  console.log(a);
  let a = 10;
}

// 如果不存在let，const变量提升，那么，console.log(a)应该正常输出10

// 事实输出：Uncaught ReferenceError: a is not defined
```

## 2. 类(class)
首先，ES6的类，只是一种语法糖，只是为了让基于类的面向对象开发者更熟悉语法，实际上，JS里的面向对象是基于原型的。

面向对象基于类的抽象方式，只是其他语言的一种描述形式，但这不意味着基于类是面向对象的标配。

面向对象编程的3个特性：
* 抽象与封装
* 继承
* 多态: 同一个消息作用于不同的对象，会产生不同的执行结果。

只要满足以上3个特性，就是面向对象的编程方式，至于是基于什么实现的，只是一个描述方式，可以基于类，同样可以基于原型，只要最终能够实现抽象与封装、继承、多态，就属于面向对象编程的方式。

> 面向对象的描述方式：
> * 基于类(Java, c#等)
> * 基于原型(JavaScript)

```
class Animal {
  // 构造函数
  constructor (name, color) {
    this.name = name;
    this.color = color;
  }
  // toString 是原型对象上的属性
  toString () {
    console.log(`name:${this.name},color:${this.color}`);
  }
}

let animal = new Animal('dog', 'white');
animal.toString();

console.log(animal.hasOwnProperty('name')); // true
console.log(animal.hasOwnProperty('toString')); // false
console.log(animal.__proto__.hasOwnProperty('toString')); // true

class Cat extends Animal {
  constructor (action) {
    super('cat', 'white');
    this.action = action;
  }
  
  toString () {
    console.log(super.toString());
  }
}

let car = new Cat('catch');
cat.toString();

console.log(cat instanceof Cat);  // true
console.log(cat isntanceof Animal); // true
```

## 3. 模块化
ES5是不支持模块化的，但随着大前端的概念越来越丰富，前端能实现的功能也越来越复杂，前端代码也相应复杂起来，模块化的优势就显现出来了。因此，在ES6种，模块化作为一个重要补充添加进来。

> 模块化的语法组成：
* export 暴露接口
* import 引用接口

```
导出接口:[什么都可以，变量，常量，对象，函数]
export var name = 'hello world';

引用接口:
import { name } from '模块文件';

默认导出[default]:
export default var name = 'hello world';

默认引用:
import name from '模块文件';
```

##### ES6模块化的一些特性：
* 一个文件即一个模块
* 模块作用域内只有暴露出的接口内容可以被外部访问，其他内容访问不到。

## 4. 箭头函数 [Arrow]
基础语法格式：() => {}

##### 特性：
* 没有this关键字，引用的是外围this，等于默认执行了语句：```var self = this;```

## 5. 函数参数支持默认值写法
```
ES6写法：
function foo (height = 50, color = 'red') {
  // pass
}

ES6之前：逻辑短路运算实现默认值
function foo (height, color) {
  var height = height || 50;
  var color = color || 'red';
  // pass
}
```

## 6. 模板字符串
写法简单，比起不停的靠+操作符执行拼接命令，代码会清晰不少。
```
var name = `Your name is ${first}${last}`;
```

## 7. 解构赋值
ES6支持解构赋值，能为开发带来不少便利。尤其是对ajax返回的数据进行处理时。

1. 快速获取数组中的值
```
var foo = ['one', 'two', 'three', 'four'];

var [one, two, three] = foo;
console.log(one); // one
console.log(two); // two
console.log(three); // three

// 如果要忽略某些值的获取
var [first, , , last] = foo;
console.log(first); // one
console.log(last);  // four

// 为解构赋值提供默认值
var a, b; 
[a = 5, b = 7] = [1];
console.log(a); // 1
console.log(b); // 7

代码等同于 ==> 

var a = 5,
    b = 7;
[a, b] = [1];
```

2. 快速获取对象中的值
```
const student = {
  name:'Ming',
  age:'18',
  city:'Shanghai'  
};

const {name, age, city} = student;
console.log(name);  // Ming
console.log(age); // 18
console.log(city);  // Shanghai
```

##### 解构赋值的应用技巧：2个值的快速交换
```
a, b交换：
[a, b] = [b, a];

解构赋值之前的2个值交换：
1. 借助temp中间量
2. 借助某种数学表达式
3. 借助数组[原理同temp中间量，还是借助了数组其中一个位置存储临时值]
```

## 8. 延展操作符 [Spread Operator]
1. 数组去掉最外层[]，形成一个序列
2. 对象去掉最外层{}，形成一个序列

## 9. 对象属性简写
```
const name='Ming',age='18',city='Shanghai';

const student = {
    name:name,
    age:age,
    city:city
};

console.log(student); //{name: "Ming", age: "18", city: "Shanghai"}

简写：
const student = {
  name,
  age,
  city
}
```

## 10. Promise 异步编程
同步编程的概念：JS代码的执行顺序 === 代码的书写顺序。

异步编程的概念：调整JS代码的执行顺序，让JS代码的执行顺序 [异于] 代码的书写顺序。个人感觉异步里的异一词的含义时区别于正常的代码书写顺序，而不是在另一个线程执行的意思。

异步编程的解决方案：
1. callback
2. promise
3. 多线程[webworker]: 多线程属于实现异步编程的一种方式，但这不是多线程的唯一功能。
