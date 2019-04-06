### Node.js实战

#### 创建web服务器

```javascript
var http = require('http');
var server = http.createServer();
server.on('request',function(req,res){
    res.writeHead(200,{'Content-Type':'text/plain'});
    res.end('Hello World\n');
})
server.listen(3000);
console.log('server started')
```

#### 实现简要的web服务器

```javascript
var http = require('http');
var mine = require('mime');
var fs = require('fs');
var path = require('path');
var cache = {};

function send404(res) {
    res.writeHead(404,{'Content-Type':'text/plain'});
    res.write('Error 404: resource not found.');
    res.end()
}

function sendFile(res, filePath, fileContent) {
    res.writeHead(200, {'Content-Type': mime.lookup(path.basename(filePath))});
    res.write(fileContent);
    res.end();
}

function serveStatic(res, cache, absPath ) {
    if (cache[absPath]) {
        sendFile(res, absPath, cache[absPath]);
    }
    else {
        fs.exists(absPath, function(exists){
            if (exists) {
                fs.readFile(absPath, function(err, data){
                    if (err) {
                        send404(res);
                    }
                    else {
                        cache[absPath] = data;
                        sendFile(res, absPath, data);
                    }
                })
            } else {
                send404(res);
            }
        })
    }
}

var server = http.createServer(function(req, res) {
    var filePath = false;
    if (req.url == '/') {
        filePath = 'public/index.html';
    } else {
        filePath = 'public' + req.url;
    }
    var absPath = './' + filePath;
    serveStatic(res, cache, absPath);
});

server.listen(3000, function() {
    console.log('server listening on port 3000.')
})
```

其逻辑主要如下：

1. 创建返回404的函数，其中包含响应头和响应内容
2. 创建返回文件内容的函数，其中包含响应头和响应内容，响应头中的mime信息通过mime模块根据给定的文件路径识别
3. 创建serveStatic，根据url寻找对应的文件，如果文件已经存在缓存中则从缓存中获取文件内容，不在则判断文件是否存在，不存在则返回404，存在则读取文件并返回文件内容，将文件内容添加进缓存中

#### Node编程基础

##### 模块的使用

模块可以是文件也可以是文件夹，默认以文件夹内的index.js文件作为默认模块出口

模块的到处可以用module.exports或exports

**注意**：require使用同步I/O，故可能造造成阻塞问题，最好只在程序初始化时使用

到处模块中对象的方法，使用exports，exports上的属性可以时任意类型的对象

```javascript
//test.js
exports.caToUs = function(ca) {
    return ca * 2;
}
```

引用对象的方法，使用require，可以省略文件后缀

```javascript
var v = require('./test')；
vv = v.caToUs(ca);
```

##### 用回调处理一次性事件

通过函数的嵌套回调可以实现一次性事件

通过创建中间函数可以减少函数嵌套

##### 用事件发射器处理重复性事件

通过on方法响应事件

通过以下方法实现一个echo服务器

```javascript
var net == require('net');
var server = net.createServer(function(socket) {
    socket.on('data', function(data) {
        socket.write(data);
    });
});
server.listen(8888);
```

通过once方法实现只响应第一次的数据

```javascript
var net == require('net');
var server = net.createServer(function(socket) {
    socket.once('data', function(data) {
        socket.write(data);
    });
});
server.listen(8888);
```

通过EventEmitter创建自定义事件发射器

```javascript
var EventEmitter = require('events').EventEmitter;
var channel = new EventEmitter();
channel.on('hello', function(str) {
    console.log(str);
})
channel.emit('hello','hello world!');
```

通过事件方式实现错误处理

```javascript
var EventEmitter = require('events').EventEmitter;
var myEmitter = new EventEmitter();
myEmitter.on('error', function(err) {
    console.log('ERROE: ' + err.message);
});
myEmitter.emit('error', new Error('Something is wrong.'))
```

通过setMaxListeners来设置在监听器数量大于多少时发出警告（默认为10个）

```javascript
channel.setMaxListener(50);
```

##### 通过事件监视器实现对文件的监视

首先创建一个自己的watcher类，继承自事件发射器

在./watch目录中的文件会自动移动至./done

