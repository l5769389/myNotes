[TOC]

# node 及npm 的常用命令

更新npm :  `npm i -g npm`

更新node: `npm install -g n      n stable`

# Express





## 常用API：

```javascript
const express =require('express')
const app =express()
app.get('/',(req,res)=>res.send('hello world'))
app.listen(3000, () => console.log('Example app listening on port 3000!'))

app.use(express.static('public')) //公开public目录，express.static叫中间件函数

//  通过/static url地址来访问/public目录
app.use('/static',express.static('public'))
```

关于` app.use() `如果在外部能访问的html中引入了js或者css指向了没有express.static()开放的目录，会出现不能访问的错误❌。原因也很简单：**js和css是通过多次访问服务器拿到的，而如果没有开放访问权限，自然是不能访问的。** 而` '/static'`则是暴露给外部使用的url



## Express中使用art-template



1. 安装

```shell
npm install --save art-template
npm install --save express-art-template
```

2

404.art

```html
{{title}}
```



app.js

```javascript
app.engine('art',require('express-art-template'))

app.get('/',(req,res)=>res.render('404.art',{  //默认从views目录中找模板文件
    title:'尝试传参'
}))
```

3. `art-template`中可以采取对模板的继承



## get post获取请求体

| 访问url                                              **服务器代码** | 页面内容                       |      |
| ------------------------------------------------------------ | ------------------------------ | ---- |
| `localhost:3000/ `                          ` res.render('index.html')` | ` href ='/post'`               |      |
| `localhost:3000/post `                  `res.render('post.html') ` | `action='/comment method=get'` |      |
| ` req.query` 获取get请求的参数                               |                                |      |
|                                                              |                                |      |

`req.params 多用于获取restful风格的url参数`

Express没有提供解析post请求的方式，需要借助middleware ：`body-parser`

```javascript
npm i body-parser --save
const bodyParser =require('body-parser');
app.use(bodyParser.urlencoded({extended:false}))
app.use(bodyParser.json())

req.body  //post发送的请求体
```



`router 的使用`

`router.js`中：

```js
let express =require('express');
let router =express.Router();
...
module.exports =router;
```

`app.js`中:

```js
let router =require('./router');  //引入router.js，获取输出的router对象。
app.use(router);
```



## Express 中 中间件（middleWare）



`Express`中中间件有几种：

* 任何请求都会进入的中间件（放在最后配置404和全局错误处理页面）

  ```js
  //use就是一个中间件
  app.use(function(req,res,next){
    console.log('1')
    //next()
    //next(err) 传递err，直接进入四个参数的中间件中
  })
  //正常情况下，如果没有next(),就会停留在当前中间件。调用了next()就调用下一个中间件
  //如果不能进入下一个中间件那就会继续往后找。
  app.use(function(req,res,next){
    console.log('2')
  })
  
  app.use(function(err,req,res,next){
    
  })
  ```

  

* 部分请求才能进入的中间件

* 严格匹配请求方法和路径的中间件

  ```js
  app.get('/',function(req,res,next){
    
  })
  ```



## path

将`path.parse(path)将url路径返回对象`

![image-20190828163755597](/Users/ljun/Library/Application Support/typora-user-images/image-20190828163755597.png)

* `path.basename`

* `path.dirname`等

  ```js
  path.parse('/home/user/dir/file.txt');
  // 返回:
  // { root: '/',
  //   dir: '/home/user/dir',
  //   base: 'file.txt',
  //   ext: '.txt',
  //   name: 'file' }
  ```

  

Node中除了`require exports`等模块相关API，还有两个特殊成员：

* `_dirname`获取当前文件模块所属目录的绝对路径，父级目录的路径
* `_filename`获取当前文件的绝对路径，本身的路径

Node中的相对路径：**相对于执行node命令所处的路径**。所以Node中使用相对路径是不可靠的

> 所以Node中的路径要尽量都写成动态的绝对路径



## node 调试

```bash
$ node --inspect app.js
然后在chrome中 输入:chrome://inspect

```





## blog项目流程

###项目目录

![image-20190829191133540](/Users/ljun/Library/Application Support/typora-user-images/image-20190829191133540.png)

models中存放着各种数据库表结构，例如：user，topic，comment等

