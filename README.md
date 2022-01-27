# daily progress

> 正在看这个项目的那位，没错就是你，你以后绝对会有出息真的

## 存档

[2021-12](./2021-12)

## 内容

【2022.1.1】今天想看源码了，于是就去看 vite 的源码了

- vite 中的性能检测原理？CPU 分析器原理？
- vite 在 rollup 打包的时候自己写了一个筛选替换的插件很有参考意义
- vite 的入口是在 bin 文件夹的 vite 文件里面，首先它判断是否作为依赖，如果不作为依赖的话，就引入 source-map-support 库来矫正 V8 的错误堆栈；然后它判断 debug、filter 和 profile 参数来设置对应的环境变量，然后引入 perf_hooks 检测运行时间，引入 inspector 检测运行时的 CPU 占用，最后执行 cli 文件；在 cli 文件中，它使用 cac 库来建立一个 cli，然后设置 cli 的各种参数，在 dev 的环境下，它先使用 cleanOptions 清理不需要的参数，然后使用 createServer 建立一个服务器。这个 createServer 是在 server 文件中定义的，再来看 server 文件。createServer 是一个典型的工厂函数，它在闭包里面进行各种处理，最后导出了一个 server。在闭包里面它主要做了如下处理：1.使用 chokidar 监控文件改动，在文件修改、新增、改变 pkg 等不同文件的情况下做不同的处理，这个不同的处理主要是使用 ModuleGraph 类来完成的，它通过给 module 建立一个各种 map，然后对 module 执行各种操作。2.建立一个 rollup container，调用 rollup 的各种原生 api 对文件进行处理，就是在这一步来给 ModuleGraph 添加各种 module。3.建立 2 个服务器，http 服务器和 websocket 服务器。在建立 http 服务器的过程中会判断是否是 https 服务器，如果不是则直接建立 http 服务器，然后判断是否使用了代理，如果是则只能建立 http1 的 https 服务器，如果不是则建立 http2 服务器。而 websocket 服务器则主要用来支持 hmr 功能。4.给 http 服务器加上各种中间件，里面有很多有意思的中间件，比如打开浏览器的中间件、打开编辑器的中间件、csr 回退中间件等，这些所有的中间件以后要重点看一下。5.在 vite 关闭的时候做各种清理工作：关闭 watcher、关闭 ws 服务器、关闭 rollup container、关闭 http 服务器。
- 需要集中看一下的东西：1.rollup container 做了什么？是怎么在 ModuleGraph 中添加各种 module 的？2. http 服务器的各种中间件的实现和作用

【2022.1.2】今天去爬山了，不过还是看了一下 vite 的 server 中间件源码：

- express 里面 redirect 都是直接修改 req.url 达成目的的，koa 里面能不能也这么做呢？我看我司里面都是 redirect 的。
- proxy 库的原理？
- magicstring 的原理到底是什么？
- koa-send 库解决了什么问题？
- node-http-proxy 的原理？

【2022.1.3】今天在看 vite 的中间件和它使用的部分库的源码。并且把部分中间件迁移到我的服务器上面。

