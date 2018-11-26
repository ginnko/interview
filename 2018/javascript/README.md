1. 几个数组内置函数的考察

    - `['1', '2', '3'].map(parseInt)`

      回调函数一般接受三个参数：value,index,array

      parseInt接受两个参数：str，base

      结果是[1, NaN, NaN]

    - `[[3, 2, 1].reduce(Math.pow), [].reduce(Math.pow)]`

      第一部分同上

      第二部分，空数组调用且没有给初始值会报错。

2. typeof 和 instanceof

    typeof：返回一个表示类型的字符串

    instanceof：这个运算符用来检测第二个参数的`constructor.prototype`是否存在于第一个参数的原型链上

    | typeof    | 结果          |
    | --------- | ----------- |
    | undefined | 'undefined' |
    | null      | 'object'    |
    | Boolean   | 'boolean'   |
    | Number    | 'number'    |
    | String    | 'string'    |
    | Symbol    | 'symbol'    |
    | Function  | 'function'  |
    | Object    | 'object'    |
    | Array     | 'object'    |

5. 数据类型

    - 基本类型：number、string、boolean、undefined、null、symbol
    - 引用类型：一般对象，数组，函数

6. promise

使用promise写一个原生的ajax

```
const myHttpClient = (url) => {
  const promise = new Promise(function(resolve, reject) {
    const req = new XMLHttpRequest();
    req.open('GET', url, true);
    req.responseType = 'json';
    req.setRequestHeader('Accept', 'application/json');
    req.onreadystatechange = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(this.statusText);
      }
    }
    req.send();
  });
  return promise;
};
```
7. 闭包

    从调用栈执行的角度理解闭包，详见这篇[文章](https://tylermcginnis.com/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)

    下面是关键点：

    > Notice that after the makeAddr Execution Context has been popped off the Execution stack, Javascript creates what's called a Closure Scope. Inside of that Closure Scope is the same variable environment that existed in the makeAddr Execution Context.

    > The reason this happend is because that we have a function nested inside of another function. The inner function is nested inside of the makeAddr function, so inner creates Closure over the makeAddr variable environment. Even after the makeAddr Execution Environment has been popped off the Execution Stack, because that Closure Scope was created, inner has access to the X variable.

8. 立即执行函数

    作用：不破坏全局的命名空间。

    写法：括号在里或括号在外均可

    已经可以被块级作用域取代了。

9. async/await
  
  使用async/await写一个ajax请求：

  ```
  const myClient = async function(url) {
    const promise = new Promise((resolve, reject) => {
      const client = new XMLHttpRequest();
      client.open('GET', url);
      client.setRequestHeader('Accept', 'Application/json');
      client.responseType = 'json';
      client.onreadystatechange = function() {
        if (this.readyState !== 4) {
          return;
        }
        if (this.status === 200) {
          resolve(this.response);
        } else {
          reject(this.statusText);
        }
      };
      client.send();
    });

    return await promise;
  };

  // 使用
  myClient('https://api.github.com/repos/ginnko/learn-http').then(res => console.log(res));
  ```

10. 深拷贝

    1. 常用方法：序列化（对Date，function无效）
    2. 借助`structured clone algorithm`
        
        1. 使用history
        2. messageChanel
    3. loadash的deepClone
    4. 自定义

        ```
          function deepClone(obj) {
            let copy;
            // 先处理简单的数据类型
            if (obj == null || typeof obj !== 'object') {
              return obj;
            }
            if (obj instanceof Array) {
              copy = [];
              const len = obj.length;
              for (let i = 0; i < len; i++) {
                copy[i] = deepClone(obj[i]);
              }
              return copy;
            }
            if (obj instanceof Function) {
              return function() {
                return obj.apply(this, arguments);
              }
            }
            if (Object.prototype.toString(obj) === '[object Object]') {
              copy = {};
              const keys = obj.keys(),
                    len = keys.length;
              for (let i = 0; i < len; i++) {
                copy[keys[i]] = deepClone(obj[keys[i]]);
              }
              return copy;
            }
          }
        ```
11. 数组去重
    
    1. Set

      ```
      const arr = [1, 2, 3, 2];
      const result = [...new Set(arr)];
      ```  

    2. filter + indexOf

      ```
      const arr = [1, 2, 3, 2];
      function deleteDuplicate(arr) {
        if (!arr instanceof Array) {
          throw new Error('请输入一个数组');
        }
        return arr.filter((value, index, array) => {
          return array.indexOf(value) === index;
        });
      }
      ```
    3. 数组先排序，再与相邻的比较，如果不相同，存入结果中

12. 正则表达式

    - 详见[此处](./RegExp.md)。

    - 正则实现去除空白

      ```
      function trim(str) {
        let reg = /^\s+|\s+$/;
        return str.replace(reg, '');
      }
      ```

13. js原型

    执行这行代码：`o = new Foo()`实际执行了下面三个步骤：

    1. 创建一个空对象`o = new Object()`
    2. 给对象`o`的内部属性`[[prototype]]`赋值`Foo.prototype`
    3. 执行函数`Foo.call(o)`

14. ES6 class

    详见[阮一峰的教程](http://es6.ruanyifeng.com/#docs/class)。

    这个看过好多遍了，感觉应该没啥问题。

15. JS继承

    详见这篇[文章](https://juejin.im/post/5bcb2e295188255c55472db0)。

    1. 原型链继承

        缺点：多个实例对引用类型的操作会被导致篡改

        示例：

        ```
        const Parent = function() {
          this.colors = ['blue'];
        };

        const Child = function() {

        }

        Child.prototype = new Parent();

        const b = new Child();
        ```
    2. 借用构造函数继承

        缺点：

          1. 只能继承父类实例的属性和方法，不能继承父类原型上的属性和方法
          2. 无法实现复用，每个子类都有父类实例函数的的副本，影响性能

        示例：

        ```
          function Parent() {
            this.name = 'haha';
            this.getName = function() {
              return this.name;
            };
          }

          function Child() {
            this.age = 8;
            Parent.call(this);
          }

          const instance = new Child();
        ```

    3. 组合继承

        组合原型链继承和借用构造函数继承两种方式。

        缺点：实例对象上的属性屏蔽了其原型对象上的同名属性，所以，组合模式的缺点就是在使用子类创建实例的时候，会有两份相同的属性。

        示例：

        ```
          function Parent() {
            this.colors = ['red'];
          }

          Parent.prototype.getColors = function() {
            return this.colors;
          }

          function Child() {
            Parent.call(this);
          }

          Child.prototype = new Parent();
          Child.prototype.constructor = Child;

          const instance = new Child();
        ```

    4. 原型式继承

        缺点：

          1. 原型链继承多个实例的引用类型属性指向相同，存在篡改的可能
          2. 无法传递参数

        示例：

        ```
          function object(obj) {
            function F(){}
            F.prototype = obj;
            return new F();
          }

          var person = {};
          var anotherPerson = object(person);
        
        ```
    5. 寄生式继承

        缺点：同原型式继承

        示例：

        ```
          function createAnother(obj) {
            var clone = object(obj);
            //本质就是装饰者模式
            clone.sayHi = function() {
              console.log('hi');
            };
            return clone;
          }
        ```

    6. 寄生组合式继承

      示例：

      ```
        function inheritPrototype(subType, superType) {
          var prototype = Object.create(superType.prototype);
          prototype.constructor = subType;
          subType.prototype = prototype;
        }

        function SuperType(name) {
          this.name = name;
          this.colors = ['red'];
        }

        Super.prototype.sayName = function() {
          console.log(this.name);
        };

        function SubType(name, age) {
          SuperType.call(this, name);
          this.age = age;
        }

        inheritPrototype(SubType, SuperType);

        SubType.prototype.sayAge = function() {
          console.log(this.age);
        };
      ```

    7. 混入式继承多个对象

      示例：

      ```
        function Myclass() {
          SuperClass.call(this);
          OtherSuperClass.call(this);
        }

        MyClass.prototype = Object.create(SuperClass.prototype);

        Object.assign(MyClass.prototype, OtherSuperClass.prototype);

        MyClass.prototype.constructor = MyClass;

        MyClass.prototype.myMethod = function() {

        };
      ```

    8. Class继承

16. 数组去重排序

    ```
      const arr1 = [1, 25, 2, 26, 1234, 6, 123];
      const arr2 = [2, 6, 2134, 6, 3, 632];
      const c = [...new Set([...arr1, ...arr2])].sort((a, b) => {
        return a - b;
      });
    ```

17. 事件
    
    参考[这里](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Building_blocks/Events#%E5%85%B6%E4%BB%96%E4%BA%8B%E4%BB%B6%E6%A6%82%E5%BF%B5）)。

    1. 事件对象，事件对象 e 的target属性始终是事件刚刚发生的元素的引用。

    2. 阻止默认行为，在事件对象上调用，e.preventDefault();

    3. 捕获和冒泡，在现代浏览器中，默认情况下，所有事件处理程序都在冒泡阶段进行注册。阻止事件冒泡，在事件对象上调用，e.stopPropagation();

    4. addEventListener最后个参数true代表捕获反之代表冒泡

    5. 阻止冒泡不停止父节点捕获

    6. 事件委托

    自定义事件，参考[这里]()。

    ```
      var event = new Event('build');

      // Listen for the event.
      elem.addEventListener('build', function (e) { /* ... */ }, false);

      // Dispatch the event.
      elem.dispatchEvent(event);
    ```

18. 原生DOM节点操控

    参考[这里](https://segmentfault.com/a/1190000009588427)。

    - window.getComputedStyle(dom).width/height 这个得到的是css的设置值，兼容性可以
    - dom.getBoundingClientRect().width/height这个得到的是以边框为基准的值，不取整，兼容性可以
    - dom.offsetWidth/offsetHeight 这个得到的是以边框为基准的值，取整，兼容性可以
    - 获取屏幕的高度和宽度（屏幕分辨率）：window.screen.height/width
    - 获取屏幕工作区域的高度和宽度（去掉状态栏）：window.screen.availHeight/availWidth
    - 网页全文的高度和宽度：document.body.scrollHeight/Width
    - 滚动条卷上去的高度和向右卷的宽度：document.body.scrollTop/scrollLeft(这个在chrome下一直都是0)使用window.scrollX和window.scrollY两个属性代替
    - 网页可见区域的高度和宽度（不加边线）：document.body.clientHeight/clientWidth
    - 网页可见区域的高度和宽度（加边线）：document.body.offsetHeight/offsetWidth

19. JS异步加载

    DOMContentLoaded和OnLoad事件的区别：

    - DOMContentLoaded : 页面(document)已经解析完成，页面中的dom元素已经可用。但是页面中引用的图片、subframe可能还没有加载完。

    - OnLoad：页面的所有资源都加载完毕（包括图片）。浏览器的载入进度在这时才停止。

    加载方法：

    1. 动态创建script

        缺点：这种加载方式在加载执行完之前会阻止 onload 事件的触发，而现在很多页面的代码都在 onload 时还要执行额外的渲染工作等，所以还是会阻塞部分页面的初始化处理

    2. async vs defer

      ![async&defer](./images/defer&async.jpg)

      - defer 和 async 在网络读取（下载）这块儿是一样的，都是异步的（相较于 HTML 解析）
      - 它俩的差别在于脚本下载完之后何时执行，显然 defer 是最接近我们对于应用脚本加载和执行的要求的
      - 关于 defer，此图未尽之处在于它是按照加载顺序执行脚本的，这一点要善加利用
      - async 则是一个乱序执行的主，反正对它来说脚本的加载和执行是紧紧挨着的，所以不管你声明的顺序如何，只要它加载完了就会立刻执行
      - 仔细想想，async 对于应用脚本的用处不大，因为它完全不考虑依赖（哪怕是最低级的顺序执行），不过它对于那些可以不依赖任何脚本或不被任何脚本依赖的脚本来说却是非常合适的，最典型的例子：Google Analytics

    3. 模块加载器

        requirejs

20. 内存泄露

    参考[这里](http://www.ruanyifeng.com/blog/2017/04/memory-leak.html)。

    那些很占空间的值，一旦不再用到，就必须检查是否还存在对它们的引用。如果是的话，就必须手动解除引用。

    定义：程序中已动态分配的堆的内存由于某种原因程序未释放或无法释放引发的各种问题，结果导致程序运行变慢，崩溃，延迟。

    出现的原因：

      1. 全局变量
      2. 定时器未清理
      3. dom元素的引用

21. 模块加载

    - ES6模块加载：ES6模块的设计思想是尽量的静态化，使得在编译的时候，就能确定模块的依赖关系以及输入和输出的变量。ES6的模块不是对象，而是通过export命令显示指定输出的代码，再通过import命令输入。ES6模块输出的是值的引用。
    - CommonJS：CommonJS模块就是对象，在运行时同步加载，用在node端。CommonJS模块输出的是一个值的拷贝。
    - AMD：运行时异步加载，用在web端。

22. let & var & const

    - let：不存在变量提升，变量声明前无法使用；不允许重复声明；拥有块级作用域。
    - const：const实际上保证的，并不是变量的值不得改动，而是 **变量指向的那个内存地址所保存的数据不得改动** 。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量所指向的那个内存地址，因此就等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构内容是不是可变的，就完全不能控制了。

23. 隐式转换

    参考[这里](https://juejin.im/post/5b6906b46fb9a04fcb5b8771)。

24. 执行栈和事件循环

    主要参考下面这三篇文章：

    1. [讲事件循环和任务队列的](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)
    2. [讲执行环境的组成和执行](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)
    3. [从js引擎的角度讲了执行上下文，涉及变量提升、闭包、作用域](https://tylermcginnis.com/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)

    下面简单总结下关于队列的知识：

      1. 任务（Task）：比如像setTimeout、click之类的。在任务和任务之间，可能会发生浏览器重新渲染更新的过程
      2. 微任务(MicroTask)：比如像promise。在两种情况下执行：

          - 在回调函数之后，只要没有其他js代码，也就是说可以在`mid-task`，不可以在`mid-execution`中执行
          - 在每个任务(Task)结束之后也就是调用栈为空的情况下
      3. 几个部件（在第一个参考中可以看到）

          - Task队列
          - Microtask队列
          - js stack：线程
          - 结果

      4. 事件冒泡算在当前的task中
      5. 点击触发的事件，冒泡时stack为空
      6. dom.click()触发的事件，冒泡时stack不为空