## Vue 中的 NextTick
### nextTick 是什么？
在 vue 官网中，提到 nextTick 是在异步更新队列中，vue.js 通常鼓励开发者使用“数据驱动”的方式思考代码，避免直接接触 DOM，但是我们经常会遇到数据变化之后等待试图没有刷新，这个时候我们就可以通过官方给予我们 Vue.nextTick(callback) api 进行时图的更新。所以我们可以理解为通过 nextTick 方法可以手动更新视图。另外在组建中我们可以使用 vm.$nextTick() 或者 this.$nextTick() 调用。另外官方解释道 $nextTick() 返回的是一个 Promise 对象，所以我们也可以在ES2017中通过 async/await 语法来使用它 await this.$nextTick() 。
### nextTick 如何实现的？
通过上面官方的解释，其实 NextTick 最后返回的是一个 Promise 对象，那么我们就明白了，那么就属于一个微任务，好了那么我们看下 nextTick 的实现
[!链接](https://github.com/vuejs/vue/blob/dev/src/core/util/next-tick.js)
`````
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    timerFunc()
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
`````
在上述代码中我们发现了 timerFunc 方法可以看出在 nextTick 中 timerFunc 是核心。另外nextTick中有 pending 的状态，应该是来控制 timerFunc 的执行。
我们下一步就来看下 timerFunc 函数
timerFunc 的声明出现在33# 但是什么也没有值
`````
let timerFunc
`````
直到#44 有了赋值，看到是一个方法
`````
# 44
timerFunc = () => {
  p.then(flushCallbacks)
  // In problematic UIWebViews, Promise.then doesn't completely break, but
  // it can get stuck in a weird state where callbacks are pushed into the
  // microtask queue but the queue isn't being flushed, until the browser
  // needs to do some other work, e.g. handle a timer. Therefore we can
  // "force" the microtask queue to be flushed by adding an empty timer.
  if (isIOS) setTimeout(noop)
}
`````
另外#68、#77和#82都有 timerFunc 的赋值
`````
#68
timerFunc = () => {
  counter = (counter + 1) % 2
  textNode.data = String(counter)
}
#77
timerFunc = () => {
  setImmediate(flushCallbacks)
}
#82
timerFunc = () => {
  setTimeout(flushCallbacks, 0)
}
`````
其实不难看出#68,#77和#82的都是在做代码的兼容性，这里面我们可以不提了，就看最上面的第一个判读#44，
其实当看到.then()已经很简单了，其实nextTick根本就是一个Promise，当浏览器的 vue 中其他的 js 执行完就会调用 flushCallbacks() 将需要手动刷新的队列更新从而让试图刷新。
