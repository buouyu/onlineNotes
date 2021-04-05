
commonjs实现模块化 
```module.exports```导出
```require```导入
es6实现模块化
```<script src="入口文件" type="module">```
引入自己写的js需要加后缀```.js```
```js
import {b} from "./b.js";//导入b.js里面导出的b变量
import "./init.js"//执行init.js
import * as a from "./a.js"//导入a.js，变量a对象为a.js所有导出的集合
import "./b.js" //这条导入语句，仅会运行模块，不适用它内部的任何导出

export var a = 1; //导出a，值为1，类似于CommonJS中的exports.a = 1
export function test() { //导出test，值为一个函数，类似于CommonJS中的exports.test = function (){}

}
export class Person {

}
export const name = "abc"
var age = 18;
var sex = 1;
export { age, sex } //将age变量的名称作为导出的名称，age变量的值，作为导出的值
//导出两个值，age和sex

//默认导入导出
import data from "./a.js" //将a.js模块中的默认导出放置到常量data中
import data, { a, b } from "./a.js"
export var a = 1;
export var b = 2;
export default {
    fn: function () { },
    name: "adsfaf"
}

var path = require("path");
var fs = require("fs"); //内置模块fs，文件处理var path = require("path");
var abPath = path.resolve(__dirname, "./test.txt");

var content = fs.readFileSync(abPath, {
    encoding: "utf-8"
})

console.log(content);
```
####webpack使用
安装webpack 在安装之前要```npm init```
注意设置镜像源，要不然会很慢
```npm i -D webpack webpack-cli```这样是非全局安装 ，npx webpack 打包

不同环境中执行```"scripts": {
        "dev": "webpack --mode=development",
        "build": "webpack --mode=production"
    },```

```js 
getComputedStyle(center[0]).left;
//只要读取某个元素的位置或尺寸信息，则会导致浏览器重新渲染 reflow
```
#####webpack编译结果分析
```js
//合并两个模块
//  ./src/a.js
//  ./src/index.js

(function (modules) {
    var moduleExports = {}; //用于缓存模块的导出结果

    //require函数相当于是运行一个模块，得到模块导出结果
    function __webpack_require(moduleId) { //moduleId就是模块的路径
        if (moduleExports[moduleId]) {
            //检查是否有缓存
            return moduleExports[moduleId];
        }

        var func = modules[moduleId]; //得到该模块对应的函数
        var module = {
            exports: {}
        }
        func(module, module.exports, __webpack_require); //运行模块
        var result = module.exports; //得到模块导出的结果
        moduleExports[moduleId] = result; //缓存起来
        return result;
    }

    //执行入口模块
    return __webpack_require("./src/index.js"); //require函数相当于是运行一个模块，得到模块导出结果
})({ //该对象保存了所有的模块，以及模块对应的代码
    "./src/a.js": function (module, exports) {
        eval("console.log(\"module a\")\nmodule.exports = \"a\";\n //# sourceURL=webpack:///./src/a.js")
    },
    "./src/index.js": function (module, exports, __webpack_require) {
        eval("console.log(\"index module\")\nvar a = __webpack_require(\"./src/a.js\")\na.abc();\nconsole.log(a)\n //# sourceURL=webpack:///./src/index.js")
      
    }
});
```
webpack配置文件```webpack.config.js```
配置文件是在node环境中执行的
```development```开发环境
```production```生产环境（上线，成一行）
```js
var path = require("path")

module.exports = {
    mode: "development",
    entry: {
        main: "./src/index.js", //属性名：chunk的名称， 属性值：入口模块（启动模块）
        a: ["./src/a.js", "./src/index.js"] //启动模块有两个
    },
    output: {
        path: path.resolve(__dirname, "target"), //必须配置一个绝对路径，表示资源放置的文件夹，默认是dist
        filename: "[id].[chunkhash:5].js" //配置的合并的js文件的规则
    },
    devtool: "source-map"
}
```
```js
module.exports = {
    entry: "./src/main.js",
    output: {
        filename: "bundle.js"
    }
}
```
devtool具体的配置见文档：https://www.webpackjs.com/configuration/devtool/