```javascript
var EventEmitter = require('events').EventEmitter, util = require('util');
function Watcher(watchDir, processDir) {
    this.watchDir = watchDir;
    this.processDir = processDir;
}
util.inherits(Watcher, EventEmitter);
var fs = require('fs');

Watcher.prototype.watch = function() {
    var watcher = this;
    fs.readdir(this.watchDir, function(err, files) {
        if (err) throw err;
        for (var index in files) {
            watcher.emit('process', files[index]);
        }
    });
}

Watcher.prototype.start = function() {
    watcher = this;
    fs.watchFile(this.watchDir, function() {
        watcher.watch();
    });
}

var watcher = new Watcher('./watch', './done');
watcher.on('process', function(file){
    var watchFile = this.watchDir + '/' + file;
    var processFile = this.processDir + '/' + file;
    console.log(watchFile + ' to ' + processFile);
    fs.rename(watchFile, processFile, function(err) {
        if (err) throw err;
    });
});
watcher.start();
```

其中util.inherits(Watcher, EventEmitter)等价于

Watcher.prototype = new EventEmitter();

##### 异步导致的问题

可能在异步函数调用之前变量就被改变了

```
function asyncfunc(callback) {
	setTimeout(callback, 200);
}
var color = 'blue';
asyncfunc(function() {
    console.log(color);
});
var color = 'green';
//green
```

通过闭包保存color的值

```javascript
function asyncfunc(callback) {
	setTimeout(callback, 200);
}
var color = 'blue';
(function(color) {}
	asyncfunc(function() {
    	console.log(color);
	});
})(color);
var color = 'green';
//blue
```

##### 异步逻辑顺序化

异步逻辑顺序化的概念也被称为流程控制，流程控制分为两类，分别为串行和并行

###### 通过嵌套回调函数实现串行

```javascript
setTimeout(function(){
	console.log('first');
    setTimeout(function(){
    	console.log('second');
    	setTimeout(function(){
            console.log('third');
    	},100);
    },500);
},1000);
```

###### 通过nimble实现串行

```javascript
var flow = require('nimble');
function func1(callback){
    setTimeout(function(){
        console.log('first');
        callback();
    },1000);
}

function func2(callback){
    setTimeout(function(){
        console.log('second');
        callback();
    },500);
}

function func3(callback){
    setTimeout(function(){
        console.log('third');
        callback();
    },100);
}

var series = [func1, func2, func3];
flow.series(series);
```

##### 实现并行流程控制

统计多个文件中的词出现的次数

```javascript
var fs = require('fs');
var completeTasks = 0;
var tasks = 0;
var wordCounts = {};
var filesDir = './text';

function checkIfComplete() {
    completeTasks++;
    if (completeTasks == tasks) {
        for (var index in wordCounts) {
            console.log(index + ': ' + wordCounts[index]);
        }
    }
}

function countWordsInText(text) {
    var words = text.toString().toLowerCase().split(/\W+/).sort();
    for (var index in words) {
        var word = words[index];
        wordCounts[word] = (wordCounts[word]) ? wordCounts[word]+1 : 1;
    }
}

function readFile(file) {
    fs.readFile(filesDir + '/' + file, function(err, data) {
        if (err) {
            completeTasks++;
            throw err;
        }
        countWordsInText(data);
        checkIfComplete();
    })
}

fs.readdir(filesDir, function(err, files) {
    if (err) throw err;
    tasks = files.length;
    for (var index in files) {
        var file = files[index];
        readFile(file);
    }
})
```

通过nimble进行混合流程控制

```javascript
var flow = require('nimble').flow;
var exec = require('child_process').exec;

function downloadNodeVersion(version, destination, callback) {
    var url = 'http://nodejs.org/dist/node-v/' + version + '.tar.gz';
    var filepath = destination + '/' + version + '.tgz';
    exec('curl ' + url + ' > ' + filepath, callback);
}

flow.series([
    function (callback) {
        flow.parallel([
            function (callback) {
                downloadNodeVersion('0.4.6', '/tmp', callback);
            },
            function (callback) {
            	downloadNodeVersion('0.4.7', '/tmp', callback);
            }
        ], callback);
    },
    function (callback) {
        exec(
        	'tar cvf node_distros.tar /tmp/0.4.6.tgz /tmp/0.4.7.tgz',
        	function(err, stdout, stderr) {
                console.log('All Done!');
                callback();
        	}
        );
    }
]);
```



#### Node中http服务器

通过引入http模块创建http服务器

```javascript
var http = require('http');
```

通过http模块中的createServer创建http服务器

```javascript
var server = http.createServer(function(request, response){
    //do something;
})
```

createServer接受一个函数作为参数，每次收到http请求后都会调用这个函数，函数接受两个参数，分别为请求对象和响应对象

Node会在回调函数触发之前解析请求http头，但不会在触发前解析请求体，调用回调函数时使用response.end()结束响应，否则会挂起直到请求超时或者一直处于打开状态

