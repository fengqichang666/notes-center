## redux是什么

```
1.redux是一个专门用于做状态管理的JS库(不是react插件库)。
2.它可以用在react, angular, vue等项目中, 但基本与react配合使用。
3.作用: 集中式管理react应用中多个组件共享的状态。
```

## 什么情况下需要使用redux

```
1.某个组件的状态，需要让其他组件可以随时拿到（共享）。
2.一个组件需要改变另一个组件的状态（通信）。
3.总体原则：能不用就不用, 如果不用比较吃力才考虑使用。
```

## redux的三个核心概念

### action

```
1.动作的对象
2.包含2个属性
	type：标识属性, 值为字符串, 唯一, 必要属性
	data：数据属性, 值类型任意, 可选属性
3.例子：{ type: 'ADD_STUDENT',data:{name: 'tom',age:18} }
```

### reducer

```
1.用于初始化状态、加工状态。
2.加工时，根据旧的state和action， 产生新的state的纯函数。
```

### store

```
1.将state、action、reducer联系在一起的对象
2.如何得到此对象?
	import {createStore} from 'redux'
	import reducer from './reducers'
	const store = createStore(reducer)
3.此对象的功能?
	getState(): 得到state
	dispatch(action): 分发action, 触发reducer调用, 产生新的state
	subscribe(listener): 注册监听, 当产生了新的state时, 自动调用
```

## redux的核心API

### createstore()

作用：创建包含指定reducer的store对象

### store对象

```
1. 作用: redux库最核心的管理对象

2. 它内部维护着:

	1 state

	2 reducer

3. 核心方法:

	1 getState()

	2 dispatch(action)

	3 subscribe(listener)

4. 具体编码:

	1 store.getState()

	2 store.dispatch({type:'INCREMENT', number})

	3 store.subscribe(render)
```



### applyMiddleware()

作用：应用上基于redux的中间件(插件库)

### combineReducers()

作用：合并多个reducer函数

##  redux-thunk异步编程

```
1.redux默认是不能进行异步处理的, 
2.某些时候应用中需要在redux中执行异步任务(ajax, 定时器)
使用异步中间件
npm install --save redux-thunk
作用：判断每个经过它的action：如果是function类型，就调用这个function（并传入 dispatch 和 getState 及 extraArgument 为参数）
```

## react-redux

```
 一个react插件库。专门用来简化react应用中使用redux
react-Redux将所有组件分成两大类
1.UI组件
	只负责 UI 的呈现，不带有任何业务逻辑
	通过props接收数据(一般数据和函数)
	不使用任何 Redux 的 API
	一般保存在components文件夹下
2.容器组件
	负责管理数据和业务逻辑，不负责UI的呈现
	使用 Redux 的 API
	一般保存在containers文件夹下
相关API
1.Provider：让所有组件都可以得到state数据
<Provider store={store}>
  <App />
</Provider>
2.connect：用于包装 UI 组件生成容器组件
import { connect } from 'react-redux'
  connect(
    mapStateToprops,
    mapDispatchToProps
  )(Counter)
3.mapStateToprops：将外部的数据（即state对象）转换为UI组件的标签属性
const mapStateToprops = function (state) {
  return {
    value: state
  }
}
4.mapDispatchToProps：将分发action的函数转换为UI组件的标签属性
```



## 1.求和案例_redux精简版

```jsx
	(1).去除Count组件自身的状态
	//引入store，用于获取redux中保存状态
	import store from '../../redux/store'
	
	increment = ()=>{
		const {value} = this.selectNumber
		store.dispatch({type:'increment',data:value*1})
	}
	(2).src下建立:
					-redux
						-store.js
						-count_reducer.js
						 
	(3).store.js：
				1).引入redux中的createStore函数，创建一个store
				2).createStore调用时要传入一个为其服务的reducer
				3).记得暴露store对象
						  /* 
                                该文件专门用于暴露一个store对象，整个应用只有一个store对象
                            */
						  //引入createStore，专门用于创建redux中最为核心的store对象
                            import {createStore} from 'redux'
                            //引入为Count组件服务的reducer
                            import countReducer from './count_reducer'
                            //暴露store
                            export default createStore(countReducer)
                            
	(4).count_reducer.js：
				1).reducer的本质是一个函数，接收：preState,action，返回加工后的状态
				2).reducer有两个作用：初始化状态，加工状态
				3).reducer被第一次调用时，是store自动触发的，
								传递的preState是undefined,
								传递的action是:{type:'@@REDUX/INIT_a.2.b.4}
						 /* 
                                1.该文件是用于创建一个为Count组件服务的reducer，reducer的本质就是一个函数
                                2.reducer函数会接到两个参数，分别为：之前的状态(preState)，动作对象(action)
                            */

                            const initState = 0 //初始化状态
                            export default function countReducer(preState=initState,action){
                                // console.log(preState);
                                //从action对象中获取：type、data
                                const {type,data} = action
                                //根据type决定如何加工数据
                                switch (type) {
                                    case 'increment': //如果是加
                                        return preState + data
                                    case 'decrement': //若果是减
                                        return preState - data
                                    default:
                                        return preState
                                }
                            }

	(5).在index.js中监测store中状态的改变，一旦发生改变重新渲染<App/>
			备注：redux只负责管理状态，至于状态的改变驱动着页面的展示，要靠我们自己写。
			
	import store from './redux/store'

    ReactDOM.render(<App/>,document.getElementById('root'))

    store.subscribe(()=>{
        ReactDOM.render(<App/>,document.getElementById('root'))
    })
```