- vite 有 2 个有意思的功能，一个是打开浏览器，它的原理是在 nodejs 上执行 shell 命令打开浏览器；另一个是打开编辑器并跳转到相应的代码行，它的原理也是使用 shell 执行命令打开，不过它还使用 shell 命令获取当前的所有进程，并在其中查找编辑器进程，从而来猜测当前用户的编辑器是哪个。这就是 nodejs 平台的力量，通过执行 shell 命令来做很多有意思的事情。还有一个有意思的就是，打开编辑器作为一个中间件运行，也就是说，我们能通过发出 http 请求来让服务器执行各种动作比如打开编辑器，这就可以进行引申了，比如我们可以加一个 ping 中间价来专门用来检测服务器是否正常；比如我们可以加一个中间件来清空服务器的缓存；比如我们可以加一个中间件来让服务器帮我们开关门；比如我们可以加一个中间件来让服务器帮我们启动电饭煲进行煮饭。
- vite 给每一个中间件命名了，而不是使用箭头函数，这样在中间件报错的时候，就会在错误信息里面显示中间件的名称！如果是箭头函数的话，错误信息里面显示的就都是 anonymous！
- koa 和 express 最大的区别有二个：1.express 在风格上沿用的是 nodejs 的回调传统，是作为 nodejs 原生服务器的一种补充；而 koa 使用 async 语法，创立了一种全新的风格。这种全新的风格带来了两点不同，一个是中间件变成了洋葱模型（express 的中间件不是洋葱模型，都只会执行一次），另一个是错误处理，在 express 里面错误都通过 next 附带的第一个参数传递下去（这也是 nodejs 回调函数的风格，第一个参数是错误），而在 koa 中，由于使用了 async 模型，所以错误会在最后被 catch，并且，如果一个中间件 throw error，就会跳过剩下的中间件，最后在 catch 中被捕捉。（所以虽然大部分 express 中间件都可以强制改造成 koa 的中间件，但是在一些边界情况下会有异常的表现）。2.koa 只抽离了核心逻辑，其余所有的部分比如 router 都放到中间件里面去完成。
- 重看 koa-compose 源码，有这么几点需要注意：1.express 的 next 有一个参数 err；但是 koa 的 next 是没有参数的，因为在 koa-compose 里面，next 其实就是下一个中间件的封装，执行 next 其实就是执行下一个中间件。2.执行到最后一个中间件的时候会怎样？会停下吗？答案是看情况。koa-compose 的第一个参数是 ctx，第二个参数是 next，所以如果传了第二个参数的话，就会执行第二个参数的 next。在 koa 里面，相关语句是 `fnMiddleware(ctx).then(handleResponse).catch(onerror)`，没有传第二个参数 next，所以当执行到最后一个中间件的时候就不会执行下一个了，已经执行到了洋葱模型中间，再从洋葱模型中心执行回去。在 koa-router 里面是这么用的`compose(layerChain)(ctx, next)`，它传递了 koa 传给它的 next 进去，所以 koa-router 执行完所有匹配的 router 之后，会去执行剩下的中间件，然后再回来。（不过现实中不会这样，因为一旦在 router 里面匹配上了，我们在代码里面就不会继续写 await next 了，这个时候就自动回去了，不再执行剩下的中间件）3.中间件必须是 async 函数吗？不需要。因为在 koa-compose 里面使用 Promise.resolve 包了一层，我们知道如果 Promise.resolve 了一个 promise，就会直接返回这个 promise，如果 Promise.resolve 后面不是一个 promise，就会在外面包一层 promise。所以中间件如果不是 async 函数，就会通过 Promise.resolve 在外面包一层 promise。（注意，比如如果在第 n 层中间件没有使用 async，那么由于它被包裹在 Promise.resolve 里面，所以在第 n - 1 层中间件里面需要使用`await next()`，但是由于第 n + 1 层中间件也会被包在 Promise.resolve 里面，所以在第 n 层使用 next 的时候，需要使用`return next()`，这个 return 是必须的，否则不会进入下一个中间件，因为当普通函数 return 了一个 promise 的时候，Promise.resolve 就会直接返回这个 promise。如果想在洋葱模型返回的时候执行一些代码，就加一个 then，比如`return next().then()`）（可以从另一个方面理解，由于 async 函数会自动返回一个 promise，所以如果中间件不是一个 async 函数的话，就需要手动返回一个 promise，所以一定要 return next）
- 重看 koa-router 源码，有这么几点需要注意：1.它其实就是给路由用了中间件模型，并且支持多个路径、命名路由和多个路由的写法。koa 的中间件也可以直接塞到路由里面使用。2.我们是这么使用路由中间件的`app.use(router.routes()).use(router.allowedMethods())`，其中`router.routes()`返回所有匹配到的路由，并使用 koa-compose 合在一起当做 koa 的中间件进行执行，然后`router.allowedMethods()`其实就是在洋葱模型的返回路径上，判断有没有正常返回，如果没有的话，就查看是否有匹配，如果有匹配的话，就添加一个 allow 的 http 头告诉客户端支持哪些 methods，同时也在这里处理 OPTIONS 请求，如果是 OPTIONS 请求的话，就正常返回。
- 看 node-http-proxy 源码，我们简要说明一下在使用的时候做了什么。首先，初始化语句`var proxy = httpProxy.createProxyServer({target:'http://localhost:9000'})`其实并没有建立一个 server，而只是做了一个代理配置的初始化；然后就有 2 种方式代理了，一种是`proxy.listen(8000)`，这里才会实际建立一个 server 代理请求；另一种是在其它 server 里面`proxy.web(req, res, { target: 'http://127.0.0.1:5050' })`进行转发，注意这里其实并没有建立 server，而是使用包裹它的 server。最后，这个代理库到底做了什么呢？代理到底是什么意思？其实简单来说代理的意思就是修改请求的 url 即`req.url`，复杂来说的话，还要把相关的 cookie、headers 转发过去，就这么简单！

