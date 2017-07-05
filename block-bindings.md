# Block Bindings (块绑定)

传统上，在`JavaScript`编程中，变量定义的方法已经变成了一个非常需要警惕的部分。在许多以C语言为基础的编程语言中，变量`variables (or bindings)`在他们声明的地方就被创建了，但是在`JavaScript`中不是这样的。变量实际上怎么被创建的依赖于你如何去声明它，同时ES6为我们提供了一个选项来使得我们更容易控制好作用域。这个章节我们就来证明为什么`var`声明是让人疑惑的，介绍在ES6中的块级绑定，同时提供一些使用它们的最佳实践。

## Var Declarations and Hoisting（var 声明和变量提升）
通过`var`去声明变量会被视为在函数的顶部声明（如果声明在函数之外会被认为是全局作用域），而不在乎声明实际是在哪儿发生的；这就被叫做`hoisting`(提升)，为了举例说明`hoisting`是怎么做的，思考接下来的函数定义：

```js
function getValue(condition) {

    if (condition) {
        var value = "blue";

        // other code

        return value;
    } else {

        // value exists here with a value of undefined

        return null;
    }

    // value exists here with a value of undefined
}
```

如果你对`JavaScript`不熟悉，你可能会认为`value`值只会在`condition`存在的时候被创建。事实上，`value`这个变量无论如何都会被创建。在这个代码之后，`JavaScript`引擎将`getValue`方法变成了像下面一样：

```js
function getValue(condition) {

    var value;

    if (condition) {
        value = "blue";

        // other code

        return value;
    } else {

        return null;
    }
}
```

变量`value`的声明被提升到了函数的顶部，但是值的初始化还在同一地点。这就意味着`value`值在`else`语句中仍然能够访问到，如果函数执行到`else`语句，这个变量就会是`undefined`，因为它没有被初始化。

这通常会导致一些新的`JavaScript`开发者在一些时候习惯了变量声明提升而忽略掉了这一特性可能最终会导致bugs。因为这个原因，ES6引入了块级作用域的操作让操作变量的声明周期变得稍微强大一点。

## Block-Level Declarations（块级别的声明）

块级声明就是指变量在声明的时候只能在其声明的块中能访问到，其他地方不能访问到。块级作用域又叫做词法作用域，在以下情况下呗创建：

1. 在函数中
2. 在一个块中（由 `{` 和 `}` 符号指明）

在很多以C语言为基础的语言中，都有块级作用域的概念。ES6中引入块级作用域的目的是希望给`JavaScript`带来一样的灵活性（一致性）

### Let Declaration (Let 声明)

`let`声明的语法和`var`声明的语法一致，你可以以最基本的方式用`let`去替换`var`声明变量，但是这样就会使得变量的作用域只会在当前块中有效（这里还有一些其他的细微的差别之后讨论）。因为`let`声明不会被提升到封闭块的顶部，你也许需要总是在块的最前面就使用`let`声明，这样变量在整个封闭的块中都可用了，下面是例子。

```js
function getValue(condition) {

    if (condition) {
        let value = "blue";

        // other code

        return value;
    } else {

        // value doesn't exist here

        return null;
    }

    // value doesn't exist here
}
```

这个版本的`getValue`方法的行为就和你所期望的其他基于C的语言行为十分相似了。因为变量`value`使用了`let`声明代替了`var`，声明并没有被提升到函数的顶部，而且变量`value`不在能访问到当执行了一次`if`语句之后。如果`condition`等于`false`，`value`将从来没有声明和初始化。

### No Redeclaration （不能重复声明）

如果一个标识符已经在作用域中被定义了，在该作用域中使用`let`去声明这个标识符会导致错误的抛出，如：

```js
var count = 30;

// Syntax error
let count = 40;
```

在这个例子中，`count`被定义了两次，一次`var`，一次`let`。因为`let`不会在同样的作用域重定义已经存在的标识符，这时候`let`就会抛出错误。但是，如果`let`声明创建了一个相同名字的变量在自己包含的作用域中，就不会产生错误，如下例子：

```js
var count = 30;

// Does not throw an error
if (condition) {

    let count = 40;

    // more code
}
```

这里的`let`声明不会抛出错误，因为在`if`语句中产生了一个新的叫做`count`的变量，而不是在包围它的块中创建了`count`。在`if`块中，新的变量在全局`count`之下，除非子块被执行，否则不会被访问到。

### Constant Declarations（常量声明）

