# vue-cli3.x 多页面配置

`
思路：遍历文件夹下的入口文件 main.js 和模版文件 index.html 。
`

### 遍历文件夹



```js
const path = require('path')
const glob = require('glob')
const fs = require('fs')

const PAGES_PATH = path.resolve(__dirname, './src/pages')
const pages = {}

// 配置 pages 目录下的多页面, 通过循环获取每一个 page 文件夹下的 html 和 js
glob.sync(PAGES_PATH + '/*/main.js').forEach(filepath => {
  let template = path.dirname(filepath) + '/index.html'
  const pageName = path.basename(path.dirname(filepath))

  try {
    fs.accessSync(template, fs.constants.F_OK)
  } catch (err) {
    template = path.resolve(__dirname, './public/index.html')
  }

  pages[pageName] = {
    entry: filepath,
    template,
    filename: `${pageName}.html`,
    title: pageName
    // chunks: ['chunk-vendors', 'chunk-common', pageName]
  }
})

module.exports = {
  publicPath: '/',
  pages: pages,
  productionSourceMap: false,
  devServer: {
    index: '/mall.html',
    port: '3000',
    proxy: {
      '/api-pre': {
        target: 'https://preapi3.vipbcw.com',
        secure: true,
        changeOrigin: true,
        pathRewrite: {
          '^/api-pre': ''
        }
      },
      '/api': {
        target: 'https://api3.vipbcw.com',
        secure: true,
        changeOrigin: true,
        pathRewrite: {
          '^/api': ''
        }
      }
    },
    historyApiFallback: {
      disableDotRule: true,
      rewrites: [
        { from: /^\/activity\.html/, to: '/activity.html' },
        { from: /./, to: '/mall.html' }
      ]
    }
  }
}
```

