---
layout: post
title: webpack code splitting
category: blog
description: async router and coding splitting
---
![](/images/2015_11/cold.jpg)

之前博客有提到过`React`和 `Webpack`，这哥俩是真哥俩

废话少说，今天咱们聊聊 [Webpack Code Splitting](https://webpack.github.io/docs/code-splitting.html)

如果你对`Webpack`和`React`还不熟悉的话，赶紧去看～

熟悉上述的人都知道，对于React之类的SPA应用来说，如果只是简单的将资源进行打包的话，你会得到一个230k（压缩后）左右的文件，即使只是简单的写了个DEMO而已。这样，肯定是不行的，那如何进行拆分呢？

这个问题暂且放一放哈

对于前端，带宽是个网站性能的瓶颈，在有限带宽的情况下，怎么优化网页的性能，说白了，就是页面加载的速度。单从资源大小上来讲，是不是越小越快呢？那，在资源大小限定的情况下，又改如何缩短资源加载时间呢？

对于基于`React`的SPA应用来说，资源大体可分为两种： 
 
*  *基础类库*  
   `react`、`react-dom`、`react-router-component`、`redux`等等。这些是所有页面公用的基础资源，它们不会经常变动，可以做永久缓存
*  *公用组件*  
   `iscroll`、`Slider`、`Pagination`、`Modal`、`Toast`等等。这些是可选资源，供不同页面使用
*  *业务相关*  
   这些和公司业务绑定的比较紧密，没法做到公用，页面间差异较大。
   
对于上述情况，优化方案如下：  

-  基础类库单独打包（vendors）
-  业务那页面拆分，单独打包
-  页面间公用资源按条件抽离（比如，共用达3处以上）

正好，Webpack有块专门讲述了如何进行Code Splitting，那我们就开始吧  

+  打包基础类库
  
    ```
    entry: {
        'app': './index.jsx',
        'vendor': ['react', 'react-dom', 'react-router-component']
    },
    plugins:[
        new webpack.optimize.CommonsChunkPlugin("vendor", "vendors.bundle.js"),
    ]
    
    ```  
   这样之后，运行webpack后，就会生成两个js文件(app.js 和 vendors.bundle.js)。app.js包涵你自己的所有代码，而vendors.bundle.js只包含你指定的那三个包。  
   
+  业务那页面拆分，单独打包 
 
    对于SPA而言，各页面按需加载是尤其重要的，这是性能优化的关键。  
    webpack允许我们自定义异步加载的bundle，像这样   

    ```
    require.ensure([/* some module to require async */], () => {
        let module1 = require('module1');
        let module2 = require('module2');
    
        // now using async loaded modules as you wish
    });
    ```
    
    如果使用最新版的 [react-router](https://github.com/rackt/react-router) 的话，要将页面按照路由拆分成按需加载的就简单多了，按照官方文档一步步来就好了哈。这次我是用的是 [react-router-component](https://github.com/STRML/react-router-component)，`react-router`每次升级变动忒大，😂😂。那又怎么改造react-router-component为异步Router呢？

    造轮子的事，还是少做为好。于是，我就找啊找，找啊找，终于找到了好朋友 😂😂  
    
    [看这里，看这里](https://github.com/QianmiOpen/react-async-router)，这里虽然用的是`react-router`，但把这个方案移步至`react-router-component`也是可以的。  
    
    ```   
    import React from 'react'
    import ReactDom from 'react-dom'

    import {  NotFound, Locations, Location } from 'react-router-component'
    import AsyncLoader from './async-loader'

    // bundle-loader, use require.ensure([], (require) -> {
    //      require('some module you want to load async');
    // })
    import Home from 'bundle?lazy!./home'
    import Pre from 'bundle?lazy!./pre'
    import About from 'bundle?lazy!./about'
    import Details from 'bundle?lazy!./details'
    import Cities from 'bundle?lazy!./cities'
    import NotFoundPage from 'bundle?lazy!./notfound'

    class App extends React.Component {
    
        render() {
            return <Locations hash>
                <Location path="/" handler={AsyncLoader(Home)}/>
                <Location path="/pre" handler={AsyncLoader(Pre)}/>
                <Location path="/about" handler={AsyncLoader(About)}/>
                <Location path="/details/:id" handler={AsyncLoader(Details)}/>
                <Location path="/cities" handler={AsyncLoader(Cities)}/>
                <NotFound handler={AsyncLoader(NotFoundPage)}/>
            </Locations>
        }
    }

    ReactDom.render(<App/>, document.getElementById('app'))

    ```  
    
    这里的 [bundle-loader](https://github.com/webpack/bundle-loader) 用的就是上面所说的`require.ensure`，但对于UI组件，还需要稍微做下设置，没错，就是那个 `AsyncLoader`  
    
    ```
    import React from 'react'

    export default function asyncLoader(comp_loader) {
        return React.createClass({
            getInitialState() {
                return {
                    comp: null
                }
            },

            componentDidMount() {
                comp_loader((Component) => {
                    this.setState({
                        comp: Component
                    });
                });
            },

            render() {
                var Component = this.state.comp;
                if (Component) {
                    return <Component {... this.props}/>
                } else {
                    return <dic>loading ...</div>;
                }
            }
        });
    }

    ```
    这样设置后，运行webpack的话，就会发现，这里每个页面都会被单独打包成一个bundle了。
    
    那，如果不想写这个 `AsyncLoader` ，还有更便捷的：   
    
    ```
    import Home from './home'
    import Pre from 'react-proxy!./pre'
    import About from 'react-proxy!./about'
    import Details from 'react-proxy!./details'
    import Cities from 'react-proxy!./cities'
    import NotFoundPage from 'react-proxy!./notfound'

    import 'scss/shared/animations'

    class App extends React.Component {
    
        render() {
            return <Locations hash>
                <Location path="/" handler={Home}/>
                <Location path="/pre" handler={Pre}/>
                <Location path="/about" handler={About}/>
                <Location path="/details/:id" handler={Details}/>
                <Location path="/cities" handler={Cities}/>
                <NotFound handler={NotFoundPage}/>
            </Locations>
        }
    }
    ```   
    
    是不是更方便了，我也是后来才找到它的 [react-proxy-loader](https://github.com/webpack/react-proxy-loader) 。那，到这里，第二个任务就完成了哈
    
+   页面间公用资源按条件抽离 
  
    webpack真的很强大，有没有啊有没有。这里还要用到 [CommonsChunkPlugin](https://webpack.github.io/docs/code-splitting.html#commons-chunk) 这里的设置还是挺复杂的哈，我也是这么一个个试出来的呢   
    
    ```
    new webpack.optimize.CommonsChunkPlugin("vendor", "js/vendor.bundle.js"),
    new webpack.optimize.CommonsChunkPlugin({
        filename: 'js/commons.bundle.js',
        children: true, //extract from all chunks
        minSize: 30 * 1000, //generate a common at least 30K, or nothing
        minChunks: 3 //extract a common modules shared within 3 or more pages
    }),
    ```
    `children`指代从所有异步bundle里提取共用资源。  
    `minSize`指代只有提取的共用资源大于30k，再生成filename指定的文件  
    `minChunks`指代只有该资源在 >=3 个页面间被引用的话，才会被打包到commons文件中。
    
    不提取各页面的公用组件时：
    ![](/images/2015_11/before-common.jpg)  
    提取各页面的共用组件后：
    ![](/images/2015_11/after-common.jpg) 
    

[DEMO SOURCE](https://github.com/Duan112358/pepper)  

哦，还有一篇在路上: [关于 CSS Modules](https://github.com/css-modules/css-modules)

没睡的晚安！  
  
帅醒的早～   

    post@Sat Nov 14 03:18:46 CST 2015
