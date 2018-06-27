---
title :  Angular 2+ 路由的那些事 
date : 2017-09-01 11:08:53
categories: "Angular 2+"
tag:
 - Angular 2+  
 - router
 - 懒加载
---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;路由的作用就是（导航）：会加载与请求路由相关联的组件，并获取特定路由的相关数据，这允许我们通过控制不同的路由，获取不同的数据，从而渲染不同的页面；


## 几种常见的路由配置：
Angular路由器是一个可选的外部Angular NgModule ，叫RouterModule;
路由器里面包含多种服务(RouterModule)，多种指令（RouterOutlet，RouterLink,RouterLinkActive），和一套配置（Routes）;

<!--more-->
```typescript
    import { RouterModule} from '@angular/router';
     RouterModule.forRoot([
           {
             path: 'test',
             component: TestComponent
           }
     ]) 
    <a routerLink="test">Test</a> 
    <router-outlet></router-outlet>
```
### 详细解析：
#### 路由定义包括下面部分：
- [1]  Path:路由器会用它来匹配浏览器地址栏中的地址，如’test’; 
- [2]  Component:导航到此路由时，需要加载的组件；
**<font color=#0099ff>注意，path不能以斜杠（/）开头。 路由器会为解析和构建最终的URL，这样当我们在应用的多个视图之间导航时，可以任意使用相对路径和绝对路径。</font>**

另外：
1. 这里用到了RouterModule对象为我们提供的两个静态方法：forRoot()和forChild() 来配置路由信息；
2. forRoot()方法提供了路由需要的路由服务提供商和指令，并基于当前浏览器 URL 初始化导航；用于在模块中定义主要的路由信息，通过调用该方法使得我们的主模块可以访问路由模块中定义的所有指令；
3. a标签中的routerLink 指令绑定一个字符串，字符串是path路径中配置的字符串，它将告诉路由器，当用户点击这个链接时，应该导航到哪里；
4. 当然routerLink还可以绑定一个数组，就是我们的带参路由,下面会具体介绍的：
```html
  <a [routerLink]="['/test', id]">test</a>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;还可以在上面这样配置添加一个routerLinkActive指令, 我们需要让用户知道哪个路由处于激活状态，通常情况下我们通过向激活的链接添加一个 class 来实现该功能,然后我们写一个active的类:
```html
<a routerLink="test" routerLinkActive="active">test</a>
   .active{
       color：red
      }
```
&nbsp;&nbsp;&nbsp;&nbsp;当此路由被点击时，字体会变成红色；这也是routerLinkActive的作用，使我们知道哪个路由处于激活状态；当然还可以添加上这个[routerLinkActiveOptions]="{exact: true}"  只有当路由完全一样时，才会将active类加载上去：
```html
      <a routerLink="dashboard" routerLinkActive="active"  [routerLinkActiveOptions]="{exact: true}">Dashboard</a>
```
&nbsp;&nbsp;&nbsp;&nbsp;chrome控制台这样显示：
![cmd-markdown-logo](/images/router-1.png)
&nbsp;&nbsp;&nbsp;&nbsp;可见routerLink还是会自动将路由链接到href上的；class="active“也作用上去啦；当切换路由时：
![cmd-markdown-logo](/images/router-2.png)
<code>class="active”</code> 移到我点击的路由上，只是应该是调用了：ngAfterContentInit(),ngOnChanges(),ngOnDestroy()

注意：第一个路径片段可以以 / ，./ 或 ../ 开头：
  如果以 / 开头，路由将从根路由开始查找
  如果以 ./ 开头或没有使用 / ，则路由将从当前激活路由的子路由开始查找
  如果以 ../ 开头，路由往上一级查找 
```html
   <a [routerLink]="['../test', id]">test</a>
```
**当然这里我们也可以通过在component里控制写:**
```typescript
  import {Router} from '@angular/router';
  <a (click)="go()">Heroes</a>
  constructor(private router: Router) {}
  go() {
    this.router.navigate(['heroes']);
  }
