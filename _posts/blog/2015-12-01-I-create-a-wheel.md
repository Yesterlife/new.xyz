---
layout: post
category: blog
title: 造了个轮子
description: 15年的最后一个月 
---
![](/images/2015_12/a_wheel.jpg)

前段时间写了几篇关于前端及前端工程化的文章：  

-  [上帝为你开了一扇窗](http://dhong.co/a-window-or-a-door/)  
-  [webpack code spliting](http://dhong.co/webpack-code-splitting/)  
-  [css modules](http://dhong.co/css-modules/)

上述方案，自己练手可以，但内部推广使用还不够：  

-  配置复杂  
-  难以更新和维护
-  耦合行太强

如果不懂我在说什么，参考下[YO](http://yeoman.io)。  

所以，想着把上述方案整合起来，打造成一个工具，类似这样：

```
$:  pepper
    ?  选择要执行的任务:
    >  初始化新项目
       生成新页面模版
       生成新组件模版
       开发调试
       项目打包
       
```

好，目标有了，接下来该如何做呢？  

1. 依赖分离   
   作为一个工具，尽可能的减少外部依赖。之前的项目结构是这样的：  
   
   ```
    .                             
    ├── /node_modules/
    ├── /src/
    │   ├── /pages/
    │   ├── /components/
    │   ├── /assets/
    │   ├── /scss/
    │   └── /utils/
    ├── webpack.config.js 
    ├── webpack.config.prod.js 
    ├── index.template.html 
    └── package.json

    ```
    分离后是这样的
    
    ```
    .                            
    ├── /node_modules/
    ├── /src/
    │   ├── /pages/
    │   ├── /components/
    │   ├── /assets/
    │   ├── /scss/
    │   └── /utils/
    ├── pepper.json
    └── package.json

    ```
    这里只保留`package.json`中的`dependencies`，移除了下面所有的`devDenpendencies`
      
    ```
    "devDependencies": {
        "autoprefixer-loader": "^3.1.0",
        "babel-eslint": "^4.1.3",
        "babel-loader": "^5.3.2",
        "babel-plugin-react-transform": "^1.1.1",
        "css-loader": "^0.21.0",
        "eslint-loader": "^1.1.1",
        "exports-loader": "^0.6.2",
        "extract-text-webpack-plugin": "^0.8.2",
        "file-loader": "^0.8.1",
        "html-webpack-plugin": "^1.6.2",
        "imports-loader": "^0.6.5",
        "json-loader": "^0.5.2",
        "less": "^2.5.3",
        "less-loader": "^2.2.2",
        "node-sass": "^3.4.2",
        "react-proxy-loader": "^0.3.4",
        "react-transform-catch-errors": "^1.0.0",
        "react-transform-hmr": "^1.0.1",
        "redbox-react": "^1.2.0",
        "rimraf": "^2.4.3",
        "sass-loader": "^3.1.1",
        "style-loader": "^0.13.0",
        "url-loader": "^0.5.5",
        "webpack": "^1.12.9",
        "webpack-dev-middleware": "^1.4.0",
        "webpack-hot-middleware": "^2.5.0"
      }
    ```
     
    这样的话，项目本身的依赖就很少了： 
     
    ```
    // package.json
    {
      "name": "demo2",
      "version": "0.1.0",
      "description": "front end solution powered by pepper",
      "author": "pepper",
      "license": "ISC",
      "dependencies": {
        "classnames": "^1.2.0",
        "react": "^0.14.1",
        "react-dom": "^0.14.1",
        "react-router-component": "^0.27.2",
        "react-slick": "^0.9.2",
        "reqwest": "^2.0.5"
      }
    }
    ```
  2.  开发调试  
  
      *  初始化新项目  
      
          ```
        $  pepper init [project_name]
          ```
          将生成如下项目模版：  
         
          ```
        .
        ├── /node_modules/
        ├── /src/
        │   ├── /pages/
        │   ├── /components/
        │   ├── /assets/
        │   ├── /scss/
        │   └── /utils/
        ├── pepper.json
        └── package.json
           ```
           
     *  生成新页面
     
        ```
        $  pepper page [new_page_name]
       
        // exec pepper page page1 
       
        ├── /src/
        │   ├── /pages/
            │   ├── /page1/
                │   ├── index.jsx
                │   ├── style.scss
         ```
     *  生成新组件  
     
        ```
        $  pepper component [new_page_name]
       
        // exec pepper component Header 
       
        ├── /src/
        │   ├── /components/
            │   ├── /Header/
                │   ├── index.jsx
                │   ├── style.scss
         ```
    *  实时预览
    
       ```
       $  pepper start
       ```
  3.  配置文件  
       [我知道你想说什么，因为他们都在配置文件里](https://www.npmjs.com/package/we-pepper)

  4.  打包构建
      *  测试环境  
      
      ```
      $  pepper test
      ```
      
      *  预上线环境   
       
      ```
      $  pepper pre
      ```
      *  正式环境  
      
      ```
      pepper release
      ```
  5.  自身完善  
  
      *  流程引导，类似YO  
      
      *  命令行参数   
      
          ```
          $  pepper                          // help ?
          $  pepper init [app_name]          // create seed proj
          $  pepper page [page_name]         // create new page
          $  pepper component [comp_name]    // create new component
          $  pepper start                    // debug start
          $  pepper test                     // test mode build
          $  pepper pre                      // pre mode build
          $  pepper release                  // release mode build
          ```

这是继上个月之后的又一版优化。不知道为啥，就是没心思仔细研读[yeoman](http://yeoman.io)的文档，学习如何定义自己的Generator，索性自己动手造了个轮子。

那如何使用呢？  

```
$ npm install we-pepper -g;// make sure webpack already install globally 
 
$ pepper init test; // then to have a try in test dir 
```

---

15年的最后一个月，简单回顾了下这一年里自己在技术领域学到了什么：

*  React, Flux, Browserify
*  Webpack, ES6, Redux
*  各种上述组合，前端乱炖

三年里，技术学习曲线日渐平缓，新的知识贵在创新，旧的知识在于积累。

像React，Docker之类的技术创新，学习它不是为了赶时髦。一种解决方案的诞生，自有其产生和存在，抑或流行的原因。不学习，你就落后了。

对于新技术的尝试，绝不应止步所在公司内的保守策略。

要回家了，下次细谈～

```
post@Tue Dec  1 21:21:22 CST 2015
```



