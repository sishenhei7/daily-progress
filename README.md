# daily progress

> 正在看这个项目的那位，没错就是你，你以后绝对会有出息真的

【2021.12.6】今天看了 vue2.x 从 entry 到 init 的过程：

- 它把很多 api 挂载到了 Vue 和 Vue.config 上面。
- Ctor 其实就是 Vue 的构造函数，在合并 config 的时候为了兼容 Vue.extend，做了很多向上查找和合并。
- 稍微理解了 vue 项目的文件组织结构，为了兼容 weex、runtime、非 runtime 等情况，它把 vue 的初始化分成了很多层，每层干特定的事。
- 在 Vue 官方文档上面的 api 那页找到了全局配置和全局 API 对应的初始化代码
- 为后面阅读代码的时候查找相关属性做准备

（接下来看 update 和 hooks）

【2021.12.7】今天仍然在看`_init`的初始化过程，看到了`callHook(vm, 'beforeCreate')`:

- 对于 internal component 和非 internal component 的初始化过程是不同的，internal component 的 `$options` 多了几个属性比如`parent`等，这些属性在初始化的时候扮演了很重要的绝色
- 对于事件，有二种，一种是普通事件，它被加到`vm._events`里面，还有一种 hook 事件，它也被加到`vm._events`里面；但是它在组件调用自身的 hook 事件的时候会被调用，并且这种 hook 事件又和直接写 created、mounted 这种也不同，在调用`callhook`的时候，会首先在`$options`里面查找 created、mounted 方法然后调用，然后在`vm._events`里面查找 hooks 并调用。
- `$emit`方法其实就是调用组件自身`vm._events`里面的方法，而`vm._events`是哪里来的呢？它其实是`_parentListeners`里面拿的，而`opts._parentListeners = vnodeComponentOptions.listeners`又是从 internal component 的父 vnode 里面拿的，而父 vnode 的`vnodeComponentOptions`是什么时候初始化的呢？
- 如果在子组件里面调用`$emit('hook:created')`不会调用自身或者父组件的 created 方法，但是会调用父组件给自己的 created 钩子
- 由于 watcher 是被独立出来，所以和组件其实解耦了，在调用 defineReactive 设置响应式后，只要在取值的时候当前有一个 watcher 就能被观测到达成响应式的目的。由于 watcher 是一个队列，并且有一个当前 watcher 的指针，所以每次只会把当前的 watcher 加到 dep 里面去，那么假设有一个计算属性的 watcher 是当前 watcher，那要怎么触发最开始的 update watcher 呢？其实这是一个链式结构，在数据变动的时候，会触发计算属性的 watcher，导致计算属性重新计算，这个又会导致前一个 watcher 重新计算，最终导致组件 update。那么 watch 属性呢？watch 属性是没有 set 的，那它是怎么触发 update 的呢？（看了下代码，答案是如果在 watch 的方法里面有 data 的赋值操作，那么由这个 data 的响应式来触发 update，这样链式作用下去，直到没有。所以即使计算属性没有被其它地方使用，它没有触发 update watcher，它也会被默默被计算，这是一个缺陷？）

（接下来看后面的 initInjections 等）

【2021.12.8】leetcode 今天是困难题，花了不少时间。所以今天看的比较少。今天在研究响应式原理：

- 在 validateProp 的默认值的时候，为什么要 observe default value（感觉是一个缺陷？）
- 为什么 store 里面的属性能够跨组件？因为不论是 definereactive 或者是 watcher，都是和组件无关的，所以只要这个属性被 definereactive 了，它就能在其它组件加入这个组件里面建立的 watcher。但是前提是在这个 store 里面的属性被求值之前，需要先有一个 watcher，目前只有计算属性能做到这个
- observe 和 defineReactive 的区别，observe 只是一个壳子，相当于一个 traverse 方法，它主要有 2 个作用，第一个是给 value 添加`__ob__`标记，另一个是循环遍历对每个属性施加 observe。另外还有一点需要注意的是，observe 方法没有 key，也就是说不会给这个 key 本身添加响应式，所以 defineReactive 给自己和自己内部的所有属性添加响应式，而 observe 只会给自己内部的所有属性添加响应式。（然后 vue 内部还有一个全局的 shouldObserve 控制要不要给内部属性添加响应式，这个实现了 props 的浅层监听）
- 这里有一个疑问，props 里面的对象，如果在父组件已经对里面的属性设置响应式了的话，那传到子组件之后，在子组件中不是应该能继续触发响应式吗？为什么实际却不能？

（接下来继续看后面的 initState 等）

【2021.12.9】今天在死磕 data 和 props 的初始化：

- 上个问题想了很久还不知道答案，继续想 ing
- 发现在`defineReactive`里面有 2 中 dep，一种是单个属性的 dep，这个 dep 是在定义 getter、setter 函数的时候设置的，它存在于闭包里面；还有一种是子对象或者子数组的 dep，这个 dep 是在 observe 的时候直接加到`__ob__`上面去的，它的作用是什么？目前只看到它会在父属性 setter 里面 depend，然后在$set 的时候 notify，但是想不出它的作用是什么？认真看了一下，应该是在添加和删除新属性的时候 notify，本来以为添加和删除新属性的时候不需要响应式，后来觉得还是有这种场景的。当时这里有一个问题，这里的 dep 和属性的 dep 能不能共用呢？（感觉是可以的，这个应该是缺陷？不过好像即使共用了，性能或者内存都没有什么提升）

（接下来继续看后面的 initState 等）

【2021.12.10】今天在死磕 watcher 和 dep：

