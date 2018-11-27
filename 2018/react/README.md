react的知识主要参考[官网文档](https://reactjs.org/docs/getting-started.html)。

1. 关于React这个前端库的理解

    React是一个库，而不是框架，它存在的意义是从下面几个角度降低前端开发的复杂性：

      1. 封装了DOM操作
      2. 增强了代码的可复用性
      3. 封装了数据更新驱动页面更新的过程
      4. 封装了浏览器的兼容性
      5. 封装了事件处理过程

    感觉上面的几点可以作为前端库出现的意义。

2. 虚拟DOM存在的意义

    1. 根据不同的平台进行相应的抽象，这也是react-dom被抽出的原因
    2. 数据更新时，操作虚拟DOM的效率远高于操作实际DOM节点的效率

3. React中的event对象

    这个对象并不是浏览器提供的，而是React内部构建的。React将浏览器原生的event对象封装了一下，解决了不同浏览器的兼容性问题，对外提供统一的接口和属性。同时，这个event对象是服务W3C标准的，它具有`event.stopPropagation`、`event.preventDefault`这种常用方法。

4. React中的事件绑定

    React调用你传给它的方法时，并不是通过对象方法的方式调用`this.handleClickOnTitle`，而是直接调用函数`handleClickOnTitle`，所以事件监听函数内并不能通过`this`获得实例，这也是为何要绑定`this`的原因。

5. React16生命周期函数

    ![生命周期函数](./images/生命周期函数.png)

    - 首次渲染

      共有四个生命周期函数：

        1. constructor

            禁止在构造函数中调用setState

        2. static getDerivedStateFromProps(nextProps, preState)

            在此处取代了`componentWillMount`。这是一个静态方法，不能在里面使用`this`。`nextProps`表示接收到的新参数，`preState`表示当前的state对象。这个函数在 **挂载**、**接收到props**、**调用setState**、**forceUpdate**时被调用，返回一个对象用来更新当前的state对象，若不需要更新，可返回null。

        3. render

            纯函数
        4. componentDidMount

            组件挂载完成之后调用，此时我们可以获取到DOM节点并操作，比如SVG，Canvas，服务器请求，订阅都可以写在这里，但要注意在`componentWillUnmount`中取消订阅。

            在`componentDidMount`中调用setState会触发一次额外的渲染，多调用了一次render函数，但是用户对此没有感知， **因为它是在浏览器刷新屏幕之前执行的**。但应该在开发中避免使用setState，因为会带来一定的性能问题，应该尽量在constructor中初始化state对象。

    - 更新阶段

      当组件的props改变，或者组件内部调用setState或者forceUpdate发生，就会进入这个阶段。这个阶段涉及的生命周期函数（5个）：

        1. static getDerivedStateFromProps

            取代componentWillReceiveProps

        2. shouldComponentUpdate(nextProps, nextState)

            这个函数返回true表示会触发重新渲染（默认），false表示不会触发重新渲染。

            默认是返回true，也就是说，只要接收到新的属性和调用了setState都会触发重新渲染，这可能会带来一定的性能问题，所以我们需要将`this.props(这个表示上一次的属性)`和`this.state(这个表示上一次的状态)`进行比较来决定是否返回false，来减少重新渲染的次数。

            **这个函数其实算作React的性能优化函数**

        3. render
        4. getSnapshotBeforeUpdate(prevProps, prevState)

            这个方法在render之后，componentDidUpdate之前调用，有两个参数prevProps和prevState，表示之前的属性和之前的state，这个函数有一个返回值，会作为第三个参数传给componentDidUpdate，如果不想要返回值，可以返回null，不写返回值有警告。而且，这个方法要和componentDidUpdate一起使用，否则会有警告。
        5. componentDidUpdate(prevProps, prevState, snapshot) 

            在这个函数里，可以操作DOM，发起服务器请求，还可以setState，但注意一定要使用if语句控制，否则会导致无限循环。

    - 卸载阶段

        - componentWillUnmount

            当组件被卸载就会调用这个函数，可以在这个函数里清除一些定时器，取消网络请求，清理无效的DOM元素等垃圾清理工作。

6. React16之前的调度算法——Stack reconciler

    React运行时存在三种实例：

      1. Elements：JSX
      2. vDOM树：对组件以及DOM节点的抽象表示，维护了组件状态以及组件与DOM树的关系
      3. DOM节点

    在首次渲染过程中构建出vDOM树，后续需要更新（setState）时，比较vDOM树，得到DOM change，再把DOM change应用（patch）到DOM树上。这个diff的过程是自上而下递归的过程（mount/update），无法中断（持续占用主线程）。这样的运行方式导致主线程上的布局、动画等周期性任务以及交互响应就无法立即得到处理，影响体验。

7. React16之后的调度算法——Fiber

    JS在浏览器的主线程上运行，恰好与样式计算、布局以及许多情况下的绘制一起运行，如果 **JS运行时间过长**，就会阻塞这些东西的工作，可能导致掉帧。

    Fiber的目的就是改变这个不可控的过程。

    - Fiber的关键特性

      1. 增量渲染（将渲染任务拆分成块，匀到多帧上）

          每次只做一小段，做完一段就把事件控制权交还给主线程，不像以前长时间占有主线程。
      2. 更新时能够暂停、终止、复用渲染任务
      3. 给不同类型的更新赋予优先级

      虚拟DOM

      4. 操作节点更容易（不用关心浏览器的实际处理过程，只需把下一刻的描述给React）
      5. 用于多种平台

      React实现

      6. reconciler：寻找某时刻前后两版UI的差异，v16之前的叫stack reconciler，之后的叫Fiber reconciler
      7. renderer：插件式的，和平台相关。包括ReactDOM，React Native，React ART。

    - Fiber

        Fiber的思路就是把渲染/更新的过程（递归diff）拆分成一系列小任务，每次检查树上的一小部分，做完看是否还有事件继续下一个任务，有的话继续，没有的话把自己挂起，主线程不忙的时候再继续处理。

        增量渲染更新需要更多的上下文信息，这种需求扩展出了`fiber tree`（Fiber上下文的vDOM tree）。

    - fiber tree

        更新的过程就是根据输入的数据以及现有的fiber tree构造出新的fiber tree（workInProgress tree）。在这个reconciler下，存在下面5类对象：

        DOM层：

        1. 真实的DOM节点

        Instance层：

        2. effect（临时结构，仅在更新时维护）：每个workInProgress tree节点上都有一个effect list，用来存放diff的结果，并向上合并
        3. workInProgress（临时结构，仅在更新时维护）：reconcile过程中，从fiber tree建立其的当前的进度快照，用于断点恢复。
        4. fiber：fiber tree与vDOM tree类似，用来描述增量更新所需的上下文信息，是个单链表结构
        5. Elements：描述UI长什么样子（type， props）

    - Fiber reconciler

      分为两个阶段：

      1. render/reconciliation：通过构造workInProgress tree得出change，这个阶段可以中断

          以fiber tree为蓝本，把每个fiber作为一个工作单元，自上向下逐节点构造workInProgress。

          1. 如果当前节点不需要更新，直接把子节点克隆过来，跳到步骤5，需要更新的话打个tag
          2. 更新当前节点状态（props，state，context等）
          3. 调用shouldComponentUpdate，false的话跳到5
          4. 调用render获得新的节点，并为子节点创建fiber（创建过程会尽量复用现有fiber，子节点的增删也发生在这里）
          5. 如果没有产生child fiber，该工作单元结束。把effect list归并到return，并把当前节点的sibling作为下一个工作单元，否则，把child作为下一个工作单元
          6. 如果没有剩余可用时间了，等到下一次主线程空闲时才开始下一个工作单元；否则立刻开始做
          7. 如果没有下一个工作单元了（回到了workInProgress tree的根节点），这一阶段结束，进入pending commit状态。

          上面1-6是工作循环，7是出口。工作循环每次只做一件事，做完看要不要喘口气。工作循环结束时，workInProgress的根节点上的effect list即使收集到的所有side effect。

          综上，构建workInProgress tree的过程就是diff的过程，通过`requestIdleCallback`这个函数（会在浏览器主线程空闲时期依次调用函数）来调度执行一组任务。没完成一组任务，看看有没有插队的，时间到了就把控制权交还给主线程，知道下一次requestIdleCallback回调再继续构建workInProgress。


      2. commit：这个阶段应用这些DOM change，这个阶段不可以中断，需要一次完成

          处理effect list，包括：更新DOM树、调用组件生命周期函数以及更新ref等内部状态。这个阶段的实际工作量比较大，所以尽量不要在这个阶段的生命周期函数中干重活（`componentDidMount`， `componentDidUpdate`）。

    - 双缓冲技术（fiber tree <=====> workInProgress tree）

        双缓冲指的是workInProgress tree的构建完毕，得到的就是新的fiber tree，然后current指针指向workInProgress，丢弃掉旧的fiber tree。

        优点：

        1. 能够复用内部对象
        2. 节省内存分配、垃圾回收的时间开销

        每个fiber上都有一个alternate属性，也指向一个fiber，创建workInProgress节点时，优先取alternate，没有的话就创建一个新的。

    - 优先级策略（共6种）

      优先级机制存在两个问题：

      1. 生命周期函数的触发顺序和次数没有办法得到保证
      2. 低优先级的被饿死

    - 收集任务

      通过每个节点更新结束时向上归并effect list来收集任务结果，reconciliation结束后，根节点的effect list里的记录包括了DOM change在内的所有side effect。

8. redux内部运行机制

    详见[这里](https://github.com/ginnko/redux-learn/tree/master/diy_redux)。

    随着项目变大，状态变复杂，组件通信要考虑下面两个方面：

    1. 某一个状态需要在多个组件间共享
    2. 组件内的交互需要触发其他组件的状态更新

    使用redux之后，可以将React组件从业务上划分为两类：容器型组件和展示型组件。

    使用redux的异步流：由于redux所有对store状态的变更，都应该通过action触发，异步任务也不例外，而为了不将业务或数据相关的任务混入React组件中，就需要使用其他框架配合管理异步任务流程。

9.  react-redux

    详见[这里](https://github.com/ginnko/redux-learn/tree/master/diy_react_redux)。

    React除了状态提升，没有其他办法解决组件间状态共享的问题。

    redux解决了共享状态以及修改共享状态的问题。

    react-redux将context和store结合起来，这样的话每个组件既可以去context中获取store，从而获取状态，有不用担心它们乱改数据

10. 前端路由

    参考[这里](https://juejin.im/post/5ac61da66fb9a028c71eae1b)。

    前端路由的本质是JS根据url来操作DOM元素。主要有`hash路由`和`history路由`两种实现方式，react-router使用了后一种方法。

    对React而言，路由需要管理组件的生命周期函数，对不同的路由渲染不同的组件。

    - 通常的路由

        1. 一个url对应一个页面
        2. 跳转或链接通过`<a>`标签

    - 前端路由

        1. hash路由

            - window.location用来更改hash

            - window.addEventListener('hashchange', callback)用来监听url的变化

        2. hisroty路由：同步url与其对应的回调函数

            - history.pushState/replaceState 修改url，用于在浏览历史中添加/替换历史记录，但是并不触发跳转，第三个参数url要和当前地址同域，用于在地址栏中显示

            - window.addEventListener('popstate', callback)用来监听前进、后退事件

            - popstate事件：当同一个文档的浏览历史出现变化时，就会触发popstate事件，比如点击浏览器的倒退、前进按钮，js调用back，forward，go函数，上面的pushState以及replaceState不会触发这个事件。

11. redux中间件

    有了中间件后状态的变更变成：

    ```
    view -> action -> middlwware -> reducer -> store 
    ```

    redux使用中间件，解释详见[这里](https://juejin.im/post/59dc7e43f265da4332268906)。

    ```
    let store = createStore(reducer, initState, applyMiddleware(logger));
    ```
    上面的applyMiddleware是一个enhancer，组合storeCreator的高阶函数，返回的是个新的强化过的StoreCreator，再执行storeCreator就能得到一个加强版的store。

    常见的中间件：

      1. redux-logger：提供日志输出
      2. redux-thunk：处理异步操作，action可以是函数，会在`next(action)`之前拦截被处理
      3. redux-promise：处理异步操作，actionCreator的返回值是promise

    redux中间件的洋葱圈模型：`中间件A => 中间件B => 原始dispatch => 中间件B => 中间件A`。

12. 虚拟DOM提高性能的原因

    虚拟DOM相当于在JS和真实DOM之间加了一个缓存，在利用diff算法时，可以避免没必要的DOM操作，从而提高性能。

13. React性能优化

    1. 重写shouldComponentUpdate来避免不必要的dom操作
    2. 使用key来帮助React识别列表中所有子组件的最小变化

14. flux思想

    flux最大的特点就是数据的单向流动

    1. 用户访问view
    2. view发出用户的action
    3. dispatcher收到action，要求store进行相应的更新
    4. store更新后，发出一个change事件
    5. view收到change事件后，更新页面













        

