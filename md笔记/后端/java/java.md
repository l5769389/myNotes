# 数据类型

![image-20220113162322302](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220113162322302.png)

JVM通常会把boolean表示为4B.

char 除了表示ASCII码 还可以表示Unicode字符。

```java
final double PI =3.14  // final表示常量
```

```java
var sb = new StringBuilder();
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





# 常见概念

## jar和war包的区别

1. jar包可以理解为java打成的包，war包为javaweb打成的包（一个完整的web工程）。
2. 把war包放在tomcat目录的webapp下，tomcat服务器在启动的时候可以直接使用这个war包。

## tomcat和nginx的区别

[tomcat 与 nginx，apache的区别是什么？](https://www.zhihu.com/question/32212996)

1. tomcat，**Application Server**  是一个运行在JVM之上，用来管理servlet，返回动态资源。是一个Servlet/JSP应用的容器。作为一个应用的执行容器需要支持开发语言的Runtime（java），其主要保证的是应用相关的规范如：类库、安全等。
2. nginx，**Http Server**，其主要作用是在HTTP协议层面的控制，如代理、负载均衡等，它要保证的是将内容如实的反应给客户端。