####loader
loader在```webpack.config.js```里的配置
```js
module: {
        rules: [
            {
                test: /index\.js$/, //正则表达式，匹配模块的路径
                use: [
                    {
                        loader:"./loaders/test-loader.js",
                        options:{
                            changeVar:"未知数"//需要用到loader-utils库
                        }
                    }
                ] //匹配到了之后，使用哪些加载器
            }, //规则1
            {
                test: /\.js$/, //正则表达式，匹配模块的路径
                use: ["./loaders/loader3", "./loaders/loader4"] //匹配到了之后，使用哪些加载器
            } //规则2
        ], //模块的匹配规则
    }
```
loaderjs代码
```js
var loaderUtils = require("loader-utils")

module.exports = function(sourceCode){
    //sourceCode : 变量 a = 1;
    sourceCode ="abc";
    console.log("test-loader运行了")
    var options = loaderUtils.getOptions(this);//这里的options就是loader配置的options
    var reg = new RegExp(options.changeVar, "g");
    return sourceCode.replace(reg, "var");
}
```
####plugin
在webpack.config.js中的配置
```js
var MyPlugin = require("./plugins/MyPlugin")

module.exports = {
    mode: "development",
    watch: true,//可监听变动，自动编译
    plugins: [
        new MyPlugin()
    ]
}
```
pluginjs代码
```js
module.exports = class MyPlugin {
    apply(compiler) {
        //在这里注册事件，类似于window.onload  $(function(){})
        compiler.hooks.done.tap("MyPlugin-done", function(compilation){
            //事件处理函数
            console.log("编译完成");
        })
    }
}
```
```js
class MyPlugin{
    apply(compiler){
        compiler.hooks.事件名称.事件类型(name, function(compilation){
            //事件处理函数
        })
    }
}
```
**事件名称**

即要监听的事件名，即钩子名，所有的钩子：https://www.webpackjs.com/api/compiler-hooks
**事件类型**

这一部分使用的是 Tapable API，这个小型的库是一个专门用于钩子函数监听的库。

它提供了一些事件类型：

- tap：注册一个同步的钩子函数，函数运行完毕则表示事件处理结束
- tapAsync：注册一个基于回调的异步的钩子函数，函数通过调用一个回调表示事件处理结束
- tapPromise：注册一个基于Promise的异步的钩子函数，函数通过返回的Promise进入已决状态表示事件处理结束

####配置环境
多写几个配置文件，然后集中在webpack.config.js里
```js
var baseConfig = require("./webpack.base")
var devConfig = require("./webpack.dev")
var proConfig = require("./webpack.pro")

module.exports = function (env) {
    if (env && env.prod) {
        return {
            ...baseConfig,
            ...proConfig
        }
    }
    else {
        return {
            ...baseConfig,
            ...devConfig
        }
    }
}
```
package.json里面
```js
"scripts": {
        "dev": "webpack",
        "prod": "webpack --env.prod"
    },
```
####其他配置
```js
var path = require("path")
module.exports = {
    mode: "development",
    devtool: "source-map",
    entry: {
        index: "./src/index.js"
    },
    // externals: {
    //     jquery: "$",
    //     lodash: "_"
    // },
    stats: {
        colors: true,
        modules: false,
        hash: false,
        builtAt: false
    }
}
```

