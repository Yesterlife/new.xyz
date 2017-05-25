---
layout: post
title: RN 之旅
description: learn once, write everywhere
category: blog
---
入职微影的近两年里，一直在 react 的阵营中耕耘：团队内部开发了 pepper (类似 [create-react-app](https://github.com/facebookincubator/create-react-app )的自动化工具）、公共组件 vision-ui ，并先后协同其他团队成员，完成 [M 站](https://m.wepiao.com)、[微信端演出票](https://wechat.show.wepiao.com)、[微信端电影票](https://wx.wepiao.com) 等诸多项目的重构工作，也取得了不错的成绩。

如今，公司内部的前端项目几乎全部切换至 react 上来，期间 react 社区也发生了巨大的变化，相关生态 [react-native](https://facebook.github.io/react-native) 、[react-vr](https://facebook.github.io/react-vr) 也逐渐趋于稳定和日益壮大。因而在 react 方向的横向扩展，就成了团队接下来研究方向。 

终于，在 16 年的下半年，迎来了拥抱 react-native 的机会。

> 注：下文中简称 react-native 为 **RN**

### 背景介绍

>  [娱票儿票房分析](https://appsto.re/cn/4poF-.i)是我司出品的一款电影数据类应用，为电影爱好者和从业者提供专业、精准、实时的行业数据查询工具，内容包括实时票房、预售票房、上座率、全国排片、城市排片、影院排片、观音人次、影院排期、电影票房排行榜等。 — Apple Store 介绍

![票房分析](https://ww1.sinaimg.cn/large/006tNc79ly1ffwgkrjczrj30ot0f30tp.jpg)

原票房分析 app 属于混合模式移动应用（Hybrid App）, 即在 [Web App](https://piaofang.wepiao.com) 上包一层 Native 的壳子，从而兼备 Native  和 Web 的一些优势：

* Native 的推送、截屏、分享
* Web 的快速迭代和发版、图表渲染等

但另一方面，也意味着在体验和性能上的妥协。考虑到本次重构的目标：

* 优化性能
* 提升体验
* 全新改版（图表内容较多）

怎么实现上述目标成了首先要解决的问题?

### 改版方案

针对上面提到的重构目标，内部研究了两种方案：

* Hybrid  调整成 pepper 支持的项目

  根据之前的重构经验，轻车熟路，用时也比较少。原 web 部分是基于 react 实现多页面应用，用 pepper 重构成基于 react 的单页面应用（SPA），就这点来说，性能方面提升空间十分有限。对于体验，仍然摆脱不了 Hybird 的硬伤。就团队成长来说，几乎也学不到什么新的技能。**不可行！**

* Native 完全推到重来

  此方案倒是可以解决性能和体验的问题，但和我们**前端**团队就没啥关系了。**就此打住**！

* RN

  结合诸多线上实例和社区反馈，再结合自身状况：「Learn Once，Write Everywhere 」的理念，实践在此处再合适不过。对于用 JS 渲染成的原生App 而言，体验自不必说。性能方面，从社区反馈以及自身体验（下了几个 App 尝试了下）几乎觉察不出区别（iOS 很棒，嗯，Android 欠佳）。图表问题，旧版 web 使用 [ECharts](http://echarts.baidu.com/) 方案，还好社区内有一个 WebView 桥接的 [react-native-echarts](https://github.com/somonus/react-native-echarts) 实现可言替换。

  **就是它了！**

方案确定后，重构的任务自然而然的归结为以下几个方面  

* 迁移原有 Native 的功能
* 图表渲染
* 热更新
* 项目结构设计

#### 迁移原有 Native 的功能

原票房分析 的 Native 功能主要为： 

* 截屏、录屏分享
* 推送

通过与客户端协商好的 URL Schema 协议进行交互。RN 方面，提供了  [Native Modules](http://facebook.github.io/react-native/docs/native-modules-ios.html) 作为 Native 功能的延伸，JS 可以直接调用 [Native Modules](http://facebook.github.io/react-native/docs/native-modules-ios.html) 中对外的接口，以应对 RN 中缺少的、高性能、多线程的一些场景。这点，在整合原有 App 功能上极为方便，也赋予了混合 RN App 的能力，即在原有 Native 中整合 RN 的过度方案。 

#### 图表渲染

由于原 web 图表基于 [echarts](http://echarts.baidu.com) 的缘故，RN 版的 [react-native-echarts](https://github.com/somonus/react-native-echarts) 成了目前最适合的方案。虽然该方案是基于 webview 实现的图表，但能将性能和体验的降级的控制在图表展示上，且完全兼容旧版 API ，还算完美。

RN 版的其他实现，大部分比较基础和简单。也有一些[基于 Native 封装的方案](https://github.com/wuxudong/react-native-charts-wrapper) 看起来也挺丰富，但在需求匹配上不如 [react-native-echarts](https://github.com/somonus/react-native-echarts)  适合。

#### 热更新

它解决了 Native 开发最受诟病的问题，具备了 Web 快速迭代的能力。

> [CodePush](https://microsoft.github.io/code-push/) is a service from Microsoft that makes it easy to deploy live updates to your React Native app. If you don't like going through the app store process to deploy little tweaks, and you also don't like setting up your own backend, give CodePush a try. — [react-native offical site](http://facebook.github.io/react-native/releases/0.44/docs/more-resources.html#development-tools)

说到它，不得不提两句前阵子的 [Apple警告邮件](https://github.com/bang590/JSPatch/issues/746) 事件，一时让「 热更方案岌岌可危 」。对 RN 来讲，虽然是躺着中枪，但开发者对事件的反应来看，没有热更的日子简直不敢想 😂。

#### 项目结构设计

在项目结构上，延续了 react 项目的经验:

![project design](https://ww1.sinaimg.cn/large/006tNc79ly1ffwol47h84j31740regmr.jpg)

项目的依赖上，与 react 的差异主要在 UI 上，`*-react-native-*` 都是针对 RN 的，而 [redux](http://redux.js.org/) 全家桶两个项目间共享。所以，如果在移植一套 web 方案的话，只要在提供一套 web 的 UI 方案即可（ [react-native-web](https://github.com/necolas/react-native-web) 更进一步，兼容 web 的做法为移植提供了更多的便利，观望中...）。

| 名称                                   | 说明                     |
| ------------------------------------ | ---------------------- |
| react                                |                        |
| react-native                         |                        |
| **热更新**                              |                        |
| react-native-code-push               | 热更新方案                  |
| **UI**                               |                        |
| react-native-extended-stylesheet     | 样式，可拓展，支持全局变量          |
| react-native-scrollable-tab-view     | 可滚动 Tabs               |
| widow-ui                             | 公共组件库                  |
| native-echarts                       | ECharts 图表库            |
| react-native-swiper                  | 开屏引导插件                 |
| react-native-vector-icons            | Icon                   |
| **国际化**                              |                        |
| react-native-i18n                    | 国际化支持                  |
| **统计**                               |                        |
| react-native-google-analytics-bridge | ga  统计                 |
| react-native-talkingdata             | talkingdata 统计         |
| **redux 全家桶**                        |                        |
| redux                                |                        |
| react-redux                          | react 集成               |
| redux-actions                        | 便捷的 action 创建处理        |
| redux-logger                         | action 日志              |
| redux-saga                           | 异步 action，基于 Generator |
| reselect                             | mapStateToProps 的优化再封装 |

**插件安装**：在安装 RN 插件时，有些要执行下 `react-native link xxx-xxx`，有些则不用。它们的区别主要在于是否有依赖封装的 Native Modules 模块，`react-native link` 所做的就是把依赖的 Native Modules 模块配置到 ios 和 android 项目中。这个操作会对 Native 的代码做些修改，具体的改动部分可以参考相关插件的手动配置说明（建议熟悉一下，卸载的时候方便些 [捂脸] ）。

万事俱备，接下来进入实战环节，Let's do it

### 实战演练

实战部分，会提到开发中常见的一些问题，还有些代码上的优化，主要涵盖以下方面：

* 调试
* 组件
* 样式
* 热更新

#### 调试

以 IOS 调试为例，在当前 app 中，使用快捷键 `⌘` + **D** 即可调出调试选项（摇一摇就不太好使了吧～）

![debugger react-native app](https://facebook.github.io/react-native/releases/0.44/img/DeveloperMenu.png)

为便于开发调试，推荐开启以下选项

| 名称                  | 说明                         |
| ------------------- | -------------------------- |
| Remote JS Debugging | 在 Chrome 中查看 log 信息，👍     |
| Hot Reload          | 保留应用状态的即时刷新，告别 `⌘` + **R** |

如果你也和我一样，对黄色的警告信息简直不能忍受：

```javascript
console.disableYellowBox = true; 
```

红屏（redbox）的错误就不能忽视了。需要注意的是，不管红屏还是黄屏都不会在正式发布的 app 里出现，当然，红屏的话应用就直接闪退了。

> RedBoxes and YellowBoxes are automatically disabled in release (production) builds.   — react-native official site

补充一下：开发的大部分场景下，都不需要 Xcode 或者 Android Studio，尽管使用自己喜爱的 IDE 就好了。

```bash
$ react-native run-ios # 会自己调出 IOS 模拟器
$ react-native run-android # Android 模拟器需要手动启动
```

#### 组件

首先说下第三方组件的引用，以 [react-native-vector-icons](https://github.com/oblador/react-native-vector-icons) 为例：

```bash
$ yarn add react-native-vector-icons # 下载
$ react-native link react-native-vector-icons # 配置 native 包引用
```

第一步安装组件时都会用到，第二步只针对依赖 native 实现的组件。该 Icon 组件实现依赖 [Native Modules](http://facebook.github.io/react-native/docs/native-modules-ios.html) ，所以需要将对应 native 的实现部分配置到对应的 native 项目中（ios 和 android 目录下都会有相应修改），之后就可以像 RN 内置的组件那样使用了（ RN 组件在项目初始化的时候就已经配置好了）。

下面大致介绍下，如何用 JS 实现一个服务费组件：

```jsx
import React, { Component } from 'react';
import { View, Text, Switch, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  fee: {
    flexDirection: 'row',
    justifyConent: 'flex-end',
    alignItems: 'center'
  },
  text: {
    color: '#333',
    fontSize: 12
  },
})

export default class Fee extends Compoennt {
  static defaultProps = {
    disabled: false,
    value: true,
    onChange: () => {},
  }
  render() {
      const { disabled, onChange, value } = this.props;
    return (
        <View style={styles.fee}>
            <Text style={styles.text}>
            服务费
            </Text>
            <Switch
                disabled={disabled}
                onValueChange={onChange}
                value={value}/>
        </View>
    )
  }
}
```

将文件保存成 `Fee/index.js` 即可

```
├── Fee
│   └── index.js
```

需要注意的是，由于 `Switch` 的实现基于原生组件，对应平台的 UI 样式会有些差异： 

![switch difference](https://ww3.sinaimg.cn/large/006tNbRwly1ffxidmidlyj30r207egli.jpg)

针对平台性的差异，[RN 官方推荐了两种做法](http://facebook.github.io/react-native/releases/0.18/docs/platform-specific-code.html#content)

* 使用包含对应平台的文件后缀

  ```jsx
  BigButton.ios.js
  BigButton.android.js
  ```

* Platform 模块

  ```jsx
  import { Platform, StyleSheet } from 'react-native';

  const styles = StyleSheet.create({
    height: (Platform.OS === 'ios') ? 200 : 100,
  });
  ```

依赖上述方案，可以做到与系统 UI 风格的协调统一，当前，完全保持一致也是可以的。对于 UI 细节的调整，就要使用到 StyleSheet 了，也就是接下来要讲到的样式。

#### 样式

StyleSheet 基本类似于 web 平台的样式方案 [CSS in JS](https://medium.com/@steida/css-in-js-the-argument-refined-471c7eb83955) ，类似的原因在于，这个是面向 native 组件的，差异还是有的，[这里是完整的支持列表](https://github.com/doyoe/react-native-stylesheet-guide)。没有了选择器和 media query 的干扰，[CSS in JS](https://medium.com/@steida/css-in-js-the-argument-refined-471c7eb83955) 的方案用在此处还是比较可行的，至于公共样式的提取，[react-native-extended-stylesheet](https://github.com/vitalets/react-native-extended-stylesheet) 处理的还算完美。

```jsx
import ESS from 'react-native-extended-stylesheet'

// define global style, color or fontSize
ESS.build({
  'color-yellow': '#ff5200',
  'color-blue': 'blue',
  'size-small': 12,
})

// usage in StyleSheet
const styles = ESS.create({
  button: {
    color: '$color-yellow',
    fontSize: '$size-small'
  },
  active: {
    color: '$color-blue'
  }
})

<Button style={styles.button} title='OK'/>
// combine multiple styles
<Button style={[styles.button, isActive ? styles.active || {}]} title='OK'/>
  
// usage in js
<Icon size={28} color={ESS.value('color-yellow')} name='share'/>
```

其他的暂且放下，今天只想说 Flexbox 。web 平台布局方案 Flexbox 的引入，使得 web 到 RN 的过度简直完美，[背后的 yoga](https://github.com/facebook/yoga) 功不可没。

#### 更新推送

如果代码的更新不涉及 native 部分（iOS 或者 android 目录）的修改， [code-push ](https://github.com/Microsoft/code-push) 是不二之选。

参考 [react-native-code-push](https://github.com/Microsoft/react-native-code-push) 官方文档，集成使用甚为便捷：

```jsx
import codePush from "react-native-code-push";

@codePush({ checkFrequency: codePush.CheckFrequency.ON_APP_RESUME }) // check updates at every resume
class MyApp extends Component {
  ...
}
```

发布更新的环节：

```bash
$ code-push login # 设置好账户信息
$ code-push app add xxx # 创建 app

# 发布命令
$ code-push release-react [app-name] [platform] [options]

# release demo
$ code-push release-react deadpool-ios ios -m -t 3.4.4 # 针对 3.4.4 的 iOS 推送强制更新
# 默认推送为 Staging 状态，相当于预上线，测试完毕后可切换到正式版本
$ code-push promote deadpool-ios Staging Production
```

当然，更新的回滚也是支持的

```
# Release a ios production rollback
code-push rollback deadpool-ios Production
```

[更多选项请参考官方文档](https://github.com/Microsoft/code-push/tree/master/cli)

#### 小结

关于页面跳转的卡顿问题，利用 `InteractionMamanger` 的收效显著

```jsx
import { InteractionMamanger } from 'react-native';

// heavy jobs 
InteractionManager.runAfterInteractions(() => {
  // api request or page navigation
})
```

对于 [ListView](http://facebook.github.io/react-native/releases/0.42/docs/listview.html) 的性能问题，在 43 以后的版本中用 [FlatList](http://facebook.github.io/react-native/releases/0.44/docs/flatlist.html) 解决

Redux 方面，web 到 RN 都是通用的。RN 版中，尝试了 [redux-saga](https://github.com/redux-saga/redux-saga)，基于 Generator 实现的异步 action 方案，比 [redux-thunk](https://github.com/gaearon/redux-thunk) 更具灵活性。在 saga 里一个 action 的处理流程被拆分4 部分：

* **take** 主动监听 action 的触发

  ```javascript
  // action defination
  function requestMovies(params) {
    return {
      type: REQUEST_MOVIES,
      params
    }
  }
  // dispatch action
  this.props.actions.requestMovies(params);
  ...
  // here, using a saga demond to watch your action
  function* watchRequestMovies() {
    while(true) {
      const { params } = yield take(types.FETCH_MOVIES);
      yield fork(requestMovies, params)
    }
  }
  ```

* **select** 获取当前的状态

  ```jsx
  // fetch previous movies state from store
  const originalsMovies = select(store => store.movies)
  ```

* **call** 执行操作

  ```javascript
  try{
    // send http request to fetch movies
    const movies = yield call(GET, API_MOVIES_URL, params)
  } catch(err) {
    // process error state
  }
  ```

* **put** 相当于 dispatch ，是 action 处理的最后一个过程

  ```javascript
  // recice movies action logic
  function receiveMoviesAction(movies) {
    return {
      type: RECEIVE_MOVIES_SUCCESSED,
      movies
    }
  }
  ...
  yield put(receiveMoviesAction(movies))
  ```

以上便是 saga 处理 action 的过程，完整的 reqestMovies 如下：

```
function* requestMovies({ params }) {
  // fetch previous movies state from store
  const originalMovies = select(store => store.movies)
  if(originalMovies.length) {
      // using cached movies
    put(receiveMoviesAction(originalsMovies))
  }
  try {
    // send http request to fetch movies
    const moviesData = yield call(GET, API_MOVIES_URL, params);
    if(moviesData.ret === 0) {
      put(receiveMoviesAction(movieData.movies))
    } else {
      put(receiveMoveisFailedAction())
    }
    // hide loading state
    put(hideLoadingStateAction())
  } catch(err) {
    // process error state
  }
} 
```

对比  [redux-thunk](https://github.com/gaearon/redux-thunk) , saga 使得 action 更加存粹，业务处理逻辑全部拆分到 saga 的各个流程当中，易于多流程的组合和测试（[官方示例的登录操作](https://redux-saga.js.org/docs/advanced/NonBlockingCalls.html)）

### 总结

![ready to release](https://ww4.sinaimg.cn/large/006tNbRwly1ffxongaymyj311s0o040c.jpg)

>  Learn Once, Write Everywhere

web 到 RN 的平滑过度，是该理念的有效证明。Redux 模块和 Flexbox 的复用，将差异集中缩减至 UI 环节，性能敏感部分又可切换至 Native 实现，更加坚定了对 RN 未来的信心。

[react-vr](https://facebook.github.io/react-vr) 的开源，似乎宣告了 VR 时代的来临，你准备好了吗？
