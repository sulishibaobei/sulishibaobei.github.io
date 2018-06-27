---
title :  "前端面试中遇见的那些纯js题" 
date : 2017-09-05 11:08:53
categories: "前端面试题整理"
tag:
 - 面试  
 - javascript
 - 字符串,数组,设计模式
---

## js 定义类有哪些常用的方法？
### 1.工厂方式
```javascript
  function Car(){
      var ocar=new Obejct;  //不用括号
      ocar.color='blue';
      ocar.doors=4;
      ocar.showColor=function(){
          this.color;
      }
      return ocar;
  }
  var car1=Car();
  var car2=Car();
  console.log(car1);  //{color: "blue", doors: 4, snowColor: ƒ}  f指的是function()
  console.log(car2);  //{color: "blue", doors: 4, snowColor: ƒ}
```
<!--more-->
**注：**
1.调用此方法时，将创建新的对象，并赋予它所有的属性和方法。使用此函数可以创建两个属性完全相同的对象；
2.我们可以通过给函数传递不同的参数来得到具有不同值得对象

```javascript
   function Car(color,door){
       var ocar=new Object;
       ocar.color=color;
       ocar.doos=door;
       ocar.showColor=function(){
           console.log(this.color);
       }
       return ocar;
   }
   var car1=Car("red",5);
   var car2=car("blue",4);
   car1.showColor(); //red；
   car2.showColor(); //blue
```
在上面的两个例子中，每次创建一个Car()对象都会创建一个showColor()，意味着每个对象都有自己的showcolor()方法，但是实际上每个对象可以共享同一个函数，最后通过将函数的属性指向该方法*

```javascript
   function showColor(){
        console.log(this.color);
    }
    function Car(color,door){
      var ocar=new Object();
      ocar.color=color;
      ocar.doors=door;
      ocar.showColor=showColor;
      return ocar;
    }
    var car1=new Car('red',4);
    car1.showColor();
```
*当然这里扯远了*
### 2.构造函数方法
这个和工厂方式差不多

```javascript
function Car(color,door){
       this.color=color;
       this.doors=door;
       this.showColor=function(){
           console.log(this.color);
       }
   }
   var car1=new Car("red",4);
   var car2=new Car("blue",5);
   car1.showColor();//red
   car2.showColor();//blue
```
**注：**
这种模式和工厂的方式区别在于，不用通过自己去New一个对象，而是使用this关键字；
因为在调用构造函数时就已经创建了对象，而在函数内部只能通过this来访问此对象
但是，它和工厂方式一样，每次调用对象都会创建自己的方法
### 3.原型方式
该方式利用了对象的prototype属性，首先用空函数创建类名，然后所有的属性和方法都被赋予prototype属性；

```javascript
  function Car(){
  }
  Car.prototype.color="red";
  Car.prototype.doors=4;
  Car.prototype.showColor=function(){
      console.log(this.color);
  }
  var car1=new Car();
  var car2=new Car();
   console.log(car1); 
```
**看看最后输出的是什么**
![js](/images/js-1.png)
**注：**
1.首先定义一个空函数，然后通过prototype属性来定义对象的属性；调用该函数时，
原型的所有属性都会立即赋予要创建的对象，所有该函数的对象存放的对是指向showColor()的指针，
语法上看起来都属于同一个对象；
2.但是这个函数没有参数，不能通过传参来初始化属性，必须要在对象创建后才能改变属性的默认值；
3.原型方式还有个很大的弊端；如果属性指向的是对象时：

```javascript
  function Car(){
  }
  Car.prototype.color="red";
  Car.prototype.doors=4;
  Car.prototype.arr=new Array('a',"b");
  Car.prototype.showColor=function(){
      console.log(this.color);
  }
  var car1=new Car();
  var car2=new Car();
  car1.arr.push('cc');
  console.log(car1.arr); //(3) ["a", "b", "cc"]
  console.log(car2.arr); //(3) ["a", "b", "cc"]
```
**注：**
arr对象指向的是一个数组，这里用的是数组的引用值，Car的两个对象指向的都是同一个数组所以car1添加值以后，在car2中也能看到值；
形式是这样的：
![js](/images/js-2.png)
### 4.联合方式（构造函数+原型方式)
```javascript
function Car(color,door){
      this.color=color;
      this.doors=door;
      this.arr=new Array("aa","bb");
  };
  Car.prototype.showColor=function(){
       console.log(this.color);
  }
  var car1=new Car("red",4);
  var car2=new Car("blue",5);
  car1.arr.push("cc");
  console.log(car1);
  console.log(car1.arr); // (3) ["aa", "bb", "cc"]
  console.log(car2.arr);  //(2) ["aa", "bb"]
```
**首先来看看car1对象**
![js](/images/js-3.png)
**注：**
1.car1和car2对象都有自己单独的arr属性，所以改变其中一个另一个并不会受影响
2.此方式是用构造函数定义对象的非函数属性，用原型对象定义对象的方法
### 5.动态原型方式
和混合模式相似，区别就是赋予对象方法的位置不一样；

```javascript
function Car(color,door){
    this.color=color;
    this.doors=door;
    this.arr=new Array("aa","bb");
    if(typeof Car._initialized =="undefined"){
        Car.prototype.showColor=function(){
            console.log(this.color);
        };
          Car._initialized=true;
    };
}
var car1=new Car('blue',3);
var car2=new Car('red',3);
car1.showColor();
car2.showColor();
console.log(car1);
```
**注：**此方法是用一个标志来判断是否已经给原型赋予了此方法，这样可以保证该方法只被创建一次
### 6.混合工厂方式
**目的是创建假构函数，只返回另一种对象的新实例**

```javascript
function Car(){
    var ocar=new Object();//带括号
    ocar.color="red";
    ocar.doors=4;
    ocar.showColor=function(){
        console.log(this.color);
    }
    return ocar;
}
var car1=new Car();
var car2=new Car();
car1.showColor(); //red
```
**注：**和工厂方式很像，不通的就是这是通过New运算符
### 7.对象直接量
采用set get的方式

```javascript
 var car1={
    color:"",
    getColor: function(){
        console.log(this.color);  //red
        return this.color;
    },
    setColor:function(color){
        this.color=color;
    }
 }
 car1.setColor('red');
 car1.getColor();
```
### 8.定义函数对象
```javascript
 var car2=new Function(); //注意大写
 car2.prototype = {
     color:"",
     getColor:function(){
         console.log(this.color);//blue
         return this.color;
     },
     setColor:function(color){
         this.color=color;
     }
 }
 car2.prototype.setColor('blue');
 car2.prototype.getColor();
 console.log(car2.prototype);//{color: "blue", getColor: ƒ, setColor: ƒ}
 ```
也是运用上面的原型方式和混合工厂模式
## js类继承的方法有哪些
既然是继承，首先得有个父类

```javascript
//定义一个动物类
 function Animal(name){
     //给一个Name属性
     this.name=name || 'Animal';
     //实例方法
     this.sleep=function(){
         console.log(this.name+" 在睡觉")；
     }
 }
 //原型方法
 Animal.prototype.eat=function(food){
     console.log(this.name + '正在吃' +food);
 }
```
### 原型链继承
**核心：** 将父类的实例作为子类的原型

