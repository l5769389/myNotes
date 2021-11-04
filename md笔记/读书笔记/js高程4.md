[toc]



# 4.变量、作用域和内存

## 原始值和引用值

1. 原始值(primitive value):是最简单的数据。
   1. 按值访问
   2. Undefined、Null、boolean、number、string、symbol
2. 引用值(reference value):是多个值构成的对象。js不允许直接访问内存位置。
   1. 按引用访问。

<img src="https://tva1.sinaimg.cn/large/008eGmZEgy1gnwmi2880rj313y0qmtcl.jpg" alt="image-20210222211650568" style="zoom:50%;" />

### 传参是按值传递的

1. ```js
   function addTen(num) { 
     num += 10; 
     return num;
   }
   let count = 20;
   let result = addTen(count);
   console.log(count); // 20，没有变化
   console.log(result); // 30
   ```

2. ```js
   function setName(obj) {
     obj.name = "Nicholas";
     obj = new Object(); 
     obj.name = "Greg"; 	// 'Grey'
   }
   let person = new Object(); 
   setName(person); 
   console.log(person.name);// "Nicholas"
   ```



## 垃圾回收机制

1. 标记清理(mark-and-sweep)
2. 引用计数(reference counting)



# 集合引用类型

## Object

1. 创建方式：**对象字面量(object literal)**

2. 访问属性：

   1. `person.name`
   2. `person['name']` 这种方式可以使用变量访问属性。

3. 属性的类型

   1. 数据属性：通过`Object.defineProperty()修改`

      1. `[[configurable]]`：是否可以通过delete删除并重新定义
      2. `[[Enmerable]]`:是否可以通过for-in循环。通常都可以
      3. `[[Writable]]`
      4. `[[Value]]`

      ```js
      let person ={};
      Object.defineProperty(person,'name',{
        writable:false,
        value:'Nick'
      })
      ```

      

   2. 访问器属性 :**不能直接定义，必须通过Object.defineProperty()**

      1. `[[configurable]]`
      2. `[[Enmerable]]`
      3. `[[Get]]`
      4. `[[Set]]`

      ```js
      let book={
          year_:2017,
          edition:1,
      };
      Object.defineProperty(book,'year',{
              get:function (){
                  return this.year_
              },
              set:function (newVal){
                  if(newVal >2017){
                      this.year_ = newVal;
                      this.edition = newVal -2017 +1;
                  }
              }
      })
      
      book.year =2018;
      console.log(book.edition) //2
      
      let descriptor = Object.getOwnPropertyDescriptor(book,'year_');
      descriptor.year_ // 2017
      descriptor.configurable //false
      ```

      

### 属性的API

1. 定义单个属性:`Object.defineProperty()`

2. 定义多个属性:`Object.defineProperties()`

3. 读取属性的描述符：`Object.getOwnPropertyDescriptor()`如上例中：

4. 对象相等判断：`Object.is()`

   1. 解决了： `+0 === -0` 相等 ，`NaN === NaN` false的情况.
   2. `Object.is(NaN,NaN)// true` 

5. `Object.assign(dest,src1,src2)`：

   1. src中可枚举属性 和 自有属性(`Object.hasOwnProperty() `为true)
   2. 修改dest对象。同时方法也返回该对象。
   3. 浅拷贝
   4. 会覆盖
   5. 不能复制函数
   6. 如果出错则是部分复制，不会回退。

   

   

## 对象语法增强

1. 可计算属性:

   ```js
   const nameKey ='name'
   let Person ={
       [nameKey]:'smart'
   }
   console.log(Person);
   ```

2. 简写方法名：

   ```js
   let person = {
     sayName(name) {
       console.log(`My name is ${name}`);
     }
   };
   ```

3. 解构赋值：

   ```js
   let person ={
       name:'mart',
       age:27
   }
   // 没有的属性为undefined
   const {name,job} =person;
   console.log(job);  // undefined
   
   //防止没有设置默认值
   const {name,job= 'se'} =person;
   console.log(job);  // 'se'
   
   //嵌套解构,注意只解构了：title
   let person ={
       name:'mart',
       age:27,
       job:{
           title:'se'
       }
   }
   const {job:{title}} =person;
   console.log(title) 
   
   ```

   