```js
let mongoose =require('mongoose')
let Schema = mongoose.Schema;


let userSchema = new  Schema({
    email:{
        type:String,
        required:true
    },
    nickname:{
        type: String,
        required: true

    },
    password:{
        type:String,
        required:true
    },
    createTime:{
        type:Date,
        //不要写Date.now() 这样会立即调用
        default:Date.now
    },
    lastModifiedTime:{
        type:Date,
        default: Date.now
    },
    avatar:{
        type:String,
        default:'/public/img/avatar-default.png'
    },
    bio:{
        type:String,
        default:''
    },
    gender:{
        type:Number,
        enum:[-1,0,1],
        default:-1
    },
    birthday:{
        type:Date,

    },
    status:{
        type:Number,
        //0 没有权限限制
        //1 不可以评论
        //2  不可以登录使用
        enum:[0,1,2],
        default:0
    }
})


module.exports =mongoose.model('User',userSchema)
```

​																				user表



### 同步异步问题：

> 表单的默认提交（action）默认是同步提交，会导致浏览器卡死，等待服务器的响应结果。而且同步表单提交浏览器会将返回（json 或者html）的直接渲染成html（ 把当前页面覆盖掉）

```
async 和 
```

如果采用这种方式可以在`res.redirect()中返回到同一页面，并且在页面上加入{{err_message}}`来返回提示。（如github的sign in就是这么做的）

这样做的好处是：

* 客户端没有js脚本，在没有ajax的浏览器上显示统一。
* 异步ajax中的`res.redirect()`（只针对同步请求）也是异步的。会导致服务端重定向失效，用`window.location.href =`去跳转





### session保存用户登录状态

> ```
>  领过苹果的不能再领了
>  老师发苹果
>  HTTP 是无状态的
> 
>  你自己记住你自己
> 
>  Cookie 小纸条
> 
>  Cookie 可以用来保存一些不太敏感的数据。
>  但是不能用来保存用户登陆状态。
>  isVIP: true
> 
>  记住用户名、购物车
> 
>  Session
> 
>  超市 -》 电子柜（）服务端
>  你（客户端）（二维码小票（开箱凭证）Cookie）（凭证是唯一的，不可能重复）
>  一旦丢失，不可找回，如果小票丢失，你的状态也就丢失了。
> 
>  钥匙是服务器给你的，所以这就很安全了，不太容易伪造出来。
>  这个时候我们就可以包一些敏感的数据保存到服务端。
>  客户端只需要拿着这把钥匙就可以了。
> ```

`express中默认不支持session和cookie`需要 npm下载 *express-session*

