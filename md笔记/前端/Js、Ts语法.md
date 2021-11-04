# TS

## 基础类型

### boolean、number、string

```tsx
let isDone:boolean  =false;
let decLiteral: number =6;
let name: string = `Gene`;
```



### 数组

```tsx
let list:number[] =[1,2,3];

//数组泛型
let list1:Array<number> =[1,2,3]
```





### 元组Tuple

> **表示一个已知数量和元素类型的数组。**

<img src="https://tva1.sinaimg.cn/large/008i3skNgy1grt3vhypauj30li0dydhu.jpg" alt="image-20210624093703404" style="zoom:50%;" />



### 枚举

```tsx
enum Color {Red,Green,Blue}
let c:Color =Color.Blue 
let cName =Color[2];  //blue
```

可以为第一项添加一个起始序列:

```tsx
enum Color {Red =1,Green,Blue}
```



### Any、Void、Null、undefined

默认情况下null 和undefined 是所有类型的子类。也就是说可以把null和undefined赋值给number类。

当指定`--strictNullChecks`标记，null和undefined只能赋值给void和它们各自。





### Never

* never类型是任何类型的子类型，也可以赋值给任何类型；

* 没有类型是`never`的子类型或可以赋值给`never`类型（除了`never`本身之外）。 即使 `any`也不可以赋值给never。

```tsx
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```



### Object



## 类型断言

> 你清楚的知道一个实体的确切类型.

以下两种方式：**推荐as**。

```tsx
let someValue: any ='this is a  string';
let strLength: number =(<string>someValue).length;
let strLength1:number =(someValue as string).length;
```





## 接口

