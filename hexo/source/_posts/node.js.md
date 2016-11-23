---
title: Nodejs
date: 2016-11-23 15:30:16 #文章生成时间，一般不改，当然也可以任意修改
categories: 编程笔记 #分类
tags: [JavaScript,Nodejs,] #文章标签，可空，多标签请用格式，注意:后面有个空格

---
### 一、Node.js的介绍
简单的说 Node.js 就是运行在服务端的 JavaScript。  
Node.js 是一个基于Chrome JavaScript 运行时建立的一个平台。  
Node.js是一个事件驱动I/O服务端JavaScript环境，基于Google的V8引擎，V8引擎执行Javascript的速度非常快，性能非常好。  
  我们可以使用以下命令来查看当前的 Node 版本：
  

```js
$ node -v
v4.4.3
第一个Node.js程序：Hello World！
以下是我们的第一个Node.js程序：
实例

console.log("Hello World");
打开cmd，到文件的目录下通过node命令来执：

node helloworld.js
```


如果是在webstorm中运行，直接右键run就可以运行了，也可以在左下角下面有一个Terminal,打开后也就是打开cmd一样的，也可以输入命令运行。



### 二、node的http模块

模块:把各种功能划分在模块中,通过模块编写相关代码
http模块:负责web服务器相关任务的模块


	
```js
// 1、引入http模块
var http = require('http');
// 2、创建服务
// 当服务受到客户端发来的http请求时,执行回调函数
var server = http.createServer(function (req, res) {
/*
* req参数:请求对象,从客户端发到服务器中的请求信息存放在这个对象中
* res参数:响应对象,需要返回给客户端的信息要放在这个对象中
*
*/
console.log(req.url);
console.log(req.method);
res.setHeader('content-type', 'text/html;charset=utf-8;')
//index.html:welcome to index;
//login.html:please input your user and pass
if (req.url == '/index.html' || req.url == '/') {
   res.write('welcome to index');
} else if (req.url == '/login.html') {
   res.write('please input your user and pass');
} else {
   res.write('404, not found');
}
   res.end();
});
// 3、让服务监听某个端口
server.listen('8888');
```

### 三、node的文件操作
fs:专门负责所有的文件、目录操作的模块

	  
```js
var fs = require('fs');
// 读取文件
/*
* readFile():异步读取文件的方法。
* readFile()的三个参数
* 第一个参数:要读取的文件路径
* 第二个参数:文件的编码格式
* 第三个参数:读取成功后的回调函数,该函数两个参数:第一个是读取失败时
* 的失败信息,第二个表示读取成功后的文件内容
*
*/  
fs.readFile('./demo.txt', 'utf-8',function (err, data) {
if (!err) {
   console.log(data);
}
});
var data = fs.readFileSync('./demo.txt');
console.log(data.toString());
//写入文件操作
// a表示拼接的形式写入新内容,w以覆盖的形式写入新内容
fs.writeFile('./hello.txt','一只乌鸦口渴了',{flag: 'a'}, function (err) {
if (err) {
     console.log(err);
 } else {
     console.log('写入成功');
 }
});
fs.writeFileSync('./hello.txt','hello world',{flag:'w'});
利用node的文件读写操作,实现复制文件的功能

1、同步读取demo.txt文件中的数据
var data = fs.readFileSync('./demo.txt','utf-8');

2、把读取出来的输入写到另一个新文件中

fs.writeFileSync('./demo副本.txt', data, {flag:'w'});
fs.writeFileSync('./1副本.gif',fs.readFileSync('./1.gif'),{flag:'w'});
```



### 四、文件操作stream读写文件


stream: 读写是分块读写,把一个文件切分为若干块,分别读写着若干块文
	            件,大大减轻服务器对内存的要求


	  
```js
var fs = require('fs');
// 创建文件读取流
var rs = fs.createReadStream('./1.gif');
// 创建文件写入流
var ws = fs.createWriteStream('./2.gif');
// 读取流在读取文件时,每次读64k的数据,每读64k,就会触发rs下的data事件,可以给这个事件绑定函数,通过函数参数获取到读取的数据
   rs.on('data', function (chunk) {
 // 调用写入流ws的write方法,把每次读出的64k数据写进指定的文件中
 // write方法的回调函数:当写入结束时,会触发执行
   ws.write(chunk,function () {
        console.log('写入完毕');
    });
});
//pipe():是读取流的方法,参数是要把数据写入的目的地(一般是写入流)。该方法会控制读取流的读取速度:每读64k数据后,交给写入流写入,当数据写入到磁盘后,再进行下一次读取。
   rs.pipe(ws);
```



