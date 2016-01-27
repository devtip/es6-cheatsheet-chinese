# ES6手抄
@(ES6笔记)[es6,es2015,es6入门]

这份笔记包含ES2015 [ES6] 提示, 技巧, 最佳实践 和一些你日常工作常用的代码片段. 欢迎贡献！本文尽量保留原作者的意思，标记为＂ps:＂都是个人的理解，当然也有一些翻译不到位的地方，望请见谅！



- 译者：[＠devtip](https://github.com/devtip/)
- 翻译日期: 2016-01-27
- 原文地址: https://github.com/DrkSephy/es6-cheatsheet
- pdf版本请点击[这里](http://pan.baidu.com/s/1qXtFQPI)前往百度网盘



------------------------

[TOC]

-------------------------

## var 对比 let / const

> 除var之外, 我们现在可以通过两个新的标识符(**let**，**const**)来存储变量. 而它们与 **var**不同的是, **let** 和 **const** 语句并不会提升到作用域的前端，这是因为ＥS6之前不存在＂**块级作用域**＂！

一个使用var的例子:

```javascript
var snack = 'Meow Mix';

function getFood(food) {
  // snack变量被提升到这里
  // var snack = undefined
    if (food) {
        var snack = 'Friskies';
        // snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // undefined
```

然而, 当我们使用**let**替换**var**，程序到底发生了什么:

```javascript
let snack = 'Meow Mix';

function getFood(food) {
    if (food) {
      // 这里不会被提升到作用域的前端
        let snack = 'Friskies';
        return snack;
    }
    // 当food为false时，它只能从上一级作用域，直到全局作用域进行查找
    return snack;
}

getFood(false); // 'Meow Mix'
```


当重构代码时候，特别要小心那些使用**var**的代码！盲目地使用**let**代替**var**将会产生意想不到的行为！


> **注意**: **let** 和　**const**都是块级作用域的. 因此，块级作用域的标识符在定义之前就被引用，将会产生`ReferenceError`.

```javascript
console.log(x);
let x = "hi";   // 引用错误: x还没有定义
```

> **最佳实践**: 在遗留代码中保留**var**声明，以表示这段代码重构需要慎重考虑！党我们在一个新的代码库中工作时，使用**let**声明的变量，将会随着时间的推移发生改变，以及**const**声明的变量，通常并不会改变！（实质上，使用const声明的变量只有＂只读＂状态）


## 使用语句块替换IIFE

> **自调用函数表达式**的常见用途是将变量封装在其作用域．ES6提供了一种创建块级作用域的能力，因此，我们不在单纯局限于函数作用域！

```javascript
(function () {
    var food = 'Meow Mix';
}());
console.log(food); // 引用错误
```

使用ES6块:

```javascript
{
    let food = 'Meow Mix';
}
console.log(food); // 引用错误
```

## 箭头函数

很多时候，嵌套函数的目的是想＂维护＂当前词法作用域中**this**。看下面的例子：

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character; // Cannot read property 'name' of undefined（不能从undefined读取到name属性）
    });
};
```

One common solution to this problem is to store the context of **this** using a variable:
一个比较普遍的解决方案是通过一个变量，（通常是that）来当前上下文的**this**

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    var that = this; // 存储当前上下文的this
    return arr.map(function (character) {
        return that.name + character;
    });
};
```

我们可以为数组的map方法传入一个适当的上下文`this`:
```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }, this);
}
```

甚至我们可以使用ｂind来为函数绑定适当的上下文:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }.bind(this));
}
```

使用**箭头函数**,词法作用域中**this**并没有变得很神秘，重写上面的代码（this已经进行了正确的绑定）:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(character => this.name + character);
}
```

> **最佳实践**: 当你想维护词法作用域中this，最好使用**箭头函数**.


当你使用函数表达式只是简单地返回一个值，箭头函数也显得更为简洁！


```javascript
var squares = arr.map(function (x) { return x * x }); // 函数表达式
```

