CMS：内容管理系统

# 基础环境

1. windows版 nginx
2. mongodb+studio 3T
3. 





## 微服务的项目结构



<img src="https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320200350834.png" alt="image-20220320200350834" style="zoom:150%;" />

微服务项目导入IDEA中。





# 服务器部分

## nginx 本地服务

```nginx
    server {
        listen       80;
        server_name  www.xuecheng.com;	
        ssi on;	
        location /  {
            alias   D:\my-project\cms\cms\xc-ui-pc-static-portal/;
            index  index.html index.htm;
        }
```

注意点：

* 本地配置server_name

* **页面的ssi**：server side include

  在index.html中通过

  ```html
  <!--#include virtual="/include/header.html"-->
  ```

  引入。

* nginx alias

* 在chrome没生效，在firefox上生效了。







# CRUD 

参考视频文档中的1-3天。



## create

1. controller的 interface:

```java
  public CmsPageResult add(CmsPage cmsPage);
```

2. controller的实现：

```java
    @PostMapping("/add")
public CmsPageResult add(@RequestBody CmsPage cmsPage) {
        CmsPageResult result = cmsPageService.add(cmsPage);
        return result;
    }
```

3. service层的interface:

4. service层的实现:

   要根据已知的一些不能重复的参数进行查询，如果不存在则可以创建。

```java
    public CmsPageResult add(CmsPage cmsPage) {
        CmsPage searchedCmsPage = cmsPageRepository.findCmsPageByPageNameAndSiteIdAndPageWebPath(cmsPage.getPageName(), cmsPage.getSiteId(), cmsPage.getPageWebPath());
        if (searchedCmsPage == null) {
            cmsPage.setPageId(null);
            // 创建
            CmsPage savedPage = cmsPageRepository.save(cmsPage);
            return new CmsPageResult(CommonCode.SUCCESS, savedPage);
        }
        return new CmsPageResult(CommonCode.FAIL, null);
    }
```

5. dao

利用了mongodb自带的方法

```java
public interface CmsPageRepository extends MongoRepository<CmsPage,String> {
		// save 方法， 直接传入cmsPage对象。
}

```





## （条件）查询retrieves



1. controller的 interface:

```java
public QueryResponseResult findList(int page, int size, QueryPageRequest queryPageRequest);
```

2. controller的实现：

```java
    @GetMapping("/list/{page}/{size}")
    @ApiOperation("分页查询列表")
    public QueryResponseResult findList(@PathVariable("page") int page,@PathVariable("size") int size, QueryPageRequest queryPageRequest) {
        QueryResponseResult list1 = cmsPageService.findList(page, size, queryPageRequest);
        return list1;
    }
```

3. service层的interface:

4. service层的实现:

```java
    public QueryResponseResult findList(int page, int size, QueryPageRequest queryPageRequest) {
        // 分页条件
        Pageable pageable = PageRequest.of(page, size);
        // 自定义查找对象
        CmsPage filterCmsPage = new CmsPage();
        if (StringUtils.isNotEmpty(queryPageRequest.getSiteId())) {
            filterCmsPage.setSiteId(queryPageRequest.getSiteId());
        }
        if (StringUtils.isNotEmpty(queryPageRequest.getTemplateId())) {
            filterCmsPage.setSiteId(queryPageRequest.getTemplateId());
        }
        if (StringUtils.isNotEmpty(queryPageRequest.getPageAliase())) {
            filterCmsPage.setSiteId(queryPageRequest.getPageAliase());
        }
        // 查找example匹配对象，并且设置模糊查询的属性名。
        ExampleMatcher exampleMatcher = ExampleMatcher.matching().withMatcher("pageAliase", ExampleMatcher.GenericPropertyMatchers.contains());
        Example<CmsPage> example = Example.of(filterCmsPage, exampleMatcher);
        // 根据 example和分页条件对象查找，调用原生方法。
        Page<CmsPage> all = cmsPageRepository.findAll(example, pageable);
        QueryResult queryResult = new QueryResult();
        queryResult.setList(all.getContent());
        queryResult.setTotal(all.getTotalElements());
        QueryResponseResult queryResponseResult = new QueryResponseResult(CommonCode.SUCCESS, queryResult);
        return queryResponseResult;
    }
```

