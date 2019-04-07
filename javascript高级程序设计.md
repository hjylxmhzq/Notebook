## javascript高级程序设计

### 内存问题

变量离开执行环境时自动解除引用

手动接触引用可以设置引用变量为null使垃圾回收机制自动回收引用变量



### 引用类型

#### Object类型

定义object类型的多种方法

使用new：

```javascript
var obj = new Object();//等价于var obj = {};只保留包含默认属性和方法的变量
obj.name = 'name';
```

定义字面量：

```javascript
var obj = {name: 'name'};
```

#### Array类型

##### 排序方法

sort(), reverse()

```javascript
arr=arr.sort();//ascend
arr=arr.sort((val1,val2)=>{return val2-val1});//decend,回调函数返回正值说明val2应在val1前面
```

##### 操作方法

concat()：合并数组

```javascript
arr=arr.concat(val1,val2,...);
arr=arr,concat();//赋值数组（浅复制）
```

slice()：剪切数组

```javascript
arr.slice(start,end);
```

splice：删除插入数组

```javascript
arr.splice(start,length,inserted1,inserted2,...);
```

##### 位置方法

indexOf()：寻找第一个对应元素的位置

lastIndexOf()：寻找最后一个对应元素的位置

##### 迭代方法

every()：是否对每一项运行函数都返回true

filter()：返回由true项组成的数组

forEach()：对数组中每一项运行对应元素，不返回值

map()：同forEach()，但返回由结果组成的数组

some()：是否有一项运行的函数返回true

##### 缩小方法

reduce()：对每一项执行函数，输入参数包括了前一项运行函数返回的结果

```javascript
arr.reduce(function(prev,cur,index,arr){
    return prev + cur;
});//==sum(arr)
```

reduceRight()：方向于reduce()相反

#### Date类型

```javascript
var now = new Date();
var someday = new Date.parse("Nov 25, 2018");
//or
var someday = new Date("Nov 25, 2018");
```

Date.UTC()：通过年月日时分秒毫秒构建日期对象，构建时间基于UTC时间而非本地时间

```javascript
var someday = new Date(Date.UTC(2018,0,1,0,1,1));//2018-1-1,00:01:01，注意月份从0开始
```

使用Date()构造函数也会模仿Date.UTC()的行为，但构建的时间是基于本地时间，也即Date.UTC构建的时间为格林尼治时间，表示本地时间时需要转换，而Date构建的时间直接为本地时间

##### Date对象格式化方法

toLocaleString()：转换为本地的不含时区信息的时间信息字符串

toString()：转换为包含时区时间信息的字符串

以上两个方法表示的信息因浏览器而异，故使用价值不大

valueOf()：转换为日期的毫秒表示，故可以通过数学运算符来比较Date对象

##### 时间组件方法

getTime()：返回日期毫秒数，同valueOf()

setTime()：设置日期毫秒数

getFullYear()：取得四位数年份

getMonth()：获取月份，0开始

getDate()：获取月份中的天数，1-31

getDay()：获取星期几，0开始

getHours()：获取小时数，0开始，超过23增加天数

getMinutes()：获取分钟数，0开始，超过59增加小时数

getSeconds()：获取秒数，0开始，超过59增加分钟数

**其中月，星期，时，分，秒，毫秒都是从0开始** 

#### 正则表达式(RegExp)类型

有两种创建方法

```javascript
var regexp = / pattern / flags;//通过字面量创建
var regexp = new RegExp("pattern","flags");//通过构造函数创建
```

其中flags有

g：全局模式，应用于所有字符串而非匹配第一个猴立刻停止

i：忽略大小写模式

m：多行匹配模式

通过字面量和构造函数创建的正则对象时一样的，但是对于元字符再构造函数中的正则表达式字符串需要双重转义，即

```javascript
var a = /\\/;
var b = new RegExp("\\\\");
//a和b都表示在字符串中寻找一个\
```

在ES3中，字面量正则表达式会共享一个实例，而使用构造函数创建的正则表达式对象每次都会创建新的实例，在ES5中规定字面量和构造函数创建的正则对象都必须创建新的实例

##### RE实例属性

global：flags中的g，bool型

ignoreCase：flags中的i

lastIndex：int，表示搜索下一个匹配项开始的位置

multiline：flags中的m

