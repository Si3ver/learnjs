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

。。。

## 条件判断
```
// 'use strict';

// var height = parseFloat(prompt('请输入身高(m):'));
// var weight = parseFloat(prompt('请输入体重(kg):'));

// var bmi = weight * 1.0 / (height*height);

// if(bmi <= 18.5){
// 	alert('过轻');
// }else if(bmi <= 25){
// 	alert('正常');
// }else if(bmi <= 28){
// 	alert('过重');
// }else if(bmi <= 32){
// 	alert('肥胖');
// }else{
// 	alert('严重肥胖');
// }


// 'use strict';
// // var arr = ['Bart', 'Lisa', 'Adam'];
// // var i;
// // alert(arr.length);
// // for(i = 0; i < arr.length; i++){
// //     alert(arr[key]);
// // }

// function sum(...rest) {
//     var s = 0;
//     for(var i = 0; i < rest.length; i++){
//         s += rest[i];
//     }
//     return s;
// }

'use strict'
function area_of_circle(r,pi){
	// alert(arguments[1] === undefined);
    var s;
    if(arguments[1] === undefined){
        s = r*r*3.14;
    }else{
        s = r*r*pi;
    }
    return s;	
}

alert(area_of_circle(2));
alert(area_of_circle(2,2.1416));
```

