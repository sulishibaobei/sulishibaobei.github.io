---
title :  Angular 4+ HttpClient 
date : 2017-09-08 9:08:53
categories: "Angular 2+"
tag:
 - Angular 2+  
 - httpClient
---

Angular 4.3.0-rc.0 版本已经发布🐦。在这个版本中，我们等到了一个令人兴奋的新功能 - HTTPClient API 的改进版本；
HttpClient 是已有 Angular HTTP API 的演进，它在一个单独的 @angular/common/http 包中。这是为了确保现有的代码库可以缓慢迁移到新的 API；
大多数前端应用都需要通过 HTTP 协议与后端服务器通讯。现代浏览器支持使用两种不同的 API 发起 HTTP 请求：XMLHttpRequest 接口和 fetch() API；
<!--more-->
@angular/common/http中的HttpClient类，Angular 为应用程序提供了一个简化的 API 来实现 HTTP 功能。它基于浏览器提供的XMLHttpRequest接口。 HttpClient带来的其它优点包括：可测试性、强类型的请求和响应对象、发起请求与接收响应时的拦截器支持，以及更好的、基于可观察（Observable）对象的错误处理机制；
1.如何使用httpClient;

```typescript
import {NgModule} from '@angular/core';
import {BrowserModule} from '@angular/platform-browser';
import {HttpClientModule} from '@angular/common/http';
@NgModule({
  imports: [
    BrowserModule,
    // Include it under 'imports' in your application module
    // after BrowserModule.
    HttpClientModule,
  ],
})
export class MyAppModule {}
```
注意：导入方式和HttpModule一样的，记得要在BrowserModule后面导入；区别是：在@angular/common/http 模块里面，而不是@angular/http中
这样我们可以使用这些啦：

```typescript
class HttpClient {
  request(first: string|HttpRequest<any>, url?: string, options: {...}): Observable<any>
  delete(url: string, options: {...}): Observable<any>
  get(url: string, options: {...}): Observable<any>
  head(url: string, options: {...}): Observable<any>
  jsonp<T>(url: string, callbackParam: string): Observable<T>
  options(url: string, options: {...}): Observable<any>
  patch(url: string, body: any|null, options: {...}): Observable<any>
  post(url: string, body: any|null, options: {...}): Observable<any>
  put(url: string, body: any|null, options: {...}): Observable<any>
}   
```
2.发起一个请求来获取Json数据；
首先创建一个json文件，其实就是上一篇的同一个json文件：
{
  "data": [
    { "id": 1, "name": "Windstorm" },
    { "id": 2, "name": "Bombasto" },
    { "id": 3, "name": "Magneta" },
    { "id": 4, "name": "Tornado" }
  ]
}

怎么调用Http请求呢？
2.1 app.componment.ts;

```typescript
import { Component,OnInit } from '@angular/core';
import {HttpClient} from '@angular/common/http';  /*注释1*/
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent  implements OnInit {
constructor(private http:HttpClient){} 
 datas:string[];
  ngOnInit(){
    this.http.get('./assets/heroes.json').subscribe(data=>{  /*注释2*/
         this.datas=data['data'];
         console.log(this.datas)
    }) 
  }
}
```
注意：
注释1：组件中引入的是HttpClient,也是在单独的库，@angular/common/http中;之前的项目里面引入的一直都是http，这就是改版后的区别；
注释2：现在 JSON 是默认的数据格式，我们不需要再进行显式的解析；this.http.get('xx').subscribe();之前的还需要res.json()去手动转；
还有一点要注意：我并没有去hero.service.ts里面去操作了，而是直接在组件中发请求获取数据，这样更加简单明了；
```html
<div>
  <ul  *ngFor="let data of datas">
    <li>{{data.id}}  {{data.name}}</li>
  </ul>
</div>
```
html代码还是一样简单，并没有改变；
这是相比较之前的Http简单的方式；

```typescript
  get(url: string, options: {
    headers?: HttpHeaders,
    observe?: HttpObserve,
    params?: HttpParams,
    reportProgress?: boolean,
    responseType?: 'arraybuffer'|'blob'|'json'|'text',
    withCredentials?: boolean,
  }): Observable<any> 
```
这是get方法详解，请注意，返回的都是observable对象，而不是使用promise了；
3.响应体的检查
3.1上面的代码中：

```typescript
 datas:string[];   //定义一个接口来描述这个类型的正确形态
this.datas=data['data']; /*注释1*/
```
注释1：我们取json对象data值时要data['data']，而不是之前的data.data了；如果改成：
 `this.datas=data.data;`