通过response.write()写入响应数据

```javascript
var server = http.createServer(function(request, response){
    response.write('hello world');
    response.end();
})
```

也可以将响应写在end()中

```javascript
var server = http.createServer(function(request, response){
    response.end('hello world');
})
```

最后通过listen方法启动服务器，填入监听端口作为参数

```javascript
server.listen(3000);
```

以上服务器默认以200作为返回http头的状态码

自定义http响应头可以使用setHeader()，getHeader()，removeHeader()

```javascript
var server = http.createServer(function(req, res){
    body = 'hello world';
    res.setHeader('Content-Type', 'text/plain');
    res.setHeader('Content-Length', body.length)
    res.end(body);
})
```

设置响应状态码可以直接给res.statusCode赋值

```javascript
var server = http.createServer(function(req, res){
    body = 'hello world';
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.setHeader('Content-Length', body.length)
    res.end(body);
})
```

**注意**：node在写入http响应体之后会刷新响应头，故statusCode和setHeader要放在res.write和res.end之前

##### 处理POST请求

```javascript
var http = require('http');
var server = http.createServer(function(req,res) {
    req.on('data', function(chunk) {
        console.log('parsed', chunk);
    });
    req.on('end', function() {
        console.log('done!');
        res.end();
    });
});
server.listen(3000);
```

其中chunk为Buffer对象（字节数组）

通过setEncoding可以将流编码设置为ascii或者utf8

```javascript
var http = require('http');
var server = http.createServer(function(req,res) {
    req.setEncoding('utf8');
    req.on('data', function(chunk) {
        console.log('parsed', chunk);
    });
    req.on('end', function() {
        console.log('done!');
        res.end();
    });
});
server.listen(3000);
```

通过res.method判断使用的方法，并组装完整的请求体，将每一次请求推入items数组中

```javascript
var http = require('http');
items = [];
var server = http.createServer(function(req,res) {
    switch (req.method) {
        case 'POST':
            req.setEncoding('utf8');
            var item = '';
            req.on('data', function(chunk) {
                item = item + chunk;
            });
            req.on('end', function() {
                items.push(item);
                console.log(items);
                res.end('OK\n');
            });
            break;
    }
});
server.listen(3000);
```

在GET请求中返回items数组中的内容

```javascript
var http = require('http');
items = [];
var server = http.createServer(function(req,res) {
    req.setEncoding('utf8');
    switch (req.method) {
        case 'POST':
            var item = '';
            req.on('data', function(chunk) {
                item = item + chunk;
            });
            req.on('end', function() {
                items.push(item);
                console.log(items);
                res.end('OK\n');
            });
            break;
        case 'GET':
            items.forEach((element, index) => {
                res.write(index + ') ' + element + '\n');
            });
            res.end();
            break;
    }
});
server.listen(3000);
```

通过设置Content-Length可以隐式禁用Node块编码，但Content-Length应使用字节长度而非字符长度，在有多字节字符时字符长度不等于字节长度，故使用Buffer.byteLength方法计算字节长度

```javascript
var http = require('http');
items = [];
var server = http.createServer(function(req,res) {
    req.setEncoding('utf8');
    switch (req.method) {
        case 'POST':
            var item = '';
            req.on('data', function(chunk) {
                item = item + chunk;
            });
            req.on('end', function() {
                items.push(item);
                console.log(items);
                res.end('OK\n');
            });
            break;
        case 'GET':
            var body = items.map((element, index) => {
                return index + ') ' + element + '\n';
            }).join('');
            res.setHeader('Content-Type', 'text/plain; charset: "utf-8"');
            res.setHeader('Content-Length', Buffer.byteLength(body));
            res.end(body);
            break;
    }
});
server.listen(3000);
```

实现DELETE和PUT请求

