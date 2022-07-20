# 基础信息

## maven

### 多模块项目

![image-20220707152718188](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707152718188.png)

![image-20220707153118077](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707153118077.png)



denpendencyMangement 和dependencies的区别

	1.  denpendencyMangement只在父项目中声明，不引入。需要子项目显式地引入。
	1.  dependencies中的会自动引入，且默认子项目继承。





## redis

常用数据类型： String、List、Hash、Set、SortedSet（如排行榜）.

一些特性：

1. 都是原子操作
2. 可以对key设置过期时间: 定时删除、惰性删除、定期删除。
3. 两种持久化方式： 
   1. RDB（快照、默认），一次性全部持久化、保存数据。
   2. **AOF： 保存用户的写指令。**



### 速度快的原因

1. 完全基于内存
2. 数据结构简单
3. 单线程，没有切换
4. 多路IO复用模型。

![image-20220707165736258](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707165736258.png)







### 多路IO复用模型

单线程或单进程同时监测若干个文件描述符是否可以执行IO操作的能力。本质上用更少的资源做更多的事。

#### 时分复用：

一种解决办法是对CPU进行时分复用(多个事件流将CPU切割成多个时间片，不同事件流的时间片交替进行)。在计算机系统中，我们用线程或者进程来表示一条执行流，通过不同的线程或进程在操作系统内部的调度，来做到对CPU处理的时分复用。

缺点：

* 线程/进程创建成本
* CPU切换不同线程/进程成本
* 多线程的资源竞争



#### linux中的I/O模型



##### 阻塞IO

