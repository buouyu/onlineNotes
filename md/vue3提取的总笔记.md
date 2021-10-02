# 零碎知识点
```js
//new Date()  返回一个时间对象
//date 是一个时间戳
//date1 是一个字符串 "Wed, 02 Jun 2021 12:43:47 GMT"
const date = new Date().getTime();
const date1 = new Date(date).toGMTString();
//得到当前年份
new Date().getFullYear()
```
vue中的input事件 $event.target.value可获得input标签的value
```html
<input type="text"  name="name" :value="activeStu.name" @input="edit('name', $event.target.value)"  />
```
继承组件，把组件当作函数来调用
```js
import Vue from 'vue';
import toast from './index.vue';
//./index.vue是一个组件
// webpack.merge 参数合并（toast 参数，提前内置
const Toast = Vue.extend(toast);
// new Vue()

export default function ({ msg, type }, duration = 3000) {
  const app = new Toast({
    el: document.createElement('div'),
    data() {
      return {
        msg,
        type
      }
    }
  });
  if (duration < 2000) {
    duration = 2000;
  }
  document.body.appendChild(app.$el);
  setTimeout(() => {
    app.flag = false;
  }, duration-500)
  setTimeout(() => {
    document.body.removeChild(app.$el);
  }, duration)
}


//在main.js
import toast from './components/toast'
Vue.prototype.$toast=toast;
```
编辑后让其自动更新
```js
// 1.硬性重新加载
        // window.location.reload();
        // 2. 当前组件重新拉取数据 正常。
        // this.getStuList();
        // 3. 本地直接合并对象，减少请求
        Object.assign(this.activeStu, this.stu);
```




## 入门

### 搭建工程
1.`vue create project` 选vue3 vue-cli 搭建vue3工程 
内部用到webpack有点慢
2.vite构建工具,轻，快
`npm init vite-app project-name`
通过npm 下载最新的vite-app,然后用vite-app搭建proje-name工程
然后再删除vite-app,临时下载，这样保证每次使用的vite-app都是最新的版本
npm i 进入后安装依赖
npm run dev
npm run build
引入.vue文件要加后缀名，除了.js不用加后缀名，其他都加后缀名
### vue3的重大变化

```js
//main.js
import { createApp } from "vue";
import App from "./App.vue";
import "./index.css";
//vue2
// const app = new Vue(options)
// Vue.use()
// app.$mount("#app")
//vue3
// 不存在构造函数Vue
// const app = createApp(App);
// app.mount("#app");
createApp(App).mount("#app");
```
vue3 this指向一个代理对象
vue3组件 可以有多个根节点
支持option API
在复杂组件中用composition API提高组件的可维护性
```html
<template>
  <h1>count:{{ countRef }}</h1>
  <p>
    <button @click="decrease">decrease</button>
    <button @click="increase">increase</button>
  </p>
</template>

<script>
import { ref } from "vue";
function useCount() {
  let countRef = ref(0);
  const increase = () => {
    countRef.value++;
  };
  const decrease = () => {
    countRef.value--;
  };
  return {
    countRef,
    increase,
    decrease,
  };
}
export default {
  setup() {
    // console.log("所有生命周期钩子函数之前调用");
    // console.log(this); // this -> undefined
    // setup中，count是一个对象
    // 实例代理中，count是一个count.value
    //1. 新增
    //2. 修改
    //3. 删除
    return {
      ...useCount(),
    };
  },
};
</script>
```
```js
import {ref,onMounted,onUnmounted,computed} from 'vue';
//onMounted相当于mounted周期函数
//onUnmounted相当于distroyed 
//computed相当于计算属性
onMounted(()=>{
        window.addEventListener('hashchange',onHashChange)
    });
    onUnmounted(()=>{
        window.removeEventListener('hashchange',onHashChange);
    })
    const filteredTodosRef = computed(()=>{
        return filter(todosRef.value,visibilityRef.value);
    })
//get获取
//set设置
const allDoneRef = computed({
        get(){
            return todosRef.value.filter(it=>!it.completed).length==0;
        },
        set(val){
            todosRef.value.forEach(element => {
                element.completed=val;
            });
        }
    })
```
vue中的几个事件
```html
//dblclick 点击两次触发
 <input class="edit" type="text" 
            v-model="item.title"
            @blur="doneEdit(item)"
            @keyup.enter="doneEdit(item)"
            @keyup.escape="cancleEdit(item)"
            />
<label @dblclick="editTitle(item)">{{item.title}}</label>

```

