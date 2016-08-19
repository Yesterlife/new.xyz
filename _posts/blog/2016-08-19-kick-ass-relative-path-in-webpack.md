---
layout: post
title: kick ass relative path using webapck
description: use custom module_name in js and sass
category: blog
---

### JS

* Prerequirement
   
  Replace modules by other modules or paths. Following [webpack alias configuration](http://webpack.github.io/docs/configuration.html#resolve-alias)

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
* Usage

  ```
  import CusomerModules from ‘custom_module_name’
  ```

### SCSS

* Prerequirement

  config [sass-loader](https://github.com/jtangelder/sass-loader) 

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

* Usage 

  webpack provides an [advanced mechanism to resolve files](http://webpack.github.io/docs/resolving.html). The sass-loader uses node-sass' custom importer feature to pass all queries to the webpack resolving engine. Thus you can import your Sass modules from `node_modules`. Just prepend them with a `~` to tell webpack that this is not a relative import:
  
  ```css
  @import "~bootstrap/css/bootstrap";
  ```
  
  Alternatively, for bootstrap-sass:
  ```css
  @import "~bootstrap-sass/assets/stylesheets/bootstrap";
  ```
  
  It's important to only prepend it with `~`, because `~/` resolves to the home directory. webpack needs to distinguish between `bootstrap` and `~bootstrap` because CSS- and Sass-files have no special syntax for importing relative files. Writing `@import "file"` is the same as `@import "./file";`

  combine **previous alias feature**, we can link our assets as following
  
  ```
  background-image: url(~custom_assets_dir/path/to/your/images/file.jpg)
  ```


That's it. Cool !


