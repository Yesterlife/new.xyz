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
| [状态](#state)        | 居家的我们           |
| [样式](#theme)        | 装修                 |

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

上面的配置中，整个 App 的页面结构一目了然(有没有户型图的即视感？)。注意，路由是可以嵌套的。对于共享导航布局的页面，可以将导航提取到父级页面(`Category` 和 `List` 中共享的导航，放置到 `Home` 中)。
![page switch](/images/2016_07/switch.jpg)
图中两个页面共享底部导航，根据路由的不同，激活不同的 `Icon`.

### <span id="component">组件</span>
每一层建筑，按照大小规格的不同，划分成若干个不同的房间，每个房间按照功能布置成厨房、卧室、卫生间等等。同样的每个页面，按照 UI，也可以分成 `Header`，`Content`，`Footer` 等。

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
注意：代码示例中，并没有直接罗列所有的组件，而是做了一层 `div` 的封装。这里参考了 [Container Component](https://medium.com/@learnreact/container-components-c0e67432e005#.cwigmx3fe)

> A Container does data fetching and then renders its corresponding sub-component. That's it.

页面是个顶层组件，后续的页面都是由它进行渲染。顶层组件负责子组件的布局，以及后续组件所需数据的获取。上面示例中，将 `Home` 拆分成三部分，`Home__Header` 是固定顶部导航，`Home__Footer`是固定底部导航，中间是内容部分，数据由当前组件传递给子组件。子组件只做自己份内的事，无需关注其他。

页面被拆分成若干个组件，类似的，每个组件又可以拆分成若干个子组件，按照分治策略的思想，最终回归到 `vision-ui` 与 DOM 元素的组合。  

> `vision-ui`：得益于 UI 设计的统一，前端按照设计规范，将公共组件进行了抽离，就诞生了 `vision-ui` 。它包含了很多常用的组件，如 `Button`, `Label`, `LazyImage`, `Loading`, `Message`, `Modal`, `Stepper`, `Text`, `Tabs`等，也有一些预置的样式定义，便于后续的复写和继承。

这种观念，在 `React` 的 `Component` 实现中，得到了完美体现。`createElement` 和 `createClass` 的区别就在于此，后者的无限细分，就是前者的组合调用。`createElement` 生成的是实际的 DOM 元素。 
以 `Footer` 实现为例：  

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

下面以点击某个按钮，显示和隐藏 `Modal` 为例。Modal 的显示和隐藏通过 state 中的 `show_modal` 进行切换，同时也更新了对应的 `Icon`。

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
稍微不同的是，我们把 `Modal` 的隐藏交由了 `Modal` 自己去完成。它通过调用父组件的 `togglePickerModal` 来设置 `show_modal` 的值，从而达到隐藏自己的目的。

```
import Modal from 'vision-ui'

export default props => (
    <Modal onClose={props.onClose} 
        okText="确定"
        cancelText="取消">
        { /* some content */ }
    </Modal>
)

```

组件的状态，可以由父组件传递给子组件，同级间的状态交互，可有共同的父组件管理。在组件组成的树形结构中，数据就是这样一层层，自上而下传递的。在 [Flux](http://facebook.github.io/flux/) 之前，状态都是通过 setState 操作和管理，可以想象一下，多级组件之间, 跨层级间的数据传递，是多么的痛苦。

[flux](http://facebook.github.io/flux/) 是 Facebook 官方出品的单向数据流解决方案。借助于 F 家 VirtualDOM 高效的 Diff 算法，将数据的修改从 `setState` 中解脱出来，以 `Action` 的触发取代，统一集中到 Store 处理。[redux](https://github.com/reactjs/redux) 是 flux 思想的社区实现，简洁和高效。

![redux data flow](/images/2016_07/redux_flowchart.png)

> Redux is a predictable state container for JavaScript apps.

redux 中的 `connect` 函数采用[HOC](https://medium.com/@franleplant/react-higher-order-components-in-depth-cf9032ee6c3e#.pxjs82j6g) 的方式，对参数组件做了一层封装，在封装后的组件中，将相关数据传递给了参数组件。

```
// HOC 原理
hocFactory:: W: React.Component => E: React.Component
```
下面是 redux 官方的使用示例，store 中的数据，以`connect`的方式传递给了根组件 `TodoApp`

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
参考这种做法，任何组件都可以把数据 connect 进来。这样一来，我们就可以跨越组件的限制，将想要的属性传递给对应的组件。但此种方式仅推荐在顶层组件中使用，比如 Router 对应的 Page 组件。
组件本身是职责和功能的一种隔离，是最小数据和功能的集合。树形结构是组件化的最好体现，按照层级的高低，功能和复杂度逐步较低。`connect` 里拥有 `store` 中所有的数据，是对组件的进一步封装。尽量减少组件树中 `connect` 的使用，做到树形结构的精简和高效。

那多层组件件的数据传递该如何呢？`Container` 组件给了我们很好的参考，合理拆分，避免跨越层级件的组件依赖。

### <span id="theme">样式</span>

与组件类似，样式也是和组件相关联的。不同点在于，我们把样式分成了全局样式和局部样式

- 全局样式

全局样式包含 App 的样式重置，以及一些常用的布局（flex, float, none）动画（fade, slide)，以及变量定义(采用SCSS）

flex 是前端的布局利器，兼容问题一度让我们望洋兴叹。[PostCSS](http://postcss.org/)的使用，解决了我们的后顾之忧。

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
- 局部样式

局部样式是组件内部的样式定义，我们按照 [BEM](https://en.bem.info/methodology/key-concepts/) 模式对样式进行隔离。这是一个良好编码习惯的开始，在提高 CSS 的性能的同时，在输出格式上也利于阅读和理解。

![bem](/images/2016_07/bem.jpg) </br>

- 主题

对于 SCSS 而言，把颜色、字体、边距风格进行统一，集中在一个文件（variables.scss）。所有的组件样式都引用该文件，使用该文件中定义的变量定义。SCSS 的这种特性让主题定制变得简单明了。
`vision-ui` 的默认主题是 `wechat` 绿，通过 `theme.scss` 中的一行样式复写，主题就变成了 `QQ` 蓝。

```
/* theme.scss, 用于覆盖 variables 中的样式定义*/

$main-color: #2835e9;

```
![theme](/images/2016_07/theme.jpg)

下面的部分，是实现的原理。
`vision-ui` 中用到的色值、字体大小、边框，都集中放到了 `variables.scss` 中，以便于主题的自定义。众多的 `UI` 框架里也都是这么做的，这点是及其重要的。
通过在 `variables` 之前引用 `theme`，按照 `SCSS` 中 `!default` 关键字的原理，后声明的变量定义会覆盖前面的，这点和 `CSS` 类似。 

```
/* varilables.scss */

@import 'vision-ui/components/variables',
    'theme';

/* 其他样式变量定义 */
```
同样的，使用 `vision-ui` 中预定义的变量也很简单，在使用之前，引入上面的 `variables.scss` 即可。
这里面包含所有 `vision-ui` 中所有的变量定义（复写后的），当然，也包括自己定义的。

```
/* some_component.scss */

@import 'variables';

.Component {

    &__Header {
        color: $main-color;
        font-size: $small;
    }

    &__Content {
        padding: 5px 10px;
        border: 1px solid $grey;
    }

}

```
以上便是我们对 `SCSS` 的使用手法，在此安利 `BEM`。 

### 总结

以上是我们在整个重构中的 UI 策略，它极大的缩减了我们在 UI 调整上的时间（大部分组件都是 vison-ui 的再封装)。另一方面，在多人协作上，统一的规范，在减少了代码的冗余的同时，也提高了代码的可阅读性，得以让我们更高效的继续推进。

走出自己的舒适区，多学习，多实践，学习之路永无止境。

在路上

<a href="http://www.lagou.com/jobs/169113.html" style="display:block">
    <img src="/images/2016_07/we-want-you.jpg"/>
</a>
