---
title : 前端面试中常见的那些HTML/CSS题
date : 2017-09-26 10:08:53
categories: "前端面试题整理"
tag:
 - CSS   
 - HTML5
 - 面试题
---
## 什么是盒子模型？
一般面试都会问：盒子模型你是怎么理解的？
盒子模型分为两种：W3C(标准的)盒子模型和IE(怪异)盒子模型
![html](/images/html-1.png)
<!--more-->
有图可知，标准的盒子模型包含margin,border,padding,content，并别content部分不包含其他部分；
例如一个盒子模型如下：margin:20px;border:10px;padding:10px;width:200px;height:100px;
如果用w3c计算盒子模型占用为：宽度为：20x2+10x2+10x2+200=280px;高度为：20x2+10x2+10x2+100=180px;
盒子的实际宽度为：10x2+10x2+200=240px; 因为实际的宽度margin是不计算在内；在IE6以上统一 
盒子的实际高度为：10x2+10x2+100=140px;
所以：标准盒子模型下，元素的宽度等于content的宽度；盒子模型占据的位置计算方式是2*margin+2*border+2*padding+width/height;
盒子的实际大小：2xborder+2xpadding+width/height
![html](/images/html-2.png)
有图可知IE盒子模型包含padding，margin，border，content四部分，但是ie盒子模型的content已经计算border和padding在内。
还是上面那一题，如果采用IE盒子模型方式计算，盒子需占据的位置宽度为：20*2+200=240px; 高度为：20*2+100=140px;
盒子的实际大小为：宽 200px; 高100px；
所以:IE盒子模型下，元素的宽度实际上包含了content+padding+border在内，所以计算的时候实际不需要再加上padding,border;
w3c可能也认识到这种差异性，所以允许我们事先定义盒子模型的解析方式；
box-sizing:content-box(default) | border-box | inherit
content-box:让元素维持W3C的标准模型；
border-box：让元素维持IE传统盒子模型;
接下来通过代码来看一看差别：
来一段基本的代码：

```html
<div id="div1">content-box</div>
<div id="div2">border-box</div>
```
```css
*{
            margin:0;
            padding:0;
}
div{
            width: 200px;
            height: 100px;
            background: red;
            margin: 20px;
            padding: 20px;
            border: 10px solid black;
}
```
这一段简单的代码，来看看效果：
![html](/images/html-3.png)
这时候，两个div显示的效果是一样的，默认都是采用content-box方式解析的；
接下来改变代码看一看：
```css
#div2{
  box-sizing: border-box;
}
```
先来看看效果图：
![html](/images/html-4.png)
从肉眼直观来看，第二个盒子显然比第一个盒子小了许多，但是我们要用网页设计的观点来解释这个问题的话就可以看下面两张图：
![html](/images/html-5.png)
这是第一个div的图；
![html](/images/html-6.png)
这是第二个div的图；
这就是为什么我们上面的题算出来的结果是那样的；IE下认为一个盒子的宽度应该是包含content,padding和border在内的；所以第二张图的时候，padding和border都侵蚀了元素的内容部分；
最后总结一点：
>* w3c中的盒子模型占用的大小：margin + border + padding + width/height;
width: margin2 + border2 + padding2 + width;
height: margin2 + border2 + padding2 + height;
w3c中盒子模型的实际大小：border + padding + width/height;
width: border2 + padding2 + width;
height: border2 + padding2 + height;
w3c中盒子模型的内容区域的大小：width/height
width: width;
height: height;

>* IE 盒子模型占用的宽度：margin + content-width/height;
width: margin2 + width;
height: margin2 + height;
IE 盒子模型的实际大小：content-width/height
width: width;
height: height;

