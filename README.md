# JavaScript-Tips
偶然看到有很多人都有自己的一个javascript tips项目，这个项目是每天分享一个JavaScript Tip，受到这个项目的启发，我也打算每天更新一个小Tip，其中会有一些难以区分或不熟悉的概念、常用的算法、技巧等内容，希望借此也可以来巩固自己的javascript。

### 目录
##### 2016-03-08 至 2016-03-31

* [2016-03-08-数组去重算法](#1.1)
* [2016-03-09-如何打乱数组顺序](#1.2)

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
---

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
    return array;
}
```
