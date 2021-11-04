

# CommonJS AMD CMD ES6

## CommonJs &&Node.JS

1. Node.Js使用CommonJs
2. 使用时，用module.exports 输出，require 输入
3. 用<font color =red>**同步**的方式</font>加载module，主要是因为服务器端文件都在本地，读取很快。
4. 



### AMD  && require.js

> 异步模块定义（async module definition），以浏览器为目标环境，主要考虑了异步的问题。

1. AMD使用异步加载module
2. require.js实现AMD规范
3. 依赖前置，提前执行



###  UMD 通用模块定义

为了统一CommonJs 和 AMD 产生的。



<font size =5>CMD && sea.js</font>

1. CMD也是<font color =red>异步加载</font>
2. Sea.js推广中产生的
3. 依赖就近，延迟执行



<font size =5>ES6</font>

<font color =red>es6中的模块不是对象，在编译时就引入模块代码，而不是在运行加载时。</font>

```js
/** export default **/
//定义输出
export default { basicNum, add };
//引入
import math from './math';
function test(ele) {
    ele.textContent = math.add(99 + math.basicNum);
}

```



<font size =5>ES6 和 CommonJs 模块对比</font>

1. **CommonJS输出的是值拷贝，ES6输出的是值引用**
   * 也就是说CommonJs一旦输出，模块内部的输出就无法影响到该值
   * ES6会影响到
2. **CommonJs是运行时加载，ES6是编译时输出接口**
   * 运行时加载：CommonJS模块就是对象，所以只用在运行时才能产生该对象。在输入时，<font color =red>先加载整个模块生成一个对象，然后再从这个对象上读取方法</font>
   * 编译时加载：ES6模块不是对象，而只是一段代码，所以可以只加载某个输出值，而不用加载整个模块





# 安装

1. 先安装Node.js

2. 安装webpack

   1. 本地安装 vs 全局安装

      > **不推荐全局安装webpack，这会将webpack锁定到指定版本，在使用不同的webpack版本的项目，导致构建失败**

   ```shell
   npm i --save-dev webpack
   npm i --save-dev webpack@<version>
   
   npm i --global webpack  //全局安装
   ```

   

# 起步

```shell
npm init -y
npm i webpack webpack-cli --save-dev
```

> '分发(dist)':构建过程产生的代码最小化和优化后"输出的目录",最终在浏览器加载

> **安装要打包到生产环境的安装包时，使用`npm  install --save `**
>
> 安装用于开发环境的安装包,使用`npm install --save-dev` 也就是说--save是本地，-dev是开发环境



启动有两种方式：

* cli默认启动

  `在命令行的文件目录下： npx webpack`:会将脚本作为entry，output为main.js

* cli通过webpack.config.js启动：

   在命令行的文件目录下：`npx webpack --config webpack.config.js`，默认如果有config文件存在，会自动使用

```js
const path =require('path')
module.exports ={
    entry: './src/index.js',
    output: {
        filename: "bundle.js",
        path: path.resolve(__dirname,'dist')
    }
}
```



* NPM脚本方式

  ​	**` npm run build`**

  ​	解决了CLI启动不方便的问题:

  ```json
  //package.json 
  
  {
      "name": "webpack-demo",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
  +     "build": "webpack"     
      },
      "keywords": [],
      "author": "",
      "license": "ISC",
      "devDependencies": {
        "webpack": "^4.0.1",
        "webpack-cli": "^2.0.9",
        "lodash": "^4.17.5"
      }
    }
  
  ```

  

# 管理css，图片，字体等

> webpack 最出色的功能之一就是，除了 JavaScript，还可以通过 loader *引入任何其他类型的文件*。

## 加载css

1. `npm install --save-dev style-loader css-loader`

2. 在webpack.config.js中：

```js
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
+   module: {
+     rules: [
+       {
+         test: /\.css$/,
+         use: [
+           'style-loader',
+           'css-loader'
+         ]
+       }
+     ]
+   }
  };
```

3. index.js中

   ```js
   import 'xxx.css'  //引入css文件
   ```



## 加载图片

1. `npm install --save-dev file-loader`

2. 在webpack.config.js中：

```js
 const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
    module: {
      rules: [
        {
          test: /\.css$/,
          use: [
            'style-loader',
            'css-loader'
          ]
        },
+       {
+         test: /\.(png|svg|jpg|gif)$/,
+         use: [
+           'file-loader'
+         ]
+       }
      ]
    }
  };
```

