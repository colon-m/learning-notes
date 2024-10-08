# node.js是什么

运行在chrome v8引擎上的运行环境，执行JS代码。

chrome浏览器还需要解析、渲染HTML、CSS等相关渲染引擎，另外还需要提供支持浏览器操作的API、浏览器自己的事件循环等

node.js也需要进行一些额外操作，比如文件系统的读/写，网络IO、加密、压缩解压文件等操作

我们编写的JS代码会经过V8引擎，再通过Node.js的Bindings，将任务放到Libuv的事件循环中；libuv是使用c语言编写的库；libuv提供了事件循环、文件系统读写、网络IO、线程池等等内容

想要切换不同版本的nodejs，使用n/nvm可以实现，但不支持windows，nvm-windows支持（github上）

```
nvm install latest 安装最新node版本
nvm list 展示目前安装的所有版本
nvm use 切换版本
```

# 输入

使用nodejs执行js文件时，后面可以跟参数，该参数被放在全局对象process的内置对象中。process里面含有其他信息，如版本、操作系统等等。

```
process.argv //传入的具体参数
process.argc //传入的参数个数
```

# 输出

console.log()

console.trace() //打印调用栈

# REPL

Read-Eval-Print Loop，翻译"读取-求值-输出 循环"

REPL是一个简单的、交互的编程环境

# 特殊的全局对象

其实是模块中的变量，只是每个模块都有

```
__dirname 当前文件所在的目录结构
__filename 当前目录结构和文件名称
```

## global和window区别

两者都是全局对象，但是在浏览器中执行var定义的属性，默认会添加到window对象上，而node中不会

# 模块化(CommonJS规范)

a.js

```js
let name = "l"
const hello = function () {
  console.log("hello")
}

exports.name = name
exports.hello = hello
```

hello.js

```js
let {
  name, hello
} = require("./a")

console.log(name)
hello()
```

exports本质是一个对象，可通过另一个文件修改属性值

但是实际上用的时候用的是module.exports

```js
let name = "l"
const hello = function () {
  console.log("hello")
}

module.exportS = {
    name,
    hello
}
```

此时module.exports创建了一个新对象，与exports指向不同对象

require：

参数首先判断是不是核心模块；
再者是路径，路径若没加后置，则先按照.js.json.node顺序查找该名字的文件，若没有，把名字当作目录文件夹，在该目录下查找名为index的文件；
然后是名称，会从当前文件夹向上寻找node modules的文件夹，查看里面是否有对应名字的文件下，有的话就到文件夹下面的index.js里面引用



模块被引入时，会执行一次，而再次被引入时不会执行。原因，每个模块都会被抽象成一个module对象，module.loaded默认为false，第一次引入后，值为true。

若有多层引入，执行顺序遵循深度优先算法

CommonJS是导入同步操作，意味着后面的代码需要等待前面的代码执行完成，对于浏览器不使用。

AMD异步模块定义（淘汰了）是异步操作规范。

CMD通用模块定义，异步操作，吸收了CommonJS规范

# ES Module

采用export和import导入导出

a.js

```js
const name = "l"
const hello = function () {
  console.log("hello")
}

export {
  name, //还可以起别名 name as fname，对应的import fname
  hello
}
```

```js
//可以这样直接导出，只是不能起别名
export const name = "l"
export const hello = function () {
  console.log("hello")
}
```



hello.js

```js
import {
  name as fname, hello //导入时起别名
} from "./a.js"

console.log(fname)
hello()
```

```js
//给整个模块起个别名
import * as foo from "./a.js"

console.log(foo.name)
foo.hello()
```



index.html

```js
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <script src="./a.js" type="module"></script>
  <script src="./hello.js" type="module"></script>
</body>

</html>
```

把模块封装给html文件，必须用server打开，不能当作文件在本地打开，因为不支持file协议，所以引用不了模块文件

## 实际开发中的导入导出

utilty工具文件夹下面有a.js b.js index.js

与utilty同层的文件有index.html main.js

