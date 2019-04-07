### ECMAScript6
#### 变量
##### let和var
let变量只在代码块中存在，只在一轮循环中有效如：

```
var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 6
```
但如过用var输出结果会是10
并且let变量在循环变量和循环体中属于不同的作用域，如
```
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
//abc
//abc
//abc
```
var存在变量提升的现象，let则没有
```
console.log(a);//undefine
var a = 123;
console.log(b);//Error
let b = 234;
```
这一条产生了另一个影响，就是在一个区块内如果有let或const，即使在区块外用var定义了变量，在区块内也不能在定义变量前使用，如：
```javascript
var a = 123;
if 1 {
	tmp = 234;//error
	let tmp;
}
```
所以要尽量定义变量后再使用，但要注意在同一个作用域不能重复定义同一变量

作用域的问题，ES6块级作用域内用let定义变量不会出现变量对外部作用域的影响，同时规定可以在块级作用域中定义函数，但是函数不能在作用域外引用。例如：
```
function f() { console.log('I am outside!'); }

(function () {
  function f() { console.log('I am inside!'); }
  if (false) {
  }
  f();
}());
```

解构赋值
ES6中的解构赋值使用模式匹配的方法
只要对象支持迭代就可以使用解构赋值
用起来类似这样
```
var [a,[b,c]] = [1,[2,3]]
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth] = fibs();
let {foo,bar} = {foo:'f',bar:'b'};
```
for of 遍历可迭代对象，如：
```
var map = new Map();
map.set('foo','1')
map.set('bar','2')
for (let [key,value] of map) {
	console.log(key + '->' + value)}
```
####  字符串
新增unicode表示法

```
'\u{1F680}' === '\uD83D\uDE80'
```
当码点大于ffff时
\u{xxxxx}与\uxxxx\uxxxx等价

字符串方法str.codePointAt()
能正确识别4字节存储的字符的十进制码
str.fromCodePoint()作用相反，输入16进制utf码输出字符，可识别32位utf-16

for of 循环同样可以识别32位utf-16字符！如：
```
for (let c of string){
	console.log(c)
}
```

str.normalize()方法
字符的正规化，有一些带有音标的字符有多种表示方法，normalize将合成字符全部分割为多utf-16表示的方法
```
'\u01D1'.normalize() === '\u004F\u030C'.normalize()
```
参数参考：
>NFC，默认参数，表示“标准等价合成”（Normalization Form Canonical Composition），返回多个简单字符的合成字符。所谓“标准等价”指的是视觉和语义上的等价。
>NFD，表示“标准等价分解”（Normalization Form Canonical Decomposition），即在标准等价的前提下，返回合成字符分解的多个简单字符。
>NFKC，表示“兼容等价合成”（Normalization Form Compatibility Composition），返回合成字符。所谓“兼容等价”指的是语义上存在等价，但视觉上不等价，比如“囍”和“喜喜”。（这只是用来举例，normalize方法不能识别中文。）
>NFKD，表示“兼容等价分解”（Normalization Form Compatibility Decomposition），即在兼容等价的前提下，返回合成字符分解的多个简单字符。

