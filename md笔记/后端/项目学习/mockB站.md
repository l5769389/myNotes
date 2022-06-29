# 获取前端请求

* `@RestController`

  是以下两者的结合：

  * @controller： 将当前类注入IOC容器中，项目跑起来的时候被实例化.
  * @ResponseBody：表示该类中所有的API接口返回的数据，都是以JSON形式，而不是html或者文本形式



* @RequestMapping



* GetMapping







#  mybatis

![image-20220607150622556](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220607150622556.png)



1. 如何查出文件夹递归结构的样子？

   如下是三层结构：

```xml
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
```



teachPlan为正常的数据库映射结构：

teachPlanNode继承了 teachPlan，添加children属性。

```
public class TeachplanNode extends Teachplan {
    List<TeachplanNode> children;
}
```





2. select中的sql语句传参：

```xml
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
```









# RESTful API

**命名原则：**

1. HTTP后的url为**名词复数形式**
2. url采用全小写，如果长度较长采用-连接

如：

/users   / users-fans  

错误：❌

/getUsers  /getUsersFans





**分级原则:**

1. 一级用来定位资源分类:   /users
2. 二级仍用来定位资源分类如：
   1. /users/20
   2. /users/20/fans/1

![image-20220607160626353](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220607160626353.png)

先搜索，在搜索结果下进行过滤 和排序

![image-20220607160802247](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220607160802247.png)







# 通用配置类

## 统一的响应类

成功和错误都是两个静态方法

1. 成功可以定制响应data
2. 失败可以定制响应的code，和msg

```java
public class JsonResponse<T> {
    private String code;
    private String msg;
    private T data;

    public JsonResponse(String code, String msg){
        this.code=code;
        this.msg=msg;
    }
    public JsonResponse(T data){
        this.data =data;
        this.code ="0";
        this.msg="success";
    }

    public static JsonResponse<String> success(){
        return new JsonResponse<>(null);
    }

    public static JsonResponse<String> success(String data){
        return new JsonResponse<>(data);
    }


    public static JsonResponse<String> fail(){
        return new JsonResponse<>("1","失败");
    }


    public static JsonResponse<String> fail(String code,String msg){
        return new JsonResponse<>(code,msg);
    }

	// get set 省略
}

```





## 加解密工具

## 全局异常处理

 

定义一个RuntimeException的子类 ConditionException

```
public class ConditionException extends RuntimeException {
    public static final long serialVersionUID =1L;
    private String code;

    public ConditionException(String code,String name) {
       super(name);
       this.code =code;
    }

    public ConditionException(String name) {
        super(name);
        this.code ="500";
    }

    public String getCode() {
        return code;
    }

    public void setCode(String code) {
        this.code = code;
    }
}
```



异常捕获

```java
@ControllerAdvice
@Order(Ordered.HIGHEST_PRECEDENCE)
public class CommonGlobalExceptionHandler {
    @ExceptionHandler(value = Exception.class)
    @ResponseBody
    public JsonResponse<String> commonExceptionHandler(HttpServletRequest request, Exception e){
        String eMessage = e.getMessage();
        if (e instanceof ConditionException){
            String code = ((ConditionException) e).getCode();
            return new JsonResponse<>(code,eMessage);
        }else {
            return  new JsonResponse<>("500",eMessage);
        }
    }

}
```









## Json信息转换配置

```java
@Configuration
public class JsonHttpMessageConverterConfig {

    @Bean
    @Primary
    public HttpMessageConverters fastJsonHttpMessageConvertes(){
        FastJsonHttpMessageConverter fastJsonHttpMessageConverter = new FastJsonHttpMessageConverter();
        FastJsonConfig fastJsonConfig = new FastJsonConfig();
        fastJsonConfig.setDateFormat("yyyy-MM-dd HH:mm:ss");
        fastJsonConfig.setSerializerFeatures(
                SerializerFeature.PrettyFormat,
                SerializerFeature.WriteNullStringAsEmpty,
                SerializerFeature.WriteNullListAsEmpty,
                SerializerFeature.WriteMapNullValue,
                SerializerFeature.MapSortField,
                SerializerFeature.DisableCircularReferenceDetect
        );
        fastJsonHttpMessageConverter.setFastJsonConfig(fastJsonConfig);
        return new HttpMessageConverters();
    }
}
```



### fastJson的循环引用

```java
ArrayList<Object> list = new ArrayList<>();
Object o = new Object();
list.add(o);
list.add(o);
// [{},{"$ref":"$[0]"}]
System.out.println(JSONObject.toJSONString(list));
// [{},{}]
System.out.println(JSONObject.toJSONString(list,SerializerFeature.DisableCircularReferenceDetect));
```









