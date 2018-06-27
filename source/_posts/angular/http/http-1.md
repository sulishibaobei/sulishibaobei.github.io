---
title :  Angular 4+ Http 
date : 2017-09-08 9:08:53
categories: "Angular 2+"
tag:
 - Angular 2+  
 - http
---

HTTP: 使应用能够对远端服务器发起相应的Http调用；
你要知道：HttpModule并不是Angular的核心模块，它是Angualr用来进行Web访问的一种可选方式，并位于一个名叫@angual/http的独立附属模块中；也就是说：使用http之前要引入此模块;
<!--more-->
## 1.基本使用：

```typescript
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import {HttpModule} from '@angular/http';  /*引入它*/
    import { AppComponent } from './app.component';
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        HttpModule  /*导入*/
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
```
注意，现在HttpModule已经是根模块AppModule的imports数组的一部分了;
## 2.介绍模拟web api 发送请求
注意：是在你没有真实的远程服务请求地址时，自己模拟一个web服务，用于测试代码；倘若有真实的远程服务器，则不需要这个；
### 2.1 引入InMemoryWebApiModule，
这是Angular提供的一个辅助服务，负责与远程服务器对话 — 替换成了内存 Web API服务，类似于后端服务；<b>import { InMemoryWebApiModule } from 'angular-in-memory-web-api';</b>
注意：导入这个，如果出现错误，可能是你的node-module 文件库里面没有此库，这时候打开你的package.json文件：<b>"dependencies"</b>里面配置这样一句：<b>"angular-in-memory-web-api": "^0.3.2"</b> 重新运行<b>npm install</b>;
### 2.2 向内存服务中注入数据，如果没有数据是不能运行的；
<b>import { InMemoryDataService }  from './in-memory-data.service';</b>
注意几点：

```javascript
    in-memory-data.service.ts 文件是你自己创建的，用来存放实例数据的；
    in-memory-data.service.ts 这样写：
    import { InMemoryDbService } from 'angular-in-memory-web-api';
    export class InMemoryDataService  implements InMemoryDbService{
       createDb() {
        const heroes = [
          { id: 0,  name: 'Zero' },
          { id: 1, name: 'Mr. Nice' },
          { id: 2, name: 'Narco' },
          { id: 3, name: 'Bombasto' },
        ];
        return {heroes};
      }
    }
```
这里相当于自己创建了一个内部数据库；
### 2.3导入的文件如何在app.module.ts中注入：

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import {HttpModule} from '@angular/http';
import { InMemoryWebApiModule } from 'angular-in-memory-web-api';
import { InMemoryDataService }  from './in-memory-data.service';
import { AppComponent } from './app.component';
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    HttpModule,
    InMemoryWebApiModule.forRoot(InMemoryDataService),  /*这样注入*/
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
这里现在就是全部代码；
## 3.如何发起http请求；
### 3.1创建一个服务；

```javascript
import {Injectable} from '@angular/core';
@Injectable()
export class HeroService{
}
```
angular 中所有创建服务的模式都是这样，我们创建了一个HeroService的文件，下载要在app.module.ts中注入它；

```javascript
import {HeroService} from './hero.service';
  providers: [HeroService],
  bootstrap: [AppComponent]
```
注意：
1.providers 是专门用来注入服务的；
2. 可以在app.module.ts里的providers中注入服务，也可以在你的子模块和组件中注入，
前者是共享的服务，一次注入，整个项目都有效，如果在子模块中或者子组件中注入，这只是你属于当前模块或当前组件下有效，其他的组件需要使用，都得自己重新注入；
一般情况下：返回的是一个能解析的promise(承诺)
### 3.2 引入http  ;

```javascript
import {Http} from '@angular/http';
constructor(private http:Http){ }
```
一定要在构造里创建它；
### 3.3 尝试获取内存数据库里面的信息；

