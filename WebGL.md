### WebGL

WebGL是OpenGL ES在浏览器canvas中的实现

#### 创建步骤

##### 创建canvas

```html
<body onload="main()">
  <canvas id="glcanvas" width="640" height="480">
    Your browser doesn't appear to support the HTML5 <code>&lt;canvas&gt;</code> element.
  </canvas>
</body>
```

##### 获取canvas上下文引用

```javascript
function main(){
	const canvas = document.getElementById('glcanvas');
    const gl = canvas.getContext('webgl');
    if (!gl){
        alert('your browser may not support WebGL');
    }
}
```

##### 清除颜色

```javascript
gl.clearColor(0.0,0.0,0.0,1.0);//rgba
gl.clear(gl.COLOR_BUFFER_BIT);//用确定的颜色清除缓存
```

##### 创建2D内容

WebGL中包含两种着色器，分别为顶点着色器和片段着色器，其中顶点着色器用以计算顶点坐标，片段着色器用以确定每个像素点的颜色