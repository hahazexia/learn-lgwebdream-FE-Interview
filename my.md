# my

1. 如下表达式会输出什么？

```js
5 * '5'
16 + '5'
16 + ''
16 > '5'
'5' > '5'
```

<details>
<summary>答案</summary>

```js
5 * '5' // 25
16 + '5' // '165'
16 + '' // '16'
16 > '5' // true
'5' > '5' // false
```

* JavaScript 遇到预期为数值的地方，就会将参数值自动转换为数值。系统内部会自动调用Number()函数。除了加法运算符（+）有可能把运算子转为字符串，其他运算符都会把运算子自动转成数值。
* 加法运算符，如果一个运算子是字符串，另一个运算子是非字符串，这时非字符串会转成字符串，再连接在一起。
* 比较运算符，如果两个运算子都是原始类型的值，则是先转成数值再比较。而都是字符串则按照字典顺序进行比较。

</details>
<br><br>

2. 如下执⾏后会输出什么?

```js
var obj = {
    a: 10,
    b: () => {
        console.log(this.a);
        console.log(this);
    }
}
obj.b()
```

<details>
<summary>答案</summary>

```js
var obj = {
    a: 10,
    b: () => {
        console.log(this.a); // undefined
        console.log(this); // window
    }
}
obj.b()
```

* 箭头函数没有自己的 this，它的 this 就是外层代码块的 this，此处的外层就直接到了全局作用域，所以指向了 window
</details>
<br><br>

3. 如下执⾏后会输出什么?

```js
let department = '58-ABG-FE';
console.log('my department is ' + (department === '58-ABG-FE') ? '58-ABG-FE' : '58-ABG-RD')
```

<details>
<summary>答案</summary>

```js

let department = '58-ABG-FE';
console.log('my department is ' + (department === '58-ABG-FE') ? '58-ABG-FE' : '58-ABG-RD')
// '58-ABG-FE'
```

* 加减乘除，还有条件运算符，与或非，还有自增自减运算符的优先级都比条件运算符（?:）的优先级高，所以先计算 `'my department is ' + (department === '58-ABG-FE')` 然后才会计算条件运算符的结果
</details>
<br><br>

4. 如下输出什么？

```js
const user = {
    name: 'fe',
    age: 28,
    company: {
        department: 'ABG-FE'
    }
};
const { company: {department = 'ABG-RD'}} = user;
console.log(department);
```

<details>
<summary>答案</summary>

```js
const user = {
    name: 'fe',
    age: 28,
    company: {
        department: 'ABG-FE'
    }
};
const { company: {department = 'ABG-RD'}} = user;
console.log(department); // 'ABG-FE'
```
</details>
<br><br>

5. 如下输出什么？

```js
const print = (fn) => {
    let a = 200;
    fn();
}
let a = 100;
const fn = () => {
    console.log(a);
};
print(fn);

```

<details>
<summary>答案</summary>

```js
const print = (fn) => {
    let a = 200;
    fn();
}
let a = 100;
const fn = () => {
    console.log(a);
};
print(fn); // 100

```
* 函数 fn 是在函数 print 的外部声明的，所以它的作用域绑定外层，内部变量 a 不会到函数 print 体内取值，所以输出 100，而不是 200。
* 函数执行时所在的作用域，是定义时的作用域，而不是调用时所在的作用域。
</details>
<br><br>

6. 什么是闭包？⼿写⼀个闭包?

<details>
<summary>答案</summary>

```js
function f1() {
  var n = 999;
  function f2() {
    console.log(n);
  }
  return f2;
}

var result = f1();
result(); // 999
```

闭包就是函数 f2，即能够读取其他函数内部变量的函数。由于在 JavaScript 语言中，只有函数内部的子函数才能读取内部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。闭包最大的特点，就是它可以“记住”诞生的环境，比如f2记住了它诞生的环境 f1，所以从 f2 可以得到 f1 的内部变量。在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。
</details>
<br><br>

7. 如下输出什么？

```js
function foo() {
    return a => {
        console.log(this.a);
    }
}
const obj1 = {a: 2};
const obj2 = {a: 3};
const bar = foo.call(obj1);
console.log(bar.call(obj2))
```
<details>
<summary>答案</summary>

```js
function foo() {
    return a => {
        console.log(this.a);
    }
}
const obj1 = {a: 2};
const obj2 = {a: 3};
const bar = foo.call(obj1);
console.log(bar.call(obj2)) // 2
```
* 箭头函数的 this 就是外出代码块的 this，所以就是 foo 的 this，所以是 obj1
</details>
<br><br>

