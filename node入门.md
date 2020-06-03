## Node基础

### 一、安装与使用
#### 1、快速入门

[NodeJs官网](https://nodejs.org/en/) 注意：将node添加到环境变量

NodeJs是由ECMAScript以及Node环境提供的一些附加API组成的，包括文件、路径、网络等模块。

- 在浏览器中全局对象是window,而在Node中全局对象则是global.

- node.js规定一个js文件就是一个模块,模块内部定义的变量和函数默认情况下外部无法得到.

- 模块内部可以使用exports对象进行成员导出,使用require方法导入其他模块.

- exports是module.exports的别名(地址引用关系),导出对象最终以module.exports为准.导出单个时用exports,多个时用module.exports

#### 2、系统模块

##### fs --- 文件系统

`const fs = require('fs');`

读取文件内容

`fs.readFile('文件路径/文件名称','文件编码',callback);`

##### path --- 文件路径模块

`const path = require('path');`

拼接文件路径

`path.join('node','hello.js');`

大多数情况使用绝对路径.  `__dirname`获取当前文件的绝对路径. 

#### 3、第三方模块

`npm install ~ `

##### gulp

`const gulp = require('gulp');`

基于Node平台开发的前端构建工具

- 项目上线、html、css、js文件压缩合并
- 语法转换(ES6、less)
- 公共文件抽离
- 修改文件浏览器自动刷新

##### config

`const config = require('config');`

允许开发人员将不同运行环境下的应用配置信息抽离到单独的文件中，模块内部自动判断当前应用的运行环境，并读取对应的配置信息，极大提供应用配置信息的维护成本，避免了当运行环境重复的多次切换时，手动到项目代码中修改配置信息.

##### bcrypt

依赖环境

- python 2.x

- node-gyp

    `npm install -g node-gyp`

- windows-build-tools

    `npm install --global --production windows-build-tools`

```javascript
    // 导入bcrypt模块
    const bcrypt = require('bcrypt');
    // 生成随机字符串 gen =##### generate 生成 salt 盐
    let salt = await bcrypt.genSalt(10);
    // 使用随机字符串对密码进行加密
    let pass = await bcrypt.hash('明文密码', salt);
    // 密码比对
    let isEqual = await bcrypt.compare('明文密码', '加密密码');
```

##### joi

定义用户信息规则

##### formidable

解析表单规则

##### package.json

使用 `npm init`生成

- 项目依赖

`dependencies`

- 开发依赖

`devdependencies`

#### 5、模块加载机制

##### 当模块拥有路径没有后缀时

1.require方法根据模块路径查找模块，如果是完整路径，直接引入模块.

2.如果模块后缀省略，先找同名JS文件再找同名JS文件夹.

3.如果找到了同名文件夹，找文件夹中的index.js.

4.如果文件夹中没有index.js就会去当前文件夹中的package.json文件中查找main选项中的入口文件.

5.如果找指定的入口文件不存在或者没有指定入口文件就会报错，模块没有被找到.

##### 当模块没有路径也没有后缀时

1.Node.js会假设它是系统模块.

2.Node.js会去node_modules文件夹中.

3.首先看是否有该名字的JS文件.

4.再看是否有该名字的文件夹.

5.如果是文件夹看里面是否有index.js.

6.如果没有就会去当前文件夹中的package.json文件中查找main选项中的入口文件.

7.如果找指定的入口文件不存在或者没有指定入口文件就会报错，模块没有被找到.

### 二、创建web服务器

#### 1、简单服务器

```javascript
    const http = require("http");
    const app = http.createServer();
    app.on('request',(req,res) => {
        res.send("<h1>hi,user!</h1>");
    });
    app.listen(3000);

```

#### 2、http协议

##### 报文

> 在http请求和响应的过程中传递的数据块就叫做报文,包括传送的数据和一些附加信息,并且要遵守规定好的格式.

##### 请求报文

请求方式 `GET` `POST`

请求地址 

```javascript

    app.on('request',(req,res) => {

        req.headers //获取请求报文
        req.url //获取请求地址
        req.method //获取请求方法

    });
```

##### 响应报文

http状态码
- 200 请求成功
- 404 请求的资源没有被找到
- 500 服务器端错误
- 400 客户端请求有错误

内容类型
- text/html
- text/css
- application/javascript
- image/jpeg
- application/json

```javascript

    app.on('request',(req,res) => {

        res.writeHead(200, {
            'Content-Type': 'text/html;charset=utf8'
        });

    });

```

设置响应头

`response.setHeader('Content-Type','text/html');`

当使用`response.setHeader()`设置响应头时,他们将与传给`response.writeHead()`的任何响应头合并,其中`response.writeHead()`的响应头优先.

##### http请求与响应处理

###### 1.GET请求参数处理

- 参数被放置在浏览器地址中,例如：`http://localhost:3000?name=xx&age=22`;
- 参数获取需要借助系统模块`url`,`url`模块用来处理`url`地址.

```javascript

    const http = require('http');
    const url = require('url');
    const app = http.createServer();
    app.on('request',(req,res) => {
        let { query } = url.parse(req.url,true);
    });
    app.listen(3000);

```

###### 2.POST请求参数

- 参数被放置在请求体中传输.
- 获取POST参数需要使用data事件和end事件.
- 使用`querystring`系统模块将参数转换成对象格式.

```javascript

    const http = require('http');
    const app = http.createServer();
    const querystring = require('querystring');
    app.on('request',(req,res) => {
        let postParams = '';
        req.on('data',params => {
            postParams += params;
        };
        req.on('end',() => {
            console.log(querystring.parse(postParams));
        });
    });
    app.listen(3000);

```

##### 路由

路由是指客户端请求地址与服务端程序代码的对应关系.请求什么响应什么.

```javascript

    app.on('request',(req,res) => {
        //获取客户端的请求路径
        let { pathname } = url.parse(req.url);
        if(pathname == '/' || pathname == '/index')
        {
            res.end('欢迎来到首页!');
        }
        else if(pathname == '/list')
        {
            res.end('欢迎来到列表页!');
        }
        else
        {
            res.end('抱歉!您访问的页面出游了!');
        }
    });

```
##### 静态资源与动态资源

- 服务器端不需要处理,可以直接响应给客户端的资源就是静态资源,例如css,js,image等.
- 相同的请求地址不同的响应资源,这种资源就是动态资源.

### 三、nodejs异步编程

- 使用回调函数获取异步API执行结果
- 回调地狱
- promise
- 异步函数async

异步函数是异步编程语法的终极解决方案,它可以让我们将异步代码写成同步的形式,让代码不再有回调函数嵌套,使代码清晰明了.

```javascript

    const fn = async () => {}

    async function fn() {} 

```

##### async 关键字

1.普通函数定义前加async关键字,普通函数变成异步函数.
2.异步函数默认返回promise对象.
3.在异步函数内部使用return关键字进行结果返回,return关键字代替resolve方法.
4.在异步函数内部使用throw抛出程序异常
5.调用异步函数再链式调用then方法获取异步执行结果.
6.调用异步函数再链式调用catch方法获取异步函数执行的错误信息.

##### await 关键字

1.await关键字只能出现在异步函数中
2.await后面只能写promise对象
3.await关键字可以暂停异步函数向下执行,直到promise返回结果 


### 四、mongoDB

#### 环境搭建

- 使用Node.js操作MongoDB数据库需要依赖NodeJs第三方包mongoose
- 项目目录下使用`npm install mongoose`命令下载
- 使用`net start mongoDB`启动数据库服务

```javascript

    const mongoose = require("mongoose");
    mongoose.connect('mongondb://localhost/playround',{
        useUnifiedTopology: true,
        useNewUrlParser: true
    })
    .then(() => console.log("数据库连接成功"))
    .then(() => console.log("数据库连接失败"));

    const courseSchema = new mongoose.Schema({
        name: String,
        author: String,
        isPublished: Boolean
    });

    const Course = mongoose.model("Course",courseSchema);

    Course.create({
        name: "xxr",
        author: "xdq",
        isPublished: false
    })
    .then(result => {
        console.log(result)
    });

    Course.save();

```
#### mongoose验证

> 创建集合规则时,可以设置当前字段的验证规则,验证失败则输入插入失败

- required: true 必传字段
- minlength: 3 字符串最小长度
- maxlength: 20 字符串最大长度
- min: 2 数值最小为2
- max: 100 数值最大为100
- enum: ['html','css','javascript','nodejs']
- trim: true 去除字符串两边的空格
- validate: 自定义验证器
- default: 默认值
- message: 自定义错误信息

获取错误信息: error.errors['字段名称'].message