```javascript
const arr = [1, 2, 3, 4, 5];
const squares = arr.map(x => x * x); // Arrow Function for terser implementation
```

> **最佳实践**: 尽可能使用**箭头函数**替代函数表达式.


## 字符串
随着ES6标准库功能的日臻完善，我们可以在字符串中使用新方法，诸如**.includes()**和**.repeat()**。

### .includes( )

```javascript
var string = 'food';
var substring = 'foo';
console.log(string.indexOf(substring) > -1);
```

当字符串是包含关系，将会返回一个`> -1`的值，我们可以简单地使用 **.includes()**取代这种操作，并且**.includes()**将会返回一个布尔(boolean)值：

```javascript
const string = 'food';
const substring = 'foo';
console.log(string.includes(substring)); // true
```

### .repeat( )

```javascript
function repeat(string, count) {
    var strings = [];
    while(strings.length < count) {
        strings.push(string);
    }
    return strings.join('');
}
```
在ＥS6,我们有一个更简洁的接口实现(**.repeat()**)来实现字符串重复功能:

```javascript
// String.repeat(numberOfRepetitions)
'meow'.repeat(3); // 'meowmeowmeow'
```

### 模板字面量

使用**模板字面量**, 我们可以构造一段拥有特殊字符的字符串，并且我们无需显式地对它们进行转义！

```javascript
var text = "This string contains \"double quotes\" which are escaped."
```

```javascript
let text = `This string contains "double quotes" which are escaped.`
```

**模板字面量** 也支持(变量)插值, 它扮演的是连接字符串和值的角色:

```javascript
var name = 'Tiger';
var age = 13;
console.log('My cat is named ' + name + ' and is ' + age + ' years old.');
```

更为简单的是:

```javascript
const name = 'Tiger';
const age = 13;
console.log(`My cat is named ${name} and is ${age} years old.`);
```

在ES5,我们通常就想下面这样处理新行:
```javascript
var text = (
  'cat\n' +
  'dog\n' +
  'nickelodeon'
)
```

或者:

```javascript
var text = [
  'cat',
  'dog',
  'nickelodeon'
].join('\n')
```

**模板字面量** 会为我们保留新行而无需显式对它们进行上面的操作:

```javascript
let text = ( `cat
dog
nickelodeon`
)
```


**模板字面量**甚至可以接收表达式:

```javascript
let today = new Date()
let text = `The time and date is ${today.toLocaleString()}`
```


## 解构赋值

解构,意味着它允许我们以更为方便的语法从(多层嵌套的)数组和对象中＂提取＂出来，并将它们存储在相应的变量！


### 数组解构赋值

```javascript
var arr = [1, 2, 3, 4];
var a = arr[0];
var b = arr[1];
var c = arr[2];
var d = arr[3];
```

```javascript
let [a, b, c, d] = [1, 2, 3, 4];
console.log(a); // 1
console.log(b); // 2
```

### 对象解构赋值

```javascript
var luke = { occupation: 'jedi', father: 'anakin' }
var occupation = luke.occupation; // 'jedi'
var father = luke.father; // 'anakin'
```

```javascript
let luke = { occupation: 'jedi', father: 'anakin' }
let {occupation, father} = luke;
console.log(occupation); // 'jedi'
console.log(father); // 'anakin'
```


## 模块