a.js

```js
export const name = "l"
```

b.js

```js
export const age = 1
```

index.js

```js
export { name } from "./a.js"
export { age } from "./b.js"

//export * from "./a.js"
//export * from "./b.js"
```

main.js

```js
import { name, age } from "./utilty/index.js"

console.log(name)
console.log(age)
```

index.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <script src="./main.js" type="module"></script>

</body>

</html>
```

## export default

a.js

```js
const name = "l"
export default name
```

b.js

```js
export default function () {
  console.log("hello")
}
```

main.js

```js
import fname from "./utilty/a.js"
import sayHello from "./utilty/b.js"

console.log(fname)
sayHello()
```

index.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <script src="./main.js" type="module"></script>

</body>

</html>
```

每个模块只能使用一次默认导出，导入时可以自定义名字，不必在意导出时是否有名字

## import()函数

import只能写在顶部，不能写在逻辑代码中。因为ES Module在被js引擎解析时，必须先知道它的依赖关系，也就是import了哪些东西。

但是可以使用import（）函数动态加载模块,函数返回的是一个promise对象

a.js

```js
export const name = "l"
```

main.js

```js
const flag = true

if (flag) {
  const importPromise = import("./utilty/a.js")
  importPromise.then(res => {
    console.log(res.name)
  })
} else {
  import("./utilty/b.js").then(res => {
    console.log(res.name)
  })
}

```

import.meta是查看当前模块的url属性

## ES Module的解析/加载过程

阶段一：构建，根据地址**查找**js文件，并且**下载**，将其解析成**模块记录**；根据impor...from...找到依赖的js文件下载下来，通常会有一个入口文件，然后通过`import`代码去寻找与之关联的其他模块，形成一个`依赖关系树（AST）`。形成一个映射关系module map，里面装的（url地址，module record）)，`Module Record` 包含了当前模块的 `AST`，引用了哪些模块的变量，以及一些特定属性和方法。

阶段二：实例化，对模块记录进行实例化，并且**分配内存空间**，把模块指向对应的内存地址。解析模块的导入和导出语句，生成Module Environment Record管理 `Module Record` 的变量，然后让 export 和 import 都指向这些内存块。这个过程叫做链接（linking）

阶段三：运行，运行代码，计算值，并且将值填充到内存地址中。

# npm

Node Package Manager，Node包管理工具。现在不仅仅是Node包管理工具，而是在整个前端项目中作为依赖包管理工具。

npm管理的包可以在npm官网上查看。npm管理的包实际上是放在registry里面的，安装包也是从里面下载的

## packge.json包

通过npm install dayjs （也可以是其他的包）：

```
会有一个package.json文件 ，该文件用来记录该包相关的项目信息

```

使用命令npm init 用来创建本项目的packge.json包

```
npm init -y //默认所有回答都是yes
```

通过脚手架创建，会自动创建packge.json包

### 常见的属性：

name项目名称

version版本

descrip描述

author作者

license开源协议

private，记录当前项目是否是私有的。true时，npm是不能发布它的

main程序入口

scripts，是一个对象

```
{
“start” ：“**.js” 
//使用npm run start 就可运行脚本，也可以npm start

}
```

dependencies:{}项目的依赖包，放开发环境和生产环境都需要的依赖包

```
npm install 就可以下载项目的依赖包了
```

devDependencies 一些生产环境不需要的包，比如webpack

```
npm install babel --save-dev 放入devDependencies，仅适用开放环境
npm install babel -D 简写
```

peerDependencies需要安装的宿主包

engines指定Node和NMP版本号

browerslist配置打包后JS浏览器的兼容情况

### 版本号

“^X.Y.Z”

“~X.Y.Z”

X主版本号：大的版本更新，可能不兼容之前的版本

Y此版本号：增加了新特性、功能，兼容了之前的版本

Z修订号：改了bug

^：X不变，Y和Z安装最新版本

~：X\Y不变，安装Z最新版本

## npm install

