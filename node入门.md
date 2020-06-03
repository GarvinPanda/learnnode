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

```diff 
    ! gulp
```

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
    app.on('request',(req,res) =##### {
        res.send("<h1>hi,user!</h1>");
    });
    app.listen(3000);

```

#### 2、http协议