- 把 props 的问题想清楚了。props 在被传递给子组件的时候，是关闭了 observing 的，所以在给 props 的属性设置响应式的时候，不会设置内部属性的响应式，但这并不意味着内部属性没有响应式，内部属性有没有响应式取决于它在父组件里面本身被设置了响应式没有。所以父组件把 props 传到子组件，如果这个 props 在父组件里面被设置了响应式的话，那么在子组件里面也会有响应式；如果在父组件里面没有被设置为响应式的话，那么在子组件里面就没有响应式。
- 今天把以前碰到的 2 个不触发响应式的问题的原因想清楚了。
- observe 的第二个参数 asRootData 到底有啥用处？
- 在初始化 data 的时候为什么要 pushTarget 来关闭收收集依赖，正常情况下是不会出现 data 函数里面出现响应式数据的，但是还是有特殊情况的，比如在 data 函数里面调用了具有响应式的 props
- 在初始化 props 的时候关闭了深度监听，只使用 defineReactive 给 `_props` 上面的属性设置了 getter 和 setter，所以 `_props` 没有 `__ob__` 属性；在初始化 data 的时候是直接 observe 的 `_data` 对象，所以 `_data` 对象里面啥都有。
- $data 其实就是 `_data`, $props 其实就是 `_props`
- 以前我一直以为计算属性是初始化一个 watcher + defineReactive 的结合，所以才导致它能被依赖也能依赖别人，现在仔细看了一遍代码，才发现完全是错误的。计算属性其实就是一个 lazy watcher，它在被代理到 vm 上面的时候和 `_data` 或者 `_props` 不同，`_data` 或者 `_props` 是直接代理，而计算属性的这个 watcher 在被代理的时候会判断 dirty 再求值，并且在求值的时候会把自己这个 watcher 推到 watcher 队列的最前面，这就导致在 touch 到其它具有响应式的数据的时候会把这个计算属性 watcher 加到自己的依赖中；由于每个 watcher 自身也收集了依赖于这个 watcher 的 dep，所以在这个计算属性求值结束之后，它对自己调用 depend，就迫使这个 watcher 收集的所有依赖于它的 dep 再次收集依赖，而收集的这个依赖就是依赖于这个计算属性的计算属性，这就实现了计算属性的互相依赖。（其实不是互相依赖，而是在 A 依赖 B 的时候，B 强制让所有依赖它的 dep 也依赖 A 而已）
- 在计算属性初始化的时候有一点需要注意，就是在 ssr 的情境下，计算属性会直接求值，不具备响应式。

（接下来继续看 initWatch 和 initComputed 的不同，以及 dep、subs 里面的具体内容）

【2021.12.11】今天在死磕 dep 和 sub：

- 在 watcher 求值的时候，会在 get 方法里面调用 pushTarget，这样每次建立一个非 lazy watcher 的时候，都会自动把这个 watcher 推到 watcher 队列的最前面。如果是 lazy watcher 呢？lazy watcher 不会在初始化的时候求值，需要手动调用 evaluate 方法求值，所以它在初始化的时候是不会成为别人的依赖的。那么它在什么时候才求值并成为以来的呢？计算属性就是一个 lazy watcher，它在需要被求值的时候，才会成为别人的依赖，然后在别人更新的时候收到通知来更新自己。所以计算属性如果没有被逼迫着求值，那么在整个组件的生命周期里面，它是不会运行的！！！（之前好像理解错了，以为会运行，这里更正心智模型。）
- 一个 dep 的 sub 里面有很多 watcher，然后 dep 按顺序一个个调用这些 watcher 的 notify 方法来提醒它们更新。但是 watcher 真正的更新如果按这个顺序的话可能有问题，因为 dep 里面的 watcher，肯定父组件在前面，子组件在后面，如果按照这个的话，那么是父组件先更新，子组件后更新，可能会有问题。所以有一个 watcher 的更新调度器（当然，性能也是这个调度器建立的另一个原因），这个调度器在更新 watcher 的时候，会按照 id 从小到大的顺序更新，即先更新父组件，再更新子组件。（为什么是这样？？？）
- 在劫持数组方法的时候，只劫持了 observe 的数组的方法
- 在什么时候会依赖`$data.__ob__`？（这是一个 root 级别的 observer）（按理说应该不会依赖它，因为`__ob__`的依赖是通过调用这个响应式属性来更新的，而`_data`并不是 vm 的一个响应式属性。记住，data 的初始化是对`_data`使用 observe，而不是 defineReactive）
- 看完了关于响应式的内容，只觉得所有的响应式机制都不依赖于组件，都是跨组件的，这就是为什么 vuex 的 store 里面的响应式数据能在任意组件里面触发响应式的原因。然后如果 render 的时候如果引用了计算属性，那么计算属性的变更是间接导致重新 render 的，计算属性在 evaluate 之后调用了自己这个 watcher 的 depend 方法，导致所有依赖它的 dep 再重新收集一次依赖，这又导致了触发计算属性变化的改动能够直接通知组件重新 render，并且由于 watcher 的调度，id 小的 watcher 先执行，导致 update 的 watcher 总是最后一个执行，所以组件总是最后一个重新 render。（如果在组件 mounted 之后使用`$watch`方法建立的 watcher 貌似是在 update 的 watcher 之后执行的，不过貌似也没有什么影响。）
- provide 和 inject 的属性也和 props 一样，如果父组件里面的数据没有响应式，那么在子组件里面也没有响应式，如果在父组件里面有响应式，那么在子组件里面就有响应式；它和 props 有一点不同，provide 的属性在改变的时候，是没有响应式的，但是 props 的属性在改变的时候是一定有响应式，为什么呢？我看他们都使用了 defineReactive 了啊？
- 为什么有一个 vm 为 vue 的 watcher ？是怎么建立组件的？（因为最顶层`main.js`里面本来就初始化了一个 vue 实例，它被挂载到每个 vue 实例的 $root 属性上面）

（明天解决上面 2 个疑问，因为没有在`_props`的依赖里面看到 update watcher，所以怀疑是不是父组件造成的子组件更新，而不是 props 的响应式导致子组件更新的）

【2021.12.12】今天在看 vue 实例的渲染和挂载流程：

