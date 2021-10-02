## 其他零碎知识点
```js
const numbers = new Array(30);
//每一位都是undifined
numbers.fill(0);//每一位都是0


```

### 创建工程
yarn create react-app name
运行 yarn start

emmet 插件vscode设置 支持js快速书写dom元素
emmet.includeLanguages:{
    wxml:'html',
    javascript:'javascriptreact'
}
VS Code插件
ESlint  减少无意义的代码
ES7 React/Redux/GraphQL/React-Native snippets  快速书写代码

浏览器插件 React Developer Tools 方便调试

```html
<div id="root"></div>
    <!-- React的核心库，与宿主环境无关 -->
    <script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <!-- 依赖核心库，将核心的功能与页面结合 -->
    <script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <!-- 编译JSX -->
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>

    <script type="text/babel">
        //创建一个span元素
        var span = React.createElement("span", {}, "一个span元素");
        // var span = <span>一个span元素</span>
        //创建一个H1元素
        var h2 = React.createElement("h2", {
            title: "第一个React元素"
        }, "Hello", "World", span);
        // var h1 = <h1 title="第一个React元素">Hello World <span>一个span元素</span></h1>;
        // console.log(h1);
        ReactDOM.render(<h1 title="第一个React元素">Hello World <span>一个span元素</span></h1>, document.getElementById("root"));
    </script>
```

基本语法
```js
// const obj = {
//     a: 1,
//     b: 2
// }
const obj = <span>这是一个span元素</span>;
const arr = [2, null, false, undefined, 3];
const numbers = new Array(30);
numbers.fill(0);
var lis = numbers.map((item, i) => (<li key={i}>{i}</li>));
const div = (
    <div>
        {a} * {b} = {a * b}
        <p>
            {/* 以下不会产生任何输出 */}
            {null}
            {undefined}
            {false}
        </p>
        <p>
            {/* 普通对象无法放置 */}
            {/* {obj} */}
        </p>
        <p>
            {/* React元素对象没问题 */}
            {obj}
        </p>
        <p>
            {arr}
        </p>
        <ul>
            {lis}
        </ul>
    </div>
);
//属性的使用方法，style传对象
const url = "https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2962719555,3613138778&fm=27&gp=0.jpg";
const cls = "image";
const div = (
    <div>
        <img src={url} className={cls} style={{
            marginLeft: "50px",
            width:"200px"
        }} alt="" />
    </div>
);

//解除保护机制，字符串里面的标签可以正常使用
const content = "<h1>afasfasfd</h1><p>阿斯顿法定发送</p>";
const div = (
    <div dangerouslySetInnerHTML={{
        __html: content
    }}>
    </div>
);
//重新渲染
setInterval(() => {
    num++;
    const div = (
        <div title="asdfadf">
            {num}
        </div>
    );
    ReactDOM.render(div, document.getElementById("root"));
}, 1000);
```
### 组件
函数组件，类组件
```js
//函数组件
import React from 'react'
export default function Name(props){
    return (<div>{props.name}</div>)
}
//类组件
import React, { Component } from 'react'
export default class BallList extends Component {

    constructor(props) {
        super(props);
        this.state = {
            ballInfoes: []
        }
        const timer = setInterval(() => {
            var info = {
                left: getRandom(0, document.documentElement.clientWidth - 100),
            };
            this.setState({
                ballInfoes: [...this.state.ballInfoes, info]
            });
        }, 1000);
    }
    render() {
        const balls = this.state.ballInfoes.map((item, i) => <Ball key={i} {...item} />)
        return (
            <>
                {balls}
            </>
        )
    }
}

```
事件函数this指向undefined，但使用箭头函数就可以正常了
### setState 
setState 如果改变状态的代码处于某个HTML元素的事件中，则其是异步的，否则是同步
```js
handleClick = () => {
        this.setState({
            n: this.state.n + 1
        }, () => {
            //状态完成改变之后触发，该回调运行在render之后
            console.log(this.state.n);
        });
    }

//下面的这两个相当于只会执行一个，第二个参数函数this.setState执行完就会执行
        this.setState({
            n:self.state.n+1,
        },()=>{console.log('first');})
        this.setState({
            n:self.state.n+1,
        },()=>{console.log('second');})//first second都能打印出来
//下面这两个两个都会有作用，传的函数的参数代表state的没改变之前的状态
        this.setState(cur => ({
            n: cur.n + 1
        }));

        this.setState(cur => ({
            n: cur.n + 1
        }));

this.setState(cur => {
            //参数prev表示当前的状态
            //该函数的返回结果，会混合（覆盖）掉之前的状态
            //该函数是异步执行
            return {
                n: cur.n + 1
            }
        }, ()=>{
            //所有状态全部更新完成，并且重新渲染后执行
            console.log("state更新完成", this.state.n);
        });
```
### 组件的生命周期
新版React >= 16.0.0
constructor
static getDerivedStateFromProps
            shouldComponentUpdate
            render
componentDidMount   getSnapshotBeforeUpdate
                     componentDidUpdate

```js
    static getDerivedStateFromProps(props, state) {
        console.log("getDerivedStateFromProps");
        return null;//不改变当前状态
        // return { //用新的对象替换掉之前的状态
        //     n: props.n
        // }
    }
    getSnapshotBeforeUpdate = (prevProps, prevState) => {
        console.log("getSnapshotBeforeUpdate");
        return 132;
    }
    componentDidUpdate(prevProps, prevState, snap) {
        //这里的第三个参数snap是getSnapshotBeforeUpdata的返回值
        console.log("componentDidUpdate", snap);
    }
```
![react新版本生命周期](./assets/vue-react/react新版本生命周期.png)
![react旧版本生命周期](./assets/vue-react/react旧版本生命周期.png)
### 传递元素内容
如果给自定义组件传递元素内容，则React会将元素内容作为children属性传递过去。

