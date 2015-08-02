# ECMAScript 6 <sup>[git.io/es6features](http://git.io/es6features)</sup>

## 介绍
ECMAScript 6, 又被称为 ECMAScript 2015, 是最新版的ECMAScript规范.  ES6 是对ECMAScript的一次重要升级, 对该语言的第一次升级是ES5，已经在2009发布. 主要的JavaScript引擎对ES6的实现[正在进行中](http://kangax.github.io/es5-compat-table/es6/)。

在此可查看[ES6 standard](http://www.ecma-international.org/ecma-262/6.0/) 规范的完整内容。

ES6包括如下新的语言特性:

- [arrows](#arrows)
- [classes](#classes)
- [enhanced object literals](#enhanced-object-literals)
- [template strings](#template-strings)
- [destructuring](#destructuring)
- [default + rest + spread](#default--rest--spread)
- [let + const](#let--const)
- [iterators + for..of](#iterators--forof)
- [generators](#generators)
- [unicode](#unicode)
- [modules](#modules)
- [module loaders](#module-loaders)
- [map + set + weakmap + weakset](#map--set--weakmap--weakset)
- [proxies](#proxies)
- [symbols](#symbols)
- [subclassable built-ins](#subclassable-built-ins)
- [promises](#promises)
- [math + number + string + array + object APIs](#math--number--string--array--object-apis)
- [binary and octal literals](#binary-and-octal-literals)
- [reflect api](#reflect-api)
- [tail calls](#tail-calls)

## ECMAScript 6 特性

### Arrows
Arrows 是 function 的缩写形式， 使用语法 `=>`。  在语法上，这个特性与 C#，Java 8，CoffeeScript 中的相关特性类似。他支持语句体，同时也支持也支持有返回值的表达式。  与function不同，arrows 与它的外围代码共享相同的关键词 `this`.

```JavaScript
// 表达式
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);
var pairs = evens.map(v => ({even: v, odd: v + 1}));

// 语句体
nums.forEach(v => {
  if (v % 5 === 0)
    fives.push(v);
});

// 关键词 this
var bob = {
  _name: "Bob",
  _friends: [],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + " knows " + f));
  }
}
```

### Classes
ES6 classes 是一种简单的语法糖，基于原型式面向对象模式。它提供了一种单一方便的声明形式，
让类的模式容易使用，且增强了可交互性。Classes 支持 原型式的继承方式、super关键词、实例和静态方法、构造器。

```JavaScript
class SkinnedMesh extends THREE.Mesh {
  constructor(geometry, materials) {
    super(geometry, materials);

    this.idMatrix = SkinnedMesh.defaultMatrix();
    this.bones = [];
    this.boneMatrices = [];
    //...
  }
  update(camera) {
    //...
    super.update();
  }
  get boneCount() {
    return this.bones.length;
  }
  set matrixType(matrixType) {
    this.idMatrix = SkinnedMesh[matrixType]();
  }
  static defaultMatrix() {
    return new THREE.Matrix4();
  }
}
```

### Enhanced Object Literals

对象字面量被扩展了，支持在定义时设置原型，`foo: foo`简写，方法定义，调用父类方法，使用表达式计算属性的名称。基于这些，对象字面量与类的定义更加紧密，同时，让面向对象设计更加方便。

```JavaScript
var obj = {
    // __proto__
    __proto__: theProtoObj,
    // Shorthand for ‘handler: handler’
    handler,
    // Methods
    toString() {
     // Super calls
     return "d " + super.toString();
    },
    // Computed (dynamic) property names
    [ 'prop_' + (() => 42)() ]: 42
};
```

### Template Strings
Template strings provide syntactic sugar for constructing strings.  This is similar to string interpolation features in Perl, Python and more.  Optionally, a tag can be added to allow the string construction to be customized, avoiding injection attacks or constructing higher level data structures from string contents.

模板字符串提供了构造字符串的语法糖。这个特性类似于Perl、Python以及其他语言中的字符串插值的特性。同时，可以使用标签来自定义字符串的构建，通过此方法来避免注入攻击，或者基于字符串构建更高级的数据结构。

```JavaScript
// Basic literal string creation
`In JavaScript '\n' is a line-feed.`

// Multiline strings
`In JavaScript this is
 not legal.`

// String interpolation
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`

// Construct an HTTP request prefix is used to interpret the replacements and construction
GET`http://foo.org/bar?a=${a}&b=${b}
    Content-Type: application/json
    X-Credentials: ${credentials}
    { "foo": ${foo},
      "bar": ${bar}}`(myOnReadyStateChangeHandler);
```

### Destructuring

解构允许使用模式匹配的方式绑定变量，可以用于匹配数组和对象。解构是软失败的，类似于标准的对象属性查找 `foo["bar"]`，bar属性不存在时会返回 `undefined`

```JavaScript
// list matching
var [a, , b] = [1,2,3];

// object matching
var { op: a, lhs: { op: b }, rhs: c }
       = getASTNode()

// object matching shorthand
// binds `op`, `lhs` and `rhs` in scope
var {op, lhs, rhs} = getASTNode()

// Can be used in parameter position
function g({name: x}) {
  console.log(x);
}
g({name: 5})

// Fail-soft destructuring
var [a] = [];
a === undefined;

// Fail-soft destructuring with defaults
var [a = 1] = [];
a === 1;
```

### Default + Rest + Spread
函数定义时可以指定参数默认值. 调用函数时把数组作为连续的参数传入. 把参数列表后面的参数作为一个数组.  此方法可用于替换 `arguments`，并且直接解决常见的情况.

```JavaScript
function f(x, y=12) {
  // 如果没传入y（或者y的值为undefined），y等于12
  return x + y;
}
f(3) == 15
```
```JavaScript
function f(x, ...y) {
  // y 是数组
  return x * y.length;
}
f(3, "hello", true) == 6
```
```JavaScript
function f(x, y, z) {
  return x + y + z;
}
// 数组中的元素一次作为函数参数
f(...[1,2,3]) == 6
```

### Let + Const
块作用的绑定构建.  `let` 是一个新的 `var`，支持块作用域.  `const` 只允许赋值一次。 静态限制，避免变量在声明前被使用.


```JavaScript
function f() {
  {
    let x;
    {
      // 正确，定义在当前块作用域内
      const x = "sneaky";
      // 错误, x是常量
      x = "foo";
    }
    // 错误，当前作用域内已经定义过
    let x = "inner";
  }
}
```

### Iterators + For..Of

枚举对象提供了类似于CLR中的IEnumerable，Java中的Iterator的自定义枚举方式。区别于 `for..in`，使用 `for..of` 实现自基于枚举对象的自定义枚举。不需要想LINQ一样，实现一个数组，使用lazy设计模式。


```JavaScript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur }
      }
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
```

枚举基于”鸭子类型“的接口 (使用 [TypeScript](http://typescriptlang.org) 类型语法，仅用于展示):

```TypeScript
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```

### Generators

生成器使用 `function*` 和 `yield` 来简化迭代器的构建。以`function*`方式声明的函数返回一个生成器实例。生成器是迭代器的子类，包含额外的`next`和`throw`方法。这允许变量可以流回到生成器中，`yield`是一个表达式形式，用于返回一个值（或抛出异常）。

注意：生成器也可用于 'await'形成的同步变成，参考ES7 `await` 提议。

```JavaScript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
```

生成器接口是 (使用 [TypeScript](http://typescriptlang.org)语法，仅用于展示):

```TypeScript
interface Generator extends Iterator {
    next(value?: any): IteratorResult;
    throw(exception: any);
}
```

### Unicode

非破坏性的完整支持Unicode，包括字符串中新的Unicode字面量形式、新的正则表达式模式`u`，以及用于处理字符串在21位编码的码位级的Api. 这些扩展让JavaScript可用于创建全球性的应用。


```JavaScript
// same as ES5.1
"𠮷".length == 2

// new RegExp behaviour, opt-in ‘u’
"𠮷".match(/./u)[0].length == 2

// new form
"\u{20BB7}"=="𠮷"=="\uD842\uDFB7"

// new String ops
"𠮷".codePointAt(0) == 0x20BB7

// for-of iterates code points
for(var c of "𠮷") {
  console.log(c);
}
```

### Modules

在语言层面支持模块，用于组件定义。模式来源于著名的Javascript模块加载器（AMD, CommonJS）。运行时的行为由宿主环境的默认加载器决定。隐式异步模型 - 代码知道加载的模块完成加载且处理后才会执行。

Language-level support for modules for component definition.  Codifies patterns from popular JavaScript module loaders (AMD, CommonJS). Runtime behaviour defined by a host-defined default loader.  Implicitly async model – no code executes until requested modules are available and processed.

```JavaScript
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
```
```JavaScript
// app.js
import * as math from "lib/math";
alert("2π = " + math.sum(math.pi, math.pi));
```
```JavaScript
// otherApp.js
import {sum, pi} from "lib/math";
alert("2π = " + sum(pi, pi));
```

额外的特性包括： `export default` 和 `export *`:

```JavaScript
// lib/mathplusplus.js
export * from "lib/math";
export var e = 2.71828182846;
export default function(x) {
    return Math.log(x);
}
```
```JavaScript
// app.js
import ln, {pi, e} from "lib/mathplusplus";
alert("2π = " + ln(e)*pi*2);
```

### Module Loaders

模块加载器支持:

- 动态加载
- 状态隔离
- 全局命名空间隔离
- 编译钩子
- 嵌套虚拟化

默认的模块加载器可配置，并且新的加载器可以被构建为在隔离或受限制的上下文环境中加载和处理代码。


```JavaScript
// Dynamic loading – ‘System’ is default loader
System.import('lib/math').then(function(m) {
  alert("2π = " + m.sum(m.pi, m.pi));
});

// Create execution sandboxes – new Loaders
var loader = new Loader({
  global: fixup(window) // replace ‘console.log’
});
loader.eval("console.log('hello world!');");

// Directly manipulate module cache
System.get('jquery');
System.set('jquery', Module({$: $})); // WARNING: not yet finalized
```

### Map + Set + WeakMap + WeakSet

用于一般算法的高效的数据结构。WeekMaps，WeekSets，只能添加对象的引用。

```JavaScript
// Sets
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;

// Maps
var m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) == 34;

// Weak Maps
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined

// Weak Sets
var ws = new WeakSet();
ws.add({ data: 42 });
// Because the added object has no other references, it will not be held in the set
```

### Proxies

代理可用于创建创建代理对象，代理对象可以拥有被代理对象的全部行为。可用于拦截，对象虚拟化，日志/profiling 等等。


```JavaScript
// Proxying a normal object
var target = {};
var handler = {
  get: function (receiver, name) {
    return `Hello, ${name}!`;
  }
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
```

```JavaScript
// Proxying a function object
var target = function () { return 'I am the target'; };
var handler = {
  apply: function (receiver, ...args) {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
```

如下是可用于代理的全部运行时元操作：

```JavaScript
var handler =
{
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
```

### Symbols

Symbols 让对象拥有属性访问控制的能力。Symbols 允许属性被命名为 `string` (如ES5中的方式)，或者 `symbol`。Symbols是一个新的私有类型。`name`参数是可选的，仅用于调试 - 并不用于控制属性的唯一性。Symbols是唯一的（就像 gensym），但是不是私有的，因为他们可以通过反射的机制暴漏出来，如：`Object.getOwnPropertySymbols`


```JavaScript
var MyClass = (function() {

  // module scoped symbol
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

  return MyClass;
})();

var c = new MyClass("hello")
c["key"] === undefined
```

### Subclassable Built-ins

在ES6中，内置的`Array`, `Date` 以及 DOM `Element` 可以被继承。

函数 `Ctor` 的构造过程现在分为两个阶段：

- 调用 `Ctor[@@create]` 创建对象，安装其他任何特殊的行为
- 在新的实例上触发构造器以完成初始化

`@@create` 标记可以用过 `Symbol.create`访问.  内建对象现在显示暴漏他们的`@@create`.

```JavaScript
// Pseudo-code of Array
class Array {
    constructor(...args) { /* ... */ }
    static [Symbol.create]() {
        // Install special [[DefineOwnProperty]]
        // to magically update 'length'
    }
}

// User code of Array subclass
class MyArray extends Array {
    constructor(...args) { super(...args); }
}

// Two-phase 'new':
// 1) Call @@create to allocate object
// 2) Invoke constructor on new instance
var arr = new MyArray();
arr[1] = 12;
arr.length == 2
```

### Math + Number + String + Array + Object APIs

新增了很多工具扩展，包括核心的Math工具库，Array转换扩展方法，String扩展方法，用于拷贝的Object.assign。

```JavaScript
Number.EPSILON
Number.isInteger(Infinity) // false
Number.isNaN("NaN") // false

Math.acosh(3) // 1.762747174039086
Math.hypot(3, 4) // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2

"abcde".includes("cd") // true
"abc".repeat(3) // "abcabcabc"

Array.from(document.querySelectorAll('*')) // Returns a real Array
Array.of(1, 2, 3) // Similar to new Array(...), but without special one-arg behavior
[0, 0, 0].fill(7, 1) // [0,7,7]
[1, 2, 3].find(x => x == 3) // 3
[1, 2, 3].findIndex(x => x == 2) // 1
[1, 2, 3, 4, 5].copyWithin(3, 0) // [1, 2, 3, 1, 2]
["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys() // iterator 0, 1, 2
["a", "b", "c"].values() // iterator "a", "b", "c"

Object.assign(Point, { origin: new Point(0,0) })
```

### Binary and Octal Literals
新增两个数字字面量形式：二级制（`b`），八进制（`o`）

```JavaScript
0b111110111 === 503 // true
0o767 === 503 // true
```

### Promises

Promises 用于异步编程。

```JavaScript
function timeout(duration = 0) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, duration);
    })
}

var p = timeout(1000).then(() => {
    return timeout(2000);
}).then(() => {
    throw new Error("hmm");
}).catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
})
```

### Reflect API
完整的反射API。此API在对象上暴露了运行时级别的元操作，从效果上来说，这是一个反代理API，并允许调用与代理陷阱中相同的元操作。实现代理非常有用。

```JavaScript
// No sample yet
```

### Tail Calls

（ES6）保证尾部调用时栈不会无限增长，这使得递归算法在面对未作限制的输入时，能够安全地执行。

```JavaScript
function factorial(n, acc = 1) {
    'use strict';
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// Stack overflow in most implementations today,
// but safe on arbitrary inputs in ES6
factorial(100000)
```
