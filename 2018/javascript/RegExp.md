## 涉及正则表达式的常用函数

- 概况

  - 定义在RegExp对象上的
    1. [exec](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)
    2. [test](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test)

  - 定义在String对象上的
    1. [match](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)
    2. [search](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/search)
    3. [replace](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)
    4. [split](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split)

  - [在线网站](https://regexr.com/)

- 详细介绍
  1. exec()
    语法： `regexObj.exec(str)`
    返回值： 
      - 匹配成功： 返回一个数组
      - 匹配失败： 返回null
    实例：
      ````
      var re = /quick\s(brown).+?(jumps)/ig;
      var result = re.exec('The Quick Brown Fox Jumps Over The Lazy Dog');

      ````
    结果：
      - result的元素：

          - `[0]:'Quick Brown Fox Jumps'`
          - `[1]: 'Brown'`
          - `[2]:'Jumps'`

      - result的属性：

          - `index: 4-首次索引值`
          - `input: 'The Quick Brown Fox Jumps Over The Lazy Dog'-原始字符串`

      - re的属性

          - `lastIndex: 25-下次匹配的起始位置`

            ```
            //正则表达式不能放在while循环判断中，因为每次迭代都会重置lastIndex，导致死循环

            var myRe = /ab*/g;
            var str = 'abbcdefabh';
            var myArray;
            while ((myArray = myRe.exec(str)) !== null) {
              var msg = 'Found ' + myArray[0] + '. ';
              msg += 'Next match starts at ' + myRe.lastIndex;
              console.log(msg);
            }
            ```

  2. test()
    语法: `regexObj.test(str)`
    返回值: 如果正则表达式和指定的字符串匹配,返回true,否则返回false
    实例: 
      ```
      let str = 'hello world!';
      let result = /^hello/.test(str);
      console.log(result); 
      // true

      ```
    lastIndex属性的使用:
      如果正则表达式设置了全局标志,test()的执行会改变正则表达式lastIndex的属性
      ```
      var regex = /foo/g;

      // regex.lastIndex is at 0
      regex.test('foo'); // true

      // regex.lastIndex is now at 3
      regex.test('foo'); // false

      ```
  3. match()
    语法:`str.match(regexp);`
    返回值:
      - 匹配成功: 返回一个数组,数组的第一项是进行匹配完整的字符串,之后的项是圆括号捕获的结果
      - 匹配失败: 返回null
    实例:
      - 正则表达式不使用g标志,str.match()会返回和RegExp.exec()相同的结果.返回的数组具有index和input属性.

      ```
      var str = 'For more information, see Chapter 3.4.5.1';
      var re = /see (chapter \d+(\.\d)*)/i;
      var found = str.match(re);

      console.log(found);

      // logs [ 'see Chapter 3.4.5.1',
      //        'Chapter 3.4.5.1',
      //        '.1',
      //        index: 22,
      //        input: 'For more information, see Chapter 3.4.5.1' ]

      // 'see Chapter 3.4.5.1' 是整个匹配。
      // 'Chapter 3.4.5.1' 被'(chapter \d+(\.\d)*)'捕获。
      // '.1' 是被'(\.\d)'捕获的最后一个值。
      // 'index' 属性(22) 是整个匹配从零开始的索引。
      // 'input' 属性是被解析的原始字符串。

      ```

      - 正则表达式使用g标志,str.match()返回所有匹配项
      ```
      var str = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz';
      var regexp = /[A-E]/gi;
      var matches_array = str.match(regexp);

      console.log(matches_array);
      // ['A', 'B', 'C', 'D', 'E', 'a', 'b', 'c', 'd', 'e']

      ```
  4. search()
    语法:`str.search(regexp)`
    返回值:
      - 匹配成功: search()返回正则表达式在字符串中首次匹配项的索引.
      - 匹配失败: 返回-1
    实例:
      ```
        function testinput(re, str){
        var midstring;
        if (str.search(re) != -1){
          midstring = " contains ";
        } else {
          midstring = " does not contain ";
        }
        console.log (str + midstring + re);
      }

      ```
  5. replace()
    语法: `str.replace(regexp|substr, newSubStr|function)`
    描述: 
      - 由regexp匹配到的内容会被第二个参数的返回值替换掉
      - newSubStr中可以使用特殊的变量名, 比如$n
      - function中也有指定的参数,比如pn
    返回值:
      - 替代后的新字符串(原字符串不改变)
    实例:
      - 一个普通的替换
        ```
          var str = 'Twas the night before Xmas...';
          var newstr = str.replace(/xmas/i, 'Christmas');
          console.log(newstr);  // Twas the night before Christmas...
        
        ```
      - 第二个参数是字符串使用特殊变量名
        ```
          var re = /(\w+)\s(\w+)/;
          var str = "John Smith";
          var newstr = str.replace(re, "$2, $1");
          // Smith, John
          console.log(newstr);
        
        ```
      
      - 第二个参数是函数使用指定参数
        ```
        function replacer(match, p1, p2, p3, offset, string) {
          // p1 is nondigits, p2 digits, and p3 non-alphanumerics
          return [p1, p2, p3].join(' - ');
        }
        var newString = 'abc12345#$*%'.replace(/([^\d]*)(\d*)([^\w]*)/, replacer);
        console.log(newString);  // abc - 12345 - #$*%

        //替代循环
        //'x' 产生一个 'on' 状态，'-'（连接符）产生一个 'off' 状态，而 '_' （下划线）表示 'on' 状态的长度。

        var str = 'x-x_';
        var retArr = [];
        str.replace(/(x_*)|(-)/g, function(match, p1, p2) {
          if (p1) { retArr.push({ on: true, length: p1.length }); }
          if (p2) { retArr.push({ on: false, length: 1 }); }
        });

        console.log(retArr);

        //结果:
        [
          { on: true, length: 1 },
          { on: false, length: 1 },
          { on: true, length: 2 }
        ]

        ```
  
  6. split()
    语法: `str.split([separator])`  这里separator使用一个正则表达式
    返回值:
      - 正常情况下: 分隔符分割的字符组成的数组
      - 空字符串: 具有一个空字符串元素的数组
      - 空字符串,separator也是空字符串: 空数组
    实例:
      - 不使用捕获括号
        ```
          var names = "Harry Trump ;Fred Barney; Helen Rigby ; Bill Abel ;Chris Hand ";

          console.log(names);

          var re = /\s*;\s*/;
          var nameList = names.split(re);

          console.log(nameList);
        
         ```
      - 使用捕获括号
        ```
          var myString = "Hello 1 word. Sentence number 2.";
          var splits = myString.split(/(\d)/);

          console.log(splits);

          //[ "Hello ", "1", " word. Sentence number ", "2", "." ]

        ```
