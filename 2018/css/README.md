1. BFC

    参考[这里](https://www.cnblogs.com/xiaohuochai/p/5248536.html)

    FC是格式化上下文，它是页面中的一块渲染区域，有一套渲染规则，决定了其子元素如何布局，以及和其他元素之间的关系和作用。

    BFC（Block Formatting Context）块级格式化上下文，是用于布局块级盒子的一块渲染区域，这个区域是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然。

    - 触发条件：
        1. 根元素，HTML元素
        2. float的值不为none
        3. overflow的值不为visible
        4. display的值为inline-block，table-cell，table-caption
        5. position的值为absolute或者fixed

    - BFC的作用：

        1. 阻止元素被浮动元素覆盖

            改变被覆盖元素的属性值，使其成为BFC，阻止浮动元素的覆盖

        2. 包含浮动元素

            改变高度塌陷的父元素盒子的属性值，使其成为BFC，以此来包含子元素浮动盒子
        
        3. 属于同一个BFC的两个相邻块级子元素的上下外边距会发生重叠。所以当两个相邻块级子元素分属于不同的BFC时，可以阻止margin重叠。

2. 盒模型

    分为两种：

    1. box-sizing：content-box。这种盒子的宽度只算自身窗体的大小。
    2. box-sizing：border-box。这种盒子的宽度会算上border、padding、以及自身窗体的大小。

3. 获取元素宽高的方式

    1. dom.style.width/height。这种方式只能获取到dom元素内联样式所设置的宽高。
    2. window.getComputedStyle(dom).width/height。这种方式获取页面渲染完之后的结果，就是说不管哪种方式设置的样式都能获取到且兼容性好。
    3. dom.getBoundingClientRect().width/height。这种方式是根据元素在视窗中的绝对位置来获取宽高的。
    4. dom.offsetWidth/offsetHeight。最常用，兼容性好。

4. css reset & normalize.css

    - 目的：通过重置样式，保持浏览器样式的一致性
    - css reset：几乎为所有标签添加了样式，有大段的继承，是一种暴力的解决方式
    - normalize.css：保持了很多浏览器的样式，保持尽可能的一致，修复了浏览器的显示bug，模块化

5. 水平居中

    1. 内联元素：text-alin：center
    2. 不定宽块状元素：margin-left/margin-right：auto

6. 垂直居中

    1. 内联元素：设置height和line-height两个属性相等
    2. flex布局

        ```
          .center-flex {
            display: flex;
            flex-direction: column;
            justify-content: center;
          }
        ```
    3. 表布局

      ```
        .father {
          display: table;
        }
        .children {
          display: table-cell;
          vertical-align: middle;
          text-align: center;
        }
      ```
7. 垂直水平居中

    1. 定位

        ```
          .father {
            position: relative;
          }
          .child {
            position: absolute;
            left: 50%;
            top: 50%;
            transform: translate(-50% -50%);
          }
        ```
    2. flex

        ```
          display: flex
          justify-content: center;
          align-items: center;
        ```

8. css优先级

    1. 每个选择其都有权值，权值越大优先级越高
    2. 继承的样式优先级低于自身指定的样式
    3. !important优先级最高，js也无法修改
    4. 权值相同，靠近元素的样式优先级高

9. 清除浮动的方法

    1. 通用方法(用在浮动元素的父元素上)：

      ```
        .clear:after {
          content: '';
          display: table;
          clear: both;
        }
      ```
    2. 父级元素成为BFC
        
        1. 父级元素同时浮动
        2. 父级元素设置inline-block
        3. 给父级元素设置overflow

10. 画三角形

    ```
      .triangle {
        width: 0;
        height: 0;
        border: 50px solid transparent;
        border-bottom: 50px solid yellow;
        background: white;
      }
    ```

11. link & @import 导入css

    1. link是标签，除了加载css之外，还能加载其他事务；@import属于css范畴，只能加载css
    2. link引用css时，在页面载入时同时加载；@import需要等页面完全载入后才能加载
    3. link无兼容性问题；@import在低版本浏览器上不适用
    4. link支持使用js控制DOM去改变样式；@import不支持

12. 相对定位提升层级

    position: relative

13. 自适应布局

    参考[这里](http://www.cnblogs.com/xiaohuochai/p/5452865.html)。

14. 实现固定宽高比

    参考[这里](https://blog.csdn.net/Honeymao/article/details/77884744)。

    方式一：padding-top/padding-bottom

    当margin/padding取形式为百分比的值时，无论是left/right，还是top/bottom，都是以父元素的width为参照物的。采用这种方法，需要把容器的height设置为0。而容器内容的所有元素都需要采用position:absolute。

    宽高比4：3

    ```
    .div1{
          position:relative;
          width:50%;
          height:0;
          padding-top: 37.5%;
          background-color: yellow;
    }

    //或者

    .div{
      width:50%;
    }
    .div:after { 
      content: ""; 
      display: block;
      width: 100%;
      background-color: orange;
      padding-top: 30%;
    } 
    ```

