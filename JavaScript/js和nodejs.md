# nodejs与js的区别

1.  node.js是平台，Js是编程语言。

2.  js是客户端编程语言，需要浏览器的JavaScript解释器进行解释执行（前端 JS）。

3.  node.js是一个基于Chrome JavaScript运行时建立的平台，它是对Google V8引擎进行了封装的运行环境,把浏览器的解释器封装起来作为服务器运行平台，用类似JavaScript的结构语法进行编程，在node.js上运行。是基于服务端的 javascript （后端 Node.js）。

5.  node.js与js语法一样，组成不一样。

## 组成

### JavaScript的组成

-   ECMAScript（语言基础，如：语法、数据类型结构以及一些内置对象）
-   DOM（一些操作页面元素的方法）
-   BOM（一些操作浏览器的方法）

### Node.js的组成

-   ECMAScript（语言基础，如：语法、数据类型结构以及一些内置对象）
-   OS（操作系统）
-   file（文件系统）
-   net（网络系统）
-   database（数据库）

## 事件循环机制

浏览器和Node 环境下，microtask 任务队列的执行时机不同

-   Node端，microtask 在事件循环的各个阶段之间执行
-   浏览器端，microtask 在事件循环的 macrotask 执行完之后执行