- 因为设置响应式是在 beforeCreate 之后，所以我们可以在 beforeCreate 里面给 data 加上属性而不需要使用`$set`，这估计就是 beforeCreate 钩子设计的原因：在 vue 设置响应式之前，给用户提供一个处理东西的时机。
- 初始化 watcher 的时候有一个 isRenderWatcher 参数，它会把 update 的 watcher（也叫做 render watcher）加在 \_watcher 属性上，把其它 watcher 放到 \_watchers 里面。
- `vm.$vnode == null`这句是判断什么的？
- hoc 的 vnode 为什么和 parent 的 vnode 是一样的？
- `__patch__`方法会根据平台进行自动注入，如果是在浏览器端，则是平常的 patch，如果是在服务端，则会变成一个空函数，所以这个时候，在服务器端根本没有进行 patch，只是把数据都准备好了，最后等着调用 renderToString 方法。
- directive 和 ref 是怎么工作的？后面需要看一下，还有 transition 组件为啥是“透明”的？为啥“transition”组件需要 vue 内部支持？
- 在 vue 实例调用 update 函数之前，会先调用`_render`函数生成 vnode，`_render`函数其实是一个与平台无关的函数，它负责把用户定义的 render 函数用 createElement 生成一个 vnode，createElement 内部会判断当前的 vue 实例是否是一个子组件，如果不是，则直接 new Vnode，如果是的话，就调用 createComponent 先做一些子组件相关的处理，比如加上子组件的 hooks，然后再 new Vnode 生成 vnode。（那是怎么判断是不是一个子组件的呢？简单来说，就是判断当前要创建的 vnode 的 tag 是不是一个 html tag，如果不是的话，需要去找到这个 tag 的 Ctor，在寻找的时候，会消除驼峰和非驼峰的差异，所以我们在写的时候既能写驼峰也能写非驼峰）
- 为什么在 vue.extend 里面初始化了 initProps 就能 avoids Object.defineProperty calls 了啊？
- inlineTemplate 是什么？
- 顶层 vue 实例的构造函数是 vue，其它都是 vuecomponent，它们的构造函数是 vuecomponent。他们的流程是这样的：首先是在顶层 new Vue 开始构造 Vue 实例，这个 Vue 实例在构造的时候会触发 render watcher，render watcher 调用`_render`方法生成自己的 vnode，此时也会生成这个 vnode 的 children 下的 vnode，由于顶层元素一定是一个平凡的 html 元素，所以顶层的 vnode 使用 new Vnode 的方式生成 Vnode；而其它的 vue 组件都作为 vuecomponent 使用 createComponent 来生成 Vnode，其中会先从 components 里面找到自己的 Ctor，再在 createComponent 里面使用 Vue.extend 进行合并 Ctor。然后 render watcher 调用`_update`方法开始 patch，这里判断如果是一个平凡的 html 的 vnode 就直接使用平台的 dom 方法生成 dom，如果是一个组件，则使用 createComponent 来生成组件 dom，注意`_update`的 createComponent 和`_render`的 createComponent 是两个不同的方法。
- 在使用 createComponent 方法生成组件 dom 的时候，会先调用 vnode 的生命周期 hooks（分为 2 种 hooks，一种是生命周期 hooks，它被储存在`vnode.data.hook`里面，使用它来维护 vnode 生成的 componentInstance 的生命周期，；另一种是 module 的 hooks，它被储存在一个闭包 cbs 里面，它分为平台的 hooks 和 自定义 hooks，使用平台的 hooks 来操作 vnode 相关的 dom 的 class、attrs、events 等等，自定义的 hooks 是和 directive 相关的 hooks），此时会触发生命周期 hooks 的 init hook，这个 hook 会使用 Vnode 的 componentOptions 里面的 Ctor 来生成 vuecomponent 实例，这样就进入了子组件的生命周期。
- vue 实例的 mount 方法可以不传参数，此时会生成一个 dom 片段，然后可以通过`document.body.appendChild(vm.$el)`将它挂载。（参考 elementUI 的 message 组件的实现方式）
- 在`_update`的 createComponent 方法里面可以不用管`vnode.children`，因为 vue 组件是用 slot 来行使 children 的功能的
- isPatchable 的判断逻辑是判断当前`vnode.componentInstance._vnode`是否存在，原因是当前的 vnode 是在父组件里面使用 createElement 建立的 vnode，并不是组件的`_render`生成的 vnode，`vnode.componentInstance`是生成的 vuecomponent 实例，它下面的`_vnode`才是这个 vuecomponent 通过`_render`生成的 vnode。所以如果`vnode.componentInstance._vnode`存在，就表示这个 vnode 所代表的组件是通过`_render`生成的，是可以传给`_update`函数的，即是可以 patch 的。

【2021.12.13】今天在看 vue 实例的建立和更新流程：

- 每个 vm 的`$children`里面的 vueComponent 都是先由 createElement 建立 vnode，这个 vnode 被放在 `$vnode` 上面（也叫`_parentVnode`），然后通过 new Ctor 进行初始化实例（注意这里的 Ctor 是之前生成 creatElement 的时候，先从父组件的 components 里面找到子组件，然后使用 Vue.extend 生成的），这个实例就是前面说的 vueComponent，然后这个实例被放在`$vnode.componentInstance`上面，在 new 的时候，会通过自身的 `_render` 函数再生成一个组件的 vnode，这个 vnode 被放在`_vnode`上面，然后调用`_update`进行挂载，如果是第一次挂载，则给要挂载的 dom 建立一个空的 vnode 作为 oldVnode，然后调用 createElm 建立 dom，建立 dom 的时候，会把之前的那个空的 vnode 的 dom 的父组件作为 parentNode，然后把渲染的 dom 挂载到这个 parentNode 下面，注意这里会整个替换之前的 dom。挂载之前会先创建 children 的 dom，创建 children 的 dom 的时候又会维护一个队列，把创建完并且挂载了的 vnode 送入队列，最后在顶层组件挂载的时候一起从这个队列里面拿出 vnode 触发 mounted 钩子。注意，这个队列里面触发的是子组件的钩子，最顶层的 vue 实例的钩子由它自己的生命周期触发。（注意，如果顶层 vue 实例是第一次挂载，则它本身正常挂载，但是当子组件在初始化的时候，子组件也从`_update`进入到了 patch 函数里面，这个时候，由于没有传 el 进去，所以它的 oldVnode 不存在，此时会让 isInitialPatch 设置为 true，它会使所有子子组件的 insertedVnodeQueue 都放到子组件的 vnode 的 pendingInsert 里面去，而子组件又会把 pendingInsert 的内容放到父组件的 insertedVnodeQueue 里面去，这样一级一级最后把各级的 vnode 都放到顶层 vue 实例的 insertedVnodeQueue 队列里面去，当顶层 vue 实例被挂载之后再一起按顺序触发。）
- patchVnode 里面比较新老 vnode 是否相等有什么意义？好像没有这种场景?

