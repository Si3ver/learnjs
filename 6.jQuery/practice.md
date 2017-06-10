
### jQuery -> 选择器
```练习
selected = $('#para-1');
selected = $('.color-red.color-green');
selected = $('.color-red');
selected = $('#test-jquery > p');  or selected = $('[class^=color-]')
selected = $('input[name=name]');
selected = $('input[name=name],input[name=email]');
```

#### jQuery -> 选择器 -> 层级选择器
```
selected = $('[class^=lang-]');    //所有语言
selected = $('.test-selector > ul > li');    //所有动态语言
selected = $('.test-selector > ol > li');    //所有静态语言
selected = $('.lang-javascript')    //javascript
selected = $('.lang-lua');
selected = $('.lang-c');
```
