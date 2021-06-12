# js

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

5. 介绍防抖节流原理、区别以及应用，并用JavaScript进行实现 [链接](https://github.com/lgwebdream/FE-Interview/issues/15)

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

6. 约瑟夫问题使用递归方法解决？

<details>
<summary>答案</summary>

## 约瑟夫环问题解析

## 历史

约瑟夫问题（有时也称为约瑟夫斯置换），是一个出现在计算机科学和数学中的问题。在计算机编程的算法中，类似问题又称为约瑟夫环。

人们站在一个等待被处决的圈子里。 计数从圆圈中的指定点开始，并沿指定方向围绕圆圈进行。 在跳过指定数量的人之后，处刑下一个人。 对剩下的人重复该过程，从下一个人开始，朝同一方向跳过相同数量的人，直到只剩下一个人，并被释放。

问题即，给定人数、起点、方向和要跳过的数字，选择初始圆圈中的位置以避免被处决。

这个问题是以弗拉维奥·约瑟夫命名的，他是1世纪的一名犹太历史学家。他在自己的日记中写道，他和他的40个战友被罗马军队包围在洞中。他们讨论是自杀还是被俘，最终决定自杀，并以抽签的方式决定谁杀掉谁。约瑟夫斯和另外一个人是最后两个留下的人。约瑟夫斯说服了那个人，他们将向罗马军队投降，不再自杀。约瑟夫斯把他的存活归因于运气或天意，他不知道是哪一个。

## leetcode 原题

[原题链接](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof)

0, 1, ···, n-1 这 n 个数字排成一个圆圈，从数字 0 开始，每次从这个圆圈里删除第 m 个数字（删除后从下一个数字开始计数）。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4 这 5 个数字组成一个圆圈，从数字 0 开始每次删除第 3 个数字，则删除的前 4 个数字依次是 2、0、4、1，因此最后剩下的数字是 3。

## 递归解题思路

如果记上述问题为函数 f(n, m)，这时第一个被删除的数字为 (m - 1) % n。当第一个数字被删除后，第二轮删除将从 m % n 处开始，而第二轮删除则可以记为函数 f(n - 1, m)。将 f(n, m) 的问题转化为了 f(n - 1, m) 的问题，所以可以使用递归求解，只需要知道上一轮的 f(n, m) 和下一轮的 f(n - 1, m) 的关系即可。

我们这里将第一轮的数字画成图观察：

```
// 第一轮没有删除数字之前如下，一共 0 ~ (n - 1) 个数字

0
1
...
(m - 1) % n
m % n
...
n - 1

```

// 而第一轮删除后的数字变成了如下形式：

```
// (m - 1) % n 被删除

0
1
...
(m - 2) % n
m % n
...
n - 1

```

(m - 1) % n 被删除后，排在它前面的所有数字将被移动到尾部，因为下一轮删除将从 m % n 为起点开始：

```
// m % n 是新的起点

m % n
(m + 1) % n
...
n - 1
0
...
(m - 2) % n

```

于是新的起点是旧一轮的 m % n，而作为新的一轮，它是 0，于是就有如下一一对映的关系：

```
// 上一轮和下一轮数字关系

m % n ------------------- 0
(m + 1) % n ------------- 1
...                       ...
n - 1 ------------------- (n - 1) - (m % n)
0 ----------------------- n - (m % n)
...                       ...
(m - 2) % n ------------- n - 2
```

这时候记旧一轮的序号为 y，新一轮的序号为 x，则它们的关系通过上图可以推导出：

```
y = (m - 2) % n
x = n - 2

(m - 2) % n
(m - 2) % n + 0
(m - 2) % n + (n % n)
(m + n - 2) % n
((n - 2) + m) % n

// 就是如下公式：

y = (x + m) % n

// 而将 x 和 y 换成对应的 f 函数，则得到下面的递归公式：

f(n, m) = (f(n - 1, m) + m) % n (n > 1)
f(1, m) = 0 (n = 1)
```

有了这个公式 `f(n, m) = (f(n - 1, m) + m) % n`，就可以写出递归调用的代码了：


```js
var lastRemaining = function(n, m) {
    if (n === 1) return n - 1;
    return (lastRemaining(n - 1, m) + m) % n;
};
```

</details>
<br><br>