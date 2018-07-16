##### 该demo基于vue-cli3.0，可以用于移动端的多页面开发



> vue-cli3.0是最新版本的vue官方脚手架，有着很多新功能以及简单的目录结构，详情请参考[官方文档](https://cli.vuejs.org/)

> 同时，项目使用vw进行移动端适配，viewport如今已被大多数浏览器兼容。关于vue适配vw，详情可看《[如何在Vue项目中使用vw实现移动端适配](https://www.w3cplus.com/mobile/vw-layout-in-vue.html)》

```powershell
npm install		//安装依赖
```

```powershell
npm run serve	//运行
```

vue-cli3.0的源码中，已经自带多页面配置的源码了。在文档中，也明确说明可以直接在pages属性上进行配置。

在根目录新建vue.config.js配置文件，这样配置即可。

```javascript
module.exports = {
  pages: {
    index: {
      // entry for the page
      entry: 'src/index/index.js',
      // the source template
      template: 'src/index/index.html',
      // output as dist/index.html
      filename: 'index.html',
      // when using title option,
      // template title tag needs to be <title><%= htmlWebpackPlugin.options.title %></title>
      title: 'Index Page'
    }
  }
}
```

可以添加`about: {}`这样的页面，这样打包出来就有两个html文件了。

**然而，这样每加一个页面就要加一个配置项，显然麻烦，我们可以通过一段代码，自动添加**。

```javascript
const fs = require('fs')
const glob = require("glob")
const px2vw = require("postcss-px-to-viewport")

const pages = {}
let entries
try {
  // 获取相关入口
  entries = glob('src/views/*/index.js', {sync: true})
} catch (err) {
  entries = []
  throw err
}
// 格式化生成入口
entries.forEach((file) => {
  // file字符串为 src/views/.../index.js
  const fileSplit = file.split('/')
  const pageName = fileSplit[2]
  let pageHtml = fileSplit.slice(0, 3).join('/') + '/index.html'
  if (!fs.existsSync(pageHtml)) {
    // 入口如果不配置直接使用 _default.html
    pageHtml = fileSplit.slice(0, 2).join('/') + '/_default.html'
  }
  pages[pageName] = {
    entry: file,
    template: pageHtml,
    filename: `${pageName}.html`
  }
})
module.exports = {
  pages,
}
```

这样就大功告成了。



**不需要移动端适配的同学可以不看下面的**

首先先安装插件

```powershell
npm install postcss-px-to-viewport --save-dev
```

然后在vue.config.js上这样配置。

```javascript
css: {
    loaderOptions: {
      css: {
        // options here will be passed to css-loader
      },
      postcss: {
        plugins: () => [px2vw({
          viewportWidth: 750,
          viewporHeight: 134,
          unitPrecision: 3,
          viewportUnit: 'vw',
          selectorBlackList: ['.ignore', '.hairlines'],
          minPixelValue: 1,
          mediaQuery: false
        })]
      }
    }
  }
```

项目简单的配置大概就这些，更深入的vw适配配置，请查看上面那篇文章。

**如果你喜欢，请给我一个star咯~**

