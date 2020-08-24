# Promise 学习

### Promise 原理
1. 什么是Promise ?  
Promise是一个对象，用来表示异步操作的最终完成(或失败)及其结果值。
2. Promise 的三种状态
- 进行中 pending
- 完成 fulfilled  
- 失败 rejected
3. 实现方法
   promise 采用观察者模式,用特定的方式来注册对应状态的事件处理函数   
``````
//  雏形
function Promise(fu) {
    var val = null,
        callbacks = []; // 可能存在同时有多个回掉函数
    this.then = function(onFulfilled){
        callbacks.push(onFulfilled);
    }
    function resolve(value) {
        callbacks.forEach(funciton(callback){
            callback(value);
        })
    }
    fn(resolve)
}
``````
4. Promise 有一下特点
  - 对象的状态不受外界影响。Promise 对象代表一个异步操作，有三种状态：Pending（进行中）、Resolved（已完成，又称 Fulfilled）和 Rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是 Promise 这个名字的由来，它的英语意思就是「承诺」，表示其他手段无法改变。
  - 一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise 对象的状态改变，只有两种可能：从 Pending 变为 Resolved 和从 Pending 变为 Rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果。就算改变已经发生了，你再对 Promise 对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。
  
5. 缺点  
首先，无法取消 Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。第三，当处于 Pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

6. 基本api   
``````
Promise.resolve()

Promise.reject()

Promise.prototype.then()

Promise.prototype.catch()

Promise.all() // 所有的完成

Promise.race() // 竞速，完成一个即可
``````

7. 规范表现形式
- 可以通过链式编程的方式对异步操作进行同步处理 
- 上一个操作的输出值是下一个操作的输入值

### 补充资料
[阮一峰Promise](https://es6.ruanyifeng.com/#docs/promise)

### 从 Promise 理解javascript 事件循环机制
#### [javascript-event-loop](https://github.com/wang90/Promise/blob/master/event-loop.html)
#### [vue 中的 nextick](https://github.com/wang90/Promise/blob/master/vue-nextick.html)

### 利用 Promise 编程
##### [Promise实现图片懒加载](https://github.com/wang90/Promise/blob/master/load-image.html)