[记录用户登录次数Demo:](https://www.npmjs.com/package/express-session)

```js
var express = require('express')
var parseurl = require('parseurl')
var session = require('express-session')
 
var app = express()
 
app.use(session({
  secret: 'keyboard cat',//自定义加密字符串,防止客户端刻意伪造
  resave: false, // 
  saveUninitialized: true   //无论是否使用session，都默认分配一个session
}))
 
app.use(function (req, res, next) {
  if (!req.session.views) {
    req.session.views = {}
  }
 
  // get the url pathname
  var pathname = parseurl(req).pathname
 
  // count the views
  req.session.views[pathname] = (req.session.views[pathname] || 0) + 1
 
  next()
})
 
app.get('/foo', function (req, res, next) {
  res.send('you viewed this page ' + req.session.views['/foo'] + ' times')
})
 
app.get('/bar', function (req, res, next) {
  res.send('you viewed this page ' + req.session.views['/bar'] + ' times')
})
```



默认session数据存在内存中，服务器重启就会丢失，生产环境需要把session进行持久化。







# npm脚本

npm 允许在`package.json`文件里面，使用`scripts`字段定义脚本命令.

```javascript
{
  // ...
  "scripts": {
    "build": "node build.js"
  }
}	
```



`npm run build  等同于运行  node build.js`



**查看当前项目的所有npm 脚本命令： `npm run `**

## 原理

* 每当执行 ` npm run` 就新建一个Shell。一般Shell可以执行的脚本就能放在npm脚本中
  * 新建的这个shell会将当前目录的`node_modules/.bin` 目录加入 `PATH`变量，执行完再将` PATH`变量恢复原样
  * 正是上面这个原因: .bin目录下的所有脚本都可以直接用脚本名运行。

```shell
"test": "mocha test"   就可以
而不用写成：
"test": "./node_modules/.bin/mocha test"
```



## 通配符 && 传参 && 执行顺序

由于 npm 脚本就是 Shell 脚本，因为可以使用 Shell 通配符。

```js

"lint": "jshint *.js"      // *表示任意文件名
"lint": "jshint **/*.js"		// **表示任意一层子目录
```



用 -- 传参

```shell
$ npm run lint --  --reporter checkstyle > checkstyle.xml
```



```bash
 npm run script1.js & npm run script2.js   // & 并行执行
 npm run script1.js &&  npm run script2.js  //顺序执行
```



## 默认值

```js
'start' :'node server.js'
'install': 'node-gyp rebuild'
```



## 钩子   &&  简写形式

`npm `为所有的脚本都加入了 `pre  和 post`两个钩子，包括自定义的脚本

例如：

` build 就有 prebuild  和postbuild`

用户执行`npm run build`的时候，会自动按照下面的顺序执行：

`npm run prebuild && npm run build && npm run postbuild`

 

```shell
npm start是npm run start
npm stop是npm run stop的简写
npm test是npm run test的简写
npm restart是npm run stop && npm run restart && npm run start的简写
```



## 常用的脚本

```js

// 删除目录
"clean": "rimraf dist/*",

// 本地搭建一个 HTTP 服务
"serve": "http-server -p 9090 dist/",

// 打开浏览器
"open:dev": "opener http://localhost:9090",

// 实时刷新
 "livereload": "live-reload --port 9091 dist/",

// 构建 HTML 文件
"build:html": "jade index.jade > dist/index.html",

// 只要 CSS 文件有变动，就重新执行构建
"watch:css": "watch 'npm run build:<span style="color:#f50" id="abc">css</span>' assets/styles/",

// 只要 HTML 文件有变动，就重新执行构建
"watch:html": "watch 'npm run build:html' assets/html",

// 部署到 Amazon S3
"deploy:prod": "s3-cli sync ./dist/ s3://example-com/prod-site/",

// 构建 favicon
"build:favicon": "node scripts/favicon.js",
```





# koa2 基础

#### 获取请求参数

| 请求      | 路径        |                                                    | 获取方式                              |      |
| --------- | ----------- | -------------------------------------------------- | ------------------------------------- | ---- |
| Get       | `/user/:id` |                                                    | `ctx.params.id`(id和路径id是对应名称) |      |
| Get       | /user       | await axios.get('user', {   params: {     id,   }, | ctx.query                             |      |
| Post/put  | /xxx/xxx    |                                                    | ctx.request.body                      |      |
| post 图片 | /upload/    |                                                    | ctx.request.files.文件名              |      |
|           |             |                                                    |                                       |      |



### koa2 启动

```shell
npm i -g koa-generator
npm init -y
koa2 -e projectName

 $ cd koa-learn && npm install   //安装依赖
 
```

初始化完在package.json中可以发现npm script

```js
 "scripts": {
    "start" : "node bin/www",
    "dev": "./node_modules/.bin/nodemon bin/www",    //nodemon启动
    "prd": "pm2 start bin/www",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```



#### async 和 await

```js
router.get('/', async (ctx, next) => {
  await ctx.render('index', {
    title: 'Hello Koa 2!'
  })
})

// async 和 await 一起使用
```

```js
router.get('/testAsync',async (ctx)=>{
  console.log('start',new Date().getTime());
  
  // 因为这里使用了await ，所以程序会卡在这里等待返回结果
  const  a =await new Promise(((resolve, reject) => {
    setTimeout(function () {    //这里只是模拟了一个耗时操作。
      console .log('async a',new Date().getTime());
      resolve('abc')    //resolve的内容就是返回的东西
    },1000)
  }))
  const b = await 12   // 非promise对象会自动转成promise对象
  const c =await Promise.resolve(12) 
  
  ctx.body={
    a
  }
})
```









###  koa router

```js
//server端 自定义router.js
import Router from 'koa-router'

const router =new Router({
  prefix:'/city',
})

router.get('/list',async (ctx)=>{
  ctx.body =['beijing','tianjing']
})

export default router

```

```js
//  server/index.js

...
const cityInterface = require('./interface/city')
async function start () {
  
  app.use(cityInterface.routes()).use(cityInterface.allowedMethods());
}
```

### `koa2 && mongodb `

#### mongodb shell命令

```shell
mongod  //启动mongodb
mongo  //新建命令行启动mongo
show dbs
use db_demo   //新建db_demo数据库
db.goods.insert({'productId':1000,'productName':'aaaa','salePrice':3000})
WriteResult({ "nInserted" : 1 })   //直接插入
//查看
db.goods.find() //查看goods下的所有数据

db.dropDatabase() //删除
```

* NoSQLBooster for MongoDB 可视化

  * 集合右键：import collection
  * Mongoimport  utility

* 命令行文件导入：

  ```shell
  mongoimport -d db_demo -c users --file  路径
  ```

  

#### `node 操作mongodb`

```js
router.get('/article/get', async (ctx, next) => {
  let url =`mongodb://localhost:27017/`
  let dbName='db_jianshu';
  await MongoClient.connect(url)
      .then(async  client=>{
     ctx.body= await client.db(dbName).collection('article').find().toArray();
      })
})


//也就是如下
const client =await MongoClient.connect(url);
const db =client.db(dbName);
const find =db.collection('article').find();
const array =await find.toArray();
ctx.body =array
```



### CORS解决跨域

解决方案：

1. `CORS`

   1. koa版

   9528端口访问3000端口，那么origin就设置为了9528.

   ```js
   const cors =require('koa2-cors');
   app.use(cors({
       origin:['http://localhost:9528'], 
       credentials:true
   }))
   ```



### token 相关

* `koa-jwt`：用来解决哪些路由可以不需要token访问
* `jsonwebtoken`:生成token 以及验证.

1. 后端确认哪些不需要token验证

```js
app.use(koaJwt({secret:SECRET}).unless({
  path:[/^\/api\/login/]
}))
```



2. 在login页面给前端返回token

   1. 根据用户信息生成token

   ```js
      const token = jwt.sign({email},SECRET,{expiresIn: '1h'})
   ```

   2. 在ctx.body中返回token

3. 前端保存，在axios中配置拦截器加入

   ```js
       config.headers.authorization = `Bearer ${token.value}`;
   ```

4. 后端获取token并校验，校验有如下几种可能。

   1. token错误
   2. token失效
   3. token正确

   ```js
   app.use(async (ctx, next) => {
     if (ctx.header && ctx.header.authorization){
       console.log(ctx.header.authorization);
       const parts = ctx.header.authorization.split(' ');
       const schema = parts[0];
       const token = parts[1];
       try {
         //token正确
         jwt.verify(token,SECRET,{
           complete:true
         })
       }catch (e){
         console.log('token 过期')
         //需要向返回的头中添加authorization
       }
     }
     return next().catch((err) => {
       // token错误.
       if (err.status === 401) {
         // 自定义返回结果
         ctx.status = 401;
         ctx.body = {
           code: 401,
           msg: err.message
         }
       } else {
         throw err;
       }
     })
   })
   
   ```

   

## mongodb 相关

### CRUD

#### 查询

```js
const mongoose =require('mongoose');
const Schema =mongoose.Schema;

const UserSchema =new Schema({
    id:{
        type:String,
        required:true
    },
    username:{
        type: String,
        required: true
    },
    password:{
        type:String,
        required:true
    }
})

module.exports =mongoose.model('User',UserSchema);
```

```js
  let result = await User.findOne({username})
  // 如果查到返回该条记录
  // 如果没有查到结果，返回null
```



### 更新

### 删除





假设已经存在一个TallySchema:

```js
    id:{
        type:String,
        required:true
    },
    username:{
        type: String,
        required: true
    },
    data:[
        {
            time:{
                type: Date,
                required: true
            },
            category:{
                type:String,
                required:true
            }
        }
    ]

}
```

`data 数组就是一个子文档`

在db中建立表后，如何向data数组中插入数据？

```js
  Tally.updateOne(
      {
        username:username
      },
      {
        $push:{
          'data':{
            time:Date.now(),
            category:category
          }
        }
      },
      function (err) {
        console.log(err)
      }
  )
