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
* 多态

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
