几个要点：
1. Javascript是单线程的
2. 事件循环（Event Loop）是javascript的执行机制
3. 一个线程中，Event Loop是唯一的

---

如下图：
1. 主线程运行时，产生堆（heap）和栈（stack）
2. 栈中的代码调用各种外部API，在“任务队列”中加入各种事件
3. 栈中的代码执行完毕后，主线程就会去读取“任务队列”，执行事件对应的回调
![image](https://github.com/Rranran/notes/blob/master/images/event%20loop.jpg)

> 执行栈中的代码，总是在读取”任务队列”之前执行


---
异步任务分为 宏任务task（macrotask） 与 微任务 microtask

`宏任务`： 主代码块、setTimeout、setInterval、I/O、UI交互事件、postMessage、requestAnimationFrame、MessageChannel、setImmediate(Node.js环境）  
`微任务`：Promise.then、MutaionObserver、process.nextTick(Node.js环境）

JS的运行机制：
1. 执行一个宏任务（栈中没有就从事件队列中获取）
2. 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
3. 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务
4. 当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染
5. 渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）  

如下图：
![image](https://github.com/Rranran/notes/blob/master/images/js%20running.jpeg)

---


练习：

```javascript
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})

// 1，7，6，8，2，4，3，5，9，11，10，12
```

---

另外还有一个async await  

**async 函数**  
1. async 函数返回一个 Promise 对象，可以使用 then 方法添加回调函数。
2. async 函数内部抛出错误，会导致返回的 Promise 对象变为 reject 状态。抛出的错误对象会被 catch 方法回调函数接收到。
3. async 函数返回的 Promise 对象，必须等到内部所有 await 命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到 return 语句或者抛出错误。也就是说，只有 async 函数内部的异步操作执行完，才会执行 then 方法指定的回调函数。

```javascript
async function func1() {
    return 1
}

// func1的运行结果是一个Promise对象，可使用then来处理后续逻辑

func1().then(res => {
    console.log(res);  // 1
})
```

**await 命令**
await 的含义为等待，也就是 async 函数需要等待 await 后的函数执行完成并且有了返回结果（ Promise 对象）之后，才能继续执行下面的代码

```javascript
setTimeout(_ => console.log('setTimeout')

async function main() {
  console.log('async start')
  await Promise.resolve()
  console.log('async end')
}

main()

console.log(2)

// async start -> 2 -> async end -> setTimeout

```
> async/await 本质上还是基于 Promise 的一些封装，而 Promise 是属于微任务的一种。所以在使用await关键字与 Promise.then 效果类似  
> async 函数在 await 之前的代码都是同步执行的，可以理解为 await 之前的代码属 于 new Promise 时传入的代码，await 之后的所有代码都是在 Promise.then 中的回调


---
```javascript
const p = Promise.resolve();
(async () => {
    await p;
    console.log('await end');
})();
p.then(() => {
    console.log('then 1');
}).then(() => {
    console.log('then 2');
});
// chrome中运行结果是 await end -> then 1 -> then 2 
// 在node中是 then 1 -> then 2 -> await end
```

浏览器的Event loop是遵循的HTML5的标准
1. 先取出一个macrotask来执行，执行完成后下一步
2. 取一个microtask来执行，执行完成后，再取出一个来执行，直到microtask queqe清空，执行下一步
3. 更新UI渲染  


NodeJs的Event loop遵循的是libuv
1. 初始化Event loop
2. 执行主代码，同样遇到异步操作，分配给对应的队列，直到主代码执行完成
3. 执行代码中出现的所有microtask 先执行完所有的NextTick,再执行其他micro task,
4. 开始Event loop