5. dao

利用了mongodb自带的方法

```java
public interface CmsPageRepository extends MongoRepository<CmsPage,String> {
		// findAll(example, pageable)
}

```



## update



1. controller的 interface:

```java
  public CmsPageResult edit(String id,CmsPage cmsPage);
```

2. controller的实现：

   使用了put去更新。

```java
    @PutMapping("/edit/{id}")
    public CmsPageResult edit(@PathVariable("id") String id,@RequestBody CmsPage cmsPage) {
        CmsPageResult update = cmsPageService.update(id, cmsPage);
        return  update;
    }
```

3. service层的interface:

4. service层的实现:

   先根据id查出一个结果的Optional对象。然后调用原生的save方法。

```java
    public CmsPageResult update(String id, CmsPage cmsPage) {
        // 更新和删除挺类似的
        Optional<CmsPage> findResult = cmsPageRepository.findById(id);
        if (findResult.isPresent()) {
            CmsPage cmsPage1 = findResult.get();
            cmsPage1.setTemplateId(cmsPage.getTemplateId());
            cmsPage1.setSiteId(cmsPage.getSiteId());
            cmsPage1.setPageAliase(cmsPage.getPageAliase());
            cmsPage1.setPageName(cmsPage.getPageName());
            cmsPage1.setPageWebPath(cmsPage.getPageWebPath());
            cmsPage1.setPagePhysicalPath(cmsPage.getPagePhysicalPath());
            // dao层save
            CmsPage save = cmsPageRepository.save(cmsPage1);
            // 保存成功会将保存的对象返回。
            if (save != null) {
                return new CmsPageResult(CommonCode.SUCCESS, save);
            }
        }
        return new CmsPageResult(CommonCode.FAIL, null);
    }
```

5. dao

利用了mongodb自带的方法

```java
public interface CmsPageRepository extends MongoRepository<CmsPage,String> {
		// save 方法， 直接传入cmsPage对象。
}
```





## delete



1. controller的 interface:

```java
  public CmsPageResult delete(String id);
```

2. controller的实现：

   使用了put去更新。

```java
    @DeleteMapping("/del/{id}")
    public CmsPageResult delete(@PathVariable("id") String id) {
        CmsPageResult result = cmsPageService.delete(id);
        return result;
    }
```

3. service层的interface:

4. service层的实现:

```java
    public CmsPageResult delete(String id) {
        try {
            // 和创建一样，
            cmsPageRepository.deleteById(id);
            CmsPageResult cmsPageResult =new CmsPageResult(CommonCode.SUCCESS,null);
            return  cmsPageResult;
        }catch (Exception e){
            CmsPageResult cmsPageResult =new CmsPageResult(CommonCode.FAIL,null);
            return  cmsPageResult;
        }
    }
```

5. dao

利用了mongodb自带的方法

```java
public interface CmsPageRepository extends MongoRepository<CmsPage,String> {
		// delete 方法
}
```







## 异常处理

![image-20211223085147557](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20211223085147557.png)





微服务类的全局异常处理放在xc-framework-common /exception下来统一抛出.

```java
// 抛出 RuntimeException 是为了减小代码侵入性
public class CustomException extends  RuntimeException{
    ResultCode resultCode;
    public CustomException(ResultCode resultCode){
        this.resultCode =resultCode;
    }
    public ResultCode getResultCode() {
        return resultCode;
    }
}
```

用一个静态方法来throw。

```java

public class ExceptionCast {
    public static void exceptionCast(ResultCode resultCode){
            throw new  CustomException(resultCode);
    }
}
```







# 远程请求接口

springmvc使用RestTemplate请求http接口，RestTemplate底层可以采用第三方的http客户端实现如：Apache HttpClient、OkHttpClient。





# freemarker使用



## 基础使用

controller中：

```java
@Controller
@RequestMapping("/aaa")
public class FreemarkerController {
    @RequestMapping("/aaa")
    public String test(Map<String,Object> map){
        map.put("name","liujun");
        return  "index";
    }
```

resources/templates文件夹下：创建index.ftlh文件（或者ftl）。

