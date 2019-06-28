```js
const fs = require('fs')
const path = require('path')

const filePath = path.resolve(__dirname + '/src')
const extnameArray = ['.vue', '.scss', '.css']

/**
 * 修改文件
 */
function fileWrite (filePath) {
  fs.readFile(filePath, 'utf8', (err, files) => {
    if (err) return console.log(err)

    const result = files.replace(/[\d|\d+(\.\d+)]*rem/g, str => {
      const number = str.split('rem')[0] * 12
      return `${number === '' ? '' : number}px`
    })

    fs.writeFile(filePath, result, 'utf8', err => {
      if (err) return console.log(err)
    })
  })
}

/**
 * 遍历文件
 * @param {String} filePath - 需遍历的文件夹
 */
function fileDisplay (filePath) {
  //根据文件路径读取文件，返回文件列表
  fs.readdir(filePath, (err, files) => {
    if (err) console.log(err)
    else {
      //遍历读取到的文件列表
      files.forEach(filename => {
        //获取当前文件的绝对路径
        var filedir = path.join(filePath, filename)
        //根据文件路径获取文件信息，返回一个fs.Stats对象
        fs.stat(filedir, (eror, stats) => {
          if (eror) console.log('获取文件stats失败')
          else {
            const isFile = stats.isFile() // 文件
            const isDir = stats.isDirectory() // 文件夹
            const isCorrectFile = extnameArray.includes(path.extname(filedir))

            if (isFile && isCorrectFile) {
              fileWrite(filedir)
            }
            if (isDir) {
              fileDisplay(filedir) // 递归，如果是文件夹，就继续遍历该文件夹下面的文件
            }
          }
        })
      })
    }
  })
}

fileDisplay(filePath)
```