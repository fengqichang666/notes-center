# 在 TypeScript 的项目中使用 Redux Toolkit

前言：

在以往的项目中低成本使用了 useReducer + useContext 作为简单的状态管理器，运行良好但是缺乏一些作为状态管理器的要素：

1.  DevTools 支持，通常使用者只能通过 React DevTools 查看；
2.  缺乏对异步操作的处理；
3.  性能上的弱势

安装 Redux ToolKit（以下简称RTK），考虑到我们通常使用YYL初始化项目，只需安装 RTK：

`yarn add @reduxjs/toolkit react-redux` 

创建文件：/src/store/index.ts ，配置全局Store ，以常用的用户信息为例：

```
import { configureStore } from '@reduxjs/toolkit'
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux'
import { userSlice } from './user/userSlice' // 稍后创建
...
const store = configureStore({
 reducer: {
 user: userSlice.reducer
 },
 devTools: process.env.NODE_ENV !== 'production'  // 不让生产环境调试
})
export default store
// ⚠️ 注意这里：我们使用 ReturnType 和 typeof 来获得全局 state 的类型，供其他组件调用的使用使用
export type RootState = ReturnType<typeof store.getState>
```

然后，在 /src/store/index.ts 中导出关键 hooks，以便于我们在业务组件调用：

```
// 利用 typeof 推断出 dispatch 的类型
export type AppDispatch = typeof store.dispatch 
// 以下两行，都是为了让 TypeScript 能够推断出状态的类型；往后，我们会在整个应用中重复使用这两个成员
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector
export const useAppDispatch = () => useDispatch<AppDispatch>()
```

还需要做的一点是，需要在我们应用顶层（实际上可以自行选择需要的任何节点上）挂在 store

```
import { Provider } from 'react-redux'
import store from '../../store
const Main = () => (
 <Provider store={store}>
 <Router>
 <App />
 </Router>
 </Provider>
)
```

在 RTK中，很方便通过 “slice” 划分状态。一个 “slice“ 可以包含：

* name
* state
* reducers （莫约对应 Vuex 中的 mutations）
* ...

首先创建 /src/store/user/userSlice.ts ， 定义初始化状态：

```
 import { Data } from 'src/service/models/UserInfoResult' // 通过 Tagee-CLI 生成
interface State {
 userInfo: Data
 userRole: number
}
const initialState: State = {
 userInfo: undefined,
 userRole: 0
}
```

创建 slice：

```
export const userSlice = createSlice({
 name: 'user',
 initialState,
 reducers: {
 setUserInfo: (state, action: PayloadAction<Data>) => {
 state.userInfo = action.payload
 },
 setUserRole: (state, action: PayloadAction<number>) => {
 state.userRole = action.payload
 }
 },
})
// 导出actions，所以我们在业务组件中可以 导出然后 dispatch 
export const { setUserInfo, setUserRole } = userSlice.actions
export default userSlice.reducer
```

比如，在业务组件中：

```
import { setUserRole } from 'src/store/user/userSlice
import { useAppDispatch } from '../../store'
const App = () => {
 const dispatch = useAppDispatch()
 return (
 <Button onClick={() => {
 dispatch(setUserRole(1)) // 调用。
 }}>Click</Button>
 )
}
```

然而，通常 userRole 和 userInfo 都是通过异步接口获取的，这时候怎么办。一个标准的做法是使用“thunk”，RTK支持创建 thunk 。

```
import { createAsyncThunk } from '@reduxjs/toolkit'
export const fetchUserInfo = createAsyncThunk('user/fetchUserInfo', async () => {
 const response = (await fetcher.get(paths.user.userInfo)) as UserInfoResult
 return response.data
})
export const userSlice = createSlice({
 // ...
 extraReducers: (builder) => {
 builder.addCase(fetchUserInfo.fulfilled, (state, action) => {
 state.userInfo = action.payload
 })
 builder.addCase(fetchUserRole.fulfilled, (state, action) => {
 state.userRole = action.payload
 })
 }
```

thunk 的一个能力是支持异步的actions，例子：

```
useEffect(() => {
 const doLoginStaff = async () => {
 const { header } = await onHeaderReady()
 header({ target: '#h' })
 return isLoggedIn()
 }
 const initialize = async () => {
 const isLogged = await doLoginStaff()
 if (!isLogged) {
 login()
 } else {
 await dispatch(fetchUserRole())
 await dispatch(fetchUserInfo())
 setInitialized(true) // 需要拉完关健数据才显示对应的UI
 }
 }
 initialize()
}, [dispatch])
```

那么，我们在 Vuex 中常用到的 Getters \(computed state\) 怎么实现呢？ 既然 getters只是一些计算的函数，所以理所当然地，我们可以在 Redux 中定义可复用的纯函数：

创建 /src/store/user/selectors.ts ：

```
import { RootState } from '..'
export const isAnchor = (s: RootState) => [5, 6, 7].includes(s.user.userRole)
export const isGuild = (s: RootState) => [1, 2, 3].includes(s.user.userRole)` 
```

在业务组件中：

```
import { isGuild } from 'src/store/user/selectors'
const App = () => {
 const isUserGuild = useAppSelector(isGuild)
 ...
```

