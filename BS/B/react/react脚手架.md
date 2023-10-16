## react脚手架

> 1. xxx脚手架: 用来帮助程序员快速创建一个基于xxx库的模板项目
>
>    1. 包含了所有需要的配置（语法检查、jsx编译、devServer…）
>
>    2. 下载好了所有相关的依赖
>
>    3. 可以直接运行一个简单效果
>
> 2. react提供了一个用于创建react项目的脚手架库: create-react-app
>
> 3. 项目的整体技术架构为:  react + webpack + es6 + eslint
>
> 4. 使用脚手架开发的项目的特点: 模块化, 组件化, 工程化

## 创建项目并启动

> ***第一步***，全局安装：npm i -g create-react-app
>
> ***第二步***，切换到想创项目的目录，使用命令：create-react-app hello-react
>
> ***第三步***，进入项目文件夹：cd hello-react
>
> ***第四步***，启动项目：npm start

## react脚手架项目结构

```
public ---- 静态资源文件夹
		favicon.icon ------ 网站页签图标
		index.html -------- 主页面
		logo192.png ------- logo图
		logo512.png ------- logo图
		manifest.json ----- 应用加壳的配置文件
		robots.txt -------- 爬虫协议文件
src ---- 源码文件夹
		App.css -------- App组件的样式
		App.js --------- App组件
		App.test.js ---- 用于给App做测试
		index.css ------ 样式
		index.js ------- 入口文件
		logo.svg ------- logo图
		reportWebVitals.js
			--- 页面性能分析文件(需要web-vitals库的支持)
		setupTests.js
			---- 组件单元测试的文件(需要jest-dom库的支持)
```

## 配置代理



### 方法一

> 在package.json中追加如下配置

```json
"proxy":"http://localhost:5000"
```

说明：

1. 优点：配置简单，前端请求资源时可以不加任何前缀。
2. 缺点：不能配置多个代理。
3. 工作方式：上述方式配置代理，当请求了3000不存在的资源时，那么该请求会转发给5000 （优先匹配前端资源）

### 方法二

1. 第一步：创建代理配置文件

   ```
   在src下创建配置文件：src/setupProxy.js
   ```

2. 编写setupProxy.js配置具体代理规则：

   ```js
   const proxy = require('http-proxy-middleware')
   
   module.exports = function(app) {
     app.use(
       proxy('/api1', {  //api1是需要转发的请求(所有带有/api1前缀的请求都会转发给5000)
         target: 'http://localhost:5000', //配置转发目标地址(能返回数据的服务器地址)
         changeOrigin: true, //控制服务器接收到的请求头中host字段的值
         /*
         	changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
         	changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:3000
         	changeOrigin默认值为false，但我们一般将changeOrigin值设为true
         */
         pathRewrite: {'^/api1': ''} //去除请求前缀，保证交给后台服务器的是正常请求地址(必须配置)
       }),
       proxy('/api2', { 
         target: 'http://localhost:5001',
         changeOrigin: true,
         pathRewrite: {'^/api2': ''}
       })
     )
   }
   ```

说明：

1. 优点：可以配置多个代理，可以灵活的控制请求是否走代理。
2. 缺点：配置繁琐，前端请求资源时必须加前缀。

## 消息订阅-发布机制

> 1. 工具库: PubSubJS
>
> 2. 下载: npm install pubsub-js --save
>
> 3. 使用: 
>
>    1) import PubSub from 'pubsub-js' //引入
>
>    2) PubSub.subscribe('delete', function(data){ }); //订阅
>
>    3) PubSub.publish('delete', data) //发布消息

## Fetch

特点

1. fetch: 原生函数，不再使用XmlHttpRequest对象提交ajax请求
2. 老版本浏览器可能不支持

相关API

```
  // GET
  fetch(url).then(function(response) {
    return response.json()
  }).then(function(data) {
    console.log(data)
  }).catch(function(e) {
    console.log(e)
  });
  // POST
  fetch(url, {
    method: "POST",
    body: JSON.stringify(data),
  }).then(function(data) {
    console.log(data)
  }).catch(function(e) {
    console.log(e)
  })
```

## React路由

### SPA的理解

> 1. 单页Web应用（single page web application，SPA）。
>
> 2. 整个应用只有***一个完整的页面***。
>
> 3. 点击页面中的链接***不会刷新***页面，只会做页面的***局部更新。***
>
> 4. 数据都需要通过ajax请求获取, 并在前端异步展现。

### 路由的理解

**什么是路由**

