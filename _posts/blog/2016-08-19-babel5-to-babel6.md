---
layout: post
title: upgrade babel5 to babel6
description: some notes about upgrade babel5 to babel6
category: blog
---
TL;DR

> Notes about upgrading babel5 to babel6

babel5 config file `.babelrc`

```
{
     stage: 0,
     env: {
          development: {
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
babel6 config file `.babelrc`

```
  // Don't try to find .babelrc because we want to force this configuration.
  babelrc: false,
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

#### Differences
* performance impovements 


 Babel 5 changed the transformation and traversal pipeline dramatically to make way for some major performance improvements that have been implemented in Babel 6    

* plugin presets ( `stage-[0/1/2]`, `react`, `es2015`, `es2016` ]) 


 Since specifying and maintaining dozens of transformers in a config file would be a lot of work, Babel 6 is introducing the concept of Plugin Presets which group together similar plugins for easy consumption.  


* indepent transformer plugins (etc `babel-plugin-transform-decorators-legacy `  for `@decorator` )


 Since Babel is focusing on being a platform for JavaScript tooling and not an ES2015 transpiler, we’ve decided to make all of the plugins opt-in. This means when you install Babel it will no longer transpile your ES2015 code by default.  


* [Babel@6 doesn't **export default module.exports** any more](https://github.com/59naga/babel-plugin-add-module-exports)