阻塞IO意味着当我们发起一次IO操作后一直等待成功或失败之后才返回，在这期间程序不能做其它的事情。阻塞IO操作只能对单个文件描述符进行操作，详见[read](https://link.zhihu.com/?target=http%3A//man7.org/linux/man-pages/man2/read.2.html)或[write](https://link.zhihu.com/?target=http%3A//man7.org/linux/man-pages/man2/write.2.html)。









##### 非阻塞IO

我们在发起IO时，通过对文件描述符设置O_NONBLOCK  flag来指定该文件描述符的IO操作为非阻塞。非阻塞IO通常发生在一个for循环当中，因为每次进行IO操作时要么IO操作成功，要么当IO操作会阻塞时返回错误EWOULDBLOCK/EAGAIN，然后再根据需要进行下一次的for循环操作，这种类似轮询的方式会浪费很多不必要的CPU资源，是一种糟糕的设计。和阻塞IO一样。

<u>其实就是不标记阻塞，执行到的时候如果阻塞了，那就就轮询。</u>



##### 信号驱动IO

利用信号机制，让内核告知应用程序文件描述符的相关事件。



##### 异步IO 

这是对于同步IO的，前面4种本质上都是同步IO。在操作完成后通知程序进行处理。



##### IO多路复用

linux：select 、poll、epoll

mac：kqueue

windows：IOCP

redis： epoll，没有epoll用select

nginx： epoll，没有用select





###### select

你在大学读书，你的朋友来找你：select版宿管阿姨，就会带着你的朋友挨个房间找，直到找到你。

连接变多性能下降。其实就像数组，挨着轮询。

1024个文件描述符





###### epoll

epoll版阿姨，会先记下每位同学房间号， 你的朋友来时，只需告诉你的朋友你住在哪个房间即可，无需亲自带着你朋友满大楼找人

连接数和性能无关，类似于map吧。







### 缓存穿透 和缓存雪崩

![image-20220707164922643](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707164922643.png)







## mysql



![image-20220707165911447](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707165911447.png)



![image-20220707170403517](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707170403517.png)



### MYSQL索引 B树和B+树

#### B树

![image-20220707170710168](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707170710168.png)













#### B+树

![](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707170830199.png)







### 常见索引类型





#### 聚簇索引（主键索引）

![image-20220707171440484](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707171440484.png)





#### 辅助索引

![image-20220707171640897](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220707171640897.png)







#### 唯一索引

#### 单列索引

#### 多列索引







## Spring Data JPA

![image-20220708081656458](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220708081656458.png)





springboot1 默认tomcat数据库连接池、

springboot2 换成了HikariCP连接池。





## springboot



### 应用启动入口

有三种方式：

1. 默认方式，静态run方法。

```java
SpringApplication.run(SpringbootStudyApplication.class, args);
```



2. 通过api调整行为

```java
        SpringApplication springApplication = new SpringApplication(SpringbootStudyApplication.class);
        springApplication.setBannerMode(Banner.Mode.OFF);
        springApplication.run(args);
```



3. SpringApplicationBuilder的 Fluent API，实现链式调用。

```java
        new SpringApplicationBuilder(SpringbootStudyApplication.class)
                .bannerMode(Banner.Mode.OFF)
                .run(args);
```





### 定时任务

1. @EnableScheduling

```java
@SpringBootApplication
@EnableScheduling
public class SpringbootStudyApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootStudyApplication.class, args);
    }

}
```



2. 定时任务的设置方式

```java
 private final DateTimeFormatter fmt = DateTimeFormatter.ofPattern("HH:mm:ss");
@Scheduled(cron = "*/3 * * * * ?")
public void schedule01(){
    log.info("01-->{}", LocalDateTime.now().format(fmt));
}
```



```java

@Scheduled(fixedDelay = 3000)
public void schedule01(){
    log.info("01-->{}", LocalDateTime.now().format(fmt));
}
```





### 异步任务



@EnableAsync

```java
@SpringBootApplication
@EnableAsync
public class SpringbootStudyApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootStudyApplication.class, args);
    }

}
```



重新配置一个异步任务的线程池

```java
@Slf4j
@Configuration
public class AsyncpoolConfig implements AsyncConfigurer {

    @Bean
    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor threadPoolTaskExecutor = new ThreadPoolTaskExecutor();
        threadPoolTaskExecutor.setCorePoolSize(3);
        threadPoolTaskExecutor.setMaxPoolSize(3);
        threadPoolTaskExecutor.setQueueCapacity(20);
        threadPoolTaskExecutor.setKeepAliveSeconds(10);
        threadPoolTaskExecutor.setThreadNamePrefix("imoocAsync_");


        threadPoolTaskExecutor.setWaitForTasksToCompleteOnShutdown(true);
        threadPoolTaskExecutor.setAwaitTerminationSeconds(60);

        // 拒绝策略
        threadPoolTaskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());

        threadPoolTaskExecutor.initialize();
        return threadPoolTaskExecutor;
    }

    @Override
    //定义异常任务的处理类
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return new AsyncExceptionHandler();
    }

    class AsyncExceptionHandler implements  AsyncUncaughtExceptionHandler{

        @Override
        public void handleUncaughtException(Throwable ex, Method method, Object... params) {
            log.info("AsyncError:{},Methods:{},Params:{}",
                        ex.getMessage(),
                    method.getName(),
                    JSON.toJSONString(params)
                    );
        }
    }
}
```





异步任务类

```java
@Async("getAsyncExecutor")
public Future<Integer> asyncProcess() throws InterruptedException {
    log.info("current thread name ->{}",Thread.currentThread().getName());
    TimeUnit.SECONDS.sleep(2);
    return new AsyncResult<>(100);
}
```





### 单元测试

测试上面的异步任务，并获取返回值

```java
@Slf4j
@SpringBootTest
@RunWith(SpringRunner.class)
public class AsyncServiceTest {
    @Autowired
    AsyncService asyncService;

    @Test
    public void  test() throws ExecutionException, InterruptedException {
        long start = System.currentTimeMillis();
        Future<Integer> future = asyncService.asyncProcess();
        log.info("async result :{}",future.get());
        log.info("exec time:{}",System.currentTimeMillis() - start);
    }
}
```

可以在异步任务的返回中设置一个超时时间，来抛出一个异常。

![image-20220710145139391](/Users/ljun/Library/Application Support/typora-user-images/image-20220710145139391.png)







### 开机启动

有如下两种方式：

可以通过@Order()注解来标记顺序。

![image-20220710150327387](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220710150327387.png)





### jackson

jackson的配置文件。

```java
@Configuration
public class JacksonConfig {
    @Bean
    public ObjectMapper getObjectMapper(){
        ObjectMapper mapper = new ObjectMapper();
        return  mapper;
    }
}
```

下面设置的@JsonFormat 也可以在上面的jackson的配置文件中设置。

同时默认情况下，如果某一个属性没有设置，前端会受到null。也可以在上面的jackson的配置文件中设置

![image-20220710170341061](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220710170341061.png)

响应：

```json
{
    "name": "liuj",
    "age": 19,
    "rt": "2022-07-10 08:57:32"
}
```





新的jackson设置：

```java
@Configuration
public class JacksonConfig {
    @Bean
    public ObjectMapper getObjectMapper(){
        ObjectMapper mapper = new ObjectMapper();
        mapper.setSerializationInclusion(JsonInclude.Include.NON_NULL); //返回属性不为null
        mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss")); // 时间格式
        return  mapper;
    }
}
```



### actuator监控

> ˈæktʃueɪtər*/*    [自] 执行机构；激励者；促动器；致动器

方便对于springboot微服务功能的监控。

分类：

1. 原生端点：
   1. 应用配置类
   2. 度量指标
   3. 操作控制类
2. 自定义端点



![image-20220710172228859](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220710172228859.png)





依赖：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```



配置：

<img src="https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220710175417584.png" alt="image-20220710175417584" style="zoom:50%;" />

请求可以得到一些基本的信息：

![image-20220710175458889](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220710175458889.png)





#### 自定义指标

```java
@Endpoint(id = "dateTime")
public class DateTimeEndPoint {

    private String format ="yyyy-MM-dd HH:mm:ss";
    @ReadOperation
    public Map<String,Object> info(){
        HashMap<String, Object> map = new HashMap<>();
        map.put("name","liuj");
        map.put("dateTime",new SimpleDateFormat(format).format(new Date()));
        return map;
    }

    @WriteOperation
    public void setFormat(String format){
         this.format =format;
    }
}
```



config：

```java
@Configuration
public class DateTimeEndPointConfig {
    @Bean
    @ConditionalOnMissingBean
    @ConditionalOnAvailableEndpoint
    public DateTimeEndPoint dateTimeEndPoint(){
        return  new DateTimeEndPoint();
    }
}
```





### 自定义starter

#### 创建

创建一个新的springboot项目。

1. 添加依赖：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-actuator-autoconfigure</artifactId>
        </dependency>
```



2. 创建接口和实现类：

```java
public class SplitServiceImp implements  ISplitService{
    @Override
    public List<String> split(String value) {
        return Stream.of(StringUtils.split(value,","))
                .collect(Collectors.toList());
    }
}
```



3. 创建config

```java
@Configuration
@ConditionalOnClass(value = {ISplitService.class, SplitServiceImp.class})
public class SplitAutoConfig {
    @Bean
    @ConditionalOnMissingBean
    ISplitService starterService(){
        return  new SplitServiceImp();
    }
}
```



4. 创建：src/main/resources/META-INF/spring.factories



5. 将3步骤中的config目录写入4中：

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration = com.example.config.SplitAutoConfig
```



6. mvn install

7. 在本地的maven仓库中可以发现：

<img src="https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220710204319158.png" alt="image-20220710204319158" style="zoom:80%;" />







#### 使用

1. 引入上面的包

```xml
        <dependency>
            <groupId>com.example</groupId>
            <artifactId>split-spring-boot-starter</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
```



2. 

```java
@Slf4j
@SpringBootTest
@RunWith(SpringRunner.class)
public class Test1 {
    @Autowired
    private ISplitService iSplitService;

    @Test
    public void test(){
        List<String> strings = iSplitService.split("1,2,3");
        log.info("{}",strings);
    }
}
```





###  自动启动和关闭shell

#### 自动启动

1. 配置项目打包的jar名称，不然会加入版本号：

```xml
    <build>
        <finalName>imooc-springboot-study</finalName>
    </build>
```



2. 项目打包后，imooc-springboot-study.jar文件会在target目录下，在target目录下执行以下命令：

```shell
#！ /usr/bin/env bash
nohup java -jar springboot-study-0.0.1-SNAPSHOT.jar &
```





#### 关闭

ps是Process Status的缩写， 

- -f 表示更为完整的输出
- -e表示列出所有的进程



grep是文本搜索。

* -v 是反向排除。` grep -v grep` 就是在搜索结果中排除含有grep.



if [[ -z "${pid}" ]]

* -z 表示如果pid变量为0。



```shell
#!/usr/bin/env bash
pid=`ps -ef | grep imooc-springboot-study.jar | grep -v grep | awk '{print $2}'`
if [[ -z "${pid}" ]]
then
    echo application is already stopped
else
  echo kill ${pid}
  kill -9 ${pid}
fi
```







# spring cloud常用组件

## 多模块的聚合

### 父模块

最外层的父级maven项目仅有一个pom文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>imooc-coupon</artifactId>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>coupon-eureka</module>
    </modules>
    <!--聚合项目默认为pom-->
    <packaging>pom</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <version>2.7.1</version>
        <artifactId>spring-boot-starter-parent</artifactId>
    </parent>

    <!--自动引入，子项目自动继承-->
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
    </dependencies>

    <!--需要子项目显式的引入-->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <!--在下面的properties中配置-->
                <version>${spring-cloud.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <properties>
        <maven.compiler.source>18</maven.compiler.source>
        <maven.compiler.target>18</maven.compiler.target>
        <spring-cloud.version>Greenwich.SR</spring-cloud.version>
    </properties>

    <repositories>
        <repository>
                <id>spring-milestones</id>
                <name>Spring Milestones</name>
                <url>https://repo.spring.io/milestone</url>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
        </repository>
    </repositories>

</project>

```





## 注册中心Eureka

![image-20220711124635305](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220711124635305.png)

上面两层Map，第一层map的key是服务名，第二层map的key是实例名。



### 单节点

1. 依赖:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>imooc-coupon</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <name>coupon-eureka</name>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
            <version>3.1.3</version>
        </dependency>
    </dependencies>

    <artifactId>coupon-eureka</artifactId>

    <properties>
        <maven.compiler.source>18</maven.compiler.source>
        <maven.compiler.target>18</maven.compiler.target>
    </properties>

</project>

```





2. application.yml文件中:

```yaml
spring:
  application:
    name: coupon-eureka

server:
  port: 8000

eureka:
  instance:
    hostname: localhost
  client:
    fetch-registry: false    # 是否需要从Eureka Server获取注册信息，单节点不需要设置为false
    register-with-eureka: false  # 是否将自己注册到eureka
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/


```



3. 启动springboot



### 多节点

1. 双节点：

```xml
spring:
  application:
    name: coupon-eureka

---
spring:
  profiles: server1
server:
  port: 8000
eureka:
  instance:
    hostname: server1
    prefer-ip-address: false
  client:
    serviceUrl:
      defaultZone: http://server2:8001/eureka/

---
spring:
  profiles: server2
server:
  port: 8001
eureka:
  instance:
    hostname: server2
    prefer-ip-address: false
  client:
    serviceUrl:
      defaultZone: http://server1:8000/eureka/

```



2. 设置host:  (/etc/hosts)

```
127.0.0.1       server1
127.0.0.1       server2
127.0.0.1       server3
```



3. 启动：

```java
 java -jar coupon-eureka-1.0-SNAPSHOT.jar --spring.profiles.active=server1
```



```shell
 java -jar coupon-eureka-1.0-SNAPSHOT.jar --spring.profiles.active=server2
```











## 网关Zuul

### 基本概念

![image-20220711184439287](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220711184439287.png)

实现4个抽象方法：

![image-20220711184749289](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220711184749289.png)



### 网关注册到Eureka上

依赖：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>imooc-coupon</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>coupon-gateway</artifactId>

    <properties>
        <maven.compiler.source>18</maven.compiler.source>
        <maven.compiler.target>18</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <version>3.1.3</version>
            <exclusions>
                <exclusion>
                    <groupId>javax.servlet</groupId>
                    <artifactId>servlet-api</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
            <version>2.2.10.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.7.1</version>
            </plugin>
        </plugins>
    </build>

</project>

```



启动类:

```java
@EnableZuulProxy            /*表示当前的应用是zuul server*/
@SpringCloudApplication   /*springboot + 服务发现 + 熔断*/
public class ZuulGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ZuulGatewayApplication.class,args);
    }
}
```



yml配置:

```yaml
server:
  port: 9000