source：返回一个字面量形式（即单次转义）的正则表达式字符串

##### RE实例方法

exec()：regexp.exec(str)找不到匹配项返回nill，找到匹配项则返回一个数组实例，数组中包含匹配的字符串，如果有捕获组也会包含在数组中，这个数字实例还包含两个额外的属性index和input，index表示匹配项在字符串中的位置，input表述输入的字符串，如果模式中没有全局模式那么每次匹配都是从头开始，如果使用全局模式那么每次匹配都是从上一次匹配之后的位置开始，但是IE中的行为有所不同，两种情况下都是以全局模式的方式进行

#### 面向对象程序设计

##### 属性特征

- Enumerable：是否能够通过for in循环，直接定义属性时默认为true
- Configurable：能否通过delete重新定义属性，能否修改属性特征，能否修改属性为访问器属性，如果定义为false则此属性不能再被修改
- Writable：是否能够修改属性的值
- Value：属性的数据值

##### defineProperty方法

修改属性默认特征可以采用defineProperty方法

```javascript
var person = {}
Object.defineProperty(person, "name", {
    writable: false,
    value: "Nicholas"
});
```

当修改name的值时，操作会被忽略，在严格模式下会抛出错误

##### 访问器属性

```javascript
book = {
    _year: 2004,
    edition: 1
};

Object.defineProperty(book, "year", {
    get: function(){
        return this._year;
    },
    set: function(value){
        this._year = value;
        this.edition+=1;
    }
})
```

##### 定义多个属性defineProperties

```javascript
book = {};

Object.defineProperties(book, {
    _year: {
        value: 2004
    },
    edition: {
        value: 1
    },
    year: {
        get: function(){return this._year},
        set: function(value){
            this._year = value;
            this.edition+=1;
        }
    }
});
```

##### 获取属性特征getOwnPropertyDescriptor

```javascript
descriptor = Object.getOwnPropertyDescriptor(book, "year");
console.log(descriptor);
//{ get: [Function: get],
//	set: [Function: set],
//	enumerable: false,
//	configurable: false }
```

##### 创建对象

###### 工厂模式

```javascript
function createPerson(name, age, job){
    var o = new Object();
    o.age = age;
    o.name = name;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    };
    return o;
}

var person1 = createPerson('john', 24, 'engineer');
var person2 = createPerson('Greg', 23, 'doctor);
```

###### 构造函数模式

```javascript
function Person(name, age, job){
    this.age = age;
    this.name = name;
    this.job = job;
    this.sayName = function(){
        alert(this.name);
    };
}

var person1 = new Person('john', 24, 'engineer');
var person2 = new Person('Greg', 23, 'doctor);
```

构造函数内没有显式创建新的对象，也没有返回值，最后person1和person2分别保存Person的不同实例，它们都有一个constructor属性，是一个指向构造函数Person的指针

当将构造函数当作普通函数来使用的时候，this指向全局变量（在浏览器中为window）

###### 原型模式

在构造函数中创建的变量不能在多个实例中共享，而原型模式可以实现变量共享

在创建函数时会根据一组特定的规则为函数创建一个prototype属性，这个属性指向函数的原型对象，在原型对象上有一个constructor属性指向原型对象所属的函数

```javascript
function Person(){}
Person.prototype.name = 'john';
Person.prototype.age = 29;
Person.prototype.job = 'Engineer';
Person.prototype.sayName = function(){
    alert(this.name);
};
var person1 = new Person();
var person2 = new Person();
alert(person1.sayName == person2.sayName);//true
```

在创建的实例中，部分实现包含了__proto\_\_属性，用于连接实例与构造函数的原型对象

通过isPrototypeOf方法可以判断一个原型对象是否时某个实例的原型

```javascript
Person.prototype.isPrototypeOf(person1);//true
```

ES5中新增了getPrototypeOf方法取得某实例的原型对象

```javascript
Object.getPrototypeOf(person1) == Person.prototype;//true
```

如果在实例中创建与原型属性同名的属性，则原型中的属性会被屏蔽，而使用实例中新创建的属性，即是将属性设置为null也不能使其重新指向原型中的对象，但可以使用delete完全删除实例中新建的对象

```javascript
var Person = function(){};
Person.prototype.age = 24;
var person1 = new Person();
person1.age = 25;
delete person1.age
alert(person1.age);//24
```

