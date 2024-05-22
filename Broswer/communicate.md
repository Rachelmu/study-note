# 浏览器跨标签页通信的方式
## 常用方法
1.使用localStorage或sessionStorage存储共享数据，并通过监听storage事件来实现数据的变化检测和同步更新。
2.使用BroadcastChannel API，它提供了一种跨窗口通信的机制，可以在不同标签页之间发送消息。
3.使用window.postMessage()方法，该方法允许在不同的窗口或标签页之间安全地传递消息。
4.借助服务端的实时通信技术，如WebSocket，通过服务器作为中介来实现标签页之间的消息传递和数据同步。

## 浏览器通信方式
在多进程浏览器中，不同标签页之间的通信是通过进程间通信 IPC 机制来实现的。IPC 是操作系统提供的一种机制，允许不同进程之间交换数据和消息，从而实现协同工作。
1.基于管道的通信：
- 管道是一种半双工的通信机制，可用于同一父进程与其子进程之间通信，或者用于同一计算机上的不同进程之间通信。
- 命名管道提供了进程间进行双向通信的能力。可以被多个进程打开和使用。其中一个进程将数据写入管道，而另一个进程则可以从管道中读取这些数据。命名管道通常用于在不相关的进程之间传递数据，比如客户端和服务器之间的通信。
- 匿名管道是一种用于单向通信的机制，仅用于具有父子关系的进程之间。它只能在创建时通过操作系统提供的机制进行传递。匿名管道在创建时自动建立，并且只能用于具有亲缘关系的进程之间的通信。其中一个进程将数据写入管道的写端，而另一个进程则从管道的读端读取这些数据。
2.消息队列：消息队列允许进程通过将消息放入队列中来进行通信。进程可以从队列中接收消息，实现异步通信。消息队列适用于不需要直接的点对点连接的场景，而且可以在不同计算机之间通信。
3.共享内存：共享内存允许多个进程访问同一块物理内存区域，从而实现高效的数据共享。进程可以在共享内存中读写数据，而不需要显式的数据传输操作。
4.套接字Socket：套接字通信是一种在计算机网络中实现进程间通信的方式。它基于网络协议栈，使用 TCP 或 UDP 等传输层协议，在不同的主机之间进行数据传输和通信。
5.Remote Procedure Call：RPC 允许一个进程通过网络请求调用另一个进程中的函数，就像调用本地函数一样。远程过程调用隐藏了底层通信细节，使得进程间通信更加方便。
6.信号（Signal）：信号通信是一种在操作系统中实现进程间通信的机制。它允许一个进程向另一个进程发送信号，用于通知、中断或请求处理等目的。它是一种异步事件，当某个事件发生时，操作系统会向进程发送相应的信号。进程可以事先注册信号处理函数来捕获并处理这些信号。

## JavaScript 如何实现跨标签页通信
### BroadcastChannel
BroadcastChannel 通信的方式原理就是一个命名管道。它允许让指定的同源下浏览器不同的窗口来订阅它。
通过 postMessage 方法，一个页面可以将消息发送到频道中，而其他页面则可以监听 message 事件来接收这些消息。通过这种方式是短线了一种实时通信的机制，可以在不同的页面之间传递信息，实现页面间的即时交流。
所有页面必须是同源。
```js
[Exposed=(Window,Worker)]
interface BroadcastChannel : EventTarget {
  constructor(DOMString name);

  readonly attribute DOMString name;
  undefined postMessage(any message);
  undefined close();
  attribute EventHandler onmessage;
  attribute EventHandler onmessageerror;
};

const broad = new BroadcastChannel("moment");

setInterval(() => {
broad.postMessage({
    value: `moment ${new Date()}`,
});
}, 3000);

broad.onmessage = function (e) {
    console.log(e.data);
};
```
### Service Worker
Service Worker 它是一种服务工作线程，是一种在浏览器背后运行的脚本，用于处理网络请求和缓存等任务。它是一种在浏览器与网络之间的中间层，允许开发者拦截和控制页面发出的网络请求，以及管理缓存，从而实现离线访问、性能优化和推送通知等功能。

### localStorage
在 Web Storage 中,每一次将一个值存储到本地存储时,都会触发一个 storage 事件,由事件监听器发送给回调函数的事件对象。
其中有几个自动填充的属性如下：
- key：被修改的键；
- newValue：修改后的新值；
- oldValue：修改前的值；
- storageArea：事件监听对应的 Storage 对象
``` js
  let index = 0;
  setInterval(() => {
    localStorage.moment = `moment ${index++}`;
  }, 1000);

  window.addEventListener("storage", (e) => {
    console.log("被修改的键: ", e.key);
    console.log("旧值: ", e.oldValue);
    console.log("新值: ", e.newValue);
  });
```
### SharedWorker
SharedWorker 是一种在 Web 浏览器中使用的 Web API，它允许不同的浏览上下文,如不同的浏览器标签页之间共享数据和执行代码。它可以用于在多个浏览上下文之间建立通信通道，以便它们可以共享信息和协同工作。
与普通的 Worker 不同，SharedWorker 可以在多个浏览上下文中实例化，而不仅限于一个单独的浏览器标签页或框架。这使得多个浏览上下文可以共享同一个后台线程，从而更有效地共享数据和资源，而不必在每个标签页或框架中都创建一个独立的工作线程。
### IndexDB
IndexedDB 是一种在浏览器中用于存储和管理大量结构化数据的 Web API。它提供了一种持久性存储解决方案，允许 Web 应用程序在客户端存储数据，以便在不同会话、页面加载或浏览器关闭之间保留数据。
与传统的 cookie 或 localStorage 等存储方式不同，IndexedDB 更适合存储复杂的、结构化的数据，例如对象、数组、键值对等。这使得它特别适用于应用程序需要存储大量数据、执行高级查询或支持离线工作的情况。
### cookie
``` html
<!-- a.html -->
<script>
  let index = 0;
  setInterval(() => {
    document.cookie = `supper=moment ${index++}`;
  }, 1000);
</script>

<!-- b.html -->
<script>
  console.log("cookie 的值为: ", document.cookie);

  setInterval(() => {
    console.log("cookie 的值发生了变化: ", document.cookie);
  }, 1000);
</script>
```
### postMessage
window.postMessage() 方法可以安全地实现跨源通信。通常，对于两个不同页面的脚本，只有同源时，这两个脚本才能相互通信。