```





## 基本操作

* 开启 ：`mongod`
* 连接：新开一个命令行：`mongo`
* 断开：在连接状态输入`exit`

基本命令：数据库=> 集合=>文档

* `show dbs` 查看所有数据库
* `db` 查看当前操作的数据库
* `use 数据库名` 切换到指定数据库
* `show collections `显示当前db的所有集合`[{},{}]`



#### mongodb社区版

今天启动mongdb失败，于是选择卸载重装：

1. `brew uninstall mongodb`

2. `brew install mongodb`的时候报错
   1. MongoDB不再是开源的了，并且已经从Homebrew中移除 
   2. `brew tap mongodb/brew`  
   3. `brew install mongodb-community`
3. 启动：`brew services start mongodb-community`  
4. 关闭：`brew services stop mongodb-community`



```js
{
  qq:{  //数据库
    users:[  //集合
      {name:'zhangsan',age:15}, //文档
      {}
    ]
  },
  baidu:{
    
  }   
}  
```



```js
db.dropDatabase()   //删除db
>db.mycol2.drop()   //删除集合
```



安装:

```shell
brew install mongod
```

[[brew updating 很慢的问题]](https://lug.ustc.edu.cn/wiki/mirrors/help/brew.git)

```shell
替换brew.git:
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git

