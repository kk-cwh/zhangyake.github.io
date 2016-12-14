title: webpack入门
date: 2016-03-30 11:12:42
tags: 前端
original: false
---

#### 什么是 webpack？
[webpack](http://webpack.github.io/docs/)是近期最火的一款模块加载器兼打包工具，它能把各种资源，例如JS（含JSX）、coffee、样式（含less/sass）、图片等都作为模块来使用和处理。它就是一个前端工具，可以让我们进行各种模块加载，预处理后，再打包。任何静态资源都可以视作模块，然后模块之间也可以相互依赖，通过webpack对模块进行处理后，可以打包成我们想要的静态资源。
<!-- more -->

1. 安装webpack

  首先需要安装[node.js](https://nodejs.org)环境,见[node官网](https://nodejs.org).
  安装nodejs后使用npm 安装 webpack命令

        $ npm install webpack -g 
  这样webpack 命令即能在全局环境下使用

2. webpack小示例第一步
    
  创建空目录Demo文件夹 文件夹中创建app空文件夹
  在app文件夹中创建entry.js文件
  文件内容如下 --entry.js
  
        document.write("It works.");

  在app文件夹中创建 index.html 
  文件内容如下 --index.html 
  
        <!DOCTYPE html>
        <html lang="en">
        <head>
          <meta charset="UTF-8">
          <title>webpack入门</title>
        </head>
        <body>
          <script type="text/javascript" src="bundle.js" charset="utf-8"></script>
        </body>
        </html>
  执行命令：
  
        $ cd Demo/app
        $ webpack ./entry.js bundle.js 
该命令会对entry.js文件编译并创建一个bundle.js文件
如果成功的话，它会显示如下：

        Hash: ca188ee5789bb780fcec
        Version: webpack 1.13.0
        Time: 65ms
            Asset     Size  Chunks             Chunk Names
        bundle.js  1.42 kB       0  [emitted]  main
          [0] ./entry.js 28 bytes {0} [built]
在浏览器中打开index.html 显示 It works.
3. 第二步依赖文件加载
    
  在app文件夹下添加content.js内容为

        module.exports = "It works from content.js.";
  更改entry.js文件内容为:

        document.write(require("./content.js"));
  执行命令：
    
        $ webpack ./entry.js bundle.js 
    
  刷新浏览器index.html页面, 显示It works from content.js.
*webpack会分析你的输入文件的依赖的其他文件。这些文件（称为模块）都会最终包含在你的bundle.js中。webpack会给每个模块的一个独特的ID以及保存所有模块的ID以便在bundle.js文件访问。仅在启动时执行输入模块，在运行时提供需要的功能，并在需要时执行依赖.*
4. 第一次使用loaders
  
  我们要添加一个CSS文件到我们的示例中
    webpack只能处理JavaScript本身，所以我们需要css-loader去装载CSS文件,同样也需要style-loader。
*执行命令*
    
        $ npm install css-loader style-loader
   在app文件夹下添加style.css文件
    文件内容如下--style.css

        body {
          background: yellow;
        }
更新entry.js文件

        require("!style!css!./style.css");
        document.write(require("./content.js"));
执行命令：

        $ webpack ./entry.js bundle.js 
 刷新浏览器index.html页面, 显示带有黄色背景的It works from content.js.
 
 webpack只能处理JavaScript本身，style.css通过!style!css!装载机管道以特定的方式中改变输出 bundle.js 文件的内容。这些转换后的结果是一个JavaScript模块。
如果我们不想使用 require("!style!css!./style.css");
而想直接使用require("./style.css");
更新entry.js文件内容为:

        require("./style.css");
        document.write(require("./content.js"));
执行命令时要绑定加载模块：

        $ webpack ./entry.js bundle.js --module-bind 'css=style!css'
   刷新浏览器index.html页面, 显示同样的效果。
  某些环境下这里可能要用双引号 "css=style!css"
        
5. 使用配置文件 **webpack.config.js**

  在Demo文件夹下创建webpack.config.js
  文件内容如下:
    
        module.exports = {
            entry: "./app/entry.js",
            output: {
                path: __dirname,
                filename: "./app/bundle.js"
            },
            module: {
                loaders: [
                    { test: /\.css$/, loader: "style!css" }
                ]
            }
        };
    
  现在只需要在Demo目录下执行命令:

        $ webpack
    执行成功会显示:
        Hash: ab14e3789227f2cbf6c0
        Version: webpack 1.13.0
        Time: 955ms
                Asset     Size  Chunks            Chunk Names
        ./app/bundle.js  11.8 kB       0  [emitted]  main
           [0] ./app/entry.js 67 bytes {0} [built]
           [5] ./app/content.js 45 bytes {0} [built]
              + 4 hidden modules

   **webpack会自动加载当前目录下的webpack.config.js文件**
   
6. 漂亮的输出
随着项目的增长，编译可能需要更长的时间。所以我们要展示一些进度条、颜色…可以使用命令
    
        $webpack --progress --colors
7. 使用watch model
使用watch model模式时，可理解为监听模式,如果检测到任何文件更改，它将再次运行编译。

        $webpack --watch 
8. 使用webpack开发服务器

        // npm 全局安装webpack开发服务器
        $ npm install webpack-dev-server -g
    在Demo文件夹下执行
        $ webpack-dev-server --progress --colors
    webpack-dev-server 会在本地提供一个静态文件服务器 
    http://localhost:8080/webpack-dev-server/
    同时内部也在使用webpack的watc模式自动编译更新
    浏览器中打开http://localhost:8080/webpack-dev-server/
    只要文件有更新 浏览器也会自动刷新页面。
    
参考文档:http://webpack.github.io/docs/tutorials/getting-started