# 注册和登录

## 前端



![image-20220626090315298](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220626090315298.png)









## 用户表和用户信息表

1. utf8和utf8mb4的区别

“MySQL在5.5.3之后增加了这个utf8mb4的编码,mb4就是most bytes 4的意思,专门用来兼容四字节的unicode。utf8mb4是utf8的超集,除了将编码改为utf8mb4外不需要做其他转换。当然,为了节省空间,一般情况下使用utf8也就够了。”

2. collation(排列规则)的作用

如设置大小写是否敏感等。



3. String转为date存入数据库

```java
SimpleDateFormat formatter = new SimpleDateFormat( "yyyy-MM-dd");
String s= "2011-07-09 "; 
// string 转为 date
Date date =  formatter.parse(s);

// date转为string
String date = formatter.format(new Date())
```





4. 执行插入的时候如何获取插入成功的主键

```
<insert id="addUser"
        parameterType="com.l5769389.bli.domain.User"
        useGeneratedKeys="true"
        keyProperty="id"
>
    insert into
        t_user(
               phone,
               email,
               password
    )values (
             #{phone},
             #{email},
             #{password}
                    )
</insert>
```

```
userRepository.addUser(user);
```

这样当user被插入成功后，user中会自动将id设为数据库中的主键，





5. 如何保证两次插入数据库一起成功、失败

```java
    @Transactional
    public void addUser(User user) {
       userRepository.addUser(user);
       userRepository.addUserInfo(userInfo);
    }
```



6. String 和Long类型转换

```java
  long userID = Long.parseLong(sUserID);
	
//
	String.valueOf()
```









# 发布订阅



# springboot整合websocket

* 依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
```



* 配置类

```java
@Configuration
public class WebsocketConfig {
    public ServerEndpointExporter serverEndpointExporter(){
        return  new ServerEndpointExporter();
    }
}
```





* 服务类
  ```java
  @Component
  @ServerEndpoint("/imserver")
  @Slf4j
  public class WebsocketService {
      private Session session;
      private String sessionId;
      public static final AtomicInteger ONLINE_COUNT = new AtomicInteger(0);
      private static final ConcurrentHashMap<String,WebsocketService> WEBSCOKET_MAP =new ConcurrentHashMap<>();
  
  
      @OnOpen
      public void openConnection(Session session){
          this.sessionId =session.getId();
          this.session =session;
          if (WEBSCOKET_MAP.containsKey(sessionId)){
              WEBSCOKET_MAP.remove(sessionId);
              WEBSCOKET_MAP.put(sessionId,this);
          }else {
              WEBSCOKET_MAP.put(sessionId,this);
              ONLINE_COUNT.getAndIncrement();
              log.info("连接成功,sessionID为:"+sessionId+"当前在线人数为:"+ONLINE_COUNT);
          }
      }
  
      @OnClose
      public void closeConnection(){
          System.out.println("close");
      }
  
  
  }
  ```

  

## springboot多例模式注入bean

默认springboot是单例的，所以在多例的websocket中，直接注入会不成功。

```java
@Component
@ServerEndpoint("/imserver")
@Slf4j
public class WebsocketService {
    @Resource
    private RedisTemplate<String,String> redisTemplate;

    private static ConfigurableApplicationContext CONTEXT;

    public static void setApplication(ConfigurableApplicationContext application){
        WebsocketService.CONTEXT =application;
    }

    @OnOpen
    public void openConnection(Session session){
        RedisTemplate redisTemplate = (RedisTemplate) WebsocketService.CONTEXT.getBean("redisTemplate");
        redisTemplate.opsForValue().get("test");

    }

    @OnClose
    public void closeConnection(){
        System.out.println("close");
    }


}
```



在项目的启动类中:调用静态方法，将context传入。然后通过context来获取bean。

```java
@SpringBootApplication
public class MockBliApi {
    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(MockBliApi.class, args);
        WebsocketService.setApplication(context);
    }
}
```









# 文件上传

使用fdfs上传

tomcat对于文件大小有限制，修改文件限制，在配置文件中：

```properties
spring.servlet.multipart.max-file-size=100MB
spring.servlet.multipart.max-request-size=100MB
```



前端获取文件上传进度：

<img src="https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220628150717154.png" alt="image-20220628150717154" style="zoom:50%;" />



根据实际上传文件得知，每次能上传的大小约为7M，如果文件上传进度小于7M，那么无需获取进度。

如果对于文件进行了分片，那么得到的就是分片后每一片上传的进度。







## 断点续传

### 前端分片

```js