在ES6中，你也可以使用`const`声明语法来定义一个变量。变量采用`const`定义会被认为是常量，意味着他们的值一旦被设置就不能被改变。因为这个原因，每个`const`变量必须在声明的时候初始化，如下例子：

```js
// Valid constant
const maxItems = 30;

// Syntax error: missing initialization
const name;
```

这里`maxItems`变量被初始化了，所以它的`const`声明工作起来没有任何问题。但是如果你尝试执行上述代码的程序，`name`变量会引起语法错误，因为`name`没有被初始化。

- Constant vs Let Declarations

常量和`let`声明一样，是块级的声明。这就意味着一旦执行流执行到常量声明的块之外，常量就无法被访问到了，而且声明并没有被提升，就像例子演示的一样：

```js
if (condition) {
    const maxItems = 5;

    // more code
}

// maxItems isn't accessible here
```

在这个代码中，常量`maxItems`是在`if`语句中被定义的。一旦这个语句执行完毕，`maxItems`在这个块之外就无法被访问到了。

另外一个和`let`相似的地方就是，在同一个作用域中使用`const`声明一个已经定义好的变量也会抛出一个错误。这不会有任何影响无论变量是用`var`（全局作用域和函数作用域）或者`let`（块级作用域）声明的，考虑如下例子的代码：

```js
var message = "Hello!";
let age = 25;

// Each of these would throw an error.
const message = "Goodbye!";
const age = 30;
```

单独来看，两个`const`什么都是有效的，但是这个例子在之前有`var`和`let`的声明，它们都不会像想象的一样执行。

不管上述类似的地方，在`let`和`const`之间存在一个需要记住的很大的差别。当你尝试给一个之前定义过的常量赋值的时候会抛出一个错误，无论是在严格模式还是非严格模式之下：

```js
const maxItems = 5;

maxItems = 6;      // throws error
```

跟其他语言中的常量一样的是，`maxItems`变量在之后不能被赋予一个新的值。但是，和其他语言中常量不一样的地方是，一个常量的值也许会被改变如果他是一个object。

- Declaring Objects with Const

`const`声明阻止的改变是**binding**（引用）不是**value**本身，这就意味着`const`对对象的声明不能阻止对象的改变。如下例子：

```js
const person = {
    name: "Nicholas"
};

// works
person.name = "Greg";

// throws an error
person = {
    name: "Greg"
};
```

这里，`person`的绑定被创建，通过一个对象含有一个属性并且初始化。这里修改`person.name`是不会引起错误的，因为这次改变改变的是`person`内部的属性，而不是`person`自身绑定的值。当代码尝试给`person`赋值时（尝试改变绑定），就会引起错误。这里`const`机制的设计可能不是那么容易理解，只需要记住：`const`会阻止绑定关系的改变，而不是绑定值的改变。

### The Temporal Dead Zone（暂时性死区）

一个被`let`或者`const`声明的变量是无法被访问到的除非在他们的声明之后。如果尝试这样做会导致reference error，即使是使用`typeof`这种安全的操作符：

```js
if (condition) {
    console.log(typeof value);  // ReferenceError!
    let value = "blue";
}
```

这里，变量`value`被`let`定义和初始化的，但是这条语句不会被执行因为前一行已经抛出了一个错误。这个问题是由于`value`值存在一个被社区称为是暂时性死区(TDZ)的概念。TDZ并不是ES6标准里面被明确命名的，但是TDZ经常被团队成员用来解释和描述为什么`let`和`const`声明在声明变量之前不能被访问到。这一章节讨论了一些TDZ引起的微妙的声明位置问题，虽然例子是使用`let`，记得`const`有相同的情况。

这个特性在任何时候都是正确的，当你尝试使用一个`let`或者`const`声明的变量在定义他们之前。就像上面的例子演示得一样，即使你使用通常情况下安全的操作符`typeof`也一样。但是你能使用`typeof`在一个变量声明的块之外，尽管它可能给不了你之后的结果。考虑代码：

```js
console.log(typeof value);     // "undefined"

if (condition) {
    let value = "blue";
}
```

当使用`typeof`的时候，变量`value`并不在TDZ内，因为操作是发生在`value`被声明的块之外的。这意味着`value`并没有任何绑定，所以`typeof`返回`"undefined"`。

TDZ只是block bindings（块绑定）的一个独特的方面，另一个独特的方面是是在循环中。

## Block Binding in Loops（循环中的块绑定）

可能开发者最想要具有变量块级作用域的地方就是在`for`循环之中，计数器变量只在循环中被使用。例如，在Javascript中如下代码是不太正常的：

