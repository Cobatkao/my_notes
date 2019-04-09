# 06-async/await

# async和await在干嘛？

从字面上来看，async是「异步」的意思，加上await可以看作「async wait」。其意义也就更加明了，async用于声明一个异步函数，而await用于等待一个异步方法完成✅。

他们出现的意义是什么呢？async/await是为了简化多个Promise的同步操作，就像Promise要解决层层嵌套的回调函数的问题一样。

所以async/await就是要完善Promise还不够完美的地方，是在Promise的基础上进行改进的，因此也很好理解为什么await只能“等待”Promise对象。（下文描述）

## 个人理解

async/await是在Promise的基础上做了改进，await接收一个Promise对象，而当Promise执行到resolve()或者reject()的时候（fulfilled和rejected），await才会继续往下执行。

所以我们也能看出，Promise和async/await的应用场景是**大量连续的异步操作**。

# async

async作为一个关键字放到函数前面，表示函数内部有异步操作。因为async就是异步的意思，异步函数也就意味着该函数的执行不会阻塞后面代码的执行。另外注意，一般await关键字要在 async关键字函数的内部，await写在外面会报错。

```javascript
async function timeout() {
　　return 'hello world';
}
```

### 返回值

async后面的函数始终返回一个promise对象，其实只要函数前面加上了async，这个函数就总返回一个promise对象。实际上：

1. 如果async关键字函数返回的不是promise，会自动用`Promise.resolve()`包装；
2. 如果async关键字函数显式地返回promise，那就以你返回的promise为准；

我们用then获取promise对象的值：

```javascript
async function timeout() {
    return 'hello world'
}
timeout().then(result => {
    console.log(result);
})
console.log('虽然在后面，但是我先执行');

//虽然在后面，但是我先执行
//hello world
```

如上，立即执行函数并返回一个promise对象，我们发现单独使用async并没有阻塞同步代码的执行。后面我们继续看await。

### 隐式返回promsie

实际上，async会通过一个隐式的Promise返回其结果，resolve方法会负责传递这个值。
![](media/15463120803889/15463121191267.jpg)
当 async 函数抛出异常时，Promise的reject方法也会传递这个异常值。但是如果你的代码使用了异步函数，它的语法和结构会更像是标准的同步函数。

### 总结

async确保了函数返回一个promise，即使其中包含非promise。

# await

await等待什么呢？await等的是右侧「表达式」的结果，后面常放一个返回promise执行完成功resolve后才会继续执行async函数。

⚠️await关键字只能放到async函数里面。

await只在后面表达式为promise时等待promise的计算结果返回，如果没有给他返回个Promise，那它还是会继续向下执行。

### ✨返回值

右侧表达式**结果出来后**，对于await来说，分2个情况

* 不是promise对象
    * 如果不是 promise , await会阻塞后面的代码，**先执行async外面的同步代码，同步代码执行完，再回到async内部**，把这个非promise的东西，作为 await表达式的结果。
* 是promise对象
    * 如果它等到的是一个 promise 对象，await就忙起来了，它会阻塞async后面的代码，先执行async外面的同步代码，等着 Promise 对象 fulfilled，然后把 resolve 的参数作为 await 表达式的运算结果。
    
    > 看到上面的阻塞一词，心慌了吧……放心，这就是 await 必须用在 async 函数中的原因。async 函数调用不会造成阻塞，它内部所有的阻塞都被封装在一个 Promise 对象中异步执行。
    


**所以await等待的不是所有的异步操作，等待的只是Promise。**

看一个例子

```javascript
function doubleAfter2seconds(num) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(2 * num)
        }, 2000);
    } )
}

async function testResult() {
    let result = await doubleAfter2seconds(30);
    console.log(result);
}

testResult();
```

定义了一个异步操作的函数，2秒后返回数字的2倍，再写了一个async函数，使用await 关键字，await后面放置的就是返回promise对象的一个表达式，所以它后面可以写上 doubleAfter2seconds 函数的调用。最后，我们调用它：

### 代码过程

现在我们看看代码的执行过程，调用testResult函数，它里面遇到了await, await表示等一下，代码就暂停到这里，不再向下执行了，它等什么呢？

等后面的promise对象执行完毕，然后拿到promise resolve的值并进行返回，返回值拿到之后，它继续向下执行。具体到我们的代码, 遇到await之后，代码就暂停执行了， 等待doubleAfter2seconds(30) 执行完毕，doubleAfter2seconds(30) 返回的promise 开始执行，2秒之后，promise resolve 了， 并返回了值为60， 这时await 才拿到返回值60， 然后赋值给result， 暂停结束，代码才开始继续执行，执行 console.log语句。

