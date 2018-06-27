---
title :  nginx第一课  
date : 2017-09-25 11:08:53
categories: "前端那些微小事"
tag:
 - nginx  
 - 配置
---
有这样一个问题，我的nicefish项目里面要从我的服务器http://112.74.180.53 里面去拿去数据，然后遇见了跨域；然后就想到了用nginx做一个代理去拿数据；
其实解决的办法由很多种，单单就跨域的这个问题的话，proxy.config.json也是可以解决，jsonp也可以解决，这里聊一聊我用nginx解决的过程；
<!--more-->
## nginx 下载 
[戳这里](http://nginx.org/en/download.html)
我下载的是1.12.1 ,哪个版本都可以
打开可以看到几个文件夹和一个可执行文件 ，向下面的一样；
![nginx](/images/nginx-1.png)
html 文件里面主要放html代码或者项目的；
conf 主要是放置我们的配置文件；
我们切换到当前文件内运行nginx.exe或者nginx start默认的端口号是80，浏览器打开localhost 就可以看到一个页面，这时候运行的是html文件里面的index.html；如果想修改端口号，则是conf文件夹里的nginx.conf 下面会仔细来介绍这个配置文件的；

打开nginx.conf文件：

```javascript
#user  nobody;
worker_processes  1;  #允许生成的进程数2 ,默认数1

#error_log  logs/error.log;  #指定日志路径，级别，这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;   #指定ngInx进程运行文件存放地址
events {                       #events块
    worker_connections  1024;   #最大连接数  默认为512
    #accept_mutex on;  #设置网络连接序列化，防止惊群现象发生，
    # multi_accept on; # 设置一个进程是否同时接受多个网络连接，默认为off;
    # use epoll;  #事件驱动模型， select|poll|kqueue|epoll|resig|/dev/poll|eventport;
}

http {      # http 块  可以嵌套多个server ,配置代理
    include       mime.types;  #文件扩展名与文件类型映射表
    default_type  application/octet-stream;  #默认文件类型，默认为text/plain
    #proxy_method get;
    #access_log off; # 取消服务日志
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    # 自定义格式
    #access_log  logs/access.log  main;  #combined为日志格式默认值

    sendfile        on; #允许sendfile方式传输文件，默认值为off，可以在http块，server块，location块；
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65; #链接超时时间，默认值为75s，可以在http块，server块,location块；
    #sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    #gzip  on;
    #upstream mysvr{
            #server 127.0.0.1:80;
            #server 112.74.180.53:80;
    #}
    server {      # server块  配置虚拟主机的相关参数，一个http中可以有多个server
        listen       80;  #监听端口
        server_name  tomcat;   #监听地址
        root  html;
        index  index.html;
        location /file {    #配置请求的路由 以及各个页面的处理情况
            proxy_pass  http://112.74.180.53:80;
        }
        location /toutiao {
            proxy_pass http://v.juhe.cn;

        }
        location ~* \.(js|css|png|gif|jpg|mp3|ogg|ttf|woff|woff2|html|json|map)$ {
              expires 30d;
       }

        default_type 'text/html';
        charset utf-8;
       
         # keepalive_timeout 120; #单链接请求上限次数
       # location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
               #root path; #根目录
               #index vv.txt #设置默认页
               #proxy_pass http://mysvr #请求转向mysvr定义的服务器列表
               #deny 127.0.0.1; #拒绝的ip
               #allow 172.18.5.54 #允许的ip
         # }
       
        #charset koi8-r;
        #access_log  logs/host.access.log  main;
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #proxy_intercept_errors on;   #如果被代理服务器返回的状态码为400或者大于400，设置的error_page配置起作用。默认为off。
        error_page   500 502 503 504  /50x.html; #错误页

        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```
`#`都是被注释的代码；
root  html;
index  index.html;
这就是为什么运行localhost会打开html里面index.html文件的原因，都是在配置文件里配置好的；
也就是如果我d盘有个项目，root换成d盘路径，打开的就是我的项目了；
```javascript
upstream mysvr{
        #server 127.0.0.1:8081;
        server 112.74.180.53:80;
 } 
```
看到有这样一段代码，被我注释掉了；
其实是和这个一起使用的
```javascript
 location /file {    #配置请求的路由 以及各个页面的处理情况
            proxy_pass  http://112.74.180.53:80;
        }
```
如果上面的upstream mysvr不被注释，那么这段应该这么写：

```javascript
location /file {    #配置请求的路由 以及各个页面的处理情况
            proxy_pass  http://mysvr;
        }
```
能用到的每一行都在后面写了注释，跟着使用就可以啦；
举个例子：我要去这个下面获取数据http://112.74.180.53/file/comment-mock.json
注意看看我的location那里怎么配置的；
proxy_pass  http://112.74.180.53:80; 将地址写在这里；
location /file 表示只要遇见/file开头的请求就调用这个代理；这就是为什么配置了多个的原因：

```javascript
 location /file {    #配置请求的路由 以及各个页面的处理情况
            proxy_pass  http://112.74.180.53:80;
        }
 location /toutiao {
            proxy_pass http://v.juhe.cn;

        }
```
不同的文件，调用不同的；但是怎么知道是不是file开头呢？就是和我们的html里面的代码有关；
我们的url中这样写就可以啦：private url='/file/comment-mock.json';
碰到是这个请求的就会走入第一个配置啦；
这边也推荐几个觉得写得不错的文章，如果还有疑问，可以去这些文章中看一看；
[用nginx的反向代理机制解决前端跨域问题](http://www.cnblogs.com/gabrielchen/p/5066120.html)
[nginx常用代理配置](http://www.cnblogs.com/fanzhidongyzby/p/5194895.html)
[玩玩nginx](http://blog.csdn.net/zhangliangzi/article/details/52143358)
或者私信给我，我们一起来聊一聊；