npm install  ** 局部安装，在哪个文件夹书写，就下载到该文件夹

npm install ** -g 全局下载

一般是工具包用全局下载

npm install ** 默认安装开发和依赖

npm install ** -D 开发依赖

npm install 根据package.json中的依赖包

install可以简写为 i

## package-lock文件（非手动记录）

 最终指定的版本

# yarn

克服早期npm的缺点

yarn init / yarn init -y  yarn run start  使用方法和npm一样

yarn install 和npm Install 一致

yarn add ** 和npm Install  ** 一致

# cnpm

下载源是镜像

# npx

npx **

```
是npm5.2之后自带的一个命令，取当前文件夹下的node_modules的.bin文件下**
```

**

```
windows执行某个命令：1优先在当前目录下寻找对应程序，2然后就去环境变量中查找
```

使用局部的包命令

```
1.明确查找到node_modules下面对应的包
2.在scripts定义脚本，
```

# npm打包

1.使用npm init -y 生成package.json，修改项目信息

2.注册npm账号，npmjs.com

3.npm login 

4.npm publish

更新：
1.修改version 

4.npm publish

删除：

npm unpublish

过期：

npm deprecate

# pnpm

performant npm ，速度快，节省磁盘空间的软件包管理器

支持单仓多包，严格（创建了一个非平铺的node_modules，代码无法访问任意包）

## 硬链接hard link

是指电脑文件系统中多个文件平等**共享同一个文件存储单元**；

删除一个文件名字后，还可以使用其他名字继续访问该文件

window:

```
mklink /H new.js origin.js
```



## 软链接（符号链接）

包含一条以绝对路径或相对路径，是指向其他文件或目录的引用。

window:

```
mklink new.js origin.js
```

就是常见的快捷文件

当时有npm或者yarn，有100个项目，若都有一个相同的依赖包，则会重复100份。

pnmp会将依赖包存放在一个**统一位置**，克服了以上缺点，节省了空间；若有不同版本以来，会将版本不同文件存储起来；使用的是**硬链接**

## pnpm是非扁平化的node_modules目录

扁平化：所有的包，包括依赖包都存储在一个文件node_modules下面。缺点：没有应用就可使用包

## pnpm包的存储

包会下载到store磁盘中，项目需要引用则用硬链接关联

# 内置模块path

对路径和文件进行处理

```
path.extname(filepath) 返回后缀
path.extname(filepath)
path.dirname(filepath) 返回目录
path.join(path1,path2) 拼接路径
path.resolve(path1,path2...) 从右向左执行，直到生成一个绝对路径，直到左端则加上当前文件目录
```

# webpack

静态的模块化打包工具

webpack运行时依赖Node环境的

## webpack.config.js配置文件

```
打包配置
module.exports = {
	entry:"",
	output:{
	filename: "",
	path:"" 绝对路径
	}
}
```

## webpack依赖图

webpack处理应用程序时，会从入口文件开始，生成一个**依赖关系图**，该图会包含应用程序中所需的所有模块（.包含js\图片等资源）

## css-loader

webpack能处理js文件，但不能处理css文件，可是webpack的扩展性好，使用loader可以处理各种文件

采用配置文件设置：

在wk.config.js的module.exports中加入

```
modules:{
	rules:[
		//告诉webpack匹配什么文件
		test：/\.css$/,
		//使用哪些loader
		use:[   //use中使用顺序是从后向前
			{loader: "style-loader"}   //需要npm install style-loader -D
			{loader: "css-loader"}   //需要npm install css-loader -D
		]
	]
}
```



css-loader只解析css，并不会添加到页面，使用style-loader完成。

在所需css的js文件中 头部写import  “路径/文件”

```
use:["style-loader","css-loader"] //只需要loader时可以简写
```

## less-loader

同样操作