> 1. 一个路由就是一个映射关系(key:value)
>
> 2. key为路径, value可能是function或component

### 路由分类

#### 后端路由

> 1. 理解： value是function, 用来处理客户端提交的请求。
> 2. 注册路由： router.get(path, function(req, res))
> 3. 工作过程：当node接收到一个请求时, 根据请求路径找到匹配的路由, 调用路由中的函数来处理请求, 返回响应数据

#### 前端路由

> 1) 浏览器端路由，value是component，用于展示页面内容。
> 2) 注册路由: <Route path="/test" component={Test}>
> 3) 工作过程：当浏览器的path变为/test时, 当前路由组件就会变为Test组件

### react-router-dom的理解

> - react的一个插件库。
> - 专门用来实现一个SPA应用。
> - 基于react的项目基本都会用到此库。

### react-router-dom相关API

#### 内置组件

```
1.<BrowserRouter>
2.<HashRouter>
3.<Route>
4.<Redirect>
5.<Link>
6.<NavLink>
7.<Switch>

```

#### 其它

```
1.history对象
2.match对象
3.withRouter函数
```

### 路由使用

下载 react-router-dom: `npm install --save react-router-dom`

```jsx
index.js
//引入ReactDOM
import ReactDOM from 'react-dom'
import {BrowserRouter} from 'react-router-dom'
ReactDOM.render(
	<BrowserRouter>
		<App/>
	</BrowserRouter>,
	document.getElementById('root')
)

import {Link,NavLink,Route} from 'react-router-dom'
import Home from './components/Home'
import About from './components/About'

{/* 在React中靠路由链接实现切换组件--编写路由链接 */}
<Link className="list-group-item" to="/about">About</Link>
<Link className="list-group-item" to="/home">Home</Link>
{/* <NavLink>是<Link> 的一个特定版本, 会在匹配上当前 URL 的时候会给已经渲染的元素添加样式参数 比如可以实现路由链接的高亮，通过activeClassName指定样式名 */}
<NavLink activeClassName="atguigu" className="list-group-item" to="/about">About</NavLink>
<NavLink activeClassName="atguigu" className="list-group-item" to="/home">Home</NavLink>

{/* 注册路由 */}
<Route path="/about" component={About}/>
<Route path="/home" component={Home}/>
```

### 路由组件与一般组件

```jsx
		1.写法不同：
					一般组件：<Demo/>
					路由组件：<Route path="/demo" component={Demo}/>
		2.存放位置不同：
					一般组件：components
					路由组件：pages
		3.接收到的props不同：
					一般组件：写组件标签时传递了什么，就能收到什么
					路由组件：接收到三个固定的属性
										history:
													go: ƒ go(n)
													goBack: ƒ goBack()
													goForward: ƒ goForward()
													push: ƒ push(path, state)
													replace: ƒ replace(path, state)
										location:
													pathname: "/about"
													search: ""
													state: undefined
										match:
													params: {}
													path: "/about"
													url: "/about"
```

### 封装NavLink

```jsx
export default class MyNavLink extends Component {
	render() {
		// console.log(this.props);
		return (
			<NavLink activeClassName="atguigu" className="list-group-item" {...this.props}/>
		)
	}
}

// 使用
<MyNavLink to="/about">About</MyNavLink>
```

### Switch的使用

```jsx
{/* 注册路由 只显示匹配到的第一个路由*/}
{/*
			1.通常情况下，path和component是一一对应的关系。
			2.Switch可以提高路由匹配效率(单一匹配)。
*/}
<Switch>
	<Route path="/about" component={About}/>
	<Route path="/home" component={Home}/>
	<Route path="/home" component={Test}/>
</Switch>
```

### 解决多级路径刷新页面样式丢失的问题

	1.public/index.html 中 引入样式时不写 ./ 写 / （常用）
	2.public/index.html 中 引入样式时不写 ./ 写 %PUBLIC_URL% （常用）
	3.使用HashRouter

### 路由的严格匹配与模糊匹配

```
1.默认使用的是模糊匹配（简单记：【输入的路径】必须包含要【匹配的路径】，且顺序要一致）
2.开启严格匹配：<Route exact={true} path="/about" component={About}/>
3.严格匹配不要随便开启，需要再开，有些时候开启会导致无法继续匹配二级路由
```

### Redirect的使用

```jsx
1.一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由
2.具体编码：
<Switch>
	<Route path="/about" component={About}/>
	<Route path="/home" component={Home}/>
	<Redirect to="/about"/>
</Switch>
```