会报错：`does not exist on type 'Object`
那是因为HttpClient把 JSON 格式的响应体解析成了一个Object，它并不知道这个对象的形态应该是什么。所以我们应该告诉你需要取得data是个什么类型；这也是更符合typescript语法的做法；
4.如果将要获取的不是JSON文件如何？
```typescript
 this.http.get('/assets/1.txt',{responseType:'text'}).subscribe(
      data=>console.log(data)
)
```
注意：这样我们就要指定需要获取的文本格式；
控制台将会打印我的1.txt中的内容：www.sulishibaobei.com
 5.如何带参查询数据

```typescript
  const params = new HttpParams()
    .set('id', '1').set('name','Windstorm')
    this.cus=this.http.get('assets/heroes.json',{params})
    .do(console.log)
    .map(data=>
        console.log( _.values(data))
    );
```
引入：httpParams参数：
`import {HttpClient, HttpParams} from '@angular/common/http';`
我们通过链式语法调用set()方法，构建了params对象；每当set()方法被调用时，都会包含新的值进来，并且防止之前对象不被修改；
http://localhost:4200/assets/heroes.json?id=1&name=Windstorm
请求的链接地址将会是这样的形式；这个有点像http里添加url参数；
还可以设置Headers；
`const headers = new HttpHeaders().set("X-CustomHeader", "custom header value");`
还有其他的请求，之后会逐一分析；
另外还有几点比较重要的：多行并发发送请求和顺序发送请求，避免发送重复请求； 

```typescript
import 'rxjs/add/operator/shareReplay';
const httpGet = this.http
    .get("assets/heroes.json")
    .map(data => _.values(data))
    .shareReplay();
```
这样即使你将 httpGet再赋值给另一个变量，或重复调用也不会再次请求了
并行发送 HTTP 请求的一种方法是使用 RxJs 中的 forkjoin 操作符：

```typescript
import 'rxjs/add/observable/forkJoin';
Requests() {
    const result = Observable.forkJoin(
        this.http.get('/assets/heroes.json'),
        this.http.get('/assets/heroes.json')
    );

   result.subscribe(
        values => {
            console.log("all values", values)
        }
    );
}
```

顺序发送请求

```typescript
sequentialRequests() {
    const sequence$ = this.http.get<Hero>('/assets/heroes.json')
        .switchMap(hero => {
            hero.id+= ' - TEST ';
            return this.http.put('/assets/heroes.json', hero)
        });
        
    sequence$.subscribe();
}
```
6.拦截器  （拦截所有的请求和响应这也是@angular/common/http的核心特性之一）
它能声明一些拦截器，拦在应用和后端之间。当应用程序发起一个请求时，拦截器可以在请求被发往服务器之前先转换这个请求。并且在应用看到服务器发回来的响应之前，转换这个响应。这对于处理包括认证和记录日志在内的一系列工作都非常有用。
6.1 如何写一个拦截器

```typescript
import {Injectable} from '@angular/core';
import {HttpEvent, HttpInterceptor, HttpHandler, HttpRequest, HttpResponse} from '@angular/common/http';
import {Observable} from 'Rxjs/Observable';

@Injectable()
export class NoopInterceptor implements HttpInterceptor {
   intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
       return next.handle(req).map(event => {
        if (event instanceof HttpResponse) {
          if (event.status === 401) {
            // JWT expired, go to login
          }
        }
        return event;
      }
   } 
}
```
1.intercept是一个方法，它把一个请求对象转换成一个返回这个响应的可观察对象（Observable）。从这个意义上说，每个拦截器都要完全自己处理这个请求；
2.响应拦截器可以通过在 next.handle(req) 返回的流对象 (即 Observable 对象) 上应用附加的 Rx 操作符来转换响应事件流对象；
内部可以做自己的处理操作；
但是此时拦截器还未使用在组件上；

```typescript
import {NgModule} from '@angular/core';
import {HTTP_INTERCEPTORS} from '@angular/common/http';
@NgModule({
  providers: [{
    provide: HTTP_INTERCEPTORS,
    useClass: NoopInterceptor,
    multi: true,
  }],
})
export class AppModule {}
```
providers里面配置我们的信息；
注意multi: true选项。这是必须的，因为它会告诉 Angular 这个 HTTP_INTERCEPTORS 表示的是一个数组，而不是单个的值；