---
title :  webpack初略学习 
date : 2017-09-07 9:11:53
categories: "前端那些打包工具"
tag:
 - webpack   
 - CMD,COMMONJS
 - 模块
---
熟话说浓缩就是精华，哈哈，所以就这么简单粗暴的介绍下吧，写下的都是精华。 已经不是第一次听说webpack，但是我的起步有点晚，现在才看。开门见山~~ 

## 1. 什么是webpack？
webpack是当下最热门的前端资源模块化管理和打包工具（就是一个打包器），可以将许多松散的模块按照依赖和规则打包成符合生产环境部署的前端资源，还可以将需要加载的模块进行代码分离，等到实际需要的时候再进行异步加载。通过loader的转换，任何资源都可以视作模块，如COMMONJS模块 ,AMD模块，JS CSS ,JSON 等 ，它会根据模块的依赖关系进行静态分析，然后将这些模块按照指定的规则生成对应的静态资源。

<!--more-->
## 2.为什么要有webpack等打包器？
1.平常的模块加载方式都是在&lt;script&gt;标签里引入，这种情况是在全局作用模式window下的，这样做的弊端就是只能按&lt;script&gt;的书写顺序进行加载，而且全局作用域下容易产生冲突，引入数量比较多的文件情况下难以管理。 
2.而且，如果单独请求每个模块会造成请求次数过多，导致应用启动缓慢，但是，将所有文件压缩成一个文件，一次请求加载所有模块会导致流量的浪费，初始化过程缓慢。 
3.所以编译的时候对所有的代码进行静态分析，分析出各个模块的类型及依赖关系，然后将他们提交给适配的加载器处理，所以webpack就应运而生。 
 
## 3.COMMONJS模块，AMD模块，CMD模块，UMD模块几种常用模块？
Commonjs：允许模块通过require方法同步加载所需依赖的其他模式，然后通过exports或module.exports来导出需要暴露的接口。

```javascript
 eg： require("module");
      require("./script.js");
       export.doStuff= function (){};
       module.exports=someValue; 
 ```
优点：各个模块可以进行重用，npm中已经有许多可供使用的包。 
缺点：只能是同步加载的，同步就意味着阻塞加载，所以不适合在浏览器环境中，因为浏览器的资源是同步加载的，不能非阻塞的并行加载多个模块。 
应用实例：nodejs 
Amd模块：它是在声明模块的时候就指定所有的依赖dependencies，并且还要当做形参传到factory中，对于依赖的模块提前执行依赖前置。

```javascript
 eg:  define("module", ["dep1", "dep2"],  function (d1, d2){  return  someExportedValue; });
      require([ "module", "../file"],
      function (module, file) {  /*   ...   */  }); 
```
优点：适合异步加载模块，在浏览器环境中，并且可以并行运行多个模块。和commonjs相反。        
缺点：提前加好所有依赖，代码要复杂很多。        
应用实例：requirejs，curl   
Cmd模块：和AMD类似，并和Commonjs也有很大的兼容性。  

```javascript
 eg: define( function (require, exports, module){
    var  $ = require('jquery');
    var  Spinning = require('./spinning');
  exports.doSomething  = ... module.exports = ... }) 
 ```
优点：容易在nodejs中运行你的代码             
缺点：依赖spm打包，模块的加载逻辑比较重、             
应用实例：Sea.js   
Umd模块：主要解决模块定义的跨平台问题

```javascript
 eg:  import "jquery";
   export   function  doStuff() {}
   module  "localModule" {} 
```
优点：容易进行静态分析，面向未来的ES标准；                  
缺点：原生浏览器不支持。                  
 
##  4.如何使用webpack？
假装你的电脑里面已经安装了Node.js, 然后输入：npm install webpack -g  ,可以通过webpack -h来查看安装信息。
(1)假装创建一个项目test,进入项目路径，创建一个index.html页面，代码如下图： 
![webpack](/images/webpack-1.png)                
(2)然后再创建一个名为：bundle.js的文件，在上图的index.html中引入它。此文件里面什么代码都不需要写。 
(3)在创建一个名为entry.js的文件，不需要在index.html页面引入哟，此文件里面用来写内容。 
<b>document.write( "hello world"); </b>
(4)然后将entry.js打包进bundle.js文件中，webpack会分析入口文件，解析包含依赖关系的各个文件，然后打包到bundle.js中 
 <b>webpack entry.js bundle.js </b>
webpack打包的原理就是给每一个模块都分配一个唯一的id并通过这个id索引和访问模块，启动时，会先执行entry.js里面的代码，其他模块会在运行require的时候执行。 
(5)此时打开bundle.js文件查看，你就可以发现里面已经 有代码了: 
![webpack](/images/webpack-2.png) 
(6)运行inde.html页面，在浏览器中就可以看到Hello world. 
但是webpack本身是只能处理javascript模块的，如果碰到css,json等其他模块文件，就需要使用loader转换。 loader就是一个装换器，一个可以输入任何资源但是最终都会帮你转成js模块的工具。 
**总的来说loader有以下几点：** 
1.管道方式链式调用（允许多个文件转换，最后必定输出js文件）。
2.loader可以同步或异步执行 
3.倘若在nodejs环境下，可以做任何事情  
4.loader加载时可以接受传参。   
5.可以通过文件扩展名来绑定不同类型的文件。 
6.loader是通过Npm发布和安装。7.通过定义package.json来指定，同样也可以导出一个loader来使用，命名规则是：xxx-loader。一般是什么功能就命名什么。 

**下面就直接来试试：** 
(1)在项目里面在新建一个名为：style.css的文件：输入<b>body{ background:green}</b>
(2)修改entry.js;加入这一句<b>require("!style-loader!css-loader!./style.css")</b>
(3)安装Loader.<b>npm install css-loader style-loader</b>先用css-loader读取它，再用style-loader将它插入页面中。
(4)重新打包:<b>webpack entry.js bundle.js</b>
(5)刷新index.html页面可以看到效果。
如果有错误，cmd打包时就会显示，index.html运行时，fireug也会显示的。 
loader转换器还可以通过配置文件来执行哟 
(1)通过往package.json里面新增配置项来完成。deDependencies 
![webpack](/images/webpack-3.png) 
(2)在新配置个webpack.config.js文件

 ```javascript
var  webpack = require('webpack')
module.exports  = {
    resolve: { fallback: path.join(__dirname,  "node_modules") },
    resolveLoader: { fallback: path.join(__dirname,  "node_modules") },
    entry:  './entry.js',
    output: {
        path: __dirname,
        filename:  'bundle.js'
    },
    module: {
        loaders: [
            {test:  /\.css$/, loader: 'style!css'}
        ]
    },
    plugins:[
          new  webpack.BannerPlugin('This file is created by yu')
    ]

}  
```
(3)直接运行webpack ，刷新index.html 就可以查看效果 