（明天看为什么要 prepatch，prepatch 的作用是什么？）

【2021.12.14】今天在看 vue 实例的更新流程：

- prepatch 的作用是更新 parentVnode 数据，方便组件更新。由于组件是在 vnode 生命周期钩子的 init 里面初始化成 vuecomponent 的，所以在更新的时候，它不会重新使用`_vnode`建立 vuecomponent，只是触发 render watcher 来依次调用`_render`和`_update`进行更新。
- vuecomponent 在因为 render watcher 重新渲染的时候，其实只是想重新渲染 html 节点罢了，它通过双边比较 parentVnode，进行 diff，然后更新 text、或者删除、或者新建一个组件。它只负责本组件的 template，并不负责子组件里面的 template，子组件的 template 通过子组件自己的 render watcher 来触发更新。
- 由于`_props`是响应式的，所以在父组件传给子组件的 props 发生变化的时候，会触发子组件的 render watcher 进行更新，赋值的这一步是在 updateChildComponent 里面发生的，它造触发了组件的更新。但是为什么要把 injection 也设计成浅响应式，貌似没有这种赋值的场景啊？
- beforeUpdate 是在 render watcher 的 before 钩子里面触发的，而 updated 则是在 watcher 的调度里面触发的。由于 watcher 里面有个 vm 属性绑定了 vm 实例，所以可以从 watcher 那里触发 updated 钩子。最后是调度的问题，由于 parent 的 render watcher 的 id 比 子组件的 render watcher 的 id 小，那讲道理应该父组件先触发 updated 生命周期钩子啊？（问题解决，看了下代码，watcher 的调度那里是从队列末尾开始触发的！）

（明天详细看一下 updateChildComponent 方法和 hydrate 方法）

【2021.12.15】今天在看 vue 实例的 hydrate 流程和 createBasicRenderer 的流程：

- 在开始 patch 的时候，由于根 vue 实例的 oldVnode 传入的是一个 dom 元素，此时就回去判断这个 dom 元素上面有没有 data-server-rendered 属性，有的话，就启动 hydrate。由于此时子元素没有 data-server-rendered 属性，所以在根 vue 实例启动 hydrate 的时候，会把 hydrating 一级一级的传下去，让子组件也启动 hydrate。在启动 hydrate 之后，主要做了这几件事：1.判断 dom 和 vnode 是否匹配，如果 vnode 的 tag 是 dom 元素的 tag，则匹配成功；如果 vnode 的 tag 是 vuecomponent，也匹配成功，交给 vuecomponent 自己去匹配；其它情况下匹配失败。2.调用 vnode 的 init 的生命周期来生成一个 vuecomponent 实例（此时会把 dom 和 hydrating，这样就能在这个组件的 patch 阶段也进行 hydrate 了），3.判断所绑定的 dom 有没有子元素和 vnode 有没有子元素，如果 dom 有但是 vnode 没有，则不管；如果 dom 没有但是 vnode 有，则通过这些 vnode 进行生成新的 dom 元素；如果都有，则进入 children 的比较阶段，在 dom 上面使用 firstChild 和 nextSibling 一次获取子元素的 dom，把它和对应的 vnode 进行 hydrate。4.遍历 data 里面的数据，通过调用平台的方法给 vnode 添加 class、attrs、styles 等。5.如果 vnode.tag 不存在，则表明这是一个 text 或 comment 节点，所以判断 dom 和 vnode 的 text 是否一样，不一样就覆盖更新。

（明天继续看 createBasicRenderer 的流程）

【2021.12.16】占个坑，今天加班到 10 点没时间看，后面补上

【2021.12.17】今天在看 createBasicRenderer 的编译过程：

- directive 的服务端是怎么实现的？
- 首先会建立一个 RenderContext ，这个 RenderContext 里面有个 renderStates 和 next，renderStates 是一个先进后出的栈结构（为什么？），里面存着当前渲染的元素的子元素，然后使用 next 方法可以从 renderStates 的末尾拿出一个元素进行渲染。建立完 RenderContext 就在 Vue 的 prototype 上面绑定辅助函数，绑定成功之后就规范组件的 render 函数，注意这里生成的 render 函数和客户端生成的 render 函数是不同的（有哪些不同）。然后根据 render 函数生成 vnode。最后根据生成的 vnode 函数进行渲染，当 renderStates 栈为空的时候，结束渲染，调用 done 方法，把渲染结果传入到用户的回调函数里面去。
- 在带编译器的 vue 库中，vue 会先保存`$mount`方法，然后判断 template，如果 template 是一个 dom 元素，则通过 innerhtml 和 outerhtml 取它的 html 字符串，然后组装 render function 对 template 进行编译，编译之后会生成 render 和 staticRenderFns 函数，然后把他们挂载到 options 上面，最后启动之前保存的`$mount`方法。
- 在生成 render 函数的时候，会先生成一个基础的 compiler，基础 compiler 的工作时编译、优化和生成。然后再这个基础的 compiler 上层做 2 层封装。第一层组装各种 options，第二层显示编译过程中的各种错误和 tips，并且把基础 compiler 生成的 render 和 staticRenderFns 字符串转化为函数，并且加上一个缓存，key 为分隔符 + template ，最后返回转化为函数的 render 和 staticRenderFns。（这里其实就是把一开始生成的 ast 转化为函数，和编译语言的编译很相似，也是把 IR 转化为语言代码。）

（明天继续看编译流程）

【2021.12.18】今天在 vue 的编译过程：

