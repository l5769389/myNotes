

# 数值

## 二进制和八进制

```js
0b11  //3   二进制
0o11  //9   八进制

转为10进制
Number(0B11)
```





## 数值分割

```js
let a = 1_000
a === 1000 // true
```





## 方法

将全局`isNaN()、isFinite()、parseInt()、parseFloat()` 移植到Number对象上。

```js
Number.isFinite();  // 检查数值是否有限（不是Infinity） 只对数值有效
Number.isNaN();     // 是否为NaN、只对数值有效
```



```js
Number.isInteger() //判断是否为整数，如果精度很高，不要使用。
```

JavaScript 内部，整数和浮点数采用的是同样的储存方法，所以 25  和 25.0 被视为同一个值。都是整数。**JS中数值存储采用64位双精度，数值精度最多为53个二进制**



![image-20220119160444727](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220119160444727.png)





```js
Number.EPSILON //极小常量，实质是一个能够接受的误差范围。
Number.MIN_SAFE_INTEGER <= Number.isSafeInteger() <= Number.MAX_SAFE_INTEGER
```





## BigInt

```js
const a =1234n // BigInt 类型。
42n === 42 //false
typeof 123n // 'bigint'
```





# 函数

## 

## 参数默认值

### 和解构赋值使用

```js
function foo({x,y =5}){   //只使用了解构
    console.log(x,y);
}
foo(); // 因为没有参数，x、y无法赋值，报错，可以改成：

function foo({x,y =5}={}){  // {x,y =5} 为解构   ={} 为参数默认值.
    console.log(x,y);
}
```



### function.length

`length`属性的含义是，该函数预期传入的参数个数。 如果设置了参数默认值，那么就不再计入预期了。





























# class

![image-20210723105833085](https://tva1.sinaimg.cn/large/008i3skNgy1gsqp79ik5tj316k0okacr.jpg)