【2022.1.4】今天自己重写了 koa 上面的 proxy 中间件，看了下 http 相关的 node 文档。

【2022.1.5】vite 上面的中间件迁移完毕，开始迁移库上面的部分中间件。

- 在写代码的时候要注意支持查看代码的时候的编辑器跳转
- 当匹配不到的时候就取 index.html 这个逻辑到底在哪儿？
- 使用 sirv 库改写 koa-static 提升性能
- 重学环境变量：前端的环境变量分为 shell 的环境变量和打包时候的环境变量 2 种。shell 的环境变量可以通过 process.env 访问；然后可以在 script 里面通过 export（mac）和 set（windows）两种方式来定义，如果 cross-env 库解决了前面跨平台的问题，在不同平台上只需使用 cross-env 来定义即可。在使用 webpack 打包的时候也有一个环境变量的概念，在打包文件里面也可以通过 process.env 来访问，在 webpack 打包的时候会直接使用定义的环境变量进行替换；这里的环境变量可以通过 definePlugin 来定义，在 vue-cli 里面，一般是通过 development、production 相关的环境文件来定义，但是也可以直接定义在 shell 的环境变量里面，vue-cli 会自动引入，但是前提是需要以 VUE*APP* 开头，vue-cli 引入相关的代码在[这里](https://github.com/vuejs/vue-cli/blob/60140af5ba029e30d433ebf5afd442f754ee87e5/packages/%40vue/cli-service/lib/config/base.js#L183)
- koa-static 真是太狡猾了，其实所有的事情都代理给 koa-send 去做了，它自己只做了 methods 判断和简单的错误处理。
- 为什么`http://localhost:9000`的 ctx.req.url 的值是`/`，然后`http://localhost:9000/`的 ctx.req.url 的值也是`/` ？koa 里面 ctx.req.url 是这样处理的，它先使用 node 原生的 url.parse 解析出一个 URL 对象，然后支持对 search 等的各种处理，最后通过 url.format 组装成 url 返回给 ctx.req.url。因为在解析出 URL 对象的时候`http://localhost:9000`解析出的 URL 对象的 pathname 就是`/`，所以`http://localhost:9000`和`http://localhost:9000/`的 ctx.req.url 是一样的。
- 为什么 koa-static 支持自动在路径下面加 index.html？就是在请求文件的时候，它会自动把 index.html 文件返回给我们？因为在 koa-send 里面，会判断路径是否是斜线结尾的，如果是的话，会默认自动加 index.html（这个支持自定义。）由于在 koa-static 里面会把传入的参数当做 root ，把 url 的 path 当做 path 传给 koa-send，所以 koa-static 并不支持完成后端路由的功能，只能用 koa-send 了。

【2022.1.6】在写中间件

- path.resolve()、`__dirname`、process.cwd()、require.resolve()，重新认识相关的 resolve 路径问题

【2022.1.7】在写中间件

- nuxt 的 fetch 是怎么实现的？
- 在 url 上面有语言链接的情况下，router 是怎么正确 parse 的？
- nuxt 里面的 module 和 plugin 的原理分别是什么？
- 调研一下怎么压测，使用 sirv 替换 koa-static 进行对比

【2022.1.8】今天爬山，没写太多，准备开始写网站了（服务端还差 axios 封装，到时候和客户端 axios 一起）

【2022.1.9】今天看 nuxt 源码，明天写总结。

【2022.1.10】今天总结 nuxt 源码。

- nuxt 是一个 monorepo，里面有 cli、builder、config、core、vue-app、vue-renderer、webpack 等库，这次从 cli 开始，看三个命令的流程：dev、build、start。
- dev 流程主要分为监听 dev 和构建 dev，在监听 dev 中：1. load nuxt.config.js 文件里面的内容，然后使用里面的配置初始化 nuxt 对象，初始化 nuxt 对象的时候，会初始化 ModuleContainer 和 server：ModuleContainer 用来处理 modules；只初始化了 server 的各种依赖和壳子，并没有建立服务器。2.加上 restart 和 change 钩子，触发 restart 的时候，会先清除掉 nuxt 的各种钩子，然后重新监听 dev 和构建 dev。3.等待 nuxt ready。在等待的过程中，会先等待 ModuleContainer ready，这个时候就开始执行 modules 里面的代码了，并且把里面的 tpl 模板作为 plugin 添加进去，这个时候 tpl 模板还没有被转义；然后等待 server ready，这个时候开始初始化 vue-renderer，初始化的时候会添加 build:resources 钩子，当触发这个钩子的时候会 load 各种 resources，然后给 server 添加各种中间件，在这里会按顺序加上 dev 中间件、用户自定义中间件和 nuxt 中间件（dev 中间件是在 server:devMiddleware 钩子里面加的；nuxt 中间件会根据 route 渲染出 html，并给 html 加上各种标签，最后加上各种请求头）4.启动 nuxt 开始监听端口（注意，这里有 2 个有意思的地方，一个是使用 process.memoryUsage() 获取 heapUsed 和 rss 数据并使用 pretty-bytes 打印出来；另一个是使用 opener 库使用浏览器打开 dev 环境 url）这个时候，监听 dev 就完毕了，开始构建 dev 了：1.首先会初始化一个 builder，在初始化的过程中，会注册 build:done 钩子，在钩子启动的时候，启动 watchClient 和 watchRestart 两个监听回调。（watchClient 会监听文件改动，然后重新生成路由、store、渲染 template 等；watchRestart 回调主要是生成一个 restart watcher，然后把各种依赖的文件加入到里面去，当文件发生变化的时候，就触发 watch:restart 钩子）然后支持热加载。（首先使用 serverMiddlewarePaths 方法获取服务器端 connect 的所有中间件路径，然后起了一个 chokidar 来监听这些路径，最后使用 replaceMiddleware 替换相应的中间件。热加载的具体原理是什么？后面需要搞清楚一下）最后初始化 webpack 等构建工具的 bundleBuilder。2.让这个 builder 执行 build。首先会等待 nuxt ready，这里的 nuxt ready 和上面一样。然后生成路由、store、渲染 template 等，这个和 watchClient 里面的动作基本一致。然后根据 plugins 里面的 src 加载 plugins，此时并没有执行。最后让 bundleBuilder 开始构建。（这里以 webpack 构建为例，会先生成 server 和 client 的入口文件，然后让 webpack 分别从入口开始构建，这里可以设置构建使用的线程数，是使用 thread-loader 实现。从 client 入口构建的时候，会创建 webpackDevMiddleware、和 webpackHotMiddleware 中间件，并加到 server 的 dev 中间件里面去。这两个中间件做了什么？后面要细看；从 server 入口构建的时候，会使用 compiler.watch 来检测变动。）到这里 dev 流程就完毕了。
- build 流程分为两种，一种是 generate 生成静态资源（比如预渲染），另一种是调用构建工具的 builder 执行 build 方法。我们首先来看 generate：1.首先是初始化 generator，在这一步会初始化各种路径和 route，并且使用[defu](https://www.npmjs.com/package/defu)库进行默认值赋值，最后使用 webpack 初始化 builder。2.首先还是等待 nuxt ready，这里面执行的过程和上面的 nuxt ready 一样;然后调用 builder 的 forGenerate 方法设置 static 路径，然后启用正常的 build 流程，让 webpack build；然后初始化 routes（如果是预渲染的情况，就会对每一个 route 调用 nuxt-render 的 ssr 模式，生成 html，然后使用 node-html-parser 库解析 html，对于爬虫的情况替换里面的 href 链接到本站，最后使用 html-minifier 库精简 html，并生成预渲染文件）；最后调用 afterGenerate 方法，在这个方法里面会 render 一个 spa 作为 fallback（这里总结一下 spa、modern、ssr 的 render 方法的不同，在 spa 里面，render 方法会加上各种 meta、style、link 然后生成对应的 html；在 modern 里面和 spa 差不多，只不过里面的 style 是 module 形式的；在 ssr 里面，也和 spa 差不多，只不过把内容的 html 标签渲染进去了）。
- start 流程很简单，等待 nuxt ready 之后建立服务器就行了。

【2022.1.11】今天在总结 nuxt 的 build 流程和 start 流程

【2022.1.12】今天在写博客的业务架子

【2022.1.13】今天在看 webpack 的源码，初看之下感觉 webpack 其实就是一个壳子，它实际提供的是一个类似 pipe 的流程，首先我们把配置传给 webpack，建立一个或者多个 compiler，这个 compiler 在调用 run 方法进行 compile 的时候，会建立一个 compilation，然后使用 compilation 进行各种调用钩子和回调处理。

- webpack-cli 是怎么支持不需要配置就自动打包的？
- webpack 的 loader 是在哪里执行的？
- webpack 怎么拆分 chunk 的？
- webpack 的 plugin 是在哪儿执行的？
- webpack 从入口文件开始是怎么处理的？
- webpack 的 performance 做了些什么？

【2022.1.14】生病了，休息一天

【2022.1.15】今天在看 webpack 源码，明天写总结

【2022.1.16】今天在总结 webpack 源码

- 首先从`webpack.js`进入，里面会先校验传给 webpack 的 options 参数，然后使用这个 options 创建一个 compiler 对象，最后判断加了回调函数没有，如果加了就自动执行`compiler.run()`，如果没有就返回这个 compiler 对象（值得注意的是，使用 cli 运行 webpack 的时候，在 cli 里面就加了回调函数，然后自动执行了`compiler.run()`，我们手动引入 webpack 的时候就没有加回调，所以需要手动再 run 一下）。这里说一下创建 compiler 对象的时候做了什么，它是用 createCompiler 创建的，在 createCompiler 函数中，会首先标准化 options，比如把字符串形式的 entry 标准化为对象形式，然后对 options 参数添加默认选项，就是说如果用户没有指定相关字段的话，就用默认选项填充。最后才 创建 compiler 对象，在创建的时候会初始化 compiler 实例中的各种字段和钩子，创建完了之后添加第一个 plugin，环境 plugin，初始化环境，调用 environment 和 afterEnvironment 钩子，然后使用`WebpackOptionsApply`来添加各种内置 plugin，调用 initialize 钩子。
- 然后就进入了`compiler.js`里面，看看执行`compiler.run()`的时候做了什么。很简单，首先调用了 beforeRun 和 run 钩子，然后读取[records 路径](https://webpack.docschina.org/configuration/other-options/#recordspath)，如果有设置的话，webpack 会把相关的记录放到这个文件夹下面。最后开始 compile 了。
- 在 compile 阶段，首先会初始化[normalModuleFactory](https://webpack.docschina.org/api/normalmodulefactory-hooks/)和[contextModuleFactory](https://webpack.docschina.org/api/contextmodulefactory-hooks/)（这里说明一下 normalModuleFactory 和 contextModuleFactory，webpack 使用 NormalModuleFactory 从入口开始，一步步解析文件请求最后生成一个模块实例；而 contextModuleFactory 则是为了 require.context api 而设置了，它会匹配文件夹下面的所有模块，并把这些模块传入到 NormalModuleFactory 中进行处理），然后调用 beforeCompile 和 compile 钩子，之后使用 normalModuleFactory 和 contextModuleFactory 初始化 compilation 对象，最后一步步调用 make、finishMake、compilation.finish、compilation.seal、afterCompile 钩子就完成了。
- 这里解释一下是怎么一步步打包的。首先我们前面说在 WebpackOptionsApply 里面添加了各种内置 plugin，其中有一个 plugin 就是 EntryOptionPlugin，这个 plugin 会判断 entry 是否有多个，然后对每个 entry 都添加一个 EntryPlugin，它做了两件事，一件事是在 compilation 钩子里面，把 EntryDependency 添加到 dependencyFactories 里面去；另一件事是在 make 钩子里面，调用 compilation.addEntry 方法，参数是 entry。这样，在 compilation 对象执行 make 钩子的时候，就会对所有的 entry 使用 addEntry 方法。
- 在 addEntry 里面，首先会把 entry 组装成 entryData，然后调用 addEntry 钩子，最后调用 addModuleTree 方法。在 addModuleTree 方法里面会检查参数，之后调用 handleModuleCreation 方法。在 handleModuleCreation 方法里面首先会初始化 currentProfile，这里面是各种需要记录的时间，然后又调用 factorizeModule 方法，factorizeModule 方法会把这个 entry 加入到 factorizeQueue 这个 AsyncQueue 里面去，它会使用前面的 normalModuleFactory 依次调用 beforeResolve、factorize 钩子，在调用 factorize 钩子的时候又会调用 resolve、afterResolve、createModule 钩子，在这个过程中，它正确解析了 entry 的 url，并且获得了需要加载的 loaders，最后生成了一个 module。生成 module 之后就会回到 compilation 的 handleModuleCreation 方法的回调函数里面，在这里会执行 addModule 方法加入之前生成 module。
- 在 addModule 阶段，首先会把它加到 addModuleQueue 里面去，在里面会调用 \_addModule 方法进行处理，处理完之后回调用 \_handleModuleBuildAndDependencies 方法，在 \_handleModuleBuildAndDependencies 方法里面又会调用 buildModule 方法，buildModule 方法又会把这个 module 放到 buildQueue 里面进行处理，处理的时候会判断是否需要 build，并且执行 needBuild 钩子，然后调用这个 module 的 build 方法，它是一个 normalModule，所以就转到`NormalModule.js`文件里面去，在 build 的时候，会先调用 \_dobuild 方法，在这个方法里面会抽出需要使用的 loaders 然后使用 runLoaders 执行 loaders，然后会回到 build 方法里面执行回调函数，它会先检查 dependencies，然后执行 beforeParse 钩子，调用 parser 的 parse 方法（注意：这里的 parser 是在 plugin 里面初始化的，如果是 js 就使用 js parser 生成 ast，如果是 css 就使用 css parser）完成之后就生成 hash 文件名，并生成 snapshot，最后回到 compilation 的 \_buildModule 方法里面去调用 `this._modulesCache.store` 进行储存。然后回到 \_handleModuleBuildAndDependencies 方法的回调函数，执行 processModuleDependencies 方法，它会把 module 加入到 processDependenciesQueue 里面去进行处理，在这里会给 dependencies 加上 webpack 自己的 require 和 module 相关的代码。这样就处理完了从 entry 进入到生成打包文件的整个流程了。

【2022.1.17】今天加入了 pinia 作为状态管理库和 i18n 作为多语言库。参考[vue3-ssr-template](https://github.com/xmimiex/vue3-ssr-template)

【2022.1.18】今天在精简 server 代码，并且做了一部分业务代码

【2022.1.19】今天在调研 tailwind css 和 windicss，考虑项目的 css 方案，最终接入了 windicss

【2022.1.20】今天在调研 icon 方案，最终使用了 antfu 的 icon 方案。并且在学习windicss的语法，写了一部分博客的业务组件。

【2022.1.21】今天在写[blog-monorepo](https://github.com/sishenhei7/blog-monorepo)

【2022.1.22】今天去爬山了，然后在捣鼓rust。

【2022.1.23】今天在看[rust 权威指南](https://book.douban.com/subject/35081743/)

【2022.1.24】今天写了一部分[blog-monorepo](https://github.com/sishenhei7/blog-monorepo)，然后在看[rust 权威指南](https://book.douban.com/subject/35081743/)

【2022.1.25】今天在给[blog-monorepo](https://github.com/sishenhei7/blog-monorepo)加入api

【2022.1.26】今天在研究env的导入方式

【2022.1.27】今天在修改[blog-monorepo](https://github.com/sishenhei7/blog-monorepo)里面env的导入方式
- vite里面修改环境变量需要重启才能生效