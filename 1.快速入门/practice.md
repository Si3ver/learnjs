## 字符串
ES6模板字符串,注意，是反引号。
```
var name = '小明';
var age = 20;
alert(`你好, ${name}, 你今年${age}岁了!`);
```

## 数组
练习：在新生欢迎会上，你已经拿到了新同学的名单，请排序后显示：欢迎XXX，XXX，XXX和XXX同学！
```
'use strict';
var arr = ['小明', '小红', '大军', '阿黄'];
var last = arr.pop();
var x = arr.join(",");
alert(`欢迎${x}和${last}同学`);
```
## 对象