str.padStart()和str.padEnd()
用提供的字符补全一定宽度的字符串，如
```
'aa'.padStart(5,'cd');//'cdcaa'
```
模板字符串
yong两个`括起来的字符串，可以用${}插入js代码，也可以作为多行字符串使用，如：
```
var word = 'js';
var sentence = `hello ${word}`;
```
字符串模板也是可以嵌套的，如：
```
var a = addrs => `<div>${addrs.map(addr=>`<p>${addr.value}</p>`)}</div>`
a({value:'this is the value in addr'})
```
raw()方法
str.raw()返回原始未转义字符串，如
```
'abc\nd'.raw();//'abc\\nd'
```

#### 正则表达式
定义模板方法

```
var regexp = /xyz/i;
or
var regexp = new RegExp('xyz','i');
在es6中新增
var regexp = new RegExp(/abc/ig, 'i').flags;//忽略原有模式参数
```
其中第二个参数是模板的使用方式

加u修饰可以使正则表达式支持32为unicode-16，如：
```
var s = '𠮷';
/^.$/.test(s) // false
/^.$/u.test(s) // true
```
匹配模式中可以用unicode表示，注意要在前面加\u，否则会解释为量词如：
```
/\u{20BB7}/u.test('𠮷') // true
```
>修饰符g，全局匹配，后一次匹配从上一次匹配之后的位置开始
>修饰符y，全局粘连匹配，后一次匹配从前一次之后的首个位置开始，相当于在么一次匹配中都使用了^头部匹配
>修饰符u，匹配32位utf-16字符

量词使用c{n}表示，表示n个c，如：
```
var reg = /a{2}/;
```
字符串的正则方法，一共有4种如下：
String.prototype.match 调用 RegExp.prototype[Symbol.match]
String.prototype.replace 调用 RegExp.prototype[Symbol.replace]
String.prototype.search 调用 RegExp.prototype[Symbol.search]
String.prototype.split 调用 RegExp.prototype[Symbol.split]

regexp对象的使用方法：
```
var regex = /a/g;//全局搜索a
'aaxa'.replace(regex,'-');//使用字符串的正则表达式方法
or
regex.exec('aaxa');//搜索第一个a
regex.exec('aaxa');//搜索第二个a
...
```
正则表达式中的 . 可以代表单个字符，但是有两个例外，一个是32位的utf-16字符，这个通过u模式可以匹配，还有一个是行终止符，行终止符包括以下四种：
U+000A 换行符（\n）
U+000D 回车符（\r）
U+2028 行分隔符（line separator）
U+2029 段分隔符（paragraph separator）
当要匹配真正的所有字符时可以采用dotAll的匹配模式s：
```
/abc.efg/s.test('abc\nefg');//true
```
正则表达式中的先行断言，先行否定断言，后行断言（ES2018），后行否定断言（ES2018），如：
```
/\d+(?=%)/.exec('100%');//100
/\d+(?!%)/.exec('100%');//10
/(?<=a)\d+/.exec('30a20');//20
/(?<!a)\d+/.exec('a2');//0
```
#### 数值
二进制表示法：
0b10101
八进制表示法：
0o767
二，八进制字符串转换为十进制
Number('0o767');//503

ES6在Number内移植了parseInt和parseFloat，逐步模块化

Number.isInteger();判断时候为整数
要注意js内部整数和浮点数采用同样的存储方法，所以
```
Number.isInteger(25.0);//true
```
同时js数值存储为64位双精度格式（53个二进制位，1个隐藏位和52个有效位），所以精度超过的位会被丢弃，isInteger()会误判：
```
Number.isInteger(5.00000000000000002);//true
```
Number.EPSILON，一个常量，表示浮点数最小能表示的精度

Number.isSafeInteger();判断整数是否处在正确范围内

ES6为Math对象新增了与数学相关的方法，有如下：

Math.trunc方法用于去除一个数的小数部分，返回整数部分，相当于对<0的数计算Math.ceil()，对>0的数计算Math.floor()

Math.sign方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。具体返回值是：
参数为正数，返回+1；
参数为负数，返回-1；
参数为 0，返回0；
参数为-0，返回-0;
其他值，返回NaN。

Math.cbrt方法用于计算一个数的立方根

Math.clz32，JavaScript 的整数使用 32 位二进制形式表示，Math.clz32方法返回一个数的 32 位无符号二进制整数形式有多少个前导 0。
Math.clz32(1);// 31
Math.clz32(1 << 1);// 30
Math.imul();
Math.imul();
Math.hypot();
Math.expm1();
Math.log1p();
Math.log10();
Math.log2();
ES6 新增了 6 个双曲函数方法。
Math.sinh(x) 返回x的双曲正弦
Math.cosh(x) 返回x的双曲余弦
Math.tanh(x) 返回x的双曲正切
Math.asinh(x) 返回x的反双曲正弦
Math.acosh(x) 返回x的反双曲余弦
Math.atanh(x) 返回x的反双曲正切
ES2016可以使用 ** 进行指数运算：
3 ** 2; //9
2 ** 3 ** 2; //512

#### 函数
函数预定义参数默认值，如：

```
function func(a = 'world'){
	console.log('hello ' + a);
}
```
要注意的是参数的默认值不是静态的：
```
function func(p = x + 1){
	console.log(p)
}
var x = 100;
func();//101
```
可以传递对象给函数：
```
function func({x, y=5} = {}){
	console.log(x, y);
}
```
通过给函数参数最外层赋默认值，即使没有传入对象作为参数也会有默认的{}作为参数，当传入了对象而没有y时，使用5作为y的值

要注意一下两种写法的区别
```
// 写法一
function m1({x = 0, y = 0} = {}) {
  return [x, y];
}

