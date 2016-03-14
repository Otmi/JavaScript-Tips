# JavaScript-Tips
偶然看到有很多人都有自己的一个javascript tips项目，这个项目是每天分享一个JavaScript Tip，受到这个项目的启发，我也打算每天更新一个小Tip，其中会有一些难以区分或不熟悉的概念、常用的算法、技巧等内容，带着自己的思考和体会也有从其他人那学习的经验,比如[这里](https://github.com/loverajoel/jstips)。希望借此也可以来巩固自己的javascript。

### 目录
##### 2016-03-08 至 2016-03-31

* [2016-03-08-数组去重算法](#1.1)
* [2016-03-09-如何打乱数组顺序](#1.2)
* [2016-03-10-检测数据类型](#1.3)
* [2016-03-11-判断对象中是否存在某个属性](#1.4)
* [2016-03-14-变量和函数的提前声明](#1.5)

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


