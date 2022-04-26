# daily progress

> 正在看这个项目的那位，没错就是你，你以后绝对会有出息真的

## 存档

[2021-12](./2021-12)

[2022-01](./2022-01)

[2022-02](./2022-02)

## 内容

【2022.4.20】之前做[操作系统](https://mit-public-courses-cn-translatio.gitbook.io/mit6-s081/lec01-introduction-and-examples/1.3-why-hard-and-interesting)的实验的时候发现，提交给github的分支不会被记录，所以就没有再记录了，之后也浑浑噩噩玩了一段时间，从现在开始痛定思痛！

- 想做的事：
1.给自己写的vue3加上unit test
2.看完微信读书上面的react hooks
3.看完node-interview，https://github.com/ElemeFE/node-interview/tree/master/sections/zh-cn
4.看完微信读书上面的rust教程
5.再看一遍你所不知道的js
6.看完微信读书上线的函数式rust编程（选看）
7.看完这里的博客，https://github.com/qappleh/Interview/issues
8.看完这里的内容，https://f2qrvzh48g.feishu.cn/docs/doccnFNAUepxSBYCL08EyzXCjOc（选看）
9.想学一下docker

- 想弄清楚的几个问题：
1.js里面不同类型不同环境下变量的内存是怎么释放的？
2.v8的JS执行过程
3.electron底层原理
4.tree sharking 实现原理，为什么ESM可以tree sharking
5.buffer 堆外内存的更多信息

- 类型判断：什么场景下使用typeof、instanceof、tostring？es6的类型判断？window、dom元素的类型判断？constructor存在的意义和用途？getPrototypeOf和prototype？for in和hasOwnProperty？
- js 里面没有引用传递，都是值传递，只不过对于简单类型，传递的值是值，对于复合类型，传递的值是引用。
- 使用 process.memoryUsage() 和 process.memoryUsage.rss() 查看 nodejs 的内存使用情况。

【2022.4.21】在看node-interview中的[模块](https://github.com/ElemeFE/node-interview/blob/master/sections/zh-cn/module.md)

- 编写一个 json 对象的拷贝函数：

```ts
function deepCopy(value: any, hashMap = new WeakMap<any>()) {
  if (typeof value !== 'object') return value
  if (hashMap.has(value)) return hashMap.get(value)

  const ret = {}
  hashMap.set(value, ret)
  for (const key in value) {
    if (value.hasOwnProperty(key)) {
      ret[key] = deepCopy(value[key], hashMap)
    }
  }
  return ret
}
```

- esm 和 cjs 的区别：
1.从使用场景上来说，cjs是执行时加载，执行到加载语句的地方才开始加载，所以适用于加载很快速的场景，比如模块在本地的nodejs；esm是编译时加载，在编译的时候就开始加载，适用于加载没那么快的场景，比如浏览器。
2.从输出值上来说，他们对于对象类型，输出的都是值的引用；但是对于基本类型，cjs输出的是值的引用；esm输出的是值的拷贝。所以在esm里面不允许修改导入的值。

- 内存泄漏
1.使用--inspect指令连接浏览器进行调试node，https://nodejs.org/en/docs/guides/debugging-getting-started/
2.在浏览器的memory栏生成快照，对比快照定位泄漏的地方。
3.对于产线环境，以前是使用heapdump包生成快照，现在node内置快照模块，https://dev.to/bengl/node-js-heap-dumps-in-2021-5akm，我们生成快照之后导入浏览器进行定位。

【2022.4.22】继续看八股文 + 基础知识

- node 在调用 require 的时候，会执行以下步骤：
1.解析：找到文件的绝对路径。
2.加载：确定文件的类型。
3.封装：使用函数将文件代码封装起来，给它一个独立的上下文。
4.评估：执行文件代码
5.缓存：缓存到module.exports里面去

- treeshaking相关：
1.在库的pkg里面设置sideeffects可以开启对此库的treeshaking，否则不会对这个库进行treeshaking。
2.在函数执行的前面添加```/*#__PURE__*/```可以将此函数执行标记为无副作用的，没有被导入的话就会被treeshaking掉。
3.可以通过动态导入达到类似treeshaking的目的，但比不上treeshaking。动态导入运用了import的能力，不全量导入这个库，而是全量导入这个库的一部分文件。
4.如果import的路径是一个正则活动带变量的话，导入的时候会把静态路径文件夹的内容全部打包进去。
5.treeshaking的原理是借助了import的静态性，它里面没有动态内容，所以在编译阶段生成ast的时候，能够提前知道文件导入了哪些内容，导出了哪些内容，然后就可以对那些没有用到的导出做一些标记，然后在代码生成的阶段，删掉这些标记的内容。

- js里面不同类型不同环境下变量的内存是怎么释放的？
1.对于栈上面的变量，在相关的上下文执行结束的时候，esp指针会下移，导致栈上的变量的内存自动被回收掉了
2.对于堆上面的变量，基本方式是先标记所有被引用到的变量，然后清理没有被标记的变量；为了加快速度，把堆分为新生代和老生代两种区域，新生代存生命周期短并且占用内存小的变量；老生代存生命周期长或者占用内存大的变量。在新生代使用scavenge的方法清理内存，即把新生代分为两个相等的区域，内存分配的时候只在其中一个区域进行内存分配，然后在gc的时候，先标记出有被引用到的变量，然后把这些变量复制到另一个区域，复制完之后清空之前的那个区域；老生代使用标记清除的方式，先标记出有被引用到的变量，然后释放未被引用到的变量，在内存碎片多的时候会朝着一端复制变量，同时使用了增量标记的方式来加快速度。
3.对于堆外内存，也是由 js 的 gc 来清除，但是不是用上面任何一种方式。堆外内存在创建的时候会自动加上一个释放内存的钩子函数，每次gc的时候，gc会判断对外内存的引用情况，然后调用这个钩子函数来释放内存。

【2022.4.24】继续看八股文 + 基础知识

- promise 的reduce写法

```ts
promiseArr.reduce(
  (accu, curr) => accu.then((result) => curr.then(res => [...result, res])), Promise.resolve([])
)
```

- promise.then的第二个参数和catch的区别：1.前者不能捕获到第一个参数里面的错误。（所以永远不建议使用第二个参数，而应该使用catch）2.前者和后者在不同的事件循环里面。

- 实现一个 sleep 函数：

```ts
function sleep(ms) {
  const start = Date.now()
  const expire = start + ms
  while(Date.now() < expire) {}
}
```

- 事件循环
1.浏览器里面的事件循环比较简单，指的是浏览器在执行同步代码的时候，会把它们放到一个宏任务队列里面去执行，通过宏任务api可以把部分任务放到宏任务队列尾部，这样宏任务队列一个个一次执行；但是这样有一个问题，就是部分宏任务代码要等很长时间才能执行，不适用于一些实时性要求高的场景，为了解决这个问题，每个宏任务队列又有一个微任务队列，通过微任务api可以把部分任务放到微任务队列里面去，当当前的宏任务队列执行完毕之后，会依次执行微任务队列里面的任务，当前微任务队列里面的任务执行完毕之后，再执行下一个宏任务。在浏览器里面还有一个UI渲染，在微任务队列执行完毕之后，下一个宏任务执行之前执行。
2.浏览器中的宏任务api：settimeout、setinterval、messageChannel、requestAnimationFrame、IO等。
3.node里面的事件循环要复杂的多，它把宏任务队列分为好几个部分，依次是timer、poll、check等部分。其中check负责执行settimeout、setinterval的宏任务、poll负责执行IO的宏任务、check负责执行setimmediate的宏任务。上面每个宏任务阶段执行之后，都会执行所有当前宏任务下的微任务，微任务也分为好几个部分，依次是process.nextTick、promise、其它微任务。就这样按次序执行宏任务和微任务。

- v8的执行过程
1.v8采用即时编译jit技术，混合编译执行和解释执行
2.首先使用词法分析识别各种token，然后使用语法分析生成ast树
3.然后使用ignition引擎把ast树转化为字节码，进行解释执行
4.在执行的过程中会识别热点代码，并使用turbofan转化为机器码加快执行的速度

- v8的优化
1.对函数的惰性解析和预解析
2.快慢属性
3.隐藏类和内联缓存

【2022.4.25】继续看八股文 + 基础知识

- 什么是守护进程，为什么要用守护进程
1.守护进程是不会被父进程杀死的进程，主要是通过通过把父进程挂靠到1上面实现的，守护进程没有控制终端tty，所以不会因为控制终端关闭而被杀死。所以守护进程的子进程可以安全的在后台运行。（这里好像还有当前工作目录、session相关的知识，需要抽个时间总结一下）

- 前端加密方式
1.简单加密，比如把密码先base64，然后分成几段，打乱再拼接。
2.对称加密（加密和解密都用同一个秘钥），比如DES、AES等。
3.非对称加密（加密和解密用不同的秘钥），比如RSA等。
4.单向hash加密（不可逆），比如md5、SHA1等。
5.单向hash算法加盐（不可逆），比如SHA256等。
6.用户登录密码需不需要加密？这个和加密无关，和你的app的生态有关。如果你是开源生态，比如github，可以允许用户通过机器人、爬虫等第三方工具登录，那么不需要加密，直接明文https，靠https加密就行了；如果你是闭源生态，比如知乎，不允许用户通过机器人、爬虫等方式登录，那么需要加密。

- 作用域是指如何查找变量的一套规则。具体在js里面作用域有哪些规则呢？
1.使用词法作用域，也就是根据代码编写的位置来确定作用域。
2.作用域分为2种，一种是函数作用域，由函数生成；另一种是块作用域，由大括号、try catch、with等代码块形成。
3.代码提升，通过var或者function声明的变量会提升到前面，其中function提升的优先级更高。
4.闭包，由于函数式一等公民，所以在作用域内向其它地方传递函数的时候（比如回调、返回值等），当前作用域会销毁，所以会把当前作用域内的变量放到闭包中保存到堆上面。

- http相关
1.http1.1增加了keep-alive；http2增加了首部压缩、二进制分帧、多路复用等；http3即quic使用udp。
2.强缓存使用expire或者cache-control；弱缓存使用if-not-modified和if-none-match。
3.应用层http协议解决了什么问题？a.描述这个应用是什么样子的（首部字段：content-type、accept-charset、host等等）。b.需不需要缓存（强缓存和弱缓存）。c.是不是有连接的？（三次握手和四次挥手）
4.传输层tcp协议解决了什么问题？a.怎么和process通信（socket四元数组）。b.可靠性的传输（错误探测-校验和、传输反馈-ack，重传-timer）。c.保证顺序（sequence-numbers）。d.流量控制（拥塞控制）
5.网络层ip协议解决了什么问题？a.怎么在路由间传输（forwarding and routing）。b.以什么方式来传输（diagram）

【2022.4.26】继续看八股文 + 基础知识

- 粘包问题
1.出现的原因：TCP为了减少IO消耗，在发送数据包的时候会缓存它们，如果短时间内有多个数据包的话，会缓冲到一起作一次发送。
2.粘包为什么会是一个问题：接收端不能识别服务端的消息边界，所以不能把服务端的消息分开展示。
3.解决方法：多次发送之前间隔一个等待时间；关闭粘包算法；进行封包、拆包。

- 怎么保证可靠传输
发送方在发送每个数据包的时候都会分配一个序列号，接收方在接收每个数据包的时候都会返回一个ACK，这样发送方如果发现某个包没有被对方ACK，则会超时重发。而且接收方使用这个序列号来保证数据包的顺序。

- 什么是DNS
早期在使用TCP/IP通信的时候，ip地址是一串比较长的数字，很难记；于是人们想了一个办法，给ip设置一个别名，这样只需要记住别名就可以了，这个别名就是今天的域名，而记录这个域名和ip地址对应关系的服务就是DNS。（Domain Name Service）

- 使用node直接运行命令
```bash
node -p -e "Boolean(process.stdout.isTTY)"
```
