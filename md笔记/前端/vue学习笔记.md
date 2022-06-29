## vue中的路由传参问题

1. 方法

```js
  router.push({
       path:`/column/${id}`
     })    
```

```js
//router.js
  {
    path:'/column/:id',
    name:'Column',
    component:Column,
  },
   
```

```js
//获取参数
  const id =route.params.id;
```

2. 标签中

```vue
  <router-link :to="{
                name:'article',
                 params:{
                  id:3
                }
              }">
或者
    <router-link :to="{
                path:`article/${id}`,
              }">
```

```js
//router.js
     {
        path: 'article',
        name: 'article',
        component: Article,
      },
```

都是下面这种获取方式

```vue
// article页面中 
route.params.id
```









#### wach的使用

watch一个对象的属性变化。

```js
    watch(() => emailRef.val, (val, preval) => {
      console.log(val);
      console.log(preval);
    });

	watch(a,(val,preval)=>{})
```



#### 自定义组件数据与外部组件的联动(v-model)

1. 外部组件：`<ValidateInput  v-model="emailVal">`

2. 内部组件：也自建一个`v-model`。
3. `watch`内部组件的v-model变化。`context.emit('update:modelValue', newVal);`将监听到的新值emit出去。



#### 子组件触发父组件事件并传值

父组件中：

1. @监听事件=‘本组件的事件’

