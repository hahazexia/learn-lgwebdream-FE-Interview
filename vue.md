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