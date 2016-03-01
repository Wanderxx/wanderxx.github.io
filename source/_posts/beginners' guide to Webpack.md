title: Webpack 新手入门
---

原文链接  https://medium.com/@dabit3/beginner-s-guide-to-webpack-b1f1a3638460#.g5dc9raag

##### 或者 当你开始使用webpack时需要知道的一些必备知识

[点击这里](https://github.com/dabit3/beginning-webpack)到最终的GitHub 代码库。

Webpack 可算是一项最新最伟大的前端开发工具。它的模块包和所有现代化的前端工作流(Babel ReactJs CommonJs, etc)合作的如胶似漆.
以下是我一个webpack菜鸟的学习心得

## 开搞
#### Webpack 共识

1. webpack的最佳伴侣是NPm 而非 Bower
2. 选择一种模块系统（AMD CommonJS ES6 ）

#### 安装 webpack

```javascript
npm install webpack -g
```

## 最基本的构建

在你的根目录里创建两个文件 index.html app.js

#### app.js:

```javascript
document.write('welcome to my app');
console.log('app loaded')
```
  
#### index.html:

```html
<html>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

打开你的控制台，运行：

```javascript
webpack ./app.js bundle.js
```

以上命令 使用 打包命令(webpack) 引用 app.js  以及生成导出的文件 bundle.js (译者注：这里大家可能有所疑惑，把一个js变成另一个js有什么意义。 假如 当你的 app.js 引用了其他的js 或者 css 时，webpack 就会将他们统统打包 甚至压缩成一个文件， 怎么样？是不是再也不用在html里写冗长的scrpit 标签了？)

## 定义一个配置文件
Webpack 里的配置文件是一个基本的commonJs 模块。 这个配置文件包含了项目里所有的配置 ， loaders(稍后解释)， 以及其他项目相关信息

在你的根目录里创建 一个 webpack.config.js 文件 ，加上如下代码

```javascript
module.exports = {
  entry: "./app.js",
  output: {
    filename: "bundle.js"
  }
}
```

**entry**--  指向项目里最顶层的 一个文件 或者 一组文件，在这个项目里我们只用了 app.js

**output**-- 包括输出配置的一个对象。 在我们的项目中，我们只给filename 关键字 指定了 bundle.js, 即webpack 将会构建的输出文件

现在 打开命令行开始运行我们的app吧！

```javascript
webpack
```

一旦我们建立了 webpack.config 文件， webpack 命令 就会跟进我们的配置来构建输出文件

## 扩展config 文件 和 webpack 的功能

#### watchmode 监察模式

 在监察模式当中，Webpack 会监察你的所有文件，任一文件有所变动，它就会立马重新构建，重新创建你的输出文件。
 
有两种方法来允许监察模式。

1 命令行 敲

```javascript
webpack --watch
```

2 在我们的配置文件里加上一个关键字 watch 并设为true

```javascript
  module.exports = {
    entry: "./app.js",
    output: {
      filename: "bundle.js"
    }, 
    watch: true
  }
```

如此这般，webpack 就会自动捕捉你的文件变动啦

#### Webpack Dev Server (Webpack 开发者服务器)

Webpack 有一个web 服务器 叫做 webpack-dev-server, 我们现在本地安装一下它

```javascript
  npm install webpack-dev-server -g
```

在命令行中运行下面的命令

```javascript
  webpack-dev-server
```

![enter image description here](https://cdn-images-1.medium.com/max/800/1*CMLS4OdA1aUuhvrUMr4Gfw.jpeg)

然后 请在浏览器打开  http://localhost:8080/webpack-dev-server/ , 你就会发现你的app 正在那里运行啦，伴随着那些 logs ~~

*请注意 webpack 文档中的一句话*

> “这个 dev server 使用了Webpack 的侦查模式(watch mode)。它会将结果文件保留运行在内存中而不是生成到硬盘里。所以你并不会看到新的bundle.js,  需要的话你还是的敲入 webpack 命令”

当webpack dev server 运行时， 你会发现你在app里做的任何变化都会实时展现在服务器里（热加载 hot-loading）

热加载是默认使用的，如果你想禁掉并且去掉顶部那条 app ready status 栏，就在浏览器中直接 输入 http://localhost:8080/

如果要使用热加载，但是去掉顶部的 App ready status 栏， 就用下面的 命令，

```javascript
webpack-dev-server --inline
```

然后打开 http://localhost:8080/  你会发现 热加载还在 讨厌的 app ready 栏已经不在了

### 构建多个文件 

1.  需要的文件
 
  在你的项目中加入 looger.js 文件
  
  在 logger.js, 加入如下代码
  
```javascript
console.log('logger.js is now loaded...');
```

   在 app.js 中 将如下代码添至文件的顶端 以获取 logger.js
 
```javascript
require('./logger');
```

现在， 关闭后再次运行 webpack-dev-server 你会在控制台中看到 刚刚添加的log

2. 再添加一个入口文件到webpack.config.js 

在你的项目中加入 global.js，并添加如下代码，

```javascript
console.log('global.js is now loaded...');
```
  
现在，打开 weboack.config.js, 给 entry 关键字 赋一个数组

```javascript
  module.exports = {
    entry: ["./global.js", "./app.js"],
    output: {
      filename: "bundle.js"
    }
  }
```

 再次 关闭 运行 webpack-dev-server 你会发现 global.js 的log 也出现在控制台了

### webpack 加载器 和 预加载器

加载器让webpack 学会新的功能
  
**文档里说** “加载器可以让你使用 require 或者 ‘加载’他们是预先处理那些文件。加载器们有点像其他工具里的 ‘任务’ 并且同时提供一个牛逼的方法来处理前端构建步骤。有的加载器可以把像coffeescript之类不同的语音转成 javascript，有的加载器把图片转出数据地址。有的甚至能让你在js里require css文件！”

以下， 我们将要安装了两个加载器： Babel 和 JSHint. 开始之前。在我们开始用这些loaders之前，我们需要先安装几个npm 模块。
首先 使用 npm init 命令创建一个 package.json文件。 然后在Terminal 里 敲入：

```javascript
npm install babel-core babel-loader jshint jshint-loader node-libs-browser babel-preset-es2015 babel-preset-react webpack  --save-dev
```

将会把这些模块保存为开发的依赖

**在Webpack中使用Babel**

现在我们已经装好了Babel, 开始写点 es6 吧，然后把babel loader 加进来。

1. 打开 logger.js  删掉 console.log 语句， 用下面代码代替

```javascript
    let checkName= (firstName, lastName) => {
     if(firstName !== 'nader' || lastName !== 'dabit') {
       console.log('You are not Nader Dabit');
     } else {
        console.log('You are Nader Dabit');
      }
    }
    checkName('nader', 'jackson');
```
2. 把logger.js 改名为 logger.es6

3. 打开 webpack.config.js 并且 加入 babel loader. 若要在webpack里加入loader，必须先创建已个名为 module 的对象。module对象里再加入loaders 数组，数组里依次创建对应的loader 对象。 如下

      module.exports = {
       entry: ["./global.js" , "./app.js"],
       output: {
         filename: "bundle.js"
       },
       module: {
         loaders: [
           {
             test: /\.es6$/,
             exclude: /node_modules/,
             loader: 'babel-loader',
             query: {
               cacheDirectory: true, 
               presets: ['react', 'es2015'] 
             }
           }
         ]
       },
       resolve: {
         extensions: ['', '.js', '.es6']
       },
      }


以上你会发现，我们为第一个loader加入了几个key

1.  **test**-- 一个正则表达式来判定何种文件会通行在这个loader里。 我们加了一个 es6的拓展名
2.  **exclude** -- 这个loader 应该忽略哪些文件。 在此，我们加入了 node_modules 文件夹
3.  **loader** -- 我们将要使用的loader名（babel-loader）
4.  **query** --你可以通过写查询语句或者查询属性来将参数传给loader
5.  **cacheDirectory** -- 默认 false. 当设定后，给定的文件夹会缓存加载器的结果。之后的webpack构建就会优先
尝试从缓存中读取来避免安规的Babel编译过程。

我们也加入了一个名为 'resolve' 的关键字到我们的 module.exports 对象中、 resolve 可以让我们定义好何种类型的文件不需要给出特定文件名后缀就能被处理。这样我们可以使用如下语句

    require('./logger');

而非

    require('./logger.es6');

现在我们已经可以回到我们的项目里来了。关掉然后敲 webpack-dev-server 重新运行我们的 web server, 你就能看到新的log “You are not Nader Dabit”

*恭喜！你正在webpack上运行babel啦*

### 创建一个开始脚本

这个实际上是npm的一个特性， 但确实是些你值得做的事儿

在package.json, 有一个scripts 的关键字。用一下内容代替你原先script中的内容

    "scripts": {
      "start": "webpack-dev-server"
    },

现在我们就能用 npm start 来快速的开启 web server啦

    npm start

因为我们以后会不断加入更多参数，这样做确实省好多事呢

### 分离的产品和开发构建

若要运行出产品包, 让webpack 压缩你的code， 给webpack加个 -p 旗帜
然后就会产生一个压缩包

    webpack -p

**撰写不同的配置文件来应对成品与开发**

安装 strip-loader:

    npm install strip-loader --save-dev

strip-loader 可以将任意的函数从里的成品代码中剥离出去（稍后我们用到）

创建 webpack-production.config.js, 写下如下代码：

    var WebpackStripLoader = require('strip-loader');
    var devConfig = require('./webpack.config.js');
    var stripLoader = {
     test: [/\.js$/, /\.es6$/],
     exclude: /node_modules/,
     loader: WebpackStripLoader.loader('console.log')
    }
    devConfig.module.loaders.push(stripLoader);
    module.exports = devConfig;

1. **WebpackStripLoader** -- 我们请求的 strip-loader
2. **devConfig** -- 原始的 webpack 配置文件
3. **var striploader** -- 我们创建的一个新loader对象。唯一值得一提到是下面这行

    loader:WebpackStripLoader.loader('console.log')
    
  在这我们让 strip-loader 去掉任何 console.log语句WebpackStripLoader.loader() 可以加入任意多的参数
4. devConfig.module.loader.push(stripLoader) -- 我们将新loader 放进原来、的配置里

5. module.exports =devConfig -- 导出新的配置对象
  然后在终端里运行下面的代码
  
    webpack --config webpack-production.config.js -p

这次运行webpack带着config 标志，让我们利用定义好的配置文件。 -p 压缩了成品的代码

打开bundle.js 你就会发现我们的包里已经没有console 了



















