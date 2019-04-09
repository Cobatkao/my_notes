# 08-Symbol

# 用途

- 作为常量
- 作为属性
- 半隐藏属性

## 作为常量

```javascript
// 虽然都是symbol，但是值都不一样
const java = Symbol()
const ruby = Symbol()
const perl = Symbol()
const php = Symbol()
const vb = STMBOL()

var lang = java

if (lang === java) {
  console.log('我是java')
}
if (lang === php) {
  console.log('我是php')
}
```

**Symbol 可以创建全局一个独一无二的值**，symbol括号内的值可以认为这个名字就是个注释。如下代码可以证明 Symbol 的名字与值无关：

```
var a1 = Symbol('a')
var a2 = Symbol('a')
a1 !== a2 // true
```

他真正的值是背后的hash。

## 作为属性

```
let s1 = Symbol('mysymbol')
let s2 = Symbol('mysymbol')

var obj = {}
obj[s1] = 'hello'
obj[s2] = 'world'

console.log(obj)
console.log(obj[s1])
console.log(obj[s2])
```

两个symbol作为属性看上去相等，但其实完全不相等。

## 半隐藏属性

```
const MYKEY = Symbol();
class User {
    constructor(key,name,age){
        this[MYKEY] = key;
        this.name = name;
        this.age = age;
    }
    checkKEY(key){
        return this[MYKEY] === key;
    }
}

let user = new User(123, 'Curry', 29);
console.log(user.name, user.age, user[MYKEY]);
console.log(user.checkKEY(123));   //true
console.log(user.checkKEY(456));   //false
console.log(Object.keys(user));    //[ 'name', 'age' ]
console.log(JSON.stringify(user)); //{"name":"Curry","age":29}
```

![Screen Shot 2018-11-08 at 10.25.39 AM.png](quiver-image-url/F372D57A58D97367D0EC46AA989145A4.png =434x119)

如上，当你知道指向Symbol的变量名，就可以直接访问symbol变量，但是无法通过Object.keys，JSON.stringify来将其遍历出来，也就是不可枚举的属性。