```
modules:{
	rules:[{
		//告诉webpack匹配什么文件
		test：/\.css$/,
		//使用哪些loader
		use:[   //use中使用顺序是从后向前
			{loader: "style-loader"},   //需要npm install style-loader -D
			{loader: "css-loader"}   //需要npm install css-loader -D
		]
	},
		{
		//告诉webpack匹配什么文件
		test：/\.less$/,
		//使用哪些loader
		use:[   //use中使用顺序是从后向前
			{loader: "style-loader"},  //需要npm install style-loader -D
			{loader: "css-loader"},   //需要npm install less-loader -D
			{loader: "less-loader"}   //需要npm install less-loader -D
		]
	}
	]
}
```

## postcss-loader

考虑到style中属性的浏览器兼容性，另外还需要使用浏览器插件autoprefixer

```
modules:{
	rules:[{
		//告诉webpack匹配什么文件
		test：/\.css$/,
		//使用哪些loader
		use:["style-loader","css-loader","postcss-loader"]
	}
	]
}
```

可以单独配置一个postcss.config.js配置：

```
modue.exports = {
	plugins:[
		"autoprefixer"
	]
}
```

所以wk.config.js里面的匹配可以简写：

```
modules:{
	rules:[{
		//告诉webpack匹配什么文件
		test：/\.css$/,
		//使用哪些loader
		use:[   //use中使用顺序是从后向前
			{loader: "style-loader"},   //需要npm install style-loader -D
			{loader: "css-loader"},   //需要npm install css-loader -D
			{
			loader: "postcss-loader"  //需要npm install postcss-loader -D
			options:{
				postcssOptions:{plugins:["autoprefixer"]} //需要npm install autoprefixer -D
			}
			},  
		]
	}
	]
}
```

### postcss-preset-env

预设一些插件，包含了autoprefixer，所以就可以代替掉autoprefixer

## .png .jpg .svg .gif

加载这些资源已经是webpack的内置配置了

```
modules:{
	rules:[{
		//告诉webpack匹配什么文件
		test：/\.css$/,
		//使用哪些loader
		use:["style-loader","css-loader","postcss-loader"]
	},
	{
		test：/\.(png|jpe?g|svg|gif)$/,
		type: "asset"
	}
	]
}
```

同样在需要使用图片的地方写上 import “路径名/文件名”，或者 import 名字  from “路径名/文件名” 

type: "asset/resource" ,把图片放服务器里并生成对应的url，缺点：多了一次http请求

type: "asset/inline"，把图片进行base64编码，并把编码后的源码放入打包后的js中，缺点：造成js文件大，下载时间长

合理规范：小的图片进行base64编码，大的使用url请求

```
{
		test：/\.(png|jpe?g|svg|gif)$/,
		type: "asset",
		parser:{
			dataUrlCondition:{
			maxSize: 60* 1024
			}
		},
		generator:{
			//占位符
			//name: 指向原来的图片名称
			//ext: 扩展名
			//hash: webpack生成的hash
			filename: "img/_[hash:8][name][ext]" //图片存储在img文件下；hash截取8位
		}
	}
```

## Babel

实际上是一个工具链，主要用于旧浏览器或者环境中，将ECMAScript 2015+代码转换为向后兼容版本的JavaScript；

包括语法兼容、源代码转换等

```
modules:{
	rules:[{
		//告诉webpack匹配什么文件
		test：/\.m?js$/,
		//使用哪些loader
		use:{
			loader: "babel-loader", //需要npm install style-loader -D
			options:{
				presets:[
				["@babel/preset-env"] //需要npm install @babel/preset-env -D
				]
			}
		}
	}
	]
}
```

## vue-loader

首先需要npm install vue

1.创建.vue文件，例如创建了hello.vue

2.引入文件，

```
import {createApp} from "vue/dist/vue.esm-bundler"
import hello from '路劲文件名'

creatApp(hello).mount('#app')
```

3.配置文件

```
const {VueLoaderPlugin} = require("vue-loader/dist/index")

modules:{
	rules:[{
		//告诉webpack匹配什么文件
		test：/\.vue$/,
		//使用哪些loader
		loader:"vue-loader" // //需要npm install vue-loader -D
	}
	]
}
plugin:[ 
	new VueLoaderPlugin()
]
```

