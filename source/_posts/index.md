---
title :  关于个人博客的搭建
date : 2017-09-06 11:08:53
categories: "Hexo"
tag:
 - node.js
 - hexo
 - next
 
copyright: true
top: 20
---

![fengjing](/images/fengjing-1.jpg)
<!--more-->
## 博客前生
[博客基本搭建]http://www.cnblogs.com/sulishibaobei/p/6428241.html, 这是用的yilia主题
之前是将博客托管在github上，运行http://sulishibaobei.github.io 即可以访问，如果你只是初步尝试，建议看这个，教你10分钟搭建一个属于自己的博客
## 博客今世
1.在阿里云上购买了一个域名：www.sulishibaobei.com ,将域名解析到sulishibaobei.github.io上，现在我的博客访问地址就是[www.sulishibaobei.com](http://www.sulishibaobei.com);
这样访问http://sulishibaobei.github.io 将会重定向到http://www.sulishibaobei.com 上;
2.给博客重新更换了主题：next;方法还是和之前更换yilia主题一样，github上将想要的主题下载好，放置在<b>Themes</b>文件夹下面,更换站点根目录的<b>_config.yml</b>文件下的<b>theme: next</b>
3.<b>hexo clean </b>,<b>hexo g</b>,<b>hexo d ;</b>这三步重新编译，这样你的博客就焕然一新了;
不一定要是这两个主题，因为配置都是大同小异,只是因为Next里面继承了许多的功能比较全，像我这么懒惰的人就用它了;

## 博客最基本配置
主题更换好以后，怎么让主题的功能更突出就需要操作主题里面的<b>_config.yml</b>文件了,是主题下的文件;
### 1.菜单栏<b>menu</b>的配置
![menu](/images/theme-1.png)
找到主题配置文件<b>_config.yml</b>中的的menu,大概是这样一段代码：
```json
menu:
  主页: / || home
  关于: /about/ || user
  标签: /tags/ || tags
  归档: /archives/ || archive
  展示: /site/ || th
  日程: /schedule/ || calendar
  有料 : /something
  公益404: /404.html || heartbeat
menu_icons:
  enable: true
  blog:/images/1.jpg
```
注：你下载下来的是英文的,根据你的习惯,喜欢中文就切换成中文的;本来主题就提供了配置语言的,在next主题的languages里面,我们只需要配置即可；
在根站点的_config.yml文件中配置<b>language: zh-Hans</b>表示你要使用的是中文,配置了这一句就不用像我这样手动修改;
有可能你并不需要像我这么多项，根据自己的需求进行配置，其实就是将它#注释的地方去掉注释就行
关于图标前面的图标,你不写的话，就会采用主题默认的,例如：有料;主题里为我们提供的几种图标，例如：home,tags等;还有一类是你自己想自定义图标，
menu_icons就是配置图标的,例如blog,在需要使用图标的||后面写上blog;
但是关出现这些标签，怎么让标签点击到对于的页面这就需要我们去创建应的文件夹<b>hexo new page tags</b>  创建的文件夹名和配置的名字标签: /tags是一样的;
新创建的文件夹会出现在source文件下，和_post文件夹同级,默认的文件是index.md,其中会只有这两行代码：
```md
---
title: tags
date: 2017-09-05 14:45:27
---
```
按照所需的数量，依次创建完成好，最基本的菜单栏就完成了;
### 2.<b>header</b>和个人内容介绍的配置
 ![header](/images/theme-3.png) 
 ![themes](/images/theme-4.png) 
这个是在<b>站点根目录_config.yml</b>配置的，在这个文件里面配置的内容即便更换主题，这些内容也不会改变，只是显示位置不一样；找到<b>site</b>

```md
# Site 网站
title: 少女雨的博客
subtitle: 爱生活爱远方爱自由更爱你
description: 12级毕业生，南方姑娘，前端程序媛，前途堪忧的代码洁癖者;
author: 少女雨
email: 380571249@qq.com
keywords: "前端,js,jquery,javascript,html5,开发者,程序媛，typescript,angular，校园java醋虐者"
language: zh-Hans
```
填上你的个人信息，这块就算完成了

### 3.标签页的配置Category & Tag 
![tag](/images/theme-5.png)
这块的配置也是在站点根目录_config.yml下的；
```md
#default_category: uncategorized
category_map: 分类
tag_map:  标签
archives_map: 日志
```
找到这一部分,配置就行,但是怎么和你博客的内容关联起来这需要这样配置：
例如：想让标签和博客文章关联起来，找到source文件下的tags文件index.md文件，添加这一句：
```md
---
title:  
date: 2017-09-01 11:07:25
type: "tags"
---
```
告诉主题，你想关联tags
其他的关于category_map等也是这样配置的;
关于这一部分标签如何配置，建议参看这篇文章，[戳这里](https://hexo.io/zh-cn/docs/front-matter.html#%E5%88%86%E7%B1%BB%E5%92%8C%E6%A0%87%E7%AD%BE)
### 4. 社交连接,Rss,友链配置
![links](/images/theme-6.png)
这一部分是在主题文件下的_config.yml里面配置：
找到这一块rss:
false：禁用 RSS，不在页面上显示 RSS 连接。
留空：使用 Hexo 生成的 Feed 链接。 你可以需要先安装 hexo-generator-feed 插件。
具体的链接地址：适用于已经烧制过 Feed 的情形。
社交链接，找到这个social
```md
social:
  GitHub : https://github.com/sulishibaobei/ || github
  E-Mail : 380571249@qq.com || envelope
  weibo :  http://weibo.com/xiaoyu051027/profile?rightmod=1&wvr=6&mod=personinfo/ || weibo 
  CSDN :   http://blog.csdn.net/sulishibaobei/  CSDN
  博客园 :  http://www.cnblogs.com/sulishibaobei/    
  简书 :    http://www.jianshu.com/u/e6c53e37e95b/ 
  #Google: https://plus.google.com/yourname || google
  #Twitter: https://twitter.com/yourname || twitter
  #FB Page: https://www.facebook.com/yourname || facebook
  #VK Group: https://vk.com/yourname || vk
  #StackOverflow: https://stackoverflow.com/yourname || stack-overflow
  #YouTube: https://youtube.com/yourname || youtube
  #Instagram: https://instagram.com/yourname || instagram
  #Skype: skype:yourname?call|chat || skype
social_icons:
  enable: true
  icons_only: false
  transition: false 
```
根据你自己的想法，换上自己链接配置就行，图标和上面介绍的Menu是一样的；
友链，找到这个links_title 
```md
# Blog rolls  友情链接
links_title: 友情链接
#links_layout: block
#links_layout: inline
links:
  Angular 4.x 修仙之路 : https://segmentfault.com/blog/angular4
```
填上自己的就行，配置多个在链接那里添加就行；
基本上博客雏形就配置完成了;
### 5.基本配置
还有一些关于主题选择，字体配置的小细节，[请看](http://theme-next.iissnan.com/theme-settings.html)

## 博客内容完善
### 阅读量统计
![yuedu](/images/theme-7.png)
这部分是采用[leanCloud](https://leancloud.cn/)实现的，推荐一篇写的好的文章,[戳这里](https://notes.wanghao.work/2015-10-21-%E4%B8%BANexT%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0%E9%98%85%E8%AF%BB%E9%87%8F%E7%BB%9F%E8%AE%A1%E5%8A%9F%E8%83%BD.html#%E9%85%8D%E7%BD%AELeanCloud)
### 访客量统计
![yuedu](/images/theme-8.png)
这部分采用不蒜子统计统计
找到这一部分：
```md
busuanzi_count:
  # count values only if the other configs are false
  enable: true
  # custom uv span for the whole site
  site_uv: true
  site_uv_header: <i class="fa fa-user"></i>本站访客数
  site_uv_footer: 人次
  # custom pv span for the whole site
  site_pv: true
  site_pv_header: <i class="fa fa-eye"></i> 本站总访问量
  site_pv_footer: 次
  # custom pv span for one page only
  page_pv: true
  page_pv_header: <i class="fa fa-file-o"></i> 本文总阅读量
  page_pv_footer: 次
```
### 评论系统
我才用的是有言评论，localhost时候会失败，但是采用http页面评论时会成功;配置看这里[next](http://www.jianshu.com/p/4729e92fddbe)
如果你是Next主题5.1以上的版本，只需要注册，然后将Uid号填在主题文件下的_config.yml
从多说不能用的时候，facebook也不能用，DISQUS，HyperComments是国外的，请求不一定会成功；
使用畅言需要备案，除非你的网站备案了，否则也不行；来必力尝试过也不行，选择友言是比较合理的；

### 搜索服务和百度统计
搜索采用的是：Local Search ;
配置可以看[这里](http://theme-next.iissnan.com/third-party-services.html)

### 博客收录
如何将让自己的博客在搜索引擎被搜索到：[看这里](http://www.jianshu.com/p/df46bca5889d)

### 博客小功能
如何点击的时候出现桃心，如何更换链接颜色等[看这里](http://blog.csdn.net/qq_33699981/article/details/72716951)

## 关于后续
有一些基本的配置,很难一个一个写出来;如果看到其他的功能,而我没有写出来的，欢迎给我私信;


