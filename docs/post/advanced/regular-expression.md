# 初学正则表达式

2017 年 03 月 10 日

## 定义

正则表达式是用于 匹配 字符串中字符组合的模式。
在 JavaScript 中，正则是一个对象。

## 创建（2 种方式）

1. 字面量 `/pattern/flags`
2. 调用 RegExp 对象的构造函数 `new RegExp(pattern [,flags])`

flags 取值

```
flags值    =>   描述
g              全局搜索
i              不区分大小写搜索
m              多行搜索
y              执行“粘性”搜索,匹配从目标字符串的当前位置开始，可以使用y标志。
```

## 使用

1. 可被用于 RegExp 的 exec、test 方法

- exec 成功返回一个字符串数组、未找到返回 null
- test 返回 true 或者 false

2. String 对象的 match、replace、search 和 split 方法

- 返回一个数组或者在未匹配到时返回 null。
- 一个在字符串中执行查找匹配的 String 方法，并且使用替换字符串替换掉匹配到的子字符串。
- 一个在字符串中测试匹配的 String 方法，它返回匹配到的位置索引，或者在失败时返回-1。
- 一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中的 String 方法。

## 编写

1. 使用简单模式，如 /abc/仅仅匹配字符串中字符 abc 同时出现并且按照这个顺序。

```javascript
"Hi, do you know your abc's?".match(/abc/g); // ["abc"]
'Grab crab'.match(/abc/g); // null
```

2. 使用特殊字符，如 /ab\*c/匹配一个单独的 a 后面跟了 0 个或者多个 b 且后面跟着 c 的任何字符组合

```javascript
'cbbabbbbcdebc'.match(/ab*c/g); // ["abbbbc"]
```

## 规则

```
  字符    =>      含义
  \              转义。在非特殊字符之前表示，下一个字符是特殊的，匹配一个字符边界。
  ^              匹配输入的开始。
  $              匹配输入的结束。
  *              匹配前一个表达式0次或多次。 等价于 {0,}。
  +              匹配前一个表达式1次或多次。 等价于 {1,}。
  ?              匹配前面一个表达式0次或者1次。
  .              小数点）匹配除换行符之外的任何单个字符。
  (x)            捕获括号。 匹配 ‘x’ 并且记住匹配项。
  (?:x)          非捕获括号。 匹配 ‘x’ 但是不记住匹配项。
  x(?=y)         正向肯定查找。匹配’x’仅仅当’x’后面跟着’y’。但是y不属于匹配结果的一部分。
  x(?!y)         正向否定查找。匹配x仅仅当x后面不跟着y。
  {n}            n是一个正整数，匹配了前面一个字符刚好发生了n次。
  {n,m}          匹配前面的字符至少n次，最多m次。 如果 n 或者 m 的值是0， 这个值被忽略。
  [xyz]          一个字符集合，匹配方括号的中任意字符，包括转义字符。破折号可指定一个字符范围。 对于点（.）和星号（*）这样的特殊符号在一个字符集中没有特殊的意义。他们不必进行转义，不过转义也是起作用的。
  [^xyz]         一个反向字符集。 它匹配任何没有包含在方括号中的字符。你可以使用破折号（-）来指定一个字符范围。任何普通字符在这里都是起作用的。
  [\b]           匹配一个退格(U+0008)。
  \b             匹配一个词的边界。
  \B             匹配一个非单词边界。
  \d             匹配一个数字。 等价于[0-9]。
  \D             匹配一个非数字字符。
  \cX            当X是处于A到Z之间的字符的时候，匹配字符串中的一个控制符。
  \f             匹配一个换页符 (U+000C)。
  \n             匹配一个换行符 (U+000A)。
  \r             匹配一个回车符 (U+000D)。
  \s             匹配一个空白字符，包括空格、制表符、换页符和换行符。
  \S             匹配一个非空白字符。
  \t             匹配一个水平制表符 (U+0009)。
  \v             匹配一个垂直制表符 (U+000B)。
  \w             匹配一个单字字符（字母、数字或者下划线）。 等价于[A-Za-z0-9_]。
  \W             匹配一个非单字字符。
  \n             当 n 是一个正整数，一个返回引用到最后一个与有n插入的正值表达式(counting left parentheses)匹配的副字符串。比如 /apple(,)\sorange\1/ 匹配”apple, orange, cherry, peach.”中的’apple, orange’ 。
  \0             匹配 NULL (U+0000) 字符。 不要在这后面跟其它小数，因为 \0<digits> 是一个八进制转义序列。
  \xhh           与代码 hh 匹配字符（两个十六进制数字）
  \uhhhh         与代码 hhhh 匹配字符（四个十六进制数字）。
  \u{hhhh}       (仅当设置了u标志时) 使用Unicode值hhhh匹配字符 (十六进制数字)。
```

## 实战

key:

```javascript
var str, result1, result2;
str =
  'i am {{ fengyue }}, my email is {{ fengyue@alibaba-inc.com }}, my company is {{ alibaba }}';
// your code
console.log(result1); // ["{{fengyue}}","{{fengyue@alibaba-inc.com}}","{{alibaba}}"]
console.log(result2); //["fengyue", "fengyue@alibaba-inc.com","alibaba"]
```

answer:

```javascript
// 先去掉空格
result1 = str.replace(/\s/g, '').match(/\{\{[^\}]+\}\}/g); //["{{fengyue}}","{{fengyue@alibaba-inc.com}}","{{alibaba}}"]
// 匹配{{和}}之前的字符，并且这些字符的不是}，排除{{}}之间存在{{ 或者 }}
result2 = str.replace(/\s/g, '').match(/([^\{\{\}\}]+)(?=\}\})/g);
// 先去掉花括号，得到除花括号外的字符，在这些字符中选择后面跟着}}的字符
```

key:

```javascript
var myRe = /d(b+)d/g;
var myArray = myRe.exec('cdbbdbsbz'); // ['dbbd','bb'] (x)表示锁定x，所以可以没有d
// 不需要访问正则表达式的属性
var myArray = /d(b+)d/g.exec('cdbbdbsbz');
// 通过一个字符串构建正则表达式
var myRe = new RegExp('d(b+)d', 'g');
var myArray = myRe.exec('cdbbdbsbz');
```

answer:

```javascript
var re = /(\w+)\s(\w+)/;
var str = 'John Smith';
var newstr = str.replace(re, '$2, $1');
console.log(newstr); // "Smith, John"
// 在匹配到的替换文本中，脚本使用替代的$1,$2表示第一个和第二个括号的子字符串匹配。
var re = /\w+\s/g;
var str = 'fee fi fo fum';
var myArray = str.match(re);
console.log(myArray); //  ["fee ", "fi ", "fo "]
```