```typescript
import {Injectable} from '@angular/core';
import {Http} from '@angular/http';
import 'rxjs/add/operator/toPromise';
import {Hero} from './hero';
@Injectable()
export class HeroService{
    constructor(private http:Http){ 
 /*注释1*/  private heroUrl='api/heroes';
 /*注释2*/     getHeroes(): Promise<Hero[]>{
                return this.http.get(this.heroUrl)
 /*注释3*/             .toPromise()
 /*注释4*/             .then(response =>response.json().data as Hero[])
/*注释5*/              .catch((error: any) => Promise.reject(error || "Server error"));
                          }
            }
```
现在我写了一个getHeroes()的方法：
解析几点：
注释1：http请求肯定需要Url地址，这里需要导入数据库里面的名；api/heroes 其实就是你内存数据库 web api里面`return {heroes};`
根据你返回的值，调用相应的api地址；
注释2：使用的是http承诺（promise），一定要有返回值，返回的是一个可以解析成英雄列表的Observable对象 ,也就是我们的Hero；也就是需要导入这个`import {Hero} from './hero';`
hero.ts文件包含：

```javascript
export class Hero{
    id:number;
    name:string;
}
```
Observable（可观察对象）是一个管理异步数据流的强力方式，后续会学习；
注释3：.toPromise会将我们得到的Observable对象转化成promise对象；
但是Angular中的observable对象并没有一个topromise操作符，这里就需要借助其他工具，`import 'rxjs/add/operator/toPromise';`
更多详细的可以看rxjs库操作符；
注释4：在 then 回调中提取出数据，这也是angular中一大特点，默认 JSON 解析,这个由json方法返回的对象只有一个data属性。 
这个data属性保存了英雄数组，这个数组才是调用者真正想要的。 所以我们取得这个数组，并且把它作为承诺的值进行解析 
注意：web api 返回的是带有data属性的对象，而你真是的api可以返回其他值的；
注释5：错误处理,有异常很正常，我们可以将他们传给错误处理器；现在我们只是将错误记录到控制台，真实案例中，可以将错误进行处理；
## 4.如何将获得的数据显示在界面；
### 4.1 组件中调用Hero.service.ts；

```javascript
import { Component } from '@angular/core';
import {HeroService} from './hero.service';
import {Hero}  from './hero';
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
 /*注释1*/ hero:Hero[];
 /*注释2*/ constructor(private service:HeroService){
             this.getHeroes();
            }
          getHeroes(){
/*注释3*/     this.service.getHeroes().then(
               hero=>{
                 this.hero=hero;
                  console.log(this.hero);
                    }
/*注释4*/     ).catch((error: any) => Promise.reject(error || 'client error'))
           }
}
```
注释1： 用来接收返回的对象；
注释2： 对引入的hero.service.ts服务初始化；
注释3： 返回的是promise对象，用then方法去解析得到的值；
注释4： 预期会犯错，抛出异常；
现在可以看看控制台打印的hero对象：
![http](/images/http-1.png)
如何显示在我们的html上呢？
```html
 <ul *ngFor="let a of hero">
   <li>{{a.id}} : {{a.name}}</li>
 </ul>
```
简单的写了这样一段：这里注意：多对数组或对象形式用*ngFor遍历；
运行localhost:4200,这样就可以让数据正常显示啦；
![http](/images/http-2.png)
这里介绍的是采用promise的形式调用http的方法，原理就是：hero.service中，http.get()返回的Observable后面串联了一个toPromise操作符。 该操作符把Observable转换成了Promise，并且我们把那个承诺返回给了调用者；
## 5.http promise还能做什么？
### 5.1 尝试通过id获取英雄；
hero.service.ts中添加这段代码：

```typescript
 getHero(id:number):Promise<Hero>{
         const url = `${this.heroUrl}/${id}`;
         return this.http.get(url)
         .toPromise()
         .then(response =>
                 response.json().data as Hero,  
          )
         .catch((error: any) => Promise.reject(error || 'Server error'));
     }
```
和获取所有英雄的方法类似，这个的步骤是先获取所有的英雄，并从中过滤出与id匹配的那一个；
匹配api/hero/:id 模式，有点像路由；
### 5.2 组件中如何调用；

