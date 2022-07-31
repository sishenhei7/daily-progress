# daily progress

> 正在看这个项目的那位，没错就是你，你以后绝对会有出息真的

> （2022-06-06）日报坚持不下来，改成周报试试，每周日对这一周做详细总结，类似[manjusaka的周报](https://www.manjusaka.blog/weekly/2022-06-week1.html)

## 存档

[2021-12](./2021-12.md)

[2022-01](./2022-01.md)

[2022-02](./2022-02.md)

[2022-04](./2022-04.md)

[2022-06](./2022-06.md)

## 2022年7月第一周

### 生活

1. 这周深圳的疫情又开始严重了，周三公司所在的办公楼被封了，幸运的是，我们当晚没有被封在办公楼，而是直接测完核酸就放人回家了。周四就全员居家办公了，周五回到办公室工作。
2. 周末的时候突然发现，买的蒙牛优益C饮料只能在常温下放一天，否则就会被细菌污染，可是我前几周都是带5瓶到办公室去，然后每天喝一瓶，在常温下放了5天呢，难怪之前肚子有些不舒服，真的吓死了。以后一定要注意食品的保质信息。
3. 发现无籽麒麟瓜真的超甜超好吃，之前买的麒麟瓜都是有籽的，不知道是不是被坑了。发现在超市买的麒麟瓜比美团上面的贵，后面在美团上买一个试试。
4. 在京东上面买了 2 瓶 swisse 护肝片，打算吃吃看有没有效果。
5. 看[《说话的魅力》](https://book.douban.com/subject/3988256/)有一些感悟：就是如果确实发生了一些不好的事情，这时候我们不应该去后悔或者因为这个而去责骂别人，因为已经发生了，就算你责骂别人也无济于事；相反，如果我们看开一些，发生就发生了，我们没有能力在扭转结果了，只能注意以后不要发生这种事，同时，我们用这个心态去关爱别人，我们也许能活的更快乐，并且交到更多的朋友。

### 技术

1. 这周刷 leetcode 刷疯了，保持了每天至少刷 3 题的节奏，结果把脑袋刷晕了，周赛打的稀烂，只做出了 1 题，做第二题的时候脑袋晕了，导致做第三题的时候延迟了 5 分钟才 AC，最终只在规定的时候内提交了 1 题。总结原因，主要是每天刷 3 题刷的自己不自信了，自己的状态也确实不太好，打算再保持一周每天3题的刷题节奏看看。
2. 和微软的朋友讨论了一下关于协程的东西，并且查了一下资料，涉及到 cps、trampoline、尾递归、无栈协程和有栈协程、callcc、阴阳谜团这些概念，也搜到了一些相关的 paper，读起来挺吃力的，脑袋都晕了还没有理解清楚。等理解清楚了写一篇文章出来。
3. 因为在查上面的资料，所以[Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters)和[MIT公开课-分布式系统](https://www.bilibili.com/video/BV1qk4y197bB?p=3&vd_source=c6be3f72a67d4ae3e8f5ed24365119e5)都鸽了一周。
4. [leetcode-crawler](https://github.com/sishenhei7/leetcode-crawler) 项目也只修了一些 bug，这周也没有开始写 C 端展示。

### 总结

这周基本上感觉自己晕乎乎的，一方面每天刷 3 道 median 以上的 leetcode 题刷的有些不自信了；另一方面看协程相关的东西好难理解。但是和朋友讨论协程的时候认识到了我的一些思维局限，查东西的时候查到王垠的博客去了，发现大佬们对这些概念都有非常深入的理解，cps 这种编程方式也让我大开眼界，编程真好啊（不是

## 2022年7月第二周

### 生活

1. 这周四去海边夜骑了，没有上次凉爽，海风都是热的。但是骑完出了一身汗，呼吸了海边的新鲜空气，还是感觉非常爽的。最近的运动量总算达成了。
2. 发现周末总是吃的太饱了，吃了小米粥、自己炒的菜、西瓜、方便面、酸奶、烤肠，吃的太撑了，以后要注意减下去。
3. 发现现在的生活不是自己想要的，但是又没有破局的办法，困扰ing，这周每天晚上1点才睡。
4. 无意间发现易中天的品三国系列还挺有意思的，然后发现他写有一本关于中国史的书[《易中天中华史》](https://book.douban.com/subject/35665782/)，正好我想看一些历史的东西，所以开始看了。
5. 看[《说话的魅力》](https://book.douban.com/subject/3988256/)有一些感悟：1.说话的时候最好以关怀代替质问，以建议代替责难，以暗示代替直言。2.试着逆向思考，表达逆转式的幽默。

### 技术

1. 这周打了两场 leetcode 周赛，打的时候脑子清醒了一些，两次都在前 500 名以内（希望后面能打进 200 名），感觉挺有成就感的。其中有一次周赛在最后 2 分钟 AC 了一题，真的太刺激了。但两次都没有做出第四题来，后面那次周赛无限接近，超时之后想了1个小时还是没有想出来，感觉好可惜。另外发现了 2 本比较好的书: [挑战程序设计竞赛](https://book.douban.com/subject/24749842/) 和 [算法竞赛入门经典](https://book.douban.com/subject/4138920/)，后面可以找时间看一下。想了下为什么最近这么热衷于打周赛，一个是因为我是数学专业的，希望能在这里发挥我的特长；另一个是因为想训练下自己在紧张环境下的抗压能力；最后是打周赛挺刺激挺爽的。
2. 发现 cps 只是一个编程概念，而不是一个语法或者解决问题的方法，而协程这个语法是由编译器实现的而不是用 cps 实现的，所以我之前的理解的方向有些偏。所以我又跑去看[EOPL](https://book.douban.com/subject/3136252/)了，希望能理清楚 continuation 相关的东西，碰巧我对编译器也挺感兴趣的。看的时候又又跑去把 scheme 的语法看了，这是相关资料：[Scheme](https://people.csail.mit.edu/jaffer/r5rs_toc.html) 和 [Scheme入门教程](http://deathking.github.io/yast-cn/)
3. [Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters)、[MIT公开课-分布式系统](https://www.bilibili.com/video/BV1qk4y197bB?p=3&vd_source=c6be3f72a67d4ae3e8f5ed24365119e5)和[leetcode-crawler](https://github.com/sishenhei7/leetcode-crawler)这周又鸽了，罪过罪过。

### 总结

不能再开坑了，先把现有的坑全部填完吧，下周要加油哦，不能再鸽了。另外这个夏天真的好热，听说上海都已经 40 度了，深圳也是热的不行，大家要注意防暑呀！

## 2022年7月第三周

### 生活

1. 这周没怎么锻炼，下周要加大锻炼量了。
2. 这周偶然认识了一个在华为做后端的校友，他也是转行的，其中的艰辛懂的都懂；但是他去华为了，我还在一个小厂干，真是感慨万千。
3. 想了很多关于裸辞和搬家的问题，最后还是决定先不裸辞，但是搬家还是要搬的，还是一个人住更好。
4. [《易中天中华史》](https://book.douban.com/subject/35665782/)看完了第一卷，感觉挺有意思的，女娲、亚当、夏娃、伏羲、黄帝、蚩尤、炎帝、尧、舜、禹相关的历史挺有意思的，易中天还夹带了一些私货，讲了从原始族群、氏族、部落、部落联盟到国家的发展历程，让人大开眼界。
5. [《说话的魅力》](https://book.douban.com/subject/3988256/)看完了，有一些感悟：1.你的才艺是一回事，你的“气”是另一回事。只有才艺、胆识加上气度，才能成为群众魅力，也才能创造超级的领导者、表演者和演说家。2.说话的音量、姿势、动作甚至灯光，都能影响说话的效果。我们在说话的时候，如果呼出了很多气，声音就会显得很温柔，如果说话的时候不怎么呼气，声音就会显得干巴巴的，这个深有感触，当我在公司做分享的时候，经常会由于紧张，而在说话的时候呼出很少的气，最后导致声音干巴巴的。以后如果要温柔的说话，就要注意说话的时候呼出很多气了。另外灯光也是一门学问，顶上或者后面的灯会让人显得很黑和阴暗，对于美貌大打折扣，所以以后远程面试的时候，一定要注意脸上的灯光。

### 技术

1. 这周周赛被 bigint 坑了，第三题 testcase 没有通过，想了好久没找到原因，最后被别人提醒才发现是 bigint 的锅，再加上这周的周赛又很简单，导致最后即使 AC 了四题，最终排名却排到了 2200+。这次是第一次发现，周赛的 testcase 还能 hidden 不显示原因的。然后上周的第三题还被 rejudge 并且被判不通过，导致排名从 400+ 掉到了 2000+，最后看了一下题才发现，有个场景没有考虑到，真的太亏了，要是在比赛的时候发现只会扣 5 分钟，不至于掉这么多排名。离获得 knight 勋章又多了好几周了。
2. 这周把 [MIT公开课-分布式系统](https://www.bilibili.com/video/BV1qk4y197bB?p=3&vd_source=c6be3f72a67d4ae3e8f5ed24365119e5) 的 mapreduce lab 做完了，test 全部 pass 了，挺有成就感的。raft 的 paper 也看了一半了。
3. [EOPL](https://book.douban.com/subject/3136252/) 看完了前两章，目前没发现啥有意思的东西，只是在理清和适应相关概念和写法。
4. [leetcode-crawler](https://github.com/sishenhei7/leetcode-crawler)修了一个bug。
5. [Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters)这周鸽了，罪过罪过。

### 总结

总的来说，这周过得还不错，生活方面对于说话的技巧有很多感慨；技术方面做 mapreduce 的实验做的挺爽的。目前[EOPL](https://book.douban.com/subject/3136252/)没带来很强的感受，期待后续，[Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters)之前本来就快看完了，下周尽量看完收个尾吧。这周还是很热，七月过了一半了，七月新番有哪些好看的呢？

## 2022年7月第四周

### 生活

1. 这周也没怎么锻炼，太热了没去爬山，租车店又被疫情封控了所有也没有夜骑，和同事约好下周一起去健身房健身。
2. 这周发现常喝的鲜牛奶不是以前那个味了，这才突然想起现在天气这么热，买的鲜牛奶不一定能确保新鲜（即使保质期还没到），本来打算换牛奶砖喝了，后来发现另一种鲜牛奶喝着还行，就先喝那种鲜牛奶吧。
3. 周末突然发现在空调房吹风扇有点晕，查了下资料竟然发现，开空调不一定比开风扇差，之前不适应空调房所以觉得尽量不开空调只吹风扇会比较好，但是资料说吹风扇直吹脑袋和脚都不好，而且会导致吹到的一边迅速降温，但是没吹到的地方又降不了温不太好，生活小常识又GET！
4. 周末房间里的老空调开始猛滴水了，都滴到电脑和食物上，气得想搬走了，于是在手机上看了一下租房信息，最后总结了一下这次的租房策略：为了以后工作调动方便，这次租房只在自如或泊寓上面租，原因是想找押一付一的房子，同时也体验一下自如或者泊寓的房子。如果城中村或者有好的合租的话（安静 + 室友是程序员不瞎搞 + 无情侣），也不是不可以租。预算在2000-3000，3000都可以接受，睡得好才是真的好。另外也考虑在蛇口那里（海边）租。现在只是制定计划，下个月开始看房源，有合适的就开始租了。
5. [《易中天中华史》](https://book.douban.com/subject/35665782/)第二卷国家快看完了。国家的出现因素是人为了获得安全感（人身安全、财产安全、身份认同等），它是父系氏族、图腾的延续；巫术是科学和宗教的妈妈，巫术其实也是人们对大自然规律的探究，只不过走歪了，于是延伸出了科学和宗教；有些人的宗教起到了国家的作用，比如犹太人，他们没有国家，但是是宗教让他们团结到了一起。
6. 香港以前的 TVB 真是 yyds，[使徒行者](https://www.bilibili.com/video/BV1HZ4y1e7A2?spm_id_from=333.999.0.0&vd_source=c6be3f72a67d4ae3e8f5ed24365119e5) 和 [飞虎](https://www.bilibili.com/video/BV1pS4y177XQ?spm_id_from=333.999.0.0)真是太好看了，都有点想去看电视剧了。

### 技术

1. 这周周赛感觉挺简单的，两场周赛都 AC 了 4 题，希望不会被 rejudge。另外研究了一下 dijkstra 算法，本来我以为单点最短路就是一个简单的 bfs 而已，直到我做了一个最短路的题才猛然醒悟竟然不是，然后看了下 dijkstra 算法才知道原来如此，dijkstra 算法用到了最小堆来维护未遍历的距离最短的节点；值得一提的是，dijkstra 算法在某些场景下可以把最小堆换成双端队列实现，比如[信物传送](https://leetcode.cn/problems/6UEx57/)这题。另外我还研究了一下有序队列，之前做有序队列都是用二分找到index，然后用splice插入，但是今天突然发现用splice插入可能性能很差，所以找了下有序队列的真正实现，才发现一般是用最大堆、红黑树等方式实现的，比较高大上的实现貌似还有 finger 树，对了，还有最大堆还有一个update方法，我在自己实现的最大堆里面漏了，后面要补上。
2. 这周把 [MIT公开课-分布式系统](https://www.bilibili.com/video/BV1qk4y197bB?p=3&vd_source=c6be3f72a67d4ae3e8f5ed24365119e5) 的 raft 课程学完了（有 2 课，paper 见 [Raft](http://nil.csail.mit.edu/6.824/2020/papers/raft-extended.pdf)），对于这种主从同步的分布式流程大概搞清楚了，接下来就是做实验 2 了。
3. 才发现[Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters)后面还有好几章没看，而且内容好多，重新认识到 haskell 这门编程语言真的挺复杂的，之前学了点 scheme 就天真的以为 haskell 和 scheme 差不多，现在才发现我太天真了，scheme 确实是世上最简单的编程语言啊。
4. [EOPL](https://book.douban.com/subject/3136252/) 和 [leetcode-crawler](https://github.com/sishenhei7/leetcode-crawler) 这周鸽了。
5. 附上一个 [rust os](https://learningos.github.io/rust-based-os-comp2022/)训练营，后面准备开坑。感觉我准备朝语言方向发展？

### 总结

现在天气太热了，基本都是蜗居，没怎么出门0.0周末又被找房子的事干扰着导致没怎么看书，罪过罪过。哎，天太热了啊啊啊！

## 2022年7月第五周

### 生活

1. 这周租车店仍旧被封了，不能夜骑，于是在公司健身房锻炼了几天。
2. 学到一句：优秀之处并非原创；原创之处并非优秀。
3. 这周一直在网上看房，看到合适的又觉得太远了，近了的又觉得太贵了要3000+。本来想在蛇口那边租房子，想着在海边住着该有多舒适啊，但是又舍不得那几个钱；然后又想着在一条人少的地铁始发站或终点站附近租，这样每天上下班不用太挤，然而还是没看到合适的并且太远了。最后思来想去还是续租了半年，希望这半年内至少要达成这几个目标中的一个：1.工作在大厂稳定下来。2.找一个女朋友一起合租。一个人租房真是太难受了，如果能在大厂稳定下来，那就去租这个大厂附近的公寓或者小区；如果有女朋友一起合租，就去租个两房一厅，或者租个大的一房一厅。暗暗发誓：半年之后一定不能为了这区区3000多块钱的租房费用而考虑再三了，这个苦只能吃这半年！
4. 继续读[《你不可不知的人性》](https://book.douban.com/subject/25803206/)，看的挺有感触的，这是摘抄：1.记得二十多年前，名音乐家邓昌国先生在世的时候，有一次我问他对一位新起的年轻钢琴家的看法。“她最好别结婚。”邓先生说。“为什么？”“因为她最好活在她纯真的世界，不要接触柴米油盐。”事实证明他的话没错，那耀眼的一颗星，结了婚、离了婚，消失了光芒。我后来常想，是不是有些人就应该一辈子活在象牙塔里做他的梦，在那水晶般纯洁的世界里，以他们的“冰雪聪明”创作。2.这就是人，总觉得别人欠自己的。人人都这么感觉，所以反过来想，就成了“我们总是欠别人的”。看完这本《你不可不知的人性》，我希望你也能反过来想想：“自己有没有亏欠？自己的人性又如何？”当你叹人性可悲的时候，也能想想自己的“卑微”与“悲哀”。
5. [《易中天中华史》](https://book.douban.com/subject/35665782/)看了不多，依稀只记得国外是君权神授，而中国是君权天授。国外正因为是君权神授，所以他们信奉神这个宗教；但是中国是君权天授，是老天授予的，所以中国人不怎么相信宗教，所以中国人是祖宗崇拜。那么中国皇帝怎么获得上天的授权呢，答案是依靠以人为本。

### 技术

1. 这周周赛又做完了 4 道，感觉这几周的周赛太没有技术含量了。可能是因为大学生放暑假了吧，好让大学生冲分。这周碰到了一个题[952](https://leetcode.cn/problems/largest-component-size-by-common-factor/)，本来想用并查集的改进版本来做，但是怎么都AC不了，后来放弃了，用并查集模板做的，自己又对并查集有了更深的认识；对了还有一个并查集路径压缩的方法，其实就是在find递归函数里面递归的把祖先元素赋给自己。预估 1850 分就可以上knight，估计 8 月中旬就能有 knight 勋章了。
2. 发现[EOPL](https://book.douban.com/subject/3136252/)真的太难了，就算我是数学系出身，但还是看了2遍前面2章才看懂。
3. [MIT公开课-分布式系统](https://www.bilibili.com/video/BV1qk4y197bB?p=3&vd_source=c6be3f72a67d4ae3e8f5ed24365119e5)、[Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters)和[leetcode-crawler](https://github.com/sishenhei7/leetcode-crawler)这周都鸽了。
4. 附上几个学习资源：[chrome blink源码](https://github.com/nwjs/blink)、[v8源码讲解](https://github.com/v8blink/v8-JavaScript-Documents)、[chrome博客](https://blog.chromium.org/)、[用ts实现ts，没有运行时](https://github.com/ronami/HypeScript)、[linux核心代码品读](https://github.com/sunym1993/flash-linux0.11-talk)、[jest实践指南](https://github.yanhaixiang.com/jest-tutorial/)

### 总结

又有差不多 1 个月没怎么看动漫了，只是偶尔看看漫画，越来越觉得动漫没啥意思了。7月份就这样过去了，越来越觉得前端行业没啥前途了，三年又三年，我的未来何去何从？