```html
<Comp content1={<h2>第2组元素内容</h2>} content2={<h2>第3组元素内容</h2>}>
        <h2>第1组元素内容</h2>
    </Comp>
```
### 表单
```js
export default class FormTest extends Component {

    state = {
        loginId: "",
        loginPwd: "",
        sex: "male",
        chooseLoves: [],
        loves: [
            { value: "football", text: "足球" },
            { value: "basetball", text: "篮球" },
            { value: "movie", text: "电影" },
            { value: "music", text: "音乐" },
            { value: "other", text: "其他" },
        ],
        city: "beijing"
    }

    handleChange = e => {
        let val = e.target.value; //读取表单的值
        let name = e.target.name; //读取表单的name属性
        if (e.target.type === "checkbox") {
            //对val进行特殊处理
            if (e.target.checked) {
                val = [...this.state.chooseLoves, val];
            }
            else {
                val = this.state.chooseLoves.filter(it => it !== val);
            }
        }
        // const newPartialState = {};
        // newPartialState[name] = val;
        // this.setState(newPartialState)
        this.setState({
            [name]: val
        })
    }

    /**
     * 获取所有的爱好多选框
     */
    getLoveCheckBoxes() {
        const bs = this.state.loves.map(it => (
            <label key={it.value} >
                <input type="checkbox" name="chooseLoves"
                    value={it.value}
                    checked={this.state.chooseLoves.includes(it.value)}
                    onChange={this.handleChange}
                />
                {it.text}
            </label>
        ));
        return bs;
    }

    render() {
        const bs = this.getLoveCheckBoxes();
        return (
            <div>
                <p>
                    <input type="text"
                        name="loginId"
                        value={this.state.loginId}
                        onChange={this.handleChange} />
                </p>
                <p>
                    <input type="password"
                        name="loginPwd"
                        value={this.state.loginPwd}
                        onChange={this.handleChange} />
                </p>
                <p>
                    <label>
                        <input type="radio" name="sex"
                            value="male"
                            checked={this.state.sex === "male"}
                            onChange={this.handleChange}
                        />
                        男
                    </label>

                    <label>
                        <input type="radio" name="sex"
                            value="female"
                            checked={this.state.sex === "female"}
                            onChange={this.handleChange}
                        />
                        女
                    </label>
                </p>
                <p>
                    {bs}
                </p>
                <p>
                    <select name="city" value={this.state.city}
                        onChange={this.handleChange}
                    >
                        <option value="beijing">北京</option>
                        <option value="shanghai">上海</option>
                        <option value="chengdu">成都</option>
                    </select>
                </p>
                <p>
                    <button onClick={() => {
                        console.log(this.state)
                    }}>获取表单数据</button>
                </p>
            </div>
        )
    }
}

```
### 默认属性和类型检查
```js
//类组件的默认属性
static defaultProps = {
        a: 1,
        b: 2,
        c: 3
    }
or
ClassDefault.defaultProps = {
    a: 1,
    b: 2,
    c: 3
}
//函数组件的默认属性
FuncDefault.defaultProps = {
    a: 1,
    b: 2,
    c: 3
}

//类型检查
//直接引入
import PropTypes from "prop-types";
//先混合属性
    static defaultProps = {
        b: false
    }

    //再调用相应的函数进行验证
    static propTypes = {
        a: PropTypes.number.isRequired,  //a属性必须是一个数字类型,并且必填
        b: PropTypes.bool.isRequired, //b必须是一个bool属性,并且必填
        onClick: PropTypes.func, //onClick必须是一个函数
        c: PropTypes.any, //1. 可以设置必填   2. 阵型保持整齐（所有属性都在该对象中）
        d: PropTypes.node.isRequired, //d必填，而且必须是一个可以渲染的内容，字符串、数字、React元素
        e: PropTypes.element, //e必须是一个React元素
        F: PropTypes.elementType, //F必须是一个组件类型
        g: PropTypes.instanceOf(A), //g必须是A的实例
        sex: PropTypes.oneOf(["男", "女"]), //属性必须是数组当中的一个
        h: PropTypes.arrayOf(PropTypes.number), //数组的每一项必须满足类型要求
        i: PropTypes.objectOf(PropTypes.number), //每一个属性必须满足类型要求
        j: PropTypes.shape({ //属性必须满足该对象的要求
            name: PropTypes.string.isRequired, //name必须是一个字符串，必填
            age: PropTypes.number, //age必须是一个数字
            address: PropTypes.shape({
                province: PropTypes.string,
                city: PropTypes.string
            })
        }),
        k: PropTypes.arrayOf(PropTypes.shape({
            name: PropTypes.string.isRequired,
            age: PropTypes.number.isRequired
        })),
        m: PropTypes.oneOfType([PropTypes.string, PropTypes.number]),
        score: function (props, propName, componentName) {
            console.log(props, propName, componentName);
            const val = props[propName];
            //必填
            if (val === undefined || val === null) {
                return new Error(`invalid prop ${propName} in ${componentName}，${propName} is Required`);
            }
            //该属性必须是一个数字
            if (typeof val !== "number") {
                return new Error(`invalid prop ${propName} in ${componentName}，${propName} is not a number`);
            }
            const err = PropTypes.number.isRequired(props, propName, componentName);
            if(err){
                return err;
            }
            //并且取值范围是0~100
            if (val < 0 || val > 100) {
                return new Error(`invalid prop ${propName} in ${componentName}，${propName} must is between 0 and 100`);
            }
        }
    }
```
### HOC 高阶组件
传入一个组件返回一个组件
1. 不要在render中使用高阶组件
2. 不要在高阶组件内部更改传入的组件
```js
//components
import React from "react"
export class A extends React.Component{
    //不再关注跟该组件不相关的事情
    render(){
        return <h1>A:{this.props.a}</h1>
    }
}

export class B extends React.Component{
    //不再关注跟该组件不相关的事情
    render(){
        return <h1>B:{this.props.b}</h1>
    }
}

//HOC/withLog.js
import React from "react"

/**
 * 高阶组件
 * @param {*} comp 组件
 */
export default function withLog(Comp, str) {
    return class LogWrapper extends React.Component {

        componentDidMount() {
            console.log(`日志：组件${Comp.name}被创建了！${Date.now()}`);
        }

        componentWillUnmount() {
            console.log(`日志：组件${Comp.name}被销毁了！${Date.now()}`);
        }


        render() {
            return (
                <>
                    <h1>{str}</h1>
                    <Comp {...this.props} />
                </>
            )
        }
    }
}
//HOC/withLogin.js
import React from 'react'

export default function withLogin(Comp) {
    return function LoginWrapper(props) {
        if (props.isLogin) {
            return <Comp {...props} />
        }
        return null;
    }
}

//App.js
import React from 'react'
import { A, B } from "./components/Comps";
import withLog from "./HOC/withLog";
import withLogin from "./HOC/withLogin"
let AComp = withLogin(withLog(A, "abc"));
let BComp = withLogin(withLog(B, "Bcd"));
export default class App extends React.Component {
    render() {
        return (
            <div>
                <AComp isLogin a={1} />
                <BComp isLogin b={1} />
                <button onClick={() => {
                    this.setState({})
                }}>点击</button>
            </div>
        )
    }
}
```
### ref
ref不能作用于函数组件
```js
1.字符串 已被摒弃
<input ref="txt" type="text" />
this.refs.txt.focus();
this.refs.compA.method();

2.对象
constructor(props){
        super(props);
        this.txt = React.createRef();
    }

    handleClick = () => {
        this.txt.current.focus();
    }
<input ref={this.txt} type="text" />
3.函数
componentDidMount() {
        console.log("didMount", this.txt);
    }

    getRef = el => {
        console.log("函数被调用了", el);
        this.txt = el;
    }
<input ref={this.getRef} type="text" />

1. componentDidMount的时候会调用该函数
   1. 在componentDidMount事件中可以使用ref
2. 如果ref的值发生了变动（旧的函数被新的函数替代），分别调用旧的函数以及新的函数，时间点出现在componentDidUpdate之前
   1. 旧的函数被调用时，传递null
   2. 新的函数被调用时，传递对象
3. 如果ref所在的组件被卸载，会调用函数

```
### ref转发
forwardRef方法：

1. 参数，传递的是函数组件，不能是类组件，并且，函数组件需要有第二个参数来得到ref
2. 返回值，返回一个新的组件
```js
1.函数组件转发
function A(props, ref) {
    return <h1 ref={ref}>
        组件A
        <span>{props.words}</span>
    </h1>
}

//传递函数组件A，得到一个新组件NewA
const NewA = React.forwardRef(A);

export default class App extends React.Component {

    ARef = React.createRef()

    componentDidMount() {
        console.log(this.ARef.current);
        //指向h1 DOM对象
    }

    render() {
        return (
            <div>
                <NewA ref={this.ARef} words="asfsafasfasfs" />
                {/* this.ARef.current:  h1 */}
            </div>
        )
    }
}
2.类组件转发
import React from 'react'

class A extends React.Component {
    render() {
        return <h1 ref={this.props.abc}>
            组件A
        <span>{this.props.words}</span>
        </h1>
    }
}

const NewA = React.forwardRef((props, ref) => {
    return <A {...props} abc={ref} />
})

export default class App extends React.Component {

    ARef = React.createRef()

    componentDidMount() {
        console.log(this.ARef.current);
        //指向h1DOM元素
    }


    render() {
        return (
            <div>
                <NewA ref={this.ARef} words="asfsafasfasfs" />
                {/* this.ARef.current:  h1 */}
            </div>
        )
    }
}

```
### context上下文
```js
1.旧版本上下文（可以忽略不看）
//父组件创建上下文
static childContextTypes = types

    state = {
        a: 123,
        b: "abc"
    }

    /**
     * 得到上下文中的数据
     */
    getChildContext() {
        console.log("获取新的上下文");
        return {
            a: this.state.a,
            b: this.state.b,
            onChangeA: (newA) => {
                this.setState({
                    a: newA
                })
            }
        }
    }
//子组件使用上下文，函数组件在第二个参数里
static contextTypes = types

    render() {
        return <p>
            ChildB，来自于上下文的数据：a: {this.context.a}, b:{this.context.b}
            <button onClick={() => {
                this.context.onChangeA(this.context.a + 2);
            }}>子组件的按钮，a+2</button>
        </p>
    }
//就近原则获取上下文
2.新版本上下文
const ctx = React.createContext();

//创建上下文
render() {
        return (
            <ctx.Provider value={this.state}>
                <div>
                    <ChildA />

                    <button onClick={() => {
                        this.setState({
                            a: this.state.a + 1
                        })
                    }}>父组件的按钮，a加1</button>
                </div>
            </ctx.Provider>
        )
    }
//使用上下文
<h2>
            <ctx.Consumer>
                {value => <>{value.a}，{value.b}</>}
            </ctx.Consumer>
        </h2>
//类组件也可以使用this.context
```
### PureComponent
减少不必要的组件更新，提高效率
快捷键rpc
```js
import React, { PureComponent } from 'react'
export default class Name extends PureComponent {

}
```
### render props
有时候，某些组件的各种功能及其处理逻辑几乎完全相同，只是显示的界面不一样，建议下面的方式认选其一来解决重复代码的问题（横切关注点）
```js
//一个功能MouseLisener.js
export default class MouseListener extends PureComponent {
    state = {
        x: 0,
        y: 0
    }

    divRef = React.createRef()

    handleMouseMove = e => {
        //更新x和y的值
        const { left, top } = this.divRef.current.getBoundingClientRect();
        const x = e.clientX - left;
        const y = e.clientY - top;
        this.setState({
            x,
            y
        });
    }

    render() {
        return (
            <div ref={this.divRef} className="point" onMouseMove={this.handleMouseMove}>
                {this.props.render ? this.props.render(this.state) : "默认值"}
            </div>
        )
    }
}
//使用
 <MouseListener render={mouse => <>横坐标：{mouse.x}，纵坐标：{mouse.y}</>} />

```
### portal
插槽：将一个React元素渲染到指定的DOM容器中
ReactDOM.createPortal(React元素, 真实的DOM容器)，该函数返回一个React元素
**注意事件冒泡**
1. React中的事件是包装过的
2. 它的事件冒泡是根据虚拟DOM树来冒泡的，与真实的DOM树无关。
```js
import ReactDOM from "react-dom"

function ChildA() {
    return ReactDOM.createPortal(<div className="child-a" style={{
        marginTop: 200
    }}>
        <h1>ChildA</h1>
        <ChildB />
    </div>, document.querySelector(".modal"));
}
```
### 错误边界
错误边界：是一个组件，该组件会捕获到渲染期间（render）子组件发生的错误，并有能力阻止错误继续传播
```js
//写成一个组件，可以捕捉子元素错误
<ErrorBound>
            <Comp1 />
</ErrorBound>
//ErrorBound.js
static getDerivedStateFromError(error) {
        console.log("发生错误了");
        return {
            hasError: true
        }
    }
// 运行时间点：渲染子组件的过程中，发生错误之后，在更新页面之前

    componentDidCatch(error, info) {
        console.log("记录错误信息");
    }
// 运行时间点：渲染子组件的过程中，发生错误，更新页面之后，由于其运行时间点比较靠后，因此不太会在该函数中改变状态

render() {
        // setTimeout(() => {
        //     throw new Error("asfasdfasf");
        // }, 1000);
        if (this.state.hasError) {
            return <h1>出现错误了！</h1>
        }
        return this.props.children
    }
```
**细节**