8. js 的垃圾回收机制是什么，常用的是哪种？

<details>
<summary>答案</summary>

* js 中的变量存储在 栈空间 和 堆空间中。函数的执行上下文会存储在栈空间中，执行上下文中的原始类型的值（布尔，数字，字符串）直接保存在栈空间中，而引用类型的值（对象）保存在堆空间中，栈空间中只保存引用类型在堆中的地址。栈来维护程序执行期间上下文的状态，如果所有的数据都存放在栈空间里面，那么会影响到上下文切换的效率，进而又影响到整个程序的执行效率。
* 栈内存的回收：调用栈在执行每一个函数的时候，有一个记录当前执行状态的指针（ESP），当一个函数执行结束，ESP 向下移动，指向另一个函数的执行上下文，上面函数的执行上下文虽然保存在栈内存中，但是已经是无效内存了。如果再有新的函数被调用，这块内存会直接被覆盖掉。
* 堆内存的回收：
    * 当栈中的 ESP 指针移动的时候，调用结束的执行上下文就无效了，但是它引用的引用类型数据依然占用堆中的空间。
    * 代际假说：
        1. 大部分对象在内存中存在的时间很短，简单来说，就是很多对象一经分配内存，很快就变得不可访问；
        2. 不死的对象，会活得更久。
    * V8 中会把堆分为 新生代 和 老生代 两个区域
        1. 新生代中存放的是生存时间短的对象。副垃圾回收器，主要负责新生代的垃圾回收。
        2. 老生代中存放的生存时间久的对象。主垃圾回收器，主要负责老生代的垃圾回收。
    * 不论什么类型的垃圾回收器，它们都有一套共同的执行流程：
        1. 标记空间中活动对象和非活动对象。
        2. 回收非活动对象所占据的内存。
        3. 内存整理
    * 副垃圾回收器：
        1. 新生代中用Scavenge 算法来处理。新生代空间对半划分为两个区域，一半是对象区域，一半是空闲区域
        2. 新加入的对象都会存放到对象区域，当对象区域快被写满时，就需要执行一次垃圾清理操作。
        3. 在垃圾回收过程中，首先要对对象区域中的垃圾做标记；标记完成之后，就进入垃圾清理阶段，副垃圾回收器会把这些存活的对象复制到空闲区域中，同时它还会把这些对象有序地排列起来，所以这个复制过程，也就相当于完成了内存整理操作，复制后空闲区域就没有内存碎片了。
        4. 完成复制后，对象区域与空闲区域进行角色翻转，也就是原来的对象区域变成空闲区域，原来的空闲区域变成了对象区域。这样就完成了垃圾对象的回收操作，同时这种角色翻转的操作还能让新生代中的这两块区域无限重复使用下去。
        5. 因为新生区的空间不大，所以很容易被存活的对象装满整个区域。为了解决这个问题，JavaScript 引擎采用了对象晋升策略，也就是经过两次垃圾回收依然还存活的对象，会被移动到老生区中。
    * 主垃圾回收器：
        1. 标记 - 清除（Mark-Sweep）：遍历调用栈，标记活动对象和垃圾数据。然后将垃圾数据清除掉。
        2. 标记 - 整理（Mark-Compact）：遍历调用栈，标记活动对象和垃圾数据，存活的对象都向一端移动，然后直接清理掉端边界以外的内存。
* 全停顿：

JavaScript 是运行在主线程之上的，一旦执行垃圾回收算法，都需要将正在执行的 JavaScript 脚本暂停下来，待垃圾回收完毕后再恢复脚本执行。我们把这种行为叫做全停顿（Stop-The-World）。

为了降低老生代的垃圾回收而造成的卡顿，V8 将标记过程分为一个个的子标记过程，同时让垃圾回收标记和 JavaScript 应用逻辑交替进行，直到标记阶段完成，我们把这个算法称为增量标记（Incremental Marking）算法。

使用增量标记算法，可以把一个完整的垃圾回收任务拆分为很多小的任务，这些小的任务执行时间比较短，可以穿插在其他的 JavaScript 任务中间执行，这样当执行上述动画效果时，就不会让用户因为垃圾回收任务而感受到页面的卡顿了。
</details> 
<br><br>

9. 什么是深度优先遍历和广度优先遍历？

<details>
<summary>答案</summary>

