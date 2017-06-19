## 6. OOP

### 6.1 理解对象

#### 属性类型

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

### 6.2 创建对象

#### 1. 工厂模式

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

### 2. 构造函数模式

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

### 3. 原型模式

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

#### 组合使用构造函数模式和原型模式（集两种模式之长，最常用）

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

#### 动态原型模式

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

#### 寄生器构造函数模式

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
