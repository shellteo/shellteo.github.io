---
layout: post
title:  "JavaScript运行机制"
date:   2018-07-10
categories: JS
author: 张翔
location: ShangHai, China
description: javascript是单线程的，js任务分为同步任务和异步任务，同步和异步是javascript中的核心内容！这篇主要解析javascript的运行机制！
---
---
javascript是单线程的，js任务分为**同步任务**和**异步任务**，同步和异步是javascript中的核心内容，如何理解这些内容呢？
更加细分一点来说的话javascript任务类型分为两种：**macrotask（宏任务）**和**microtask（微任务）**，在ECMAScript中，**macrotask可称为task**，**microtask称为jobs**。

## 一、macrotask（宏任务）
**macrotask**，可以理解是每次执行栈执行的代码就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）
每一个task会从头到尾将这个任务执行完毕，不会执行其它
浏览器为了能够使得JS内部task与DOM任务能够有序的执行，会在一个task执行结束后，在下一个 task 执行开始前，对页面进行重新渲染
（task->渲染->task->...）

## 二、microtask（微任务）
**microtask**，可以理解是在当前 task 执行结束后立即执行的任务
也就是说，在当前task任务后，下一个task之前，在渲染之前
所以它的响应速度相比**setTimeout**（setTimeout是task）会更快，因为无需等渲染，所以Vue自己实现**nextTick**（后面会说到）。
也就是说，在某一个macrotask执行完后，就会将在它执行期间产生的所有microtask都执行完毕（在渲染前）

## 三、如何形成macrotask和microtask？

 - macrotask：**主代码块**，**setTimeout**
   ，**setInterval**（可以看到，事件队列中的每一个事件都是一个macrotask），**setImmediate**
   ，**I/O** ，**UI rendering**

 - microtask：**Promise** ，**process.nextTick** ，**Object.observe**
   ，**MutationObserver**等

process.nextTick是在nodejs环境下面的方法（关于NodeJs的以后再说）并且process.nextTick的优先级比Promise高，Vue中有模拟nextTick的实现。

## 四、例子
看下面例子的输出情况。
解释如下：
1.主线程是**macrotask**，输出1
2.然后执行**setTimeout**，回调函数被添加到**macrotask**中
3.接着执行**Promise**，直接执行**new Promise**，输出7，**then**后面的函数被添加到**microtask**
4.最后执行**nextTick**，回调函数被添加到**microtask**
5.第一轮**macrotask**执行结束，检查**microtask**中有任务，分别是第三步then和第四步的nextTick，虽然nextTick比then后加入到microtask，但是**nextTick执行优先级高于Promise**，所以输出6 8
6.第二轮**macrotask**从第二步的**setTimeout**回调函数开始
7.执行**Promise**，输出4，**then**后面的回调函数被添加到**microtask**
8.接着执行，直接输出2
9.执行**nextTick**，回调函数被添加到**microtask**
10.第二轮**macrotask**执行结束，检查**microtask**中有任务，分别是第七步的then和第九步nextTick，输出3 5，整个代码到此执行结束。
完整输出：**1 7 6 8 4 2 3 5**

```javascript
console.log('1');
setTimeout(function() {
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})
process.nextTick(function() {
    console.log('6');
})
```

## 五、Vue中nextTick的实现

代码如下：100多行，首先定义了**microTimerFunc**和**macroTimerFunc**，对于 macro task 的实现，优先检测是否支持**原生 setImmediate**，这是一个高版本 IE 和 Edge 才支持的特性，不支持的话再去检测是否支持原生的 **MessageChannel**，如果也不支持的话就会降级为 **setTimeout 0**；而对于 micro task 的实现，则检测浏览器是否原生支持 **Promise**，不支持的话直接指向 **macro task** 的实现。

```javascript
/* @flow */
/* globals MessageChannel */

import { noop } from 'shared/util'
import { handleError } from './error'
import { isIOS, isNative } from './env'

const callbacks = []
let pending = false

function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}

// Here we have async deferring wrappers using both microtasks and (macro) tasks.
// In < 2.4 we used microtasks everywhere, but there are some scenarios where
// microtasks have too high a priority and fire in between supposedly
// sequential events (e.g. #4521, #6690) or even between bubbling of the same
// event (#6566). However, using (macro) tasks everywhere also has subtle problems
// when state is changed right before repaint (e.g. #6813, out-in transitions).
// Here we use microtask by default, but expose a way to force (macro) task when
// needed (e.g. in event handlers attached by v-on).
let microTimerFunc
let macroTimerFunc
let useMacroTask = false

// Determine (macro) task defer implementation.
// Technically setImmediate should be the ideal choice, but it's only available
// in IE. The only polyfill that consistently queues the callback after all DOM
// events triggered in the same loop is by using MessageChannel.
/* istanbul ignore if */
if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  macroTimerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else if (typeof MessageChannel !== 'undefined' && (
  isNative(MessageChannel) ||
  // PhantomJS
  MessageChannel.toString() === '[object MessageChannelConstructor]'
)) {
  const channel = new MessageChannel()
  const port = channel.port2
  channel.port1.onmessage = flushCallbacks
  macroTimerFunc = () => {
    port.postMessage(1)
  }
} else {
  /* istanbul ignore next */
  macroTimerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}

// Determine microtask defer implementation.
/* istanbul ignore next, $flow-disable-line */
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  microTimerFunc = () => {
    p.then(flushCallbacks)
    // in problematic UIWebViews, Promise.then doesn't completely break, but
    // it can get stuck in a weird state where callbacks are pushed into the
    // microtask queue but the queue isn't being flushed, until the browser
    // needs to do some other work, e.g. handle a timer. Therefore we can
    // "force" the microtask queue to be flushed by adding an empty timer.
    if (isIOS) setTimeout(noop)
  }
} else {
  // fallback to macro
  microTimerFunc = macroTimerFunc
}

/**
* Wrap a function so that if any code inside triggers state change,
* the changes are queued using a (macro) task instead of a microtask.
*/
export function withMacroTask (fn: Function): Function {
  return fn._withTask || (fn._withTask = function () {
    useMacroTask = true
    const res = fn.apply(null, arguments)
    useMacroTask = false
    return res
  })
}

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
    if (useMacroTask) {
      macroTimerFunc()
    } else {
      microTimerFunc()
    }
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```