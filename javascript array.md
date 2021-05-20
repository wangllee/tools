# javascript中array常用函数整理
1.**shift()**、**pop()**  

分别**取数组中的第一个和最后一个**，此方法会改变原数组。

```javascript
const numbers = [1,2,3,4,5];
console.log(numbers.shift());
console.log(numbers.pop());
console.log(numbers);

> 1
> 5
> Array [2, 3, 4]
```

2.**concat()**

此方法用于**合并两个或多个数组**。此方法不会更改现有数组，而是返回一个新数组

```javascript
const num1 = [[1]];
const num2 = [2, [3]];
const numbers = num1.concat(num2);
console.log(numbers); 
num1[0].push(4);  //concat复制引用类型，所以munmbers也发生了变化
console.log(numbers);

> Array [Array [1], 2, Array [3]]
> Array [Array [1, 4], 2, Array [3]]
```

3.**forEach()**

此方法对数组的**每个元素执行一次函数**。

语法：arr.forEach(callback(element[, index [, array]])[, thisArg])

```
callback：为数组中每个元素执行的函数，该函数接收一至三个参数：
	element：当前元素
	index：可选 当前元素索引
	array：可选 数组本身
thisArg：可选 当执行回调函数 callback 时，用作 this 的值
```

callback 可用箭头函数简写，arr.forEach((item,index,array)=>{})。

4.**every()**

此方法测试一个数组内的所有元素是否都能通过某个指定函数的测试。它返回一个布尔值。

语法：arr.every(callback(element[, index [, array]])[, thisArg])

返回值：bool，如果回调函数的每一次返回都为 truthy 值，返回 true ，否则返回 false。即当所有的元素都符合条件才会返回true 。

```javascript
//所有数据都大于10
console.log([12, 15, 8, 30, 144].every(x => x > 10));
console.log([12, 15, 18, 30, 144].every(x => x > 10)); 

> false
> true
```

5.**some()**

此方法测试数组中是不是至少有1个元素通过了被提供的函数测试。它返回的是一个Boolean类型的值。

语法：arr.some(callback(element[, index [, array]])[, thisArg])

返回值：bool，数组中有至少一个元素通过回调函数的测试就会返回true；所有元素都没有通过回调函数的测试返回值才会为false。用来**判断数组中是否含有某数据**时使用。

```javascript
//是否存在比10大的数据
console.log([1, 5, 8, 30, 144].some(x => x > 10));
console.log([2 ,5, 8, 0, 4].some(x => x > 10)); 

> true
> false
```

5.**filter()**

此方法从数组中**筛选符合条件的数据**组成新的数组。

语法：arr.filter(callback(element[, index[, array]])[, thisArg])

返回值：Array，符合条件的数组。

```javascript
//字符串长度大于6的数据
const words = ['spray', 'limit', 'exuberant', 'destructe'];
const result = words.filter(word => word.length > 6);
console.log(result);

> Array ["exuberant", "destructe"]
```

6.find（findIndex）

此方法数组中第一个满足所提供测试函数的元素的值（索引）

语法：arr.filter(callback(element[, index[, array]])[, thisArg])

返回值：第一个符合条件的数据（的索引）

```javascript
const array1 = [5, 12, 15, 30, 144];
const found = array1.find(element => element > 10);
console.log(found);

> > "value:" 12 "	index:" 1
```



