spring:
  application:
    name: coupon-gateway
eureka:
  client:
    service-url:
      defaultZone: http://server1:8000/eureka/

```





这样网关zuul就注册到了注册中心Eureka上了。



### 过滤器

#### 定义抽象过滤器

```java
public abstract class AbstractZuulFilter extends ZuulFilter {
    //在过滤器间传递消息，数据保存在每个请求的ThreadLocal中、
    RequestContext requestContext;
    private final static String NEXT ="next";
    @Override
    public boolean shouldFilter(){
        RequestContext currentContext = RequestContext.getCurrentContext();
        return (boolean)currentContext.getOrDefault(NEXT,true);
    }
    @Override
    public Object run() throws ZuulException {
        requestContext = RequestContext.getCurrentContext();
        return crun();
    }
    protected abstract Object crun();
    Object fail(int code, String msg){
        requestContext.set(NEXT,false);
        requestContext.setSendZuulResponse(false);
        HttpServletResponse response = requestContext.getResponse();
        response.setContentType("text/html;charset=UTF-8");
        requestContext.setResponseStatusCode(code);
        requestContext.setResponseBody(String.format(
                "{\"result\" : \"%s!\" }",msg
        ));
        return null;
    }
    Object success(){
        requestContext.set(NEXT,true);
        return null;
    }
}
```



#### 定义前抽象过滤器

```java
public abstract class AbstractPreZuulFilter extends AbstractZuulFilter{
    @Override
    public String filterType() {
        return FilterConstants.PRE_TYPE;
    }
}
```



#### 定义后抽象过滤器

```java
public abstract class AbstractPostZuulFilter extends AbstractZuulFilter{
    @Override
    public String filterType() {
        return FilterConstants.POST_TYPE;
    }
}
```



#### 定义token验证过滤器

```java
@Slf4j
@Component
public class TokenFilter extends  AbstractPreZuulFilter{
    @Override
    public int filterOrder() {
        return 1;
    }
    @Override
    protected Object crun() {
        HttpServletRequest request = requestContext.getRequest();
        log.info(request.getMethod(),request.getRequestURL().toString());
        String token = request.getParameter("token");
        if (token == null){
            log.error("token is empty");
            return  fail(401,"token is empty");
        }
        return  success();
    }
}
```

#### 定义限流过滤器

```java
@Slf4j
@Component
public class RateLimiterFilter extends  AbstractPreZuulFilter{
    RateLimiter rateLimiter = RateLimiter.create(2.0);
    @Override
    public int filterOrder() {
        return 2;
    }
    @Override
    protected Object crun() {
        HttpServletRequest request = requestContext.getRequest();
        if (rateLimiter.tryAcquire()){
            log.info("get rate token success");
            return success();
        }
        log.error("rate limit {}",request.getRequestURL());
        return fail(402,"error:rate limit");
    }
}
```

#### 获取请求执行时间过滤器

```java
public class PreRequestFilter extends AbstractPreZuulFilter{
    @Override
    protected Object crun() {
        requestContext.set("startTime",System.currentTimeMillis());
        return success();
    }