```typescript
detailhero:Hero;
getHero(id:number){
    this.service.getHero(id).then(
       hero=>{
         this.detailhero=hero;
         console.log(this.detailhero);
        }
   ).catch((error: any) => Promise.reject(error || 'client error'))
  }
```
方法都类似，只是你需要传入想查询的id;
constructor中运行此方法，比如我想知道第二个英雄的详情：<code>this.getHero(2);</code>
看看控制台输出：
![http](/images/http-3.png)
看看html代码：<code><p *ngIf="detailhero">{{detailhero.name}}</p></code>
注意：单个对象用*ngIf ；
## 6.http promise还有什么请求方式？

```javascript
request(url: string | Request, options?: RequestOptionsArgs): Observable<Response>;
    /**
     * Performs a request with `get` http method.
     */
    get(url: string, options?: RequestOptionsArgs): Observable<Response>;
    /**
     * Performs a request with `post` http method
     */
    post(url: string, body: any, options?: RequestOptionsArgs): Observable<Response>;
    /**
     * Performs a request with `put` http method.
     */
    put(url: string, body: any, options?: RequestOptionsArgs): Observable<Response>;
    /**
     * Performs a request with `delete` http method.
     */
    delete(url: string, options?: RequestOptionsArgs): Observable<Response>;
    /**
     * Performs a request with `patch` http method.
     */
    patch(url: string, body: any, options?: RequestOptionsArgs): Observable<Response>;
    /**
     * Performs a request with `head` http method.
     */
    head(url: string, options?: RequestOptionsArgs): Observable<Response>;
    /**
     * Performs a request with `options` http method.
     */
    options(url: string, options?: RequestOptionsArgs): Observable<Response>;
```
### 6.1 我们来添加一个英雄；
hero.service.ts中加入这一段：

```javascript
/*注释1*/ private headers = new Headers({'Content-Type': 'application/json'});
      addHero(name:string):Promise<Hero>{
          return this.http.post(this.heroUrl,JSON.stringify({name:name}),{headers:this.headers})
          .toPromise()
          .then(
              res=>
                  res.json().data as Hero
          )
          .catch((error: any) => Promise.reject(error || 'Server error'));
      }
```
注释1：

```typescript
import {Http,Headers} from '@angular/http';
```
需要引入Headers，然后new 一个实例；
但是这个参数是可选的；
组件中怎么接收：

```javascript
addhero:Hero;
  addHero(name:string){
    this.service.addHero(name).then(
        hero=>{
          this.addhero=hero;
          console.log(this.addhero);
          this.hero.push(this.addhero);  /*将添加的数据Push进hero数组*/
        }
    ).catch((error: any) => Promise.reject(error || 'client error'))
  }
```
一样的代码，就不过多解释；
我们的英雄在哪里添加呢？
肯定是要有一个input输入框的：

```html
 <div>
     add:<input #heroName/>
     <button (click)="addHero(heroName.value);heroName.value=''">add</button>
 </div>
```
添加之后，可以看到控制台会有输出：
![http](/images/http-4.png)
但是界面显示的还是这些：
![http](/images/http-5.png)
Angular会自动检测数据的变换，当有数据变换时，会实时更新
还有一些其他方法，自己去尝试下；
hero.service.ts:

```javascript
import { Injectable } from '@angular/core';
import { Http, Headers,Response} from '@angular/http';
import 'rxjs/add/operator/toPromise';
import { Hero } from './hero';
@Injectable()
export class HeroService {
    constructor(private http: Http) { }
    private heroUrl = 'api/heroes';
    getHeroes(): Promise<Hero[]> {
        return this.http.get(this.heroUrl)
            .toPromise()
            .then( this.extractData )  /*注释1*/
            .catch((error: any) => Promise.reject(error || 'Server error'));/*注释2*/
    }

    getHero(id: number): Promise<Hero> {
        const url = `${this.heroUrl}/${id}`;
        return this.http.get(url)
            .toPromise()
            .then(this.extractData)
            .catch(this.handleError);
    }
    private headers = new Headers({ 'Content-Type': 'application/json' });
    addHero(name: string): Promise<Hero> {
        return this.http.post(this.heroUrl, JSON.stringify({ name: name }), { headers: this.headers })
            .toPromise()
            .then(this.extractData)
            .catch(this.handleError);
    }

   /**
    *
    * @param res 接收数据的方法
    */
    private extractData(res: Response)  {  /*注释2*/
        let body = res.json();
        return body.data|| {}; /*注释3*/
    }

    /**
     * 
     * @param error 处理错误的方法
     */
    private handleError(error: Response | any) {
        return Promise.reject(error || 'Server error');
    }
}
```
注释1：将then()和catch()里面的部分提取出来，每次使用只需要调用方法即可；
注释2：接收的Response记得将Response在Http对象中引入；
注释3：这里接收的data其实是一个any型;

