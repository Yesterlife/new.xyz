---
layout: post
title: RN 的踩坑之路
description: 没错，确实是踩坑之路
category: blog
---
![github](/images/2016_08/octocat.jpg)

最近的两周，启动了尘封许久的 React-Native 之旅。这之前，混迹于 React 阵营一年有余，时刻关注 React-Native 的动向，无论怎样

> Learn Once, Write Anywhere

的哲学，深深触动着我，去一探究竟。

---
开始之前，先看个 Demo（ UI 参考 [微信演出票](http://wechat.show.wepiao.com)）
![demo](/images/2016_08/demo.gif)
在 Demo 的实现过程中，一直想遵循 web 开发中的体验，很自然的将问题分成两部分

### 业务逻辑

得益于 React 的理念（专注于 UI 渲染），业务逻辑部分很大程度上取自于 Web 端
![directory diversion](/images/2016_08/directory.jpg)
app 目录是采用 [redux](http://redux.js.org) 和 [reselect](https://github.com/reactjs/reselect) 的业务逻辑实现，这块和 UI 无关，些许改动，就可以整个迁移过来。

> 提示:  [这里是 redux 和 reselect 的 demo 示例](http://dhong.co/redux-and-reselect)

```
➜  src git:(master) ✗ tree app -L 1
app
├── actions
├── constants
├── reducers
├── selectors
└── store
```

### UI

在[演出票重构之 UI 篇](http://dhong.co/beautiful-coding) 中，非常喜欢 [react-router](https://github.com/reactjs/react-router) 的使用方式，那 RN 中呢？

抱着这个问题，我并没有在 RN 的官方文档中找到答案，[Routing and Navigation in React Native](http://blog.paracode.com/2016/01/05/routing-and-navigation-in-react-native/) 里说的很明白了，果断放弃了官方的 Navigator（造轮子的事就交给你们了）。在 [Awesome React-Navite](https://github.com/jondot/awesome-react-native) 挑了几个高 star 的组件，[react-native-router-flux](https://github.com/aksonov/react-native-router-flux) 很快映入眼帘

> React Native Router based on new React Native Navigation API

没错，这就是我想要的，路由的配置和 React-Router 很是类似，就是把 Router 换成了 Scene。最最贴心的，竟然支持 redux, 看到这里，我都要感动哭了 😂😂😂

```
const RouterWithRedux = connect()(Router)
const routes = (
    <RouterWithRedux createReducer={reducerCreate} getSceneStyle={getSceneStyle}>
        <Scene key='modal' component={Modal}>
            <Scene key='root' hideTabBar>
                <Scene key='splash' component={Splash} hideNavBar hideTabBar initial/>
                <Scene key="main" tabs>
                    <Scene key='home' component={Home} title={APP_NAME} icon={props => <TabIcon name='home' text='首页' {...props}/>}/>
                    <Scene key='category' component={Category} title='演出分类' icon={props => <TabIcon name='category' text='分类' {...props}/>}/>
                    <Scene key='gift' component={Gift} title='周边' icon={props => <TabIcon name='gift' text='周边' {...props}/>}/>
                    <Scene key='user' component={My} title='我的'  icon={props => <TabIcon name='user' text='我的' {...props}/>}/>
                </Scene>
                <Scene key='details' component={Details} title='演出详情'/>
                <Scene key='error' component={Error} hideNavBar hideTabBar/>
            </Scene>
        </Scene>
    </RouterWithRedux>
)

const store = createStore()
export default class App extends Component {
    render() {
        return (
            <Provider store={store}>
            { routes }
            </Provider>
        )
    }
}

```
Router 的配置中， 首页的底部导航也得到很好的展现。关于 Icon ，不得不提 [react-native-vector-icons](https://github.com/oblador/react-native-vector-icons)，除了常见的 web 字体库，这货也支持引入第三方的字体库，看到对 [Icomoon](https://icomoon.io) 的支持，鄙人再次被感动的痛苦流涕😂
![RN-show 首页](/images/2016_08/home.jpg)
左边是 web 版的，右边是 RN 的 IOS 版的。在分类页的实现中，采用了 [react-native-scrollable-tab-view](https://github.com/brentvatne/react-native-scrollable-tab-view)
![RN-show 分类页](/images/2016_08/category.jpg)
利用 `react-native-router-flux` 的 `Actions` 做页面间的切换，很是方便

```
// 跳 详情页 
Actions.details({ concert /* 参数传递在这里*/ })
```
除此之外，对于历史记录的管理，有套和 Web History 类似的 API。

>  BUG 小提示:  
> 1. 利用` Actions` [跳 Tabs 中定义的 Scene 时，无法接收到传递的参数](https://github.com/aksonov/react-native-router-flux/issues/563)，这个真是坑死我了。。。  
> 2. React-Native 中 [Fetch not Allowed via HTTP at default on IOS](http://stackoverflow.com/questions/31254725/transport-security-has-blocked-a-cleartext-http)，也是入门的大坑之一

补充一点，关于 Hot Reload，React-Native 已经先天具备，但和 Web 比起来，呵呵呵。。。

以上便是 RN 初探之旅遇到的点滴。

### 小结

得益于 React 和 React-Native 的丰富生态，组件化的统一已不是一个愿景了，下面的 RN 相关的组件同时支持 IOS 和 Android，而 `redux`，`Fetch` 之类已经不区分 Web 和 Native ，这个也许就是组件化的未来，谁知道呢？让我们拭目以待

```
  "dependencies": {
    "fetch": "^1.1.0",
    "react": "^15.2.1",
    "react-dom": "^15.2.1",
    "react-native": "^0.31.0",
    "react-native-button": "^1.6.0",
    "react-native-htmlview": "^0.5.0",
    "react-native-router-flux": "^3.34.0",
    "react-native-scrollable-tab-view": "^0.5.3",
    "react-native-search-bar": "^2.16.0",
    "react-native-spinkit": "^0.1.2",
    "react-native-swiper": "^1.4.8",
    "react-native-vector-icons": "^2.0.3",
    "react-redux": "^4.4.5",
    "redux": "^3.5.2",
    "redux-actions": "^0.10.1",
    "redux-logger": "^2.6.1",
    "redux-thunk": "^2.1.0",
    "reselect": "^2.5.3"
  }

```
Native 也好，Web 也罢，App的实现过程已转化为组件的自由组合和少许的定制，研发的门槛愈来愈底。那，DEV 的我们路在何方呢？

这个问题容我好好考虑下...

---

**有感：很多问题真不是自己代码的问题，没事多 Google，对了，引用的第三方类库下的 Issues 里也要多搜搜看**

题图是[上篇文章](http://dhong.co/beautiful-coding)的奖励，就酱
