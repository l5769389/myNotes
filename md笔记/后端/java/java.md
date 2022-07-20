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





# 基础

## 继承

主要解决： **共性抽取**

将相同的一些性质，抽取到更高级别的类中。



**子类调用的如果是父类中的方法，那么实际的作用域也会在父类中。**

```java
public class F {
    int a =3;
  public void getF(){
      System.out.println(a);
    }
}

public class S extends  F {
    int a =4;
    public void getS(){
        System.out.println(a);
    }
}

@Test
public void testEnum(){
    S s = new S();
    s.getF(); // 3
}
```



局部变量、成员变量、父类变量。

```java
    public class F {
        int a =3;
      public void getF(){
          System.out.println(a);
        }
    }

    public class S extends  F {
        int a =4;
        public void getS(){
            int a =5;
            System.out.println(a); //5 
            System.out.println(this.a); // 4
            System.out.println(super.a); //3
        }
    }

    @Test
    public void testEnum(){
        S s = new S();
        s.getS();
    }
```



### 继承中的构造器

1. 先调用父类中的构造器，然后调用自身的
2. 其实子类的构造方法中默认隐含了一个”super()“调用。  如果父类没有无参构造器，通过super(args);
3. 子类中的super()必须是第一行。

```java
    public class F {
        public F() {
            System.out.println("f");
        }
    }

    public class S extends  F {
        public S() {
          // super();
            System.out.println("s");
        }
    }
    @Test
    public void testEnum(){
        S s = new S();  // f 然后 s
    }
```





## 抽象类

```java
public abstract class Animal{
   public abstract void eat()
}
```

1. **不能直接new 一个抽象类对象。**比如不能直接new 一个动物。而应该用一个具体的动物（如猫）去继承了Animal之后，才能被创建。

2. **抽象类中不一定包括抽象方法。** 有抽象方法的一定是抽象类。
3. **抽象类的子类必须覆盖重写所有抽象方法，否则子类也要是抽象类。**





## 接口

**一种公共标准**

接口中可以包含：

jdk7： 

1. 常量， 必须要用pulic static final 修饰
2. 抽象方法，接口中的修饰符关键字必须是：public abstract  ，可以省略。

jdk8：

3. 默认方法，为了解决接口升级的问题。 接口A中原先有一个抽象方法，然后又B、C两个实现类。现在要在接口A中新添加一个抽象方法，那么BC必然都报错。

3. 静态方法

jd9：

5. 私有方法，解决默认方法共用的部分。(普通私有方法、普通静态方法)

```java
public interface interfaceName{
   void methodA();  //public abstract void methodA();
  default void methodB(){  // 默认方法。也可以被覆盖。
 			//
  }
  static void methodC(){}   //静态方法
  private void methodD(){}  // 私有方法
  int num = 10 // public static final int num =10;
}
```







## 集合

collection接口

* List接口
  * ArrayList
  * LinkedList
  * Vector
* Set接口



### 方法



#### add

#### clear

#### remove

#### contains

#### isEmpty

#### size

#### toArray转为数组





### Iterator

* next() 返回迭代的下一个元素
* hasNext()  仍有下一个元素返回true



```java
        ArrayList<String> list = new ArrayList<>();
        list.add("1");
        list.add("2");
        list.add("3");

        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()){
            String next = iterator.next();
            System.out.println(next);
        }
```





### 增强for

```java
        ArrayList<String> list = new ArrayList<>();
        list.add("1");
        list.add("2");
        list.add("3");

        for (String s : list) {
            System.out.println(s);
        }
```







## 泛型

E:未知的数据类型。

```java
public class ArrayList<E>{
  public E get(int index){}
}
```



之所以要使用泛型，其实是对数据的一种约束。防止出错。

```java
        ArrayList list = new ArrayList();
        list.add(1);
        list.add("234");
        Iterator iterator = list.iterator();
        while (iterator.hasNext()){
            Object next = iterator.next();
            System.out.println(next); // Object类型，如果强转又会报错
        }
```



















# 常见概念

## jar和war包的区别

1. jar包可以理解为java打成的包，war包为javaweb打成的包（一个完整的web工程）。
2. 把war包放在tomcat目录的webapp下，tomcat服务器在启动的时候可以直接使用这个war包。

## tomcat和nginx的区别

