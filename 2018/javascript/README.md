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
    req.open('GET', url);
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