还需要@vue/compiler-sfc ，一般会自动下载，目的是对template进行解析

## resolve模块解析

resolve帮助webpack从每个require/import语句中，找到需要引入的模块代码。

webpack使用enhanced-resolve来解析文件路径；

解析文件路径：

1.绝对路径：直接解析.

2.相对路径：由上下文目录加上相对路径，生成模块的绝对路径

3.模块路径：从指定目录中检索，默认值是‘node_modules’，可以设置别名

### 文件还是文件夹

路径名最后如果是文件：

有扩展名直接打包，否则使用resolve.extensions选项作为文件扩展名解析

在module.exports ={}里面加上：

```
resolve:{
	extensions:[".js",".json",...]
	alias:{
		pathname:path.resolve(__dirname,"./src/utils")
	}
}
```

路径名最后如果是文件夹：

则会根据resolve.mainFiles配置选项中查找指定文件，默认为index，

再根据resolve.extensions来解析扩展名

## webpack常见插件

loader只是完成某种模块类型的转换，插件能完成更加广泛的任务。

比如自动删除打包文件：CleanWebpackPlugin

首先安装这个插件：

npm install clean-webpack-plugin -D

然后在配置文件中;

```
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
module.exports ={
	plugins:[
		new CleanWebpackPlugin()
	]
}
```

自动生成html打包文件：

npm install html-webpack-plugin -D

```
const {  HtmlWebpackPlugin } = require(' html-webpack-plugin')
module.exports ={
	plugins:[
		new HtmlWebpackPlugin({
			title:"标题"
			template:""//可以使用自定义模板
		})
	]
}
```

DefinePlugin是webpack内置插件，作用是环境变量注入。可以再任意位置使用name

```
const {  DefinePlugin } = require(' html-webpack-plugin')
const {  De } = require(' webpack')
module.exports ={
	plugins:[
		new HtmlWebpackPlugin({
			title:"标题"
			template:""//可以使用自定义模板
		}),
		new DefinePlugin({
			name:"l"
		})
	]
}
```

还有一个默认注入的环境变量,是process对象

process.env.NODE_ENV用于判断当前环境是开发还是生产环境

## mode配置

告知webpack使用相应模式的内置优化，有None、production、development默认是production生产环境，

## webpack开启本地服务器

为克服每次修改了代码需要手动打包至硬盘，重启浏览器，使用web-dev-server

1.npm install web-dev-sever -D

2.使用命令webpack serve --config wk.config.js

或者把在wk.config.js文件的script里写

```
scripts:{
	"serve": "webpack serve --config wk.config.js"
}
```

每次使用npm run serve即可，这个方法不会把打包后的文件放在硬盘，而是放在内存

## 模块热替换

指应用程序在运行过程中，修改、替换、添加、删除模块的操作不会刷新整个页面。

优点：不重新加载整个页面，可以保留某些应用程序的状态；只更新需要变化的内容，节省开发时间；修改了css、js源代码，会立即在浏览器更新。

默认情况下，webpack-dev-sever已经支持HMR，我们只需要开启即可（默认已经开启；）

在wk.config.js文件里：

```
module.exports ={
	devServer:{
		hot: true
		port: 8888 //修改端口号
		host: "0.0.0.0" //设置主机地址
		open: true //自动打开浏览器
		compress: true //压缩文件，客户浏览器收到的就是一个压缩包，然后浏览器解压
	}
}
```

但是我们需要手动指定哪些模块更新时进行HMR

```
if(module.hot){
	module.hot.accept("模块")
}
```

### 开发和生产配置

一般会写两个配置文件wk.prod.config.js和wk.dev.config.js

可以写一个公共的配置文件wk.com.confg.js，抽取开发和生产环境的公共参数，使用webpack-merge合并文件

1.npm install webpack-merge -D

