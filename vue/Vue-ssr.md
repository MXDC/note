#### Client side render

JavaScript 可以动态的生成页面内容，client side render指的就是前端拿到数据后，才用`JavaScript`动态把内容渲染到网页上。

#### Server side render

如果页面由`Javascript`动态生成，网页只有经过浏览器加载并执行`Javascript`，等响应回来之后才能动态产生内容。这样对`SEO`支持不友好,只能搜索到空荡荡的`HTML`。不过`Google`的爬虫支持执行`Javascript`。`Server side render`像`MVC`中页面渲染一样，先渲染好页面，客户端就可以接收到网站的页面。

### vue + webpack项目配置

#### vue-loager + webpack项目配置

1. 创建一个`vue-ssr`目录作为项目目录

2. 在`vue-ssr`目录下面使用`npm`初始化项目

   > 执行`npm init`初始化项目后，会在`vue-ssr`目录项目生成一个`package.json`文件

3. 安装`webpack，vue，vue-loader,`

   同样在`vue-ssr`目录下，执行`npm i webpack vue vue-loader`，运行完后会生成`node_modules`目录。

   > npm WARN vue-loader@15.7.2 requires a peer of css-loader@* but none is installed. You must install peer dependencies yourself.
   >
   > 执行`npm i css-loader  vue-template-compiler`
   
4. 在`vue-ssr`下面创建`src`目录用于存放源码

5. 在`src`目录下面创建`app.vue`文件，内容如下:

   ```vue
   <template>
     <div id="app">
         {{text}}
     </div>
   </template>
   
   <script>
   export default {
       data() {
           return {
               text:"hello"
           }
       },
   }
   </script>
      
   <style>
   #app{
       color: rgb(255, 0, 0);
   }
   </style>
   ```

   

6. 在`src`目录下面创建`webpack.config.js`,配置文件如下:

   ```javascript
   const path = require("path")
   1. 
   module.exports = {
   //2. 入口文件   
    entry:path.join(__dirname,"src/index.js"),
   //2. 出口文件
    output:{
        // 文件名
        filename:"bundle.js",
        // 文件路径   
        path : path.join(__dirname,"dist")
       }
   }
   }
   ```

   7. 创建`index.js`内容如下:

      ```javascript
      import Vue from "vue"
      import App from "./app.vue"

      const root = document.createElement('div')
      document.body.appendChild(root)
      // 将模板挂载到html 元素中
      new Vue({
          render:(h)=>h(App)
      }).$mount(root)
      
      ```
      
   8. 在`package.json`文件中的`scripts`中添加webpack打包命令
   
      ```json
       "scripts": {
          "test": "echo \"Error: no test specified\" && exit 1",
          "build":"webpack --config webpack.config.js"
        },
      ```
   
   9. `webpack.config.js`,配置文件如下:
   
      ```javascript
      const path = require("path")
      const VueLoaderPlugin = require('vue-loader/lib/plugin')
      module.exports = {
          entry:path.join(__dirname,"src/index.js"),
          output:{
              filename:"bundle.js",
              path : path.join(__dirname,"dist")
          },
          module:{
              rules:[
                {test:/\.css$/,use:['style-loader','vue-loader']},
                {test:/\.vue$/,use:['vue-loader']}
              ]
            },
      
          plugins:[
              new VueLoaderPlugin()
          ]
      }
      ```
   
   10. 配置webpack-dev-server
   
      * 安装webpack-dev-server `npm i webpack-dev-server`
   
      * 在`package.jons`中的`scripts`中添加`webpack-dev-server`命令
   
      * 在`webpack.config.js`中配置`target`
   
        > ```
        > target:"web",
        > ```
        >
        > 编译为类浏览器环境里可用**（默认）**
   
      * 添加`cross-env`实现夸平台设置变量
   
        ```json
        "scripts": {
            "test": "echo \"Error: no test specified\" && exit 1",
            "build": "cross-env NODE_ENV=production webpack --config webpack.config.js",
            "dev": "cross-evn NODE_ENV=dev webpack-dev-server --config webpack.config.js"
          },
        ```
   
        这样可以在webpack.config.js接收到NODE_ENV 变量：`process.env.NODE_ENV`
   
      * 修改`webpack.config.js`如下:
   
        ```javascript
        const path = require("path")
        const VueLoaderPlugin = require('vue-loader/lib/plugin')
        
        const isDev = process.env.NODE_ENV === 'development'
        
        const config = {
            target:"web",
            entry:path.join(__dirname,"src/index.js"),
            output:{
                filename:"bundle.js",
                path : path.join(__dirname,"dist")
            },
            module:{
                rules:[
                  {test:/\.css$/,loader:"css-loader"},
                  {test:/\.vue$/,use:['vue-loader']}
                ]
              },
        
            plugins:[
                new VueLoaderPlugin()
            ]
        }
        // 如果是开发环境会添加devServer属性
        if(isDev){
            config.devtool:""
            config.devServer = {
                port:'80',
                host:'0.0.0.0',
                // 热部署
                hot:true,
                overlay:{
                    // 将编译过程中的错误显示到网页
                    errors:true
                }
            }
        }
        
        module.exports = config
        ```
   
   11. 使用`html-webpack-plugin`挂载`js`
   
       没有`html`入口文件是访问不了的,`htmlwebpack-plugin`会将编译好的bundle.js挂载到`index.hmtl`(没有就创建)中
   
       * 安装`npm i html-webpack-plugin`
   
       * 在`webpack.config.js`引用
   
         ```javascript
         const HTMLPlugin = require('html-webpack-plugin')
         const webpack = require('webpack')
         ......
         
             plugins:[
                 new VueLoaderPlugin(),
                 new HTMLPlugin(),
                 // 定义全局变量
                 new webpack.DefinePlugin({
                     'process.env':{
                         NODE_ENV: isDev ? '"development"' :'"production"'
                     }
                 })
             ]
         ```
   
         

