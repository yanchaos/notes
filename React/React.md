# React.js
## 1.创建基本的webpack4.x项目
1.运行```npm init -y```快速初始化项目。
2.项目根目录下创建```src```源代码目录和```dist```产品目录。
3.在```src```目录下创建index.html,index.js.
4.使用cnpm安装webpack```cnpm install webpack webpack-cli -D```,在根目录下创建```webpack.config.js```,进行初始化配置。

```javascript
//向外暴露一个打包的配置对象，webpack是基于node构建的，支持所有node的api和语法
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
* 全局安装cnpm运行```npm install cnpm -g ```

5.webpack4.x提供了约定大于配置的概念，目的是尽量减少webpack配置文件的体积：
* 默认约定了：
* 打包入口是```src ```-> ```index.js```
* 打包的输出文件是：```dist``` -> ```main.js```

6.webpack4.x中新增了mode选项(此选项为必选项)，可选值为```development```和```production```;
7.在webpack中不能使用```export default{} //这是ES6中向外导出的模块```

## 2.在项目中使用react
1.运行```cnpm install react react-dom -S``` 安装包；
* react: 专门用来创建组件，虚拟DOM元素的，同时组件的生命周期也在这个包中
* react-dom：专门进行DOM操作的，最主要的应用场景就是```ReactDOM.render()```
2.在```index.html```中创建容器
```html
<!-- 容器 将来使用React创建的DOM元素会被渲染到这个指定的容器中 -->
<div id="app"></div>
```
3.在```index.js```中导入包。
```javascript
import React from 'react'
import ReactDOM from 'react-dom'
```
4.创建一个虚拟DOM元素。
```javascript
/**
 * 参数1：是创建虚拟DOM的元素名称；
 * 参数2：是一个对象或null，表示当前DOM的属性；
 * 参数3：子节点,包括其他虚拟DOM或文本子节点
 * 参数n：其他子节点
 * */
const myh1 = React.createElement('h1', {
    id: 'myh1',
    title:'this is h1'
}, '这是一个大大的h1')
```
5.渲染虚拟DOM到页面上。
```javascript
/**
 * 参数1：要渲染的那个虚拟dom
 * 参数2：指定页面上一个容器(DOM元素)
 */
ReactDOM.render(myh1, document.getElementById('app'))
```
## 3.jsx语法
-----
>什么是jsx语法:就是符合xml规范的js语法

1.如何启用jsx语法？
* 安装```babel```插件:
  * 运行```cnpm install babel-core babel-loader babel-plugin-transform-runtime -D```
  * 运行```cnpm install babel-preset-env babel-preset-stage-0 -D```
* 安装能够识别转换jsx语法的包```babel-preset-react```:
  * 运行```cnpm install babel-preset-react -D```
