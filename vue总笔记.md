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