```javascript
 function Cat(){
 }
 Cat.prototype=new Animal();
 Cat.prototype.name='cat';
 var cat=new Cat();
 console.log(cat.name); //cat
 console.log(cat.eat('fish')); //cat正在吃fish
 console.log(cat.sleep());  //cat 在睡觉
 console.log(cat instanceof Animal); //true 
 console.log(cat instanceof Cat); //true
```
**注：**使用的的原型方式；
*特点:*
  1. 非常纯粹的继承关系，实例是子类的实例，也是父类的实例
  2. 父类新增原型方法和原型属性，子类都能访问
  3. 简单，易于实现

*缺点:*
  1. 要想为子类新增方法和属性，必须要在new Animal()这样的语句指向之后，不能放在构造器中；
  2. 无法实现多继承
  3. 来自原型对象的引用属性是所有实例共享的
  4. 创建子类实例时，无法向父类构造函数传参
  
```javascript
 var cat1=new Cat()
 console.log(cat1.name) //cat
```
### 构造继承 call（）

```javascript
 function Cat(name){
     Animal.call(this);
     this.name=name || 'Animal';
 }
 var cat=new Cat('cat');
 console.log(cat.name); //cat
 console.log(cat.sleep());  //cat 在睡觉
 console.log(cat instanceof Animal); // false
 console.log(cat instanceof Cat); // true
```
**注：**这里是调用不到eat原型方法的;
*特点:*
  1. 解决了1中，子类实例共享父类引用属性的问题；
  2. 创建实例时，可以向父类传参；
  3. 可以实现多继承（call 多个父类对象);

*缺点:*
  1. 实例并不是父类的实例 
  2. 只能继承父类的实例和方法，并不能继承原型属性和方法；
  3. 无法实现函数复用，每个子类都有父类实例函数的副本，影响性能

### 实例继承  instace
**核心：** 为父类实例添加新特性，作为子类实例返回

```javascript
 function Cat(name){
     var instace=new Animal();
     instace.name=name || 'Animal';
     return instace;
 }
var cat=new Cat();
console.log(cat.name); //Animal
console.log(cat.sleep()); //Animal 在睡觉
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // false
```
*特点:*不限制调用方法，不管是new 子类()还是子类(),返回的对象具有相同的效果；
*缺点:*
1.实例是父类的实例，不是子类的实例;
2.不支持多继承
### 拷贝继承

```javascript
  function Cat(name){
      var animal=new Animal();
      for(var p in animal){
          Cat.prototype[p]=animal[p];
      }
      Cat.prototype.name=name || 'Tom';
  }
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```
*特点:*支持多继承;
*缺点:*
   1. 效率较低，内存占用高（因为要拷贝父类的属性）
   2. 无法获取父类不可枚举的方法（不可枚举方法，不能使用for in 访问到）

### 组合继承
**核心：**通过调用父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型，实现函数复用

```javascript
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
Cat.prototype = new Animal();
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // true
```
*特点：*
    1.弥补了方式2的缺陷，可以继承实例属性/方法，也可以继承原型属性/方法
    2.既是子类的实例，也是父类的实例
    3.不存在引用属性共享问题
    4.可传参
    5.函数可复用
*缺点：*调用了两次父类构造函数，生成了两份实例（子类实例将子类原型上的那份屏蔽了）

### 寄生组合继承
**核心：**通过寄生方式，砍掉父类的实例属性，这样，在调用两次父类的构造的时候，就不会初始化两次实例方法/属性，避免的组合继承的缺点

```javascript
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
(function(){
  // 创建一个没有实例方法的类
  var Super = function(){};
  Super.prototype = Animal.prototype;
  //将实例作为子类的原型
  Cat.prototype = new Super();
})();

var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true
```
特点:堪称完美
缺点: 实现较为复杂
## 数组去重
给定无序数组，要求去除数组中的重复数组，并且返回新的无重复数组
ES6写法：

```javascript
var array=[1,2,3,5,2,4,1,9,8];
Array.from(new Set(array));
console.log(Array.from(new Set(array)));//(7) [1, 2, 3, 5, 4, 9, 8]
```
ES5写法：

```javascript
console.log(uniqueArray(array));//(7) [1, 2, 3, 5, 4, 9, 8]
function uniqueArray(array){
    var hashmap={};
    var unique=[];
    for(var i=0;i<array.length;i++){
        //hasOwnProperty boolean 用于表示一个对象本身是否具有指定名称的属性值
        if(!hashmap.hasOwnProperty([array[i]])){
            hashmap[array[i]]=1;
            unique.push(array[i]);
        }
    }
    return unique;
}
```
## js类多重继承的实现方法是怎么样的
其实就是第二部分的代码；
1.call/apply

```javascript
var s1 = function(name){
   this.name = name;  //专门存放名字的
}
var s2 = function(sex){
   this.sex = sex;  //专门存放性别的的
}
var s3 = function(age){
  this.age = age;  //专门存放年纪的
}
var Student = function(name, sex, age, score){
 s1.call(this, name);
 s2.call(this, sex);
 s3.call(this, age);
 this.score = score;
}
Student.prototype.constructor = Student;
var s = new Student('jack', 'male', '12', '100');
console.log(s.name+" "+s.age+ " " + s.age+" "+ s.score);//jack 12 12 100
```
1.使用call()实现多重继承，核心就是用Function类的call方法去绑定新的类，使新的类实例化后的对象继承了该属性及方法
2.这样我们就可以根据各个不同的功能模块分不同程序员独立开发，最后合并起来，实现了多重继承。
3.但它的缺点是基类的方法只能定义在类中，这样在每次实例化的时候都会创建该方法，造成多余内存占用
这也是我们上面的构造继承的缺点

2.原型链继承

```javascript
var s1 = function(){
}
s1.prototype.getName = function(){alert(this.name)};
var s2 = function(){
}
s2.prototype =new s1();
s2.prototype.constructor = s2;
s2.prototype.getSex = function(){alert(this.sex)};
var s3 = function(){
}
s3.prototype = new s2();
s3.prototype.constructor = s3;
s3.prototype.getAge = function(){alert(this.age)};
var s = new s3();
s.name = 'jack';
s.sex = 'male';
s.age = '22';
s.getName();//jack
s.getSex();//male
s.getAge();//22
```
出现代码冗余，而且无法传递参数

3.混合模式继承
就是将上面的两种方式进行结合，扬长避短；

```javascript
var s1 = function(name){
this.name = name;
}
s1.prototype.getName = function(){alert(this.name)};
var s2 = function(sex){
this.sex = sex;
}
s2.prototype =new s1();
s2.prototype.getSex = function(){alert(this.sex)};
var s3 = function(age){
this.age = age
}
s3.prototype = new s2();
s3.prototype.getAge = function(){alert(this.age)};
var s4 = function(name, sex, age){
s1.call(this, name);
s2.call(this, sex);
s3.call(this, age);
}
s4.prototype = new s3();
s4.prototype.constructor = s4;
var s = new s4('jack', 'male', '25');
s.getName();//jack
s.getSex();//male
s.getAge();//25
```
1.属性用构造的方法，方法采用原型模式；大大提高了效率
## js中的作用域是什么样子的
### 变量作用域
全局变量的作用域：全局，在代码定义的任何地方都是有定义的；
局部变量和函数的参数只有在函数体内才有定义
另外，局部变量的优先级要高于同名的全局变量，也就是说当局部变量与全局变量重名时，局部变量会覆盖
全局变量；

