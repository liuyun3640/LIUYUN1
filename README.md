# LIUYUN1
1.浏览器运行原理
https://segmentfault.com/a/1190000012925872
1.1 进程：是cpu资源分配的最小单位；
1.2 线程：是cpu调度的最小单位；
1.3 浏览器：
    （1）Browser进程；（2）第三方插件进程；（3）GPU进程，最多一个用于3D绘制；（4）浏览器渲染进程，浏览器内核，Renderer进程，默认每个Tab页面一个进程<有时空白tab会被合并>；
1.4 渲染进程：
    （1）GUI渲染线程，负责渲染浏览器界面，构建dom树，renderObject树，布局和绘制；重绘和回流该进程会执行；GUI线程和js引擎线程互斥；
    （2）JS引擎线程
    （3）任务触发线程
    （4）定时器线程
    （5）异步HTTP请求线程
1.5 worker:
    (1) 创建Worker时，JS引擎向浏览器申请开一个子线程（子线程是浏览器开的，完全受主线程控制，而且不能操作DOM）
    (2) JS引擎线程与worker线程间通过特定的方式通信（postMessage API，需要通过序列化对象来与线程交互特定的数据）
1.6 硬件加速
   （1）最常用的方式：translate3d、translateZ
   （2）opacity属性/过渡动画（需要动画执行的过程中才会创建合成层，动画没有开始或结束后元素还会回到之前的状态）
   （3）will-chang属性（这个比较偏僻），一般配合opacity与translate使用（而且经测试，除了上述可以引发硬件加速的属性外，其它属性并不会变成复合层）
1.7 Event Loop谈JS的运行机制
JS分为同步任务和异步任务
同步任务都在主线程上执行，形成一个执行栈
主线程之外，事件触发线程管理着一个任务队列，只要异步任务有了运行结果，就在任务队列之中放置一个事件。
一旦执行栈中的所有同步任务执行完毕（此时JS引擎空闲），系统就会读取任务队列，将可运行的异步任务添加到可执行栈中，开始执行。
！！macrotask中的事件都是放在一个事件队列中的，而这个队列由事件触发线程维护
！！microtask中的所有微任务都是添加到微任务队列（Job Queues）中，等待当前macrotask执行完毕后执行，而这个队列由JS引擎线程维护
总结：
执行一个宏任务（栈中没有就从事件队列中获取）
执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染
渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）
<img width="726" alt="image" src="https://user-images.githubusercontent.com/18298598/178404206-e1ed5378-35e3-487f-aeef-1a6814ae2e49.png">
<img width="791" alt="企业微信截图_5d84e7d2-36ec-482d-9742-a821374bdc5c" src="https://user-images.githubusercontent.com/18298598/178404249-29fc7772-69d3-475c-b83d-77b9315b0ccf.png">
<h2>Promise</h2>
<p>1.Promise.catch() 是promise 在rejected状态下执行的回调，当then里有.then(sus, fail)的时候执行fail不再执行catch</p>
<p>2.Promise.any 返回第一个状态最先置成fullfill的promise，参数是[]空数组时，状态时rejected</p>
<p> Promise.race是返回第一个状态改变的promise，不管事fullfill还是rejected， 参数是[]时，状态一直是penddign</p>
<p>3.Promise.finally  Promise.resolve(2).then(() => 77, () => {}),返回的promise value是77</p>
<p> Promise.resolve(2).finally(() => 77) ，返回value是 2； 当finally函数里没有表示reject的操作时，是透传上个promise的的状态（resolve和reject）和返回值</p>
<p>但是Promise.reject(3).finally(() => {throw 99}) and Promise.reject(3).finally(() => Promise.reject(99)) 是返回99</p>
<p>4.Promise.resolve 看一下有重点 https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve</p>
<p>5.Promise.then 有nextTick简单实现 https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/then（没看懂）</p>
<p>6.Promise.all 等全部fullfile 返回；或者第一个reject返回，进入catch, 参数是[]时返回 fulfilled 的空数组； Promise.allSettled 等所有的promise状态都返改变,参数是[]时返回状态是fulfilled的空数组[]</p>