```
这种效果也是一样的；这里就需要注入router服务：router方法用于切换路由很有用，下面会具体来介绍router服务的；
   路由出口:RouterOutlet是由RouterModule提供的指令之一。当我们在应用中导航时，路由器就把激活的组件显示在`</router-outlet>` 里面。不写
```html
     <router-outlet></router-outlet>
```
会导致组件内容不加载进来，从而不显示内容；
但是一个组件可以共用一个routeroutlet，所以app.component.ts里面配置即可：
```html
    <router-outlet></router-outlet>
```
  

## 第二种写法：
请看代码：
```typescript
   RouterModule.forRoot([...]) 将[] 及中间的内容当成配置文件提取出去；
   RouterModule.forRoot(routes),
   routes是我们需要导入的配置文件参数名：
   import { routes} from './app-routing.module';
   app-routing.module:中我们可以这样写：
   import { DashboardComponent } from './dashboard/dashboard.component';
   import { HeroesComponent } from './hero/heroes.component';
   import { HeroDetailComponent } from './detail/hero-detail.component';
   export const routes = [
     { path: '', redirectTo: '/dashboard', pathMatch: 'full' },
     { path: 'dashboard',  component: DashboardComponent },
     { path: 'detail/:id', component: HeroDetailComponent },
     { path: 'heroes',     component: HeroesComponent },  { path: '**', component:DashboardComponent}];
```

**注意：**
 **{ path: '', redirectTo: '/dashboard', pathMatch: 'full' }**
   表示重定向路由：需要一个pathMatch属性，告诉路由器是如何用URL去匹配路由的路径的，没有这个属性就会报错；意思就是当路由URL等于’’时，会去加载DashboardComponent组件；所以你运行你的服务端口号：localhost:4200首先加载的就会是这个组件；
*<font color=#000000>{ path: "**", component:DashboardComponent}</font>*
   **路径是一个通配符，表示除了以上几种path,任何的路径都会加载DashboardComponent组件，这个记得写在路由配置最后

###  当然这种方式我们还能这么写:
1.  无declarations！声明是关联模块的重点。我们将AppRoutingModule抛出去，当做一个路由模块，
```javascript
  import { NgModule } from '@angular/core';
  import { Routes,RouterModule} from '@angular/router';
  import { DashboardComponent } from './dashboard/dashboard.component';
  import { HeroesComponent } from './hero/heroes.component';
  const routes = [
    { path: '', redirectTo: '/dashboard', pathMatch: 'full' },
    { path: 'dashboard',  component: DashboardComponent },
    { path: 'heroes',     component: HeroesComponent }
  ];
  @NgModule({
    imports: [ RouterModule.forChild(routes) ],
    exports: [ RouterModule ]
  })
  export class AppRoutingModule {}
```
2. app.module.ts中引入：
```typescript
   import { AppRoutingModule} from './app-routing.module';
```
3. imports:中导入这个就可以啦**AppRoutingModule**
这种用法和上面这种用法是一样的还有一点：如何解决第二次刷新出现404问题：
```typescript
   [RouterModule.forRoot(routes,{useHash:true})]
```
配置后面这一点，通过添加一个#，防止刷新第二次出现404；*http://localhost:4201/#*
RouterModule.forChild(routes)写在子模块里面，而且这边用的是forChild(),不是forRoot（）,使用forRoot()也是不会错的，但是一般情况下
<code><font color=red>根模块中使用forRoot()，子模块中使用forChild()</font></code>
   forChild()只能用在特性模块中，这样的一点好处就是我们不必在主模块就配置好所有的路由信息，在需要的时候将他们导入主模块；

### 参数化路由
```typescript
{ path: 'detail/:id', component: HeroDetailComponent },
```
配置参数化路由格式： :id 是一个占位符，当导航到HeroDetailCompnent组件上时，会被填入一个特定的id；
**这里我们是这样绑定的：**
```html
<a *ngFor="let hero of heroes"  [routerLink]="['/detail', hero.id]"  class="col-1-4"></a>
```
<font color=red>eg: http://localhost:4201/detail/11 这时的id等于11；</font>
- [1]传参类型的id作用可以根据传入的id不同让HeroDetailComponent显示不同的内容；
- [2]但是怎么能让其显示不同的内容呢？ 也就和我们这个id有关系，如何获取这个id 用在我们的组件里面呢？
- [3]通过注入ActivatedRoute服务，一站式获取路由信息；
```typescript
import { ActivatedRoute} from '@angular/router';
 constructor(
    private route: ActivatedRoute,
     ) {}
