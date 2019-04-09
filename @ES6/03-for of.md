# 03-for of

## for of的区别

ES2015 引入了 for..of 循环，它结合了 forEach 的简洁性和中断循环的能力

```javascript
for (const v of ['a', 'b', 'c']) {
  console.log(v);
}
// a b c

for (const [i, v] of ['a', 'b', 'c'].entries()) {
  console.log(i, v);
}
// 0 "a"
// 1 "b"
// 2 "c"
```

### 使用注意

1. 推荐在循环**对象属性**的时候，使用for...in，在**遍历数组**的时候的时候使用for...of来替代for循环。
2. for...in循环出的是可枚举属性—key，for...of循环出的是**可迭代对象的迭代值**—value。它的使用范围包括：

    1. 数组
    2. Set
    3. Map
    4. 类数组对象，如 arguments 对象、DOM NodeList 对象
    5. Generator 对象
    6. 字符串

### 对比for in

> JavaScript 原有的for...in循环，只能获得对象的键名，不能直接获取键值，所以它不适合遍历数组。ES6 提供for...of循环，允许遍历获得键值。

1. **使用范围**：for of只可以循环可迭代对象的可迭代属性值，不可迭代属性在循环中被忽略了。for...of循环可以使用的范围包括字符串、数组、Set、Map、类似数组对象（比如arguments对象、DOM NodeList 对象）
2. **遍历输出顺序**：for in遍厉数组无法保证输出值的顺序，而for of可以保证输出顺序，
3. for...of如果想要获得对象的键名或数组的索引，可以通过和Object.keys()搭配使用
4. 此外，作用于数组的for-in循环除了遍历数组元素以外,还会遍历自定义属性及原型对象上的属性。
5. **for...of的步骤**：for-of循环首先调用集合的Symbol.iterator方法，紧接着返回一个新的迭代器对象。迭代器对象可以是任意具有.next()方法的对象；for-of循环将重复调用这个方法，每次循环调用一次。举个例子，这段代码是我能想出来的最简单的迭代器：

    ```javascript
    var zeroesForeverIterator = {
     [Symbol.iterator]: function () {
       return this;
      },
      next: function () {
      return {done: false, value: 0};
     }
    };
    ```


### 普通对象使用for of

如果实在想用for...of来遍历普通对象的属性的话，可以通过和Object.keys()搭配使用，先获取对象的所有key的数组
```javascript
var student={
    name:'wujunchuan',
    age:22,
    locate:{
    country:'china',
    city:'xiamen',
    school:'XMUT'
    }
}
for(var key of Object.keys(student)){
    //使用Object.keys()方法获取对象key的数组：["name", "age", "locate"]
    console.log(key+": "+student[key]);
}
//name: wujunchuan
//age: 22
//locate: [object Object]
```

## for in缺点
* 数组的键名是数字，但是for...in循环是以字符串作为键名“0”、“1”、“2”等等。
* for...in循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
* 某些情况下，for...in循环会以任意顺序遍历键名。

总之，for...in循环主要是为遍历对象而设计的，不适用于遍历数组。

