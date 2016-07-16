### Pepper
pepper 是团队开发的一款用于基础构建的工具，我们用它来生成，构建，和打包项目文件

```
➜  pepper
? 选择要执行的任务:  (Use arrow keys)
❯ 项目打包
  代码格式校验
  开发调试
  初始化新项目 (es5)
  初始化新项目 (es6 & react & react-router)
  初始化新项目 (es6 & react & react-router & redux)
  创建新页面
  创建新组件
  升级pepper
```
如上所示，它集成了诸多功能，而它的使用也很简单。

```
➜  pepper init wechat
➜  tree wechat -L 1
wechat
├── README.md
├── mock.js
├── node_modules
├── npm-shrinkwrap.json
├── package.json
├── pepper.config.js ＃pepper 配置文件
├── proxy.html
├── src
├── template.html
└── test.html

2 directories, 8 files
```
该配置文件，及其上面的项目都可以通过 pepper 的新建项目命令（pepper init），快速生成。配置预览如下

```
// pepper.config.js
module.exports = {
    // debug host
    "host": "0.0.0.0",
    // debug port
    "port": "9527",
    // pepper src entry, also inner webpack entry, default to `src/pages/index.js`
    "base": "src",
    // target build dir
    "build": "dist",
    "static": {
        "start": "/",
        "test": "/",
        "pre": "/",
        "release": "//your_cdn_domain" // here use CDN domain
    },
    "api": {
        "start": "",
        "test": "",
        "pre": "",
        "release": "//your_api_enterpoint",
    },
    // third patry libs to bundle
    "vendor": ["react", "react-dom", 'react-router'],
    // dir alias, could use globally, despite of CWD
    "alias": {
        "scss": "scss",
        "wepiao": "components",
    },
    // source map options
    "devtool": "source-map",
    // switch for CSS Modules
    "css_modules": false,
    // switch for eslint
    "eslint": false,
    // limit image size for use base64, (smaller use base64, larger use url)
    "base64_image_limit": 10240 // 10k
}
```
另外通过配置 `package.json` 中的 `scripts`命令 ，可以很方便的在项目任何目录执行调试、打包等命令

```
  "scripts": {
    "pepper": "pepper",
    "cptest": "cp test.html proxy.html dist",
    "start": "pepper start",
    "test": "pepper -V && pepper test ",
    "pre": "pepper -V && pepper pre",
    "release": "pepper -V && pepper release"
  },
```
以上，pepper 为我们搭好了的项目的根基。

