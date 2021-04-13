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