    @Override
    public int filterOrder() {
        return 0 ;
    }
}

```

```java
@Slf4j
@Component
public class AccessLogFilter extends AbstractPostZuulFilter{
    @Override
    protected Object crun() {
        HttpServletRequest request = requestContext.getRequest();
        Long startTime = (Long) requestContext.get("startTime");
        StringBuffer url = request.getRequestURL();
        long duration = System.currentTimeMillis() - startTime;
        log.info("url:{},duration:{}",url,duration);
        return success();
    }

    @Override
    public int filterOrder() {
        return FilterConstants.SEND_RESPONSE_FILTER_ORDER -1;
    }
}
```





## 微服务调用Feign(Ribbon)

## 断路器Hystrix







## 通用模块

在imooc-coupon 模块下 imooc-coupon-service 模块 下新建coupon-common模块来存放通用模块。

### 通用配置定义

如配置http消息转换器:

```java
/*自定义HTTP消息转换器*/
@Configuration
public class WebConfiguration implements WebMvcConfigurer {
    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
       converters.clear();
       converters.add(new MappingJackson2HttpMessageConverter());
    }
}
```

配置jackson：

```java
@Configuration
public class JacksonConfig {
    @Bean
    public ObjectMapper getObjectMapper(){
        ObjectMapper mapper = new ObjectMapper();
        mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
        return  mapper;
    }
}
```









### 通用响应定义

![image-20220712101535739](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220712101535739.png)

1. 定义通用响应实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class CommonResponse<T>{
    private int code;
    private String message;
    private T data;
}
```

