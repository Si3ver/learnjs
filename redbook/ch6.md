# 6. OOP

## 6.1 理解对象

### 属性类型

1. 数据属性
  - [[configurable]]、[[Enumerable]]、[[Writable]]、[[value]]，默认全为true;
  - Object.getOwnPropertyDescriptor()可以查看某对象属性。
  - 使用Object.defineProperty()修改对象的数据属性;

2. 访问器属性
  - [[Configurable]]、[[Enumerable]]、[[Get]]、[[Set]];
  - 访问器属性不能直接定义，必须用Object.defineProperty()来定义。

```javascript
console.log(Object.getOwnPropertyDescriptor(window, "undefined"));
		//Object {value: undefined, writable: false, enumerable: false, configurable: false}
```

```javascript
var coolguy = {
  name: "william chow",
  _age: 22,
  edition: 1,
  job: "master candidate",
  sayName: function() {
    console.log(this.name);
  }
};
console.log(coolguy);		//Object {name: "william chow", age: 22, job: "master candidate", sayName: function}

// I. 修改name属性的值，并使其变为只读
Object.defineProperty(coolguy, "name", {
  writable: false,
  value: "si1ver"
});							//Object {name: "si1ver", age: 22, job: "master candidate", sayName: function}

// II. 修改访问器属性,22岁是第一版，之后edition随年龄更新迭代
Object.defineProperty(coolguy, "age", {
  get: function() {
    return this._age;
  },
  set: function(newAge) {
    if (newAge > 22) {
      this._age = newAge;
      this.edition += newAge - 22;
    }
  }
});
coolguy.age = 28;
console.log(coolguy.edition);	//7
console.log(Object.getOwnPropertyDescriptor(coolguy, "age"));
		//Object {enumerable: false, configurable: false, get: function, set: function} 访问器属性
console.log(Object.getOwnPropertyDescriptor(coolguy, "_age"));
		//Object {value: 28, writable: true, enumerable: true, configurable: true}  
console.log(Object.getOwnPropertyDescriptor(coolguy, "edition"));
		//Object {value: 7, writable: true, enumerable: true, configurable: true}
```

### 定义多个属性

Object.defineProperties()

```javascript
var book = {};

Object.defineProperties(book, {
  //定义一个数据属性 book._year
  _year: {
    writable: true,
    value: 2004
  },
  //定义一个数据属性 book.edition
  edition: {
    writable: true,
    value: 1
  },
  //定义一个访问器属性 book.year
  year: {
    get: function() {
      return this._year;
    },
    set: function(newValue) {
      if (newValue > 2004) {
        this._year = newValue;
        this.edition += newValue - 2004;
      }
    }
  }
});
```

## 6.2 创建对象

### 6.2.1 工厂模式

```javascript
function createPerson(name, age, job) {
  var o = new Object();
  o.name = name;
  o.age = job;
  o.job = job;
  o.sayName = function() {
    console.log(this.name);
  };
  return o;
}

var person1 = createPerson("william", 22, "master candidate");
var person2 = createPerson("si1ver", 25, "front end engineer");
console.log(person1);
console.log(person2);
```

缺点：对象识别困难。所创建的对象只能instanceof到Object这一级。

### 6.2.2 构造函数模式

```javascript
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function() {
    console.log(this.name);
  }
}

var person1 = new Person("william", 22, "master candidate");
var person2 = new Person("si1ver", 25, "front end engineer");
console.log(person1);
console.log(person2);
```

缺点：上述代码对象和函数绑定，每个Person对象都会有一份sayName()代码。
如果改进，指向一个全局函数，则封装性又太差，导致全局函数很乱。

### 6.2.3 原型模式

+ XXX.prototype指向一个对象，叫做原型对象。(XXX是一个构造函数，即Function对象)
+ hasOwnProperty()检测该实例是否具有某属性，以确定访问的是实例属性还是原型对象的属性。
+ in操作符，无论在实例属性还是原型对象中，只要存在该属性，都将返回true。
+ Object.keys()方法接收一个对象，返回所有可枚举属性组成的数组。

注：下例中，Person()是person1与person2两个实例对象的构造函数，其prototype属性指向函数的原型对象Person Prototype。

```javascript
function Person() {}
Person.prototype.name = "william";
Person.prototype.age = 22;
Person.prototype.job = "master candidate";
Person.prototype.sayName = function() {
  console.log(this.name);
};

var person1 = new Person();
var person2 = new Person();
console.log(person1.sayName == person2.sayName);    //true

//添加实例属性
person2.name = "si1ver";
person1.sayName();        //"william"
person2.sayName();        //"si1ver"
person2.hasOwnProperty("name");   //true
console.log("name" in person2);   //true

//删除实例属性
delete person2.name;      
person2.sayName();        //"william"
person2.hasOwnProperty("name");   //false
console.log("name" in person2);   //true

//Object.keys()
console.log(Object.keys(Person.prototype)); //["name", "age", "job", "sayName"]
console.log(Object.keys(Person));           //[]
console.log(Object.keys(person1));          //[]
console.log(Object.keys(person2));          //[]
```

#### 更简单的语法 & 原型的动态性

对象字面量形式来重写原型对象（慎重，会切断现有实例与新原型之间的联系，详见P157图）。