```jsp
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>$Title$</title>
</head>
<body>
    <div>
        <span>${name}</span>
    </div>
</body>
</html>

```

这样访问：localhost:8088/aaa/aaa 返回的就是index.ftlh文件。**controller中的map会作为数据给index文件**





## freemarker基本语法

### 遍历数组+if判断

```jsp
        <#list studentList as student>
            <div>
                <span <#if student.name =="小s">style="background: red" </#if> >${student_index}</span>
                <span>${student.name}</span>
            </div>
        </#list>
```



### 遍历map和map取值

```html
    <#--获取map属性-->
    <div>
        <span>姓名:</span>
        <span>${studentsMap['student1'].name}</span>
    </div>
```



```html
    <#--遍历map-->
    <div>
        <#list studentsMap?keys as key>
            <span>姓名:</span>
            <span>${studentsMap[key].name}</span>
        </#list>
    </div>
```





### 空值处理

```html
    <#--空值处理 1-->
    <div>
        <#if maybeNull??>
            <span>maybeNull exist</span>
        </#if>
    </div>

    <#--空值处理 2-->
    <span>${(studentsMap['students'].name)!''}</span>
```





### 内嵌函数 

> `var?function`

### 时间格式化

```html
 <span>${student.birthday?date}</span>
```



### 数组长度

```html
 <span>${studentList?size}</span>
```









# GridFS

mongodb提供的用于持久化存储文件的模块。

将文件按照256K的大小分割成多个块进行存储。使用了两个集合：

1. chunks  collection存储文件的二进制数据
2. files 存储文件的元数据。



## GridFS存数据

1.文件 2.输入流 3gridFsTemplate存储。

```java
@SpringBootTest
@RunWith(SpringRunner.class)
public class TestGridFS {
    @Autowired
    GridFsTemplate gridFsTemplate;
        @Test
        public void testGridFS() throws FileNotFoundException {
            File file = new File("/Users/ljun/IdeaProjects/cms/be/xc-service-manage-cms/pom.xml");
            FileInputStream fileInputStream = new FileInputStream(file);
            ObjectId objectID = gridFsTemplate.store(fileInputStream, "测试存储");
            System.out.println(objectID.toString());
        }
}
```





## GridFS取数据

1.创建一个bean GridFSBucket

```java
@Configuration
public class MongodbConfig {
    @Value("${spring.data.mongodb.database}")
    String db;

    @Bean
    public GridFSBucket getGridFSBucket(MongoClient mongoClient){
        MongoDatabase database = mongoClient.getDatabase(db);
        GridFSBucket bucket = GridFSBuckets.create(database);
        return  bucket;
    }
}
```



2.下载

```java
    @Autowired
    GridFSBucket gridFSBucket;
        @Test
        public void queryFile() throws IOException {
            String fileID ="62439d535c2dddd21f874b85";
            GridFSFile gridFSFile = gridFsTemplate.findOne(Query.query(Criteria.where("_id").is(fileID)));
            //下载流
            GridFSDownloadStream gridFSDownloadStream = gridFSBucket.openDownloadStream(gridFSFile.getObjectId());
            GridFsResource gridFsResource = new GridFsResource(gridFSFile, gridFSDownloadStream);
            String s = IOUtils.toString(gridFsResource.getInputStream(), "UTF-8");
            System.out.println(s);
        }
```







## 删除

```java
@Test
public void testDelFile() throws IOException {
//根据文件id删除fs.files和fs.chunks中的记录
gridFsTemplate.delete(Query.query(Criteria.where("_id").is("5b32480ed3a022164c4d2f92")));
}
```







# 静态化

freemarker的模板存储在GridFS中，templateID在一张template表中，模板需要的数据存储在config表中，都获取到之后，通过freemarker的自带方法返回最终的html。













# 消息队列

MQ：（Message Queue）由erlang语言基于AMQP（Advanced Message Queue，高级消息队列）。





![image-20220404123336644](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220404123336644.png)



1. 发送方：
   1. 创建连接
   2. 创建通道
   3. 声明队列
   4. 发送消息
2. 接收方
   1. 创建连接
   2. 创建通道
   3. 声明队列
   4. 监听队列
   5. 接收消息
   6. ack回复