ES6之前，我们使用诸如[Browserify](http://browserify.org/)库来创建在客户端模块，并且像**Node.js**一样使用[require](https://nodejs.org/api/modules.html#modules_module_require_id)来加载模块.随着ES6的来临，我们可以直接使用任何类型（AMD和CommonJS）的模块。

### CommonJS中暴露接口

```javascript
module.exports = 1
module.exports = { foo: 'bar' }
module.exports = ['foo', 'bar']
module.exports = function bar () {}
```

### ES6中暴露接口

在ES6, 我们暴露接口的方式变得多样化.我们甚至可以实现**Named Exports（暴露名称）**:

```javascript
export let name = 'David';
export let age  = 25;​​
```

甚至暴露一个对象列表：

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

export { sumTwo, sumThree };
```


我们甚至通过简单地使用**export**关键字来暴露一个值:

```javascript
export function sumTwo(a, b) {
    return a + b;
}

export function sumThree(a, b, c) {
    return a + b + c;
}
```

最后，我们可以暴露**默认**的模块接口（**export default bindings**）：

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

let api = {
    sumTwo,
    sumThree
}

export default api
```

> **最佳实践**: 你应当一直在模块的最底部使用**export default**方法，这样会使得模块（对模块的使用者）更为清晰, 并且有必要指出当前模块暴露了什么，这样可以节省它们大多时间(去理解代码的逻辑).  更何况，CommonJS模块通常会导出一个值或对象。我们坚持采用这种模式，将会使代码更容易阅读，并让CommonJS模块和ES6模块之间修改代码简直就是＂easy job＂！

### ES6中的导入

ES6给我们带来各种导入（importing）风格. 我们可以导入整个文件:


```javascript
import `underscore`
```

> 有一点非常重要是，import指令只有在当前文件的顶部才能生效.

就像Python一样，我们可以以命名的方式导入模块相应的部分(比如sumTwo, sumThree):

```javascript
import { sumTwo, sumThree } from 'math/addition'
```

甚至可以为它们取"别名":

```javascript
import {
  sumTwo as addTwoNumbers,
  sumThree as sumThreeNumbers
} from 'math/addition'
```

此外，我们可以使用`*`字符**导入所有的东西**（也称为命名空间导入）：
```javascript
import * as util from 'math/addition'
```


最后，我们可以从模块中导入值的列表：

```javascript
import * as additionUtil from 'math/addtion';
const { sumTwo, sumThree } = additionUtil;
```

当我们导入默认的对象(这里值得是Ｒeact)，我们甚至可以选择性地这个对象的一些方法(React.Component): 

```javascript
import React from 'react';
const { Component, PropTypes } = React;
```

>**注意**：模块暴露的值是**绑定**关系，而不是引用关系。因此，改变模块这种＂绑定＂关系，会影响所导出模块内的值。避免更改这些模块暴露出来的公共接口。

## 参数

ES5中, 我们处理函数的**默认参数**,**不定参数(PS:参数数量不固定)**,**命名参数**有各种不同的方式. 在ES6, 实现相同的功能变得更为简洁！


### 默认参数
```javascript
function addTwoNumbers(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

ES6简化了我们为函数参数提供＂默认值＂:

```javascript
function addTwoNumbers(x=0, y=0) {
    return x + y;
}
```

```javascript
addTwoNumbers(2, 4); // 6
addTwoNumbers(2); // 2
addTwoNumbers(); // 0
```

### 其余参数

在ES5，我们经常像下面那样处理＂函数参数数量不确定＂的情况：

```javascript
function logArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

使用**rest**运算符(PS:（３个点）｀...｀), 我们可以不定数量的参数:

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}
```

### 命名参数

ES5中，一个处理"命名参数"的常用模式是**可配置对象**模式，这种模式在jQuery中广泛使用!

```javascript
function initializeCanvas(options) {
    var height = options.height || 600;
    var width  = options.width  || 400;
    var lineStroke = options.lineStroke || 'black';
}
```

我们可以通过＂解构赋值＂来实现相同的功能:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'}) {
        ...
    }
    // Use variables height, width, lineStroke here
```

如果我们想整个值都是可配置(PS:可选)的，我们"解构"一个空的对象:


```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'} = {}) {
        ...
    }
```

### Spread操作符

我们可以使用spread操作符向函数传递一个数组，而这个数组的值作为函数参数来使用,(比如获取数组的最大值):
``` javascript
// ES5
Math.max(-1, 100, 9001, -32) // 9001
Math.max.apply(null, [-1, 100, 9001, -32]) // 9001
```

