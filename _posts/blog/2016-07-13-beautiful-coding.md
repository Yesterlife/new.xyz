---
layout: post
title: 演出票之 React 重构
description: 我们是怎样用 React 对演出票进行重构的
category: blog
---

一直以来就有种执念：软件的构建，如同建筑的建造，都是一种美学。

众多IT行业里，前端是个奇葩，得益于历史原因，造就了漫天花海的浏览器端的适配与兼容。
另一点，对于一个FER，内心其实也是崩溃的，模块加载这条血泪之路，[HUX的这篇可谓经典](http://huangxuan.me/2015/07/09/js-module-7day/)。同样的，[CSS也不堪重负](http://huangxuan.me/2015/12/28/css-sucks-2015/)，友谊的小船说翻就翻 😂😂😂

冬天过去了，春天还会远吗？

React 的发布，带来了前端的春天，一经推出，便引领了模块化、组件化的浪潮，前端终于走上正轨。

两个月前，我们对[微信演出票](http://wechat.show.wepiao.com)的微信端，用 React 进行了重构。

构造 App 的过程，与构造一座建筑并无区别。

### Pepper
pepper 是团队开发的一款用于基础构建的工具，我们用它来生成，构建，和打包项目文件

```
➜  pepper
? 选择要执行的任务:  (Use arrow keys)
❯ 项目打包
  代码格式校验
  开发调试
  初始化新项目 (es5)
  初始化新项目 (es6 & react & react-router)
  初始化新项目 (es6 & react & react-router & redux)
  创建新页面
  创建新组件
  升级pepper
```
如上所示，它集成了诸多功能，而它的使用也很简单。

```
➜  pepper init wechat
➜  tree wechat -L 1
wechat
├── README.md
├── mock.js
├── node_modules
├── npm-shrinkwrap.json
├── package.json
├── pepper.config.js ＃pepper 配置文件
├── proxy.html
├── src
├── template.html
└── test.html

2 directories, 8 files
```
该配置文件，及其上面的项目都可以通过 pepper 的新建项目命令（pepper init），快速生成。配置预览如下

```
// pepper.config.js
module.exports = {
    // debug host
    "host": "0.0.0.0",
    // debug port
    "port": "9527",
    // pepper src entry, also inner webpack entry, default to `src/pages/index.js`
    "base": "src",
    // target build dir
    "build": "dist",
    "static": {
        "start": "/",
        "test": "/",
        "pre": "/",
        "release": "//your_cdn_domain" // here use CDN domain
    },
    "api": {
        "start": "",
        "test": "",
        "pre": "",
        "release": "//your_api_enterpoint",
    },
    // third patry libs to bundle
    "vendor": ["react", "react-dom", 'react-router'],
    // dir alias, could use globally, despite of CWD
    "alias": {
        "scss": "scss",
        "wepiao": "components",
    },
    // source map options
    "devtool": "source-map",
    // switch for CSS Modules
    "css_modules": false,
    // switch for eslint
    "eslint": false,
    // limit image size for use base64, (smaller use base64, larger use url)
    "base64_image_limit": 10240 // 10k
}
```
另外通过配置 `package.json` 中的 `scripts`命令 ，可以很方便的在项目任何目录执行调试、打包等命令

```
  "scripts": {
    "pepper": "pepper",
    "cptest": "cp test.html proxy.html dist",
    "start": "pepper start",
    "test": "pepper -V && pepper test ",
    "pre": "pepper -V && pepper pre",
    "release": "pepper -V && pepper release"
  },
```
以上，pepper 为我们搭好了的项目的根基。

### React

接下来的一层层，交由 react 构建。
[react-router](https://github.com/reactjs/react-router) 是 React 生态的前端路由实现，这里把每条 Router 指向的页面，比做建筑的每一层。

```
const routes = (history) => (
    <Router history={history}>
        <Route path="/" component={Home} />
        ...
        <Route path="/about" component={About} />
        <Route path="*" component={Home} />
    </Router>
)
```
每一层建筑，按照大小规格的不同，划分成若干个不同的房间，每个房间按照功能布置成厨房、卧室、卫生间等等。同样的每个页面，按照 UI，也可以分成 Header，Content，Footer等。下面是首页的布局

```
render() {
    const { concerts, categories } = this.props

    return (
        <div className='Home flex flex-columns'>
            <div className="Home__Header">
                <Header/>
            </div>
            <div className="Home__Content flex-auto">
                <Advertising />
                <CategoryList categories={categories} />
                <ConcertList concerts={concerts} />
            </div>
            <div className="Home__Footer">
                <Footer/>
            </div>
        </div>
    )
}
```
效果如下

![images](/images/2016_07/preview.png)

页面被拆分成若干个组件，类似的，每个组件又可以拆分成若干个子组件，按照分治策略的思想，最终回归到 vision-ui 与 DOM 元素的组合。

> `vision-ui`：得益于 UI 设计的统一，前端按照设计规范，将公共组件进行了抽离，就诞生了 vision-ui 。它包含了很多常用的组件，如Button，Label，LazyImage，Loading，Message，Modal，Stepper，Text，Tabs等，也有一些预置的样式定义，便于后续的复写和继承。
以 Footer 实现为例：

```
import { Tabs, Tab, Icon } from 'vision-ui'
...

render() {
    return (
        <Tabs type="icon" className="Footer" onChange={this.onTabChange.bind(this)}>
        {
            TABS.map((tab, index) =>
                <Tab key={index}>
                    <Icon type={tab.icon}/>
                    { tab.text }
                </Tab>
            )
        }
        </Tabs>
    )
}
```

按照这样的方式，就诞生了很多的组件。我们按照复用的程度，进行了组织和整理：

```
➜  src git:(dev) ✗ tree components -L 1
components
├── CityPicker
├── ConcertList
├── FilterPicker
├── Footer
├── Header
├── Navbar
├── ImageSlider
├── Service
└── index.js

8 directories, 1 file
```
`components` 目录用于存放页面之间共享的组件，如果没有被复用，就放置到当前页面好了。

```
➜  src git:(dev) ✗ tree pages/home -L 1
pages/home
├── Advertising.js
├── CategoryList.js
├── Splash.js
├── home.scss
└── index.js

0 directories, 5 files
```
React 中的 JSX 写法，将模版语言和 JS 进行了融合，这点在构造组件上很是方便。

#### redux
[redux](https://github.com/reactjs/redux) 是小而美的 [Flux](http://facebook.github.io/flux/) 开源社区实现。将业务代码按照官方教程进行组织，提高可读性，降低复杂度，便于维护和管理。

```
➜  src git:(dev) ✗ tree app -L 1
app
├── actions
├── constants
├── reducers
├── selectors
└── store

5 directories, 0 files
```
#### reselect
[reslect](https://github.com/reactjs/reselect) 作用于 redux 的 mapStateToProps 过程中，对 reducer 中 state 的过滤和缓存。
![reselect](/images/2016_07/reselect.png)

```
// selectors/schedule.js

import { createSelector } from 'reselect'

// use detail page's title
const getTitle = state => state.detail.title
// use home page's city
const getCity = state => state.home.city

const getSchedules = state.schedule.list

export default createSelector(
    getTitle,
    getCity,
    getSchedules,
    (title, city, schedules) => {
        return {
            title,
            city,
            schedules,
        }
    }
)
```
上面的示例中，将 detail 和 home 页面的数据进行了组合，拼接后一同返回到 schedule 页面。
在 selector 的使用上，利用了 es6 的 `decorator` 语法，将 [react-redux](https://github.com/reactjs/react-redux) 中的 `connect` 抽离成 @connect 语法糖

```
import { connect } from 'react-redux'
import { bindActionCreators } from 'redux'
import * as actions from 'app/actions'

function mapStateToProps(props, state) {
    if (!props) return state
    if (typeof props === 'function’) return props
    if (typeof props === 'string') return { [props]: state[props] }
    if (Array.isArray(props)) {
        return props.reduce((prev, curr) => prev[curr] = state[curr], {})
    }
    return state
}

function mapDispatchToProps(dispatch) {
    return {
        actions: bindActionCreators(actions, dispatch)
    }
}

export default params => {
   return target => connect(mapStateToProps.bind(null, params), mapDispatchToProps)(target)
}
```
接下来，就可以这么使用了，甚为方便

```
import connect from 'utils/connect'
import SchduleSelector from 'app/selectors/schedule'

@connect(ScheduleSelector)
export default class Schedule extends React.Component {
    ...
}
```
以上是组件内容的构建，接下来处理样式的问题

### Flex & PostCss & BEM

flex 是前端的布局利器，结合 [PostCSS](http://postcss.org/)，有效解决样式的兼容问题 

```
.flex {
    display: flex;
}
```
经由 postcss 处理后，可自动补全浏览器前缀，以及兼容性的样式（注：需合理配置 browsers 属性） 

```
.flex {
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex
}
```
对于 [BEM](https://en.bem.info/methodology/key-concepts/)，是一个良好编码习惯的开始。这些特性在提高 CSS 的性能的同时，在输出格式上也利于阅读和理解。
![bem](/images/2016_07/bem.jpg)
充分利用 SCSS 中的变量定义和 Mixins 的特性，可以很快捷的切换不同皮肤。这点，在 vision-ui 的主题定制上得到了良好体现。
![theme](/images/2016_07/theme.jpg)

### 性能优化
性能优化的环节，我们主要在两部分上进行了处理：

#### 资源加载
常规环节的压缩合并，资源按需加载，这些都是在 pepper 里做的处理. 

```
➜  wechat git:(dev) pepper release
Hash: 55515d1782ce3c2a646d
Version: webpack 1.13.1
Time: 29808ms
                     Asset           Size  Chunks             Chunk Names
       js/seat-402ce71e.js        62.9 kB   8, 14  [emitted]  seat
    js/commons-e42816d9.js         242 kB   0, 14  [emitted]  shared
     js/search-331c31c5.js        15.8 kB   2, 14  [emitted]  search
  js/orderlist-5e357d66.js          30 kB   3, 14  [emitted]  orderlist
js/orderdetail-92212bf3.js        28.8 kB   4, 14  [emitted]  orderdetail
   js/category-7b4f8c07.js        12.4 kB   5, 14  [emitted]  category
     js/detail-0d089ae7.js        33.4 kB   6, 14  [emitted]  detail
   js/seatarea-e61b16e2.js        54.1 kB   7, 14  [emitted]  seatarea
       js/home-806f6d3b.js         107 kB   1, 14  [emitted]  home
   js/schedule-94bff107.js        16.1 kB   9, 14  [emitted]  schedule
    js/payment-ee2ebec7.js        49.7 kB  10, 14  [emitted]  payment
    js/address-da1deb3d.js        12.4 kB  11, 14  [emitted]  address
     js/result-3b611bce.js        13.8 kB  12, 14  [emitted]  result
     js/vendor-10a316b2.js         215 kB  13, 14  [emitted]  vendor
               favicon.ico  NaN undefined          [emitted]
                index.html        3.05 kB          [emitted]
   [0] multi vendor 124 bytes {13} [built]
    + 503 hidden modules
```
按照 Router 的配置，可以按照页面将 JS 拆分（webpack async thunk），只有在访问对应的路径，才会去加载对应的页面资源。另一点，pepepr 将接口和静态资源切换到不同的域名下，提高浏览器并发数量，这个也可以在 pepper 里配置。

#### 缓存优化  
API请求缓存按需配置。
```
// params = { item_id }
export function fetchConcertDetail(params, showLoading = true) {
    let url = `${API}/shows`

    return (dispatch, getState) => {
      return new GET(url, { params })
        .cache(false)
        .loading(showLoading)
        .send()
        .then(resp => {
            resp && dispatch(receiveConcertDetail(resp))
        })
        .catch(error => {
        Message.show(error.message)
            dispatch(receiveConcertDetail([], params))
        })
    }
}
```
关于 CDN 上资源的缓存，这个可以按需配置，这里不再赘述。
