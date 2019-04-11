### Blob和FileReader

html5支持Blob对象，用以支持二进制文件，

创建一个blob对象有两种方式，一个是传入一个数组，另一种是对已有blob对象进行切片

```javascript
let newBlob = new Blob([JSON.stringify({a:1})], { type: 'text/json' });
let newBlobSplit = new Blob.slice(0,1,newBlob);
```

blob对象可以通过FileReader来读取，通过FileReader可以将blob读取为base64的DataURL，二进制字符串，文本

```javascript
let reader = new FileReader();
reader.onload = function(fr) {
    console.log(fr.target.result)
};
reader.readAsDataURL(blob);//读取为dataurl
reader.readAsBinaryString(blob);//读取为二进制字符串
reader.readAsText(blob, 'UTF-8');//读取为文本
```

FileReader同样可以读取FileList中的file，对读取本地文件提供支持

假设有html

```html
<input type="file" onchange="handleChange(this.files)" />
```

有js

```javascript
let reader = new FileReader();
reader.onload = function(fr) {
    console.log(fr.target.result)
}
function handleChange(files) {
    reader.readAsText(files[0], 'UTF-8');
}
```

#### URL.createObjectURL()

用window.URL.createObjectURl()同样可以创建DataURL数据

```javascript
let blobURL = window.URL.createObjectURL(blob);//从blob创建
let blobURL = window.URL.createObjectURl(files[0]);//从文件创建
```

