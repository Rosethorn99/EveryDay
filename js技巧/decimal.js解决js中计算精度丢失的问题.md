在JavaScript中计算数值的过程中，很容易遇到`0.1+0.2!=0.3`的问题，其原因是数值由十进制转成双精度浮点数的二进制过程中会出现精度丢失导致的。

官方API文档：[地址](https://link.juejin.cn?target=http%3A%2F%2Fmikemcl.github.io%2Fdecimal.js%2F "http://mikemcl.github.io/decimal.js/")。

### 安装和引入

执行命令`npm i decimal.js --save`安装。

`import {Decimal} from 'decimal.js'`引入使用。

### 处理数值

在使用decimal.js计算数值前，要先把数值用`Decimal`处理一下，`Decimal`是个构造函数，处理后返回一个 `decimal`对象。

  ```arduino
const decimal_10 = new Decimal(10);
console.log(decimal_10)
复制代码
```

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/695a4bf66eeb46929eb3a624a0394ea0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

`Decimal`构造函数的参数可以是字符串也可以是数字，字符串只能是表示数字的字符串，如`'10'`，也可以传`NaN`或`Infinity`。

参数前缀为`0x`或`0X`时表示十六进制，为`0b`或`0B`时表示二进制，为/`0o`或 `0O`时表示八进制。当然这些不常用，了解一下即可。

特别注意要判断参数不能为`null`或`undefined`或空值，否会报错。

### 格式化数值

数值被处理后，不能直接展示到页面，得格式化一下。一般常用的由以下几种。

-   `toString()` 格式化为字符串。
    
-   `valueOf()` 格式化为字符串，但是有符号零 `console.log((new Decimal(-0)).valueOf()) // -0`。
    
-   `toNumber()` 格式化为Number类型，转换为原始数字的值。
    
-   `toFixed()` 格式化为字符串类型，用法和JS中`toFixed()`一样，不同的是decimal.js中的`toFixed()`有第二参数，可以设置舍入的类型。
    

还要一些不常用的

-   `toBinary()` 格式化为二进制。
    
-   `toHexadecimal()` 格式化为十六进制。
    
-   `toOctal()` 格式化为八进制。
    

### 加减乘除

在计算中最经常用到就是加减乘除，下面来看一下decimal.js中的加减乘除，有两种用法，一种是使用`Decimal`类的静态方法，一种是使用`Decimal`类实例方法。

-   加法

```ini
Decimal.add(1, 2); // 3

const x = new Decimal(1);
const result = x.plus(2); // 3
复制代码
```

-   减法

```ini
Decimal.sub(3, 1); // 2

const x = new Decimal(3);
const result = x.sub(1); // 2
复制代码
```

-   乘法

```ini
Decimal.mul(3, 2); // 6

const x = new Decimal(3);
const result = x.mul(2); // 6
复制代码
```

-   除法

```ini
Decimal.div(6, 2); // 3

const x = new Decimal(6);
const result = x.div(2); // 3
复制代码
```

**使用除法时候要注意 除数（分母）不能为0。**

### 取绝对值

```arduino
Decimal.abs(-3); // 3

const x = new Decimal(-3);
const result = x.abs(); // 3
复制代码
```

### 比较

-   大于

```ini
const x = new Decimal(2);
const y = new Decimal(1);
console.log(x.greaterThan(y)); // true
复制代码
```

-   大于等于

```ini
const x = new Decimal(2);
const y = new Decimal(2);
console.log(x.greaterThanOrEqualTo(y)); // true
复制代码
```

-   小于

```ini
const x = new Decimal(2);
const y = new Decimal(1);
console.log(y.lessThan(x)); // true
复制代码
```

-   小于等于

```ini
const x = new Decimal(2);
const y = new Decimal(2);
console.log(y.lessThanOrEqualTo(x)); // true
复制代码
```

-   等于

```vbnet
const x = new Decimal(2);
const y = new Decimal(2);
console.log(x.equals(y)); // true
复制代码
```

### 判断

-   是否是整数

```scss
const x = new Decimal(1) 
x.isInteger(); // true 
const y = new Decimal(123.456) 
y.isInt(); // false
复制代码
```

-   是否是正数

```ini
x = new Decimal(0);
x.isPositive();// true
y = new Decimal(-2);
y.isPos(); // false
复制代码
```

-   是否是负数

```ini
x = new Decimal(-0); 
x.isNegative(); // true 
y = new Decimal(2); 
y.isNeg(); // false
```

