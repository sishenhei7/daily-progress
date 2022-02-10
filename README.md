# daily progress

> 正在看这个项目的那位，没错就是你，你以后绝对会有出息真的

## 存档

[2021-12](./2021-12)

[2022-01](./2022-01)

## 内容

【2022.2.1~2022.2.6】过年，看完了东野圭吾的[《恶意》](https://book.douban.com/subject/3646172/)

【2022.2.7】在看面试题和[《effective typescript》](https://book.douban.com/subject/34893998/)，有点迷茫不知道干啥

【2022.2.8】在看 vue3 的源码，这周看完写总结

【2022.2.9】今天在看[《剑指前端offer》](https://febook.hzfe.org/awesome-interview/book1/network-security)，看vue3源码看到了updateEffect那里

- xss 是跨站脚本攻击，是指攻击者利用漏洞将脚本代码注入到其它用浏览器的攻击方式，常见的类型有反射型，储存型和dom型。反射型是指攻击者通过在 url 上加上恶意代码，然后服务器在取出恶意代码之后拼接到 html 里面返回给用户浏览器。储存型是指，攻击者把恶意代码储存到服务器数据库里面，用户在访问的时候，服务器把这段恶意代码注入到用户浏览器里面。dom型是指攻击者不经过服务器，直接通过在 url 上面加上恶意代码，用户在打开 url 的时候，页面执行 url 上面的恶意代码。
- 怎么防范 xss？1.对外部输入的内容进行充分转义；开启CSP，规定哪些外部资源可以加载和执行；设置httponly属性，禁止js读取cookie。
- csrf 是跨站请求伪造，是指攻击者在第三方网站通过诱导用户向被攻击网站发送请求，利用用户在被攻击网站已有的身份凭证，达到攻击的目的。
- 怎么防范 csrf ?1.设置白名单，仅允许referer是指定来源的请求。2.双重cookie认证，请求时需要在请求头里面也加上cookie，服务器在认证的时候比较请求头和cookie是否一致。3.csrf token，通过在请求里面加上csrf token，然后比较是否和服务器储存的一致。4.cookie 使用samesite 属性。5.验证码验证。
- 中间人攻击常用的攻击方式有2种：arp欺骗和dns欺骗。中间人攻击的防范是支持https和开启hsts。
- 浏览器渲染流程：处理html标记生成dom树；根据css样式表，计算出dom所有节点的样式；计算每个元素的布局信息；分层渲染；合成。
- 浏览器打开url做了些什么？思路：1.浏览器对url的智能建议、智能补充、清空之前的页面。2.dns解析，根据url的域名查找ip地址。3.使用一个随机端口通过socket连接操作系统构造http请求，操作系统连接网卡，通过网卡把请求发出去。此时如果数据包太大，会把数据包进行分块。4.和服务器进行三次握手连接上TCP，如果使用https又进行https握手，然后进行传输数据，传输完毕之后进行TCP四次挥手。5.浏览器接收到html数据之后就进行渲染了，详细见渲染流程。

【2022.2.10】今天在看vue3源码

- [细说 Vue.js 3.2 关于响应式部分的优化](https://juejin.cn/post/6995732683435278344)
- [Thoughts on ES6 Proxies Performance](https://thecodebarbarian.com/thoughts-on-es6-proxies-performance)
- [Object.observe为何要被移除？](https://github.com/luokuning/blogs/issues/1)