###### 判断属性在实例中还是原型中hasOwnProperty

```
var Person = function(){};
Person.prototype.age = 24;
var person1 = new Person();
person1.age = 25;
person1.hasOwnProperty("age");//true
delete person1.age
person1.hasOwnProperty("age");//false
```

###### 判断属性是否在实例或者原型中in

```javascript
var Person = function(){};
Person.prototype.age = 24;
var person1 = new Person();
person1.age = 25;
"age" in person1;//true
delete person1.age
"age" in person1;//false
```

可以根据hasOwnProperty和in的差异定义一个函数hasPrototypeProperty

```javascript
var hasPrototypeProperty(obj, att){
    return !obj.hasOwnProperty(att) && att in obj;
}
```

同样，使用for-in循环会遍历所用可以访问的在实例中或者原型中的属性，但是for-in不能遍历对象中的不可枚举属性（enumerable: false），根据规定，所用用户定义的属性都是可枚举的，故在对象中定义原型中不可枚举的属性时可以将属性变成可枚举的

##### 重设prototype对象

为了方便，可以重设整个prototype对象

```javascript
Person.prototype = {
    age: 24,
    name: 'john'
};
```

此时使用instanceof仍然可以正常返回结果，但prototype中的constructor指向不正确了，如果在prototype中的constructor很重要，那可以重新设置constructor的指向

```javascript
Person.prototype = {
	constructor: Person,
    age: 24,
    name: 'john'
};
```

##### 模仿块级作用域

通过匿名函数可以创建模块级的作用域

```javascript
(function(){
	var sum = 0;
    for (var i=0; i<100; i++){
        sum += i;
    }
    alert(sum);
})()
```

##### 创建私有变量

创建私有变量可以通过闭包的方式是的变量存在于作用域链中，函数中的参数，局部变量，函数内定义的其它函数都可以作为私有变量

```javascript
function Person(name){
    this.sayName = function(){
        console.log(name);
    }
    this.setName = function(value){
        name = value;
    }
}

var person = new Person('john');
person.sayName();
```

这种方法使用时需要用到构造函数，而在创建的实例中各变量独立非共享

可以通过闭包创建位于原型中的变量来共享私有变量

```javascript
(function(){
    var privateValue = 0;
    myObject = function(){};
    myObject.prototype.getValue = function(){
        console.log(privateValue);
    }
    myObject.prototype.setValue = function(value){
        privateValue = value;
    }
})()
```

#### 客户端检测

##### 能力检测

即通过判断客户端中是否提供某一功能

###### 判断方法是否存在

```javascript
function getElement(id)
if (document.getElementById){
    return document.getElementById(id);
} else {
    return document.all[id]
}

function isSortable(obj){
    return !!obj.sort;
}
```

###### 更可靠的检测

判断某个对象的类型

```javascript
function isSortable(obj){
    return typeof obj.sort === 'function'; 
}
```

##### 浏览器的怪癖检测

在部分浏览器中存在一些“bug”，可以通过特殊的方法检测，例如在IE8之前，如果自定义的方法与原有不可枚举方法同名，那么将被视为不可枚举，而在ECMAScript中规定自定义属性默认为可枚举，检测如下

```javascript
function hasDontEnumQuirk(){
    var obj = {toString: function(){}};
    for (var prop in obj){
        if (prop === 'toString')
        	return false;
    }
    return true;
}
```

##### 用户代理检测

一般不推荐使用用户代理检测，因为客户端有可能隐瞒真实信息，且用户代理信息十分复杂

使用用户代理检测通过解析navigator.userAgent进行



#### DOM

##### 检测节点类型

通过nodeType属性确定节点类型，除了IE以外的浏览器可以通过比较常量来确定节点属性

```javascript
somenode.nodeType == Node.ELEMENT_NODE;
```

再所有浏览器中都可以使用数值来确定节点属性值

```javascript
somenode.nodeType == 1;//element node
```

##### 节点关系

每个节点都有childNodes属性，属性包含了一个NodeLIst对象，通过Array.prototype.slice()可以将NodeList对象转换为数组类型，获取NodeList中对象可以用下标的方法，也可以用item方法，NodeList是一个动态的对象，只反映操作时的节点状态，且含有一个length属性

```javascript
var firstChild = someNode.childNodes[0];
var secondChild = someNode.childNodes.item(1);
```

