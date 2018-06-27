---
title :  Angular 4 自定义组件封装遇见的一些事儿 
date : 2017-09-08 11:05:53
categories: "Angular 2+"
tag:
 - Angular 2+  
 - 自定义
 - component
---
![sf](/images/fengjing-2.jpg)
<div class="image-caption">你用Angular 吗？</div>
<!--more-->
一.介绍 
说说封装Angular 组建过程中遇见的一些问题和感悟.用久了Angular,就会遇见很多坑，许多基于Angular开发的框架最喜欢做的事情就是封装组件,然后复用因为这是最省事的. 
二.基本构建组件思想 
![sf](/images/sf-2.png)
<div class="image-caption">界面构建草图</div>
简要介绍上图： 
1.View 就是我们需要完成的界面,但是界面无非就是由若干个label,button,table,img,list等一些基本控件构成的,所有的前端页面构成都是一样,只是加上CSS排版确定最后的显示效果。  
2.五种基类，其实不止五种,只是列举。这里就需要我们将基类都封装成组件的形式,对外提供组件标签就可以使用.不管构建多少个页面,都可以用这几个基本组件完成. 
eg：一个button组件 

![sf](/images/sf-3.png)
<div class="image-caption">button.component.html</div>
这个Button组件里面是还包裹一个label组件的.

![sf](/images/sf-4.png)
<div class="image-caption">button.component.ts</div>
对外只用提供选择器，就可以使用Button了.
3.可是为什么还要有中间的UIbuileder呢？ 
是这样的，每次引选择器名是不是也很麻烦，看起来没有省力多少， 
封装的基本组件只需要对外抛出一个TYPEID，UIbuilder只要知道1是代表button组件，2代表label组件，UIbuilder将组件构建出来。 

![sf](/images/sf-5.png)
<div class="image-caption">UIBuilder.html 构建组建图</div>
VIEW界面需要Button给我传一个1，需要label传一个2，需要什么传对于的id就行。此时的界面和组件是完全分离的，这样构件界面的时候几乎可以没有代码就出来一个页面啦。而且下次用的时候，基类是完全不用动的。 
三.样式控制。 
也许你会觉得这样构件出来的只有最简单的页面，那么样式呢？ 
的确呀，这样就是很老土的界面，那么样式要怎么实现呢？ 
其实Angular 提供了很多的方法的。 
1.一张样式表，定义所有的样式。相当于boostrap一样。 

![sf](/images/sf-6.png)
<div class="image-caption">button样式定义</div>
都知道双花括号的定义,你只要在最外面将你需要的样式名传进来就可以啦.
2.代码修改样式 
ElementRef ，Renderer2 有多少人知道这两个属性的。 

![sf](/images/sf-7.png)
<div class="image-caption">代码修改样式</div>
这种都是可以在外部修改样式的,你的基组件不需要改动。 可能看起来费劲,但用起来好处很多，不用copy,copy了,而且你的思想也会到一个新的境界。 
其实，我用Angular有一段时间了,如果你也是,欢迎来交流。 