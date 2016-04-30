---
layout: post
title: pepper doc
category: blog
description: 都说太复杂了，难道真是这个样子吗
---

[![NPM Version](http://img.shields.io/npm/v/we-pepper.svg?style=flat)](https://www.npmjs.org/package/we-pepper)
[![NPM Downloads](https://img.shields.io/npm/dm/we-pepper.svg?style=flat)](https://www.npmjs.org/package/we-pepper)

*  环境依赖 `nodejs` , `npm`
*  Pepper 安装   
```
npm install we-pepper -g # install pepper globally
```
*  项目初始化    

```
pepper init [project_name] 

# create a starter project
.
├── /dist/                              # 代码打包目录
├── /node_modules/                      # node依赖包
├── /src/                               # 源码目录
│   ├── /pages/                         # 页面
│   ├── /components/                    # 公用组件
│   ├── /assets/                        # 图片、字体资源
│   ├── /scss/                          # 公用样式    
│   └── /utils/                         # 公用JS模块
├── pepper.config.js                    # pepper的配置文件
├── index.template.html                 # 首页HTML模版, 可选
└── package.json                        # 这个就不用说了吧
```

*  开发调试 
 
```
pepper start
# or
pepper start -p PORT

```
该命令会启动一个 `express` 服务，负责托管所有的静态资源，支持 Code Hot Load，支持 Mock 服务，支持路由和数据格式的配置。
  
   **注意**：该模式下，静态资源都缓存在内存中，配置的`build`目录里是看不到静态资源的  

*  流程引导  命令行下直接输入 `pepper` 
 
```
? 选择要执行的任务:  (Use arrow keys)
❯ 项目打包
开发调试
初始化新项目 (es5)
初始化新项目 (es6 & react & react-router)
初始化新项目 (es6 & react & react-router & redux)
创建新页面
创建新组件
升级pepper

```
#### 配置清单  

```
{
    "host": "0.0.0.0",
    "port": "8080",
    "base": "src",
    "build": "dist",
    "static": { // CDN domain, or just leave it blank if not using
        "start": "/",
        "test": "/",
        "pre": "//static.wepiao.com/",
        "release": "//static.wepiao.com/"
    },
    "globals": {  // 配置全局变量，这里配置了 static_api 和 api 两个全局变量
        // static api
        "static_api": {
            "start": "", // local api base entry
            "test": "//m.wepiao.com",
            "pre": "//m.wepiao.com", // online api base entry
            "release": "//m.wepiao.com"
        },
        "api": {
          "start": "",
          "test": "",
          "pre": "//api.wepiao.com",
          "release": "//api.wepiao.com"
      }
    },
    "vendor": ["react", "react-dom"],
    "alias": {
        "wepiao": "components",
        "scss": "scss"
    },
    "devtool": "source-map",
    "template": {
        "path": "index.template.html", //使用自定义模版，忽略下面所有设置
        "title": "",
        "keywords": "",
        "description": "",
        "viewport": "",
        "favicon": "assets/images/favicon.ico"
    },
    "pages": "pages",
    "scss": "scss",
    "components": "components",
    "assets": "assets",
    "eslint": false, // 如果启用该选项，请在项目根目录提供自己的 `.eslintrc`
    "css_modules": false,
    "transfer_assets": false,
    "base64_image_limit": 10240,
    "esmode": 'es6' // swith esmode on template creating, es5/es6, default to es6
}
```
**注意**：路径的配置建议使用 `//`开头，省去协议名字 `http:`，这主要是为了向 https 迁移，因为`//`资源，会使用和当前网页相同的协议加载资源。

key                  | 说明                                                                                                       
----                 |----------
host                 | 调试IP, 默认0.0.0.0                                                                                        
port                 | 调试端口, 默认8080                                                                                         
base                 | 业务代码根目录                                                                                             
build                | 代码打包目录                                                                                               
static               | 静态资源跟路径，便于切换到CDN                                                                              
api                  | 接口根路径                                                                                                 
vendor               | 打包类库资源                                                                                               
alias                | 路径别名, 添加后，在任意子目录引用: `import { some_thing } from 'alias_name/..'`                           
devtool              | webpack调试模式设置, 具体参见: [webpack-devtool](http://webpack.github.io/docs/configuration.html#devtool) 
pages                | 自定义 pages 路径                                                                                          
scss                 | 自定义 scss 路径                                                                                           
assets               | 自定义 assets 路径                                                                                         
components           | 自定义 components 路径                                                                                     
eslint               | 是否开启 eslint，默认关闭, 如果启用该选项，请在项目根目录提供自己的 `.eslintrc`                            
css_modules          | 是否启用 [CSS Modules](https://github.com/css-modules/css-modules), 默认关闭                               
esmode               | 模版代码风格，支持 `es5` 和 `es6` , 默认为 `es6`, 兼容 `es7`                                               
transfer_assets      | 打包时是否 copy `asset` 目录，默认 false                                                                   
base64_image_limit   | 对所有小于该大小的图片启用 base64 转码, 默认 10240 (10k)                                                   
template.title:      | 模版 `document.title`                                                                                      
template.keywords    | 模版 `meta keywords`, 默认为空                                                                             
template.description | 模版 `meta description`, 默认为空                                                                          
template.viewport    | 模版 `meta viewport`, 默认`width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no`         
template.favicon     | 模版 `favicon`, 默认为空                                                                                   
template.path:       | 设定自定义模版的的路径，会替换默认模版,可选。设置后会忽略上面五项模版设置. 有语法要求, 参考index.template.html 

### 补充说明

  -  全局变量  

 `pepper.config.js`中配置的`globals`中的配置将在代码中将以全局变量'UPPER_CASE'的形式导出 ，例如API和STATIC  

```
console.log(API) // pepper.config.js中的api[MODE]设置,注意使用环境
Reqwest({
 url: API + '/your_sub_url',
 ...
})
```
  -  程序有四种模式  

构建模式            |  说明
----                |----
start               |开发调试, 静态资源内存缓存, 启express服务，可自定义端口和host，hot reload
test                |测试模式，不启服务，不压缩 
pre                 |预发布，不启服务，压缩
release             |发布模式, 不启服务，压缩