```
**接下来我们这样试试：**
```typescript
public params;
this.route.params.subscribe(
      params => {
         this.params = params;
         console.log(this.params);
      }
    );
```
*这样获取来的是一个对象：直接取id就能获取到了；既然是一站式获取，肯定不止这几个功能 后面会具体介绍它：*
### 路由配置是也可以通过子路由来配置children：
```typescript
{
    path: 'heroes',
    component: HeroesComponent,
    children: [
      { path: 'heroTest', component: HeroTestComponent },
    ]
   }
```
是这样配置的；此时HeroTestComponent组件的路由其实是:’heroes/heroTest’;
### 懒加载loadChildren:
```typescript
{
     path:'loadtest',
     loadChildren:'./loadtest/loadtest.module#LoadtestModule'
   }
```
**路由是这样配置的**：

- [1]这里注意几点：
```typescript
import { LoadtestComponent } from './loadtest/loadtest.component';
```
组件不需要在app.module.ts引入

- [2]loadtest.module.ts 也不需要在app.module.ts中引入；而是通过loadchildren属性，在需要的时候告诉Angular路由依据loadchildren
 属性配置的路径去加载LoadtestModule模块，这就是模块懒加载功能；当用户需要的时候才回去加载，大大减少了应用启动时的资源加载大小；

- [3] loadChildren后面的字符串由三部分组成：
(3.1) 需要导入模块路劲的相对路径
(3.2) #分隔符
(3.3) 导出模块类的名称

- [4]还有一点也是也是重要的：loadtestModule代码是这样的：里面要引入自己的路由；
```typescript
import { NgModule } from '@angular/core';
import {CommonModule} from '@angular/common';
import { LoadtestComponent } from './loadtest.component';
import {RouterModule} from '@angular/Router';
import {route} from './loadtest-routing.module';
@NgModule({
    imports:[
        CommonModule,
        RouterModule.forChild(route),
    ],
    declarations:[
        LoadtestComponent
    ]
})
export class LoadtestModule{}
```
在route路由里面记得这样配置这样一句才不会出错：
```typescript
import { LoadtestComponent } from './loadtest.component';
export const route = [
    {
       path:'',
        component: LoadtestComponent
    },
]
```
<font color=red>path:’’,才能保证代码不出错；</font>
懒加载的文件要注意：app.module.ts中：
```typescript
declarations: [
   AppComponent,
   DashboardComponent,
   HeroDetailComponent,
   HeroesComponent,
   TestComponent,
 ],
 ```
这里面的文件，采用懒在家的模块是引用不到得，因为lazy加载文件有自己的ngModule ，如果要使用的组件是同一个，最好建立一个shareModule模块；
采用commonModule 将共享文件放进去，之后的Module里使用再加载进imports中；

## Router服务：
```typescript
 class Router{
2.         errorHandler:ErrorHandler
3.         navigated: boolean
4.         urlHandlingStrategy:UrlHandlingStrategy
5.         routeReuseStrategy:RouteReuseStrategy
6.         config:Routes
7.        initialNavigation():void
8.        setUpLocationChangeListener():void
9.        get routerState():RouterState
10.       get url(): string
11.       get events():Observable<Event>
12.       resetConfig(config:Routes):void
13.       ngOnDestroy():void
14.       dispose():void
15.       createUrlTree(commands: any[], navigationExtras:NavigationExtras):UrlTree
16.       navigateByUrl(url: string|UrlTree, extras:NavigationExtras):Promise<boolean>
17.       navigate(commands: any[], extras:NavigationExtras):Promise<boolean>
18.       serializeUrl(url:UrlTree): string
19.       parseUrl(url: string):UrlTree
20.       isActive(url: string|UrlTree, exact: boolean): boolean
21.       }
```
这是Router API为我们提供的方法和属性；

看看几个常用的：
###  `navigate()` 
该方法支持的参数类型和routerLink指令一样，所以他们的作用也是一样的：
```typescript	
this.router.navigate(['test', id]);
```
**或者**
```typescript
this.router.navigate(['test']);
```
> 调用该方法后页面会自动跳转到对应的路由地址；
```typescript
this.router.navigate(['test'], { relativeTo: this.route});
```
我们可以设置一个参照路径，参照路径this.route从ActivatedRoute里面取；配置这个可以让自己知道相对于什么位置导航，this.route就是相对于当前的路由进行导航，
假如当前url：localhost:4200/hero ,那么导航后的结果就是：localhost:4200/hero/test
### navigateByUrl()
 这个叫做绝对路由；
```typescript
this.router.navigateByUrl('home');
```
可以帮助你快速的切换到某个路由下面，如果你当时的路由是这样的：
localhost:4200/hero/test 点击这个路由后就是：localhost:4200/home 我们一般用这个路由来回到首页；
和navigate()的区别还有点是：这个不是根据参数来确定路由地址的
### config 会将页面所有的路由配置信息都显示：
![cmd-markdown-logo](/images/router-3.png)
看看路由树：
![cmd-markdown-logo](/images/router-4.png)
### url 输出当前 的路由path
<code>
eg：http://localhost:4200/detail/11
url: /detail/11
</code>
### 每次导航前都会调用events方法；
```typescript
RouterModule.forRoot(routes, {enableTracing: true }) 
```
通过在控制台配置enableTracing: true可以在控制台看到相关改变；
 **注意：enableTracing: true 只能在forRoot()里面添加**
具体的事件有：
![cmd-markdown-logo](/images/router-5.png)
 chrome控制台:
![cmd-markdown-logo](/images/router-6.png)
**注意：这些事件是以Observable的形式提供的**

## ActivateRoute API :
```typescript
interface ActivatedRoute {
snapshot: ActivatedRouteSnapshot
url: Observable<UrlSegment[]>
params: Observable<Params>
queryParams: Observable<Params>
fragment: Observable<string>
data: Observable<Data>
outlet: string
component: Type<any>|string|null
get routeConfig(): Route|null
get root(): ActivatedRoute
get parent(): ActivatedRoute|null
get firstChild(): ActivatedRoute|null
get children(): ActivatedRoute[]
get pathFromRoot(): ActivatedRoute[]
get paramMap(): Observable<ParamMap>
get queryParamMap(): Observable<ParamMap>
toString(): string
}
```
### parmaMap
```java
第一步：import { Router, ActivatedRoute, ParamMap } from '@angular/router';
第二步：import 'rxjs/add/operator/switchMap';导入switchMap操作符是因为我们稍后将会处理路由参数的可观察对象Observable ;会在以后的章节中介绍操作符的；
第三步：
constructor(
 private heroService: HeroService,
 private route: ActivatedRoute,
 private router: Router,
  ) {}