```js
for (var i = 0; i < 10; i++) {
    process(items[i]);
}

// i is still accessible here
console.log(i);   //10
```

在其他具有默认块级作用域特性的语言中，这个例子按预期来讲只有`for`循环内部能访问到变量`i`。在JavaScript中，变量`i`在循环结束之后仍然能访问到因为`var`声明存在提升。使用`let`代替它，如下代码就会像预期一样执行：

```js
for (let i = 0; i < 10; i++) {
    process(items[i]);
}

// i is not accessible here - throws an error
console.log(i);
```

在这个例子中，变量`i`只会存在于`for`循环中，一旦循环结束，变量在其他任何地方都无法访问到了。

### Function in Loops（循环中的函数）

再循环中，`var`的特征是具有持续创建函数的问题，因为函数变量在函数作用域之外是能被访问到的。考虑如下代码：

```js
var funcs = [];

for (var i = 0; i < 10; i++) {
    funcs.push(function() { console.log(i); });
}

funcs.forEach(function(func) {
    func();     // outputs the number "10" ten times
});
```

你也许通常认为这个代码会打印0到9，但是他在一行打印了10次10数字。这是因为`i`在每一次循环迭代中共享访问，这就意味着在循环中创建的函数都获取到了相同的变量引用。变量`i`在循环结束后值为`10`，所以当执行`console.log(i)`时，这个值每次都被打印。

为了解决这个问题，开发者使用立即执行函数表达式(IIFEs)来在循环中迫使一个新的迭代变量拷贝被创建。如下例子：

```js
var funcs = [];

for (var i = 0; i < 10; i++) {
    funcs.push((function(value) {
        return function() {
            console.log(value);
        }
    }(i)));
}

funcs.forEach(function(func) {
    func();     // outputs 0, then 1, then 2, up to 9
});
```

这个版本在循环中采用了IIFE，`i`的值被传递给了IIFE，编程了一个自身的拷贝叫做`value`。这个值被迭代函数使用，所以当每个函数被调用的时候，`value`值是从0到9的。幸运的是，ES6中`let`和`const`的块级绑定可以轻松的解决问题。

### Let Declarations in Loops（循环中的let声明）

`let`声明能有效的模仿IIFE在上述例子中做的事情。在一个迭代中，循环和上一次迭代一样创建了一个相同名字的**新的变量**并且初始化它，这就意味着你能完全忽略掉IIFE并且得到相同的结果：

```js
var funcs = [];

for (let i = 0; i < 10; i++) {
    funcs.push(function() {
        console.log(i);
    });
}

funcs.forEach(function(func) {
    func();     // outputs 0, then 1, then 2, up to 9
})
```

这个代码得循环的结果就和使用`var`和IIFE的结果一样，但是可以验证的是会更加干净。`let`声明在循环中每次创建了一个新的变量`i`，所以循环创建的每个函数在内部得到了它独有的`i`的拷贝。每一个`i`的拷贝都被赋予了一个值在循环每次迭代开始的时候。相同的情况再`for-in`和`for-of`也一样。

```js
var funcs = [],
    object = {
        a: true,
        b: true,
        c: true
    };

for (let key in object) {
    funcs.push(function() {
        console.log(key);
    });
}

funcs.forEach(function(func) {
    func();     // outputs "a", then "b", then "c"
});
```

在这个例子中，`for-in`循环跟`for`循环有相同的行为。循环每次迭代，一个新的`key`的绑定被创界，所以每次创建的`function`都有他自己的一个`key`变量的拷贝。结果表明函数每次输出不同的值，如果`var`来声明`key`，所有的函数会输出`c`。

> It’s important to understand that the behavior of `let` declarations in loops is a specially-defined behavior in the specification and is not necessarily related to the non-hoisting characteristics of `let`. In fact, early implementations of `let `did not have this behavior, as it was added later on in the process.

大致意思就是要理解循环中`let`声明的行为，不要简单地认为是没有提升（hoisting）

### Constant Declarations in Loops（循环中的常量声明）

在ES6的标准中，并没有不允许在循环中使用`const`声明。但是，它们会有不同的行为会根据你使用的循环的类型。对于一般的`for`循环，你可以使用`const初始化，但是循环会抛出一个警告如果你想改变值。如下：

```js
var funcs = [];

// throws an error after one iteration
for (const i = 0; i < 10; i++) {
    funcs.push(function() {
        console.log(i);
    });
}
```

在这个代码中，`i`被声明为一个常量。在循环的第一次迭代中，`i`为0执行成功。但是当`i++`的时候会抛出错误，因为你尝试了修改常量的值。同样的，你能使用`const`声明一个循环变量如果你不去改变变量的值。

另一方面，当使用`for-in`或者`for-of`时候，`const`变量和`let`变量表现得一样，所以如下代码不会有错误：

```js
var funcs = [],
    object = {
        a: true,
        b: true,
        c: true
    };

