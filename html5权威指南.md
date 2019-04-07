html5权威指南

noscript用来在不支持js时显示内容

如果需要在不支持js时进行跳转，需要在nscript中加入meta：

```html
<noscript>
	<meta http-equiv="refresh" content="0; http://www.xxx.com" />
</noscript>
```