在vue3中除了v-for要自己加key，其他它会自己加唯一key值，例如if else
vue3中v-if的优先级高于v-for

v-model
```html
//子组件
<template>
  <div class="check-editor">
    <div class="check-editor-inner">
      <div
        class="checkbox"
        :class="{ checked: modelValue }"
        @click="handleChecked"
      ></div>
      <input
        type="text"
        class="editor"
        :value="title"
        @input="handleTextChange"
      />
    </div>
  </div>
</template>

<script>
export default {
  //默认的v-model   这个modelValue是固定的
  //titleModifiers也是固定的，加了trim，{trim:true},也可以多加
 props:{
   modelValue:Boolean,
   title:String,
   titleModifiers:{
     default:()=>({})
   }
 },
 setup(props,ctx){
   const handleChecked = ()=>{
     //抛出的事件名注意格式，这个是默认
     ctx.emit('update:modelValue',!props.modelValue)
   }
  const handleTextChange=e=>{
    let value = e.target.value;
    if(props.titleModifiers.trim){
      value=value.trim();
    }
    ctx.emit('update:title',value);
  }
   return {
     handleChecked,
     handleTextChange
   }
 }
};
</script>
//父组件
<CheckEditor
          v-for="item in products"
          :key="item.id"
          v-model="item.sell"
          v-model:title.trim="item.title"
        />

//vue3的key可以直接写在template标签上
<template v-for="(item, index) in sells" :key="item.id">
          <span>{{ index + 1 }}.</span>
          <strong>{{ item.title }}</strong>
</template>
```

####vue-router 
    "vue-router": "^4.0.0-beta.13"

```js
//router/index.js
import { createRouter, createWebHistory } from "vue-router";
import routes from "./routes";

// https://next.router.vuejs.org/guide/migration/
export default createRouter({
  history: createWebHistory(), // mode:"history"
  routes,
});
//router/routes.js
import { getAsyncPage } from "../util";

const Home = getAsyncPage("../views/Home.vue");
const About = getAsyncPage("../views/About.vue");

export default [
  { path: "/", component: Home },
  { path: "/about", component: About },
];
//main.js
import { createApp } from 'vue'
import App from './App.vue'
import router from "./router/index.js";
createApp(App).use(router).mount('#app');
```
####异步页面加载进度条
npm i nprogress
```js
import "nprogress/nprogress.css";//npm i nprogress
import NProgress from "nprogress";
NProgress.configure({
    trickleSpeed:50,//每隔50毫秒动一次
    showSpinner:false,//不显示小圆圈转悠
})
//
NProgress.start()//开始进度
NProgress.done()//结束进度
```

标签加在某个特定标签的里面，在最后
Teleport标签里面的东西可以appendChild body标签
```html
<Teleport to="body">
        <Modal v-if="modalVisible">
          <button @click="modalVisible = false">关闭朦层</button>
        </Modal>
</Teleport>
```
####得到一个异步页面or组件
```js
import { defineAsyncComponent, h } from "vue";
import Error from "../components/Error.vue";
import Loading from "../components/Loading.vue";
//得到异步组件传入组件地址即可，让一个组件变成异步的
export function getAsyncComponent(path){
    return defineAsyncComponent({
        loader:async ()=>{
            await delay();
            if(Math.random()<0.3){
                throw new TypeError();
            }else{
                return import(path);
            }
        },
        loadingComponent:Loading,//加载中的组件
        errorComponent:{//加载错我的组件
            //相当于一个组件的配置，但里面运用的插槽，
            //要用到render 配置h
            render(){
                return h(Error,"出错了！！！")
            }
        }
    })
}

```
### reactiveApi
reactive() 只能传入一个对象，变成响应式，取值的时候不用.value
readonly() 只读，传入代理或对象，变成响应式，取值不用.value
ref() 啥都能传，取值要.value
computed() 传入function 返回{value:....}

剩下的看详细课件。。。。

### composition API
gsap 需要npm安装，从一个值缓慢变换到另一个值
```js
import gsap from "gsap";
// bars.value[i] 中的属性 逐步变化到 barsTarget.value[i]
        gsap.to(bars.value[i], {
          ...barsTarget.value[i],
          duration: 1,
        });
//toRef的props用法
//这里的useGdp(gdpRef,maxSize)
import { toRef } from "vue";
export default {
  props:['gdp'],
  setup(props){
      return {
          ...useGdp(toRef(props,"gdp"),600)
      }
  }
};
```


