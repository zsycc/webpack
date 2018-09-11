# webpack
学习webpack(版本 3.5.6)
webpack.config.js文件module.exports = {}
1.什么是webpack
webpack => 模块打包机 => exports / module.exports
它可以做什么事情呢,分析你的项目结构,.找到JavaScript模块以及其他一些浏览器不能直接运行的扩展语言
例如(less),并将其转换和打包为合适的格式供浏览器使用
2.为什么使用webpack
把js,css,img 等(模块)打包,减少请求
 3.安装webpack
(1)通过npm去下载webpack,第一步该用什么命令:npm init => package.json
(2)安装webpack===>全局安装 (不推荐 => 原因 => 框架更新太快)
   npm i(install) -g webpack(版本号) 3.5.6
   局部安装 (推荐) npm i --save-dev webpack@(版本号) 3.5.6(--save-dev开发的模式去使用)
4.用终端命令实现webpack打包(webpack模块) (入口) (出口)
(1)node_modules/.bin/webpack app/main.js public/build_main.js  -- hash(散列函数)
 build_main.js 合并的js名
 写法:在HTML src引入build_main.js
(2)用终端操作终归不好管理,那么webpack提供了一个配置文件,名字叫做webpack.config.js(规定)
    然后可以直接使用node_modules/.bin/webpack进行打包 = > 他会搜索是否存在webpack.config.js
webpack.config.js://唯一的入口文件   entry:__dirname + '文件路径',
                  //打包之后的文件该输出到哪个位置
                  output: {
                      path: __dirname + "输出位置",
                      filename: "合并的js名"
                  }
(3) 在package.json里面的script这个对象是一个自定义指令,我们可以简化命令实现打包 "start":"webpack",
     用npm start进行打包
     key值是有限制的,需要是关键字:key:(access, adduser, bin, bugs, c, cache, completion, config,
    ddp, dedupe, deprecate, dist-tag, docs, doctor, edit,
    explore, get, help, help-search, i, init, install,
    install-test, it, link, list, ln, login, logout, ls,
    outdated, owner, pack, ping, prefix, profile, prune,
    publish, rb, rebuild, repo, restart, root, run, run-script,
    s, se, search, set, shrinkwrap, star, stars, start, stop, t,
    team, test, token, tst, un, uninstall, unpublish, unstar,
    up, update, v, version, view, whoami)
    如果不是关键字,用npm run 自定义指令进行打包
5.webpackd的功能
(1)调试更简单(可以打断点):source maps :打包之后的文件如果配置了 source maps 可以卡到以来的模块代码 ==>在哪配置
写法: 在webpack.config.js里  devtool:'eval-source-map'  偏中小型的一种
注意:使用eval打包源文件模块,在同一个文件重生成干净的完整的source map,这个选项可以在不影响
    构建速度的前提下生成完整的source map,但是对打包后输出的JS文件的执行具有性能和安全的隐患
    在开发阶段这是一个非常好的选项,在生产阶段则一定不要启用这个选项
    对小到中型的项目中,eval-source-map是一个非常好的选项，
    再次强调你只应该开发阶段使用它，我们继续对上文新建的webpack.config.js，进行如下配置
    代码热更新 => 创建自己的开发测试服务器
(2)提供一个本地开发服务器:这个服务器是基于node.js, 不过这个模块需要 下载并且依赖devServer
  下载:npm i --save-dev webpack-dev-server@版本号  => 3.0版本 => webpack 4.0服务的
    npm i --save-dev webpack-dev-server@2.9.5  => 2.0版本 => webpack 3.0服务的
    不加版本号,下载的是最新版本
   在package.json里面的script这个对象是一个自定义指令,加上"server": "webpack-dev-server --open",直接用npm run server
  写法:在webpack.config.js: //devServer 2.9.5 启动自带热更新
    //并且他会监测 所依赖的模块是否修改
    //我们update code => 状态触发 => 重新打包 => 触发刷新
    devServer:{
        // 本地服务器 所加载的页面所在的目录
        contentBase:'./public',
        // 服务器端口 => 默认端口 8080
        port:'222'
        ......
    }
