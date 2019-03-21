# 面向对象

* 基于类
* 基于原型

#### ES6模拟基于类中私有属性的实现：
```
// 模块化文件中，定义非暴露变量Symbol变量
const bar = Symbol('bar');

class Person {
  constructor () {
    // 
  }
  
  [bar] () {
    // 私有方法，外部访问不到，原因：1. Symbol变量唯一 2. Symbol变量未暴露
  }
}
```