## 2.求和案例_redux完整版

```jsx
	新增文件：
		1.count_action.js 专门用于创建action对象
		/* 
            该文件专门为Count组件生成action对象
        */
        import {INCREMENT,DECREMENT} from './constant'

        export const createIncrementAction = data => ({type:INCREMENT,data})
        export const createDecrementAction = data => ({type:DECREMENT,data})

		2.constant.js 放置容易写错的type值
        /* 
            该模块是用于定义action对象中type类型的常量值，目的只有一个：便于管理的同时防止程序员单词写错
        */
        export const INCREMENT = 'increment'
        export const DECREMENT = 'decrement'
```

## 3.求和案例_redux异步action版

```jsx
	 (1).明确：延迟的动作不想交给组件自身，想交给action
	 (2).何时需要异步action：想要对状态进行操作，但是具体的数据靠异步任务返回。
	 (3).具体编码：
	 			1).yarn add redux-thunk，并配置在store中
	 			  //引入createStore，专门用于创建redux中最为核心的store对象
                    import {createStore,applyMiddleware} from 'redux'
                    //引入为Count组件服务的reducer
                    import countReducer from './count_reducer'
                    //引入redux-thunk，用于支持异步action
                    import thunk from 'redux-thunk'
                    //暴露store
                    export default createStore(countReducer,applyMiddleware(thunk))
	 			2).创建action的函数不再返回一般对象，而是一个函数，该函数中写异步任务。
	 			  //异步action，就是指action的值为函数,异步action中一般都会调用同步action，异步action不是必须要用的。
                    export const createIncrementAsyncAction = (data,time) => {
                        return (dispatch)=>{
                            setTimeout(()=>{
                                dispatch(createIncrementAction(data))
                            },time)
                        }
                    }
	 			3).异步任务有结果后，分发一个同步的action去真正操作数据。
	 (4).备注：异步action不是必须要写的，完全可以自己等待异步任务的结果了再去分发同步action。
```

## 4.求和案例_react-redux基本使用

```jsx
		(1).明确两个概念：
					1).UI组件:不能使用任何redux的api，只负责页面的呈现、交互等。
                    increment = ()=>{
                        const {value} = this.selectNumber
                        this.props.jia(value+1)
                    }
					2).容器组件：负责和redux通信，将结果交给UI组件。
		(2).如何创建一个容器组件————靠react-redux 的 connect函数
						connect(mapStateToProps,mapDispatchToProps)(UI组件)
							-mapStateToProps:映射状态，返回值是一个对象
							-mapDispatchToProps:映射操作状态的方法，返回值是一个对象
			  //引入Count的UI组件
                import CountUI from '../../components/Count'
                //引入action
                import {
                    createIncrementAction,
                    createDecrementAction,
                    createIncrementAsyncAction
                } from '../../redux/count_action'

                //引入connect用于连接UI组件与redux
                import {connect} from 'react-redux'

                /* 
                    1.mapStateToProps函数返回的是一个对象；
                    2.返回的对象中的key就作为传递给UI组件props的key,value就作为传递给UI组件props的value
                    3.mapStateToProps用于传递状态
                */
                function mapStateToProps(state){
                    return {count:state}
                }

                /* 
                    1.mapDispatchToProps函数返回的是一个对象；
                    2.返回的对象中的key就作为传递给UI组件props的key,value就作为传递给UI组件props的value
                    3.mapDispatchToProps用于传递操作状态的方法
                */
                function mapDispatchToProps(dispatch){
                    return {
                        jia:number => dispatch(createIncrementAction(number)),
                        jian:number => dispatch(createDecrementAction(number)),
                        jiaAsync:(number,time) => dispatch(createIncrementAsyncAction(number,time)),
                    }
                }

                //使用connect()()创建并暴露一个Count的容器组件
                export default connect(mapStateToProps,mapDispatchToProps)(CountUI)
		(3).备注1：容器组件中的store是靠props传进去的，而不是在容器组件中直接引入
		(4).备注2：mapDispatchToProps，也可以是一个对象
```

