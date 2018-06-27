---
title :  nodejs+websocket实时聊天系统  
date : 2017-09-07 9:08:53
categories: "html5"
tag:
 - nodejs  
 - websocket
---

## 介绍下websocket： 
  webSocket协议本质上是一个基于tcp的协议; 
## 建立一个websocket连接，大体的过程： 
 1.客户端浏览器首先向服务器发起一个http请求，这个请求和平常的请求有什么不同呢？ 
 多了一点附加头信息："upgrade:web Socket” 表明我这申请的是一个websocket的http请求； 
 2.服务器收到请求后，解析这些附加的头信息，然后产生应答信息返回给客户端，这样，连接就建立了； 
 3.双方就可以通过这个连接通道自由的信息传递，这个连接会一直存在，直到一方自动关闭连接； 

<!--more-->

### 客户端到服务端：

```javascript
GET /demo HTTP/1.1
Host: example.com
Connection: Upgrade
Sec-WebSocket-Key2: 12998 5 Y3 1 .P00
Upgrade: WebSocket
Sec-WebSocket-Key1: 4@1 46546xW%0l 1 5
Origin: http://example.com
[8-byte security key]
```
### 服务端到客户端：

```javascript
HTTP/1.1 101 WebSocket Protocol Handshake
Upgrade: WebSocket
Connection: Upgrade
WebSocket-Origin: http://example.com
WebSocket-Location: ws://example.com/demo
[16-byte hash response]
```
从客户端到服务端请求的信息里面包含：‘Sec-webSocket-key1","Sec-WebSocket-key2"和“[8-byte security key]”这样的信息；这是客户端浏览器需要向服务端提供的握手信息，服务端解析这些头信息，并且在握手的过程中依据这些信息生成一个16位的安全密钥并返回给客户端，以表明服务器端获取了客户端的请求； 
**大致步骤：** 
1.	逐个字符读取 Sec-WebSocket-Key1 头信息中的值，将数值型字符连接到一起放到一个临时字符串里，同时统计所有空格的数量；
2.	将在第 1 步里生成的数字字符串转换成一个整型数字，然后除以第 1 步里统计出来的空格数量，将得到的浮点数转换成整数型；
3.	将第 2 步里生成的整型值转换为符合网络传输的网络字节数组；
4.	对 Sec-WebSocket-Key2 头信息同样进行第 1 到第 3 步的操作，得到另外一个网络字节数组；
5.	将 [8-byte security key] 和在第 3，第 4 步里生成的网络字节数组合并成一个 16 字节的数组；
6.	对第 5 步生成的字节数组使用 MD5 算法生成一个哈希值，这个哈希值就作为安全密钥返回给客户端，以表明服务器端获取了客户端的请求，同意创建 WebSocket 连接
 
```javascript
 var  wsServer = 'ws://localhost:8888/Demo';  //连接地址
 var  websocket = new WebSocket(wsServer);   //建立连接
 websocket.onopen = function (evt) { onOpen(evt) };  //4个事件
 websocket.onclose = function (evt) { onClose(evt) }; 
 websocket.onmessage = function (evt) { onMessage(evt) }; 
 websocket.onerror = function (evt) { onError(evt) }; 
 function onOpen(evt) { 
 console.log("Connected to WebSocket server."); 
 } 
 function onClose(evt) { 
 console.log("Disconnected"); 
 } 
 function onMessage(evt) { 
 console.log('Retrieved data from server: ' + evt.data); 
 } 
 function onError(evt) { 
 console.log('Error occured: ' + evt.data); 
 }
 ```

浏览器的支持情况： 
 
| 浏览器	| 支持情况
| --- |---
|Chrome	|Supported in version 4+
|Firefox	|Supported in version 4+
|Internet Explorer	|Supported in version 10+
|Opera	|Supported in version 10+
|Safari	|Supported in version 5+
 
 
## 正文来了：基于websocket制作的简单聊天系统；
###  简单的界面，大致效果就是这样的：
![websocket](/images/websocket-1.png)
### 实现逻辑代码： 

```javascript
var ws = new WebSocket('ws://127.0.0.1:8082');
    ws.onopen = function (e) {
        console.log("连接服务器成功");
    }
                ws.onmessage = function (e) {
                    value1.removeAttribute("readOnly");
                    var time = new Date();
                    mess.innerHTML += time.toUTCString() + ":" + e.data + "&lt;br&gt;";
                    document.getElementById("send").onclick = function (e) {
                        ws.send(input.value + "说:" + value1.value);
                        value1.value = " ";
                    }
                    document.onkeydown = function (e) {
                        e = e || window.event;
                        if (e.keyCode == 13) {
                            document.getElementById("send").onclick();
                            return false;
                        }
                    }
                }
                ws.onclose = function (e) {
                    console.log("服务器关闭");
                }
                ws.onerror = function () {
                    console.log("连接出错");
     }
```
连接地址：ws://127.0.0.1:8082&nbsp; 那是哪里来的呢？（注意http请求则是写成http://xxx,https请求则是https://xxx；ws当然是ws://xxx);wbsocket只是客服端，地址当然是从我们的服务端给的呀； 
服务端的搭建采用了一个这样的库： `nodejs-websocket` 
 1. `npm isntall -g nodejs-websocket `
 2. 在js页面引入它 

 ```javascript
 var ws = require("nodejs-websocket" );  
 ``` 
 3. 创建一个服务 

