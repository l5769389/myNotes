# 模块打包



# 3 资源的输入输出

1. entry、chunk、bundle

通常情况下一个entry对应一个chunk。如果只有一个entry。那么chrunk name：`main`。如果有多个那么需要分开命名。



1. entry
   1. context：设置entry的前缀。只能为字符串。如果多个entry，可以省略。
   2. entry：可以是string、array、object、function。**如果是多对象只能是对象形式**

```js
// context的用法
module.exports ={
  context:path.join(__dirname,'./src'),
  entry:'./scripts/index.js',
}

//多个entry
module.exports ={
  entry:{
    index:'./src/index.js', // chrunkname: index . entry路径为./src/index.js
    lib:'./src/lib.js'
  }
}


```



2. 实际使用
   1. spa应用：通常只有一个entry。当一个bundle超过250kb时，webpack警告
   2. 多页应用：每个页面都配置一个entry
   3. 可以将应用中的第三方模块提取成vendor（供应商）

```js
module.exports ={
  entry:{
    pageA:'./src/pageA.js',
    pageB:'./src/pageB.js',
    vendor:['react','react-router'],
  }
}


```





3. output

   1. `filename`字符串或者相对路径。

   ```js
   module.exports ={
     entry:{
       index:'./src/index.js',
       lib:'./src/lib.js'
     }
     output:{
     	filename:[name].js   // 会生成名为index lib的bundle.或者如下：
       filename:[name]@[chrunkhash].js  //加入hash来控制缓存。
   }
   }
   ```

   2. `path`:指定资源的输出位置。绝对路径。通常就是:`path.join(__dirname,'./dist')`.**通常不需要配置**

   3. `publicPath`有两个作用：

      1. 输出位置。同上`path`

      2. 请求位置前缀，页面资源分为：

         1. HTML页面直接请求的，如：script标签加载的js文件
         2. js代码中的请求（ajax）、css中请求的字体图片。**publicPath就是规定了这部分的请求位置**

      3. 有以下几种形式：

         1. 以当前请求的html为相对路径

            ```js
            // 当前HTML地址为：https://liuj.online/app/index.html
            // ajax请求的资源名：0.chunk.js
            publicPath:'' //https://liuj.online/app/0.chunk.js 
            publicPath:'./js' //https://liuj.online/app/js/0.chunk.js 
            ```

         2. 以Host为基础路径

         ```js
         // 当前HTML地址为：https://liuj.online/app/index.html
         // ajax请求的资源名：0.chunk.js
         publicPath:'/' //https://liuj.online/0.chunk.js 
         publicPath:'/js/' //https://liuj.online/js/0.chunk.js 
         ```

         3. cdn上的绝对路径

         ```js
         // 当前HTML地址为：https://liuj.online/app/index.html
         // ajax请求的资源名：0.chunk.js
         publicPath:'http://cdn.com/' //http://cdn.com/0.chunk.js 
         ```

      4. `webpack-dev-server`中的`publicPath`是指定了`webpack-dev-server`的静态资源服务路径。如下：虽然打包bundle在dist/目录下。但是由于devServer规定了publicPath是`/asset/`，只能访问：`/asset/bundle.js`.**所以通常要保持path 和 devServer中的publicPath一致**

         ```js
         module.exports ={
           entry:{
             index:'./src/index.js',
           }
           output:{
           	filename:'bundle.js'// 
             path.join(__dirname,'./dist')
         },
           devServer:{
             publicPath:'/asset/',
             port:3000,  
           }
         }
         ```

         

# 4. 预处理器

1. Loader: 本质上是一个函数。支持链式调用

   ```js
   module.exports={
     module:{
       rules:[
         {
           test:/.css$/,
           use:['style-loader','css-loader'] //先css-loader 后 style-loader
         }
       ]
     }
   }
   ```

   

   1. `exclude`/`include`

      exclude优先级比include更高。所以。下例中。会包括src目录下的非lib目录。

   ```js
   module.exports={
     module:{
       rules:[
         {
           test:/.css$/,
           use:['style-loader','css-loader'] //先css-loader 后 style-loader
           exclude:/src\/lib/,
           include:/src/ 
         }
       ]
     }
   }
   ```

   

   2. `resource / issuer`

      如:`import './style.css'`中被加载模块是 **resource**，加载者是  **issuer**

      在上面的代码中：test、exclude、include 本质上是对于resource的配置。 issuer是对加载者的配置。

      ```js
      module.exports={
        module:{
          rules:[
            {
              use:['style-loader','css-loader'], //先css-loader 后 style-loader
              resource:{
                  test:/.css$/,
                  exclude:/src/,
              },
              issuer:{
                  test:/.js$/,
                	exclude:/node_modules/,
              }
            }
          ]
        }
      }
      ```

      

   3. `enforce`

      webpack中的loader按照执行顺序分为：pre  ~~inline~~ normal post 。如一下eslint：

      ```js
       rules:[
            {
              test:/.js$/,
              use:'eslint-loader'
              enforce:'pre'  //强制使得其在所有代码之前执行。
            }
          ]
      ```

      



## 常用loader

1.  babel-loader ： es6转为es6

   1. 包含以下几个模块：
      1. babel-loader: babel和webpack协同模块
      2. @babel/core: 编译器核心模块
      3. @babel/preset-env: 预置器

   2. 可以优化

2. ts-loader

3. file-loader：打包文件类型资源。返回publicPath

4. url-loader: 和file-loader类似。用户可以设置一个阈值。**大于该阈值时和file-loader一样。小于时返回该文件的base64编码。**