```javascript
var http = require('http');
var url = require('url');
items = [];
var server = http.createServer(function(req,res) {
    req.setEncoding('utf8');
    switch (req.method) {
        case 'POST':
            var item = '';
            req.on('data', function(chunk) {
                item = item + chunk;
            });
            req.on('end', function() {
                items.push(item);
                console.log(items);
                res.end('OK\n');
            });
            break;
        case 'GET':
            var body = items.map((element, index) => {
                return index + ') ' + element + '\n';
            }).join('');
            res.setHeader('Content-Type', 'text/plain; charset: "utf-8"');
            res.setHeader('Content-Length', Buffer.byteLength(body));
            res.end(body);
            break;
        case 'DELETE':
            var pathname = url.parse(req.url).pathname;
            var i = parseInt(pathname.slice(1), 10);
            if (isNaN(i)) {
                res.statusCode = 400;
                res.end('Invalid id\n');
            } else if (!items[i]) {
                res.statusCode = 404;
                res.end('Item not found\n');
            }
            else {
                res.statusCode = 200;
                items.splice(i,1);
                res.end('Item deleted\n')
            }
            break;
        case 'PUT':
            var pathname = url.parse(req.url).pathname.slice(1);
            var i = parseInt(pathname.split(' ')[0]);
            if (isNaN(i)) {
                res.statusCode = 400;
                res.end('Invalid id\n');
            } else if (!items[i]) {
                res.statusCode = 404;
                res.end('Item not found\n');
            } else {
                item = '';
                req.on('data', function(chunk) {
                    item = item + chunk;
                });
                req.on('end', function() {
                    items.splice(i, 1, item);
                    res.end('Item changed\n');
                });
            }
    }
});
server.listen(3000);
```

##### 用Node实现静态文件服务

在Node中__dirname代表脚本文件所在目录，故在不同程序中这个变量有不同的值

```javascript
var fs = require('fs');
var parse = require('url').parse;
var join = require('path').join;
var http = require('http')

var root = __dirname

var server = http.createServer(function(req, res) {
    var pathname = parse(req.url).pathname;
    var filepath = join(root, pathname);
})

server.listen(3000);
```

通过fs.createReadStream实现文件的流式传输

```javascript
var fs = require('fs');
var parse = require('url').parse;
var join = require('path').join;
var http = require('http')

var root = __dirname

var server = http.createServer(function(req, res) {
    var pathname = parse(req.url).pathname;
    var filepath = join(root, pathname);
    var stream = fs.createReadStream(filepath);
    stream.on('data', function(chunk) {
        res.write(chunk);
    })
    stream.on('end', function() {
        res.end();
    })
})

server.listen(3000);
```

使用管道方法实现文件的传输

res.end()会在pipe结束后自动调用

```javascript
var fs = require('fs');
var parse = require('url').parse;
var join = require('path').join;
var http = require('http')

var root = __dirname

var server = http.createServer(function(req, res) {
    var pathname = parse(req.url).pathname;
    var filepath = join(root, pathname);
    var stream = fs.createReadStream(filepath);
    stream.pipe(res);
})

server.listen(3000);
```

通过对stream注册error事件，可以避免读取文件出错时程序直接抛出错误

```javascript
var fs = require('fs');
var parse = require('url').parse;
var join = require('path').join;
var http = require('http')

var root = __dirname

var server = http.createServer(function(req, res) {
    var pathname = parse(req.url).pathname;
    var filepath = join(root, pathname);
    var stream = fs.createReadStream(filepath);
    stream.on('error', function() {
        res.statusCode = 400;
        res.end('Server Error.\n');
    })
    stream.pipe(res);
})

server.listen(3000);
```

通过fs.stat()可以检查文件状态，若文件不存在，则err.code值为‘ENOENT’，文件大小可通过stat.size获得

```javascript
var fs = require('fs');
var parse = require('url').parse;
var join = require('path').join;
var http = require('http')

var root = __dirname

var server = http.createServer(function(req, res) {
    var pathname = parse(req.url).pathname;
    var filepath = join(root, pathname);
    fs.stat(filepath, function (err, stat) {
        if (err) {
            if (err.code == 'ENOENT') {
                res.statusCode = 404;
                res.end('Not Found.\n');
            } else {
                res.statusCode = 500;
                res.end('Server Error.\n');
            }
        } else {
            var stream = fs.createReadStream(filepath);
            stream.on('error', function() {
                res.statusCode = 400;
                res.end('Server Error.\n');
            });
            res.setHeader('Content-Length', stat.size);
            stream.pipe(res);
        }
    })
})

server.listen(3000);
```

使用querystring模块对表单数据进行解析

