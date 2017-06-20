# 7. 函数表达式

## 7.1 递归

用***arguments.callee()***代替函数体内函数名。

```javascript
function factorial(num){
  if(num <= 1){
    return 1;
  }else{
    return num * arguments.callee(num - 1);
  }
}

var test = factorial;
console.log(test(10));		//3628800
```

严格模式下，则使用命名函数表达式：

```javascript
"use strict"
var factorial = (function f(num){
  if(num <= 1){
    return 1;
  }else{
    return num * f(num - 1);
  }
});

var x = factorial;
console.log(x(10));			//3628800
```

## 7.2 闭包

```javascript
function Person(name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
}
Person.prototype.sayName = function(){
	console.log(this.name);
}

var p1 = new Person("william", 22, "master candidate");
var p2 = new Person("si1ver", 25, "front end engineer");

function createComperation(propertyName) {
  return function(object1, object2){
    var value1 = object1[propertyName];
    var value2 = object2[propertyName];
    if (value1 < value2){
      return -1;
    }else if (value1 > value2){
      return 1;
    }else {
      return 0;
    }
  };
}

var c = createComperation("age");
console.log(c(p1, p2));				//-1
```

[第六章](https://github.com/Si3ver/learnjs/blob/master/redbook/ch6.md)
