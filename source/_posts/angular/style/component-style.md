---
title :  Angular 2+ 设置组件样式的那些事 
date : 2017-09-02 11:05:53
categories: "Angular 2+"
tag:
 - Angular 2+  
 - ngStyle
 - ngClass
---
![fengjing](/images/fengjing-4.jpg)
<div class="image-caption">你用Angular吗？</div>

##  介绍 :如何只改动最简单的css代码，呈现完全不一样的视图效果。
###  第一种：最基本的设置：
<!--more-->
<img src="http://upload-images.jianshu.io/upload_images/4116930-982f4ad0d5fd7408.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" > 
<div class="image-caption">图1 代码</div>
<img src="http://upload-images.jianshu.io/upload_images/4116930-66db768ea2abfff5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
<div class="image-caption">图2 界面运行效果图</div>
平常，想给一个label或者p等标签添加样式，我们就是这样操作，在Angular中也是一样的。现在，如果我想要将字体换成红色呢，首先想到的就是去修改.label里的color属性值，可如果样式表是封装的或者外部引用的，不方便修改呢？
这时候就要用到ElementRef 和Renderer2了。可以去Angular 官网里搜索哟。
<img src="http://upload-images.jianshu.io/upload_images/4116930-84cb5a9a2291484d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
<div class="image-caption">&nbsp; renderer.class 图</div>
###  第二种:我们可以这样用
<img src="http://upload-images.jianshu.io/upload_images/4116930-a174137d1ddabdf5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
<div class="image-caption">&nbsp; label.html</div>
<img src="http://upload-images.jianshu.io/upload_images/4116930-047f9e0a9248b70d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
<div class="image-caption">&nbsp; label.ts</div>
当然ElementRef和renderer2都引用自@angular/core库里面的。 至于ViewChild 和ngAfterViewInit()可以自行去了解。
<img src="http://upload-images.jianshu.io/upload_images/4116930-062a49888ff8ea87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"> 
<div class="image-caption">&nbsp;&nbsp;&nbsp; 修改效果图</div>

当然，你说这样修改的弊端有没有呢？肯定是有的，因为，this.el.nativeElement获取的是当前元素，如果用*ngFor生成了一系列的label的话，它只会给你修改第一个label的。那有没有其他的办法可以修改呢。当然也是有的！

### 第三种：我们可以获取到元素的节点进行操作?
<img src="http://upload-images.jianshu.io/upload_images/4116930-12c5b5c2c91e5027.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
<div class="image-caption">&nbsp; label.ts</div>

获取元素节点，还是用上面的elementref。 如果要改变所有的，For循环皆可以啦。可是，这样做又有问题了？如果想不同的label不同字体颜色怎么办呢？
**当然Angular也提供了方法呀？ ngStyle和ngClass可以用呀！**

<img src="http://upload-images.jianshu.io/upload_images/4116930-9812d0ae51e1617b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
<div class="image-caption">&nbsp;ngStyle</div>
<p>[ngStyle]="{color:'&nbsp; '}"&nbsp; 和[style.color]是一样的意思。
你的label想变成什么颜色，你就给我传那个值进来，0就是green......
这种适用于只需要修改少量属性的标签。
如果你需要修改颜色，字体大小，间距....这种方式显然太繁琐，这时候ngclass就来了。</p>
<img src="http://upload-images.jianshu.io/upload_images/4116930-9e8fc68d79cdd836.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
<div class="image-caption">&nbsp;ngclass</div>
<p>注意ngClass后面的字样。label为什么没有用单引号呢，而后面的text-primary却有呢？
.label是一个变量，我们的样式是可以传参的。</p>
<img src="http://upload-images.jianshu.io/upload_images/4116930-09c7142d6bee0a3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240">
<p>也就是你的html标签不需要改动，需要什么样式就在ts离传入即可。ngClass也可以利用这个特性去修改。
其实这几种用的地方也挺多的，不过得根据实际需求去采用不同形式。
欢迎来找我交流Angular 或前端技术哟。</p>
			
 
 
 
 