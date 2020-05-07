---
layout: article
title: Promise puzzles
date: 2017-06-30 15:14:42
tags: JavaScript
---

[Twitter](http://link.zhihu.com/?target=https%3A//twitter.com/nolanlawson/status/578948854411878400)上的一个问题，起先是在[知乎一篇专栏](https://zhuanlan.zhihu.com/p/25198178)上看到的。问下列代码的执行顺序

![Promise-puzzles]({{ site.url }}/assets/imgs/web/Promise-puzzles.png)

<!-- more -->

这里没有定义`doSomething`和`doSomethingElse`， 所以`puzzle #3`的解释可能不一样。
先给出下面的定义：
```js
function doSomething() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('something');
        }, 1000);
    });
}

function doSomethingElse() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('somethingElse');
        }, 1500);
    });
}
```
## Puzzle #1
```js
console.time('case 1')
doSomething().then(() => {
    return doSomethingElse()
}).then(function finalHandler(res) {
    console.log(res)
    console.timeEnd('case 1')
})
```
- 打印信息为
```
somethingElse
case 1: 2511.396ms
```
- 执行顺序为
``` 
doSomething()
|----------|
             doSomethingElse()
             |---------------|
                             finalHandler(somethingElse)
                             |->
```
- 这是正常写promise的代码

##  Puzzle #2
``` js
console.time('case 2')
doSomething().then(() => {
    doSomethingElse()
}).then(function finalHandler(res) {
    console.log(res)
    console.timeEnd('case 2')
})
```
- 打印信息
```
undefined
case 2: 1016.124ms
```
- 执行顺序
```
doSomething()
|----------|
             doSomethingElse()
             |---------------|
             finalHandler(undefined)
             |->
```
- 这里相比Puzzle #1 在`then`中去掉了`return `，所以`doSomethingElse `是异步执行的。而没有`return`任何值，所以`finalHandler`中为`undefined`

## Puzzle #3
```js
console.time('case 3')
doSomething().then(
    doSomethingElse()
).then(function finalHandler(res) {
    console.log(res)
    console.timeEnd('case 3')
})
```
- 打印信息
```
something
case 3: 1021.446ms
```
- 执行顺序
```
doSomething()
|----------|
doSomethingElse()
|---------------|
             finalHandler(something)
             |->
```
- 这里发生了**值穿透**？？，看了[这个](https://zhuanlan.zhihu.com/p/25178630)后还是有点不太明白。`doSomethingElse`这里不会等待`doSomething`执行完毕后执行么
- `then`需要接受一个函数，这里却传了一个值

这里如果`doSomethingElse`返回的是一个函数，结果就不一样了
```js
function doSomethingElse() {
    return () => {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                resolve('somethingElse');
            }, 1500);
        });
    }
}
```
- 打印信息:
```
somethingElse
case 3: 2522.012ms
```
- 执行顺序
```
doSomething()
|----------|
doSomethingElse()
|----------------|
                   finalHandler(something)
                   |->
```


## Puzzle #4
```js
console.time('case 4')
doSomething().then(doSomethingElse)
    .then(function finalHandler(res) {
        console.log(res)
        console.timeEnd('case 4')
    })
```
- 打印结果:
```
somethingElse
case 4: 2537.144ms
```
- 执行顺序
```
doSomething()
|----------|
             doSomethingElse(something)
             |---------------|
                             finalHandler(somethingElse)
                             |->
```
- 这里给`then`传递都还是一个函数，所以和Puzzle #1的结果是一样的
