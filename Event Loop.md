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

参考文档：  
https://coderlt.coding.me/2017/12/13/event-loop/  
https://juejin.im/post/5b498d245188251b193d4059#heading-11  
