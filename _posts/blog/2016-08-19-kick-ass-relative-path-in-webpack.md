---
layout: post
title: kick ass relative path using webapck
description: 如何在 JS 和 SCSS 中使用自定义模块名
category: blog
---

相对路径的问题，可谓让人伤透了脑筋，绑定的目录结构可谓牵一发动全身。

> `../../path_to_your_file` 依赖当前资源的位置 

下面的做法主要围绕 webpack 的 alias 配置, 可以在项目中引入自定义的模块名（路径别名），来跨越目录结构的限制

### kick ass in JS

* 配置预设
   
  设置自定义的模块名称，可以设置为路径或者其他的模块. 具体参考 [webpack alias configuration](http://webpack.github.io/docs/configuration.html#resolve-alias)

  ```
  resolve: {
      // These are the reasonable defaults supported by the Node ecosystem.
      extensions: ['.js', '.json', ''],
      alias: {
        // This `alias` section can be safely removed after ejection.
         ‘custom_module_name’: custom_module_path,
      // can also proxy to other modules
        ‘react’: ‘preact’
      }
  },
  ```
* 使用指南

  ```
  import CusomerModules from ‘custom_module_name’
  ```

### kick ass in SCSS

* 配置预设

  这里使用 [sass-loader](https://github.com/jtangelder/sass-loader) 

  ```
  module.exports = {
	  ...
	  module: {
		loaders: [
		  {
			test: /\.scss$/,
			loaders: ["style", "css", "sass"]
		  }
		]
	 }
 }
  ```

* 使用指南 

  借助于 [webpack 强大的文件索引能力](http://webpack.github.io/docs/resolving.html), `sass-loader`将 `node-sass'的 `custom importer` 特性，将查询代理到 webpack 的索引引擎，从而使得 `node_modules` 中的 SASS 引用成为了可能. 现在，只需在引用的模块前缀 `~` 字符，它用来告诉 webpack 这是一个相对路径的引用，示例如下:
  
  ```css
  @import "~bootstrap/css/bootstrap";
  ```
  
  > 对于 CSS- 和 SASS- 来说，`@import 'file' 和 `@import './file` 结果是一样的。前缀 `~` 字符用于告诉 webpack 这是模块路径，而非当前的相对路径

  结合 **webpack alias**，同样的，静态资源的引用可可以脱离相对路径
 
  ```
  background-image: url(~custom_assets_dir/path/to/your/images/file.jpg)
  ```


就酱紫 ~ 

> Thks GOD, It's Friday ! 