* 广度优先遍历是从指定节点开始遍历，先遍历相邻的节点，然后再一层一层地遍历之后相邻地节点，先广度后深度。
* 深度优先遍历是从指定节点开始遍历，先沿某一条路径遍历到最后一个节点，然后原路退回再遍历下一条路径，先深度后广度。
</details>
<br><br>

10. 如果我写了10个操作，都是改变页面背景色，每个颜色都不同，那么浏览器是怎么运作的，是依次把十个都执行一遍呢，还是优化成只改变最后一次？

<details>
<summary>答案</summary>

* js 代码不会优化成一次，会依次执行十次
* 但是渲染阶段浏览器会做一些优化，修改背景色并没有改变元素的几何形状，所以不会改变布局，不会触发回流，只会触发重绘。如果每次 DOM 操作都即时地反馈一次回流或重绘，那么性能上来说是扛不住的。于是浏览器自己缓存了一个 flush 队列，把我们触发的回流与重绘任务都塞进去，待到队列里的任务多起来、或者达到了一定的时间间隔，或者“不得已”的时候，再将这些任务一口气出队。
</details>
<br><br>

11. 浏览器的 reflow 是什么？

<details>
<summary>答案</summary>

reflow 重排是Web瀏覽器進程的名稱，用於重新計算文檔中元素的位置和幾何形狀，以重新呈現部分或全部文檔。由於重排是瀏覽器中的用戶阻止操作，因此對於開發人員了解如何縮短重排時間以及了解各種文檔屬性（DOM深度，CSS規則效率，不同類型的樣式更改）對重排的影響非常有用。時間。有時，重排文檔中的單個元素可能需要重排其父元素以及緊隨其後的所有元素。

以下是一些簡單的準則，可幫助您最大程度地減少網頁中的重排：

* 減少不必要的DOM深度。DOM樹中某一級別的更改會導致該樹的每個級別的更改-一直到根，一直到修改節點的子級。這導致花費更多的時間執行回流。
* 最小化CSS規則，並刪除未使用的CSS規則。
* 如果您進行複雜的渲染更改（例如動畫），請不要進行此操作。使用絕對位置或固定位置來完成此操作。
* 避免使用不必要的複雜CSS選擇器-特別是後代選擇器-這些選擇器需要更多的CPU能力來進行選擇器匹配。
</details>
<br><br>

12. 介绍一下js事件循环？

<details>
<summary>答案</summary>

* 单线程

JavaScript语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。

为什么不允许js可以实现多线程？因为如果实现了多线程，一个线程创建了一个div元素，而另外一个线程删除了这个div元素，那么这个时候浏览器应该听谁的？

所以为了避免出现这种互相冲突的操作，js从一开始就是单线程的，这就是它的核心特征。

* 任务队列

单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。

如果排队是因为计算量大，CPU忙不过来，倒也算了，但是很多时候CPU是闲着的，因为IO设备（输入输出设备）很慢（比如Ajax操作从网络读取数据），不得不等着结果出来，再往下执行。

JavaScript语言的设计者意识到，这时主线程完全可以不管IO设备，挂起处于等待中的任务，先运行排在后面的任务。等到IO设备返回了结果，再回过头，把挂起的任务继续执行下去。

于是，所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
主线程不断重复上面的第三步。

* 事件循环（event loop）

主线程从"任务队列"中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）。

主线程运行的时候，产生堆（heap）和栈（stack），栈中的代码调用各种外部API，它们在"任务队列"中加入各种事件（click，load，done）。只要栈中的代码执行完毕，主线程就会去读取"任务队列"，依次执行那些事件所对应的回调函数。

</details>
<br><br>

13. 如果想实现 `a === 1 && a === 2 && a === 3` 为 true，怎么实现？

<details>
<summary>答案</summary>

```js
const a = (function() {
    let i = 1;
    return {
        valueOf: function() {
            return i++;
        }
    }
})();

console.log(a == 1 && a == 2 && a == 3); // true
```

```js
let i = 1;

Reflect.defineProperty(this, 'a', {
    get() {
        return i++;
    }
});

console.log(a === 1 && a === 2 && a === 3);
```
</details>
<br><br>

14. 排序算法说一说

<details>
<summary>答案</summary>