- 理论上来说，data 可以不必强求用一个函数，因为可以在编译阶段使用一个函数包住？
- 为什么 vue 的 template 里面的模板语法可以使用插值，因为 vue 在编译的时候会把这个插值放到一个函数里面去执行。
- vue 的编译流程是，首先生成 ast 树，在生成的时候，从头到尾遍历 template 字符串，并使用正则检测开始节点、结束节点、注释节点等，然后对这些节点生成 ast node，在生成的同时，也会把相应的 data、props、listeners 解析出来，在 ast 节点上储存起来；还会把 v-if、v-for、v-else 解析出来，在 ast 节点上打上标记。然后对 ast 树进行优化，主要是给静态节点打上标记，静态节点有 2 种情况，一种是纯文本 html 节点；另一种是平台保留的 html 节点，并且这个节点的 class、style 属性都是静态的（注意这里还有个回溯操作，就是如果子元素有不是静态的节点，那么这个节点会被改成非静态节点）。除了给静态节点打上标记，还会给静态 root 打上标记，静态 root 是指本身是静态节点，并且子元素不全是纯文本节点的节点。最后根据 ast 生成 render 代码。主要是一层一层遍历 ast 节点，然后判断节点是否是静态节点、v-if 节点、v-once 节点、v-for 节点、slot 节点等等，然后分别对这些节点进行处理，生成 render 函数的代码字符串，最后使用 with 包在一个 function 里面（注意，这里需要提取对应的 data、props、listener、slot 到代码字符串里面去。并且，这里在给静态 root 节点生成代码的时候，会把所有的静态节点代码放到 staticRenderFns 里面去，在 render 函数的代码里面存的是 staticRenderFns 的引用，执行的时候会直接调用 staticRenderFns 里面的代码。）
- 这个编译流程和 ssr 的生成 html 的流程有什么不同？
- 标记的静态节点和静态 root 节点在后面 patch 的时候发挥着什么样的作用？静态节点的标记在后面没有被用到，它是用来帮助标记静态 root 节点的，如果是静态 root 节点，在编译的时候会把它的 render 函数放到 staticRenderFns 里面去，并且缓存起来，以后每次 patch 的时候就直接从这里面拿结果。
- slot 在这整个流程里面是怎么被处理的？现在不使用 slot 统一使用 scopedSlot 了。它的机制是这样的，首先在 parentVnode 里面传递一个 scopedSlot 属性，里面是建立 slot vnode 的函数。然后，在 vue component 的 render 函数里面的 children 字段中使用`$scopedSlots[name]`来调用上面的函数来生成 vnode。（这里要注意判断存在的情况，在编译生成这段代码的时候判断了存不存在的情况的，所以在手写的时候也要判断）
- renderSlot 的第二个参数 fallback 是一个 vnode 数组，所以在 slot 里面可以继续写 vnode，表示如果父级没有传这个 slot 的话，就使用里面的 vnode 作为 fallback。

（明天看 ssr 生成 html 的流程）

【2021.12.19】今天在 vue ssr 生成 html 的过程：

- 由于 vue 的差值是用这种形式`with(this){return ${code}}`包裹的，所以它不仅提供了作用域，而且 code 里面的表达式根本不需要写 this。
- vue ssr 的编译流程和普通编译流程大致相同，它使用同样的方法生成 ast 树，但是在优化和生成阶段有些变化。由于在 ssr 的环境下，vue 是不具备响应式的，所以可以做更多的优化，不仅仅可以把 text 节点转化为字符串，还可以把表达式节点也转化为字符串，更可以把不具有`components/slots/custom directives`的 vue component 节点转化为字符串，vue 根据元素和子元素是否能转化为字符串，给元素打上标记。在生成阶段，vue 根据上一步中打的标记，给节点分成几个 type，比如 RAW、EXPRESSION、INTERPOLATION，然后分别对这几个 type 进行不同的处理；比如对于 RAW 则使用`_ssrNode`包裹，生成一个 StringNode 节点（注意，这里不是 vnode 节点）；如果没有标记，则退化为普通的编译进行处理（注意，由于我们在优化节点并没有标记静态节点，所以在 staticRenderFns 里面应该是没有值的）。由于在优化阶段 vue 既标记了父元素，也标记了子元素，所以会使用上面同样的方法处理子元素，处理完之后把父子元素打平塞到一个 mergedSegments 里面去连接起来。生成完毕之后，就用 witch 包裹把代码到一个 function 里面去作为 render 方法返回。
- 生成 render 函数之后，就先调用 vue 实例的`_render`方法，它会先处理各种依赖，然后处理各种检查，最后把 createElement 传入到 render 函数里面生成 Vnode。我们上面提到过，Vnode 里面有 StringNode，它不是 Vnode，所以 vue 会调用 ssr 专门的 renderNode 方法进行渲染。（普通过程是在这一步执行 patch）
- `$options._scopeId`是从哪儿来的？vue-loader?
- 异步组件在整个编译、渲染、ssr 的流程中是怎么进行的？
- renderNode 方法的流程是这样的，首先判断 StringNode 的情况，如果是的话，就直接渲染它的 open 和 close 属性，这里的 open 指的不是 open tag，而是所有包括 text 的 string；如果这个 StringNode 有 children 的话，就把 children 推入 renderStates 里面去，使用 next 交给 renderStates 对子元素调用 renderNode 进行渲染；然后判断是 vue component 的情况，这里会像一般的渲染流程一样，调用 createComponentInstanceForVnode 建立一个 vue component，然后对它的 render 函数进行规范化，然后使用 `_render` 生成 vnode，最后对生成的 vnode 调用 renderNode 进行渲染（注意，这里有一个 cache 的场景，如果设置了 cache 的话，会在 renderStates 里面操作 cache 进行缓存，并且一步步合到 parent 里面去）；然后判断是 element 即原生 html 元素的情况，这个时候需要考虑 class、attr、style、dirs 等的情况，在 renderStartingTag 里面逐个判断这些场景，一步步加到 starting tag 里面去，然后判断 children 的场景，和前面那一样，移交给 renderStates 进行渲染，最后加上 end tag。然后是判断 comment 的情况，分为两种，一种是普通的 comment，另一种是代表异步组件的 comment，分别进行处理（异步组件的情况下次抽个时间一起看）。最后就是 text 文本节点了，直接使用 escape 之后添加到 write 流里面去即可。这样一步一步直到 renderStates 为空，就调用上层传来的 done 回调，这个 done 回调其实就是 renderToString 里面的 done 回调，它接收 2 个参数，第一个是 null，第二是上面所渲染的 result 即 html。这就是整个 create-basic-renderer 的 renderToString 的流程。（为什么第一个参数是 null，因为这是 node 风格的回调，第一个参数一般是 error，如果成功就是 null，如果失败就是失败的 error）
- 其实一般我们并不使用 create-basic-renderer 里面的 renderToString，而是使用 create-renderer 里面的 renderToString，因为这个里面的 renderToString 做增加了 html 模板的插值处理。
- vue 对 html 模板的编译使用的是 `lodash.template`。vue ssr 使用这个方法把用户提供的模板分成了三部分：head、neck、tail，然后分别渲染，并且在其中注入 css、script 等内容。（注意，vue ssr 还提供了手动注入的功能，它是通过把 TemplateRenderer 的 renderStyles、renderState 等方法挂载到 context 上面去实现的。）