## 7. Observable对象
Http服务中的每个方法都返回一个 HTTP Response对象的Observable实例；
Observable 就是一个拥有以下特性的函数：
    它接收一个 observer 对象作为参数，该对象中包含 next、error 和 complete 方法
    它返回一个函数，用于在销毁 Observable 时，执行清理操作
在 RxJS 中，返回的是 Subcription 对象，该对象中包含一个 unsubscribe 方法。
一个 Observable 对象设置观察者 (observer)，并将它与生产者关联起来。该生产者可能是 DOM 元素产生的 click 或 input 事件，也可能是更复杂的事件，如 HTTP。
当 Observable 对象产生新值的时候，我们可以通过调用 next() 方法来通知对应的观察者。若出现异常，则会调用观察者的 error() 方法。当我们订阅 Observable 对象后，只要有新的值，都会通知对应的观察者。但在以下两种情况下，新的值不会再通知对应的观察者：已调用 observer 对象的 complete() 方法消费者对数据不再感兴趣，执行取消订阅操作
此外在执行最终的 subscribe() 订阅操作前，我们传递的值可以经过一系列的链式处理操作。执行对应操作的东西叫操作符，每个操作符执行完后会返回一个新的 Observable 对象，然后继续我们的处理流程。
那么我们可以直接返回Observable给组件吗？
我们可以看看，之前getHero()那一段如果直接改成返回Observable对象会如何；
```javascript
getHeroes():Observable<Hero[]>{  /*注释1*/
        return this.http.get(this.heroUrl)
        .map(this.extractData); 
    }
```
注释1：不用调用toPromise()方法，自然也不用then()去取数据，而是直接返回Observable对象之后
调用RXJS中的map操作符来从返回的数据中提取数据；
所以你要引入：
```javascript
import { Observable }  from 'rxjs/Observable';
import 'rxjs/add/operator/map';
```
操作符之后会讲到，注意和之前的promise方法对比；
组件中怎么接收数据Observable对象呢？
```javascript
 getHeroes(){
    this.service.getHeroes().subscribe(    /*注释1*/
      hero=>{
        this.hero=hero;
        console.log(this.hero);
      }
    )
  }
```
这样显示的效果也是一样的；
注释1：调用 subscribe() 方法，来获取最终的值，采取订阅的方式，而不是用then();
## 8. Observabel和Promise 有什么不同
### 8.1 两个都可以用于web API http的调用，但Observable是可以中途取消的，而Promise一旦触发就不能被取消；
```javascript
 setTimeout(() => {
        this.service.getHero().unsubscribe();
      }, 1000);
```
### 8.2 因为Promise只能发起一次请求，只要接收到数据，就会算完成，所以Promise只能发射一个值就算结束，Observable可以连续发送好几个值，在服务器对第一个请求作出回应之前，再开始另一个不同的请求；

```javascript
this.heroes = this.searchTerms
    .debounceTime(300)        
    .distinctUntilChanged()  
    .switchMap(term => term   
      ? this.service.search(term)
      : Observable.of<Hero[]>([]))
    .catch(error => {
      console.log(error);
      return Observable.of<Hero[]>([]);
    });
this.heroes.subscribe(value => console.log(value));
```
这个后面代码操作符这块有详解；
### 8.3 Observable提供了很多的工具函数，最最最常用的filter和map；详细看看这两个map和filter；
 map() 操作符返回一个新的 Observable 对象
 filter() 操作符执行过滤操作，然后又返回一个新的 Observable 对象