3. index.js

   ```js
   + import Icon from './icon.png';
   ```

   所有的图片将会被转换成：b021a867bca5c3d0505c46ee85460916.png  这种文件名 被放置在dist目录下。

   而且：css中`url('./my-image.png')`这种也会被转换为**最终形式的路径**



>  [image-webpack-loader](https://github.com/tcoopman/image-webpack-loader) *和* [url-loader](https://www.webpackjs.com/loaders/url-loader)用来压缩和优化图像





## 加载字体

file-loader 和 url-loader可以加载字体

webpack.config.js

```js
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
    module: {
      rules: [
        {
          test: /\.css$/,
          use: [
            'style-loader',
            'css-loader'
          ]
        },
        {
          test: /\.(png|svg|jpg|gif)$/,
          use: [
            'file-loader'
          ]
        },
+       {
+         test: /\.(woff|woff2|eot|ttf|otf)$/,
+         use: [
+           'file-loader'
+         ]
+       }
      ]
    }
  };
```



## 加载数据

加载CSV,TSV和XML 需要：csv-loader  和xml-loader  过程和上面类似，不再赘述。





# 管理输出

## 输出多个bundle.js文件

webpack.config.js：

```js
  const path = require('path');

  module.exports = {
+   entry: {
+     app: './src/index.js',    //entry1
+     print: './src/print.js'   //entry2
+   },
    output: {
+     filename: '[name].bundle.js',    //根据entry名动态生成output名
      path: path.resolve(__dirname, 'dist')
    }
  };
```

## HtmlWebpackPlugin

1. Htmlwebpackplugin 会默认生成index.html，如果原来有就会替换原先的。
2. 所有的bundle都会自动添加到html中

安装：

1. ` npm install --save-dev html-webpack-plugin`

2. 

   ```js
     const path = require('path');
   + const HtmlWebpackPlugin = require('html-webpack-plugin');
   
     module.exports = {
       entry: {
         app: './src/index.js',
         print: './src/print.js'
       },
   +   plugins: [
   +     new HtmlWebpackPlugin({
   +       title: 'Output Management'
   +     })
   +   ],
       output: {
         filename: '[name].bundle.js',
         path: path.resolve(__dirname, 'dist')
       }
     };
   ```

   

## 清理/dist文件夹:`clean-webpack-plugin`

> 原理：通过 manifest，webpack 能够对「你的模块映射到输出 bundle 的过程」保持追踪

` npm install clean-webpack-plugin --save-dev`

```js
//webpack.config.js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
+ const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    plugins: [
+     new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Output Management'
      })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```

# 开发

## source map

> webpack打包后，很难追踪到源码中的错误。source map就是用来将编译后的代码映射回原始源代码

```js
//webpack.config.js
const path =require('path');
const HtmlWebpackPlugin =require('html-webpack-plugin');
const {CleanWebpackPlugin} =require('clean-webpack-plugin');
module.exports={
    entry: {
        app:'./src/index.js',
        print:'./src/print.js'
    },
    devtool: "inline-source-map",
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            title: "Webpack App"
        }),

    ],
    output: {
        filename:'[name].bundle.js',
        path: path.resolve(__dirname,'dist')
    }
}
```



## 代码改变后自动编译

有以下几种工具：

* webpack's watch Mode

  1. 在`package.json中`添加：

  ```js
    {
      "name": "development",
      "version": "1.0.0",
      "description": "",
      "main": "webpack.config.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
  +     "watch": "webpack --watch", 
        "build": "webpack"
      },
      "keywords": [],
      "author": "",
      "license": "ISC",
      "devDependencies": {
        "clean-webpack-plugin": "^0.1.16",
        "css-loader": "^0.28.4",
        "csv-loader": "^2.1.1",
        "file-loader": "^0.11.2",
        "html-webpack-plugin": "^2.29.0",
        "style-loader": "^0.18.2",
        "webpack": "^3.0.0",
        "xml-loader": "^1.2.1"
      }
    }
  ```

  2. ` npm run watch`

  ，就会看到 webpack 编译代码，然而却不会退出命令行。这是因为 script 脚本还在观察文件。

* **<font color='red'>Webpack-dev-server</font>****  最常用

  提供了一个简单的web服务器，并且实时重新加载（live reloading）

  1. `npm install --save-dev webpack-dev-server`

  2. 以下配置告知 `webpack-dev-server`，在 `localhost:8080` 下建立服务，将 `dist` 目录下的文件，作为可访问文件。

  3. ```js
     //webpack.config.js
      const path = require('path');
       const HtmlWebpackPlugin = require('html-webpack-plugin');
       const CleanWebpackPlugin = require('clean-webpack-plugin');
     
       module.exports = {
         entry: {
           app: './src/index.js',
           print: './src/print.js'
         },
         devtool: 'inline-source-map',
     +   devServer: {
     +     contentBase: './dist'
     +   },
         plugins: [
           new CleanWebpackPlugin(['dist']),
           new HtmlWebpackPlugin({
             title: 'Development'
           })
         ],
         output: {
           filename: '[name].bundle.js',
           path: path.resolve(__dirname, 'dist')
         }
       };
     ```

     3. `package.json`

        ```json
          {
            "name": "development",
            "version": "1.0.0",
            "description": "",
            "main": "webpack.config.js",
            "scripts": {
              "test": "echo \"Error: no test specified\" && exit 1",
              "watch": "webpack --watch",
        +     "start": "webpack-dev-server --open",
              "build": "webpack"
            },
            "keywords": [],
            "author": "",
            "license": "ISC",
            "devDependencies": {
              "clean-webpack-plugin": "^0.1.16",
              "css-loader": "^0.28.4",
              "csv-loader": "^2.1.1",
              "file-loader": "^0.11.2",
              "html-webpack-plugin": "^2.29.0",
              "style-loader": "^0.18.2",
              "webpack": "^3.0.0",
              "xml-loader": "^1.2.1"
            }
          }
        ```

        

* Webpack-dev-middleware

  是一个容器（wrapper），将webpack处理后的文件传递给一个服务器。

  



# 热模块替换（`Hot Module Replacement`     HMR）

> 只适用于开发环境中

官方文档中的demo已经过期，似乎在webpack-dev-server中已经集成了。



# tree shaking

> 用于描述移除js上下文中的未引用的代码（dead-code）

例如：新建一个math.js

```js
export function sqare(x) {
    return x*x;
}
export function cube(x) {
    return x*x*x;
}
```

Math.js中导出了两个方法，但是在index.js中只import 一个（比如`square()`），这样的话`cube()`:就是<font color=red>所谓的**未引用代码(dead code):**</font>

提示如下：

```js
/* 1 */
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
/* unused harmony export square */
/* harmony export (immutable) */ __webpack_exports__["a"] = cube;
function square(x) {
  return x * x;
}

function cube(x) {
  return x * x * x;
}
```

## 压缩输出

通过启用uglifyjs压缩插件来压缩代码：

```js
//webpack.config.js

const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
- }
+ },
+ mode: "production"
};
```



## 将文件标记为无副作用(side-effect-free）

> *副作用」的定义是，在导入时会执行特殊行为的代码，而不是仅仅暴露一个 export 或多个 export。举例说明，例如 polyfill，它影响全局作用域，并且通常不提供 export。*





# 生产环境构建

开发环境（development） 和生产环境（production）构建目标差异很大。

开发环境：

1. 实时重新加载（live reloading）
2. 热模块替代（HMR）
3. local server

生产环境：

需要更小的bundle。更轻量的source map



> <font color=red>通常建议为每个环境编写独立的webpack配置 </font>将公共部分抽取出来，通过`webpack-merge的工具合并`

1. 安装`webpack-merge`

   `npm install --save-dev webpack-merge`

2. project

   ```
     webpack-demo
     |- package.json
   - |- webpack.config.js
   + |- webpack.common.js  //通用配置
   + |- webpack.dev.js    //开发环境配置
   + |- webpack.prod.js		//生产环境配置
     |- /dist
     |- /src
       |- index.js
       |- math.js
     |- /node_modules
   ```

3. webpack.common.js

   ```js
   + const path = require('path');
   + const CleanWebpackPlugin = require('clean-webpack-plugin');
   + const HtmlWebpackPlugin = require('html-webpack-plugin');
   +
   + module.exports = {
   +   entry: {
   +     app: './src/index.js'
   +   },
   +   plugins: [
   +     new CleanWebpackPlugin(['dist']),
   +     new HtmlWebpackPlugin({
   +       title: 'Production'
   +     })
   +   ],
   +   output: {
   +     filename: '[name].bundle.js',
   +     path: path.resolve(__dirname, 'dist')
   +   }
   + };
   ```

4. webpack.dev.js

   ```js
    const merge = require('webpack-merge');
   + const common = require('./webpack.common.js');
   +
   + module.exports = merge(common, {
   +   devtool: 'inline-source-map',      //开发环境是inline-source-map
   +   devServer: {
   +     contentBase: './dist'
   +   }
   + });
   ```

5. Webpack.prod.js

   **source map 使用source-map  而不是webpack.dev.js中的inline-source-map**

   ```js
   + const merge = require('webpack-merge');
   + const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
   + const common = require('./webpack.common.js');
   +
   + module.exports = merge(common, {
   +   plugins: [
   +     new UglifyJSPlugin({
    				 sourceMap: true       
   				})
   +   ]
   + });
   ```

6. Package.json

   npm start  ====》开发环境

   npm run build  ====》生产环境脚本

   ```js
   {
       "name": "development",
       "version": "1.0.0",
       "description": "",
       "main": "webpack.config.js",
       "scripts": {
     
   +     "start": "webpack-dev-server --open --config webpack.dev.js",
   
   +     "build": "webpack --config webpack.prod.js"
       },
       "keywords": [],
       "author": "",
       "license": "ISC",
       "devDependencies": {
         "clean-webpack-plugin": "^0.1.17",
         "css-loader": "^0.28.4",
         "csv-loader": "^2.1.1",
         "express": "^4.15.3",
         "file-loader": "^0.11.2",
         "html-webpack-plugin": "^2.29.0",
         "style-loader": "^0.18.2",
         "webpack": "^3.0.0",
         "webpack-dev-middleware": "^1.12.0",
         "webpack-dev-server": "^2.9.1",
         "webpack-merge": "^4.1.0",
         "xml-loader": "^1.2.1"
       }
     }
   ```



# 代码分离

代码分离就是将代码分离到不同的bundle中，然后按需加载或者并行加载这些文件。

有三种常用的代码分离方法：

* 入口起点：使用 entry 手动分离
* 防止重复：使用 `CommonsChunkPlugin`去重和分离chunk
* 动态导入：通过模块内联函数来调用分离代码

## entry point

即定义多个entry，前文也有例子

```js
const path = require('path');
const HTMLWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: {
    index: './src/index.js',    //entry 1
    another: './src/another-module.js'    //entry2
  plugins: [
    new HTMLWebpackPlugin({
      title: 'Code Splitting'
    })
  ],
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};

```

缺点：

>  **入口chunks重复引入模块。比如another-module.js和 index.js中都引入lodash模块****



## 防止重复(prevent duplication)

CommonChunkPlugin 插件可以将公共的依赖模块提取到已有的入口chunk中，或者提取到一个新生成的chunk。

```js
//webpack.config.js
  const path = require('path');
+ const webpack = require('webpack');
  const HTMLWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
    entry: {
      index: './src/index.js',
      another: './src/another-module.js'
    },
    plugins: [
      new HTMLWebpackPlugin({
        title: 'Code Splitting'
-     })
+     }),
+     new webpack.optimize.CommonsChunkPlugin({
+       name: 'common' // 指定公共 bundle 的名称。
+     })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```



## 动态导入  参考官方文档





# 懒加载

例如：点击按钮发生计算，计算中会调用lodash库。可以让lodash库在第一次发生交互时调用，而不是在页面载入时就调用。

```js
button.onclick = e => import(/* webpackChunkName: "print" */ './print').then(module => {
var print = module.default;
```





# 缓存

> 如果我们在部署新版本时不更改资源的文件名，浏览器可能会认为它没有被更新，就会使用它的缓存版本。

所以需要我们通过必要的配置，以确保webpack生成的文件能被客户端缓存，而在文件内容变化后，能够请求到新的文件。

# 待续







# 自制vue toast插件

## 新建文件夹，npm初始化，安装webpack

```shell
npm init -y
npm install
npm i --save-dev webpack
```



## 测试版的index.html来编辑样式

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .toast-container{
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;

            display: flex;
            justify-content: center;
            align-items: center;
        }
        .toast{
            width: 200px;
            height: 60px;
            font-size:2rem;
            display: flex;
            justify-content: center;
            align-items: center;
            background: rgba(0,0,0,0.5);
            border-radius: 20px
        ;
        }
    </style>
</head>
<body>
    <div class="toast-container">
        <div class="toast">
            <span>hello toast</span>
        </div>
    </div>
</body>
</html>
```

