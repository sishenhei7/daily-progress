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

【2022.4.21】继续看八股文 + 基础知识

- node 在调用 require 的时候，会执行以下步骤：
1.解析：找到文件的绝对路径。
2.加载：确定文件的类型。
3.封装：使用函数将文件代码封装起来，给它一个独立的上下文。
4.评估：执行文件代码
5.缓存：缓存到module.exports里面去


