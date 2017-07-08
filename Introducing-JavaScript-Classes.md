# Introducing JavaScript Classes (JavaScript中类的介绍)

和其他面向对象的编程语言不一样的是，JavaScript在被创建的时候并没有提供类和类的接口来作为定义和对象相关的主要方式，这让很多开发者十分困惑。从最早的pre-ECMAScript 1到ECMAScript 5，很多库提供了公关方法来使得JavaScript看起来像支持类。虽然一些JavaScript开发者强烈地认为这个语言不需要类的概念，很多的库专门为了这个目的而被建立，最后导致了在ES6中包含了类的概念。

当你在探索ES6中的类时，对你使用类并了解其底层机制是很有帮助的，所以这个章节从ES5中开发者如何获得类相似的行为开始，之后你会看到，ES6中的类和其他语言中的类并不完全相似。它有一些独特性从而能拥抱JavaScript这门有动态特性的语言。

## Class-Like Structures in ECMAScript 5（在ES5中与类相似的结构）

在ES5和更早的时候，JavaScript没有类。和类最相似的是创造一个构造函数然后向它的原型设置方法。这个通常的方法一般叫做创建自定义类型，例如：

```js
function PersonType(name) {
    this.name = name;
}

PersonType.prototype.sayName = function() {
    console.log(this.name);
};

let person = new PersonType("Nicholas");
person.sayName();   // outputs "Nicholas"

console.log(person instanceof PersonType);  // true
console.log(person instanceof Object);      // true
```

在这个代码中，`PersonType`是一个构造函数，创建了一个名叫`name`的属性。`sayName()`方法被设置到了原型上所以所有的`PersonType`对象的实例共享了这个函数。然后，一个新的`PersonType`的实例通过`new`操作符被创建。这就使得`person`对象被认为是一个`PersonType`的实例同事通过原型继承了`Object`。

这是很多类模仿（class-mimicking）的JavaScript库的基础，也是ES6开始的地方。

## Class Declarations（类声明）

最简单的ES6中的类是依靠类声明，和很多其他的语言看起来类似。

### A Basic Class Declaration（基本的类声明）

类的声明从一个`class`关键字之后的类的名字开始。剩下的语法看起来就和简单的对象字面量方法类似，但是**不需要再他们之间用逗号隔开**，举个例子，一个简单的类声明：

```js
class PersonClass {

    // equivalent of the PersonType constructor
    constructor(name) {
        this.name = name;
    }

    // equivalent of PersonType.prototype.sayName
    sayName() {
        console.log(this.name);
    }
}

let person = new PersonClass("Nicholas");
person.sayName();   // outputs "Nicholas"

console.log(person instanceof PersonClass);     // true
console.log(person instanceof Object);          // true

console.log(typeof PersonClass);                    // "function"
console.log(typeof PersonClass.prototype.sayName);  // "function"
```

这里类声明`PersonClass`和之前例子中`PersonType`的行为十分类似。但是作为定义一个函数来作为构造器，类声明允许你使用特殊的`constructor`方法来直接定义类中的构造器，由于类方法使用简单的语法，所以不必要再使用`function`关键字了。所有其他的方法名都没有特殊的含义，所以你可以添加你想要的任何方法。

> Own properties, properties that occur on the instance rather than the prototype, can only be created inside a class constructor or method. In this example, `name` is an own property. I recommend creating all possible own properties inside the constructor function so a single place in the class is responsible for all of them.（大致意思就是尽量将私有属性都放在构造函数中）

有趣的是，类声明只是之前自定义类型声明的一个语法糖。`PersonClass`声明事实上是创建了一个函数具有`constructor`方法的行为，这也是为什么`typeof PersonClass`返回的是`"function"`的原因。在这个例子中`sayName()`方法最终也是`PersonClass.prototype`上的方法，和之前例子中`sayName()`和`PersonType.prototype`的关系相似。这个相似点允许你缓和使用自定类型和类声明从而不必要过于担心你具体使用的是哪种。

### Why to Use the Class Syntax（为什么使用类语法）

先不考虑类和自定义类型之间的相似，这儿有一些非常重要的不同点需要记住：

1. 类声明和函数什么不一样的是，**不会提升（not hoisted）**，类声明和`let`声明行为一样所以存在暂时性死区知道代码执行到声明处之前
2. 所有在类声明中的代码都会自动的执行严格模式，没有任何的办法选择在类的内部退出严格模式。
3. 所有的方法不可枚举，这是一个与自定义类型重大的不一样。在自定义类型中你需要使用`Object.defineProperty`去使得方法不可枚举。
4. 所有方法都缺少内部的`[[Construct]]`方法，如果你尝试使用`new`去调用会抛出错误。
5. 不使用`new`调用类的构造器会抛出错误。
6. 尝试在类方法里面重写类的名字会抛出错误。

## Class Expressions
## Classes as First-Class Citizens
## Accessor Properties
## Computed Member Names
## Generator Methods
## Static Members
## Inheritance with Derived Classes
## Using new.target in Class Constructors
## Summary
