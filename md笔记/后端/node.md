# koa

# koa 使用模板引擎

## webstorm 配置ejs/vue模板

![image-20210822142017447](https://tva1.sinaimg.cn/large/008i3skNgy1gtpjmg71f9j617w0u0q7i02.jpg)



1. main.js中：配置模板

```js
app.use(views(__dirname + '/views', {
  extension: 'ejs'
}))
```



2. router.js 中: 

```js
router.get('/login', async (ctx, next) => {
  await ctx.render('login', {
    title: 'Hello Koa 2!',
    isMe:true,
  })
})
```



3. 模板中：

```ejs
<body>
    <% if(isMe) {%>
        <a href="#">提到我的</a>
    <%} else {%>
        <button>关注</button>
    <%}%>
</body>
```







# sequelize

[官网](https://www.sequelize.com.cn/core-concepts/getting-started)



## ORM

##  建模 & 同步数据库

## CRUD