2.在开发或者生产文件中使用 const { merge } =require("wk.com.config")

3.导入公共文件，const commonConfig = require("./wk.com.config")

4.module.exports = (commonConfig ,{

​	mode: "production",

​	...

})

# GIT

版本控制，指能追踪项目从蓝图到定案的过程

集中式版本控制，特点是单一的集中管理的服务器，保存所有文件的修订版本。协同卡还人员通过客户端连接到这台服务器，取出最新的文件或者提交更新

分布式版本控制：客户端并不止提取最新版本的文件快照，而是把代码仓库完整的镜像下来，包括完整的历史记录；任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复；因为每一次克隆操作，实际上都是一次对代码仓库的完整备份

## Bash-CMD-GUI

Bash是shell的一种，linux与Mac OS X都将Bash作为默认shell。

Git bash就是一种shell，是windows下的命令行工具，可以执行linux命令；但它是基于CMD的，在CMD基础上添加了新的命令；

Git CMD是windows os上的命令解释程序

Git GUI 是图形界面

## Git配置分类

git config --sysem  //为系统上每一个用户及他们仓库的通用配置

--global //只针对当前用户

--loacal //只针对当前仓库

安装Git后，第一时间就是设置用户名和邮件地址：

```
 git config --global user.name "colon"
 git config --global user.email "123131@gmail.com"
 git config --list //查看配置信息
 git config user.name //查看用户名
```

## 获取仓库

我们需要一个Git来管理源代码，那么需要一个本地的Git仓库。

两种获取Git项目仓库的方式：

1.初始化一个Git仓库，将当前项目文件都添加进去

```
 git init //仅仅是初始化，生成了一个.git本地仓库，文件还没有被追踪。
```

2.从远程Git仓库克隆

```
 git clone https://github..........
```

## 文件的状态划分

未跟踪untracked：默认情况下，Git仓库下的文件没有被添加至Git仓库管理中，还需要通过add命令来操作变成暂缓状态staged

已跟踪：表示文件已添加至Git仓库管理

### 已跟踪下的状态

staged：文件通过add操作之后的状态

Unmodified:commit命令后，将staged状态的文件提交至Git仓库之后的状态

Modified：修改了某个unmodified文件后的状态

unmodified状态的文件被移除，状态将变为Untracked

```
 git commit -a -m "***"  //-a 表示add操作
 git add aaa.js //开始跟踪一个文件，放入暂存区
```

如果不想跟踪某些文件，比如日志文件等待，可以创建一个.gitignore文件，在文件里面列出文件名。一般脚手架会自动给出

```
 .DS_Store
 node_modules
 /dist
```



### 查看状态

```
 git status //可以查看相关变化文件的状态
 git log  //可以查看版本日志
 git log --pretty=oneline //一次提交日志于一行显示
```

## 校验和

Git中所有的数据在存储前需要计算校验和，通过校验和来引用。

Git使用SHA-1散列机制来计算校验和（Hash），是由40个十六进制的字符组成。

## 版本回退

Git通过HEAD指针记录当前版本

HEAD是当前分支引用的指针，总是指向该分支上的最后一次提交

可以通过改变HEAD指向进行版本回退

```
 git reset --hard HEAD^  //回退上一版本
 git reset --hard HEAD^^  //回退上上次版本
 git reset --hard HEAD~100  //回退上100次版本
 git reset --hard 2d44982  //回退指定的commit id
 git reflog //查看所有的版本操作
```

## 远程仓库Remote Repository

若远程仓库是私有的，有两种方式访问：

1.HTTPS凭证验证。

因为HTTP协议是无状态的，不记录每次链接的状态记录。每一次访问私有仓库，都需要带有账号和密码。

如果是Mac，Git有一种osxkeychain模式，会将凭证缓存到系统用户的钥匙串中。

如果是Windows，安装“Git Credential Manager for Windows”辅助工具：

```
 git config credential.helper //如有manager-core，则成功
```

可以在操作系统中的凭证管理器删除凭证

