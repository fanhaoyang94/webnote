## 关于 new 关键字

### Object.create()

**基本语法：**
```JS
Object.create(proto,[propertiesObject])
```

**参数：**
- `proto`：新创建的对象的原型对象（必须）
- `propertiesObject`：可选。要添加到新对象的属性描述符（类似于 `Object.defineProperties`的第二个参数）

**返回值：**
- 一个新对象，带着指定的原型对象和属性


**与 new Object() 和字面量 {} 的区别：**

`new Object()`和`{}`创建的对象继承自`Object.prototype`，而`Object.create()`可以指定原型，甚至可以创建没有原型的对象（将第一个参数指定为 null）。
---

### 探索 new 魔法
好了回归正题我们来探索 `new` 关键字，先看下面这段代码
```JS
let Animal = function(name,age){
  this.name = name;
  this.age = age;
};
Animal.prototype.getName = function(){
  return this.name
}
let dog = new Animal('dog',1);
console.log(dog.getName);                 // obj
console.log(dog.age);                     // 1
```

可以看到实例 `dog` 可以访问到 `Animal` 构造函数中的属性，也可以访问到其原型对象 `Animal.prototype` 上的属性.

**让我们换种方式，不实用new关键字**
```JS
let Animal = function(name,age){
  this.name = name;
  this.age = age;
}
let dog = Animal('dog',1);
console.log(dog);              // undefined
console.log(age);               // 1
console.log(dog.name);         // Cannot read property 'name' of undefined
```

从结果可以看到，不使用 `new` 关键字，返回的结果是 `undefined` 。我们直接打印 `age` 可以获取到值，是因为JavaScript代码在默认情况下 `this` 的指向是window。

*再来看下，构造函数返回一个对象会发生什么**
```JS
function Animal(name){
  this.name = name;
  return {age:1}
}
let dog = new Animal('dog');

console.log(dog);                 // {age:1}
console.log(dog.name);            // undefined
console.log(dog.age);             // 1
```
从结果可以看到：如果构造函数 `return` 出来的是一个和 `this` 无关的对象时，`new` 命令会直接返回这个对象，而不是生成一个绑定了最新 `this` 新对象并返回出来。

如果构造函数return回来的不是一个对象，那么还是会按照new的实现步骤，返回新生成的对象
```JS
// 我们通过代码来验证下

function Animal(name){
  this.name = name;
  return 'animal'
}

let dog = new Animal('dog');
console.log(dog);                 // Animal { name: 'dog' }
console.log(dog.name);            // dog
```

**总结：new 到底都施了哪些魔法**

- 创建一个空的简单的 `JavaScript` 对象，即 `{}`;
- 将这个空对象的原型指向构造函数的 `prototype` 属性
- 将构造函数的``绑定到这个新的对象
- 执行构造函数内部的代码
- 如果构造函数没有返回对象，则返回这个新对象


### new 的实现
结合上面的内容，我们一起来创造魔法--实现 `new`
```JS
function _new(constructor, ...args){
  // 参数校验
  if(typeof constructor !== 'function'){
    throw new TypeError('constructor must be a function');
  }

  // 1. 创建以构造函数原型为原型的新对象
  const obj = Object.create(constructor.prototype);

  // 2. 执行构造函数，绑定 this 到新对象
  const result = constructor.apply(obj, args);

  // 3. 处理返回值
  // 如果构造函数返回对象或者函数，则返回该值
  // 否则返回新创建的对象
  const isObj = result && (typeof result === 'object' || typeof result === 'function');
  
  return isObj? result : obj;
}

// 语法测试

function Animal(name,speed) {
    this.name = name;
    this.speed = speed;
}

Animal.prototype.getSpeed = function() {
  console.log(`The speed of the ${this.name} is ${this.speed}km/h`)
}

// 测试 class
const dog = _new(Animal,'dog',10);
const tiger = new Animal('tiger',100);

console.log('\n=== 测试 class ===');
console.log(dog);                             // Animal { name: 'dog', speed: 10 }
console.log(tiger);                           // Animal { name: 'tiger', speed: 100 }
dog.getSpeed();                               // The speed of the dog is 10km/h
console.log(dog instanceof Animal);           // true
```

**实现要点总结**

**原型链连接：** 使用 Object.create(constructor.prototype) 确保新对象的原型正确

**this 绑定：** 使用 apply 方法将构造函数的 this 绑定到新对象

**返回值处理：** 只有当构造函数返回对象或函数时，才返回该值，否则返回新对象

**错误处理：** 确保传入的构造函数确实是函数

这个实现完整地模拟了原生 new 关键字的行为。理解这个实现有助于深入掌握 JavaScript 的原型机制和 this 绑定规则。


### 参考链接

[1][完全搞懂js 中的new()到底做了什么?](https://blog.csdn.net/qq_27674439/article/details/99095336)

[2][一起搞清楚JS中的new到底做了什么，并模拟实现一个new](https://zhuanlan.zhihu.com/p/364634740)
