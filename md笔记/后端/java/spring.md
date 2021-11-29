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