## RabbitMQ的工作模式

### Work queue

将多个计算拆分多个来获得最终结果



![image-20220404130224112](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220404130224112.png)

多个消费端共同消费一个队列中的消息。

1. 一个消息只被一个消费者接收。
2. rabbitMQ采用轮询的方式将消息平均发送给消费者
3. 消费者处理完一条才会收到下一条消息。



### Publish/Subcribe

Publish/Subcribe模式比work queue功能更强大。Publish/Subcribe模式可以实现work queue模式的功能。

Publish/Subcribe模式生产者面向交换机发送消息。work queue面向队列发送消息(默认交换机).

![image-20220404130344093](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220404130344093.png)

一个消费者都能监听自己的队列。



如： 消费成功后，给消费者发送短信提醒+邮件提醒。









### Routing



![image-20220404131407519](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220404131407519.png)



### Topics

和routing模式类似，采用通配符匹配。

![image-20220404131528646](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220404131528646.png)

如：根据用户的通知设置去通知用户，设置接收Email的用户只接收Email，设置接收sms的用户只接收sms，设置两种
通知类型都接收的则两种通知都有效。



### Header

header模式与routing不同的地方在于，header模式取消routingkey，使用header中的 key/value（键值对）匹配
队列





### RPC

![image-20220404131957911](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220404131957911.png)





## springboot 整合消息队列

### rabbitmq安装

* mac homebrew安装
  1. `brew install rabbitmq`
  2. `brew services start rabbitmq`





http://localhost:15672/ 默认的管理界面

用户名密码：guest



### 配置

1. application.yml

   ```yaml
   server:
     port: 44000
   spring:
     rabbitmq:
       host: 127.0.0.1
       port: 5672
       username: guest
       password: guest
       virtual-host: /
   
   ```

   

2. 定义RabbitConfig类，配置Exchange、Queue、及绑定交换机.

```java
package com.example.test_rabbitmq_consumer.config;

import org.springframework.amqp.core.*;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class RabbitMQConfig {
    public static final String QUEUE_INFORM_EMAIL ="queue_inform_email";
    public static final String QUEUE_INFORM_SMS = "queue_inform_sms";
    public static final String EXCHANGE_TOPICS_INFORM = "exchange_topics_inform";

    /*交换机*/
    @Bean(EXCHANGE_TOPICS_INFORM)
    public Exchange EXCHANGE_TOPICS_INFORM(){
        return ExchangeBuilder.topicExchange(EXCHANGE_TOPICS_INFORM).durable(true).build();
    }

    /*队列*/
    @Bean(QUEUE_INFORM_SMS)
    public Queue QUEUE_INFORM_SMS(){
        Queue queue = new Queue(QUEUE_INFORM_SMS);
        return queue;
    }
    /*队列*/
    @Bean(QUEUE_INFORM_EMAIL)
    public Queue QUEUE_INFORM_EMAIL(){
        Queue queue = new Queue(QUEUE_INFORM_EMAIL);
        return queue;
    }


    /*队列绑定到交换机*/
    @Bean
    public Binding BINDING_QUEUE_INFORM_SMS(
            @Qualifier(QUEUE_INFORM_SMS) Queue queue,
            @Qualifier(EXCHANGE_TOPICS_INFORM) Exchange exchange
            ){
        return BindingBuilder.bind(queue).to(exchange).with("inform.#.sms.#").noargs();
    }

    /*队列绑定到交换机*/
    @Bean
    public Binding BINDING_QUEUE_INFORM_EMAIL(
            @Qualifier(QUEUE_INFORM_EMAIL) Queue queue,
            @Qualifier(EXCHANGE_TOPICS_INFORM)Exchange exchange
    ){
        return BindingBuilder.bind(queue).to(exchange).with("inform.#.email.#").noargs();
    }
}

```





### 生产

```java
@SpringBootTest
@RunWith(SpringRunner.class)
public class TestRabbitmqApplicationTests {

    @Autowired
    RabbitTemplate rabbitTemplate;


    @Test
    public void testSendByTopics(){
        for (int i = 0; i <5 ; i++) {
            String  message ="sms email inform to user" +i;
            rabbitTemplate.convertAndSend(RabbitMQConfig.EXCHANGE_TOPICS_INFORM,"inform.sms.mail",message);
            System.out.println("Send Message is:'" + message + "'");
        }
    }
}
```



