# webpack的学习

webpack：是一种前端资源构建工具，一个静态模态打包器

静态模块一般为 js jq less sass等将这些当作一个模块来处理，进行打包生成对应的静态资源

```bash
npm init -y
npm install webpack webpack-cli --save-dev
```

## webpack五个核心概念

### entry

入口，webpack以哪个文件为入口起点开始打包

### output

打包后输出到那里去

### loader

webpack只能处理jsjqless等，图片这种处理不了这时候就要loader来处理非JavaScript文件

### plugins

插件可以用于执行更加广泛的任务，插件的范围包括，从打包优化和压缩，一直到定义环境中的变量

### model

developement开发模式：能让代码本地调试

production：能让代码优化上线运行的环境

```js
/**
 * webpack.config.js webpack 的配置文件
作用 只是webpack干那些活（当你运行webpack指令时候，会加载里面的配置）

所有构建工具都市居于nodejs平台运行的 模块化采用commonjs

loader:1、下载 2、使用（配置loader）
plugins 1、下载 2、映入 3、使用
 */

// resolve用来拼接绝对路径的方法
const {resolve} = require('path')
// 映入插件
const HtmlWebpackPlugin  = require('html-webpack-plugin')
module.exports = {
  //webpack配置
  //入口起点
  entry: './src/index.js',
  //输出
  output: {
    filename: 'built.js',
    // 输出路径
    path: resolve(__dirname,'build')
  },
  // loader的配置
  module: {
    rules:[
      //详细loader配置
      // 不同文件要配置不同loader
      {
        //匹配哪些文件
        test: /\.css$/,
        // 使用哪些loader
        use: [//use数组中loader执行顺序，从右到左，从上到下，依次执行
          // 创建一个style标签，将js中的css样式资源插入进行，添加到head中生效
          'style-loader',
          //将css文件以字符串的形成  变成一个commonjs模块加载到js中，
          'css-loader'
        ]
      },
      {
        //匹配哪些文件
        test: /\.less$/,
        // 使用哪些loader
        use: [//use数组中loader执行顺序，从右到左，从上到下，依次执行
          // 创建一个style标签，将js中的css样式资源插入进行，添加到head中生效
          'style-loader',
          //将css文件以字符串的形成  变成一个commonjs模块加载到js中，
          'css-loader',
          'less-loader'
        ]
      }，
        // 打包其他资源(除了html/js/css资源以外的资源) 图片资源等
      {
        // 排除css/js/html资源
        exclude: /\.(css|js|html|less)$/,
        loader: 'file-loader',
        options: {
          name: '[hash:10].[ext]' //控制文本名字，控制hash值的大小
        }
      },
      {
    	test: /\.(jpg|png|gif)$/,
    	loader: 'url-loader',
    	options: {
            limit: 8*1024,
            name: '[hash:10].[ext]',
            esModule: false
        }
	  },
    {
        // 处理其他资源
        exclude: /\.(html|js|css|less|jpg|png|gif)/,
        loader: 'file-loader',
        options: {
          name: '[hash:10].[ext]',
          outputPath: 'media'
        }
      }

    ]
  },
  //plugins的配置
  plugins: [
    //html-webpack-plugin:  会常见一个html文件，引入打包输出所有的资源（js/css）
    // 需求： 需要有结构的html文件
    new HtmlWebpackPlugin({
      // 创建一个html文件
      // 复制'./src/index.js'
      template: './src/index.html'
    })
  ],
  //模式
  mode: 'development',
  // mode: 'production'
      // 开发服务器 devServer：用来自动化（自动编译，自动打开浏览器，自动刷新浏览器~~）
  // 特点：只会在内存中编译打包，不会有任何输出
  // 启动devServer指令为：npx webpack-dev-server
  devServer: {
    // 项目构建后路径
    // contentBase: resolve(__dirname, 'build'),  //不知道为什么我这里这个contentBase使用不了，找不到这个标签
    // 启动gzip压缩
    compress: true,
    // 端口号
    port: 3000,
    // 自动打开浏览器
    open: true
  }
}

```