const chunkSize =10 * 1024 * 1024;
let file;
let fileName;
const handleFileChange =e=>{
  file =e.target.files[0];
  fileName =file.name;
}
const handleUpload= async ()=>{
  
  const chunkList = createChunk(file);
  const hash = md5(chunkList);
  for (let i = 0; i <chunkList.length; i++) {
    let formData = new FormData();
    formData.append("file",chunkList[i].file);
    formData.append("total",chunkList.length);
    formData.append("currentNo",i);
    formData.append("fileName",fileName);
    formData.append("fileHash",hash);
    await post(API.upload,formData)
  }
}

const createChunk=(file)=>{
    const chunkList =[];
    let hasSlicedSize =0;
    while (hasSlicedSize < file.size){
      const currentChunk = file.slice(hasSlicedSize,hasSlicedSize+chunkSize);
      chunkList.push({
        file:currentChunk,
      })
      hasSlicedSize =hasSlicedSize +currentChunk.size;
    }
    return chunkList;
}
```





### 后端接口

对于前端请求如下格式：

<img src="https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220628151851819.png" alt="image-20220628151851819" style="zoom:50%;" />

获取前端请求数据:

```java
    @PostMapping("/sliceFile")
    public JsonResponse<Map> uploadSlice(HttpServletRequest request){
        Integer total = Integer.parseInt(request.getParameter("total"));
        Integer currentNo = Integer.parseInt(request.getParameter("currentNo"));
        String fileName = request.getParameter("fileName");
        MultipartHttpServletRequest multipartHttpServletRequest = (MultipartHttpServletRequest) request;
        MultipartFile file = multipartHttpServletRequest.getFile("file");
        try {
            HashMap<String, String> map = fileService.uploadFileBySlices(file, "",fileName, currentNo, total);
            return new JsonResponse<>(map);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
          //  return new JsonResponse<>(new HashMap());
    }
```







1. 先上传第一个分片，得到上传的地址。
2. 根据1中得到的地址来处理其余分片



```java
// 上传第一片
public String uploadAppendFile(MultipartFile file,String fileName) throws IOException {
        String fileExtName = FilenameUtils.getExtension(fileName);
        StorePath storePath = appendFileStorageClient.uploadAppenderFile(
                DEFAULT_GROUP, file.getInputStream(), file.getSize(), fileExtName);
        return storePath.getPath();
    }
// 上传其余
public String modifyAppendFile(MultipartFile file, String filePath, long offset) throws IOException {
     appendFileStorageClient.modifyFile(DEFAULT_GROUP,filePath,file.getInputStream(),file.getSize(),offset);
        return "";
    }
```



```java
    public HashMap<String,String> uploadFileBySlices(MultipartFile file,String fileMD5,String fileName,Integer sliceNo,Integer totalSlice) throws IOException {
        if (sliceNo ==0){
            //第一片
            String path = this.uploadAppendFile(file,fileName);
            if (StringUtils.isNullOrEmpty(path)){
                throw  new ConditionException("上传失败");
            }
            redisTemplate.opsForValue().set(UPLOADED_SIZE_KEY,file.getSize());
            redisTemplate.opsForValue().set(FILE_PATH,path);
            HashMap<String, String> map = new HashMap<>();
            map.put("path",path);
            map.put("sliceNo"+sliceNo,"success");
            return  map;
        }
        long uploadedSize = (long)redisTemplate.opsForValue().get(UPLOADED_SIZE_KEY);
        String path = (String) redisTemplate.opsForValue().get(FILE_PATH);
        this.modifyAppendFile(file,path,uploadedSize);
        uploadedSize =uploadedSize + file.getSize();
        redisTemplate.opsForValue().set(UPLOADED_SIZE_KEY,uploadedSize);
        HashMap<String, String> map = new HashMap<>();
        map.put("sliceNo"+sliceNo,"success");
        return  map;
    }
```





### 秒传

就是在调用fdfs的存储方法前，查一下前端给的hash值是否存在在数据库中，如果存在就直接返回给前端。





## 视频播放

将fdfs返回的视频地址，通过后端转发。好处：

1. 控制视频权限
2. fdfs文件路径不会被获得。















# 日志

## springboot 使用slf4j

引入：

```java
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
```



使用：

```java

@Slf4j
public class WebsocketService {

    @OnOpen
    public void openConnection(Session session){
            log.info("连接成功");
        }
    }

    @OnClose
    public void closeConnection(){
        System.out.println("close");
    }
}
```

