### html表单

基本表单三元素

form，input，button

form属性：

method：只支持get和post

action：有action属性时使用action属性的url作为目标地址，没有时使用网页本身url作为目标地址，如果为相对url则嫁接到网页本身url上，有base则嫁接到base的href上

一般采用post方法

enctype：编码方式，有三种，

1. application/x-www-form-urlencoded
2. multipart/form-data：用于文件上传
3. text/plain：因浏览器而异

autocomplete："on"/"off"，autocomplete可以用于form元素，也可以用于单个input元素

在任何表单元素内都有form属性，当表单元素不在form内时，可以通过form属性设置为form的id来进行关联

button的三种type：submit：提交表单，当设置为submit时button有其它属性可以进行设置

1. formaction：另外设置form的action而不在form元素内设置
2. formmethod：另外设置form的method
3. formtarget：另外设置form的target
4. formenctype：另外设置form的enctype
5. formnovalidate：是否进行有效性验证

##### input[type=text]

maxlength：最大输入数量

size：最大显示数量

placeholder

pattern：验证的正则表达书

value：初始值

- 数据列表：将list属性设置为datalist的id以自动补全

```html
<input type="text" list='datalist'></input>
<datalist id='datalist'>
	<option value='first item' label='show first item'></option>
	<option value='second item'>show second item</option>
</datalist>
```

其中datalist中value值是作为input中的到的值，而label或者元素内的值用作额外的标记

##### iframe[sanbox seamless src srcdoc width height]

sanbox：对html文档进行限制，包括表单，脚本，插件，指向其它浏览上下文的链接，允许的值有

1. allow-scripts
2. allow-forms
3. allow-top-navigation
4. allow-same-origin