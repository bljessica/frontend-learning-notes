# Vue 源码阅读

## 初始化混入

### 定义 `Vue` 的`_init_`方法：

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

#### 挂载根 DOM 元素 `$el` 到 `vm` 上

## 状态混入

由于 `flow` 在直接用 `Object.defineProperty` 声明定义对象时会有问题，因此要在这里构建对象

### 为 `Vue` 添加 `$data` 和 `$props` 属性

+   定义 `data `和 `props` 的 `get()` 方法

+   用 `Object.defineProperty()` 将 `data` 和 `props` 属性和属性描述符添加到 `Vue` 原型

### 为 `Vue` 添加 `$set` 和 `$delete` 属性

### 为 `Vue` 添加 `$watch` 方法

返回值为取消监听（ unwatch ）函数

## 事件混入

### 为 `Vue` 添加 `$on` 方法

## 生命周期混入

### 为 Vue 添加 `_update` 方法（调用钩子函数 `updated()`）

比较之前的 `DOM` 元素和现在的 `DOM` 元素，打补丁（ `_patch_()` ）

### 为 Vue 添加 `$forceUpdate` 方法

让 `Vue` 实例的监听器更新

### 为 Vue 添加 `$destroy` 方法（调用钩子函数 `beforeDestroy()`）

+   调用钩子函数 `beforeDestroy()`
+   将自己从 `$parent` 移除
+   移除监听器
+   移除与 `data` 监听器之间的关联（被 `freeze()` 的函数可能没有监听器）
+   打补丁（ `_patch_()` ）
+   调用钩子函数 `destroyed()`
+   调用 `$off()` 函数，移除自定义事件监听器

## 渲染混入

+   为 `Vue` 添加运行时的帮助参数
+   为 `Vue` 添加 `$nextTick` 方法
    +   将回调函数加入微任务队列
+   为 `Vue` 添加 `_render` 方法
    +   初始化 `$scopedSlots`
    +   调用 `render()` 函数，传入 `vm.$createElement`
    +   设置 `parent`

## 初始化全局 `API`