替换homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
```



* 使用官方的`mongodb包来操作`
* [使用` mongoose`第三方包来操作](http://www.mongoosejs.net/docs/index.html) 

```js
let mongoose =require('mongoose')
let Schema =mongoose.Schema;
mongoose.connect('mongodb://localhost/test1');

//设计表结构，保持数据的完整性，不要有脏数据
let blogSchema = new Schema({
    title:  String,
    author: String,
    body:   String,
    comments: [{ body: String, date: Date }],
    date: { type: Date, default: Date.now },
    hidden: Boolean,
    meta: {
        votes: Number,
        favs:  Number
    }
});

//将文档结构（schema）转换成model
//下面的Blog 被mongoose转换为blogs集合名称
let Blog = mongoose.model('Blog',blogSchema);


```



### mongoose CRUD操作

**mongodb中的表都要写成复数形式，如：user--->users**

```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
//这里该Schema为空好像也可以。
const postSchema = new Schema({
  createdAt:String,
  __v: Number,
  avatar: Object,
  featured: Boolean,
  author: String,
  description: String,
  title: String,
  key: Number
})
//这里Post或者写成Posts都可以,对应的是Mongodb中的Posts集合（collection）
// 建立起Post集合和postSchema的映射。
const PostModel = mongoose.model('Post',postSchema)

const skip = (Number(page)-1)*Number(pagesize);
  PostModel
      .find({})
      .skip(skip)
      .limit(Number(pagesize))
      .exec((error,doc)=>{
        console.log(error)
        console.log(doc)
      })
```



### node通过mongoose查询mongdb

#### findOne()

`findOne()操作中设置的ctx.body值以及return false   只是设置了findOne()的返回值，并不能结束外部函数`

```js
 let resultFlag = await User.findOne({
    username:username
  },function (error,doc) {
   console.log(doc);
    if (doc){
      return true;
    }else {
      return  false;
    }
  })
  if (resultFlag){
    ctx.body={
      code:-11,
      msg:'xxxxxxx'
    }
    return false;
  }


```

#### create

```js
  let newUser =await  User.create({username,password});
```



#### 排序及分页查询

```js
router.get('/',function (req,res,next) {

  let page =parseInt(req.params.page);
  let pageSize =parseInt(req.params.pageSize);
  let sort =req.params.sort;
  let skip =(page-1)*pageSize;
  let params={
     salePrice: {
        $gt:priceGt,
        $lte:priceLt
      }
  }; //传入的查询参数
  let goodsModel =Goods.find(params).skip(skip).limit(pageSize);  //重点
  goodsModel.sort({'salePrice':sort});
  goodsModel.exec(function (err,doc) {
    if (err){
      res.json({
        status:'1',
        msg:err.message
      })
    } else {
      res.json({
        status: '0',
        msg: '',
        result:{
          count:doc.length,
          list:doc
        }
      })
    }
  })
})
```



#### 处理查询异步中无法设置`ctx.body`问题

```js
const findPromise = new Promise((resolve, reject) => {
    PostModel
        .find({})
        .skip(skip)
        .limit(Number(pagesize))
        .exec((error,doc)=>{
          if (error){
            reject(error)
          }else {
            resolve(doc)
          }
        })
  } )
  const result =await findPromise;
  ctx.body =result;
```





## koa上传图片

1. Postman 测试上传图片

   <img src="https://tva1.sinaimg.cn/large/0081Kckwgy1glf2saxoezj30ma09a0ux.jpg" alt="image-20201207101927291" style="zoom:100%;" />

2. koa接收图片

   `koa-body`支持文件、json、form格式的请求体。

   1. `npm i koa-body`

   2. 设置上传路径：

      ```js
      app.use(koaBody({
        multipart:true,
        formidable:{
          uploadDir: __dirname+'/public/uploads',
          keepExtensions:true
        }
      }))
      ```

   3. `ctx.request.files.file` 获取该文件

3. koa服务器公开目录，让前端可以通过url访问图片

   1. 通过koa-static生成图片的url路径，返回给前端。