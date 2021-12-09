# 为什么使用springboot

## spring缺点

1. 代码轻量，配置较重，spring开始使用xml配置，spring2.5开始使用基于注解的组件扫描，spring3.0引入了基于java的配置。

2. 项目依赖多，发生冲突。





# 环境搭建

1. springboot的基础依赖：

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>

    <dependencies>
        // web功能起步依赖
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```



2. springboot 引导类

```java
@SpringBootApplication // 来声明引导类。
public class MyApplicationBootApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplicationBootApplication.class);
    }
}

```



3. 创建controller

```java
@Controller
public class controller {
    @RequestMapping("/map")
    @ResponseBody
    public String test(){
        return  "aaaa";
    }
}
```



4. springboot 热部署（没做出来）





### idea springboot的快速搭建

通过spring initializr





# springboot原理



# springboot配置文件

1. application.properties
2. application.yml





# springboot 集成

## 集成mybatis

https://blog.csdn.net/iku5200/article/details/82856621/

## 集成Junit

## 集成SpringData JPA

## 集成Redis
