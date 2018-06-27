---
title :  Angular 2+ 关于新版angular-cli的应用
date : 2017-09-06 11:08:53
categories: "Angular 2+"
tag:
 - Angular 2+  
 - Angular-cli
 - 工具
---
以前写过一个webstorm借助angular-cli搭建angular2.0项目的博客。 后来许久没有接触过angular，现在拾起来的时候发现已经更新，用法变了。所以来记录下，以免其他友看到照成误区。这也是说明一个道理，别人写下的东西你不一定适用，因为技术的东西说变就变，谁知道的。

## [1]基于Node环境，安装angular-cli。&nbsp; 新版的已经变成安装@angular/cli。所以安装时：`npm install -d @angular/cli `
<!--more-->
那么问题来了，一大堆错误，说缺少pathon和visual studio。还有说版本不兼容的，所以建议node的版本不要太高，也不用太低。第一次我用的7以上的版本说不支持。然后换成6.0以上也是不支持。最后换成6.9几用的好好的。并不是所有人的电脑安装都会出问题的。 既然说缺少pathon那就安装吧。推荐一个网友的解决办法，我也是参照这个解决的，就不重复啰嗦[看这里](http://www.cnblogs.com/liangxiaojie/p/4595406.html);说到底会出错的原因都是因为npm是国外的，需要FQ，会有那么一丝丝不稳定。 
不过建议大家先安装cnpm在装，就什么问题都，没有了。 
`npm install -g cnpm`  
`cnpm install -g @angular/cli `

## ng命令
安装好angular/cli之后，就可以用ng命令啦。 
例如：ng --version 查看版本号 
ng help 帮助 

**当然最重要的还是用它创建工程。ng new 工程名;** 
然后会自动帮你创建所有的依赖，搭建成一个简单的模板，大概这样 
![angular-cli](/images/angular-1.png)
node-modules会自动帮你创建，只是耗时比较长，建议一次下载多次copy。 
运行项目试试，进入你的工程目录下，然后ng serve; 默认是开启localhost:4200的。 
所以浏览器里打开localhost:4200就可以啦。

![angular-cli](/images/angular-2.png)
这是最简单的项目啦，当然你可以继续创建命令 

![angular-cli](/images/angular-3.png)
例如运行`ng g c user `

![angular-cli](/images/angular-4.png)
它是四个四个创建的，配套吗！！ 后续介绍一篇文章专门讲[angular-cli](http://blog.csdn.net/sulishibaobei/article/details/77246482)的
这些文件工程太大了，运行时可以打压，`ng serve --prod --aot` 也是可以的。 
好了就介绍这些基础的，其实后面的使用并没有改变太多啦 
之前在博客园中也介绍过关于在webstorm中如何开发angular项目，如果想在webstorm中使用，可以去看看
目前我使用VScode ，觉得挺好的！！
