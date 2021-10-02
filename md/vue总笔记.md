###VUE
#####零碎知识点
发邮件（不常用）
```html
      <a href="mailto:1879153421@qq.com">
```
`"aaA".toLowerCase()`返回`"aaa"`,字符串变小写
dom元素.outerHTML可把dom对象变成字符串格式
```js
// 容器的position是否改动过
  if (getComputedStyle(container).position === "static") {
    container.style.position = "relative";
  }
```
事件只注册一次
```js
div.addEventListener(
      "transitionend",
      function() {
        div.remove();
        // 运行回调函数
        options.callback && options.callback();
      },
      { once: true }//加这个
    );
```
获取当前时间
```js
var a = new Date().toLocaleTimeString();
    console.log(a)//下午9:16:31
```
onresize 事件会在窗口或框架被调整大小时发生。事件名字`resize`
dom元素.getBoundingClientRect(),可获得dom元素在浏览器中的位置
设置img.dataset.role = "loading";=>img[data-role=loading]
str.padStart(2, "0");如果字符串不满两位就在前面加一个"0"
- 密码默认填充
```html
<input
          type="password"
          v-model="formData.loginPwd"
          autocomplete="new-password"
        />
```





##第一个vue应用
```html
<!-- 模板 -> vue -> 真实的dom -->
    <div id="app">
      <h1>标题：商品管理系统</h1>
      <ul>
        <!-- 用到for最好用一个自定义属性绑定一个唯一的值（一定） -->
        <li v-for="(item, i) in products" :key="item.id">
          商品名称：{{item.name}}，商品库存:
          <button @click="changeStock(item, item.stock - 1)">-</button>
          {{item.stock?item.stock:'无货'}}
          <button @click="changeStock(item, item.stock + 1)">+</button>
          <button @click="remove(i)">删除</button>
        </li>
      </ul>
      <p>总库存: {{total}}</p>
    </div>
    <script src="./vue.min.js"></script>
    <script>
      var vm = new Vue({
        el: "#app",
        data: {
          // 页面中要使用的数据
          products: [
            { id: 1, name: "iphone", stock: 0 },
            { id: 2, name: "xiaomi", stock: 6 },
            { id: 3, name: "huawei", stock: 7 },
          ],
        },
        computed: {//这里面是计算，与方法运用不一样
          total() {
            return this.products.reduce((a, b) => a + b.stock, 0);
          },
        },
        methods: {//这里面是方法
          changeStock(product, newStock) {
            if (newStock < 0) {
              newStock = 0;
            }
            product.stock = newStock; // 数据变化
          },

          remove(index) {
            this.products.splice(index, 1);
          },
        },
      });
    </script>
```
```js
// 创建一个vue实例
      var vm = new Vue({
        data: {
          title: "Hello World",
          author: "袁",
        },
        template: `<div>
          <h1>第一个vue应用：{{title}}</h1>
          <p>作者：{{author}}</p>
        </div>`,
        render(h) {//根据模板生成render()
          console.log("render");
          return h("div", [
            h("h1", `第一个vue应用：${this.title}`),
            h("p", `作者：${this.author}`),
          ]);
        },
      });
      vm.$mount("#app");
```
`v-band:`绑定属性可简写成`:`
绑定style属性是一个对象
绑定class时会合并class值
```js
:style="{
      width: size + 'px',
      height: size + 'px',
    }"
```
```html
<div id="app">
      <img v-bind:src="image" alt="" />
      <img :src="image" alt="" />
      <!-- 两个一样 -->
    </div>

    <script src="./vue.min.js"></script>
    <script>
      var vm = new Vue({
        data: {
          image:
            "https://dss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2342083191,4139347491&fm=26&gp=0.jpg",
        },
      });
      vm.$mount("#app");//嵌入#app
    </script>
```
###组件
```js
var MyButton = {
            data(){
                return {
                    count:0,
                }
            },
            template:`
            <div>
            <button @click="count++">你已经点击了{{count}}次</button>
               <h1>{{title1}}</h1> 
               <h1>{{title2}}</h1> 
            </div>
            `,
            props:['title1','title2'],
        }
       // 全局注册了一个组件
      // Vue.component("MyButton", MyButton);
        var vm = new Vue({
            components:{
                MyButton,
            },
            //传参
            template: `<div>
                <MyButton title1="标题一" title2="标题二" />
                </div>
            `
        });
        vm.$mount('#app');
```
###搭建工程
vscode安装插件`Vetur`,直接default.vue可自动生成结构
安装vue-cli:`npm install -g @vue/cli`
> vue-cli: https://cli.vuejs.org/zh/