<code> <br></code>
```
假定事先写好了HeroService：

```typescript
this.route.paramMap
   .switchMap((params: ParamMap) => this.heroService.getHero(+params.get('id')))
    .subscribe(hero => this.hero = hero );
  }
```
我们这样操作，前面已经介绍过用parmas获取参数；所以这样写也可以，用的是paramMap就引入paramMap，params就引入Params

 ```typescript
 this.route.params
      .switchMap((params: Params) => this.heroService.getHero(+params['id']))
      .subscribe(hero =>
        this.hero = hero;
      }
      );
```
由于参数是作为Observable提供的，所以我们得用switchMap操作符来根据名字取得id参数，并告诉HeroService来获取带有那个id的英雄。
### snapshot(快照) 
route.snapshot提供了路由参数的初始值。 我们可以通过它来直接访问参数，而不用订阅或者添加Observable的操作符
所以获取参数的id还可以这样：
```html	
<a *ngFor="let hero of heroes"  [routerLink]="['/detail', hero.id]"  class="col-1-4"></a>
 <br>this.params = this.route.snapshot.paramMap.get('id');<br><br>console.log(this.params);
```
 所以上面的代码改成这样更好：

 ```typescript
 this.params = this.route.snapshot.paramMap.get('id');
   console.log(this.params);
 this.heroService.getHero(this.params)
   .then(hero => this.hero = hero);