2. 添加advice来处理统一响应

supports方法如果返回true 会进入beforeBodyWrite方法。

```java
@RestControllerAdvice
public class CommonResponseDataAdvice implements ResponseBodyAdvice<Object> {
    @Override
    public boolean supports(
            MethodParameter returnType,
            Class<? extends HttpMessageConverter<?>> converterType) {
        // 如果当前类上标识了@IgnoreResponseAdvice 注解，不做处理
        if (returnType.getDeclaringClass().isAnnotationPresent(IgnoreResponseAdvice.class)){
            return false;
        }
        // 如果当前方法上标识了@IgnoreResponseAdvice 注解，不做处理
        if (returnType.getMethod().isAnnotationPresent(IgnoreResponseAdvice.class)){
            return false;
        }
        return true;
    }

    /*如果supports方法返回了 true，那么执行这里*/
    /*
    *  body就是controller的返回。
    * */
    @Override
    public Object beforeBodyWrite(
            Object body,
            MethodParameter returnType,
            MediaType selectedContentType,
            Class<? extends HttpMessageConverter<?>> selectedConverterType,
            ServerHttpRequest request, ServerHttpResponse response) {
        CommonResponse<Object> response1 = new CommonResponse<>(0, "", "");
        if (body ==null){
            return  response1;
        }else if (body instanceof CommonResponse){
            CommonResponse<Object> commonResponse = (CommonResponse<Object>) body;
        }else {
            response1.setData(body);
        }
        return  response1;
    }
}
```



