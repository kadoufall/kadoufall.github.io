---
title: JS-语法相关
date: 2017-03-27 11:09:54
tags:
    - JavaScript
---

## 原始数据类型
JS**原始数据类型(primitive type)**6种
Undefined, Null, Boolean, Number, Symbol(ES6), String
  - Object是复杂数据类型
  - 原始数据类型按值传递，不可更改！
  - 复杂数据类型按引用传递

<!-- more -->

---

## typeof操作符
typeof操作符有7种返回形式的**字符串**
  - "undefined"    
  - "boolean"
  - "string"
  - "number"
  - "object"      值为**对象或者null**的时候
  - "function"
  - "symbol" 

```js
var a;
typeof a;				// "undefined"

a = "hello world";
typeof a;				// "string"

a = 42;
typeof a;				// "number"

a = true;
typeof a;				// "boolean"

a = null;
typeof a;				// "object" -- weird, bug

a = undefined;
typeof a;				// "undefined"

a = { b: "c" };
typeof a;				// "object"

a = Symbol();
typeof a;				// "symbol"

// 检测null
a = null;
(!a && typeof a == 'object')     // true
```

---

## 函数与函数返回值
注意返回值
foo是函数，foo()是函数的返回值
``` js
function foo() {
	return 42;
}

foo.bar = "hello world";

typeof foo;			// "function"
typeof foo();		  // "number"
typeof foo.bar;		// "string"
```

---

## `==`与`===`在`object`的比较
注意`==`与`===`在`object`的比较中，比较的是引用
- 下列代码中，比较`array`与`string`的时候，默认将`array`中元素以`,`相连为字符串再比较

```js
var a = [1,2,3];
var b = [1,2,3];
var c = "1,2,3";

a == c;		// true
b == c;		// true
a == b;		// false
```

---

## 变量的比较
注意两个`string`进行比较的时候，可以比较，是按照**字典序**进行比较
而一个或者两个不是`string`的时候，两个值都会强制转化为`number`
- 下面代码中`b`被强制转化成了`NaN`，而`NaN`与任何数比较都是`false`

```js
var a = 42;
var b = "foo";

a < b;		// false
a > b;		// false
a == b;		// false
```

---

## JavaScript是**编译型**语言
JavaScript是**编译型**语言，编译发生在代码执行前的很短的时间内（几微秒）。
- 编译的三个过程：分词/词法分析(Tokenizing/Lexing)；解析/语法分析(Parsing)；代码生成
- 对于`let a = 2;`这一语句，JS执行的时候，首先是编译器在当前作用域中声明一个变量（如果之前未声明的话），然后运行的时候引擎在该作用域中找到该变量，若找到则对其赋值。
-` LHS`引用（赋值操作的目标是谁）与`RHS`引用（谁是赋值操作的源头）
```js
function foo(a) {
    var b = a;
    return a + b;
}
var c = foo( 2 );
```
这里`LHS`查询有`3`处：`c=...` ; `a=2` ; `b=...`
`RHS`有`4`处：`foo(2)` ;   `= a`; `a ..` ; `+ b`  
- 区分`LHS`和`RHS`引用的原因在于：
  - 不成功的`RHS` 引用会导致抛出`ReferenceError` 异常
  - 不成功的`LHS `引用会导致自动隐式地创建一个全局变量（非严格模式下），该变量使用`LHS` 引用的目标作为标识符，或者抛出`ReferenceError` 异常（严格模式下）

---

## 欺骗语法
- `eval()`函数
```js
function foo(str, a) {
    eval( str );     // 欺骗！
    console.log( a, b );
}
var b = 2;
foo( "var b = 3;", 1 );     // 1, 3
```
  - `with`关键字：用于方便地重复引用同一个对象中的多个属性。但是可能会创建全局变量。
```js
function foo(obj) {
      with (obj) {
          a = 2;
      }
}
var o1 = {
      a: 3
};
var o2 = {
      b: 3
};
foo( o1 );
console.log( o1.a ); // 2
foo( o2 );
console.log( o2.a );     // undefined
console.log( a );         // 2——不好，a 被泄漏到全局作用域上了！
```

---

## 立即执行函数表达式
```js
var a = 2;

(function foo() {
  var a = 3;
  console.log( a ); // 3
})();

console.log( a ); // 2
```

---

## 提升
- 先有编译器找到所有声明并用合适的作用域将它们关联起来，后有引擎执行。也就是说，变量和函数的声明都将从代码出现的位置`提升`到最上方。

