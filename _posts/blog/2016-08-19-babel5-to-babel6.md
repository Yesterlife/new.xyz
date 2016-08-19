---
layout: post
title: Babel5 => Babel6
description: 关于 Babel5 升级 Babel6 的一些
category: blog
---
TL;DR

> Babel5 => Babel6

Babel5 配置文件 `.babelrc`

```
// babel stage
stage: 0,
// some ENV relative configuration
env: {
  // dev only  
  development: {
      // HMR related config
      plugins: ['react-transform'],
          extra: {
              'react-transform': [{
                  target: 'react-transform-hmr',
                  imports: ['react'],
                  locals: ['module']
              }]
          }
      }
   }
}
```
Babel6 配置文件 `.babelrc`

```
presets: [
  // let, const, destructuring, classes, modules
  'es2015',
  // exponentiation
  'es2016',
  // JSX, Flow
  'react',
  // more draft es features
  'stage-0',
],
env: {
development: {
  plugins: [
    ['react-transform', {
      transforms: [{
        transform: ‘react-transform-hmr',
        imports: ['react'],
        locals: ['module']
      }]
    }]
  ],
}
},
plugins: [
  // function x(a, b, c,) { }
  'syntax-trailing-function-commas',
  // await fetch()
  'syntax-async-functions',
  // function* () { yield 42; yield 43; }
  'transform-regenerator',
  // Polyfills the runtime needed for async/await and generators
  ['transform-runtime'), {
    helpers: false,
    polyfill: false,
    regenerator: true
  }],
  'transform-decorators-legacy',
  'add-module-exports',
]
```

#### 一些差异
* 性能提升   

  Babel 6 解决了 Babel 5 中为性能优化考虑在转换和编译时的一些妥协

  > Babel 5 changed the transformation and traversal pipeline dramatically to make way for some major performance improvements that have been implemented in Babel 6

* 预设插件集    

  由于 Babel6 将 ES 特性的拆分到独立的插件中，为了便于指定和维护对应的 ES stage, Babel 将一些常用的插件组合到一起，于是就有了这些预设。常用的预设：`stage-[0/1/2]` `react` `es2015` `es2016` 

* 独立的转换插件 (etc `babel-plugin-transform-decorators-legacy `  for `@decorator` )  

  每个特性对应一个插件，这种细粒度的拆分，便于更细粒度的定制（不需要的不引用就是了）。

* [Babel@6 doesn't **export default module.exports** any more](https://github.com/59naga/babel-plugin-add-module-exports)

#### [热更新](http://webpack.github.io/docs/hot-module-replacement.html)

* [webpack-hot-middleware](https://github.com/webpack/webpack-dev-middleware)
* [webpack-dev-middleware](https://github.com/glenjamin/webpack-hot-middleware)
* [react-transform-boilerplate](https://github.com/gaearon/react-transform-boilerplate)

上面 ENV 中的配置都是为了支持开发时的组件热更新