某些错误，错误边界组件无法捕获
1. 自身的错误
2. 异步的错误
3. 事件中的错误
总结：仅处理渲染子组件期间的同步错误

### React中的事件
1. 给document注册事件
2. 通过React的事件中阻止事件冒泡，无法阻止真实的DOM事件冒泡
3. 可以通过nativeEvent.stopImmediatePropagation()，阻止document上剩余事件的执行
4. 在事件处理程序中，不要异步的使用事件对象，如果一定要使用，需要调用persist函数

### 渲染原理
见详情
### 工具
见详情

# hook
### hook简介
见详情
### State Hook
类组件强制刷新
```js
 <button onClick={()=>{
                    //不会运行shouldComponentUpdate
                    this.forceUpdate();//强制重新渲染
}}>强制刷新</button>
```
函数组件强制渲染
```js
export default function App() {
    console.log("App Render");
    const [, forceUpdate] = useState({});
    return <div>
        <p >
            <button onClick={() => {
                forceUpdate({});
            }}>强制刷新</button>
        </p>
    </div>
}
```
函数组件使用状态
```js
import React, { useState } from 'react'
//可使用多个
const [data, setData] = useState({
        x: 1,
        y: 2
    });
const [visible, setVisible] = useState(true);
const [n, setN] = useState(0);
```
同时使用多个设置的情况
```js
export default function App() {
    console.log("App render")
    const [n, setN] = useState(0); //使用一个状态，该状态的默认值是0
    return <div>
        <button onClick={() => {
            // setN(n - 1);
            // setN(n - 1);
            setN(prevN => prevN - 1); //传入的函数，在事件完成之后统一运行
            setN(prevN => prevN - 1);
        }}>-</button>
        <span>{n}</span>
        <button onClick={() => {
            // setN(n + 1) //不会立即改变，事件运行完成之后一起改变
            // setN(n + 1) //此时，n的值仍然是0

            setN(prevN => prevN + 1); //传入的函数，在事件完成之后统一运行
            setN(prevN => prevN + 1);
        }}>+</button>
    </div>
}
```
**注意的细节**
1. useState最好写到函数的起始位置，便于阅读
2. useState严禁出现在代码块（判断、循环）中
3. useState返回的函数（数组的第二项），引用不变（节约内存空间）
4. 使用函数改变数据，若数据和之前的数据完全相等（使用Object.is比较），不会导致重新渲染，以达到优化效率的目的。
5. 使用函数改变数据，传入的值不会和原来的数据进行合并，而是直接替换。
6. 如果要实现强制刷新组件
   1. 类组件：使用forceUpdate函数
   2. 函数组件：使用一个空对象的useState
7. **如果某些状态之间没有必然的联系，应该分化为不同的状态，而不要合并成一个对象**
8. 和类组件的状态一样，函数组件中改变状态可能是异步的（在DOM事件中），多个状态变化会合并以提高效率，此时，不能信任之前的状态，而应该使用回调函数的方式改变状态。如果状态变化要使用到之前的状态，尽量传递函数。
### Effect Hook
```js
import React, { useState, useEffect } from 'react'
 useEffect(() => {
        console.log("副作用函数，仅挂载时运行一次")
        return () => {
            console.log("清理函数，仅卸载时运行一次")
        };
    }, []); //使用空数组作为依赖项，则副作用函数仅在挂载的时候运行
```
**细节**
1. 副作用函数的运行时间点，是在页面完成真实的UI渲染之后。因此它的执行是异步的，并且不会阻塞浏览器
   1. 与类组件中componentDidMount和componentDidUpdate的区别
   2. componentDidMount和componentDidUpdate，更改了真实DOM，但是用户还没有看到UI更新，同步的。
   3. useEffect中的副作用函数，更改了真实DOM，并且用户已经看到了UI更新，异步的。
2. 每个函数组件中，可以多次使用useEffect，但不要放入判断或循环等代码块中。
3. useEffect中的副作用函数，可以有返回值，返回值必须是一个函数，该函数叫做清理函数
   1. 该函数运行时间点，在每次运行副作用函数之前
   2. 首次渲染组件不会运行
   3. 组件被销毁时一定会运行
4. useEffect函数，可以传递第二个参数
   1. 第二个参数是一个数组
   2. 数组中记录该副作用的依赖数据
   3. 当组件重新渲染后，只有依赖数据与上一次不一样的时，才会执行副作用
   4. 所以，当传递了依赖数据之后，如果数据没有发生变化
      1. 副作用函数仅在第一次渲染后运行
      2. 清理函数仅在卸载组件后运行
5. 副作用函数中，如果使用了函数上下文中的变量，则由于闭包的影响，会导致副作用函数中变量不会实时变化。
6. 副作用函数在每次注册时，会覆盖掉之前的副作用函数，因此，尽量保持副作用函数稳定，否则控制起来会比较复杂。
### 自定义Hook
```js
//myHooks/usePageStudents.js
import { useEffect, useState } from "react"
import { getStudents } from "../services/student"

/**
 * 根据页码和页容量获取学生数据，得到一个响应结果
 * 并且，当页码和页容量变化时，将重新获取学生数据
 */
export default function useAllStudents(page = 1, limit = 10) {
    const [resp, setResp] = useState()
    useEffect(() => {
        (async () => {
            const resp = await getStudents(page, limit);
            setResp(resp);
        })();
    }, [page, limit])
    return resp;
}
//Test.js
function Test() {
    const [page, setPage] = useState(1)
    const resp = usePageStudents(page, 10);
    if (resp) {
        const list = resp.findByPage.map(it => <li key={it.id}>{it.name}</li>)
        return (
           
        )
    }
    return null;
}
/* eslint "react-hooks/exhaustive-deps": "off" */
加这行注释可以取消eslint产生的useEffect第二个参数数组没有元素的警告
```
### Reducer Hook
```js
//myHooks/useReducer.js
import { useState } from "react"
/**
 * 通用的useReducer函数
 * @param {function} reducer reducer函数，标准格式
 * @param {any} initialState 初始状态
 * @param {function} initFunc 用于计算初始值的函数
 */
export default function useReducer(reducer, initialState, initFunc) {
    const [state, setState] = useState(initFunc? initFunc(initialState): initialState)

    function dispatch(action) {
        const newState = reducer(state, action)
        console.log(`日志：n的值  ${state}->${newState}`)
        setState(newState);
    }

    return [state, dispatch];
}
//Test.js
import React from "react"
import useReducer from "./useReducer"

/**
 * 该函数，根据当前的数据，已经action，生成一个新的数据
 * @param {*} state 
 * @param {*} action 
 */
function reducer(state, action) {
    switch (action.type) {
        case "increase":
            return state + 1;
        case "decrease":
            if (state === 0) {
                return 0;
            }
            return state - 1;
        default:
            return state;
    }
}

export default function App() {
    const [n, dispatch] = useReducer(reducer, 10, (args) => {
        console.log(args)
        return 100
    });
    return (
        <div>
            <button onClick={() => {
                dispatch({ type: "decrease" })
            }}>-</button>
            <span>{n}</span>
            <button onClick={() => {
                dispatch({ type: "increase" })
            }}>+</button>
        </div>
    )
}
```
### Context Hook
```js
import React, { useContext } from 'react'

const ctx = React.createContext();

// function Test() {
//     return <ctx.Consumer>
//         {value => <h1>Test，上下文的值：{value}</h1>}
//     </ctx.Consumer>
// }
function Test() {
    const value = useContext(ctx);
    return <h1>Test，上下文的值：{value}</h1>
}

export default function App() {
    return (
        <div>
            <ctx.Provider value="abc">
                <Test />
            </ctx.Provider>
        </div>
    )
}
```
### Callback Hook
```js
import React, { useState, useCallback } from 'react'

function Parent() {
    console.log("Parent Render")
    const [txt, setTxt] = useState(1)
    const [n, setN] = useState(0)
    const handleClick = useCallback(() => {
        setTxt(txt + 1)
    }, [txt])
//useCallback返回一个函数，二参记录依赖项
    return (
        <div>
            {/* 函数的地址每次渲染都发生了变化，导致了子组件跟着重新渲染，若子组件是经过优化的组件，则可能导致优化失效 */}
            <Test text={txt} onClick={() => {
                setTxt(Math.random());
            }} />
            {/* 下面这个用法改变input中的数据，不会导致重新渲染 */}
            <Test text={txt} onClick={handleClick} />
            <input type="number"
                value={n}
                onChange={e => {
                    setN(parseInt(e.target.value))
                }}
            />
        </div>
    )
}
```
### Memo Hook
跟useCallback类似，return一个函数
```js
import React, { useState, useMemo } from 'react'

const handleClick = useMemo(() => {
        return () => {
            setTxt(txt + 1)
        };
    }, [txt])
//handleClick是一个地址固定的函数

const list = useMemo(() => {
        const list = [];
        for (let i = range.min; i <= range.max; i++) {
            list.push(<Item key={i} value={i}></Item>)
        }
        return list;
    }, [range.min, range.max])

<ul>
    {list}
</ul>
```
### Ref Hook
```js
import React, { useState, useRef } from 'react'

function Test(props){
    const inpRef = useRef();
    <input ref={inpRef} type="text" />
            <button onClick={() => {
                console.log(inpRef.current.value)
    }}>得到input的值</button>
}

export default function App() {
    const [n, setN] = useState(10)
    const nRef = useRef(n); // {current:10}
    useEffect(() => {
        const timer = setInterval(() => {
            nRef.current--;
            setN(nRef.current);
            if(nRef.current === 0){
                clearInterval(timer);
            }
        }, 1000)
        return () => {
            clearInterval(timer);
        }
    }, [])
    return (
        <div>
            <h1>{n}</h1>
        </div>
    )
}
```
### ImperativeHandle Hook
调用函数组件里面的方法
```js
import React, { useRef, useImperativeHandle } from 'react'

function Test(props, ref) {
    useImperativeHandle(ref, () => {
        //如果不给依赖项，则每次运行函数组件都会调用该方法
        //如果使用了依赖项，则第一次调用后，会进行缓存，只有依赖项发生变化时才会重新调用函数
        //相当于给 ref.current = 1
        return {
            method(){
                console.log("Test Component Called")
            }
        }
    }, [])
    return <h1>Test Component</h1>
}

const TestWrapper = React.forwardRef(Test)//ref转发

// class Test extends React.Component {

//     method() {
//         console.log("Test method called");
//     }

//     render() {
//         return <h1>Test Component</h1>
//     }
// }

export default function App() {
    // const [, forceUpdate] = useState({})
    const testRef = useRef();
    return (
        <div>
            <TestWrapper ref={testRef} />
            <button onClick={() => {
                testRef.current.method();
                // console.log(testRef)
                // forceUpdate({})
            }}>点击调用Test组件的method方法</button>
        </div>
    )
}

```
### LayoutEffect Hook