3. 添加一个不需要经过2步骤处理的注解

```java
@Target({
        ElementType.TYPE,
        ElementType.METHOD
})
@Retention(RetentionPolicy.RUNTIME)
public @interface IgnoreResponseAdvice {

}
```







### 统一异常处理

![image-20220712101428989](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220712101428989.png)

### 定义异常类

```java
public class CouponException extends Exception{
    public CouponException(String message){
        super(message);
    }
}
```



定义异常拦截

```java
@RestControllerAdvice
public class GlobalExceptionAdvice {
    /*对于 CouponException类的异常进行处理*/
    @ExceptionHandler(value = CouponException.class)
    public CommonResponse<String> handleCouponException(
            HttpServletRequest request,
            CouponException couponException
    ){
        CommonResponse<String> response = new CommonResponse<>(-1, "business error", couponException.getMessage());
        return  response;
    }
}
```





## spring data jpa

1. 含有内部类的复杂对象如何存入数据库？
2. 如何将一个k->v映射存db的时候存v，取的时候转为k。

**使用jpa的 converter**

### 依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.29</version>
        </dependency>
```



### 配置

```yaml
spring:
  application:
    name: eureka-client-coupon-template
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/imooc_coupon_data
    hikari:
      username: root
      password: 12345
```



### 枚举类、实体类和数据库映射类

####  枚举类

注意下；根据枚举类中的一个属性值，查找整个枚举类的方法

1. 判断传入参数是否为空：`Objects.requireNonNull()`
2. 获取枚举类中的所有对象:`values()`
3. 过滤后返回一个 Optional对象: `findAny()`
4. 如果Optional对象为空：`orElseThrow()`

```java
@Getter
@AllArgsConstructor
public enum CouponCategory {
    MANJIAN("满减","001"),
    ZHEKOU("折扣","002"),
    LIJIAN("立减","003");
    private String description;
    private String code;

    public static CouponCategory of(String code){
        Objects.requireNonNull(code);
        return Stream.of(values())
                .filter(item -> item.code.equals(code))
                .findAny()
                .orElseThrow(() -> new IllegalArgumentException(code + "not exists!"));
    }
}
```

#### 实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class TemplateRule {
    private Expiration expiration;
    private Discount discount;
    private int limitation;
    private Usage usage;
    /*权重，哪些优惠券可以叠加使用， list[]*/
    private String weight;

    public static class Expiration{
        private String period;  //对应 PeriodType
        private int gap;   //对应 PeriodType为shift
        private long deadLine;

        boolean validate(){
            return  null!= PeriodType.of(period) && gap > 0 && deadLine >0;
        }
    }

    public static class  Discount{
        /*额度： 满减(20)  折扣(85)  立减(10)*/
        private int quota;
        private int base;
        boolean validate(){
            return  quota > 0 & base >0;
        }
    }

    public static class Usage{
        private String province;
        private String city;
        /*[文娱，生鲜，家具，全品类]*/
        private String goodsType;
        boolean validate(){
           return StringUtils.isNotEmpty(province)
                   && StringUtils.isNotEmpty(city)
                   && StringUtils.isNotEmpty(goodsType);
        }
    }
}
```



#### 数据库映射类

1. `@EntityListeners(AuditingEntityListener.class)`
2. ` @Basic` 和`@Transient`
3. ` @Convert(converter = xxxx.class)` 来设置将该类中的内容以何种方式映射到数据库中：

```java
@Data  /*实现getter 和setter */
@Entity  /*jpa 映射*/
@Table(name = "coupon_template")  /*对应表的名称*/
@EntityListeners(AuditingEntityListener.class)  /*自动插入一些属性*/
@NoArgsConstructor
public class CouponTemplate implements Serializable {
    @Id  /*自增主键*/
    @GeneratedValue(strategy = GenerationType.IDENTITY)  /*主键的生成策略*/
    @Column(name = "id", nullable = false)  /*对应于数据表的列*/
    @Basic  /* @Transient  来表示该属性不存在于数据库中*/
    private int id;

    @Column(name = "expired", nullable = false)
    private Boolean expired;

    @Column(name = "category", nullable = false)
    @Convert(converter = CouponCategoryConverter.class)
    private CouponCategory category;

    @CreatedDate
    @Column(name = "create_time", nullable = false)
    private Date createTime;

    @Column(name = "rule", nullable = false)
    @Convert(converter = TemplateRuleConverter.class)
    private TemplateRule rule;
}
```



