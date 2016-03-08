# JavaScript-Tips
偶然看到有很多人都有自己的一个javascript tips项目，这个项目是每天分享一个JavaScript Tip，受到这个项目的启发，我也打算每天更新一个小Tip，其中会有一些难以区分或不熟悉的概念、常用的算法、技巧等内容，希望借此也可以来巩固自己的javascript。

### 目录
##### 2016-03-07 至 2016-03-31

* [2016-03-07-数组去重算法](#1.1)

<h5 id='1.1'>数组去重算法</h5>
利用数组的indexOf方法
	
``` javascript
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

``` javascript
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

``` javascript
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
