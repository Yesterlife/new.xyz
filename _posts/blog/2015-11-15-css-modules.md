---
layout: post
title: CSS Modules
category: blog
description: global CSS is dying in the sun
---
![](/images/2015_11/runner-far.jpg)

继昨天的 **Webpack Code Splitting**，这周第二个任务：**CSS MODULES**

昨天的博客到凌晨** 03:18 **才算完结，今天的 **22:58** 分开始，还不知道要写到啥时候

**007 幽灵党**，开场前奏不错，要不咱也来段儿：

---

上午8点多醒来的时候，外面一片雾蒙蒙，这几天的空气真的很差，但还是挡不住出去跑步的脚步。这周还没有跑过一公里，这两天一定要做点什么吧～

于是，整理行装，准备出门。哎，可恼人的是，地铁卡和工卡不知道哪去了，难道就这么取消行程吗？不行，这样不行，雾霾怕什么，墨菲定律说了，该发生的总会发生，所以，毅然决然出去跑步，为了这个小插曲，我要刷个半马，以示吾心（谢谢关心爱护我的朋友们，带上口罩跑步真的很不爽，所以，嘿嘿）。

雾霾中的奥森也是不缺人的，朦胧之中自有其优美之处，你可曾发现？天公也不错，把羞涩的太阳也放出来了一小会儿，瞅了瞅奥森还有谁在跑步，哈哈。半马中途，遇到一高中生，聊到了彼此间的同感 “**独行者步疾，结伴者行远** ”

>  Wanna go fast, go alone. Wanna go far, go together.

小伙子现在高三，跑龄5年，从一个小胖子，跑成了一个帅小伙（笑～）。也许当初跑步是为了减肥，但现在已经不是了，它已经变成了一种习惯。作为一个跑者，本身是孤独的，我也感同身受。同样，我们都享受跑步带来的畅快淋漓，满身臭汗，冲个热水澡，带走一切不如意，尽情享受焕然一新的自己。

和他相比，我的跑龄只有5个月，同样也不是为了减肥。我也很少和跑团的小伙伴们一起跑，我的节奏太快，她的配速较慢，为了彼此的跑步体验，还是做一个孤独的跑者比较好吧。当然，有时候也会一起，边跑边聊，畅快且尽兴。

跑步，是一种享受，跑的时候不要去想，给大脑一个放松的时间，带动机体，尽情放纵，在汗水中涅槃，用热水澡冲走疲惫，躺在床上，呆呆看着天花板，渐渐入睡，惬意的人生就这么简单。

---

享受归享受，天要下雨，娘要嫁人，干货来也。

CSS MODULES的概念，很新潮，但也是自然而然的事。在模块化开发提上议程之后，全局CSS的问题也日益显现出来。

借助于webpack这样的工具，用 style-loader解决了样式和组件一起打包的问题。这样，组件就可以完全JS化，NICE。  

```
// webpack config
module: {
        loaders: [{
            test: /\.jsx?$/,
            loaders: ['react-hot', 'babel'],
            exclude: /node_modules/,
            include: __dirname
        }, {
            test: /\.json$/,
            loader: "json"
        }, {
            test: /\.scss$/,
            exclude: [path.resolve(__dirname, config.base + '/scss')],
            loader: 'style!css?!autoprefixer!sass?sourceMap'
        }, {
            test: /\.(png|jpg|svg)$/,
            loader: 'url?limit=51200'//50k
        }, {
            test: /\.(ttf|eot|svg|woff[1-9]?)$/,
            loader: "file?name=assets/fonts/[name]-[hash:8].[ext]"
        }]
    },
    resolve: {
        root: __dirname,
        extensions: ['', '.js', '.json', '.jsx', '.scss', '.css']
    }
```
按照上述的配置之后，我们就可以这样写一个组件了

```
import React from 'react'
import styles from './style'

export default class Footer extends React.Component {
    render() {
      return <footer className='fixed-ft'>
        <ul className='nav-sub'>
            <li className='current'>
                <a href='javascript:void(0)'>
                    <i className='ico-movie'></i>上映
                </a>
            </li>
            <li>
                <a href='javascript:void(0)'>
                    <i className='ico-cinema'></i>影院
                </a>
            </li>
            <li>
                <a href='javascript:void(0)'>
                    <i className='ico-my'></i>我的
                </a>
            </li>
        </ul>
      </footer>
    }
}
```

```
// style.scss
.nav {
    position: fixed;
    width: 100%;
    background-color: #fefefe;
    z-index: 1000;
    top: 0;
}
.curent {
    background-image: url()'../../images/active.png')
}

```

