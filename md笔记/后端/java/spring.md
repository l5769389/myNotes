# spring

## 引入

### 程序耦合

#### 例子1：jdbc

```java
//  DriverManager.registerDriver(new com.mysql.jdbc.Driver()); 
    Class.forName("com.mysql.jdbc.Driver");
    Connection connection =        DriverManager.getConnection("jdbc:mysql://localhost:3306/javaTable","root","123456");
                PreparedStatement preparedStatement =connection.prepareStatement("select * from account");
                ResultSet resultSet =preparedStatement.executeQuery();
                while (resultSet.next()){
                    System.out.println(resultSet.getString("name"));
                }
```



1. ` DriverManager.registerDriver(new com.mysql.jdbc.Driver()); ` 耦合
2. 以反射的方式引入：`Class.forName("com.mysql.jdbc.Driver")`
3. 引入内容处理为配置文件。



#### 例子2：三层架构

三层架构中：View层 new Service层， Service层 new Dao层。



### 解决方案

#### 工厂类和配置文件

1. 读取配置文件：

   ```java
   properties =new Properties();
                   InputStream inputStream = BeanFactory.class.getClassLoader().getResourceAsStream("bean.properties");
                   properties.load(inputStream);
   ```

2. 返回bean

   ```java
   String beanPath = properties.getProperty(beanName);
   bean =Class.forName(beanPath).getDeclaredConstructor().newInstance();
   ```

   

### 修改为单例的

上面返回的bean每次创建会生成一个新的实例。下面通过工厂模式修改为单例的：

重点在于HashMap的操作：

```java
 private static Map<String,Object> beansMap =new HashMap<>();
                Enumeration<Object> keys =properties.keys(); // 获取keys
// 遍历
                while (keys.hasMoreElements()){
                    String key =keys.nextElement().toString();
                    String beanPath =properties.getProperty(key);
                    Object bean =Class.forName(beanPath).getDeclaredConstructor().newInstance();
                    beansMap.put(key,bean);
                }
```









# SPRING IOC

降低程序间的耦合。

基础依赖：

```xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
```

会自动引入spring-jcl包，这其实是一个log模块。



ApplicationContext 和 beanFactory

1. ApplicationContext 采用**立即加载**的方式，只要读取完配置文件立即创建对象。单例对象适用，常用。

2. beanFactory采用的是**延迟加载**的方式，使用时才创建对象。多例对象适用。





ApplicationContext的三个常用实现类：

1. ClassPathXmlApplicationContext : 加载类路径下的配置文件
2. FileSystemApplicationContext：磁盘任意路径，要有访问权限
3. AnnotationConfigApplicationContext: 读取注解





### spring 创建bean对象的方式

1. 通过默认构造函数。如下，如果类中没有默认构造函数则无法创建。

   ```xml
   <bean id="xxx" class="xxx.xxx"></bean>
   ```

2. 使用某个类(factory-bean)中的方法(factory-method)创建对象，并存入spring容器。

   ```xml
   <bean id="xxx" factory-bean="xxxx"   factory-method="xxxx"></bean>
   ```

3. 使用某个类中的静态方法创建对象并存入spring。

   ```xml
   <bean id="xxx" class="xxx.xxx" factory-method="xxxx"></bean>
   ```

   



### bean的作用范围

bean标签的scope属性：

1. singleton:单例，默认值。
2. prototype: 多例的。
3. request：web应用的请求范围。
4. session：web应用的会话范围。
5. global-session：集群web应用的会话范围。





### bean 的生命周期

单例对象：和容器一样。

 	1. 创建：容器创建，bean创建
 	2. 存在：容器存在，bean存在
 	3. 销毁：容器销毁，bean销毁。

多例对象：

1.  创建：使用时创建
2. 存在：容器存在，bean存在
3. 销毁：垃圾回收。





### spring的依赖注入（DI）

能注入的数据有三类：

1. 基本类型和String

2. 其他类型bean：

   ```xml
   <bean id="now" class="java.util.Date"></bean>
   ```

3. 复杂类型/集合类型

   ```java
   private String[] string;
   private Map<String,String> map;
   private Properties props;
   ```

   



注入的方式：

1. 使用构造函数提供: `constructor-arg`

   ```xml
   <bean id="xxx" class="xxx.xxx">
   	<constructor-arg></constructor-arg>
   </bean>
   ```

     ```xml
     <bean id="xxx" class="xxx.xxx">
     	<constructor-arg name="birthday" ref="now"></constructor-arg>
     </bean>
     ```

   `constructor-arg`:

    * type:	指定要注入数据的类型，也就是构造函数中的类型。
    * index： 
    * **name：指定给构造函数中的指定名称的参数赋值**
    * value: 用于给基本类型和String类型赋值
    * ref：其他的bean类型。也就是下面的2、3。

   * 优点：注入数据时必须的。否则构造函数无法执行

   * 缺点：用不到的数据也必须注入。

2. **使用set方法提供**: `property`

   ```xml
   <bean id="xxx" class="xxx.xxx">
   	<property name="name" value="aaa"></constructor-arg> // 基础类型
       <property age="age" ref="now"></constructor-arg>   //其他bean
       <array>    // 复杂类型
       	<value>aaa</value>
           <value>aaa</value>
           <value>aaa</value>
       </array>
   </bean>
   ```

   * 优点：创建对象可以使用默认的构造函数。
   * 缺点：有必须的成员必须有值时，不能确保有值。

3. 使用注解提供

   





### 注解

注解的作用：

1. **创建对象：和xml的<bean>一样。**

   * @Component 把当前类存入spring容器中。属性value用于指定bean的id。不写默认是类名首字母小写。
   * @Controller、@Service 、 @Repository  作用和属性与@Component一样。是spring为三层模型专门提供的。

2. **注入数据：<bean>的propety标签一样。**

   * @Autowired： 自动按照类型注入，如果容器中有唯一一个bean对象和要注入的类型匹配，就注入。
     * 出现位置可以变量、方法上。
     * 多个匹配的情况下：

   ```java
   @Autowired
   private IAccountDao accountDao =null;
   ```

      如果Spring的IOC容器中，有两个类型为IAccountDao的实例，那么接下来会根据accountDao的名称尝试确定。* 

   *  @Qualifier:按照类型注入的基础上，再按照名称注入
     * 给类成员注入不能单独使用要和@Autowired配合，给方法参数注入时可以。
     * 属性value，注明注入的id。

   * @Resource：直接指定bean的id，可以单独使用
     * 属性name：注入的id

   * @value 注入基本类型和string类型
     * 属性value：指定数据值，可以使用spring的spEL

3. **改变作用范围：<bean>中的scope属性一样。**

   * @Scope
     * 属性：value，指定取值。

4. **生命周期相关：<bean>中`init-method` 和`destory-method`一样。**
   * @preDestory
   * @postDestory





## IOC 案例 ： 单元测试

