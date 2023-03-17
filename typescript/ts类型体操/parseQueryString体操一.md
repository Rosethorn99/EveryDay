### 简单实现一个parseQueryString的方法

```js
function parseQueryString(queryStr) {
    if (!queryStr || !queryStr.length) {
        return {};
    }
    const queryObj = {};
    const items = queryStr.split('&');
    items.forEach(item => {
        const [key, value] = item.split('=');
        if (queryObj[key]) {
            if(Array.isArray(queryObj[key])) {
                queryObj[key].push(value);
            } else {
                queryObj[key] = [queryObj[key], value]
            }
        } else {
            queryObj[key] = value;
        }
    });
    return queryObj;
}
```

这段代码很容易看出来就是做 query string 的 parse 的，会把 'a=1&b=2&c=3' 的字符串 parse 成 { a: 1, b: 2, c: 3 } 返回。如果有同名的 key 的话，就合并到一个数组里。

JS 的逻辑大家写的比较多，这部分很容易理解：

![[Pasted image 20230317084234.png]]

那如果要给这个函数加上类型，大家会怎么加呢？

我猜，大部分人会这么加：

![[Pasted image 20230317084339.png]]

参数是 string 类型，返回值是 parse 之后的对象类型 object。

这样是可以的，而且 object 还可以写成 Record<string, any>，因为对象是索引类型（索引类型就是聚合多个元素的类型，比如对象、class、数组都是）。

![[Pasted image 20230317084409.png]]

Record 是 TS 内置的一个高级类型，是通过映射类型的语法来生成索引类型的：

```typescript
type Record<K extends string | number | symbol, T> = { 
    [P in K]: T;
}
```

比如传入 'a' | 'b' 作为 key，1 作为 value，就可以生成这样索引类型：

![[Pasted image 20230317084446.png]]

所以这里的 Record<string, any> 也就是 key 为 string 类型，value 为任意类型的索引类型，可以代替 object 来用，更加语义化一点：

![[Pasted image 20230317084512.png]]

但是不管是返回值类型为 object 还是 Record<string, any> 都存在一个问题：返回的对象不能提示出有哪些属性：

![[Pasted image 20230317084537.png]]

对于习惯了 ts 的提示的同学来说，没有提示太不爽了。怎么能让这个函数的返回的类型有提示呢？

这就要用到类型编程了。