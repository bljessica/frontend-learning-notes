# Vue 源码阅读

## 初始化过程

### 1 初始化混入

#### 定义 `Vue` 的`_init_`方法：

#### 初始化 `vm.$options`

#### 初始化生命周期

+   将 `vm` 添加到 `parent` 的 `$children` 中
+   初始化 `$parent`
+   初始化 `$root`
+   初始化 `$children`
+   初始化 `$refs`

#### 初始化 `events`

+   继承父元素的监听事件

#### 初始化 `render`

+   初始化 `$vnode`
+   初始化 `$slots`
+   初始化 `$scopedSlots`
+   初始化 `$createElement()` 
+   为 `vm` 添加响应式属性 `$attrs`（父作用域中不作为 `prop` 被识别且获取的 `attribute` 绑定（除 `class` 和 `style`）） 和 `$listeners` （父作用域中的 (不含 `.native` 修饰器的) `v-on` 事件监听器））
    +   `src/core/observer/index.js		defineReactive()` 用 `Object.defineProperty()` 重写属性的描述符，在 `get()` 中分配依赖收集器 `dep` ，在 `set()` 方法被调用后通知视图更新

#### 调用钩子函数 `beforeCreate()`

#### 初始化 `injections`

+   给 `vm` 添加响应式的注入的属性

#### 初始化 `state`

+   初始化 `props`
    +   校验
    +   为 `vm` 添加响应式属性
+   初始化 `methods`
    +   为 `vm` 添加方法
+   初始化 `data`
    +   为 `vm` 添加属性 (`proxy` 函数，没有额外操作)，为 `data` 添加 `observer`
+   初始化 `computed`
    +   为 `vm` 添加响应式计算属性，为每个计算属性添加一个内部监听器，计算属性的结果进行缓存
+   初始化 `watch`
    +   为每个 `watch` 中的属性添加监听器

#### 初始化 `provide`

#### 调用钩子函数 `created()`

#### 挂载根 `DOM` 元素 `$el` 到 `vm` 上

### 2 状态混入

由于 `flow` 在直接用 `Object.defineProperty` 声明定义对象时会有问题，因此要在这里构建对象

#### 为 `vm` 添加 `$data` 和 `$props` 属性

+   定义 `data `和 `props` 的 `get()` 方法

+   用 `Object.defineProperty()` 将 `data` 和 `props` 属性和属性描述符添加到 `Vue` 原型

#### 为 `vm` 添加 `$set` 和 `$delete` 属性

#### 为 `vm` 添加 `$watch` 方法

返回值为取消监听（ unwatch ）函数

### 3 事件混入

#### 为 `vm` 添加 `$on` 方法

### 4 生命周期混入

#### 为 `vm` 添加 `_update` 方法（调用钩子函数 `updated()`）

比较之前的 `DOM` 元素和现在的 `DOM` 元素，打补丁（ `_patch_()` ）

#### 为 `vm` 添加 `$forceUpdate` 方法

让 `Vue` 实例的监听器更新

#### 为 `vm` 添加 `$destroy` 方法（调用钩子函数 `beforeDestroy()`）

+   调用钩子函数 `beforeDestroy()`
+   将自己从 `$parent` 移除
+   移除监听器
+   移除与 `data` 监听器之间的关联（被 `freeze()` 的函数可能没有监听器）
+   打补丁（ `_patch_()` ）
+   调用钩子函数 `destroyed()`
+   调用 `$off()` 函数，移除自定义事件监听器

### 5 渲染混入

#### 为 `Vue` 添加运行时的帮助参数

#### 为 `vm` 添加 `$nextTick` 方法

+   将回调函数加入微任务队列

#### 为 `vm` 添加 `_render` 方法

+   初始化 `$scopedSlots`
+   调用 `render()` 函数，传入 `vm.$createElement`
+   设置 `parent`

### 6 初始化全局 `API`

+   `Vue.config` ，只有（`get`）方法

