# daily progress

> 正在看这个项目的那位，没错就是你，你以后绝对会有出息真的

> 日报坚持不下来，改成周报试试，每周日对这一周做详细总结（2022-06-06），类似[manjusaka的周报](https://www.manjusaka.blog/weekly/2022-06-week1.html)

## 存档

[2021-12](./2021-12.md)

[2022-01](./2022-01.md)

[2022-02](./2022-02.md)

[2022-04](./2022-04.md)

## 2022年6月第二周

### 生活

1. 这周一直在下雨，没有去夜骑，下周一定要去一次。
2. 感觉周末在家做的瘦肉炒菜不那么好吃了，但是发现小米粥超好喝，准备下周少炒菜，多煮小米粥。
3. 喜欢上了吃榴莲。之前买的榴莲到了，开了之后发现没熟，然后用毛巾包起来放了一晚上（不敢放太久，怕坏掉了）之后，终于熟了一半，把果肉扣出来放冷冻了。用深圳消费券在京东上又下单了200元10斤的榴莲！
4. 看完了[《爱的艺术》](https://book.douban.com/subject/3026879/)，懂得了爱是一门艺术，需要不断地练习、精进和实践，在爱他人的过程中实践。只爱一个或几个人是不对的。
5. 发现自己最近说话比较直，比较愣头青，想了下这样对我以后的发展不好，准备改一改这个毛病。所以开始在微信读书上面读起了[《你不可不知的人性》](https://book.douban.com/subject/25843222/)和[《说话的魅力》](https://book.douban.com/subject/3988256/)，并且每天反省当天说错了哪些话，怎么说可以更好。
6. 看完了[七武器之孔雀翎](https://space.bilibili.com/1545141866/channel/collectiondetail?sid=91989)的解说，发现超有意思啊。顺带看完了[少年张三丰](https://space.bilibili.com/1545141866/channel/collectiondetail?sid=170519)的解说。有点剧荒了，在b站找了点有关历史的纪录片以后看看。
7. 开始看[《法治的细节》](https://book.douban.com/subject/35635639/)普及一下法律知识，但是感觉比较枯燥，以后可能会刷刷[罗翔老师的b站视频](https://space.bilibili.com/517327498?spm_id_from=333.337.0.0)

### 技术

1. 仍旧在保持 lc 的每日一题。和群里的引证大佬讨论，有些时候有序数组比线段树效率更高。
2. 最近在看[a tour of go](https://go.dev/tour/moretypes/4)的时候看到，go里面的指针如果指向一个 struct，那么可以直接用这个指针加点号来获取这个 struct 里面的属性，而不需要给指针解引用，这个应该是在编译的时候做的优化。然后我突然灵光一现，js 里面的引用和指针有什么不同呢？js 里面是没有地址这个概念了，所以 js 里面的引用虽然也和指针一样是一个地址，但是 js 在解析这个地址的时候不会把地址暴露给用户，而是加了一层优化，就是每次获取引用的值的时候，就把这个引用的地址指向的数据而不是这个地址返回给用户，所以我们在给引用赋值的时候，会改变这个引用的地址，而不会改变这个引用指向的对象。
3. 在看 react 的源码，把 react 从开始到渲染的流程大致过了一遍。才发现 react 和 vue 真可谓天差地别。虽然两者都是前端框架，但是 react 更侧重于快速响应，它用了很多代码（虽然有很多代码是丑陋的）来实现了一个时间分片的功能，使得react在更新的时候可以不阻塞渲染，从而能够快速响应。而 vue 对这方面的优化基本没有。另一点是 react hooks 实现了副作用的抽离，这个和函数式里面副作用的抽离不谋而合，虽然vue3也仿照 react hooks 推出了 composition api，但是感觉两者的理念不同，实现也不同。对于 react 我还有很多疑问，比如 setstate 的时候是怎么更新的？react hooks 是怎么加入进去并抽离副作用的？ssr 是怎么实现的？react fibre 更深入的理解？恰好我发现了[《React技术揭秘》](https://react.iamkasong.com/preparation/idea.html)，准备带着这些疑问一直看下去。
4. 学完了著名的[MIT公开课-分布式系统](https://www.bilibili.com/video/BV1qk4y197bB?p=3&vd_source=c6be3f72a67d4ae3e8f5ed24365119e5)的**前两课**，为什么要学这门课，是想了解一下分布式到底是怎么架构的。现在各种云、k8s、运维都使用了分布式，所以我觉得了解一下分布式的底层实现是非常有必要的。这也是补习计算机基础的必需课。和以前一样，我不仅会看完所有的视频，还会看完所有的课前 paper 和做完所有的课后实验。
5. 对函数式编程有了浓厚的兴趣，目前在开始看[Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters)（看到了第6章）和[Category Theory for Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)。比较大的感受是发现了[list comprehension](http://learnyouahaskell.com/starting-out#im-a-list-comprehension)，即列表推导式，这个语法在 python 里面也有，但是在 js 里面没有，感觉它非常强大和优雅啊。还有就是类型在编程里面真的非常重要，动态类型语言写起来让人很不放心啊。最后就是函数式编程的思维了，现在不管是并发领域，还是副作用抽离，都是编程语言的发展方向，越来越向函数式靠拢了。

### 总结

开了很多坑，希望自己能坚持下去；这周也没啥输出，下周要抽时间输出点东西了。