```javascript
   var num = 1;            //声明一个全局变量
   function func() {
   　  var num = 2;        //声明一个局部变量
       return num;
   }
   console.log(func());    //输出：2 
```
 注：声明局部变量时一定要使用var,否则，解释器会将该变量当做全局对象window的属性。
### 函数作用域
在JavaScript中函数作用域（变量在声明它们的函数体以及这个函数体嵌套的任意函数体都是有定义的）

```javascript
 function func() {
            console.log(num);           //输出：undefined，而非报错，因为变量num在整个函数体内都是有定义的
            var num = 1;                //声明num 在整个函数体func内都有定义
            console.log(num);           //输出：1
        }
        func();
```
JavaScript的函数作用域是指在在函数内声明的所有变量在函数体内始终是可见的，也就是说在函数体内变量声明之前就已经可用了
作为属性的变量，当声明一个全局变量的时候，实际上是定义了全局对象window的一个属性

```javascript
  var num = 1;            //声明全变量num
        alert(window.num)       //输出：1 声明的全局变量实际上就是声明了一个window对象的属性
```
## js中的this指的是什么
1.在全局作用域时：this指向window

```javascript
  console.log(this === window);  //true
```
因此,我们在全局作用域声明的变量也会指向this

```javascript
var x = 1;
console.log(this.x);//1
console.log(window.x);//1
```
2.当在函数中时,this指向调用函数的那个对象。

```javascript
var x = 0;
function num(){
  this.x = 1;
}
console.log(this.x);//0
num();
console.log(this.x);//1
```
这里就是经常容易犯得错误，很多人觉得，当this已经在一个function之中时，其目前所处位置为当前的局部作用域，所以目前指向的应该是此函数 

但是，如果你将这个函数实例化（new）之后，此函数将生成一个全新的环境，此时在此实例中的this也会随之发生变化，它将指向所在实例。首先new关键字会创建一个空的对象，然后会自动调用一个函数apply方法，将this指向这个空对象，这样的话函数内部的this就会被这个空的对象替代;

