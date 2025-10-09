## JS 继承

### 核心概念：原型链

在理解继承之前，必须明白 JavaScript 的原型链机制：

  - 每个对象都有一个隐藏属性 [[Prototype]]（可通过 __proto__ 访问）

  - 每个函数都有一个 prototype 属性

  - 当访问对象的属性时，如果对象自身没有，就会沿着原型链向上查找

  ```JS
  // 简单的原型链示例
  const parent = { name: "Parent" };
  const child = { age: 10 };

  // 设置 child 的原型为 parent
  Object.setPrototypeOf(child, parent);

  console.log(child.age);    // 10 - 自身属性
  console.log(child.name);   // "Parent" - 从原型继承
  ```

### 原型链继承

  原型链继承是JS最基础的继承方式，每一个构造函数都有原型对象 `prototype` ，原型对象都包含指向构造函数的指针 `constructor` ，实例则包含一个指向原型对象的指针 `__proto__`。让一个构造函数的原型对象等于另一个类的实例，此时原型对象将包含一个指向另一个原型的指针，如此层层递进，就构成了实例与原型的链条。相应的可以在原型链上查找属性

  ```JS

  function Parent(){
    this.name = 'parent'
    this.color = ['red','green','yellow'] 
  }

  Parent.prototype.say = function(){
    console.log('我是',this.name)
  }

  function Child(){
    this.age = 3
  }

  Child.prototype = new Parent();   // 核心：将Child的原型指向Parent的实例

  const child1 = new Child();
  child1.say();                      // 我是 parent
  child1.color.push('pink');
  console.log(child1.color);          // ['red','green','yellow','pink']

  const child2 = new Child();
  console.log(child2.color)           // ['red','green','yellow','pink']注意：child2的children也被修改了

  ```

  **缺点：**

  - 引用类型属性被所有实例共享

  - 创建子类实例时，无法向父类构造函数传参

### 构造函数继承
  ```JS
  function Parent(name){
    this.name = name;
    this.colors = ['red','green']
    this.say = function(){
      console.log('我是',this.name)
    }
  }

  Parent.prototype.move = function(){
    console.log('向前移动')
  }

  function Child(name,age){
    // 关键：在子类构造函数中调用父类构造函数
    Parent.call(this,name)  // 相当于把 Parent 的代码在这里执行一遍
    this.age = age;
  }

  const child1 = new Child('child1',10);
  const child2 = new Child('child2',20);

  child1.colors.push('yellow');
  console.log(child1.colors);              // ['red', 'green', 'yellow']
  console.log(child2.colors);              // ['red', 'blue'] - 引用类型不共享了！

  child1.say();                            // 我是 child1
  child1.move();                           // VM503:27 Uncaught TypeError: child1.move is not a function

  ```

  **优点：**

  - 避免了引用类型属性共享

  - 可以向父类传参

  **缺点：**

  - 方法都在构造函数中定义，每次创建实例都要创建一遍方法，无法复用
  - 只能继承父类实例属性和方法，不能继承原型属性和方法
  

### 组合继承

  结合原型链继承和构造函数继承的有点
  
  ```JS
  function Parent(name){
    this.name = name;
    this.colors = ['red','green'];
  }

  // 方法定义在原型上，实现复用
  Parent.prototype.move = function(){
    console.log('向前移动');
  }

  function Child(name,age){
    // 1. 继承属性（构造函数继承）
    Parent.call(this, name);              // 第二次调用Parent
    this.age = age;
  }

  // 2. 继承方法（原型链继承）
  Child.prototype = new Parent();         // 第一次调用 Parent
  Child.prototype.constructor = Child;    // 修复构造函数

  // 子类自己的方法
  Child.prototype.say = function(){
    console.log('我是', this.name);
  }

  const child1 = new Child('child1',10);
  const child2 = new Child('child2',20);

  child1.colors.push('yellow');
  console.log(child1.colors)              // ['red', 'green', 'yellow']
  console.log(child2.colors)              // ['red', 'green']
  child1.move()                           // 向前移动
  child2.say()                            // 我是 child2
  ```

  **优点：**

  - 融合了两种继承方式的优点，是 JavaScript 中最常用的继承模式

  **缺点：**

  - 调用了两次父类构造函数