另外还有一点：css3的伸缩盒模型指的是[FlexBox](http://www.sulishibaobei.com/css/fb-120170908/),想了解的戳链接

## 为什么样式定义开头要定义* {margin:0;padding:0;}
是用来清除所有元素的默认内外边距；
举一个例子：
```html
<div id="div1"></div>
```
```css
 div{
            width: 100px;
            height: 200px;
            background: red;
         }
```
这时候我们可以看看效果：
![html](/images/html-7.png)
这时候，我们并没有给div设置margin和padding,但是确有一个边距存在，这就是浏览器给我们的默认边距；
当我们设置这样一句：
```css
 *{
            margin: 0;
            padding: 0;
        }
```
![html](/images/html-8.png)
看到效果了吧，也就是设置这样一句的作用;
例外平常还有两中比较常见的设置方法，可能有些人会这样设置：
```css
html{
    margin:0;
    padding:0;
}
```
这种方式是没用的，并不能清除脏边距；
还有一种
```css
body{
    margin:0;
}
```
这种方式是有效地，不同的浏览器，不同的兼容性，从代码通用性来说，设置*号是最好的方式；

## 行内元素有哪些？块级元素有哪些？空元素有哪些？
行内元素：a,b,span,img,input ,select,label,button,textarea;
块级元素：div ,ul,li ,dl,dd,dt,p,h1-h6,hr,table,p;
空元素(没有内容的HTML元素,自动闭合的) : br,meta,hr,link,input,img;

块级元素和行内元素的转换：
块级元素默认display:block，行内非替换元素（a,span）默认为display:inline；行内替换元素(input) 默认为display:inline-block；
display:none；不显示该元素，也不会为该元素保留原先占有的文档流位置；
这就要区别visibility:hidden，两者都会将某个元素隐藏起来，但是visibility:hidden仍然会保留元素的空间在那里;
display:block;转换为块级元素；
display:inline;转换为行内元素；
display:inline-block;转换为行内块级元素；
当将行内元素设置为float:left/right，该行内元素的display属性会被赋予block值，且拥有浮动特性；行内元素去除了之间的莫名空白；
当为行内元素进行定位时，position:absolute与position:fixed都会使得原因的行内元素变为块级元素；

## css实现垂直水平居中
这是一道经典的题，解决的办法有许多种，曾经写过一篇博客专门讲这个[这里](http://blog.csdn.net/sulishibaobei/article/details/77116939)
这里还是简单说一说：
```html
 <div class="wrapper">
        <div class="content"></div>
    </div>
```
```css
.wrapper {
      position: relative;
      width: 500px;
      height: 500px;
      background-color: #ddd;
     }
    .content{
        background-color:#6699FF;
        width:200px;
        height:200px;
        position: absolute;        /*父元素需要相对定位*/
        top: 50%;
        left: 50%;
        margin-top:-100px ;    /*二分之一的height，width*/
        margin-left: -100px;
    } 
```
效果就是这个样子的：
![html](/images/html-9.png)
具体的可以看看我的博客自己算一算；

## 简述一下src和href的区别？
href是指向网络资源所在位置，建立和当前元素(锚点)或当前文档(链接)之间的链接，用于超链接;
src是指向外部资源的位置，指向的内容将会嵌入到文档中当前的位置，在请求src资源时会将其指向的资源下载并应用到文档内；例如js脚本，img图片和frame等元素；当浏览器解析该元素时，会暂停其他资源的下载和处理，直到将该资源加载，编译，执行完毕，图片和框架等元素也如此；

## 什么是CSS Hack?
一般来说是针对不同的浏览器写的不同的CSS，就是 CSS Hack;目的是使你的CSS代码兼容不同的浏览器；
大致分为三种：内部Hack，选择器Hack,HTML头部引用(if IE)Hack；
内部Hack:IE6能识别下划线"_"和星号"*"，IE7能识别下划线"_"，而firefox两个都不能识别等；
```css
.test{
 color:#090\9; /* For IE8+ */
 *color:#f00;  /* For IE7 and earlier */
 _color:#ff0;  /* For IE6 and earlier */
}
```
选择器Hack:IE6能识别*html .class{}，IE7能识别*+html .class{} 或者 *.first-child+html .class{}等；
```css
* html .test{color:#090;}       /* For IE6 and earlier */
    * + html .test{color:#ff0;}     /* For IE7 */
```
具体的可以参照这张表：
![html](/images/html-10.png)
HTML头部引入(if IE)Hack：针对所有的IE：<!--[if IE]><![endif]-->,具体的实现如下：
表示这段文字只在IE显示；
```html
<!--[if IE]>
  <style>
     .test{
         color:red
     }
  </style>
<![endif]-->
```
表示这段文字只在IE6显示：
```html
<!--[if IE]>
<![endif]-->
```
只在IE6以上版本生效
```html
<!--[if gte IE 6]>
这段文字只在IE6以上(包括)版本IE浏览器显示
<![endif]-->
```
只在IE8上不生效
```html
<!--[if ! IE 8]>
这段文字在非IE8浏览器显示
<![endif]-->
```
非IE浏览器生效
```html
<!--[if !IE]>
这段文字只在非IE浏览器显示
<![endif]-->
```

## 页面导入样式时，使用link和@import有什么区别？
link属于XHTML标签，除了加载的CSS外，还能用于定义RSS，定义rel连接属性等作用；而@import是css提供的，只能用于加载css；
页面被加载完成时，Link会同时被加载，而@import引用的CSS会等到页面被加载完再加载；
import是CSS2.1提出来的，只有IE5以上才能识别，而link是xhtml标签，无兼容性问题；


## 简述同步和异步的区别？
同步是阻塞模式，异步是非阻塞模式；
同步就是指一个进程在执行某个请求的时候，若该请求需要一段时间才能返回信息，那么这个进程将会一直等待下去，直到收到返回信息才继续执行下去；
像平常的`<script>加载js文件`，`<img/>加载图片`都是同步的；
异步是指进程不需要一直等下去，而是继续执行下面的操作，不管其他进程的状态。当有消息返回时系统会通知进程进行处理，这样可以提高执行的效率。
像我们平常使用的ajax，默认都是异步的；

## px,em和rem的区别；
px和em都是长度单位，区别是，px的值是固定的，指定是多少就是多少，计算比较容易。em得值不是固定的，并且em会继承父级元素的字体大小。
浏览器的默认字体高都是16px。所以未经调整的浏览器都符合: 1em=16px。那么12px=0.75em, 10px=0.625em
如果rem呢？是指相对于根元素的字体大小的单位，和em类似，只是一个计算的规则是依赖根元素一个是依赖父元素计算。
## 什么叫优雅降级和渐进增强？
渐进增强：针对低版本浏览器进行构建页面，保证最基本的功能，然后针对高级浏览器进行效果，交互等改进和追加功能达到更好的用户体验；
优雅降级：一开始就构建完整的功能，然后再针对低版本浏览器进行兼容；
区别：
a.优雅降级是从一个复杂的现状开始，并试图减少用户体验的供给；
b.渐进增强则是从一个非常基础的，能够起作用的版本开始，并不断扩大，以适应未来环境的需要；
c.降级(功能衰减)意味着朝前看，同时保证其根基处于安全地带；

## 浏览器的内核分别是什么？
IE：trident内核；
Firefox:gecko内核；
Safari:webkit内核；
Opera:Blink内核；
Chrome：Blink(基于webkit，Google和Opera Software共同开发)
现在的双核浏览器指的是：拥有IE兼容内核和非IE极速内核两个内核(简而言之就是一个IE内核，一个非IE内核)；

## 跨域资源请求方式
首先理解什么是跨域：由于浏览器的同源策略，凡是发送请求url的协议，域名，端口三者之间任意一与当前页面地址不同即为跨域；
解决跨域的几种办法：结合我之前写过的[文章](http://blog.csdn.net/sulishibaobei/article/details/52136441)
1.JSONP只能使用get方式，通过绑定一个jsonpcallback回调函数；
例如：`http://www.sulishibaobei.com?callback=jsonpCallback`;
一般我是采用ajax的方式的，需要的参数一目了然；
```javascript
    $(function () {  
               $.ajax({  
                   async: true,  
                   type: "GET",  
                   dataType: 'jsonp',  
                   jsonp: 'callback',  
                   jsonpCallback: 'callback',  
                   url: "https://www.sina.cn",  
                   contentType: "application/json;utf-8",  
                   success: function (msg) {  
                       alert(msg);  
                   }  
               });  
           })  
```
这里既然提到了ajax，我也写过具体的文章介绍的，[请看](http://blog.csdn.net/sulishibaobei/article/details/77334270)
2.proxy代理的方式
代理的方式有许多种，之前我在网上copy过一段代码，用的yahoo的一个代理：
`http://query.yahooapis.com/v1/public/yql` url前面多加这么一段，让他替我们发请求；
后来用过nginx，这篇文章里面有过[介绍](http://www.sulishibaobei.com/nginx/ng-120170925/)
因为我工作中主要用到的都是angular所以还介绍一种在angular中解决跨域的，配置proxy.config.json文件，原理和nginx是一样的；
3.cors 这是现代浏览器支持跨域资源请求的一种方式;通过添加一个响应头：Access-Control-Allow-Origin；
```javascript
 res.setHeader("Access-Control-Allow-Origin","*");
```
说道这种方式，之前我使用node的require去发送请求时，就使用过这一种，效果杠杠的；

4.script,iframe采用内嵌的方式也不会产生跨域的；
```javascript
<script src="url"></script>
```
5.xdr IE8，IE9有一种解决跨域的方案，只支持get,post，而且对于协议不同的跨域是不行的；比如http下发送Https;
在ie下，用原生ajax其实也可以得到数据；和xdr类似；
```javascript
xdr.responseText
```

## 谈一谈垃圾回收机制以及内存管理？
Javascript 具有自动垃圾回收机制，执行环境会负责管理代码过程中使用的内存；
原理：垃圾收集器会定期（周期性）找出那些不在继续使用的变量，然后释放其内存。
不再使用的变量也就是生命周期结束的变量，当然只可能是局部变量，全局变量的生命周期直至浏览器卸载页面结束，局部变量只在函数的执行过程中存在，而在这个过程中会为局部变量在栈堆上分配相应的空间，以存储它们的值，然后在函数中使用这些变量，直至函数结束，而闭包中由于内部函数的原因，外部函数并不能算是结束；
看看代码：
```javascript
function fn1(){
    var obj={name:'sulishibaobei',age:22}
}
function fn2(){
   var obj={name:'sulishibaobei',age:22};
   return obj; 
}
var a=fn1();
var b=fn2();
```
首先，定义两个function,分别叫做fn1和fn2，当fn1被调用时，进入fn1的环境，会开辟一块内存存放对象{name:'sulishibaobei',age:10}，而当调用结束后，出了fn1环境，那么该块就会被js引擎中垃圾回收器自动释放；在fn2被调用的过程中，返回的对象被全局变量b所指向，所以该块内存并不会被释放；
但是垃圾回收器是怎么知道那个需要被清除的，这就需要跟踪哪个标记是无用的变量；对于不再用的变量打上记号，以备垃圾回收机制清除；
标记清除是最常用的垃圾回收方式；当变量进入环境时，例如，在函数中声明一个变量，就将这个变量标记为'进入环境',当离开时，就标记为'离开环境';被标记为'离开环境'的就会被清除了；
```javascript
function test(){
    var a=10;//被标记，进入环境
    var b=20;//被标记，进入环境
}
test();//执行完毕之后,a,b又被标记离开环境，被回收
```
垃圾回收器在运行的时候会给存储在内存中的所有变量都加上标记（当然，可以使用任何标记方式）。然后，它会去掉环境中的变量以及被环境中的变量引用的变量的标记（闭包）。而在此之后再被加上标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。最后，垃圾回收器完成内存清除工作，销毁那些带标记的值并回收它们所占用的内存空间。
采用这种方式的浏览器一般有IE,Firefox,Opera,Chrome,Safari等；
引用计数也是垃圾回收的一种方式；原理是跟踪记录每个值被引用的次数；当声明了一个变量并将一个引用类型值赋给该变量时，则这个值的引用次数就是1；如果同一个值又被赋予另一个变量，则该值的引用次数加1；相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数减1；当这个值的引用次数变成0时，则说明没有办法访问这个值了，因而就可以将其占有的内存空间回收回来；这样当垃圾回收机制再次运行时，它就会释放那些引用次数为0的值所占有的内存；
```javascript
function test(){
  var a={} //a引用次数为0；
  var b=a; //a的引用次数加1，为1；
  var c=a; //a的引用次数加1，为2；
  var b={}; //a的引用次数减1，为1；
}
```
这种方式中有一个问题，循环引用
```javascript
function fn(){
    var a={};
    var b={};
    a.pro=b;
    b.pro=a;
}
fn();
```
以上a，b引用的次数都是2，fn()执行完毕后，两个对象都已经离开环境，在清除标记下不会有问题，但是在引用计数策略下，因为a，b的计数都是2，所以不会被垃圾回收机制清除；如果fn函数被大量的调用，就会造成内存泄露；在IE7和IE8上，内存直线上升；
解决办法就是：手动解除循环引用；将变量赋值为null；等垃圾回收机制运行时，就会清除这些值了；
IE9+后并不会产生循环引用导致dom内存泄露问题；
GC的缺陷：GC时，停止响应其他操作，可能对于连贯性比较高的应用不好；
GC的优化：
1.分代回收：目的是通过分区"临时"与"持久"对象，多回收"临时对象"区，少回收"持久对象"区；减少每次需要遍历的对象，从而减少每次GC的耗时；
2.增量GC：每次处理一点，处理多次；显然对象太多，就不适合采取这种方式；
根据实际情况比如：低 (对象/s) 比率时，中断执行GC的频率，simple GC更低些；如果大量对象都是长期“存活”，则分代处理优势也不大。

## 开发过程中遇到的内存泄露情况，如何解决的？
这是和上一个问题连在一块的，什么是内存泄露呢？按我的理解就是，已经不需要使用的变量还存在内存中没有被垃圾回收机制给清除掉，堆积在一起造成了系统内存的浪费，导致程序运行出现缓慢或者崩溃的情况;
要搞清楚几点：内存泄露并不是占用很大内存才叫内存泄露，1byte的浪费都可以称为内存泄露;内存泄露是堆区，栈区不会泄露；跳转网页依然存在，直到浏览器的关闭；
几种常见的会出现内存泄露的情况:
1.循环引用
```javascript
function fn(){
    var a={};
    var b={};
    a.pro=b;
    b.pro=a;
}
fn();
```
还是上题中的例子；
2.某些dom操作(IE)
3.自动类型装箱转换(IE6,IE7)
```javascript
var s='fffff';
alert(s.length);
```
这里也会造成内存泄露是因为在js类型中，String类型并非对象，但是可以使用（.）运算符，因为js的默认类型转换机制，允许js在遇到（.）运算符时，自动将string类型转换为Object中对应的String对象。该临时转换的对象100%会泄露；

解决办法就是：
1.显示转换类型；
```javascript
var str=new String("hhhhhh");
alert(str.length);
```
这大概就是typescript语言使用的原因；
2.避免循环事件引用，将值覆为null；可以采用try..finally
3.垃圾箱，将不要的对象放置在垃圾箱中；
IE下，DOM对象不会被CG程序回收，只有在离开页面时才会被回收。一旦使用了DOM对象，千万不要试图o=null，可以设置一个叫garbage 的div，并且将其display设置为none，将不用的DOM对象存入其中。
阮一峰老师介绍过关于内存泄露的解决办法，可以[看看](http://www.ruanyifeng.com/blog/2017/04/memory-leak.html)

## 一次完整的Http过程是怎么样的？
基本流程：
a.域名解析(查找浏览器解析) 
b.发起TCP三次握手
第一次握手：建立连接时，客户端发送syn包(syn=j)到服务器，并进入SYN_SENT状态，等待服务器确认；SYN：同步序列编号；
第二次握手：服务器收到syn包，必须确认客户的SYN(ack=j+1)，同时自己也发送一个SYN包(syn=k),即SYN+ACK包，此时服务器进入SYN_RECV状态；
第三次握手：客户端收到服务器的SYN+ACK包，向服务器发送确认包ACK(ack=k+1),此包发送完毕，客户端和服务器进入ESRABLISHED(TCP连接成功)状态，完成三次握手；
c.建立TCP连接后发起http请求；
d.服务端响应http请求，浏览器得到html代码；
e.浏览器解析html代码，并请求html代码中的资源；
f.浏览器对页面进行渲染，呈现给用户；

## Http的状态码有哪些？
1.200状态码：成功2xx，成功处理了请求的状态码
a.200:服务器已成功处理了请求并提供了请求的网页；
b.204：服务器成功处理了请求，但没有返回任何内容；
c.206：客户端发送了i一个带有Range头的GET请求，服务器完成了它；


2.300状态码 ： 重定向3xx，每次请求中使用重定向不超过5次；
a.301:请求的网页已永久转移到新的位置，当URLs发生变化时，使用301代码。搜索引擎中保存新的URL；
b.302:请求的网页临时转移到新的位置，搜索引擎索引中保存原来的URL；
c.304：如果网页自请求者上次请求后没有更新，则用304代码告诉搜索引擎机器人，可节省带宽和开销；

3.400状态码：客户端错误4xx，表示请求可能出错，妨碍了服务器的处理；
a.400：服务器不理解请求的语法；
b.403：服务器拒绝请求；
c.404：服务器找不到要请求的网页，服务器上不存在网页经常会返回此代码；
d.410：请求的资源永久删除后，服务器返回此响应，该代码与404相似，但是资源以前存在而现在不存在的情况下，有时用来替代404页面代码，如果资源已永久删除，应使用301指定资源的新位置；

4.500状态码
服务器错误5xx:表示服务器在处理请求时发生内部错误，这些错误可能是服务器本身的错误，并不是请求出错；
a.500:服务器遇到错误，无法完成请求；
b.503：服务器目前无法使用（由于超载或停机维护)

## Https是如何实现加密？
https是在http上面多了一层协议ssl,该协议用来给传输的内容加密；
https加密流程：
  1.客户端将SSL协议的版本号，加密算法的种类，产生的随机数A等信息传给服务器；
  2.服务器选择其中的一种组合作为加密方式，同时将自己的证书，公钥，另外一个随机数B一起传给客户端；
  3.客户端验证客户端返回的信息(包括证书，签名，域名等),验证成功，则生成对称加密密钥s，用公钥加密后返回给服务器；
  4.服务器用私钥将这段密钥解密，得到对称密钥s,并用此密钥加密一段握手消息返回给客户端；
  5.客户端收到握手消息，用对称密钥解密，验证成功，则握手成功；
单向加密和双向加密：
单向加密：服务端有一套(两份)证书:含公钥和私钥的Jks文件(此文件自己保留),和只含公钥的ser文件，其中ser文件是要给客户端的；
双向加密：除了具有单向加密的特性(服务端验证客户端),客户端也有一套(两份)证书来验证服务端消息，对于android来说，分别bks文件(含公钥和私钥，自己保留)，ser文件(含公钥给服务端);

## 关于算法题
[快速排序算法](http://www.ruanyifeng.com/blog/2011/04/quicksort_in_javascript.html)  可以参考阮一峰老帅的；
二分法查找也叫做对半查找：是一种在有序数组中查找特定元素的搜索算法；
查找过程：
 a.首先，从有序数组的中间元素开始搜索，如果该元素正好是目标元素(即要查找的元素),则搜索过程结束，否则进行下一步；
 b.如果目标元素大于或者小于中间元素，则在数组大于或者小于中间元素的那一半区域查找，然后重复第一步的操作；
 c.如果某一步为空，则表示找不到目标元素；

## 对Node的优缺点看法？
优：Node是基于事件驱动和无阻塞的适合处理并发请求，客户端代码是由javascript编写，易懂；
缺：不稳定
如果还有好多看法，欢迎指出；

## 性能优化的方法？
css方面：
a.减少http请求次数：[css Sprites](http://www.sulishibaobei.com/css/sp-120170907/),js、css源码压缩,
使用webp后缀的图片；data缓存，图片服务器，CDN托管；图片大小适中；
b.当设置样式很多时，尽量设置className,而不是操作style；
c.图片预加载，将样式表放在顶部，脚本放在底部；
javascript方面：
a.用innerHTML代替操作DOM，优化Javascript性能；
b.用变量保存请求结果，每次操作本地变量，避免请求次数；
c.少用全局变量，缓存dom节点查找结果，减少IO读取操作；






















 
















