---
layout: post
title: Redux & Reselect
category: blog
description: Redux 的半年和 Reselect 的一周
---

来微票儿的半年里，从第一天就开始使用 [Redux](http://redux.js.org/) 了。

关于 Redux，没有比官方文档更介绍更详细的了， 当然你也可以看看[我同事怎么说](https://github.com/ingf/ingf.github.io/issues/4)。

> 环境依赖 ：`node.js` `npm`
> 
> sudo npm install we-pepper -g

##### 使用 pepper

```
➜  pepper
? 选择要执行的任务:
  项目打包
  开发调试
  初始化新项目 (es5)
❯ 初始化新项目 (es6 & react & react-router)
  初始化新项目 (es6 & react & react-router & redux)
  创建新页面
  创建新组件
  升级pepper
```
##### 初始化项目( react, react-router )

```
? 选择要执行的任务:  初始化新项目 (es6 & react & react-router)
? 请输入项目的名称: [英文] redux_and_reselect_toturial
项目 redux_and_reselect_toturial 创建成功
➜  tree redux_and_reselect_toturial -L 3
redux_and_reselect_toturial
├── README.md
├── mock.js
├── package.json
├── pepper.config.js
├── src
│   ├── assets
│   │   └── images
│   ├── components
│   │   ├── Header
│   │   └── index.js
│   ├── pages
│   │   ├── about
│   │   ├── home
│   │   └── index.js
│   ├── scss
│   │   ├── normalize.scss
│   │   └── reset.scss
│   └── utils
└── template.html

10 directories, 9 files
➜  
```

关于项目结构参考 [pepper 使用文档](http://dhong.co/pepper-doc) 和 [pepper-talks](http://dhong.co/pepper-talks)

**安装 redux 相关依赖, 启动调试 **

```
npm install redux react-redux react-router-redux redux-thunk redux-logger --save;
npm install;
pepper start;
```
**输出结果**

```
➜  redux_and_reselect_toturial pepper start
[HPM] Proxy created: /proxy  ->  http://example.proxy.com
[HPM] Proxy rewrite rule created: "^/xyz" -> "/abc"
🌎  Listening at http:// 0.0.0.0 9527
webpack: wait until bundle finished: /
Warning: you are using an outdated format of React Transform configuration. Please update your configuration to the new format. See the Releases page for migration instructions: https://github.com/gaearon/babel-plugin-react-transform/releases
webpack built 4c582c0ca8ab2300db44 in 8066ms
Hash: 4c582c0ca8ab2300db44
Version: webpack 1.12.11
Time: 8066ms
                   Asset       Size  Chunks             Chunk Names
            js/shared.js     189 kB       0  [emitted]  shared
     js/home-f5886aac.js     160 kB       1  [emitted]  home
    js/about-d32e781d.js     150 kB       2  [emitted]  about
     js/vendor.bundle.js     682 kB       3  [emitted]  vendor
        js/shared.js.map     238 kB       0  [emitted]  shared
 js/home-f5886aac.js.map     256 kB       1  [emitted]  home
js/about-d32e781d.js.map     248 kB       2  [emitted]  about
 js/vendor.bundle.js.map     814 kB       3  [emitted]  vendor
              index.html  693 bytes          [emitted]
chunk    {0} js/shared.js, js/shared.js.map (shared) 180 kB {3} [rendered]
    [0] multi shared 40 bytes {0} [built]
     + 76 hidden modules
chunk    {1} js/home-f5886aac.js, js/home-f5886aac.js.map (home) 146 kB {0} [rendered]
     + 136 hidden modules
chunk    {2} js/about-d32e781d.js, js/about-d32e781d.js.map (about) 137 kB {0} [rendered]
     + 132 hidden modules
chunk    {3} js/vendor.bundle.js, js/vendor.bundle.js.map (vendor) 641 kB [rendered]
    [0] multi vendor 40 bytes {3} [built]
     + 157 hidden modules
webpack: bundle is now VALID.
```

打开浏览器，输入 http://0.0.0.0:9527, 将看到如下效果  
![](images/2016_04/demo1.jpg)