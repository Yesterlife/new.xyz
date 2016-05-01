---
layout: post
title: reselect for redux state (2)
description: cache, remap for redux state
category: blog
---

接上篇 [reselect fo redux state](http://dhong.co/redux-and-reselect)。

先回顾下上篇中的知识点和遗留的问题：

> 知识点  
> 1. redux 单项数据流的实现，action -> reducer -> store -> component   
> 2. setState 与 action 协作方式的不同，后者可以缓存当前组件的信息。  
> 3. 对 state 进行统一管理  
> 
> 问题   
> 1. 切换页面时，render 中数据的重复计算  
> 2. 如何更好的缓存数据  

关于 redux 的问题，我和同事的想法有很多不同。  

* 按照官方教程，reducer 只存放最原始数据，业务相关数据的处理放到 component 这级来做，也就是 render 之前（对于在 render 里处理数据，我一直耿耿于怀）。对于异步数据的处理，如果放到 componentDidMount 或者 componentWillReceiveProps 里，会很棘手，根本原因在于 props 中的原始数据的再处理。 在 render 中处理，避免了状态判断的问题，即拿到的数据总是最新的，缺点就是每次重新计算。   

  所以，我主张把对业务数据的处理部分提到 reducer 这一环节，这样的话，后续的引用组件只需关注 render 环节就好了，这也正是 react 所擅长的（stateless component is very efficient）。但这样也有问题，原始数据被污染了，在不同组件间共享数据的时候尤为严重。不同组件间数据会有些差异，这就意味着要保存多份原始数据的处理结果，数据的冗余就出现了，在保持数据的一致性面前，这简直就是噩梦。 
* 一定要保留原始数据。官方文档中， mapStateToProps 与 connect 的组合使用，看起来很简单，实际上别有用心，将数据的过滤和筛选工作放到 mapStateToProps 里，保留了原始数据的完整性。这就是设计模式中的适配器模式，但这也没有避免数据重新计算的问题。  
* 精简数据。只分配给组件所需要的数据，不要更多。多就意味着冗余，有可能引起副作用。   
* component 与 component 之间数据的传递。**Container component** 模式很受用，不论从布局还是数据层面，都是很优雅的额解决方案。问题出现在，多层级 component 数据传递上。对于这个问题，一种是中规中矩的一层层传递，另一种偷懒做法就是 **connect** Container 组件的数据，我当然偏向于偷懒做法。  
* 优雅和实际间的取舍。官方的示例优雅精练，这点不容置喙。然现实问题复杂多变，难免催生出众多野路子，所谓 happy hacking，也是源于此。如果你说，每个程序员都是艺术家，我举双手。但艺术呢，需要能欣赏它的人，代码也一样，自认为优雅的代码可能没几个同事能读懂（注释注释注释......），少给后人留点坑吧。之前的一段时间，总是觉得抱着“先完成，再完美”的理念（感觉被前公司洗脑了😂😂😂）。对我们来说，最悲哀的莫过于收拾前人留下的烂摊子，所以嘛，给后人多留条活路吧。认真对待每一件事，不要把完美留给明天（明天还有新的需求等着呢😂😂😂）。尽量参照官方，那是大家共同的文档。  
* 交流和沟通。无需赘言，上面的很多内容，是现在才领悟到的。分享是一种态度，也是一种能力。多尝试分享自己的想法和感悟，自己明白是一回事儿，能让别人明白就是另一回事儿了。说也好，写也好，于己而言，是总结也是精练。  

----    
   
#### 关于 [Reselect](https://github.com/reactjs/reselect)  

> * Selectors can compute derived data, allowing Redux to store the minimal possible state.
> * Selectors are efficient. A selector is not recomputed unless one of its arguments change.
> * Selectors are composable. They can be used as input to other selectors.

最开心的莫过于，遇到问题的时候，Google 出来类似的话了，so 开搞吧。

```
➜  src git:(master) ✗ vi app/selectors/movies.js
➜  src git:(master) ✗ more app/selectors/movies.js
import { createSelector } from 'reselect'

const getMovies = state => state.home.movies

const getLoading = state => state.home.loading

const getSort = state => state.home.filter.sort

const getSearch = state => state.home.filter.search

export default createSelector(
    getMovies,
    getLoading,
    getSort,
    getSearch,
    (movies, loading, sort, search) => {
        // first filter
        let _movies = search.length ?
            movies.filter(m => m.show_title.toLowerCase().indexOf(search.toLowerCase()) !== -1) :
            [...movies]

        // then sort
        _movies = sort === 'default' ?
            _movies :
            _movies.sort((m1, m2) => m1[sort] < m2[sort])

        console.log('computed', Date.now());

        // final data structure we wanted.
        return {
            sort,
            search,
            movies: _movies,
            loading,
        }
    }
)
➜  src git:(master) ✗
```
由于精简了最终返回的数据结构，抽离了业务数据的计算，所以，组件层面要稍微调整下：

```
import styles from './movies';
// import { component_name } from 'wepiao'
import connect from 'utils/connect'
import MoviesSelector from 'app/selectors/movies'

@connect(MoviesSelector)
export default class Movies extends React.Component {

    componentWillMount() {
        // use cached resource at necessary
        this.props.loading && this.props.actions.fetchMovies() // use default actor name
    }

    componentDidMount() {
        let { sort, search } = this.props
        this.refs.input.value = search
        this.refs.select.value = sort
    }

    onChangeSort(e) {
        console.log(e.target.value);
        this.props.actions.filterMovies({ sort: e.target.value })
    }

    onChangeSearch(e) {
        console.log(e.target.value)
        this.props.actions.filterMovies({ search: e.target.value })
    }

    // filterAndSortMovies(movies) {
    //     let { sort, search } = this.props.home.filter
    //     console.log(sort, search);
    //     let _movies = search.length ?
    //         movies.filter(m => m.show_title.toLowerCase().indexOf(search.toLowerCase()) !== -1) :
    //         movies
    //     if(sort === 'default') {
    //         return _movies
    //     } else {
    //         return _movies.sort((m1, m2) => m1[sort] < m2[sort])
    //     }
    // }

    render() {
        let { loading, movies } = this.props

        // movies = this.filterAndSortMovies(movies)
        
        ...
    }

```
经过 reselect 这一层，component 得到的 props 结构是这样的 

![](images/2016_05/remap.jpg)

注意，我在 selector 返回最终数据的函数中，插入了一段 log 用于记录从新计算的过程

```
console.log('computed', Date.now());

```
在下图中，当页面刷新的时候，有两次计算过程，一次是 API 请求之前，一次发生在拿到 API 返回的数据之后，这是正常的逻辑。之后，在页面前进和后退的过程中，没有再看到计算的日志。由此，我们的目的就达到了。

![](images/2016_05/compared.gif) 

此中情况的关键还在于，调用 action 的时机。这里，我处理的有点牵强 

```
componentWillMount() {
    // use cached resource at necessary
    this.props.loading && this.props.actions.fetchMovies() // use default actor name
}
```
然而，这确实缓存利用的关键所在，它决定了原始数据能否重新利用。从根源处判断，才是解决缓存问题的关键所在。

之前，也一直纠结在这块逻辑上，如何利用意境计算出来的数据（已经缓存在 store 中了嘛）。请求层面的缓存，只是解决了 HTTP 时延的问题，但前端的渲染前的计算缓存，同样重要，试想，整个 app 都用 stateless components 搭建，性能岂不爆表，这才是将缓存发挥到极致。API 层数据缓存（session），UI 层业务模型缓存(reselect)，react 层 DOM 缓存（virtual DOM）, 哈哈，这么多层，性能上不去才怪呢!

---


#### 小结

到上面这一步，磕磕绊绊，半年了。现在的前端，日新月异，技术层面日益多元化。就现在这个项目骨架，就涉及到下面这么多东西，这，对一个新人来说，很难接受，也无从着手。这块，也是不得不考虑的一点。

resource                                                             |  description
----                                                                 |-------
[react](http://facebook.github.io/react)                           |  UI library
[react-router](https://github.com/reactjs/react-router)            |  react router component
[redux](http://redux.js.org)                                       |  react flux implementation, more elegant ang powerful
[redux-thunk](http://github.com/gaearon/redux-thunk)               |  The easiest way to write async action creators
[redux-logger](https://github.com/fcomb/redux-logger)              |  Log every Redux action and the next state
[react-router-redux](https://github.com/reactjs/react-router-redux)|  Ruthlessly simple bindings to keep React Router and Redux in sync
[reselect](https://github.com/faassen/reselect)                    |  Efficient derived data selectors inspired by NuclearJS
[react-redux](https://github.com/gaearon/react-redux)              |  react binding
[whatwg-fetch](http://github.github.io/fetch/)                     |  fetch polyfill
[pepper](https://www.npmjs.com/package/we-pepper)                  |  build tools, based on webpack, mainly for react projects

另外一点，开源组件的依赖中，你都知道其功能特点吗？还是只是参照官方 Demo 照搬过来的？

关于我，现在从事前端基础工程建设的工作，包含性能相关，工具库和UI库的搭建等工作，有时候不免在前沿技术层面走的太快，而忽略了真正的使用者的需求，忽视了他们的立场。所以，就想着把这些东西封装起来（that's [pepper](https://www.npmjs.com/package/we-pepper)），让使用变的简单。

这就好比 IDE 与 VI 的区别，喜欢的爱的死去活来，不喜欢的嗤之以鼻。

前路的一扇窗户，照亮着你前行的路，走近它，能看到更宽广的天空，打开它，你看到的是整个世界。

> Stay Hungary, Stay Foolish