### 五、文件操作之目录操作


   
```js
var fs = require('fs');
// fs.mkdir():创建一个新路径
// 第二个参数:mode,由四位数字组成:
// 第一位固定写法:0;第二位:表示管理员权限;第三位:表示:组用户权限;第四位:表示其他用户权限。
// 1:表示可执行
// 2:表示可写
// 4:表示可读
// 我们通过叠加以上权限的数组,能够灵活设置文件夹的权限
fs.mkdir('./directory', 0777, function (err) {
         if (err) {
             console.log(err);
         } else  {
             console.log('创建成功');
         }
});

// 判断目录是否已经存在
// fs.exists():两个参数,第一个参数:表示要判断的目录路径;第二个参数:表示判断结果的回调函数,该函数有一个布尔的参数,用来标示该目录是否存在
fs.exists('./directory', function (bol) {
          console.log(bol);
});
// 查看文件或目录的详情
fs.stat('./directory', function (err, stats) {
   if (err) {
     console.log(err);
   } else {
     console.log(stats);
    }
})

// 把文件或目录由相对路径转换为绝对路径
fs.realpath('./1.gif',function (err, path) {
  if (err) {
     console.log(err);
  } else {
     console.log(path);
  }
})
// 给文件重命名
fs.rename('./demo.txt','./newDemo.txt', function (err) {
   if (err) {
       console.log(err);
   } else {
             console.log('改名成功');

   }

});
```

### 六、http.js
```js
var http = require('http');
var fs = require('fs');
var url = require('url');
var server = http.createServer(function (req, res) {
var urlStr = req.url;
// 参数放true,可以把请求参数转换为对象模式
  //pathname是接口
    //query是指请求参数
var urlObj = url.parse(urlStr,true);
if (urlObj.pathname == '/') {
    var rs = fs.createReadStream('./login.html');
    rs.pipe(res);
} else if (urlObj.pathname == '/msg') {
    console.log(urlObj.query);
    if (urlObj.query.user == 'admin' &&
        urlObj.query.pass == '123') {
        res.write('{err:1,msg:"成功"}');//请求链接一直没有
             断开
        res.end();
    }
}});
server.listen('8888');
```

### 七、http模块接受get请求数据  

```js
var http = require("http");  
var fs = require("fs");  
var url = require("url");//专门用来处理url的  
var server = http.createServer(function (req,res) {  
    res.setHeader('content-type', "text/html;charset=utf-8;");  
    var urlstr = req.url;  
    var urlobj =url.parse(urlstr,true);//为了分离接口和参数，把url转换成对象  
    if(urlobj.pathname =='./login.html'||urlobj.pathname =="/"){  
       var rs = fs.createReadStream('./login.html');
        rs.pipe(res);
    }else if(urlobj.pathname == "/login"){
        if(urlobj.query.user=='wlj'&&urlobj.query.pass=='123'){
            res.end('{"err":1,"msg":"登录成功"}');
        }else{
            res.end('{"err":0,"msg":"登录失败"}');
        }
    }
});
 server.listen(8100, function () {
 console.log("服务器已经启动");
```

### 八、http模块接受post请求数据
```js
var http = require("http");
var fs = require("fs");
var url = require("url");
//专门用来处理url的,上面三个框架是系统自带的
var qs = require("querystring");
//自己引入框架就是在工程目录下用npm install querystring;
//自己引入的一个框架，可以把post请求获取的字符串参数转换为对象
//把前端不同的请求，发送不同的页面，叫做路由
var server = http.createServer(function (req,res) {
    res.setHeader('content-type', "text/html;charset=utf-8;");
    var urlobj = url.parse(req.url,true);
    if(urlobj.pathname =='./login.html'||urlobj.pathname =="/"){
        var rs = fs.createReadStream('./login.html');
        rs.pipe(res);
    }else if(urlobj.pathname == "/login"){
        req.on("data", function (chunk) {
            //req本身是一个读取流，res是一个写入流
            var str = chunk.toString();
            var obj = qs.parse(str);
            //console.log(obj);
            if(obj.user == "wlj"&&obj.pass == "123"){
                res.end('{"err":1,"msg":"success"}')
            }else{
                res.end('{"err":0,"msg":"err"}')
            }
        })
    }
});
    server.listen(8200, function () {
        console.log("服务器已经启动");
});
```

