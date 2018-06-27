---
title :  Flexible Box布局基础知识详解 
date : 2017-09-08 10:08:53
categories: "CSS"
tag:
 - CSS   
 - 弹性布局
 - Flexible
---
## 1.基本概念，借用阮一峰老师的一张图：
![fb](/images/fb-1.png)
<!--more-->
容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross start，结束位置叫做cross end。
项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。
## 2.容器的基本属性
flex-direction
flex-wrap
flex-flow
justify-content
align-items
align-content

### 2.1 flex-direction  属性决定主轴的方向 (及行排列)  默认值：row

```css	
.box{
    flex-direction：row | row-reverse | column |column-reverse   /*有四个值  分别的显示效果*/
       }
```
![fb](/images/fb-2.png)
html5实现代码：

```html
  <div class="box">
          <div class="box-item">1</div>
          <div class="box-item">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>
   </div>
```
css3部分实现代码：

```css
 body{
           margin: 0 auto;
           width: 1000px;
        }
         .box{
             background: gold;
             margin: 1px;
             display: flex;  /*必须设置这个*/
             flex-direction: row;  /*一排的方式排列*/
         }
         .box-item{
             width: 100px;
             height: 100px;
             line-height: 100px;
             background: #ccc;
             color: white;
             text-align: center;
             margin: 5px;
         }
```
实现效果：
![fb](/images/fb-3.png)
如果css3改成   `flex-direction: row-reverse`;
![fb](/images/fb-4.png)
其他两个属性类推；
### 2.2 flex-wrap  定义如果一条轴线排不下，如何换行 默认：nowrap

```css
 .box{
             flex-wrap：nowrap | wrap | wrap-reverse;
     }
```
html部分代码：
```html
  <div class="box1">
          <div class="box-item">1</div>
          <div class="box-item">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>
           <div class="box-item">1</div>
          <div class="box-item">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>
           <div class="box-item">1</div>
          <div class="box-item">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>
    </div>
```
css 部分代码：

```css
 .box1{
             background: gold;
             margin: 1px;
             display: flex;
             flex-flow: wrap;
         }
         .box-item{
             width: 100px;
             height: 100px;
             line-height: 100px;
             background: #ccc;
             color: white;
             text-align: center;
             margin: 5px;
         }
```
效果：
![fb](/images/fb-5.png)
这是换行的效果，其他效果可以尝试；
### 2.3 flex-flow 是flex-direction 和 flex-wrap的缩写；所以当独写上面的要写两个
默认值为row norap

```css
  .box{
       flex-flow: flex-direction || flex-wrap
      }
```
### 2.4 justify-content 属性定义了项目在主轴上的对齐方式

```css
 .box{
            justify-content:flex-start | flex-end | center | space-between | space-around;
        }
```
flex-start（默认值）：左对齐
flex-end：右对齐
center： 居中
space-between：两端对齐，项目之间的间隔都相等。
space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。 
html5代码：

```html
 <div class="box2">
          <div class="box-item">1</div>
          <div class="box-item">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>
          <div class="box-item">1</div>
          <div class="box-item">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>
    </div>
```
css3代码：
```css 
 .box2{
             background: gold;
             margin: 1px;
             display: flex;
             justify-content: center; /**可以换换其他的属性值*/
         }
         .box-item{
             width: 100px;
             height: 100px;
             line-height: 100px;
             background: #ccc;
             color: white;
             text-align: center;
             margin: 5px;
         }
```
效果图：
![fb](/images/fb-6.png)
其他的可以自己试试：
flex-start（默认值）：左对齐
flex-end：右对齐
center： 居中
space-between：两端对齐，项目之间的间隔都相等。
space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

### 2.5 align-items 定义项目在交叉轴上如何对齐（纵轴）
```css
 .box{
           align-items:flex-start | flex-end |center | baseline |stretch
       }
```
html5代码：

```html
 <div class="box3">
          <div class="box-item">1</div>
          <div class="box-item item-tall">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>
          <div class="box-item">1</div>
          <div class="box-item item-tall">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div> 
          <div class="box-item">1</div>
          <div class="box-item item-tall">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>        
    </div>
```
css3代码：

```css
   .box3{
             background: gold;
             margin: 1px;
             display: flex;
             align-items:flex-end; /*可以换其他值看看*/
             flex-wrap: wrap;
         }
         .box-item{
             width: 100px;
             height: 100px;
             line-height: 100px;
             background: #ccc;
             color: white;
             text-align: center;
             margin: 5px;
         }
         .item-tall{
             height: 200px; /*交叉轴，高度不一*/
             line-height: 200px;
         }
```
效果：
![fb](/images/fb-8.png)
其他的可以自己试试：
flex-start：交叉轴的起点对齐。
flex-end：交叉轴的终点对齐。
center：交叉轴的中点对齐。
baseline: 项目的第一行文字的基线对齐。
stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。
### 2.6 align-content  属性定义了多根轴线(多行)的对齐方式，如果项目只有一根轴线，该属性起不来作用
```css
 .box {
          align-content: flex-start | flex-end | center | space-between | space-around | stretch;
        }
```
html代码：

```html
  <div class="box3 box3-tall">
          <div class="box-item">1</div>
          <div class="box-item">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>
          <div class="box-item">1</div>
          <div class="box-item">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div> 
          <div class="box-item">1</div>
          <div class="box-item">2</div>
          <div class="box-item">3</div>
          <div class="box-item">4</div>        
    </div>
```
css代码：

```css
   .box3{
             background: gold;
             margin: 1px;
             display: flex;
             flex-wrap: wrap;
             align-content: space-around;
         }
         .box-tall {
             height: 300px;
             }
         .box-item{
             width: 100px;
             height: 100px;
             line-height: 100px;
             background: #ccc;
             color: white;
             text-align: center;
             margin: 5px;
         }
```
效果：
![fb](/images/fb-8.png)
其他的可以自己试试：
flex-start：与交叉轴的起点对齐。
flex-end：与交叉轴的终点对齐。
center：与交叉轴的中点对齐。
space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
stretch（默认值）：轴线占满整个交叉轴。
## 3.容器里子元素的属性
order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
flex-grow属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch
通常我们定义flex:1;
![fb](/images/fb-11.png)
表示的就是这三个；
### 3.1 order 属性
html代码：
```html
<div class="box4">
          <div class="box-item1 ">1</div>
          <div class="box-item1 order">2</div>        /*注意是第二个元素有Order类*/
    </div>

```
css代码：
```css
 .box4{
             background: gold;
             margin: 1px;
             display: flex;
         }
         .box-item1{
            flex: 1;
             line-height: 100px;
             background: #ccc;
             color: white;
             text-align: center;
             margin: 5px;
             
         }
         .order{
             background: blue;
             order: -1;  
         }   

```

效果图：
![fb](/images/fb-9.png)
如果我这样设置：

```css
 .order{
             background: blue;
             order: -1;
            flex-grow:2; /*多了这个*/
         }
```
![fb](/images/fb-10.png)
其他的去试一试，大概就是这样