useEffect：浏览器渲染完成后，用户看到新的渲染结果之后
useLayoutEffectHook：完成了DOM改动，但还没有呈现给用户

应该尽量使用useEffect，因为它不会导致渲染阻塞，如果出现了问题，再考虑使用useLayoutEffectHook
```js
import React, { useState, useLayoutEffect, useRef } from 'react'

export default function App() {
    const [n, setN] = useState(0)
    const h1Ref = useRef();
    useLayoutEffect(() => {
        h1Ref.current.innerText = Math.random().toFixed(2);
    })
    return (
        <div>
            <h1 ref={h1Ref}>{n}</h1>
            <button onClick={() => {
                setN(n + 1)
            }}>+</button>
        </div>
    )
}
```
### DebugValue Hook

useDebugValue：用于将自定义Hook的关联数据显示到调试栏

如果创建的自定义Hook通用性比较高，可以选择使用useDebugValue方便调试
```js
import React, { useState, useEffect, useDebugValue } from 'react'

function useTest(){
    const [students, ] = useState([])
    useDebugValue("学生集合")
    return students;
}

export default function App() {
    useState(0)
    useState("abc")
    useEffect(() => {
        console.log("effect")
    }, [])
    useTest();
    return (
        <div>
        </div>
    )
}
```
### React动画（一）
yarn add react-transition-group
[react-transition-group官网](https://reactcommunity.org/react-transition-group/transition)

```js
import React, { useState } from "react";
import { Transition } from 'react-transition-group';
import "./index.css"
const duration = 5000;

export default function App() {
    const [inProp, setInProp] = useState(true);
    return (
        <div>
            //in,timeout必传属性,in为true，state为entered
            <Transition in={inProp} timeout={duration}
                addEndListener={(node, done) => {
            //node为下面的div的DOM对象
            //done函数提前调用，则下面的state提起变为entered or exited
                    node.addEventListener("transitionend", () => {
                        console.log("过渡结束了")
                    }, { once: true })
                }}
            >
                {state => {
            //state 为 entered entering exited exiting
                    return <div className={state}>
                        I'm a fade Transition!
                    </div>
                }}

            </Transition>
            <button onClick={() => setInProp(!inProp)}>
                Click to Toggle
            </button>
        </div>
    );
}
```
### React动画 - CSSTransition
# 后面的没写

# router
### React Router
1. react-router：路由核心库，包含诸多和路由功能相关的核心代码
2. react-router-dom：利用路由核心库，结合实际的页面，实现跟页面路由密切相关的功能

如果是在页面中实现路由，需要安装react-router-dom库

```js
import { BrowserRouter as Router, Route, Switch } from "react-router-dom"

 <Router>
            <Switch>
                <Route path="/a/b" component={B} />
                <Route path="/a" exact component={A}>
                    {() => {
                        return <div>
                            <h1 style={{ color: "red" }}>必定会看到的内容</h1>
                            <p>adfasdfasdf</p>
                        </div>
                    }}
                </Route>
                <Route component={C} />
            </Switch>
        </Router>

<Router>
            <Route path="/a" exact component={A}>
                <h1 style={{ color: "red" }}>必定会看到的内容</h1>
                <p>adfasdfasdf</p>
            </Route>
            <Route path="/a/b" component={B} />
            <Route component={C} />
</Router>
```


# redux
### redux
```js
import { createStore } from "redux";

//假设仓库中仅存放了一个数字，该数字的变化可能是+1或-1
//约定action的格式：{type:"操作类型", payload:附加数据}

/**
 * reducer本质上就是一个普通函数
 * @param state 之前仓库中的状态（数据）
 * @param action 描述要作什么的对象
 */
function reducer(state, action) {
    //返回一个新的状态
    if (action.type === "increase") {
        return state + 1;
    }
    else if (action.type === "decrease") {
        return state - 1;
    }
    return state;//如果是一个无效的操作类型，数据不变
}

window.store = createStore(reducer, 10);

const action = {
    type: "increase"
}
console.log(window.store.getState()); //得到仓库中当前的数据
window.store.dispatch(action); //向仓库分发action
console.log(window.store.getState()); //得到仓库中当前的数据
```
### action
```js
//redux/action/action-type.js
export const INCREASE = Symbol("increase");
export const DECREASE = Symbol("decrease");
export const SET = Symbol("set");
//redux/action/number-action.js
import * as actionTypes from "./action-type"
/**
 * 得到一个用于增加数字操作的action
 */
export function getIncreaseAction() {
    return {
        type: actionTypes.INCREASE
    };
}

export function getDecreaseAction() {
    return {
        type: actionTypes.DECREASE
    }
}

export function getSetAction(newNumber) {
    return {
        type: actionTypes.SET,
        payload: newNumber
    }
}


//redux/index.js
import { createStore, bindActionCreators } from "redux";
import * as actionTypes from "./action/action-type"
import * as numberActions from "./action/number-action"

//假设仓库中仅存放了一个数字，该数字的变化可能是+1或-1
//约定action的格式：{type:"操作类型", payload:附加数据}
/**
 * reducer本质上就是一个普通函数
 * @param state 之前仓库中的状态（数据）
 * @param action 描述要作什么的对象
 */
function reducer(state, action) {
    //返回一个新的状态
    if (action.type === actionTypes.INCREASE) {
        return state + 1;
    }
    else if (action.type === actionTypes.DECREASE) {
        return state - 1;
    }
    else if (action.type === actionTypes.SET) {
        return action.payload;
    }
    return state;//如果是一个无效的操作类型，数据不变
}

const store = createStore(reducer, 10);

//第一个参数，是action创建函数合并的对象，第二个参数是仓库的dispatch函数
//得到一个新的对象，新对象中的属性名与第一个参数的属性名一致
const boundActions = bindActionCreators(numberActions, store.dispatch);

console.log(store.getState()); //得到仓库中当前的数据

//得到一个increase action，并直接分发
boundActions.getIncreaseAction(); //向仓库分发action

console.log(store.getState()); //得到仓库中当前的数据

boundActions.getSetAction(3);

console.log(store.getState()); //得到仓库中当前的数据
```
### reducer
```js
//redux/action/usersAction.js
export const ADDUSER = Symbol("add-user");
export const DELETEUSER = Symbol("delete-user");
export const UPDATEUSER = Symbol("update-user");

export const createAddUserAction = (user) => ({
    type: ADDUSER,
    payload: user
})

export const createDeleteUserAction = (id) => ({
    type: DELETEUSER,
    payload: id
})

export const createUpdateUserAction = (id, newUserData) => ({
    type: DELETEUSER,
    payload: {
        ...newUserData,
        id
    }
})
//redux/action/loginUserAction.js
export const SETLOGINUSERTYPE = Symbol("set-login-user");

/**
 * 设置登录用户的action
 * @param {*} user 
 */
export function createSetLoginUserAction(user) {
    return {
        type: SETLOGINUSERTYPE,
        payload: user
    

//redux/reducer/user.js
import * as usersAction from "../action/usersAction"
import uuid from "uuid"

const initialState = [
    { id: uuid(), name: "用户1", age: 11 },
    { id: uuid(), name: "用户2", age: 12 }
];

export default (state = initialState, { type, payload }) => {
    switch (type) {
        case usersAction.ADDUSER:
            return [...state, payload];
        case usersAction.DELETEUSER:
            return state.filter(it => it.id !== payload);
        case usersAction.UPDATEUSER:
            return state.map(it => it.id === payload.id ? {...it, ...payload} : it);
        default:
            return state
    }
}
//redux/reducer/loginUser.js
import { SETLOGINUSERTYPE } from "../action/loginUserAction"

const initialState = null

export default (state = initialState, { type, payload }) => {
    switch (type) {
        case SETLOGINUSERTYPE:
            return payload
        default:
            return state
    }
}
//redux/reducer/index.js
import loginUserReducer from "./loginUser"
import usersReducer from './users'
import { combineReducers } from "redux"

// export default (state = {}, action) => {
//     const newState = {
//         loginUser: loginUserReducer(state.loginUser, action),
//         users: usersReducer(state.users, action)
//     };
//     return newState;
// }
// export default combineReducers({
//     loginUser,
//     users
// })
export default combineReducers({
    loginUser: loginUserReducer,
    users: usersReducer
})
```
### store
```js
import { createStore } from "redux";
import reducer from "./reducer"
import { createAddUserAction, createDeleteUserAction } from "./action/usersAction"


const store = createStore(reducer);

console.log(store)

const unListen = store.subscribe(() => {
    console.log(store.getState());
})
//监听 分发一个action会执行传递的函数

store.dispatch(createAddUserAction({
    id: 3,
    name: "abc",
    age: 10
}));

unListen(); //取消监听

store.dispatch(createDeleteUserAction(3));
```
### 中间件
中间件：类似于插件，可以在不影响原本功能、并且不改动原本代码的基础上，对其功能进行增强。在Redux中，中间件主要用于增强dispatch函数。
```js
import { createStore, bindActionCreators, applyMiddleware } from "redux";
import reducer from "./reducer"
import { createAddUserAction, createDeleteUserAction } from "./action/usersAction"

const logger1 = store => next => action => {
    console.log("中间件1")
    console.log("旧数据", store.getState());
    console.log("action", action);
    next(action);
    console.log("新数据", store.getState());
    console.log("")
}

const logger2 = store => next => action => {
    console.log("中间件2")
    console.log("旧数据", store.getState());
    console.log("action", action);
    next(action);
    console.log("新数据", store.getState());
    console.log("")
}

//应用中间件，方式1：
// const store = createStore(reducer, applyMiddleware(logger1, logger2));

//方式2：
const store = applyMiddleware(logger1, logger2)(createStore)(reducer)

const actionCreators = {
    addUser: createAddUserAction,
    deleteUser: createDeleteUserAction
}

const actions = bindActionCreators(actionCreators, store.dispatch)

actions.addUser({ id: 3, name: "abc", age: 111 })
actions.deleteUser(3)
```
# react中间件
### redux-logger
需要安装
```js
import logger from "redux-logger"
const store = createStore(reducer, applyMiddleware(logger));
```

### redux-thunk
需下载安装
```js
import { createStore, applyMiddleware } from "redux";
import reducer from "./reducer"
import logger from "redux-logger"
import thunk from "redux-thunk"

export default createStore(reducer,
//thunk要放在logger前面
    applyMiddleware(
        thunk,
        logger
    )
);
//store/action/userAction.js
export  function fetchAllStus(){
    return async function(dispatch){
        dispatch(createIsLoadingAction(true))
        const datas = await getAllStudents();
        // console.log(datas)
        const actinos=createSetUserAction(datas)
        dispatch(actinos)
        dispatch(createIsLoadingAction(false))
    }
}

//store/index.js
//运行一个副作用的函数
store.dispatch(fetchAllStus())
console.log(store.getState())
//fetchAllStus执行完，会执行then里面的函数
store.dispatch(fetchAllStus()).then(()=>{
    console.log('加载完成')
})

```
### redux-promise

```js
//store/action/searchUser.js
export async function fetchStudents(condition) {
    return {
        type: actionTypes.setStudentsAndTotal,
        payload: searchStudents(condition).then(resp =>{
            console.log(resp.datas,resp.cont)
            return {
                datas:resp.datas,
                total:resp.cont
            }
        })
    }
}
//store/index.js
import logger from "redux-logger"
import promise from "redux-promise"
//需要安装
const store = createStore(reducer,
    applyMiddleware(promise, logger)
)
const action =fetchStudents(store.getState().students.condition)
store.dispatch(action)
```
### redux-saga
安装react-saga
```js
//store/saga/index.js
import {take,all} from 'redux-saga/effects'
import countorTask from './countorTask';
import studentTask from './studentTask'
import { SETNUM } from '../action/numAction'

export default function* saga(){
    yield take(SETNUM)
    yield all([countorTask(),studentTask()])
    console.log('all结束')
}
//store/saga/countorTask.js
import { takeEvery } from "@redux-saga/core/effects";
import { INCREASENUM } from "../action/numAction";
import { DECREASENUM } from "../action/numAction";
function* increaseNum(){
    console.log('increase')
}
function* decreaseNum(){
    console.log('decrease')
}
export default function* countorTask(){
    yield takeEvery(INCREASENUM,increaseNum);
    yield takeEvery(DECREASENUM,decreaseNum)
    console.log('正在监听，decrease，increase')
}
//store/index.js
import { createStore,applyMiddleware } from "redux";
import reducer from "./reducer";
// import { fetchStudens } from "./action/student/searchResult";
import logger from 'redux-logger'
import { createSetNumAction,createIncreaseNumAction,createDecreaseNumAction } from "./action/numAction";
import rootSaga from './saga'
import createSagaMiddleware from "@redux-saga/core";
const sagaMid = createSagaMiddleware()

const store = createStore(reducer,applyMiddleware(sagaMid,logger))
sagaMid.run(rootSaga)
```
- take指令：【阻塞】监听某个action，如果action发生了，则会进行下一步处理，take指令仅监听一次。yield得到的是完整的action对象
- all指令：【阻塞】该函数传入一个数组，数组中放入生成器，saga会等待所有的生成器全部完成后才会进一步处理
- takeEvery指令：不断的监听某个action，当某个action到达之后，运行一个函数。takeEvery永远不会结束当前的生成器
```js
function* fetchStudents() {
    //设置为正在加载中
    yield put(setIsLoading(true))
    //当saga发现得到的结果是一个Promise对象，它会自动等待该Promise完成
    //完成之后，会把完成的结果作为值传递到下一次next
    //如果Promise对象被拒绝，saga会使用generator.throw抛出一个错误
    //searchStudents()返回一个promise
    const resp = yield searchStudents()
    yield put(setStudentsAndTotal(resp.datas, resp.cont))
    yield put(setIsLoading(false));
}

function* fetchStudents() {
    //设置为正在加载中
    yield put(setIsLoading(true))
    const condition = yield select(state => state.students.condition);
    //使用call指令，按照当前仓库中的条件
    //mockStudents是副函数
    const resp = yield call(mockStudents, condition)
    yield put(setStudentsAndTotal(resp.datas, resp.cont))
    yield put(setIsLoading(false));
}
function* fetchStudents() {
    //设置为正在加载中
    yield put(setIsLoading(true))
    const condition = yield select(state => state.students.condition);
    //使用call指令，按照当前仓库中的条件
    const resp = yield call(searchStudents, condition)
    yield put(setStudentsAndTotal(resp.datas, resp.cont))
    yield put(setIsLoading(false));
}
function* fetchStudents() {
    //设置为正在加载中
    yield put(setIsLoading(true))
    //当saga发现得到的结果是一个Promise对象，它会自动等待该Promise完成
    //完成之后，会把完成的结果作为值传递到下一次next
    //如果Promise对象被拒绝，saga会使用generator.throw抛出一个错误
    try{
        const resp = yield mockStudents()
        console.log(resp);
    }
    catch(err){
        //err表示reject的内容
        console.log(err);
    }
}
function* fetchStudents() {
    //设置为正在加载中
    yield put(setIsLoading(true))
    const condition = yield select(state => state.students.condition);
    //使用call指令，按照当前仓库中的条件
    try {
        //cps(函数，函数的参数)
        const resp = yield cps(mockStudents, condition)
        yield put(setStudentsAndTotal(resp.datas, resp.cont))
    }
    catch (err) {
        console.log(err.message);
    }
    finally {
        yield put(setIsLoading(false));
    }
}
```
- delay指令：【阻塞】阻塞指定的毫秒数
- put指令：用于重新触发action，相当于dispatch一个action
- call指令：【可能阻塞】用于副作用（通常是异步）函数调用
- apply指令：【可能阻塞】用于副作用（通常是异步）函数调用
- select指令：用于得到当前仓库中的数据
- cps指令：【可能阻塞】用于调用那些传统的回调方式的异步函数

```js
function* asyncIncrease() {
    let task;
    while (true) {
        yield take(actionTypes.asyncIncrease)
        //监听到了action，并且在开启新任务之前，取消之前的任务
        if (task) {
            //之前有任务
            yield cancel(task)
            console.log("之前的任务被取消掉了")
        }
        task = yield fork(function* () {
            yield delay(2000)
            yield put(increase());
        })
    }
}

var isStop = false; //是否停止
function* autoIncrease() {
    isStop = false;
    while (true) {
        yield delay(2000);
        if (isStop) {
            break;
        }
        yield put(increase());
    }
}

function stopAutoIncrease(){
    isStop = true;
}
export default function* () {
    yield takeLatest(actionTypes.autoIncrease, autoIncrease);
    yield takeLatest(actionTypes.stopAutoIncrease, stopAutoIncrease);
    console.log("正在监听autoIncrease")
}

function* stopTask() {
    if (task) {
        yield cancel(task);
    }
}
let task;
function* autoIncrease() {
    while (true) {
        yield take(actionTypes.autoIncrease)
        yield* stopTask();
        task = yield fork(function* () {
            while (true) {
                yield delay(2000);
                yield put(increase());
            }
        })
    }
}

/**
 * 自动增加和停止的流程控制
 * 流程：自动增加 -> 停止 -> 自动增加 -> 停止
 */
function* autoTask() {
    while (true) {
        yield take(actionTypes.autoIncrease); //只监听autoIncrease
        const task = yield fork(function* () {
            try {
                while (true) {
                    yield delay(2000);
                    yield put(increase());
                }
            }
            finally {
                if (yield cancelled()) {
                    console.log("自动增加任务被取消掉了！！！")
                }
            }
        })
        yield take(actionTypes.stopAutoIncrease); //转而监听stopAutoIncrease
        yield cancel(task);
    }
}
function asyncAction() {
    //生成一个1000~5000毫秒的随机时间
    var duration = Math.floor(Math.random() * 4000 + 1000);
    return new Promise(resolve => {
        setTimeout(() => {
            if (Math.random() > 0.5) {
                resolve(increase())
            }
            else {
                resolve(decrease());
            }
        }, duration);
    })
}
export default function* () {
    var result = yield race({
        action1: call(asyncAction),
        action2: call(asyncAction)
    })
    console.log(result);
}

function* autoTask() {
    while (true) {
        yield take(actionTypes.autoIncrease); //只监听autoIncrease
        yield race({
            autoIncrease: call(function* () {
                while (true) {
                    yield delay(2000);
                    yield put(increase());
                }
            }),
            cancel: take(actionTypes.stopAutoIncrease)
        })
    }
}
```
- fork：用于开启一个新的任务，该任务不会阻塞，该函数需要传递一个生成器函数，fork返回了一个对象，类型为Task
- cancel：用于取消一个或多个任务，实际上，取消的实现原理，是利用generator.return。cancel可以不传递参数，如果不传递参数，则取消当前任务线。
- takeLastest：功能和takeEvery一致，只不过，会自动取消掉之前开启的任务
- cancelled：判断当前任务线是否被取消掉了
- race：【阻塞】竞赛，可以传递多个指令，当其中任何一个指令结束后，会直接结束，与Promise.race类似。返回的结果，是最先完成的指令结果。并且，该函数会自动取消其他的任务

### redux-actions
- createAction(s)
import { createAction } from 'redux-actions';
import { createActions } from 'redux-actions';
```js
let noop = createAction('NOOP', amount => amount);
// same as 这俩效果一样
noop = createAction('NOOP');
noop(42) 返回 {
  type: 'NOOP',
  payload: 42
}

const updateAdminUser = createAction(
  'UPDATE_ADMIN_USER',
  updates => updates,
  () => ({ admin: true })
);

updateAdminUser({ name: 'Foo' });
// {
//   type: 'UPDATE_ADMIN_USER',
//   payload: { name: 'Foo' },
//   meta: { admin: true },
// }

const { actionOne, actionTwo, actionThree } = createActions(
  {
    // function form; payload creator defined inline
    ACTION_ONE: (key, value) => ({ [key]: value }),
    // array form
    ACTION_TWO: [
      first => [first], // payload
      (first, second) => ({ second }) // meta
    ]
    // trailing action type string form; payload creator is the identity
  },
  'ACTION_THREE'
);
actionOne('key', 1) 返回 {
  type: 'ACTION_ONE',
  payload: { key: 1 }
};

actionTwo('first', 'second') 返回 {
  type: 'ACTION_TWO',
  payload: ['first'],
  meta: { second: 'second' }
};

actionThree(3) 返回 {
  type: 'ACTION_THREE',
  payload: 3
};
```
- handleAction(s)
返回的是reducer，可用combineReducers进行融合
import { handleAction } from 'redux-actions';
import { handleActions } from 'redux-actions';
```js
handleAction(
  'APP/COUNTER/INCREMENT',
  (state, action) => ({
    counter: state.counter + action.payload.amount
  }),
  defaultState//默认数据
);

const reducer = handleActions(
  {
      //用createAction(s)导出函数的名字
      //函数名是类型转化过来的
    [increase]: (state, action) => ({
      counter: state.counter + action.payload
    }),

    DECREMENT: (state, action) => ({
      counter: state.counter - action.payload
    })
  },
  { counter: 0 }//默认数据
);

//用map
const reducer = handleActions(
  new Map([
    [
      [increase],//函数的名字
      (state, action) => ({
        counter: state.counter + action.payload
      })
    ],

    [
      DECREMENT,
      (state, action) => ({
        counter: state.counter - action.payload
      })
    ]
  ]),
  { counter: 0 }
);

const options = {
  prefix: 'counter', // String used to prefix each type
  namespace: '--' // Separator between prefix and type.  Default: `/`
}

createActions({ ... }, 'INCREMENT', options)

handleActions({ ... }, defaultState, options)
```
- combineActions
```js
//三个类型进行的操作一样
export default handleActions({
    [combineActions(increase, decrease, add)]: (state, { payload }) => state + payload
}, 0)
```

### react-redux
链接react和redux
- Provider组件：没有任何UI界面，该组件的作用，是将redux的仓库放到一个上下文中。
- connect：高阶组件，用于链接仓库和组件的
  - 细节一：如果对返回的容器组件加上额外的属性，则这些属性会直接传递到展示组件
  - 第一个参数：mapStateToProps: 
    - 参数1：整个仓库的状态
    - 参数2：使用者传递的属性对象
  - 第二个参数：
    - 情况1：传递一个函数 mapDispatchToProps
      - 参数1：dispatch函数
      - 参数2：使用者传递的属性对象
      - 函数返回的对象会作为属性传递到展示组件中（作为事件处理函数存在）
    - 情况2：传递一个对象，对象的每个属性是一个action创建函数，当事件触发时，会自动的dispatch函数返回的action
  - 细节二：如果不传递第二个参数，通过connect连接的组件，会自动得到一个属性：dispatch，使得组件有能力自行触发action，但是，不推荐这样做。
```js
//App.js
import React from 'react'
import { Provider } from "react-redux"
import store from "./store"
import Counter from "./components/Counter"

export default function App() {
    return (
        <Provider store={store}>
            <Counter abc={123}/>
        </Provider>
    )
}
//./components/Counter
import React from 'react'
import { asyncDecrease, asyncIncrease, increase, decrease } from "../store/action/counter"
import { connect } from "react-redux"
//展示组件
function Counter(props) {
    return (
        <div>
            <h1>{props.number}</h1>
            <p>
                <button onClick={props.onAsyncDecrease}> 异步减 </button>
                <button onClick={props.onDecrease}> 减 </button>
                <button onClick={props.onIncrease}> 加 </button>
                <button onClick={props.onAsyncIncrease}> 异步加 </button>
            </p>
        </div>
    )
}

/**
 * 将整个仓库的状态，映射到当前需要的数据
 * @param {*} state 
 */
function mapStateToProps(state, ownProps) {
    console.log(ownProps)
    return {
        number: state.counter
    }
}

/**
 * 映射事件处理函数
 * @param {*} dispatch 
 */
function mapDispatchToProps(dispatch) {
    return {
        onAsyncDecrease() {
            dispatch(asyncDecrease())
        },
        onDecrease() {
            dispatch(decrease())
        },
        onIncrease() {
            dispatch(increase())
        },
        onAsyncIncrease() {
            dispatch(asyncIncrease())
        },
    }
}
// //connect返回一个高阶组件
// const hoc = connect(mapStateToProps, mapDispatchToProps)

// //传入展示组件，返回一个容器组件
// export default hoc(Counter)
export default connect(mapStateToProps, mapDispatchToProps)(Counter)
---
import { bindActionCreators } from "redux"
function mapDispatchToProps(dispatch) {
    return bindActionCreators({ 
        onAsyncDecrease: asyncDecrease,
        onDecrease: decrease, 
        onIncrease: increase,
        onAsyncIncrease: asyncIncrease
    }, dispatch)
}
---
----
const creators = {
    onAsyncDecrease: asyncDecrease,
    onDecrease: decrease,
    onIncrease: increase,
    onAsyncIncrease: asyncIncrease
};

export default connect(mapStateToProps, creators)(Counter)
----
```

### react-redux 其他api
```js
//connectAdvanced该函数和connect一样，也是用于连接React组件和Redux仓库的，只不过它的配置比connect少一些
function selectorFactory(dispatch) {
    return function (state, ownProps) {
//ownProps自己传递的属性
        return {
            current: state.students.condition.page,
            total: state.students.result.total,
            limit: state.students.condition.limit,
            panelNumber: 5,
            onPageChange: (newPage) => {
                dispatch(changeCondition({ page: newPage }))
                dispatch(fetchStudents());
            }
        }
    }
}
const PagerTemp = connectAdvanced(selectorFactory)(Pager)
```
- createProvider
createProvider(字符串key)：通过一个唯一的key值创建一个Provider组件。
```js
var Provider1 = createProvider("p1");
var Provider2 = createProvider("p2");
```

### react-router
/store/reducer/index.js
```js
// 创建一个唯一的reducer
import students from "./student"
import counter from "./counter"
import { combineReducers } from "redux"
import { connectRouter } from "connected-react-router"
import history from "../history"

export default combineReducers({
    students,
    counter,
    //添加路由状态
    router: connectRouter(history)
})
```
/store/history.js
```js
import { createBrowserHistory } from "history";
export default createBrowserHistory();
```
/store/index.js
```js
// 用于创建仓库，并导出
import { createStore, applyMiddleware } from "redux"
import reducer from "./reducer"
import logger from "redux-logger"
import createSagaMiddleware from "redux-saga"
import rootSaga from "./saga"
import { composeWithDevTools } from "redux-devtools-extension"
import { routerMiddleware } from "connected-react-router"
import history from "./history"
const routerMid = routerMiddleware(history)

const sagaMid = createSagaMiddleware(); //创建一个saga的中间件

const store = createStore(reducer,
    composeWithDevTools(applyMiddleware(routerMid, sagaMid, logger))
)

sagaMid.run(rootSaga); //启动saga任务

export default store;
```
/App.js
```js
import React from 'react'
import { Provider } from "react-redux"
import store from "./store"
import { BrowserRouter as Router, Route, Switch } from "react-router-dom"
import { ConnectedRouter } from "connected-react-router"
import Admin from "./pages/Admin"
import Login from "./pages/Login"
import history from "./store/history"

export default function App() {
    return (
        <Provider store={store}>
            <Router>
            <ConnectedRouter history={history}>
                <Switch>
                    <Route path="/login" component={Login} />
                    <Route path="/" component={Admin} />
                </Switch>
            </ConnectedRouter>
            </Router>
        </Provider>
    )
}

```

### dva
> 官方网站：https://dvajs.com
> dva不仅仅是一个第三方库，更是一个框架，它主要整合了redux的相关内容，让我们处理数据更加容易，实际上，dva依赖了很多：react、react-router、redux、redux-saga、react-redux、connected-react-router等。

1. dva默认导出一个函数，通过调用该函数，可以得到一个dva对象
   
2. dva对象.router：路由方法，传入一个函数，该函数返回一个React节点，将来，应用程序启动后，会自动渲染该节点。

3. dva对象.start: 该方法用于启动dva应用程序，可以认为启动的就是react程序，该函数传入一个选择器，用于选中页面中的某个dom元素，react会将内容渲染到该元素内部。

4. dva对象.model: 该方法用于定义一个模型，该模型可以理解为redux的action、reducer、redux-saga副作用处理的整合，整合成一个对象，将该对象传入model方法即可。
   1. namespace：命名空间，该属性是一个字符串，字符串的值，会被作为仓库中的属性保存
   2. state：该模型的默认状态
   3. reducers: 该属性配置为一个对象，对象中的每个方法就是一个reducer，dva约定，方法的名字，就是匹配的action类型
   4. effects: 处理副作用，底层是使用redux-saga实现的，该属性配置为一个对象，对象中的每隔方法均处理一个副作用，方法的名字，就是匹配的action类型。
      1. 函数的参数1：action
      2. 参数2：封装好的saga/effects对象
   5. subscriptions：配置为一个对象，该对象中可以写任意数量任意名称的属性，每个属性是一个函数，这些函数会在模型加入到仓库中后立即运行。

/models/counter.js
```js
export default {
    namespace: "counter",
    state: 0,
    reducers: {
        increase(state) {
            return state + 1;
        },
        decrease(state) {
            return state - 1;
        },
        add(state, { payload }) {
            return state + payload;
        }
    },
    effects: {
        *asyncIncrease(action, { call, put }) {
            yield call(delay, 1000);
            yield put({ type: "increase" })
        },
        *asyncDecrease(action, { call, put }) {
            yield call(delay, 1000);
            yield put({ type: "decrease" })
        }
    },
    subscriptions: {
        resizeIncrease({ dispatch }) {
            //订阅窗口尺寸变化，每次变化让数字增加
            window.onresize = () => {
                dispatch({ type: "increase" })
            }
        },
        resizeDecrease({ dispatch, history }) {
            history.listen(() => {
                dispatch({ type: "decrease" })
            })
        }
    }
}

function delay(duration) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve()
        }, duration);
    })
}
```
/index.js
```js
import React from 'react';
import App from "./App"
import dva from "dva";
import counterModel from "./models/counter"
import studentsModel from "./models/students"

//得到一个dva对象
const app = dva();

//在启动之前定义模型
app.model(counterModel)
app.model(studentsModel)

//设置根路由，即启动后，要运行的函数，函数的返回结果会被渲染
app.router(() => <App />)

app.start("#root")
```
/App.js
```js
import React from 'react'
import Counter from "./Counter"
export default function App() {
    return (
        <div>
            根组件
            <Counter />
        </div>
    )
}
```
/Counter.js
```js
import React, { useRef } from 'react'
import { connect } from "dva"

function Counter(props) {
    const inp = useRef();
    return (
        <div>
            <h1>{props.number}</h1>
            <button onClick={props.onAsyncDecrease}>异步减</button>
            <button onClick={props.onDecrease}> - </button>
            <button onClick={props.onIncrease}> + </button>
            <button onClick={props.onAsyncIncrease}>异步加</button>
            <p>
                <input type="number" ref={inp} />
                <button onClick={() => {
                    const n = parseInt(inp.current.value);
                    props.onAdd(n);
                }}>加上</button>
            </p>
        </div>
    )
}

const mapStateToProps = state => ({
    number: state.counter
})

const mapDispatchToProps = dispatch => ({
    onIncrease() {
        dispatch({
            type: "counter/increase"
        })
    },
    onDecrease() {
        dispatch({
            type: "counter/decrease"
        })
    },
    onAdd(n) {
        dispatch({ type: "counter/add", payload: n })
    },
    onAsyncIncrease() {
        dispatch({ type: "counter/asyncIncrease" })
    },
    onAsyncDecrease() {
        dispatch({ type: "counter/asyncDecrease" })
    }
})

export default connect(mapStateToProps, mapDispatchToProps)(Counter)
```

5. 在dva中同步路由到仓库
   1. 在调用dva函数时，配置history对象
   2. 使用ConnectedRouter提供路由上下文


6. 配置：
   1. history：同步到仓库的history对象
   2. initialState：创建redux仓库时，使用的默认状态
   3. onError: 当仓库的运行发生错误的时候，运行的函数
   4. onAction: 可以配置redux中间件
      1. 传入一个中间件对象
      2. 传入一个中间件数组
   5. onStateChange: 当仓库中的状态发生变化时运行的函数
   6. onReducer：对模型中的reducer的进一步封装
   7. onEffect：类似于对模型中的effect的进一步封装
   8. extraReducers：用于配置额外的reducer，它是一个对象，对象的每一个属性是一个方法，每个方法就是一个需要合并的reducer，方法名即属性名。
   9. extraEnhancers: 它是用于封装createStore函数的，dva会将原来的仓库创建函数作为参数传递，返回一个新的用于创建仓库的函数。函数必须放置到数组中。

/index.js
```js
import routerConfig from "./routerConfig"
import dva from "dva";
import counterModel from "./models/counter"
import studentsModel from "./models/students"
import { createBrowserHistory } from "history"

// const logger = store => next => action => {
//     console.log("老状态：", store.getState());
//     next(action);
//     console.log("新状态：", store.getState());
//     console.log("")
// }

//得到一个dva对象
const app = dva({
    history: createBrowserHistory(),
    initialState: {
        counter: 123
    },
    // onError(err, dispatch) {
    //     console.log(err.message, dispatch);
    // }
    // onAction: logger
    // onStateChange(state) {
    //     console.log(state.counter);
    // },
    // onReducer(reducer) {
    //     return function (state, action) {
    //         console.log("reducer即将被执行")
    //         const newState = reducer(state, action);
    //         console.log("reducer执行结束")
    //         return newState;
    //     }
    // }
    // onEffect(oldEffect, sagaEffects, model, actionType) {
    //     return function* (action) {
    //         console.log("即将执行副作用代码")
    //         yield oldEffect(action);
    //         console.log("副作用代码执行完毕")
    //     }
    // }
    extraReducers: {
        abc(state = 123, action) {
            return state;
        },
        bcd(state = 456, action) {
            return state;
        }
    },
    extraEnhancers: [function (createStore) {
        return function (...args) {
            console.log("即将创建仓库1")
            return createStore(...args);
        }
    }, function (createStore) {
        return function (...args) {
            console.log("即将创建仓库2")
            return createStore(...args);
        }
    }]
});

//在启动之前定义模型
app.model(counterModel)
app.model(studentsModel)

//设置根路由，即启动后，要运行的函数，函数的返回结果会被渲染
app.router(routerConfig)

app.start("#root")
```
/routerConfig.js
```js
import React from 'react'
import Counter from "./Counter"
import { routerRedux, NavLink, Route, Switch } from "dva/router"

//routerRedux: 里面包含了所有connected-react-router的东西
function Home() {
    return <h1>首页</h1>
}

export default function ({ history }) {
    return (
        <routerRedux.ConnectedRouter history={history}>
            <div>
                <ul>
                    <li>
                        <NavLink to="/">首页</NavLink>
                    </li>
                    <li>
                        <NavLink to="/counter">计数器</NavLink>
                    </li>
                </ul>
                <div>
                    <Switch>
                        <Route path="/counter" component={Counter} />
                        <Route path="/" component={Home} />
                    </Switch>
                </div>
            </div>
        </routerRedux.ConnectedRouter>
    )
}
```
#### dva插件

通过```dva对象.use(插件)```，来使用插件，插件本质上就是一个对象，该对象与配置对象相同，dva会在启动时，将传递的插件对象混合到配置中。

##### dva-loading

该插件会在仓库中加入一个状态，名称为loading，它是一个对象，其中有以下属性

- global：全局是否正在处理副作用（加载），只要有任何一个模型在处理副作用，则该属性为true
- models：一个对象，对象中的属性名以及属性的值，表示哪个对应的模型是否在处理副作用中（加载中）
- effects：一个对象，对象中的属性名以及属性的值，表示是哪个action触发了副作用

需要安装dva-loading
/index.js
```js
import routerConfig from "./routerConfig"
import dva from "dva";
import counterModel from "./models/counter"
import studentsModel from "./models/students"
import { createBrowserHistory } from "history"
import createLoading from "dva-loading"

//得到一个dva对象
const app = dva({
    history: createBrowserHistory()
});

app.use(createLoading()); //使用dva-loading插件

//在启动之前定义模型
app.model(counterModel)
app.model(studentsModel)

//设置根路由，即启动后，要运行的函数，函数的返回结果会被渲染
app.router(routerConfig)

app.start("#root")
```
# umi
### umi
全局安装umi
npm init 
umi dev
> 官网：https://umijs.org/

### 约定式路由
目录结构
 src 
   - layouts
     -- index.js
   - components
     -- Menu.js
   - pages
     -- a.js
     -- b.js
     -- index.js

/src/layouts/index.js
注意引入改了很多 不支持umi/link这种形式的引入
```js
import React from 'react'
// import Link from "umi/link" //实际上就是react-router-dom中的Link组件
// import NavLink from "umi/navlink" //实际上就是react-router-dom中的NavLink组件
import Menu from "@/components/Menu"
// import {NavLink} from "umi"
export default function index(props) {
    return (
        <div>
            {/* <div>
                <NavLink to="/">首页</NavLink>
                <NavLink to="/a">a页</NavLink>
                <NavLink to="/b">b页</NavLink>
                <NavLink to="/sub">sub页</NavLink>
            </div> */}
            <Menu />
            {props.children}
            <div><h1>页脚</h1></div>
        </div>
    )
}

```
/src/components/Menu.js
这里面的引入是正确的
```js
import React from 'react'
import {NavLink} from "umi"
import {history} from "umi"

export default function Menu() {
    return (
        <div>
            <NavLink to="/">首页</NavLink>
            <button onClick={() => {
                history.push("/a")
            }}>a页</button>
            <NavLink to="/b">b页</NavLink>
            <button onClick={() => {
                history.push("/sub")
            }}>sub页</button>
        </div>
    )
}
```
/src/components/A.js
```js
import React from 'react'
import {withRouter} from "umi"

function A(props) {
    return (
        <>
        <h1>withRouterA</h1>
        <div>
            {props.location.pathname}
        </div>
        </>

    )
}

export default withRouter(A);
```

### 配置式路由
版本更新比较大，请看官方文档
/.umirc.js
```js
export default {
    routes: [
        {
            path: "/",
            component: "../layouts/index.js",
            exact: false,
            routes: [
                {
                    path: "/", component: "./index", title: "首页",
                    wrappers: ["@/routes/HandleTitle.js"]
                },
                {
                    path: "/login", component: "./login", title: "登录页",
                    wrappers: ["../routes/HandleTitle.js"]
                },
                {
                    path: "/welcome", component: "./welcome", title: "欢迎页",
                    wrappers: ["./src/routes/PrivateRouter.js", "./src/routes/HandleTitle.js"]
                },
              
            ]
        }
    ]
}
```
### umi使用dva
安装umi-plugin-react
/.umirc.js
```js
export default {
    plugins: [
        ["umi-plugin-react", {
            title: true, //开启title插件
            dva: true, //开启dva插件
            immer: true, //开启immer插件  dva-immer 基础immer库 袁老师不建议启用该插件
            routes: {
                exclude: [/.*\/models\/.*/, /.*\/model\.js/]
            }
        }]
    ]
}
```
/src/models/counter.js
```js
export default {
    state: 0,
    reducers: {
        increase(state) {
            return state + 1;
        },
        decrease(state) {
            return state - 1;
        }
    }
}
```
/src/components/Counter.js
```js
import React from 'react'
import { connect } from "dva"

function Counter({ number, onIncrease, onDecrease }) {
    return (
        <div>
            <h1>{number}</h1>
            <p>
                <button onClick={onDecrease}>减</button>
                <button onClick={onIncrease}>加</button>
            </p>
        </div>
    )
}

const mapStateToProps = state => ({
    number: state.counter
})

const mapDispatchToProps = dispatch => ({
    onDecrease() {
        dispatch({ type: "counter/decrease" })
    },
    onIncrease() {
        dispatch({ type: "counter/increase" })
    },
})

export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```
### 使用样式
非全局样式要引入
![dva使用样式](./assets/vue-react/dva使用样式.png)


### 代理和数据模拟

**代理**

代理用于解决跨域问题

配置```.umirc.js```中的proxy，配置方式和devServer中的proxy配置相同

**数据模拟**

用于解决前后端协同开发的问题

数据模拟可以让前端开发者在开发时，无视后端接口是否真正完成，因为使用的是模拟的数据

umijs约定：

1. mock文件夹中的文件
2. src/pages文件夹中的_mock.js文件

以上两种JS文件，均会被umijs读取，并作为数据模拟的配置

可以自行发挥，添加模拟数据，通常，我们会和mockjs配合。

/.umirc.js
```js
export default {
    plugins: [
        ["umi-plugin-react", {
            title: true, //开启title插件
            dva: true, //开启dva插件
            immer: true, //开启immer插件  dva-immer 基础immer库 袁老师不建议启用该插件
            routes: {
                exclude: [/.*\/models\/.*/, /.*\/model\.js/]
            }
        }]
    ],
    // proxy: { //相当于webpack中的devServer中的proxy配置
    //     "/api": {
    //         target: "http://api.duyiedu.com",
    //         changeOrigin: true //修改源
    //     }
    // }
}
```
/mock/student.js
```js
import Mock from "mockjs"
var result = Mock.mock({
    msg: "查询成功",
    status: "success",
    "data|100": [{
        name: "@cname",
        address: "@city",
        appkey: /demo\d{2}_\d{10}/,
        "birth|1980-2000": 0,
        "ctime|1554049417-1564049417": 0,
        email: "@email",
        "id|+1": 1,
        phone: /1\d{10}/,
        sNo: /\d{5}/,
        "sex|1": [0, 1],
        "utime|1554049417-1564049417": 0
    }]
})


//导出的是数据模拟的配置
//该文件会被umijs读取
export default {
    "GET /api/student/findAll": {
        msg: "查询成功",
        status: "success",
        data: result
    }
}
```
/src/services/student.js
```js
const appkey = "demo13_1545210570249";

/**
 * 获取所有学生
 */
export async function getAllStudents() {
    return await fetch("/api/student/findAll?appkey=" + appkey)
        .then(resp => resp.json()).then(resp => resp.data);
}

export async function getStudents(page = 1, limit = 10) {
    return await fetch(`/api/student/findByPage?appkey=${appkey}&page=${page}&size=${limit}`)
        .then(resp => resp.json()).then(resp => resp.data);
}
```
### umi配置

**额外的约定文件**

- src/pages/document.ejs: 页面模板文件
- src/global.js：在umi最开始启动时运行的js文件
- src/app.js：作运行时配置的代码
  - patchRoutes: 函数，该函数会在umi读取完所有静态路由配置后执行
  - dva
    - config： 相当于new dva(配置)
    - plugins： 相当于dva.use(插件)
- .env: 配置环境变量，这些变量会在umi编译期间发挥作用
  - UMI_ENV：umi的环境变量值，可以是任意值，该值会影响到.umirc.js
  - PORT
  - MOCK

**umirc配置**

**umi配置**

书写在.umirc.js文件中的配置

- plugins：配置umijs的插件
- routes：配置路由（会导致约定式路由失效）
- history：history对象模式（默认是browser）
- outputPath：使用umi build后，打包的目录名称，默认./dist
- base: 相当于之前BrowserRouter中的basename
- publicPath: 指定静态资源所在的目录
- exportStatic: 开启该配置后，会打包成多个静态页面，每个页面对应一个路由，开启多静态页面应用的前提条件是：没有动态路由

**webpack配置**
### umi脚手架

create-umi
yarn create umi

