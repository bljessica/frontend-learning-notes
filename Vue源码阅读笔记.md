# Vue 源码阅读

## 初始化混入

定义 Vue 的`_init_`方法：

+   初始化生命周期
    +   将 `vm` 添加到 `parent` 的 `$children` 中
    +   初始化 `vm.$parent`
    +   初始化 `vm.$root`
    +   初始化 `vm.$children`
    +   初始化 `vm.$refs`
+    初始化事件 `events`
    +   继承父元素的监听事件
+   初始化渲染函数 `render`
    +   初始化 `vm.$vnode`
    +   初始化 `vm.$slots`
    +   初始化 `vm.$scopedSlots`
    +   初始化 `vm.$createElement()` 
    +   为 `vm` 添加响应式属性 `$attrs`（父作用域中不作为 `prop` 被识别且获取的 `attribute` 绑定，除了 `class` 和 `style`） 和 `$listeners`
+   调用钩子函数 `beforeCreate()`
+   初始化 `injections`
+   初始化 `state (data/props)`
+   初始化 `provide`
+   调用钩子函数 `created()`
+   挂载根元素

## 状态混入

+   定义 data 和 props 的 `get()` 方法
+   用 Object.defineProperty() 将 data 和 props 属性和属性描述符添加到 Vue 原型
+   为Vue 原型添加 $set 和 $delete 属性

