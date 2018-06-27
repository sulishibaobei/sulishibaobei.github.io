---
title :  滑稽的下午--Angular 2+管道的使用  
date : 2017-09-07 9:08:53
categories: "Angular 2+"
tag:
 - Angular 2+  
 - pipe
 - 管道
 - 日期
---
注:所有的angular 2+ 其实就包含了4,是向上兼容的；

![pipe](/images/pipe-1.png)
让一个component组件里的时间显示当前时间并自动刷新。 
**过程:** 
1.首先获取当前时间 new Date();
2.弄个定时器隔一秒刷新呀；

<!--more-->
 ` app.component.ts中这样写到：`

 ```javascript 
 InitialTime= new  Date();  // 获取当前时间  
 data:string;
  getTime(m){   // 判断获取的分钟数是否小于10，小于10只会显示一位数，前面自动补0 
       if (m&lt;10 ){
         m ="0"+ m;
          this .data= this .InitialTime.getHours()+":"+m;   // 显示时间(小时+分钟) eg   17:15 
    }
      else  {
          this .data= this .InitialTime.getHours()+":"+ this .InitialTime.getMinutes();     // 显示时间(小时+分钟) eg   17:15 
    } 
  }
  constructor(){
       this .getTime( this .InitialTime.getMinutes()); // 运行上面方法让时间显示 
     setInterval(()=&gt;{  // 设置定时器，隔1秒刷新一次，好实时监控时间 
               this .InitialTime= new  Date();
                this .getTime( this  .InitialTime.getMinutes());                              
              }, 1000 );                
     } 
```
`app.component.html中这样写：`

```html
 &lt;span class="time"&gt; {{ date }}&lt;/span&gt;
```
 
然后变成我想要的效果了，可是代码我自己都嫌长，而且还是用传统js方法写的。后来就想不是angular里面有Pipe管道吗，是不是可以试试。 
`app.component.ts是这样写的。` 

```javascript
 time:any=Date.now();  // 获取当前时间 
  constructor(){
     setInterval(() =&gt;{  // 设置定时器，隔1秒刷新一次，好实时监控时间 
           //   this.birthday = new Date();       
            this .time=Date.now();   // Date里面自带方法                   
               },1000 );  
  }
```
`app.component.html是这样写的：`

```html
&lt;span class="time"&gt; {{time | date:"HH:mm" }}&lt;/span&gt; 
```
**注：**
|是管道标示符。angular里面提供了内置的datePipe管道可以供我们使用 
  HH：mm 表示输出小时和分钟；yy:MM:dd&nbsp; 表示输出年月日 
  详情可以看看官网的管道介绍里面关于DatePipe Api的介绍。 
  同样的效果，代码量就大大减少，所以学习还是在完成的基础上多尝试。耶~~~ 
  后续会有详细的关于管道学习的[更多文章]()
 