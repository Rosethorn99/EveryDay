**Reflect** 是一个内置的对象，它提供拦截 JavaScript 操作的方法。这些方法与 [proxy handler (en-US)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy "Currently only available in English (US)") 的方法相同。`Reflect` 不是一个函数对象，因此它是不可构造的。这是MDN上的解释.

Reflect也是ES6新增的一个API，它是一个对象，字面的意思是反射。

### 这个Reflect有什么用呢？

- 它主要提供了很多操作JavaScript对象的方法，有点像Object中操作对象的方法；

- 比如Reflect.getPrototypeOf(target)类似于 Object.getPrototypeOf()；

- 比如Reflect.defineProperty(target, propertyKey, attributes)类似于Object.defineProperty() ；

### 如果我们有Object可以做这些操作，那么为什么还需要有Reflect这样的新增对象呢？

- 这是因为在早期的ECMA规范中没有考虑到这种对 对象本身 的操作如何设计会更加规范，所以将这些API放到了Object上面；

- 但是Object作为一个构造函数，这些操作实际上放到它身上并不合适；

- 另外还包含一些类似于 in、delete操作符，让JS看起来是会有一些奇怪的；

- 所以在ES6中新增了Reflect，让我们这些操作都集中到了Reflect对象上；

### Object和Reflect对象之间的API关系，可以参考MDN文档：

- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/Comparing_Reflect_and_Object_methods

### Reflect的常见方法

Reflect中有哪些常见的方法呢？它和Proxy是一一对应的，也是13个：

#### Reflect.getPrototypeOf(target)   类似于 Object.getPrototypeOf()。

#### Reflect.setPrototypeOf(target, prototype)设置对象原型的函数. 返回一个 Boolean， 如果更新成功，则返回true。

#### Reflect.isExtensible(target)  类似于Object.isExtensible()

#### Reflect.preventExtensions(target)  类似于Object.preventExtensions()。返回一个Boolean。

#### Reflect.getOwnPropertyDescriptor(target, propertyKey)   类似于Object.getOwnPropertyDescriptor()。如果对象中存在该属性，则返回对应的属性描述符, 否则返回 undefined.

#### Reflect.defineProperty(target, propertyKey, attributes) 和 Object.defineProperty() 类似。如果设置成功就会返回 true

#### Reflect.ownKeys(target)返回一个包含所有自身属性（不包含继承属性）的数组。(类似于Object.keys(), 但不会受enumerable影响).

#### Reflect.has(target, propertyKey)判断一个对象是否存在某个属性，和 in 运算符 的功能完全相同。

#### Reflect.get(target, propertyKey[, receiver])获取对象身上某个属性的值，类似于 target[name]。

#### Reflect.set(target, propertyKey, value[, receiver])将值分配给属性的函数。返回一个Boolean，如果更新成功，则返回true。

#### Reflect.deleteProperty(target, propertyKey)作为函数的delete操作符，相当于执行 delete target[name]。

#### Reflect.apply(target, thisArgument, argumentsList)对一个函数进行调用操作，同时可以传入一个数组作为调用参数。和Function.prototype.apply() 功能类似。

#### Reflect.construct(target, argumentsList[, newTarget])对构造函数进行 new 操作，相当于执行 new target(...args)