```js
foo();

function foo() {
    console.log( a );     // undefined
    var a = 2;
}
```
上述代码实际执行中为
```js
function foo() {
  var a;
  console.log( a );     // undefined
  a = 2;
}
foo();
```
- 注意的是，函数声明会被提升，但是函数表达式却不会被提升。
```js
foo();     // 不是ReferenceError, 而是TypeError!
var foo = function bar() {
    // ...
};
```
这里`foo`变量被提升，但是`foo`未被赋值，初始值为`undefined`，对`undefined`值进行函数调用会导致`TypeError`异常
- 函数声明会被优先提升。可能会覆盖变量声明

```js
foo(); // 1

var foo;

function foo() {
  console.log( 1 );
}

foo = function() {
  console.log( 2 );
};
```
- 在一个普通块内部的函数声明通常会被提升到所在作用域的顶部
后面的函数声明会覆盖前面的函数声明

```js
foo(); // "b"

var a = true;
if (a) {
  function foo() { console.log("a"); }
} else {
  function foo() { console.log("b"); }
}
```

---

## 数组
数组是对象，所以也可以包含字符串键值和属性（但是不计算在数组长度内）
```js
let a = [];
a[0] = 1;
a['hello'] = 'world';

console.log(a.length);        // 1
console.log(a.hello);         // world
console.log(a['hello']);      // world
``` 
但是如果字符串键值可以被强制转为十进制数字，则会被视为数字索引处理
```js
let a = [];
a['9'] = 77;

console.log(a.length);      // 10
``` 

---

## 字符串与字符数组
- 都有`length`，`indexof()`，`concat()`

```js
let a = 'hello';
let b = ['h', 'e', 'l', 'l', 'o'];


console.log(a.length);      // 5
console.log(b.length);       // 5

console.log(a.indexOf('l'));       // 2
console.log(b.indexOf('l'));       // 2

console.log(a.concat(' world'));      // hello world
console.log(b.concat('world'));       // ["h", "e", "l", "l", "o", "world"]
```
- **字符串是不可变的**，数组是可变的。
字符串的成员函数不改变其原始值，而是创建并返回一个新的字符串
数组的成员函数在其原始值上进行操作

```js
let a = 'hello';
let b = ['h', 'w', 'l', 'l', 'o'];

a[0] = 'H';
b[0] = 'H';

console.log(a);     // hello
console.log(b);    // ["H", "w", "l", "l", "o"]
```
- 数组有`reverse()`，字符串没有
字符串的翻转。下面方法不适用于包含复杂字符（Unicode，如星号、多字节字符）

```js
let a = 'hello';

let c = a.split('').reverse().join('');

console.log(c);
```

---

## 数字
- `number`类型，“双精度”格式（64位二进制）
- 小数点前的0可以省略，小数点最后的0也可以省略

```js
let a = 0.64;
let b = .64;

a = 12.0;
b = 12.;
```
- Number对象  `toFixed()`：把 Number **四舍五入为指定小数位数的数字**
 `NumberObject.toFixed(num)`
  - num 必需。规定小数的位数，是 0 ~ 20 之间的值，包括 0 和 20，有些实现可以支持更大的数值范围。如果省略了该参数，将用 0 代替。
  - 返回 NumberObject 的字符串表示
  - 注意num必须是Number类型
  - 数字常量也可以调用，但是需要注意运算符，因为会首选识别为数字常量，其次才是对象

```js
let a = 17.272;
let c = a.toFixed(2);       // "17.27"

// 42.toFixed(3);     // SyntaxError
(42).toFixed(3);    // "42.000"
0.42.toFixed(3);    // "0.420"
42..toFixed(3);    // "42.000"
```
- Number对象`toPrecision()`函数，指定显示的**有效数位**
- 浮点数比较问题。设置机器精度，通常值为2^-52。ES6引入了Number.EPSION，ES6之前可以写polyfill
```js
if (!Number.EPSILON){
    Number.EPSILON = Math.pow(2,-52);
}
```

---

## NaN
- 非数值。表示一个本来要返回数值但为返回数值的情况
- 任何涉及NaN的操作都会返回NaN
- NaN是唯一一个**非自反的**  ` NaN !== NaN` 为`true`
- 全局工具函数`isNaN()`，但这个意思是“检查参数是否**既不是NaN，也不是数字**”
- ES6,的工具函数检测NaN，**`Number.isNaN()`**。写polyfill的话，可以利用NaN的非自反性

---

## 零值
- 负零 -0，出现在`let a = 0 / -3`，`let b = 0 * -3`等返回值中，加减运算不会出现负零
- 对负零进行字符串化，返回`0`
```js
JSON.stringify(-0);     // 0
JSON.parse('-0');       // -0
```
- `-0 === 0`返回true，区分：
```js
function isNegZero(n) {
    n = Number(0);
    return (n === 0) && (1 / n === -Infinity);
}
```

---

## Object.is()
ES6的新的工具函数，**`Object.is()`判断绝对相等**

```js
let a = 1 / 'hh';
let b = -3 * 0;

Object.is(a, NaN);    // true
Object.is(b, -0);        // true
```