####plugins配置
以下三个都需要安装
```js
const { CleanWebpackPlugin } = require("clean-webpack-plugin")//清空原有生成
const HtmlWebpackPlugin = require('html-webpack-plugin')//自动生成HTML
const CopyPlugin = require('copy-webpack-plugin');//复制静态资源
module.exports = {
    mode: "development",
    devtool: "source-map",
    entry: {
        home: "./src/index.js",
        a: "./src/a.js"
    },
    output: {
        filename: "scripts/[name].[chunkhash:5].js"
    },
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({//生成两个js分别引入各自的js
            template: "./public/index.html",
            filename: "home.html",
            chunks: ["home"]
        }),
        new HtmlWebpackPlugin({
            template: "./public/index.html",
            filename: "a.html",
            chunks: ["a"]
        }),
        new CopyPlugin([
            { from: "./public", to: "./" }
        ])
    ]
}
```
####开发服务器
下载webpack-dev-server库
配置文件,不需要引入，在webpack.config.js里
```js
devServer: {
        port: 8000,
        open: true,
        proxy: { //代理规则
            "/api": {
                target: "http://open.duyiedu.com",
                changeOrigin: true //更改请求头中的host和origin
            }
        }
    },
    stats: {
        modules: false,
        colors: true
    }
```
例js文件
```js
const url = `/api/student/findAll?appkey=demo13_1545210570249`;
fetch(url).then(resp => resp.json()).then(resp => {
    console.log(resp)
})
```
运行:npx webpack-dev-server
####普通文件处理
有两个库（都需要安装，webpack配置文件里不需要引入）：
file-loader和url-loader
```js
module: {
        rules: [
            {
                test: /\.(png)|(gif)|(jpg)$/,
                use: [{
                    loader: "url-loader",如果是file-loader则直接把匹配到的文件复制到下面的路径
                    options: {
                        // limit: false //不限制任何大小，所有经过loader的文件进行base64编码返回
                        limit: 10 * 1024, //只要文件不超过 100*1024 字节，则使用base64编码，否则，交给file-loader进行处理
                        name: "imgs/[name].[hash:5].[ext]"//生成的文件路径
                    }
                }]
            }
        ]
    },
```
####解决文件路径问题
```js
module.exports = {
    mode: "development",
    devtool: "source-map",
    output: {
        filename: "scripts/[name].[chunkhash:5].js",
        publicPath: "/"
    },
    module: {
        rules: [
            {
                test: /\.(png)|(gif)|(jpg)$/,
                use: [{
                    loader: "file-loader",
                    options: {
                        name: "imgs/[name].[hash:5].[ext]"
                    }
                }]
            }
        ]
    },
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            template: "./public/index.html",
            filename: "html/index.html"
        })
    ],
    devServer: {
        open: true,
        openPage: "html/index.html",
    },
    stats: {
        modules: false,
        colors: true
    }
}
```
####webpack内置插件
引入webpack
```js
const webpack = require("webpack")

module.exports = {
    mode: "development",
    devtool: "source-map",
    plugins: [
        new webpack.DefinePlugin({
            PI: `Math.PI`, // const PI = Math.PI
            VERSION: `"1.0.0"`, // VERSION = "1.0.0"
            DOMAIN: JSON.stringify("duyi.com")  // DOMAIN = "duyi.com"
        }),
        //头部添加一行注释信息
        new webpack.BannerPlugin({
            banner: `
            hash:[hash]
            chunkhash:[chunkhash]
            name:[name]
            author:yuanjin
            corporation:duyi
            `
        }),
        //自动加载模块，而不必到处 import 或 require
        new webpack.ProvidePlugin({
            $: 'jquery',
            _: 'lodash'
        })
    ]
}
```
###css工程化
下载两个库（loader在webpack配置文件里都不需要引入）：
css-loader:将css代码转换为js代码,将css代码作为字符串导出
style-loader:css-loader导出的字符串加入到页面的style元素中
style-loader只是动态把样式加入到head中，并不会直接加入html-webpack-plugin库生成的HTML页面中
```js
module: {
        rules: [
            { test: /\.css$/, use: ["style-loader", "css-loader"] },
            {
                test: /\.png$/, use: "file-loader"
            }
        ]
    }
```
####BEM
命名规范
```css
.banner__container {
    width: 520px;
    height: 280px;
    margin: 0 auto;
    outline: 1px solid;
    overflow: hidden;
    position: relative;
}

.banner__imgcontainer {
    width: 1560px;
    height: 280px;
    /* background: red; */
}

.banner__imgcontainer img {
    float: left;
    width: 520px;
    height: 280px;
}
```
####css in js
```js
export const redBg = {
    backgroundColor: "#f40",
    color: "#fff",
}

export function border(width = 2, color = "#333") {
    return {
        border: `${width}px solid ${color}`
    }
}
```
```js
export function applyStyles(dom, ...styles) {
    let targetStyles = {}; //最终合并的样式对象
    for (const style of styles) {
        targetStyles = {
            ...targetStyles,
            ...style
        }
    }

    for (const key in targetStyles) {
        const value = targetStyles[key];
        dom.style[key] = value;
    }
}
```
####css module
开启css module  webpack配置文件modules设为true
```js
module: {
        rules: [
            {
                // test: /\.css$/, use: ["style-loader", {
                //     loader: "css-loader",
                //     options: {
                //         // modules: {
                //         //     localIdentName: "[local]-[hash:5]"
                //         // }
                //         modules:true
                //     }
                // }]
                test: /\.css$/, use:["style-loader", "css-loader?modules"]
            }
        ]
    },
```
把css细分，然后当作模块导入js
```js
import style1 from  "./assets/style1.css"
import style2 from "./assets/style2.css"
console.log(style1)//为类名对应的hash值对象
const div1 = document.getElementById("div1");
div1.className = style2.c1;
```
经过`["style-loader", "css-loader?modules"]`前
```css
h1{
    color: red;
}
.paragraph{
    color:green;
}
```
经过`["style-loader", "css-loader?modules"]`后,在head中动态渲染
```css
h1{
    color: red;
}
._2OLWzgH94jonrTFvIfsj7l{
    color:green;
}
```
####less
需要下载less
```shell
npm i -D less
```
安装好了less之后，它提供了一个CLI工具`lessc`，通过该工具即可完成编译
在那个文件夹里面`npx lessc lby.less lby.css `
在webpack中的应用 下载less-loader
```js
module: {
        rules: [
            { test: /\.css$/, use: ["style-loader", "css-loader"] },
            //下面这个在js中可直接引入less 
            { test: /\.less$/, use: ["style-loader", "css-loader?modules", "less-loader"] },
        ]
    },
```
具体的使用见文档：https://less.bootcss.com/
####postcss
与webpack类似下载`npm i -D postcss postcss-cli`
转换代码`npx postcss ./css/source.pcss -o ./css/lby.css`
postcss.config.js文件
```js
module.exports = {
    map: false, //关闭source-map
    plugins: {
        "postcss-preset-env": {
            stage: 0, //哪怕是处于草案阶段的语法，也需要转换
            preserve: false
        },
        "postcss-apply": {},
        "postcss-color-function": {}
    }
}
```
package.json文件
```js
"devDependencies": {
        "postcss": "^7.0.26",
        "postcss-apply": "^0.12.0",
        "postcss-cli": "^7.1.0",
        "postcss-color-function": "^4.1.0",
        "postcss-preset-env": "^6.7.0",
        "stylelint": "^13.0.0",
        "stylelint-config-standard": "^19.0.0"
    }
```
postcss在webpack中的使用，下载postcss-loader
`test: /\.pcss$/, use: ["style-loader", "css-loader?modules", "postcss-loader"]`
抽离css文件
下载库`mini-css-extract-plugin`
output.publicPath是把所有的引入前面都加它的值，包括css里面引入的图片等
```js
const HtmlWebpackPlugin = require("html-webpack-plugin")
var { CleanWebpackPlugin } = require("clean-webpack-plugin")
const MiniCssExtractPlugin = require("mini-css-extract-plugin")
module.exports = {
    mode: "development",
    entry: {
        main: "./src/index.js",
        // other: "./src/other.js"
    },
    output: {
        filename: "js/[name].[chunkhash:5].js",
        publicPath: "./"//所有的引入前面都加./
    },
    module: {
        rules: [
            {
                test: /\.css$/, use: [MiniCssExtractPlugin.loader, "css-loader?modules"]
            },
            {
                test: /\.jpg$/, use: {
                    loader: "file-loader",
                    options: {
                        name: "img/[hash:5].[ext]"
                    }
                }
            }
        ]
    },
    devServer: {
        open: true
    },
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            template: "./public/index.html"
        }),
        new MiniCssExtractPlugin({
            filename: "css/[name].[contenthash:5].css"
        })
    ]
}
```
###babel
几乎和postcss一样
需要下载
```shell
npm i -D @babel/core @babel/cli
```
转换js文件`npx babel a.js -o lby.js`
转换文件夹`npx babel js -d lby`
没有配置babel没啥用,其配置文件为`.babel`
babel预设，安装`npm i @babel/preset-env`
`.babelrc`文件配置babel
```js
{
    "presets": [//babel预设
        ["@babel/preset-env", {
            "useBuiltIns": "usage",
            "corejs": 3
        }]
    ],
    "plugins": [//babel插件
        ["@babel/proposal-class-properties", {
            "loose": true
        }],
        "@babel/proposal-function-bind",
        "@babel/proposal-optional-chaining",
        "transform-remove-console",//移除console
        "@babel/transform-runtime"
    ]
}
```
package.json中的配置
```js
"devDependencies": {
    "@babel/cli": "^7.8.4",
    "@babel/core": "^7.8.4",
    "@babel/plugin-proposal-class-properties": "^7.8.3",
    "@babel/plugin-proposal-function-bind": "^7.8.3",
    "@babel/plugin-proposal-optional-chaining": "^7.8.3",
    "@babel/plugin-transform-runtime": "^7.8.3",
    "@babel/preset-env": "^7.8.4",
    "babel-plugin-transform-remove-console": "^6.9.4"
  },
  "dependencies": {
    "@babel/runtime": "^7.8.4",
    "core-js": "^3.6.4",
    "regenerator-runtime": "^0.13.3"
  }
```
> 具体的配置见：https://www.babeljs.cn/docs/babel-preset-env#options