```
两种方法：params 和snapshot到底什么时候该用哪种呢？

1. 需要直接访问参数，主要获取初始值，不用订阅的情况下用snapshot；
2. 需要连续导航多次的用params;

## 总结 ,路由主要是用到了这些方面啦：
 ![cmd-markdown-logo](/images/router-7.png)
## 给路由添加一些新特性：
### 添加动画

[1] 在app.module.ts中引入启用Angular动画必备的, 记得在imports中导入：
```typescript
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
```
[2] 在app.component.ts同级下创建一个animation.ts文件，用来存放我们的动画效果；
 ```typescript
 import { animate, AnimationEntryMetadata, state, style, transition, trigger } from '@angular/core';
export const slideInDownAnimation: AnimationEntryMetadata =
  trigger('routeAnimation', [
    state('*',
      style({
        opacity: 1,
        transform: 'translateX(0)'
      })
    ),
    transition(':enter', [
      style({
        opacity: 0,
        transform: 'translateX(-100%)'
      }),
      animate('0.2s ease-in')
    ]),
    transition(':leave', [
      animate('0.5s ease-out', style({
        opacity: 0,
        transform: 'translateY(100%)'
      }))
    ])
  ]);
  ```
 假定我有以上代码，视图进场和出场；

1. 构建动画需要的库；
2. 导出了一个名叫slideInDownAnimation的常量，并把它设置为一个名，用于外部引入此ts文件；
3. 叫routeAnimation的动画触发器。带动画的组件将会引用这个名字。用在外部html页面引用
4. 指定了一个通配符状态 —— *，它匹配该路由组件存在时的任何动画状态。
5. 定义两个过渡效果，其中一个（:enter）在组件进入应用视图时让它从屏幕左侧缓动进入（ease-in），另一个（:leave）在组件离开应用视图时让它向下飞出。

[3] 如何使用动画；
1. 在需要的组件中引入变量名为：slideInDownAnimation的文件animation.ts；

```typescript
import {slideInDownAnimation} from '../animation';
```
[4]组件中配置
```typescript
templateUrl: 'hero-detail.component.html',
animations: [slideInDownAnimation]
```
[5]html模板中这样引入：	
```typescript
<div *ngIf="hero" [@routeAnimation]="'active'">
```
 @routeAnimation 动画触发器名 
![cmd-markdown-logo](/images/router-8.png)
 点击之后会自动加载动画的；
### 多重路由出口
**一般情况下：我们使用一个路由出口就行啦，什么情况下会使用第二路由呢？**

1.创建一个新组件ComposemessageComponent
2.路由配置：
```typescript
 {
     path:'compose',
     component:ComposemessageComponent,
     outlet:'popup'
   }
```
3.html页面这样配置：
```typescript
  <nav>
     <a routerLink="dashboard" routerLinkActive="active"  [routerLinkActiveOptions]="{exact: true}">Dashboard</a>
     <a (click)="go()" >Heroes</a>
     <a routerLink="test"  routerLinkActive="active"  [routerLinkActiveOptions]="{exact: true}">Test</a>
      <a routerLink="loadtest"  routerLinkActive="active">loadTest</a> 
      <a [routerLink]="[{ outlets: { popup: ['compose'] } }]">Contact</a>
   </nav>
  <router-outlet></router-outlet>
  <router-outlet name="popup"></router-outlet>