```javascript
num = "0";
function setThis(){
  this.num = "1";
}
console.log(this.num);//"0"
new setThis();
console.log(this.num);//"0"
console.log(new setThis().num);//1
```
另外，在我们写javascript的时候，我们通常还会有一种调用函数的方法，即为元素绑定事件，比如button.addEventListener(‘click', fn, false)等，如果在fn里面需要使用到this的话，那么此时this指向事件处理元素，也就是button

3.作为对象调用时,this 指代上级对象

```javascript
function test(){
　　alert(this.x);
}
var o = {};
o.x = 1;
o.m = test;
o.m(); // 1
```
总之：this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，实际上this的最终指向的是那个调用它的对象

## call,apply,bind的区别是什么
一般用来指定的this环境;

```javascript
   var a={
             user:'少女雨',
             fn:function(){
                 console.log(this.user);
             }
         }
         var b=a.fn;
         b(); //undefined
         a.fn();  //少女雨
```
引言：运行b()得到的是undefined，其实和我们的this指向有关系;因为此时this指向的是b;而下面a.fn()为什么能输出来能，因为此时this指向的是我们的a;也就是常说的那句，this始终指向的是调用它的那个对象;
虽然a.fn()能打印出我们的数据，但是并不是最科学的方式，这时候可以通过下面的方法：
1.call():调用一个对象的一个方法，以另一个对象替换当前对象
说明：
call 方法可以用来代替另一个对象调用一个方法。call 方法可将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。
如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。 

```javascript
  var a={
       user:'少女雨',
       fn:function(){
           console.log(this.user);
             }
         }
         var b=a.fn;
         b.call(a); //少女雨
```
简单来说：通过在call方法，给定第一个参数；将b添加到哪个环境中，this就会指向那个对象；
call方法除了第一个参数以外还能添加多个参数

```javascript
  var a={
             user:'少女雨',
             fn:function(a){
                 console.log(this.user);
                 console.log(a); //2
             }
         }
         
         var b=a.fn;
         b(); //undefined
         a.fn();  //少女雨
         b.call(a); //少女雨
         b.call(a,2);//少女雨 2
```
这样就可以用作传参
2.apply():应用某一对象的一个方法，用另一个对象替换当前对象。
说明：
如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。
如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。
apply方法和call方法相似，它也可以改变this指向；

```javascript
  var a={
       user:'少女雨',
       fn:function(){
            console.log(this.user);
             }
         } 
         var b=a.fn;
         b.apply(a);  //少女雨
```
同样的apply也可以有多个参数，但不同的是，第二个参数必须是一个数组，

```javascript
 var a={
       user:'少女雨',
       fn:function(a){
            console.log(this.user);
             console.log(a);  //10
             }
         } 
         var b=a.fn;
         b.apply(a,[10]);  //少女雨  10
```
注意：如果call和apply的第一个参数是Null,那么this指向的是window对象；

```javascript
  var a={
       user:'少女雨',
       fn:function(a){
             console.log(this);  
             }
         } 
         var b=a.fn;
         b.apply(null);
         //Window {stop: function, open: function, alert: function, confirm: function, prompt: function…}
```
3.bind() 和call,apply方法不同，但是都可以改变this的指向

```javascript
 var a={
       user:'少女雨',
       fn:function(a){
             console.log(this.user);       
             }
         } 
         var b=a.fn; 
         b.bind(a);
```
这里没有打印出数据；这就是bind和call,apply不同的地方，实际上bind返回的是一个修改过后的函数；
你看：

```javascript
 var c=b.bind(a); 
         console.log(c);
         /*function (a){
             console.log(this.user);       
             }*/
```
若想打印出数据，需要再执行以下c();
同样bind()也可以有多个参数，并且参数可以执行的时候再次添加，但注意，参数是按照形参顺序进行的；
就是这样

```javascript
   var a={
       user:'少女雨',
       fn:function(a,b,c){
             console.log(this.user);   //少女雨
             console.log(a,b,c);     //1 3 4
             }
         } 
         var b=a.fn; 
         var c=b.bind(a,1); 
         c(3,4);
```
总结：call和apply都是改变上下文中的this并立即执行这个函数，都可以添加多个参数，但是apply方法添加的参数必须是一个数组;bind方法可以让对应的函数想什么时候调用就什么时候调用，并且参数可以在执行时候添加
##  caller , callee和arguments分别是什么
1.arguments:在函数调用时，会自动在该函数内部生成一个名为arguments的隐藏对象，该对象类似于数组，但又不是数组；
可以用[]操作符获取函数调用时传递的实参,并不局限于函数声明的参数列表;
表示被这拗口的话混乱了；

```javascript
 function obj(){
      console.log( 'arguments instanceof Array? ' + (arguments instanceof Array) ); // arguments instanceof Array? false
      console.log( 'arguments instanceof Object? ' + (arguments instanceof Object) );  // arguments instanceof Object? true
      console.log(arguments);  //Arguments(0)
  }
  obj();         
```
我们通过控制台显示的数据还看：
![js](/images/js-4.png)
从打印的结果看，arguments是一个对象，并且里面还包含了callee，callee中又包含caller等属性；
接下来，我们gia百年代码看看，让函数调用时，传递参数，而函数本身没有参数；
```javascript
  function obj(){
      console.log( 'arguments instanceof Array? ' + (arguments instanceof Array) ); // arguments instanceof Array? false
      console.log( 'arguments instanceof Object? ' + (arguments instanceof Object) );  // arguments instanceof Object? true
      console.log(arguments);  //Arguments(0)
  }
  obj('xiaonv','LOVE',23);
```
和上面的代码没有什么区别，只是obj传递了参数；
![js](/images/js-5.png)
很显然，Arguments包含了3个参数，也就是我们调用obj()时传递的；
所以，也就是说为什么arguments是存储的实际传递给函数的参数呢，而不是函数声明的参数；
2.callee 
上面的截图中，我们已经看到了callee的出现，可以看出，callee是arguments对象的一个成员，并且值就是为我们被执行的obj()方法；
我们在obj方法中加入这一句：

```javascript
 console.log(arguments.callee);
```
![js](/images/js-6.png)
从而可知：arguments.callee是指向参数arguments对象的函数，在这里就是obj咯。
3.caller又是哪个？
记得上图也出现过，caller则是callee里的一个属性对象；该属性保存着调用当前函数的函数；
注意：是调用，不仅仅包含闭包，如果没有父函数，则为null;
所以上诉代码中，我们这样输出：
```javascript
 console.log(arguments.callee.caller);  //null
```
因为obj方法没有父函数，所以为null；
接下来，我们再看：

```javascript
//父函数
function parent(){
    //子函数
     function child(){
         console.log(child.caller);
     }
    child();
    console.log(parent.caller); //null
} 
parent();    
```
看看输出结果：
![js](/images/js-7.png)
可以看到caller保存的是parent对象；这是因为child有一个父函数parent，并且在父函数中被执行child(); 而后一个parent没有父函数，所以为null；
那这样看看：

```javascript
 //parent2调用了child2
function parent2(){
    child2();
}
function child2(){
    console.log(child2.caller);
}
 parent2();
```
结果呢：
![js](/images/js-8.png)
看见区别了吗？ parent2调用了child2，但child2不是嵌套在parent2里的函数；
所以只显示了child2();
大概就是这样;

## 什么是闭包，闭包有什么用处
相信很多人都会有这个困惑，闭包到底是什么？
### 什么是闭包呢？
闭包是有权访问<b>另一个函数作用域的变量</b>的函数
简单点来说：javascript允许使用内部函数---即函数定义和函数表达式位于另一个函数体内，而且，这些内部函数可以访问它们所在的内部函数中声明的所有全局变量，参数和声明的其他内部函数。当其中一个这样的内部函数在包含它们的外部函数之外被调用时，就会形成闭包；
### 作用域的作用 
这是理解闭包的第一步；前面已经介绍过作用域了，无非就是全局和局部；
javascript语言的特殊之处，在于函数内部可以直接读取全局变量；其中内部函数中可以访问外部函数的变量，是因为内部函数的作用域链中包含了外部函数的作用域；

```javascript
var n=999;
   function f1(){
       console.log(n); //999
   }
   f1();
```
而另一方面，函数外部是无法取到函数内部的值；

```javascript
   function f1(){
       var n=999;    
   }
   f1();
   console.log(n);  //n is not defined
```
还有一点要注意，函数内部声明变量时，一定要使用var命令，不然等于声明了一个全局变量，变量作用域会提升；

```javascript
function f1(){
　 n=999;
}
f1();
alert(n); // 999
```
### 闭包的几种写法
简单介绍下，和上面的定义类的几种方法其实就是闭包；

```javascript
function Circle(r) { 
   this.r = r; 
} 
Circle.PI = 3.14159; 
Circle.prototype.area = function() { 
  return Circle.PI * this.r * this.r; 
} 
var c = new Circle(1.0); 
console.log(c.area()); //3.14159
```
还有

```javascript
var Circle={ 
"PI":3.14159, 
"area":function(r){ 
return this.PI * r * r; 
} 
}; 
alert( Circle.area(1.0) );//3.14159
```
可以结合上面的例子简单理解下；

### 闭包的作用
1.可以读取函数内部的变量，
2.让这些变量的值始终保持在内存中；
但是究竟怎么使用闭包才能读取到函数内的局部变量呢？

```javascript
 function f1(){
　　var n=999;
　　function f2(){
　　　 console.log(n); // 999
　　}
f2();
}
```
上面的代码，我在函数内部再定义了一个函数；f2被包含在f1内部了，所以可以访问f1内部的所有全局变量；
但是f1去不能访问f2内部的局部变量；
这就是js的“链式作用域"结构，子对象会一级一级的向上寻找所有父对象的变量，所以父对象的所有变量，对子对象都是可见的，反之则不成立；
```javascript
 function f1(){
　　var n=999;
　　function f2(){
　　　　console.log(n); 
　　}
　　return f2;
}
var result=f1();
result(); // 999
```
f2可以读取到f1的变量，那么将f2作为返回值，不久可以在f1中读取到它的内部变量了；
但是如何将变量的值存在内存中呢？

```javascript
function f1(){
    var n=99;
    nAdd=function(){ //匿名函数
       n+=1; //全局变量
    }
    function f2(){
        console.log(n);
     }
     return f2;
}
var result=f1();
result(); //99
nAdd();
result(); //100
```
这段代码中result实际上就是闭包f2函数，它一共运行了两次，第一次值是99，第二次是100；这证明了，函数f1中的局部变量n一直保存在内存中，并没有在f1调用后被自动清除。
原因就在于f1是f2的父函数，而f2被赋给了一个全局变量，这导致f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

### 闭包与this
在闭包中使用this对象会导致一些问题；因为匿名函数的执行具有全局性，而this对象通常指向window对象；
```javascript
var name = "The window";
var object = {
  name:"My object",
  getNameFun:function(){
   return function(){
   return this.name;
  };
 }
};
console.log(object.getNameFun()); //"The window"（在非严格模式下）
```
但是，将其阿布作用域中的this对象保存在一个闭包能够访问的变量里，就可以让闭包访问该对象啦；
```javascript
getNameFun:function(){
    var that=this;
   return function(){
      return that.name;
  };
```
### 使用闭包注意点
1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

## defineProperty,hasOwnProperty,isEnumerable 都是做什么用的?

javascript中任何一个构造函数对有其对应的原型，我们可以给原型赋予一些我们想要的属性；

```javascript
function Gadget(name,color){
     this.name=name;
     this.color=color;
     this.whatAreYou=function(){
         return 'I am a' + this.color + ' ' + this.name;
     }
 }
 Gadget.prototype.price=100;
 Gadget.prototype.rating=3;
 Gadget.prototype.getInfo=function(){
     return 'Rating' +this.rating + ' ,price:' +this.price;
 };
 var newtoy=new Gadget('少女雨','black');
 console.log(newtoy);
```
先看看输出结果：
![js](/images/js-8.png)
这里定义了一个Gadget类的实例 --newtoy对象，在这个对象中，我们可以访问对象内部以及其原型对象中的属性和方法；如果想要获得某个对象所有的属性列表，可以使用for-in循环；
```javascript
for (i in newtoy) {
     console.log(i+ '= ' + newtoy[i]);
}
```
可以得到这样的结果：
```javascript
name= 少女雨
color= black
whatAreYou= function (){
         return 'I am a' + this.color + ' ' + this.name;
     }
price= 100
rating= 3
getInfo= function (){
     return 'Rating' +this.rating + ' ,price:' +this.price;
 }
```
这时候，如果我们想把原型中的属性过滤掉，就可以首先使用hasOwnProperty()来判断该属性是否属于原型对象内部的：
```javascript
for (i in newtoy) {
    if(newtoy.hasOwnProperty(i))
      console.log(i+ '= ' + newtoy[i]);
}
```
输出结果是这样的：
```javascript
name= 少女雨
color= black
whatAreYou= function (){
         return 'I am a' + this.color + ' ' + this.name;
}
```
需要注意以下几点：
> * 只有那些可枚举的属性才会被显示出来(一般内建属性都是不可枚举的)
> * 原型中的各个原型属性才会被显示出来，当然前提是它们是可枚举的；
> * propertyIsEnumerable()用于测试该属性是否可枚举，对于所以的原型属性，propertyIsEnumerable()都会返回false，包括那些在for-in循环中可枚举的属性。
console.log(newtoy.propertyIsEnumerable()) //false
但如果propertyIsEnumerable()的调用是来自原型链上的某个对象，那么该对象中的属性是可枚举的。例如：
newtoy.constructor.prototype.propertyIsNumerable('price'); //返回: ture

另外还有一个比较常用的isPrototypeOf()方法，这个方法告诉我们当前对象是否是另外一个对象的原型；
```javascript
function Gadget(name,color){
     this.name=name;
     this.color=color;
     this.whatAreYou=function(){
         return 'I am a' + this.color + ' ' + this.name;
     }
 }
 Gadget.prototype.price=100;
 Gadget.prototype.rating=3;
 Gadget.prototype.getInfo=function(){
     return 'Rating' +this.rating + ' ,price:' +this.price;
 };

function Human(){
    this.name=name;
}
Gadget.prototype=Human;
var newtoy=new Gadget('少女雨','black');
console.log(Gadget.isPrototypeOf(newtoy)); //false
console.log(Human.isPrototypeOf(newtoy));  //true
```
第一个输出false，因为newtoy是Gadget的一个实例，不是原型；
第二个输出true，因为Human是Gadget的原型，所以也就是newtoy的原型；

defineProperty是es5新加的给对象属性设置描述符的方法，使用的方式如下：
Object.defineProperty(obj,prop,descriptor)  //对象，属性，描述符
基本的描述符有这三个：
> * writable --是否为可写
> * configurable -- 是否为可配置的
> * enumerable -- 是否为可枚举的
顾名思义，writable设置属性是否为可写，如果是false，那属性之后的修改将会无效
configurable属性是否可配置，设置为false后，该属性不可被删除，也不可再更改为可配置的，但是可以从可写改为不可写
enumerable 属性是否可枚举，如果是false，则属性不可枚举，不可枚举属性对  for ... in语句和Ojbect.keys是不可见的
```javascript
var o = {
  name: 'xu'
}
Object.defineProperty(o, 'name', {
  writeable:false   //不可写
})
Object.defineProperty(o, 'name', {
  configurable:false  
})
Object.defineProperty(o, 'name', {
  enumerable: false
})
```
对value的get/set方法采用的就是此方法；

## js常用的设计模式的实现思想，单例，工厂，代理，装饰，观察者模式等;
### 什么是设计模式呢？
设计模式是一种解决问题的方案，用于解决在软件设计过程中普遍存在的问题；学习设计模式是为了学习如何合理的整理我们的代码；
### 设计模式六要素
单一职责原则：一个类只负责一个功能领域中相应的职责，或者可以定义为：就一个类而言，
应该只有一个引起他变化的原因；
单一职责原则要求一个类不能承担过多的职责，形成"超级类"。需要根据不同职责分为不同的类，从而降低类的复杂度，提高可阅读性，增加系统的稳定性；
开闭原则：一个软件实体应该对扩展开发，对修改关闭，即软件实体应尽量在不修改原有代码
的情况下进行扩展；
里氏代换原则：所有引用父类的地方都必须能透明的使用起子类的对象；
里氏代换原则要求在软件中一个基类能够替换为它的子类而不会因此任何异常。因此要求子类可以扩展父类的功能，但不能修改父类本身的非抽象方法；
依赖倒置原则：抽象不应该依赖于细节，细节应当依赖于抽象，换言之，要针对接口编程，而不是针对现实编程；
接口隔离原则：使用多个专门的接口，而不使用单一的总接口，即客户端不应该依赖那些它不需要的接口。
接口隔离原则要求我们设计接口不应太大，接口应仅仅提供客户端需要的行为，在使用此原则时应注意接口的粒度，接口太小会导致接口泛滥，不利于维护，接口太大灵活性较差，使用不便；
迪米特法则：一个软件实体应当尽可能少地与其他实体发生相互作用；
迪米特法则要求我们应该减少对象之间的交互，如果两个对象之间不必彼此之间直接通信，那么这两个对象就不应该发送任何直接的相互作用，当需要调用另一个对象的某一方法时，可以通过第三者来转发此调用。
### 几种常用设计模式
#### 单例模式
作用：用来保证一个类只有一个实例，用来解决一个全局使用的类频繁的创建和销毁，从而节约系统资源；

```javascript
var single = (function(){
  var unique;
  function getInstance(){
　　　　// 如果该实例存在，则直接返回，否则就对其实例化
    if( unique === undefined ){
      unique = new Construct();
    }
    return unique;
  }
  function Construct(){
    // ... 生成单例的构造函数的代码
  }
  return {
    getInstance : getInstance
  }
})();
```
可以通过 single.getInstance() 来获取到单例，并且每次调用均获取到同一个单例。这就是 单例模式 所实现的效果。
应用场景：适用于我们平常将代码封装起来，只暴露一个入口，从而避免全部变量被污染；

#### 工厂模式
概念：提供创建对象的接口，根据调用者的参数，生产相对应的产品；
分类：
简单工厂模式:使用一个类，通常为单体，来生成实例；
复杂工厂模式：将其成员对象的实例化推到子类中，子子类可以重写父类接口方法以便创建的时候指定自己的对象类型。父类只对创建过程中的一般性问题进行处理，这些处理会被子类继承，子类之间是相互独立的，具体的业务逻辑会放在子类中进行编写。
简单工厂模式：
```javascript
var XMLHttpFactory =function(){};　　　　　　//这是一个简单工厂模式
　　XMLHttpFactory.createXMLHttp =function(){
　　　 var XMLHttp = null;
　　　　if (window.XMLHttpRequest){
　　　　　　XMLHttp = new XMLHttpRequest()
　　　 }else if (window.ActiveXObject){
　　　　　　XMLHttp = new ActiveXObject("Microsoft.XMLHTTP")
　　　　}
　　return XMLHttp;
　　}
　　//XMLHttpFactory.createXMLHttp()这个方法根据当前环境的具体情况返回一个XHR对象。
　　var AjaxHander =function(){
　　　　var XMLHttp = XMLHttpFactory.createXMLHttp();
　　　　...
　　}
```
复杂工厂模式：
```javascript
var XMLHttpFactory =function(){};　   //这是一个抽象工厂模式
XMLHttpFactory.prototype = {
　　//如果真的要调用这个方法会抛出一个错误，它不能被实例化，只能用来派生子类
　　createFactory:function(){
 　　throw new Error('This is an abstract class');
　　}
}
var XHRHandler =function(){}; //定义一个子类
// 子类继承父类原型方法
extend( XHRHandler , XMLHttpFactory );
XHRHandler.prototype =new XMLHttpFactory(); //把超类原型引用传递给子类,实现继承
XHRHandler.prototype.constructor = XHRHandler; //重置子类原型的构造器为子类自身
//重新定义createFactory 方法
XHRHandler.prototype.createFactory =function(){
　　var XMLHttp =null;
　　if (window.XMLHttpRequest){
 　　XMLHttp =new XMLHttpRequest();
　　}else if (window.ActiveXObject){
 　　XMLHttp =new ActiveXObject("Microsoft.XMLHTTP")
　　}
　　return XMLHttp;
}
```
简单对象使用工厂模式会增加其系统的复杂度。所以工厂模式适用的场景只是对象的构建十分复杂
，处理大量具有相同属性的小对象，需要依赖具体环境创建不同实例；

#### 代理模式
概念：把对一个对象的访问, 交给另一个代理对象来操作
举一个例子：我们公司的补打卡是最后是要交给大boss来审批的，但是公司那么多人，每天都那么多补打卡，那大boss岂不是被这些琐事累死。所以大boss下会有一个助理，来帮忙做这个审批，最后再将每个月的补打卡统一交给大boss看看就行；

```javascript
// 补打卡事件
var fillOut = function (lateDate) {
  this.lateDate = lateDate;
};
// 这是bigBoss
var bigBoss = function (fillOut) {
  this.state = function (isSuccess) {
    console.log("忘记打卡的日期为：" + fillOut.lateDate + ", 补打卡状态：" + isSuccess);
  }
};
// 助理代理大boss 完成补打卡审批
var proxyAssis = function (fillOut) {
  this.state = function (isSuccess) {
    (new bigBoss(fillOut)).state(isSuccess); // 替bigBoss审批
  }
};
// 调用方法：
var proxyAssis = new proxyAssis(new fillOut("2016-9-11"));
proxyAssis.state("补打卡成功");
// 忘记打卡的日期为：2016-9-11, 补打卡状态：补打卡成功
```
应用场景：比如图片的懒加载，我们就可以运用这种技术。在图片未加载完成之前，给个loading图片，加载完成后再替换成实体路径。
还有解决跨域问题都能采用代理模式；
#### 观察者模式（发布订阅)
概念：定义对象间的一种一对多的依赖关系，以便当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并自动刷新，也被称为是发布订阅模式。
它需要一种高级的抽象策略，以便订阅者能够彼此独立地发生改变，而发行方能够接受任何有消费意向的订阅者。
发布订阅流程：
1. 确定谁是发布者(比如我的博客)。
2. 然后给发布者添加一个缓存列表，用于存放回调函数来通知订阅者。
3. 发布消息，发布者需要遍历这个缓存列表，依次触发里面存放的订阅者回调函数。
4. 退订（比如不想再接收到这些订阅的信息了，就可以取消掉）
这种其实很好理解，特别是我学习了框架之后，subscribe用来订阅；unsubscribe取消订阅；

还有更多设计模式；可以自行去了解下；

## 列举数组相关的方法
一个简单数组：
```javascript
var mycars=new Arrat();
mycars[0]='Sab';
mycars[1]='Volvo';
mycars[2]='BMW';
console.log(mycars);
```
输出结果很简单，就是这样： 
![js](/images/js-10.png)
从图可以看出数组有啥属性吧，length,construtor,prototype;现在主要来介绍数组的方法；
![js](/images/js-11.png)
看看如何使用：
```html
<button onclick="myage.forEach(myFunction)">点我</button>
```
```javascript
   var mycars=new Array();
    mycars[0]='Sab';
    mycars[1]='Volvo';
    mycars[2]='BMW';
    console.log(mycars);  //["Sab", "Volvo", "BMW"]
    var myshops=new Array('小雨','haha');
    console.log(myshops);//["小雨", "haha"]
    var mys=mycars.concat(myshops);
    console.log(mys);//["Sab", "Volvo", "BMW", "小雨", "haha"]
    var mybeds=new Array('床');
    var mys=myshops.concat(mybeds,mycars);
    console.log(mys);//["小雨", "haha", "床", "Sab", "Volvo", "BMW"]
    //所以concat不止传入一个参数，和拼接顺序有关；
    mycars.copyWithin(1,0);
    console.log(mycars);//["Sab", "Sab", "Volvo"]
    //copyWiththin是用于数组内部的
    var myage=[32,15,25,25];
    function checkAdult(age){
      return age>=18;
    }
    console.log(myage.every(checkAdult));//false
    //every用于检测所有元素是否都符合条件，有一个不满足都会返回false；如果有一个返回false，则其余的并不会被检测；并外不能检测空数组；和 some方法相反，some方法一旦有满足条件就返回true；
    console.log(myage.filter(checkAdult));//[32, 25, 25]
    //filter输出的是符合检索的所有值，对空数组无效
    console.log(myage.find(checkAdult)); //32
    //find返回的是符合条件的第一个值，如果没有符合条件的就返回undefined，对空数组无效
    //对于以上的检索方法都是传入一个检查条件
    console.log(myage.findIndex(checkAdult));//0
    //findIndex 顾名思义，返回的是符合条件的值的索引值，和上面的find类似；没有返回-1；
    mycars.fill('shaonvyu');
    console.log(mycars);//["shaonvyu", "shaonvyu", "shaonvyu"]
    //fill 使用一个固定值来替换数组中所有的值，对空数组无效
    function myFunction(item, index) {
      console.log(  "index[" + index + "]: " + item );  //index[0]: 32  index[1]: 15   index[2]: 25   index[3]: 25
    }
    //forEach用于调用数组中的每一个元素，并将元素给回调函数，有一个用于场景，点击的时候想获取每一个列表的值，但是使用for返回的总是只有最后一个，这时候可以试试forEach；对空数组无效;
    console.log(mycars.indexOf('Sab')); //-1
    console.log(myshops.indexOf('haha'));  //1
    //indexof用来返回某个指定字符串首次出现在数组中的位置,按从左到右顺序索引的，如果想反过来就使用lastIndexOf；没有找到则返回-1；
    //但是上面的两个输出为什么第一个是-1,从直观感觉看，Sab这个值却是在mycars数组中；那是因为数组开始时new的一个空数组，Sab是我们后来加入进去的值，采用Indexof索引的是原始数组，所以找不到；
    var join=mycars.join();
    console.log(join); //shaonvyu,shaonvyu,shaonvyu
    // join用于把数组中的所有元素转换成一个字符串，用,或者指定的符号进行分割；为什么输出是shaonvyu呢，因为上面的fill方法；
    console.log(myage.map(Math.sqrt)); //[5.656854249492381, 3.872983346207417, 5, 5]
    //map返回一个新的数组，数组中的值是经过函数调用返回的值，对空数组无效，按序处理
    console.log(mys);//["小雨", "haha", "床", "Sab", "Volvo", "BMW"]
    console.log(mys.pop());//BMW
    console.log(mys);//["小雨", "haha", "床", "Sab", "Volvo"]
    //pop方法删除数组的最后一个元素，并返回被删除元素的值；会改变数组长度，shift方法用来删除数组的第一个元素，和pop相反；
    console.log(mys.shift()); //小雨
    console.log(mys.indexOf('haha')); //0
    console.log(mycars.push('Sab')); //4
    console.log(mycars);//["shaonvyu", "shaonvyu", "shaonvyu", "Sab"]
    //push() 方法可向数组的末尾添加一个或多个元素，并返回新的长度。新增的元素被添加在末尾;改变了数组长度，如果想在开始位置添加，使用unshift方法;都是返回新的长度
    function getSum(total,age){
      return total + age;
    }
    console.log(myage.reduce(getSum));//97
    // reduce 只返回一个值，从左到右开始计算，作为一个累加，如果想从右到左，使用reduceRight方法，功能一样
    console.log(myshops.reverse()); //["haha", "小雨"]
    console.log(myshops);//["haha", "小雨"]
    //reverse 反序输出数组，将数组顺序颠倒；
    console.log(mys);//["haha", "床", "Sab", "Volvo"]
    console.log(mys.slice(1,3)); //["床", "Sab"]
    //slice方法从已有数组中返回指定的部分，并返回出来，输出的是索引，取前不取后；不改变原始数组
    console.log(mys);//["haha", "床", "Sab", "Volvo"]
    mys.splice(2,0,"HRE","DDD")
    console.log(mys);//["haha", "床", "HRE", "DDD", "Sab", "Volvo"]
    //splice() 方法用于插入、删除或替换数组的元素。会改变原始数组
    console.log(myage.sort()); //[15, 25, 25, 32]
    var a=[-1,3,45,8,35];
    console.log(a.sort()); //[-1,3,45,8]
    //sort对数组内的字母或数字进行排序，并按升序或者降序，默认是升序；这个方法会改变原始数组;注意不能对还有负数的数组进行排序
    console.log(mys.toString())//haha,床,HRE,DDD,Sab,Volvo
    //tostring是最常用的， 可把数组转换为字符串，并返回结果。数组中的元素之间用逗号分隔。 
    console.log(mycars.valueOf()); //["shaonvyu", "shaonvyu", "shaonvyu", "Sab"]
    //valueOf返回数组的原始值，和mycars输出的值一样，原始值指的是Array 对象派生的所有对象继承出来的值，不改变数组长度
```
以上就是所有的数组方法了；

## 列举与字符串相关的常用方法
首先应该知道字符串就是一个或多个排列在一起的字符，放在单引号或双引号之中。
```javascript
 var str="abcdfe";
console.log(str.length);//6
```
字符串只有一个属性，就是length；
常用的多一些方法：
![js](/images/js-12.png)

```javascript
var str="abcdfe";
    console.log(str.length);//6
    console.log(str.charAt(2)); //c
    //charAt(n) 传入的是一个索引值，如果不在 0~str.length-1之间，则返回一个空字符串
    console.log(str.charCodeAt(2));//99
    //charCodeAt() 用法和charAt一样，只是返回的是指定位置的Unicode值, fromCharCode()将unicode值转成字符串
    var str1="fdrref";
    var a=str.concat(str1);
    console.log(a)//abcdfefdrref
    //concat 连接两个或多个字符串，返回连接后的字符串
    console.log(str.indexOf('c'));//c
    //indexof返回 substr 在字符串 str 中首次出现的位置,从 start 位置开始查找，如果不存在，则返回 -1。start可以是任意整数，默认值为 0。如果 start < 0 则查找整个字符串（如同传进了 0）。如果 start >= str.length，则该方法返回 -1，除非被查找的字符串是一个空字符串，此时返回 str.length.从最后一个位置查找lastIndexOf()
    console.log(str1.localeCompare(str));//1 
    //localCompare用本地特定的顺序来比较两个字符串，大于返回1，小于返回-1
    console.log(str.match(/ab/)) //["ab", index: 0, input: "abcdfe"]
    //match 返回一个包含匹配结果的数组，如果没有匹配项，则返回 null。如果参数传入的是一个非正则表达式对象，则会使用 new RegExp(obj) 隐式地将其转换为正则表达式对象,返回一个数组
    console.log(str.replace('a','love'));//lovebcdfe
    //replace  str.replace(regexp|substr, newSubStr|function)  替换 str 的子字符串 不改变原始字符串
    console.log(str);//abcdfe
    console.log(str.search(/d/)); //3
    //search() 查找 str 与一个正则表达式是否匹配。如果匹配成功，则返回正则表达式在字符串中首次匹配项的索引；否则，返回 -1。如果参数传入的是一个非正则表达式对象，则会使用 new RegExp(obj) 隐式地将其转换为正则表达式对象
    console.log(str.slice(3,-1))//df
    //slice 返回从 start 到 end （不包括）之间的字符，可传负值;
    console.log(str.split('b')); //["abcdfe"]
    console.log(str.split('b'));//["a", "cdfe"]
    //split 返回一个数组，分隔符 separator 可以是一个字符串或正则表达式,把字符串分割为子字符串数组
    console.log(str);//abcdfe
    console.log(str.substr(4,2)); //fe
    // 返回 str 中从指定位置开始到指定长度的子字符串，start可为负值,第一个表示索引，第二个表示长度
    console.log(str.substring(2,5));//cdf  
    //提取字符串中两个指定的索引号之间的字符  都是取前不取后
    console.log(str.toLocaleLowerCase()); //abcdfe
    console.log(str.toLocaleUpperCase()); //ABCDFE
    //toLocaleLowerCase()根据主机的语言环境把字符串转换为小写，只有几种语言（如土耳其语）具有地方特有的大小写映射，toLocaleUpperCase()反之
    console.log(str.toLowerCase());//abcdfe
    console.log(str.toUpperCase()); //ABCDFE
    //将指定字符串转换成小写或者大写
    console.log(str.toString());//abcdfe
    //tostring 	返回字符串对象值
    var a=" fdfde dffd ";
    console.log(a.trim()); //fdfde dffd
    //trim移除字符串首尾空白，中间的不移除
    console.log(str.valueOf());//abcdfe
    //返回某个字符串的原始值，和直接输出一样
```
好了，所有的字符串的方法都在这里呀；

## 判断一个字符串中出现次数最多的字符，统计这个次数

```javascript
var str = 'asdfdffhwfeuwdasdsaaaaaaaa';
    var json = {};
    //遍历str拆解其中的每一个字符，将其中某个字符得到值及其出现的次数拿出来作为json的key和value
    for (var i = 0; i < str.length; i++) {
      //判断json中是否有当前str值
      if (!json[str.charAt(i)]) {
        //如果不存在，就将当前值添加到json中去；
        json[str.charAt(i)] = 1;
      } else {
        //else的话就让数组中已有的当前值的index值++；
        json[str.charAt(i)]++;
      }
    }
    console.log(json); 
    //储存出现次数最多的值和次数
    var iMax = 0;
    var iIndex = '';
    //遍历json 使用打擂算法统计需要的值
    for (var i in json) {
      //如果当前项大于下一项
      if (json[i] > iMax) {
        //让当前值更改为出现次数最多的值
        iMax = json[i];
        iIndex = i;
      }
    }
    //打印最终结果
    console.log ('出现最多的值是'+ iIndex+ '出现的次数为'+iMax); //出现最多的值是a出现的次数为10
```
看看json 里面存入的值：
![js](/images/js-13.png)

## 编写一个方法，求一个字符串的字节长度

```javascript
 var str="22来那个";
    console.log(getStrlen(str)); //3 5
    function getStrlen(str){
      var json={len:0};
      var re=/[\u4e00-\u9fa5]/;
      for(var i=0;i<str.length;i++){
        if(re.test(str.charAt(i))){
          json['len']++;
        }
      };
      return json['len']+" "+str.length;
    }
```
关于里面的正则表达式，可以[看这里](http://www.sulishibaobei.com/js/zhengze20170925/)

## JavaScript中如何检测一个变量是一个String类型？请写出函数实现

```javascript
typeof(obj)=='string';
obj.constructor==String;
```
说到数据类型，补充一点：
```javascript
console.log(typeof null);
console.log(typeof {});
console.log(typeof []);
console.log(typeof undefined);
```
结果：
```javascript
object
object
object
undefined
```
## 给String对象定义一个repeatify方法。该方法接收一个整数参数，作为字符串重复的次数，最后返回重复指定次数的字符串
例如：console.log('hello'.repeatify(3));
输出：hellohellohello.
```javascript
   String.prototype.repeatify=String.prototype.repeatify || function(times){
      var str='';
      for(var i=0;i<times;i++){
          str+=this;
      }
      return str;
   }
   console.log('hello'.repeatify(3)) //hellohellohello
```

## 下面这段代码的结果是什么？为什么？

```javascript
function test(){
     console.log(a); //undefined
     console.log(foo()); //2
     var a=1;
     function foo(){
       return 2;
     }
   }
   test();
```
变量和函数的声明都被提前至函数体的顶部，而同时变量并没有被赋值；因此，当打印变量a时，它虽然存在于函数体，仍然是Undefined;

## 怎么添加，移动，移除，复制，创建和查找节点？
1)创建新节点
createDocumentFragment()  //创建一个DOM片段
createElement() //创建一个具体的元素
createTextNode()  //创建一个文本节点
2)添加，移除，替换，插入
appendChild() //添加
removeChild() //移除
replaceChild() //替换
insertBefore() //插入
3)查找
getElementsByTagName() //通过标签名称
getElementsByName() //通过元素的Name属性的值
getElementById() //通过元素Id,唯一性；
现在还有一个通用的：querySelector() //可以是类名，id名，标签名；