```js
function bubble (arr) {
    if (arr.length <= 1) return arr;
    
    for (let i = 0; i < arr.length - 1; i++) {
        for (let j = 0; j < arr.length - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
            }
        }
    }
    return arr;
}

// 优化
function bubbleOptimise (arr) {
    if (arr.length <= 1) return arr;
    
    for (let i = 0; i < arr.length - 1; i++) {
        let hasChange = false;
        for (let j = 0; j < arr.length - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
            }
        }
        if (!hasChange) break;
    }
    return arr;
}

function insert (arr) {
    if (arr.length <= 1) return arr;

    let preIndex;
    let current;

    for (let i = 1; i < arr.length; i++) {
        current = arr[i];
        preIndex = i - 1;

        while (i >= 0 && arr[preIndex] > current) {
            arr[preIndex + 1] = arr[preIndex];
            preIndex--;
        }

        if (preIndex + 1 !== i) {
            arr[preIndex + 1] = current;
        }
    }
    return arr
}

function insertOptimise (arr) {
    if (arr.length <= 1) return arr;
    let current, low, high, middle

    for (let i = 1; i < arr.length; i++) {
        low = 0;
        high = i - 1;
        current = arr[i];

        while (low <= high) {
            middle = Math.floor((high + low) / 2);
            if (current >= arr[middle]) {
                low = middle + 1;
            } else {
                high = middle - 1;
            }
        }

        for (let j = i; j > low; j--) {
            arr[j] = arr[j - 1];
        }
        arr[low] = current;
    }
    return arr;
}


 function quickSort (arr) {
　　if (arr.length <= 1) { return arr; }
    let pivotIndex = Math.floor(arr.length / 2);

　　let pivot = arr.splice(pivotIndex, 1)[0];
　　let left = [];
　　let right = [];

　　for (var i = 0; i < arr.length; i++){ 
　　　　if (arr[i] < pivot) {
　　　　　　left.push(arr[i]);
　　　　} else {
　　　　　　right.push(arr[i]);
　　　　}
　　}
　　return quickSort(left).concat([pivot], quickSort(right));
};


```
</details>
<br><br>

15. 去重

<details>
<summary>答案</summary>

```js
function duplicateRemoval (arr) {
    return [...new Set(arr)];
}

function duplicateRemoval (arr) {
    return Object.keys(arr.reduce((acc, i) => (acc[i] = i, acc), {}))
}

function duplicateRemoval (arr) {
    return arr.reduce((acc, i) => (!acc.includes(i) ? acc.push(i) : '', acc) , [])
}

```
</details>
<br><br>

16. 垂直居中

<details>
<summary>答案</summary>

实现子容器水平、垂直居中对齐的方式有（父容器，子容器宽高不确定。）：

  1. 父容器.parent{text-align: center; display: table-cell; vertiacal-align: middle;}。 子容器.child{display: inline-block;}

  2. 利用定位。父容器.parent{position: relative;} 子容器.child{position: absolute; top: 50%, left: 50%; transform: translate(-50%, -50%); }

  3. 利用弹性布局。父容器display: flex; justify-content: center; align-items: center;
</details>
<br><br>

17. css 伪类 伪元素

<details>
<summary>答案</summary>

* 伪类
    1. :first-child 一组兄弟元素中第一个元素
    2. :first-of-type 一组兄弟元素中指定类型的第一个元素
    3. :last-child 一组兄弟元素中最后一个元素
    4. :last-of-type 一组兄弟元素中指定类型的最后一个元素
    5. :not 匹配不符合参数选择器的元素
    6. :nth-child(an+b) 先找到当前元素的兄弟元素，然后按照位置的先后顺序从 1 开始排序
    7. :fullscreen 应用于当前处于全屏显示模式的元素
* 尾元素
    1. ::after(:after) 使用 ::after 会创建一个伪元素，该伪元素会成为选中元素的最后一个子元素
    2. ::before(:before)使用 ::before 会创建一个伪元素，该伪元素会成为选中元素的第一个子元素
    3. ::selection 用于文档中被用户高亮的部分
</details>
<br><br>





```js
/**
5.如何做兼容（js，babel、 ployfill， css， postcss， 根据具体的浏览器）
7.webpack 你做过那些配置
8. this.$nextTick
9.key 与 refs 干啥用的
10. vue-router 有几种模式，区别是啥
11. cookie ，sessionStorage 以及 localStorage 区别，是啥
12. cookie ，sessionStorage ，localStorage 以及vuex 在浏览器刷新的时候那些有哪些没有，能干啥
 16.vue 数据传值
 17.讲讲vuex
 18.讲讲react
 19. v-if 与 v-for 在同一个元素里谁的优先级高
 20. 如何使用addRoute 进行路由动态注册

*/
```


