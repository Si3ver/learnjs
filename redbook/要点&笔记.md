发现一篇比较棒的笔记：https://www.anotherhome.net/2073 

### 3.4 数据类型

+ ```5+1``` 5种基本数据类型：Undifined、Null、Boolean、Number、String; 1种复杂数据类型(引用类型)：Object。

+ typeof关键字结果分别为："undifined"、"object"、"boolean"、"number"、"string"。一般引用类型typeof结果为"object"，函数(属于特殊的引用类型)typeof结果为"function"。

+ 检测引用类型的具体类型用```instanceof```关键字，返回true\false。

+ ECMAScript 5新增了Array.isArray()方法，解决了两个以上不同全局执行环境中用instanceof Array无法区分的问题。


## 4. 变量、作用域和内存问题

+ ECMAScript中所有函数的参数都是按值传递的。只不过引用类型的访问方式为引用。(见书P71下)

+ javascript执行环境类型只有两种：全局&局部(函数)，没有块级作用域。

### 5.4 RegExp类型

~ 记录要点、易混淆点，特别细的小点不用太在意。不要变成“抄书”，否则浪费时间&毫无意义。