最后我们通过调用 subscribe() 方法，来获取最终的值
Filter:过滤器
```html
getHeroes(){
    this.service.getHeroes().filter(hero => hero.length >= 2)
    .subscribe(
        hero=>{
           this.hero=hero;
        }
    )
  }
```
Map的用法上面有介绍；
但是，使用承诺的方式能让调用方法更容易写，并且承诺已经在 JavaScript 程序员中被广泛接受了。
再来看看Observable操作符：
根据名字搜索用户：
```html
search(term: string): Observable<Hero[]> {
        return this.http
               .get(`api/heroes/?name=${term}`)
               .map(response => response.json().data as Hero[]);
    }
```
 看看组件代码：
```html
 import { Observable } from 'rxjs/Observable';  /*注释1*/
import { Subject }  from 'rxjs/Subject';  /*注释2*/
// Observable class extensions
import 'rxjs/add/observable/of';  /*注释3*/
// Observable operators
import 'rxjs/add/operator/catch';  /*注释4*/
import 'rxjs/add/operator/debounceTime';  /*注释5*/
import 'rxjs/add/operator/distinctUntilChanged';  /*注释6*/
import 'rxjs/add/operator/switchMap'; /*注释6*/
```
引入这些操作符：
注释1：引入RXJS的可观察对象；
注释2：Suject是一个可观察事件流中的生产者，通过调用Next()对象，可以将新观察的对象放入可观察流中；
注释3：是Rxjs为Observable对象提供的扩展，
注释4：异常操作符，拦截失败的可观察对象。
注释5：相当于settimeout(),延迟；
注释6：distinctUntilChanged确保只在过滤条件变化时才发送请求， 这样就不会重复请求同一个搜索词了；
注释7：switchMap()会为每个从debounce和distinctUntilChanged中通过的搜索词调用搜索服务。 它会取消并丢弃以前的搜索可观察对象，只保留最近的；使用这个每触发一次事件都会引起一次Http()的调用，即使你使用延迟，但同一时间还是会有多个Http请求，并且返回的顺序不一定是请求发起的顺序；
看看Http()方法调用：
```typescript
 heroes: Observable<Hero[]>;
  private searchTerms=new Subject<string>();   // searchTerms生成一个产生字符串的Observable
  /*每当调用search()时都会调用next()来把新的字符串放进该主题的可观察流中*/
  search(term:string):void{
    this.searchTerms.next(term);
  }
   ngOnInit(): void {
     this.heroes = this.searchTerms
    .debounceTime(300)        
    .distinctUntilChanged()  
    .switchMap(term => term   
      ? this.service.search(term)
      : Observable.of<Hero[]>([]))
    .catch(error => {
      console.log(error);
      return Observable.of<Hero[]>([]);
    });
}
```
看看代码：
```html
 <div>
  search :<input #searchBox (keyup)="search(searchBox.value)"/>
     <div  *ngFor="let b of heroes | async">
        {{b.name}}
    </div>
</div>
```
为什么使用一个async管道呢？因为得到的是Observable对象，不是数组，*ngFor不能遍历可观察对象；
效果：
![http](/images/http-6.png)
看完这个，大概知道：
Observable（可观察对象）是基于推送（Push）运行时执行（lazy）的多值集合；
关于操作符，我有话要说：
大部分RxJS操作符都不包括在Angular的Observable基本实现中，基本实现只包括Angular本身所需的功能，也就是，你需要的大多操作符都需要从rxjs中引入；
## 9.上述的例子，其实说的都是关于web api,内存服务器的，占用内存的东西，速率都有点慢，如果你要从本地获取文件记住不要用这个；
可以换用json文件获取：
 创建一个heroes.json，记得放在assets文件下： 
{
  "data": [
    { "id": 1, "name": "Windstorm" },
    { "id": 2, "name": "Bombasto" },
    { "id": 3, "name": "Magneta" },
    { "id": 4, "name": "Tornado" }
  ]
}
我们app.module.ts里面的：
   `InMemoryWebApiModule.forRoot(InMemoryDataService),`
不用引入，引入之后会默认使用web api；
然后hero.service.ts中这样
private heroesUrl = 'assets/heroes.json';
其他的都是一样的；
这种方式对于请求静态的本地数据是比较有用的；如果是请求真是的远程服务器地址，url换成远程地址就可以；

 

 

 