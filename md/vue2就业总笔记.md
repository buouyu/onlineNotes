##其他知识点
try ,catch,finally
```js
try {
      this.content = await this.contentPromise;
      this.error = null;
    } catch (err) {
      this.content = null;
      this.error = err;
    } finally {
      this.isLoading = false;
    }
```
显示特定组件
```html
      <component :is="comps[curIndex]"></component>
```
```js
import Comp1 from './components/Comp1';
import Comp2 from './components/Comp2';
import Comp3 from './components/Comp3';
export default {
  data() {
    return {
      comps: Object.freeze([Comp1, Comp2, Comp3]),
      curIndex: 0,
    };
  },
};
```


### VUE过滤器
参见官网文档：https://cn.vuejs.org/v2/guide/filters.html

### 作用域插槽

参见官网文档：https://cn.vuejs.org/v2/guide/components-slots.html#%E4%BD%9C%E7%94%A8%E5%9F%9F%E6%8F%92%E6%A7%BD

属性：

- `$slots`：用于访问父组件传递的普通插槽中的vnode
- `$scopedSlots`：用于访问父组件传递的所有用于生成vnode的函数（包括默认插槽在内）

###veu组件过渡和动画
```html
<transition
    :name="`image-${direction}`"
    enter-active-class="image-enter-active"
    leave-active-class="image-leave-active"
    >
      <img :src="curImage" alt="" class="image" :key="curIndex"/>
</transition>
//如果direction为next 则有两个类名
// .image-next-enter  进入的最初状态
//.image-next-leave-to 离开的最末状态
//.image-enter-active 进入过渡
//.image-leave-active 离开过渡
```
```css
.image-next-enter{
  opacity: 0;
  transform: translateX(200px);
}
.image-enter-active,
.image-leave-active
{
  transition: 0.5s;
}
.image-next-leave-to{
  opacity: 0;
  transform: translateX(-200px);
}
.image-prev-enter{
  opacity: 0;
  transform: translateX(-200px);
}
.image-prev-leave-to{
  opacity: 0;
  transform: translateX(200px);
}
```
列表组
```html
<transition-group tag="ul" class="todo-container" name="todo" appear>
      <li v-for="item in todos" :key="item.id" class="todo">
        <span>{{item.content}}</span>
        <button @click="finishTodo(item)">over</button>
      </li>
    </transition-group>
```
```css
.todo-enter{
  opacity: 0;
  transform: translateX(100%);
}
.todo-leave-to{
  opacity: 0;
  transform: translateX(-100%);
}
.todo-leave-active{
  position: absolute;
}
```
###优化
- 使用冻结对象
静态数据，没有响应式，改变数据毫无意义
```js
data() {
    return {
      normalDatas: [],
      freezeDatas: [],
    };
  },
  methods:{
      loadFrozenDatas() {
      this.freezeDatas = Object.freeze(this.getDatas());
      console.log("freezeDatas", this.freezeDatas);
    },
  }
```
- 函数式组件
意味它无状态 (没有响应式数据)，也没有实例 (没有 this 上下文)
```html
<template functional>
  <h1>NormalComp: {{ props.count }}</h1>
</template>

<script>
export default {
  functional: true,
  props: {
    count: Number,
  },
};
</script>

<style></style>
```
- 使用计算属性
如果模板中某个数据会使用多次，并且该数据是通过计算得到的，使用计算属性以缓存它们
- 非实时绑定的表单项

当使用v-model绑定一个表单项时，当用户改变表单项的状态时，也会随之改变数据，从而导致vue发生重渲染（rerender），这会带来一些性能的开销。

特别是当用户改变表单项时，页面有一些动画正在进行中，由于JS执行线程和浏览器渲染线程是互斥的，最终会导致动画出现卡顿。

我们可以通过使用lazy或不使用v-model的方式解决该问题，但要注意，这样可能会导致在某一个时间段内数据和表单项的值是不一致的。

- 保持对象引用稳定

在绝大部分情况下，`vue`触发`rerender`的时机是其依赖的数据发生**变化**

若数据没有发生变化，哪怕给数据重新赋值了，`vue`也是不会做出任何处理的

下面是`vue`判断数据**没有变化**的源码

```js
// value 为旧值， newVal 为新值
if (newVal === value || (newVal !== newVal && value !== value)) {
  return
}
```

因此，如果需要，只要能保证组件的依赖数据不发生变化，组件就不会重新渲染。

对于原始数据类型，保持其值不变即可

对于对象类型，保持其引用不变即可

从另一方面来说，由于可以通过保持属性引用稳定来避免子组件的重渲染，那么我们应该细分组件来尽量避免多余的渲染

- 使用v-show替代v-if

对于频繁切换显示状态的元素，使用v-show可以保证虚拟dom树的稳定，避免频繁的新增和删除元素，特别是对于那些内部包含大量dom元素的节点，这一点极其重要

