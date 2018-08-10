# ES6

# ES7 特性

1. Array.prototype.includes
2. Exponentiation Operator(求幂运算)

## Array.prototype.includes

Array.prototype.includes 的用法很简单，用来检查数组中是否存在某个值。ES5中 indexOf（）方法返回一个元素在数组中的位置或者没找到的情况下返回-1。它返回一个数字，而不是一个布尔值,开发人员需要实施额外的检查。(以下为ES6中检查数组中是否存在某个值的用法）

``` javascript
let arr = ['react', 'angular', 'vue']

if (arr.indexOf('react') !== -1) {
  console.log('Can use React')
}

// 或者使用~（位异非），对任何数字 a 相当于 -(a + 1)
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

## 求幂运算符（**）

基本用法

>     3 ** 2           // 9

效果同：

>    Math.pow(3, 2)   // 9

** 是一个用于求幂的中缀算子，比较可知，中缀符号比函数符号更简洁，这也使得它更为可取。 下面让我们扩展下思路，既然说**是一个运算符，那么它就应该能满足类似加等的操作，我们姑且称之为幂等，例如下面的例子，a的值依然是9：

>     let a = 3
>     a **= 2  // 9
 

对比下其他语言的指数运算符：

Python: x ** y
CoffeeScript: x ** y
F#: x ** y
Ruby: x ** y
Perl: x ** y
Lua, Basic, MATLAB: x ^ y
不难发现，ES的这个新特性是从其他语言（Python，Ruby等）模仿而来的。


# ES8新特性

## 异步函数(Async functions)

* 为什么要引入async

众所周知，JavaScript语言的执行环境是“单线程”的，那么异步编程对JavaScript语言来说就显得尤为重要。以前我们大多数的做法是使用回调函数来实现JavaScript语言的异步编程。回调函数本身没有问题，但如果出现多个回调函数嵌套，例如：进入某个页面，需要先登录，拿到用户信息之后，调取用户商品信息，代码如下：

``` javascript
this.$http.jsonp('/login', (res) => {
  this.$http.jsonp('/getInfo', (info) => {
    // do something
  })
})
```

假如上面还有更多的请求操作，就会出现多重嵌套。代码很快就会乱成一团，这种情况就被称为“回调函数地狱”（callback hell）。

于是，我们提出了Promise，它将回调函数的嵌套，改成了链式调用。写法如下：

``` javascript
var promise = new Promise((resolve, reject) => {
  this.login(resolve)
})
.then(() => this.getInfo())
.catch(() => { console.log("Error") })
```

从上面可以看出，Promise的写法只是回调函数的改进，使用then方法，只是让异步任务的两段执行更清楚而已。Promise的最大问题是代码冗余，请求任务多时，一堆的then，也使得原来的语义变得很不清楚。此时我们引入了另外一种异步编程的机制：Generator。

Generator 函数是一个普通函数，但是有两个特征。一是，function关键字与函数名之间有一个星号；二是，函数体内部使用yield表达式，定义不同的内部状态（yield在英语里的意思就是“产出”）。一个简单的例子用来说明它的用法：

``` javascript
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
```

上面代码定义了一个 Generator 函数helloWorldGenerator，它内部有两个yield表达式（hello和world），即该函数有三个状态：hello，world 和 return 语句（结束执行）。Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，必须调用遍历器对象的next方法，使得指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。换言之，Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。上述代码分步执行如下：

``` javascript
hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }
```

Generator函数的机制更符合我们理解的异步编程思想。

用户登录的例子，我们用Generator来写，如下：

``` javascript
var gen = function* () {
  const f1 = yield this.login()
  const f2 = yield this.getInfo()
};
```

虽然Generator将异步操作表示得很简洁，但是流程管理却不方便（即何时执行第一阶段、何时执行第二阶段）。此时，我们便希望能出现一种能自动执行Generator函数的方法。我们的主角来了：async/await。

ES8引入了async函数，使得异步操作变得更加方便。简单说来，它就是Generator函数的语法糖。

``` javascript
async function asyncFunc(params) {
  const result1 = await this.login()
  const result2 = await this.getInfo()
}
``` 

是不是更加简洁易懂呢？

变体
异步函数存在以下四种使用形式：

函数声明： async function foo() {}
函数表达式： const foo = async function() {}
对象的方式： let obj = { async foo() {} }
箭头函数： const foo = async () => {}

### 常见用法汇总

处理单个异步结果：

``` javascript
async function asyncFunc() {
  const result = await otherAsyncFunc();
  console.log(result);
}
```

顺序处理多个异步结果：

``` javascript
async function asyncFunc() {
  const result1 = await otherAsyncFunc1();
  console.log(result1);
  const result2 = await otherAsyncFunc2();
  console.log(result2);
}
```

并行处理多个异步结果：

``` javascript
async function asyncFunc() {
  const [result1, result2] = await Promise.all([
    otherAsyncFunc1(),
    otherAsyncFunc2()
  ]);
  console.log(result1, result2);
}
``` 

处理错误：

``` javascript
async function asyncFunc() {
  try {
    await otherAsyncFunc();
  } catch (err) {
    console.error(err);
  }
}
``` 

若想进一步了解async的具体实践，可参见[阮一峰的博客文章](es6.ruanyifeng.com/#docs/async)

## Object.entries()和Object.values()

### Object.entries()

如果一个对象是具有键值对的数据结构，则每一个键值对都将会编译成一个具有两个元素的数组，这些数组最终会放到一个数组中，返回一个二维数组。简言之，该方法会将某个对象的可枚举属性与值按照二维数组的方式返回。若目标对象是数组时，则会将数组的下标作为键值返回。例如：

``` javascript
Object.entries({ one: 1, two: 2 })    //[['one', 1], ['two', 2]]
Object.entries([1, 2])                //[['0', 1], ['1', 2]]
```

注意：键值对中，如果键的值是Symbol，编译时将会被忽略。例如：

>     Object.entries({ [Symbol()]: 1, two: 2 })       //[['two', 2]]

Object.entries()返回的数组的顺序与for-in循环保持一致，即如果对象的key值是数字，则返回值会对key值进行排序，返回的是排序后的结果。例如：

>     Object.entries({ 3: 'a', 4: 'b', 1: 'c' })    //[['1', 'c'], ['3', 'a'], ['4', 'b']]
 

使用Object.entries()，我们还可以进行对象属性的遍历。例如：

``` javascript
let obj = { one: 1, two: 2 };
for (let [k,v] of Object.entries(obj)) {
  console.log(`${JSON.stringify(k)}: ${JSON.stringify(v)}`);
}