就这一个函数，我们可能看不出async/await 的作用，如果我们要计算3个数的值，然后把得到的值进行输出呢？

```javascript
async function testResult() {
    let first = await doubleAfter2seconds(30);
    let second = await doubleAfter2seconds(50);
    let third = await doubleAfter2seconds(30);
    console.log(first + second + third);
}
```

6秒后，控制台输出220，我们可以看到，写异步代码就像写同步代码一样了，再也没有回调地域了。


# 处理异常

如果请求发生异常，怎么处理？ 它用的是try/catch 来捕获异常，把await 放到 try 中进行执行，如有异常，就使用catch 进行处理。

# 三种异步方式

### 回调地狱

![](media/15463120803889/15463121394334.jpg)

### Promise来了

![](media/15463120803889/15463121573742.jpg)


### async/await来了

![](media/15463120803889/15463121699992.jpg)

所以，async/await是在promise的基础上做了改进，await接收一个Promise对象，而当Promise执行到resolve()或者reject()的时候，await才会继续往下执行。

# 从面试题开始

```javascript
async function async1() {
    console.log( 'async1 start' )
    await async2()
    console.log( 'async1 end' )
}
    
async function async2() {
    console.log( 'async2' )
}
    
console.log( 'script start' )
    
setTimeout( function () {
    console.log( 'setTimeout' )
}, 0 )
    
async1();
    
new Promise( function ( resolve ) {
    console.log( 'promise1' )
    resolve();
} ).then( function () {
    console.log( 'promise2' )
} )
    
console.log( 'script end' )
```

一下子集齐了各种异步操作，让人晕头转向。先简单回顾一下event loop：

> 1. 一段代码运行时，会先执行宏任务队列中的代码，如果遇到**定时器**如setTimeout，会先把任务放到event station中，等待条件达到了，把回调函数推入「宏任务的队列」中，下一轮宏任务执行时调用。
> 2. 如果执行中遇到`promise.then()`之类的微任务，就会推入到「微任务队列」中，在本轮宏任务的同步代码执行都完成后，依次执行所有的微任务1、2、3

下面一步步解析面试题中的代码：

1. **>>> script start**：首先遇到两个异步函数声明，虽然有async关键字，但不是调用我们就不用管。然后首先是打印同步代码`console.log('script start')`
2. 然后代码走到定时器setTimeout，将它放入宏任务队列
3. **>>> async1 start**：代码进入async1并调用它，首先打印同步代码`console.log( 'async1 start' )`
4. **>>> 执行await async2()**：await的执行机制是：1.它先计算出右侧的结果，2.然后看到await后，跳出async函数，执行外部其它同步代码
    1. **>>> async2**：先得到await右侧表达式的结果：执行async2()，打印同步代码console.log('async2'), 并且`return Promise.resolve(undefined)`
    2. await后，中断async函数，先执行async外的同步代码
5. **>>> promise1**：被阻塞后，要执行async之外的代码，执行new Promise()，Promise构造函数是直接调用的同步代码，所以 `console.log( 'promise1' )`
6. 代码运行到`promise.then()`，发现这个是微任务，将异步函数微任务队列中，微任务会在当前宏任务的同步代码执行完毕，才会依次执行；
7. **>>> script end**：执行剩余的同步代码，本题中，执行完这个同步代码后，**「async外的代码」**总算走完了，该回到await代码，执行`await Promise.resolve(undefined)`，并返回结果。回忆平时我们用promise，调用resolve后，何时能拿到处理结果？是不是需要在then的第一个参数里，才能拿到结果。所以这里的 await Promise.resolve() 就类似于`Promise.resolve(undefined).then((undefined) => {})`，把then的第一个回调参数 (undefined) => {} 推入微任务队列，then执行完，才是await async2()执行结束。然后继续执行await下面的代码。
    > 如果一个 Promise 被传递给一个 await 操作符，await 将等待 Promise 正常处理完成并返回其处理结果。
8. **处理微任务队列**：执行微任务1，打印promise2，微任务2并没有。
9. **>>> async1 end**微任务2执行后，await async2()语句结束，后面的代码不再被阻塞，所以打印：console.log( 'async1 end' )
10. **>>> setTimeout**宏任务2的执行比较简单，就是打印：console.log('setTimeout')

[面试题](https://segmentfault.com/a/1190000017224799)
[参考](https://segmentfault.com/a/1190000016923185)

# 参考&拓展

1. https://www.cnblogs.com/SamWeb/p/8417940.html
2. 推荐！[Async/Await替代Promise的6个理由](https://www.cnblogs.com/fundebug/p/6667725.html)
3. [理解 JavaScript 的 async/await](https://segmentfault.com/a/1190000007535316?utm_source=tag-newest)