[tomcat 与 nginx，apache的区别是什么？](https://www.zhihu.com/question/32212996)

1. tomcat，**Application Server**  是一个运行在JVM之上，用来管理servlet，返回动态资源。是一个Servlet/JSP应用的容器。作为一个应用的执行容器需要支持开发语言的Runtime（java），其主要保证的是应用相关的规范如：类库、安全等。
2. nginx，**Http Server**，其主要作用是在HTTP协议层面的控制，如代理、负载均衡等，它要保证的是将内容如实的反应给客户端。









# 流

## 字节流

### IO内存输出流

#### 写一个字节

```java
FileOutputStream fileOutputStream = new FileOutputStream("./a.txt");
fileOutputStream.write(97);
fileOutputStream.close();
```

0~127会查找ascii码表。

剩余部分查找系统默认码表。



#### 写多个字节

```java
FileOutputStream fileOutputStream = new FileOutputStream(new File("./b.txt"));
byte[] bytes = {65,66,67,68,69}; //ABCDE
byte[] bytes1 ={-65,-66,-67,68,69}; //烤紻E
fileOutputStream.write(bytes1);
fileOutputStream.close();
```

第一个字节是0~127. 查询ASCII表

第一个字节是负数，那么两个字节一起查询系统默认表（如：GBK），然后最后剩下的69还是查询ASCII表。



#### 加入偏移

偏移1，长度2。

```java
fileOutputStream.write(bytes,1,2); //BC
```



#### 写入字符

**String.getBytes()**

```java
            FileOutputStream fileOutputStream = new FileOutputStream(new File("./b.txt"));
            byte[] bytes = "啦啦啦".getBytes();
            fileOutputStream.write(bytes);
            fileOutputStream.close();
```





#### 文件续写

```java
new FileOutputStream("./b.txt",true);
```

```java
FileOutputStream fileOutputStream = new FileOutputStream("./b.txt",true);
byte[] bytes = "啦啦啦".getBytes();
fileOutputStream.write(bytes);
fileOutputStream.close();
```





#### 文件换行

```java
"\r\n".getBytes()
```







### IO内存输入流

#### 循环读完文件

```java
FileInputStream fileInputStream = new FileInputStream("./b.txt");
int current;
while ((current = fileInputStream.read()) != -1){
    System.out.println(current);
}
fileInputStream.close();
```





#### 一次读取多个字节

1. `fis.read(bytes)` 返回实际读取的长度。
2. `new String()`将bytes转为String

![image-20220703203700920](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220703203700920.png)







## 字符流

### 字符输入流Reader

每次读取一个字符，就避免了中文一个字符对应2-4个字节时的乱码问题。

```java
FileReader fileReader = new FileReader("./b.txt");
int current;
while ((current =fileReader.read()) !=-1){
    System.out.print((char) current);
}
```



### 字符输出流Writer

```java
FileWriter fileWriter = new FileWriter("./b.txt");
fileWriter.write("96");
fileWriter.flush();
```





## 异常处理

```js
try(定义可能异常的对象){
  
}catch(){}
```

jdk7中：

![image-20220704090820275](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220704090820275.png)



jdk9中：

![image-20220704091115756](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220704091115756.png)





## Properties集合

* 是一个持久的属性集，K和V都是字符串。被大量使用，如：`System.getProperties()`
* 是和IO流相结合



集合的操作方法：

```java
        Properties properties = new Properties();
        properties.setProperty("x","1");
        properties.setProperty("y","2");

        Set<String> set = properties.stringPropertyNames();
        for (String item : set){
            System.out.println(properties.getProperty(item));
        }
```



### 持久化

`store`方法。

要结合：字符流、或者字节流使用。

**如果字节流不要存中文。**

![image-20220704093236157](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220704093236157.png)





### 读取





# lambda

线程runable：

```java
class ImgRunnable implements Runnable{
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName() +"run");
        }
    }
    
    @Test
    public void testEnum(){
        ImgRunnable imgRunnable = new ImgRunnable();
        Thread thread = new Thread(imgRunnable);
        thread.start();
    }
```



用匿名内部类来简化：

```java
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName() + "run");
            }
        });
        thread.start();
```



使用lambda来简化：

```java
Thread thread = new Thread(
  ()-> System.out.println(Thread.currentThread().getName() + "run")
);

```



凡是可以根据上下文推导出来的内容可以省略(和箭头函数类似)：







## ::

[::表达式](https://blog.csdn.net/zhoufanyang_china/article/details/87798829)

```java
String[] arr = {"a","b","c"};
List<String> stringList = Arrays.asList(arr);
stringList.forEach(System.out::println);
```







# 函数接口

## 定义

> **有且只有一个抽象方法的接口**，接口中可以包含其他的方法(默认、静态、私有)

`@FunctionalInterface`注解。



## 使用





# stream流

![image-20220713193320380](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220713193320380.png)

在上图中，filter、map、skip都是对函数模型进行操作。集合元素没有被处理，只有当终结方法count执行时，整个模型才会执行。

有如下两个特点：

1. pipeLine 中操作都会返回流本身。
2. 内部迭代。



## 获取流

### collection.stream()

```java
        ArrayList<Object> list = new ArrayList<>();
        Stream<Object> stream = list.stream(); // 数组

        HashSet<Object> objects = new HashSet<>();  // set
        objects.stream();


        // k
        Set<String> keySet = hashMap.keySet();    //map
        
        // v
        Collection<String> values = hashMap.values();
        
        // k-v
        Set<Map.Entry<String, String>> entries = hashMap.entrySet();
```

总的来说 map ->set 然后来获取流。





### Stream.of()

```java
Stream.of(1,2,3);
Integer[] arr ={1,2,3};
Stream.of(arr);
```





## 常用方法

* 延迟方法，返回值仍是 stream对象，可以链式调用
* 终结方法： 返回值不再是stream对象。 count 和forEach



### forEach

**遍历+ 终结**

1. 接收一个Consumer接口函数。

```java
Stream.of("1","2","3").forEach(item -> System.out.println(item));
```





### filer

**过滤为子集合流**

```java
long count = Stream.of("1", "2", "3").filter(item -> item != "2").count();
System.out.println(count);  //2
```





###  map

```java
Stream.of("1", "2", "3").map(item -> Integer.parseInt(item))
                        .forEach(item -> System.out.println(item));  //  1 2 3
```



### count

`long count()`



### 截取

#### 取用前几个：limit

`Stream<T> limit(long maxSize)`



#### 跳过前几个：skip

如果3个元素，跳过3个，那么返回一个长度为0的流。





### 组合

#### concat

```java
        Stream<String> stream = Stream.of("1");
        Stream<String> stream1 = Stream.of("2");
        Stream.concat(stream, stream1)
                .forEach(item-> System.out.println(item));
```







