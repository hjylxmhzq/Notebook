### Node 笔记

#### 模块加载过程

##### 模块分类

- 核心模块
- 文件模块
- 自定义模块

##### 步骤

- 路径分析
- 文件定位
- 编译执行

对自定义模块的查找会随目录树向上检索

自定义模块的导入是最慢的

文件模块可以省略拓展名，自动按照.js .node .json的顺序补全



#### 包描述文件与NPM

#### package.json文件

package.json为包描述文件，在NPM中实际需要的字段为name,version,description,keywords,repositories,author,bin,main,scripts,engines,dependencies,devDependencies

- author：包作者
- bin：可以将脚本添加到执行路径中
- main：引入包时作为模块的入口，如果没有，则检查包目录下的index.js，index.node，index.json
- dependencies：安装包时用到的依赖
- devDepandencies：只在开发时用到的依赖

#### NPM常用功能

##### 版本号

```javascript
npm -v
```

##### 安装依赖包

- 普通安装：npm install <包名>
- 全局安装：npm install <包名> -g
- 本地安装：npm install <url地址/tarball文件/文件夹> 只要文件中或文件夹中包含package.json
- 从非官方源安装：npm install --registry=url
- 指定默认安装源：npm config set registry url

##### package.json中的scripts字段

通过scripts字段中的属性为npm提供钩子

如

```javascript
"scripts":{
    "preinstall":"preinstall.js",
    "install":"install.js"
}
```

以交互式的方式初始化package.json文件，通过npm init命令

##### 前后端共用模块

对于使用AMD/CMD规范(前端场景)的模块使用define方式定义，即

```javascript
//define(factory) factory即实际的代码内容
define(function(){
    var hello = function(){};
    return hello;
})
```

使用commonjs规范(Node场景)定义的模块

```javascript
module.exports = (funtion(){
    var hello = function(){};
    return hello;
})()
```

创建一个兼容多种规范额模块

```javascript
(function(name, definition){
    var hasDefine = typeof define === 'function';
    var hasExports = typeof module !== 'undefined' && module.exports;
    if (hasDefine) {
        define(definition);
    }
    else if (hasExports) {
        module.exports = definiton();
    }
    else {
        this[name] = definition();//对于浏览器直接挂载到this(window)对象中
    }
})('hello',function(){
    var hello = function(){};
    return hello;
});
```

#### Node中的非阻塞异步I/O实现

Node在windows和\*nix系统中采用libuv实现系统上的兼容性，在windows中Node采用IOCP机制，在\*nix中采用多线程机制，两种机制的原理都是通过独立在主线程外的I/O线程进行轮询或阻塞I/O实现总体上的非阻塞异步I/O

##### 观察者

Node中的观察者是文件I/O，网络请求等，在浏览器中是鼠标事件等，每种事件都有对应的观察者，观察者将事件和回调函数联系起来

##### 事件循环

在Node启动时会创建一个循环，用以从观察者处取得事件和对应的回调函数，执行相应的回调函数后继续循环，直到没有事件时退出循环

##### 非I/O的异步API

主要又setTimeout，setInterval，setImmediate，process.nextTick

其中process.nextTick()和setImmediate()为立即执行的异步函数，但process.nextTick()优先级比setImmediate()高

#### 异步编程

##### 高阶函数

返回值为函数或者传入值为函数的函数

##### 偏函数

返回一个预置参数的函数的函数

如判断对象类型的函数

```javascript
var isType = function(type){
    return function(obj){
        return isString.call(obj) == '[object' + type + ']';
    }
}
var isSting = isType('String');
var isFunction = isType('Function');
```

##### 异常处理

在编写异步方法时，对异常捕获与顺序方法有所不同，一般约定传入的回调函数的第一各参数时错误信息

```javascript
var async = function(callback){
    try {
        //do
    }
    catch(err) {
        return callback(err);
    }
    callback(null, results);
}
```

注意不要将callback放入try内，否则如果callback出错容易混淆，所以将相应结果（err/results）传给回调函数即可，无需过多处理

##### 异步编程解决方案

- 事件发布/订阅模式

```javascript
emitter.on("event1",function(mesg){
    console.log(mesg);
});//事件发布
emitter.emit('event1','i am message');//事件发布
```

多异步之间的协作，通过构造哨兵变量和第三方函数实现

```javascript
var after = functione(times, callback) {
    var count = 0, results = {};
    return function(key, value) {
        results[key] = value;
        count++;
        if (count==times) {
            callback(results);
        }
    }
}
var done = after(3, render);
```

通过创建事件和侦听器实现多对一的收敛（完成多个并行的函数后再执行回调函数）和一对多的发散（实现一个侦听器的多次发布）

```javascript
var emitter = event.Emitter();
var emitter.on("done", done);
fs.readFile(template_path, 'utf8', function(err, template){
    emitter.emit('done', template);
})
db.query(sql,function(err, data){
    emitter.emit('done', data);
})
l10n.get(function(err, resources){
    emitter.emit('done', resources)
})
```

- Promise/Deferred模式