### 消费

新的工程:

```java
@Component
public class ReceiveHandler {

    @RabbitListener(queues = "queue_inform_email")
    public void receive_email(String msg, Message message, Channel channel){
        System.out.println(msg);
    }

    @RabbitListener(queues = "queue_inform_sms")
    public void receive_sms(String msg, Message message, Channel channel){
        System.out.println(msg);
    }
}
```





# 页面发布

前端点击静态化成功后，html文件由cms服务器存储在数据库中，在每一台服务器上配置一个cms_client服务，cms存储完成后，就通过消息队列告知每一个cms_client服务，去将html文件下载到服务器的指定位置。

![image-20220408104027911](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220408104027911.png)





# 课程管理

持久层采用：

1. spring data jpa ：用于表的基本CRUD
2. mybatis： 用于复杂的查询操作
3.  druid： 使用阿里巴巴提供的spring boot 整合druid包druid-spring-boot-starter管理连接池。





# mybatis

最终想返回前端一个如下样子：

```json
[
  { 
    id: 1,
    label: '一级 1',
    children: [
        	{
          	id: 4,
          	label: '二级 1‐1' 
        	}
      ] 
  }
]
```



要实现该部分包含如下：

1. 一个基础的数据库映射类包含：id、label字段。 Teachplan
2. 最终要返回的一个结果集：TeachplanNode

```java
// Teachplan
@Data
@ToString  // Lombok
@Entity
@Table(name="teachplan")
@GenericGenerator(name = "jpa-uuid", strategy = "uuid")
public class Teachplan implements Serializable {
    private static final long serialVersionUID = -916357110051689485L;
    @Id
    @GeneratedValue(generator = "jpa-uuid")
    @Column(length = 32)
    private String id;
}
```

```java
//TeachplanNode
@Data
@ToString
public class TeachplanNode extends Teachplan {
    List<TeachplanNode> children;
}
```







## controller

注意一下获取参数的方式

```java
    @GetMapping("/teachplan/list/{courseID}")
    public TeachplanNode findTeachplanList(@PathVariable("courseID") String courseID) {
        TeachplanNode teachplanNode = courseService.findTeachplanList(courseID);
        return teachplanNode;
    }
```



## dao

```java
package com.xuecheng.manage_course.dao;
@Mapper
public interface TeachplanMapper {
    public TeachplanNode selectList(String courseID);
}

```

##  xml

要注意要和上面同名。

```xml
<mapper namespace="com.xuecheng.manage_course.dao.TeachplanMapper">  //映射上面的dao中interface
  //id是标识 type中是resultMap的映射类
    <resultMap id="teachplanMap" type="com.xuecheng.framework.domain.course.ext.TeachplanNode">
        <id property="id" column="one_id"></id>
        <result property="pname" column="one_name"></result>
      //ofType  collection对应的映射类。
        <collection property="children" ofType="com.xuecheng.framework.domain.course.ext.TeachplanNode">
            <id property="id" column="two_id"></id>
            <result property="pname" column="two_name"></result>
            <collection property="children" ofType="com.xuecheng.framework.domain.course.ext.TeachplanNode">
                <id property="id" column="three_id"></id>
                <result property="pname" column="three_name"></result>
            </collection>
        </collection>
    </resultMap>
		
    <select id="selectList" resultMap="teachplanMap" parameterType="java.lang.String">
        SELECT
            a.id one_id,
            a.pname one_name,
            b.id two_id,
            b.pname two_name,
            c.id three_id,
            c.pname three_name
        FROM
            teachplan a
                LEFT JOIN teachplan b  ON b.parentid = a.id
                LEFT JOIN teachplan c ON c.parentid =b.id
        WHERE	a.parentid = '0'
        <if test="_parameter!=null and _parameter!=''">
            AND a.courseid =#{courseId}
        </if>
        ORDER BY a.orderby,
                 b.orderby,
                 c.orderby
    </select>
</mapper>
```





## sql语句

如上

注意左连接查询，









##  pageHelper

将分页参数设置在ThreadLocal中。

