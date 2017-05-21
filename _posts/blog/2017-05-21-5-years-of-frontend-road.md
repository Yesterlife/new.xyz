---
layout: post
title: 5 年前端路
description: 职业生涯即将跨过第 5 个年头...
category: blog
---
职业生涯即将跨过第 5 个年头，追忆往事，思绪万千。

当你做出某个选择的时候，并不会知道，它对你的今后有多大的影响，直到有一天，回过头来，重新看过，曾经的那些小选择，是多么的关键，乃至影响深远。

### Angular & Bootstrap

12 年底，初次接触后台管理项目，要选择一个前端的实现方案。此时的前端，[Backbone](http://backbonejs.org "backbone offical site"), [Konckout](http://knockoutjs.com/ "knockout offical site"), [Ember](http://emberjs.com "emberjs offical site")… 众多框架各成一派，鱼龙混杂。可最近几周的 [Github 趋势排名 ](https://github.com/trending/javascript?since=monthly "github trending")， 一度被 [Angular](https://angularjs.org "angularjs") 霸占，这又是一个怎样的后起之秀？

![angularjs offical site](https://ww4.sinaimg.cn/large/006tNc79ly1ffs1fito3pj31kw12z42b.jpg "angularjs offical site")

**入坑理由：JQuery 完败 Angular 之双向数据绑定**

双向绑定的黑魔法，成了 [Angular](https://angularjs.org "angular.js") KO 对手的杀手锏，吸引了众多的开发者的涌入。以至于到现在，看到 [Angular 与 React 混战](https://zhuanlan.zhihu.com/p/20549104?columnSlug=FrontendMagazine "angular vs react") 的那刻，自己早已转投至 [react](https://facebook.github.io/react "react") 靡下，又是思绪万千。历史的车轮不断向前，优秀的思想，总是会被不断地沉淀和净化。做为一名程序员，就要要紧跟时代的脚步，不断学习。未来的潮流，又将被谁主导？我们拭目以待！

回到 [Bootstrap](http://getbootstrap.com "bootstrap")，从 v2 开始，对其 12 列的栅栏系统，印象深刻。参考其官网丰富详细的使用文档，前端新手的我，依葫芦画瓢，最终也完成了一个个页面，一一看去，还特别地自我感觉良好（笑）。

![bootstrap offical sit](https://ww4.sinaimg.cn/large/006tNc79ly1ffs1jwthaaj31kw12z0vc.jpg "Bootstrap")

不懂 CSS，不懂 JQuery，不懂 HTML，都没关系，使用 Bootstrap ，你也能做出专业前端水准的作品，这就是 [Bootstrap](http://getbootstrap.com "Bootstrap") 的魔力。贴心的是，还对各个尺寸的终端做了适配。就是在这样的蛊惑下，我做出了职业生涯的一个重要选择，入坑前端，但心里还是不免疑惑：*做网站真的如此简单？*

后续近一年里，随着 [Angualr](https://angularjs.org "angular.js") 与 [Bootstrap](http://getbootstrap.com "bootstrap") 在项目中的不断实践，新的知识如泉水般涌入，每一日都收获颇丰，几乎空白的 JavaScript、CSS、HTML 技能图谱，日益丰盈，那种大脑每天被知识充斥的感觉，至今都甚为怀念。

后来的某一天，脱离了后台网站的开发，工作重心转移到了移动端页面，从此拉开切图仔职业生涯的帷幕。

> Bootstrap 太大了，不能用 !!!
>  Angular 太大了，不能用 !!!
> 什么？不会切页面 !!!
>  什么？不懂 CSS Model !!!
>  什么？不知道如何优化页面性能 !!!

使惯了各种库和框架的我，在性能要求苛刻的移动端面前，被迫卸下了所有的装备，一下子不知所措。扪心自问，没了框架、类库，我还会点什么？

对于知识，要保持一颗敬畏之心，随着知识的积累，会慢慢发现：

> 懂的越多，不懂得也越多。

### Grunt & Gulp & Webpack

[Angualr](https://angularjs.org "angularjs") 与 [Bootstrap](http://getbootstrap.com "Bootstrap") 的学习，让自己跨进了前端的门槛。而失去它们之后的工作，又让自己大开眼界，犹如跳出井口的井底之蛙：前端之路才刚刚开始。

JS 模块化，代码压缩、合并，雪碧图，缓存优化和自动化构建是网站性能优化的常用手段，这些方面的空白，又让我心潮澎湃，我知道这扇门之后，必将是一片更广阔的世界。

![static resources dependencies graph](https://ww2.sinaimg.cn/large/006tNc79ly1ffs1l0rq0wj318c0icwf7.jpg "JS Modules")

JS 的模块化是前端的核心基础，大型项目里尤其如此。仅仅是 JS 模块的依赖分析，就已经让开发者心力交瘁了，针对每种文件类型，还有一堆的插件等待配置，而各种配置项又暗藏玄机。这就是 [Grunt](http://www.gruntjs.net "gruntjs offical site") 时代自动化构建的现状。 究其原因，CSS、HTML 和 JS 的预处理任务之间，缺少协同合作。当时条件下， JS 里还并不能直接引入 HTML 和 CSS 依赖（在某些插件的配置下应该也能做到）。虽然遇到的大多数问题，都能找到一个对应的 `grunt-xxxx` 解决（无非再加一项插架的配置），可有没有什么办法能处理的更优雅一些呢 ？

![glup vs grunt](https://ww3.sinaimg.cn/large/006tNc79ly1ffs1lmx6xij30qd0dh74q.jpg "gulp vs grunt")

[Gulp 之于 Grunt](https://medium.com/@preslavrachev/gulp-vs-grunt-why-one-why-the-other-f5d3b398edc4 "gulp vs grunt")，在 Task 协作方面略胜一筹，文件流的理念，回到了流程处理的本质，减少了中间临时文件的开销，可，还是没能支持不同类型任务间的协作。

[Webpack](https://webpackjs.org "webpack offical site") 最早出现于 [Pete Hunt](https://twitter.com/floydophone "pete hunt") 关于[How instangram works](https://youtube.com "youtube") 的分享中，当中提到了很多关于 [SPA](https://zh.wikipedia.org/wiki/%E5%8D%95%E9%A1%B5%E9%9D%A2%E5%BA%94%E7%94%A8 "单页面应用") 的一些资源整合和加载优化的方案，在 [instangram.com](https://instangram.com "instangram") 的性能优化中收效显著。而这些方案，都能够在一个叫 [Webpack](https://webapck.js.org "webpack") 的插件中得到支持。千呼万唤始出来，此后 [Webpack](https://webapck.js.org "webpack") 脱颖而出。 

![webpack offical site](https://ww3.sinaimg.cn/large/006tNc79ly1ffs1musho3j31kw12zgo1.jpg "Webpack")

[Webpack](https://webapck.js.org "webpack") 的理念中，引入的任一种资源，都会被同等看待，只需你告诉它，遇到对应后缀名称的文件该如何处理？这也意味着，索引入口文件，就能找到项目中依赖的所有其它文件。回过头来，重新看过 [Grunt](http://www.gruntjs.net "gruntjs offical site") 和 [Gulp](http://gulpjs.com "gulp offical site") 中基于单一文件类型的处理流程，弊处在于，不能很好的分析入口文件中所依赖的其它文件类型，因而造就了依赖树结构的不完整性。[Webpack](https://webapck.js.org "webpack") 从更高的角度，将文件类型的分类也纳入囊中，自己将文件处理流程，按照文件后缀名归类，再分配给配置项里的各个插件。

写到这里，突然领悟到其中蕴含的众多软件设计理念，单一职责、命令模式、抽象工厂等等，被巧妙地组合利用后，方显现软件工程学的真谛。

如果说是[Pete Hunt](https://twitter.com/floydophone "pete hunt") 的分享推动了[Webpack](https://webapck.js.org "webpack") 的流行，就[React](https://facebook.github.io/react "react") 的火热之势而言，称他为 「[React](https://facebook.github.io/react "react") 之父」也并不为过。

### React

![react offical site](https://ww2.sinaimg.cn/large/006tNbRwly1ffsnscyratj31j016wgpu.jpg "react offical site")

2013 年 5 月，[Facebook](https://facebook.com "facebook offical site") 开源了 [React](https://facebook.github.io/react "react")，它最初诞生自 Ads 团队，后被应用于 [Instangram.com](https://instangram.com "instangram offical site") 的优化中而名声鹊起，迅速在公司内部传播开来。声明式语法，松耦合、可预知、易测试的组件化方案，专注于 UI 渲染，是 [React](https://facebook.github.io/react "react") 与众不同的三大特性，但开源初期并不被业内人士看好。于是，[Pete Hunt](https://twitter.com/floydophone "pete hunt") 开始辗转于各大 JSConf，怀揣着 [React](https://facebook.github.io/react "react")，引领大家 [Rethinking Best Practices](https://www.youtube.com/results?sp=CAA%253D&q=rethinking+best+practices "rethinking best practices")。自此之后，如当年 iPhone 席卷传统手机行业那样，[React](https://facebook.github.io/react "react") 在前端一路高歌，就有了不久前的那场[与 Angular 的遭遇战](https://zhuanlan.zhihu.com/p/20549104?columnSlug=FrontendMagazine "react vs angular")。

![react vs angular](https://ww2.sinaimg.cn/large/006tNc79ly1ffs1i66i6fj30m80k1acm.jpg "angular vs react")

[ Reconciliation （virtual DOM）](https://facebook.github.io/react/docs/reconciliation.html "Reconciliation")（v16 之后的版本中，被 [Fiber](https://github.com/acdlite/react-fiber-architecture "react fiber architecture") 取代）是 [React](https://facebook.github.io/react "react") 的核心思想，在优化性能的同时，也提供了它迁移到其它平台的能力。

| virtual DOM 实现                           | 平台            |
| ---------------------------------------- | ------------- |
| [react-dom](http://facebook.github.io/react) | HTML          |
| [react-native](http://facebook.github.io/react-native) | Android & iOS |
| [react-vr](http://facebook.github.io/react-vr) | VR            |
| ...                                      | ...           |
| react-xxx                                | xxx           |

> Learn Once, Write Everywhere

在这一思想的引领下，我们只需关注组件本身，在组件的生命周期中，处理相关的一切，而不用关心它究竟剑指何方。[React](https://facebook.github.io/react "react") 是接口，react-xxx 就是接口的具体实现。

注：还记得 react-dom 什么时候从 react 中拆分出去的吗？如今，prop-types 也被拆分开来，对此，你又有什么想法？

尽管 [React](https://facebook.github.io/react "react") 如此优秀，但并不完美，在处理复杂数据流时，略显吃力。[Flux](http://facebook.github.io/flux/ "flex offical site") 的出现，[React](https://facebook.github.io/react "react") 如虎添翼。就这样，我拜倒在 [React](https://facebook.github.io/react "react") 的石榴裙下，之后的几年里，见证了它的成长和成名，自己也因此受益良多。

好的思想，总会催生出更多的同类，活跃的 [React](https://facebook.github.io/react "react") 社区里优秀的框架和插件层出不穷，最出名的非 [Redux](http://redux.js.org "reduxjs offical site") 莫属。

### Redux

![redux concept](https://ww1.sinaimg.cn/large/006tNbRwly1ffsyo3ew3lj315p0x975h.jpg "redux")

与 [Flux](http://facebook.github.io/flux/ "flex offical site") 同样思想的 [Redux](http://redux.js.org "reduxjs offical site") 可谓是，青出于蓝而胜于蓝。更好的易用性，便捷的扩展性，让其在社区内备受瞩目。 基于其优雅的实现方式，一大堆的扩展插件也涌现出来。按照时间顺序，以下是我们团队使用的扩展项。

| redux 系列                                 | 推荐指数      | 说明                                  |
| ---------------------------------------- | --------- | ----------------------------------- |
| [redux-logger](https://github.com/theaqua/redux-logger) | ★ ★ ★ ★ ★ | action 日志                           |
| [redux-actions](https://github.com/acdlite/redux-actions) | ★ ★ ★ ★   | 更便捷的 action 处理                      |
| [redux-thunk](https://github.com/gaearon/redux-thunk) | ★ ★ ★ ★   | 异步 action 实现 ( 基于 Promise )         |
| [reselect](https://github.com/reactjs/reselect) | ★ ★ ★ ★ ★ | mapStateToProps 优化                  |
| [redux-saga](https://github.com/yelouafi/redux-saga) | ★ ★ ★ ★   | 异步 action 实现 ( 基于 Generator )，可控性更强 |

每个扩展项都是为了解决某些特定的问题而设立。[redux-saga](https://github.com/yelouafi/redux-saga) 和 [redux-thunk](https://github.com/gaearon/redux-thunk) 都致力于解决异步 action 的问题，两者虽然疏通同归，但前者拥有更细粒度的把控，将一个 action 的执行流程，拆分成若干个过程，而这些过程之间，又可以任意组合与拼接，而这正是处理复杂业务流程所必需的，也正是 [redux-thunk](https://github.com/gaearon/redux-thunk) 短板所在。

插件的使用，是为了解决问题，具体解决了什么问题，又是如何解决的，作为插件使用者的我们，还应该更进一步，深入源码一探究竟。时不时的问问自己，[没了框架、没了插件，还知道些什么？](http://dhong.co/fighting-front-end-fatigue)

### 其它

写到这里，依然漏掉了如多， [Babel](http://babeljs.io) 、ES[6~7]、[Yarn](http://yarnpkg.com)、[Prettier](https://prettier.github.io/prettier) 等等，好的东西，大家都会在用，也无需我在此多言。好比我每天会翻一翻 [Github 趋势排名 ](https://github.com/trending/javascript?since=monthly "github trending")，相信你也有自己获取最前沿技术的途径，不管怎样，努力追赶就是。关于知识的学习和获取，强烈推荐 [Awesome 系列](https://github.com/search?o=desc&q=awesome-%2A&s=stars&type=Repositories&utf8=%E2%9C%93) 。

---

16 年末至今，开始致力于 [react-native](http://facebook.github.io/react-native) 的学习，在 Web 到 Native 的转移过程中，真实体会到了「 Learn Once，Write Everywhere 」的意义。

| web  | native |
| ---- | ------ |
| span | Text   |
| div  | View   |
| img  | Image  |

Native 之于 Web ，没有所谓的谁优谁劣，更多的是从体验和其它方面的折衷选择。如今，微信小程序、[Expo](https://docs.expo.io/) 将 Web 一链既达的体验也引入到 Native 中，成为了高级版的浏览器。Android 开发，要用 Java；iOS 开发需要用 Swift 或者 Object-C；但跨平台的 Web 开发只需要 Html & JS & CSS；这些大家都很清楚，可很少人会问为什么？现在，终于，可以使用同一种语言（ JS）开发 IOS 和 Android 了（同时具备了一链即达的特性），绕了好大一圈，好似回到了 Web 时代。试问，为什么没有从浏览器着手升级体验呢？

回首走过的 5 个年头，这是一个好的时代，见证了诸多框架的兴衰成败。

而今，即将迈入第 6 个年头，这又是一个坏的时代，AI、VR 的时代还有多远？[攻壳机动队](http://baike.baidu.com/link?url=d6BcbdW2cFY_tpqnhnDgB8vaMnjPLSQI17NSaaWCCL5DOxndW3I7W2_zBb8DbN7GaUS_Y7CMO_NDcYM1-ma6j96P38p0QZJyYMA5HzifLLMDjYTnIl1fckqi2uYXYuMU) 何时成为现实呢？

我要学 VR 了，你呢？