```
这是我的页面所有的路由配置；
![cmd-markdown-logo](/images/router-9.png)
点击Contact 不会替换其他的组件信息，注意看<code>Url:http://localhost:4200/dashboard(popup:compose)</code>
点击Contact url地址没有变成<code>http://localhost:4200/contact</code>而是采用圆括号加载
- 圆括号包裹的部分是第二路由。
- 第二路由包括一个出口名称（popup）、一个冒号分隔符和第二路由的路径（compose）
而是显示在下面，点击test也是一样：
![cmd-markdown-logo](/images/router-10.png)

Contact路由加载的组件不会被清除，一直显示在下面，状态一直被激活；
这里我们就能知道第二路由的用处：即使在应用中的不同页面之间切换，这个组件也应该始终保持打开状态，多重出口可以在同一时间根据不同的路由来显示不同的内容；
但是什么时候清除我们的第二路由呢？如果我页面不需要呢？
注意：
```html
<a (click)="go()" >Heroes</a>
go() {
   this.router.navigateByUrl('heroes');
 }
 ```
当点击Heroes时，Contact路由加载的内容就不会被显示：
![cmd-markdown-logo](/images/router-1.png)
原因是这样的：
它使用Router.navigateNyUrl()方法进行强制导航,所以路由清除啦；
还可以这样清除：

```typescript
this.router.navigate([{ outlets: { popup: null }}]);
outlets属性的值是另一个对象，该对象用一些出口名称作为属性名。 唯一的命名出口是'popup'。但这里，'popup'的值是null。null不是一个路由，
但却是一个合法的值。 把popup这个RouterOutlet设置为null会清除该出口，并且从当前URL中移除第二路由popup
```
### 路由守卫
按照上面所说：任何用户都能在任何时候导航到任何地方，这样就有问题，可能此用户并没有权限切换到此路由，
可能用户未登陆不能切换，或者做一些友好提示之后再切换；
所以路由守卫就来了：
守卫返回一个值，以控制路由器的行为：

1. 如果它返回true，导航过程会继续
2. 如果它返回false，导航过程会终止，且用户会留在原地。
也就是你导航的路由是可以取消的，路由守卫还有一个好处就是回退功能时，可以防止用户无限回退，走出app；
 路由守卫怎么做：
用CanActivate来处理导航到某路由的情况。
用CanActivateChild来处理导航到某子路由的情况。
用CanDeactivate来处理从当前路由离开的情况.
用Resolve在路由激活之前获取路由数据。
用CanLoad来处理异步导航到某特性模块的情况。
---
返回的值是一个Observable<boolean>或Promise<boolean>，路由器会等待这个可观察对象被解析为true或false。
在分层路由的每个级别上，我们都可以设置多个守卫。 路由器会先按照从最深的子路由由下往上检查的顺序来检查CanDeactivate()和CanActivateChild()守卫。 然后它会按照从上到下的顺序检查CanActivate()守卫。 如果特性模块是异步加载的，在加载它之前还会检查CanLoad()守卫。 如果任何一个守卫返回false，其它尚未完成的守卫会被取消，这样整个导航就被取消了。
看看路由守卫怎么实现：
1.new 一个新项目activeComponent;
2.编写守卫服务：

```typescript
import { Injectable } from '@angular/core';
import { CanActivate } from '@angular/router';
@Injectable()
export class LoadTestService implements CanActivate{
   canActivate() {
    console.log('AuthGuard#canActivate called');
    return true;
  }
}
```
 3.路由中这样导入我们的守卫：

 ```typescript
import { ActiveComponent } from './active/active.component';
import {LoadTestService} from './loadtest.service';
export const route = [
    {
        path:'',
        component: LoadtestComponent,
        canActivate:[LoadTestService],
        children:[
            {
                path:'a',
                component: ActiveComponent
            }   
        ]
    },
]
```
这样我们的ActiveComponent就是受保护的； 当然这只是模拟；还有更多用法，以后来列举；

 