```javascript
function Person(){}
Person.prototype = {
  name: "william",
  age: 22,
  job: "master candidate",
  sayName: function() {
    console.log(this.name);
  }
};

//重设构造函数，只适用于ECMAScript 5兼容的浏览器
//以下两个值默认是true、Object。
Object.defineProperty(Person.prototype, "constructor", {
  enumerable: false,
  value: Person
});

var friend = new Person();
Person.prototype.sayHi = function() {
  console.log("hi");
}

friend.sayHi();
```

### 6.2.4 组合使用构造函数模式和原型模式（集两种模式之长，最常用）

```javascript
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
}

Person.prototype = {
  constructor: Person,
  sayName: function() {
    console.log(this.name);
  }
}

var person1 = new Person("william", 22, "master candidate");
var person2 = new Person("si1ver", 25, "front end engineer");
person1.sayName();        //william
person2.sayName();        //si1ver
console.log(person1.sayName == person2.sayName);    //true
```

### 6.2.5 动态原型模式

所有信息都封装在了构造函数中，兼具前一种方法两优点。

```javascript
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;

  //只在sayName()方法不存在的情况下，才将其添加到原型中
  if (typeof this.sayName != "function"){
    Person.prototype.sayName = function(){
      console.log(this.name);
    }
  }
}
```

### 6.2.6 寄生器构造函数模式

```javascript
function Person(name, age, job){
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function(){
    console.log(this.name);
  }
  return o;
}

var friend = new Person("william", 22, "master candidate");
friend.sayName();       //william
```

寄生器构造函数体内部和工厂模式一样。
该模式可以在特殊情况下用来为对象创建构造函数。例如，想创建一个具有自定义方法的特殊数组，而又不可能修改原生Array()构造函数，则可用该模式实现。

```javascript
function SpecialArray(){
  //创建数组
  var values = new Array();

  //添加值
  values.push.apply(values, arguments);

  //添加方法
  values.toPipedString = function() {
    return this.join("|");
  };

  //返回数组
  return values;
}

var colors = new SpecialArray("red", "blue", "green");
console.log(colors.toPipedString());          //red|blue|green
console.log(colors instanceof Array);         //true
console.log(colors instanceof SpecialArray);  //false
```

### 6.2.7 稳妥构造函数模式

适合在一些安全的环境中（禁用new, this）, 或者防止数据被其他应用程序（如Mashup程序）改动时使用。

***不使用new调用构造函数***。

除了调用方法，没有其他方式访问其数据成员的值。

```javascript
function Person(name, age, job){
  var o = new Object();

  //private members
  var nameUC = name.toUpperCase();

  //public members
  o.sayName = function(){
    console.log(name);
  };
  o.sayNameUC = function(){
    console.log(nameUC);
  }

  return o;
}

var person1 = Person("william", 22, "master candidate");
person1.sayName();            //william
person1.sayNameUC();          //WILLIAM
console.log(person1.name);    //undefined 私有成员无法访问
console.log(person1.nameUC);  //undefined
```

## 6.3 继承

### 6.3.1 原型链

```javascript
function SuperType(){
  this.property = true;
}
SuperType.prototype.getSuperValue = function(){
  return this.property;
};

function SubType(){
  this.subproperty = false;
}
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function() {
  return this.subproperty;
};

var instance = new SubType();
console.log(instance.getSuperValue());  //true
```

1. 别忘记默认的原型 Object.prototype
2. 确定原型XXX(Object/SuperType/SubType)和实例instance的关系 
  - instance instanceof XXX
  - XXX.prototype.isPrototypeOf(instance)
3. 谨慎地定义方法
  - 不要使用对象字面量创建原型方法，它会重写原型链，导致切断原型链。
4. 原型链的问题
  - 由于原型中包含引用类型值所带来的的问题。如下例中，SubType的所有实例都会共享一个colors属性。
  - 创建子类型的实例时，无法向超类型的构造函数传递参数。

```javascript
function SuperType(){
  this.colors = ["red", "blue", "green"];
}

function SubType(){}
SubType.prototype = new SuperType();

var instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors);    //["red", "blue", "green", "black"]

var instance2 = new SubType();
console.log(instance2.colors);    //["red", "blue", "green", "black"]
```

### 6.3.2 借用构造函数（伪造对象\经典继承）

```javascript
function SuperType(){
  this.colors = ["red", "blue", "green"];
}

function SubType(){
  SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors);    //["red", "blue", "green", "black"]

var instance2 = new SubType();
console.log(instance2.colors);    //["red", "blue", "green"]
```

### 6.3.3 组合继承

+ 原型属性和方法 -> 原型链实现继承 sayName()
+ 借用构造函数 -> 对实例属性继承 name, colors

```javascript
function SuperType(name){
  this.name = name;
  this.colors = ["blue", "red", "green"];
}
SuperType.prototype.sayName = function(){
  console.log(this.name);
};

function SubType(name, age) {
  SuperType.call(this, name);   //~借用构造函数 -> 实例属性 name, colors
  this.age = age;
}
SubType.prototype = new SuperType();  //~原型链 -> 原型属性&原型方法sayName()
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
  console.log(this.age);
}

var instance1 = new SubType("william", 22);
instance1.colors.push("black");
console.log(instance1.colors);    //["red", "blue", "green", "black"]
instance1.sayName();              //william
instance1.sayAge();               //22

var instance2 = new SubType("si1ver", 25);
console.log(instance2.colors);    //["red", "blue", "green"]
instance2.sayName();              //si1ver
instance2.sayAge();               //25
```

### 6.3.4 原型式继承
### 6.3.5 寄生式继承
### 6.3.6 寄生组合式继承

可选~