## 创建对象

> class = 构造函数+原型继承



### 工厂模式

> 函数中创建新对象，将形参赋值给新对象。返回新对象。

```js
function createPerson(name,age,job){
  let o = new Object();
  o.name =name;
  o.age =age;
  o.job =job;
  o.sayName=function(){
    console.log(this.name)
  }
  return o;
}
```

**缺点：因为在函数内创建了o，所以工厂模式创建的对象不好确定新创建对象的类型**



### 构造函数模式

> 构造函数和工程模式几乎一致
>
> **构造函数和普通函数的区别就是是否使用new调用。**

```js
function Person(name,age,job){
  this.name =name;
  this.age =age;
  this.job =job;
  this.sayName=function(){
    console.log(this.name)
  }
}

let person = new Person();
let person = new Person;  //都可以
```

**缺点在于：构造函数中的方法，在每一个实例中都创建了一遍**，为了解决这个办法就出现了原型模式



#### new的时候做了啥

1. 内存中新建了一个对象
2. 新对象的`[[Prototype]]`指向构造函数的`[[Prototype]]`
3. 构造函数中的this指向新对象。
4. 执行构造函数的代码（给新对象添加属性）
5. 如果构造函数返回非空对象，则返回该对象；否则返回上面的新对象。



### 原型模式

### 原型

<img src="http://ww2.sinaimg.cn/large/006tNc79ly1g5zbzyrslqj30xw0u0qfs.jpg" alt="image-20190814172409847" style="zoom:50%;" />





### 属性枚举

**只要通过对象可以访问，就可以用in ， hasOwnProperty() 只能访问在实例上的属性**

1. `Object.keys()  和 for-in`返回属性无序
2. `Object.assign()`有序





# Class

## 基础概念

实例的原始值属性每个实例共有一份，引用值属性各有一份。

**this的引用属性是每个实例独有的，类块中的内容是类的原型上的，所有实例共有**

```js
class Person{
    constructor() {
        this.name ='123';
        this.name1=new String('234');
        this.arr=[1,2]
    }
  //类块中定义在原型上。
    say(){
        console.log('666')
    }
}
const p1 =new Person();
const p2 =new Person();

//实例的原始值属性每个实例共有一份
console.log(p1.name ===p2.name) //true

//实例的引用值属性各有一份。
console.log(p1.name1 ===p2.name1) //false
console.log(p1.arr ===p2.arr) //false

console.log(p1.say ===p2.say) //true
person.prototype.say();
```



### 静态类方法

**适合用于做工厂方法**

```js
class Person {
    constructor(age) {
        this.age_ =age;
    }
    static create(){
        return new Person(Math.random()*100)
    }
}

console.log(Person.create()) //Person { age_: 64.57807964745896 }

```





## 继承

```js
class Vehicle {
    identifyProptype(id){
        console.log(id,this)
    }
    static identifyClass(id){
        console.log(id,this)
    }
}
const v =new Vehicle();
v.identifyProptype('v');  //v Vehicle {}
Vehicle.identifyClass('v');  // v [class Vehicle]

class Bus extends Vehicle{}
const b =new Bus();
b.identifyProptype('b'); // b Bus {}
Bus.identifyClass('b');  //b [class Bus extends Vehicle]

```



### super

1. 在派生类的构造函数中调用父类的构造函数
2. 在派生类的静态方法中调用父类的静态方法

```js
class Vehicle{
   constructor() {
        this.name='123'
    }
    static say(){
        console.log('v')
    }
}

class Bus extends Vehicle{
    constructor() {
        // super之前不能引用this
        super();
      //	super()将调用父类的构造函数返回的实例赋值给this.
        console.log(this) //  Bus { name: '123' }
    }
    static speak(){
        super.say();
    }
}
new Bus();
```



注意：

* super只能在派生类的**构造函数和静态方法**中使用。
* 要么调用父类的构造函数，要么引用静态方法。
* super()将调用父类的构造函数返回的实例赋值给this。派生类自动获得了父类constructor中的属性。





###  抽象基类



### 继承内置类型

注意如下：