+   暴露 'util' 方法（不是全局 `API` ）
+   `Vue.set`（通知依赖收集器）
+   `Vue.delete`（通知依赖收集器）
+   `Vue.nextTick` 
+   `Vue.observable`
+   `Vue.options`
+   `Vue.use`
+   `Vue.mixin`
+   `Vue.extend` (使用基础 Vue 构造器，创建一个“子类”)(继承)
+   注册 `component` 、`assets` 、`filter` 方法
+   `vm.$isServer` 当前实例是否运行于服务器



## 要点

### 1 响应式原理

Vue 的双向绑定原理主要是利用 `Object.defineProperty()` 结合发布/订阅模式实现的。

发布/订阅模式定义了对象的一对多关系，当某个对象的状态发生变化时，所有依赖于他的对象都将获得通知。

Vue 采用 `Object.defineProperty()` 来拦截对象的读写操作，将对象封装为可观察的对象，通过 `get() `进行依赖收集，通过 `set()` 对相关对象进行通知。Dep 是收集依赖的容器，将数据的变化广播给订阅对象，订阅对象从而执行相应的更新。

### 2 虚拟DOM

视图更新过程：

+   创建 vnode（vnode 分为四类：组件类型、标签元素类型、注释类型、文本类型），用 js 来表示 DOM 结构
+   根据虚拟 DOM 树构建出实际 DOM 树
+   当状态发生变更时，重新构造一颗对象树，将新的树和旧的树进行比较，记录差异
+   将差异应用到真正的 DOM 树上(`createEle()` )，视图就更新了（ `updateChildren()` （diff 算法的核心））

patch 函数的逻辑：

+   如果新节点不存在，旧节点存在，则在调用 `invokeDestroyHook()` 函数后直接销毁旧节点。

+   如果新节点存在，旧节点不存在，则调用 `createElm()` 创建新真实DOM节点并插入到正确位置。

+   如果新旧节点基本属性一样且都是VNode类型，则调用 `patchVnode()` 函数进行 patch（使用 `diff` 算法）。

    （主要功能是复用节点，节点是否可以被复用是通过 *sameVnode* 方法判定的）

    +   如果新旧节点一样，则不做比较。

    +   如果新节点是文本节点，继续判断文本是否需要修改，如需修改则调用 `setTextContent()` 完成。

    +   如果新旧节点都有子节点且不相同，则调用 `updateChildren()` 进行子节点对比。(`diff` 算法核心)

        （Vue的 diff 算法在比较节点时，只会进行同级比较，不会跨层比较）

        Vue 采用双端比较算法来进行节点复用，定义了新旧节点头尾四个指针

        +   若 oldStartVnode 与 newStartVnode 为一组可复用节点，则调用 `patchVnode()` 复用旧开始节点，然后将新旧开始节点前移。
        +   若 oldEndVnode 与 newEndVnode 为一组可复用节点，则调用 `patchVnode()` 复用旧结尾节点，然后将新旧结尾节点后移。
        +   若 oldStartVnode 与 newEndVnode 为一组可复用节点，则调用 `patchVnode()` 复用旧开始节点，然后将该真实DOM节点插入到 oldEndVnode 对应DOM节点的后面，最后前移旧开始节点、后移新结尾节点。
        +   若 oldEndVnode 与 newStartVnode 为一组可复用节点，则用 `patchVnode()` 复用旧结尾节点，然后将该真实DOM节点插入到 oldStartVnode 对应DOM节点的前面，最后后移旧结束节点、前移新开始节点。
        +   当经过四轮双端比较，也就是新旧首尾节点都比较之后，如果没有发现可复用的节点，则使用新开始节点与未处理的旧子节点挨个比较，找寻可复用的节点。
        +   当只有新节点数组中还有未处理节点时，则创建新节点并插入到适当位置中；当只有旧节点还有未处理节点时，则删除节点对应的DOM

    +   如果只有新节点有子节点，则创建真实DOM节点添加到子节点位置。

    +   如果只有旧节点有子节点，则直接删除旧节点的子节点。

    +   如果旧节点是文本节点而新节点文本属性为空，则将节点文本内容置空。

+   如果新旧节点基本属性不一样或者旧节点是真实 DOM 类型，根据新 VNode 创建一个真实 DOM 节点，插入到旧节点的位置，将旧节点替换掉。



