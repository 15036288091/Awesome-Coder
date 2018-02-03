[![返回目录](https://parg.co/UCb)](https://github.com/wxyyxc1992/Awesome-CheatSheet)

[🔆 中文版本](./JavaScript-CheatSheet.md) | [☀️ English Version](./JavaScript-CheatSheet.en.md)

# 现代 JavaScript 语法速览与实战清单

本文是对于现代 JavaScript 的语法速览与实战清单，从属于 [Awesome CheatSheet](https://github.com/wxyyxc1992/Awesome-CheatSheet) ，它是对某项技术/领域的语法速览与实践备忘清单集锦，包含了 JavaScript，Java，Go，Python，Rust 等常见的编程语言，Web，数据库，信息安全等 [ITCS 知识图谱与技术路线](https://parg.co/bwI)中归档的知识技能点，其致力于提升学习速度与研发效能，即可以将其当做速查手册，也可以作为轻量级的入门学习资料。本文并未区分 ECMAScript 各个版本，而是以目前规范中的，或者常用的能够通过 babel 转换的语法为主要介绍。

本清单参考了 [30 seconds of code](https://github.com/Chalarangelo/30-seconds-of-code)，[JavaScript hacks for ES6 hipsters](https://parg.co/Uuy), [The Definitive JavaScript Handbook](https://parg.co/UZS), [Modern JavaScript Cheatsheet](https://github.com/mbeaudru/modern-js-cheatsheet) 等。

更多 JavaScript 相关学习资料参考笔者的 [Awesome JavaScript Reference](https://github.com/wxyyxc1992/Awesome-Reference#javascript) 与[现代 JavaScript 开发：语法基础与工程实践](https://parg.co/bxN)。

# 基础语法

# 表达式与控制流

## 变量声明与作用域

JavaScript 支持多种变量声明方式:

```

```

JavaScript 中的作用域主要分为全局作用域( Global Scope )与局部作用域( Local Scope )两大类, 在 ES6 之前，JavaScript 中只存在着函数作用域；而在 ES6 中，JavaScript 引入了 let、const 等变量声明关键字与块级作用域，在不同作用域下变量与函数的提升表现也是不一致的。

```js
function outer() {
  let a = 1;
  function inner() {
    let b = 2;
    function innermost() {
      let c = 3;
      console.log(a, b, c); // 1 2 3
    }
    innermost();
    console.log(a, b); // 1 2 — 'c' is not defined
  }
  inner();
  console.log(a); // 1 — 'b' and 'c' are not defined
}
outer();
```

在 JavaScript 中，所谓提升( Hoisting )，即指所有绑定的声明会在控制流到达它们出现的作用域时被初始化；这里的作用域其实就是所谓的执行上下文( Execution Context )，每个执行上下文分为内存分配( Memory Creation Phase )与执行( Execution )这两个阶段。在执行上下文的内存分配阶段会进行变量创建，即开始进入了变量的生命周期；变量的生命周期包含了声明( Declaration phase )、初始化( Initialization phase )与赋值( Assignment phase )过程这三个过程。

```js
{
  /* Original code */
  console.log(i); // undefined
  var i = 10;
  console.log(i); // 10
}

{
  /* Compilation phase */
  var i;
  console.log(i); // undefined
  i = 10;
  console.log(i); // 10
}
// ES6 let & const
{
  console.log(i); // ReferenceError: i is not defined
  const i = 10;
  console.log(i); // 10
}
```

传统的 var 关键字声明的变量允许在声明之前使用，此时该变量被赋值为 undefined；而函数作用域中声明的函数同样可以在声明前使用，其函数体也被提升到了头部。这种特性表现也就是所谓的提升( Hoisting )；虽然在 ES6 中以 let 与 const 关键字声明的变量同样会在作用域头部被初始化，不过这些变量仅允许在实际声明之后使用。在作用域头部与变量实际声明处之间的区域就称为所谓的暂时死域( Temporal Dead Zone )，TDZ 能够避免传统的提升引发的潜在问题。另一方面，由于 ES6 引入了块级作用域，在块级作用域中声明的函数会被提升到该作用域头部，即允许在实际声明前使用；而在部分实现中该函数同时被提升到了所处函数作用域的头部，不过此时被赋值为 undefined。

## 变量赋值与拷贝

严格来讲，JavaScript 中并不存在引用传递，仅存在值传递，而我们常说的所谓 Pass By Reference，实际上传递的也是引用值，更多讲解参考[这里](https://parg.co/bxN)。

```js
// 原始类型直接值拷贝
var a = 2; // 'a' hold a copy of the value 2.
var b = a; // 'b' is always a copy of the value in 'a'
b++; // b = 3

// 复杂类型是引用拷贝
var c = [1, 2, 3];
var d = c; // 'd' is a reference to the shared value
d.push(4); // Mutates the referenced value (object)
console.log(c); // [1,2,3,4]
console.log(d); // [1,2,3,4]
/* Compound values are equal by reference */
var e = [1, 2, 3, 4];
console.log(c === d); // true
console.log(c === e); // false
```

对于浅层对象，我们可以直接进行浅拷贝：

```js
```

对于复杂对象的深层拷贝，可以采用如下方法：

```js
```

# 基本数据类型

JavaScript 内置了 7 种基础数据类型：null, undefined, boolean, number, string, object 以及 symbol.

```js
typeof 0; // number
typeof true; // boolean
typeof "Hello"; // string
typeof Math; // object
typeof null; // object  !!
typeof Symbol("Hi"); // symbol (New ES6)
```

undefined 表示定义缺失，而 null 表示值为空，undefined 常用于未初始化变量的默认值，未传入的函数参数，以及对象的未定义属性。

## 类型判断与变量比较

而在比较运算与加法运算中，都会涉及到将运算符两侧的操作对象转化为原始对象，主要利用了 ToPrimitive 函数或者对象的 [Symbol.toPrimitive] 属性方法。JavaScript 的原始类型转换表如下：

| 原始值           | 转化为数值类型 | 转化为字符串类型  | 转化为 Boolean 类型 |
| ---------------- | -------------- | ----------------- | ------------------- |
| false            | 0              | "false"           | false               |
| true             | 1              | "true"            | true                |
| 0                | 0              | "0"               | false               |
| 1                | 1              | "1"               | true                |
| "0"              | 0              | "0"               | true                |
| "1"              | 1              | "1"               | true                |
| NaN              | NaN            | "NaN"             | false               |
| Infinity         | Infinity       | "Infinity"        | true                |
| -Infinity        | -Infinity      | "-Infinity"       | true                |
| ""               | 0              | ""                | false               |
| "20"             | 20             | "20"              | true                |
| "twenty"         | NaN            | "twenty"          | true                |
| [ ]              | 0              | ""                | true                |
| [20]             | 20             | "20"              | true                |
| [10,20]          | NaN            | "10,20"           | true                |
| ["twenty"]       | NaN            | "twenty"          | true                |
| ["ten","twenty"] | NaN            | "ten,twenty"      | true                |
| function(){}     | NaN            | "function(){}"    | true                |
| { }              | NaN            | "[object Object]" | true                |
| null             | 0              | "null"            | false               |
| undefined        | NaN            | "undefined"       | false               |

常见的容易混淆的表达式，更多比较表格参考 [JavaScript-Equality-Table](http://dorey.github.io/JavaScript-Equality-Table/)：

```js
// 加法优先进行字符串连接，减法优先转化为数值
1 + null = 1
1 + "2" = "12"
"" + 1 + 0 = "10"
"" - 1 + 0 = -1

null + 1 = 1
undefined + 1 = NaN

// 0[object Object]1
{} + [] + {} + [1]

// NaN[object Object]
{} + [1,2] + {} + []

// false，等式两侧存在 NaN，则为 false
NaN == NaN

// true, 先进行 Bool 操作转化为 false，然后两侧都变为数字 0
[] == ![]
```

## Regex: 正则表达式

对于常量正则表达式，可以使用正则字符串方式；而对于动态的正则表达式，可以使用正则表达式构造函数 :

```js
// Regular Expression Literal
const regexLiteral = /cat/;

// Regular Expression Constructor
const regexConstructor = new RegExp("cat");
```

正则表达式可以用来判断元素存在性，用于字符串替换等：

```js
const str1 = "the cat says meow";
const hasCat = /cat/;
hasCat.test(str1);
// true

function removeCc(str) {
  return str.replace(/([A-Z])/g, " $1");
}
removeCc("camelCase"); // 'camel Case'
removeCc("helloWorldItIsMe"); // 'hello World It Is Me'
```

* Symbols

| 符号 | 描述                                                           |
| ---- | -------------------------------------------------------------- |
| .    | (period) Matches any single character, except for line breaks. |
| \*   | Matches the preceding expression 0 or more times.              |
| +    | Matches the preceding expression 1 or more times.              |
| ?    | Preceding expression is optional (Matches 0 or 1 times).       |
| ^    | Matches the beginning of the string.                           |
| $    | Matches the end of the string.                                 |

* Character groups

| 符号   | 描述                                                                                                                      |
| ------ | ------------------------------------------------------------------------------------------------------------------------- |
| \d     | Matches any single digit character.                                                                                       |
| \w     | Matches any word character (alphanumeric & underscore).                                                                   |
| [XYZ]  | Character Set: Matches any single character from the character within the brackets. You can also do a range such as [A-Z] |
| [XYZ]+ | Matches one or more of any of the characters in the set.                                                                  |
| [^a-z] | Inside a character set, the ^ is used for negation. In this example, match anything that is NOT an uppercase letter.      |

* Flags: There are five optional flags. They can be used separately or together and are placed after the closing slash. Example: /[A-Z]/g I’ll only be introducing 2 here.

| 符号 | 描述                    |
| ---- | ----------------------- |
| g    | Global search           |
| i    | case insensitive search |

* Advanced

| 符号   | 描述                                                                      |
| ------ | ------------------------------------------------------------------------- |
| (x)    | Capturing Parenthesis: Matches x and remembers it so we can use it later. |
| (?:x)  | Non-capturing Parenthesis: Matches x and does not remembers it.           |
| x(?=y) | Lookahead: Matches x only if it is followed by y.                         |

# 集合类型

## Array: 数组

```js
const uniqueArray = arr => [...new Set(arr)];

uniqueArray([1, 2, 2, 3, 4, 4, 5]);
// [1,2,3,4,5]
```

### Array Like

### Transform: 变换

`reduce()` 函数能够将某个函数作用于数组中的每个  元素，从而将多个值转换为单个值；其典型的用法为计算数组和值，或者进行数组扁平化：

```js
// 指定初始值
let result = arr.reduce(callback, initValue);

// 计算数组和值
let sum = arr.reduce((acc, val) => {
  return acc + val;
});

// 使用 reduce 进行数组扁平化
const flatten = arr => arr.reduce((a, v) => a.concat(v), []);
// flatten([1,[2],3,4]) -> [1,2,3,4]

// 深度扁平化
const flattenDepth = (arr, depth = 1) =>
  depth != 1
    ? arr.reduce(
        (a, v) => a.concat(Array.isArray(v) ? flattenDepth(v, depth - 1) : v),
        []
      )
    : arr.reduce((a, v) => a.concat(v), []);
// flattenDepth([1,[2],[[[3],4],5]], 2) -> [1,2,[3],4,5]
```

# 函数

## Definition: 函数定义

## 参数

ES6 中引入了所谓的默认参数 :

```js
// 传统的默认参数编写方式
function filterEvil(array, evil) {
  evil = evil || "darth vader";
  return array.filter(item => item !== evil);
}

// ES6 默认参数
function filterEvil(array, evil = "darth vader") {
  return array.filter(item => item !== evil);
}

// 默认参数可以用来进行必要参数检测
const isRequired = () => {
  throw new Error("param is required");
};

function filterEvil(array, evil = isRequired()) {
  return array.filter(item => item !== evil);
}
```

## Call: 函数调用

可以使用 apply 来连接两个数组：

```js
let countries = ["Moldova", "Ukraine"];
let otherCountries = ["USA", "Japan"];
countries.push.apply(countries, otherCountries);
console.log(countries); // => ['Moldova', 'Ukraine', 'USA', 'Japan']
```

较为全面的 JavaScript 中函数调用方式列举如下：

```js
console.log(1);
(_ => console.log(2))();
eval('console.log(3);');
console.log.call(null, 4);
console.log.apply(null, [5]);
new Function('console.log(6)')();
Reflect.apply(console.log, null, [7])
Reflect.construct(function(){console.log(8)}, []);
Function.prototype.apply.call(console.log, null, [9]);
Function.prototype.call.call(console.log, null, 10);
new (require('vm').Script)('console.log(11)‘).runInThisContext();
```

# 类与对象

# 其他

## ES6 Module: 模块

ES2015 Modules 中主要的关键字就是 `import` 与 `export`，前者负责导入模块而后者负责导出模块。完整的导出语法如下所示：

```js
// default exports
export default 42;
export default {};
export default [];
export default foo;
export default function () {}
export default class {}
export default function foo () {}
export default class foo {}

// variables exports
export var foo = 1;
export var foo = function () {};
export var bar; // lazy initialization
export let foo = 2;
export let bar; // lazy initialization
export const foo = 3;
export function foo () {}
export class foo {}

// named exports
export {foo};
export {foo, bar};
export {foo as bar};
export {foo as default};
export {foo as default, bar};

// exports from
export * from "foo";
export {foo} from "foo";
export {foo, bar} from "foo";
export {foo as bar} from "foo";
export {foo as default} from "foo";
export {foo as default, bar} from "foo";
export {default} from "foo";
export {default as foo} from "foo";
```

相对应的完整的支持的导入方式如下所示：

```js
// default imports
import foo from "foo";
import {default as foo} from "foo";

// named imports
import {bar} from "foo";
import {bar, baz} from "foo";
import {bar as baz} from "foo";
import {bar as baz, xyz} from "foo";

// glob imports
import * as foo from "foo";

// mixing imports
import foo, {baz as xyz} from "foo";
import * as bar, {baz as xyz} from "foo";
import foo, * as bar, {baz as xyz} from "foo";
```

## Error Handling: 异常处理

```js
try {
  let hello = prompt("Type hello");
  if (hello !== "hello") {
    throw new Error("Oops, you didn't type hello");
  }
} catch (e) {
  alert(e.message);
} finally {
  alert("thanks for playing!");
}
```