```js
class MyArray extends Array{
    shuffle(){}
}
let a = new MyArray(1,2,3,4);
//filter应该返回的是数组类型的实例。但是默认情况下返回的类型和原始实例是一样的。
const odd =a.filter(x=>!!(x%2));

console.log(a instanceof Array) //true
console.log(a instanceof MyArray) // true
console.log(odd instanceof MyArray) // true
```



**如果不想返回的类型和原始类型一样，可以覆盖`Sybmol.species`访问器**,详情参考本书263页。





### 类混入（mixin）

> 组合胜过继承。

```js
class Vehicle {

}
const BazMixin =(superClass)=>class extends  superClass {
    baz(){
        console.log('baz')
    }
}
class Bus extends BazMixin(Vehicle){}

const b =new Bus();
b.baz();
```









# 第三章

## 3.3 var、let、const

1. let范围是块级作用域，var是函数作用域。
   1. 这也就是为什么for循环采用var会渗透出去。
   2. let在循环中，js会在每次迭代中声明一个新的迭代变量。
2. let有暂时性死区，即不会在作用域中被提升。
3. 在全局作用域内var声明的变量会挂载在window对象下，let不会。
4. const声明变量必须同时初始化，不能修改const声明的变量。剩下与let一样。



## 3.4 数据类型

### undefined

1. let var声明但未初始化就是相当于赋值undefined。
2. 对于未声明的变量只能调用typeof不会报错。typeof的结果为undefined。



### null

1. 要使用null为保存对象的变量初始化。

   ```js
   let message=null;
   if(!message){
       //执行
   }
   ```

   

### Boolean

注意：

1. 非空字符串==> true， ’‘ ==>false
2. 对象中，任意对象都是true。只有 null是false。**注意{}仍是true**



### Number

#### NaN

1. 任何涉及NaN的操作返回都是NaN
2. NaN 不等于任何值，包括NaN
3. **isNaN函数会尝试将参数转为数值，任何能转为数值的都会返回false。不能转为数值的如“abc”则返回true**



#### 数值转换

1. 任何数据类型转为数值：`Number()`
   1. true:1,false:0
   2. **null：0   Number(null) // 0**
   3. **undefined：NaN   Number(undefined) //NaN**
   4. **string：从头开始尝试转换,空字符串返回0，如果不能转换则返回NaN**
   5. 对象：先调用valueOf尝试转换，如果是NaN，再用toString尝试转换。
2. String=>数值：`parseInt()、parseFloat()`
   1. parseInt：
      1. 如果第一个不是数值/加减号,那么(包括空字符串)立即返回NaN。
         1. ’123blue‘ ==> 1234
         2. ’22.5‘=>22  小数点不是有效整数字符。
      2. 需要传入第二个参数来告知如何解析。
   2. parseFloat和parseInt类似，不同之处在于：
      1. 第二次遇到的小数点就无效了。
      2. 忽略字符串开头0。
      3. 只能解析十进制数，遇到16进制直接返回0：parseFloat('0xA') // 0





### 字符串

1. 字符字面量，如：\n，\t这种。
2. 字符串都是**不可变（immutable）**的，每次修改都是先销毁原来，再将新的保存到该变量。

#### 模板字面量

##### 标签函数

形如：`simpleTag`${a}+${b} =${a+b}` `

实际上传入`simpleTag()`函数的参数有四个：

1. 被${}分割的部分，也就是说有n个${} 第一个参数就是一个长n+1的数组。
2. 后面参数依次是${}中的值。



**可以通过剩余操作符收集标签函数中的参数**

```js
function simpleTag(strings,...expressions){}
```



##### 原始字符串

**String.raw 获取原始字符串，也就是Unicode字符或者换行符不转义**

```js
console.log(`\u00A9`); // ©
console.log(String.raw`\u00A9`); // \u00A9
```





### Symbol 类型

> 幂等性：`f(f(x)) =f(x)`

1. 是原始类型，所以可以用typeof判断类型

   ```js
   let sym = Symbol();
   typeof sym // symbol
   ```

2. 可以传入一个符号描述(String)，但是符号描述和符号无关，仅用来调试使用

   ```js
   let sym1 =Symbol();
   let sym2 =Symbol();
   sym1 == sym2 //false
   ```

   ```js
   let sym1 =Symbol('1');
   let sym2 =Symbol('1');
   sym1 == sym2 //false
   ```

