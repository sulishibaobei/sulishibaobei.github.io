---
title :  bootstrap简单图文环绕效果 
date : 2017-09-08 9:08:53
categories: "Bootstrap"
tag:
 - bootstrap   
 - 图文环绕
---
一.下载bootstrap-3.3.7 
二.在html页面引入css,js; 
eg: 
```html
<link src="bootstrap-3.3.7-dist/css/bootstrap.min.css">
<script type="text/javascript" src="jquery.min.js"></ script> 
<script type="text/javascript" src="bootstrap-3.3.7-dist/js/bootstrap.min.js"></ script> 
```

<!--more-->
注意：因为会用到jquery，所以引入了jquery .js; 
三.body部分的代码是这样的： 
```html
<body style="margin: 10px;padding: 0px">
    <div class="left">  
    </div>
    <div class="right">
    </div>
<body/>
```
简单解析下： 
  body里包含两个div ,一个在左边排列(用于放图片)，一个在右边排列(用于放文字);都用class名进行区分啦，left和right; 
简单的给两个div添加点样式： 
 
```css
   .left  { 
     float :  left ; 
     max-width :  43% ; 
     padding-left :  5px ;  
        } 
    .right{ 
    margin-left :  5px ;
        } 
``` 

现在还看不出什么效果; 
给第一个div里添加图片; 
<code>&lt; img src="index.jpg" class="img-responsive" alt="图像"&gt; </code>
给第二个div添加文字，里面的文字是采用Boostrap的样式; 

```html
<div class="container">
                <h1>图文环绕</h1>
                <div class="row">
                  <div class="col-xs-6 col-md-offset-3" style=" box-shadow: inset 1px -1px 1px #ccc, inset -1px 1px 1px #ccc;">
                        <div class="row">
                            <div class="col-md-6" style=" box-shadow: inset 1px -1px 1px #ccc, inset -1px 1px 1px #ccc;">
                                <p>&nbsp;&nbsp;&nbsp;风雨中走过却不安世事的心</p>
                            </div>
                      </div>
                               <p>&nbsp;&nbsp;&nbsp;我还是很喜欢你，像风走了三千米不问归期 我希望有个如你一般的人如山间清爽的风如古城温暖的光从清晨到夜晚由山野到书房只要最后是你就好 </p>
                 </div>
                <div class="col-xs-6 col-sm-offset-3" style="box-shadow: inset 1px -1px 1px #ccc, inset -1px 1px 1px #ccc;">
                      <p>&nbsp;&nbsp;&nbsp;我觉得这个世界美好无比。晴时满树花开，雨天一湖涟漪，阳光席卷城市，微风穿越指间，入夜每个电台播放的情歌，沿途每条山路铺开的影子，全部是你不经意写的一字一句，留我年复一年朗读。这世界是你的遗嘱，而我是你唯一的遗物。
                      </p>
                      <p>&nbsp;&nbsp;一个人的记忆就是座城市，时间腐蚀着一切建筑，把高楼和道路全部沙化。如果你不往前走，就会被沙子掩埋。所以我们泪流满面，步步回头，可是只能往前走 </p>
               </div>
               <div class="clearfix visible-xs"></div>
                  <div class="col-xs-6 col-md-offset-3" style=" box-shadow:inset 1px -1px 1px #ccc, inset -1px 1px 1px #ccc;">
                   <p>&nbsp;&nbsp;&nbsp;你燃烧，我陪你焚成灰烬。你熄灭，我陪你低落尘埃。你出生，我陪你徒步人海。你沉默，我陪你一言不发，你欢笑，我陪你山呼海啸。你衰老，我陪你满目疮痍。你逃避，我陪你隐入夜晚。你离开，我只能等待。 </p>
               </div>
             <div class="col-xs-6 col-sm-offset-3" style="box-shadow: inset 1px -1px 1px #ccc, inset -1px 1px 1px #ccc;">
                 <p>我希望买的鞋子是你渴望的颜色。我希望拨通电话时你恰好在想我。我希望说早安你也刚起床。我希望写的书是你欣赏的故事。我希望关灯的刹那你正泛起困意。我希望买的水果你永远觉得是甜的。我希望点的歌都是你喜欢唱的。我希望我希望的是你希望的。
                  </p>
             </div>
             <div class="clearfix visible-xs"></div>
    </div>
  </div>
``` 
采用的是bootstrap的架构： container&gt; row&gt; column&nbsp; 
将css样式也写在标签上，所以有点看起来复杂，其实很简单，这几段文字的结构都一样。 
看看运行效果： 
![bt](/images/bt-1.png)
拖动页面试试
![bt](/images/bt-2.png)
你可以通过控制台随意拖动页面的宽度，看看文字的适配程度。
[源码](https://github.com/sulishibaobei/bootstrap/tree/master/bootstrap%20by%20around)
另外有两个免费网站模板也可以看看，在同一个github路径下