// doesn't cause an error
for (const key in object) {
    funcs.push(function() {
        console.log(key);
    });
}

funcs.forEach(function(func) {
    func();     // outputs "a", then "b", then "c"
});
```

这里代码中的函数和`"Let Declaration in Loops"`章节中的第二个例子几乎一样。唯一的不同是`key`的值不能在循环中被改变。`for-in`和`for-of`之所以能使用`const`是因为循环是在每一次迭代中创建了一个新的绑定而不是尝试去修改已有绑定的值。（和之前例子中使用`for`代替`for-in`类似）

## Global Block Bindings

另外一个`var`和`let`以及`const`的不同是在全局作用域中的行为。当`var`被用在全局作用域的时候，它会创建一个新的全局变量，这个全局变量是global对象的一个属性（在浏览器中是`window`），这就意味着你可以通过`var`马上重写一个已经存在的全局变量。

```js
// in a browser
var RegExp = "Hello!";
console.log(window.RegExp);     // "Hello!"

var ncz = "Hi!";
console.log(window.ncz);        // "Hi!"
```

即使是`RegExp`这种已经在`window`上定义了的全局变量，通过`var`去重写是不安全的。这个例子表明一个新的`RegExp`已经从原来的被重写了。类似的，`ncz`被一个全局变量定义，然后立即被定义为`window`的一个属性，这是JavaScript一贯的工作方式。

如果你在全局作用域中使用`let`和`const`，一个新的绑定在全局作用域中被创建，没有新的属性被添加到全局对象中。这也以为这你不能通过`let`或者`const`去重写全局变量，你只能隐藏它。如下例子：

```js
// in a browser
let RegExp = "Hello!";
console.log(RegExp);                    // "Hello!"
console.log(window.RegExp === RegExp);  // false

const ncz = "Hi!";
console.log(ncz);                       // "Hi!"
console.log("ncz" in window);           // fals
```

这儿，一个新的`RegExp`的`let`声明创建了一个global之下的`RegExp`绑定。这意味着`window.RegExp`和`RegExp`并不一样，所以并没有对全局作用域带来损害。同时，`ncz`的`const`声明也创建了一个绑定但没有创建一个全局对象的属性。这个特性也使得`let`和`const`在全局作用域的使用中变得更加安全，当你不想创建一个全局对象的属性的时候。

> You may still want to use `var` in the global scope if you have a code that should be available from the global object. This is most common in a browser when you want to access code across frames or windows.

## Emerging Best Practices for Block Bindings（显露出的块绑定的最佳实践）

当ES6还在草案阶段的时候，JavaScript开发者普遍认为应该默认使用`let`声明变量来取代`var`声明变量。`let`的表现是他们认为`var`应该表现的。所以直接替换更具有逻辑意义。同样的，也应该使用`const`声明去针对那些需要修改保护的变量

但是，随着越来越多的开发者迁移到ES6，交替使用的方法得到了更多的欢迎：使用`const`作为默认选项，只有当你认为变量的值需要改变的时候采用`let`。这个基本原理是大多数变量在他们初始化之后都不会改变，因为非预期的改变会带来bugs，这个想法具有很重大的意义，也在我们使用ES6中值得探索。

## Summary（总结）

`let`和`const`的快绑定给JavaScript引入了块级作用域的概念，这些声明不会提升而却只会存在在声明它们的块中。这个机制更像其他的语言而更少地带来了不想要的错误，变量也能在他们被需要的时候声明。另一方面，你不能访问到变量在声明它们之前，即使是使用安全操作符`typeof`，因为TDZ，所以尝试在声明之前访问块绑定会导致错误。

在很多情况下，`let`和`const`和`var`的机制十分类似；但是在循环中不一样。`let`和`const`，在`for-in`和`for-of`循环的每一次迭代中都创建了一个新的绑定。这意味着在循环中创建的函数能访问到当前循环迭代中绑定的值而不是整个循环最后一次迭代后的值（`var`的机制）。在`for`循环中`let`声明也是一样，但是尝试在循环中修改`const`声明会导致错误。

当下块绑定的最佳实践是使用`const`作为默认的声明方式，只当我们知道变量需要改变时，使用`let`替代。这回保证代码一个基本水平的不可变性从而帮助我们防止一些类型错误。