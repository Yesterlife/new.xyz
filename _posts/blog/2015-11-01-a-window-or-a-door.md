---
layout: post
title: 上帝为你开了一扇窗
category: blog
description: 干货推荐，程序员们看过来
---

![ghost](/images/2015_11/ghost.jpg)

今天说点干货。

如果你不是个程序员，直接翻到底部就可以了，谢谢～

脱离jQuery的阵营，已经很久了，那得从Angular.js说起。Angular和jQuery的主要区别在于DOM的操作上，但这正是这点，开启了前段框架的序幕。什么双向绑定，动态更新，组件化，依赖注入，很多的设计模式也被引入前端领域，前端也越来越像个前端了。

与Angular一起流行起来的还有UI框架，[Bootstrap](http://getbootstrap.com)，[Semantic-UI](http://www.semantic-ui.com),[UIKit](http://getuikit.com)...  现在说起BS，估计没几个人不知道，它给WEB开发带来的便利，大家有目共赌。

Android 5.0的发布，更新为Web UI注入了一股新的血液，[Matreial Design](https://www.google.com/design/spec/material-design/introduction.html)[需翻墙]。

其实今天的主角并不是它们几个，呵呵呵。。。

我是的[React](facebook.github.io/react)和[webpack](https://webpack.github.io/)的死忠粉，😊，所以下面的你们懂的

[React](facebook.github.io/react)的出现可谓是抢尽了风头，牛叉的diff算法，单项数据流，Flux...

[webpack](https://webpack.github.io/)又是什么呢？[grunt](http://gruntjs.com)和[gulp](http://gulpjs.com)大家不陌生吧，我开始也是grunt起步的，接触了gulp之后，grunt就被抛弃了。同样的，接触了webpack之后，gulp就开始坐上了冷板凳。三者殊途同归，但思想上有很大不同，具体的大家去看文档吧。

下面主要说下React和webpack的结合和使用：

React给前端带来了什么？  

* 组件化  
    组件化的思想很早就有，也吵了很久，Angular和[Polymer](https://www.polymer-project.org/1.0/)里都有相关体现，个人觉得还是Polymer的比较彻底，没有相关底层类库层的依赖，但这些为什么没有热起来，却被React抢了风头，这其中原理，我也答不上一二，感兴趣的可以去知乎搜罗搜罗。组件化的目的，说白了就是提高复用性，就像搞原生应用开发那样，自定义控件。但是，由于浏览器端的局限，只接受HTML和JS，CSS，其他高级语法它根本不鸟你，所以，web端的自定义控件要在浏览器那一层之上。为此，React推出了自己的Virtual Dom，它负责自定义控件到浏览器端的转换，而且还有一套高性能的差异化对比算法，来高效地更新。
    
    上面说的其实Angular也里都有，但Angular使用起来可就没那么简单了。深入下去的话，会遇到各种Service，Filter，Controller，Directive，Provider等等，光这些区别就让我们很头疼了，更不用说自定义组件里还有很多Scope的坑等着你呢？另外一点，Angular的自动绑定很不错，就是性能不是太好，当然，Angular还是不错的，哈哈。现在的[Angular2](http://www.angular2.com/)，做出了很多的改进，性能也提升不少，还是值得一试的
    
    回到React上来。在React身上，衍生出了很的优秀的框架，[Flux](https://facebook.github.io/flux/)[set state set 到手软有没有，还好有他]，当然，它也有很多问题，但它的思想很好，所以，更多优化后的版本出现了，现在我用它[redux](https://github.com/rackt/redux)，看这里[awesome redux](https://github.com/xgrommx/awesome-redux)
    
    更多资源，参见[awesome react](https://github.com/enaqx/awesome-react)
    
* 编码规范
    如果用React的话，JSX结合ES6，写起来还是很爽的，兼容和编译的问题，交给webpack就好了。如果非要写CSS的话，SCSS，LESS，拿来就是，但我更推崇[react-style](https://github.com/js-next/react-style)，这俩个也不错[radium](https://github.com/FormidableLabs/radium)，[reactcss](http://reactcss.com/)，它们的好处嘛，来再看看[Pete Hunt大神怎么说](https://www.youtube.com/watch?v=VkTCL6Nqm6Y&feature=youtu.be)[对不住兄弟，这个也要翻墙]
    
    -  [ES6-style](https://github.com/gf-rd/es6-coding-style)
    -  [JSX-style](https://github.com/petehunt/jsxstyle)

webpackyou带来了什么？  

* 包管理
    依赖管理一直是前端的痛，痛就要治，所以我们有了[Seajs](http://seajs.org/)，[Requirejs](http://requirejs.org)，[browserify](http://browserify.org)，至于什么[AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)，[CMD](https://github.com/seajs/seajs/issues/242)，[CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1.1)，可以先看看，我是比较喜欢commonjs，熟悉node的话都应该懂吧。webpack支持AMD和requirejs，国外的东西，就不要指望支持seajs了，😢。
* 预处理
    预处理的工作是由一堆loader实现的，这点和grunt或者gulp的插件差不多，不同的是，时机和顺序。webpack要求必须又一个入口文件，然后按照入口文件里的引用去分析各个引入的文件，然后按照文件名后缀分配任务给各个loader。更高一层上，webpack也有插件，这些插件的生命周期贯穿整个webpack的运行时，所以才有了下面的优化。
* 优化  
    对于优化，前端工程化例听到最多的是图片合并压缩，CSS，html压缩，JS合并合并等等，和Grunt,Gulp一样，webpack也能做，但webpack做的更好。依赖关系上的优化，是webpack亮点，这里也是压缩合并之外的难点所在，怎样更好的去除重复，以及合理合并公用资源，什么情况下要拆分，等等
    
    更多参见[code splitting](http://webpack.github.io/docs/code-splitting.html)
    
-------
资源汇总：
  
* [Bootstrap](http://getbootstrap.com)  
* [Semantic-UI](http://www.semantic-ui.com),[UIKit](http://getuikit.com) 
* [Matreial Design](https://www.google.com/design/spec/material-design/introduction.html)
* [Polymer](https://www.polymer-project.org/)
* [Angular2](http://www.angular2.com/) 
* [React](facebook.github.io/react)
* [Flux](https://facebook.github.io/flux/)
* [redux](https://github.com/rackt/redux)
* [awesome redux](https://github.com/xgrommx/awesome-redux)
* [awesome react](https://github.com/enaqx/awesome-react)
* [react-style](https://github.com/js-next/react-style)
* [reactcss](http://reactcss.com/)
* [radium](https://github.com/FormidableLabs/radium)
* [ES6-style](https://github.com/gf-rd/es6-coding-style)
* [JSX-style](https://github.com/petehunt/jsxstyle)
* [Seajs](http://seajs.org/)
* [Requirejs](http://requirejs.org)
* [CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1.1)
* [browserify](http://browserify.org)
* [AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)
* [CMD](https://github.com/seajs/seajs/issues/242)
* [webpack](https://webpack.github.io/)
* [code splitting](http://webpack.github.io/docs/code-splitting.html)

视频资源

* [Pete Hunt: how instagram works](https://www.youtube.com/watch?v=VkTCL6Nqm6Y&feature=youtu.be)

-------


***好多好多的东西需要学习和了解， 有没有啊有没有，😖😖😖***

当上帝为你开启一扇窗，其实是为你开启了一扇门。

嗯，就是这样，当你学习一门知识的时候，它中间会穿插出好多其他的资源和引用。就像读一本书，收获的不止是它的内容，还有它相关的其它书目，以及它们中暗含的那些
    
说了这么多，可能有人会问？

当一系列的新概念忽然而来的时候，作为一个程序员，我们要做的是什么呢？

有些人，比如像我，就喜欢尝试新的东西，不断踩坑，踩坑，再踩坑，比较享受知识吸收和储备的过程。

当然，有人会说，这中间会出现很多的新框架，新思想，每个都去尝试的话，哪会有这么多时间，再说了，这些新东西都还不成熟，还无法应用到实际项目中去，私下尝尝鲜就可以了。

我只想说，重要的是过程，而非结果，收获了就是值得的，学习本身就是知识或者技能储备的过程。厚积薄发，是金子总会发光的