创建一个工程`vue create lby-project`
浏览器安装插件vue devtools 需要科学上网

####计算属性
```html
    <button @click="fullName = '姬成'">修改姓名为姬成</button>
```
```js
computed: {
    fullName: {
      get() {//取值时会调用
        console.log("fullname called");
        return this.firstName + " " + this.lastName + Date.now();
      },
      set(val) {//赋值时会调用 这里val为姬成
        console.log("正在设置全名", val);
        this.firstName = val[0];
        this.lastName = val.substr(1);
      },
    },
  },
```
###事件组件
v-for  v-if  v-else-if v-else  v-show
v-if="true" 等于true则显示，为false则不显示，直接没有dom节点
v-show="true" 等于true则显示，为false则不显示，只是dispaly设为none
`:class="{abc:okk>1}"`后面为true则添加这个class，为false则不加
组件里抛出一个事件
```js
// 抛出一个事件
  this.$emit("pageChange", newPage);
  //pageChange是事件名 后面的newPage是参数
```
注册事件
```js
@pageChange="handlePageChange($event)"
//handlePageChange是一个方法 参数$event是固定的，不能能变
```

###优化工程结构
对单个vue文件进行运行监测
如何测试组件效果？
https://cli.vuejs.org/zh/guide/prototyping.html
安装插件`npm install -g @vue/cli-service-global`
调用方法时没有参数可直接这样写,handleLoaded是方法名字
`@load="handleLoaded"`
###插槽
```html
<div class="layout-container">
  <header>
    <!-- 我们希望把页头放这里，提供插槽，名为header -->
    <slot name="header"></slot>
  </header>
  <main>
    <!-- 我们希望把主要内容放这里，提供插槽，名为default -->
    <slot></slot>
  </main>
  <footer>
    <!-- 我们希望把页脚放这里，提供插槽，名为footer -->
    <slot name="footer"></slot>
  </footer>
</div>
```
父级引用
```html
<BaseLayout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>
<!-- 默认也可以不写v-slot -->
  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  <template v-slot:default>
    
  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</BaseLayout>
```
v-slot:right简写`<template #right>`
###路由
加载特定组件  详情请见相关课件
安装插件`npm i vue-router`
在src文件下新建一个router文件夹
  里面有一个index.js文件和一个routers.js文件
  router.js里面的文件
  ```js
import Home from "@/views/Home";
import About from "@/views/About";
import Blog from "@/views/Blog";
import Project from "@/views/Project";
import Message from "@/views/Message";

export default [
  { name: "Home", path: "/", component: Home },
  { name: "About", path: "/about", component: About },
  { name: "Blog", path: "/blog", component: Blog },
  { name: "Project", path: "/project", component: Project },
  { name: "Message", path: "/message", component: Message },
];
  ```
  index.js里面的文件
  ```js
import Vue from "vue";
import VueRouter from "vue-router";
import routes from "./routes";
Vue.use(VueRouter); // 使用一个vue插件

const router = new VueRouter({
  // 配置
  routes, // 路由匹配规则
  mode: "history",//按照path路径匹配,匹配方式
});
export default router;

  ```
在main.js里面引入router/index.js
```js
import Vue from 'vue';
import './styles/global.less';
import App from './App.vue';
import router from './router';

new Vue({
  router,
  render: h => h(App),
}).$mount('#app')
```
然后就可以根据不同的路径加载不同的组件，
组件加载在router内置标签`<RouterView />`里面

