### SVG

##### SVG 使用 XML 格式定义矢量图形

#### 结构

svg使用`<svg>`标签进行包裹，在最外层，所有标签必须关闭

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
</svg>
```

##### 嵌入方法

SVG 文件可通过以下标签嵌入 HTML 文档：`<embed>`、`<object> `或者 `<iframe>`。

SVG的代码可以直接嵌入到HTML页面中，或直接链接到SVG文件。

###### embed

允许使用脚本，但embed推荐html5中使用：

```html
<embed src="circle1.svg" type="image/svg+xml" />
```

###### object

主流浏览器都支持，支持html4

```html
<object data="circle1.svg" type="image/svg+xml"></object>
```

###### iframe

允许使用脚本，推荐html5使用：

```html
<iframe src="circle1.svg"></iframe>
```

###### 直接嵌入

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
   <circle cx="100" cy="50" r="40" stroke="black" stroke-width="2" fill="red" />
</svg>
```

##### 表示矩形

在svg标签中使用rect标签代表矩形，矩形的样式属性使用style表示，不同样式用分号分开

###### 样式

- fill：填充，属性值为颜色，例'rgb(0,0,0)'，'black'
- stroke：轮廓的样式，属性值为颜色，例'rgb(0,0,0)'
- stroke-width：轮廓宽度，例1

```html
<svg xmlns="http://www.w2.org/2000/svg" version="1.1">
	<rect width="300" height="100"
	style="fill:rgb(0,0,255);stroke:red;stroke-width:1"
	/>
</svg>
```

##### 表示圆形

使用circle标签表示

###### 样式

- cx，cy：圆点x，y坐标
- r：半径

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <circle cx="100" cy="50" r="40" stroke="black"
  stroke-width="2" fill="red"/>
</svg>
```

##### 表示椭圆

使用ellipse元素表示

###### 样式

- cx，cy：圆点坐标
- rx，ry：x，y轴方向上的半径

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <ellipse cx="300" cy="80" rx="100" ry="50"
  style="fill:yellow;stroke:purple;stroke-width:2"/>
</svg>
```

表示多个椭圆

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <ellipse cx="240" cy="100" rx="220" ry="30" style="fill:purple"/>
  <ellipse cx="220" cy="70" rx="190" ry="20" style="fill:lime"/>
  <ellipse cx="210" cy="45" rx="170" ry="15" style="fill:yellow"/>
</svg>
```

##### 表示直线

使用line表示

###### 样式

- x1，y1，x2，y2：起始点和终止点x，y坐标

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <line x1="0" y1="0" x2="200" y2="200"
  style="stroke:rgb(255,0,0);stroke-width:2"/>
</svg>
```

##### 表示多边形

使用polygon表示

###### 样式

- points：折现各个定点坐标，使用空格分开各个顶点，使用逗号分开x和y坐标

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <polygon points="200,10 250,190 160,210"
  style="fill:lime;stroke:purple;stroke-width:1"/>
</svg>
```

使用fill-rule可以选择填充颜色的规则：

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <polygon points="100,10 40,180 190,60 10,60 160,180"
  style="fill:lime;stroke:purple;stroke-width:5;fill-rule:evenodd;" />
</svg>
```

fill-rule的有效值有：nonzero | evenodd | inherit

nonzero：按该规则，要判断一个点是否在图形内，从该点作任意方向的一条射线，然后检测射线与图形路径的交点情况。从0开始计数，路径从左向右穿过射线则计数加1，从右向左穿过射线则计数减1。得出计数结果后，如果结果是0，则认为点在图形外部，否则认为在内部。

evenodd：按该规则，要判断一个点是否在图形内，从该点作任意方向的一条射线，然后检测射线与图形路径的交点的数量。如果结果是奇数则认为点在内部，是偶数则认为点在外部。

##### 表示折线

使用polyline表示

###### 样式

- points：折线各个顶点坐标，使用空格分开各个顶点，使用逗号分开x和y坐标

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <polyline points="0,40 40,40 40,80 80,80 80,120 120,120 120,160" style="fill:white;stroke:red;stroke-width:4" />
</svg>
```

##### 表示路径

使用path表示

###### 样式

- d：表示路径点，其中有一下参数
  - M = moveto
  - L = lineto
  - H = horizontal lineto
  - V = vertical lineto
  - C = curveto
  - S = smooth curveto
  - Q = quadratic Bézier curve
  - T = smooth quadratic Bézier curveto
  - A = elliptical Arc
  - Z = closepath

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
    <path d="M150 0 L75 200 L225 200 Z" />
</svg>
```

##### 表示文本

使用text表示

###### 样式

- x，y：位置坐标

text内可包含textPath标签用来创建路径上的文字，包含tspan标签用来创建换行文本，包含a标签用来创建超链接文本

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <text x="0" y="15" fill="red">I love SVG</text>
</svg>
```

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1"
xmlns:xlink="http://www.w3.org/1999/xlink">
   <defs>
    <path id="path1" d="M75,20 a1,1 0 0,0 100,0" />
  </defs>
  <text x="10" y="100" style="fill:red;">
    <textPath xlink:href="#path1">I love SVG I love SVG</textPath>
  </text>
</svg>
```

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <text x="10" y="20" style="fill:red;">Several lines:
    <tspan x="10" y="45">First line</tspan>
    <tspan x="10" y="70">Second line</tspan>
  </text>
</svg>
```

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1"
xmlns:xlink="http://www.w3.org/1999/xlink">
  <a xlink:href="http://www.w3schools.com/svg/" target="_blank">
    <text x="0" y="15" fill="red">I love SVG</text>
  </a>
</svg>
```

