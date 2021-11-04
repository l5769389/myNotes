# spring boot +mybatis

## demo

### 1.pom中添加依赖

### 2. application.properties添加sql配置

注意：learnJava对应的是sql中db的名称：

<img src="https://tva1.sinaimg.cn/large/008eGmZEgy1goyhds6iv4j30bk058aai.jpg" alt="image-20210327151112568" style="zoom:50%;" />

```properties
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/learnJava?characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=tthappy123
```



### 3. 新建一个对应数据库表的实例类

```java
public class Student {
    private int id;
    private String name;
  	// getter setter
}
```



### 4. 添加一个mapper接口是db和db操作的接口

@Select 语句也可以单独抽取。

```java
@Mapper
public interface StudentMapper {
    @Select("select * from student")
    public List<Student> getAll();
}
```





### 5.在controller中操作db

```java
@RestController
public class studentController {
    @Autowired
    StudentMapper studentMapper;
    @GetMapping("/getAll")
    public List<Student> getAll(){
        return  studentMapper.getAll();
    }
}
```