## 5.求和案例_react-redux优化

```jsx
		(1).容器组件和UI组件整合一个文件
		//当前UI组件 下方使用connect()()创建并暴露一个Count的容器组件
        export default connect(
            state => ({count:state}),

            //mapDispatchToProps的一般写法
            /* dispatch => ({
                jia:number => dispatch(createIncrementAction(number)),
                jian:number => dispatch(createDecrementAction(number)),
                jiaAsync:(number,time) => dispatch(createIncrementAsyncAction(number,time)),
            }) */

            //mapDispatchToProps的简写
            {
                jia:createIncrementAction,
                jian:createDecrementAction,
                jiaAsync:createIncrementAsyncAction,
            }
        )(Count)
		(2).无需自己给容器组件传递store，给<App/>包裹一个<Provider store={store}>即可。
		   import store from './redux/store'
            import {Provider} from 'react-redux'

            ReactDOM.render(
                <Provider store={store}>
                    <App/>
                </Provider>,
                document.getElementById('root')
            )
		(3).使用了react-redux后也不用再自己检测redux中状态的改变了，容器组件可以自动完成这个工作。
		(4).mapDispatchToProps也可以简单的写成一个对象
		(5).一个组件要和redux“打交道”要经过哪几步？
						(1).定义好UI组件---不暴露
						(2).引入connect生成一个容器组件，并暴露，写法如下：
								connect(
									state => ({key:value}), //映射状态
									{key:xxxxxAction} //映射操作状态的方法
								)(UI组件)
						(4).在UI组件中通过this.props.xxxxxxx读取和操作状态
```

## 6.求和案例_react-redux数据共享版

```jsx
		(1).定义一个Pserson组件，和Count组件通过redux共享数据。
		(2).为Person组件编写：reducer、action，配置constant常量。
		(3).重点：Person的reducer和Count的Reducer要使用combineReducers进行合并，
				合并后的总状态是一个对象！！！
           store.js
           import {createStore,applyMiddleware,combineReducers} from 'redux'
            //引入为Count组件服务的reducer
            import countReducer from './reducers/count'
            //引入为Count组件服务的reducer
            import personReducer from './reducers/person'
            //引入redux-thunk，用于支持异步action
            import thunk from 'redux-thunk'

            //汇总所有的reducer变为一个总的reducer
            const allReducer = combineReducers({
                he:countReducer,
                rens:personReducer
            })

            //暴露store
            export default createStore(allReducer,applyMiddleware(thunk))

		容器组件
        //使用connect()()创建并暴露一个Count的容器组件
        export default connect(
            state => ({
                count:state.he,
                renshu:state.rens.length
            }),
            {
                jia:createIncrementAction,
                jian:createDecrementAction,
                jiaAsync:createIncrementAsyncAction,
            }
        )(Count)

		UI组件
        export default connect(
            state => ({yiduiren:state.rens,he:state.he}),//映射状态
            {jiaYiRen:createAddPersonAction}//映射操作状态的方法
        )(Person)
		(4).交给store的是总reducer，最后注意在组件中取出状态的时候，记得“取到位”。
```

## 7.求和案例_react-redux开发者工具的使用

```
		(1).yarn add redux-devtools-extension
		(2).store中进行配置
				import {composeWithDevTools} from 'redux-devtools-extension'
				const store = createStore(allReducer,composeWithDevTools(applyMiddleware(thunk)))
```

## 8.求和案例_react-redux最终版

```jsx
		(1).所有变量名字要规范，尽量触发对象的简写形式。
		(2).reducers文件夹中，编写index.js专门用于汇总并暴露所有的reducer
		/* 
            该文件用于汇总所有的reducer为一个总的reducer
        */
        //引入combineReducers，用于汇总多个reducer
        import {combineReducers} from 'redux'
        //引入为Count组件服务的reducer
        import count from './count'
        //引入为Person组件服务的reducer
        import persons from './person'

        //汇总所有的reducer变为一个总的reducer
        export default  combineReducers({
            count,
            persons
        })
	store.js
	//引入createStore，专门用于创建redux中最为核心的store对象
    import {createStore,applyMiddleware} from 'redux'
    //引入汇总之后的reducer
    import reducer from './reducers'
    //引入redux-thunk，用于支持异步action
    import thunk from 'redux-thunk'
    //引入redux-devtools-extension
    import {composeWithDevTools} from 'redux-devtools-extension'

    //暴露store 
    export default createStore(reducer,composeWithDevTools(applyMiddleware(thunk)))
```