3. 不能用new新建，为了避免和Boolean 、String、Number一样 使用new 就生成了符号包装对象

   ```js
   let b= Boolean();
   console.log(typeof b) //boolean
   let c =new Boolean();
   console.log(typeof c) //object
   ```

   如果想生成符号包装对象，可以通过`Object()`函数

   ```js
   let sym =Symbol();
   let wrapperSym =Object(sym);
   typeof wrapperSym // object
   ```

   

#### 全局符号注册表

> 重用和共享符号实例。

1. `Symbol.for() `和`Symbol.keyFor()`
   1. `Symbol.for() ` 接收符号描述 生成符号。**符号描述存在则返回相同的符号。（其实就是为了可以复用）**
   2. `Symbol.keyFor()`接收符号查询对应符号描述。没查到返回undefined。
2. `Symbol.for() `和`Symbol()`即使传入相同的符号描述，生成的符号也是不同的。







### Object类型

## 操作符

### +、-

一元运算符（+、-）放在非数值前面时，会先执行类型转换。同前面数值转换时，`Number()`一样。



### 位操作符

1. ECMAScript中所有数值都是64位存储。
2. **位操作不是直接操作64位，而是先把数转为32位，位操作，之后转为64位**
3. NaN 和Infinity在位操作中都被当成0处理了。

按位非

```js
~num1
//相当于
-num1-1;
```

左移：<<

有符号右移：>>

符号都会保留。 

无符号右移: >>>



### 布尔操作符

`!!`相当于转型函数`Boolean()`



### 指数操作符

`Math.pow()` ==` ** `

和`+=`类似也有 `**=`



### 标签语句

通常和标签嵌套连用。

```js
start: for (let i = 0; i < count; i++) {
console.log(i);
}
```





# 四、变量作用域和内存

## 原始值和引用值

1. 原始值：undefined、null、boolean、number、string、symbol
   1. 新建只能直接赋值，不能用new。用new则会生成一个Object类型实例。
2. 引用值： Object









# 二十五、 客户端存储



![image-20210927125352153](/Users/ljun/Documents/myNotes/读书笔记/C:\Users\msze0088\AppData\Roaming\Typora\typora-user-images\image-20210927125352153.png)







## cookie

1. 名称、值、域、路径、过期时间、安全标志。
2. 读写：
   1. 读取：`decodeURIComponent( document.cookie)`
   2. 写：`document.cookie =encodeURIComponent('name')+'='+encodeURICompnent('a')`
3. HTTP-only cookie 。客户端和服务端都能设置，只能服务端读取。防止攻击。



## localStorage 和sessionStorage

 

# indexedDB

```js
const request = indexedDB.open('dbName',version)
// 会触发upgradeneeded事件。
request.onerror=function(e){
    //e.target
}
request.onsuccess=function(e){
    //e.target
}
```

### 事务

* 原子性：Atomicity

* 一致性：Consitency

* 隔离性：Isolation

* 持久性：Durability

  

|                               | 脏读 | 不可重复读 | 幻读 |
| ----------------------------- | ---- | ---------- | ---- |
| read uncommitted              | 1    | 1          | 1    |
| read committed （大多数默认） | x    | 1          | 1    |
| repeatable read (mysql 默认)  | x    | x          | 1    |
| serializable                  | x    | x          | x    |
|                               |      |            |      |



七种传播行为。



```js
							   //对象存储名称  //权限	
let transaction  =db.transaction('users','readwrite');
const store =transaction.objectStore('users');
const request =store.get('007')
request.onerror=function(e){
    //e.target
}
request.onsuccess=function(e){
    //e.target
}

//事务可以完成多个请求。事务本身也有事件处理程序。
transaction.onerror =function
transaction.oncomplete=function
```



### 游标

游标使用时会对行加锁，可能会影响其他业务的正常进行。而且，数据量大时其效率也较低效。另外，内存也是其中一个限制。 因为游标其实是相当于把磁盘数据整体放入了内存中，如果游标数据量大则会造成内存不足，所以，在数据量小时才使用游标.

