# 小程序互转插件

[gulp-vapp-transform](https://github.com/jd-ftf/gulp-vapp-transofrm) is a gulp plugin for Vapp and Weapp.

京东微信小程序互转工具

## Usage

首先, install `@ftf/gulp-vapp-transform` as a development dependency:

``` bash
npm install --save-dev @ftf/gulp-vapp-transform
```

然后添加到 `gulpfile.js` .

## 简单示例

``` javascript
const {
    htmlTransform,
    jsTransform,
    styleTransform,
    copyNoChange
} = require('@ftf/gulp-vapp-transform')

// 使用转换方法
const styleTask = () => styleTransform(entryPath, distPath, 'scss')()
const jsTask = () => jsTransform(entryPath, distPath)()
const htmlTask = () => htmlTransform(entryPath, distPath, 'jxml')()
const entryCopyPath = [
  `${entry}/**`,
  `!${entry}/**/*.js`,
]
const copy = () => copyNoChange(entryCopyPath, distPath)()

    ...otherTask

// 执行task
exports.dev = series(
    parallel(styleTask, jsTask, htmlTask, ...otherTask, copy)
)
```

## API

`@ftf/gulp-vapp-transform` 对外暴露四个个处理方法：

* htmlTransform - htl页面处理函数
* jsTransform - js文件处理函数
* styleTransform - css样式处理函数（当前预处理语言为scss）
* **copyNoChange** - （v1.1.2新增）未处理文件直接生成到目标文件夹

**请在在配置中设置当前环境变量 `process.env. PLATFORM` 为 `wx` 或 `jd` 。**

### htmlTransform

**html页面处理task事件:**

*htmlTransform(entryPath, outputPath, replaceExa, toExt)*

#### 参数

| 参数      |    类型 | 描述  | 是否必需 |
| :--------: | :--------:| :--: | :--: |
| entryPath  | String |  当前入口文件夹的全(绝对)路径   | 是 |
| outputPath  | String |  生成文件夹的全(绝对)路径   | 是 |
| replaceExa  | String |  需要解析替换的页面文件的后缀名 | 是 |
| *toExt (v1.1.2新增)*  | String | 指定生成的文件后缀， 若不设置该值，则走默认规则，即根据 `PLATFORM` 判断 | 否 |

#### 示例

1. 基本用法

``` javascript
const {
    htmlTransform
} = require('@ftf/gulp-vapp-transform')

const htmlTask = () => htmlTransform(entryPath, distPath, 'jxml')()
```

功能：

匹配 `entryPath` 下的所有后缀名为 `jxml` 的文件，如果当前环境变量 `process.env.PLATFORM === 'wx(jd)''` ， 将内部的逻辑转换成 `微信小程序(京东小程序)` 的可执行代码，生成到目标文件夹 `distPath` ，后缀名为 `wxml(jxml)` ；

2. 手动控制

除此之外也可以设置定向的生成后缀。

``` JavaScript
const {
    htmlTransform
} = require('@ftf/gulp-vapp-transform')

// 不根据PLATFORM值判断生成后缀， 根据entryPath 下的 jxml 文件  -> distPath 路径下的 wxml
const htmlTask = () => htmlTransform(entryPath, distPath, 'jxml', 'wxml')()
```

### jsTransform

**js文件处理task事件:**

*jsTransform(entryPath, outputPath, openBabel)*

#### 参数

| 参数      |    类型 | 描述  | 是否必需 |
| :--------: | :--------:| :--: | :--: |
| entryPath  | String |  当前入口文件夹的全(绝对)路径   | 是 |
| outputPath  | String |  生成文件夹的全(绝对)路径   | 是 |
| openBabel  | Boolean |  是否开启babel编译， 默认值为 `false` | 否 |

#### 示例

``` javascript
const {
    jsTransform
} = require('@ftf/gulp-vapp-transform')

const jsTask = () => jsTransform(entryPath, distPath)()
```

功能：

匹配 `entryPath` 下的所有后缀名为 `js` 的文件，如果当前环境变量 `process.env. PLATFORM === 'wx(jd)'` ，将内部的逻辑转换成 `微信小程序(京东小程序)` 的可执行代码，不进行babel编译， 生成到目标文件夹 `distPath` 。

### styleTransform

**样式文件处理task事件:**

*styleTransform(entryPath, outputPath, replaceExa, toExt)*

#### 参数

| 参数      |    类型 | 描述  | 是否必需 |
| :--------: | :--------:| :--: | :--: |
| entryPath  | String |  当前入口文件夹的全(绝对)路径   | 是 |
| outputPath  | String |  生成文件夹的全(绝对)路径   | 是 |
| replaceExa  | String|  需要解析替换的页面文件的后缀名， 当前支持 `scss / jxss / wxss` | 是 |
| *toExt (v1.1.2新增)*  | String | 指定生成的文件后缀， 若不设置该值，则走默认规则，即根据 `PLATFORM` 判断 | 否 |

#### 示例

1. 基本用法

``` javascript
const {
    styleTransform
} = require('@ftf/gulp-vapp-transform')

const styleTask = () => styleTransform(entryPath, distPath, 'scss')()
```

功能：

匹配 `entryPath` 下的所有后缀名为 `scss` 的文件，如果当前环境变量 `process.env. PLATFORM === 'wx(jd)'` ，后缀名变更为 `wxss(jxss)` ，生成到目标文件夹 `distPath` 。

2. 手动控制

`styleTransform` 也可以设置定向的生成后缀。

``` JavaScript
const {
    styleTransform
} = require('@ftf/gulp-vapp-transform')

// 不根据PLATFORM值判断生成后缀， 根据entryPath 下的 scss 文件  -> distPath 路径下的 jxss
const styleTask = () => styleTransform(entryPath, distPath, 'scss', 'jxss')()
```

### copyNoChange

**无修改文件，直接生成到目录(v1.1.2):**

*copyNoChange(entryPath, outputPath)*

#### 参数

| 参数      |    类型 | 描述  | 是否必需 |
| :--------: | :--------:| :--: | :--: |
| entryPath  | String |  当前入口文件夹的全(绝对)路径   | 是 |
| outputPath  | String |  生成文件夹的全(绝对)路径   | 是 |

#### 示例

``` JavaScript
const {
    copyNoChange
} = require('@ftf/gulp-vapp-transform')

/**
 *  !后方的路径都不需要复制，因为前面的task已经将文件生成， 需要原样赋值的为除手动生成之外的文件
 *  更多语法参考gulp匹配规则
 */
const entryPath = [
  `${entry}/**`,
  `!${entry}/**/*.js`,
]

const jsTask = () => jsTransform(entryPath, distPath)()
// copy 文件到路径distPath
const copy = () => copyNoChange(entryPath, distPath)()
```