> PageHelper是mybatis的通用分页插件，通过mybatis的拦截器实现分页功能，拦截sql查询请求，添加分页语句， 最终实现分页查询功能。



在调用dao的service方法中设置分页参数：PageHelper.startPage(page, size)，分页参数会设置在ThreadLocal中

PageHelper在mybatis执行sql前进行拦截，从ThreadLocal取出分页参数，修改当前执行的sql语句，添加分页 sql。





1. 添加依赖

```xml
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.2.4</version>
        </dependency>
```



2. application.yml添加方言类型

```yaml
pagehelper:
	helper‐dialect: mysql
```



3.  定义mapper

```java
@Mapper
public interface CourseMapper {
   Page<CourseInfo> findCourseList();
}
```

4. 定义mapper.xml映射文件   **注意一下连表查询**

```xml
<mapper namespace="com.xuecheng.manage_course.dao.CourseMapper">
    <select id="findCourseList" resultType="com.xuecheng.framework.domain.course.CourseInfo">
               SELECT course_base.*,
               (SELECT pic FROM course_pic WHERE courseid = course_base.id) pic
               from course_base
    </select>
</mapper>
```

5. 测试类

```java
@Test
public void  testPageHelper(){
        PageHelper.startPage(1,10);
        Page<CourseInfo> list = courseMapper.findCourseList();
        System.out.println(list);
    }
```





















# 分布式文件存储

常见的分布式文件系统：

1. NFS，网络文件系统
2. GFS，google的分布式系统
3. HDFS，hadoop的分布式文件系统。





## fastDSF



### 描述

FastDFS是用c语言编写的一款开源的分布式文件系统，它是由淘宝资深架构师余庆编写并开源。FastDFS专为互联 网量身定制，充分考虑了冗余备份、负载均衡、线性扩容等机制，并注重高可用、高性能等指标，使用FastDFS很 容易搭建一套高性能的文件服务器集群提供文件上传、下载等服务。

为什么要使用fastDFS呢？

上边介绍的NFS、GFS都是通用的分布式文件系统，通用的分布式文件系统的优点的是开发体验好，但是系统复杂 性高、性能一般，而专用的分布式文件系统虽然开发体验性差，但是系统复杂性低并且性能高。**fastDFS非常适合 存储图片等那些小文件，fastDFS不对文件进行分块，所以它就没有分块合并的开销，fastDFS网络通信采用 socket，通信速度很快。**







![image-20220410205219333](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220410205219333.png)

1）Tracker

**Tracker Server作用是负载均衡和调度**，通过Tracker server在文件上传时可以根据一些策略找到Storage server提 供文件上传服务。可以将tracker称为追踪服务器或调度服务器。

FastDFS集群中的Tracker server可以有多台，Tracker server之间是相互平等关系同时提供服务，Tracker server 不存在单点故障。客户端请求Tracker server采用轮询方式，如果请求的tracker无法提供服务则换另一个tracker。

2）Storage

**Storage Server作用是文件存储，客户端上传的文件最终存储在Storage服务器上**，Storage server没有实现自己 的文件系统而是使用操作系统的文件系统来管理文件。可以将storage称为存储服务器。

Storage集群采用了分组存储方式。storage集群由一个或多个组构成，集群存储总容量为集群中所有组的存储容 量之和。一个组由一台或多台存储服务器组成，组内的Storage server之间是平等关系，不同组的Storage server 之间不会相互通信，同组内的Storage server之间会相互连接进行文件同步，从而保证同组内每个storage上的文件 完全一致的。一个组的存储容量为该组内的存储服务器容量最小的那个，由此可见组内存储服务器的软硬件配置最 好是一致的。

采用分组存储方式的好处是灵活、可控性较强。比如上传文件时，可以由客户端直接指定上传到的组也可以由 tracker进行调度选择。一个分组的存储服务器访问压力较大时，可以在该组增加存储服务器来扩充服务能力（纵向 扩容）。当系统容量不足时，可以增加组来扩充存储容量（横向扩容）。

3）Storage状态收集

Storage server会连接集群中所有的Tracker server，定时向他们报告自己的状态，包括磁盘剩余空间、文件同步 状况、文件上传下载次数等统计信息。





