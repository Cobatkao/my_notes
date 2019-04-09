# 02-ES5实现let/const

模拟块级作用域实现let

```
function outputNum(count){
//块级作用域
  (function(){
    for(var i = 0; i < count; i ++){
      console.log(i)
    }
  })();
  console.log(i)
}

outputNum(5)
```

我们可以看到通过使用立即执行函数，变量i只会在for循环的语句块中有定义，循环一旦结束，变量i就会被销毁，循环外面就访问不到变量i了。

## Object.defineProperty()实现const

Object.defineProperty() 方法可以定义对象属性的数据描述和存储描述

> `configurable`
当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，也能够被删除。默认为 false。
`enumerable`
当且仅当该属性的 enumerable 为 true 时，该属性才能够出现在对象的枚举属性中。默认为 false。
`value`
该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。默认为 undefined。
`writable`
当且仅当该属性的 writable 为 true 时，该属性才能被赋值运算符改变。默认为 false。

实现const，我们把writable设置为false的时候，该属性是只读的，也就满足了常量了性质，再把configurable设置为false，使得除了value之外的描述符无法被修改。

注意：
1. **特栗**：configurable的限制有一个特例，就是writable可以由true改为false，不能由false改为true
2. **数据描述符默认值**：当我们用常规方法定义属性的时候，其除 value 以外的数据描述符默认均为 true ，而当我们用`Object.defineProperty()`定义属性的时候，默认为 false。

所以我们这样定义也可以，其它数据描述符默认为false

```
var CONST = {};
Object.defineProperty(CONST, "A", {
    value: 1,
    enumerable: true
});
Object.defineProperty(CONST, "B", {
    value: 2,
    enumerable: true
});
```

上面是从对象的属性层面去定义一组常量，我们还可以从对象的角度去配置一个对象包括它的所有属性！

我们可以`Object.freeze()`来对一个对象进行冻结，实现常量的需求，该方法会阻止对象扩展，并冻结对象，将其所有属性设置为只读和不可配置

```
//第一种方法：属性层面,对象可扩展
var CONST = {};
Object.defineProperty(CONST, "A", {
    value: 1,
    enumerable: true
});

//第二种方法：对象层面，对象不可扩展
var CONST = {};
CONST.A = 1;
Object.freeze(CONST);
```

### setter和getter函数

```
function setConst(name, value) {
  Object.defineProperty(window, name, {
    set(x) {
      throw new Error(`Assignment to constant variable ${name}`);
    },
    get() {
      return value;
    }
  })
}

setConst('name','jack');
name = 'rose';
//Uncaught Error: Assignment to constant variable name
```

# 参考

1. [JS常量定义](http://www.cnblogs.com/dong-xu/p/6239199.html)