```javascript
var http = require('http');
var qs = require('querystring');

var item = '';
var getHtml = function(item) {
    return '<html><head><title>test</title></head>'
    + '<body>'
    + '<p>'
    + item
    + '</p>'
    + '<form method="post" action="/">'
    + '<p><input type="text" name="item"></p>'
    + '<p><input type="submit" value="show"></p>'
    + '</form>'
    + '</body'
    + '</html>';
}

var server = http.createServer(function(req, res) {
    switch (req.method) {
        case 'GET':
            var html = getHtml('')
            res.setHeader('Content-Type', 'text/html');
            res.setHeader('Content-Length', Buffer.byteLength(html));
            res.end(html);
            break
        case 'POST':
            var d = '';
            req.setEncoding('utf8');
            req.on('data', function(chunk) {
                d += chunk;
            })
            req.on('end', function() {
                var obj = qs.parse(d);
                item = obj.item
                var html = getHtml(item);
                res.setHeader('Content-Type', 'text/html');
                res.setHeader('Content-Length', Buffer.byteLength(html));
                res.end(html);
            })
            break
    }
})

server.listen(3000);
```

使用formidable处理上传的文件

```javascript
var http = require('http');
var qs = require('querystring');
var formidable = require('formidable');
var root = __dirname

var getHtml = function() {
    return '<html><head><title>test</title></head>'
    + '<body>'
    + '<form enctype="multipart/form-data" method="post" action="/">'
    + '<p><input type="text" name="name"></p>'
    + '<p><input type="file" name="file"></p>'
    + '<p><input type="submit" value="upload"></p>'
    + '</form>'
    + '</body'
    + '</html>';
}

function isFormData(req) {
    var type = req.headers['content-type'] || '';
    console.log(type);
    return 0 == type.indexOf('multipart/form-data');
}

var server = http.createServer(function(req, res) {
    switch (req.method) {
        case 'GET':
            var html = getHtml();
            res.setHeader('Content-Type', 'text/html');
            res.setHeader('Content-Length', Buffer.byteLength(html));
            res.end(html);
            break
        case 'POST':
            if (!isFormData(req)) {
                res.statusCode = 400;
                res.end('type error.\n');
            } else {
                var form = formidable.IncomingForm();
                form.uploadDir = root;
                form.parse(req, function(err, fields, files) {
                    console.log(fields);
                    console.log(files);
                    res.end('Upload complete.');
                });
            }
            break
    }
})

server.listen(3000);
```

通过progress事件计算上传进度

```javascript
form.on('progress', function(received, exceptd) {
	var percent = Math.floor(received / exceptd * 100);
	console.log(percent);
});
```

创建https服务器

首先创建密钥对

```bash
#创建私钥和证书（证书包含公钥和持有者信息）
openssl genrsa 1024 > key.pem
openssl req -x509 -new -key key.pem > key-cert.pem
```

通过https模块创建https服务器

```javascript
var https = require('https');
var fs = require('fs');

var options = {
    key: fs.readFileSync('./key.pem'),
    cert: fs.readFileSync('./key-cert.pem')
};

var server = https.createServer(options, function (req, res) {
    res.end('hello world.');
})
```



#### Node中的数据存储

##### 文件存储

通过文件的形式存储程序运行的数据，可以使用process.argv获取运行脚本时的命令行参数，通过process.cwd()获取当前工作目录

```javascript
var command = process.argv.splice(2);
var file = require('path').join(process.cwd(), '/.tasks');
```

读取和写入文件

```javascript
var fs = require('fs');

fs.readFile(file, 'utf8', function(err, data) {
    if (err) throw err;
    var content = JSON.parse(data || '[]');
});

fs.writeFile(file, JSON.stringify(data), function(err) {
    if (err) throw err;
});
```

##### 数据库存储

通过Node的mysql模块连接mysql数据库

首先安装node-mysql模块

```bash
npm install mysql
```

###### 查询记录

```javascript
var mysql = require('mysql');
var sql = 'SELECT * FROM work';
var db = mysql.createConnection({
    host: '127.0.0.1',
    user: 'root',
    password: '',
    database: 'test'
});

db.connect();

db.query(sql, function(err, results) {
    console.log(results);
});

db.end();
```

###### 插入记录

sql语句中的？为参数，在query中的第二个参数中以数组形式提供，mysql模块会自动转义以防止注入攻击

```javascript
db.connect();
var sql = 'INSERT INTO work VALUES(?, ?, ?)';
db.connect();
db.query(sql, [2,3,4], function(err, result) {
    console.log(result);
});
db.end();
```

###### 更新记录

```javascript
db.connect();
var sql = 'UPDATE work SET column1=? WHERE ID=?';
db.query(sql, [2, 1001], function(err, result) {
    console.log(result);
});
db.end();
```

###### 删除记录

```javascript
db.connect();
var sql = 'DELETE FROM work WHERE ID=1001';
db.query(sql, function(err, result) {
    console.log(result);
});
db.end();
```

##### 非关系型数据库（NoSQL）

常用的非关系数据库有Redis和MongoDB等