（明天看 TemplateRenderer 的 renderStyles、renderState 等方法的实现）

【2021.12.20】今天在 vue ssr renderToString 和 createBundleRenderer 的相关逻辑：

- TemplateRenderer 的所有注入 renderResourceHints、renderStyles、renderState、renderScripts 基本上都是从 clientManifest 里面和 context 里面取的。其中 clientManifest 是客户端代码打包的时候 VueSSRClientPlugin 生成的，context 是用户在服务端自己定义的。需要注意的是 state 的注入，首先用户在服务端代码里面在路由和 store 准备好了之后，会使用`context.state = store.state`把 store 里面的 state 加到 context 里面，然后在 TemplateRenderer 里面会把这个 state 序列化并注入到`window.__INITIAL_STATE__`里面去（注入的方式是把一个 script 加到 body 里面去，然后在这个 script 里面注入`window.__INITIAL_STATE__`，注入完毕之后通过`document.currentScript||document.scripts[document.scripts.length-1]`获取到当前执行的 script，然后把当前执行的这个 script 删除。），然后在客户端，在挂载应用之前，通过`store.replaceState(window.__INITIAL_STATE__)`把注入的数据填充到 store 里面去，这样应用在挂载的时候就能获取 store 里面的数据了。
- vue-i18n 在 ssr 的时候是怎么注入的？为什么有些没有用到或者其他模块的 i18n 也被注入进来了？
- `const wrapper = NativeModule.wrap(code)`，nodejs 的 module 模块有这个 wrap 方法吗？
- createBundleRenderer 在 vue ssr 中说明了[四大好处](https://ssr.vuejs.org/zh/guide/bundle-renderer.html#%E4%BD%BF%E7%94%A8%E5%9F%BA%E6%9C%AC-ssr-%E7%9A%84%E9%97%AE%E9%A2%98)，我们现在根据 createBundleRenderer 的执行流程来说明这四个好处是怎么实现的。首先，createBundleRenderer 会校验传入的各种参数是否合法，然后创建一个 sourcemapConsumer，当出现错误的时候使用这个 sourcemapConsumer 进行定位（好处 1：内置的 source map 支持）。然后使用之前提到过的带有模板编译功能的 createRenderer 初始化 renderer（好处 4：使用 clientManifest 进行资源注入），并且初始化 bundleRunner。这个 bundleRunner 的原理是，先使用`module.wrap`来包裹代码，并使用 vm.Script 编译代码，然后判断用户是否需要使用 runInNewContext 功能，并在用户定义的作用域里面运行之前编译好的代码，由于之前编译的代码是 webpack 打包之后生成的 commonjs 代码，所以运行的结果是导出了一个模块，然后使用`compiledWrapper.call(m.exports, m.exports, r, m)`把生成的模块挂载到 m.exports 上面，并再次把 userContext 传入给模块并运行，最后返回结果（注意，这里运行的代码是个黑盒子，这个黑盒子经过了 vuessrplugin 和 vue-style-loader 的处理，目前看来它们默默的干了二件事：把注册了的组件塞到 \_registeredComponents 里面去；把 style 塞到 `__VUE_SSR_CONTEXT__` 和 `_styles` 里面去；（好处 3：关键 CSS 注入））。这就是 bundleRunner 的执行原理，但是目前返回的是这个 bundleRunner，它还没有运行，它接收一个 userContext 参数然后返回一个 promise（好处 2：在开发环境甚至部署过程中热重载。因为每次 webpack 热重载会生成新的文件，导致我们在请求服务端的时候服务端重新编译这些文件）。然后我们会在 createBundleRenderer 暴露的 wrapper renderToString api 中运行这个 bundleRunner，它将在 promise 里面返回 app 在 userContext 下的运行时，然后把这个运行时传入之前 createRenderer 初始化的 renderer.renderToString，最后在回调里面返回结果（这也是为什么在 createBundleRenderer 里面的 renderToString 已经与 app 解耦的原因）。
- 上面的热重载功能需要浏览器请求服务器才能重新编译，但是怎么实现在 bundle 变化的时候自动重新编译，然后自动把新的编译文件推送到浏览器端的呢？这个貌似才是真正的热重载，在 createBundleRenderer 里面没有看见关于这个的代码，所以这个是在哪里实现的？

（明天看相关插件 vue-style-loader、vue-loader、VueSSRServerPlugin、VueSSRClientPlugin 的实现）

【2021.12.21】今天在看 vue ssr VueSSRServerPlugin 和 VueSSRClientPlugin 的相关逻辑：

- 突然想到，createBundleRenderer 为什么要用 vm 来执行脚本，直接执行不好吗？vm 是另一个进程能提升效率吗？
- 在组件更新进行 patch 的时候，首先会比较新旧 vnode 是否是 sameVnode，标准就是 key、tag、data 是否一样等等，注意这里如果是 sameVnode 的话，并不是说直接跳过不渲染了，而是说不生成新的元素，然后进入 patchVnode 流程。在 patchVnode 的时候，首先会调用新 vnode 的 prepatch 钩子，在 prepatch 钩子里面，会给 oldVnode 的 componentInstance 实例更新数据（所以这里是跳过了新 vnode 的 componentInstance 的实例化），注意，就是在这一步，才触发了子组件的更新，是怎么触发的呢，主要表现在如下三点：1.更新 `$attrs` 和 `$listeners`，由于我们之前已经把 `$attrs` 和 `$listeners` 设置为响应式了，所以如果在组件 render 的时候用到了这两个变量的话，子组件就会更新（这个功能实现了 hoc 组件）。2.在浅响应式下更新 props，如果在组件 render 的时候用到了这个 props，则子组件也会更新。（值得一说的是，这里会有默认值，对默认值也会执行浅响应式）3.强制更新，如果有插槽发生了变化，或者有动态插槽，就需要强制更新了，通过调用`$forceUpdate`方法进行强制更新。`$forceUpdate`强制更新的原理，只不过是调用了组件的渲染 watcher 的 update 方法而已。

（明天看[webpack 插件机制](https://zhuanlan.zhihu.com/p/94577244)和[tapable 库源码](https://github.com/webpack/tapable)）

【2021.12.22】今天在看 webpack 的插件机制和 VueSSRServerPlugin 和 VueSSRClientPlugin 插件：

- webpack 的插件是为了解决 loader 所不能解决的问题而诞生的，它向开发者提供了 webpack 引擎中完整的能力，开发者可以通过回调函数，将自己的行为引入到 webpack 的构建流程中。一个 webpack 插件就是一个 js 类，这个类需要提供一个 apply 方法，在这个 apply 方法里面开发者通过 [tapable](https://github.com/webpack/tapable) 可以给 webpack 引擎的各个阶段添加自己的回调函数。webpack 引擎主要分为 2 个阶段，第一个是 compiler，它是整个 compile 流程，[这个](https://github.com/webpack/webpack/blob/122db57e7bb0ddb8327b37eeaa0adb9bd5135962/lib/Compiler.js#L125)是在这个阶段开发者能插入的钩子；另一个是 compilation，它是单个文件的 compile 流程，[这个](https://github.com/webpack/webpack/blob/ccecc17c01af96edddb931a76e7a3b21ef2969d8/lib/Compilation.js#L605)是在这个阶段开发者能插入的钩子。可以看到，可以插入的钩子非常非常多。开发者也能插入不同类型的钩子，有同步钩子、异步钩子、promise 钩子、瀑布钩子等等，更多的可以看[这里](https://github.com/webpack/tapable/blob/master/lib/index.js#L8)。这就是 webpack plugin 的整体架构。
- VueSSRServerPlugin 插件又做了什么呢？它其实主要是在 webpack compiler 的 emit 阶段，拦截所有输出，让所有的 js 文件和 sourcemap 文件都输出到`vue-ssr-server-bundle.json`里面去，这个 bundle 里面有 3 个字段，entry 就是 webpack 里面设置的入口文件，files 就是所有输出的 js 文件内容，maps 就是所有的 sourcemap 文件内容。
- VueSSRClientPlugin 插件又做了什么呢？它也主要是在 webpack compiler 的 emit 阶段，统计文件内容信息，输出到`vue-ssr-client-manifest.json`里面去，这个就是之前 create-renderer 需要的 manifest 文件。主要统计如下信息：1.publicPath，即 webpack 里面设置的 publicPath 信息；2.all，所有输出的文件名字；3.initial，所有的入口 js 和 css 文件名字；4.async，所有不在 initial 里面的 js 和 css 文件名字；5.modules，所有的非空 chunks 在 all 里面的 index 映射数组，它结合 `_registeredComponents` 字段可以拿到本页用到的所有的文件的路径，有了这些文件路径就可以用 script 或者 links 标签把需要 async 加载的文件插入到模板 html 里面去。
- 看了下 vue-loader 和 vue-style-loader 源码，放弃了，先做重要的事情：学习 vue3 和源码。

（明天看[vue3](https://v3.cn.vuejs.org/guide/installation.html#%E5%8F%91%E5%B8%83%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)的官方文档）

【2021.12.23】今天在看 vue3 的官方文档：

- 动态参数预期会求出一个字符串， null 例外。这个特殊的 null 值可以用于显式地移除绑定。任何其它非字符串类型的值都将会触发一个警告
- 在 DOM 中使用模板时（直接在一个 HTML 文件里撰写模板），还需要避免使用大写字符来命名键名，因为浏览器会把 attribute 名全部强制转为小写
- 模板表达式都被放在沙盒中，只能访问一个受限的全局变量列表，如 Math 和 Date。你不应该在模板表达式中试图访问用户定义的全局变量
- 但是，这种方法对于可复用组件有潜在的问题，因为它们都共享相同的防抖函数。为了使组件实例彼此独立，可以在生命周期钩子的 created 里添加该防抖函数
- 此时，模板不再是简单的和声明性的。你必须先看一下它，然后才能意识到它执行的计算取决于 author.books。如果要在模板中多次包含此计算，则问题会变得更糟。
- 计算属性将基于它们的响应依赖关系缓存，`Date.now ()`如果声明为计算属性将永远不会更新。
- 当它用于原生 HTML 元素时，is 的值必须以 vue: 开头，才可以被解释为 Vue 组件。这是避免和原生自定义元素混淆
- 这个 prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用。在这种情况下，最好定义一个本地的 data property 并将这个 prop 作为其初始值;这个 prop 以一种原始的值传入且需要进行转换。在这种情况下，最好使用这个 prop 的值来定义一个计算属性
- 当被提供的内容只有默认插槽时，组件的标签才可以被当作插槽的模板来使用。这样我们就可以把 v-slot 直接用在组件上
- 重新创建动态组件的行为通常是非常有用的，但是在这个案例中，我们更希望那些标签的组件实例能够被在它们第一次被创建的时候缓存下来。为了解决这个问题，我们可以用一个`<keep-alive>`元素将其动态组件包裹起来。
- 异步组件在默认情况下是可挂起的。这意味着如果它在父链中有一个 `<Suspense>`，它将被视为该 `<Suspense>` 的异步依赖。在这种情况下，加载状态将由 `<Suspense>` 控制，组件自身的加载、错误、延迟和超时选项都将被忽略。

（明天继续看[vue3](https://v3.cn.vuejs.org/guide/installation.html#%E5%8F%91%E5%B8%83%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)的官方文档）

【2021.12.24】今天在看 vue3 的官方文档：

- 如果要对一个元素进行硬件加速，可以应用以下任何一个 property (并不是需要全部，任意一个就可以)：1.perspective: 1000px。2.backface-visibility: hidden。3.transform: translateZ(0)。4.will-change: transform。
- 请注意，与 props 不同，attrs 和 slots 的 property 是非响应式的。如果你打算根据 attrs 或 slots 的更改应用副作用，那么应该在 onBeforeUpdate 生命周期钩子中执行此操作。
- 为了增加 provide 值和 inject 值之间的响应性，我们可以在 provide 值时使用 ref 或 reactive。现在，如果这两个 property 中有任何更改，MyMarker 组件也将自动更新！
- 当使用响应式 provide / inject 值时，建议尽可能将对响应式 property 的所有修改限制在定义 provide 的组件内部。有时我们需要在注入数据的组件内部更新 inject 的数据。在这种情况下，我们建议 provide 一个方法来负责改变响应式 property。
- 如果要确保通过 provide 传递的数据不会被 inject 的组件更改，我们建议对提供者的 property 使用 readonly。
- 但与生命周期钩子的一个关键区别是，watch() 和 watchEffect() 在 DOM 挂载或更新之前运行副作用，所以当侦听器运行时，模板引用还未被更新。因此，使用模板引用的侦听器应该用 flush: 'post' 选项来定义，这将在 DOM 更新后运行副作用，确保模板引用与 DOM 保持同步，并引用正确的元素。
- 我们的自定义指令现在已经足够灵活，可以支持一些不同的用例。为了使其更具动态性，我们还可以允许修改绑定值。
- 要为某个组件创建一个 VNode，传递给 h 的第一个参数应该是组件本身；如果我们需要通过名称来解析一个组件，那么我们可以调用 resolveComponent。render 函数通常只需要对全局注册的组件使用 resolveComponent。而对于局部注册的却可以跳过。
- 在底层实现里，模板使用 resolveDynamicComponent 来实现 is attribute。如果我们在 render 函数中需要 is 提供的所有灵活性，我们可以使用同样的函数。
- 可以使用 withDirectives 将自定义指令应用于 VNode。
- 诸如 `<keep-alive>、<transition>、<transition-group> 和 <teleport>` 等内置组件默认并没有被全局注册。这使得打包工具可以 tree-shake，因此这些组件只会在被用到的时候被引入构建。
- 函数式组件是自身没有任何状态的组件的另一种形式。它们在渲染过程中不会创建组件实例，并跳过常规的组件生命周期。
- 函数式组件可以像普通组件一样被注册和消费。如果你将一个函数作为第一个参数传入 h，它将会被当作一个函数式组件来对待。
- 要选用将 SFC 作为自定义元素的模式，只需使用 .ce.vue 作为文件拓展名即可
- vue-devtools 是怎么向我们展示那些对于用户不可见的运行时变量的？
- Proxy 的使用确实引入了一个需要注意的新警告：在身份比较方面，被代理对象与原始对象不相等。
- 所有回调都会收到一个 debugger 事件，其中包含了一些依赖相关的信息。推荐在这些回调内放置一个 debugger 语句以调试依赖。

【2021.12.25】今天在看 vue3 和 ssr 的官方文档：

- 要让 TypeScript 正确推断 Vue 组件选项中的类型，需要使用 defineComponent 全局方法定义组件
- 为了告诉 TypeScript 这些新 property，我们可以使用模块扩充 (module augmentation)
- 所有 store 中 state 的变更，都放置在 store 自身的 action 中去管理。这种集中式状态管理能够被更容易地理解哪种类型的变更将会发生，以及它们是如何被触发
- 这种转义是使用原生浏览器 API (例如 textContent) 完成的，因此只有当浏览器本身易受攻击时才会存在漏洞
- 和使用 createApp 创建的、只能在客户端运行的 Vue 应用不同，创建一个服务端渲染应用需要使用 createSSRApp
- Vue Router 提供了懒加载支持，允许 webpack 在此进行代码分离；因此在客户端和服务端我们都需要等待路由器先解析异步路由组件以合理地调用组件内的钩子。为此我们会使用 router.isReady 方法。
- Vue 会断言客户端生成的虚拟 DOM 树与从服务器渲染出来的 DOM 结构是相匹配的。如果不匹配，则它会放弃激活的过程，抛弃已生成的 DOM 并从头开始渲染。这会在浏览器控制台产生一个警告，但网站还是能正常工作。

（接下来开始使用 vue3 开发博客了，抄[bilibili 的专栏页](https://www.bilibili.com/read/home?from=category_17)和[专栏文章页](https://www.bilibili.com/read/cv14487379?from=category_0)），UI 框架使用[Naive UI](https://www.naiveui.com/zh-CN/os-theme)

【2021.12.26】今天在搭 blog ssr 的[框架](./packages/blog-vue3)

- 怎么把 express 中间件转化为 koa 中间件？原理是什么？

【2021.12.27】今天在搭 blog ssr 的[框架](./packages/blog-vue3)

【2021.12.28】今天在搭 blog ssr 的[框架](./packages/blog-vue3)

- eslint 检测 server 的 @ 正常，检测 src 的 @ 报错了，为什么？
- vue3 的插件怎么写？
- 还需要集成 vuex、i18n
- logo 引入和顶部的 undefined 需要解决一下

【2021.12.29】今天在解决 blog ssr 的[框架](./packages/blog-vue3)的问题，终于把框架在生产环境搭起来了。明天完善 server 。

- 多语言的引入要找时间搞个方案

【2021.12.30】今天在优化代码，并且引入了 robots 中间件。晚上花了一点时间写[年终总结（上）](https://github.com/sishenhei7/react-blog/issues/39)

【2021.12.31】今天在看别人的 koa 服务器的中间件代码。然后在跨年画画，写年终总结（下）

- 怎么在库里面避免别人因为按需引入而要加的`declare module 'xxxx.js'声明`

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
