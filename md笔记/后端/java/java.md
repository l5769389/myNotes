# 基础知识

## 安装

### JDK安装

https://blog.csdn.net/qq_44866828/article/details/118963681





## 基本类型

### 数据类型

#### 整形

0b1001 ，0b表示二进制

1_000_000 易读。

* byte :1字节
* short：2字节
* int： 4字节
* long： 8字节



#### 浮点型

* float 4字节
* **double 8字节** 常用



####  char型

#### 枚举类型

`enum Size{SMALL,MEDIUM}`



#### 字符串

**不可变字符串**

**使用：`s.equals(t)` 检测字符串是否相等**

== 检测字符串的位置是否相等。



检测既不是null又不是空

`(str !=null) && str.length()!=0`



##### StringBuilder类 构建字符串

```java
Stringbuilder builder =new Stringbuilder();
builder.append(str)
String str1 =builder.toString();  
```



#### 输入输出文件

输入：

`Scanner in =new Scanner(Paths.get("myfile.txt"),"UTF-8")`

输出：

`PrintWriter out =new PrintWriter("myfile.txt","UTF-8")`



### 数组

**大小不可变，其中元素可以变**

`int[] a = new int[100]`

初始化为0、false、null。



* 数组拷贝：
  * `Arrays.copyOf(arr,length)`: 值拷贝
  * `int[] arr1 =arr`: 引用拷贝。
* 多维数组
  * `double[][] arr`













# 常用API学习

1. Scanner类

获取用户输入

```java
import java.util.Scanner; 
Scanner scanner =new Scanner(System.in);
        System.out.println("请输入第一个数:");
        int num1 =scanner.nextInt();
			  String str =scanner.next();
```



2. 匿名对象类

   接收传参，或者返回一个对象。

```java
new Person().name="aaa";
new Person().showName() //null  新对象
```



3. Random类

   ```java
           Random random =new Random();
           System.out.println(random.nextInt());
   				System.out.println(random.nextInt(9)); //[0,9)
   ```

   

4. ArrayList 集合

   `<E>`：

   * 泛型，表示都是什么统一类型。
   * **只能是引用类型，不能是基本类型**。

   `ArrayList<String> list = new ArrayList<>();`

   ```java
   list.add(E e);  //有返回值，但是一定成功，对于其他集合来说。返回的是成功标识。
   list.get(int index)
   list.remove(int index)
   list.size()  
   ```

   

```java
// 向ArrayList中存入基本类型数据。要使用包装类。
```



5. String类

   * String的内容不可变。
   * 相当于char[]字符数组，底层是用byte[]字节数组
   * ==是对对象地址值比较。内容比较通过`equals()`

   

   常见的创建方式：

   * `public String()`
   * `public String(char[] array)`根据字符数组创建对应字符串:`char[] charArray={'A','B','C'}`
   * `public String(byte[] array)`根据字节数组创建 `byte[] byteArray ={97, 98,99}`

   

   * `String str = "hello"`

   

   **字符串常量池 ** : 程序中直接写上双引号的字符串就在常量池中。 

   

   

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfysevyo2nj322d0u0nmu.jpg" alt="image-20200620152951075" style="zoom:150%;" />

对于`String str1= “abc”`而言。栈中保存的是字符串常量池中字符串对象的地址。该常量池中存的也是byte[]。

对于`charArray而言`。直接存放在堆中`char[]`。当`new String(charArray)`时转为`byte[]`==》字符串对象。





String转换API：

* `public char[] toCharArray()`
* `byte[] getBytes()`
* `replace(oldString,newString)`