如上面的枚举类，想要将数据的id映射到数据库中，从数据库中取的时候，直接映射为数据：

```java
public class CouponCategoryConverter implements AttributeConverter<CouponCategory,String> {

    @Override
    public String convertToDatabaseColumn(CouponCategory couponCategory) {
        return couponCategory.getCode();
    }

    @Override
    public CouponCategory convertToEntityAttribute(String s) {
        return CouponCategory.of(s);
    }
}
```



如上面的实体类，想要生成string 来存储在db中，从db中取回时候映射回该类：

```java
public class TemplateRuleConverter implements AttributeConverter<TemplateRule,String> {
    @Override
    public String convertToDatabaseColumn(TemplateRule templateRule) {
       return JSON.toJSONString(templateRule);
    }

    @Override
    public TemplateRule convertToEntityAttribute(String s) {
          return JSON.parseObject(s,TemplateRule.class);
    }
}
```







### dao层

```java
public interface CouponTemplateDao extends JpaRepository<CouponTemplate,Integer> {
    CouponTemplate findByName(String name);

    List<CouponTemplate> findAllByAvailbleAndExpired(
            Boolean available,
            Boolean expired
    );
}
```







### service 层







# 优惠券业务

## 优惠券模板微服务

![image-20220719122716093](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220719122716093.png)



1. 根据日期如220714 来随机生成：如"071422"的字符串。

```java
String yyMMdd = new SimpleDateFormat("yyMMdd").format(new Date());
IntStream stream = yyMMdd.chars(); // ascii码表中的值
List<Character> list = stream.mapToObj(item -> (char) item)   //将ascii码表的值再转换为字符。
        .collect(Collectors.toList());  // 收集方式：toList
Collections.shuffle(list);  //洗牌
String s = list.stream().map(Object::toString).collect(Collectors.joining()); // 收集方式joining。
```

2. 随机数

```java
char[]  bases =  new char[]{'1','2','3','4','5','6','7','8','9'};
String suffix8 = RandomStringUtils.random(1, bases) + RandomStringUtils.randomNumeric(7);
return suffix8;
```



3. 如下功能：

```java
[
  {
    id:3,
    name:"aaa"
  },
    {
    id:4,
    name:"bbb"
  }
]
```

想要生成如下map:

```java
{
  3:{
    id:3,
    name:"aaa"
  },
  4:{
    id:4,
    name:"bbb"
  }
}
```

```java
        List<CouponTemplate> all = templateDao.findAllById(ids);
        return  all.stream().map(TemplateBaseServiceImpl::template2TemplateSDK)
                .collect(Collectors.toMap(
                        CouponTemplateSDK::getId,
                        Function.identity()
                ));
```





### 定时清理已经过期的优惠券

优惠券中有两个字段expired 和 deadline，开启定时任务查找所有未过期的优惠券模板，检查其deadline。如果此次检查已经过期了，那么就将其expired字段修改后存入数据库。





### 健康检查接口

```java
package org.example.controller;

import lombok.extern.slf4j.Slf4j;
import org.example.exception.CouponException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.cloud.client.serviceregistry.Registration;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @author ljun
 * @version 1.0
 * Create by 2022/7/16 7:59 PM
 */

@RestController
@Slf4j
public class HealthCheck {
    /***
     * 服务发现客户端
     */
    private final DiscoveryClient discoveryClient;
    /***
     * 服务注册接口，提供获取服务id的方法
     */
    private final Registration registration;

    public HealthCheck(DiscoveryClient discoveryClient, Registration registration) {
        this.discoveryClient = discoveryClient;
        this.registration = registration;
    }


    @GetMapping("/health")
    public String health(){
        log.debug("view health api");
        return  "";
    }
    @GetMapping("/exception")
    public String exception() throws CouponException {
        log.debug("view exception api");
        throw new CouponException("CouponTemplate Error");
    }

    /***
     * 获取Eureka Server上的微服务元信息
     * @return
     */
    @GetMapping("/info")
    public List<Map<String ,Object>> info(){
        String serviceId = registration.getServiceId();
        List<ServiceInstance> instances = discoveryClient.getInstances(serviceId);
        ArrayList<Map<String, Object>> result = new ArrayList<>();
        instances.forEach(item -> {
            HashMap<String, Object> info = new HashMap<>();
            info.put("serviceId",item.getServiceId());
            info.put("instanceId",item.getInstanceId());
            info.put("port",item.getPort());
            result.add(info);
        });
        return  result;
    }
}
```



