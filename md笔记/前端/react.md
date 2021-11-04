



1. npx是什么？
   1. 局部安装
   2. 可以通过`npx`来调用一些局部方法:`npx mocha`



# redux

## redux基础流程

大多数的应用都会使用 middleware 或 enhancer 来拓展 Redux store 的功能。**（注：middleware 很常见，enhancer 不太常见）** middleware 拓展了 Redux `dispatch` 函数的功能；enhancer 拓展了 Redux store 的功能。

![image-20210401095034689](https://tva1.sinaimg.cn/large/008eGmZEgy1gp407pxr4tj30ay03kt8r.jpg)

index.js:

![image-20210401095111877](https://tva1.sinaimg.cn/large/008eGmZEgy1gp408bcm4hj315m0emdip.jpg)



reducer.js

![image-20210401095153977](https://tva1.sinaimg.cn/large/008eGmZEgy1gp4091vwu4j31qi0p2tf7.jpg)

### 组件内的state来自store

`const [loginFlag, setLoginFlag] = useState(store.getState().loginFlag);`

### 组件内事件发起action:

```js
 const loginAction ={
                type:'login',
                payload:true
            }
store.dispatch(loginAction)
```

1. reducer中接收action，整体返回一个新的state

   ```js
   export default (state=defaultState,action)=>{
       if (action.type === 'login'){
           let obj ={...state};
           obj.loginFlag =action.payload;
           return  obj;
       }
       return state
   }
   ```

   

### 组件中订阅store的改变

```js
  store.subscribe(()=>{
            setLoginFlag(store.getState().loginFlag)
        })
```



### combineReducer拆分redux中的数据

https://redux.js.org/api/combinereducers







## React-redux







## ActionType

在上面流程中用到两次action type 可以统一抽取出来。

```js
// ActionType.js
export const LOGIN_ACTION =`login`
```



```js
      const loginAction ={
                type:LOGIN_ACTION,
                payload:true
            }
            store.dispatch(loginAction)
```



## 将所有action的创建放到ActionCreator中

```js
import {LOGIN_ACTION} from "./ActionType";

const getLoginAction=(value)=>({
    type:LOGIN_ACTION,
    payload:value,
})

export {getLoginAction}
```

```js
  const loginAction =getLoginAction(true);
  store.dispatch(loginAction)
```







## redux-thunk

> 可以创建异步函数的action

### redux中配置

```js
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers/index';
const store = createStore(rootReducer, applyMiddleware(thunk));
```



### 在actionCreator中返回一个函数。

```js
const getLoginAction=(value)=>{
    return ()=>{
        axios.post('http://localhost:3001/api/login',{
            ...value
        })
            .then(res=>{
                console.log(res)
            })
            .catch(err=>console.log(err))
    }
}
```



### dispatch

```js
   const action =  getLoginAction(values);
   store.dispatch(action)
```





##  redux-saga

redux-thunk和redux-saga 都是redux的中间件。

1. redux本身的action 只能是一个对象形式
2. redux-thunk中的action可以变为一个异步函数。将异步请求放置在action中。（微调）
3. redux-saga 是在redux的基础上截获store.dispatch，然后将其做一定处理，最后再传递给store。（单独拆分）



### 配置redux-saga:

```js
import {createStore,applyMiddleware} from "redux";
import createSagaMiddleware from 'redux-saga'
import mysaga from "./saga";
const sagaMiddleware =createSagaMiddleware();
const store =createStore(
    reducer,
    applyMiddleware(sagaMiddleware)
);
sagaMiddleware.run(mysaga);
```



### 生成action：

```js
import {LOGIN_ACTION} from "./ActionType";
const getLoginAction=(value)=>({
   type:LOGIN_ACTION,
   payload:value
})
export {getLoginAction}
```

### 组件中dispatch action

```js
 const action =getLoginAction(values);
 store.dispatch(action);
```

### saga.js中截获dispatch，并进行处理，通过`put`放行。

```js
import { call, put, takeEvery, takeLatest } from 'redux-saga/effects'
import axios from "axios";
import {LOGIN_ACTION} from "./ActionType";
function* fetchLoginState(values){
    try{
      const res = yield axios.post('http://localhost:3001/api/login',{
            ...values
        })
        // put(res)
        console.log(res)
    }catch (e) {
        console.log(e)
    }
}
function* mysaga() {
   yield takeEvery(LOGIN_ACTION,fetchLoginState)
}
export default mysaga
```



# immutable.js

> 确保store中的state是不可变对象。





# react

### 父子组件传值



<img src="https://tva1.sinaimg.cn/large/008eGmZEgy1gog2utajrrj30z60dcgn5.jpg" alt="image-20210311170815003" style="zoom: 67%;" />

这样，子组件中在调用handler(xxx)的时候，父组件会收到xxx。



### 父组件调用子组件方法 && 子组件调用父组件方法

![image-20210329104831901](/Users/ljun/Library/Application Support/typora-user-images/image-20210329104831901.png)











# react-router

## 全局的layout实现

### 1.router.js

<img src="https://tva1.sinaimg.cn/large/008eGmZEgy1gp568s7buaj30u00xf789.jpg" alt="image-20210402100444639" style="zoom:50%;" />



### 2.router.config.js

<img src="https://tva1.sinaimg.cn/large/008eGmZEgy1gp56ax0708j30z30u0104.jpg" alt="image-20210402100650511" style="zoom:67%;" />



### 3.layout中判断是哪一个组件

![image-20210402100835421](https://tva1.sinaimg.cn/large/008eGmZEgy1gp56cqibwbj30te0zegq3.jpg)



### 4.app.js中

![image-20210402100900579](https://tva1.sinaimg.cn/large/008eGmZEgy1gp56d5g9i7j30u60dgwfs.jpg)





## 命令式导航

1.

```js
import history from 'history/browser';
   history.push({
            pathname:'/'
        })
 history.go();
```



2. app.js中没有usehistory()

```js
   const history =createHashHistory();
    console.log(history);
```