### 嵌套路由

```jsx
{/*
	1.注册子路由时要写上父路由的path值
	2.路由的匹配是按照注册路由的顺序进行的
*/}
<div>
	<ul className="nav nav-tabs">
		<li>
			<MyNavLink to="/home/news">News</MyNavLink>
		</li>
		<li>
			<MyNavLink to="/home/message">Message</MyNavLink>
		</li>
	</ul>
	{/* 注册路由 */}
	<Switch>
		<Route path="/home/news" component={News}/>
		<Route path="/home/message" component={Message}/>
		<Redirect to="/home/news"/>
	</Switch>
</div>
```

### 向路由组件传递参数

			1.params参数
						路由链接(携带参数)：<Link to='/demo/test/tom/18'}>详情</Link>
						注册路由(声明接收)：<Route path="/demo/test/:name/:age" component={Test}/>
						接收参数：this.props.match.params
			2.search参数
						路由链接(携带参数)：<Link to='/demo/test?name=tom&age=18'}>详情</Link>
						注册路由(无需声明，正常注册即可)：<Route path="/demo/test" component={Test}/>
						接收参数：this.props.location.search
						备注：获取到的search是urlencoded编码字符串，需要借助querystring解析
			3.state参数
						路由链接(携带参数)：<Link to={{pathname:'/demo/test',state:{name:'tom',age:18}}}>详情</Link>
						注册路由(无需声明，正常注册即可)：<Route path="/demo/test" component={Test}/>
						接收参数：this.props.location.state
						备注：刷新也可以保留住参数

### 编程式路由导航

```jsx
借助this.prosp.history对象上的API对操作路由跳转、前进、后退
-this.prosp.history.push()
//push跳转+携带params参数
// this.props.history.push(`/home/message/detail/${id}/${title}`)

//push跳转+携带search参数
// this.props.history.push(`/home/message/detail?id=${id}&title=${title}`)

//push跳转+携带state参数
// this.props.history.push(`/home/message/detail`,{id,title})

-this.prosp.history.replace()
//replace跳转+携带params参数
//this.props.history.replace(`/home/message/detail/${id}/${title}`)

//replace跳转+携带search参数
// this.props.history.replace(`/home/message/detail?id=${id}&title=${title}`)

//replace跳转+携带state参数
// this.props.history.replace(`/home/message/detail`,{id,title})

-this.prosp.history.goBack()
-this.prosp.history.goForward()
-this.prosp.history.go()
```

### BrowserRouter与HashRouter的区别

```
		1.底层原理不一样：
					BrowserRouter使用的是H5的history API，不兼容IE9及以下版本。
					HashRouter使用的是URL的哈希值。
		2.path表现形式不一样
					BrowserRouter的路径中没有#,例如：localhost:3000/demo/test
					HashRouter的路径包含#,例如：localhost:3000/#/demo/test
		3.刷新后对路由state参数的影响
					(1).BrowserRouter没有任何影响，因为state保存在history对象中。
					(2).HashRouter刷新后会导致路由state参数的丢失！！！
		4.备注：HashRouter可以用于解决一些路径错误相关的问题。
```

### antd的按需引入+自定主题

		1.安装依赖：yarn add react-app-rewired customize-cra babel-plugin-import less less-loader
		2.修改package.json
				....
					"scripts": {
						"start": "react-app-rewired start",
						"build": "react-app-rewired build",
						"test": "react-app-rewired test",
						"eject": "react-scripts eject"
					},
				....
		3.根目录下创建config-overrides.js
				//配置具体的修改规则
				const { override, fixBabelImports,addLessLoader} = require('customize-cra');
				module.exports = override(
					fixBabelImports('import', {
						libraryName: 'antd',
						libraryDirectory: 'es',
						style: true,
					}),
					addLessLoader({
						lessOptions:{
							javascriptEnabled: true,
							modifyVars: { '@primary-color': 'green' },
						}
					}),
				);
			4.备注：不用在组件里亲自引入样式了，即：import 'antd/dist/antd.css'应该删掉

### withRouter

非路由组件可以通过withRouter高阶组件访问 History 对象的属性和进行匹配。withRouter将在渲染时向包装组件传递更新的 match、location 和 history 属性。

```jsx
//组件内引入withRouter
import {withRouter} from 'react-router-dom'

//导出组件时withRouter包裹组件
//withRouter可以加工一般组件，让一般组件具备路由组件所特有的API
//withRouter的返回值是一个新组件
export default withRouter(Header)
```