## jQuery的事件委托方法on,live,delegate以及bind之间有什么区别?
随着Dom结构的复杂化，有许多新的元素动态的添加进来，直接用click事件会发现新添加的元素并不能直接取到，这里就需要用到事件委托的方法；
什么是事件委托呢？
假定有一个dom结构是这样的body>div>a，如果点击a标签，click事件会从a一直冒泡到div和body,因此，发生在a上面的单击事件，div和body同样也可以处理，就可以是事件委托。具体来说，事件委托是事件目标本身不处理事件，而是将事件交于其父元素或者祖先元素处理，甚至根元素(document)；
先直接看看代码：
1.bind:作用是在选择到得元素上绑定事件类型的监听函数，
bind(type,[data],function(eventObject))
type:事件类型，如click，change,mouseover等；
data:传入监听函数的参数，通过event.data取到，可选;
function:监听函数，可传入event对象，这里的event是jquery封装的event对象，与原生的event对象有区别，使用时得注意；
源码：
bind:function(types,data,fn){
    return this.on(types,null,data,fn);
}
这里其实也是调用了on方法；
bind的特点就是会把监听器绑定到目标元素上，有一个绑一个，在页面上的元素不会动态添加的时候使用它没什么问题。但如果列表中动态增加一个“列表元素5”，点击它是没有反应的，必须再bind一次才行。
2.live
live(type,[data],fn)
源码：
live:function(types,data,fn){
    jQuery(this.context).on(types,this.selector,data,fn);
    return this;
}
可以看到Live方法并没有将监听器绑定到自己身上；而是绑定到this.context上，Live正是利用了事件委托机制来完成事件监听处理，把节点都委托给了document，添加新的元素不必再绑定一次监听；
缺点也是有的：默认把事件绑定到$(document)元素，如果DOM嵌套结构很深，事件冒泡通过大量祖先元素会导致性能损失；只能放在直接选择的元素后面，不能在连缀的DOM遍历方法后面使用；即$("#table td").live...可以，但$("#table").find("td").live...不行；
而且$()函数会找到当前页面中的所有td元素并创建jQuery对象，但在确认事件目标时却不用这个td元素集合，而是使用选择符表达式与event.target或其祖先元素进行比较，因而生成这个jQuery对象会造成不必要的开销;
解决的办法就是在$(document).ready()方法外部调用.live()，使其形成一个闭包，例如
```javascript
(function($){
    $("table td").live("click",function(){/*显示更多信息*/});
})(jQuery);
```
3.delegate
为了解决bind方法的局限性，jquery1.3引入了live方法，而后为了解决"事件传播链过长"问题，jquery1.4方法又指定了上下文，而为了解决无谓生成元素集合问题，jquery1.4.2引入了delegate；
将监听事件绑定在就近的父级元素上，源码:
delegate: function( selector, types, data, fn ) {
return this.on( types, selector, data, fn );
}
是这样使用的：$("table").delegate("td","click",function(){/*显示更多信息*/});
直接将目标元素选择符('td'),事件('click')以及处理程序与'受施方'(table)绑定在一起了；

4.on 
on(type,[selector],[data],fn)参数和delegate差不多，但是有细微区别;type和select换了位置，其次，select变成了可选项；而且与bind的效果一样，event.currenttarget对象是自己；
