* 添加```.babelrc```配置文件
```json
{
    "presets": ["env", "stage-0", "react"],
    "plugins": ["transform-runtime"]
}
```
* 在webpack.config.js中添加```babel-loader```配置项：
```js
//webpack默认只能打包处理.js后缀文件，不能主动处理第三方模块，所以要配置第三方loader
module: {    //所有第三方模块的配置规则
    rules: [
        { test: /\.js|jsx$/, use: 'babel-loader', exclude: /node_modules/ }, //千万别忘记添加exclude排除项
    ]
}
```
>编译时出现```Module build failed: Error: Cannot find module @babel/core```是因为```babel-loader```版本不匹配导致，可以通过一线两种方式解决：
> * 降版本：运行如下命令将 babel-loade 版本降低到 7.x 版本就可以了。
> ```cnpm i babel-loader@7.1.5 -D```
> * 升级版本,参考[babel-core版本升级](https://blog.quietguoguo.com/4013.html)
>
### 注意事项
* jsx绑定数据使用```{}```包裹变量，例如:```id={mydiv}```；
* 当需要在jsx控制的区域内写js表达式，则需要把js代码写在{}内；
* jsx绑定css样式，需要将```class```替换为```className```,例如：
```jsx
<input type="text" className="input-text-demo" />
```
* label标签的for属性需要使用htmlFor替换，例如：
```jsx
<label htmlFor={mydiv}>用户名：</label><input id={mydiv} type="text" ></input>
```
## 4.组件
---
### 第一种创建组件的方式
> 使用构造函数来创建，如果需要传递参数，需要在构造函数的参数列表中使用props来接收；
> 必须向外return一个合法的jsx创建的虚拟DOM；
```jsx
function Hello(props){
    // 如果在组件中return null，则表示此组件什么都不渲染
    // return null
    // 在组件中必须返回一个合法的虚拟dom元素
    return <div><span>这是 hello组件 {props.user.name}</span></div>
}
const dog = {
    name:'大黄',
    age:3,
    gender:'公的'
}
ReactDOM.render(<div>
    <Hello user={dog}></Hello>
</div>, document.getElementById('app'))
 ```
*  父组件可以通过props向子组件传值
* 可以使用{...obj}属性扩散传递参数，例如：
```jsx
<Hello {...dog}></Hello>
```
* 将```Hello```组件封装到单独的jsx文件中，组件名首字母必须大写，例如：
 ```jsx
 import React from 'react'

// 第一种创建组件的方式
export default function Hello(props){
    // 如果在组件中return null，则表示此组件什么都不渲染
    // return null
    // 在组件中必须返回一个合法的虚拟dom元素
    return <div><span>这是 hello组件 {props.name}</span></div>
}
 ```
* 配置webpack以省略.jsx后缀名，在webpack.config.js导出的配置对象中添加```resolve```节点：
```js
//...

module.exports = {
    //...
    resolve: {
        extensions: ['.js', '.jsx', '.json'] //表示这几种文件类型的后缀可以不写
    }
}
```
### 第二种创建组件的方式
> 使用class关键字创建组件
> ES6 中class关键字是使用面向对象的新形式；
1.最基本的结构：
```jsx
//组件名称首字母必须大写，且继承自React.Component
class 组件名称 extends React.Component{
  //在组件中必须有 render 函数，返回一个合法的jsx虚拟DOM结构
  render(){
    return <div>这是组件创建的组件</div>
  }
}
```
class关键字创建的组件中使用外部通过props传递的参数，不需要接收，直接通过```this.props.参数名称```,在class组件内部，```this```表示当前组件的实例对象。例如：
```jsx
import React from 'react';
export default class Movies extends React.Component {
    render() {
        return <div>这是{this.props.name}的Movie组件</div>
    }
}
```
### 两种创建组件方式的对比
> 注意：用```class```关键字创建的组件，有自己的私有数据和生命周期函数；用```function```方式创建的组件只有props，没有自己的私有数据和声明周期函数；
* 1.使用```构造函数```创建的组件，叫做“无状态组件”；
* 2.使用```class```关键字创建的组件，叫做“有状态组件”；
> 有状态组件和无状态组件的**本质区别**是：有无state属性和有无生命周期函数。
> state中的数据是可读可写的。
### 普通样式表通过modules参数启用模块化
> 目的：解决普通css样式表作用域冲突问题。
* 1.在```css-loader```之后通过？追加参数，其中固定参数modules表示为普通的css样式表启用模块化，修改```webpack.config.js```,为```css-loader```添加参数，如下所示：
```js
module.exports = {
    //...
    module: {    //所有第三方模块的配置规则
        rules: [
        //...
            { test: /\.css$/, use: ['style-loader', 'css-loader?modules']}, //打包处理css的第三方loader
        ]
    }
    //...

}
```
> css模块化只针对 类选择器 和 id选择器生效，不会将普通标签选择器模块化.
* 2.在需要的组件中import导入样式表，并接收模块化的样式对象：
```jsx
import cssobj from '@/css/cmtlist.css';
```
* 3.在需要的html标签上使用```className```指定模块化的样式：
```jsx
<h3 className={cssobj.user}>评论人：{this.props.user}</h3>
```
