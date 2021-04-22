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