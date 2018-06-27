---
title :  Css3雪碧图 
date : 2017-09-07 9:08:53
categories: "CSS"
tag:
 - CSS   
 - 雪碧图
 - gulp
---
Css雪碧图： CSS雪碧 即CSS Sprite，也有人叫它CSS精灵，是一种CSS图像合并技术，该方法是将小图标和背景图像合并到一张图片上，然后利用css的背景定位来显示需要显示的图片部分。 
原理：CSS雪碧的基本原理是把你的网站上用到的一些图片整合到一张单独的图片中，从而减少你的网站的HTTP请求数量。该图片使用CSS background和background-position属性渲染，这也就意味着你的标签变得更加复杂了，图片是在CSS中定义，而非&lt;img&gt;标签。 
如何生成雪碧图？ 
安装gulp插件： `npm install –g gulp` 
安装制作雪碧图需要的插件 `npm install –g gulp.spritesmith `
最好全局和本地各装一遍；

<!--more--> 
新建一个Gulpfile.js文件，将下列代码放进去：

```javascript
var gulp=require('gulp');   //引入这两个gulp  gulp.spritesmith
    spritesmith=require('gulp.spritesmith');   
      gulp.task('default',function(){   //新建名为sprite的任务
         gulp.src('images/*.png')  //需要合成的图片路径  *为通配符  表示这个文件夹下面所有后缀为.png的文件都会被应用
            .pipe(spritesmith({  
    imgName:'sprite.png',//合成后需要保存图片的路径 
    cssName:'sprite.css',  //合成后需要保存的css的路径
    padding:5,  //表示两个图片间的空隙
    algorithm:'binary-tree', // Algorithm 有四个可选值分别为top-down、left-right、diagonal、alt-diagonal、binary-tree
    // cssTemplate:'dist/handlebarsStr.css' //生成的css模板文件，可以是函数也可以是字符串
    }))  
    .pipe(gulp.dest('dist/')) //将生成的文件放到dist文件夹下 
})  
 ```
或者:进入项目 `npm init` 输入需要的信息,创建一个package.json文件:

```json
 {
  "name": "package.json",
  "version": "1.0.0",
  "description": "",
  "main": "Gulpfile.js",
  "dependencies": {
    "gulp": "^3.9.1",
    "gulp.spritesmith": "^6.5.1"
  },
  "devDependencies": {},
  "scripts": {
    "test": "echo \"Error: no test specified\" &amp;&amp; exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

这里提几点需要注意的： 
需要一个Gulpfile.js（首字母小写也可以） ，运行时执行 gulp default（defaullt是你新建的任务名，不是固定的，你写成其他也行）
<b>imgName:'sprite.png'</b>//合成后需要保存图片的路径<b>cssName:'sprite.css'</b>
这两个文件需要你先创建，里面的文件都要预先创建;我这里是放在dist下面就要在dis下面创建这两个文件 
合成的雪碧图； 
![sp](/images/sp-1.png)
css样式：

```css
.icon-1  {
  background-image: url(sprite.png);
  background-position: 0px 0px;
  width: 33px;
  height: 33px;
}
.icon-2 {
  background-image: url(sprite.png);
  background-position: -38px 0px;
  width: 33px;
  height: 33px;
}
.icon-3 {
  background-image: url(sprite.png);
  background-position: 0px -38px;
  width: 33px;
  height: 33px;
}
.icon-4 {
  background-image: url(sprite.png);
  background-position: -38px -38px;
  width: 33px;
  height: 33px;
}
.icon-5 {
  background-image: url(sprite.png);
  background-position: -76px 0px;
  width: 33px;
  height: 33px;
}
.icon-6 {
  background-image: url(sprite.png);
  background-position: -76px -38px;
  width: 33px;
  height: 33px;
}
.icon-7 {
  background-image: url(sprite.png);
  background-position: 0px -76px;
  width: 33px;
  height: 33px;
}
```
algorithm：四个参数只是决定图片的排列顺序； 
top-down： 
![sp](/images/sp-2.png)
具体的参数可以看这里： 
![sp](/images/sp-3.png)
好了,雪碧图就合成啦； 
下面会讲讲如何使用雪碧图； 
创建一个文件：index.html 
引入生成的css 
```html
<link rel=”stylesheet” type=”text/css” href=”./dist/sprite.css” />
```
写基本架构：<b>class="icon-1"</b> 这里是css里生成的样式名；按顺序写好就行; 

```html
<ul class="sprite">
      <li>
          <s class="icon-1"></s>
          <a href="">衣服</a>
      </li>
      <li>
          <s class="icon-2"></s>
          <a href="">鞋子</a>
      </li>
      <li>
          <s class="icon-3"></s>
          <a href="">背包</a>
      </li>
      <li>
          <s class="icon-4"></s>
          <a href="">双肩包</a>
      </li>
      <li>
          <s class="icon-5"></s>
          <a href="">裤子</a>
      </li>
        <li>
          <s class="icon-6"></s>
          <a href="">羽绒服</a>
      </li>
        <li>
          <s class="icon-7"></s>
          <a href="">外套</a>
      </li>
  </ul>
```
给他们新增加一些样式：

```css
        ul {
            list-style: none;
            margin: 0;
            padding: 0;
        }

        .sprite {
            margin: 10px auto;
            width: 206px;
            border: 1px solid #b51600;
        }

        .sprite li {
            cursor: pointer;
            height: 42px;
            width: 206px;
            background-color: #b51600;
            border-bottom: 1px solid #911001;
            border-top: 1px solid #c11e08;
        }

        .sprite li a {
            color: #fff;
            line-height: 42px;
            font-size: 14px;
        }
```
效果就是这样： 
![sp](/images/sp-3.png)
引入了css文件，class也改动了，怎么没有图标呢？ 
![sp](/images/sp-4.png)
生成的css文件里面就告诉你了，让标签`dispay:block`;

```css 
  .sprite li s{      
            display: inline-block;
            margin-left: 10px;
            margin-right: 8px;
        }
```
看看效果： 
![sp](/images/sp-5.png)