### 原型式继承
  ```JS
  function createObject(o) {
    function F() {}
    F.prototype = o;
    return new F();
  }

  const parent = {
    name: 'Parent',
    colors: ['red', 'blue'],
    sayName: function() {
        console.log(this.name);
    }
  };

  const child1 = createObject(Parent.parent);
  const child2 = createObject(Parent.parent);

  child1.name = 'child1';
  child1.colors.push('green');

  console.log(child2.colors); // ['red', 'blue', 'green'] - 还是共享引用类型


  // ES5 的 Object.create() 就是这种模式的规范化：

  const parent = {
      name: 'Parent',
      colors: ['red', 'blue']
  };

  const child = Object.create(parent, {
      age: {
          value: 10,
          writable: true,
          enumerable: true
      }
  });
  ```

### 寄生式继承

  ```JS
  function createChild(parent,age){
    const clone = Object.create(parent); // 创建对象
    clone.age = age;                      // 增强对象
    clone.sayAge = function(){
      console.log(this.age)
    }
    return clone
  }

  const parent = {
    name: 'parent',
    colors: ['red','blue']
  }

  const child = createChild(parent,10);
  ```

### 寄生组合式继承（理想）

  这是最完美的继承方式，也是 ES6 class extends 的实现原理。
  
  ```JS
  function inheritPrototype(Child,Parent){
    // 创建父类原型的副本
    const prototype = Object.create(Parent.prototype)
    // 修复 constructor 指向
    prototype.constructor = Child;
    // 设置子类原型
    Child.prototype = prototype;
  }

  function Parent(name){
    this.name = name;
    this.colors = ['red','green'];
  }

  Parent.prototype.sayName = function(){
    console.log(this.name);
  }

  function Child(name,age){
    Parent.call(this,name);             // 只调用一次父类构造函数
    this.age = age;
  }

  // 实现继承
  inheritPrototype(Child,Parent);

  // 子类自己的方法
  Child.prototype.sayAge = function(){
    console.log(this.age);
  }

  const child1 = new Child('child1',10)


  // 另一种古老的写法，个人觉得更接近原理，更好理解

  function createObj(obj){
    function F(){};
    F.prototype = obj;
    return new F()
  }

  function inheritPrototype(Parent,Child){
    Child.prototype = createObj(Parent.prototype);
    Child.prototype.constructor = Child;
  }

  function Parent(name){
    this.name = name;
    this.colors = ['red','green']
  }
  Parent.prototype.sayName = function(){
    console.log(this.name)
  }

  function Child(name,age){
    Parent.call(this,name);
    this.age = age;
  }

  inheritPrototype(Parent,Child)

  Child.prototype.sayAge = function(){
    console.log(this.age);
  }

  const child1 = new Child('child1', 10);
  const child2 = new Child('child2', 20);
  child1.colors.push('blue');
  console.log(child1.colors);                   // ['red', 'green', 'blue']
  console.log(child2.colors);                   // ['red', 'green']
  child1.sayName();                             // child1
  child2.sayAge();                              // 20
  ```
  **优点：**

  - 只调用一次父类构造函数

  - 避免了在子类原型上创建不必要的属性

  - 原型链保持不变


### ES6 的 Class 继承

  ```JS
  class Parent {
    constructor(name){
      this.name = name;
      this.colors = ['red','green'];
    }

    sayName(){
      console.log(this.name)
    }
  }

  class Child extends Parent{
    constructor(name,age){
      super(name)                           // 必须调用 super()
      this.age = age
    }

    sayAge(){
      console.log(this.age)
    }

    // 方法重写
    sayName(){
      super.sayName();                      // 调用父类方法
      console.log('这是子类重写的方法');
    }
  }

  const child1 = new Child('child1',10);
  const child2 = new Child('child2',20);
  child1.colors.push('blue');
  console.log(child1.colors);               // ['red', 'green', 'blue']
  console.log(child2.colors);               // ['red', 'green']
  child1.sayName();                         // 'child1'   '这是子类重写的方法'
  child1.sayAge();                          // 10
  ```


### 参考链接
[1] [JavaScript 继承（五）寄生式、组合寄生继承](https://www.jianshu.com/p/db7c4e9a80a3)

[2] [JS 常见的 6 种继承方式](https://blog.csdn.net/jatej/article/details/120317973)
