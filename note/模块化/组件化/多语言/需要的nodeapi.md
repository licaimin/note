# update-pkg

## Install

```
$ npm install --save update-pkg
```

## Usage

```
const Pkg = require('update-pkg') 
const pkg = new Pkg()pkg.data //=> package.json object pkg.set('author.name', 'EGOIST')
pkg.saveSync()// or Promisepkg.save().then(/* ... */)
```

# path

## path.resolve()

`path.resolve`方法用于将相对路径转为绝对路径。

它可以接受多个参数，依次表示所要进入的路径，直到将最后一个参数转为绝对路径。如果根据参数无法得到绝对路径，就以当前所在路径作为基准。除了根目录，该方法的返回值都不带尾部的斜杠。

 dest: 'src/lang/' // 目标目录

const targetFolder = path.resolve(dest)

  console.log('路径', targetFolder)// h:\temp\study\mynpm\test\src\lang

# fs

### mkdirpSync

### writeFile

# download

getByDataFilter

返回的数据 { spreadsheetId: '1sb9Yx9L8--2sTzhfObOqLrSCFv45Gohzlbjr0ik_42o',
  properties:
   { title: 'test',
     locale: 'en_GB',
     autoRecalc: 'ON_CHANGE',
     timeZone: 'Etc/GMT',
     defaultFormat:
      { backgroundColor: [Object],
        padding: [Object],
        verticalAlignment: 'BOTTOM',
        wrapStrategy: 'OVERFLOW_CELL',
        textFormat: [Object] } },
  sheets: [ { properties: [Object] } ],
  spreadsheetUrl:
   'https://docs.google.com/spreadsheets/d/1sb9Yx9L8--2sTzhfObOqLrSCFv45Gohzlbjr0ik_42o/edit' }

## GET返回数据

  data:
   { range: 'Sheet1!A1:Z1000',
     majorDimension: 'ROWS',
     values: [ [Array], [Array], [Array], [Array] ] } }
tabMap [ [ [ '对照行', '中文Chinese', '英文 English' ],
    [ 'key', 'zh', 'en' ],
    [ '我问问', '我问问', '问问' ],
    [ '123' ] ],
  [ [ '对照行', '中文Chinese', '英文 English' ],
    [ 'key', 'zh', 'en' ],
    [ '22', '22', '22' ],
    [ '123' ] ] ]

# dataMap

dataMapwwwwwwwww { default:
   [ { key: '我问问', zh: '我问问', en: '问问' },
     { key: '123', zh: undefined, en: undefined } ],
  Sheet1:
   [ { key: '22', zh: '22', en: '22' },
     { key: '123', zh: undefined, en: undefined } ] }

# output

{ zh: { '22': '22', '123': undefined, wwwwww: '我问问' },

en: { '22': '22', '123': undefined, wwwwww: '问问' } }

target [ 'zh', 'en' ]

{ zh: { message: { '22': '22', '123': undefined, wwwwww: '我问问' } } }

```
let target = ['zh','en']
let obj = { zh: { '22': '22', '123': undefined, wwwwww: '我问问' },
en: { '22': '22', '123': undefined, wwwwww: '问问' } }
target.forEach((lang, index) => {
        const messag333e = obj[lang]
        console.log('message', messag333e)

        let fileData = {
          [lang]: {
            messag333e
          }
        }
        console.log('message', fileData)

    })
输出
message { '22': '22', '123': undefined, wwwwww: '我问问' }
message { zh:
   { messag333e: { '22': '22', '123': undefined, wwwwww: '我问问' } } }
message { '22': '22', '123': undefined, wwwwww: '问问' }
message { en:
   { messag333e: { '22': '22', '123': undefined, wwwwww: '问问' } } }
   
   注意多了个key值
```

转为json格式

```
function esm (obj) {
  let data = json(obj)
  return `/* eslint-disable */\nexport default ${data}`
}
```

```
sssss { en: { message: { '22': '22', '123': undefined, wwwwww: '问问' } } }
strFileData /* eslint-disable */
export default {
  "en": {
    "message": {
      "22": "22",
      "wwwwww": "问问"
    }
  }
}
```

```
f (!dryRun) {
          fs.mkdirpSync(targetFolder)
          fs.writeFile(targetPath, strFileData, function (err) {
            if (err) {
              console.log(err)
              resolve(false)
              log.error(`写入 ${targetPath} 失败`)
            } else {
              log.proc(`写入 ${targetPath} 成功`)
              if (index === len) { resolve(true) }
            }
          })
        }
        
        最后再创建文件夹，写入文件
```