关键字：频繁切换显示状态、内部包含大量dom元素
- 延迟加载
根据浏览器的渲染帧加载元素
```js
//一开始卸载mixins里面的文件
export default function(maxFrameCount) {
  return {
    data() {
      return {
        frameCount: 0,
      };
    },
    mounted() {
      const refreshFrameCount = () => {
        requestAnimationFrame(() => {
          this.frameCount++;
          if (this.frameCount < maxFrameCount) {
            refreshFrameCount();
          }
        });
      };
      refreshFrameCount();
    },
    methods: {
      defer(showInFrameCount) {
        return this.frameCount >= showInFrameCount;
      },
    },
  };
}
```
```html
<template>
  <div class="container">
    <div class="block" v-for="n in 21" v-if="defer(n)">
      <heavy-comp></heavy-comp>
    </div>
  </div>
</template>
<script>
import HeavyComp from "./components/HeavyComp.vue";
import defer from "./mixin/defer";
export default {
  mixins: [defer(21)],
  components: { HeavyComp },
};
</script>
```
- keep-alive
缓存组件,保存状态，include和exclude属性
另外它还提供了max属性，通过它可以设置最大缓存数
把相对不常用的组件删掉
key数组记录目前缓存的组件key值，如果组件没有指定key值，则会为其自动生成一个唯一的key值
```js
//在keep-alive里面的组件，生成执行activeed,消失执行deactivated
  activated() {
    console.log('comp1 activated');
  },
  deactivated() {
    console.log('comp1 deactivated');
  },
```
```html
<keep-alive :include="$store.state.tabs.pageNames" max="2">
        <router-view></router-view>
</keep-alive>
```
```js
//store里面tabs.js的代码
export default {
  namespaced: true,
  state: {
    pageNames: [], // 选项卡的页面
  },
  mutations: {
    addPage(state, newPageName) {
      if (!state.pageNames.includes(newPageName)) {
        state.pageNames.push(newPageName);
      }
    },
    removePage(state, pageName) {
      const index = state.pageNames.indexOf(pageName);
      if (index >= 0) {
        state.pageNames.splice(index, 1);
      }
    },
  },
};
```
- 长列表优化
常列表加载出来或者下拉的时候可能会出现卡顿，
让她只加载出来特定的部分
要npm i vue-virtual-scroller
[vue-virtual-scroller](https://github.com/Akryum/vue-virtual-scroller)
```html
//items:数组 
//itemSize:显示多少元素
<template>
  <div id="app">
    <RecycleScroller
      :items="items"
      :itemSize="54"
      class="scroller"
      v-slot="{ item }"
    >
      <ListItem :item="item" />
    </RecycleScroller>
  </div>
</template>

<script>
import ListItem from './components/ListItem.vue';
// import RecycleScroller from './components/RecycleScroller';
import { RecycleScroller } from 'vue-virtual-scroller';
import 'vue-virtual-scroller/dist/vue-virtual-scroller.css';
var items = [];
for (var i = 0; i < 10000; i++) {
  items.push({
    id: i + 1,
    count: i + 1,
  });
}
export default {
  components: { ListItem, RecycleScroller },
  data() {
    return {
      items,
    };
  },
};
</script>
```
###vue2其他api
[vue2其他api](https://cn.vuejs.org/v2/api/#errorHandler)
看看文档
###vue-cli 模式和环境变量
vue-cli在打包时，会将process.env.XXX进行替换
关于环境变量如何定义：
1. 首先读取当前机器的环境变量
2. 读取.env文件

详见vue-cli官网[模式和环境变量](https://cli.vuejs.org/zh/guide/mode-and-env.html#%E6%A8%A1%E5%BC%8F%E5%92%8C%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F)

```js
//在.env.development
VUE_APP_SERVERBASE=http://www.test.com
//在.env.production
VUE_APP_SERVERBASE=http://www.duyiservice.com

//在api/request.js
export default function(url, ...args) {
  return fetch(`${process.env.VUE_APP_SERVERBASE}${url}`, ...args);
}
```
###vue-cli 更多配置
所有配置参考：[vue-cli 配置](https://cli.vuejs.org/zh/config/#%E5%85%A8%E5%B1%80-cli-%E9%85%8D%E7%BD%AE)

 ##### vue.config.js

- devServer
- publicPath
- outputDir
- runtimeCompiler
- transpileDependencies
- configureWebpack
- css.requireModuleExtension

##### babel配置

写到项目根目录下的`babel.config.js`中

##### ESLint

ESLint 可以通过 `.eslintrc` 或 `package.json` 中的 `eslintConfig` 字段来配置。

##### postcss

写到`postcss.config.js`

###vue-cli其他命令
- vue ui
导航的模式帮助你创建和管理vue项目
- vue add @vue/router
帮你创建好router的文件模板
然后插入项目中
- vue add @vue/vuex
同理上面