![image-20220717083950834](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220717083950834.png)



coupon-template 原本的访问路径为：ip:7001/coupon-template/xxx

通过网关后的访问路径为： ip:9000/imooc/coupon-template/xxx













### redis 乱码

查看redis-cli中的数据出现了如下乱码：

![image-20220716211112731](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220716211112731.png)

原来是需要修改redisTemplate的一些序列化配置：

```java
@Configuration
@AutoConfigureAfter(RedisAutoConfiguration.class)
public class RedisCacheAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean(name = "redisTemplate")
    public RedisTemplate<Object, Object> redisTemplate(
            RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        //使用fastjson序列化
        FastJsonRedisSerializer fastJsonRedisSerializer = new FastJsonRedisSerializer(Object.class);
        // value值的序列化采用fastJsonRedisSerializer
        template.setValueSerializer(fastJsonRedisSerializer);
        template.setHashValueSerializer(fastJsonRedisSerializer);
        // key的序列化采用StringRedisSerializer
        template.setKeySerializer(new StringRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

    @Bean
    @ConditionalOnMissingBean(StringRedisTemplate.class)
    public StringRedisTemplate stringRedisTemplate(
            RedisConnectionFactory redisConnectionFactory) {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```



查看redis某个key对应数据的类型：

```shell
type "imooc_coupon_template_code,9"
```



查看redis某个key对应数据的具体信息：

```shell
hgetall keyName
```











## 优惠券分发微服务

### 实体类对象

1. `@Table(name= "coupon_data")` 表映射
2. `@EntityListeners(AuditingEntityListener.class)` 可以自动填充一些属性
3. `@JsonSerialize(using = CouponSerialize.class)` 自定义序列化

```java
@Data
@Entity
@Table(name= "coupon_data")
@EntityListeners(AuditingEntityListener.class)
@NoArgsConstructor
@JsonSerialize(using = CouponSerialize.class)
public class CouponData {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private int template_id;

    private int user_id;

    private int coupon_code;

    @CreatedDate
    private Date assign_time;

    @Convert(converter = CouponStatusConverter.class)
    private CouponStatus status;
    
    public static  CouponData invalidCoupon(){
        CouponData couponData = new CouponData();
        couponData.setId(-1);
        return couponData;
    }

    public CouponData(int template_id, int user_id, int coupon_code, CouponStatus status) {
        this.template_id = template_id;
        this.user_id = user_id;
        this.coupon_code = coupon_code;
        this.status = status;
    }
}
```

自定义序列化的实现：

```java
public class CouponSerialize extends JsonSerializer<CouponData> {

    @Override
    public void serialize(CouponData couponData,
                          JsonGenerator jsonGenerator,
                          SerializerProvider serializerProvider) throws IOException {
        jsonGenerator.writeStartObject();
        jsonGenerator.writeStringField("id",String.valueOf(couponData.getId()));
        jsonGenerator.writeStringField("assignTime",
                new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(couponData.getAssign_time()));
        jsonGenerator.writeEndObject();
    }
}
```





### redis的使用

1. 生成的时候将记录的有效期设置为一定范围的随机数。**防止缓存雪崩，一个时间都失效去访问数据库**
2. 如果查询到的数据不存在就存一条假数据，以便下次访问的时候直接返回。**防止缓存击穿，查完缓存，查数据库**

```java
 redisTemplate.expire(redisKey,getRandomExpirationTime(1,2), TimeUnit.SECONDS);
```

生成假数据存入redis时，可以使用pipeline来**一次插入多条**：

```java
    public void saveEmptyCouponListToCache(Integer userId,List<Integer> statusList,String redisKey){
      //生成假数据
        HashMap<String, String> invalidCouponMap = new HashMap<>();
        invalidCouponMap.put("-1",JSON.toJSONString(CouponData.invalidCoupon()));

      // 管道
        SessionCallback<Object> sessionCallback = new SessionCallback<Object>() {
            @Override
            public  Object execute(RedisOperations redisOperations) throws DataAccessException {
                statusList.forEach( item -> {
                    redisOperations.opsForHash().putAll(redisKey,invalidCouponMap);
                });
                return null;
            }
        };
      
        List<Object> list = redisTemplate.executePipelined(sessionCallback);
        log.info("Pipeline Exe Result:{}",JSON.toJSONString(list));
    }
```

