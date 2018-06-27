---
title :  angular4 中自定义pagination组件 
date : 2017-09-08 9:08:53
categories: "Angular 2+"
tag:
 - Angular2+  
 - 分页组件
---
![fengjing](/images/fengjing-3.jpg)
<div class="image-caption">你用Angular 吗?</div>
<!--more-->
</div>
一.介绍 
一个基于angular4 开发的可以分页的组件。组件的好处就是可以复用，复用.....作为一个前端码农，开始的分页功能实现是我用jquery写的，其他同事用的时候都要将我的代码拷贝过去，于是我被鄙视了。为了不继续被鄙视就开始了尝试。 
二.基本功能 
1.根据规定的每页数据条数，分页显示数据 
2.上下页操作数据 
直接上界面图可能清晰点：  
<img src="http://upload-images.jianshu.io/upload_images/4116930-2fe57ab69ccdcb87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="" data-original-src="http://upload-images.jianshu.io/upload_images/4116930-2fe57ab69ccdcb87.png?imageMogr2/auto-orient/strip" data-image-slug="2fe57ab69ccdcb87" data-width="1338" data-height="655"> 
<div class="image-caption">项目截图</div>
根据图片进行讲解下：根据你总数据条数:totalNum; 每页显示条数:pageSize,通过组件会自动给你分页显示。 
eg：总数41 条数据,每页显示10条数据，则是分成5页。如果想每页显示7条，则会分成6页。 
三.简要代码 
架构目录图： 
<img src="http://upload-images.jianshu.io/upload_images/4116930-fed7af4e2c9f5530.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="" data-original-src="http://upload-images.jianshu.io/upload_images/4116930-fed7af4e2c9f5530.png?imageMogr2/auto-orient/strip" data-image-slug="fed7af4e2c9f5530" data-width="360" data-height="351"> 
<div class="image-caption">项目目录</div>
有两个组件：pagination和page;&nbsp; pagination是用来显示前面数据的，page是用来下面分页的。 
pagination.html示例代码： 
<img src="http://upload-images.jianshu.io/upload_images/4116930-7fc90a6263bba71b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="" data-original-src="http://upload-images.jianshu.io/upload_images/4116930-7fc90a6263bba71b.png?imageMogr2/auto-orient/strip" data-image-slug="7fc90a6263bba71b" data-width="1517" data-height="603"> 
<div class="image-caption">数据显示部分代码</div>
ngb-xxx不用在意，是用的ng-bootstrap，只是为了让界面好看点。其实就是一个简单的table用来显示数据。*ngFor用来取数据，懂Angular 的人应该都会，这不是重点就不介绍了。 
&lt;page&gt;&lt;/page&gt;&nbsp; 
这里是引入page组件。但是传入了一些属性值；关于模板语法，依赖注入，自定义事件就不介绍了。只要知道这里是：pagination组件向page组件传入了几个值：pageSize(页面显示数据条数)，totalNum(总数据条数),curPage(当前页码 ），totalPage(总页码),pageData(每页显示数据) 
其中pageSize和pageData是一样的，只是后面为了操作，多传了一个值； 
那么：pageSize等值都是哪里来的呢，当然是pagination.ts里定义的： 
<img src="http://upload-images.jianshu.io/upload_images/4116930-a003c8ca2a6946ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="" data-original-src="http://upload-images.jianshu.io/upload_images/4116930-a003c8ca2a6946ae.png?imageMogr2/auto-orient/strip" data-image-slug="a003c8ca2a6946ae" data-width="948" data-height="296"> 
<div class="image-caption">pagination.ts代码</div>
代码中的page就是你获得到得数据，自然totalNum就会等于数据的长度。依次算出来就好了。 pagination组件算是完成了，现在就来看看page是怎么接收到数据并进行操作的。 
page.html页面的代码：   
<img src="http://upload-images.jianshu.io/upload_images/4116930-bdee3bc2c0eaf98c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="" data-original-src="http://upload-images.jianshu.io/upload_images/4116930-bdee3bc2c0eaf98c.png?imageMogr2/auto-orient/strip" data-image-slug="bdee3bc2c0eaf98c" data-width="1753" data-height="404"> 
<div class="image-caption">page.ts 分页数据的html</div>
其实很简单：就一个a标签显示条数，页数的，三个button,一个显示上一页，一个页数，一个下一页。每个按钮都绑定了一个changePage事件； 
看一下实现部分的代码： 
<img src="http://upload-images.jianshu.io/upload_images/4116930-012ecce551a391ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="" data-original-src="http://upload-images.jianshu.io/upload_images/4116930-012ecce551a391ef.png?imageMogr2/auto-orient/strip" data-image-slug="012ecce551a391ef" data-width="1664" data-height="1269"> 
<div class="image-caption">page.ts分页实现部分代码</div>
@Input是接收pagination.html组件传过来的数据。@Output是定义pagination.html页面定义的changeCurPage事件。 
通过三个if else判断，对传多来的数据进行处理。其实就分为：数据只够显示一页；数据显示到最后一页；数据刷新到中间页码；这三种情况下的操作。 
好了，基本就完成了。有些东西也是借助网友的想法，但是思路大致都是这样。现在我改变条数和数据量看看： 
<img src="http://upload-images.jianshu.io/upload_images/4116930-a5a797d4493a0cb9.png" alt="" data-original-src="http://upload-images.jianshu.io/upload_images/4116930-a5a797d4493a0cb9.png" data-image-slug="a5a797d4493a0cb9"> 
<div class="image-caption">&nbsp; 项目截图</div>
详情代码及效果：https://github.com/sulishibaobei/pagination