---

## 复杂数据类型Object按照引用传递
下面代码中，在foo函数中，x是指向了a，所以`x.push(5)`仍然在操作a
而`x = [6, 7, 8, 9]`后，x就不再指向a
```js
function foo(x) {
    x.push(5);

    x = [6, 7, 8, 9];
    x.push(10);
    x;
}

let a = [1, 2, 3, 4];

foo(a);

console.log(a);      // [1, 2, 3, 4, 5]
```
- 下面的代码中，传入的参数改为了`a.slice()`，这里就变成了按照值传递，因为slice函数实际是返回了一个新的数组

```js
function foo(x) {
    x.push(5);

    x = [6, 7, 8, 9];
    x.push(10);
    x;
}

let a = [1, 2, 3, 4];

foo(a.slice());

console.log(a);
```

---

## 拆箱操作

下面代码中，在函数foo内部，x++的时候，进行了拆箱操作，于是x变为了基本数据类型。而外部的b并不会随之改变

```js
function foo(x) {
    x++;
    console.log(x);      // 3
}

let a = 2;
let b = new Number(a);

foo(b);

console.log(b);    // 2
```

---

## 内部属性[[Class]]
- 所有typeof返回值为"object"的对象都包含一个内部属性[[Class]]
- 可通过`Object.prototype.toString.call()`查看
- null，undefined
- 字符串、数字和布尔会自动装箱
```js
Object.prototype.toString.call([1, 2, 3])    // [object Array]
Object.prototype.toString.call(null)          // [object Null]
Object.prototype.toString.call(undefined)    // [object Undefined]
Object.prototype.toString.call('hhh')        // [object String]
Object.prototype.toString.call(42)          // [object Number]
Object.prototype.toString.call(true)      // [object Boolean]
```

---

## Array()
下面代码中，`Array(3)`，并不是创建一个包含3这个元素的数组，而是创建了一个空数组，其length属性值为3
```js
let a = Array(3);
console.log(a);      // [undefined × 3]
```
- 创建包含undefined元素的数组
```js
let a = Array.apply(null, { length: 3 }); 
console.log(a);      // [undefined, undefined, undefined]
```

---

## Date.now()

`Date.now()` 等价于  `new Date().getTime()`

---

## `JSON.stringify()`可选参数

- `JSON.stringify()` 传递一个可选参数`replacer`，它可以是数组或者函数，用于指定对象序列化过程中哪些属性需要被处理
- 还有一个可选参数是`space`，用于指定输出的缩进格式。`space`为正整数时指定每一级缩进的字符数。为字符串的时候，前10个字符用于指定每一级的缩进
- 若传递的对象有`toJSON()`方法，则会先调用该方法

```js
let a = {
    b: 12,
    c: '12',
    d: [1,2],
};

console.log(JSON.stringify(a, ['b', 'c']));      // {"b":12,"c":"12"}

let b = JSON.stringify(a, (k, v) => {
    if (k !== 'c') {
        return v;
    }
});

console.log(b);     // {"b":12,"d":[1,2]}

console.log(JSON.stringify(a,null,3));
/*
{
   "b": 12,
   "c": "12",
   "d": [
      1,
      2
   ]
}
*/
```

---

## ToBoolean
- 假值
  - undefined
  - null
  - false
  - +0, -0, NaN
  - ""
- 假值对象
  ```js
  if (document.all) {
    // for IE
  }
  ```

---

## 显式强制类型转换
- 一元运算符`+`  会将字符串显式转换为数字
```js
let a = '123';
let b = +a;
console.log(b);     // 123
```
- 一元运算符`+ `会将日期对象显式转为Unix时间戳
```js
let a = new Date();
let b = +a;
console.log(b);     // 1495198522348
```
- `~`运算符。`~x` 大致相当于  `-(x+1)`。所以对于`x==-1`的时候，可以作为真假值的判断。但是我感觉这并不是很直观

```js
let a = 'Hello World';

if (a.indexOf('lo') >= 0) {
    // find
}

if (~a.indexOf('lo')) {
    // find
}
```
- `parseInt()`对传入的字符串从左到右解析，遇到第一个非数字字符就停止。为了保证不出奇怪的bug，应该只传递字符串，而且指定第二个参数为基数
- 显示强制转为Boolean类型
  - Boolean()
  - !!
- ** || 和 &&在JS中返回的是某个操作数**
  - `a || b`   可视为   `a ? a : b`
  - `a && b`   可视为   `a ? b : a`
- `==`允许在比较的时候进行强制类型转换，而`===`不允许。对于`==`先比较类型，不相同再进行类型转换再比较

---

## 标签语句

```js
function foo() {
    bar: {
        console.log('hello');
        break bar;
        console.log('never runs');
    }
    console.log('world');
}

foo();
// hello
// world
```