(3)---loaders(JS打包):通过使用不同的loader,,webpack 有能力调用外部的工具和模块,实现对不同格式的文件的处理,比如说es6 =>
    es5;转化成浏览器兼容的js文件 ====> 因为babel 一直就是干这个的,不能抢人家饭碗 ===> 下载babel的处理模块包
    babel的安装和配置
    babel的三个核心模块:babel-loader ====>让webpack知道babel是如何运行
                babel-core  =====> 让babel知道如何解析代码
                babel-preset-env ====> 可以根据不同的环境转换代码
    安装:cnpm/npm i --save-dev babel-loader@7.1.1 babel-core babel-preset-env
    babel-loader ====>  es6进行转义 ====> (前端的export与export default规范)
    注意:HTML引入合并的JS文件要写在定义Vue组件前
    写法:在webpack.config.js: module:{
        rules:[
            {
                //必须要有 一个用以匹配loaders所处理文件的扩展名的
                // 正则表达式(查找所有JS文件)
                test:/\.js$/,
                //后面配置的是loader的名称
                use:'babel-loader',
                //include / exclude 必须处理的文件和需要并逼得文件
                exclude:'/node_modules'
            }
        ]
    }
(4)对css进行打包!!!
  下载:cnpm i --save-dev style-loader css-loader@0.28.11
  写法:在webpack.config.js: module:{
        rules:[
            //安装style-loader 与 css-loader
            {
                test:/\.css$/,
                //配置多个loader时候 use => value 是数组
                //如果没有额外配置 直接string
                //如果有额外配置 为json对象
                use:[
                    'style-loader',
                    {
                        loader:'css-loader',
                        options:{
                            //把css当成 模块 => json对象来看待
                            modules:true,
                            localIdentName:'[name]__[local]--[hash:base64:5]'
                        }
                    }
                ]
            }
        ]
    }
在需要打包的JS文件里接收  import 自定义名字 from '文件路径'
                        在data里返回:data(){
                                           return{
                                              zsy_css:自定义名字
                                          }
                                      }
                                  })
                         在自定义组件::class="自定义名字.wrap"
(5)图片打包
  下载:下载:cnpm i --save-dev file-loader url-loader
  写法:module:{
        rules:[
            //打包图片 file-loader/url-loader
            {
                test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
                use: [
                    {
                        loader: 'url-loader',
                        // 配置 url-loader 的可选项
                        options: {
                            // 限制 图片大小 10000B，小于限制会将图片转换为
                            // base64格式
                            limit: 10000,
                            // 超出限制，创建的文件格式
                            // build/images/[图片名].[hash].[图片格式]
                            name: 'images/[name].[hash].[ext]'
                        }
                    }
                ]
            }
        ]
    }
    在需要打包的JS文件里接收 let 变量名 = require('图片路径');
    在data里返回:data(){
                                           return{
                                              big:变量名
                                          }
                                      }
                                  })
    在自定义组件::src="big"
(6)插件
注意:插件需要用require引入,用的是webpack自带插件
1.版权所有，翻版必究
安装:cnpm install --save-dev html-webpack-plugin  
引入:let webpack = require('webpack');
写法:plugins:[
 new webpack.BannerPlugin('版权所有，翻版必究')
]
2.HtmlWebpackPlugin:依据一个简单的index.html模板，生成一个自动引用你打包后的JS文件的新index.html。
 安装:npm install --save-dev html-webpack-plugin
 引入:const HtmlWebpackPlugin = require('html-webpack-plugin');
 写法:new HtmlWebpackPlugin({
            template: __dirname + "文件路径"
        })
 3.变化格式类型
  安装:cnpm i --save-dev uglify-js-plugin
  引入:let UglifyJsPlugin = require('uglify-js-plugin');
  写法:new webpack.optimize.UglifyJsPlugin()
4.去除build文件中的残余文件
 安装:cnpm install clean-webpack-plugin --save-dev
 引入:const CleanWebpackPlugin = require("clean-webpack-plugin");
 写法:new CleanWebpackPlugin('build/*.*', {
            // 绝对路径
            root: __dirname,
            //将日志写入控制台。
            verbose: true,
            ////使用布尔“true”来测试/模拟删除。 （不会删除文件）。
            //默认值：false - 删除文件
            dry: false
        })
5.JS.css代码分离
 安装:cnpm install --save-dev extract-text-webpack-plugin
 引入:const ExtractTextPlugin = require('extract-text-webpack-plugin');
 写法:new ExtractTextPlugin("style.css")
 6.缓存:filename: "bundle-[hash].js"
  在文件名最后加上[hash]散列函数
"devDependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^7.1.1",
    "babel-preset-env": "^1.7.0",
    "babel-preset-es2015": "^6.24.1",
    "clean-webpack-plugin": "^0.1.19",
    "css-loader": "^1.0.0",
    "extract-text-webpack-plugin": "^3.0.2",
    "file-loader": "^2.0.0",
    "html-webpack-plugin": "^3.2.0",
    "style-loader": "^0.23.0",
    "uglify-js-plugin": "^0.0.6",
    "url-loader": "^1.1.1",
    "webpack": "^3.5.6"
  }