即使有图片也无所谓，只要在设置的大小范围内，仍旧可以以Base64的格式存储在JS里，这样的组件才是我们最最期待的。

然并卵，即使这样，仍旧有全局CSS的问题，那如何隔绝外部样式的干扰或者冲突呢？
那，去看看Facebook和Instagram的网页，有什么发现没？  
![](/images/2015_11/facebook.jpg)
![](/images/2015_11/instgram.jpg)

**广告时间： inst@duan112358, facebook@duan112358, twitter@duan112358 **

上述做法是不是很酷？现在，CSS MODULES就给予了我们这种实现方案，借助于webpack,那用起来就更方便了  

第一步，webpack设置

```
// loader config
{
    test: /\.scss$/,
    exclude: [path.resolve(__dirname, config.base + '/scss/shared')],
    loader: ExtractTextPlugin.extract('style', 'css?modules&importLoaders=1&localIdentName=[name]__[local]__[hash:base64:8]!autoprefixer!sass')
}

// plugin config add
new ExtractTextPlugin('css/[name]-[contenthash:8].css', { allChunks: true })
// for why set allChunks:true, i will tell later.
```
第二步，在JS里使用导出后的样式。[css-modules/webpack-demo](https://github.com/css-modules/webpack-demo)里很详细了，无需我在此赘言。

```
import React from 'react'
import ReactCssModules from 'react-css-modules'
import styles from '../../scss/style'

@ReactCssModules(styles, {allowMultiple:true, errorWhenNotFound: false})
export default class Footer extends React.Component {
    render() {
      return <footer styleName='fixed-ft' class='nav navbar'>
        <ul styleName='nav-sub'>
            <li styleName='current'>
                <a href='javascript:void(0)'>
                    <i styleName='ico-movie'></i>上映
                </a>
            </li>
            <li>
                <a href='javascript:void(0)'>
                    <i styleName='ico-cinema'></i>影院
                </a>
            </li>
            <li>
                <a href='javascript:void(0)'>
                    <i styleName='ico-my'></i>我的
                </a>
            </li>
        </ul>
      </footer>
    }
}

```
不过推荐采用这总做法，styleName指代该组件特有样式，className指代全局样式，两者可以同时使用。

那说下，为什么要指定** {allChunks: true} ** ？

上篇博文[webpack code spliting](http://dhong.co/webpack-code-splitting/)提到异步模块的问题以及解决方案。这里的allChunks就是为解决异步Chunks里的样式而设置的。我们知道，webpack不会主动分析异步Chunks里的依赖，其中的依赖也只是会打包到该Chunk中去，样式也是一样。那，如果不采用** {allChunks: true} **，结果如何呢？

![](/images/css-modules-html.jpg)
![](/images/css-modules-style.jpg)

CSS的处理是正常的，但为什么没有将CSS映射到DOM中去呢？  

```
import styles from './ScopedSelectors.css';

import React, { Component } from 'react';

export default class ScopedSelectors extends Component {

  render() {
    return (
      <div className={ styles.root }>
        <p className={ styles.text }>Scoped Selectors</p>
      </div>
    );
  }

};

```
打印输出这里的style,发现是个空值，真是个奇怪的问题，那启用***{ allChunks: true }***为什就会有值了呢？这也忒奇怪了

那CSS MODULES的实现究竟是怎么一回事呢？
![](/images/2015_11/css-modules-require.jpg)
正常情况下是导出了一个原样式和处理后样式的一个MAP映射，这也是可以想象得到的，那如果不去处理异步模块里的CSS引用，那没有任何样式MAP的输出也是理所当然的事了。

可设置了{allChunks: true}之后为什么就可以了呢？这就是 [extract-text-webpack-plugin](https://github.com/webpack/extract-text-webpack-plugin) 的魔法所在吧，等我细细研究一番这和各位探讨下

那如果使用在异步模块里使用CSS MODULES的话，就只好把样式全都提取出来，放到一个css里去了,者的缺是异步模块的一大痛点，拆出来的模块，又何必要再合回去呢？😂😂

所以，上次代码拆分的DEMO暂且没有采用CSS MIDODULES的方案，至少在该问题解决之前。

如果你有更好的方案，欢迎留言～

同样[DEMO](https://github.com/Duan112358/pepper)大餐奉上，谢谢光临

客观慢走，不送～

```
post@Sun Nov 15 01:25:19 CST 2015
```