//输出结果如下：
'one': 1
'two': 2
 ```

### Object.values()

它的工作原理跟Object.entries()很像，顾名思义，它只返回自己的键值对中属性的值。它返回的数组顺序，也跟Object.entries()保持一致。

``` javascript
Object.values({ one: 1, two: 2 })            //[1, 2]
Object.values({ 3: 'a', 4: 'b', 1: 'c' })    //['c', 'a', 'b']
```

## 字符串填充：padStart和padEnd

ES8提供了新的字符串方法-padStart和padEnd。padStart函数通过填充字符串的首部来保证字符串达到固定的长度，反之，padEnd是填充字符串的尾部来保证字符串的长度的。该方法提供了两个参数：字符串目标长度和填充字段，其中第二个参数可以不填，默认情况下使用空格填充。

``` javascript
'Vue'.padStart(10)           //'       Vue'
'React'.padStart(10)         //'     React'
'JavaScript'.padStart(10)    //'JavaScript'
 ```

可以看出，多个数据如果都采用同样长度的padStart，相当于将呈现内容右对齐。

上面示例中我们只定义了第一个参数，那么我们现在来看看第二个参数，我们可以指定字符串来代替空字符串。

``` javascript
'Vue'.padStart(10, '_*')           //'_*_*_*_Vue'
'React'.padStart(10, 'Hello')      //'HelloReact'
'JavaScript'.padStart(10, 'Hi')    //'JavaScript'
'JavaScript'.padStart(8, 'Hi')     //'JavaScript'
 ```

从上面结果来看，填充函数只有在字符长度小于目标长度时才有效，若字符长度已经等于或小于目标长度时，填充字符不会起作用，而且目标长度如果小于字符串本身长度时，字符串也不会做截断处理，只会原样输出。

padEnd函数作用同padStart，只不过它是从字符串尾部做填充。来看个小例子：

``` javascript
'Vue'.padEnd(10, '_*')           //'Vue_*_*_*_'
'React'.padEnd(10, 'Hello')      //'ReactHello'
'JavaScript'.padEnd(10, 'Hi')    //'JavaScript'
'JavaScript'.padEnd(8, 'Hi')     //'JavaScript'
```

## Object.getOwnPropertyDescriptors()

顾名思义，该方法会返回目标对象中所有属性的属性描述符，该属性必须是对象自己定义的，不能是从原型链继承来的。先来看个它的基本用法：

``` javascript
let obj = {
  id: 1,
  name: 'test',
  get gender() {
    console.log('gender')
  },
  set grade(g) {
    console.log(g)
  }
}
Object.getOwnPropertyDescriptors(obj)

//输出结果为：
{
  gender: {
    configurable: true,
    enumerable: true,
    get: f gender(),
    set: undefined
  },
  grade: {
    configurable: true,
    enumerable: true,
    get: undefined,
    set: f grade(g)
  },
  id: {
    configurable: true,
    enumerable: true,
    value: 1,
    writable: true
  },
  name: {
    configurable: true,
    enumerable: true,
    value: 'test',
    writable: true
  }
}
```

方法还提供了第二个参数，用来获取指定属性的属性描述符。

``` javascript
let obj = {
  id: 1,
  name: 'test',
  get gender() {
    console.log('gender')
  },
  set grade(g) {
    console.log(g)
  }
}
Object.getOwnPropertyDescriptors(obj, 'id')