![image-20201127230904770](https://tva1.sinaimg.cn/large/0081Kckwgy1gl44u0xdjfj311201waal.jpg)

2. 父组件中接收传过来的值。并将父组件事件名return

<img src="https://tva1.sinaimg.cn/large/0081Kckwgy1gl44vwyxmij30um0b4wfk.jpg" alt="image-20201127231056342" style="zoom:50%;" />



子组件中：

1. 定义需要让父组件监听的事件名:

   <img src="https://tva1.sinaimg.cn/large/0081Kckwgy1gl44x5w12zj30ge01qweh.jpg" alt="image-20201127231207728" style="zoom:100%;" />



2. 在setup中触发父组件监听的事件

<img src="https://tva1.sinaimg.cn/large/0081Kckwgy1gl4519vgovj30ru07ijsi.jpg" alt="image-20201127231604990" style="zoom:50%;" />



#### 父组件调用子组件方法

1.父组件

```js
 <ValidateInput ref="EmailChild" >  //ValidateInput是子组件
```

`setup中`

```js
 const EmailChild = ref(null);
 EmailChild.value.validateInput();
```



2. 子组件

```js
 const validateInput = () =>{}
```





#### 使用createApp创建节点

如toast全局组件。

```typescript
import { createApp } from 'vue';
//引入要创建的组件
import Toast from '@/components/Toast.vue';

export type MessageType ='success' | 'failure' | 'warning';
const createMessage = (msgType: MessageType, msg: string, timeout = 2000) => {
  //创建组件实例,并传入原组件中的props属性。
  const toastInstance = createApp(Toast, {
    msg,
    msgType,
  });
  console.log(toastInstance);
  const mountNode = document.createElement('div');
  document.body.appendChild(mountNode);
  toastInstance.mount(mountNode);
};
export default createMessage;

```





## 问题

### 图片src动态赋值

**出现错误情况**：

1. 报错：`index.vue?11e0:4 Uncaught (in promise) TypeError: Cannot read property 'url' of undefined`
2. 在浏览器的前进后退中，前一页面中的组件会到后一页面中显示。

**场景**：进入页面根据id查询该用户信息。给头像赋值。

1. 创建响应式对象用于接收对象信息：`const author = ref<any>({});`
2. 在axios响应中赋值:`author.value = res.data.data.author;`
3. 模板中`<img :src="author.avatar.url">`。会报错。



**解决方案**

不使用`author.avatar.url`对img赋值。而新用一个响应式对象。

1. `const imgSrc = ref('');`
2. `imgSrc.value = author.value.avatar.url;`
3. `<img :src="imgSrc">`

可能是因为author对象嵌套会导致初始为null的情况。









# 传值问题



# [Vue 组件通信的 8 种方式](https://segmentfault.com/a/1190000040390222)

1. 父==>子
   1. 传值：props
   2. 调用方法：
2. 子==>父，传值调用方法一起。
3. 父子双向绑定：

![image-20210812100058994](https://i.loli.net/2021/08/12/4w8jIEtCpYAi9De.png)

其核心就是：

	1.  子组件的值通过父组件的props进行修改。
	2.  子组件的修改触发了父组件的事件，从而父组件中触发了对于传递给子组件props的修改。







# deep

```
::v-deep .item {
   p{
    display: inline-block;
  }
```





# placeholder使用i18

```vue
    :placeholder="$t(`pform.gender`)"
```



# 插件使用

## 插件基础形式

```js
// message/index.js
const Message={};

Message.install =function (Vue,options) {
    Vue.prototype.$toast =(message)=>{
        const Templ =Vue.extend({
            template:`<div class="message">${message}</div>`
        })
        let temp =new Templ().$mount().$el;
        document.body.appendChild(temp);
    }
}

export default Message;
```





<img src="/Users/ljun/Documents/myNotes/前端/C:\Users\msze0088\AppData\Roaming\Typora\typora-user-images\image-20210824103759574.png" alt="image-20210824103759574" style="zoom: 130%;" />











## loading插件

1. loading.js

```js
import Vue from 'vue'
import lmLoading from '@/components/Loading'
const LmLoadingConstructor=Vue.extend(lmLoading)
let instance
const initInstance = () => {
    instance = new LmLoadingConstructor({
        el: document.createElement('div'),
        data(){
            return {
                visible: false,
            }
        }
    })
}
const LmLoading=function (){
    if (!instance) {
        initInstance()
    }
}
LmLoading.show=function (timeout){
    return new Promise((resolve)=>{
        document.querySelector('#app').appendChild(instance.$el)
        instance.visible = true
        if(typeof timeout==='number'){
            let timeNum=setTimeout(()=>{
                clearTimeout(timeNum)
                instance.visible=false
            },timeout)
        }
        resolve(true)
    })
}

LmLoading.hidden=function (){
    instance.visible =false
}

export default LmLoading
```

2. loading.vue

```vue
<template>
  <div class="loading-container" v-if="visible">
	<svg></svg>
  </div>
</template>

<script>
export default {
  name: "Loading"
}
</script>

<style scoped lang="scss">
  .loading-container{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: hsla(0,0%,100%,.9);
    z-index: 10;
    transition: opacity 0.3s;
    svg{
      fill: #42b983;
    }
  }
</style>
```



3. main.js

```js
import LmLoading from "@/utils/loading.js";
Vue.use(LmLoading)
Vue.prototype.$loading =LmLoading;
```



4. http.js中

```js
import LmLoading from "@/utils/loading.js";
LmLoading.show(2000);
```



## 如何实现this.loading.show()这种调用

### 方案一：

```js
//loading.js
import LoadingTemp from './Loading'
import Vue from "vue";
const Loading1 ={};
let loading;
Loading1.install =function (Vue,options) {

}

function initInstance() {
    if (!loading){
        let LoadingTempl =Vue.extend(LoadingTemp);
        loading =new LoadingTempl();
        document.querySelector('#app').appendChild(loading.$mount().$el);
    }
}

Loading1.show =function () {
    if (!loading){
        initInstance();
    }
    loading.visible=true;
}

export default Loading1;
```

```js
//main.js
Vue.prototype.$loading1=Loading1;
```



```vue
Loading1.show(); //方式一
this.$loading1.show(); //f
```





## message插件

在尝试挂在插件时报错：

```
[Vue warn]: You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.
```

vue有两种形式的代码：compiler模式 和runtime模式。

可以通过修改vue.config.js：

```js
    configureWebpack:{
        resolve:{
            alias:{
                'vue$':'vue/dist/vue.esm.js'
            }
        }
    }
```





1. 

```vue
// MessageTemp.vue
<template>
  <transition
    name="fade"
  >
    <div
        v-if="showFlag"
        class="message-container" :class="type"
        :style="{top: top +'px'}"
    >
      <div>
        <i :class='icon'></i>
        {{content}}
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: "MessageTemp",
  props:{
    type:{
      type:String,
      default:'info',
      validate:function (val){
        return ['success','info','warning'].indexOf(val)!==-1;
      }
    },
    content:{
      type:String,
      default: ''
    },
    delay:{
      type:Number,
      default:2000,
    },
    top:{
      type:Number,
      default:0
    }
  },
  data:function () {
      return {
        icon:`el-icon-${this.type}`,
        showFlag:false,
      }
  },
  methods:{
    destroy(cb=null){
      setTimeout(()=>{
        this.showFlag =false;
        cb ? cb():""
      },this.delay)
    }
  }
}
</script>

<style scoped lang="scss">
  .message-container{
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    z-index: 200;
    padding: 20px;
    font-size: 20px;
    min-width: 300px;
    box-sizing: border-box;
    border-radius: 4px;
  }
  .success{
    background-color: #f0f9eb;
    border-color: #e1f3d8;
    color: red;
  }
  .warning{
    background-color: #fef0f0;
    border-color: #fde2e2;
  }
  .fade-enter-active, .fade-leave-active {
    transition: all 1s ease;
  }
  .fade-enter,.fade-leave-to {
    opacity: 0;
  }

</style>
```

2. 

```vue
// index.js
import MessageTemp from "@/components/Message/MessageTemp";
const Message ={};
import Vue from "vue";
// accInstanceCount 用来记录当前出现的message框出现次数。
// activeInstanceCount 用来记录现存的message框次数。
let activeInstanceCount =0;
let accInstanceCount=0;
// install 中会传入Vue 和options参数.
Message.install =function () {
    Vue.prototype.$message=function (option) {
          initInstance(option);
    }
}
const initInstance =(option)=>{
    const MessageTempl =Vue.extend(MessageTemp)
    activeInstanceCount++;
    accInstanceCount++;
    let defaultOption ={
        delay:2000,
        top:(accInstanceCount-1) *80 +40,
    }
    let options = Object.assign({},defaultOption,option);
    let message =new MessageTempl({
        propsData:options
    });
    let messageNode =message.$mount().$el;
    let appNode =document.querySelector('#app');
    appNode.appendChild(messageNode);
    message.showFlag =true;
    message.destroy(()=>{
        activeInstanceCount--;
        activeInstanceCount===0? accInstanceCount=0:'';
    })
}

Message.message =function (option) {
    initInstance(option)
}
export default Message;
```



3. 使用
   1. 在vue组件中：`this.$message()`
   2. 组件外：`Message.message()`





# vue配置代理

```js
// vue.config.js    devServer:{        proxy:{            '*':{                target:config.baseUrl,                ws:true,                changeOrigin:true,            }        }    }
```





# 还原data中对象初始值

```js
  Object.assign(this.currentInfo,this.$options.data().currentInfo);
```





# 国际化使用

## 全局使用vue-i18n

[如何全局使用](https://segmentfault.com/a/1190000015008808)

```
import $i18n from "@/assets/i18n/i18n";content:$i18n.t((`pLogin.success`))
```



## elementUI+自身国际化切换

```js
// cn.js 中文部分const cn = {   test:{       emergency:'急诊',       service:'中文1',       maintance:'中文2',       laser:'中文2',       menu:'中文2',       welcome:'欢迎',       NewScan:'中文1',       DicomViewer:'中文1',       ServiceFunctions:'中文1',       ringOrient:'中文',       batteryPower:'中文',       systemTem:'中文',       HospitalNetWork:'中文',       Navigation:'中文',       forward:'向前',       backward:'向后'   },}export default cn
```



```js
// i18n.jsimport Vue from 'vue'import VueI18n from 'vue-i18n'import en from './langs/en'import cn from './langs/cn'// 引入element-ui中的中英文语言包import enLocale from 'element-ui/lib/locale/lang/en';import zhLocale from 'element-ui/lib/locale/lang/zh-CN';Vue.use(VueI18n)// 合并自身语言和element-ui的语言包。const messages = {    en: Object.assign(en,enLocale),    cn: Object.assign(cn,zhLocale)}const i18n = new VueI18n({    locale: 'en', // 设置默认语言    messages,    silentTranslationWarn: true})export default i18n
```



```js
import i18n from "./assets/i18n/i18n";i18n.locale ='cn'; //切换语言Vue.use(ElementUI,{    i18n:(key, value) => i18n.t(key, value)})
```

![image-20210826111149716](/Users/ljun/Documents/myNotes/前端/C:\Users\msze0088\AppData\Roaming\Typora\typora-user-images\image-20210826111149716.png)











# axios http请求问题

## 请求的拦截重发

[类似问题的博客](https://segmentfault.com/a/1190000020210980)

1. 

```vue
   post(API.patient.getList, {
        "counts": 0,
        "page": 0,
        "pageSize": 0,
        "searchContent": ""
      })
    .then(res => {})


```



2. http.js

```js
function post(url, params) {
    return new Promise((resolve, reject) => {
      return  axios.post(url, paras)
          .then(res=>resolve(res))
          .catch(e=>reject(e))
    })
}
```



3. response拦截器中

   遇到401的请求，就返回一个没有resolve的promise对象。同时将该请求放入一个数组arr中。然后通过一个刷新token的标志位来发起重新回去token的请求。获取到新的token之后就把arr中进行遍历调用。

```js
axios.interceptors.response.use(function (response) {

    if (response.data.access_token) {
        access_token = response.data.access_token;
        localStore.set('access_token', response.data.access_token)
    }
    if (response.data.refresh_token) {
        refresh_token = response.data.refresh_token;
        localStore.set('refresh_token', response.data.refresh_token)
    }
    return response;
}, function (error) {
    if (error.response) {
        switch (error.response.status) {
            case 401:
                console.log('token过期,过期的请求url是' + error.config.url + '准备插入数组中');
                localStore.remove('access_token');
                access_token = '';
                refresh_token = localStore.get('refresh_token');
                if (!freshing_token_flag) {
                    if (refresh_token) {
                        localStore.remove('refresh_token');
                        let params = {};
                        params.client_id = 'msz';
                        params.client_secret = 'msz@12345'
                        params.grant_type = 'refresh_token';
                        params.refresh_token = refresh_token;
                        axios.post(API.login,params)
                            .then(res => {
                                failure_request_arr.forEach(cb => cb());
                                failure_request_arr = [];
                            })
                        freshing_token_flag = true;
                        // 重登录请求
                    } else {
                        // 让用户重新登录
                        Message.message({
                            type: 'warning',
                            content: '登录失效，重新登录'
                        })
                        setTimeout(() => {
                            router.push('/login')
                        }, 1000)
                    }
                }
                //收到401请求，那么就将该请求存到数组中，发起重新获取token请求。
                return new Promise(resolve => {
                    failure_request_arr.push(() => {
                        let storedConfig =JSON.parse(JSON.stringify(error.config));
                        storedConfig.headers.Authorization = `bearer ${access_token}`
                        delete storedConfig.transformRequest;
                        delete storedConfig.transformResponse;
                        return axios.request(storedConfig)
                            .then(res => {
                                console.log(res);
                                resolve(res)
                            })
                    })
                })

            case 400:
                // 只有登录会出现400，将获得的key处理给login页面
                var {error_description: key} = error.response.data;
                return Promise.reject({
                    status: -1,
                    key,
                });

            default:
                Promise.reject(error)
                break;
        }
    }
});
```



# 函数定义以及传参

```js
fn(a,b,c)

function fn(...argarr){ 
    fn1(...argarr)
}

function fn1(...argarr){
    console.log(argarr)
}
```







# vue-router

## hash和history两种方式

```html
<body>
    <a href="#/home">home</a>
    <a href="#/about">about</a>
    <div id="view">

    </div>
<script>
    let view =document.querySelector('#view');
    window.addEventListener('hashchange',cb)
    window.addEventListener('load',cb)
    function cb() {
        let hash =location.hash;
        view.innerHTML =`<span>${hash}</span>`
    }
</script>
</body>
```



```html

```





## 手写vue-router

[博客链接地址](https://juejin.cn/post/6991640600533532679#heading-19)

注意几点：

1. `Vue.use()`做了什么？
   1. 引入了`router-link`和`router-view` 全局注册了组件。
   2. `this.$router` 是`VueRouter`的实例对象。 `this.$route`是当前路由对象。

```js
import VueRouter from 'vue-router'Vue.use（VueRouter）
```

2. 生成`router`实例：

```js
const router = new VueRouter({  mode: "hash",  base: process.env.BASE_URL,  routes,});
```

3. 在main.js中挂载。这样router作为VueRouter的实例对象就挂载到根实例的`$options`上。

```js
new Vue({  router,  render: (h) => h(App),}).$mount("#app");
```





### `Vue.use()`源码



### 实现

1. 为什么要使用插件
2. 为什么要使用全局混入？
   1. 为了让挂载根组件`$options`上的`router`可以在各个子组件中通过`this.$router获取。`



# vue父子组件v-model实现

父组件：

```vue
   <div class="radio-container">        <my-radio v-model="radio" label=1>          选择1        </my-radio>        <my-radio v-model="radio" label=2>          选择2        </my-radio>      </div>
```



子组件：

```vue
<template>    <span class="radio-item"  @click="handleChange">      <span          class="radio-circle"          :class="active ==label ?'is-selected':''"      >      </span>      <input          type="radio"          class="radio_original"      />      <span>         <slot/>      </span>    </span></template><script>export default {  name: "MyRadio",  model:{    prop:'active',    event:'click',  },  props:{    label:{      type:[Number,String],    },    active:[Number,String],  },  methods:{    handleChange(e){      this.$emit('click',this.label)    }  }}</script>
```



父组件中的v-model 通过 子组件的model属性。将props转成子组件中的：

```js
  model:{    prop:'active',    event:'click',  },
```

子组件中对于event的监听触发父组件中的model变化。









# vue3



## setup中 父组件调用子组件

<img src="https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220624094858462.png" alt="image-20220624094858462" style="zoom:67%;" />





#  vite

## vite vue3中使用require 

```js
const getUrl =name=>{
  return new URL(`../../assets/${name}`,import.meta.url).href;
}
{
  src:getUrl('video.mp4')
}
```





