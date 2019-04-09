# 04-Proxy

## 是什么？

Proxy 用于修改某些操作的默认行为。Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

```js
var proxy = new Proxy(target, handler);
```

- target参数表示所要拦截的目标对象
- handler参数也是一个配置对象，给每个被「代理」的操作定制拦截行为

比如，需要定制**对目标对象属性的访问行为**

```js
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

proxy.time // 35
proxy.name // 35
proxy.title // 35
```

如上，无论proxy后面跟什么属性名都返回35，因为被中间的拦截函数总返回35。

### get

```js
get(target, propKey, receiver)
```

拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。可以接受三个参数，依次为目标对象、属性名和 proxy 实例本身（严格地说，是操作行为所针对的对象），其中最后一个参数可选。


# 参考与拓展

1. [es6 javascript的Proxy 实例的方法](https://blog.csdn.net/qq_30100043/article/details/53443017)
2. [每日一题-proxy](https://zhuanlan.zhihu.com/p/22584733)
3. [vue双向数据绑定原理](https://juejin.im/post/5b86119a6fb9a019f928d82d)