//输出结果为：
{
  id: {
    configurable: true,
    enumerable: true,
    value: 1,
    writable: true
  }
}
```

由上述例子可知，该方法返回的描述符，会有两种类型：数据描述符、存取器描述符。返回结果中包含的键可能的值有：configurable、enumerable、value、writable、get、set。

使用过Object.assign()的同学都知道，assign方法只能拷贝一个属性的值，而不会拷贝它背后的复制方法和取值方法。Object.getOwnPropertyDescriptors()主要是为了解决Object.assign()无法正确拷贝get属性和set属性的问题。

``` javascript
let obj = {
  id: 1,
  name: 'test',
  get gender() {
    console.log('gender')
  }
}
Object.assign(obj)

//输出结果为：
{
  gender: undefined
  id: 1,
  name: 'test'
}
```

此时，Object.getOwnPropertyDescriptors方法配合Object.defineProperties方法，就可以实现正确拷贝。

``` javascript
let obj = {
  id: 1,
  name: 'test',
  get gender() {
    console.log('gender')
  }
}
let obj1 = {}
Object.defineProperties(obj1, Object.getOwnPropertyDescriptors(obj))
Object.getOwnPropertyDescriptors(obj1)

//输出结果为：
{
  gender: {
    configurable: true,
    enumerable: true,
    get: f gender(),
    set: undefined
  },
  id: {
    configurable: true,
    enumerable: true,
    value: 1,
    writable: true
  },
  name: {
    configurable: true,
    enumerable: true,
    value: 'test',
    writable: true
  }
}
```

上述代码演示了，我们如何来拷贝一个属性值为赋值方法或者取值方法的对象。

## 共享内存和原子（Shared memory and atomics）

ES8引入了两部分内容：新的构造函数SharedArrayBuffer、具有辅助函数的命名空间对象Atomics。共享内存允许多个线程并发读写数据，而原子操作则能够进行并发控制，确保多个存在竞争关系的线程顺序执行。

共享内存和原子也称为共享阵列缓冲区，它是更高级的并发抽象的基本构建块。它允许在多个工作者和主线程之间共享SharedArrayBuffer对象的字节（缓冲区是共享的，用以访问字节，将其包装在类型化的数组中）。这种共享有两个好处：

* 可以更快地在web worker之间共享数据
* web worker之间的协调变得更加简单和快速

那么，我们为什么要引入共享内存和原子的概念呢？以及SharedArrayBuffer的竞争条件是什么，Atomics又是如何解决这种竞争的？推荐下面的文章，文章讲解很详细，图文并茂，带你深入了解SharedArrayBuffer和Atomics。

内存管理碰撞课程：segmentfault.com/a/1190000009878588

图解 ArrayBuffers 和 SharedArrayBuffers：segmentfault.com/a/1190000009878632

用 Atomics 避免 SharedArrayBuffers 竞争条件：segmentfault.com/a/1190000009878699

Atomics对象提供了许多静态方法，配合SharedArrayBuffer对象一起使用，可以帮助我们去构建一个内存共享的多线程编程环境。Atomic操作安装在Atomics模块上。与其他全局对象不同，Atomics不是构造函数。您不能使用new操作符或Atomics作为函数调用该对象。所有的属性和方法Atomics都是静态的，这一点跟Math类似。下面链接贴出了Atomics提供的一些基本方法：

developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics

关于共享内存和原子的深入研究，也可以参考Axel Rauschmayer博士的《Exploring ES2016 and ES2017》一书中的内容。具体章节链接: exploringjs.com/es2016-es2017/ch_shared-array-buffer.html

## 函数参数列表与调用中的尾部逗号

该特性允许我们在定义或者调用函数时添加尾部逗号而不报错。

``` javascript
let foo = function (
  a,
  b,
  c,
) {
  console.log('a:', a)
  console.log('b:', b)
  console.log('c:', c)
}
foo(1, 3, 4, )

//输出结果为：
a: 1
b: 3
c: 4
```

上面这种方式调用是没有问题的。函数的这种尾逗号也是向数组和字面量对象中尾逗号看齐，它适用于那种多行参数并且参数名很长的情况，开发过程中，如果忘记删除尾部逗号也没关系，ES8已经支持这种写法。

这么用有什么好处呢？

首先，当我们调整结构时，不会因为最后一行代码的位置变动，而去添加或者删除逗号。

其次，在版本管理上，不会出现因为一个逗号，而使本来只有一行的修改，变成两行。例如下面：

``` javascript

(
  'abc'
)
到

(
  'abc',
  'def'
)
```

在我们版本管理系统里，它会监测到你有两处更改，但是如果我们不必去关心逗号的存在，每一行都有逗号时，新加一行，也只会监测到一行的修改。

# 参考资料
[1] [ES6](www.ecma-international.org/ecma-262/6.0/index.html)
[2] [ES7](www.ecma-international.org/ecma-262/7.0/index.html)
[3] [ES8](www.ecma-international.org/ecma-262/8.0/index.html)
[4] [ECMAScript 6 入门](es6.ruanyifeng.com/#docs/intro)
[5] [10分钟学会ES7+ES8](juejin.im/entry/5a65b0ee5188257333662bac)