如果在webpack中应用babel,下载babel-loader
在webpack.config.js中的配置
```js
module: {
        rules: [
            { test: /\.js$/, use: "babel-loader" }
        ]
    }
```
###性能优化
- 减少模块解析
在webpack.config.js里面配置
```js
module: {
        noParse: /test/  //正则，匹配到的不解析
        //就是直接把匹配到的js文件放到函数里
        //如果匹配的文件内部有引入，则需要手动改动
    }
```
- 优化loader性能
```js
module: {
    rules: [
      {
        test: /\.js$/,
        use: [
          {
            loader: "cache-loader",//缓存loader结果
            options:{
              cacheDirectory: "./cache"//缓存的文件夹
            }
          },
          "thread-loader",//开启多线程
          "babel-loader"
        ]
      }
    ]
  }
```
- 热替换
- 手动打包
package.json里
```js
 "scripts": {
    "dev": "webpack",
    "dll": "webpack --config webpack.dll.config.js"
  },
```
配置一个webpack.dll.config.js的文件
```js
const webpack = require("webpack");
const path = require("path");

module.exports = {
  mode: "development",
  entry: {
    jquery: ["jquery"],
    lodash: ["lodash"]
  },
  output: {
    filename: "dll/[name].js",
    library: "[name]" // 每个bundle暴露的全局变量名
  },
  plugins: [
    new webpack.DllPlugin({//生成资源清单
      path: path.resolve(__dirname, "dll", "[name].manifest.json"),
      //dll是文件夹，后面的是资源清单文件
      name: "[name]"//资源清单中，暴露的变量名
    })
  ]
};
```
先用这个文件打包`npm run dll`
在模板html文件中手动引入这两个公共资源库
在配置一个webpack.config.js文件
```js
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const webpack = require("webpack");

module.exports = {
  mode: "development",
  // devtool: "source-map",
  entry: {
    main: "./src/index.js",
    other: "./src/other.js"
  },
  output: {
    filename: "[name].[hash:5].js"
  },
  plugins: [
    new CleanWebpackPlugin({
      // 要清除的文件或目录
      // 排除掉dll目录本身和它里面的文件
      cleanOnceBeforeBuildPatterns: ["**/*", "!dll", "!dll/*"]
    }),
    new HtmlWebpackPlugin({
      template: "./public/index.html"
    }),
    //引入两个资源清单
    //在打包的时候就不会解析这两个库，节约时间
    new webpack.DllReferencePlugin({
      manifest: require("./dll/jquery.manifest.json")
    }),
    new webpack.DllReferencePlugin({
      manifest: require("./dll/lodash.manifest.json")
    })
  ]
};

```
然后`npm run dev`进行打包
- 自动打包
webpack配置文件
```js
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const HtmlWebpackPlugin = require("html-webpack-plugin")

module.exports = {
  mode: "production",
  entry: {
    page1: "./src/page1",
    page2: "./src/page2"
  },
  output: {
    filename: "[name].[hash:5].js"
  },
  optimization: {
    splitChunks: {
      //分包配置
      chunks: "all",
      // maxSize: 60000
      // automaticNameDelimiter: ".",
      // minChunks: 1,
      // minSize: 0
      cacheGroups: {
          //默认会抽离相同的js模块
        styles: {
          minSize: 0,
          test: /\.css$/,
          //匹配css文件，把多页面相同的引入模块抽离出来，形成一个新chunk
          //默认会抽离
          minChunks: 2
        }
      }
    }
  },
  module: {
    rules: [
      { test: /\.css$/, use: [MiniCssExtractPlugin.loader, "css-loader"] }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(),
    new MiniCssExtractPlugin({
      filename: "[name].[hash:5].css",
      chunkFilename: "common.[hash:5].css"
      // chunkFilename是配置来自于分割chunk的文件名
    }),
    new HtmlWebpackPlugin({
      template:"./public/index.html",
      chunks: ["page1"],
      filename: "html/index1.html"
    }),
    new HtmlWebpackPlugin({
      template:"./public/index.html",
      chunks: ["page2"],
      filename: "html/index2.html"
    })
  ],
  stats: {
    colors: true,
    chunks: false,
    modules: false
  }
};
```
- 代码压缩
terser支持es6,目前比较流行
webpack配置
```js
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const TerserPlugin = require("terser-webpack-plugin")//下载,简化js
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');//下载,简化css
module.exports = {
  mode: "development",
  module: {
    rules: [
      { test: /\.css$/, use: [MiniCssExtractPlugin.loader, "css-loader"] }
    ]
  },
  optimization: {
    // 是否要启用压缩，默认情况下，生产环境会自动开启
    minimize: true,
    minimizer: [
      // 压缩时使用的插件，可以有多个
      new TerserPlugin(),
      new OptimizeCSSAssetsPlugin()
    ]
  },
  plugins: [
    new CleanWebpackPlugin(),
    new MiniCssExtractPlugin({
      filename: "[name].[hash:5].css"
    }),
    new HtmlWebpackPlugin()
  ],
  stats: {
    colors: true,
    chunks: false,
    modules: false
  }
};
```
- tree shaking
删除用不到的代码
webpack配置
```js
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const DeepScope = require("webpack-deep-scope-plugin").default;
const MiniCss = require("mini-css-extract-plugin");
const Purgecss = require("purgecss-webpack-plugin");
const path = require("path");
const globAll = require("glob-all");
const srcAbs = path.resolve(__dirname, "src"); //得到src的绝对路径
const htmlPath = path.resolve(__dirname, "public/index.html");
const paths = globAll.sync([`${srcAbs}**/*.js`, htmlPath]);
//paths:得到一个文件路径数组，服务于purgecss-webpack-plugin

module.exports = {
  mode: "production",
  module: {
    rules: [{ test: /\.css$/, use: [MiniCss.loader, "css-loader"] }]
  },
  plugins: [
    new CleanWebpackPlugin(),
    new DeepScope(),
    new MiniCss(),
    new Purgecss({
      paths
    })
  ]
};
```
- 懒加载
```js
const btn = document.querySelector("button");
btn.onclick = async function() {
  //动态加载
  //import 是ES6的草案
  //浏览器会使用JSOP的方式远程去读取一个js模块
  //import()会返回一个promise   （* as obj）
  // const { chunk } = await import(/* webpackChunkName:"lodash" */"lodash-es");
  const { chunk } = await import("./util");
  const result = chunk([3, 5, 6, 7, 87], 2);
  console.log(result);
};
```
- eslink
下载eslink库`npm i -g eslink`
.eslinkrc.json配置文件
```js
{
  "env": {
    "browser": true,
    "es6": true
  },
  "extends": "eslint:recommended",
  "parserOptions": {
    "ecmaVersion": 2018,
    "sourceType": "module"
  },
  "rules": {
    "curly": "off"
  }
}

```
- bundle analyzer
自动出现一个页面，可以查看打包前，打包后，压缩后的代码比例
需要下载webpack-bundle-analyzer库
```js
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const WebpackBundleAnalyzer = require("webpack-bundle-analyzer")
  .BundleAnalyzerPlugin;

module.exports = {
  mode: "production",
  optimization: {
    splitChunks: {
      chunks: "all"
    }
  },
  plugins: [new CleanWebpackPlugin(), new WebpackBundleAnalyzer()]
};
```
- gzip
使用`compression-webpack-plugin`插件对打包结果进行预压缩，可以移除服务器的压缩时间
webpack配置文件
```js
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const CmpressionWebpackPlugin = require("compression-webpack-plugin")
module.exports = {
  mode: "production",
  optimization: {
    splitChunks: {
      chunks: "all"
    }
  },
  plugins: [
    new CleanWebpackPlugin(),
    new CmpressionWebpackPlugin({
      test: /\.js/,
      minRatio: 0.5//压缩比率 ：压缩后的大小/压缩前的大小>0.5就压缩
    })
  ]
};
```