2.SSH密钥

Secure Shell安全外壳协议，是一种加密的网络传输协议，可在不安全的网络中为网络服务提供安全的传输环境。

是以非对称加密方式实现身份验证。

目前使用人工生成一堆公钥和私钥，公钥放在带访问的仓库（电脑）之中，私钥自己保管。

```
 ssh-keygen -t ed25519 -C “可以是邮件号” //ed25519是产生密钥的方法
```



```
 git remote //查看远程仓库
 git remote -v //查看远程仓库详细
 git remote add 仓库名（自己起的） 仓库地址 //连接远程仓库
 git pull //把远程仓库拿下来，pull是fetch和 merge操作
 git push //上传至远程仓库
```

所以一般操作是

```
 git add.
 git commit -m ""
 git pull
 git push
```

## 远程仓库的交互

```
 git clone http://......   //从远程仓库clone代码，存储到新创建的目录中
 将本地仓库push到远程仓库。默认情况是将当前分支（比如master）推到远程仓库中；
 git push
 git push origin（仓库） master（分支）
 从远程仓库fetch获取最新代码
 git fetch  //已经设置了上游分支
 git fetch origin（仓库） master（分支）
 获取到代码后默认并没有合并到本地仓库，需要merge合并
 git merge  //已经设置了上游分支
 git merge origin/master
 把fetch和merge操作合并就是pull操作
 git pull
 git fetch  + git merge（rebase）
```

## 标签

Git可以给仓库历史中的某一次提交打上标签，一般使用v1.0，v2.0等等

标签分为轻量标签与附注标签

```
 git tag v1.0
 git tag -a v1.1 -m"附注标签"
```

默认情况，git push并不会传送标签到远程仓库服务器上。

```
 git push origin v1.0
 git push origin -tags
```

删除本地tag

```
 git tag -d v1.1
```

删除远程tag

```
 git push origin --delete v1.1
```

检出tag：查看某些版本的内容，若要修改，需创建个新的分支

```
 git checkout v1.0
```

## Git提交对象

在进行提交操作时，Git会保存一个提交对象。该对象会包含一个指向暂存内容快照的指针；还包含了作者的姓名、邮箱、提交时的信息和指向父对象的指针

## 分支

Git分支，本质上仅仅时指向提交对象的可变指针。

Git的默认分支名为master，指向最后的提交提交对象

```bash
git branch //查看分支
git branch abc //创建新的分支
git checkout abc //切换到abc分支，head指针指向abc
git checkout -b zxc //创建并切换到zxc分支，head指针指向zxc
```

开发某个项目，在默认分支master上进行开发，完成一个大的版本时，发布版本，打上一个tag v1.0.0，然后继续在master上进行开发，但若遇到v1.0.0有bug的时候，切换到v1.0.0节点在此创建新的分支git checkout -b hotfix，该分支用于解决bug。解决之后，打上标签v1.0.1，把该分支合并到master分支git checkout master，git merge hotfix，合并后的节点有两个父亲

```bash
git branch //查看当前所有分支
git branch -v //同时查看最后一次提交
git branch --merged //查看所有合并到当前分支的分支
git branch --no-merged //查看所有没有合并到当前分支的分支
```

移除分支（指针）

```bash
git branch -d 分支名 
git branch -D 分支名 //强制移除 
```

## 远程分支

公开分享一个分支时，需要将其推送到有写入权限的远程仓库上：

运行git push <remote>  <branch>;

git push origin <branch>

跟踪远程分支：

当克隆一个仓库时，它通常会自动创建一个跟踪origin/master的master分支；

跟踪其他分支：git checkout --track<remote>/<branch>

如果本地检测不到分支名，则会在远程仓库匹配：

git checkout <branch>

删除远程分支：

git push origin -d <branch>

## rebase变基

使用rebase命令将提交到某一分支上的所有修改移至另一分支上。比如：

git checkout experiment

git rebase master

永远不要rebase主分支，会造成大量的提交历史改变