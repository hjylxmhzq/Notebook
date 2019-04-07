### Javascript六种继承方法

#### 原型链继承

```javascript
function Parent() {
    this.name = ['kevin'];
}
Parent.prototype.getName = function() {
    console.log(this.name);
}
function Child() {}
Child.prototype = new Parent();
let child1 = new Child();
let child2 = new Child();
child1.name;//['kevin']
child1.name.push('john');
child2.name;//['kevin','john']
```

原型链继承的问题在于不能给原型构造函数传入参数，且创建子类的实例时采用复制的方法会造成共享继承

#### 构造函数继承

```javascript
function Parent() {
    this.name = ['kevin'];
}
function Child() {
    Parent.call(this);
}
let child1 = new Child();
let child2 = new Child();
child1.name.push('john');
child1.name;//['kevin','john']
child2.name;//['kevin']
```

通过构造函数继承可以向原型传参，且创建属性不会被共享

#### 原型链和构造函数组合继承

```javascript
function Parent(name) {
    this.name = ['kevin'];
    this.name.push(name);
}
function Child(name,age) {
	Parent.call(this,name);
	this.age = age;
}
Child.prototype = new Parent();
Child.prototype.constructor = Child;
let child1 = Child('john',18);
child1.name;//['kevin','john'];
child1.age;//18
```

#### 原型式继承

```javascript
function createObject(o){
    let F = function(){};
    F.prototype = o;
    return new F();
}
```

原型式继承同样有继承属性共享的问题

#### 寄生式继承

```javascript
function Parent() {
    this.name = 'kevin';
}
function createObject(Parent){
    let child = Object.create(Parent);
    child.sayHello(){console.log('hello');}
    return child;
}
```

