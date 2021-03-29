
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
