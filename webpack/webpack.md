# webpack配置
## 1.创建基本的webpack4.x项目
1.运行```npm init -y```快速初始化项目。
2.项目根目录下创建```src```源代码目录和```dist```产品目录。

3.在```src```目录下创建index.html,index.js.

4.使用cnpm安装webpack```cnpm install webpack webpack-cli -D```,在根目录下创建```webpack.config.js```,进行初始化配置。

* 全局安装cnpm运行```npm install cnpm -g ```

5.webpack4.x提供了约定大于配置的概念，目的是尽量减少webpack配置文件的体积：
* 默认约定了：
* 打包入口是```src ```-> ```index.js```
* 打包的输出文件是：```dist``` -> ```main.js```

6.webpack4.x中新增了mode选项(此选项为必选项)，可选值为```development```和```production```;
```javascript
//向外暴露一个打包的配置对象，webpack是基于node构建的，支持所有node的api和语法
module.exports = {
    mode: "development",    // development  production
}
```
7.在webpack中不能使用```export default{} //这是ES6中向外导出的模块```
8.配置```html-webpack-plugin```插件，将index.html放到内存中，避免重复的硬盘读写和main.js手动引用；
```js
/向外暴露一个打包的配置对象，webpack是基于node构建的，支持所有node的api和语法
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

//创建一个plugin的实例对象
const htmlPlugin = new HtmlWebpackPlugin({
    template: path.join(__dirname, './src/index.html'),
    filename: 'index.html'
})

module.exports = {
    mode: "development",    // development  production
    //在webpack4.x中，有一个很大的特性，就是‘约定大于配置’，默认的打包入口文件时src->index.js
    plugins: [
        htmlPlugin
    ],
}
```
9.使用```webpack-dev-server```进行热部署
```
cnpm install webpack-dev-server -D
```
在```package.json```文件的```script```节点配置启动命令：
```json
{
  //...
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack",
    "watch": "webpack --watch",
    // --open立即使用浏览器打开 --port监听端口 --hot热部署
    "dev": "webpack serve --open --port 3000 --hot --host 127.0.0.1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^7.1.5",
    "babel-plugin-transform-runtime": "^6.23.0",
    "babel-preset-env": "^1.7.0",
    "babel-preset-react": "^6.24.1",
    "babel-preset-stage-0": "^6.24.1",
    "html-webpack-plugin": "^5.3.1",
    "webpack": "^5.36.0",
    "webpack-cli": "^4.6.0",
    "webpack-dev-server": "^3.11.2"
  },
}

```
10.配置webpack以省略.jsx等后缀名，在webpack.config.js导出的配置对象中添加```resolve```节点：
```js
//...

module.exports = {
    //...
    resolve: {
        extensions: ['.js', '.jsx', '.json'] //表示这几种文件类型的后缀可以不写
    }
}
```
11.配置webpack设置根目录；
```js
module.exports = {
    //...
    resolve: {
        extensions: ['.js', '.jsx', '.json'],//表示这几种文件类型的后缀可以不写
        alias: {//别名
          '@': path.json(__dirname, './src')  //这样@就表示项目根目录中的src这一层路径
        }
    }

}
```
