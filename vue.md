# vue

1. 按要求完成问题：一个List页面上，含有1000个条目的待办列表，现其中100项在同一时间达到了过期时间，需要在对应项的text-node里添加“已过期”文字。需要尽可能减少dom重绘次数以提升性能。[链接](https://github.com/lgwebdream/FE-Interview/issues/848)
    * 在不使用vue、react的前提下写代码解决一下问题
    * 尝试使用vue或react解决上述问题

<details>
<summary>答案</summary>

原生模拟代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <button id="expire1">过期设置(暴力法)</button>
    <button id="expire2">过期设置(innerHTMl)</button>
    <ul id="wrap"></ul>
</body>
<script>
    //生成大量dom 
    let start = new Date().getTime()
    let $ul = document.getElementById("wrap");
    let el = document.createDocumentFragment()
    let allKeys = []
    for(var i = 0; i < 1000; i++){
        let li = document.createElement('li');
        li.dataset.key = i  //key
        li.innerHTML = i
        el.appendChild(li)
        allKeys.push(i)
    }
    $ul.appendChild(el)
    // 生成过期项 模拟服务端生成的数据
    function getExpireKeys(){
        let keys = []
        while(keys.length < 100){
        let randomKey = Math.floor(Math.random() * 1000)
        if(keys.indexOf(randomKey) === -1){
            keys.push(randomKey)
        }else{
            continue
        }
        }
        return keys
    }
    // 暴力项 逐项遍历
    document.getElementById('expire1').onclick = function(){
        let expireKeys = getExpireKeys()
        let children = $ul.children;
        let start = Date.now()
        for (let i = 0; i < expireKeys.length; i++) {
        const element = document.querySelector(`[data-key="${expireKeys[i]}"]`);
        element.innerHTML = element.innerHTML + '已过期'
        }
    }
    //模板字符串 innerHtml替换
    document.getElementById('expire2').onclick = function(){
        let expireKeys = getExpireKeys()
        const item = []
        for (let i = 0; i < allKeys.length; i++) {
        item.push( `<li>${allKeys[i]} ${expireKeys.indexOf(allKeys[i]) !== -1 ? '已过期' : ''}</li>`)
        }
        $ul.innerHTML = item.join('')
    }
</script>
</html>
```

vue 模拟代码：

```html
<template>
    <div id="app">
        <button @click="setExpire">过期</button>
        <ul>
            <li v-for="item in allKeys" :key="item.value">
                {{item.value}}
                {{item.expire ? '已过期' : ''}}
            </li>
        </ul>
    </div>
</template>

<script>
export default {
  data() {
    return {
      allKeys: [],  //所有项
    }
  },
  created(){
    for(var i = 0; i < 1000; i++){
      this.allKeys.push({
        value: i,
        expire: false
      })
    }
  },
  methods: {
    setExpire(){
      let keys = this.getExpireKeys()
      for (let i = 0; i < this.allKeys.length; i++) {
        if(keys.indexOf(this.allKeys[i].value) !== -1){
          this.allKeys[i].expire = true
        }
      }
    },
    // 生成过期项 模拟服务端生成的数据
    getExpireKeys(){
      let keys = []
      while(keys.length < 100){
        let randomKey = Math.floor(Math.random() * 1000)
        if(keys.indexOf(randomKey) === -1){
          keys.push(randomKey)
        }else{
          continue
        }
      }
      return keys
    }
  },
}
</script>
```
</details>
<br><br>

2. Redux 和 Vuex 有什么区别，说下一它们的共同思想？[链接](https://github.com/lgwebdream/FE-Interview/issues/206)

<details>
<summary>答案</summary>

区别：

* vuex 和 redux 的同步异步操作方式有区别：

vuex的同步异步方式不一样

view——>commit——>mutations——>state变化——>view变化（同步操作）

view——>dispatch——>actions——>mutations——>state变化——>view变化（异步操作）

redux 的同步异步方式一样

view——>dispatch——>actions——>reducer——>state变化——>view变化（同步异步一样）

* redux 需要增加订阅函数，也就是我们的 store.subscribe(render), 但是 vue 是双向绑定，不需要这步操作。
* vuex 改进了 redux 中的 action 和 reducer 函数，以 mutations 变化函数取代 reducer，无需 switch，只需在对应的 mutations 函数里改变 state 值就可以。


共同思想：

* 单一的数据源，变化可以预测
* 本质上: redux 和 vuex 都是对 MVVM 思想的服务，将数据从视图中抽离的一种方案
* 形式上: vuex 借鉴了 redux，将 store 作为全局的数据中心，进行数据管理

</details>
<br><br>

3. 说一下对 React 和 Vue 的理解，它们的异同 [链接](https://github.com/lgwebdream/FE-Interview/issues/347)

<details>
<summary>答案</summary>

1. 相似之处

* 都将注意力集中保持在核心库，而将其他功能如路由和全局状态管理交给相关的库
* 都有自己的构建工具，能让你得到一个根据最佳实践设置的项目模板
* 都使用了 virtual DOM 提高重绘性能
* 都有 props 的概念，允许组件间的数据传递
* 都鼓励组件化应用，将应用拆成一个个功能明确的模块，提高复用性

2. 不同之处
    1. 数据流
        * vue默认支持数据双向绑定，而 react 一直提倡单向数据流
    2. 虚拟DOM
        * vue 2.x 开始引入 virtual DOM，消除了和 react 在这方面的差异，但是在具体细节还是有各自的特点
        * vue 宣称可以更快地计算出 virtual DOM 的差异，这是由于它在渲染过程中，会跟踪每一个组件的依赖关系，不需要重新渲染整个组件树
        * 对于 react 而言，每当应用的状态被改变时，全部子组件都会重新渲染。当然，这可以通过 PureComponent/shouldComponentUpdate 这个生命周期方法来进行控制，但 vue 将此视为默认的优化。
    3. 组件化
        * react 和 vue 最大的不同是模板的编写。
        * vue 鼓励你去写近似常规 html 的模板。写起来很接近标准 html 元素，只是多了一些属性。react 推荐所有的模板通过 javascript 语法扩展 JSX 书写。
        * 具体来讲：react 中 render 函数是支持闭包特性的，所以 import 的组件在 render 中可以直接调用。但在 vue 中，由于模板中使用数据必须挂在 this 上进行一次中转，所以我们 import 一个组件用完了之后，还需要在 components 中再声明一下。
    4. 监听数据变化的实现原理不同
        * vue 通过 getter/setter 以及一些函数的劫持，能精确知道数据变化，不需要特别的优化就能达到很好的性能
        * react 默认是通过比较引用的方式进行的，如果不优化（PureComponent/shouldComponentUpdate）可能导致大量不必要的 virtual DOM 重新渲染。这是因为 vue 使用的是可变数据，而 react 更强调数据的不可变。
    5. 高阶组件
        * react 可以通过高阶组件（High Order Components HOC）来扩展，而 vue 需要通过 mixins 来扩展
        * 原因：高阶组件就是高阶函数，而 react 的组件本身就是纯粹的函数，所以高阶函数对 react 来说易如反掌。相反 vue 使用 html 模板创建视图组件，这使模板无法有效的编译，因此 vue 不采用 HOC 来实现。
    6. 构建工具
        * react create-react-app
        * vue vue-cli
    7. 跨平台
        * react react-native
        * vue weex
</details>
<br><br>

4. 对虚拟 DOM 的理解？虚拟 DOM 主要做了什么？虚拟 DOM 本身是什么？[链接](https://github.com/lgwebdream/FE-Interview/issues/479)

<details>
<summary>答案</summary>

1. 什么是 virtual DOM 

* 从本质上讲，virtual dom 是一个 javascript 对象，通过对象的方式来表示 dom 结构。将页面的状态抽象为 js 对象的形式，配合不同的渲染工具，使跨平台渲染成为可能。通过事务处理机制，将多次 dom 修改的结果一次性更新到页面上，**从而有效减少页面渲染次数，减少修改 dom 的重绘重排次数，提高渲染性能**。

虚拟 dom 是对 dom 的抽象，这个对象是更加轻量级的对 dom 的描述。它设计的最初目的，就是更好的跨平台，比如 nodejs 就没有 dom，如果想实现 ssr，那么一个方式就是借助虚拟 dom，因为虚拟 dom 本身是 js 对象。在代码渲染到页面之前，vue 或者 react 会把代码转换成一个对象（virtual dom）。以对象的形式来描述真实 dom 结构，最终渲染到页面。在每次数据发生变化前，虚拟 dom 都会缓存一份，变化之时，现在的虚拟 dom 会与缓存的虚拟 dom 进行比较。

在 vue 或 react 内部封装了 diff 算法，通过这个算法来进行比较，渲染时修改改变的变化，原先没有发生改变的通过原来的数据渲染。

另外现在前端框架的一个基本要求就是无须手动操作 dom，一方面是因为手动操作 dom 无法保证程序性能，多人协作的项目中如果 review 不严格，可能会有开发者写出性能较低的代码，另一方面更重要的是省略手动 dom 操作可以大大提高开发效率。

2. 为什么要用 virtual dom
    1. 保证性能下限，在不进行手动优化的情况下，提供过得去的性能
    
    看一下页面渲染的一个流程：
    
    * 解析 html --> 生成 dom --> 生成 cssom --> layout --> paint --> compiler

    下面对比一下修改 dom 时真实 dom 操作和 virtual dom 的过程，来看一下它们重排重绘的性能消耗：

    * 真实 dom: 生成 html 字符串 + 重建所有 dom 元素
    * virtual dom: 生成 vnode + domdiff + 必要的 dom 更新

    virtual dom 更新 dom 的准备工作耗费更多的时间，也就是 js 层面，相比于更多的 dom 操作它的消费是极其便宜的。尤雨溪在社区论坛中写道：**框架给你的保证是，你不需要手动优化的情况下，我依然可以给你提供过得去的性能**。
    
    2. 跨平台
    virtual dom 本质上是 javascript 对象，它可以很方便的跨平台操作，比如服务端渲染，uniapp 等。

3. virtual dom 真的比真实 dom 性能好吗
    1. 首次渲染大量 dom 时，由于多了一层虚拟 dom 的计算，会比 innerHTML 插入慢
    2. 正如它能保证性能下限，在真实 dom 操作的时候进行针对性的优化时，还是很快的
</details>
<br><br>

5. 介绍单页应用和多页应用？[链接](https://github.com/lgwebdream/FE-Interview/issues/593)

<details>
<summary>答案</summary>

* 多页应用，每次页面跳转，都会请求后台返回新的页面，这就是多页应用。
    * 路由由后端控制。
    * 优点：首屏时间快，SEO 效果好。缺点：页面跳转加载慢，会白屏

* 单页应用，跳转页面的时候不会再请求新的页面，只有一个页面文件，依靠 js 改变页面的内容。
    * 路由由前端控制。
    * 优点：切换页面加载快，不会白屏。缺点：首屏加载慢，SEO 不友好。

</details>
<br><br>