### 文件上传、下载流程

![image-20220410205639465](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220410205639465.png)

![image-20220410205737259](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220410205737259.png)



###  java && fastdfs

[fastdfs-client-java 无法下载](https://blog.csdn.net/weixin_51966599/article/details/123111390)







###  mac docker fastdfs 安装

注：

fastdfs的 docker镜像里的配置文件路径在：  `/etc/fdfs/`

fastdfs storage 的nginx路径在：`/usr/local/nginx`



1. `docker pull delron/fastdfs`

2. `docker network create my-net`  创建桥接网络，mac上必须使用桥接网络，不能使用host

3. 创建tracker: 

   ```
   docker run -d -p 22122:22122 --name tracker --net=my-net -v ~/fdfs/tracker:/var/fdfs delron/fastdfs tracker
   ```

4. 创建storage: **注意修改TRACKER_SERVER**

   ```
   sudo docker run -dti --name storage -p 8080:8080 -p 23000:23000 -e TRACKER_SERVER=192.168.1.4:22122 -v ~/fdfs/storage:/var/fdfs delron/fastdfs storage
   ```

5. 进入tracker 查看 tracker 和storage通讯时，tracker采用的ip：（或者 docker inspect storage）
   1. `docker exec -it tracker /bin/bash`
   2. `ifconfig`

​	<img src="https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220412100216032.png" alt="image-20220412100216032" style="zoom: 50%;" />

6. 回到mac shell下，根据5中查到的ip，设置别名:因为，172.19.0.2是内部网桥，需要别名到本地，注意需要Gateway也需要设置别名。

   <img src="https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220412143529449.png" alt="image-20220412143529449" style="zoom:50%;" />

   1. `sudo ifconfig lo0 alias 172.19.0.2`  
   2. `sudo ifconfig lo0 alias 192.168.1.4`  //mac本机的ip地址

注意：6中的还原命令为：

```
sudo ifconfig lo0 -alias 10.0.75.2 
sudo ifconfig lo0 -alias 172.19.0.1
```





7、 验证成功

* yml

```
server:
  port: 6666
fdfs:
  tracker-list:
    - 192.168.1.4:22122 # tacker服务的地址
  connect-timeout: 30000 # 连接超时时间
  so-timeout: 3000 # 读取文件超时时间
  thumb-image: # 缩率图
    height: 200
    width: 200
dsfHostName: http://192.168.1.4:8080/

```



* java:

```java
@RestController
@RequestMapping("/uploadController")
public class FdsUploadController {

    @Autowired
    private FastFileStorageClient fastFileStorageClient;

    @Value("${dsfHostName}")
    private String dsfHostName;

    @RequestMapping("/uploadPng")
    public void uploadPng(MultipartFile file) {
        try {
            System.out.println(file);
            String fileExtName = FilenameUtils.getExtension(file.getOriginalFilename());
            System.out.println(fileExtName+"******************************");
            StorePath storePath = fastFileStorageClient.uploadImageAndCrtThumbImage(file.getInputStream(), file.getSize(), fileExtName, null);
            String fullPath = storePath.getFullPath();
            System.out.println(dsfHostName + fullPath);
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

* postman:

![image-20220412143819968](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220412143819968.png)

























# Eureka注册中心

微服务之间通过远程调用来知道服务端的ip地址和端口号，注册中心帮助管理这些ip地址和端口号。

![image-20220414200019255](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220414200019255.png)



## Eureka单机环境搭建

## Eureka高可用环境搭建



![image-20220414200905980](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220414200905980.png)







# Feign远程调用



## Ribbon

> **客户端负载均衡器**



### nginx的负载均衡

客户端通过一个服务器其分配负载。

![image-20220414202544663](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220414202544663.png)



而客户端负载均衡没有上图中的负载均衡器，即没用通过黄色的server。



![image-20220414202622361](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220414202622361.png)







## Feign

> Feign是Netﬂix公司开源的轻量级rest客户端，使用Feign可以非常方便的实现Http 客户端。Spring Cloud引入 Feign并且集成了Ribbon实现客户端负载均衡调用。

也就是spring cloud 对于Ribbon的二次封装。







# 课程预览

