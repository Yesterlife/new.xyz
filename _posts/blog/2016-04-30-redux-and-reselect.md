---
layout: post
title: reselect for redux state
category: blog
description: Redux 的半年和 Reselect 的一周
---

来微票儿的半年里，从第一天就开始使用 [Redux](http://redux.js.org/) 了。

关于 Redux，没有比官方文档更介绍更详细的了， 当然你也可以看看[我同事怎么说](https://github.com/ingf/ingf.github.io/issues/4)。

下面先从 redux 的使用开始，一步步配置 redux 和 react 

> 环境依赖 ：`node.js` `npm`
> 
> sudo npm install we-pepper -g

**使用 pepper**

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

**初始化项目( react, react-router )**

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

> about `tree` command, install it with brew: 
> `brew install tree`

**安装 redux 相关依赖, 启动调试**

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

**配置 redux & react **

* 创建 redux 相关目录结构
 
```
➜  redux_and_reselect_toturial cd src
➜  src mkdir -p app/{actions,reducers,constants,selectors,store}
➜  src tree app -L 2
app
├── actions
├── constants
├── reducers
├── selectors
└── store
```
* 创建 action 

```
➜  src vi app/actions/index.js
➜  src more app/actions/index.js
import { FETCH_MOVIES, MOVIES_URL } from 'app/constants'

// fetch movies contains specified actor, such as Jason Statham
export function fetchMovies(name = 'Jason Statham') {
    return (dispatch, getState) => {
        fetch(`${MOVIES_URL}?actor=${name}`).
            then(resp => resp.json()).
            then(movies => dispatch(receiveMovies(movies)))
    }
}

// return fetched movies as redux action
function receiveMovies(movies) {
    return {
        type: FETCH_MOVIES,
        movies
    }
}

```
*  创建 `app/constants/index.js`

```
➜  src vi app/constants/index.js
➜  src more app/constants/index.js
// action type
export const FETCH_MOVIES = 'FETCH_MOVIES'

export const MOVIES_URL = 'http://netflixroulette.net/api/api.php'

```
*  创建 reduce 处理API返回的 movies 

```
➜  src vi app/reducers/home.js
➜  src more app/reducers/home.js
import { FETCH_MOVIES } from 'app/constants'

const initState = {
    movies: [],
    loading: true
}

export default (state = initState, action) => {
    switch(action.type) {
        case FETCH_MOVIES:
            // when receive movies from action
            const { movies } = action;

            return Object.assign({}, state, {
                movies,
                loading: false
            })
        default:
            return state
    }
}
➜  src
```
* 有了 ation 和 reducer ，接下来就是配置 redux 和 react 的关联
首先配置 store 和 reducer 的关联

```
➜  src vi app/store/index.js
➜  src more app/store/index.js
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import reducer from 'app/reducers'
import { routerReducer, routerMiddleware } from 'react-router-redux'

let middlewares = [thunk]

// exported by pepper
const MODE = process.env.MODE

if( MODE !== 'release') {
    // only add logger middleware except release mode
    let createLogger = require('redux-logger')

    // log redux action operation using console.log
    const logger = createLogger({
        level: 'info',
        logger: console,
        collapsed: true
    })

    middlewares = [...middlewares, logger]
}

export default (history, initialState) => {
    // add react-router history middleware in
    middlewares = [...middlewares, routerMiddleware(history)]
    // apply middlewares within store
    const createStoreWithMiddleware = applyMiddleware(...middlewares)(createStore)
    // bind reducer and initState
    return createStoreWithMiddleware(reducer, initialState)
}
➜  src
```
导出 `app/reducers` 目录下的 reducers, 现在只有 home

```
➜  src vi app/reducers/index.js
➜  src more app/reducers/index.js
import { combineReducers } from 'redux'
import { routerReducer } from 'react-router-redux'
import home from './home'

/*
* combine all reducers into one root reducers, which store all app state
* redux only has one sotre, so only one state tree is necessary
*/
const rootReducer = combineReducers({
    home,
    // react-router require this config
    routing: routerReducer,
})

export default rootReducer
➜  src
```
接下来，要配置 react 和 redux store 的关联, 改动如下

```
➜  src vi pages/index.js
➜  src more pages/index.js
import ReactDom from 'react-dom'
import { Router, Route, Link, IndexRoute, useRouterHistory } from 'react-router'

import { connect, Provider } from 'react-redux'
import { syncHistoryWithStore } from 'react-router-redux'
// use this script to create an redux store, which combine reducers
import configureStore from 'app/store'

import { createHashHistory } from 'history'
import objectAssign from 'object-assign'
import Home from 'react-proxy?name=home!./home'
import About from 'react-proxy?name=about!./about'

Object.assign = objectAssign

// use hash router at current, for html5 history api require backend support
const appHistory = useRouterHistory(createHashHistory)({ queryKey: false })
// added in history middleware
const store = configureStore(appHistory)
// the final history with redux store merged in
const history = syncHistoryWithStore(appHistory, store)

let routes = history => (
    <Router history={history}>
        <Route path="/" component={Home} />
        <Route path="/about" component={About} />
    </Router>
)

ReactDom.render(
    <Provider store={store}>
    { routes(history) }
    </Provider>
, document.getElementById('app'))
```
另外，需在改动下 `pepper.config.js`, 在`alias`里将 `app` 加进去

```
// dir alias, could use globally, despite of CWD
"alias": {
    "scss"          :   "scss",
    "wepiao"        :   "components",
    "utils"         :   "utils",
    "app"           :   "app"
},
```
这样，pepper 就知道到 `app` 目录下去找 `app` 下的文件了，默认 `index.js`
当修改了 `pepper.config.js` 文件时，要重新启动下 pepper。现在就可以从 console 里看到 redux action 的相关日志了

![](images/2016_04/actionlog.jpg)

为了便于使用 pepper ,我们稍微修改下 `package.json` ,这样就可以在项目的任意目录执行 pepper 相关指令了，改动如下：

```
➜  src more ../package.json
{
  "name": "pepper",
  "version": "0.0.1",
  "description": "wepiao front end solution",
  "main": "app.js",
  "author": "FEI@wepiao.com",
  "license": "ISC",
  "scripts": {
     "start": "pepper start",
     "pepper": "pepper"
  },
  "dependencies": {
    "classnames": "^1.2.0",
    "history": "^2.0.1",
    "object-assign": "^4.0.1",
    "react": "^0.14.1",
    "react-dom": "^0.14.1",
    "react-redux": "^4.4.5",
    "react-router": "^2.0.0",
    "react-router-redux": "^4.0.4",
    "redux": "^3.5.2",
    "redux-logger": "^2.6.1",
    "redux-thunk": "^2.0.1"
  }
}
➜  src
```
* 关于API请求，这里将使用 ES6 中的 `fetch` API , 由于兼容性的问题，最好安装下相关的 polyfill 

```
npm install whatwg-fetch --save
```  
然后在 `pages/index.js` 里导入

```
import { Router, Route, Link, IndexRoute, useRouterHistory } from 'react-router'
import 'whatwg-fetch' // here it is
import { connect, Provider } from 'react-redux'
```
**注：`pages/index.js` 是整个项目的入口，先于所有文件执行，因而是添加各种 polyfill 的最佳位置**

* 使用 pepper 创建一个新的页面

```
➜  npm run pepper page movies

➜  src tree pages -L 2
pages
├── about
│   ├── index.js
│   └── style.scss
├── home
│   ├── index.js
│   └── style.scss
├── index.js
└── movies
    ├── index.js
    └── movies.scss

3 directories, 7 files

```
可以看到，movies 页面创建成功。接下来，按照 `movies/index.js` 文件底部的注释，添加该页面的路由配置

```
// first import new created movies page
import Movies from 'react-proxy?name=movies!./movies'

// then add an /movies router for the page
<Route path="/about" component={About} />
<Route path="/movies" component={Movies} />
```
刷新页面，访问 `http://0.0.0.0:9527/#/movies`, 效果如下

![](images/2016_04/movies-log.jpg)

* actions 去哪了  

搞了这么多配置，似乎忽略了 actions 的存在，接下来就该它登场了。然而，这个也是要配置的，是不是要疯掉了，:(

![](images/2016_04/redux-demo.jpg)

redux 官方 demo 里是这么处理的，但我觉得还有更好的方式。结合 ES6 `decorator` 特性，可以将上述操作封装成一个方法，更加方便的调用

```
➜  src vi utils/connect.js
➜  src more utils/connect.js
import { connect } from 'react-redux'
import { bindActionCreators } from 'redux'
import * as actions from 'app/actions'

function mapStateToProps(props, state) {
    if(!props) return state

    // filter state by specified string key
    if(typeof props === 'string') return { [props]: state[props] }

    // filter state by specified props
    if(Array.isArray(props)) {
        return props.reduce((prev, curr) => prev[curr] = state[curr], {})
    }

    return state
}

function mapDispatchToProps(dispatch) {
    return {
        actions: bindActionCreators(actions, dispatch)
    }
}

export default props => {

    // filter state to props map, or use your own map
    let stateToPropsMap = typeof props === 'function' ? props : mapStateToProps.bind(null, props)

    return target => connect(stateToPropsMap, mapDispatchToProps)(target)
}
➜  src
```
这样一来，stateToProps  和 actions 的 处理将变得异常优雅和精练

* 将 actions 和 movies state 和 Movies 页面进行关联

```
➜  src more pages/movies/index.js
import styles from './movies';
// import { component_name } from 'wepiao'
import connect from 'utils/connect'

@connect()
export default class Movies extends React.Component {

    static propTypes = {

    }

    static defaultProps = {

    }

    componentWillMount() {
        console.log('home reducer: ', this.props.home)
        console.log('all actions: ', this.props.actions)
    }
    render() {

        return (
            <div className='Movies'>
                some content ...
            </div>
        )
    }
}
➜  src
``` 
刷新页面，你将看到 

![](images/2016_04/reducer-log.jpg)

* 调用 action 获取 Movies 信息

拿到 action 和 home reducer 的默认数据后，我们就可以调用 API ，去获取真实的数据了。
修改 `pages/movies/index.js` 如下

```
    componentWillMount() {
        console.log('home reducer: ', this.props.home)
        console.log('all actions: ', this.props.actions)

        this.props.actions.fetchMovies() // use default actor name
    }

    render() {
        let { loading, movies } = this.props.home

        return (
            <div className='Movies'>
            {
                loading ? 'Loading...' :
                movies.map(movie => (
                    <div key={movie.show_id}>
                    {
                        movie.show_title
                    }
                    </div>
                ))
            }
            </div>
        )
    }
```
下面将是见证奇迹的时刻，刷新页面，如下图所示

![](images/2016_04/load-movie.gif)

