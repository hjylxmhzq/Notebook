CSS权威指南

CSS权重排序

1. 读者的重要声明
2. 创作人员的重要声明
3. 创作人员的正常声明
4. 读者的正常声明
5. 用户代理声明



当权重相同时按特殊性排序

当特殊性相同时越在之后的声明权重越大

a-b-c:

a：id选择器数量

b：其它属性和伪类数量

c：元素名和伪元素数量



锚的伪元素的顺序

推荐顺序：

LVHA

1. a:link
2. a:visited
3. a:hover
4. a:active



字体

serif：有衬线

sans-serif：无衬线

monospace：宽度一致

cursive：模仿手写体

fantasy：无显著特征



italic和oblique字体

white-space：

pre-line：保留换行符，合并空格

pre-wrap：pre-line的升级版，保留空格和换行

normal：合并换行和空格

pre：保留换行和空格，但是不允许自动换行



再style标签中使用media属性

\<style media="screen AND (min-width:500px)">

some styles

\</style>

在media属性中的判断条件有

width/device-width，height/device-height指定窗口/整个设备宽度高度