// 写法二
function m2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
```

带有默认值额参数应该是函数的尾参数，否则参数是无法省略的
```
function f(x = 1, y) {
  return [x, y];
}
f() // [1, undefined]
f(2) // [2, undefined])
f(, 1) // 报错
f(undefined, 1) // [1, 1] 当输入undefined时可以让函数使用这个位置的默认值
```

函数的length属性返回函数没有默认值的参数数量（必须输入的参数数量）
```
function func(a,b,c=10){
	console.log(a,b,c);
}
func.length;//2
```

函数在有默认参数时，参数区域会形成一个作用域，如：
```
var x = 1;
function func(x, y=x){
	console.log(y);
}
func(3);//3
```

函数的rest参数，使用...变量名的方式获取剩余变量，其中rest是一个数组，如
```
function func(a,...rest){
	for i in rest {
		console.log(i)
	}
}
```

ES2016规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。

function.name返回函数名，但是对于匿名函数赋值给变量的情况ES5和ES6处理不同，如下
```
var f = function(){};
f.name;//''  ES5
f.name;//'f' ES6
```

箭头函数，用法如下
```
var sum = (a,b) => a+b;
//等价于
var sum = (a,b) => {return a+b;}'
(args)=>{
	doSomething;
}
```
箭头函数与定义函数的不同：
this对象是固定的，就是定义时所在的对象，其原因是箭头函数没有this，它的this使用了外层结构的this
不能当构造函数，不能用作new
函数内不存在arguments对象
不能使用yield，不能用作生成器

因为一般函数使用的bind，apply，call改变了本身this的指向，而箭头函数没有this对象，也就无法使用这些方法了

用箭头函数实现管道（pipeline）
```
var multifuncs = (...funcs) => val => funcs.reduce((a,b)=>b(a),val);
var mul = multifuncs((x)=>x+1,(x)=>x*2);
mul(5);//12
```

函数的尾调用
```
function f(x){
	return g(x);//尾调用
}
```

尾调用优化
当尾部调用的函数不使用到外部函数的内部变量时，当运行尾调用的函数时就可以删除外部函数的调用帧，只保留尾调用的调用帧，以达到节省内存的目的

函数柯里化
函数的柯里化也就是将函数所需要输入的变量减少，将第一个变量设置为默认，实现的一个方法就是从柯里化的函数输入所需变量，将第一个变量保存在内部，然后返回一个减少了一个变量的函数，这个函数内部调用了需要柯里化的函数。
```
function tailfactorial(n,total){
	if (n === 1) return total;
	return tailfactorial(n-1,n * total);
}//尾调用的阶乘函数
function currying(f, n){
	return function(m){
		return f.call(this,m,n)
	}
}
var factorial = currying(tailfactorial,1);
factorial(4);//24
```

另一个方法就是使用ES6中的函数默认参数：
```
function factorial(n,total=1){
	if (n === 1) return total;
	return factorial(n-1,n * total);
}
factorial(4);//24
```

要注意的是函数的尾调用优化仅在严格模式下有效，因为一般情况下函数有两个属性func.arguments和func.caller，分别指示函数输入的参数和调用这个函数额函数，在严格模式下这两个属性会失效

蹦床函数trampoline，蹦床函数可以将递归调用的函数转换为循环调用，实现如下
```
function trampoline(fn){
	while (fn && fn instanceof Function){
		f = f();
	}
	return f;
}
```

ES6允许函数定义和运行时最后一个参数后有一个逗号
```
function func(param1,param2,){doSomething};
func(a,b,);
```

#### 数组

数组前加...
...[a,b,c]表示函数参数中...rest的逆过程（拓展运算符），将数组转换为参数序列，如
```
function func(...rest){
	console.log(...rest);
}
func(1,2,3);//1 2 3
```

拓展运算符后还可以放置表达式
```
var n = 1
var a = [...(n>1?[1,2]:[3,4]),5];//[3,4,5]
```

数组的复制，拓展运算符提提供了复制数组的新方法，以为直接的数组赋值复制指数复制只想底层数组的指针，并不是复制数组的内容，所以复制数组内容要使用其它的方法
```
var a = [1,2,3];
var b = a;
b[0] = 0;
a;//[0,2,3]

