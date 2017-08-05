## JS定时器

在谈js定时器以前，我觉得有必要了解下javascript的事件运行机制，**简称(javascript event loop)**。众所周知，javascript是**单线程**，就是说一次只能完成一个任务，多个任务的执行需要排队。前一个任务结束，才能执行后一个任务，如果前一个任务耗时很长，后面的任务就处于挂起状态，不得不等到前一个任务完成。



js任务分为同步和异步任务。在了解同步和异步之前，需要理解浏览器的并发模型：

![如图](https://camo.githubusercontent.com/6afeb8387f6587856af0cdd2e00bc23be8a68ac1/687474703a2f2f6f61376436647871742e626b742e636c6f7564646e2e636f6d2f626c6f672f696d616765732f64656661756c742e737667)

左边的栈存储的是同步任务，所谓同步的任务就是那些能立即执行的任务，如变量和初始化、事件绑定等等直接声明调用的操作。右边的堆（heap）用来存储声明的对象。下面的就是任务队列。一个某个异步任务有了响应（触发），就会被推入队列中。如用户的点击事件、浏览器收到服务的响应和之前提到的setTimeout定时器事件等等。每个异步任务都有一个回调函数。



再来说说setTimeout：

> 在单线程的Javascript引擎中，setTimeout()是如何运行的呢，这里就要提到浏览器内核中的事件循环模型了。简单的讲，在Javascript执行引擎之外，有一个任务队列，当在代码中调用setTimeout()方法时，注册的延时方法会交由浏览器内核其他模块（以webkit为例，是webcore模块）处理，当延时方法到达触发条件，即到达设置的延时时间时，这一延时方法被添加至任务队列里。这一过程由浏览器内核其他模块处理，与执行引擎主线程独立，执行引擎在主线程方法执行完毕，到达空闲状态时，会从任务队列中顺序获取任务来执行，这一过程是一个不断循环的过程，称为事件循环模型。

当一个异步事件触发，它的回调函数先进入事件队列中排队，任务队列是一个先进先出的数据结构，排在前面的事件，一旦执行栈为空，优先被主线程读取到栈中执行。主线程从任务队列中读取事件，这个过程循环不断，所以整个的这种运行机制又称为**Event loop**。



下面有必要讲下 **javascript定时器的工作原理**：

有必要看下[原文链接]([http://ejohn.org/blog/how-javascript-timers-work/](http://ejohn.org/blog/how-javascript-timers-work/))，当然也能看下袁锅锅的[Javascript的定时器的工作原理](https://segmentfault.com/a/1190000002633108)

为了理解定时器的内部工作原理，我们需要了解一个非常重要的概念：**定时器设定的延时是没有保证的。**因为在所有浏览器中执行的javascript单线程异步事件（比如鼠标点击事件和定时器）都只有在主线程有空即执行栈为空的情况下采取执行。通过图片来说明：

![图片](https://segmentfault.com/img/bVlc84)

在解释上图时，首先先解释下setTimeout和setInterval的区别：

- setTimeout(fun, delay)：延时delay毫秒之后，啥也不管，直接将回调函数推入事件队列
- setInterval(fun, delay)：延时delay毫秒之后，先看看事件队列中是否存在还没有执行的回调函数（setInterval的回调函数），如果存在，就不需要再往事件队列中加入回调函数了。



我们再来解释上面的图。

上图蓝色区域表示任务的执行时间，首先是Javascript代表的同步任务。在10ms处注册了一个setTimeout事件，并且在之后又多了一个鼠标点击事件，在鼠标点击事件后又多了个setInterval事件。



当setTimeout相对注册该事件过了10ms时，开始触发事件。可是现在蓝色区域的同步任务还未执行完，即主线程任务未执行完，执行栈还不为空。则把该处的回调函数推入事件队列。然而，事件队列中已经有一个点击触发的事件，因为他比定时器过10秒才触发快，所以优先进入到队列。

等Javascript代表的蓝色区域同步任务执行完之后，主线程便从任务队列中取到读到鼠标点击事件，开始执行mouse click callback的蓝色区域的回调函数，然而在这个区域中setInterval的离注册事件过10ms到时事件开始触发，并将回调函数推入事件队列中。此时它的前面排着10ms setTimeout事件。等Mouse Click回调函数执行完，执行栈又为空，推入10ms setTimeout事件，并执行，此时在Timer蓝色区域下一个10ms setInterval事件触发，由于之前事件队列中有一个interval事件了，则丢弃，不进入队列。

等Timer的回调执行完，随即执行事件队列里的第一个Interval事件，在Interval蓝色执行期间，又有个interval事件触发，则推入事件队列，即本次Interval事件执行完下一个直接取到执行栈执行，下面的setInterval事件可谓是畅通无阻了，按照每10ms执行~



表达的不清楚，请见谅，本文也是结合了几篇讲解定时器文章做了一个总结。



参考链接：

[javascript计时器工作原理](https://segmentfault.com/a/1190000002633108)

[javascript定时器工作原理](http://blog.mcbird.cn/2015/09/18/javascript-timers/)

[javascript单线程异步机制](https://github.com/pramper/Blog/issues/4)

[阮一峰：再谈event loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

