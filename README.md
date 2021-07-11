# React-Of-SourceCode

## 拉钩课程第四趴

### 手撸 React 之 源码调试

---

### React 16 架构组成： 
- Scheduler   调度层：调度任务的优先级，高优先级的先进入协调层
- Reconciler  协调层：构建 Fiber 数据结构，比对 Fiber 对象找出差异，记录 Fiber 对象要进行的 DOM 操作
- Renderer    渲染层：负责将发生比变化的部分渲染到页面上

---

### React 版本区别

#### Scheduler 调度层

- React 15 : 循环加递归的方式进行 VirtualDOM 的比对，但是由于递归结束前无法控制停止，如果 VirtualDOM 树的层级较深，则这个比对过程会长期占用 javascript 主线程，而 javascript 又是单线程的，所以无法同时执行其他任务，导致比对过程中无法即时执行元素动画，造成页面卡顿的现象。
- React 16 : 放弃了循环加递归进行 VirtualDOM 比对的方式，采用循环模拟递归。在 window 对象中提供了requestIdleCallback API，它可以在浏览器空闲时间执行任务，但是其自身也有问题，如浏览器的支持性不好、触发频率也不是很稳定、所以 React 放弃了 requestIdleCallback 的使用，自己实现了任务调度库，即 Scheduler，它也可以实现在浏览器空闲时执行任务，而且还可以设置任务的优先级，高优先级的任务先执行，低优先级的任务后执行。

#### Reconciler 协调层

- React 15 : 协调器和渲染器交替执行，即找到差异就直接更新差异。
- React 16 : 协调器和渲染器不再交替执行，协调器负责找出所有差异并为差异打上标记之后，统一交给渲染器进行 DOM 更新。

#### Reconciler 协调层

- 根据协调器为 Fiber 节点打的标记同步执行对应的 DOM 操作。