其它的节点关系属性还有parentNode，previousSibling，nextSibling，firstChild，lastChild

###### 判断是否有子节点

通过childNodes的length属性判断，或者使用hasChildNodes方法来判断每个节点都有一个ownerDocument属性，指向这个整个文档的节点

##### 添加节点

节点属性是只读的，所以要使用其提供的方法来操作节点

###### appendChild

在childNodes末尾添加一个节点，返回插入的节点

```javascript
var returnNode = parentNode.appendChild(someNode);
```

###### insertBefore

在某个节点前面添加节点，接受两个参数（新节点，后一个节点），如果第二个参数为null，则行为与appendChild相同，返回插入的节点

```javascript
var returnNode = parentNode.insertBefore(newNode, parentNode.lastNode);
parentNode.insertBefore(newNode, null);
//equal to: parentNode.appendChild(newNode);
```

##### 替换节点

###### replaceChild

用新节点替换旧节点，返回被替换的节点

```javascript
var returnNode = parentNode.replaceChild(newNode, parentNode.lastChild);
```

##### 移除节点

###### removeChild

移除节点，返回移除的节点

```javascript
var lastChild = parentNode.removeChild(parent.lastChild);
```

##### 复制节点

###### cloneNode

cloneNode接受一个参数，表示是否执行深复制，如果传入true则复制在节点之下的整个节点树，否则只复制节点本身，复制后的节点不在节点树之中，需要使用appendChild，insertBefore，replaceNode插入到节点树之中

#### Document对象

##### doctype属性

Document对象中有doctype属性，在Chrome，Safari等之中表示文档声明<!DOCTYPE>，不作为节点树中节点出现，而在IE9+及以前版本和firefox中，将文档声明作为document中的第一个节点，也可访问doctype属性

在IE8-中，文档声明被解释为注释，并且被作为一个comment节点

##### documentElement属性

指向html节点

```javascript
document.documentElement == doucment.firstChild;//true
```

##### body节点

由于body节点使用较多，所以也存在与docuemnt属性中

##### title属性

包含着title中的文本信息

注意：修改title属性并不会修稿文档中title的内容

##### URL属性

即地址栏显示的URL

##### domain属性

只包含域名信息

**注意**：通过设置不同document中的domain属性，可以解决跨域访问的限制，但是当设置了domain之后不能再改回去，否则会出错

##### referrer属性

取得来源页面的信息

##### getElementById和getElementsByTagName

通过id和tagname来获取节点

getElementByTagName()返回的是HTMLCollection对象，其与NodeList对象很相似，也可以通过下标和item方法来取得节点

```javascript
var divList = getElementsByTagName('myDiv');
var firstEle = divList[0];
firstEle = divList.item(0);
```

通过节点属性可以获得元素中的属性

```javascript
first.src;//获取第一个div节点中的src属性
```

通过name属性对对象进行了命名的元素可以直接通过名称进行访问，或者通过namedItem方法进行检索

```html
<div src="a.gif" name="mypic"></div>
<div src="b.gif"></div>
```

```javascript
var divList = getElementsByTagName('div');
var myPic = divList['mypic'];
myPic = divList.namedItem('mypic');
```

##### document对象中的特殊集合

这些集合都是HTMLCollection对象，包括

- anchors：所有a元素
- links：所有包含href的a元素
- forms：所有form元素
- images所有img元素

##### 文档写入方法

- write

通过write方法直接再文档里写入

```javascript
document.write("<strong>" + (new Date()).toString() + "</strong>");
```

当再write中包含script时要注意转义，如果不进行转义的话/script标签会被认为是外部script的结束标记

```javascript
<script>
	document.write('<script src="/test.js">'+'<\/script>');
</script>
```

在页面加载完成后使用write会重写整个页面

```javascript
window.onload = function(){
    document.write('reload all page!');
};
```

- writeln

相比write会在末尾添加\n换行符

#### 事件

捕获阶段和冒泡阶段，目标阶段都是再冒泡阶段

注：IE中的事件传入函数没有event参数，event存储与window对象中，且不能再捕获阶段添加事件

IE：attachEvent(type, ()=>{})

other：addEventListener(type, ()=>{}, true[/false])

最后参数true表示在捕获节点添加事件，false表示在冒泡阶段添加事件















