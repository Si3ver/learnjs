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

var compareNames = createComperation("name");
var result = compareNames({name: "william"}, {name: "si1ver"});
console.log(result);   //1
compareNames = null;   //解除对匿名函数的引用，以便释放内存
```

当createComperation()返回后，其执行环境的作用域链会被销毁，但它的活动对象仍会保留在内存中，直到匿名函数的活动被销毁才会被回收。
参见P180图，理解闭包。

### 7.2.1 闭包与变量

```javascript
function createFunctions(){
  var result = new Array();
  for(var i = 0; i < 10; i++){
    result[i] = function(){
      return i;
    }
  }
  return result;
}
console.log(createFunctions()[0]());  //10
console.log(createFunctions()[1]());  //10
console.log(createFunctions()[2]());  //10
```

```javascript
function createFunctions(){
  var result = new Array();
  for(var i = 0; i < 10; i++){
    result[i] = function(num){
      return function(){
        return num;
      };
    }(i);
  }
  return result;
}
console.log(createFunctions()[0]());  //0
console.log(createFunctions()[1]());  //1
console.log(createFunctions()[2]());  //2
```



[第六章](https://github.com/Si3ver/learnjs/blob/master/redbook/ch6.md)
