---
layout: post
title: 演出票重构之 UI 篇
description: 我们是怎样用 React 对演出票进行重构的
category: blog
---

一直以来就有种执念：软件的构建，如同建筑的建造，都是一种美学。

众多IT行业里，前端是个奇葩，得益于历史原因，造就了漫天花海的浏览器端的适配与兼容。
另一点，对于一个FER，内心其实也是崩溃的，模块加载这条血泪之路，[HUX的这篇可谓经典](http://huangxuan.me/2015/07/09/js-module-7day/)。同样的，[CSS也不堪重负](http://huangxuan.me/2015/12/28/css-sucks-2015/)，友谊的小船说翻就翻 😂😂😂

冬天过去了，春天还会远吗？
React 的发布，带来了前端的春天，一经推出，便引领了模块化、组件化的浪潮，前端终于走上正轨。
两个月前，我们对[微信演出票](http://wechat.show.wepiao.com)的微信端，用 React 进行了重构。

构造 App 的过程，与构造一座建筑并无区别

| App                   |  建筑                |
| ----                  | ---                  |
| [路由](#router)       | 电梯、楼梯           |
| [组件](#component)    | 房间                 |
| [状态切换](#state)    | 居家的我们           |
| [样式主题](#theme)    | 装修                 |

### <span id="router">路由</span>

[react-router](https://github.com/reactjs/react-router) 是 React 生态的前端路由实现。App 按照路由进行拆分，每个路由指向一个页面。

```
const routes = (
    <Router>
        <Route path="/" component={Home}>
            <Route path="category" component={Category}/>
            <Route path="list" component={List}/>
            <Route path="list/:id" component={Detail}/>
        </Route>
        ...
        <Route path="/about" component={About} />
        <Route path="*" component={Home} />
    </Router>
)
```

上面的配置中，整个 App 的页面结构一目了然(有没有户型图的即视感？)。注意，路由是可以嵌套的。对于共享导航布局的页面，可以将导航提取到父级页面(Category 和 List 中共享的导航，放置到 Home 中)。
![page switch](/images/2016_07/switch.jpg)
图中两个页面共享底部导航，根据路由的不同，激活不同的 Icon.

### <span id="component">组件</span>
每一层建筑，按照大小规格的不同，划分成若干个不同的房间，每个房间按照功能布置成厨房、卧室、卫生间等等。同样的每个页面，按照 UI，也可以分成 Header，Content，Footer等。

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
效果如下图所示
![images](/images/2016_07/preview.png)
注意：代码示例中，并没有直接罗列所有的组件，而是做了一层 div 的封装。这里参考了 [Container Component](https://medium.com/@learnreact/container-components-c0e67432e005#.cwigmx3fe)

> A Container does data fetching and then renders its corresponding sub-component. That's it.

页面是个顶层组件，后续的页面都是由它进行渲染。顶层组件负责子组件的布局，以及后续组件所需数据的获取。上面示例中，将 Home 拆分成三部分，`Home__Header` 是固定顶部导航，`Home__Footer`是固定底部导航，中间是内容部分，数据由当前组件传递给子组件。子组件只做自己份内的事，无需关注其他。

页面被拆分成若干个组件，类似的，每个组件又可以拆分成若干个子组件，按照分治策略的思想，最终回归到 vision-ui 与 DOM 元素的组合。  

> `vision-ui`：得益于 UI 设计的统一，前端按照设计规范，将公共组件进行了抽离，就诞生了 vision-ui 。它包含了很多常用的组件，如 Button，Label，LazyImage，Loading，Message，Modal，Stepper，Text，Tabs等，也有一些预置的样式定义，便于后续的复写和继承。

这种观念，在 react 的 Component 实现中，得到了完美体现。createElement 和 createClass 的区别就在于此，后者的无限细分，就是前者的组合调用。createElement 生成的是实际的 DOM 元素。 
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
`components` 目录用于存放页面之间共享的组件，如果没有被复用，且放置到当前目录。    

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

### <span id="state">状态</span>

房间的温馨离不开人的参与，页面之间以状态进行耦合。这里的状态主要指 UI 的交互，它涉及到页面中组件的变化与组合，以及页面间的切换。

下面以点击某个按钮，显示和隐藏 Modal 为例。Modal 的显示和隐藏通过 state 中的 `show_modal` 进行切换，同时也更新了对应的 Icon。

```
togglePickerModal() {
    this.setState({
        show_modal: !this.state.show_modal
    })
}

render() {
    return (
        <div className="Picker">
            <div className="Picker__Header">
                <Icon className="Picker__Header__Icon"
                    onClick={this.togglePickerModal.bind(this)}
                    type={`filter--${!this.state.show_modal ? 'up' : 'down'}`}/>
            </div>
            { 
                this.state.show_modal && 
                    <PickerModal onClose={this.togglePickerModal.bind(this)}/> 
            }
        </div>
    )
}
```
稍微不同的是，我们把 Modal 的隐藏交由了 Modal 自己去完成。它通过调用父组件的 `togglePickerModal` 来设置 `show_modal` 的值，从而达到隐藏自己的目的。

```
import Modal from 'vision-ui'

export default props => {
    return (
        <Modal onClose={props.onClose} 
            okText="确定"
            cancelText="取消">
            { /* some content */ }
        </Modal>
    )
}

```

组件的状态，可以由父组件传递给子组件，同级间的状态交互，可有共同的父组件管理。想象一下 DOM 树的结构，组件树也是一样的，数据和状态就是这样一层层传递下来。在 [Flux](http://facebook.github.io/flux/) 之前，状态都是通过 setState 操作和管理，可以想象一下，多级组件之间的状态传递，有多么的痛苦。

> Redux is a predictable state container for JavaScript apps.

[redux](https://github.com/reactjs/redux)，通过一种新的模式，让属性传递变得简洁和优雅。

```
import { addTodo } from './actionCreators'
import { bindActionCreators, connect } from 'redux'

function mapStateToProps(state) {
      return { todos: state.todos }
}

function mapDispatchToProps(dispatch) {
      return bindActionCreators({ addTodo }, dispatch)
}

// here use HOC wraps props to TodoApp
export default connect(mapStateToProps, mapDispatchToProps)(TodoApp)
```

#### [HOC](https://medium.com/@franleplant/react-higher-order-components-in-depth-cf9032ee6c3e#.pxjs82j6g)

> A Higher Order Component is just a React Component that wraps another one.

```
hocFactory:: W: React.Component => E: React.Component
```
利用 ES7 中的 `decorator` 特性，将 `connect` 用 IOC 进行封装，结合 [reselect(https://github.com/reactjs/reselect) 对组件需要的属性进行下筛选

```
@connect(AppSelector)
export default class TodoApp extends React.Component {
    ...
}
```
这样一来，我们就可以跨越组件的限制，将想要的属性传递给对应的组件。

### <span id="theme">样式主题</span>

这里主要介绍 vision-ui 中的样式与主题的设置。vison-ui 中

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
