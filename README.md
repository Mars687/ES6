# ES6

# ES7 特性

1.Array.prototype.includes
2.Exponentiation Operator(求幂运算)

## Array.prototype.includes

Array.prototype.includes 的用法很简单，用来检查数组中是否存在某个值。ES5中 indexOf（）方法返回一个元素在数组中的位置或者没找到的情况下返回-1。它返回一个数字，而不是一个布尔值,开发人员需要实施额外的检查。(以下为ES6中检查数组中是否存在某个值的用法）

``` javascript
let arr = ['react', 'angular', 'vue']

if (arr.indexOf('react') !== -1) {
  console.log('Can use React')
}

// 或者使用一点点hack 位运算符 ~ 使代码更加紧凑一些，因为~（位异或）对任何数字相当于-(a + 1):
if (~arr.indexOf('react')) {
  console.log('Can use React')
}
```

数组的 includes 方法有如下签名：

Array.prototype.includes（value：任意值）： boolean
如果传入的值在当前数组（this）中则返回 true，否则返回 false：
``` javascript
['a', 'b', 'c'].includes('a')  // true
['a', 'b', 'c'].includes('d')  // false
```
includes 方法与 indexOf 方法很相似——下面两个表达式是等价的：

arr.includes(x)
arr.indexOf(x) >= 0

唯一的区别是 includes() 方法能找到 NaN，而 indexOf() 不行：
``` javascript
[NaN].includes(NaN)  // true
[NaN].indexOf(NaN)  // -1
```

includes 不会区分 +0 和 -0 （这也与其他 JavaScript 特性表现一致）：

>     [-0].includes(+0)  // true

类型数组也有 includes() 方法：

Typed Arrays will also have a method includes():

``` javascript
let tarr = Uint8Array.of(12, 5, 3);
console.log(tarr.includes(5)); // true
```
