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

