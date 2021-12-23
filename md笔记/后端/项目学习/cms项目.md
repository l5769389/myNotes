CMS：内容管理系统

# 基础环境

1. windows版 nginx
2. mongodb+studio 3T
3. 





## 微服务的项目结构



![image-20211221084204467](https://raw.githubusercontent.com/l5769389/note-img-repository/main/image-20211221084204467.png)



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











