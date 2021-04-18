# learn-lgwebdream-FE-Interview

学习整理 [lgwebdream](https://github.com/lgwebdream) 的 [前端题](https://github.com/lgwebdream/FE-Interview)。

<br><br><br>

***

1. 写一个 mySetInterVal(fn, a, b)，每次间隔 a, a+b, a+2b, ..., a+nb 的时间，然后写一个 myClear，停止上面的 mySetInterVal。[链接](https://github.com/lgwebdream/FE-Interview/issues/7)

<details>
<summary>答案</summary>

实现一

```js
function mySetInterVal (fn, a, b) {
    this.a = a;
    this.b = b;
    this.count = 0;
    this.timer = null;

    this.start = () => {
        this.timer = setTimeout(() => {
            console.log('运行')
            fn();
            this.count++;
            this.start();
        }, this.a + this.count * this.b)
    };

    this.clear = () => {
        clearTimeout(this.timer);
        this.count = 0;
        console.log('结束')
    }
}

let t = new mySetInterVal(() => {}, 1000, 1000);
t.start();

setTimeout(() => {
    t.clear();
}, 20000);
```

实现二

```js
function mySetInterVal (fn, a, b) {
    let timer = null;

    const loop = (time) => {
        timer = setTimeout(() => {
            console.log('运行')
            fn()
            loop(time + b);
        }, time);
    }
    loop(a);

    return () => {
        clearTimeout(timer);
        console.log('结束')
    }
}

let clear = mySetInterVal(() => {}, 1000, 1000);

setTimeout(() => {
    clear();
}, 20000);
```
</details>
<br><br>

2. 合并二维有序数组成一维有序数组，归并排序的思路？[链接](https://github.com/lgwebdream/FE-Interview/issues/8)

<details>
<summary>答案</summary>

这个题目其实提出了两个问题，一个是数组的展平，一个是归并排序的实现。

解法一，利用 Generator 函数返回 Iterator 的特性，递归调用自己，然后使用扩展运算符将 Iterator 展开变成数组，归并排序直接使用标准实现就可以了。

```js
function mergeSort (arr) {
    const len = arr.length;

    if (len < 2) {
        return arr;
    }

    const middle = Math.floor(len / 2),
        left = arr.slice(0, middle),
        right = arr.slice(middle);

    return merge(mergeSort(left), mergeSort(right));
}

function merge (left, right) {
    const result = [];

    while (left.length && right.length) {
        if (left[0] <= right[0]) {
            result.push(left.shift());
        } else {
            result.push(right.shift());
        }
    }

    while (left.length) {
        result.push(left.shift());
    }

    while (right.length) {
        result.push(right.shift());
    }

    return result;
}

function* flat (arr) {
    if (Array.isArray(arr)) {
        for (let i = 0; i < arr.length; i++) {
            yield* flat(arr[i]);
        }
    } else {
        yield arr;
    }
}

function mergeSortFlatten () {
    return mergeSort([...flat([...arguments])]);
}

let arr1 = [[1,2,3],[4,5,6],[7,8,9],[1,2,3],[4,5,6]];
let arr2 = [[1,4,6],[7,8,10],[2,6,9],[3,7,13],[1,5,12]];

mergeSortFlatten (arr1, arr2);
// [1, 1, 1, 1, 2, 2, 2, 3, 3, 3, 4, 4, 4, 5, 5, 5, 6, 6, 6, 6, 7, 7, 7, 8, 8, 9, 9, 10, 12, 13]
```

解法二，直接使用现成的方法，但是这样没有实现归并排序。

```js
function mergeSortFlatten (...arr) {
    return arr.flat(Infinity).sort((a, b) => a - b )
}

let arr1 = [[1,2,3],[4,5,6],[7,8,9],[1,2,3],[4,5,6]];
let arr2 = [[1,4,6],[7,8,10],[2,6,9],[3,7,13],[1,5,12]];

mergeSortFlatten (arr1, arr2);
// [1, 1, 1, 1, 2, 2, 2, 3, 3, 3, 4, 4, 4, 5, 5, 5, 6, 6, 6, 6, 7, 7, 7, 8, 8, 9, 9, 10, 12, 13]
```
</details>
<br><br>

3. 多种方式实现斐波那契数列？[链接](https://github.com/lgwebdream/FE-Interview/issues/9)

<details>
<summary>答案</summary>

实现一

```js
function fib (n) {
    if (n < 0) throw new Error('输入的数字不能小于0');
    if (n === 2) {
        return 1;
    }
    if (n === 1) {
        return 0;
    }
    return fib(n - 1) + fib(n - 2);
}
```

实现二

```js
function fib (n) {
    if (n < 0) throw new Error('输入的数字不能小于0');
    if (n === 2) {
        return 1;
    }
    if (n === 1) {
        return 0;
    }
    function _fib (n, a, b) {
        if (n === 1) return a;
        return _fib(n - 1, b, a + b);
    }
    return _fib(n, 0, 1);
}

```

实现三

```js
function fibonacci (n) {
    if (n === 2) {
        return 1;
    }
    if (n === 1) {
        return 0;
    }
    const r = [0, 1]
    let i = n - 2;
    while (i--) {
        r.push(r[r.length - 1] + r[r.length - 2]);
    }
    return r[n - 1];
}
fibonacci(10)
// 34
```

实现四

```js
function* fib () {
    let [prev, curr] = [0, 1];
    yield prev;
    for (;;) {
        yield curr;
        [prev, curr] = [curr, prev + curr];
    }
}

for (let n of fib()) {
    if (n > 1000) break;
    console.log(n);
}
// 0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
```

</details>
<br><br>

4. 字符串中出现的不重复最长长度？[链接](https://github.com/lgwebdream/FE-Interview/issues/10)

<details>
<summary>答案</summary>

实现一

```js
//循环字符串，没有重复的就存下每次的字符的位置和字符自身，按顺序排列，有重复的就重置循环变量从索引低的重复字符处后一位重新开始循环，缺点，循环次数过多

function lengthOfLongestSubstring (str) {
    if (str.length <= 1) {
        return str.length;
    }
    let t = [];
    let i = 0
    while (i < str.length) {
        let has = t.findIndex(item => item.char === str[i]);
        if (has >= 0) {
            i = t[has].pos;
            t = [];
        } else {
            t.push({
                char: str[i],
                pos: i
            });
        }
        i++;
    }
    
    return t.length;
}

// 优化一下
function lengthOfLongestSubstring (str) {
    let t = {};
    let res = 0;
    let j = 0;

    for (let i = 0; i < str.length; i++) {
        if (t[str[i]]) {
            j = t[str[i]];
        }
        res = i - j;
        t[str[i]] = i;
    }
    return res;
}
```

实现二

```js
//把字符串变成字符数组，reduce遍历数组，累计值就是最新的最长无重复字符的子字符串

function lengthOfLongestSubstring (s) {
    const arr = [...s]
    let res = 1;
    let result = arr.reduce((total, cur, i, arr) => {
        if (i == 0) {
            return cur;
        } else {
            if (total.indexOf(cur) < 0) {
                return total + cur
            } else if (res < total.length) {
                res = total.length
                return total.slice(total.indexOf(cur) + 1, total.length) + cur
            } else {
                return total.slice(total.indexOf(cur) + 1, total.length) + cur
            }
        }
    }, "")
    if (res < result.length) {
        res = result.length
    }

    return res
}
```

实现三

```js
// map 存下不重复字符的位置，i是不重复子串的起始位置，如果有重复字符就重置i，j - i 是不重复子串的长度

function lengthOfLongestSubstring (s) {
    let map = new Map();
    let i = -1
    let res = 0
    for (let j = 0; j < s.length; j++) {
        if (map.has(s[j])) {
            i = Math.max(i, map.get(s[j]))
        }
        res = Math.max(res, j - i)
        map.set(s[j], j)
    }
    return res
}

console.log(lengthOfLongestSubstring("loddktdji"))
console.log(lengthOfLongestSubstring("dvdf"))
console.log(lengthOfLongestSubstring("adfafwefffdasdcx"))
```
</details>
<br><br>

5. 介绍chrome 浏览器的几个版本？[链接](https://github.com/lgwebdream/FE-Interview/issues/11)

<details>
<summary>答案</summary>

Chrome 浏览器提供 4 种发布版本，即稳定版(Stable)、测试版(Beta)、开发者版(Dev)和金丝雀版(Canary)。<br>

虽然 Chrome 这几个版本名称各不相同，但都沿用了相同的版本号，只是更新早晚的区别。就好比 iOS 等系统，Beta 版可以率先更新到 iOS 12 并进行测试，不断改进稳定后，正式版才升级到 12 版本。<br>

Chrome 也是如此，更新最快的 Canary 会领先正式版 1-2 个版本。

1. Canary（金丝雀） 版

只限用于测试，Canary 是 Chrome 的未来版本，是功能、代码最先进的Chrome 版本，一方面软件本身没有足够时间测试，另一方面网页也不一定支持这些全新的功能，因此极不稳定。好在，谷歌将其设定为可独立安装、与其他版本的 Chrome 程序共存，因此适合进阶用户安装备用，尝鲜最新功能。这种不稳定性使得 Canary 版目前并不适合日常使用。
Chrome Canary 是更新速度最快的 Chrome 版本，几乎每天更新。它相当于支持自动更新、并添加了谷歌自家服务与商业闭源插件（Flash 等）的 Chromium，更加强大好用。

2. 开发者版(Dev)

Chrome Dev 最初是以 Chromium 为基础、更新最快的 Chrome，后来则被 Canary 取代。Dev 版每周更新一次，虽然仍不太稳定，但已经可以勉强满足日常使用，适合 Web 开发者用来测试新功能和网页。
让 IT 人员使用开发者版，开发者可以通过开发者版测试自己公司的应用，确保这些应用能与Chrome 最新的 API 更改及功能更改兼容。注意：开发者版并非百分之百稳定，但开发者可以提前 9 至 12 周体验即将添加到 Chrome 稳定版的功能。

3. 测试版(Beta)

Chrome Beta 以 Dev 为基础，每月更新一次。它是正式发布前的最后测试版本，所有功能都已在前面几个版本中得到测试并改进，因此已经十分稳定，普通用户也可以用来日常使用
让 5% 的用户使用测试版，测试版用户可以提前 4-6 周体验即将在 Chrome 稳定版中推出的功能。测试版用户可以发现特定版本可能存在的问题，让您可以先解决问题，然后再向所有用户推出该版本。

4. 稳定版(Stable)

最后的 Chrome Stable 就是我们熟知的正式版，它以 Beta 为基础，几个月更新一次。由于所有的功能都已经过数个月反复测试，是稳定性最高的 Chrome 版本。
让大多数用户使用稳定版，稳定版是已进行充分测试的版本，稳定版每 2-3 周会进行一次小幅更新，并且每 6 周会进行一次重大更新。
所以要定期下载开发者版，体验Chrome 最新的 API和新功能 ，发现自己的应用跟新API和新功能的是否有兼容问题，找到开发亮点。

* 对于Chrome的历史版本测试

可以使用Docker Selenium 做分布式自动化测试，部署多个重点关注的版本，进行自动化测试，对比差异。

</details>
<br><br>

6. React 项目中有哪些细节可以优化？实际开发中都做过哪些性能优化？[链接](https://github.com/lgwebdream/FE-Interview/issues/12)

<details>
<summary>答案</summary>

对于正常的项目优化，一般都涉及到几个方面，开发过程中、上线之后的首屏、运行过程的状态 <br>

* 来聊聊上线之后的首屏及运行状态：

* 首屏优化一般涉及到几个指标FP、FCP、FMP；要有一个良好的体验是尽可能的把FCP提前，需要做一些工程化的处理，去优化资源的加载方式及分包策略，资源的减少是最有效的加快首屏打开的方式；

* 对于CSR的应用，FCP的过程一般是首先加载js与css资源，js在本地执行完成，然后加载数据回来，做内容初始化渲染，这中间就有几次的网络反复请求的过程；所以CSR可以考虑使用骨架屏及预渲染（部分结构预渲染）、suspence与lazy做懒加载动态组件的方式。

* 当然还有另外一种方式就是SSR的方式，SSR对于首屏的优化有一定的优势，但是这种瓶颈一般在Node服务端的处理，建议使用stream流的方式来处理，对于体验与node端的内存管理等，都有优势；

* 不管对于CSR或者SSR，都建议配合使用Service worker，来控制资源的调配及骨架屏秒开的体验

* react项目上线之后，首先需要保障的是可用性，所以可以通过React.Profiler分析组件的渲染次数及耗时的一些任务，但是Profile记录的是commit阶段的数据，所以对于react的调和阶段就需要结合performance API一起分析；

* 由于React是父级props改变之后，所有与props不相关子组件在没有添加条件控制的情况之下，也会触发render渲染，这是没有必要的，可以结合React的PureComponent以及React.memo等做浅比较处理，这中间有涉及到不可变数据的处理，当然也可以结合使用ShouldComponentUpdate做深比较处理；

* 所有的运行状态优化，都是减少不必要的render，React.useMemo与React.useCallback也是可以做很多优化的地方；

* 在很多应用中，都会涉及到使用redux以及使用context，这两个都可能造成许多不必要的render，所以在使用的时候，也需要谨慎的处理一些数据；

* 最后就是保证整个应用的可用性，为组件创建错误边界，可以使用componentDidCatch来处理；


* 实际项目中开发过程中还有很多其他的优化点：

1. 保证数据的不可变性
2. 使用唯一的键值迭代
3. 使用web worker做密集型的任务处理
4. 不在render中处理数据
5. 不必要的标签，使用React.Fragments

</details>
<br><br>

7. react 最新版本解决了什么问题？加了哪些东西？[链接](https://github.com/lgwebdream/FE-Interview/issues/13)

<details>
<summary>答案</summary>

1. React 16.x的三大新特性 Time Slicing, Suspense，hooks

* Time Slicing（解决CPU速度问题）使得在执行任务的期间可以随时暂停，跑去干别的事情，这个特性使得react能在性能极其差的机器跑时，仍然保持有良好的性能
* Suspense （解决网络IO问题）和lazy配合，实现异步加载组件。 能暂停当前组件的渲染, 当完成某件事以后再继续渲染，解决从react出生到现在都存在的「异步副作用」的问题，而且解决得非常的优雅，使用的是「异步但是同步的写法」，我个人认为，这是最好的解决异步问题的方式
*  此外，还提供了一个内置函数 componentDidCatch，当有错误发生时, 我们可以友好地展示 fallback 组件；可以捕捉到它的子元素（包括嵌套子元素）抛出的异常；可以复用错误组件。

2. React16.8

* 加入hooks，让React函数式组件更加灵活

hooks之前，React存在很多问题

在组件间复用状态逻辑很难<br>
复杂组件变得难以理解，高阶组件和函数组件的嵌套过深。<br>
class组件的this指向问题<br>
难以记忆的生命周期<br>

* hooks很好的解决了上述问题，hooks提供了很多方法

useState 返回有状态值，以及更新这个状态值的函数<br>
useEffect 接受包含命令式，可能有副作用代码的函数。<br>
useContext 接受上下文对象（从React.createContext返回的值）并返回当前上下文值，<br>
useReducer useState的替代方案。接受类型为(state，action) => newState的reducer，并返回与dispatch方法配对的当前状态。<br>
useCallback 返回一个回忆的memoized版本，该版本仅在其中一个输入发生更改时才会更改。纯函数的输入输出确定性<br>
useMemo 纯的一个记忆函数<br>
useRef 返回一个可变的ref对象，其.current属性被初始化为传递的参数，返回的 ref 对象在组件的整个生命周期内保持不变。<br>
useImperativeMethods 自定义使用ref时公开给父组件的实例值<br>
useMutationEffect 更新兄弟组件之前，它在React执行其DOM改变的同一阶段同步触发<br>
useLayoutEffect DOM改变后同步触发。使用它来从DOM读取布局并同步重新渲染<br>

3. React16.9

* 重命名 Unsafe 的生命周期方法。新的 UNSAFE_ 前缀将有助于在代码 review 和 debug 期间，使这些有问题的字样更突出
* 废弃 javascript: 形式的 URL。以 javascript: 开头的 URL 非常容易遭受攻击，造成安全漏洞。
* 废弃 “Factory” 组件。 工厂组件会导致 React 变大且变慢。
* act() 也支持异步函数，并且你可以在调用它时使用 await。
* 使用 `<React.Profiler>` 进行性能评估。 在较大的应用中追踪性能回归可能会很方便

4. React16.13.0

* 支持在渲染期间调用setState，但仅适用于同一组件
* 可检测冲突的样式规则并记录警告
* 废弃unstable_createPortal，使用createPortal
* 将组件堆栈添加到其开发警告中，使开发人员能够隔离bug并调试其程序，这可以清楚地说明问题所在，并更快地定位和修复错误。

</details>
<br><br>

8. 说一下 Http 缓存策略，有什么区别，分别解决了什么问题？[链接](https://github.com/lgwebdream/FE-Interview/issues/14)

<details>
<summary>答案</summary>

1. 浏览器缓存策略

浏览器每次发起请求时，先在本地缓存中查找结果以及缓存标识，根据缓存标识来判断是否使用本地缓存。如果缓存有效，则使用本地缓存；否则，则向服务器发起请求并携带缓存标识。根据是否需向服务器发起HTTP请求，将缓存过程划分为两个部分：强制缓存和协商缓存，强缓优先于协商缓存。

* 强缓存，服务器通知浏览器一个缓存时间，在缓存时间内，下次请求，直接用缓存，不在时间内，执行比较缓存策略。
* 协商缓存，让客户端与服务器之间能实现缓存文件是否更新的验证、提升缓存的复用率，将缓存信息中的Etag和Last-Modified 通过请求发送给服务器，由服务器校验，返回304状态码时，浏览器直接使用缓存。

HTTP缓存都是从第二次请求开始的：

* 第一次请求资源时，服务器返回资源，并在response header中回传资源的缓存策略；
* 第二次请求时，浏览器判断这些请求参数，击中强缓存就直接200，否则就把请求参数加到request header头中传给服务器，看是否击中协商缓存，击中则返回304，否则服务器会返回新的资源。这是缓存运作的一个整体流程图：

![picture](./img/http-cache.png)

2. 强缓存

* 强缓存命中则直接读取浏览器本地的资源，在network中显示的是from memory或者from disk
* 控制强制缓存的字段有：Cache-Control（http1.1）和Expires（http1.0）
* Cache-control是一个相对时间，用以表达自上次请求正确的资源之后的多少秒的时间段内缓存有效。
* Expires是一个绝对时间。用以表达在这个时间点之前发起请求可以直接从浏览器中读取数据，而无需发起请求
* Cache-Control的优先级比Expires的优先级高。前者的出现是为了解决Expires在浏览器时间被手动更改导致缓存判断错误的问题。

如果同时存在则使用Cache-control。

3. 强缓存-expires

* 该字段是服务器响应消息头字段，告诉浏览器在过期时间之前可以直接从浏览器缓存中存取数据。
* Expires 是 HTTP 1.0 的字段，表示缓存到期时间，是一个绝对的时间 (当前时间+缓存时间)。在响应消息头中，设置这个字段之后，就可以告诉浏览器，在未过期之前不需要再次请求。
* 由于是绝对时间，用户可能会将客户端本地的时间进行修改，而导致浏览器判断缓存失效，重新请求该资源。此外，即使不考虑修改，时差或者误差等因素也可能造成客户端与服务端的时间不一致，致使缓存失效。
* 优势特点
    * HTTP 1.0 产物，可以在HTTP 1.0和1.1中使用，简单易用。
    * 以时刻标识失效时间。
* 劣势问题
    * 时间是由服务器发送的(UTC)，如果服务器时间和客户端时间存在不一致，可能会出现问题。
    * 存在版本问题，到期之前的修改客户端是不可知的。

4. 强缓存-cache-control

* 已知Expires的缺点之后，在HTTP/1.1中，增加了一个字段Cache-control，该字段表示资源缓存的最大有效时间，在该时间内，客户端不需要向服务器发送请求。
* 这两者的区别就是前者是绝对时间，而后者是相对时间。下面列举一些 Cache-control 字段常用的值：(完整的列表可以查看MDN)
    * max-age：即最大有效时间。
    * must-revalidate：如果超过了 max-age 的时间，浏览器必须向服务器发送请求，验证资源是否还有效。
    * no-cache：不使用强缓存，需要与服务器验证缓存是否新鲜。
    * no-store: 真正意义上的“不要缓存”。所有内容都不走缓存，包括强制和对比。
    * public：所有的内容都可以被缓存 (包括客户端和代理服务器， 如 CDN)
    * private：所有的内容只有客户端才可以缓存，代理服务器不能缓存。默认值。
* Cache-control 的优先级高于 Expires，为了兼容 HTTP/1.0 和 HTTP/1.1，实际项目中两个字段都可以设置。
* 该字段可以在请求头或者响应头设置，可组合使用多种指令：
    * 可缓存性
        * public：浏览器和缓存服务器都可以缓存页面信息
        * private：default，代理服务器不可缓存，只能被单个用户缓存
        * no-cache：浏览器器和服务器都不应该缓存页面信息，但仍可缓存，只是在缓存前需要向服务器确认资源是否被更改。可配合private，过期时间设置为过去时间。
        * only-if-cache：客户端只接受已缓存的响应
    * 到期
        * max-age=：缓存存储的最大周期，超过这个周期被认为过期。
        * s-maxage=：设置共享缓存，比如can。会覆盖max-age和expires。
        * max-stale=：客户端愿意接收一个已经过期的资源
        * min-fresh=：客户端希望在指定的时间内获取最新的响应
        * stale-while-revalidate=：客户端愿意接收陈旧的响应，并且在后台一部检查新的响应。时间代表客户端愿意接收陈旧响应的时间长度。
        * stale-if-error=：如新的检测失败，客户端则愿意接收陈旧的响应，时间代表等待时间。
    * 重新验证和重新加载
        * must-revalidate：如页面过期，则去服务器进行获取。
        * proxy-revalidate：用于共享缓存。
        * immutable：响应正文不随时间改变。
    * 其他
        * no-store：绝对禁止缓存
        * no-transform：不得对资源进行转换和转变。例如，不得对图像格式进行转换。

* 优势特点
    * HTTP 1.1 产物，以时间间隔标识失效时间，解决了Expires服务器和客户端相对时间的问题。
    * 比Expires多了很多选项设置。
* 劣势问题
    * 存在版本问题，到期之前的修改客户端是不可知的。

5. 协商缓存

* 协商缓存的状态码由服务器决策返回200或者304
* 当浏览器的强缓存失效的时候或者请求头中设置了不走强缓存，并且在请求头中设置了If-Modified-Since 或者 If-None-Match 的时候，会将这两个属性值到服务端去验证是否命中协商缓存，如果命中了协商缓存，会返回 304 状态，加载浏览器缓存，并且响应头会设置 Last-Modified 或者 ETag 属性。
* 协商缓存在请求数上和没有缓存是一致的，但如果是 304 的话，返回的仅仅是一个状态码而已，并没有实际的文件内容，因此 在响应体体积上的节省是它的优化点。
* 协商缓存有 2 组字段(不是两个)，控制协商缓存的字段有：Last-Modified/If-Modified-since（http1.0）和 Etag/If-None-match（http1.1）
* Last-Modified/If-Modified-since表示的是服务器的资源最后一次修改的时间；Etag/If-None-match表示的是服务器资源的唯一标
识，只要资源变化，Etag就会重新生成。
* Etag/If-None-match的优先级比Last-Modified/If-Modified-since高。

6. 协商缓存-协商缓存-Last-Modified/If-Modified-since

    1. 服务器通过 Last-Modified 字段告知客户端，资源最后一次被修改的时间，例如 Last-Modified: Mon, 10 Nov 2018 09:10:11 GMT
    2. 浏览器将这个值和内容一起记录在缓存数据库中。
    3. 下一次请求相同资源时时，浏览器从自己的缓存中找出“不确定是否过期的”缓存。因此在请求头中将上次的 Last-Modified 的值写入到请求头的 If-Modified-Since 字段
    4. 服务器会将 If-Modified-Since 的值与 Last-Modified 字段进行对比。如果相等，则表示未修改，响应 304；反之，则表示修改了，响应 200 状态码，并返回数据。

* 优势特点
    * 不存在版本问题，每次请求都会去服务器进行校验。服务器对比最后修改时间如果相同则返回304，不同返回200以及资源内容。
* 劣势问题
    * 只要资源修改，无论内容是否发生实质性的变化，都会将该资源返回客户端。例如周期性重写，这种情况下该资源包含的数据实际上一样的。
    * 以时刻作为标识，无法识别一秒内进行多次修改的情况。 如果资源更新的速度是秒以下单位，那么该缓存是不能被使用的，因为它的时间单位最低是秒。
    * 某些服务器不能精确的得到文件的最后修改时间。
    * 如果文件是通过服务器动态生成的，那么该方法的更新时间永远是生成的时间，尽管文件可能没有变化，所以起不到缓存的作用。

7. 协商缓存-Etag/If-None-match

* 为了解决上述问题，出现了一组新的字段 Etag 和 If-None-Match
* Etag 存储的是文件的特殊标识(一般都是 hash 生成的)，服务器存储着文件的 Etag 字段。之后的流程和 Last-Modified 一致，只是 Last-Modified 字段和它所表示的更新时间改变成了 Etag 字段和它所表示的文件 hash，把 If-Modified-Since 变成了 If-None-Match。服务器同样进行比较，命中返回 304, 不命中返回新资源和 200。
* 浏览器在发起请求时，服务器返回在Response header中返回请求资源的唯一标识。在下一次请求时，会将上一次返回的Etag值赋值给If-No-Matched并添加在Request Header中。服务器将浏览器传来的if-no-matched跟自己的本地的资源的ETag做对比，如果匹配，则返回304通知浏览器读取本地缓存，否则返回200和更新后的资源。
* Etag 的优先级高于 Last-Modified。

* 优势特点
    1. 可以更加精确的判断资源是否被修改，可以识别一秒内多次修改的情况。
    2. 不存在版本问题，每次请求都回去服务器进行校验。

* 劣势问题
    1. 计算ETag值需要性能损耗。
    2. 分布式服务器存储的情况下，计算ETag的算法如果不一样，会导致浏览器从一台服务器上获得页面内容后到另外一台服务器上进行验证时现ETag不匹配的情况。
</details>
<br><br>

9. 介绍防抖节流原理、区别以及应用，并用JavaScript进行实现 [链接](https://github.com/lgwebdream/FE-Interview/issues/15)

<details>
<summary>答案</summary>

1. 防抖 debounce

解释：防抖就是将单位时间内的多次函数调用合并成一次，推迟到单位时间之后触发，或者在等待单位时间之前就立即触发。

实现思路：用一个变量存下定时器，每次函数被触发的时候都会重置定时器重新计时，这样直到不再触发函数，定时器才能正常被执行然后调用目标函数。

使用场景：

* 输入框监听输入事件或者按键抬起事件，会被频繁触发，防抖后只在停止输入了才去触发函数做搜索或者其他操作
* 防止多次点击按钮提交请求

实现一：

```js
function debounce (fn, wait) {
    let timer;
    return function () {
        const context = this;
        const args = arguments;
        clearTimeout(timer);
        timer = setTimeout(() => {
            fn.apply(context, args)
        }, wait);
    }
}
```

实现二：

```js
function debounce (func, wait, immediate) { // 有时希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行。
  let timeout;
  return function () {
    const context = this;
    const args = arguments;
    if (timeout) clearTimeout(timeout);
    if (immediate) {
      const callNow = !timeout;
      timeout = setTimeout(function () {
        timeout = null;
      }, wait)
      if (callNow) func.apply(context, args)
    } else {
      timeout = setTimeout(function () {
        func.apply(context, args)
      }, wait);
    }
  }
}
```

实现三：

```js
// func函数可能会有返回值，所以需要返回函数结果，但是当 immediate 为 false 的时候，因为使用了 setTimeout ，我们将 func.apply(context, args) 的返回值赋给变量，最后再 return 的时候，值将会一直是 undefined，所以只在 immediate 为 true 的时候返回函数的执行结果。
function debounce (func, wait, immediate) {
  let timeout, result;
  return function () {
    const context = this;
    const args = arguments;
    if (timeout) clearTimeout(timeout);
    if (immediate) {
      const callNow = !timeout;
      timeout = setTimeout(function () {
        timeout = null;
      }, wait)
      if (callNow) result = func.apply(context, args)
    }
    else {
      timeout = setTimeout(function () {
        func.apply(context, args)
      }, wait);
    }
    return result;
  }
}
```

2. 节流 throttle

解释：规定在单位时间内只能触发一次函数。

使用场景：

* 窗口改变大小，resize 事件
* 拖拽滚动条，scroll 事件
* 拖拽 html 元素

实现一：

```js
function throttle(func, wait) {
  let timeout;
  return function () {
    const context = this;
    const args = arguments;
    if (!timeout) {
      timeout = setTimeout(function () {
        timeout = null;
        func.apply(context, args)
      }, wait)
    }

  }
}
```

实现二：

```js
function throttle(func, wait) {
  let context, args;
  let previous = 0;

  return function () {
    let now = +new Date();
    context = this;
    args = arguments;
    if (now - previous > wait) {
      func.apply(context, args);
      previous = now;
    }
  }
}
```

</details>
<br><br>