```javascript
// ES6
Math.max(...[-1, 100, 9001, -32]) // 9001
```

## 类

ES6之前，我们通过创建一个构造器函数和扩展构造函数的原型(prototype)的属性这种方式来实现类：

```javascript
function Person(name, age, gender) {
    this.name   = name;
    this.age    = age;
    this.gender = gender;
}

Person.prototype.incrementAge = function () {
    return this.age += 1;
};
```

通常像下面这种方式来继承类：

```javascript
function Personal(name, age, gender, occupation, hobby) {
    Person.call(this, name, age, gender);
    this.occupation = occupation;
    this.hobby = hobby;
}

Personal.prototype = Object.create(Person.prototype);
Personal.prototype.constructor = Personal;
Personal.prototype.incrementAge = function () {
    return Person.prototype.incrementAge.call(this) += 1;
}
```

ES6迫切之下为ＪavaScript引擎提供一种＂语法糖＂的方式来掩盖上面代码中丑陋的行为，那就是直接使用class关键字来创建类:

```javascript
class Person {
    constructor(name, age, gender) {
        this.name   = name;
        this.age    = age;
        this.gender = gender;
    }

    incrementAge() {
      this.age += 1;
    }
}
```

:-( 
使用**extends**关键字来继承父类(PS:这里值得是Ｐerson)

```javascript
class Personal extends Person {
    constructor(name, age, gender, occupation, hobby) {
      super(name, age, gender);
      this.occupation = occupation;
      this.hobby = hobby;
    }

    incrementAge() {
      super.incrementAge();
      this.age += 20;
      console.log(this.age);
    }
}
```

> **最佳实践**: 虽然ES6提供这种创建类的语法以掩盖运行在JavaScript引擎背后对于类的实现原理，但是对于那些入门的家伙是相当好的特性，这样允许他们写出更为干净的代码．


## Symbols(标记)

Symbols在ES6之间就已经存在了, 但是我们现在可以通过公共接口直接使用Symbols.其中一个例子是创建独特的属性键，这将永远不会(与其它代码)发生冲突：

```javascript
const key = Symbol();
const keyTwo = Symbol();
const object = {};

object[key] = 'Such magic.';
object[keyTwo] = 'Much Uniqueness'

// Two Symbols will never have the same value
>> key === keyTwo
>> false
```

## Maps(映射)

**Maps**在JavaScript中是非常有用的数据结构.ES6之前, 我们通过对象来创建**hash** maps来实现相同的目标:

```javascript
var map = new Object();
map[key1] = 'value1';
map[key2] = 'value2';
```

然而，这不能避免我们意外的＂覆盖＂某些特定属性名：
(ps:例如下面代码将hasOwnProperty方法修改为一个字符串)

```javascript
// ps:代码不能直接运行
> getOwnProperty({ hasOwnProperty: 'Hah, overwritten'}, 'Pwned');
> TypeError: Property 'hasOwnProperty' is not a function
```


实际上，**Maps**允许我们**set(设置)**, **get(获取)*和 **search(查找)**值，甚至为我们提供更多的功能.

```javascript
let map = new Map();
> map.set('name', 'david');
> map.get('name'); // david
> map.has('name'); // true
```

Ｍaps的最令人震惊的部分是，我们不再局限于使用字符串。现在，我们可以使用任何类型作为键，并且它不会被类型转换为字符串(ps:对象的所有键都是字符串类型!)

``` javascript
// ps:
var obj = {name: 'codemarker'};
typeof Object.keys(obj)[0]; //=> "string"
```

```javascript
let map = new Map([
    ['name', 'david'],
    [true, 'false'],
    [1, 'one'],
    [{}, 'object'],
    [function () {}, 'function']
]);

for (let key of map.keys()) {
    console.log(typeof key);
    //=> string, boolean, number, object, function
};
```


> **注意**：使用非基本数据类型(比如函数或对象)将会导致`map.get()`方法测试相等性问题时无法正常地工作。因此，坚持使用基本数据类型，如字符串，布尔和数字。(ps:Sorry，没看懂这段话意图的！)

我们可以通过**.entries( )**来遍历整个map:

```javascript
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
```



## WeakMaps

为了在<ES5的版本中存储私有数据，我们不得不采用各种不同的方法以实现这样的意图。其中一种方法是使用命名约定(ps:下面代码中的_age, _incrementAge)：

```javascript
class Person {
    constructor(age) {
        this._age = age;
    }

    _incrementAge() {
      this._age += 1;
    }
}
```

但是，命名约定可能造成代码库混乱，并且这种方案并不总是可行的。取而代之，我们将使用WeakMaps来存储我们的私有数据:

```javascript
let _age = new WeakMap();
class Person {
  constructor(age) {
    _age.set(this, age);
  }

  incrementAge() {
    let age = _age.get(this) + 1;
    _age.set(this, age);
      if(age > 50) {
        console.log('Midlife crisis');
      }
  }
}
```

使用WeakMaps存储私有数据最cool的地方是：它们的键并不会**泄露**！ 我们可以通过**Reflect.ownKeys()**进行验证:

```javascript
> const person = new Person(50);
> person.incrementAge(); // 'Midlife crisis'
> Reflect.ownKeys(person); // []
```

## Promises（承诺）
> Promises将会改写回调函数的风格(callback hell):

ps:(个人理解，**callback hell**是一种异步Ｊavascript回调函数的代码书写风格，比如下面代码中func2必须等待func1有相应的处理结果(value１)，回调才能继续处理下去！很明显的问题，代码将会横向＂变态级＂扩张)

```javascript
func1(function (value1) {
  func2(value1, function(value2) {
    func3(value2, function(value3) {
      func4(value3, function(value4) {
        func5(value4, function(value5) {
          // Do something with value 5
        });
      });
    });
  });
});
```

将代码改为＂竖直＂风格看看(ps:哈哈，是不是优雅多了？):

```javascript
func1(value1)
  .then(func2)
  .then(func3)
  .then(func4)
  .then(func5, value5 => {
    // Do something with value 5
  });
```

ES6之前, 我们采用 [bluebird](https://github.com/petkaantonov/bluebird) 或者[Q](https://github.com/kriskowal/q)实现上面优雅的方案.现在ES6原生支持Promise:

```javascript
new Promise((resolve, reject) =>
    reject(new Error('Failed to fulfill Promise')))
    .catch(reason => console.log(reason));
```

Promise有两个事件处理程序, 分别是**resolve**和**rejected**．当 Promise的状态为**fulfilled**时，**resolve**函数将会被调用，而当 Promise的状态为**rejected**时，**resolve**函数将会被调用!


> **Promises的好处**: 使用一连串的回调嵌套来处理错误将会使程序变得混乱．使用Promises的好处，我们的代码结构变得非常清晰，以冒泡的形式抛出错误并优雅地处理相应的错误。此外，被resolved/rejected　Promise后的值是保持不变的 - 它永远不会改变。


下面是使用承诺的一个实际的例子：

```javascript
var fetchJSON = function(url) {
  return new Promise((resolve, reject) => {
    $.getJSON(url)
      .done((json) => resolve(json))
      .fail((xhr, status, err) => reject(status + err.message));
  });
}
```

我们也使用通过**Promise.all( )**方法来使Promises并行处理异步操作的数组：

```javascript
var urls = [
  'http://www.api.com/items/1234',
  'http://www.api.com/items/4567'
];

var urlPromises = urls.map(fetchJSON);

Promise.all(urlPromises)
  .then(function(results) {
     results.forEach(function(data) {
     });
  })
  .catch(function(err) {
    console.log("Failed: ", err);
  });
```


## THX FOR READING
> :-(