内置`<RouterLink />`标签相当于a标签,可实现不刷新页面切换组件
属性to相当于href ，to哪个切换相应的组件
exact为true,就是加入class值exact-active-class,为false则class没有这个值
切换到哪个组件那个对应的routerlink标签加入active-class类名
active-class和exact-active-class属性值均是代替自身类名
```html
<template>
  <nav class="menu-container">
    <RouterLink
      v-for="(item, i) in items"
      :exact="item.exact"
      :key="i"
      active-class="selected"
      exact-active-class=""
      :to="{ name: item.name }"
    >
      <div class="icon">
        <Icon :type="item.icon" />
      </div>
      <span>{{ item.title }}</span>
    </RouterLink>
  </nav>
</template>

<script>
import Icon from "@/components/Icon";
export default {
  components: {
    Icon,
  },
  data() {
    return {
      items: [
        {
          name: "Home",
          title: "首页",
          icon: "home",
          exact: true,
        },
        {
          name: "Blog",
          title: "文章",
          icon: "blog",
          exact: false, //没有exact-active-class类名
        },
        {
          name: "About",
          title: "关于我",
          icon: "about",
          exact: true,
        },
        {
          name: "Project",
          title: "项目&效果",
          icon: "code",
          exact: true,
        },
        {
          name: "Message",
          title: "留言板",
          icon: "chat",
          exact: true,
        },
      ],
    };
  },
 
};
</script>
```
###ref
运用一个组件
```html
<div>
    <!-- 写在标签上可得到dom元素 -->
    <p ref="para">some paragraph</p>
    <!-- ChildComp是子组件 -->
    <ChildComp ref="comp" />
    <button @click="handleClick">查看所有引用</button>
  </div>
```
在父组件中
```js
      console.log(this.$refs.comp);
```
可得到子组件中的vue实例，里面有各种子组件中的数据、方法等，可以改变
在标签上  得到一个dom元素
```js
      console.log(this.$refs.para);
      //<p>some paragraph</p>
```
传入组件和属性可得到组件的dom元素
```js
function(comp, props) {
  const vm = new Vue({
    render: (h) => h(comp, { props }),
  });
  vm.$mount();
  return vm.$el;//得到一个dom元素
}
```
文件名`name.module.css`或`name.module.less`直接运用css module
向实例中注入成员       mian.js
```js
import {showMessage} from './utils';
Vue.prototype.$showMessage = showMessage;
new Vue({
  router,
  render: h => h(App),
}).$mount('#app')
```
###获取远程数据
设置mockjs响应时间间隔
```js
import './banner.js';
import Mock from 'mockjs';
Mock.setup({
    timeout:"1000-2000",
})
```
请求数据下载axios ` npm i axios` 这个常用
在src/api/request.js 新建文件
```js
import axios from "axios";
import showMessage from "../utils/showMessage";

const ins = axios.create(); // 创建一个axios的实例
ins.interceptors.response.use(function(resp) {
  //resp为请求到的数据
  if (resp.data.code !== 0) {
    showMessage({
      content: resp.data.msg,
      type: "error",
      duration: 1500,
    });
    return null;
  }
  return resp.data.data;
});

export default ins;
```
若想得到banner数据，新建一个src/api/banner.js 文件
```js
import request from './request';
export async function getBanners(){
    return await request.get('/api/banner');//通过mockjs劫拦
}
```
在main.js中测试
```js
import './mock';
import {getBanners} from "./api/banner";
getBanners().then((res)=>{
  console.log(res)
})
```
这里就可以正常拿到模拟的数据了

