## React简要实现

>相比经典的使用DOM操作定义事件，React采用了状态 这一抽象概念，事件被绑定在状态上，状态改变便刷新界面。

首先是React的简要实现方法
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset='utf-8'>
  </head>
  <body>
    <div class='wrapper'></div>
    <script type='text/javascript'>
      class Component {
        constructor(props = {}){
          this.props = props;
        }
        setState(state = {}){
          this.state=state;
          var oldEl = this.el;
          this.el = this.renderDOM();
          if (this.onStateChange){
            this.onStateChange(oldEl,this.el);
          }
        }
        renderDOM(){
          this.el = createDOMFromString(this.render());
          this.el.addEventListener('click',this.onClick.bind(this),false);
          return this.el;
        }
      }
      
      var createDOMFromString = (domString)=>{
        var div = document.createElement('div');
        div.innerHTML = domString;
        return div;
      };
      
      var mount = (newComponent,wrapper)=>{
        wrapper.appendChild(newComponent.renderDOM());
        newComponent.onStateChange = (oldEl,newEl)=>{
          wrapper.insertBefore(newEl,oldEl);
          wrapper.removeChild(oldEl);
        }
      };
      
      class Button extends Component{
        constructor(props = {}){
          super(props);
          this.state = {color:'red'};
        }
        render(){
          return(
              `<div style='color:${this.state.color}'>${this.state.color}</div>`
            )
        }
        onClick(){
          var clr = this.state.color == 'red'? 'blue':'red'
          this.setState({color:clr});
        }
      }
      var div = document.querySelector('.wrapper');
      mount(new Button(),div);
    </script>
  </body>
</html>
```
>这段代码创建了一个Button，绑定了事件click，只要点击就能改变style中的color属性，这是怎么做到的呢

##### 首先定义一个Component类
这个类之后要定义
一个构造函数constructor(props = {})
两个方法setState(state = {});renderDOM()
两个属性this.el;this.state
**构造函数**的作用就是传入属性值props，因为React使用了jsx的方法，也就是在js中写html，这种方法有个好处就是可以在html中加入变量，像这样${props.var}，所以我们就把要使用到的变量存在props里了。
**setState**的作用是在每次状态变化时进行界面的更新，此时传入新的state，更新的时候就采用新的state了。
**renderDOM**这是我们用来实现jsx特性的工具，在Component类的子类中我们会自定义一个render函数，这个函数返回一个具有html代码的字符串，renderDOM的作用就是将这个字符串转换成html的节点树。在真正的React中将会将html代码转换为js对象，再依靠这个对象转换到页面内容。

##### 之后构建一个Button子类
这个子类中定义了三个函数，constructor，onClick，render
**constructor**将传入的props参数传递给父类的构造函数并且设置state
**render**返回一个html代码的字符串，实际上是返回jsx代码，在jsx中嵌入{}可以插入js代码，如<h>{props.color}</h>
**onClick**定义绑定在元素上的响应事件

##### 之后定义三个函数
mount，onStateChange和createDOMFromString
**createDOMFromString**实现类似jsx的功能
**mount**把它称作挂载，通过mount在html中插入新构造的元素，然后绑定当状态改变时需要定义的函数，也就是下面的onStateChange
**onStateChange**在mount中定义，在外部给类定义这个函数，可以让类知道当状态更新时要干什么，其实主要的工作就是在原位置插入新的元素然后删除旧的元素。

概括起来它们的关系就像这样：
定义onStateChange和onClick

render->*返回一段html代码*->renderDOM->*解析html代码生成DOM元素，并且通过定义的onClick绑定事件*->得到最终的DOM元素

setState->*传入新的state调用renderDOM获取新元素,调用定义的onStateChange函数*->onStateChange->通过已定义好的方法更新html中的结构