var b = a.concat();//concat方法会复制原来数组添加新内容而不改变原来数组内容
var b = [...a];//ES6中
```

数组的合并
```
var a = [1,2,3];
var b = [4,5,6];
var c = a.concat(b);
var d = [...a,...b];
```
要注意的是这两种的方法都是对数组内容的浅拷贝：
```
var a = [{a:1}];
var b = [{b:2}];
var c = a.concat(b);
var d = [...a,...b];
a[0] === c[0];//true
a[0] === d[0];//true
```

**将字符串转变为数组**（可以识别32位utf字符）
```
[...'hello'];//['h','e','l','l','o']
```

**将可迭代对象（Iterator,Generator）转变为数组**
```
var div = document.querySelectAll('div');
var divlist = [...div];

var iterfunc = func*(){
	yield 1;
	yield 2;
	yield 3;
}
var li = [...iterfunc()];//[1,2,3]
```

Array.from()方法
Array的from方法可以将类数组对象如Map和Set转换为数组，或将可迭代对象转换为数组
```
var div = document.querySelectAll('div');
var divlist = Array.from(div);

var arr_like = {
	'0':'a',
	'1':'b',
	'2':'c',
	length:3
};
var arr = Array.from(arr_like);//['a','b','c']
```
from中第二个参数为map回调函数，如
```
Array.from(set([1,2,3]),x => x*x);//[1,4,9]
```

Array.of()，Array的of方法将数值转换为数组，与Array()的区别是参数数量不同不会导致重载，如
```
var a = Array();//[]
var a = Array(3);//[,,]
var a = Array(1,2,3);//[1,2,3]
var a = Array.of();//[]
var a = Array.of(3);//[3]
var a = Array.of(1,2,3);//[1,2,3]
```
数组copyWithin方法，数组的copyWithin方法使数组从设定的位置复制值以改变原数组，输入参数为copyWithin(target, start可选, end可选)，表示将从start开始到end结束的位置的元素复制到target开始的位置
```
var a = [1,2,3,4,5];
a.copyWithin(0,3);
a;//[4,5,3,4,5]
```

数组的find()和findIndex();
数组的find()方法输入一个回调函数，find依次把数组的值输入到函数并返回第一个回调函数返回值为true的值，回调函数的输入参数为(回调函数，当前位置，此数组)
数组的findIndex()方法与find()类似，返回符合条件值的下标，find和findIndex都可以接受第二个参数绑定回调函数的this对象
```
var a = [1,2,3,4];
a.find((value,index,arr)=>{if (value==2) return true;});//2
a.findIndex((value,index,arr)=>{if (value==2) return true;});//1
```

数组的fill()方法，可以用于数组的初始化
```
var a = [1,2,3];
a.fill(7,0,2);
a;//[7,7,3]
```
要注意fill()函数是浅拷贝，如果初始化的是一个对象，则会将对象的地址作为初始化的值
```
var a = new Array(3).fill([]);
a[0].push(5);
a;//[[5],[5],[5]]
```

#####数组的空位
ES6中新定义的方法如find，findIndex，keys，values，entries会将空位视为undefine而且不跳过，而map会跳过空位

#### 对象
ES6中允许直接写变量再对象中，以变量名作为属性名，变量值作为属性值如

```
function f(x,y){
	return {x,y};
}
//等同于
function f(x,y){
	return {x:x,y:y};
```
同样的，方法也可以简写
```
{
method(){doSomething;}
}
//等同于
{
method: function(){doSomething;}
}
```
简介写法的属性名总是字符串，而字符串属性名与非字符串调用时相同
```
{
	'name': 'name'
}
//等同于
{
	name: 'name'
}
```
当一个方法时Generator函数，前面需要加上*，如
```
const obj = {
	* m(){
		yield 'hello world';
	}
};
```
再ES6中属性的定义有两种方法，如
```javascript
{
	name: 'name',
	['na'+'me']: 'name'
}
//方括号里面也可以是变量
prokey = 'name'
{
	[prokey]: 'name'
}
```
在ES5中只能用标识符定义属性，不能使用表达式定于属性和方法

```javascript
const obj = {
    name: 'nnn',//ES5 ES6
    ['name1']: 'mmm'//ES5 ERROR
}
```



属性名表达式和简洁表达式不能同时使用

```javascript
const key = 'foo';
const baz = {[key]};//error
```
要注意的是，属性名如果是一个对象，那么对象名将会变为[object Object]
```javascript
const keyA = {a:1};
const myObj = {
	[keyA]:'valueA'
}
myObj["[object Object]"];//valueA
```
方法的name属性，方法通过name属性获取方法名
```javascript
const person = {
	say(){
		console.log('hello');
	}
}
person.say.name;'say'
```
对于取值函数和村值函数，方法名不在属性上面，而在方法的描述对象的get和set属性上

```javascript
const obj = {
    set name(s){_name = s;}
    get name(){return _name;}
}；
const descriptor = Object.getOwnPropertyDesriptor(obj,'name');//获取属性额属性
descriptor.get.name;//'get name'
descriptor.set.name;//'set name'
```

有两种特殊情况，对于Function类创建的函数，返回的name为anonymous，对于bind绑定的函数，返回‘bound ’加原来的函数名

```javascript
(new Function()).name;//'anonymous'
var funcname(){};
funcname.bind(obj).name;//'bound funcname'
```

Object.is()
ES5中有两种比较方法，&eq;&eq;和严格相等&eq;&eq;&eq;  ，其中&eq;&eq;会自动转换数据类型，&eq;&eq;&eq;NaN不等于自身，Object.is()行为与三个等于号基本相同，用来比较两个值是否严格相等。其中+0不等于-0，且NaN等于自身

Object.assign()
assign可以将source对象的可枚举属性赋值到目标对象

要注意如果后面有与前面同名的属性名则会覆盖前面的属性，如果输入的是一个对象作为参数则会返回该参数，不是对象则会先转换成对象，无法转换时会报错。但对源对象处理方式有所不同，当不是对象时会将其转换为对象，无法转换时会跳过

```javascript
const target = {a:1}
const source1 = {b:2}
const source2 = {c:3}
Object.assign(target,source1,source2);
//target{a:1,b:2,c:3}
```

assign的拷贝只拷贝原对象自身的属性，而不拷贝继承属性，也不拷贝不可枚举的属性。像字符串这样可枚举属性会转换为字符数组的形式的对象

```javascript
const s = 'abc';
Object.assign({},s);//{0:'a',1:'b',3:'c'}
```

当遇到同名属性，assign会进行属性的替换

当处理数组时，assign会把数组中的元素视为属性名为0，1，2...的对象

```
Object.assign([1,2,3],[4,5]);//[4,5,3]
```

当复制的值时取值函数，会在取值之后再赋值

##### 属性的遍历

for...in...：遍历对象自身的和继承的可遍历属性，不包括symbol属性

Object.keys(obj)：返回对象自身的不含继承的所有可枚举属性（数组形式）

Object.getOwnPropertyNames(obj)：返回对象自身的包括不可枚举的属性，不包含symbol（数组形式）

Object.getOwnPropertySymbols(obj)：返回对象自身的所有的symbol属性（数组形式）

Reflect.ownKeys(obj)：返回对象自身的所有属性，包括不可枚举的和symbol属性（数组形式）

###### 遍历顺序

- 首先遍历所有数值键，按照数值升序排列
- 其次遍历所有字符串键，按照加入时间升序排列
- 最后遍历所有 Symbol 键，按照加入时间升序排列

##### super

ES6中提供了super关键字获取原型对象

```javascript
const proto = {
	foo:()=>{console.log('hello');}
}
const obj = {
    foo(){
        return super.foo();
    }
}
Object.setPrototypeOf(obj,proto);
obj.foo();//hello
```

目前JS引擎只能在简写方法定义的对象方法内使用super关键字，在属性内使用都会报错，且super调用的原型的函数所绑定的this是继承对象

##### 对象的解构赋值

```javascript
let {x,y,z,...rest} = {x:1,y:2,z:3,a:4,b:5};
rest;//{a:4,b:5}
let {x,y} = undefine;//ERROR
let {x,y} = null;//ERROR
```

当等号右侧不是一个对象的时候会将其转换为对象，如果不能转换就会报错

拓展运算符的解构赋值必须放在最后，且是浅拷贝

```javascript
let obj = {a:{b:1}};
let {...x} = obj;
obj.a.b = 100;
x.a.b;//100
```

拓展运算符的结构赋值不能拷贝原型对象的属性

```
let o = Object.create({a:1,b:2});//创建原型{a:1,b:2}
o.c = 3;
let {a,...rest} = o;
a;//1
rest;//{c:3}
```

拓展运算符可以合并对象属性

```javascript
let x = {a:1,b:2};
let y = {b:3,c:4};
let z = {...x,...y};//{a:1,b:3,c:4}
```

后面的对象如果跟前面的对象又同名属性会覆盖前面对象属性的值

对象的拓展运算符后面也可以加表达式，加null和undefine会忽略不会报错，如果又get函数这个函数会执行

```javascript
x = 2;
const obj = {
    ...(x>1?{a:1}:{}),
    ...null,
    ...{
        get c(){return 100;}
    },
    b:2
};//{a:1,b:2,c:100}
```





##### Object.assign的常见用途

为对象添加属性

```javascript
class Point {
    constructor(x,y){
        Object.assign(this,{x,y});
    }
}
```

对象属性的描述对象，对象中的每个属性都有一个描述对象Descriptor，用来控制该属性的行为

```javascript
const obj = {foo:'bar'};
Object.getOwnPropertyDescriptor(obj,'foo');
//{value:'bar',writable:true,enumerable:true,configurable:true}
```

其中enumerable称为可枚举性，这个属性设置的初衷是让for...in...跳过这个属性，目前有四个操作会忽略不可枚举的属性：

- for...in...：遍历

- Object.keys()：获取对象中的属性

- JSON.stringify()：只对对象中的可枚举属性进行字符串化

- Object.assign()：对象的拷贝会忽略对象中不可枚举的属性

  而在这四个操作中，只有for...in...会返回继承的属性，其它三个都会忽略继承的属性

且在ES6中规定，所有class的原型的方法都是不可枚举的

```javascript
Object.getOwnPropertyDescriptor(class {foo(){}}.prototype,'foo').enumerable;//false
```

因为for...in...会把对象中的继承属性遍历，很多时候这会让问题复杂化，所以可以用Object,keys()代替for遍历

##### Object.getOwnPropertyDescriptors()

ES5中的Object.getOwnPropertyDescriptor()会获得某个对象属性的描述对象，ES2017中引入的Object.getOwnPropertyDescriptors()方法可以返回对象所有非继承属性的描述对象

```javascript
const obj = {
    foo: 123,
    get bar(){
		return 'abc';
	}
};
Object.getOwnPropertyDescriptors(obj);
//{ foo:
//    { value: 123,
//      writable: true,
//      enumerable: true,
//      configurable: true },
//   bar:
//    { get: [Function: get bar],
//      set: undefined,
//      enumerable: true,
//      configurable: true }
//}
```

通过getOwnPropertyDescriptors可以实现assign无法实现的对set和get函数的拷贝

```javascript
const const source = {
    foo: 123,
    get bar(){
		return 'abc';
	},
	set bar(value){
        foo = value;
	}
};
const target = {};
Object.assign(target,source);//target.bar:{set:undefine,get:undefine}
Object.defineProperties(target, Object.getOwnPropertyDescriptors(source));//target.bar:{get:[Function: get bar],...}
```

Object.getOwnProperty















#### Symbol

Symbol是ES6中引入的新的一种数据类型，表示独一无二的值，这是javascript中的第六种数据类型，前物种是undefine，null，布尔值（Boolean），数值型（Number），字符串（String），对象（Object）

对Symbol的定义通过Symbol函数进行，如

```javascript
let s = Symbol();
typeof s;//"symbol"
```

Symbol函数可以传入一个参数，用于对symbol对象进行描述，如果是传入的是对象，则会调用对象的toString方法，要注意的是，传入参数只是对这个对象的描述，同样描述的symbol对象是不同的

```
let s1 = Symbol('foo');
let s2 = Symbol('foo');
s1 === s2;//false
```

##### 用Symbol作为属性名

使用symbol作为属性名可以防止属性被同名属性覆盖，要注意symbol作为属性名不能使用点运算符

```javascript
let s = Symbol();
let a = {};
a[s] = 'hello';
//或
let a = {
    [s] = 'hello'
};
//或
let a = {};
Object.defineProperty(a,s,{value: 'hello'});
a[s];//hello
```

使用点运算符会把symbol作为字符串来处理

```javascript
let s = Symbol();
const a = {};
a.s = 'hello';
a[s];//undefine
a['s'];//'hello'
```

在对象内部使用symbol定义属性的时候也要用方括号括住

```javascript
let s = Symbol();
let a = {
    [s]： function(){
        console.log('hello');
    }
}
```

##### Symbol属性的遍历

当symbol作为属性名的时候，for...in(of)...，Object.key()，Object.getOwnPropertyNames()，JSON.stringify()都不会返回属性，获取以symbol作为属性名的属性可以使用Object.getOwnPropertySymbols()

#### Class

通过class定义类

class定义更类似一个语法糖

```javascript
class ClassName {
    contructor(name, ...props) {
        super(...props);//super()是父类的构造函数，如果有构造函数则必须要super()，没有构造函数会自动传入全部props
        this.name = name;
    }
    static create() {
        doSomething;
    }
    getName() {
        super.do();//在简单命名的方法内可以用super代替Object.getPrototypeOf(this)
    }
}
```

继承

```javascript
class SuperClass {}
class SubClass extends SuperClass {
    contructor(){}
}
//相当于
function SuperClass(){}
class SubClass extends SuperClass {
    contructor(){}
}
```

ES5中的继承方法

```
function Person(name) {
    this.name = name;
}
Person.prototype.say = function(){console.log('hello')};
function Friend(name) {
	Person.call(this, name);
    this.sayName = function(){
        console.log(this.name)
    }
}
Friend.prototype = Object.create(Person.prototype, {
    constructor: {
        value: Friend,
        writable: true,
        enumerable: true,
        configurable: true
    }
})

```

