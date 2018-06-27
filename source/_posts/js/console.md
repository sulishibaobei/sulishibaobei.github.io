---
title :  简述alert和console.log的区别  
date : 2017-09-05 11:08:53
categories: "前端那些微小事"
tag:
 - console.log  
 - alert
 - javascript
---

**生活中还是得有发现美好和差别的眼睛, 学习前端那么久既然还不知道alert和console.log的差别;**
本菜鸟一直以为alert和console.log其实是一样的用法，只是alert以弹出框形式呈现，而console.log在控制台输出而已。只到道昨天输出一段代码，结果发现alert和console.log输出结果不一样,本宝宝可算涨见识了, 然后就是各种查阅资料，百度查看，终于发现点眉目啦 ;

<!--more-->

**我写了这样一段代码：**
![cmd-markdown-logo](/images/console-3.png)
alert输出的结果为： 
![cmd-markdown-logo](/images/console-1.png)
console.log输出的结果为：  
![cmd-markdown-logo](/images/console-2.png)

**然后我就尴尬了**

[1]原来console.log() 可以打印任何类型的数据。而 alert() 只能输出string，
[2]如果alert输出是对象会自动调用 toString() 方法。如果想 console.log() 输出的与alert相同，需要调用 toString()。 
[3]所以将后面的改写成&nbsp;console.log(hmac.toString()) 就可以啦

**下面再来总结下两者的差别** 
```javascript
alert()
   1.1 有阻塞作用，不点击确定，后续代码无法继续执行
   1.2 alert()只能输出string,如果alert输出的是对象会自动调用toString()方法 
      e.g. alert([a,b,c]); //a,b,c  
   1.3 alert不支持多个参数的写法,只能输出第一个值
      e.g. alert(1,2,3); // 1
console.log()
   2.1 在打印台输出
   2.2 可以打印任何类型的数据
        e.g. console.log([a,b,c]); //[a,b,c]
   2.3 支持多个参数的写法
        e.g. console.log(1,2,3) // 1 2 3  
```
好啦~~~就是这样啦。 
 		 
 
 
