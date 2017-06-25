# Block Bindings (块绑定)

传统上，在`JavaScript`编程中，变量定义的方法已经变成了一个非常需要警惕的部分。在许多以C语言为基础的编程语言中，变量`variables (or bindings)`在他们声明的地方就被创建了，但是在`JavaScript`中不是这样的。变量实际上怎么被创建的依赖于你如何去声明它，同时ES6为我们提供了一个选项来使得我们更容易控制好作用域。这个章节我们就来证明为什么`var`声明是让人疑惑的，介绍在ES6中的块级绑定，同时提供一些使用它们的最佳实践。

## Var Declarations and Hoisting（var 声明和变量提升）
通过`var`去声明变量会被视为在函数的顶部声明（如果声明在函数之外会被认为是全局作用域），而不在乎声明实际是在哪儿发生的；这就被叫做**`hoisting`**(变量提升)，为了举例说明`hoisting`是怎么做的，思考接下来的函数定义：

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

变量`value`的声明被提升到了函数的顶部，但是值得初始化还在同一地点。这就意味着`value`值在`else`语句中仍然能够访问到，如果函数执行到`else`语句，这个变量就会是`undefined`，因为它没有被初始化。

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

这个版本的`getValue`方法的行为就和你所期望的其他基于C的语言行为十分相似了。因为变量`value`使用了`let`声明代替了`var`，声明并没有被提升到函数的顶部，而且变量`value`不在能访问到当执行了一次`if`语句之后。如果`condition`等于`false`，`value`讲从来没有声明和初始化。

### No Redeclaration （不能重复声明）

如果一个标识符已经在作用域中被定义了，在该作用域中使用`let`去声明这个标识符会导致错误的抛出，如：

```js
var count = 30;

// Syntax error
let count = 40;
```

在这个例子中国，`count`被定义了两次，一次`var`，一次`let`。因为`let`不会在同样的作用域重定义已经存在的标识符，这时候`let`就会抛出错误。但是，如果`let`声明创建了一个相同名字的变量在自己包含的作用域中，就不会产生错误，如下例子：

```js
var count = 30;

// Does not throw an error
if (condition) {

    let count = 40;

    // more code
}
```

这里的`let`声明不会抛出错误，因为在`if`语句中产生了一个新的叫做`count`的变量，而不是在包围它的块中创建了`count`。在`if`块中，新的变量在全局`count`之下，除非子块被执行，否则不会被访问到

## Block Binding in Loops

## Global Block Bindings

## Emerging Best Practices for Block Bindings

## Summary