```javascript 
 var server = ws.createServer(function (conn) {
    conn.on('text', function (str) {
    })

    conn.on("close", function (code, reason) {
        console.log("关闭连接");
    })
    conn.on("error", function (code, reason) {
        console.log("异常关闭");
    });
}).listen(8082);
console.log("websocket连接完毕")
```
 
### websocket连接算是建立啦！ 
下面展示下具体代码： 
`client.html `
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" type="text/css" href="bootstrap-3.3.7-dist/css/bootstrap.min.css" />
    <script src="jquery.min.js"></script>
    <script src="bootstrap-3.3.7-dist/js/bootstrap.min.js"></script>
    <style>
        .kuang {
            width: 600px;
            min-height: 50px;
            max-height: 296px;
            border: 1px solid;
            float: left;
            display: block;
            position: relative;
            overflow-y: scroll;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="row">
              <div class="jumbotron  bg-dark">
                <h1 class="jumbotron-heading">WebSocket chat,欢迎使用:</h1>
              </div>
            <div class="input-group text-left">
                <label>输入用户名：</label>
                <input type="text" id="name" />
                <button id="conn">连接</button>
                <button id="close">断开</button>
            </div>
            <div class="input-group text-muted">
                <div class="kuang" id="mess"></div>
            </div>
         <hr class="featurette-divider">
            <div class="input-group text-left">
                <input type="text" class="value" id="value1" />
                <button id="send">发送</button>
            </div>
        </div>
    </div>
    <script>
        var input = document.getElementById("name");
        var conn = document.getElementById("conn");
        var close = document.getElementById("close");
        var mess = document.getElementById("mess");
        var value1 = document.getElementById("value1");
        var pattern = /^[\u4e00-\u9fa5]{2,10}$/;
        close.disabled = true;
        if (window.WebSocket) {
            conn.onclick = function () {
                if (!pattern.test(input.value)) {
                    alert("名称不能为空且必须为中文");
                    return;
                }
                var ws = new WebSocket('ws://127.0.0.1:8082');
                conn.disabled = true;
                close.disabled = false;
                ws.onopen = function (e) {
                    console.log("连接服务器成功");
                    ws.send(input.value);
                    input.setAttribute("readOnly", 'true');
                    value1.setAttribute("readOnly", 'true');
                }
                ws.onmessage = function (e) {
                    value1.removeAttribute("readOnly");
                    var time = new Date();
                    mess.innerHTML += time.toUTCString() + ":" + e.data + "<br>";
                    document.getElementById("send").onclick = function (e) {
                        ws.send(input.value + "说:" + value1.value);
                        value1.value = " ";
                    }
                    document.onkeydown = function (e) {
                        e = e || window.event;
                        if (e.keyCode == 13) {
                            document.getElementById("send").onclick();
                            return false;
                        }
                    }
                }
                ws.onclose = function (e) {
                    console.log("服务器关闭");
                }
                ws.onerror = function () {
                    console.log("连接出错");
                }
 
                close.onclick = function () {
                    ws.onclose();
                    ws.send(input.value + 'close' + "了连接");
                    input.removeAttribute("readOnly");
                    conn.disabled = false;
                    close.disabled = true;
                }
            }
        }
    </script>
</body>
</html>
```
`server.js` 

```javascript
var ws = require("nodejs-websocket");
console.log("开始建立连接...");
var str1 = null, str2 = null, clientReady = false, serverReady = false;
var a = [];
var server = ws.createServer(function (conn) {
    conn.on('text', function (str) {
          a.push(str);
        if (!clientReady) {
            if (a[0] === str) {
                str1 = conn;
                clientReady = true;
                str1.sendText("欢迎你" + str);
                
            }
        } else if (!serverReady) {
            if (str.indexOf('close') &gt;= 0) {    
                     a.splice(2,1);
                     clientReady = false;
                     str1=null;   
                     return;
                }
            if (a[1] === str) {
                str2 = conn;
                serverReady = true;
                str2.sendText("欢迎你" + str);
                str1.sendText(str + "在线啦，你们可以聊天啦");
                return;
            } 
        } else if (clientReady &amp;&amp; serverReady) {
                str2.sendText(str);
                str1.sendText(str);
                if (str.indexOf('close') &gt;= 0) {
                    a.splice(2, a.length);
                    var len = a.length;
                    for (var i = 0; i &lt; len; i++) {
                        // 定位该元素位置
                        if (str.indexOf(a[i])&gt;=0) {     
                           a.splice(i,1);
                           if(i==0){
                               str1=str2;
                           }
                           serverReady = false; 
                           str2=null;
                           return;
                        }
                      
                    } 
                }
            }       
    })
    conn.on("close", function (code, reason) {
        console.log("关闭连接");
        clientReady = false;
        serverReady = false;
    })
    conn.on("error", function (code, reason) {
        console.log("异常关闭");
    });
}).listen(8082);
console.log("websocket连接完毕")
```
实现双人聊天，client.html开启两个窗口就行！ 
详细代码在[github](https://github.com/sulishibaobei/websocket)