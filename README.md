# JavaScript-Tips
偶然看到有很多人都有自己的一个javascript tips项目，这个项目是每天分享一个JavaScript Tip，受到这个项目的启发，我也打算每天更新一个小Tip，其中会有一些难以区分或不熟悉的概念、常用的算法、技巧等内容，带着自己的思考和体会也有从其他人那学习的经验,比如[这里](https://github.com/loverajoel/jstips)。希望借此也可以来巩固自己的javascript。

2016-03-27一个人更新这个也蛮累得，可能会改为三天一个tips

### 目录
##### 2016-03-08 至 2016-03-31

* [2016-03-08-数组去重算法](#1.1)
* [2016-03-09-如何打乱数组顺序](#1.2)
* [2016-03-10-检测数据类型](#1.3)
* [2016-03-11-判断对象中是否存在某个属性](#1.4)
* [2016-03-14-变量和函数的提前声明](#1.5)
* [2016-03-15-null和undefined的区别](#1.6)
* [2016-03-16-构造函数中，new这个关键字到底做了什么](#1.7)
* [2016-03-17-经常聊到的AMD和CMD规范是什么？](#1.8)
* [2016-03-18-使用原型链+构造函数的方式实现继承](#1.9)
* [2016-03-21-嵌套函数的作用域](#1.10)
* [2016-03-22-跨浏览器的事件处理程序](#1.11)
* [2016-03-23-计算数组中的最大最小值](#1.12)
* [2016-03-24-使用 === 替代 == ](#1.13)
* [2016-03-25-如何判断某变量是否为数组数据类型？](#1.14)

<h5 id='1.1'>数组去重算法</h5>
数组去重相信应该遇到的情况会有很多，这里提供几种方法。
利用数组的indexOf方法
```javascript
function unique(arr){
	var result=[];
	for(var i=0;i<arr.length;i++){
		if(result.indexOf(arr[i])==-1){
			result.push(arr[i]);
		}
	}
	return result;
}
```
利用hash表,可能会出现字符串和数字一样的话出错，如var a = [1, 2, 3, 4, '3', 5],会返回[1, 2, 3, 4, 5]

```javascript
function unique_1(arr){
	var hash={},result=[];
	for(var i=0;i<arr.length;i++){
		if(!hash[arr[i]]){
			hash[arr[i]]=true;
			result.push(arr[i]);
		}
	}
	return result;
}
```

排序后比较相邻，如果一样则放弃，否则加入到result。会出现与方法2一样的问题，如果数组中存在1,1,'1'这样的情况，则会排错

```javascript
function unique_2(arr){
	arr.sort();
	var result=[arr[0]];
	for(var i=1;i<arr.length;i++){
		if(arr[i]!==arr[i-1]){
			result.push(arr[i]);
		}
	}
	return result;
}
```
最简单但是效率最低的算法,也不会出现方法2和方法3出现的bug

```javascript
function unique_3(arr){
	for(var i=0;i<arr.length;i++){
		for(var j=i+1;j<arr.length;j++){
			if(arr[i]==arr[j]) {
				arr.splice(j,1);
				j--;
			}
		}
	}
	return arr;
}
```
--

 <h5 id='1.2'>打乱数组顺序</h5>
有时候我们通常会遇到要打乱一个数组内容顺序的情况，这里提供三种方法来打乱数组。

每次随机抽一个数并移动到新数组中，然后用逻辑判断。

```javascript
function shuffle(array){
    var copy=[],
        n=array.length,
        i;
    // 如果还剩有元素则继续。。。
    while(n){
        i=Math.floor(Math.random()*array.length);
        // 如果这个元素之前没有被选中过。。
        if(i in array){
            copy.push(array[i]);
            delete  array[i];
            n--;
        }
    }
    return copy;
}
```
跟方法1类似，只不过通过splice来去掉原数组已选项

```javascript
function shuffle_1(array) {
    var copy = [],
        n = array.length,
        i;
    // 如果还剩有元素。。
    while (n) {
        // 随机选取一个元素
        i = Math.floor(Math.random() * n--);
        // 移动到新数组中
        copy.push(array.splice(i, 1)[0]);
    }
    return copy;
}
```
前面随机抽数依次跟末尾的数交换，后面依次前移，即：第一次前n个数随机抽一个跟第n个交换，第二次前n-1个数跟第n-1个交换，依次类推。

```javascript
function shuffle_2(array){
    var m=array.length,
        t,i;
    while(m){
        i=Math.floor(Math.random()*m--);
        t=array[m];
        array[m]=array[i];
        array[i]=t;
    }
    return arraidy;
}
```
--
 <h5 id='1.3'>检测数组类型</h5>
我们通常会使用typeof、instanceof、isArray来检测数据的类型，这里我介绍一种万能型的：调用Object的toString方法。

```javascript
function a(){console.log("yes");}
console.log(Object.prototype.toString.call(a)); // [object Function]
var b = 123;
console.log(Object.prototype.toString.call(b)); // [object Number]
var c = "heke";
console.log(Object.prototype.toString.call(c)); // [object String]
var d = true;
console.log(Object.prototype.toString.call(d)); // [object Boolean]
var e = [1,2,3];
console.log(Object.prototype.toString.call(e)); // [object Array]
var f;
console.log(Object.prototype.toString.call(f)); // [object Undefined]
var g = null;
console.log(Object.prototype.toString.call(g)); // [object Null]
var h = {"name":"heke"};
console.log(Object.prototype.toString.call(h)); // [object Object]
```
使用`call`调用`Object`的`toString`方法，将会返回一个遵循[object NativeConstructorName]格式的字符串。其中`NativeConstructorName`指的就是变量的构造函数名

--

 <h5 id='1.4'>判断对象中是否存在某个属性</h5>
我们经常需要用到对象中的某个属性，为了保证程序的健壮性，在使用之前我们需要判断这个属性是否存在，可以用if来判断

```javascript
if(obj[property] !== undefined){
// do something
}
```

除此之外，我们还可以使用两种原生的方法，因为每一个object都继承自Object，所以都拥有 in operator 和 Object.hasOwnProperty方法

```javascript
function Person (name) {
    this.name  = name;
}
Person.prototype.age = '22';

var person = new Person("heke");
console.log(person.hasOwnProperty('name'));    // true
console.log("name" in person);                // true

console.log(person.hasOwnProperty('age'));    // false
console.log("age" in person);                 // true
```
`hasOwnProperty`和`in`都可以用来判断某个属性是否存在于对象中，区别就是`hasOwnProperty`不能搜索到从原型链继承的属性，而`in`可以。


--

 <h5 id='1.5'>变量和函数的提前声明</h5>

在JavaScript里 变量声明 是让系统知道这个变量存在，在ES5中变量定义是给这个变量赋值。变量声明会被提前到顶部，而变量定义不会。

```javascript
console.log(a);   // "ReferenceError: a is not defined"
```
上面的代码报变量没有定义的错误。

```javascript
console.log(a); // undefined
var a = 3;
```
上面的代码提示undefined，不过我们在这里对a同时做了声明和定义两个操作，从结果来看，只有声明被提前了，而定义却没有。

在ES6中，我们多了let和const两个来定义变量的命令，不过它们不像var那样会发生“变量提升”现象。所以，使用它们定义变量，变量一定要在声明后使用，否则报错。

接下来看函数：

```javascript
getName();                  // "heke"
function getName(){
    console.log("heke");
}
```
结果说明我们对函数的声明被提前了。

```javascript
getName();       // "TypeError: getName is not a function"
var getName=function(){
    console.log("heke");
}
```
事实上，第一种方式叫函数声明，这种方式能被提前到顶部，第二种方式叫函数表达式，不会被提前。

--

 <h5 id='1.6'>null和undefined的区别</h5>
 
+ undefined表示变量没有被声明或者声明了但是没有被赋值。
+ null表示这个变量不是一个值。
+ type of undefined的值为undefined
+ type of null的值为object
+ null和undefined的布尔值都为false
+ null == undefined的值为true
+ null === undefined的值为false
+ 在JSON里undefined不可用，而null是可用的
+ JavaScript里不会把一个值设置为null，只有程序员自己才能把一个值设置为null

--

 <h5 id='1.7'>构造函数中，new这个关键字到底做了什么</h5>
 
 ```javascript
 function Person(name,age){
    this.name = name;
    this.age = age;
}
var person1 = new Person("heke", "21");
 ```
我们以上面的代码为例说明new这个关键字做了哪些事。(来源于《javascript高级程序设计》)

1. 创建一个新对象person1
2. 将构造函数Person的作用域赋给person1,这样对象person1就可以访问构造函数Person里的代码。this就指向对象person1。
3. 执行构造函数里的代码，对象person的name和age分别被赋值为heke、21。
4. 返回新对象，这样我们就得到了对象person。

--
 
 <h5 id='1.8'>经常聊到的AMD和CMD规范是什么？</h5>
 
 AMD规范是RequireJS在推广过程中对模块定义的规范，CMD则是SeaJS。这些规范的目的都是为了javascript的模块化开发，特别是在浏览器端。目前这些规范的实现都能达成浏览器模块化开发的目的。

两者间的区别：

 1. 对于依赖的模块，AMD是提前执行，CMD是延迟执行。不过RequireJS从2.0开始，也改成了延迟执行（根据写法不同，处理方式不同）。
 2. CMD推崇依赖就近，AMD推崇依赖前置。
 3. AMD的API是一个当多个用（require分局部和全局），而CMD的API严格区分，推崇职责单一。
 4. AMD速度快但浪费资源，CMD则性能表现差一些。
 
--
 
 <h5 id='1.9'>使用原型链+构造函数的方式实现继承</h5> 
 组合继承，有时候也叫伪经典继承，指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。其背后的思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。这样，既通过在原型上定义方法实现了函数复用，又能够保证每个实例都有它自己的属性。
 
 下面来看一个例子：
 
 ```javascript
 function Person(name){
 	this.name=name;
 }
 Person.prototype.sayName=function(){
 	console.log(this.name);
 }
 function Man(name,age){
 	//继承属性
 	Person.call(this,name);
 	this.age=age;
 }
 //继承方法
 Man.prototype=new Person();
 Man.prototype.constructor=Person;
 Man.prototype.sayAge=function(){
 	console.log(this.age);
 }
 
 var heke=new Man("heke",21);
 var heke1=new Man("heke1",21);
 heke.sayName();//"heke"
 heke1.sayName();//"heke1"
 
 ```
 在这个例子中，Man实现了对Person的继承，俩个不同的实例分别拥有自己的属性又可以使用共同的方法。
 
--

 <h5 id='1.10'>嵌套函数的作用域</h5>
 首先来比较两段代码
 
 ```javascript
  var x = 'global';
    function f () {
        var x = 'local';
        function g() {
            alert(x);
        }
        g();
    }
    f(); // 'local'
 ```
 
 ```javascript
 var x='global';
 function g(){
 	alert(x);
 }
 function f(){
 var x='local';
 g();
 }
 f();//'global'
 ```
 这两段代码之间输出结果的差别其实就是因为嵌套函数的作用域不同造成的。首先，JavaScript的函数是通过词法来划分作用域的，而不是动态的划分作用域的，于是，函数的是在定义它们的作用域中运行，而不是在执行它们的作用域中运行。
 
 在第一段代码中，当f（）调用的时候，作用域链可以理解为由两部分组成，包含f这一调用的调用对象，然后后面是全局对象。此时查找x的值，会先从f的调用对象中查找，如果没有，再查找后面全局对象中x。同理，g因为是f的一个嵌套函数，那么，g调用的时候，作用域链应该就是由三部分组成了，g的调用对象，f的调用对象，和全局对象。函数g是要输出x的值，所以会先在g的调用对象中查找x的值，g中没有定义，接下来查找外围f调用对象中x的定义，于是找到了x='local'，那么就会输出x，而不会继续往下查找全局对象了。  如果f中也没定义x的值，那么就会继续查找作用域链后面的全局对象，结果就是global了。如果全局对象中也没定义，那么自然就是undefined。
 
 在第二段代码中，g在全局环境中定义，所以作用域链为g的调用对象，全局对象，所以在g中找不到时，就会去找全局对象，global。
 
--

 <h5 id='1.11'>跨浏览器的事件处理程序</h5>
 
 为了以跨浏览器的方式处理事件，不少开发人员会使用能够隔离浏览器差异的JS库， 虽然DOM和IE中得event对象不同，但基于它们之间的相似性依旧可以拿出跨浏览器的方案来。
 
 ```javascript
 var EventUtil={
	addHandler:function(element,type,handler){
		if(element.addEventListener){
			element.addEventListener(type,handler,false);
		}
		else if(element.attachEvent){
			element.attachEvent("on"+type,handler);
		}
		else{
			element["on"+type]=handler;
		}
	},
	getEvent:function(event){
		return event ? event:window.event;
	}，
	getTarget：function(event){
		return event.target||event.srcElement;
	},
	preventDefault:function(event){
		if(event.preventDefault){
			event.preventDefault();
		}
		else{
			event.returnValue=false;
		}
	},
	stopPropggation:function(event){
		if(event.stopPropggation){
			event.stopPropggation();
		}
		else{
			event.cancelBubble=true;
		}
	},
	removeHandler:function(element,type,handler){
		if(element.removeEventListener){
			element.removeEventListener(type,handler,false);
		}
		else if(element.detachEvent){
			element.detachEvent("on"+type,handler);
		}
		else{
			element["on"+type]=null;
		}
	}
}
 ```
 
--

 <h5 id='1.12'>计算数组中的最大最小值</h5>
 我们知道，内置函数Math.max()和Math.min()可以计算参数中的最大值和最小值。
 
 ```javascript
 Math.max(1,2,3,4,5) //5
 Math.min(1,2,3,4,5) //1
 ```
 不过这内置函数并不能直接计算数组中数值的最大值和最小值，不过我们可以利用Function.prototype.apply()来实现
 
 ```javascript
 Math.max.apply(null,[1,2,3,4,5]) //5
 Math.min.apply(null,[1,2,3,4,5]) //1
 ```
 不过在ES6中有其他的方法
 
 ```javascript
 var number=[1,2,3,4,5];
 Math.max(...number); //5
 Math.min(...number); //1
 ```
 
--

 <h5 id='1.13'>使用 === 替代 ==</h5>
 ==和(!=)操作会进行自动类型转换，而===和(!==)不会进行类型转换，当我们要比较类型的值，用===会更快也更严谨
 
 ```javascript
 [10] ==  10      // is true
 [10] === 10      // is false

 '10' ==  10      // is true
 '10' === 10      // is false

  []  ==  0       // is true
  []  === 0       // is false

  ''  ==  false   // is true but true == "a" is false
  ''  === false   // is false
 ```

--

 <h5 id='1.14'>如何判断某变量是否为数组数据类型？</h5> 
 
 * 判断其是否具有“数组性质”，如slice()方法。可自己给该变量定义slice方法，故有时会失效
 * obj instanceof Array 在某些IE版本中不正确
 * 方法一二皆有漏洞，在ECMA Script5中定义了新方法Array.isArray(), 保证其兼容性，最好的方法如下：

 ```javascript
if(typeof Array.isArray==="undefined"){
Array.isArray=function(arg){
return Object.prototype.toString.call(arg)==="[object Array]"
}
}
 ```