vue.config.js配置文件设置服务器代理
访问路径/api开头，服务器就会自动更改地址为target的值，
从服务端拿到数据
```js
// vue-cli的配置文件
module.exports = {
  devServer: {
    proxy: {
      "/api": {
        target: "http://test.my-site.com",
      },
    },
  },
};
```
###组件的生命周期
详情请看课件
beforeCreate 注入  create
beforeMount  生成真实的DOM mount
beforeUpdata  数据变动重新渲染 updata
beforeDestroy 组件销毁 destroy
运用 在相应的阶段会执行相应的函数
```js
export default {
  data(){
    return {
      timer: null
    }
  },
  created(){
    this.timer = setInterval(()=>{
     ... 
    }, 1000)
  },
  destroyed(){
    clearInterval(this.timer);               
  }
}
```
###自定义指令
```js
import loadingUrl from "@/assets/loading.svg";
import styles from "./loading.module.less";
// 得到el中loading效果的img元素
function getLoadingImage(el) {
  return el.querySelector("img[data-role=loading]");
}

function createLoadingImg() {
  const img = document.createElement("img");
  img.dataset.role = "loading";
  img.src = loadingUrl;
  img.className = styles.loading;
  return img;
}

// 导出指令的配置对象
export default function(el, binding) {
  // 根据 binding.value 的值，决定创建或删除img元素
  const curImg = getLoadingImage(el);
  if (binding.value) {
    if (!curImg) {
      const img = createLoadingImg();
      el.appendChild(img);
    }
  } else {
    if (curImg) {
      curImg.remove();
    }
  }
}
```
main.js里面全局导入
```js
import vLoading from "./directives/loading";
Vue.directive("loading", vLoading);
```
滑轮滚动事件`@wheel="handleWheel"`
tansitionend事件`@transitionend="handleTransitionend"`
###组件混入
详情见课件
就多了一个配置`mixins: [common]`
###组件递归
```js
props: {
    // [ {name:"xxx", isSelect:true, children:[ {name:"yyy", isSelect: false} ] } ]
    list: {
      type: Array,
      default: () => [],
      //默认是数组或对象要写个函数
    },
  },
```
加一个name，自己用自己
```html
<template>
  <ul class="right-list-container">
    <li v-for="(item, i) in list" :key="i">
      <span @click="handleClick(item)" :class="{ active: item.isSelect }">
        {{ item.name }}
      </span>
      <!-- 显示当前组件 -->
      <RightList :list="item.children" @select="handleClick" />
    </li>
  </ul>
</template>

<script>
export default {
  name: "RightList",
  props: {
    // [ {name:"xxx", isSelect:true, children:[ {name:"yyy", isSelect: false} ] } ]
    list: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    handleClick(item) {
      this.$emit("select", item);
    },
  },
};
</script>
```
###路由进阶
详情请见开发文章列表页-part2
`  {name:"CategoryBlog",path:"/blog/cate/:categoryId",component:Blog},`
如果URL是location:8080/blog/cate/2   
那么categoryId的值就为2
- 设置 http://localhost:8080/blog/cate/3?page=1&limit=10
```js
handlePageChange(newPager){
        const query = {
          page:newPager,
          limit:this.routeInfo.limit
        }
        if(this.routeInfo.categoryId==-1){
          this.$router.push({
            name:"Blog",
            query
          })
        }else{
          this.$router.push({
            name:"CategoryBlog",
            query,
            params:{
              categoryId:this.routeInfo.categoryId
            }
          })
        }
    }
```
- 获取
```js
routeInfo() {
      const categoryId = +this.$route.params.categoryId||-1;
      const limit = +this.$route.query.limit||10;
      const page = +this.$route.query.page||1;
      return {
        page,
        limit,
        categoryId,
      };
    },
```
watch监控   发生改变就执行
```js
watch:{
      async $route(){
          this.isLoading = true;
          this.$refs.container.scrollTop = 0;
          this.data = await this.fetchData();
          this.isLoading =false;
      }
  }
```
```html
<RouterLink :to="{
    name:'BlogDetail',
    params:{
    id:item.id,
            }
    }">
    <img :src="item.thumb" :alt="item.title" :title="item.title" />
</RouterLink>
```
####插入html代码
v-html   下面这个div里面再写东西就无效了
```html
<div v-html="blog.htmlContent" class="markdown-body"></div>
```
###父级向子级传递数据的三种方法`（this.$listeners）`
- 第一种 通过$emit回调
```js
this.$emit("click", this.count, (err) => {//课件有详情
        // 该函数传给父组件，让父组件决定什么时候执行
        this.isLoading = false;
        this.error = err;
      });
```
- 第二种 props传函数
```js
props: {
    click: Function,
  },
  methods: {
    async handleClick() {
        const err = await this.click(this.count);
        this.isLoading = false;
        this.error = err;
    },
  },
};
```
- 第三种 通过$listeners直接获取父级事件函数
```js
const err = await this.$listeners.click(this.count);//子级
//this.$listeners是 { event1: handleEvent1, event2: handleEvent2 }
```
父级事件
```html
  <LoadingButton @click="handleClick" />
```
- 扩展
子级抛出事件`$emit("submit")`=>上一级`v-on="$listeners"`=>再上以级
直接调用`@submit="handleSubmit"`
###表单v-model
输入改变数据
```html
<input :value="data" @input="data=$event.target.value" />
<!-- 等同于 -->
<input v-model="data" />
```
v-model.trim="formData.loginId"  输入空格无效果
v-model.lazy="formData.introduce" 取消聚焦才改变data
```js
formData: {
        loginId: "abc",  //text
        loginPwd: "",   //password
        loves: ["sports", "movie", "music", "other"],
                //checkbox 选哪个数组里有哪个
        sex: "male", //radio  单选
        age: 18,   //number
        introduce: "我是一只小鸭子呀咿呀咿呀哟\n嘎嘎", //textarea
        job: "1",//select option v-model加在select上面
        remember: false,//单个checkbox选为true，不选则false
      },
```
###事件总线
新建一个文件夹 eventBus.js
```js
// const listeners = {};
// // 事件总线
// export default {
//   // 监听某一个事件
//   $on(eventName, handler) {
//     if (!listeners[eventName]) {
//       listeners[eventName] = new Set();
//     }
//     listeners[eventName].add(handler);
//   },
//   // 取消监听
//   $off(eventName, handler) {
//     if (!listeners[eventName]) {
//       return;
//     }
//     listeners[eventName].delete(handler);
//   },
//   // 触发事件
//   $emit(eventName, ...args) {
//     if (!listeners[eventName]) {
//       return;
//     }
//     for (const handler of listeners[eventName]) {
//       handler(...args);
//     }
//   },
// };
import Vue from "vue";
export default new Vue({});
//import Vue from "vue";   这个方便
//Vue.prototype.$bus=new Vue({});
//在任何地方都能使用this.$bus.$on("mainScroll",fn);
```
使用事件总线
```js
// 测试事件总线
import eventBus from "./eventBus";
function handler1(data) {
  console.log("handler1", data);
}
function handler2(data) {
  console.log("handler2", data);
}
eventBus.$on("event1", handler1);
eventBus.$on("event1", handler2);
eventBus.$on("event2", handler1);
eventBus.$emit('event1',"diyici");//执行事件
```
###数据共享
安装vuex库
store.js
```js
// 数据仓库模块
import Vuex from "vuex";
import Vue from "vue";
Vue.use(Vuex); // 应用vuex插件

function delay(duration) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, duration);
  });
}

const store = new Vuex.Store({
  // 仓库配置对象
  state: {
    count: 0, // 默认值为0
  },
  mutations: {
    increase(state) {
      state.count++;
    },
    decrease(state) {
      state.count--;
    },
    // payload：负荷  负载
    power(state, payload) {
      state.count **= payload;
    },
  },
  actions: {//异步改变
    async asyncIncrease(ctx) {
      await delay(1000);
      ctx.commit("increase");
    },
    async asyncDecrease(ctx) {
      await delay(1000);
      ctx.commit("decrease");
    },
    async asyncPower(ctx, payload) {
      await delay(1000);
      ctx.commit("power", payload);
    },
  },
});
window.store = store;
export default store;
```
main.js
```js
import Vue from "vue";
import App from "./App.vue";
import "./style.css";
import store from "./store";
new Vue({
  store,
  render: (h) => h(App),
}).$mount("#app");
```
使用
```js
  this.$store.commit("decrease");
  this.$store.dispatch("asyncIncrease");//更改异步
```
###登录页面可能用到的知识点
```js
const router = new VueRouter({
  routes,
  mode: "history",
});

router.beforeEach((to, from, next) => {
  // 每当导航切换时（包含刷新页面的第一次），该函数会运行
  // from：之前的路由对象  (this.$route)
  // to: 即将进入的路由对象 (this.$route)
  // next: 确认导航的一个函数  调用该函数（无参），就会直接进入to， 调用该函数（传入参数），根据传入参数进入新的导航
  if (to.meta.auth) {
    // 需要鉴权，进入鉴权流程
    const status = store.getters["loginUser/status"];
    if (status === "loading") {
      // 加载中，无法确定是否已经登录
      next({
        path: "/loading",
        query: {
          returnurl: to.fullPath,
        },
      });
    } 
  } 
});
```
watch函数可传三个参数，第一是监听数值变化，
第二个函数第一个参数是变化的新值，第二个参数是没变化之前的值
第三个参数相当于配置
运行watch函数的返回值是取消监听
```js
export default {
  created(){
    this.unWatch = this.$store.watch(
      ()=> this.$store.getters["loginUser/status"],
      (status)=>{
        if(status!=="loading"){
          this.$router.push(this.$route.query.returnUrl||"/home")
          .catch(()=>{})
        }
      },
      {
        immediate:true
      }
    )
  },
  destroyed(){
    this.unWatch()
  }
}
```
vuex里面的mapState
首先要引入  import { mapState } from "vuex";
```js
  computed: mapState("loginUser", ["loading"]),
```
等同于
```js
computed:{
    loading(){
    return this.$store.state.loginUser.loading
  },
}
```
vuex里面的mapGetters
mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：
```js
computed{
    ...mapGetters("loginUser", ["status"]),
}
```
相当于
```js
computed{
  status(){
    return store中的getters下的state执行
  }
}
```
###打包结果优化分析
在vue.config.js文件里面
```js
configureWebpack:{
  //这里面是webpack配置
  plugins:[new BundleAnalyzerPlugin()]
  //利用webpack-bundle-analyzer可查看
  //打包结果的分析
}
```
- CDN（常见的库可从就近服务器加载，较快）
```js
//vue.config.js配置webpack 不要打包这些
configureWebpack:{
  externals:{
            vue:'Vue',
            vuex:'Vuex',
            asios:'asios',
            "vue-router":'VueRouter'
        }
}
//public/index.html 手动引入

<% if(NODE_ENV="production"){ %>
      <script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.12/vue.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/vuex/3.5.1/vuex.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/vue-router/3.4.7/vue-router.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/axios/0.21.0/axios.min.js"></script>
    <% } %>

// store/index.js
import Vue from "vue";
import {Store,install} from "vuex";
if(!window.vuex){
    install(Vue);
}
//router/index.js
import Vue from "vue";
import VueRouter from "vue-router";
if (!window.VueRouter) {
  Vue.use(VueRouter); // 使用一个vue插件
}
```
- 启用现代模式
打包出两个结果 
  大包 兼容老浏览器用户 
  小包 适用于现代浏览器
`vue-cli-service build --modern`
- 页面分包
```js
//router.js
{
    name: "Home",
    path: "/",
    component: () => import(/* webpackChunkName: "home" */ "@/views/Home"),
    meta: { title: "首页" }
  },
```
- 优化首屏相应
```html
<div id="app">
  <img src="loading.gif" />
</div>
```
###异步组件
应用
在组件加载之前给用户一点提示
npm i nprogress
引入两个
```js
import "nprogress/nprogress.css";
import { start, done, configure } from "nprogress";
//执行start开始进度
//done结束进度
//配置
configure({
  trickleSpeed: 20,
  showSpinner: false,
});
```




